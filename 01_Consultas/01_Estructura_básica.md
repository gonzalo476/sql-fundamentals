# Estructura Básica SQL

La estructura `SELECT-FROM-WHERE` es el núcleo de las consultas de proyección y filtrado en SQL. Su propósito es permitir al usuario extraer columnas específicas (`SELECT`) de una o varias tablas (`FROM`) que cumplan los criterios lógicos determinados (`WHERE`).

En el contexto de las bases de datos relacionales, la estructura implementa las operaciones de la **Álgebra Relacional** conocidas como *Selección* ([Selection (relational algebra)](https://en.wikipedia.org/wiki/Selection_(relational_algebra))) y *Proyección*. Es la herramienta primaria para transformar grandes volúmenes de datos crudos en información útil y específica para aplicaciones o reportes:

> En *álgebra relacional*, la selección es una **operación unaria** que toma una relación y devuelve únicamente aquellas tuplas que cumplen una condición, formando un subconjunto de dicha relación.

![img_algebra_relacional](https://karenpucci.wordpress.com/wp-content/uploads/2014/10/seleccion.jpg)

## Sintaxis básica

La sintaxis sigue un orden jerárquico estricto que el motor de la base de datos debe de validar antes de la ejecución.

```sql
SELECT [DISTINCT] columna1, columna2 -- Proyección
FROM tabla -- Origen
WHERE condicion -- Filtrado
```

- **SELECT:** Define las columnas de donde obtendremos los datos que queremos ver. El uso de `*` selecciona todas las columnas de una tabla.
- **FROM:** Especifica la fuente de los datos (tablas o vistas).
- **WHERE:** Aplica predicados de los datos (`=`, `<>`, `>`, `LIKE`, `IN`, `BETWEEN`).

A diferencia del orden de escritura, el motor procesa las consultas en el siguiente orden:

1. `FROM`
2. `WHERE`
3. `SELECT`