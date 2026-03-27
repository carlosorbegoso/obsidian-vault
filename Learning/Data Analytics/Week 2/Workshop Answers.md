# Taller en Clase No.3 - Respuestas

## Business Analytics y Big Data - Unidad 1

### Modelo Multidimensional: Rendimiento Académico Universitario

---

## Análisis de Requerimientos

### Pregunta 1: ¿Cuál es el mejor promedio de notas de todos los alumnos durante un año académico?

**Dimensiones involucradas:**

- `DIM_TIEMPO` (filtro por año académico)
- `DIM_ALUMNO` (agrupación por alumno)

**Medida utilizada:**

- `nota` → aplicar función `AVG()` para promedio y `MAX()` para el mejor

**Consulta SQL equivalente:**

```sql
SELECT
    t.anio_academico,
    a.nombres,
    a.apellidos,
    AVG(f.nota) AS promedio_notas
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_ALUMNO a ON f.id_alumno = a.id_alumno
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
WHERE t.anio_academico = '2024'
GROUP BY t.anio_academico, a.id_alumno, a.nombres, a.apellidos
ORDER BY promedio_notas DESC
LIMIT 1;
```

**Respuesta:** El modelo permite identificar al alumno con mejor promedio filtrando por `DIM_TIEMPO.anio_academico` y calculando `MAX(AVG(nota))` agrupado por alumno.

---

### Pregunta 2: ¿Cuáles fueron las notas del alumno José Perez durante los años 2022 al 2024?

**Dimensiones involucradas:**

- `DIM_ALUMNO` (filtro por nombre del alumno)
- `DIM_TIEMPO` (filtro por rango de años)
- `DIM_CURSO` (para ver en qué cursos obtuvo las notas)

**Medida utilizada:**

- `nota` → valor individual de cada registro

**Consulta SQL equivalente:**

```sql
SELECT
    a.nombres,
    a.apellidos,
    c.nombre_curso,
    t.anio_academico,
    t.ciclo_academico,
    f.nota
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_ALUMNO a ON f.id_alumno = a.id_alumno
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
JOIN DIM_CURSO c ON f.id_curso = c.id_curso
WHERE a.nombres = 'José'
  AND a.apellidos = 'Perez'
  AND t.anio BETWEEN 2022 AND 2024
ORDER BY t.anio, t.ciclo_academico;
```

**Respuesta:** El modelo permite obtener el historial completo de notas de un alumno específico filtrando por `DIM_ALUMNO.nombres` y `DIM_ALUMNO.apellidos`, y acotando el periodo con `DIM_TIEMPO.anio`.

---

### Pregunta 3: ¿Cuál es la nota promedio del curso de Business Analytics de los últimos cinco años?

**Dimensiones involucradas:**

- `DIM_CURSO` (filtro por nombre del curso)
- `DIM_TIEMPO` (filtro por últimos 5 años)

**Medida utilizada:**

- `nota` → aplicar función `AVG()`

**Consulta SQL equivalente:**

```sql
SELECT
    c.nombre_curso,
    t.anio,
    AVG(f.nota) AS promedio_anual
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_CURSO c ON f.id_curso = c.id_curso
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
WHERE c.nombre_curso = 'Business Analytics'
  AND t.anio BETWEEN 2020 AND 2024
GROUP BY c.nombre_curso, t.anio
ORDER BY t.anio;

-- Promedio general de los 5 años:
SELECT
    c.nombre_curso,
    AVG(f.nota) AS promedio_5_anios
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_CURSO c ON f.id_curso = c.id_curso
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
WHERE c.nombre_curso = 'Business Analytics'
  AND t.anio BETWEEN 2020 AND 2024
GROUP BY c.nombre_curso;
```

**Respuesta:** El modelo calcula el promedio de notas filtrando por `DIM_CURSO.nombre_curso = 'Business Analytics'` y `DIM_TIEMPO.anio` en el rango de los últimos 5 años.

---

### Pregunta 4: ¿Cuál es el promedio de notas de un profesor en los ciclos del año 2022 al 2024?

**Dimensiones involucradas:**

- `DIM_PROFESOR` (filtro/agrupación por profesor)
- `DIM_TIEMPO` (filtro por rango de años y ciclos)

**Medida utilizada:**

- `nota` → aplicar función `AVG()`

**Consulta SQL equivalente:**

```sql
SELECT
    p.nombres,
    p.apellidos,
    t.anio,
    t.ciclo_academico,
    AVG(f.nota) AS promedio_notas
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_PROFESOR p ON f.id_profesor = p.id_profesor
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
WHERE t.anio BETWEEN 2022 AND 2024
GROUP BY p.id_profesor, p.nombres, p.apellidos, t.anio, t.ciclo_academico
ORDER BY p.apellidos, t.anio, t.ciclo_academico;
```

