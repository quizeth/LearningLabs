# Laboratorio 4: Crear y probar un agente con Microsoft 365 Copilot

## Introducción

Los agentes de Microsoft 365 Copilot permiten configurar un comportamiento especializado y reutilizable mediante instrucciones, fuentes de conocimiento e indicaciones sugeridas.

En este laboratorio crearás con **Agent Builder** un agente que hace una primera revisión de oportunidades de venta de **Corvalia Consultoría Tecnológica** antes de decidir si se presenta oferta (revisión **Go/No-Go**). Lo probarás con una oportunidad de riesgo, corregirás sus instrucciones y reutilizarás el mismo método con una segunda oportunidad.

No necesitas conocimientos jurídicos ni financieros: las reglas que aplica el agente están en dos políticas internas breves que se incluyen en el laboratorio.

Durante todo el curso usamos estas categorías para clasificar la información:

| Etiqueta | Significado |
|---|---|
| **HECHO** | Dato que aparece expresamente en la fuente. |
| **CÁLCULO** | Resultado de aplicar una operación explícita a hechos. |
| **INFERENCIA** | Interpretación que no aparece literalmente en la fuente. |
| **CONCLUSIÓN PROVISIONAL** | Valoración sujeta a revisión humana. |
| **NO CONSTA** | El dato no aparece en la fuente. Es una respuesta válida. |

!!! warning "Usa solo los archivos del laboratorio"
    Los seis archivos son ficticios. No cargues pliegos, estimaciones ni información real de clientes.

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Crear un agente mediante lenguaje natural con Agent Builder.
- Configurar instrucciones, conocimiento permanente e indicaciones sugeridas.
- Distinguir entre conocimiento permanente reutilizable y archivos de un caso concreto.
- Verificar que el agente calcula el valor del contrato y el margen, y clasifica los criterios según las reglas internas.
- Detectar un fallo observable y corregir las instrucciones del agente.
- Reutilizar el mismo agente con otro caso sin incorporarlo al conocimiento permanente.
- Comprobar las fuentes, los cálculos y los límites que requieren revisión humana.

## Información del laboratorio

**Duración estimada:** 45 minutos.

**Material necesario:**

- Microsoft 365 Copilot Chat con acceso a **Agent Builder** (opción **Nuevo agente** o **Crear agente**).
- Los seis archivos del caso:

| Archivo | Uso |
|---|---|
| [01_Politica_Ofertas_GoNoGo.docx](recursos/files/01_Politica_Ofertas_GoNoGo.docx) | Conocimiento permanente del agente |
| [02_Politica_Aprobacion_Margen.docx](recursos/files/02_Politica_Aprobacion_Margen.docx) | Conocimiento permanente del agente |
| [03_Pliego_Cliente_A.docx](recursos/files/03_Pliego_Cliente_A.docx) | Primer caso: requisitos del cliente |
| [04_Estimacion_Interna_A.docx](recursos/files/04_Estimacion_Interna_A.docx) | Primer caso: estimación de Corvalia |
| [05_Pliego_Cliente_B.docx](recursos/files/05_Pliego_Cliente_B.docx) | Segundo caso: requisitos del cliente |
| [06_Estimacion_Interna_B.docx](recursos/files/06_Estimacion_Interna_B.docx) | Segundo caso: estimación de Corvalia |

!!! note "Licencias"
    Con Copilot Chat sin licencia premium puedes crear agentes con Agent Builder. Sin embargo, las fuentes de conocimiento disponibles dependen de la licencia y de la configuración de tu organización: es posible que no puedas añadir archivos como conocimiento permanente.

    Si no ves esa opción, sigue la **ruta alternativa**: crea el agente igualmente y adjunta los archivos 01 y 02 en cada conversación junto con los archivos del caso. El objetivo de aprendizaje se mantiene.

!!! warning "Si no puedes crear agentes"
    Tu organización puede tener desactivada la creación de agentes. En ese caso, abre una conversación en Copilot Chat, pega la descripción de la Tarea 1 como primer mensaje y adjunta los archivos 01 y 02 junto con los del caso. Mantendrás el método, pero no el comportamiento reutilizable: tendrás que repetirlo en cada conversación.

