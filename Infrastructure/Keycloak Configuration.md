# Keycloak - Configuracion de Produccion

Servidor: `135.181.249.153`
Realm: `skymek`
Puerto interno: `8080` | Puerto externo (host): `8180`

---

## User Profile - Atributos Customizados

Keycloak (versiones recientes) tiene habilitado **User Profile** por defecto. Esto significa que **solo los atributos registrados en el User Profile** se pueden guardar en los usuarios. Si un atributo no esta registrado, Keycloak lo rechaza silenciosamente.

### Atributos registrados

| Atributo      | Descripcion                                  | Editable por |
|---------------|----------------------------------------------|--------------|
| `tallerId`    | ID del taller en ms-data-service             | admin        |
| `profileType` | Tipo de perfil: `taller_admin`, `owner`, `mechanic` | admin |
| `ownerId`     | ID del propietario de vehiculo               | admin        |
| `mechanicId`  | ID del mecanico                              | admin        |
| `userId`      | ID interno del usuario                       | admin        |

### Como se configuraron

Se uso la API Admin de Keycloak (`PUT /admin/realms/skymek/users/profile`) para agregar estos atributos al User Profile del realm `skymek`. Sin esta configuracion, el `createUser` del ms-auth-service no puede guardar atributos como `tallerId` en los usuarios.

### Verificar configuracion actual

```bash
# Obtener token admin
TOKEN=$(curl -s -X POST "http://localhost:8180/realms/master/protocol/openid-connect/token" \
  -d "client_id=admin-cli" \
  -d "username=admin" \
  -d "password=<KEYCLOAK_ADMIN_PASSWORD>" \
  -d "grant_type=password" | python3 -c "import sys,json;print(json.load(sys.stdin)['access_token'])")

# Ver User Profile
curl -s "http://localhost:8180/admin/realms/skymek/users/profile" \
  -H "Authorization: Bearer $TOKEN" | python3 -m json.tool
```

### Restaurar configuracion (si se pierde)

Si se recrea el contenedor de Keycloak y se pierde la configuracion, ejecutar:

```bash
TOKEN=$(curl -s -X POST "http://localhost:8180/realms/master/protocol/openid-connect/token" \
  -d "client_id=admin-cli" \
  -d "username=admin" \
  -d "password=<KEYCLOAK_ADMIN_PASSWORD>" \
  -d "grant_type=password" | python3 -c "import sys,json;print(json.load(sys.stdin)['access_token'])")

curl -s -X PUT "http://localhost:8180/admin/realms/skymek/users/profile" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
  "attributes": [
    {
      "name": "username",
      "displayName": "${username}",
      "validations": {
        "length": {"min": 3, "max": 255},
        "username-prohibited-characters": {},
        "up-username-not-idn-homograph": {}
      },
      "permissions": {"view": ["admin", "user"], "edit": ["admin", "user"]},
      "multivalued": false
    },
    {
      "name": "email",
      "displayName": "${email}",
      "validations": {"email": {}, "length": {"max": 255}},
      "required": {"roles": ["user"]},
      "permissions": {"view": ["admin", "user"], "edit": ["admin", "user"]},
      "multivalued": false
    },
    {
      "name": "firstName",
      "displayName": "${firstName}",
      "validations": {"length": {"max": 255}, "person-name-prohibited-characters": {}},
      "required": {"roles": ["user"]},
      "permissions": {"view": ["admin", "user"], "edit": ["admin", "user"]},
      "multivalued": false
    },
    {
      "name": "lastName",
      "displayName": "${lastName}",
      "validations": {"length": {"max": 255}, "person-name-prohibited-characters": {}},
      "required": {"roles": ["user"]},
      "permissions": {"view": ["admin", "user"], "edit": ["admin", "user"]},
      "multivalued": false
    },
    {
      "name": "tallerId",
      "permissions": {"view": ["admin", "user"], "edit": ["admin"]},
      "multivalued": false
    },
    {
      "name": "profileType",
      "permissions": {"view": ["admin", "user"], "edit": ["admin"]},
      "multivalued": false
    },
    {
      "name": "ownerId",
      "permissions": {"view": ["admin", "user"], "edit": ["admin"]},
      "multivalued": false
    },
    {
      "name": "mechanicId",
      "permissions": {"view": ["admin", "user"], "edit": ["admin"]},
      "multivalued": false
    },
    {
      "name": "userId",
      "permissions": {"view": ["admin", "user"], "edit": ["admin"]},
      "multivalued": false
    }
  ],
  "groups": [
    {
      "name": "user-metadata",
      "displayHeader": "User metadata",
      "displayDescription": "Attributes, which refer to user metadata"
    }
  ]
}'
```

---

## Protocol Mappers (Cliente web-transport)

El cliente `web-transport` tiene protocol mappers que incluyen los atributos del usuario en el JWT access token:

