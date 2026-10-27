# Guía del alumno · Práctica guiada: optimización del motor SQL y Unity Catalog en Databricks

> **Duración:** 60 minutos

> **Datos:** `samples.tpch` (solo lectura) y una copia propia en tu catálogo

La sesión anterior fue teórica. Hoy lo compruebas con consultas reales. El objetivo es que salgas sabiendo **leer el plan de ejecución y el Query Profile** para detectar ineficiencias, y que distingas tres cosas que influyen en el rendimiento:

1. **Cómo se escribe la consulta** (bloque 1): qué columnas se leen, qué tipo de join elige el motor y cómo se interpreta el plan.
2. **Cómo están organizados los datos** (bloque 2): por qué un filtro a veces no ahorra lectura y qué cambia con *liquid clustering*.
3. **Qué aporta Unity Catalog** (bloque 3): quién puede optimizar una tabla y qué mantenimiento puede hacer la plataforma sola.

Algunas cosas de hoy **matizan o actualizan** lo que vimos en las diapositivas. Lo que veas en pantalla es lo que el motor hace hoy.

## Reglas de la sesión

- **No compares segundos.** El warehouse es compartido y los tiempos varían según lo que haga cada uno. Compara lo que no depende de eso: bytes leídos, ficheros leídos y descartados, y filas.
- **Nunca uses «Run all».** Ejecuta cada paso por separado: selecciona sus líneas y pulsa `Ctrl+Enter` (`Cmd+Enter` en Mac). El instructor te dirá qué paso toca.
- **No pulses «Request permissions»** cuando salga un error de permisos. Enviaría una solicitud real a los administradores.
- **Repite el paso 0.1** (`SET use_cached_result = false;`) si abres una pestaña nueva o cambias de sesión.
- **Contrasta siempre con la pantalla.** Si usas Genie Code (el asistente de IA del editor), pídele solo explicaciones y comprueba lo que dice con lo que ves. Puede ejecutar código por su cuenta.

## Antes de empezar

Puedes encontrar aquí el [script SQL completo](sqlscript.md).

El instructor te pasará la **ruta de almacenamiento** que debes usar. Sigue estos cinco pasos, de uno en uno, al ritmo que marque el instructor:

1. Abre un **SQL Editor nuevo** y pega el script completo.
2. Con `Ctrl+H` (`Cmd+H` en Mac), reemplaza **todas las apariciones** de `TU_NOMBRE` por tu **nombre y primer apellido**, en minúsculas, sin espacios ni tildes y unidos con guion bajo. Por ejemplo: `ana_garcia`. Usa «reemplazar todo»: el nombre aparece muchas veces.
3. Reemplaza `<RUTA_ALMACENAMIENTO>` por la ruta que el instructor ha pegado en el chat. **Cópiala y pégala; no la escribas a mano.**
4. Recuerda: **nunca «Run all»**. Una sentencia o un paso cada vez, con `Ctrl+Enter`.
5. Comprueba arriba a la derecha que el warehouse seleccionado es **Serverless Starter Warehouse**.

> En esta guía, `TU_NOMBRE` en los fragmentos de SQL es el texto que sustituyes por tu nombre. Si el instructor te indica algo distinto para el paso 0.2 (por ejemplo, que tu catálogo ya está creado), sigue sus instrucciones.

### Dónde ver las métricas

Después de ejecutar una consulta:

- **See performance.** Panel con las métricas de la ejecución: duración, `Bytes read`, `Rows read`, `Files read`, `Files pruned`, `Bytes read from cache`, `Spill`, `Tasks total time` y tiempo en Photon.
- **See query profile.** Grafo con lo que el motor ejecutó de verdad. Arriba a la derecha tiene los botones *Time spent*, *Memory peak* y *Rows*. Si haces clic en un nodo, ves su detalle.
- **Query History** (menú lateral). Historial de consultas, desde donde también puedes abrir el Query Profile.

