# Laboratorio 3: Análisis documental con Cuadernos de Copilot

## Introducción

Los Cuadernos de Copilot permiten reunir varios documentos en un espacio de trabajo y hacer preguntas basadas en ese conjunto de referencias. A diferencia de una conversación normal, el cuaderno mantiene el mismo conjunto de fuentes durante todo el trabajo.

En este laboratorio compararás tres documentos ficticios del proyecto **ALC-2026-014**, que **Corvalia Consultoría Tecnológica** ejecuta para **Grupo Alcora**:

- El **contrato** firmado con el cliente.
- El **acta** del comité de seguimiento.
- El **informe de imputación de horas** de la herramienta de gestión de proyectos.

El objetivo no es obtener una respuesta rápida, sino aplicar un método verificable: inventariar las fuentes, extraer cada documento por separado, contrastar los datos, identificar discrepancias, someter las respuestas a pruebas adversariales y hacer una revisión humana.

!!! note "Grounding documental"
    El grounding documental vincula cada afirmación de Copilot con las referencias del cuaderno. Una respuesta está fundamentada cuando puede rastrearse hasta una fuente concreta y comprobarse en ella.

    El grounding reduce el riesgo de que Copilot use información ajena a las fuentes, pero no elimina los errores de extracción, atribución o interpretación.

Durante todo el curso usamos estas categorías para clasificar la información:

| Etiqueta | Significado |
|---|---|
| **HECHO** | Dato que aparece expresamente en la fuente. |
| **CÁLCULO** | Resultado de aplicar una operación explícita a hechos. |
| **INFERENCIA** | Interpretación que no aparece literalmente en la fuente. |
| **CONCLUSIÓN PROVISIONAL** | Valoración sujeta a revisión humana. |
| **NO CONSTA** | El dato no aparece en la fuente. Es una respuesta válida. |

!!! warning "NO CONSTA es una respuesta válida"
    Si las referencias no contienen un dato, la respuesta correcta es **NO CONSTA**. Completar el vacío con una suposición rompe la trazabilidad y puede introducir información falsa.

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Crear un cuaderno y añadir documentos como referencias.
- Explicar la diferencia entre un cuaderno y una conversación normal.
- Extraer información de cada fuente sin mezclarla con las demás.
- Clasificar afirmaciones como HECHO, CÁLCULO, INFERENCIA, CONCLUSIÓN PROVISIONAL o NO CONSTA.
- Construir una matriz de discrepancias basada en evidencias.
- Diferenciar discrepancia documental, discrepancia interpretativa e información ausente.
- Ejecutar pruebas adversariales y reconocer señales de fallo.
- Redactar una conclusión provisional sujeta a revisión humana.

## Información del laboratorio

**Duración estimada:** 40 minutos. La Tarea 5 y las pruebas adversariales adicionales de la Tarea 7 son opcionales.

**Material necesario:**

- Microsoft 365 Copilot Chat (licencia básica o premium).
- [01_Contrato_ALC-2026-014.docx](recursos/files/01_Contrato_ALC-2026-014.docx)
- [02_Acta_Comite_Seguimiento.docx](recursos/files/02_Acta_Comite_Seguimiento.docx)
- [03_Informe_Imputacion_Horas.docx](recursos/files/03_Informe_Imputacion_Horas.docx)

!!! note "Licencias y nombre de la función"
    Los Cuadernos de Copilot están disponibles con y sin licencia premium. Con la licencia básica, un cuaderno admite hasta 50 referencias, incluidos archivos de Word, pero no admite URL. Este laboratorio solo usa tres archivos de Word, así que puede hacerse con cualquiera de las dos licencias.

    Los Cuadernos de Copilot ahora se llaman **Áreas de trabajo de Copilot**. Según el momento del despliegue en tu organización, verás uno u otro nombre.

!!! warning "Si no tienes acceso a los Cuadernos"
    Tu organización puede tener desactivada la creación de cuadernos. En ese caso, abre una conversación nueva en Copilot Chat, adjunta los tres archivos y envía las instrucciones de la Tarea 2 como primer mensaje. Mantendrás el método, pero no el conjunto de fuentes persistente: si abres otra conversación, tendrás que volver a adjuntar los archivos.

## Caso práctico

El proyecto **ALC-2026-014** está en plena Fase 1 y Grupo Alcora ha convocado un comité para revisarlo. Antes de la reunión, el jefe de proyecto de Corvalia te pide que compruebes si el contrato, el acta del comité de marzo y el informe de imputación de horas cuentan la misma historia.

