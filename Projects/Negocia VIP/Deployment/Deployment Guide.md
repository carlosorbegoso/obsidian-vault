# Deployment Guide - Negocia VIP Production

## Server Info

- **IP**: 135.181.249.153
- **SSH**: `ssh -i ~/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153`

---

## Arquitectura

Negocia VIP comparte infraestructura con Skymek en el mismo servidor.

```
Internet -> nginx (SSL) -> vip-panel:8080     (bot.negocia.vip)
                        -> keycloak:8180      (api.negocia.vip/realms/*)
                        -> apisix:9080        (api.negocia.vip/api/*)
                                |
                          +-----+-----+-----+
                          |           |     |
                     vip-core   vip-brain  vip-messaging
                      :8082      :8081      :8080
```

Infraestructura compartida (en `/opt/server-infrastructure`):
- PostgreSQL (schemas: `negocio`, `saas`, `auditoria`)
- Keycloak (realm: `negocia-vip`, theme: `negocia`)
- Redis, Vault, APISIX, etcd

Deployment de Negocia (en `/opt/negocia-architecture`):
- vip-panel (Angular frontend - bot.negocia.vip)
- vip-messaging, vip-core, vip-brain (pendientes de build)

**Nota sobre routing**: Las rutas `/realms/*` y `/resources/*` de Keycloak van directo de nginx a Keycloak (sin pasar por APISIX) para que los headers `X-Forwarded-Proto: https` lleguen correctamente. Las rutas `/api/*` van via APISIX.

---

## Red Docker

Todos los servicios se conectan a `server-infra-network` (red compartida).
Los contenedores se referencian por nombre: `postgres`, `redis`, `vault`, `keycloak`.

---

## Estado actual

| Servicio | Estado | Notas |
|---|---|---|
| vip-panel | Desplegado | bot.negocia.vip |
| vip-core | Pendiente | Necesita build con quarkus-vault |
| vip-messaging | Pendiente | Necesita build con quarkus-vault |
| vip-brain | Pendiente | Necesita build con quarkus-vault |

---

## Build & Deploy Commands

### vip-panel (Angular - bot.negocia.vip) - Contenedor Docker

```bash
# 1. Construir imagen (incluye nginx)
cd /Users/carlos/Desktop/ia-proyects/vip-panel
docker build --platform linux/amd64 --no-cache -t vip-panel:latest .

# 2. Exportar imagen
docker save vip-panel:latest | gzip > /tmp/vip-panel.tar.gz

# 3. Subir al servidor
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  /tmp/vip-panel.tar.gz \
  root@135.181.249.153:/tmp/

# 4. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f vip-panel 2>/dev/null; \
   docker rmi localhost/vip-panel:latest 2>/dev/null; \
   gunzip -c /tmp/vip-panel.tar.gz | docker load && \
   docker run -d --name vip-panel \
    --network server-infra-network \
    -p 8085:8080 \
    --restart unless-stopped \
    localhost/vip-panel:latest"
```

**Nota**: El contenedor expone puerto 8085 al host. El nginx del host hace proxy desde `bot.negocia.vip` hacia `127.0.0.1:8085`. No depende de IPs de Docker.

---

### Microservicios Quarkus (Native-Micro)

**IMPORTANTE**: Los microservicios deben tener la extension `quarkus-vault` en su `build.gradle` para que las variables `QUARKUS_VAULT_*` funcionen. Sin esta extension, el servicio falla con `SRCFG00050: quarkus.vault.kv.paths does not map to any root`.

```groovy
// build.gradle - agregar esta dependencia
implementation 'io.quarkus:quarkus-vault'
```

**Desde Mac ARM64**: usar `--platform=linux/amd64` para generar ejecutable compatible con servidor AMD64.

**Vault Integration**: Los microservicios necesitan estas variables de entorno:

- `QUARKUS_PROFILE=prod`
- `QUARKUS_VAULT_URL=http://vault:8200`
- `QUARKUS_VAULT_AUTHENTICATION_CLIENT_TOKEN=hvs.REDACTED`
- `QUARKUS_VAULT_KV_PATHS=prod/negocia/{service-name}`

Todos los secretos (passwords de Redis, PostgreSQL, API keys, etc.) se cargan automaticamente desde Vault.
NO pasar secretos como variables de entorno (vulnerabilidad de seguridad - visibles con `docker inspect`).

**Primera vez**: agregar esta variable para crear las tablas:

- `-e QUARKUS_HIBERNATE_ORM_SCHEMA_MANAGEMENT_STRATEGY=update`

---

#### vip-messaging (Puerto 8080, Schema: negocio)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/ia-proyects/vip-messaging
rm -rf build/

# 1. Build nativo
./gradlew build -Dquarkus.native.enabled=true \
  -Dquarkus.package.jar.enabled=false \
  -Dquarkus.native.container-build=true \
  -Dquarkus.native.container-runtime-options=--platform=linux/amd64 \
  -Dquarkus.native.native-image-xmx=8g

