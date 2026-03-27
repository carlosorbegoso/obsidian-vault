# Servicios de Machine Learning

## Resumen

El componente de Machine Learning del Sistema de Mantenimiento Vehicular Predictivo consta de dos servicios Python que implementan un pipeline completo de aprendizaje automático, desde el entrenamiento de modelos hasta la predicción en tiempo real (Chen & Guestrin, 2016).

| Servicio | Puerto | Stack | Propósito |
|----------|--------|-------|-----------|
| ms-prediction-service | 8000 | FastAPI + Python 3.11 | Predicciones en tiempo real |
| ml-pipeline-xgb | 8002 | FastAPI + XGBoost 2.0.2 | Entrenamiento de modelos |

---

## 1. Arquitectura del Componente ML

### Figura 10. Pipeline de Machine Learning

**Archivo:** [diagrams/47-pipeline-ml-tesis.wsd](diagrams/47-pipeline-ml-tesis.wsd)

El pipeline de Machine Learning implementa un flujo completo de entrenamiento que incluye extracción de datos, ingeniería de características, validación, entrenamiento con XGBoost, evaluación y persistencia del modelo.

> *Nota APA:* Figura 10. *Pipeline de Machine Learning para Predicción de Mantenimiento*. Elaboración propia (2024). Proceso de entrenamiento del modelo predictivo utilizando el algoritmo XGBoost.

### Figura 11. Flujo de Predicción

**Archivo:** [diagrams/46-flujo-prediccion-tesis.wsd](diagrams/46-flujo-prediccion-tesis.wsd)

El servicio de predicción implementa un flujo optimizado con caché en Redis para minimizar la latencia en consultas frecuentes.

> *Nota APA:* Figura 11. *Diagrama de Secuencia: Consulta de Predicción de Mantenimiento*. Elaboración propia (2024). Flujo de interacciones para obtener predicciones de mantenimiento vehicular.

---

## 2. ms-prediction-service

### 2.1. Descripción

Servicio de predicción en tiempo real que utiliza modelos XGBoost entrenados para proporcionar predicciones de mantenimiento vehicular, evaluación de salud de componentes y análisis de síntomas (Hastie et al., 2009).

### 2.2. Componentes del Servicio

| Componente | Función |
|------------|---------|
| PredictionRouter | API REST para solicitudes de predicción |
| VehiclePredictor | Lógica de predicción principal |
| ComponentPredictor | Predicción por componente del vehículo |
| SymptomPredictor | Análisis basado en síntomas reportados |
| RedisCache | Caché de predicciones (TTL: 30 min) |

### 2.3. Endpoints del Servicio

#### Health Check

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | / | Información del servicio |
| GET | /health | Estado de salud del servicio |

#### Predictions

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | /predictions/predict | Predicción individual de mantenimiento |
| POST | /predictions/batch-predict | Predicciones en lote |
| GET | /predictions/history/{vehicleId} | Historial de predicciones |

#### Components

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | /predictions/components/predict-components | Predicción por componente |
| GET | /predictions/components/component-health/{vehicleId} | Estado de componentes |

### 2.4. Estructura de Request/Response

**Solicitud de Predicción:**

```json
{
    "vehicle_id": "123",
    "brand": "Toyota",
    "model": "Corolla",
    "year": 2020,
    "fuel_type": "gasoline",
    "mileage": 45000,
    "last_maintenance_date": "2023-10-15",
    "last_maintenance_mileage": 42000,
    "maintenance_history": [...]
}
```

**Respuesta de Predicción:**

```json
{
    "vehicle_id": "123",
    "predicted_days_to_maintenance": 45,
    "confidence_score": 0.89,
    "health_score": 78.5,
    "failure_probability": 0.15,
    "risk_level": "MEDIUM",
    "recommended_action": "Programar mantenimiento en 30 días",
    "critical_components": ["brakes", "oil_filter"],
    "maintenance_plan": {
        "immediate": [],
        "short_term": ["oil_change", "brake_inspection"],
        "long_term": ["timing_belt"]
    },
    "health_trend": {
        "current": 78.5,
        "projected_30_days": 72.0,
        "projected_90_days": 65.0
    }
}
```

### 2.5. Configuración

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| model_path | models/xgboost_maintenance_model.joblib | Ruta del modelo |
| model_reload_interval | 3600 | Intervalo de recarga (segundos) |
| redis_url | redis://localhost:6379/0 | URL de conexión Redis |
| cache_ttl | 1800 | Tiempo de vida del caché (30 min) |
| prediction_timeout_seconds | 30 | Timeout de predicción |

---

## 3. ml-pipeline-xgb

### 3.1. Descripción

Pipeline completo de Machine Learning para el entrenamiento de modelos XGBoost. Implementa las fases de extracción de datos, ingeniería de características, validación, entrenamiento y persistencia (Brownlee, 2020).

### 3.2. Componentes del Pipeline

| Componente | Función |
|------------|---------|
| DataLoader | Extracción de datos desde PostgreSQL |
| FeaturePipeline | Orquestación de transformaciones |
| TemporalTransformer | Features temporales (mes, estación) |
| VehicleTransformer | Codificación de marca/modelo |
| StatisticalTransformer | Promedios móviles, tendencias |
| MainValidator | Validación de calidad de datos |
| XGBoostMaintenanceModel | Modelo de predicción |
| ModelManager | Persistencia y versionado |

### 3.3. Endpoints del Pipeline

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | /api/v1/training/train | Iniciar entrenamiento |
| GET | /api/v1/models/list | Listar modelos disponibles |
| GET | /api/v1/models/{model_id} | Detalle de modelo específico |

