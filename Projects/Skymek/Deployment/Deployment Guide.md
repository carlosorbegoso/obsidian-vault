# Deployment Guide - Sky Data Production

## Server Info

- **IP**: 135.181.249.153
- **SSH**: `ssh -i ~/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153`

---

## Arquitectura de Frontends

Los frontends corren como **contenedores Docker con nginx propio**.
El nginx centralizado (`nginx`) actúa como reverse proxy SSL hacia ellos.

```
Internet → nginx (SSL) → landing:8080     (skymek.app)
                             → web-transport:8080 (app.skymek.app)
                             → apisix:9080        (api.skymek.app)
```

`docker ps` debe mostrar:

- `landing`
- `web-transport`
- `nginx`
- `apisix`
- `ms-auth-service`, `ms-data-service`, `ms-workorder-service`, `ms-notification-service`, `ms-subscription-service`, `ms-inventory-service`

---

## Build & Deploy Commands

### Landing Page (Astro) - Contenedor Docker

```bash
# 1. Construir imagen (incluye nginx)
# IMPORTANTE: PUBLIC_SITE_URL se embebe en el HTML en build-time (canonical, OG tags, etc.)
cd /Users/carlos/Desktop/projects/tesis/projects/landing-page
docker rmi landing:latest 2>/dev/null
docker build --platform linux/amd64 --no-cache \
  --build-arg PUBLIC_APP_URL=https://app.skymek.app \
  --build-arg PUBLIC_SITE_URL=https://skymek.app \
  --build-arg PUBLIC_SUBSCRIPTION_API_URL=https://api.skymek.app/api/subscription \
  -t landing:latest .

# 2. Exportar imagen
mkdir -p ~/Desktop/projects/tesis/tmp
docker save landing:latest | gzip > ~/Desktop/projects/tesis/tmp/landing.tar.gz

# 3. Subir al servidor
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  ~/Desktop/projects/tesis/tmp/landing.tar.gz \
  root@135.181.249.153:/tmp/

# 4. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f landing 2>/dev/null; \
   docker rmi localhost/landing:latest 2>/dev/null; \
   gunzip -c /tmp/landing.tar.gz | docker load && \
   docker run -d --name landing \
    --network skymek-production-network \
    --restart unless-stopped \
    localhost/landing:latest"
```

### Web Transport (Angular PWA) - Contenedor Docker

```bash
# 1. Construir imagen (incluye nginx)
cd /Users/carlos/Desktop/projects/tesis/projects/web-transport
docker build --platform linux/amd64 -t web-transport:latest .

# 2. Exportar imagen
docker save web-transport:latest | gzip > ~/Desktop/projects/tesis/tmp/web-transport.tar.gz

# 3. Subir al servidor
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  ~/Desktop/projects/tesis/tmp/web-transport.tar.gz \
  root@135.181.249.153:/tmp/

# 4. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f web-transport 2>/dev/null; \
   gunzip -c /tmp/web-transport.tar.gz | docker load && \
   docker run -d --name web-transport \
    --network skymek-production-network \
    --restart unless-stopped \
    localhost/web-transport:latest"
```

**Nota**: Los frontends corren como contenedores en la red `skymek-production-network`.
El nginx centralizado hace proxy_pass hacia ellos por nombre de contenedor.

### Actualizar configuración de Nginx centralizado

Si se modifica `production-architecture/configuration/nginx/reverse-proxy.conf`:

```bash
# 1. Subir nuevo config
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  /Users/carlos/Desktop/projects/tesis/projects/production-architecture/configuration/nginx/reverse-proxy.conf \
  root@135.181.249.153:/opt/production-architecture/configuration/nginx/reverse-proxy.conf

# 2. Validar y aplicar (IMPORTANTE: usar restart, no reload)
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec nginx nginx -t && docker restart nginx"
```

> **Nota:** El contenedor nginx se llama `nginx` (es el contenedor que escucha en los puertos 80/443).
> `nginx -s reload` a veces no toma efecto aunque diga que fue exitoso.
> Usar siempre `docker restart nginx` para garantizar que la nueva config se carga.

