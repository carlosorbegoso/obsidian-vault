# Arquitectura del Sistema de Mantenimiento Vehicular Predictivo

## Resumen

El presente documento describe la arquitectura del Sistema de Mantenimiento Vehicular Predictivo, una plataforma distribuida basada en microservicios que integra tecnologías de Machine Learning para la predicción de necesidades de mantenimiento automotriz. El sistema implementa una arquitectura de capas que incluye: capa de presentación (Angular 20), capa de gateway (Apache APISIX), capa de microservicios (Quarkus/Java 21), capa de Machine Learning (FastAPI/Python), y capa de persistencia (PostgreSQL, Redis).

---

## 1. Arquitectura General del Sistema

La arquitectura del sistema sigue el patrón de microservicios, permitiendo escalabilidad independiente de cada componente y facilitando el mantenimiento y evolución del sistema (Richardson, 2018).

### Figura 1. Arquitectura del Sistema

**Archivo:** [diagrams/45-arquitectura-sistema-tesis.wsd](diagrams/45-arquitectura-sistema-tesis.wsd)

El diagrama de arquitectura presenta la organización del sistema en seis capas principales:

1. **Capa de Presentación:** Aplicación web desarrollada en Angular 20 con componentes standalone, visualización 3D mediante Three.js, y mapas interactivos con Leaflet.

2. **Capa de Gateway:** Apache APISIX como punto de entrada único, implementando autenticación JWT, rate limiting, y balanceo de carga.

3. **Capa de Microservicios:** Cuatro servicios Java implementados con Quarkus 3.x:
   - ms-auth-service (autenticación y autorización)
   - ms-data-service (gestión de datos maestros)
   - ms-workorder-service (órdenes de trabajo)
   - ms-notification-service (notificaciones multicanal)

4. **Capa de Machine Learning:** Dos servicios Python con FastAPI:
   - ms-prediction-service (predicciones en tiempo real)
   - ml-pipeline-xgb (entrenamiento de modelos XGBoost)

5. **Capa de Persistencia:** PostgreSQL 15 como base de datos relacional y Redis como sistema de caché.

6. **Capa de Seguridad:** HashiCorp Vault para gestión centralizada de secretos.

> *Nota APA:* Figura 1. *Arquitectura del Sistema de Mantenimiento Vehicular Predictivo*. Elaboración propia (2024). El diagrama representa la disposición de componentes siguiendo el patrón de arquitectura de microservicios.

---

## 2. Stack Tecnológico

### Figura 2. Stack Tecnológico

**Archivo:** [diagrams/57-tecnologias-stack-tesis.wsd](diagrams/57-tecnologias-stack-tesis.wsd)

| Capa | Tecnología | Versión | Justificación |
|------|------------|---------|---------------|
| Frontend | Angular | 20.x | Framework SPA con soporte standalone components y signals |
| API Gateway | Apache APISIX | 3.x | Gateway nativo en cloud con plugins extensibles |
| Backend | Quarkus | 3.x | Framework Java nativo con tiempos de inicio sub-segundo |
| Runtime Backend | Java | 21 LTS | Soporte para virtual threads (Project Loom) |
| ML API | FastAPI | 0.104+ | Framework Python de alto rendimiento con validación automática |
| ML Model | XGBoost | 2.0.2 | Algoritmo de gradient boosting con excelente rendimiento predictivo |
| Base de Datos | PostgreSQL | 15 | RDBMS con soporte JSONB y window functions |
| Cache | Redis | 7.x | Almacén en memoria para predicciones frecuentes |
| Secretos | HashiCorp Vault | 1.15 | Gestión segura de credenciales y configuración sensible |
| Contenedores | Docker/Podman | 24.x | Contenerización para despliegue consistente |

> *Nota APA:* Figura 2. *Stack Tecnológico del Sistema*. Elaboración propia (2024). La selección de tecnologías se basó en criterios de rendimiento, escalabilidad y mantenibilidad.

---

## 3. Infraestructura de Despliegue

### 3.1. Contenedores y Orquestación

**Figura 3. Despliegue en Contenedores**

**Archivo:** [diagrams/49-despliegue-contenedores-tesis.wsd](diagrams/49-despliegue-contenedores-tesis.wsd)

El sistema utiliza contenedores Docker/Podman para garantizar la consistencia entre ambientes de desarrollo y producción. Cada microservicio se empaqueta como una imagen de contenedor independiente, siguiendo las mejores prácticas de construcción multi-stage (Docker, 2023).

