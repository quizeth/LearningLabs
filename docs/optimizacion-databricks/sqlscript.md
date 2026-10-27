# Script SQL de la práctica · Optimización del motor SQL y Unity Catalog en Databricks

## Antes de usarlo

1. Copia el bloque de código completo en un **SQL Editor nuevo** de Databricks.
2. Reemplaza **todas** las apariciones de `TU_NOMBRE` por tu nombre y primer apellido, en minúsculas, sin espacios ni tildes y unidos con guion bajo (por ejemplo, `ana_garcia`). Aparece 16 veces, así que usa «reemplazar todo» (`Ctrl+H`, o `Cmd+H` en Mac).
3. Reemplaza `<RUTA_ALMACENAMIENTO>` por la ruta que el instructor pegue en el chat. Aparece 2 veces, en el paso 0.2. **No la escribas a mano.**
4. **Nunca uses «Run all».** Ejecuta cada paso por separado: selecciona sus líneas y pulsa `Ctrl+Enter` (`Cmd+Enter` en Mac).
5. Comprueba que el warehouse seleccionado arriba a la derecha es *Serverless Starter Warehouse*.

> El bloque final (**Cierre**) borra tu catálogo con todo lo que contiene. Ejecútalo **solo** cuando lo indique el instructor.

## Script