# 2. Verificar arquitectura (debe decir x86-64)
file build/*-runner

# 3. Construir imagen Docker
docker rmi vip-messaging:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t vip-messaging:latest .

# 4. Exportar y subir
docker save vip-messaging:latest | gzip > /tmp/vip-messaging.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/vip-messaging.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f vip-messaging 2>/dev/null; docker rmi localhost/vip-messaging:latest 2>/dev/null; \
   gunzip -c /tmp/vip-messaging.tar.gz | docker load && \
   docker run -d --name vip-messaging --network server-infra-network \
    -e QUARKUS_PROFILE=prod \
    -e QUARKUS_VAULT_URL=http://vault:8200 \
    -e QUARKUS_VAULT_AUTHENTICATION_CLIENT_TOKEN=hvs.REDACTED \
    -e QUARKUS_VAULT_KV_PATHS=prod/negocia/vip-messaging \
    --restart unless-stopped localhost/vip-messaging:latest"
```

---

#### vip-core (Puerto 8082, Schema: negocio)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/ia-proyects/vip-core
rm -rf build/

# 1. Build nativo
./gradlew build -Dquarkus.native.enabled=true \
  -Dquarkus.package.jar.enabled=false \
  -Dquarkus.native.container-build=true \
  -Dquarkus.native.container-runtime-options=--platform=linux/amd64 \
  -Dquarkus.native.native-image-xmx=8g

# 2. Verificar arquitectura (debe decir x86-64)
file build/*-runner

# 3. Construir imagen Docker
docker rmi vip-core:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t vip-core:latest .

# 4. Exportar y subir
docker save vip-core:latest | gzip > /tmp/vip-core.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/vip-core.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f vip-core 2>/dev/null; docker rmi localhost/vip-core:latest 2>/dev/null; \
   gunzip -c /tmp/vip-core.tar.gz | docker load && \
   docker run -d --name vip-core --network server-infra-network \
    -e QUARKUS_PROFILE=prod \
    -e QUARKUS_VAULT_URL=http://vault:8200 \
    -e QUARKUS_VAULT_AUTHENTICATION_CLIENT_TOKEN=hvs.REDACTED \
    -e QUARKUS_VAULT_KV_PATHS=prod/negocia/vip-core \
    --restart unless-stopped localhost/vip-core:latest"
```

---

#### vip-brain (Puerto 8081, Schema: -)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/ia-proyects/vip-brain
rm -rf build/

# 1. Build nativo
./gradlew build -Dquarkus.native.enabled=true \
  -Dquarkus.package.jar.enabled=false \
  -Dquarkus.native.container-build=true \
  -Dquarkus.native.container-runtime-options=--platform=linux/amd64 \
  -Dquarkus.native.native-image-xmx=8g

# 2. Verificar arquitectura (debe decir x86-64)
file build/*-runner

# 3. Construir imagen Docker
docker rmi vip-brain:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t vip-brain:latest .

# 4. Exportar y subir
docker save vip-brain:latest | gzip > /tmp/vip-brain.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/vip-brain.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f vip-brain 2>/dev/null; docker rmi localhost/vip-brain:latest 2>/dev/null; \
   gunzip -c /tmp/vip-brain.tar.gz | docker load && \
   docker run -d --name vip-brain --network server-infra-network \
    -e QUARKUS_PROFILE=prod \
    -e QUARKUS_VAULT_URL=http://vault:8200 \
    -e QUARKUS_VAULT_AUTHENTICATION_CLIENT_TOKEN=hvs.REDACTED \
    -e QUARKUS_VAULT_KV_PATHS=prod/negocia/vip-brain \
    --restart unless-stopped localhost/vip-brain:latest"
```

---

## Keycloak

### Credenciales del Panel

- **Email**: admin@negocia.vip
- **Password**: CHANGE_ME_IN_PRODUCTION (temporal, se pide cambiar en primer login)
- **Rol**: super_admin

### Admin de Keycloak

- **Admin URL**: http://localhost:8180 (via SSH tunnel)
- **User**: admin
- **Password**: n0SWxZl9F2egwO2DgVPNTg==

### SSH Tunnel para acceder al Admin

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 8180:localhost:8180 \
  root@135.181.249.153
```

Luego abre en tu navegador: http://localhost:8180

Selecciona el realm **negocia-vip** en el dropdown.

### Realm

- **Realm**: `negocia-vip` (archivo: `configuration/keycloak/negocia-vip-realm.json`)
- Se importa automaticamente al iniciar con `--import-realm`
- Clientes: `negocia-panel` (public, redirect: bot.negocia.vip), `negocia-admin` (public), `negocia-services` (confidential)
- Roles: `super_admin`, `tenant_admin`, `tenant_user`
- Theme personalizado: `negocia` (login en espanol)

---

## Vault Commands

### Ver secretos de Negocia

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv list secret/prod/negocia/"
```

### Ver secretos de un servicio

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv get secret/prod/negocia/vip-core"
```

### Actualizar un secreto

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv patch secret/prod/negocia/vip-brain claude-api-key=sk-ant-xxx"
```

### Unseal Vault (despues de reinicio)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec vault vault operator unseal 'HKN5r/1DQV7pTMozQnXDUfRCEe3IVnQUUVUc29QZ+yQ='"
```

---

## Database Commands

### Conectar a PostgreSQL

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -it postgres psql -U server_admin -d server_db"
```

### Ver schemas

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec postgres psql -U server_admin -d server_db -c '\dn'"
```

### Schemas de Negocia VIP

| Schema | Uso |
|---|---|
| negocio | Leads, catalogo, citas, config bot |
| saas | Tenants, planes, facturacion |
| auditoria | Logs, eventos, metricas |

---

## APISIX - Rutas de Negocia

### Configurar rutas

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "cd /opt/server-infrastructure && APISIX_ADMIN_KEY=008e7c3226c4144b032bd2a518f55e99 bash scripts/apisix/setup-negocia-routes.sh"
```

### Rutas de Negocia VIP (host: api.negocia.vip)

| Ruta | Destino | Via |
|---|---|---|
| /realms/* | keycloak:8180 | nginx directo |
| /resources/* | keycloak:8180 | nginx directo |
| /api/core/** | vip-core /api/** | APISIX |
| /api/messaging/** | vip-messaging /** | APISIX |
| /api/brain/** | vip-brain /api/** | APISIX |
| /internal/** | vip-core (IP restricted) | APISIX |
| /auth/** | keycloak /realms/negocia-vip/... | APISIX |

---

## Health Check de Microservicios

```bash
# vip-core
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker run --rm --network server-infra-network alpine wget -q -O- http://vip-core:8082/q/health/ready"

# vip-messaging
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker run --rm --network server-infra-network alpine wget -q -O- http://vip-messaging:8080/q/health/ready"

# vip-brain
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker run --rm --network server-infra-network alpine wget -q -O- http://vip-brain:8081/q/health/ready"
```

---

## Acceso a Interfaces de Administracion (SSH Tunnel)

**IMPORTANTE**: Los puertos internos estan bloqueados por firewall.

### Todos a la vez (recomendado)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 8180:localhost:8180 \
  -L 9000:localhost:9000 \
  -L 8200:localhost:8200 \
  root@135.181.249.153
```

- **Keycloak**: http://localhost:8180 (realm: negocia-vip)
- **APISIX Dashboard**: http://localhost:9000
- **Vault UI**: http://localhost:8200

---

## Servicios y Puertos (Negocia VIP)

| Servicio | Puerto Interno | Puerto Host | Acceso Externo |
|---|---|---|---|
| vip-panel | 8080 | 8085 | https://bot.negocia.vip |
| vip-messaging | 8080 | - | via APISIX /api/messaging/* |
| vip-core | 8082 | - | via APISIX /api/core/* |
| vip-brain | 8081 | - | via APISIX /api/brain/* |

---

## URLs de Acceso

| Servicio | URL | Estado |
|---|---|---|
| API Gateway | https://api.negocia.vip | Activo |
| Bot Panel | https://bot.negocia.vip | Activo |
| Landing | https://negocia.vip | Cloudflare Worker |

---

## Quarkus Vault Paths

Los microservicios obtienen secrets de Vault en `secret/prod/negocia/{service-name}`.

**vip-messaging** (`secret/prod/negocia/vip-messaging`):
- `db-url` - URL JDBC de PostgreSQL (schema: negocio)
- `db-username` - Usuario de PostgreSQL
- `db-password` - Password de PostgreSQL
- `redis-host` - Host de Redis
- `redis-port` - Puerto de Redis
- `redis-password` - Password de Redis
- `meta-verify-token` - Token de verificacion de Meta/WhatsApp
- `meta-app-secret` - App secret de Meta

**vip-core** (`secret/prod/negocia/vip-core`):
- `db-url` - URL JDBC de PostgreSQL (schema: negocio)
- `db-username` - Usuario de PostgreSQL
- `db-password` - Password de PostgreSQL
- `redis-host` - Host de Redis
- `redis-port` - Puerto de Redis
- `redis-password` - Password de Redis
- `google-credentials` - Credenciales de Google Calendar
- `smtp-host`, `smtp-port`, `smtp-username`, `smtp-password` - Config SMTP

**vip-brain** (`secret/prod/negocia/vip-brain`):
- `redis-host` - Host de Redis
- `redis-port` - Puerto de Redis
- `redis-password` - Password de Redis
- `claude-api-key` - API key de Claude/Anthropic
