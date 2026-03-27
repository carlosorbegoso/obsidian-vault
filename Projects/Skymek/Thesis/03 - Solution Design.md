# Capítulo 3 - Diseño de la Solución

## Sistema de Mantenimiento Vehicular Predictivo con Machine Learning

---

## Introducción

Este capítulo presenta el diseño de la solución, incluyendo la incepción ágil con los artefactos del marco Scrum, y la arquitectura de software documentada según los modelos C4 y 4+1.

---

## 3.1 Incepción Ágil (Análisis y Diseño)

### 3.1.1 Visión del Producto (Product Vision Board)

| Elemento | Descripción |
|----------|-------------|
| **Visión** | Transformar el mantenimiento vehicular de reactivo a predictivo mediante ML |
| **Grupo Objetivo** | Propietarios de vehículos, Administradores de talleres, Mecánicos |
| **Necesidades** | Predicción de fallas, gestión de citas, notificaciones proactivas |
| **Producto** | Sistema web con predicción ML, gestión de citas y notificaciones multicanal |
| **Valor** | Reducción de fallas imprevistas (40%), ahorro en costos (25%), mayor satisfacción |
| **Objetivos de Negocio** | 30 talleres y 1000+ vehículos en Año 1, ROI 220% en 5 años |

---

### 3.1.2 Perfil del Usuario

| Rol | Características | Necesidades | Nivel Técnico |
|-----|-----------------|-------------|---------------|
| **Propietario de Vehículo** | Persona con 1+ vehículos, usa smartphone | Ver predicciones, agendar citas, recibir alertas | Básico |
| **Administrador de Taller** | Encargado de operaciones del taller | Gestionar citas, asignar mecánicos, ver reportes | Intermedio |
| **Mecánico** | Técnico automotriz | Ver tareas asignadas, registrar trabajos | Básico |
| **Administrador del Sistema** | Personal técnico IT | Configurar sistema, monitorear servicios | Avanzado |

---

### 3.1.3 Definición de Roles

#### Equipo Scrum

| Rol | Nombre | Responsabilidad |
|-----|--------|-----------------|
| **Product Owner** | [Por definir] | Priorizar backlog, validar incrementos |
| **Scrum Master** | [Por definir] | Facilitar ceremonias, remover impedimentos |
| **Development Team** | [Por definir] | Desarrollar incrementos del producto |

#### Información del Product Owner

| Campo | Valor |
|-------|-------|
| Nombres y Apellidos | [Por definir] |
| Rol en la organización | [Por definir] |
| Tiempo en la organización | [X meses] |
| Disponibilidad para el proyecto | [X horas/sprint] |

---

### 3.1.4 Diseño Preliminar de la Solución (Product Canvas)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PRODUCT CANVAS                                   │
├─────────────────────────────────────────────────────────────────────────┤
│ NOMBRE: Sistema de Mantenimiento Vehicular Predictivo                   │
├─────────────────────────────────────────────────────────────────────────┤
│ OBJETIVO:                                                               │
│ Reducir fallas vehiculares imprevistas mediante predicción ML           │
├──────────────────────────┬──────────────────────────────────────────────┤
│ MÉTRICAS                 │ PERSONAS                                     │
│ • MAE < 5 días           │ • Propietarios de vehículos                  │
│ • 99.5% uptime           │ • Administradores de talleres                │
│ • NPS > 50               │ • Mecánicos                                  │
│ • 40% menos fallas       │                                              │
├──────────────────────────┼──────────────────────────────────────────────┤
│ PROBLEMA                 │ SOLUCIÓN                                     │
│ • Mantenimiento reactivo │ • Predicción con XGBoost                     │
│ • Fallas imprevistas     │ • Dashboard de salud vehicular               │
│ • Gestión manual         │ • Agendamiento online                        │
│ • Sin alertas            │ • Notificaciones multicanal                  │
├──────────────────────────┴──────────────────────────────────────────────┤
│ TECNOLOGÍAS: Angular 20, Quarkus, FastAPI, XGBoost, PostgreSQL, Redis   │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 3.1.5 Prototipos de Alto Nivel (Épicas con Mayor Impacto)

#### Épica 1: Gestión de Vehículos y Predicción ML

**Pantallas principales:**
- Dashboard de salud vehicular (visualización 3D)
- Lista de vehículos del propietario
- Detalle de predicción por vehículo
- Historial de mantenimientos

#### Épica 2: Gestión de Citas

