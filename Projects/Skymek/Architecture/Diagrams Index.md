# Índice de Diagramas - Sistema de Mantenimiento Vehicular Predictivo

Catálogo de diagramas técnicos optimizados para documento de tesis (APA 7ma ed.)

---

## Resumen de Diagramas

### Modelo C4 (Simon Brown, 2018)

| Fig. | Título | Archivo |
|------|--------|---------|
| C4-1 | Diagrama de Contexto | TESIS-C4-01-contexto.wsd |
| C4-2 | Diagrama de Contenedores | TESIS-C4-02-contenedores.wsd |
| C4-3 | Diagrama de Componentes | TESIS-C4-03-componentes.wsd |

### Modelo 4+1 (Kruchten, 1995)

| Fig. | Título | Archivo |
|------|--------|---------|
| 4+1-1 | Vista Lógica | TESIS-4+1-01-vista-logica.wsd |
| 4+1-2 | Vista de Procesos | TESIS-4+1-02-vista-procesos.wsd |
| 4+1-3 | Vista de Despliegue | TESIS-4+1-03-vista-despliegue.wsd |
| 4+1-4 | Vista de Escenarios | TESIS-4+1-04-vista-escenarios.wsd |

### Atributos de Calidad (Bass et al., 2012)

| Fig. | Título | Archivo |
|------|--------|---------|
| QA-1 | Atributos de Calidad | TESIS-CALIDAD-atributos.wsd |

### Diagramas Funcionales

| Fig. | Título | Archivo |
|------|--------|---------|
| 1 | Arquitectura del Sistema | TESIS-01-arquitectura-sistema.wsd |
| 2 | Despliegue en Contenedores | TESIS-02-despliegue-contenedores.wsd |
| 3 | Comunicación entre Servicios | TESIS-03-comunicacion-servicios.wsd |
| 4 | Seguridad y HashiCorp Vault | TESIS-04-seguridad-vault.wsd |
| 5 | Pipeline de Machine Learning | TESIS-05-pipeline-ml.wsd |
| 6 | Flujo de Predicción | TESIS-06-flujo-prediccion.wsd |
| 7 | Proceso de Negocio | TESIS-07-proceso-negocio.wsd |
| 8 | Casos de Uso | TESIS-08-casos-uso.wsd |
| 9 | Modelo de Datos | TESIS-09-modelo-datos.wsd |
| 10 | Autenticación JWT | TESIS-10-autenticacion.wsd |
| 11 | Sistema de Notificaciones | TESIS-11-notificaciones.wsd |
| 12 | Arquitectura Frontend | TESIS-12-frontend.wsd |

---

## Sección 3.2.1 - Arquitectura Técnica Base (Modelo C4)

### Figura C4-1. Diagrama de Contexto

**Archivo:** `diagrams/TESIS-C4-01-contexto.wsd`

Sistema con 3 actores (Propietario, Admin Taller, Mecánico) y 4 sistemas externos (SMTP, Twilio, Firebase, OpenStreetMap).

> *Nota.* Nivel 1 del modelo C4. Basado en Brown (2018).

---

### Figura C4-2. Diagrama de Contenedores

**Archivo:** `diagrams/TESIS-C4-02-contenedores.wsd`

11 contenedores: Angular 20 SPA, Apache APISIX, 4 microservicios Quarkus, 2 servicios FastAPI, PostgreSQL 15, Redis 7, HashiCorp Vault.

> *Nota.* Nivel 2 del modelo C4. Muestra tecnologías y protocolos de comunicación.

---

### Figura C4-3. Diagrama de Componentes

**Archivo:** `diagrams/TESIS-C4-03-componentes.wsd`

Estructura interna de ms-data-service, ms-prediction-service, ms-workorder-service y ml-pipeline-xgb con patrón Controller/Service/Repository.

> *Nota.* Nivel 3 del modelo C4. Patrones: Layered Architecture, Repository Pattern.

---

## Sección 3.2.2 - Arquitectura Técnica Específica (Modelo 4+1)

### Figura 4+1-1. Vista Lógica

**Archivo:** `diagrams/TESIS-4+1-01-vista-logica.wsd`

Descomposición en 4 capas: Presentación (Angular), Servicios (Quarkus), ML (FastAPI), Persistencia (PostgreSQL).

> *Nota.* Basado en Kruchten (1995) y Clements et al. (2010).

---

### Figura 4+1-2. Vista de Procesos

**Archivo:** `diagrams/TESIS-4+1-02-vista-procesos.wsd`

Concurrencia y comunicación: procesos JVM (50 threads), Python (async/await), HTTP/2, SSE, Fire-and-Forget.

> *Nota.* Patrones de comunicación síncrona y asíncrona.

---

### Figura 4+1-3. Vista de Despliegue

**Archivo:** `diagrams/TESIS-4+1-03-vista-despliegue.wsd`

Topología: Servidor Linux (8 CPU, 32GB RAM), Podman 4.x, red bridge, volúmenes persistentes.

> *Nota.* Infraestructura de contenedores con TLS 1.3.

---

### Figura 4+1-4. Vista de Escenarios

**Archivo:** `diagrams/TESIS-4+1-04-vista-escenarios.wsd`