**Respuesta:** El modelo permite analizar el desempeño de los profesores (medido por las notas de sus alumnos) agrupando por `DIM_PROFESOR` y filtrando por `DIM_TIEMPO.anio` y `DIM_TIEMPO.ciclo_academico`.

---

### Pregunta 5: ¿En qué modalidad de dictado se encuentra el mejor promedio de notas del año 2024?

**Dimensiones involucradas:**

- `DIM_MODALIDAD` (agrupación por modalidad)
- `DIM_TIEMPO` (filtro por año 2024)

**Medida utilizada:**

- `nota` → aplicar función `AVG()` y ordenar para obtener el mejor

**Consulta SQL equivalente:**

```sql
SELECT
    m.nombre_modalidad,
    m.tipo,
    AVG(f.nota) AS promedio_notas
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_MODALIDAD m ON f.id_modalidad = m.id_modalidad
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
WHERE t.anio = 2024
GROUP BY m.id_modalidad, m.nombre_modalidad, m.tipo
ORDER BY promedio_notas DESC
LIMIT 1;
```

**Respuesta:** El modelo identifica la modalidad con mejor rendimiento agrupando por `DIM_MODALIDAD.nombre_modalidad`, filtrando por `DIM_TIEMPO.anio = 2024` y ordenando por `AVG(nota)` descendente.

---

### Pregunta 6: ¿Cuál es la sede con mejor promedio de notas de los alumnos por semestre académico?

**Dimensiones involucradas:**

- `DIM_SEDE` (agrupación por sede)
- `DIM_TIEMPO` (agrupación por semestre académico)

**Medida utilizada:**

- `nota` → aplicar función `AVG()`

**Consulta SQL equivalente:**

```sql
-- Promedio por sede y semestre
SELECT
    s.nombre_sede,
    t.anio,
    t.semestre,
    AVG(f.nota) AS promedio_notas
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_SEDE s ON f.id_sede = s.id_sede
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
GROUP BY s.id_sede, s.nombre_sede, t.anio, t.semestre
ORDER BY t.anio, t.semestre, promedio_notas DESC;

-- Mejor sede por cada semestre
SELECT DISTINCT ON (t.anio, t.semestre)
    s.nombre_sede,
    t.anio,
    t.semestre,
    AVG(f.nota) AS promedio_notas
FROM FACT_RENDIMIENTO_ACADEMICO f
JOIN DIM_SEDE s ON f.id_sede = s.id_sede
JOIN DIM_TIEMPO t ON f.id_tiempo = t.id_tiempo
GROUP BY s.id_sede, s.nombre_sede, t.anio, t.semestre
ORDER BY t.anio, t.semestre, promedio_notas DESC;
```

**Respuesta:** El modelo permite comparar el rendimiento entre sedes agrupando por `DIM_SEDE.nombre_sede` y `DIM_TIEMPO.semestre`, calculando `AVG(nota)` para identificar la sede con mejor promedio en cada periodo.

---

## Resumen del Modelo

### Tabla de Hechos

| Campo          | Tipo    | Descripción               |
| -------------- | ------- | -------------------------- |
| id_rendimiento | INT     | Clave primaria             |
| id_alumno      | INT     | FK a DIM_ALUMNO            |
| id_tiempo      | INT     | FK a DIM_TIEMPO            |
| id_curso       | INT     | FK a DIM_CURSO             |
| id_profesor    | INT     | FK a DIM_PROFESOR          |
| id_modalidad   | INT     | FK a DIM_MODALIDAD         |
| id_sede        | INT     | FK a DIM_SEDE              |
| **nota** | DECIMAL | **Medida principal** |
| creditos       | INT     | Medida secundaria          |

### Dimensiones y su Uso

| Dimensión    | Atributos Clave                  | Preguntas que Responde |
| ------------- | -------------------------------- | ---------------------- |
| DIM_ALUMNO    | nombres, apellidos, carrera      | P1, P2                 |
| DIM_TIEMPO    | anio, semestre, ciclo_academico  | P1, P2, P3, P4, P5, P6 |
| DIM_CURSO     | nombre_curso, area               | P3                     |
| DIM_PROFESOR  | nombres, apellidos, especialidad | P4                     |
| DIM_MODALIDAD | nombre_modalidad, tipo           | P5                     |
| DIM_SEDE      | nombre_sede, ciudad              | P6                     |

### Granularidad

**Una fila en la tabla de hechos representa:** La nota obtenida por un alumno específico, en un curso específico, impartido por un profesor específico, en una modalidad específica, en una sede específica, durante un periodo de tiempo específico.

---

## Bus Matrix

| Proceso de Negocio     | Alumno | Tiempo | Curso | Profesor | Modalidad | Sede |
| ---------------------- | :----: | :----: | :---: | :------: | :-------: | :--: |
| Rendimiento Académico |   X   |   X   |   X   |    X    |     X     |  X  |