!!! warning "Separación entre reglas y casos"
    **Conocimiento permanente = reglas reutilizables.** Los pliegos y las estimaciones de cada oportunidad son archivos del caso y nunca se añaden al conocimiento del agente.

## Caso práctico

El equipo de preventa de Corvalia recibe cada mes varias solicitudes de oferta. Preparar una oferta cuesta tiempo y dinero, así que antes de empezar hay que responder a una pregunta: **¿merece la pena presentarse?**

Esa decisión la toma una persona (o el Comité de Ofertas), pero necesita una primera revisión homogénea. El agente no decidirá si se presenta la oferta ni aprobará precios. Su función será:

- Extraer las condiciones del pliego del cliente y de la estimación interna.
- Calcular el valor total del contrato y el margen.
- Comparar la oportunidad con las políticas internas.
- Clasificar cada criterio como VERDE, ÁMBAR o ROJO.
- Indicar quién tendría que aprobar la oferta y qué falta por saber.

Probarás el agente con dos oportunidades:

- **Caso A:** Transportes Brezo, una plataforma de gestión de flota.
- **Caso B:** Editorial Marea, un portal para autores.

---

### Tarea 1: Configurar el agente

#### Objetivo

Crear mediante lenguaje natural un agente que aplique un método reutilizable de revisión Go/No-Go.

#### Pasos

1. Abre Microsoft 365 Copilot Chat.
2. En el panel lateral, selecciona **Agentes** y después **Nuevo agente** o **Crear agente**.
3. Utiliza la creación mediante lenguaje natural y envía esta descripción:

    ```text
    Crea un agente llamado «Revisor Go/No-Go de Oportunidades» para hacer una primera revisión interna de oportunidades de venta de Corvalia Consultoría Tecnológica antes de decidir si se presenta oferta.

    Debe:
    1. Extraer las condiciones relevantes del pliego del cliente y de la estimación interna que adjunte el usuario.
    2. Compararlas con la política de ofertas Go/No-Go y la política de aprobación por margen e importe incluidas en su conocimiento.
    3. Clasificar cada afirmación relevante como HECHO, CÁLCULO, INFERENCIA o NO CONSTA.
    4. Calcular el valor total del contrato, el coste total y el margen bruto cuando existan datos suficientes, mostrando la fórmula y la sustitución numérica.
    5. Clasificar cada criterio como VERDE, ÁMBAR o ROJO según la política de ofertas.
    6. Indicar qué aprobación interna sería necesaria según la política de aprobación.
    7. Preparar una tabla final con estas columnas: criterio, condición encontrada, regla interna, clasificación y fuente.
    8. Presentar la valoración global como CONCLUSIÓN PROVISIONAL.
    9. Terminar con «Preguntas abiertas», «Siguientes pasos» y «Revisión humana necesaria».

    Nunca debe decidir si se presenta o no la oferta, aprobar precios ni sustituir a Preventa, Finanzas, Legal o al Comité de Ofertas.
    Si falta información, debe escribir NO CONSTA, no clasificar ese criterio y solicitar el dato.
    Debe identificar la fuente concreta de cada conclusión importante.
    ```

4. Abre la pestaña **Configurar**.
5. Revisa el nombre, la descripción y las instrucciones generadas.
6. Corrige cualquier formulación que permita al agente decidir, aprobar o recomendar presentarse o no.

#### Comprobación

- Agent Builder ha generado una primera configuración.
- El agente se llama **Revisor Go/No-Go de Oportunidades**.
- La descripción indica que su finalidad es una revisión preliminar, no una decisión.
- Las instrucciones exigen separar HECHO, CÁLCULO, INFERENCIA y NO CONSTA.
- Las instrucciones exigen mostrar fórmulas e identificar fuentes.
- Las instrucciones impiden que el agente decida si se presenta la oferta.

#### Reflexiona

- ¿Qué partes de la descripción ha convertido Agent Builder en instrucciones y cuáles ha resumido u omitido?
- ¿Por qué conviene revisar las instrucciones generadas en lugar de aceptarlas sin más?