---

## Bloque 0 · Preparación

### Paso 0.1 · Desactivar la caché de resultados

Databricks guarda el resultado de las consultas: si repites una, o alguien ejecuta la misma en este warehouse compartido, la respuesta sale de la caché y las métricas no sirven. Este comando lo evita en tu sesión.

```sql
SET use_cached_result = false;
```

Hay otra caché, la **de disco**, que guarda en el warehouse los ficheros leídos recientemente. **No la desactivamos.** Por eso a veces verás `Bytes read from cache` por encima de cero: es normal y no cambia cuántos ficheros se leen o se descartan, solo la duración.

### Paso 0.2 · Crear tu catálogo y tu esquema

```sql
CREATE CATALOG IF NOT EXISTS demo_TU_NOMBRE
  MANAGED LOCATION '<RUTA_ALMACENAMIENTO>/demo_TU_NOMBRE';

CREATE SCHEMA IF NOT EXISTS demo_TU_NOMBRE.practica;

USE CATALOG demo_TU_NOMBRE;
USE SCHEMA practica;

SELECT current_user(), current_catalog(), current_schema();
```

**Debe aparecer:** tu usuario, `demo_<tu_nombre>` y `practica`.

Si falla, casi siempre falta sustituir `TU_NOMBRE` o la ruta. Avisa al instructor, pero no pierdas más de un minuto: sigue mirando su pantalla.

### Paso 0.3 · Crear tu copia de `lineitem` (en una segunda pestaña)

1. Abre una **segunda pestaña** del editor con el botón `+` de las pestañas.
2. Pega ahí solo este paso y ejecútalo. **Tarda unos minutos: no esperes.** Vuelve a la primera pestaña.
3. Si vas a lanzar consultas desde la segunda pestaña, repite antes `SET use_cached_result = false;`.

```sql
CREATE OR REPLACE TABLE demo_TU_NOMBRE.practica.lineitem_cl
CLUSTER BY (l_shipdate)
AS SELECT l_orderkey, l_shipdate, l_quantity, l_extendedprice, l_discount
   FROM samples.tpch.lineitem;

OPTIMIZE demo_TU_NOMBRE.practica.lineitem_cl;
```

Copia **cinco columnas** de `lineitem` a tu catálogo, organizadas por fecha de envío. Ya veremos por qué. La usarás en los bloques 2 y 3. Si no ha terminado cuando haga falta, sigue la pantalla del instructor y anota sus valores.

---

## Bloque 1 · Leer el plan de ejecución 
Cuando escribes una consulta, ¿qué hace realmente el motor? Hay dos formas de saberlo: **`EXPLAIN`**, que dice lo que *planea* hacer, y el **Query Profile**, que dice lo que *hizo*. Veremos por qué pueden no coincidir.

### Paso 1.1 · Plan inicial con `EXPLAIN`

`EXPLAIN` **no ejecuta** la consulta: solo muestra el plan. Es largo, así que el instructor lo recorre contigo.

```sql
EXPLAIN FORMATTED
SELECT o.o_orderpriority, COUNT(*) AS num_lineas
FROM samples.tpch.orders o
JOIN samples.tpch.lineitem l ON o.o_orderkey = l.l_orderkey
WHERE o.o_orderdate >= '1995-01-01'
GROUP BY o.o_orderpriority
ORDER BY o.o_orderpriority;
```

**Localiza en tu pantalla y anota:**

- [ ] `isFinalPlan` (primera línea del plan y su detalle): ______
- [ ] Tipo de join: ______
- [ ] Prefijo de los operadores: ______
- [ ] `ReadSchema` del scan de `lineitem`: ¿qué columnas aparecen? ______
- [ ] Sección `Optimizer Statistics` (al final del plan): ______