```sql
-- =====================================================================
-- PRÁCTICA GUIADA · Optimización del motor SQL y Unity Catalog
-- Databricks SQL · Serverless SQL Warehouse (Small) · 60 minutos
-- =====================================================================
-- CÓMO USAR ESTE SCRIPT
--   1. Copia el script completo en un SQL Editor nuevo.
--   2. Busca y reemplaza (Ctrl+H; Cmd+H en Mac) todas las apariciones de  TU_NOMBRE  por tu
--      nombre y primer apellido en minúsculas, sin espacios ni tildes,
--      unidos con guion bajo (ej.: ana_garcia).
--   3. Sustituye  <RUTA_ALMACENAMIENTO>  por la ruta que te dará el
--      instructor en el chat de la sesión.
--   4. NUNCA uses "Run all". Ejecuta cada paso por separado:
--      selecciona las líneas del paso y pulsa Ctrl+Enter (Cmd+Enter en Mac).
--   5. Comprueba el warehouse seleccionado arriba a la derecha:
--      "Serverless Starter Warehouse".
-- =====================================================================


-- =====================================================================
-- BLOQUE 0 · PREPARACIÓN (0:00 – 0:08)
-- =====================================================================

-- Paso 0.1 · Desactiva la caché de resultados para que las mediciones
-- sean reales. Repite este paso si cambias de pestaña o de sesión.
SET use_cached_result = false;

-- Paso 0.2 · Crea tu catálogo, tu esquema y sitúate en ellos.
-- El catálogo necesita una ubicación de almacenamiento gestionado
-- (MANAGED LOCATION) porque el metastore no tiene una por defecto.
CREATE CATALOG IF NOT EXISTS demo_TU_NOMBRE
  MANAGED LOCATION '<RUTA_ALMACENAMIENTO>/demo_TU_NOMBRE';

CREATE SCHEMA IF NOT EXISTS demo_TU_NOMBRE.practica;

USE CATALOG demo_TU_NOMBRE;
USE SCHEMA practica;

SELECT current_user(), current_catalog(), current_schema();

-- Paso 0.3 · ABRE UNA SEGUNDA PESTAÑA DEL EDITOR y ejecuta allí
-- este paso. Tarda unos minutos: mientras tanto, seguimos con el
-- Bloque 1 en la primera pestaña.
-- Copia de lineitem (solo 5 columnas) organizada por fecha de envío
-- con liquid clustering.
CREATE OR REPLACE TABLE demo_TU_NOMBRE.practica.lineitem_cl
CLUSTER BY (l_shipdate)
AS SELECT l_orderkey, l_shipdate, l_quantity, l_extendedprice, l_discount
   FROM samples.tpch.lineitem;

OPTIMIZE demo_TU_NOMBRE.practica.lineitem_cl;


-- =====================================================================
-- BLOQUE 1 · LEER EL PLAN DE EJECUCIÓN (0:08 – 0:23)
-- Tablas: samples.tpch (solo lectura)
-- =====================================================================

-- Paso 1.1 · Plan INICIAL (no ejecuta la consulta)
EXPLAIN FORMATTED
SELECT o.o_orderpriority, COUNT(*) AS num_lineas
FROM samples.tpch.orders o
JOIN samples.tpch.lineitem l ON o.o_orderkey = l.l_orderkey
WHERE o.o_orderdate >= '1995-01-01'
GROUP BY o.o_orderpriority
ORDER BY o.o_orderpriority;

-- Paso 1.2 · Ejecución real. Después: "See performance" ->
-- "See query profile" y compara con el plan del paso 1.1.
SELECT o.o_orderpriority, COUNT(*) AS num_lineas
FROM samples.tpch.orders o
JOIN samples.tpch.lineitem l ON o.o_orderkey = l.l_orderkey
WHERE o.o_orderdate >= '1995-01-01'
GROUP BY o.o_orderpriority
ORDER BY o.o_orderpriority;

-- Paso 1.3 · (Opcional) Join con tablas pequeñas: ¿qué tipo de join
-- aparece ahora en el Query Profile?
SELECT n.n_name, COUNT(*) AS num_pedidos
FROM samples.tpch.orders o
JOIN samples.tpch.customer c ON o.o_custkey = c.c_custkey
JOIN samples.tpch.nation n   ON c.c_nationkey = n.n_nationkey
GROUP BY n.n_name
ORDER BY num_pedidos DESC;

-- Paso 1.4 · (Opcional) Anti-patrón SELECT *: compara "Bytes read"
-- de las dos consultas en Query History.
SELECT *
FROM samples.tpch.lineitem
WHERE l_quantity = 50 AND l_discount = 0.10;

SELECT l_orderkey, l_extendedprice
FROM samples.tpch.lineitem
WHERE l_quantity = 50 AND l_discount = 0.10;


-- =====================================================================
-- BLOQUE 2 · ORGANIZACIÓN FÍSICA DE LOS DATOS (0:23 – 0:38)
-- Requiere que haya terminado el paso 0.3 (segunda pestaña)
-- =====================================================================

-- Paso 2.1 · ¿Cómo están guardadas las dos tablas?
-- Fíjate en numFiles, sizeInBytes y clusteringColumns.
DESCRIBE DETAIL samples.tpch.lineitem;
DESCRIBE DETAIL demo_TU_NOMBRE.practica.lineitem_cl;

-- Paso 2.2 · El mismo filtro sobre la tabla original...
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM samples.tpch.lineitem
WHERE l_shipdate BETWEEN '1998-01-01' AND '1998-03-31';

-- Paso 2.3 · ...y sobre tu copia con clustering.
-- Compara "Files read", "Files pruned" y "Bytes read".
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE l_shipdate BETWEEN '1998-01-01' AND '1998-03-31';

-- Paso 2.4 · (Opcional) Mismo resultado, filtro escrito con una
-- función sobre la columna. ¿Cambia "Files pruned"?
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE YEAR(l_shipdate) = 1998 AND MONTH(l_shipdate) <= 3;


-- =====================================================================
-- BLOQUE 3 · UNITY CATALOG EN LA PRÁCTICA (0:38 – 0:50)
-- =====================================================================

-- Paso 3.1 · El motor recomendaba ANALYZE en samples. Inténtalo.
-- (Se espera un error: anota cuál. NO pulses "Request permissions".)
ANALYZE TABLE samples.tpch.lineitem COMPUTE STATISTICS;

-- Paso 3.2 · ¿Qué estadísticas tiene tu tabla ANTES de hacer nada?
-- Mira la sección "Optimizer Statistics" al final del plan.
EXPLAIN FORMATTED
SELECT COUNT(*) FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE l_shipdate >= '1998-01-01';

-- Paso 3.3 · Calcula estadísticas en tu tabla y repite el paso 3.2.
ANALYZE TABLE demo_TU_NOMBRE.practica.lineitem_cl
  COMPUTE STATISTICS FOR ALL COLUMNS;

-- Paso 3.4 · Tipo de tabla, ubicación e historial de operaciones.
DESCRIBE TABLE EXTENDED demo_TU_NOMBRE.practica.lineitem_cl;
DESCRIBE HISTORY demo_TU_NOMBRE.practica.lineitem_cl;

-- Paso 3.5 · Explorar Unity Catalog con SQL (information_schema)
SELECT table_catalog, table_schema, table_name, table_type, is_insertable_into
FROM samples.information_schema.tables
WHERE table_schema = 'tpch';

SELECT column_name, data_type, ordinal_position
FROM demo_TU_NOMBRE.information_schema.columns
WHERE table_name = 'lineitem_cl'
ORDER BY ordinal_position;

-- Paso 3.6 · (Opcional) Tablas de sistema. Se espera un error de
-- permisos: lee qué permiso falta. NO pulses "Request permissions".
SELECT * FROM system.query.history LIMIT 5;


-- =====================================================================
-- CIERRE (0:50 – 0:57) · Limpieza
-- Ejecuta este paso SOLO cuando lo indique el instructor.
-- =====================================================================
USE CATALOG samples;
DROP CATALOG IF EXISTS demo_TU_NOMBRE CASCADE;
```