**Pantallas principales:**
- Búsqueda de talleres (mapa)
- Calendario de disponibilidad
- Formulario de agendamiento
- Mis citas (lista y detalle)

#### Épica 3: Gestión de Taller

**Pantallas principales:**
- Dashboard del taller
- Calendario de citas
- Órdenes de trabajo
- Asignación de mecánicos

---

### 3.1.6 Historias de Usuario

#### Formato de Historia de Usuario

```
COMO [rol]
NECESITO [necesidad funcional]
PARA [valor o beneficio para el negocio]
```

#### Listado de Historias de Usuario

| ID | Épica | Historia de Usuario | Prioridad |
|----|-------|---------------------|-----------|
| HU-01 | Vehículos | COMO propietario NECESITO registrar mi vehículo PARA obtener predicciones | Alta |
| HU-02 | Vehículos | COMO propietario NECESITO ver el score de salud PARA conocer el estado de mi vehículo | Alta |
| HU-03 | Predicción | COMO propietario NECESITO consultar predicción de mantenimiento PARA planificar servicios | Alta |
| HU-04 | Predicción | COMO propietario NECESITO ver componentes en riesgo PARA priorizar reparaciones | Alta |
| HU-05 | Citas | COMO propietario NECESITO buscar talleres cercanos PARA elegir dónde llevar mi vehículo | Alta |
| HU-06 | Citas | COMO propietario NECESITO agendar cita PARA reservar servicio en taller | Alta |
| HU-07 | Citas | COMO propietario NECESITO reprogramar cita PARA cambiar fecha/hora | Media |
| HU-08 | Citas | COMO propietario NECESITO cancelar cita PARA liberar el espacio | Media |
| HU-09 | Taller | COMO admin taller NECESITO ver citas del día PARA organizar el trabajo | Alta |
| HU-10 | Taller | COMO admin taller NECESITO asignar mecánico PARA distribuir tareas | Alta |
| HU-11 | Órdenes | COMO mecánico NECESITO ver mis tareas asignadas PARA saber qué hacer | Alta |
| HU-12 | Órdenes | COMO mecánico NECESITO registrar trabajo realizado PARA documentar servicio | Alta |
| HU-13 | Notificaciones | COMO propietario NECESITO recibir alertas de mantenimiento PARA no olvidar servicios | Alta |
| HU-14 | Notificaciones | COMO propietario NECESITO elegir canal de notificación PARA recibir alertas por mi medio preferido | Media |
| HU-15 | Auth | COMO usuario NECESITO registrarme PARA acceder al sistema | Alta |
| HU-16 | Auth | COMO usuario NECESITO iniciar sesión PARA usar el sistema | Alta |

#### Criterios de Aceptación (Ejemplo HU-03)

**HU-03:** COMO propietario NECESITO consultar predicción de mantenimiento PARA planificar servicios

| # | Criterio de Aceptación | Tipo |
|---|------------------------|------|
| CA-01 | El sistema muestra días estimados para próximo mantenimiento | Funcional |
| CA-02 | El tiempo de respuesta es < 500ms sin cache | Rendimiento |
| CA-03 | El tiempo de respuesta es < 100ms con cache | Rendimiento |
| CA-04 | Se muestra nivel de confianza de la predicción | Funcional |
| CA-05 | Se muestran componentes en riesgo con probabilidad | Funcional |

#### Validación INVEST

| HU | Independent | Negotiable | Valuable | Estimable | Small | Testable |
|----|-------------|------------|----------|-----------|-------|----------|
| HU-01 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| HU-02 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| HU-03 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| HU-04 | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| ... | ... | ... | ... | ... | ... | ... |

---

### 3.1.7 Product Backlog Base

#### Técnicas de Priorización

**MoSCoW:** Clasificación en Must have, Should have, Could have, Won't have.

| Prioridad | Descripción | HUs |
|-----------|-------------|-----|
| Must have | Esencial para MVP | HU-01, HU-03, HU-05, HU-06, HU-09, HU-15, HU-16 |
| Should have | Importante pero no crítico | HU-02, HU-04, HU-10, HU-11, HU-12, HU-13 |
| Could have | Deseable | HU-07, HU-08, HU-14 |

#### Técnica de Estimación

**Planning Poker** con escala Fibonacci: 1, 2, 3, 5, 8, 13, 21

#### Product Backlog Ordenado

