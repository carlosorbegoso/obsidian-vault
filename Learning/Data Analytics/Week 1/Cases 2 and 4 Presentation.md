# Taller en Clase N° 02
## Business Analytics & Big Data

**Casos de Estudio 2 y 4**

---

# CASO 2
## E-CommerceX
### Optimización de Marketing Digital

---

## Caso 2: Contexto

**Empresa:** E-CommerceX (comercio electrónico)

**Problema:**
- Baja tasa de conversión en el sitio web
- ROI insatisfactorio en campañas de marketing
- Segmentación ineficiente de clientes
- Falta de herramientas para medir resultados

---

## Caso 2: Solución Propuesta

### **Business Analytics (BA)**

| BI (Descriptivo) | BA (Predictivo) |
|------------------|-----------------|
| ¿Cuántas ventas hubo? | ¿Quién comprará? |
| ¿Qué productos se vendieron? | ¿Qué acción mejora la conversión? |

---

## Caso 2: ¿Por qué BA?

Los requerimientos exigen capacidades **predictivas**:

- **Segmentación avanzada** → Clustering, RFM Analysis
- **Análisis de embudo** → Identificar puntos de abandono
- **Personalización** → Algoritmos de recomendación
- **Atribución** → Medir impacto real de cada canal

---

## Caso 2: Acciones a Implementar

| # | Acción | Técnica |
|---|--------|---------|
| 1 | Integración de datos | Data Warehouse (BigQuery) |
| 2 | Segmentación | RFM + K-means |
| 3 | Análisis de embudo | Cohortes, funnels |
| 4 | Modelo de atribución | Multi-touch attribution |
| 5 | Personalización | Sistemas de recomendación |
| 6 | Experimentación | A/B Testing |

---

## Caso 2: ¿Aplicar Big Data?

### **Sí**

| Criterio | Justificación |
|----------|---------------|
| **Volumen** | Millones de registros (clickstream, transacciones) |
| **Variedad** | Datos estructurados y no estructurados |
| **Velocidad** | Personalización en tiempo real |

**Tecnologías:** Apache Spark, BigQuery, Kafka

---

## Caso 2: Diagrama de Solución

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  FUENTES DE     │     │   BUSINESS      │     │   RESULTADOS    │
│     DATOS       │────▶│   ANALYTICS     │────▶│                 │
│                 │     │                 │     │                 │
│ • Google Analytics    │ • Segmentación RFM    │ • + Conversión  │
│ • CRM                 │ • Análisis embudo     │ • + ROI         │
│ • Ads (Meta/Google)   │ • Atribución          │ • + Personaliz. │
│ • E-commerce          │ • Recomendaciones     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                              │
                        Big Data: Sí
```

---

# CASO 4
## TechSolutions
### Optimización de Gestión de RRHH

---

## Caso 4: Contexto

**Empresa:** TechSolutions (tecnología en crecimiento)

**Problema:**
- Alta rotación de personal
- Dificultades para evaluar desempeño
- Falta de transparencia en oportunidades de crecimiento
- Costos elevados de reclutamiento

---

## Caso 4: Solución Propuesta

### **Business Analytics (BA)**

| BI (Descriptivo) | BA (Predictivo) |
|------------------|-----------------|
| ¿Cuántos se fueron? | ¿Quién se irá? |
| ¿De qué área eran? | ¿Por qué se irán? |

---

## Caso 4: ¿Por qué BA?

Los requerimientos exigen capacidades **predictivas**:

- **Modelo predictivo de rotación** → Machine Learning
- **Análisis de causas** → Factores que influyen en la salida
- **Análisis de sentimiento** → NLP en comentarios
- **Correlaciones** → Desempeño vs retención

---

## Caso 4: Acciones a Implementar

| # | Acción | Técnica |
|---|--------|---------|
| 1 | Integración datos HR | Consolidar HRIS, evaluaciones |
| 2 | Modelo de rotación | ML (Random Forest, XGBoost) |
| 3 | Gestión desempeño | OKRs, feedback 360° |
| 4 | People Analytics | Dashboards para HR |
| 5 | Análisis de feedback | NLP en encuestas |
| 6 | Programa retención | Intervenciones basadas en datos |

---

## Caso 4: ¿Aplicar Big Data?

### **No necesariamente**

| Criterio | Justificación |
|----------|---------------|
| **Volumen** | Miles de empleados (moderado) |
| **Variedad** | Datos principalmente estructurados |
| **Velocidad** | Análisis mensual/trimestral |

**Tecnologías:** Python + SQL + Power BI

---

## Caso 4: Diagrama de Solución

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  FUENTES DE     │     │   BUSINESS      │     │   RESULTADOS    │
│     DATOS       │────▶│   ANALYTICS     │────▶│                 │
│                 │     │                 │     │                 │
│ • HRIS                │ • Modelo predictivo   │ • -25% Rotación │
│ • Evaluaciones        │ • Análisis sentimiento│ • +20% Product. │
│ • Encuestas           │ • People Analytics    │ • + Satisfacción│
│ • Capacitación        │ • Gestión desempeño   │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                              │
                        Big Data: No
```

---

# Conclusiones

---

## Aprendizaje 1: BI vs BA

| Business Intelligence | Business Analytics |
|-----------------------|-------------------|
| ¿Qué pasó? | ¿Qué pasará? |
| ¿Qué está pasando? | ¿Qué debemos hacer? |
| **Descriptivo** | **Predictivo y Prescriptivo** |

**La elección depende de las preguntas de negocio**

---

## Aprendizaje 2: Big Data

### No siempre es necesario

| Caso 2 (E-Commerce) | Caso 4 (RRHH) |
|---------------------|---------------|
| Alto volumen | Volumen moderado |
| Tiempo real | Batch mensual |
| **Big Data: Sí** | **Big Data: No** |

**Evaluar las 4 V's: Volumen, Variedad, Velocidad, Veracidad**

---

## Aprendizaje 3: Ciclo de Valor

```
Datos → Análisis → Insight → Decisión → Acción → Medición
                                                    │
                                                    ▼
                                              Retroalimentación
```

**Los insights sin acción son solo información interesante**

---

## Aprendizaje 4: Integración de Datos

> Sin datos integrados y de calidad, cualquier análisis será limitado o erróneo.

**Primer paso crítico en ambos casos:**
- Consolidar fuentes de datos
- Establecer gobernanza de datos
- Asegurar calidad

---

# Referencias

- Davenport, T. H., & Harris, J. G. (2007). *Competing on Analytics*. Harvard Business Review Press.
- Provost, F., & Fawcett, T. (2013). *Data Science for Business*. O'Reilly Media.
- Sharda, R., Delen, D., & Turban, E. (2020). *Business Intelligence, Analytics, and Data Science* (5th ed.). Pearson.

---

# ¿Preguntas?

---