6 escenarios arquitectónicos: Predicción (<500ms), Autenticación JWT, Agendar Cita, Reentrenamiento ML, Notificaciones, Recovery (<30s).

> *Nota.* Escenarios que validan atributos de calidad.

---

### Figura QA-1. Atributos de Calidad

**Archivo:** `diagrams/TESIS-CALIDAD-atributos.wsd`

6 atributos priorizados: Rendimiento, Disponibilidad, Seguridad (Alta), Modificabilidad, Testeabilidad, Usabilidad (Media).

> *Nota.* Basado en Bass, Clements & Kazman (2012) e ISO/IEC 25010.

---

## Diagramas Funcionales (Figuras 1-12)

### Figura 1. Arquitectura del Sistema

**Archivo:** `diagrams/TESIS-01-arquitectura-sistema.wsd`

Arquitectura de microservicios en 6 capas con tecnologías específicas.

> *Nota.* Patrón de microservicios con API Gateway.

---

### Figura 2. Despliegue en Contenedores

**Archivo:** `diagrams/TESIS-02-despliegue-contenedores.wsd`

Infraestructura Docker/Podman con 11 servicios y volúmenes persistentes.

> *Nota.* Configuración de red y puertos expuestos.

---

### Figura 3. Comunicación entre Servicios

**Archivo:** `diagrams/TESIS-03-comunicacion-servicios.wsd`

Flujos REST/HTTP entre microservicios y conexiones a PostgreSQL/Redis.

> *Nota.* Patrones de comunicación síncrona.

---

### Figura 4. Seguridad y HashiCorp Vault

**Archivo:** `diagrams/TESIS-04-seguridad-vault.wsd`

Estructura de secretos organizada por ambiente y servicio con AppRole.

> *Nota.* Gestión centralizada de credenciales.

---

### Figura 5. Pipeline de Machine Learning

**Archivo:** `diagrams/TESIS-05-pipeline-ml.wsd`

Pipeline: extracción SQL, ingeniería de features, XGBoost, evaluación (MAE: 4.1 días, R²: 0.88).

> *Nota.* Proceso de entrenamiento con Optuna.

---

### Figura 6. Flujo de Predicción

**Archivo:** `diagrams/TESIS-06-flujo-prediccion.wsd`

Secuencia: Frontend → Gateway → Prediction Service → Redis/PostgreSQL. <100ms con cache.

> *Nota.* Optimización con cache Redis (TTL 5 min).

---

### Figura 7. Proceso de Negocio

**Archivo:** `diagrams/TESIS-07-proceso-negocio.wsd`

Proceso end-to-end: registro → predicción → agendamiento → ejecución → calificación.

> *Nota.* 4 actores involucrados en el proceso.

---

### Figura 8. Casos de Uso

**Archivo:** `diagrams/TESIS-08-casos-uso.wsd`

24 casos de uso en 6 paquetes: Vehículos, Citas, Taller, Servicio, ML, Notificaciones.

> *Nota.* Funcionalidades por rol de usuario.

---

### Figura 9. Modelo de Datos

**Archivo:** `diagrams/TESIS-09-modelo-datos.wsd`

12 entidades principales en 5 esquemas PostgreSQL con relaciones.

> *Nota.* Diseño normalizado con índices optimizados.

---

### Figura 10. Autenticación JWT

**Archivo:** `diagrams/TESIS-10-autenticacion.wsd`

Flujo: login → JWT (15 min) + refresh (7 días) → validación en Gateway.

> *Nota.* Implementación stateless con bcrypt.

---

### Figura 11. Sistema de Notificaciones

**Archivo:** `diagrams/TESIS-11-notificaciones.wsd`

5 canales: Email (SMTP), SMS (Twilio), Push (Firebase), WhatsApp, SSE.

> *Nota.* Reintentos automáticos y preferencias de usuario.

---

### Figura 12. Arquitectura Frontend

**Archivo:** `diagrams/TESIS-12-frontend.wsd`

Angular 20 SPA con dominios, Three.js (3D), Leaflet (mapas), Tailwind CSS.

> *Nota.* Componentes standalone con Signals.

---

## Referencias (APA 7ma ed.)

Bass, L., Clements, P., & Kazman, R. (2012). *Software Architecture in Practice* (3rd ed.). Addison-Wesley.

Brown, S. (2018). *The C4 Model for Software Architecture*. https://c4model.com

Clements, P., et al. (2010). *Documenting Software Architectures: Views and Beyond* (2nd ed.). Addison-Wesley.

ISO/IEC. (2011). *ISO/IEC 25010:2011 - SQuaRE*.

Kruchten, P. (1995). The 4+1 View Model of Architecture. *IEEE Software*, 12(6), 42-50.

---

## Exportación para Word

**Recomendaciones:**
- Formato: PNG (300 DPI)
- Herramienta: VS Code + PlantUML Extension
- Tamaño: Los diagramas usan `scale 1.3-1.5` para buena legibilidad

**Archivados:** Diagramas anteriores en `diagrams/archive/`

---

*Sistema de Mantenimiento Vehicular Predictivo - Tesis 2024*
