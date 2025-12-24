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
WHERE condición -- Filtrado
```

- **SELECT:** Define las columnas de donde obtendremos los datos que queremos ver. El uso de `*` selecciona todas las columnas de una tabla.
- **FROM:** Especifica la fuente de los datos (tablas o vistas).
- **WHERE:** Aplica predicados de los datos (`=`, `<>`, `>`, `LIKE`, `IN`, `BETWEEN`).

A diferencia del orden de escritura, el motor procesa las consultas en el siguiente orden:

1. `FROM`
2. `WHERE`
3. `SELECT`

## Ejemplo 1: MySQL

**Objetivo:** Obtener los nombres de los productos y sus precios con etiquetas personalizadas. 

**Escenario:** Inventario de tienda de tecnología.

**Tabla `productos`:**

| id `INT PRIMARY KEY` | nombre `VARCHAR(200)` | precio `DECIMAL(10,2)` | stock `INT` |
| :------------------: | :-------------------: | :--------------------: | :---------: |
|          1           |       Laptop HP       |        15999.99        |     25      |
|          2           |   Mouse Inalámbrico   |         299.50         |     150     |
|          3           |   Teclado Mecánico    |         1250.0         |     80      |

**MySQL (v8.0+):**

```sql
SELECT nombre AS articulo, precio AS costo_unitario
FROM productos
WHERE precio > 500;
```

- Se seleccionan dos columnas, renombrándolas con `AS`. El `WHERE` limita el resultado a productos costosos.

|     articulo     | costo_unitario |
| :--------------: | :------------: |
|    Laptop HP     |    15999.99    |
| Teclado Mecánico |     1250.0     |

## Ejemplo 2: SQL Server

**Objetivo:** Obtener los 5 empleados con mayor salario.

**Escenario:** Auditoria de nómina.

**Tabla `empleados`:**

| **id** `INT PRIMARY KEY` | **nombre** `NVARCHAR(100)` | **salario** `MONEY` |
| ------------------------ | -------------------------- | ------------------- |
| 1                        | Ana García                 | 3500.00             |
| 2                        | Carlos Mendoza             | 2800.50             |
| 3                        | Laura Rodríguez            | 4200.00             |
| 4                        | Sergio Torres              | 3100.75             |
| 5                        | María José López           | 5500.00             |
| 6                        | Diego Ramírez              | 2400.00             |
| 7                        | Elena Beltrán              | 3900.25             |
| 8                        | Ricardo Franco             | 4800.00             |
| 9                        | Sofía Castro               | 3250.10             |
| 10                       | Javier Ortiz               | 2900.00             |

**SQL Server (v2022):**

```sql
SELECT TOP 5 nombre, salario
FROM empleados
WHERE salario IS NOT NULL
ORDER BY salario DESC;
```

- En SQL Server se usa `TOP` inmediatamente después del `SELECT` para limitar filas.
- El uso de `NVARCHAR` y `MONEY` es estándar en T-SQL para soporte multi-idioma y precisión financiera.

| **nombre**       | **salario** |
| ---------------- | ----------- |
| María José López | 5500.00     |
| Ricardo Franco   | 4800.00     |
| Laura Rodríguez  | 4200.00     |
| Elena Beltrán    | 3900.25     |
| Ana García       | 3500.00     |

## Mejores Prácticas

1. Evitar `SELECT *` en entornos de producción, especifica siempre las columnas para reducir el tráfico de red y mejora el rendimiento.
2. Usa alias para aumentar la legibilidad (especialmente en reportes).
3. Nunca traigas todos los datos a la aplicación para filtrarlos ahí; usa el `WHERE` para que la base de datos haga el trabajo pesado.
4. Asegúrate de comparar columnas con el tipo de dato correcto en el `WHERE` (ej: no compares un string `10` con un INT `10`).

## Errores comunes

- Olvidar que el  `WHERE` no reconoce alias creados en el `SELECT`.

|                         Incorrecto ❌                         |                          Correcto ✅                          |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| `SELECT nombre, salario * 12 AS SueldoAnual FROM empleados WHERE SueldoAnual > 40000;` | `SELECT nombre, salario * 12 AS SueldoAnual FROM empleados WHERE (salario * 12) > 40000;` |

- Comparar valores NULL con `=`:

|        Incorrecto ❌        |           Correcto ✅            |
| :------------------------: | :-----------------------------: |
| `NULL = NULL` es `UNKNOWN` | Usar `IS NULL`  o `IS NOT NULL` |

## Mejoras de rendimiento

- **Índices:** El `WHERE` es el lugar donde los índices tienen mayor impacto. Una columna filtrada frecuentemente debe estar indexada.