| Servicio | Puerto | Imagen Base |
|----------|--------|-------------|
| web-transport | 4200 | nginx:alpine |
| gateway-apisix | 9080 | apache/apisix:3.x |
| ms-auth-service | 8084 | quay.io/quarkus/ubi-quarkus-native |
| ms-data-service | 8081 | quay.io/quarkus/ubi-quarkus-native |
| ms-workorder-service | 8082 | quay.io/quarkus/ubi-quarkus-native |
| ms-notification-service | 8083 | quay.io/quarkus/ubi-quarkus-native |
| ms-prediction-service | 8000 | python:3.11-slim |
| ml-pipeline-xgb | 8002 | python:3.11-slim |
| PostgreSQL | 5432 | postgres:15 |
| Redis | 6379 | redis:7-alpine |
| HashiCorp Vault | 8200 | hashicorp/vault:1.15 |

> *Nota APA:* Figura 3. *Diagrama de Despliegue en Contenedores*. Elaboración propia (2024). Representa la infraestructura de contenedores del sistema.

### 3.2. Topología de Red

**Figura 4. Topología de Red**

**Archivo:** [diagrams/55-red-contenedores-tesis.wsd](diagrams/55-red-contenedores-tesis.wsd)

Los contenedores se comunican a través de una red bridge dedicada (tesis-network), permitiendo el aislamiento del tráfico y la resolución de nombres por servicio. Solo los puertos necesarios se exponen al host para acceso externo.

> *Nota APA:* Figura 4. *Topología de Red de Contenedores*. Elaboración propia (2024). Configuración de red bridge para comunicación entre servicios.

---

## 4. Gestión de Seguridad

### 4.1. HashiCorp Vault

**Figura 5. Estructura de Secretos**

**Archivo:** [diagrams/50-vault-secretos-tesis.wsd](diagrams/50-vault-secretos-tesis.wsd)

La gestión de secretos se centraliza en HashiCorp Vault, siguiendo el principio de "secrets as a service" (HashiCorp, 2023). Los secretos se organizan jerárquicamente por ambiente (dev/prod) y por microservicio.

**Figura 6. Flujo de Obtención de Secretos**

**Archivo:** [diagrams/53-flujo-secretos-vault-tesis.wsd](diagrams/53-flujo-secretos-vault-tesis.wsd)

El proceso de obtención de secretos utiliza AppRole authentication:

1. El microservicio se autentica con role_id y secret_id
2. Vault emite un token con tiempo de expiración
3. El servicio obtiene los secretos correspondientes a su política
4. La extensión Quarkus-Vault renueva automáticamente el token

> *Nota APA:* Figuras 5 y 6. *Gestión de Secretos con HashiCorp Vault*. Elaboración propia (2024). Implementación de gestión segura de credenciales siguiendo las mejores prácticas de seguridad.

---

## 5. Comunicación entre Microservicios

### Figura 7. Patrones de Comunicación

**Archivo:** [diagrams/52-comunicacion-microservicios-tesis.wsd](diagrams/52-comunicacion-microservicios-tesis.wsd)

La comunicación entre microservicios sigue el patrón de comunicación síncrona mediante REST/HTTP, implementado a través de Quarkus REST Client (SmallRye, 2023).

| Origen | Destino | Operación | Propósito |
|--------|---------|-----------|-----------|
| ms-data-service | ms-prediction-service | POST /predictions/predict | Obtener predicción de mantenimiento |
| ms-data-service | ms-notification-service | POST /notifications/send | Enviar alertas |
| ms-workorder-service | ms-data-service | GET /vehicles/{id} | Validar datos de vehículo |
| ms-workorder-service | ms-notification-service | POST /notifications/send | Confirmar citas |

> *Nota APA:* Figura 7. *Comunicación entre Microservicios*. Elaboración propia (2024). Flujos de comunicación REST entre componentes del backend.

---

## 6. Autenticación y Autorización

### Figura 8. Flujo de Autenticación JWT

**Archivo:** [diagrams/04-flujo-autenticacion.wsd](diagrams/04-flujo-autenticacion.wsd)

El sistema implementa autenticación basada en JSON Web Tokens (JWT) con el siguiente esquema:

- **Access Token:** Duración de 15 minutos, utilizado para autorizar peticiones
- **Refresh Token:** Duración de 7 días, utilizado para renovar el access token
- **Roles:** SYSTEM_ADMIN, TALLER_ADMIN, OWNER, MECHANIC

El flujo de autenticación sigue el patrón OAuth 2.0 con tokens de renovación (IETF, 2020).

> *Nota APA:* Figura 8. *Flujo de Autenticación JWT*. Elaboración propia (2024). Secuencia de autenticación utilizando tokens de acceso y renovación.

---

## 7. Modelo de Datos

### 7.1. Modelo de Dominio

**Figura 17. Modelo de Dominio**

**Archivo:** [diagrams/44-modelo-dominio.wsd](diagrams/44-modelo-dominio.wsd)