---

### Tarea 2: Configurar el conocimiento permanente

#### Objetivo

Proporcionar al agente las reglas reutilizables sin incorporar los casos concretos de Transportes Brezo o Editorial Marea.

#### Pasos

1. En la pestaña **Configurar**, localiza **Conocimiento**.
2. Si puedes cargar archivos, añade solo estos dos:
    - 01_Politica_Ofertas_GoNoGo.docx
    - 02_Politica_Aprobacion_Margen.docx
3. Si no puedes cargar archivos, no añadas nada: adjuntarás los archivos 01 y 02 en cada conversación (ruta alternativa).
4. No añadas los pliegos ni las estimaciones (archivos 03 a 06).
5. En **Indicaciones sugeridas**, añade:

    ```text
    Revisa esta oportunidad y dime qué criterios están en ROJO.
    ```

    ```text
    Calcula el valor del contrato y el margen, y dime quién debe aprobar la oferta.
    ```

    ```text
    Resume los riesgos de esta oportunidad para el Comité de Ofertas.
    ```

6. Selecciona **Crear** y comprueba que el agente aparece en **Agentes**.

#### Comprobación

- Las reglas llegan al agente por una de estas dos rutas:
    - Los archivos 01 y 02 como conocimiento permanente.
    - Los archivos 01 y 02 preparados para adjuntarlos en cada conversación.
- Ningún pliego ni estimación forma parte del conocimiento permanente.
- Las indicaciones sugeridas aparecen en la configuración.
- El agente está creado antes de adjuntar archivos de un caso.

#### Reflexiona

- ¿Qué problema produciría añadir el pliego de un cliente al conocimiento permanente?
- ¿Qué información podrá reutilizar el agente en todas las oportunidades?
- ¿Por qué la ruta alternativa mantiene el objetivo didáctico aunque no haya conocimiento permanente?

---

### Tarea 3: Probar el agente con el caso A (Transportes Brezo)

#### Objetivo

Comprobar que el agente aplica el método configurado a una oportunidad de riesgo, usa las reglas internas y no toma decisiones.

#### Pasos

1. En **Agentes**, abre **Revisor Go/No-Go de Oportunidades**.
2. Inicia una conversación nueva con el agente.
3. Adjunta:
    - 03_Pliego_Cliente_A.docx
    - 04_Estimacion_Interna_A.docx
    - Si sigues la ruta alternativa, también 01 y 02.
4. Envía este prompt:

    ```text
    Haz la revisión completa de esta oportunidad.

    Incluye:
    - Resumen de la oportunidad.
    - Cálculos con fórmula: valor total del contrato, coste total y margen bruto.
    - Tabla de criterios con su clasificación y fuente.
    - Aprobación interna necesaria.
    - Preguntas abiertas.
    - Revisión humana necesaria.

    Aplica estrictamente tu conocimiento y muestra la fuente de cada conclusión importante.
    ```

5. Compara la respuesta con la tabla de validación de la sección *Comprobación*. Evalúa el método, no busques una redacción idéntica.

#### Comprobación

Validación mínima:

| Comprobación | Resultado esperado |
|---|---|
| Valor total del contrato | 330.000 + 120.000 × 3 = **690.000 €** |
| Coste total | 310.000 + 95.000 × 3 = **595.000 €** |
| Margen bruto | (690.000 − 595.000) / 690.000 × 100 = **13,77 %** → ÁMBAR |
| Plazo de implantación | (6 − 4) / 6 × 100 = **33,33 %** de reducción → ROJO |
| Penalizaciones | Sin tope → ROJO |
| Limitación de responsabilidad | 3 veces el valor del contrato → ROJO |
| Condiciones de pago | 120 días → ROJO |
| Equipo disponible | Falta un perfil clave sin alternativa → ROJO |
| Experiencia previa | **NO CONSTA**: no se clasifica y se pide el dato |
| Aprobación necesaria | Comité de Ofertas y, además, Finanzas |

Comprueba también que:

