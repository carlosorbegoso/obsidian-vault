# Capítulo 1 - Definición del Proyecto

## Sistema de Mantenimiento Vehicular Predictivo con Machine Learning

---

## Introducción

El presente capítulo describe la definición del proyecto de desarrollo de un Sistema de Mantenimiento Vehicular Predictivo, que utiliza técnicas de Machine Learning para anticipar las necesidades de mantenimiento de vehículos. Se analiza el contexto organizacional, la problemática identificada y se propone una solución tecnológica innovadora.

---

## 1.1 Definición del Problema

### 1.1.1 Objeto de Estudio

#### Organización Objetivo

**Sector:** Talleres de mantenimiento vehicular y propietarios de vehículos particulares.

**Contexto:** El mercado de talleres automotrices en Latinoamérica enfrenta desafíos significativos en la gestión eficiente del mantenimiento vehicular, con predominancia del mantenimiento correctivo sobre el preventivo.

#### Misión (del sistema)

Proporcionar una plataforma tecnológica que permita a talleres y propietarios de vehículos gestionar el mantenimiento vehicular de manera predictiva, reduciendo fallas imprevistas y optimizando costos.

#### Visión (del sistema)

Ser la plataforma líder en mantenimiento vehicular predictivo en Latinoamérica, transformando la forma en que se gestionan los vehículos mediante inteligencia artificial.

#### Objetivos Estratégicos

1. Reducir las fallas vehiculares imprevistas en un 40%
2. Disminuir los costos de mantenimiento correctivo en un 25%
3. Mejorar la satisfacción del cliente mediante notificaciones proactivas
4. Optimizar la productividad de los talleres en un 30%

#### Macroprocesos

```
┌─────────────────────────────────────────────────────────────────┐
│                    MACROPROCESOS DEL SISTEMA                     │
├─────────────────────────────────────────────────────────────────┤
│  1. Gestión de Vehículos                                        │
│     └─ Registro, actualización de kilometraje, historial        │
├─────────────────────────────────────────────────────────────────┤
│  2. Gestión de Mantenimiento                                    │
│     └─ Citas, órdenes de trabajo, ejecución de servicios        │
├─────────────────────────────────────────────────────────────────┤
│  3. Predicción de Mantenimiento (ML)                            │
│     └─ Entrenamiento, predicción, score de salud vehicular      │
├─────────────────────────────────────────────────────────────────┤
│  4. Gestión de Talleres                                         │
│     └─ Registro de talleres, servicios, mecánicos               │
├─────────────────────────────────────────────────────────────────┤
│  5. Notificaciones                                              │
│     └─ Alertas multicanal (email, SMS, push, WhatsApp)          │
└─────────────────────────────────────────────────────────────────┘
```

---

### 1.1.2 Campo de Acción

#### Breve Descripción

El campo de acción se centra en el **proceso de mantenimiento vehicular**, específicamente en la transición de un modelo reactivo (correctivo) a un modelo proactivo (predictivo) mediante el uso de Machine Learning.

#### Procesos del Negocio

| Proceso | Descripción | Actor Principal |
|---------|-------------|-----------------|
| Registro de vehículo | Alta de vehículos en el sistema | Propietario |
| Consulta de predicción | Obtención de predicción de mantenimiento | Propietario |
| Agendamiento de cita | Reserva de cita en taller | Propietario |
| Asignación de mecánico | Asignación de tareas al mecánico | Admin Taller |
| Ejecución de servicio | Realización del mantenimiento | Mecánico |
| Registro de trabajo | Documentación del servicio realizado | Mecánico |
| Calificación | Evaluación del servicio | Propietario |

#### Sistemas Automatizados Vinculados

| Sistema | Relación | Tipo |
|---------|----------|------|
| Sistema de notificaciones (Twilio) | Envío de SMS y WhatsApp | Externo |
| Firebase Cloud Messaging | Notificaciones push | Externo |
| OpenStreetMap | Geolocalización de talleres | Externo |
| Servidor SMTP | Envío de emails | Externo |

---

### 1.1.3 Situación Problemática

#### Análisis Crítico

**Problema Central:** Los propietarios de vehículos y talleres operan bajo un modelo de mantenimiento reactivo, lo que genera:

1. **Fallas imprevistas:** Los vehículos fallan sin previo aviso, causando inconvenientes y costos elevados.

2. **Mantenimiento correctivo costoso:** Las reparaciones de emergencia son 2-3 veces más costosas que el mantenimiento preventivo.

3. **Desconexión taller-cliente:** Los talleres no tienen visibilidad del estado de los vehículos de sus clientes.

4. **Gestión manual ineficiente:** Agendamiento de citas por teléfono, seguimiento en papel.

#### Diagrama de Ishikawa (Causa-Efecto)

```
                              ┌──────────────────────────────────┐
    PERSONAS                  │                                  │
    ├─ Falta de conocimiento  │    FALLAS VEHICULARES           │
    │  técnico del propietario│    IMPREVISTAS Y                │
    ├─ Olvido de fechas de    │    COSTOS ELEVADOS DE           │
    │  mantenimiento          │    MANTENIMIENTO                │
                              │                                  │
    PROCESOS                  └──────────────────────────────────┘
    ├─ No hay alertas                    ▲
    │  automáticas                       │
    ├─ Gestión manual         ───────────┘
    │  de citas

    TECNOLOGÍA                INFORMACIÓN
    ├─ Sin sistema de         ├─ No hay historial
    │  predicción               digitalizado
    ├─ Sin integración        ├─ Sin métricas de
    │  con talleres             salud vehicular
```

#### Análisis FODA

| Fortalezas | Debilidades |
|------------|-------------|
| Datos históricos disponibles | Sin sistema de predicción |
| Talleres con disposición al cambio | Procesos manuales |
| Clientes con smartphones | Falta de integración |

| Oportunidades | Amenazas |
|---------------|----------|
| ML para predicción | Competencia de apps genéricas |
| Notificaciones multicanal | Resistencia al cambio |
| Transformación digital | Costos de implementación |

---

### 1.1.4 Problemas a Resolver

| # | Problema | Impacto | Prioridad |
|---|----------|---------|-----------|
| P1 | No existe predicción de mantenimiento | Fallas imprevistas, costos elevados | Alta |
| P2 | Gestión manual de citas | Ineficiencia, errores, pérdida de clientes | Alta |
| P3 | Sin visibilidad del estado del vehículo | Mantenimiento reactivo | Alta |
| P4 | Comunicación deficiente taller-cliente | Pérdida de oportunidades | Media |
| P5 | Sin historial digitalizado | Decisiones sin datos | Media |

---

## 1.2 Definición de la Solución

### 1.2.1 Objetivos del Proyecto

#### Objetivo General

Desarrollar un Sistema de Mantenimiento Vehicular Predictivo que utilice técnicas de Machine Learning para anticipar las necesidades de mantenimiento, integrado con una plataforma de gestión de citas y notificaciones multicanal.

#### Objetivos Específicos

| # | Objetivo | Indicador de Medición |
|---|----------|----------------------|
| OE1 | Implementar un modelo de predicción de mantenimiento con XGBoost | MAE < 5 días, R² > 0.85 |
| OE2 | Desarrollar una plataforma web para gestión de vehículos y citas | 100% funcionalidades implementadas |
| OE3 | Integrar sistema de notificaciones multicanal | 4 canales operativos (email, SMS, push, WhatsApp) |
| OE4 | Reducir el tiempo de respuesta en consultas de predicción | < 500ms sin cache, < 100ms con cache |
| OE5 | Implementar dashboard de salud vehicular con visualización 3D | Score de salud por componente |

---

### 1.2.2 Indicadores de Éxito

| Indicador | Meta | Medición |
|-----------|------|----------|
| Precisión del modelo ML | MAE < 5 días | Evaluación con datos de prueba |
| Tiempo de respuesta API | < 200ms (p95) | Monitoreo de APM |
| Disponibilidad del sistema | 99.5% uptime | Logs del servidor |
| Usuarios activos | 30 talleres, 1000+ vehículos (Año 1) | Métricas de uso |
| Satisfacción del usuario | NPS > 50 | Encuestas |
| Reducción de fallas | 40% menos fallas imprevistas | Comparación histórica |

---

### 1.2.3 Factibilidad del Proyecto

