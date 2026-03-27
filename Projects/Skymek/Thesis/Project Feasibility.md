# Factibilidad del Proyecto

## Sistema de Mantenimiento Vehicular Predictivo

---

## 1. Factibilidad Económica

Para realizar el análisis de factibilidad económica del proyecto "Sistema de Mantenimiento Vehicular Predictivo con Machine Learning", se han estimado los costos asociados al desarrollo, implementación y operación del sistema.

### 1.1 Inversión en Personal

**Tabla 1.** Estimación de Inversión Total del Personal

| ROL | CANTIDAD | SALARIO MENSUAL (USD) | DURACIÓN (MESES) | TOTAL (USD) |
|-----|----------|----------------------|------------------|-------------|
| Desarrollador Backend (Java/Quarkus) | 2 | 2,500 | 12 | 60,000 |
| Desarrollador Frontend (Angular) | 1 | 2,200 | 12 | 26,400 |
| Especialista en Machine Learning | 1 | 3,000 | 12 | 36,000 |
| Ingeniero DevOps | 1 | 2,800 | 12 | 33,600 |
| Diseñador UX/UI | 1 | 2,000 | 6 | 12,000 |
| Gestor de Proyecto | 1 | 2,500 | 12 | 30,000 |
| **Total Personal** | **7** | | | **198,000** |

> *Nota.* Descripción de roles del personal.

- **Desarrolladores Backend:** Ingenieros de software encargados del desarrollo de los 4 microservicios en Quarkus/Java 21.
- **Desarrollador Frontend:** Responsable de la aplicación Angular 20 con visualizaciones 3D y mapas.
- **Especialista en ML:** Profesional para desarrollar el modelo XGBoost y el pipeline de entrenamiento en Python/FastAPI.
- **Ingeniero DevOps:** Configuración de contenedores, HashiCorp Vault, API Gateway y CI/CD.
- **Diseñador UX/UI:** Para asegurar interfaces intuitivas y dashboard de salud vehicular.
- **Gestor de Proyecto:** Coordinación y supervisión del desarrollo.

---

### 1.2 Inversión en Hardware

**Tabla 2.** Estimación de Inversión Total del Hardware Necesario

| ITEM | CANTIDAD | COSTO UNITARIO (USD) | TOTAL (USD) |
|------|----------|---------------------|-------------|
| VPS Desarrollo (4 vCPU, 8GB RAM) - DigitalOcean/Hetzner | 1 | 480/año | 480 |
| VPS Producción (8 vCPU, 16GB RAM) - Hetzner/Contabo | 1 | 600/año | 600 |
| Estaciones de Trabajo (Desarrollo) | 3 | 800 | 2,400 |
| Equipos de Red (Router básico) | 1 | 200 | 200 |
| **Total Hardware** | | | **3,680** |

> *Nota.* Se utilizan VPS (servidores virtuales) en lugar de servidores físicos para reducir costos. Hetzner y Contabo ofrecen excelente relación precio/rendimiento para startups.

---

### 1.3 Inversión en Servicios

**Tabla 3.** Estimación de Inversión Total de Servicios Necesarios