- Cada cálculo muestra la fórmula y la sustitución.
- Las fuentes permiten encontrar cada dato en su archivo.
- El agente separa HECHO, CÁLCULO, INFERENCIA y NO CONSTA.
- El agente no recomienda «Go» ni «No-Go» ni aprueba la oferta.
- Los archivos del caso A se han usado en la conversación y no como conocimiento permanente.

#### Reflexiona

- ¿Qué hallazgos dependen del pliego o de la estimación y cuáles proceden de las reglas internas?
- ¿Cómo ha tratado el agente la experiencia previa: la ha marcado como NO CONSTA o la ha supuesto?
- ¿Qué conclusiones siguen necesitando a Preventa, Finanzas, Legal o al Comité de Ofertas?

---

### Tarea 4: Corregir el comportamiento

#### Objetivo

Identificar un problema observable en la respuesta del agente y corregirlo modificando sus instrucciones.

#### Pasos

1. Busca un fallo concreto en la respuesta del caso A. Por ejemplo:
    - Recomienda presentarse o no presentarse.
    - No muestra la fórmula del margen o lo calcula sobre el coste en lugar del precio.
    - Clasifica un criterio que no consta.
    - No identifica las fuentes.
    - Mezcla hechos e inferencias.
    - No incluye la revisión humana.
2. Si no encuentras ningún fallo, aplica igualmente la corrección del paso 4 como refuerzo.
3. Vuelve a la edición del agente.
4. Envía esta instrucción, adaptándola al fallo que hayas detectado:

    ```text
    Modifica el agente para que:
    - Nunca recomiende «Go» ni «No-Go»; solo resuma los criterios, su clasificación y la aprobación necesaria.
    - Calcule el margen bruto según la fórmula de la política de aprobación y muestre la sustitución numérica.
    - Marque como NO CONSTA cualquier criterio sin datos, sin clasificarlo.
    - Identifique la fuente de cada conclusión importante.
    - Termine siempre con aprobaciones requeridas, preguntas abiertas y revisión humana necesaria.
    ```

5. Guarda o aplica los cambios.
6. Abre una conversación nueva con el agente, adjunta los mismos archivos del caso A y envía el mismo prompt de la Tarea 3.
7. Compara la segunda respuesta con la primera.

#### Comprobación

- La segunda respuesta corrige el fallo detectado.
- Lo que ya funcionaba se mantiene.
- El agente no emite ninguna recomendación de Go/No-Go.
- Las fórmulas, las fuentes y la revisión humana aparecen de forma explícita.

#### Reflexiona

- ¿El fallo procedía de las instrucciones del agente o de los archivos del caso?
- ¿La corrección mejora el agente para cualquier oportunidad o solo para el caso A?
- ¿Qué evidencia demuestra que la segunda respuesta es mejor que la primera?

---

### Tarea 5: Reutilizar el agente con el caso B (Editorial Marea)

#### Objetivo

Reutilizar el mismo agente y las mismas reglas con otra oportunidad, manteniendo separados el conocimiento permanente y los archivos del caso.

#### Pasos

1. Abre el agente **Revisor Go/No-Go de Oportunidades**.
2. Inicia una conversación nueva.
3. Adjunta:
    - 05_Pliego_Cliente_B.docx
    - 06_Estimacion_Interna_B.docx
    - Si sigues la ruta alternativa, también 01 y 02.
4. Envía este prompt:

    ```text
    Haz la misma revisión que aplicarías a cualquier oportunidad.
    Usa exactamente el mismo método y las mismas reglas internas.
    Muestra los cálculos, la clasificación, las fuentes, la aprobación necesaria y la revisión humana necesaria.
    ```

5. Compara el resultado con el del caso A.

#### Comprobación

Validación mínima:

| Comprobación | Resultado esperado |
|---|---|
| Valor total del contrato | 96.000 + 18.000 × 2 = **132.000 €** (sin IVA) |
| Coste total | 72.000 + 12.600 × 2 = **97.200 €** |
| Margen bruto | (132.000 − 97.200) / 132.000 × 100 = **26,36 %** → VERDE |
| Plazo de implantación | 5 meses en el pliego, igual o superior a los 4,5 estimados → VERDE |
| Penalizaciones | Tope del 10 % → VERDE |
| Limitación de responsabilidad | 1 vez el valor del contrato → VERDE |
| Condiciones de pago | 75 días → ÁMBAR |
| Equipo disponible | Todos los perfiles disponibles → VERDE |
| Experiencia previa | Dos proyectos similares → VERDE |
| Aprobación necesaria | Director/a de cuenta |