### 3.4. Proceso de Entrenamiento

El proceso de entrenamiento sigue las siguientes fases:

**Fase 1: Extracción de Datos**

Consulta SQL con window functions para generar características temporales:

```sql
SELECT
    v.*,
    m.*,
    LAG(m.date) OVER (PARTITION BY v.id ORDER BY m.date) as prev_date,
    AVG(m.cost) OVER (PARTITION BY v.id ORDER BY m.date
                       ROWS BETWEEN 3 PRECEDING AND CURRENT ROW) as avg_cost_3
FROM vehicles v
JOIN maintenance m ON v.id = m.vehicle_id
```

**Fase 2: Ingeniería de Características**

| Transformador | Features Generadas |
|---------------|-------------------|
| TemporalTransformer | month, quarter, season, month_sin, month_cos, is_weekend |
| VehicleTransformer | brand_encoded, model_encoded, fuel_type, vehicle_age |
| MaintenanceTransformer | type_encoded, cost, duration, km_since_last |
| StatisticalTransformer | avg_km_between_3, cumulative_cost, preventive_ratio |

**Fase 3: Validación**

El validador verifica:
- Estructura de datos (columnas requeridas)
- Reglas de negocio (valores válidos)
- Calidad de datos (score mínimo: 70%)
- Consistencia (tipos de datos correctos)

**Fase 4: Entrenamiento**

Configuración de XGBoost:

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| objective | reg:squarederror | Regresión |
| n_estimators | 1000 | Número máximo de árboles |
| max_depth | 8 | Profundidad máxima |
| learning_rate | 0.1 | Tasa de aprendizaje |
| subsample | 0.8 | Muestreo de filas |
| early_stopping_rounds | 50 | Parada temprana |

**Fase 5: Evaluación**

Métricas calculadas:

| Métrica | Descripción | Objetivo |
|---------|-------------|----------|
| MAE | Error Absoluto Medio | < 5 días |
| RMSE | Raíz del Error Cuadrático Medio | < 7 días |
| R² | Coeficiente de Determinación | > 0.85 |
| MAPE | Error Porcentual Absoluto Medio | < 15% |

**Fase 6: Persistencia**

Archivos generados:

```
models/
├── xgboost_maintenance_model_20231207_023715.xgb       # Booster nativo
├── xgboost_maintenance_model_20231207_023715.joblib    # Formato sklearn
├── xgboost_maintenance_model_20231207_023715_metadata.json
└── xgboost_maintenance_model_latest.xgb -> ...         # Symlink
```

### 3.5. Respuesta de Entrenamiento

```json
{
    "success": true,
    "model_id": "xgboost_maintenance_model_20231207_023715",
    "training_info": {
        "status": "completed",
        "duration_seconds": 45.2
    },
    "metrics": {
        "train_mae": 3.5,
        "train_r2": 0.92,
        "val_mae": 3.8,
        "val_r2": 0.89,
        "test_mae": 4.1,
        "test_r2": 0.88,
        "best_iteration": 450
    }
}
```

---

## 4. Métricas del Modelo Actual

El modelo XGBoost actualmente en producción presenta las siguientes métricas de rendimiento:

| Métrica | Valor | Interpretación |
|---------|-------|----------------|
| MAE | 4.1 días | Error promedio de predicción |
| RMSE | 4.8 días | Error cuadrático medio |
| R² | 0.88 | Varianza explicada (88%) |
| Tiempo de respuesta (cache hit) | <100ms | Latencia con caché |
| Tiempo de respuesta (cache miss) | <500ms | Latencia sin caché |

### 4.1. Importancia de Características

| Característica | Importancia (Gain) |
|----------------|-------------------|
| km_since_last_maintenance | 0.25 |
| vehicle_age | 0.18 |
| brand_encoded | 0.12 |
| preventive_ratio | 0.10 |
| avg_cost_3 | 0.08 |

---

## 5. Integración con el Sistema

### Figura 13. Proceso de Predicción con ML

**Archivo:** [diagrams/40-proceso-negocio-prediccion.wsd](diagrams/40-proceso-negocio-prediccion.wsd)

El proceso de predicción se integra con el flujo de negocio del sistema, permitiendo la generación de alertas automáticas cuando el modelo detecta necesidad de mantenimiento próximo.

> *Nota APA:* Figura 13. *Proceso de Negocio de Predicción con Machine Learning*. Elaboración propia (2024). Integración del modelo predictivo en el proceso de negocio.

---

## 6. Referencias

Brownlee, J. (2020). *XGBoost With Python: Gradient Boosted Trees with XGBoost and scikit-learn*. Machine Learning Mastery.

Chen, T., & Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 785-794. https://doi.org/10.1145/2939672.2939785

Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning: Data Mining, Inference, and Prediction* (2nd ed.). Springer.

---

## 7. Índice de Figuras

| # | Figura | Archivo |
|---|--------|---------|
| 10 | Pipeline de Machine Learning | 47-pipeline-ml-tesis.wsd |
| 11 | Flujo de Predicción | 46-flujo-prediccion-tesis.wsd |
| 13 | Proceso de Negocio Predicción | 40-proceso-negocio-prediccion.wsd |

Para el índice completo de figuras, consultar: [DIAGRAMAS-TESIS.md](DIAGRAMAS-TESIS.md)

---

*Documentación de Servicios ML - Sistema de Mantenimiento Vehicular Predictivo*
*Tesis 2024*
