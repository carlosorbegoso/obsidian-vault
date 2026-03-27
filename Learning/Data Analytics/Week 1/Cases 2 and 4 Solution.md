# Taller en Clase N° 02 - Business Analytics BigData

## Solución Casos de Estudio 2 y 4

---

## Diagramas de Referencia

| Caso | Diagrama |
|------|----------|
| Caso 2 - E-CommerceX | [caso2-solucion.puml](diagrams/caso2-solucion.puml) |
| Caso 4 - TechSolutions | [caso4-solucion.puml](diagrams/caso4-solucion.puml) |

> **Nota:** Para visualizar, usar [PlantUML Online Server](https://www.plantuml.com/plantuml/uml/) o extensión de VS Code "PlantUML".

---

## CASO 2: Optimización de Marketing Digital en E-CommerceX

### Diagrama
> Ver: [diagrams/caso2-solucion.puml](diagrams/caso2-solucion.puml)

### 1. Tipo de solución: **Business Analytics (BA)**

### 2. Motivo de la decisión

BA es la opción correcta porque el caso requiere **capacidades predictivas y prescriptivas**, no solo descriptivas:

- **Segmentación de clientes** usando técnicas avanzadas (clustering, RFM analysis) basadas en comportamiento de compra y ciclo de vida del cliente.
- **Análisis predictivo** para anticipar qué clientes tienen mayor probabilidad de conversión y cuáles están en riesgo de abandonar.
- **Modelos de atribución** para determinar el impacto real de cada canal de marketing en las conversiones.
- **Personalización** del contenido requiere algoritmos de recomendación que aprenden del comportamiento del usuario.

Business Intelligence solo mostraría datos históricos (cuántas ventas hubo, qué productos se vendieron), mientras que Business Analytics permite responder preguntas como "¿qué clientes tienen mayor probabilidad de comprar?" y "¿qué acciones específicas debo tomar para mejorar la tasa de conversión?".

Además, los requerimientos explícitamente mencionan:

- Técnicas de segmentación basadas en comportamiento
- Análisis de embudo de ventas para identificar puntos críticos
- Personalización de contenido
- Atribución de conversiones a canales

Todas estas son capacidades propias de Business Analytics.

### 3. Acciones para implementar la solución

| Fase | Acción | Descripción |
|------|--------|-------------|
| 1 | **Integración de datos** | Consolidar datos de Google Analytics, CRM, plataformas de publicidad (Meta Ads, Google Ads), email marketing y sistema de e-commerce en un data warehouse centralizado |
| 2 | **Segmentación avanzada** | Implementar análisis RFM (Recency, Frequency, Monetary) y algoritmos de clustering (K-means) para agrupar clientes según su comportamiento y valor |
| 3 | **Análisis de embudo** | Mapear el customer journey completo e identificar puntos de abandono mediante análisis de cohortes y funnels de conversión |
| 4 | **Modelo de atribución** | Implementar atribución multi-touch (data-driven attribution) para medir el ROI real de cada canal y campaña de marketing |
| 5 | **Motor de personalización** | Desarrollar sistemas de recomendación (collaborative filtering, content-based) para personalizar productos y contenido |
| 6 | **Framework de experimentación** | Establecer A/B testing continuo para optimizar landing pages, emails y campañas |

**Herramientas sugeridas:**

- Google Analytics 4 + BigQuery para análisis web
- Python (scikit-learn, pandas) para modelos de segmentación
- Tableau/Power BI para visualización
- Plataformas de Customer Data Platform (CDP) como Segment

### 4. ¿Aplicar Big Data?

**Sí, es recomendable aplicar Big Data**, fundamentado en las siguientes razones:

| Característica | Justificación |
|----------------|---------------|
| **Volumen** | El comercio electrónico genera grandes volúmenes de datos: millones de registros de clickstream, logs de navegación, transacciones diarias, interacciones en redes sociales |
| **Variedad** | Alta diversidad de datos: estructurados (ventas, inventario) y no estructurados (comentarios de clientes, imágenes de productos, logs de comportamiento) |
| **Velocidad** | Se requiere procesamiento en tiempo real para personalización inmediata y recomendaciones en el momento de la visita |
| **Veracidad** | Necesidad de limpiar y validar datos provenientes de múltiples fuentes |

**Tecnologías recomendadas:**

- Apache Spark para procesamiento distribuido
- Plataformas cloud (AWS Redshift, Google BigQuery, Azure Synapse)
- Apache Kafka para streaming de datos en tiempo real

---

## CASO 4: Optimización de Gestión de RRHH en TechSolutions

### Diagrama
> Ver: [diagrams/caso4-solucion.puml](diagrams/caso4-solucion.puml)

### 1. Tipo de solución: **Business Analytics (BA)**

### 2. Motivo de la decisión

Business Analytics es necesario porque los requerimientos van más allá del reporting tradicional de BI:

- **Análisis predictivo de rotación**: Identificar empleados en riesgo de abandonar la empresa usando modelos de machine learning, no solo reportar cuántos se fueron.
- **Análisis de causas raíz**: Determinar qué factores realmente influyen en la rotación (compensación, clima laboral, oportunidades de crecimiento, relación con el jefe).
- **Análisis de sentimiento**: Procesar comentarios y feedback de empleados usando NLP para identificar áreas de preocupación antes de que escalen.
- **Correlación desempeño-retención**: Encontrar patrones entre métricas de desempeño, engagement y permanencia en la empresa.

Business Intelligence respondería "el 25% de empleados se fue el año pasado y la mayoría eran del área de desarrollo". Business Analytics permite responder "¿quiénes tienen alta probabilidad de irse en los próximos 6 meses, por qué razones, y qué podemos hacer para retenerlos?".

Los requerimientos del caso incluyen:

- Analizar causas de rotación y desarrollar estrategias
- Seguimiento de desempeño con objetivos y retroalimentación
- Análisis de comentarios de empleados

Estas necesidades requieren capacidades analíticas avanzadas propias de BA.

### 3. Acciones para implementar la solución

| Fase | Acción | Descripción |
|------|--------|-------------|
| 1 | **Integración de datos HR** | Consolidar datos de sistemas HRIS, evaluaciones de desempeño, encuestas de clima organizacional, datos de capacitación, y métricas de productividad |
| 2 | **Modelo predictivo de rotación** | Desarrollar un modelo de machine learning que identifique empleados en riesgo usando variables como: antigüedad, historial de evaluaciones, salario vs mercado, carga de trabajo, tiempo desde última promoción |
| 3 | **Sistema de gestión de desempeño** | Implementar plataforma para gestión de OKRs/KPIs con seguimiento continuo, feedback 360° y one-on-ones documentados |
| 4 | **People Analytics Dashboard** | Crear dashboards para HR y gerentes con métricas clave: rotación por área, engagement scores, pipeline de talento, diversidad |
| 5 | **Análisis de feedback cualitativo** | Implementar NLP (Natural Language Processing) para procesar encuestas abiertas, exit interviews y comentarios anónimos |
| 6 | **Programa de retención basado en datos** | Diseñar intervenciones específicas basadas en insights: planes de carrera personalizados, ajustes de compensación, programas de desarrollo |

**Herramientas sugeridas:**

- Workday/SAP SuccessFactors como HRIS base
- Python (scikit-learn) para modelo de predicción de rotación
- Power BI/Tableau para dashboards de People Analytics
- Herramientas de encuestas como Culture Amp o Glint

### 4. ¿Aplicar Big Data?

**No necesariamente**, fundamentado en las siguientes razones:

| Aspecto | Análisis |
|---------|----------|
| **Volumen** | El volumen de datos de RRHH es relativamente moderado. Una empresa de tecnología en crecimiento puede tener miles de empleados, pero no millones de registros que requieran procesamiento distribuido |
| **Variedad** | Los datos son principalmente estructurados (datos demográficos, evaluaciones, salarios) y pueden manejarse con bases de datos relacionales tradicionales |
| **Velocidad** | No se requiere procesamiento en tiempo real; los análisis de RRHH típicamente se realizan en ciclos mensuales o trimestrales |
| **Costo-beneficio** | Una solución con Python/R + SQL Server/PostgreSQL + Power BI sería suficiente y más económica |

**Excepción donde sí se justificaría Big Data:**

- Si se quisiera analizar comunicaciones internas (emails, mensajes de Slack) para detectar clima organizacional
- Si se procesaran datos de productividad en tiempo real de herramientas de trabajo
- Si la empresa tuviera decenas de miles de empleados globalmente

---

## 5. Conclusiones de la tarea

### Aprendizajes clave:

1. **BI vs BA - Diferencia fundamental**
   - **Business Intelligence** responde "¿qué pasó?" y "¿qué está pasando?" (análisis descriptivo)
   - **Business Analytics** responde "¿qué pasará?" y "¿qué debemos hacer?" (análisis predictivo y prescriptivo)
   - La elección depende de las preguntas de negocio que se necesiten responder

2. **Big Data no siempre es la respuesta**
   - La decisión de usar Big Data debe basarse en las 4 V's: Volumen, Variedad, Velocidad y Veracidad
   - No toda solución analítica requiere Big Data; a veces herramientas tradicionales son más eficientes, económicas y fáciles de mantener
   - El caso de marketing digital justifica Big Data por el alto volumen y necesidad de tiempo real
   - El caso de RRHH puede resolverse con herramientas tradicionales dado el volumen moderado de datos

3. **El valor está en la acción**
   - Tanto BI como BA solo generan valor real si se traducen en decisiones y acciones concretas de negocio
   - Los insights sin acción son solo información interesante
   - Es fundamental cerrar el ciclo: datos → análisis → insight → decisión → acción → medición

4. **Integración de datos es el primer paso crítico**
   - En ambos casos, el prerequisito fundamental es consolidar datos de múltiples fuentes
   - Sin datos integrados y de calidad, cualquier análisis será limitado o erróneo
   - La gobernanza de datos es esencial para el éxito de cualquier iniciativa analítica

5. **Contexto del negocio determina la solución**
   - No existe una solución única para todos los casos
   - Cada industria y problema requiere un análisis particular de sus necesidades
   - La tecnología es un habilitador, pero el entendimiento del negocio es lo que genera valor

---

## Referencias

- Davenport, T. H., & Harris, J. G. (2007). *Competing on Analytics: The New Science of Winning*. Harvard Business Review Press.
- Provost, F., & Fawcett, T. (2013). *Data Science for Business*. O'Reilly Media.
- Marr, B. (2016). *Big Data in Practice*. Wiley.
- Sharda, R., Delen, D., & Turban, E. (2020). *Business Intelligence, Analytics, and Data Science: A Managerial Perspective* (5th ed.). Pearson.