| # | Épica | HU | Prioridad | Puntos | Dependencia | Sprint |
|---|-------|-----|-----------|--------|-------------|--------|
| 1 | Auth | HU-15 | Must | 5 | - | Sprint 1 |
| 2 | Auth | HU-16 | Must | 3 | HU-15 | Sprint 1 |
| 3 | Vehículos | HU-01 | Must | 5 | HU-16 | Sprint 1 |
| 4 | Predicción | HU-03 | Must | 8 | HU-01 | Sprint 1 |
| 5 | Vehículos | HU-02 | Should | 5 | HU-03 | Sprint 2 |
| 6 | Predicción | HU-04 | Should | 5 | HU-03 | Sprint 2 |
| 7 | Citas | HU-05 | Must | 5 | HU-16 | Sprint 2 |
| 8 | Citas | HU-06 | Must | 8 | HU-05 | Sprint 2 |
| 9 | Taller | HU-09 | Must | 5 | HU-06 | Sprint 3 |
| 10 | Taller | HU-10 | Should | 5 | HU-09 | Sprint 3 |
| 11 | Órdenes | HU-11 | Should | 3 | HU-10 | Sprint 3 |
| 12 | Órdenes | HU-12 | Should | 5 | HU-11 | Sprint 3 |
| 13 | Notificaciones | HU-13 | Should | 8 | HU-06 | Sprint 4 |
| 14 | Notificaciones | HU-14 | Could | 3 | HU-13 | Sprint 4 |
| 15 | Citas | HU-07 | Could | 3 | HU-06 | Sprint 4 |
| 16 | Citas | HU-08 | Could | 2 | HU-06 | Sprint 4 |

#### User Story Mapping

```
                    JOURNEY DEL PROPIETARIO
    ┌─────────┬─────────┬─────────┬─────────┬─────────┐
    │ Registro│ Consulta│ Agendar │ Servicio│ Feedback│
    │         │ Estado  │ Cita    │         │         │
    ├─────────┼─────────┼─────────┼─────────┼─────────┤
MVP1│ HU-15   │ HU-01   │         │         │         │
    │ HU-16   │ HU-03   │         │         │         │
    ├─────────┼─────────┼─────────┼─────────┼─────────┤
MVP2│         │ HU-02   │ HU-05   │         │         │
    │         │ HU-04   │ HU-06   │         │         │
    ├─────────┼─────────┼─────────┼─────────┼─────────┤
MVP3│         │         │ HU-07   │ HU-11   │ HU-13   │
    │         │         │ HU-08   │ HU-12   │ HU-14   │
    └─────────┴─────────┴─────────┴─────────┴─────────┘
```

---

## 3.2 Arquitectura de Software

> **Documento completo:** [03-2-ARQUITECTURA-SOFTWARE.md](03-2-ARQUITECTURA-SOFTWARE.md)

La arquitectura del sistema se documenta en un documento separado que incluye:

### 3.2.1 Arquitectura Técnica Base (PI-1-E1)

- Diagrama de Contexto (C4 Nivel 1)
- Diagrama de Contenedores (C4 Nivel 2)
- Arquitectura General del Sistema

### 3.2.2 Arquitectura Técnica Específica (PI-1: E2, E4; PI-2: E1, E2)

- Atributos de Calidad de Software (Bass et al., 2012)
- Vistas de Arquitectura (Modelo 4+1 de Kruchten, 1995)
- Modelo C4 Nivel 3: Componentes (Brown, 2023)
- Catálogo de 12 Diagramas de Arquitectura
- Evolución por Sprint

---

## 3.3 Distribución de Sprints

El proyecto se desarrolla en **4 Sprints** de 3 semanas cada uno, siguiendo el marco de trabajo Scrum.

### 3.3.1 Resumen de Sprints

| Sprint | Duración | Épicas Principales | Story Points |
|--------|----------|-------------------|--------------|
| Sprint 1 | 3 semanas | Autenticación, Vehículos, Predicción ML | 21 pts |
| Sprint 2 | 3 semanas | Dashboard Salud, Talleres, Citas | 23 pts |
| Sprint 3 | 3 semanas | Gestión Taller, Órdenes de Trabajo | 18 pts |
| Sprint 4 | 3 semanas | Notificaciones, Mejoras UX | 16 pts |

### 3.3.2 Sprint Backlog Inicial

#### Sprint 1: Fundamentos y Predicción ML