Comprueba también que:

- El agente ha usado el mismo método que en el caso A.
- Revisa qué importe ha usado para decidir la aprobación: ¿el precio con IVA del pliego o el precio sin IVA de la estimación? ¿Qué dice la política?
- La clasificación refleja las condiciones del caso B, no las del caso A.
- Los archivos del caso B no se han añadido al conocimiento permanente.

!!! note "Conclusión didáctica"
    El caso A debería producir varios criterios en ROJO y el caso B, casi todos en VERDE. La diferencia procede de los casos, no del método: el agente y sus reglas son los mismos.

#### Reflexiona

- ¿Qué elementos de la revisión se mantienen entre los dos casos?
- ¿Qué diferencias proceden exclusivamente de los archivos de cada caso?
- ¿Por qué el caso B obtiene otra clasificación sin modificar las instrucciones ni el conocimiento?

---

## Comprobación final

Comprueba que:

- Has creado un agente reutilizable con Agent Builder.
- Has proporcionado las reglas como conocimiento permanente o adjuntándolas en cada conversación.
- Puedes distinguir entre conocimiento permanente y archivos del caso.
- Ningún pliego ni estimación forma parte del conocimiento permanente.
- Has ejecutado y validado el caso A.
- Has detectado y corregido un fallo en el comportamiento del agente.
- Has reutilizado el agente con el caso B.
- El agente muestra fórmulas, clasificaciones, fuentes, aprobaciones necesarias y revisión humana.
- El agente ha distinguido HECHO, CÁLCULO, INFERENCIA, CONCLUSIÓN PROVISIONAL y NO CONSTA.
- El agente no decide si se presenta la oferta.

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Chat** | Adecuado para tareas puntuales y flexibles. |
| **Cuadernos de Copilot** | Adecuados para analizar un conjunto acotado de referencias. |
| **Agente** | Mantiene un método reutilizable con instrucciones y conocimiento persistentes. |
| **Agent Builder** | Permite crear y configurar agentes mediante lenguaje natural dentro de Microsoft 365 Copilot. |
| **Conocimiento permanente** | Reglas estables que se aplican a todos los casos. |
| **Archivos del caso** | Información variable de cada oportunidad. Nunca forma parte del conocimiento permanente. |
| **Ruta alternativa** | Adjuntar las reglas en cada conversación cuando no se pueden cargar como conocimiento. |
| **Corrección de instrucciones** | Mejora el comportamiento del agente para todos los casos, no solo para el que falló. |
| **Revisión humana** | El agente analiza; la decisión de presentar la oferta es de las personas. |

## Recursos adicionales

- [01_Politica_Ofertas_GoNoGo.docx](recursos/files/01_Politica_Ofertas_GoNoGo.docx)
- [02_Politica_Aprobacion_Margen.docx](recursos/files/02_Politica_Aprobacion_Margen.docx)
- [03_Pliego_Cliente_A.docx](recursos/files/03_Pliego_Cliente_A.docx)
- [04_Estimacion_Interna_A.docx](recursos/files/04_Estimacion_Interna_A.docx)
- [05_Pliego_Cliente_B.docx](recursos/files/05_Pliego_Cliente_B.docx)
- [06_Estimacion_Interna_B.docx](recursos/files/06_Estimacion_Interna_B.docx)
- [Información general de Agent Builder (Microsoft Learn, en inglés)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder)
- [Agregar fuentes de conocimiento a un agente en Agent Builder (Microsoft Learn, en inglés)](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agent-builder-add-knowledge)
- [Agentes en Copilot Chat (Microsoft Learn, en inglés)](https://learn.microsoft.com/en-us/copilot/manage-agents-copilot-chat)
