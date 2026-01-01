# Funciones de Ventana: Ranking

Las funciones de ventana (window functions) son funciones analíticas de SQL que permiten realizar cálculos sobre un conjunto de filas relacionadas con la fila actual, **sin colapsar el resultado** como lo haría un `GROUP BY`. Dentro de este grupo, las funciones de ranking asignan valores ordinales a las filas según un criterio de ordenamiento.

`ROW_NUMBER()`, `RANK()` y `DENSE_RANK()` se utiliza para numerar o clasificar filas dentro de una partición de datos, permitiendo resolver problemas como top-N por grupo, eliminación de duplicados, análisis comparativos y reporting analítico.

```sql
SELECT
	columna,
	[RANKING()] OVER (
    	[PARTITION BY columna_grupo]
        ORDER BY columna_orden [ASC|DESC]
    ) AS alias_ranking
FROM tabla;
```