Tienes en el [Anexo A](#anexo-a--referencia-de-operadores-del-plan) una tabla con cada línea del plan por si quieres consultar alguna.

### Paso 1.2 · Ejecución real y Query Profile

Ejecuta la consulta. Abre **See performance** y después **See query profile**.

```sql
SELECT o.o_orderpriority, COUNT(*) AS num_lineas
FROM samples.tpch.orders o
JOIN samples.tpch.lineitem l ON o.o_orderkey = l.l_orderkey
WHERE o.o_orderdate >= '1995-01-01'
GROUP BY o.o_orderpriority
ORDER BY o.o_orderpriority;
```

En el grafo, haz clic en el nodo **Inner Join** para ver su detalle. Rellena la tabla con los valores de **tu pantalla**:

| Métrica | Tu valor |
|---|---|
| Duración (varía entre ejecuciones: anótala, no la compares) | ______ |
| `Bytes read` | ______ |
| `Files read` / `Files pruned` | ______ / ______ |
| `Tasks total time` (varía entre ejecuciones) | ______ |
| Algoritmo del join (nodo *Inner Join*) | ______ |
| Filas de la primera agregación (entrada → salida) | ______ → ______ |

**Pregunta 1.** ¿Coincide el tipo de join del Query Profile con el del `EXPLAIN`? ¿Por qué podría no coincidir?

> Tu respuesta: ______

**Pregunta 2.** El nodo *Scan* de `lineitem` puede marcar más segundos que la duración de la consulta. ¿Cómo es posible?

> Tu respuesta: ______

**Pregunta 3.** ¿Por qué hay dos *Grouping Aggregate*? ¿Cuántas filas viajan por el último *shuffle*?

> Tu respuesta: ______

**Pregunta 4.** ¿Es `Bytes read` lo que esperarías si el motor leyera las tablas completas?

> Déjala abierta: la responderemos en el paso 2.1, cuando veas el tamaño real de `lineitem`.

> **Ojo con la interpretación.** `Files pruned` puede salir en 0, pero esta consulta **no filtra `lineitem`**, que es la tabla grande. Con este dato solo no podemos concluir si un filtro evita leer ficheros. Esa pregunta se responde en el bloque 2.

### Paso 1.3 *(opcional)* · Join con tablas pequeñas

Une `orders` con `customer` y con `nation`, la tabla de países, que tiene 25 filas. Abre el Query Profile y mira qué algoritmo ha usado el motor **en cada join**: ¿*broadcast* o *shuffle*? La etiqueta «Inner Join» del grafo no lo dice: es el tipo lógico. Mira el detalle de cada nodo de join o qué hay justo antes.

```sql
SELECT n.n_name, COUNT(*) AS num_pedidos
FROM samples.tpch.orders o
JOIN samples.tpch.customer c ON o.o_custkey = c.c_custkey
JOIN samples.tpch.nation n   ON c.c_nationkey = n.n_nationkey
GROUP BY n.n_name
ORDER BY num_pedidos DESC;
```

| Join | Algoritmo que ves |
|---|---|
| `orders` – `customer` | ______ |
| `customer` – `nation` | ______ |

### Paso 1.4 *(opcional)* · `SELECT *` frente a columnas concretas

Dos consultas con el mismo filtro y las mismas filas. Ejecuta las dos y compara `Bytes read` en See performance o en Query History.

```sql
SELECT *
FROM samples.tpch.lineitem
WHERE l_quantity = 50 AND l_discount = 0.10;

SELECT l_orderkey, l_extendedprice
FROM samples.tpch.lineitem
WHERE l_quantity = 50 AND l_discount = 0.10;
```

| Consulta | `Bytes read` |
|---|---|
| `SELECT *` | ______ |
| Columnas concretas | ______ |

---

## Bloque 2 · Organización física de los datos

Si tu copia ha terminado, ejecuta cada paso también sobre ella. Si no, **anota los valores del instructor** en tu tabla.

### Paso 2.1 · Cómo están guardadas las dos tablas

`DESCRIBE DETAIL` da la ficha técnica de cada tabla. Busca `numFiles`, `sizeInBytes` y `clusteringColumns`. Hay que **desplazarse a la derecha** en el resultado.

```sql
DESCRIBE DETAIL samples.tpch.lineitem;
DESCRIBE DETAIL demo_TU_NOMBRE.practica.lineitem_cl;
```

| Tabla | `numFiles` | `sizeInBytes` | `clusteringColumns` |
|---|---|---|---|
| Original · `samples.tpch.lineitem` | ______ | ______ | ______ |
| Copia · `demo_TU_NOMBRE.practica.lineitem_cl` | ______ | ______ | ______ |

Ahora puedes volver a la **pregunta 4 del paso 1.2** y responderla:

> Tu respuesta: ______

### Pasos 2.2 y 2.3 · El mismo filtro en las dos tablas

La misma consulta, el primer trimestre de 1998, sobre dos tablas: la **original** (2.2) y **tu copia** (2.3).

```sql
-- Paso 2.2 · Tabla original
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM samples.tpch.lineitem
WHERE l_shipdate BETWEEN '1998-01-01' AND '1998-03-31';

-- Paso 2.3 · Tu copia con clustering
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE l_shipdate BETWEEN '1998-01-01' AND '1998-03-31';
```

Antes de mirar métricas, comprueba que `lineas` e `importe` son **idénticos** en las dos. Lo único que cambia es el trabajo que hace el motor para llegar a él. Después, en See performance, apunta:

| Tabla | `Files read` | `Files pruned` | `Bytes read` |
|---|---|---|---|
| Original (paso 2.2) · `samples.tpch.lineitem` | ______ | ______ | ______ |
| Copia (paso 2.3) · `demo_TU_NOMBRE.practica.lineitem_cl` | ______ | ______ | ______ |

**Pregunta 1.** En la tabla original, ¿el filtro por fecha ha evitado leer algún fichero? ¿Por qué?

> Tu respuesta: ______

**Pregunta 2.** ¿Qué ha cambiado en tu copia para que el motor pueda descartar ficheros?

> Tu respuesta: ______

**Pregunta 3.** Las dos consultas devuelven lo mismo. ¿Por qué no comparamos solo la duración?

> Tu respuesta: ______

### Paso 2.4 *(opcional)* · Función sobre la columna

El mismo filtro, pero escrito con una función sobre la columna. Ejecútalo sobre tu copia y compara `Files pruned` con el del paso 2.3.

```sql
SELECT COUNT(*) AS lineas, SUM(l_extendedprice) AS importe
FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE YEAR(l_shipdate) = 1998 AND MONTH(l_shipdate) <= 3;
```

| Consulta | `Files pruned` |
|---|---|
| Paso 2.3 (rango de fechas) | ______ |
| Paso 2.4 (`YEAR` y `MONTH`) | ______ |

---

## Bloque 3 · Unity Catalog en la práctica

Unity Catalog no es una alternativa a SQL: es la capa que **gobierna los datos**. Quién es dueño de qué, quién puede hacer qué y qué hace la plataforma automáticamente. Los pasos 3.2 a 3.5 usan tu copia: si no ha terminado, síguelos en la pantalla del instructor.

Rellena esta tabla a medida que avanzas:

| Paso | Qué anotar | Tu resultado |
|---|---|---|
| 3.1 | `ANALYZE` sobre `samples`: ¿falla? ¿Qué permiso falta? | ______ |
| 3.2 | `Optimizer Statistics` de tu copia **antes** de `ANALYZE` (`missing` / `partial` / `full`) | ______ |
| 3.3 | `Optimizer Statistics` **después** de `ANALYZE`; ¿cambia el plan? | ______ |
| 3.4 | `Type` de tu copia; operaciones de `DESCRIBE HISTORY` y si hay alguna que no lanzaste tú | ______ |
| 3.5 | `table_type` e `is_insertable_into` de `samples.tpch` | ______ |
| 3.6 | `system.query.history`: ¿falla? ¿Qué permiso falta? | ______ |

### Paso 3.1 · `ANALYZE` sobre `samples`

En el bloque 1, el motor recomendaba ejecutar `ANALYZE` sobre las tablas de `samples`. Inténtalo. **Se espera un error.** Debajo aparecerá un botón *Request permissions*: **no lo pulses.**

```sql
ANALYZE TABLE samples.tpch.lineitem COMPUTE STATISTICS;
```

**Pregunta 1.** ¿Por qué `ANALYZE` exige un permiso de modificación, si solo «calcula» estadísticas?

> Tu respuesta: ______

### Paso 3.2 · Estadísticas de tu tabla antes de hacer nada

El mismo tipo de `EXPLAIN`, ahora sobre tu copia. **No mires el plan:** ve directamente al final, a la sección `Optimizer Statistics`. ¿Qué pone para `lineitem_cl`?

```sql
EXPLAIN FORMATTED
SELECT COUNT(*) FROM demo_TU_NOMBRE.practica.lineitem_cl
WHERE l_shipdate >= '1998-01-01';
```

**Pregunta 2 (primera parte).** Tu copia es una tabla **gestionada** de Unity Catalog. ¿Tenía estadísticas antes de ejecutar `ANALYZE`?

> Tu respuesta: ______

### Paso 3.3 · Calcular estadísticas y repetir el `EXPLAIN`

Ejecuta `ANALYZE` sobre tu copia. Después, repite el `EXPLAIN` del paso 3.2 y compara la sección `Optimizer Statistics` y el plan.

```sql
ANALYZE TABLE demo_TU_NOMBRE.practica.lineitem_cl
  COMPUTE STATISTICS FOR ALL COLUMNS;
```

- ¿Funciona sin error de permisos? ______
- Estado en el paso 3.2 → estado ahora: ______ → ______
- ¿Cambia el plan? ______

### Paso 3.4 · Tipo de tabla e historial

El primer comando da la ficha completa de la tabla: busca los campos `Type` y `Location`. El segundo, el historial de operaciones.

```sql
DESCRIBE TABLE EXTENDED demo_TU_NOMBRE.practica.lineitem_cl;
DESCRIBE HISTORY demo_TU_NOMBRE.practica.lineitem_cl;
```

- `Type`: ______
- `Location`: ______
- Operaciones del historial: ______
- ¿Hay alguna operación que no lanzaste tú? ______ Si la hay, anota su columna `operation` y sus parámetros.

**Pregunta 2 (segunda parte).** ¿Qué te dice el historial sobre quién ha hecho qué en la tabla?

> Tu respuesta: ______

### Paso 3.5 · Explorar Unity Catalog con SQL

Lo que ves en Catalog Explorer (menú lateral *Catalog*) también se puede consultar con SQL, y así guardarlo o repetirlo.

```sql
SELECT table_catalog, table_schema, table_name, table_type, is_insertable_into
FROM samples.information_schema.tables
WHERE table_schema = 'tpch';

SELECT column_name, data_type, ordinal_position
FROM demo_TU_NOMBRE.information_schema.columns
WHERE table_name = 'lineitem_cl'
ORDER BY ordinal_position;
```

Fíjate en las tres primeras columnas de la primera consulta. ¿Qué niveles representan?

> Tu respuesta: ______

**Pregunta 3** *(solo si `is_insertable_into` sale `YES` en tu pantalla).* `information_schema` dice que las tablas de `samples` admiten inserciones, pero el paso 3.1 ha fallado. ¿Qué nos enseña?

> Tu respuesta: ______

### Paso 3.6 *(opcional)* · Tablas de sistema

Las tablas de sistema son un almacén de datos operativos de la cuenta: consultas, facturación, auditoría, cómputo y más. Intenta leer el historial de consultas. **Se espera otro error de permisos:** lee qué permiso falta y **no pulses *Request permissions***.

```sql
SELECT * FROM system.query.history LIMIT 5;
```

**Pregunta 4.** Para leer `system.query.history`, ¿qué permisos hacen falta en cada nivel?

> Tu respuesta: ______

---

## Conclusión

### Tabla resumen

Tienes un minuto para rellenarla. Después, el instructor da las respuestas. Si saltaste el paso 1.4, usa solo los datos que tengas.

| Pregunta | Tu respuesta |
|---|---|
| ¿Qué técnica ha reducido más los bytes leídos? | ______ |
| ¿En qué situación el filtro no ha evitado leer ficheros? | ______ |
| ¿Qué podías optimizar en tu tabla y no en `samples`? ¿Por qué? | ______ |

### Limpieza

> **Ejecuta este paso solo cuando lo indique el instructor.** Borra tu catálogo con todo lo que contiene. Primero cambiamos a `samples` para no estar dentro del catálogo que vamos a borrar.

```sql
USE CATALOG samples;
DROP CATALOG IF EXISTS demo_TU_NOMBRE CASCADE;
```

Tienes esta guía y el script en GitHub Pages. Puedes repetir la práctica en cualquier workspace que tenga el catálogo `samples`, siempre que tengas permiso para crear catálogos y una ubicación de almacenamiento para ellos. La próxima vez que una consulta vaya lenta, **empieza por el Query Profile.**

---

## Anexo A · Referencia de operadores del plan

El plan se lee **de dentro hacia fuera**: lo más indentado (los *scans*) es lo primero que ocurre. Este es el plan del paso 1.1; el tuyo puede mostrar alguna línea más, por ejemplo un operador de ordenación por el `ORDER BY`.

| Línea del plan | Qué es |
|---|---|
| `AdaptiveSparkPlan` | Nodo raíz de la ejecución adaptativa (AQE): el motor puede reajustar el plan **durante la ejecución**. El número entre paréntesis no hace falta para seguir la práctica. |
| `== Initial Plan ==` | Cabecera. Es el plan **antes** de que AQE lo modifique con los datos reales. |
| `PhotonResultStage` | Etapa final que entrega el resultado de la consulta. |
| `PhotonColumnarToRow` | Convierte los datos, que Photon maneja en lotes columnares, a filas para devolver el resultado. |
| `PhotonGroupingAgg` | Operador de agregación (`GROUP BY`). Aparece dos veces en este plan: es la pregunta 3 del paso 1.2. |
| `PhotonShuffleExchangeSource` / `Sink` | Redistribución de datos entre nodos. `Sink` escribe los datos redistribuidos y `Source` los lee en la etapa siguiente. |
| `PhotonProject` | Selecciona o calcula las columnas y expresiones necesarias. |
| `PhotonShuffledHashJoin Inner` | Join `INNER` con estrategia *shuffled hash*: las dos tablas se redistribuyen por la clave del join y, en cada partición, se construye una tabla hash con uno de los lados. No es un *broadcast join*. |
| `Shuffle` ← `PhotonProject` ← `PhotonScan parquet … orders` | Rama de `orders`: lee la tabla, se queda con las columnas necesarias y la redistribuye por la clave del join. |
| `Shuffle` ← `PhotonScan parquet … lineitem` | Rama de `lineitem`: lee la tabla y la redistribuye por la clave del join. |
| `== Optimizer Statistics ==` / `missing = …` | Qué estadísticas tenía el optimizador. `missing` indica que no tenía estadísticas de esas tablas. |
| `Corrective actions: … ANALYZE TABLE …` | Recomendación de calcularlas con ese comando. |

> **No confundas dos tipos de estadísticas.** Las de **fichero** (mínimo y máximo de cada columna) sirven para *data skipping*. Las del **optimizador** (filas, valores distintos…) las calcula `ANALYZE` y ayudan a elegir el plan.

## Anexo B · Glosario

| Concepto | Qué es |
|---|---|
| **SQL warehouse** | Recurso de cómputo dedicado a SQL. *Serverless*: lo gestiona Databricks, arranca rápido y se paga por uso. Es lo que usa el SQL Editor. |
| **Photon** | Motor de ejecución vectorizado de Databricks. Sus operadores aparecen con el prefijo `Photon` en el plan. |
| **`EXPLAIN`** | Muestra el plan que el motor prevé usar, sin ejecutar la consulta. Es el plan inicial. |
| **Query Profile** | Grafo de lo que realmente ejecutó el motor, con métricas por operador. Se abre desde *See performance* o desde Query History. |
| **AQE** (Adaptive Query Execution) | Reoptimización durante la ejecución con los tamaños reales. Puede cambiar, por ejemplo, la estrategia de un join. Por eso `EXPLAIN` muestra `isFinalPlan=false`. |
| **Shuffle** | Redistribución de datos entre nodos según una clave. Suele ser costosa. |
| **`BroadcastHashJoin`** | Join que envía la tabla pequeña completa a todos los nodos, evitando redistribuir la grande. |
| **Column pruning** | Leer solo las columnas necesarias, posible porque el formato (Parquet/Delta) es columnar. |
| **Data skipping** | Descartar ficheros completos usando el mínimo y el máximo de cada columna guardados por fichero. Se ve en `Files pruned`. |
| **Liquid clustering** (`CLUSTER BY`) | Organización física que agrupa filas con valores parecidos en los mismos ficheros. Sustituye a particionado y `ZORDER` en tablas nuevas. Requiere `OPTIMIZE` para reorganizar. |
| **`OPTIMIZE`** | Compacta ficheros y, en tablas con clustering, reorganiza las filas. |
| **`ANALYZE TABLE`** | Calcula estadísticas para el optimizador basado en costes. Requiere permiso `MODIFY`. |
| **CBO** (optimizador basado en costes) | Parte del optimizador que usa estadísticas para elegir, por ejemplo, el orden y el tipo de los joins. |
| **Tabla gestionada** (`MANAGED`) | Unity Catalog controla sus ficheros, su ubicación y su mantenimiento. Puede recibir *predictive optimization*. |
| **Tabla externa** | Los ficheros están en una ubicación que decide el usuario. Unity Catalog gobierna el acceso, pero no hace mantenimiento automático. |
| **Predictive optimization** | Mantenimiento automático (`OPTIMIZE`, `VACUUM`, `ANALYZE`) de tablas gestionadas de Unity Catalog. |
| **`MANAGED LOCATION`** | Ubicación de almacenamiento donde un catálogo o esquema guarda sus tablas gestionadas. |
| **`information_schema`** | Vistas SQL estándar con los metadatos de cada catálogo (tablas, columnas, etc.). Solo muestra lo que tus privilegios te permiten ver. |
| **Tablas de sistema** (`system.*`) | Historial de consultas, facturación y más, a nivel de cuenta. Acceso restringido. |
| **Caché de resultados** | Reutiliza el resultado de una consulta idéntica. Se desactiva con `SET use_cached_result = false;`. |
| **Caché de disco** | Copia local en el warehouse de los ficheros leídos recientemente. Se refleja en `Bytes read from cache`. No cambia cuántos ficheros se leen o se descartan. |
| **`Files read` / `Files pruned`** | Métricas de *See performance*: ficheros abiertos y ficheros descartados gracias al *data skipping*. |
| **`Spill`** | Escritura a disco de datos intermedios cuando no caben en memoria. Ralentiza la consulta. |
| **`VACUUM`** | Borra los ficheros que una tabla Delta ya no usa (versiones antiguas). Reduce almacenamiento. |
| **Genie Code** | Asistente de IA integrado en Databricks. Ayuda a escribir, corregir y explicar consultas. Puede actuar como agente y ejecutar código por su cuenta. Hay que comprobar sus respuestas. |
