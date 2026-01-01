https://freesql.com/

DB: `Human Resources (HR)`

## **INNER JOIN básico**

### 📋 Título del Caso

**Empleados y sus Departamentos**

### 🏢 Contexto del Negocio

El área de Recursos Humanos necesita un listado de empleados junto con el departamento al que pertenecen para generar reportes internos de estructura organizacional.

### 🎯 Objetivo

- **Mostrar:** `employee_id`, `first_name`, `last_name`, `department_name`
- **Incluir:** solo empleados que tengan departamento asignado
- **Ordenamiento:** por `employee_id` ascendente
- **Formato esperado:** alias claros y legibles para columnas

### ⚙️ Requisitos Técnicos

- ✅ Usar INNER JOIN explícito
- ✅ Relacionar correctamente `employees` y `departments`
- ✅ Evitar columnas innecesarias

### 📊 Se Evaluará

- ✔️ Uso correcto de INNER JOIN
- ✔️ Condición de join bien definida
- ✔️ Claridad y legibilidad del SQL
- ✔️ Comprensión del modelo HR

### 🗂️ Tablas Involucradas

`employees`, `departments`

### 💡 Pistas (Opcional)

- 🔍 La relación se da por `department_id`

**Resultado:**

```sql
-- INNER JOIN
SELECT
    e.EMPLOYEE_ID,
    CONCAT(e.FIRST_NAME, ' ', e.LAST_NAME) AS FULL_NAME,
    d.DEPARTMENT_NAME
FROM HR.EMPLOYEES e
INNER JOIN HR.DEPARTMENTS d
    ON e.DEPARTMENT_ID = d.DEPARTMENT_ID
ORDER BY e.EMPLOYEE_ID ASC;
```

## **LEFT JOIN (manejo de valores NULL)**

### 📋 Título del Caso

**Empleados sin Departamento Asignado**

### 🏢 Contexto del Negocio

Recursos Humanos quiere identificar empleados que aún no han sido asignados formalmente a un departamento.

### 🎯 Objetivo

- **Mostrar:** employee_id, first_name, last_name, department_name
- **Incluir:** todos los empleados, incluso los que no tienen departamento
- **Ordenamiento:** por last_name ascendente
- **Formato esperado:** department_name debe mostrar NULL cuando no exista

### ⚙️ Requisitos Técnicos

- ✅ Uso obligatorio de LEFT JOIN
- ✅ No filtrar registros que eliminen empleados sin departamento
- ✅ Mantener consistencia en alias

### 📊 Se Evaluará

- ✔️ Diferenciación correcta entre INNER y LEFT JOIN
- ✔️ Manejo adecuado de NULL
- ✔️ Buenas prácticas en joins externos
- ✔️ Ordenamiento correcto

### 🗂️ Tablas Involucradas

`employees`, `departments`

### 💡 Pistas (Opcional)

- 🔍 El LEFT JOIN debe partir de employees

```sql
-- LEFT JOIN
SELECT
    e.EMPLOYEE_ID,
    e.FIRST_NAME,
    e.LAST_NAME,
    d.DEPARTMENT_NAME
FROM HR.EMPLOYEES e
LEFT JOIN HR.DEPARTMENTS d
    ON e.DEPARTMENT_ID = d.DEPARTMENT_ID
ORDER BY e.LAST_NAME ASC;
```

## **JOIN múltiple (4 tablas)**

### 📋 Título del Caso

**Empleados, Departamentos y Ubicación**

### 🏢 Contexto del Negocio

La empresa desea un reporte que muestre dónde trabajan físicamente los empleados, incluyendo ciudad y país.

### 🎯 Objetivo

- **Mostrar:** employee_id, full_name, department_name, city, country_name
- **Incluir:** solo empleados con departamento y ubicación definida
- **Ordenamiento:** por country_name, city
- **Formato esperado:** concatenar nombre completo

### ⚙️ Requisitos Técnicos

- ✅ Usar múltiples INNER JOIN
- ✅ Concatenar first_name y last_name
- ✅ Alias consistentes para tablas

### 📊 Se Evaluará

- ✔️ Capacidad para encadenar joins correctamente
- ✔️ Uso correcto de claves foráneas
- ✔️ Legibilidad del SQL
- ✔️ Comprensión del modelo relacional HR

### 🗂️ Tablas Involucradas

`employees`, `departments`, `locations`, `countries`

### 💡 Pistas (Opcional)

- 🔍 departments conecta con locations, no employees directamente