| Mapper        | User Attribute | Token Claim   | En access_token |
|---------------|----------------|---------------|-----------------|
| `tallerId`    | `tallerId`     | `tallerId`    | si              |
| `profileType` | `profileType`  | `profileType` | si              |
| `ownerId`     | `ownerId`      | `ownerId`     | si              |
| `mechanicId`  | `mechanicId`   | `mechanicId`  | si              |
| `userId`      | `userId`       | `userId`      | si              |

Estos mappers ya existen en Keycloak. Si se necesita verificar:

```bash
# Obtener UUID del cliente web-transport
CLIENT_UUID=$(curl -s "http://localhost:8180/admin/realms/skymek/clients?clientId=web-transport" \
  -H "Authorization: Bearer $TOKEN" | python3 -c "import sys,json;print(json.load(sys.stdin)[0]['id'])")

# Ver mappers
curl -s "http://localhost:8180/admin/realms/skymek/clients/$CLIENT_UUID/protocol-mappers/models" \
  -H "Authorization: Bearer $TOKEN" | python3 -m json.tool
```

---

## Service Account (ms-auth-service)

El cliente `ms-auth-service` tiene un service account con el rol `realm-admin` para poder:
- Crear usuarios (`POST /users`)
- Asignar roles (`POST /users/{id}/role-mappings/realm`)
- Buscar usuarios (`GET /users?email=...`)
- Resetear passwords (`PUT /users/{id}/reset-password`)
- Eliminar usuarios (`DELETE /users/{id}`)

Si se pierde este rol, el ms-auth-service devolvera 403 al intentar registrar usuarios.

### Verificar roles del service account

```bash
# Obtener UUID del service account
SA_UUID=$(curl -s "http://localhost:8180/admin/realms/skymek/clients?clientId=ms-auth-service" \
  -H "Authorization: Bearer $TOKEN" | python3 -c "import sys,json;print(json.load(sys.stdin)[0]['id'])")

SA_USER=$(curl -s "http://localhost:8180/admin/realms/skymek/clients/$SA_UUID/service-account-user" \
  -H "Authorization: Bearer $TOKEN" | python3 -c "import sys,json;print(json.load(sys.stdin)['id'])")

# Ver realm roles asignados
curl -s "http://localhost:8180/admin/realms/skymek/users/$SA_USER/role-mappings/realm" \
  -H "Authorization: Bearer $TOKEN" | python3 -m json.tool
```

---

## Configuracion del Hostname (KC_HOSTNAME)

El contenedor de Keycloak debe tener estas variables de entorno:

```
KC_HOSTNAME=keycloak
KC_HOSTNAME_PORT=8080
KC_HOSTNAME_STRICT=false
```

Esto hace que el `issuer` del JWT sea `http://keycloak:8080/realms/skymek`, que es lo que los microservicios esperan internamente (via Docker network). Si se cambia, todos los microservicios rechazaran los tokens con 401.

---

## Direct Access Grants (Cliente web-transport)

El cliente `web-transport` debe tener `directAccessGrantsEnabled: true` para permitir login con `grant_type=password` desde el frontend.

### Verificar

```bash
curl -s "http://localhost:8180/admin/realms/skymek/clients?clientId=web-transport" \
  -H "Authorization: Bearer $TOKEN" | python3 -c "import sys,json;c=json.load(sys.stdin)[0];print('directAccessGrantsEnabled:', c.get('directAccessGrantsEnabled'))"
```

### Activar si esta deshabilitado

```bash
CLIENT_UUID=$(curl -s "http://localhost:8180/admin/realms/skymek/clients?clientId=web-transport" \
  -H "Authorization: Bearer $TOKEN" | python3 -c "import sys,json;print(json.load(sys.stdin)[0]['id'])")

curl -s -X PUT "http://localhost:8180/admin/realms/skymek/clients/$CLIENT_UUID" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"clientId":"web-transport","directAccessGrantsEnabled":true}'
```

---

## Troubleshooting

### "No se pudo identificar el taller"
- El JWT no contiene `tallerId`
- Verificar que el usuario tenga el atributo `tallerId` en Keycloak
- Verificar que el User Profile permita atributos custom (seccion arriba)
- Verificar que los protocol mappers existan en el cliente `web-transport`

### 403 en registro de usuarios
- El service account de `ms-auth-service` no tiene el rol `realm-admin`
- Ver seccion "Service Account" arriba

### 401 Unauthorized en microservicios
- Issuer mismatch: verificar `KC_HOSTNAME=keycloak` y `KC_HOSTNAME_PORT=8080`
- El token debe tener issuer `http://keycloak:8080/realms/skymek`

### unauthorized_client en login
- `directAccessGrantsEnabled` esta deshabilitado en el cliente
- Ver seccion "Direct Access Grants" arriba

### Atributos no se guardan en usuarios
- User Profile no tiene registrado el atributo
- Keycloak rechaza silenciosamente atributos no registrados
- Ver seccion "User Profile" y ejecutar el script de restauracion
