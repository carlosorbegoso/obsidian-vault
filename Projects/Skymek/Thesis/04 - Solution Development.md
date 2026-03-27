# Capítulo 4 - Desarrollo de la Solución

## Sistema de Mantenimiento Vehicular Predictivo con Machine Learning

---

## Introducción

Este capítulo documenta el desarrollo iterativo e incremental del sistema a través de 4 Sprints, siguiendo el marco de trabajo Scrum. Cada Sprint incluye las ceremonias ágiles: Sprint Planning, Daily Standups, Sprint Review y Sprint Retrospective.

El desarrollo se divide en los siguientes documentos:

| Sprint | Documento | Épicas | Story Points |
|--------|-----------|--------|--------------|
| Sprint 1 | [04-1-SPRINT-1.md](04-1-SPRINT-1.md) | Auth, Vehículos, Predicción ML | 21 pts |
| Sprint 2 | [04-2-SPRINT-2.md](04-2-SPRINT-2.md) | Dashboard Salud, Talleres, Citas | 23 pts |
| Sprint 3 | [04-3-SPRINT-3.md](04-3-SPRINT-3.md) | Gestión Taller, Órdenes de Trabajo | 18 pts |
| Sprint 4 | [04-4-SPRINT-4.md](04-4-SPRINT-4.md) | Notificaciones, Mejoras UX | 16 pts |
| **Total** | | | **78 pts** |

---

## 4.1 Sprint 1: Fundamentos y Predicción ML

> **Documento completo:** [04-1-SPRINT-1.md](04-1-SPRINT-1.md)

### Resumen del Sprint

| Aspecto | Detalle |
|---------|---------|
| **Objetivo** | Implementar autenticación, registro de vehículos y predicción ML |
| **Duración** | 3 semanas |
| **Story Points** | 21 |

### Historias de Usuario

| ID | Historia de Usuario | Puntos |
|----|---------------------|--------|
| HU-15 | Registro de usuarios | 5 |
| HU-16 | Inicio de sesión | 3 |
| HU-01 | Registro de vehículos | 5 |
| HU-03 | Consulta de predicción ML | 8 |

---

## 4.2 Sprint 2: Ecosistema de Citas

> **Documento completo:** [04-2-SPRINT-2.md](04-2-SPRINT-2.md)

### Resumen del Sprint

| Aspecto | Detalle |
|---------|---------|
| **Objetivo** | Dashboard de salud vehicular y sistema de agendamiento |
| **Duración** | 3 semanas |
| **Story Points** | 23 |

### Historias de Usuario

| ID | Historia de Usuario | Puntos |
|----|---------------------|--------|
| HU-02 | Score de salud vehicular | 5 |
| HU-04 | Componentes en riesgo | 5 |
| HU-05 | Búsqueda de talleres | 5 |
| HU-06 | Agendamiento de citas | 8 |

---

## 4.3 Sprint 3: Gestión Operativa

> **Documento completo:** [04-3-SPRINT-3.md](04-3-SPRINT-3.md)

### Resumen del Sprint

| Aspecto | Detalle |
|---------|---------|
| **Objetivo** | Panel para administradores de taller y mecánicos |
| **Duración** | 3 semanas |
| **Story Points** | 18 |

### Historias de Usuario

| ID | Historia de Usuario | Puntos |
|----|---------------------|--------|
| HU-09 | Vista de citas del día | 5 |
| HU-10 | Asignación de mecánicos | 5 |
| HU-11 | Tareas del mecánico | 3 |
| HU-12 | Registro de trabajos | 5 |

---

## 4.4 Sprint 4: Comunicación y Refinamiento

> **Documento completo:** [04-4-SPRINT-4.md](04-4-SPRINT-4.md)

### Resumen del Sprint

| Aspecto | Detalle |
|---------|---------|
| **Objetivo** | Notificaciones multicanal y mejoras de usabilidad |
| **Duración** | 3 semanas |
| **Story Points** | 16 |

### Historias de Usuario

| ID | Historia de Usuario | Puntos |
|----|---------------------|--------|
| HU-13 | Alertas de mantenimiento | 8 |
| HU-14 | Preferencias de notificación | 3 |
| HU-07 | Reprogramación de citas | 3 |
| HU-08 | Cancelación de citas | 2 |

---

## 4.5 Resumen de Desarrollo

### 4.5.1 Métricas Consolidadas

| Sprint | Story Points | Velocidad | Bugs | Cobertura |
|--------|--------------|-----------|------|-----------|
| Sprint 1 | 21 | [X] | [X] | [X]% |
| Sprint 2 | 23 | [X] | [X] | [X]% |
| Sprint 3 | 18 | [X] | [X] | [X]% |
| Sprint 4 | 16 | [X] | [X] | [X]% |
| **Total** | **78** | **[Promedio]** | **[Total]** | **[Promedio]%** |

### 4.5.2 Burnup Chart del Proyecto

```
Story Points Completados vs Sprints

80 |                              *
   |                         *
60 |                    *
   |               *
40 |          *
   |     *
20 |*
   |
 0 |________________________
     S0   S1   S2   S3   S4

--- Planificado    * Real
```

### 4.5.3 Evolución del Product Backlog

| Sprint | HU Completadas | HU Restantes | % Avance |
|--------|----------------|--------------|----------|
| Sprint 1 | 4 | 12 | 25% |
| Sprint 2 | 8 | 8 | 50% |
| Sprint 3 | 12 | 4 | 75% |
| Sprint 4 | 16 | 0 | 100% |

### 4.5.4 Distribución de Funcionalidades

```
┌─────────────────────────────────────────────────────────────┐
│                 DISTRIBUCIÓN POR SPRINT                      │
├─────────────────────────────────────────────────────────────┤
│ Sprint 1 (27%)  ████████░░░░░░░░░░░░░░░░░░░░  Auth + ML     │
│ Sprint 2 (29%)  █████████░░░░░░░░░░░░░░░░░░░  Citas         │
│ Sprint 3 (23%)  ███████░░░░░░░░░░░░░░░░░░░░░  Taller        │
│ Sprint 4 (21%)  ██████░░░░░░░░░░░░░░░░░░░░░░  Notificaciones│
└─────────────────────────────────────────────────────────────┘
```

---

## Referencias del Capítulo

### Referencias Bibliográficas (APA 7ma edición)

Cohn, M. (2004). *User stories applied: For agile software development*. Addison-Wesley Professional.

Cohn, M. (2005). *Agile estimating and planning*. Prentice Hall.

Derby, E., & Larsen, D. (2006). *Agile retrospectives: Making good teams great*. Pragmatic Bookshelf.

Rubin, K. S. (2012). *Essential Scrum: A practical guide to the most popular agile process*. Addison-Wesley Professional.

Schwaber, K., & Sutherland, J. (2020). *The Scrum guide: The definitive guide to Scrum: The rules of the game*. https://scrumguides.org

Sutherland, J. (2014). *Scrum: The art of doing twice the work in half the time*. Crown Business.

---

*Sistema de Mantenimiento Vehicular Predictivo - Capítulo 4: Desarrollo de la Solución*
