# Funciones de Ventana: Agregación

Realizan cálculos sobre un conjunto de filas que están relacionadas con la fila actual. A diferencia de las funciones de agregado estándar (`GROUP BY`), **las funciones de ventana no colapsan las filas**; cada fila mantiene su identidad individual mientras muestra el resultado del calculo agregado.

Su propósito principal es realizar análisis de series temporales y cálculos estadísticos complejos, como el Total Acumulado (Running Total) o el Promedio Móvil (*Moving Average*). Son fundamentales en bases de datos relacionales modernas para generar reportes donde se requiere comparar el valor de una fila con una métrica calculada sobre un contexto específico. 

## Sintaxis básica

La sintaxis se basa en la clausula `OVER()`, que define la "ventana" de datos.

```sql
SELECT
	columna,
	SUM(col) OVER (
    	[PARTITION BY col_grupo]
        [ORDER BY col_orden]
        [ROWS o RANGE entre_limites]
    ) AS alias_resultado
FROM tabla;
```

- **PARTITION BY:** *(Opcional)* Divide las filas en grupos.
- **ORDER BY:** *(Obligatorio para cumulados)* Define la secuencia del calculo.
- **ROW/RANGE:** *(Critico para móviles)* Define el "marco" (*frame*) de filas exactas a incluir respecto a la fila actual.

## Conceptos fundamentales

- **The Frame (El Marco):** Es el subconjunto de filas dentro de la partición sobre la cual se aplica la función. Si no se especifica pero hay un `ORDER BY`, el valor por defecto es `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`.

  > El frame o window frame controla qué filas participan en el cálculo.

- **Acumulado Móvil vs. Total Acumulado:**  El Total Acumulado crece indefinidamente sumando todo lo anterior.

- **Internals:** El motor de la base de datos ordena los datos en memoria o disco y utiliza un puntero para recorrer el marco definido, evitando escaneos repetitivos de la tabla completa.

## Ejemplo 1: MySQL

Total Acumulado de Ventas

**Escenario:** Calcular cómo crecen las ventas día a día.

**Tabla `ventas_diarias`:**

| fecha `DATE` | monto `DECIMAL(10,2)` |
| ------------ | --------------------- |
| 2025-01-01   | 100.00                |
| 2025-01-02   | 150.00                |
| 2025-01-03   | 200.00                |

**Query:**

```sql
SELECT
	fecha,
	monto
	SUM(monto) OVER (ORDER BY fecha) AS acumulado_total
FROM ventas_diarias;
```

- `SUM(monto) OVER (ORDER BY fecha)` indica a MySQL que sume el monto actual más todos los montos de fechas anteriores.

**Resultado:**

|   fecha    | monto  | cumulado_total |
| :--------: | :----: | :------------: |
| 2025-01-01 | 100.00 |     100.00     |
| 2025-01-02 | 150.00 |     250.00     |
| 2025-01-03 | 200.00 |     450.00     |

## Ejemplo 2: SQL Server

Promedio Móvil (Últimos 3 días)

**Escenario:** Suavizar la volatilidad de precios promediando el precio actual con los dos anteriores.

**Tabla `PreciosHistoricos`:** 

|   fecha    | precio |
| :--------: | :----: |
| 2025-01-01 | 100.00 |
| 2025-01-02 | 110.00 |
| 2025-01-03 | 105.00 |
| 2025-01-04 | 120.00 |
| 2025-01-05 | 115.00 |

**Query:**

```sql
SELECT 
    fecha,
    frecio,
    AVG(precio) OVER (
        ORDER BY fecha 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS promedio_movil_3_dias
FROM PreciosHistoricos;
```

- `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` define una ventana de exactamente 3 filas (la actual y las 2 anteriores).
- SQL Server es extremadamente eficiente procesando marcos de ventana gracias a operadores de ejecución específicos como *Window Spool*.

**Resultado:**

|   fecha    | precio | promedio_movil_3_dias |
| :--------: | :----: | :-------------------: |
| 2025-01-01 | 100.00 |        100.00         |
| 2025-01-02 | 110.00 |        105.00         |
| 2025-01-03 | 105.00 |        105.00         |
| 2025-01-04 | 120.00 |        111.67         |
| 2025-01-05 | 115.00 |        113.33         |

## Mejores prácticas

1. **Índices:** Para que `ORDER BY` dentro de `OVER` sea rápido, la columna de ordenamiento debe estar indexada.
2. **Especificar `ROWS` vs `RANGE`:** `ROWS` es generalmente más rápido que `RANGE` porque cuenta filas físicas y no evalúa valores lógicos.
3. **Evitar particiones masivas:** Si `PARTITION BY` genera grupos demasiado grandes, el uso de memoria RAM del servidor SQL aumentará drásticamente.

## Errores comunes:

- **Error:** Omitir el `ORDER BY` en un acumulado.
  - *Efecto:* La función suma todo el set de datos en cada fila, devolviendo el total general en lugar del acumulado.
- **Error:** Confundir `ROWS` (filas) con `RANGE` (valores).
  - *Efecto:* Si hay fechas duplicadas, `RANGE` sumará todos los montos de la misma fecha a la vez, mientras que `ROWS` lo hará uno por uno.