El modelo de dominio representa las entidades principales del sistema y sus relaciones, siguiendo los principios de Domain-Driven Design (Evans, 2003).

### 7.2. Diagrama Entidad-Relación

**Figura 18. Diagrama Entidad-Relación**

**Archivo:** [diagrams/48-entidades-relaciones-tesis.wsd](diagrams/48-entidades-relaciones-tesis.wsd)

La base de datos PostgreSQL implementa separación de esquemas por microservicio, siguiendo el patrón "Database per Service" (Richardson, 2018):

| Esquema | Microservicio | Tablas Principales |
|---------|---------------|-------------------|
| ms_auth | ms-auth-service | users, roles, permissions, refresh_tokens |
| ms_data | ms-data-service | owners, vehicles, talleres, mechanics |
| workorder | ms-workorder-service | service_orders, appointments, work_performed |
| notification | ms-notification-service | notifications, templates, schedules |
| ml_pipeline | ml-pipeline-xgb | training_runs, model_metadata, predictions |

> *Nota APA:* Figuras 17 y 18. *Modelo de Datos del Sistema*. Elaboración propia (2024). Representación del dominio de negocio y modelo relacional.

---

## 8. Componente de Machine Learning

### 8.1. Pipeline de Entrenamiento

**Figura 10. Pipeline de Machine Learning**

**Archivo:** [diagrams/47-pipeline-ml-tesis.wsd](diagrams/47-pipeline-ml-tesis.wsd)

El pipeline de entrenamiento implementa las siguientes fases:

1. **Extracción de Datos:** Consultas SQL con window functions para generar features temporales
2. **Ingeniería de Características:** Transformaciones temporales, de vehículo y estadísticas
3. **Validación:** Score mínimo de 70% de calidad de datos
4. **Entrenamiento:** XGBoost con early stopping (50 rondas sin mejora)
5. **Evaluación:** Métricas MAE, RMSE, R², MAPE
6. **Persistencia:** Almacenamiento de modelo y metadata

### 8.2. Flujo de Predicción

**Figura 11. Flujo de Predicción**

**Archivo:** [diagrams/46-flujo-prediccion-tesis.wsd](diagrams/46-flujo-prediccion-tesis.wsd)

El servicio de predicción implementa caché con Redis (TTL: 30 minutos) para optimizar tiempos de respuesta. Las métricas del modelo actual:

| Métrica | Valor |
|---------|-------|
| MAE (Error Absoluto Medio) | 4.1 días |
| R² (Coeficiente de Determinación) | 0.88 |
| Tiempo de respuesta (cache hit) | <100ms |

> *Nota APA:* Figuras 10 y 11. *Sistema de Machine Learning*. Elaboración propia (2024). Pipeline de entrenamiento y flujo de predicción utilizando XGBoost.

---

## 9. Referencias

Docker. (2023). *Best practices for writing Dockerfiles*. https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

Evans, E. (2003). *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley.

HashiCorp. (2023). *Vault Documentation*. https://developer.hashicorp.com/vault/docs

IETF. (2020). *RFC 6749 - The OAuth 2.0 Authorization Framework*. https://datatracker.ietf.org/doc/html/rfc6749

Richardson, C. (2018). *Microservices Patterns: With Examples in Java*. Manning Publications.

SmallRye. (2023). *REST Client Documentation*. https://smallrye.io/docs/

---

## 10. Índice de Figuras

Para el índice completo de figuras con formato APA, consultar: [DIAGRAMAS-TESIS.md](DIAGRAMAS-TESIS.md)

| # | Figura | Archivo |
|---|--------|---------|
| 1 | Arquitectura del Sistema | 45-arquitectura-sistema-tesis.wsd |
| 2 | Stack Tecnológico | 57-tecnologias-stack-tesis.wsd |
| 3 | Despliegue en Contenedores | 49-despliegue-contenedores-tesis.wsd |
| 4 | Topología de Red | 55-red-contenedores-tesis.wsd |
| 5 | Estructura de Secretos Vault | 50-vault-secretos-tesis.wsd |
| 6 | Flujo de Secretos | 53-flujo-secretos-vault-tesis.wsd |
| 7 | Comunicación Microservicios | 52-comunicacion-microservicios-tesis.wsd |
| 8 | Flujo de Autenticación | 04-flujo-autenticacion.wsd |
| 10 | Pipeline ML | 47-pipeline-ml-tesis.wsd |
| 11 | Flujo de Predicción | 46-flujo-prediccion-tesis.wsd |
| 17 | Modelo de Dominio | 44-modelo-dominio.wsd |
| 18 | Diagrama ER | 48-entidades-relaciones-tesis.wsd |

---

*Documento de Arquitectura - Sistema de Mantenimiento Vehicular Predictivo*
*Tesis 2024*