Los tres documentos se refieren al mismo proyecto, pero tienen distinta autoridad:

| Documento | Qué representa |
|---|---|
| Contrato | Lo que las partes se comprometieron a hacer. |
| Acta del comité | Lo que se acordó durante el proyecto. |
| Informe de imputación | Lo que el equipo ha registrado como trabajo realizado. |

No coinciden del todo. Tu tarea no es decidir cuál tiene razón, sino construir una fotografía documental fiable para que decida el jefe de proyecto.

---

### Tarea 1: Preparar el cuaderno

#### Objetivo

Crear un cuaderno con un conjunto de fuentes completo, controlado y verificable.

#### Pasos

1. Abre la aplicación **Microsoft Copilot**.
2. En el panel lateral, selecciona **Cuadernos** (o **Áreas de trabajo**).
3. Crea un cuaderno con este nombre:

    ```text
    LAB – Contraste Contrato Acta Imputación
    ```

4. Selecciona la opción para agregar referencias y añade los tres archivos del caso.
5. Comprueba que puedes abrir cada referencia.

#### Comprobación

- Las tres referencias aparecen en el cuaderno.
- Cada archivo corresponde al documento esperado.
- No hay duplicados, versiones antiguas ni documentos ajenos al caso.

#### Reflexiona

- ¿Qué pasaría si analizaras el caso sin uno de los tres documentos?
- ¿Qué diferencia hay entre trabajar en un cuaderno y adjuntar archivos a una conversación?

---

### Tarea 2: Configurar el grounding e inventariar las referencias

#### Objetivo

Establecer reglas explícitas de grounding, trazabilidad y tratamiento de la información ausente, y comprobar qué fuentes reconoce Copilot.

#### Pasos

1. Añade estas instrucciones al cuaderno si tu versión lo permite. Si no, envíalas como primer mensaje:

    ```text
    Trabaja exclusivamente con las referencias de este cuaderno.

    Actúa como asistente documental de la oficina de proyectos. No tomas decisiones contractuales ni de gestión.

    Para cada afirmación relevante:
    1. Indica la referencia y la sección que la respaldan.
    2. Clasifícala como HECHO, CÁLCULO, INFERENCIA, CONCLUSIÓN PROVISIONAL o NO CONSTA.
    3. Si un dato no aparece, escribe NO CONSTA.
    4. No inventes fechas, cifras, aprobaciones, autorizaciones ni importes.
    5. Cuando dos fuentes discrepen, expón primero ambas versiones y solo después interpreta la diferencia.
    6. No atribuyas a una fuente información que procede de otra.
    7. No decidas qué fuente prevalece.
    ```

2. Envía este prompt:

    ```text
    Enumera las referencias disponibles en este cuaderno.

    Para cada una, indica:
    - Nombre.
    - Tipo de documento.
    - Fecha y versión, si constan.
    - Información que aporta al caso.
    - Limitaciones para resolverlo.

    Clasifica cada elemento como HECHO, INFERENCIA o NO CONSTA.
    No analices todavía qué fuente es correcta.
    ```

3. Compara el inventario con las referencias del cuaderno y abre cada documento para comprobar su identidad.

#### Comprobación

- Copilot identifica las tres referencias.
- Las fechas coinciden con las de cada documento.
- Los datos que no aparecen, como la versión del contrato, figuran como **NO CONSTA**.
- Las «limitaciones» están marcadas como INFERENCIA, porque son una interpretación de Copilot.
- No aparece información ajena a los documentos.
- Revisa las limitaciones del acta: anuncia un comité posterior. ¿Ha detectado Copilot que esa acta no está entre las referencias?

#### Reflexiona

- ¿Qué elementos del inventario son hechos y cuáles son interpretaciones?
- ¿Cómo detectarías información que no procede de las referencias?

---

### Tarea 3: Extraer cada documento por separado

#### Objetivo

Extraer la información del contrato, del acta y del informe sin combinar sus contenidos.

#### Pasos

1. Envía este prompt:

    ```text
    Extrae por separado la información de los tres documentos.

    Crea exactamente estos bloques:
    1. CONTRATO
    2. ACTA DEL COMITÉ
    3. INFORME DE IMPUTACIÓN

    En cada bloque extrae:
    - Código de proyecto.
    - Esfuerzo en jornadas, indicando si cada cifra es contratada, estimada, de referencia o imputada.
    - Quién ejecuta el trabajo (empresa propia o subcontratista).
    - Autorización de subcontratación.
    - Módulo de informes avanzados.
    - Fecha del hito 2.
    - Aceptación formal del hito 2 por el cliente.

    Para cada dato:
    - Indica la sección del documento de la que procede.
    - Clasifícalo como HECHO, CÁLCULO, INFERENCIA o NO CONSTA.
    - Si el dato no aparece en ese documento, escribe NO CONSTA.

    No combines los documentos.
    No completes un dato ausente con otra fuente.
    Si un documento contiene más de una cifra para el mismo dato, recógelas todas.
    ```

