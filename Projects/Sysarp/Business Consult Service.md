# 🔐 Identity Consultation API

API profesional para consultas de RUC y DNI en Perú con sistema de autenticación, rate limiting y documentación interactiva.

---

## ⚠️ ACCIÓN REQUERIDA: SECURITY FIXES

**Se han implementado correcciones de seguridad críticas. Ver [SECURITY-FIXES.md](SECURITY-FIXES.md) para detalles completos.**

**DEBES REINICIAR la aplicación para que los cambios surtan efecto:**
```bash
kill $(lsof -ti:8050)
./gradlew quarkusDev
```

---

## 🚀 Características

- ✅ Consultas de RUC (empresas) y DNI (personas)
- 🔑 Sistema de API Keys para autenticación
- 📊 Rate limiting configurable por cliente
- 📚 Documentación pública personalizada
- 🌐 Portal web para gestión de tokens
- 📈 Tracking de uso y estadísticas
- 🔒 Seguridad con validación de API keys

## 🏃 Inicio Rápido

### 1. Configurar Base de Datos

Edita `src/main/resources/application.yml`:

```yaml
quarkus:
  datasource:
    username: tu_usuario
    password: tu_password
    reactive:
      url: postgresql://localhost:5432/tu_base_datos
```

### 2. Iniciar la Aplicación

```bash
./gradlew quarkusDev
```

La aplicación estará disponible en: http://localhost:8050

### 3. Acceder a las Interfaces

- **Portal Web**: http://localhost:8050/portal - Gestión de clientes y API keys
- **Documentación**: http://localhost:8050/docs - Documentación pública de la API
- **Swagger UI**: http://localhost:8050/swagger-ui - Solo en modo desarrollo

## 📖 Uso

### Registrar Cliente

1. Accede a http://localhost:8050/portal
2. Completa el formulario "Registrar Cliente"
3. Guarda el **Client ID** generado

### Generar API Key

1. En el portal, ingresa tu Client ID
2. Genera una nueva API Key
3. Copia y guarda la key (formato: `sk_...`)

### Hacer Consultas

```bash
# Consultar RUC
curl -X GET "http://localhost:8050/api/v1/ruc/20123456789" \
  -H "X-API-Key: tu_api_key_aqui"

# Consultar DNI
curl -X GET "http://localhost:8050/api/v1/dni/12345678" \
  -H "X-API-Key: tu_api_key_aqui"
```

## 🔌 Endpoints Principales

| Endpoint | Método | Descripción | Auth |
|----------|--------|-------------|------|
| `/api/v1/ruc/{ruc}` | GET | Consultar empresa por RUC | ✅ |
| `/api/v1/dni/{dni}` | GET | Consultar persona por DNI | ✅ |
| `/api/v1/portal/clients` | POST | Registrar nuevo cliente | ❌ |
| `/api/v1/portal/clients/{id}/api-keys` | POST | Generar API key | ❌ |
| `/portal` | GET | Portal web de gestión | ❌ |
| `/docs` | GET | Documentación pública | ❌ |

## 📊 Rate Limits

- **60 requests/minuto** por cliente
- **10,000 requests/día** por cliente

Los límites son configurables por cliente en la base de datos.

## 🔐 Seguridad

### Producción

- Swagger UI está **deshabilitado** en producción
- Solo se expone documentación pública personalizada
- Autenticación obligatoria con API Keys
- Rate limiting por cliente

### Desarrollo

Para habilitar Swagger en desarrollo:

```bash
./gradlew quarkusDev -Dquarkus.profile=dev
```

Luego accede a: http://localhost:8050/swagger-ui

## 🗄️ Base de Datos

### Inicializar Tablas

Ejecuta el script SQL:

```bash
psql -U tu_usuario -d tu_base_datos -f database-init.sql
```

### Estructura

- `clients` - Clientes registrados
- `api_keys` - API keys generadas
- `api_request_logs` - Log de peticiones

## 🐳 Docker

### Desarrollo Local

```bash
docker-compose up -d
```

### Producción

Configura las variables de entorno en `.env`:

```env
DB_USERNAME=usuario
DB_PASSWORD=password
DATABASE_URL=postgresql://host:5432/database
```

## 💻 Ejemplos de Integración

### JavaScript

```javascript
const API_KEY = 'sk_tu_api_key';
const BASE_URL = 'http://localhost:8050/api/v1';

async function consultarRUC(ruc) {
  const response = await fetch(`${BASE_URL}/ruc/${ruc}`, {
    headers: { 'X-API-Key': API_KEY }
  });
  return response.json();
}
```

### Python

```python
import requests

API_KEY = 'sk_tu_api_key'
BASE_URL = 'http://localhost:8050/api/v1'

def consultar_dni(dni):
    headers = {'X-API-Key': API_KEY}
    response = requests.get(f'{BASE_URL}/dni/{dni}', headers=headers)
    return response.json()
```

## 🛠️ Tecnologías

- **Quarkus** - Framework Java reactivo
- **PostgreSQL** - Base de datos
- **Hibernate Reactive** - ORM reactivo
- **SmallRye OpenAPI** - Documentación (solo dev)
- **Mutiny** - Programación reactiva

## 📝 Estructura del Proyecto

```
src/main/java/com/sysarp/
├── adapter/
│   ├── in/web/          # Controllers REST
│   │   ├── ConsultationResource.java
│   │   ├── PortalResource.java
│   │   ├── PortalWebResource.java
│   │   ├── DocsResource.java
│   │   └── filter/
│   │       └── ApiKeyAuthFilter.java
│   └── out/persistence/ # Repositorios
├── application/
│   ├── dto/             # DTOs y Mappers
│   ├── service/         # Servicios
│   │   ├── ApiKeyService.java
│   │   ├── RateLimitService.java
│   │   └── DniService.java
│   └── usecase/         # Casos de uso
└── domain/              # Entidades
    ├── client/
    │   ├── ClientEntity.java
    │   ├── ApiKeyEntity.java
    │   └── ApiRequestLogEntity.java
    ├── dni/
    └── location/
```

## 🆘 Soporte

Para consultas o soporte técnico:
- Email: support@sysarp.com
- Documentación: http://localhost:8050/docs

## 📄 Licencia

Uso exclusivo para clientes registrados.

---

Desarrollado con ❤️ por SysArp

./gradlew build \
-Dquarkus.package.type=native \
-Dquarkus.native.container-build=true \
-Dquarkus.native.container-runtime=docker \
-Dquarkus.native.container-runtime-options="--platform=linux/amd64"



docker buildx build \
--platform=linux/amd64 \
-f src/main/docker/Dockerfile.native-micro \
-t business-service-native:1.0 .

docker images | grep business

docker save business-service-native:1.0 -o business-service-native.tar

scp -i ~/.ssh/id_ed25519 business-service-native.tar deploy@167.172.117.133:/home/deploy/images

-- desde el server
docker load -i business-service-native.tar



