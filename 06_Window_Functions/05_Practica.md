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