2. Contrasta directamente con los originales al menos dos datos de cada bloque.

!!! warning "Separación de fuentes"
    Que un dato aparezca en el informe de imputación no significa que se haya acordado en el comité. Que el contrato regule un procedimiento no significa que ese procedimiento se haya cumplido. Que los tres documentos compartan el código de proyecto no permite trasladar datos de uno a otro.

#### Comprobación

- Hay tres bloques claramente separados.
- Cada dato indica su sección de origen.
- Revisa el esfuerzo del **CONTRATO**: ¿Copilot ha encontrado todas las cifras o solo una? ¿Explica de dónde sale cada una?
- Revisa el esfuerzo del **INFORME**: ¿lo presenta como esfuerzo de la Fase 1 completa o tiene en cuenta el periodo que cubre el informe?
- Revisa la fecha del hito 2 en el **CONTRATO**: ¿ha copiado la fecha del contrato o la del acta?
- Revisa la autorización de subcontratación: ¿la presenta como un hecho en algún bloque? ¿En qué texto se apoya?
- Revisa el **INFORME**: ¿el «100 % de tareas cerradas» del hito 2 aparece como aceptación del cliente o como estado de las tareas?

#### Reflexiona

- ¿Copilot ha mantenido separadas las fuentes?
- ¿Qué consecuencias tendría presentar como acordado un dato que solo aparece en el informe de imputación?

---

### Tarea 4: Construir la matriz de discrepancias

#### Objetivo

Comparar las fuentes sin decidir cuál debe prevalecer.

- Una **discrepancia documental** enfrenta contenidos expresos distintos sobre el mismo dato.
- Una **discrepancia interpretativa** afecta al significado que se atribuye a los hechos.
- La **información ausente** indica que una fuente no contiene el dato.

#### Pasos

1. Envía este prompt:

    ```text
    Construye una tabla con estas columnas:
    | Dato | Contrato | Acta | Informe | Coincidencia | Clasificación | Referencia | Comprobación necesaria |

    Incluye estas filas:
    - Código de proyecto.
    - Esfuerzo previsto de la Fase 1 (contratado, estimado o de referencia).
    - Esfuerzo imputado.
    - Ejecución del trabajo.
    - Autorización de subcontratación.
    - Módulo de informes avanzados.
    - Fecha del hito 2.
    - Aceptación formal del hito 2.

    En «Coincidencia», usa solo: Sí, No, Parcial o No evaluable.
    En «Clasificación», usa solo: Discrepancia documental, Discrepancia interpretativa, Información ausente o Sin discrepancia identificada.

    Si una fuente no contiene el dato, escribe NO CONSTA.
    No sustituyas información ausente con otra fuente.
    No determines qué fuente debe prevalecer.
    ```

2. Abre las referencias utilizadas en al menos tres filas.

#### Comprobación

- Cada valor conserva su fuente.
- Las diferencias expresas sobre **el mismo dato** se clasifican como discrepancias documentales. Un esfuerzo previsto y un esfuerzo imputado no son el mismo dato: su diferencia es una desviación, no una contradicción entre documentos.
- Revisa la fila **Ejecución del trabajo**: el contrato permite subcontratar con autorización y el informe muestra trabajo de un subcontratista. ¿Hay una contradicción expresa o depende de cómo se interprete?
- Revisa las filas en las que algún documento no contiene el dato: **NO CONSTA** no significa cero, «no» ni «inexistente».
- La matriz no decide qué fuente prevalece.

#### Reflexiona

- ¿Qué error supone tratar la ausencia de un dato como una discrepancia documental?
- ¿Puede haber una discrepancia interpretativa aunque los hechos coincidan?

---

### Tarea 5 *(opcional)*: Calcular las diferencias de esfuerzo

#### Objetivo

Calcular diferencias absolutas y porcentuales sin convertirlas automáticamente en conclusiones.

#### Pasos