| SERVICIO | CANTIDAD | COSTO ANUAL (USD) | TOTAL (USD) |
|----------|----------|-------------------|-------------|
| Dominio (.com) | 1 | 15 | 15 |
| Certificado SSL (Let's Encrypt) | 1 | 0 | 0 |
| Twilio (SMS) - Pay as you go | 1 | 300 | 300 |
| Firebase Cloud Messaging | 1 | 0 | 0 |
| SMTP (Mailgun - 5K emails gratis/mes) | 1 | 0 | 0 |
| HashiCorp Vault (Self-hosted) | 1 | 0 | 0 |
| Backup Storage (Backblaze B2) | 1 | 60 | 60 |
| **Total Servicios** | | | **375** |

> *Nota.* Se priorizan servicios gratuitos o de bajo costo: Let's Encrypt (SSL gratis), Mailgun tier gratuito, Firebase FCM gratis, Vault self-hosted.

---

### 1.4 Inversión en Software y Licencias

**Tabla 4.** Estimación de Licencias de Software

| SOFTWARE | TIPO | COSTO ANUAL (USD) |
|----------|------|-------------------|
| Quarkus Framework | Open Source | 0 |
| Angular Framework | Open Source | 0 |
| FastAPI / Python | Open Source | 0 |
| XGBoost / Scikit-learn | Open Source | 0 |
| PostgreSQL 15 | Open Source | 0 |
| Redis 7 | Open Source | 0 |
| Apache APISIX | Open Source | 0 |
| HashiCorp Vault | Open Source | 0 |
| Podman | Open Source | 0 |
| IDE (IntelliJ IDEA Community / VS Code) | Open Source | 0 |
| Herramientas de Testing | Open Source | 0 |
| **Total Licencias** | | **0** |

> *Nota.* El stack tecnológico del proyecto utiliza principalmente software open source, minimizando costos de licenciamiento.

---

### 1.5 Gastos Operativos Anuales

**Tabla 5.** Estimación de Gastos Anuales del Sistema Implementado

| GASTO | COSTO ANUAL (USD) |
|-------|-------------------|
| Mantenimiento de Software | 6,000 |
| Soporte Técnico (medio tiempo) | 9,000 |
| VPS Producción + Desarrollo | 1,080 |
| Servicios de Notificaciones (Twilio) | 600 |
| Dominio y Backups | 75 |
| Actualizaciones y Mejoras | 3,000 |
| Reentrenamiento del Modelo ML | 1,500 |
| **Total Gastos Anuales** | **21,255** |

> *Nota.* El reentrenamiento del modelo ML se realiza trimestralmente. Soporte técnico puede ser el mismo equipo de desarrollo en fase inicial.

---

### 1.6 Resumen de Inversión Inicial

**Tabla 6.** Estimación de Inversión Total

| CATEGORÍA | COSTO TOTAL (USD) |
|-----------|-------------------|
| Inversión en Personal | 198,000 |
| Inversión en Hardware (VPS + equipos) | 3,680 |
| Inversión en Servicios | 375 |
| Inversión en Licencias | 0 |
| **TOTAL INVERSIÓN INICIAL** | **202,055** |

> *Nota.* La inversión inicial estimada es de **202,055 USD** para el desarrollo completo del sistema durante 12 meses. Se optimizan costos usando VPS económicos y servicios open source/gratuitos.

---

### 1.7 Modelo de Ingresos

El sistema de Mantenimiento Vehicular Predictivo puede generar ingresos mediante:

1. **Suscripción por Taller:** Tarifa mensual por acceso al sistema
2. **Suscripción por Vehículo:** Tarifa por vehículo registrado con predicciones ML
3. **Implementación:** Tarifa única por configuración inicial

**Tabla 7.** Supuestos del Modelo de Negocio

| CONCEPTO | VALOR |
|----------|-------|
| Tarifa mensual por Taller (Plan Básico) | 50 USD |
| Tarifa mensual por Taller (Plan Premium) | 80 USD |
| Tarifa mensual por Vehículo (propietario) | 3 USD |
| Tarifa de Implementación por Taller | 200 USD |
| Talleres iniciales (Año 1) | 30 |
| Incremento anual de Talleres | 20 |
| Vehículos promedio por Taller | 150 |
| Propietarios con suscripción premium | 25% |
| Porcentaje talleres Plan Premium | 40% |

> *Nota.* Tarifas accesibles para talleres pequeños y medianos. Plan Básico incluye gestión de citas y vehículos. Plan Premium incluye predicciones ML y reportes avanzados.

---

### 1.8 Proyección de Ingresos (5 años)

**Tabla 8.** Ingresos Esperados

| AÑO | TALLERES | INGRESO TALLERES (USD) | VEHÍCULOS PREMIUM | INGRESO VEHÍCULOS (USD) | IMPLEMENTACIÓN (USD) | TOTAL ANUAL (USD) |
|-----|----------|------------------------|-------------------|-------------------------|----------------------|-------------------|
| 1 | 30 | 23,040 | 1,125 | 40,500 | 6,000 | 69,540 |
| 2 | 50 | 38,400 | 1,875 | 67,500 | 4,000 | 109,900 |
| 3 | 70 | 53,760 | 2,625 | 94,500 | 4,000 | 152,260 |
| 4 | 90 | 69,120 | 3,375 | 121,500 | 4,000 | 194,620 |
| 5 | 110 | 84,480 | 4,125 | 148,500 | 4,000 | 236,980 |

> *Nota.* Cálculos: Talleres = (60% × 50 + 40% × 80) × 12 meses. Vehículos = 25% × talleres × 150 × 3 USD × 12 meses.

---

### 1.9 Análisis de Recuperación de Inversión

**Tabla 9.** Flujo de Caja y Recuperación de Inversión

| AÑO | INGRESOS (USD) | GASTOS OPERATIVOS (USD) | FLUJO NETO (USD) | FLUJO ACUMULADO (USD) |
|-----|----------------|-------------------------|------------------|----------------------|
| 0 | 0 | 202,055 | -202,055 | -202,055 |
| 1 | 69,540 | 21,255 | 48,285 | -153,770 |
| 2 | 109,900 | 22,318 | 87,582 | -66,188 |
| 3 | 152,260 | 23,434 | 128,826 | 62,638 |
| 4 | 194,620 | 24,606 | 170,014 | 232,652 |
| 5 | 236,980 | 25,836 | 211,144 | 443,796 |

> *Nota.* Los gastos operativos incluyen un incremento anual del 5% por inflación.

---

### 1.10 Indicadores Financieros

**Tabla 10.** Indicadores de Viabilidad Económica

| INDICADOR | VALOR |
|-----------|-------|
| **Inversión Inicial** | 202,055 USD |
| **Período de Recuperación (Payback)** | 2.5 años |
| **ROI a 5 años** | 220% |
| **VAN (Tasa 12%)** | 248,500 USD |
| **TIR** | 45% |

> *Nota.* El proyecto presenta indicadores financieros favorables con recuperación de inversión en aproximadamente 2.5 años. La optimización de costos en infraestructura (VPS en lugar de servidores físicos) y el uso de servicios open source mejoran significativamente la rentabilidad.

---

## 2. Factibilidad Técnica

### 2.1 Recursos de Software

**Tabla 11.** Recursos de Software del Proyecto

| RECURSO | DESCRIPCIÓN | TECNOLOGÍAS |
|---------|-------------|-------------|
| Framework Backend | Desarrollo de microservicios REST | Quarkus 3.x, Java 21 |
| Framework Frontend | Aplicación SPA responsive | Angular 20, TypeScript 5.x |
| Framework ML | Pipeline y API de predicciones | FastAPI, Python 3.11 |
| Modelo ML | Algoritmo de predicción | XGBoost 2.0.2, Scikit-learn |
| Base de Datos | Almacenamiento relacional | PostgreSQL 15 |
| Cache | Almacenamiento en memoria | Redis 7 |
| API Gateway | Enrutamiento y seguridad | Apache APISIX |
| Gestión de Secretos | Credenciales y certificados | HashiCorp Vault 1.15 |
| Contenedores | Virtualización de servicios | Podman 4.x / Docker |
| Visualización 3D | Dashboard de salud vehicular | Three.js |
| Mapas | Geolocalización de talleres | Leaflet + OpenStreetMap |
| Estilos | Framework CSS | Tailwind CSS 4.x |

---

### 2.2 Recursos de Hardware

**Tabla 12.** Recursos de Hardware y Tecnología

| RECURSO | DESCRIPCIÓN | ESPECIFICACIONES |
|---------|-------------|------------------|
| VPS Producción (Hetzner/Contabo) | Alojamiento del sistema | 8 vCPU, 16GB RAM, 200GB SSD |
| VPS Desarrollo (DigitalOcean/Hetzner) | Ambiente de pruebas | 4 vCPU, 8GB RAM, 80GB SSD |
| Estaciones de Trabajo | Equipos para desarrollo | i5/Ryzen 5, 16GB RAM, SSD |
| Equipos de Usuario | Acceso al sistema | Navegador web moderno |
| Dispositivos Móviles | Acceso responsive | Smartphones, tablets |

> *Nota.* Se recomienda VPS de proveedores europeos (Hetzner, Contabo) por su excelente relación precio/rendimiento comparado con AWS/GCP.

---

### 2.3 Recursos de Comunicaciones

**Tabla 13.** Recursos de Comunicaciones

| RECURSO | DESCRIPCIÓN | PROVEEDOR/TECNOLOGÍA |
|---------|-------------|----------------------|
| Internet | Conectividad del servidor | Fibra óptica 100+ Mbps |
| Email | Notificaciones por correo | SendGrid / SMTP |
| SMS | Alertas de mantenimiento | Twilio |
| Push Notifications | Alertas móviles | Firebase Cloud Messaging |
| WhatsApp | Mensajería instantánea | Twilio WhatsApp API |
| Real-time | Actualizaciones en vivo | Server-Sent Events (SSE) |

---

### 2.4 Evaluación Técnica

**Tabla 14.** Evaluación Técnica del Proyecto

| ASPECTO | EVALUACIÓN | ESTADO |
|---------|------------|--------|
| **Disponibilidad de Recursos** | Todas las tecnologías son open source o tienen planes gratuitos | ✅ Viable |
| **Compatibilidad** | Stack tecnológico probado y compatible | ✅ Viable |
| **Escalabilidad** | Arquitectura de microservicios permite escalar horizontalmente | ✅ Viable |
| **Rendimiento** | Predicciones ML en <500ms, APIs en <200ms | ✅ Viable |
| **Seguridad** | JWT, Vault, TLS 1.3, bcrypt implementados | ✅ Viable |
| **Mantenibilidad** | Código modular, CI/CD, documentación completa | ✅ Viable |
| **Expertise del Equipo** | Equipo con experiencia en tecnologías seleccionadas | ✅ Viable |

---

## 3. Factibilidad Operativa

### 3.1 Usuarios del Sistema

**Tabla 15.** Perfil de Usuarios

| ROL | DESCRIPCIÓN | NIVEL TÉCNICO |
|-----|-------------|---------------|
| Propietario de Vehículo | Registra vehículos, consulta predicciones, agenda citas | Básico |
| Administrador de Taller | Gestiona taller, mecánicos, servicios y reportes | Intermedio |
| Mecánico | Recibe tareas, registra trabajos realizados | Básico |
| Administrador del Sistema | Configuración, monitoreo, mantenimiento | Avanzado |

---

### 3.2 Beneficios Operativos

**Tabla 16.** Beneficios del Sistema

| BENEFICIO | DESCRIPCIÓN | IMPACTO |
|-----------|-------------|---------|
| Reducción de fallas | Predicción anticipada de mantenimiento | 40% menos fallas imprevistas |
| Optimización de costos | Mantenimiento preventivo vs correctivo | 25% ahorro en reparaciones |
| Mejor experiencia | Notificaciones proactivas al usuario | Mayor satisfacción del cliente |
| Eficiencia operativa | Asignación automática de mecánicos | 30% más productividad |
| Decisiones basadas en datos | Dashboard con métricas y predicciones | Mejor planificación |

---

## 4. Conclusiones de Factibilidad

### 4.1 Resumen Ejecutivo

| TIPO DE FACTIBILIDAD | RESULTADO | OBSERVACIÓN |
|---------------------|-----------|-------------|
| **Económica** | ✅ VIABLE | ROI 220%, Payback 2.5 años, TIR 45% |
| **Técnica** | ✅ VIABLE | Stack open source, VPS económicos |
| **Operativa** | ✅ VIABLE | Interfaces intuitivas, beneficios claros |

### 4.2 Recomendaciones

1. **Fase piloto:** Iniciar con 5-10 talleres para validar el modelo de negocio
2. **MVP:** Desarrollar versión mínima viable en 6 meses
3. **Iteración:** Mejorar modelo ML con datos reales de operación
4. **Escalamiento:** Expandir a nuevas regiones según demanda

---

*Sistema de Mantenimiento Vehicular Predictivo - Análisis de Factibilidad 2025*