| HU | Descripción | Puntos | Prioridad |
|----|-------------|--------|-----------|
| HU-15 | Registro de usuarios | 5 | Must |
| HU-16 | Inicio de sesión | 3 | Must |
| HU-01 | Registro de vehículos | 5 | Must |
| HU-03 | Consulta de predicción ML | 8 | Must |
| **Total** | | **21** | |

**Objetivo del Sprint:** Implementar autenticación completa, registro de vehículos y servicio de predicción ML funcional.

#### Sprint 2: Ecosistema de Citas

| HU | Descripción | Puntos | Prioridad |
|----|-------------|--------|-----------|
| HU-02 | Score de salud vehicular | 5 | Should |
| HU-04 | Componentes en riesgo | 5 | Should |
| HU-05 | Búsqueda de talleres | 5 | Must |
| HU-06 | Agendamiento de citas | 8 | Must |
| **Total** | | **23** | |

**Objetivo del Sprint:** Dashboard de salud vehicular completo y sistema de agendamiento de citas.

#### Sprint 3: Gestión Operativa

| HU | Descripción | Puntos | Prioridad |
|----|-------------|--------|-----------|
| HU-09 | Vista de citas del día | 5 | Must |
| HU-10 | Asignación de mecánicos | 5 | Should |
| HU-11 | Tareas del mecánico | 3 | Should |
| HU-12 | Registro de trabajos | 5 | Should |
| **Total** | | **18** | |

**Objetivo del Sprint:** Panel completo para administradores de taller y mecánicos.

#### Sprint 4: Comunicación y Refinamiento

| HU | Descripción | Puntos | Prioridad |
|----|-------------|--------|-----------|
| HU-13 | Alertas de mantenimiento | 8 | Should |
| HU-14 | Preferencias de notificación | 3 | Could |
| HU-07 | Reprogramación de citas | 3 | Could |
| HU-08 | Cancelación de citas | 2 | Could |
| **Total** | | **16** | |

**Objetivo del Sprint:** Sistema de notificaciones multicanal y mejoras de usabilidad.

### 3.3.3 Definition of Done (DoD)

Según Schwaber y Sutherland (2020), el DoD asegura la calidad del incremento.

| Criterio | Descripción |
|----------|-------------|
| Código completo | Funcionalidad implementada según criterios de aceptación |
| Code review | Revisión por al menos un miembro del equipo |
| Pruebas unitarias | Cobertura mínima del 80% |
| Pruebas de integración | APIs probadas end-to-end |
| Documentación | Endpoints documentados en Swagger/OpenAPI |
| Sin bugs críticos | Funcionalidad sin errores bloqueantes |
| Desplegado | Incremento desplegado en ambiente de desarrollo |

### 3.3.4 Definition of Ready (DoR)

| Criterio | Descripción |
|----------|-------------|
| Historia clara | Formato "COMO... NECESITO... PARA..." completo |
| Criterios de aceptación | Mínimo 3 criterios definidos |
| Estimación | Story points asignados por el equipo |
| Dependencias | Identificadas y resueltas |
| Mockups | Diseño de UI disponible (si aplica) |

---

## Referencias del Capítulo

### Referencias Bibliográficas (APA 7ma edición)

Bass, L., Clements, P., & Kazman, R. (2012). *Software architecture in practice* (3ra ed.). Addison-Wesley Professional.

Brown, S. (2023). *The C4 model for visualising software architecture*. https://c4model.com

Clements, P., Bachmann, F., Bass, L., Garlan, D., Ivers, J., Little, R., Merson, P., Nord, R., & Stafford, J. (2010). *Documenting software architectures: Views and beyond* (2da ed.). Addison-Wesley Professional.

Cohn, M. (2004). *User stories applied: For agile software development*. Addison-Wesley Professional.

Kruchten, P. (1995). The 4+1 view model of architecture. *IEEE Software*, 12(6), 42-50. https://doi.org/10.1109/52.469759

Rubin, K. S. (2012). *Essential Scrum: A practical guide to the most popular agile process*. Addison-Wesley Professional.

Schwaber, K., & Sutherland, J. (2020). *The Scrum guide: The definitive guide to Scrum: The rules of the game*. https://scrumguides.org

### Diagramas de Arquitectura

Ver catálogo completo en [DIAGRAMAS-TESIS.md](../DIAGRAMAS-TESIS.md)

---

*Sistema de Mantenimiento Vehicular Predictivo - Capítulo 3: Diseño de la Solución*