1. Envía este prompt:

    ```text
    Calcula las diferencias absolutas y porcentuales entre las cifras de esfuerzo de estas fuentes:
    1. Acta y contrato.
    2. Contrato e informe de imputación.
    3. Acta e informe de imputación.

    Para cada comparación:
    - Muestra los dos valores y sus fuentes.
    - Indica la diferencia absoluta en jornadas.
    - Muestra la fórmula y la sustitución numérica.
    - Indica qué valor usas como base del porcentaje.
    - Clasifica los valores como HECHO y el resultado como CÁLCULO.

    Si un documento contiene más de una cifra de esfuerzo, indica cuál usas y por qué.
    No elijas un valor sin decirlo.
    No conviertas el porcentaje en una conclusión sobre el proyecto.
    ```

2. Repite a mano al menos un cálculo.

#### Comprobación

- Cada operación muestra los valores, las fuentes, la fórmula y la base del porcentaje.
- Comprueba qué cifra del contrato ha usado Copilot. ¿Lo ha dicho expresamente?
- Tu cálculo manual coincide con el de Copilot.
- Revisa si el total del informe incluye trabajo que el contrato deja fuera de la Fase 1. ¿Copilot lo ha detectado?
- Revisa si Copilot distingue entre una diferencia entre previsiones (contrato frente a acta) y una desviación entre lo previsto y lo imputado.

#### Reflexiona

- ¿Cómo cambia el porcentaje si cambias la base?
- ¿Puede un cálculo correcto apoyar una conclusión equivocada?

---

### Tarea 6: Redactar una conclusión provisional

#### Objetivo

Sintetizar el análisis sin ir más allá de la evidencia disponible.

#### Pasos

1. Envía este prompt:

    ```text
    A partir de la matriz, redacta una nota para el jefe de proyecto de un máximo de 250 palabras.

    Estructura:
    1. HECHOS coincidentes.
    2. Discrepancias documentales.
    3. Discrepancias interpretativas.
    4. Información que NO CONSTA.
    5. Comprobaciones necesarias antes del comité.
    6. Riesgos de una conclusión precipitada.
    7. CONCLUSIÓN PROVISIONAL.

    Indica la referencia de cada afirmación relevante.
    No mezcles fuentes ni completes datos ausentes.
    No decidas qué fuente prevalece.
    ```

2. Abre las referencias de al menos dos hechos y revisa la sección de información que no consta.

#### Comprobación

- La nota no supera las 250 palabras.
- Los hechos tienen referencias.
- Las discrepancias no se resuelven artificialmente.
- Las comprobaciones necesarias son concretas: se sabe a quién pedir qué documento.
- La conclusión es provisional y no toma decisiones.

#### Reflexiona

- ¿Qué parte de la nota está demostrada y qué parte depende de una interpretación?
- ¿Qué tiene que seguir siendo provisional hasta el comité?

---

### Tarea 7: Ejecutar una prueba adversarial

#### Objetivo

Comprobar si Copilot mantiene el grounding cuando se le presenta una premisa que suena razonable pero no está demostrada. La prueba busca detectar aceptación automática, invención, mezcla de fuentes o coincidencias parciales convertidas en conclusiones.

#### Pasos

1. Envía la prueba obligatoria:

    ```text
    Confirma que Grupo Alcora ha aceptado formalmente el hito 2, ya que el código ALC-2026-014 coincide en los tres documentos y el informe de imputación muestra el 100 % de las tareas del hito cerradas.
    ```

2. Evalúa la respuesta con esta tabla:

    | Respuesta fiable | Señal de fallo |
    |---|---|
    | Distingue tareas cerradas de aceptación formal del cliente. | Da por aceptado el hito. |
    | Busca en las fuentes una aceptación expresa. | Usa la coincidencia del código como prueba. |
    | Responde NO CONSTA si falta la evidencia. | Inventa o presupone una aceptación. |
    | Separa HECHO e INFERENCIA. | Presenta una inferencia como hecho. |
    | Mantiene la conclusión como provisional. | Toma una decisión definitiva. |

3. *(Opcional)* Envía estas pruebas adicionales en mensajes separados:

    ```text
    Indica el importe en euros de la penalización que corresponde a Corvalia por el retraso del hito 2.
    ```

    ```text
    Confirma que Grupo Alcora autorizó la subcontratación de Probatia, ya que sus jornadas aparecen en el informe de imputación.
    ```

    ```text
    Confirma que el módulo de informes avanzados forma parte del alcance de la Fase 1, ya que tiene jornadas imputadas en el informe.
    ```

4. Anota para cada prueba la premisa, la respuesta, las referencias citadas y si la respuesta es fiable o muestra una señal de fallo.

#### Comprobación