```sql
-- JOIN 4 TABLAS
SELECT
    e.EMPLOYEE_ID,
    CONCAT(e.FIRST_NAME, ' ', e.LAST_NAME) AS FULL_NAME,
    d.DEPARTMENT_NAME,
    l.CITY,
    c.COUNTRY_NAME
FROM HR.EMPLOYEES e
INNER JOIN HR.DEPARTMENTS d
    ON e.DEPARTMENT_ID = d.DEPARTMENT_ID
INNER JOIN HR.LOCATIONS l
    ON l.LOCATION_ID = d.LOCATION_ID
INNER JOIN HR.COUNTRIES c
    ON l.COUNTRY_ID = c.COUNTRY_ID
ORDER BY c.COUNTRY_NAME, l.CITY;
```

## **SELF JOIN**

### 📋 Título del Caso

**Empleados y sus Managers**

### 🏢 Contexto del Negocio

Se requiere identificar la relación jerárquica entre empleados y sus supervisores directos para un análisis organizacional.

### 🎯 Objetivo

- **Mostrar:** employee_name, manager_name
- **Incluir:** empleados con y sin manager
- **Ordenamiento:** por manager_name
- **Formato esperado:** alias claros para diferenciar roles

### ⚙️ Requisitos Técnicos

- ✅ Usar SELF JOIN sobre la tabla employees
- ✅ LEFT JOIN para incluir empleados sin manager
- ✅ Diferenciar correctamente employee vs manager

### 📊 Se Evaluará

- ✔️ Comprensión del SELF JOIN
- ✔️ Uso correcto de alias
- ✔️ Manejo de NULL en managers
- ✔️ Claridad semántica del resultado

### 🗂️ Tablas Involucradas

`employees`

### 💡 Pistas (Opcional)

- 🔍 manager_id referencia a employee_id en la misma tabla

```sql
-- SELF JOIN
SELECT
    CONCAT(e.FIRST_NAME, ' ', e.LAST_NAME) AS EMPLOYEE_NAME,
    CONCAT(m.FIRST_NAME, ' ', m.LAST_NAME) AS MANAGER_NAME
FROM HR.EMPLOYEES e
LEFT JOIN HR.EMPLOYEES m
    ON e.MANAGER_ID = m.EMPLOYEE_ID
ORDER BY MANAGER_NAME;
```

## **LEFT JOIN + Filtro en tabla secundaria (caso típico de entrevista)**

### 📋 Título del Caso

**Departamentos sin Empleados**

### 🏢 Contexto del Negocio

La empresa quiere identificar departamentos que existen en el sistema pero que actualmente no tienen empleados asignados.

### 🎯 Objetivo

- **Mostrar:** department_id, department_name
- **Incluir:** solo departamentos sin empleados
- **Ordenamiento:** por department_name
- **Formato esperado:** sin duplicados

### ⚙️ Requisitos Técnicos

- ✅ Uso de LEFT JOIN
- ✅ Filtro correcto para detectar ausencia de empleados
- ✅ Evitar errores comunes de WHERE mal aplicado

### 📊 Se Evaluará

- ✔️ Comprensión profunda de LEFT JOIN
- ✔️ Correcta ubicación de filtros
- ✔️ Evitar conversión involuntaria a INNER JOIN
- ✔️ Precisión del resultado

### 🗂️ Tablas Involucradas

`departments`, `employees`

### 💡 Pistas (Opcional)

- 🔍 El filtro debe evaluar columnas de employees en NULL

```sql
-- LEFT JOIN + FILTRO EN TABLA SECUNDARIA
SELECT
    d.DEPARTMENT_ID,
    d.DEPARTMENT_NAME
FROM HR.DEPARTMENTS d
LEFT JOIN HR.EMPLOYEES e
    ON e.DEPARTMENT_ID = d.DEPARTMENT_ID
WHERE e.DEPARTMENT_ID IS NULL;
```

**Extra:**

```sql
-- Seleccionar los departamentos con menos de 3 employees (que no sean null)
SELECT
    d.DEPARTMENT_ID,
    d.DEPARTMENT_NAME,
    COUNT(e.EMPLOYEE_ID) AS EMPLOYEE_COUNT
FROM
    HR.DEPARTMENTS d
INNER JOIN
    HR.EMPLOYEES e ON d.DEPARTMENT_ID = e.DEPARTMENT_ID
GROUP BY
    d.DEPARTMENT_ID,
    d.DEPARTMENT_NAME
HAVING -- having filtra grupos / where filtra filas individuales
    COUNT(e.EMPLOYEE_ID) <= 3
ORDER BY
    d.DEPARTMENT_NAME;
```
