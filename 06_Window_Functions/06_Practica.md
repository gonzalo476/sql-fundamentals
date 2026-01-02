## 🔷 PROBLEMA 1

### 🎯 SUBTEMA/VARIANTE

`ROW_NUMBER() OVER (PARTITION BY ...)`

### 📋 Título del Caso

**Ranking de ventas por producto dentro de cada canal**

### 🏢 Contexto del Negocio

El área comercial desea analizar qué tan bien se venden los productos dentro de cada canal de venta (Online, Direct, Retail). Para ello, necesitan un ranking interno por canal.

### 🎯 Objetivo

- **Mostrar:** channel_desc, prod_name, amount_sold
- **Incluir:** un ranking por canal basado en ventas
- **Ordenamiento:** ventas de mayor a menor dentro de cada canal
- **Formato esperado:** alias claros para el ranking y métricas

### ⚙️ Requisitos Técnicos

- ✅ Usar una función de ventana para generar el ranking
- ✅ El ranking debe reiniciarse por cada canal
- ✅ No usar subqueries correlacionadas para el ranking

### 📊 Se Evaluará

- ✔️ Uso correcto de `ROW_NUMBER()`
- ✔️ Correcta partición y ordenamiento
- ✔️ Legibilidad y claridad del SQL
- ✔️ Comprensión del concepto de ventana

### 🗂️ Tablas Involucradas

```
SALES, PRODUCTS, CHANNELS
```

### 💡 Pistas (Opcional)

- 🔍 Piensa qué columnas deben ir en `PARTITION BY` y `ORDER BY`

```sql
SELECT
    c.CHANNEL_DESC,
    p.PROD_NAME,
    s.AMOUNT_SOLD,
    ROW_NUMBER() OVER (
        PARTITION BY c.CHANNEL_ID -- hace una separacion por canal
        ORDER BY s.AMOUNT_SOLD DESC -- la venta #1 con el monto mas alto
    ) AS RANKING
FROM SH.SALES s
INNER JOIN SH.CHANNELS c 
    ON s.CHANNEL_ID = c.CHANNEL_ID
INNER JOIN SH.PRODUCTS p 
    ON s.PROD_ID = p.PROD_ID;
```

## 🔷 PROBLEMA 2

### 🎯 SUBTEMA/VARIANTE

`RANK()` vs `DENSE_RANK()`

### 📋 Título del Caso

**Top productos por ventas anuales considerando empates**

### 🏢 Contexto del Negocio

El equipo de planeación anual quiere identificar los productos más vendidos por año, considerando que puede haber empates en ventas.

### 🎯 Objetivo

- **Mostrar:** calendar_year, prod_name, total_amount_sold
- **Incluir:** ranking anual de productos
- **Ordenamiento:** por año y ranking ascendente
- **Formato esperado:** ranking que refleje empates correctamente

### ⚙️ Requisitos Técnicos

- ✅ Usar agregación junto con una window function
- ✅ El ranking debe manejar empates sin saltos innecesarios
- ✅ No filtrar el top usando `ROWNUM`

### 📊 Se Evaluará

- ✔️ Elección correcta entre `RANK()` y `DENSE_RANK()`
- ✔️ Uso combinado de `GROUP BY` y funciones analíticas
- ✔️ Claridad del cálculo de métricas
- ✔️ Manejo correcto de empates

### 🗂️ Tablas Involucradas

```
SALES, PRODUCTS, TIMES
```

### 💡 Pistas (Opcional)

- 🔍 Primero agrega, luego rankea sobre el resultado

```sql
SELECT
    r.CALENDAR_YEAR,
    r.PROD_NAME,
    r.TOTAL_AMOUNT_SOLD,
    DENSE_RANK() OVER(
        PARTITION BY r.CALENDAR_YEAR
        ORDER BY r.TOTAL_AMOUNT_SOLD DESC
    ) AS RANKING
FROM (
    SELECT
        t.CALENDAR_YEAR,
        p.PROD_NAME,
        SUM(s.AMOUNT_SOLD) AS TOTAL_AMOUNT_SOLD
    FROM SH.SALES s
    JOIN SH.PRODUCTS p ON s.PROD_ID = p.PROD_ID
    JOIN SH.TIMES t ON s.TIME_ID = t.TIME_ID
    GROUP BY t.CALENDAR_YEAR, p.PROD_NAME
) r
ORDER BY CALENDAR_YEAR, RANKING;
```

**Con CTE:**

```sql
WITH sales_by_product_year AS (
    SELECT
        t.calendar_year,
        p.prod_id,
        p.prod_name,
        SUM(s.amount_sold) AS total_amount_sold
    FROM sh.sales s
    JOIN sh.products p ON s.prod_id = p.prod_id
    JOIN sh.times t ON s.time_id = t.time_id
    GROUP BY
        t.calendar_year,
        p.prod_id,
        p.prod_name
)
SELECT
    calendar_year,
    prod_name,
    total_amount_sold,
    DENSE_RANK() OVER (
        PARTITION BY calendar_year
        ORDER BY total_amount_sold DESC
    ) AS ranking
FROM sales_by_product_year
ORDER BY calendar_year, ranking;

```

## 🔷 PROBLEMA 3

### 🎯 SUBTEMA/VARIANTE

```
SUM() OVER (PARTITION BY ...)
```

### 📋 Título del Caso

**Participación de ventas por producto dentro de su categoría**

### 🏢 Contexto del Negocio

Marketing quiere saber qué porcentaje del total de ventas de una categoría corresponde a cada producto.

### 🎯 Objetivo

- **Mostrar:** prod_category, prod_name, amount_sold
- **Incluir:** total de ventas por categoría y porcentaje del producto
- **Ordenamiento:** por categoría y porcentaje descendente
- **Formato esperado:** porcentaje con alias descriptivo

### ⚙️ Requisitos Técnicos

- ✅ Usar `SUM()` como función analítica
- ✅ Evitar subqueries anidadas innecesarias
- ✅ Calcular correctamente el porcentaje

### 📊 Se Evaluará

- ✔️ Uso correcto de agregados como window functions
- ✔️ Comprensión de particiones
- ✔️ Precisión matemática del porcentaje
- ✔️ Buen uso de alias y formato

### 🗂️ Tablas Involucradas

```
SALES, PRODUCTS
```

### 💡 Pistas (Opcional)

- 🔍 El total por categoría puede calcularse sin agrupar filas
