1. Fundamentos de Consultas (The Basics)
   * Estructura básica: `SELECT`, `FROM`, `WHERE`.
   * Filtrado avanzado: Operadores lógicos (`AND`, `OR`, `IN`, `BETWEEN`, `LIKE`).
   * Agregaciones básicas: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.
   * Agrupamiento: `GROUP BY` y filtrado de agregados con `HAVING`.
   * Ordenamiento: `ORDER BY`.

2. Relaciones y Teoría de Conjuntos (Joins & Sets)
   * Tipos de Joins: `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`.
   * Joins Especiales: `CROSS JOIN` (producto cartesiano) y `SELF JOIN` (una tabla consigo misma).
   * Operaciones de Conjuntos: `UNION`, `UNION ALL` (diferencia clave en rendimiento), `INTERSECT`, `EXCEPT/MINUS`.

3. Organización y Modularidad (CTEs y Vistas)
   * CTEs (Common Table Expressions): Uso de `WITH` para limpiar código anidado.
   * CTEs Recursivos: Para manejar jerarquías (ej. organigramas, categorías padre-hijo).
   * Vistas (Views): Cuándo crear una vista lógica para simplificar accesos.
   * Subqueries: Subconsultas en `SELECT`, `FROM` y `WHERE` (y cuándo reemplazarlas por CTEs).

4. Definición y Tipado de Datos (DDL & Data Types)
   * Data Types: Numéricos (`INT`, `BIGINT`, `DECIMAL`), Texto (`CHAR` vs `VARCHAR`, `TEXT`), Booleanos.
   * Comandos DDL: `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`, `TRUNCATE TABLE`.
   * Restricciones (Constraints): Primary Keys, Foreign Keys, Unique, Not Null, Default.

5. Limpieza y Manipulación de Datos (Data Cleaning - Crucial para DE)
   * Strings: `TRIM`, `UPPER/LOWER`, `SUBSTRING`, `REPLACE`, `CONCAT`.
   * Regex: Uso de expresiones regulares para extracciones complejas (`REGEXP_LIKE`, `REGEXP_SUBSTR`).
   * Fechas: `DATE_TRUNC` (agrupar por mes/año), `DATEDIFF`, `DATE_ADD`, `CURRENT_DATE`, manejo de `TIMESTAMPS` y Zonas Horarias.
   * Manejo de Nulos: `COALESCE` (sustituir nulos), `NULLIF` (evitar divisiones por cero), `IS NULL`.
   * Casting: Conversión de tipos explícita (`CAST`, `TRY_CAST` para manejo de errores).

6. Funciones de Ventana (Window Functions)
   * Agregación: `SUM() OVER`, `AVG() OVER` (acumulados móviles).
   * Ranking: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`.
   * Posicionales (Offset): `LEAD()` (fila siguiente), `LAG()` (fila anterior) para comparar periodos de tiempo.
   * Partitioning: Entender profundamente la cláusula `PARTITION BY` y `ORDER BY` dentro de la ventana.

7. Estrategias de Carga y Modificación (DML Avanzado para ETL)
   * DML: `INSERT`, `UPDATE`, `DELETE`.
   * Upserts (MERGE): Comando `MERGE` para insertar si es nuevo o actualizar si existe (fundamental en pipelines).
   * Control de Transacciones: `BEGIN`, `COMMIT`, `ROLLBACK` (Propiedades ACID).

8. Programación en Base de Datos (Procedural SQL)
   * Stored Procedures: Creación de procedimientos almacenados para automatizar tareas.
   * Variables y Control de Flujo: `DECLARE`, `SET`, `IF/ELSE`, Loops (`WHILE`).
   * User Defined Functions (UDFs): Funciones escalares vs. tabulares.

9. Datos Semi-Estructurados (Modern SQL)
   * JSON: Parseo de JSON (`JSON_EXTRACT`, `JSON_VALUE`), arrays y aplanamiento de datos (`LATERAL FLATTEN` o `UNNEST`).

10. Optimización y Rendimiento (Performance Tuning)
    * Índices: Clustered vs Non-Clustered Indexes (cuándo ayudan y cuándo estorban).
    * Particionamiento de Tablas: Estrategias para dividir tablas masivas.
    * Planes de Ejecución: Saber leer un `EXPLAIN PLAN` para detectar Full Table Scans y cuellos de botella.
    * Vistas Materializadas: Diferencia con vistas normales y estrategia de refresco.