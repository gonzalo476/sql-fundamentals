# Funciones de Ventana: Agregación

Realizan cálculos sobre un conjunto de filas que están relacionadas con la fila actual. A diferencia de las funciones de agregado estándar (`GROUP BY`), **las funciones de ventana no colapsan las filas**; cada fila mantiene su identidad individual mientras muestra el resultado del calculo agregado.

Su propósito principal es realizar análisis de series temporales y cálculos estadísticos complejos, como el Total Acumulado (Running Total) o el Promedio Móvil (Moving Average). Son fundamentales en bases de datos relacionales modernas para generar reportes donde se requiere comparar el valor de una fila con una métrica calculada sobre un contexto específico. 

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

Promedio Móvil (Últimos 3 día)

**Escenario:** Suavizar la volatilidad de precios promediando el precio actual con los dos anteriores.