### Troubleshooting: nginx sirve archivos estáticos en vez de los contenedores Docker

Si `app.skymek.app` o `skymek.app` responden pero los cambios del contenedor no se ven
(o da 403), el servidor puede estar usando una config desactualizada que sirve `/var/www/app`
o `/var/www/landing` en lugar de hacer proxy hacia los contenedores.

**Diagnóstico:**

```bash
# Verificar qué config está activa (debe mostrar los upstreams, no root /var/www/...)
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec nginx nginx -T 2>/dev/null | grep -A3 upstream"
```

**Solución:** Volver a subir la config correcta y hacer restart:

```bash
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  /Users/carlos/Desktop/projects/tesis/projects/production-architecture/configuration/nginx/reverse-proxy.conf \
  root@135.181.249.153:/opt/production-architecture/configuration/nginx/reverse-proxy.conf

ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec nginx nginx -t && docker restart nginx"
```

---

### Microservicios Quarkus (Native-Micro)

**Importante**: Desde Mac ARM64, usar `--platform=linux/amd64` para generar ejecutable compatible con servidor AMD64

**Seguridad - Vault Integration**: Los microservicios solo necesitan 3 variables de entorno:

- `QUARKUS_PROFILE=prod` - Activa el perfil de producción
- `VAULT_URL=http://vault:8200` - URL del servidor Vault
- `VAULT_TOKEN=hvs.xxx` - Token de autenticación

Todos los secretos (contraseñas de Redis, PostgreSQL, client secrets, etc.) se cargan automáticamente desde Vault en `secret/prod/{service-name}`.
Las URLs internas entre servicios están hardcodeadas en cada `application-prod.yml` (no son secretos).
NO pasar secretos como variables de entorno (vulnerabilidad de seguridad - visibles con `docker inspect`).

**Nota General**: La primera vez que se despliega cada microservicio, agregar una de estas variables para crear las tablas:

- `-e QUARKUS_HIBERNATE_ORM_SCHEMA_MANAGEMENT_STRATEGY=update` (ms-auth-service, ms-data-service, ms-notification-service, ms-subscription-service, ms-inventory-service)
- `-e QUARKUS_HIBERNATE_ORM_DATABASE_GENERATION=update` (ms-workorder-service - usa propiedad deprecada)

---

#### ms-auth-service (Puerto 8084, Schema: ms-auth)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-auth-service
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
docker rmi ms-auth-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-auth-service:latest .

# 4. Exportar y subir
docker save ms-auth-service:latest | gzip > /tmp/ms-auth-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-auth-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-auth-service 2>/dev/null; docker rmi localhost/ms-auth-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-auth-service.tar.gz | docker load && \
   docker run -d --name ms-auth-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-auth-service:latest"
```

---

#### ms-data-service (Puerto 8081, Schema: ms-data)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-data-service
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
docker rmi ms-data-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-data-service:latest .

# 4. Exportar y subir
docker save ms-data-service:latest | gzip > /tmp/ms-data-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-data-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-data-service 2>/dev/null; docker rmi localhost/ms-data-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-data-service.tar.gz | docker load && \
   docker run -d --name ms-data-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-data-service:latest"
```

---

#### ms-workorder-service (Puerto 8082, Schema: workorder)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-workorder-service
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
docker rmi ms-workorder-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-workorder-service:latest .

# 4. Exportar y subir
docker save ms-workorder-service:latest | gzip > /tmp/ms-workorder-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-workorder-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-workorder-service 2>/dev/null; docker rmi localhost/ms-workorder-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-workorder-service.tar.gz | docker load && \
   docker run -d --name ms-workorder-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-workorder-service:latest"
```

---

#### ms-notification-service (Puerto 8083, Schema: notification)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-notification-service
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
docker rmi ms-notification-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-notification-service:latest .

# 4. Exportar y subir
docker save ms-notification-service:latest | gzip > /tmp/ms-notification-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-notification-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-notification-service 2>/dev/null; docker rmi localhost/ms-notification-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-notification-service.tar.gz | docker load && \
   docker run -d --name ms-notification-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-notification-service:latest"
```