#### Factibilidad Económica

> **Referencia:** Ver documento completo en [FACTIBILIDAD-PROYECTO.md](../FACTIBILIDAD-PROYECTO.md)

**Resumen:**

| Indicador | Valor |
|-----------|-------|
| Inversión Inicial | 202,055 USD |
| Período de Recuperación (Payback) | 2.5 años |
| ROI a 5 años | 220% |
| TIR | 45% |

#### Factibilidad Técnica

| Aspecto | Evaluación |
|---------|------------|
| Stack tecnológico | Open source maduro (Quarkus, Angular, FastAPI) |
| Infraestructura | VPS económicos (Hetzner/Contabo) |
| Expertise del equipo | Disponible |
| Escalabilidad | Arquitectura de microservicios |

**Conclusión:** El proyecto es **VIABLE** económica y técnicamente.

---

### 1.2.4 Propuesta Preliminar de la Solución

#### Descripción General

Sistema web compuesto por:

1. **Frontend:** Aplicación SPA en Angular 20 con:
   - Dashboard de salud vehicular con visualización 3D (Three.js)
   - Mapa de talleres cercanos (Leaflet + OpenStreetMap)
   - Gestión de citas y vehículos
   - Diseño responsive (mobile-first)

2. **Backend:** Arquitectura de microservicios con:
   - 4 servicios en Quarkus/Java 21 (Auth, Data, Workorder, Notification)
   - 2 servicios en FastAPI/Python (Prediction, ML Pipeline)
   - API Gateway (Apache APISIX)
   - Gestión de secretos (HashiCorp Vault)

3. **Machine Learning:**
   - Modelo XGBoost para predicción de mantenimiento
   - Pipeline de entrenamiento con Optuna (hyperparameter tuning)
   - Cache Redis para predicciones frecuentes

#### Alcances Funcionales

| Módulo | Funcionalidades |
|--------|-----------------|
| Vehículos | Registro, historial, actualización de kilometraje |
| Predicción ML | Consulta de predicción, score de salud, recomendaciones |
| Citas | Agendamiento, reprogramación, cancelación |
| Talleres | Búsqueda por ubicación, servicios, horarios |
| Órdenes de trabajo | Creación, asignación, seguimiento, cierre |
| Notificaciones | Email, SMS, push, WhatsApp, SSE tiempo real |

#### Alcances Técnicos

| Aspecto | Especificación |
|---------|----------------|
| Arquitectura | Microservicios, API REST, Event-driven |
| Base de datos | PostgreSQL 15 (5 esquemas) |
| Cache | Redis 7 |
| Contenedores | Podman 4.x |
| Seguridad | JWT, HashiCorp Vault, TLS 1.3 |
| CI/CD | GitHub Actions (planificado) |

#### Diagrama de Arquitectura de Alto Nivel

> **Referencia:** Ver diagrama completo en [TESIS-C4-01-contexto.wsd](../diagrams/TESIS-C4-01-contexto.wsd)

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Propietario │     │ Admin Taller│     │  Mecánico   │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                    ┌──────▼──────┐
                    │  Angular 20 │
                    │     SPA     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   APISIX    │
                    │   Gateway   │
                    └──────┬──────┘
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
┌──────▼──────┐     ┌──────▼──────┐     ┌──────▼──────┐
│   Quarkus   │     │   Quarkus   │     │   FastAPI   │
│  Services   │     │  Services   │     │  ML Service │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
       ┌──────▼──────┐ ┌───▼───┐ ┌─────▼─────┐
       │ PostgreSQL  │ │ Redis │ │   Vault   │
       └─────────────┘ └───────┘ └───────────┘
```

---

## Referencias del Capítulo

- Ver [FACTIBILIDAD-PROYECTO.md](../FACTIBILIDAD-PROYECTO.md) para análisis económico completo
- Ver [DIAGRAMAS-TESIS.md](../DIAGRAMAS-TESIS.md) para catálogo de diagramas
- Ver [diagrams/TESIS-C4-01-contexto.wsd](../diagrams/TESIS-C4-01-contexto.wsd) para diagrama de contexto

---

*Sistema de Mantenimiento Vehicular Predictivo - Capítulo 1: Definición del Proyecto*