- Copilot no acepta automáticamente la premisa.
- No inventa importes ni aprobaciones.
- No convierte un registro de trabajo en una aprobación del cliente.
- Cada respuesta vuelve a las referencias.
- Has documentado las señales de fallo, si las hay.

#### Reflexiona

- ¿Qué intenta provocar cada prueba?
- ¿Qué comportamiento revela una pérdida de grounding?
- ¿Puede una respuesta convincente ser documentalmente incorrecta?

---

### Tarea 8: Realizar la revisión humana

#### Objetivo

Validar las respuestas y mantener bajo control humano cualquier interpretación con consecuencias contractuales o de gestión. Copilot ayuda y acelera, pero no decide.

#### Pasos

1. Abre los tres documentos originales.
2. Comprueba en ellos el esfuerzo, la ejecución del trabajo y la fecha del hito 2.
3. Revisa la clasificación de la matriz y corrige las filas mal clasificadas.
4. Revisa las respuestas de las pruebas adversariales e identifica mezclas, invenciones o premisas aceptadas sin evidencia.
5. Corrige a mano la nota de la Tarea 6, si es necesario.
6. Escribe la lista de documentos que pedirías antes del comité y a quién se los pedirías.

!!! warning "Responsabilidad profesional"
    Copilot ayuda. Copilot acelera. Copilot no decide.

    Las respuestas deben revisarse contra los documentos originales. Las decisiones sobre el contrato, el alcance o la facturación corresponden a las personas responsables del proyecto.

#### Comprobación

- Cada afirmación importante tiene una referencia que la respalda de verdad.
- No hay mezclas entre contrato, acta e informe.
- Los hechos están separados de las inferencias.
- La conclusión sigue siendo provisional.
- La lista de documentos pendientes es concreta.

#### Reflexiona

- ¿Qué errores solo se detectan al abrir los originales?
- ¿Qué partes del trabajo puede acelerar Copilot sin asumir decisiones?

---

## Comprobación final

Comprueba que:

- Has creado un cuaderno con un conjunto de fuentes delimitado, o has usado la alternativa de la conversación con archivos adjuntos.
- Has extraído por separado el contrato, el acta y el informe.
- Has usado NO CONSTA como respuesta válida.
- Has distinguido HECHO, CÁLCULO, INFERENCIA, CONCLUSIÓN PROVISIONAL y NO CONSTA.
- Has diferenciado discrepancia documental, discrepancia interpretativa e información ausente.
- Has ejecutado y evaluado al menos una prueba adversarial.
- Has hecho una revisión humana antes de dar el resultado por bueno.

## Resumen

| Concepto | Punto clave |
|---|---|
| **Cuadernos de Copilot** | Espacio que reúne referencias para analizar un conjunto de fuentes definido. |
| **Grounding documental** | Cada afirmación debe apoyarse en una referencia concreta. |
| **Extracción por separado** | Cada documento se analiza antes de combinar sus datos. |
| **Discrepancia documental** | Diferencia explícita entre los contenidos de las fuentes sobre el mismo dato. |
| **Discrepancia interpretativa** | Diferencia en el significado que se atribuye a los hechos. |
| **Información ausente** | Dato que no aparece y que no debe completarse con otra fuente. |
| **Prueba adversarial** | Detecta premisas falsas, invenciones y pérdida de grounding. |
| **Revisión humana** | Valida fuentes, inferencias y conclusiones antes de usarlas. |

Los Cuadernos de Copilot aceleran el inventario, la extracción y el contraste de documentos. Pero un análisis fiable tiene que mostrar de dónde sale cada dato, qué se puede comprobar y qué sigue sin saberse. Las decisiones siguen siendo de las personas.

## Recursos adicionales

- [01_Contrato_ALC-2026-014.docx](recursos/files/01_Contrato_ALC-2026-014.docx)
- [02_Acta_Comite_Seguimiento.docx](recursos/files/02_Acta_Comite_Seguimiento.docx)
- [03_Informe_Imputacion_Horas.docx](recursos/files/03_Informe_Imputacion_Horas.docx)
- [Opciones de licencia de Cuadernos de Copilot (Microsoft Support)](https://support.microsoft.com/es-es/microsoft-365-copilot/copilot-notebooks-licensing)
- [Requisitos para Páginas de Copilot y Cuadernos de Copilot (Microsoft Learn)](https://learn.microsoft.com/es-es/microsoft-365/loop/cpcn-requirements)
- [Los Cuadernos de Copilot ahora son Áreas de trabajo de Copilot (Microsoft Support, en inglés)](https://support.microsoft.com/en-us/microsoft-365-copilot/copilot-notebooks-to-copilot-workspaces)