---

#### ms-subscription-service (Puerto 8085, Schema: ms-subscription)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-subscription-service
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
docker rmi ms-subscription-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-subscription-service:latest .

# 4. Exportar y subir
docker save ms-subscription-service:latest | gzip > /tmp/ms-subscription-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-subscription-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-subscription-service 2>/dev/null; docker rmi localhost/ms-subscription-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-subscription-service.tar.gz | docker load && \
   docker run -d --name ms-subscription-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-subscription-service:latest"
```

---

#### ms-inventory-service (Puerto 8086, Schema: inventory)

```bash
# 0. Limpiar builds anteriores
cd /Users/carlos/Desktop/projects/tesis/projects/ms-inventory-service
rm -rf build/

# 1. Build nativo
./gradlew build -x test -Dquarkus.native.enabled=true \
  -Dquarkus.package.jar.enabled=false \
  -Dquarkus.native.container-build=true \
  -Dquarkus.native.container-runtime-options=--platform=linux/amd64 \
  -Dquarkus.native.native-image-xmx=8g

# 2. Verificar arquitectura (debe decir x86-64)
file build/*-runner

# 3. Construir imagen Docker
docker rmi ms-inventory-service:latest 2>/dev/null; docker build --platform linux/amd64 --no-cache -f src/main/docker/Dockerfile.native-micro -t ms-inventory-service:latest .

# 4. Exportar y subir
docker save ms-inventory-service:latest | gzip > /tmp/ms-inventory-service.tar.gz
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis /tmp/ms-inventory-service.tar.gz root@135.181.249.153:/tmp/

# 5. Cargar y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ms-inventory-service 2>/dev/null; docker rmi localhost/ms-inventory-service:latest 2>/dev/null; \
   gunzip -c /tmp/ms-inventory-service.tar.gz | docker load && \
   docker run -d --name ms-inventory-service --network skymek-production-network \
    -e QUARKUS_PROFILE=prod \
    -e VAULT_URL=http://vault:8200 \
    -e VAULT_TOKEN=hvs.REDACTED \
    -e QUARKUS_OIDC_TOKEN_ISSUER=http://api.skymek.app/realms/skymek \
    --restart unless-stopped localhost/ms-inventory-service:latest"
```

**Nota**: El schema `inventory` debe existir en PostgreSQL antes del primer deploy. Si no existe:

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec postgres psql -U tesis -d tesis_db -c 'CREATE SCHEMA IF NOT EXISTS inventory;'"
```

**Nota**: Los secrets requeridos en Vault (`secret/prod/ms-inventory-service`) son: `db-username`, `db-password`, `redis-url`. Las URLs de DB y rest-clients están hardcodeadas en `application-prod.yml`.

---

### ml-pipeline (Puerto 8002, Python/FastAPI ML Pipeline XGB)

**Reemplaza a**: ~~ms-prediction-service~~ (dado de baja)
**Modelos**: montados desde `/opt/production-architecture/data/ml-models/` (volumen rw — el servicio puede reentrenar y guardar nuevos modelos)

```bash
# 1. Construir imagen localmente (para AMD64)
cd /Users/carlos/Desktop/projects/tesis/projects/ml-pipeline-xgb
docker build --platform linux/amd64 -t ml-pipeline:latest .

# 2. Exportar imagen
docker save ml-pipeline:latest | gzip > ~/Desktop/projects/tesis/tmp/ml-pipeline.tar.gz

# 3. Subir imagen al servidor
scp -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  ~/Desktop/projects/tesis/tmp/ml-pipeline.tar.gz root@135.181.249.153:/tmp/

# 4. Cargar imagen y ejecutar en servidor
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker rm -f ml-pipeline 2>/dev/null; docker rmi localhost/ml-pipeline:latest 2>/dev/null; \
   gunzip -c /tmp/ml-pipeline.tar.gz | docker load && \
   docker run -d --name ml-pipeline --network skymek-production-network \
    -e ENVIRONMENT=production \
    -e LOG_LEVEL=INFO \
    -e DB_HOST=postgres \
    -e DB_PORT=5432 \
    -e DB_NAME=tesis_db \
    -e DB_USER=tesis \
    -e DB_PASSWORD=\$(docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv get -field=db-password secret/prod/ms-data-service) \
    -v /opt/production-architecture/data/ml-models:/app/models:rw \
    --restart unless-stopped localhost/ml-pipeline:latest"
```

#### Subir modelos al servidor (primera vez o actualización manual)

Los modelos se entrenan localmente con el pipeline en `ml-pipeline-xgb/ml/` y luego se
suben manualmente al servidor. El contenedor monta `/opt/production-architecture/data/ml-models`
como `/app/models`, así que basta con copiar los archivos ahí.

**Archivos de modelo generados por el pipeline:**

| Archivo                          | Descripción                                           |
| -------------------------------- | ------------------------------------------------------ |
| `multi_model_failure.xgb`      | Predicción de fallo (binario)                         |
| `multi_model_time.xgb`         | Días hasta mantenimiento                              |
| `multi_model_component.xgb`    | Componente más probable                               |
| `multi_model_cost.xgb`         | Costo estimado                                         |
| `multi_model_quantile_q10.xgb` | Intervalo de confianza inferior (10%)                  |
| `multi_model_quantile_q50.xgb` | Mediana cuantílica                                    |
| `multi_model_quantile_q90.xgb` | Intervalo de confianza superior (90%)                  |
| `multi_model_survival.xgb`     | Modelo de supervivencia                                |
| `*.joblib`                     | Scaler / preprocesador del pipeline                    |
| `*_metadata.json`              | Metadata del entrenamiento (features, threshold, etc.) |

> Los archivos con timestamp (`*_20260219_203621.*`) son copias de respaldo generadas
> automáticamente por el pipeline. Los nombres sin timestamp son los que carga la API
> (`multi_model_*.xgb`, `*.joblib`, `*_metadata.json`).

**Subir todos los modelos desde local:**

```bash
# Desde la raíz del proyecto ml-pipeline-xgb
MODELS_DIR=/Users/carlos/Desktop/projects/tesis/projects/ml-pipeline-xgb/models
SSH_KEY=/Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis
SERVER=root@135.181.249.153
REMOTE_DIR=/opt/production-architecture/data/ml-models/

scp -i $SSH_KEY \
  $MODELS_DIR/multi_model_failure.xgb \
  $MODELS_DIR/multi_model_time.xgb \
  $MODELS_DIR/multi_model_component.xgb \
  $MODELS_DIR/multi_model_cost.xgb \
  $MODELS_DIR/multi_model_quantile_q10.xgb \
  $MODELS_DIR/multi_model_quantile_q50.xgb \
  $MODELS_DIR/multi_model_quantile_q90.xgb \
  $MODELS_DIR/multi_model_survival.xgb \
  $MODELS_DIR/*.joblib \
  $MODELS_DIR/*.json \
  $SERVER:$REMOTE_DIR
```

**Verificar que los archivos llegaron correctamente:**

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "ls -lh /opt/production-architecture/data/ml-models/"
```

**Recargar el contenedor si ya estaba corriendo (sin necesidad de rebuild):**

```bash
# El contenedor lee los modelos en cada request (/predict), no al iniciar.
# Solo es necesario reiniciarlo si quieres limpiar el estado en memoria.
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker restart ml-pipeline"

# Verificar que levantó correctamente
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker logs --tail 20 ml-pipeline"
```

#### Health Check

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker run --rm --network skymek-production-network alpine wget -q -O- http://ml-pipeline:8002/health"
```

#### APISIX Upstream (upstream_id: 5)

El upstream ya está configurado apuntando a `ml-pipeline:8002`. Si necesitas verificarlo:

```bash
curl -s http://135.181.249.153:9180/apisix/admin/upstreams/5 \
  -H "X-API-KEY: 0641894a25288722986ebc621f4684e1" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['value']['nodes'])"
```

---

### Crear Schemas en PostgreSQL (Primera vez)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec postgres psql -U tesis -d tesis_db -c 'CREATE SCHEMA IF NOT EXISTS \"ms-auth\"; CREATE SCHEMA IF NOT EXISTS \"ms-data\"; CREATE SCHEMA IF NOT EXISTS \"workorder\"; CREATE SCHEMA IF NOT EXISTS \"notification\"; CREATE SCHEMA IF NOT EXISTS \"ms-subscription\";'"
```

### Health Check de Microservicios

```bash
# Verificar que un servicio está corriendo
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker run --rm --network skymek-production-network alpine wget -q -O- http://ms-auth-service:8084/q/health/ready"
```

---

## Keycloak

### Credenciales

- **Admin URL**: http://localhost:8180 (via SSH tunnel — ver abajo)
- **User**: admin
- **Password**: G3dVrAYi3llajDb3

### SSH Tunnel para acceder al Admin de Keycloak

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 8180:localhost:8180 \
  root@135.181.249.153
```

Luego abre en tu navegador: http://localhost:8180

> **Nota:** Para abrir varios túneles a la vez (Keycloak + APISIX Dashboard + Vault):
>
> ```bash
> ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
>   -L 8180:localhost:8180 \
>   -L 9000:localhost:9000 \
>   -L 8200:localhost:8200 \
>   root@135.181.249.153
> ```

### Variables requeridas en `.env` del servidor

```
KEYCLOAK_ADMIN_PASSWORD=G3dVrAYi3llajDb3
KC_SMTP_PASSWORD=re_5FwiWR2G_NkQeKqCLM16ov3drghMNa46e
```

### Agregar variables al server (si no existen)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "grep -q KC_SMTP_PASSWORD /opt/production-architecture/.env || cat >> /opt/production-architecture/.env << 'EOF'
KC_SMTP_PASSWORD=re_5FwiWR2G_NkQeKqCLM16ov3drghMNa46e
KEYCLOAK_ADMIN_PASSWORD=G3dVrAYi3llajDb3
EOF"
```

### SMTP (Resend)

- **Host**: smtp.resend.com
- **Port**: 465
- **User**: resend
- **From**: noreply@skymek.app
- **API Key**: ver `KC_SMTP_PASSWORD` arriba

### Realm

- **Realm**: `skymek` (archivo: `configuration/keycloak/realm-skymek-prod.json`)
- Se importa automáticamente al iniciar con `--import-realm`
- Si el realm ya existe, el import se omite (`IGNORE_EXISTING`)

---

## Vault Commands

### Ver todos los secrets

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv list secret/prod/"
```

### Ver secrets de un servicio

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -e VAULT_TOKEN='hvs.REDACTED' vault vault kv get secret/prod/ms-auth-service"
```

### Unseal Vault (despues de reinicio)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec vault vault operator unseal 'BD3xCXRLhccFsoJe46YmMLOIuUGpn2m9DL/dPBKKTuw='"
```

---

## Database Commands

### Conectar a PostgreSQL

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec -it postgres psql -U tesis -d tesis_db"
```

### Ver schemas

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis root@135.181.249.153 \
  "docker exec postgres psql -U tesis -d tesis_db -c '\dn'"
```

---

## APISIX Commands

### Ver rutas configuradas

```bash
curl -s http://135.181.249.153:9180/apisix/admin/routes \
  -H "X-API-KEY: 0641894a25288722986ebc621f4684e1" | jq
```

### Crear ruta para microservicio

```bash
curl -X PUT http://135.181.249.153:9180/apisix/admin/routes/auth-api \
  -H "X-API-KEY: 0641894a25288722986ebc621f4684e1" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "auth-api",
    "uri": "/api/auth/*",
    "upstream": {
      "type": "roundrobin",
      "nodes": {"ms-auth-service:8084": 1}
    },
    "plugins": {
      "proxy-rewrite": {
        "regex_uri": ["^/api/auth/(.*)", "/$1"]
      }
    }
  }'
```

---

## URLs de Acceso

| Servicio     | URL                    |
| ------------ | ---------------------- |
| Landing Page | https://skymek.app     |
| Web App      | https://app.skymek.app |
| API Gateway  | https://api.skymek.app |

---

## Acceso a Interfaces de Administración (SSH Tunnel)

**IMPORTANTE**: Los puertos internos (9000, 8200, 5432, 6379, etc.) están bloqueados por firewall.
Para acceder a APISIX Dashboard y Vault UI, usa SSH Tunneling.

### Opción 1: Ambos a la vez (recomendado)

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 9000:localhost:9000 \
  -L 8200:localhost:8200 \
  root@135.181.249.153
```

Luego abre en tu navegador:

- **APISIX Dashboard**: http://localhost:9000
- **Vault UI**: http://localhost:8200

### Opción 2: Solo APISIX Dashboard

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 9000:localhost:9000 root@135.181.249.153
```

Abre: http://localhost:9000

### Opción 3: Solo Vault

```bash
ssh -i /Users/carlos/Desktop/projects/tesis/keys/hetzner-tesis \
  -L 8200:localhost:8200 root@135.181.249.153
```

Abre: http://localhost:8200

---

## Credenciales

Ver archivo: `production-secrets.env`

### APISIX Dashboard

- **User**: admin
- **Password**: jqiDkyoC/4Vs56pnszWxkdbTuZ9mw2dN

### Vault

- **Token**: hvs.REDACTED
- **Unseal Key**: BD3xCXRLhccFsoJe46YmMLOIuUGpn2m9DL/dPBKKTuw=

### Keycloak

- **User**: admin
- **Password**: G3dVrAYi3llajDb3
- **SMTP API Key (Resend)**: re_5FwiWR2G_NkQeKqCLM16ov3drghMNa46e

---

## Servicios y Puertos

| Servicio                | Puerto Interno | Puerto Externo           |
| ----------------------- | -------------- | ------------------------ |
| APISIX Gateway          | 9080           | Bloqueado (via Nginx)    |
| APISIX Dashboard        | 9000           | Bloqueado (SSH Tunnel)   |
| APISIX Admin            | 9180           | Bloqueado                |
| PostgreSQL              | 5432           | Bloqueado                |
| Redis                   | 6379           | Bloqueado                |
| Vault                   | 8200           | Bloqueado (SSH Tunnel)   |
| landing                 | 8080           | - (via Nginx proxy)      |
| web-transport           | 8080           | - (via Nginx proxy)      |
| ml-pipeline             | 8002           | - (via APISIX /api/ml/*) |
| ms-auth-service         | 8084           | -                        |
| ms-data-service         | 8081           | -                        |
| ms-workorder-service    | 8082           | -                        |
| ms-notification-service | 8083           | -                        |
| ms-subscription-service | 8085           | -                        |
| ms-inventory-service    | 8086           | -                        |

---

## Environment Config

### Angular (web-transport)

El archivo `src/environments/environment.docker.ts` apunta a:

```typescript
const GATEWAY_URL = 'https://api.skymek.app';
```

### Quarkus Microservices

Los microservicios obtienen secrets de Vault en `secret/prod/{service-name}`.
Solo necesitan 2 env vars al arrancar: `VAULT_URL` y `VAULT_TOKEN`.

**ms-auth-service** (`secret/prod/ms-auth-service`):

- `db-url` — URL reactiva de PostgreSQL (sin contraseña, solo host/db)
- `db-jdbc-url` — URL JDBC de PostgreSQL (sin contraseña, solo host/db)
- `db-username` — Usuario de PostgreSQL (ej: `tesis`)
- `db-password` — Contraseña de PostgreSQL
- `redis-url` — URL de Redis con contraseña (ej: `redis://:pass@redis:6379`)
- `keycloak-admin-client-secret` — Client secret de ms-auth-service en Keycloak realm skymek

Las URLs internas (Keycloak, data-service, etc.) están hardcodeadas en `application-prod.yml`
y apuntan a los nombres de contenedor Docker (`keycloak:8080`, `data-service:8081`, etc.).
