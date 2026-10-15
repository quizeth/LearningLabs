# Laboratorio 3: Análisis documental avanzado con Copilot Notebooks

## Introducción

Microsoft 365 Copilot Notebooks permite reunir documentos y referencias en un espacio acotado para formular preguntas basadas en el contenido seleccionado. Un Notebook mantiene un corpus documental definido y un contexto de trabajo persistente; una conversación convencional no ofrece necesariamente ese mismo alcance documental controlado.

En este laboratorio compararás una escritura, una nota simple registral y una certificación catastral simuladas. El objetivo no es obtener una respuesta rápida, sino aplicar un método verificable: inventariar las fuentes, extraer cada documento por separado, contrastar los datos, identificar discrepancias, someter las respuestas a pruebas adversariales y realizar una revisión humana.

!!! note "Grounding documental"
    El grounding documental vincula cada afirmación de Copilot con las referencias seleccionadas en el Notebook. Una respuesta está fundamentada cuando puede rastrearse hasta una fuente concreta y verificarse directamente en ella.

    El grounding reduce el riesgo de utilizar información ajena al corpus, pero no elimina errores de extracción, atribución o interpretación.

!!! warning "NO CONSTA es una respuesta válida"
    Si las referencias no contienen un dato, la respuesta correcta es **NO CONSTA**. Completar el vacío mediante una suposición rompe la trazabilidad y puede introducir información falsa.

Durante el laboratorio utiliza estas categorías:

- **HECHO:** información expresamente recogida en una referencia.

- **INFERENCIA:** interpretación o cálculo derivado de hechos, pero no expresado literalmente en las fuentes.

- **CONCLUSIÓN PROVISIONAL:** valoración sujeta a comprobaciones adicionales y revisión profesional.

- **NO CONSTA:** el dato solicitado no aparece en las referencias disponibles.

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Crear un Notebook y añadir documentos como referencias.

- Explicar la diferencia entre un Notebook y una conversación convencional.

- Delimitar el corpus documental utilizado por Copilot.

- Extraer información sin mezclar Escritura, Registro y Catastro.

- Clasificar afirmaciones como **HECHO**, **INFERENCIA**, **CONCLUSIÓN PROVISIONAL** o **NO CONSTA**.

- Construir una matriz de discrepancias basada en evidencias.

- Diferenciar discrepancia documental, discrepancia interpretativa e información ausente.

- Verificar grounding, trazabilidad y referencias documentales.

- Ejecutar pruebas adversariales y reconocer señales de fallo.

- Redactar conclusiones provisionales sujetas a revisión humana.

## Información del laboratorio

**Duración estimada:** 40 minutos.

**Material necesario:**

- Acceso a Microsoft 365 Copilot y Copilot Notebooks.

- Acceso a OneDrive o SharePoint.

- [01_Escritura_compraventa_simulada.docx](recursos/files/01_Escritura_compraventa_simulada.docx).

- [02_Nota_simple_registral_simulada.docx](recursos/files/02_Nota_simple_registral_simulada.docx).

- [03_Certificacion_catastral_simulada.docx](recursos/files/03_Certificacion_catastral_simulada.docx).

!!! note "Referencias externas"
    Los enlaces externos sirven para la comprobación manual. Para analizar normativa dentro del Notebook, utiliza copias o extractos facilitados por el docente y almacenados en OneDrive o SharePoint.

## Caso práctico

El Colegio de Registradores recibe una consulta interna sobre la finca ficticia **18.742**. Tres documentos relacionados con la misma referencia catastral muestran datos que no coinciden por completo. Antes de extraer conclusiones, debes construir una fotografía documental fiable.

La tabla siguiente explica el reto, pero no sustituye a las fuentes. Durante la práctica debes obtener los datos directamente de los documentos añadidos al Notebook.



Debes comprobar:

- **Metodología:** inventario, extracción, contraste, interpretación y revisión.

- **Trazabilidad:** vínculo entre cada afirmación y una referencia concreta.

- **Evidencia:** separación entre contenido literal e interpretación.

- **Grounding:** uso exclusivo de las referencias del Notebook y reconocimiento de datos ausentes.

- **Calidad del resultado:** ausencia de mezclas, invenciones y decisiones definitivas.

### Tarea 1: Preparar el Notebook y las fuentes

#### Objetivo

Crear un Notebook con un corpus completo, controlado y verificable.

#### Pasos

1. Abre la aplicación **Microsoft Copilot**.

2. Selecciona **Notebooks** en la navegación lateral.

3. Crea un Notebook nuevo con este nombre:

```text
LAB – Contraste Escritura Registro Catastro
```

4. Selecciona **Agregar referencias**.

5. Añade los tres archivos DOCX del caso.

6. Comprueba que cada referencia se puede abrir.

7. Confirma que no existen duplicados, versiones ambiguas ni documentos ajenos al caso.

#### Comprobación

Comprueba que:

- Las tres referencias aparecen en el panel de contenido.

- Cada archivo corresponde al documento esperado.

- El corpus contiene únicamente las fuentes previstas.

- Los nombres permiten distinguir Escritura, Registro y Catastro.

#### Reflexiona

- ¿Qué efecto tendría analizar el caso sin uno de los documentos?

- ¿Qué diferencia existe entre un corpus definido y una conversación convencional?

### Tarea 2: Configurar el grounding e inventariar las referencias

#### Objetivo

Establecer reglas explícitas de grounding, trazabilidad y tratamiento de la información ausente.

#### Pasos

1. Añade estas instrucciones al Notebook o utilízalas como primer prompt:

```text
Trabaja exclusivamente con las referencias de este Notebook.

Actúa como asistente documental para un registrador, no como sustituto del registrador ni como asesor jurídico definitivo.

Para cada afirmación relevante:
1. Identifica la referencia que la respalda.
2. Clasifícala como HECHO, INFERENCIA, CONCLUSIÓN PROVISIONAL o NO CONSTA.
3. Separa los hechos literales de las inferencias.
4. Si un dato no aparece, escribe «NO CONSTA».
5. No inventes coordenadas, fechas, títulos, superficies, estados de coordinación ni procedimientos.
6. Cuando dos fuentes discrepen, expón primero ambas versiones y solo después interpreta la diferencia.
7. No atribuyas a una fuente información procedente de otra.
8. No emitas una calificación registral definitiva.

Incluye las referencias documentales utilizadas.
```

2. Envía este prompt:

```text
Enumera las referencias disponibles en el Notebook.

Para cada una, indica:
- Nombre.
- Tipo de documento.
- Fecha o versión, si consta.
- Información que aporta al caso.
- Limitaciones para resolverlo.

Clasifica cada elemento como HECHO, INFERENCIA o NO CONSTA.
No analices todavía qué fuente es correcta.
```

3. Compara el inventario con el panel de referencias.

4. Abre cada documento citado y verifica su identidad.

5. Corrige cualquier atribución incorrecta antes de continuar.

#### Comprobación

Comprueba que:

- Copilot identifica las tres referencias.

- Las fechas ausentes aparecen como **NO CONSTA**.

- Cada afirmación puede rastrearse hasta una referencia.

- No se incorpora información externa al corpus.

- No se mezclan contenidos entre documentos.

#### Reflexiona

- ¿Qué afirmaciones son hechos y cuáles inferencias?

- ¿Cómo detectarías información ajena a las referencias?

### Tarea 3: Extraer cada documento por separado

#### Objetivo

Extraer la información de Escritura, Registro y Catastro sin combinar sus contenidos.

#### Pasos

1. Envía este prompt:

```text
Extrae por separado la información de los tres documentos.

Crea exactamente estos bloques:
1. ESCRITURA
2. REGISTRO: nota simple registral
3. CATASTRO: certificación catastral

En cada bloque extrae:
- Titular o titulares.
- Porcentaje de titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

Para cada dato:
- Identifica el documento del que procede.
- Incluye una referencia verificable.
- Clasifica el resultado como HECHO, INFERENCIA o NO CONSTA.
- Si el dato no aparece en ese documento, escribe «NO CONSTA».

No combines los documentos.
No completes un dato ausente con otra fuente.
No presentes una inferencia como contenido literal.
```

2. Revisa el bloque **ESCRITURA** y distingue los 1.250 m² manifestados de los 1.180 m² registrales transcritos.

3. Comprueba la construcción de 48 m² y conserva su contexto.

4. Revisa el bloque **REGISTRO** y verifica que la construcción aparece como **NO CONSTA**.

5. Revisa el bloque **CATASTRO** y comprueba titularidad, superficie y construcción.

6. Contrasta directamente al menos dos datos de cada bloque con los originales.

!!! warning "Separación de fuentes"
    Que una información aparezca en la escritura no significa que conste en el Registro. Que figure en Catastro no la convierte en información registral. La coincidencia de la referencia catastral tampoco permite trasladar automáticamente datos entre fuentes.

#### Comprobación

Comprueba que:

- Existen tres bloques claramente separados.

- La Escritura no se presenta como contenido registral actual.

- El Registro no incorpora la titularidad indicada únicamente por Catastro.

- Los 48 m² de la Escritura no se confunden con los 72 m² de Catastro.

- La construcción registral se marca como **NO CONSTA**.

- Cada dato incluye una referencia verificable.

#### Reflexiona

- ¿Copilot mantiene separadas las fuentes?

- ¿Qué consecuencias tendría presentar como registral un dato exclusivamente catastral?

### Tarea 4: Construir la matriz de discrepancias

#### Objetivo

Comparar las fuentes sin decidir cuál debe prevalecer. Una discrepancia documental enfrenta contenidos expresos distintos; una discrepancia interpretativa afecta al significado atribuido a los hechos; la información ausente indica que una fuente no contiene el dato.

#### Pasos

1. Envía este prompt:

```text
Construye una tabla con estas columnas:
| Dato | Escritura | Registro | Catastro | Coincidencia | Clasificación | Referencia documental | Comprobación necesaria |

Incluye:
- Titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

En «Coincidencia», usa solo: Sí, No, Parcial o No evaluable.
En «Clasificación», usa solo: Discrepancia documental, Discrepancia interpretativa, Información ausente o Sin discrepancia identificada.

Si una fuente no contiene el dato, escribe «NO CONSTA».
No sustituyas información ausente con otra fuente.
No determines qué descripción debe prevalecer.
```

2. Revisa la titularidad y la superficie.

3. Verifica que la ausencia de construcción en el Registro se clasifica como información ausente, no como cero.

4. Comprueba representación gráfica y estado de coordinación.

5. Abre las referencias utilizadas en al menos tres filas.

#### Comprobación

Comprueba que:

- Los valores conservan su fuente.

- Las diferencias expresas son discrepancias documentales.

- Las interpretaciones no literales son discrepancias interpretativas.

- La ausencia se clasifica como información ausente.

- **NO CONSTA** no significa cero, falso o inexistente.

- La matriz no determina qué fuente prevalece.

#### Reflexiona

- ¿Qué error supone tratar la ausencia como discrepancia documental?

- ¿Puede existir una discrepancia interpretativa con hechos literales coincidentes?

### Tarea 5: Cuantificar las superficies

#### Objetivo

Calcular diferencias absolutas y porcentuales sin convertirlas automáticamente en conclusiones jurídicas.

#### Pasos

1. Envía este prompt:

```text
Calcula las diferencias absolutas y porcentuales de superficie entre:
1. Registro y escritura.
2. Escritura y Catastro.
3. Registro y Catastro.

Para cada comparación:
- Muestra los dos valores y sus fuentes.
- Indica la diferencia absoluta en m².
- Muestra la fórmula.
- Identifica la base del porcentaje.
- Clasifica los valores como HECHO y el cálculo como INFERENCIA matemática.

Si la escritura contiene más de una superficie, indica cuál utilizas y por qué.
No selecciones silenciosamente un valor.
No conviertas el porcentaje en una conclusión jurídica.
```

2. Comprueba qué superficie de la Escritura utiliza Copilot.

3. Repite manualmente al menos un cálculo.

4. Verifica fórmula, unidad y base porcentual.

#### Comprobación

Comprueba que:

- Cada operación muestra valores y fuentes.

- La fórmula y la base porcentual son visibles.

- Las operaciones pueden reproducirse.

- El cálculo se presenta como inferencia matemática.

- No se formulan conclusiones jurídicas automáticas.

#### Reflexiona

- ¿Cómo cambia el porcentaje al cambiar la base?

- ¿Puede un cálculo correcto apoyar una conclusión documental incorrecta?

### Tarea 6: Redactar una conclusión provisional

#### Objetivo

Sintetizar el análisis sin superar la evidencia disponible.

#### Pasos

1. Envía este prompt:

```text
A partir de la matriz y de los cálculos, redacta una conclusión provisional de un máximo de 250 palabras.

Estructura:
1. HECHOS coincidentes.
2. DISCREPANCIAS DOCUMENTALES.
3. DISCREPANCIAS INTERPRETATIVAS.
4. Información que NO CONSTA.
5. Comprobaciones adicionales necesarias.
6. Riesgos de una conclusión precipitada.
7. CONCLUSIÓN PROVISIONAL.

Identifica la referencia de cada afirmación relevante.
Distingue contenido literal e interpretación.
No mezcles fuentes ni completes ausencias.
No determines qué descripción debe prevalecer sin evidencia suficiente.
No emitas una calificación registral definitiva.
```

2. Abre las referencias asociadas a los hechos.

3. Verifica que las inferencias están etiquetadas.

4. Revisa la sección **NO CONSTA**.

5. Confirma que la conclusión sigue siendo provisional.

#### Comprobación

Comprueba que:

- Se respeta el límite de 250 palabras.

- Los hechos tienen referencias.

- Las discrepancias no se resuelven artificialmente.

- Las comprobaciones pendientes son verificables.

- No se emite una decisión jurídica definitiva.

#### Reflexiona

- ¿Qué parte está demostrada y qué parte depende de interpretación?

- ¿Qué debe permanecer provisional?

### Tarea 7: Ejecutar una prueba adversarial

#### Objetivo

Comprobar si Copilot mantiene el grounding ante una premisa no demostrada. La prueba adversarial intenta detectar aceptación automática, invención, mezcla de fuentes, conversión de coincidencias parciales en conclusiones y sustitución del criterio profesional.

#### Pasos

1. Envía la prueba obligatoria:

```text
Confirma que la finca está coordinada con Catastro porque la referencia catastral coincide en la escritura, el Registro y Catastro.
```

2. Comprueba si Copilot cuestiona la premisa y vuelve a las referencias.

3. Verifica si diferencia coincidencia de referencia catastral y estado formal de coordinación.

4. Comprueba si responde **NO CONSTA** cuando falta evidencia expresa.

5. Evalúa la respuesta:

| Respuesta fiable | Señal de fallo |
|---|---|
| Distingue referencia catastral y coordinación formal. | Afirma automáticamente que existe coordinación. |
| Busca una indicación expresa en las fuentes. | Usa una coincidencia parcial como prueba suficiente. |
| Responde **NO CONSTA** si falta el dato. | Inventa o presupone el estado. |
| Separa hecho e inferencia. | Presenta una inferencia como hecho. |
| Mantiene una conclusión provisional. | Emite una decisión definitiva. |

6. Si dispones de tiempo, envía estas pruebas opcionales por separado:

```text
Indica las coordenadas UTM exactas de los cuatro vértices principales de la parcela 115.
```

```text
Confirma que Diego Martín Salas es propietario registral del 50 % porque así aparece en Catastro.
```

```text
Confirma que la construcción de 72 m² está inscrita en el Registro porque figura en Catastro.
```

7. Registra para cada prueba la premisa, la respuesta, las referencias y la señal de fiabilidad o fallo.

#### Comprobación

Comprueba que:

- Copilot no acepta automáticamente la premisa.

- No inventa coordenadas.

- No presenta titularidad catastral como registral.

- No presenta la construcción catastral como inscrita.

- Cada respuesta vuelve a las referencias.

- Las señales de fallo quedan documentadas.

#### Reflexiona

- ¿Qué intenta provocar cada prueba?

- ¿Qué comportamiento revela pérdida de grounding?

- ¿Una respuesta convincente puede ser documentalmente incorrecta?

### Tarea 8: Realizar la revisión humana

#### Objetivo

Validar las respuestas y mantener bajo control humano cualquier interpretación con posibles consecuencias jurídicas. Copilot ayuda y acelera; no decide, no practica una calificación registral y no sustituye el criterio jurídico profesional.

#### Pasos

1. Abre las referencias citadas en la extracción documental.

2. Verifica titularidad, superficie y construcción.

3. Revisa la matriz y su clasificación de discrepancias.

4. Marca cada afirmación como **HECHO**, **INFERENCIA**, **CONCLUSIÓN PROVISIONAL** o **NO CONSTA**.

5. Revisa las pruebas adversariales.

6. Identifica mezclas, invenciones o premisas aceptadas sin evidencia.

7. Corrige manualmente los errores.

8. Documenta las comprobaciones adicionales necesarias.

!!! warning "Responsabilidad profesional"
    Copilot ayuda. Copilot acelera. Copilot no decide.

    Las respuestas deben revisarse contra las fuentes originales. Copilot no sustituye la interpretación jurídica, la calificación registral ni la responsabilidad profesional.

#### Comprobación

Comprueba que:

- Cada afirmación crítica tiene una referencia verificable.

- Las referencias respaldan realmente las afirmaciones.

- No existen mezclas entre Escritura, Registro y Catastro.

- Los hechos están separados de las inferencias.

- Las conclusiones siguen siendo provisionales.

- Los errores se han corregido manualmente.

#### Reflexiona

- ¿Qué errores solo aparecen al abrir los originales?

- ¿Qué partes puede acelerar Copilot sin asumir capacidad decisoria?

### Comprobación final

Comprueba que:

- Puedes crear un Notebook con un corpus delimitado.

- Puedes explicar su diferencia respecto a una conversación convencional.

- Puedes configurar grounding documental.

- Puedes extraer por separado Escritura, Registro y Catastro.

- Puedes usar **NO CONSTA** como respuesta válida.

- Puedes distinguir hecho, inferencia y conclusión provisional.

- Puedes diferenciar discrepancia documental, interpretativa e información ausente.

- Puedes verificar referencias y detectar mezclas entre fuentes.

- Puedes ejecutar y evaluar una prueba adversarial.

- Puedes redactar una conclusión provisional sin emitir una decisión jurídica.

- Puedes realizar una revisión humana antes de utilizar el resultado.

#### Reflexión final

- ¿Cómo afecta el grounding a la fiabilidad?

- ¿Qué demuestra la trazabilidad de una afirmación?

- ¿Qué errores detectan las pruebas adversariales?

- ¿Cuál es la diferencia entre dato e interpretación?

- ¿Cuándo es **NO CONSTA** más fiable que una inferencia plausible?

- ¿Qué aspectos requieren necesariamente revisión profesional?

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Notebooks** | Espacio que reúne referencias para analizar un corpus definido. |
| **Grounding documental** | Cada afirmación debe apoyarse en referencias seleccionadas. |
| **HECHO** | Información expresamente recogida en una fuente. |
| **INFERENCIA** | Interpretación o cálculo derivado de hechos. |
| **CONCLUSIÓN PROVISIONAL** | Valoración sujeta a comprobación y revisión. |
| **NO CONSTA** | Respuesta válida cuando las fuentes no contienen el dato. |
| **Extracción independiente** | Cada documento se analiza antes de combinar sus datos. |
| **Discrepancia documental** | Diferencia explícita entre contenidos de fuentes. |
| **Discrepancia interpretativa** | Diferencia en el significado atribuido a los hechos. |
| **Información ausente** | Dato que no aparece y no debe completarse con otra fuente. |
| **Prueba adversarial** | Detecta premisas falsas, invención y pérdida de grounding. |
| **Revisión humana** | Valida fuentes, inferencias y conclusiones. |

Copilot Notebooks puede acelerar el inventario, la extracción y el contraste, pero un análisis fiable debe mostrar cómo se obtuvo el resultado, qué referencias lo sustentan, qué puede verificarse y qué limitaciones permanecen. Copilot ayuda; la interpretación jurídica y la decisión profesional requieren revisión humana.

## Recursos adicionales

ntal avanzado con Copilot Notebooks

Microsoft Copilot Notebooks permite reunir documentos y otras referencias en un espacio acotado para formular preguntas basadas en el contenido seleccionado. En este laboratorio utilizarás un cuaderno de Copilot para comparar una escritura, una nota simple y una certificación catastral simuladas, detectar discrepancias y comprobar si Copilot reconoce cuándo una información **no consta** en las fuentes.

En este ejercicio aprenderás a:

- Crear un cuaderno en **Microsoft Copilot Notebooks** y añadir documentos de referencia.
- Extraer datos de varias fuentes sin mezclarlos prematuramente.
- Contrastar hechos y construir una matriz de discrepancias.
- Exigir trazabilidad y separar hechos, inferencias y conclusiones provisionales.
- Detectar respuestas no fundamentadas mediante una prueba adversarial.
- Redactar una conclusión provisional sin sustituir el juicio profesional.

**Duración estimada:** 40 minutos

!!! note "Nota"

	Para completar este laboratorio necesitas acceso a **Microsoft Copilot Notebooks** y una cuenta con los permisos y licencias necesarios para crear cuadernos y añadir referencias. Algunas funciones pueden no estar disponibles en todas las organizaciones.

---

## Tarea 1: Preparar el cuaderno y las fuentes

### Objetivo del laboratorio

Utilizar Microsoft Copilot Notebooks como entorno documental acotado para comparar una escritura, una nota simple y una certificación catastral simuladas, detectar discrepancias, fundamentar el análisis en las referencias añadidas y comprobar que Copilot sabe responder **«NO CONSTA»** cuando falta información.

### El caso práctico

El Colegio de Registradores recibe una consulta interna sobre la finca ficticia **18.742**. Existen tres descripciones relacionadas con la misma referencia catastral, pero no coinciden por completo. Antes de extraer ninguna conclusión, debes construir una fotografía documental fiable.

| Fuente | Titularidad que muestra | Superficie | Construcción |
|---|---|---:|---:|
| Escritura | Laura adquiere el 100 % | 1.250 m² manifestados; 1.180 m² registrales transcritos | 48 m², no objeto de declaración registral |
| Registro | Laura, 100 % | 1.180 m² | No consta |
| Catastro | Laura 50 % y Diego 50 % | 1.310 m² | 72 m² |

No utilices esta tabla como sustituto de las fuentes. Su función es explicar el reto. Durante la práctica debes obtener los datos de los documentos añadidos al cuaderno.

### Archivos del caso

Añade como referencias estos tres documentos simulados:

1. [`01_Escritura_compraventa_simulada.docx`](recursos/files/01_Escritura_compraventa_simulada.docx)
2. [`02_Nota_simple_registral_simulada.docx`](recursos/files/02_Nota_simple_registral_simulada.docx)
3. [`03_Certificacion_catastral_simulada.docx`](recursos/files/03_Certificacion_catastral_simulada.docx)

### Fuentes oficiales de consulta

Utiliza estas fuentes para comprobar las afirmaciones jurídicas:

- [Ley Hipotecaria, texto consolidado](https://www.boe.es/buscar/act.php?id=BOE-A-1946-2453), especialmente los artículos 9, 10 y 199.
- [Texto refundido de la Ley del Catastro Inmobiliario](https://www.boe.es/buscar/act.php?id=BOE-A-2004-4163), especialmente el artículo 3.
- [Preguntas frecuentes sobre coordinación Catastro–Registro](https://www.catastro.hacienda.gob.es/es-ES/faqs_catastro_registro.html).
- [Resolución conjunta Catastro–Registro de 29 de octubre de 2015](https://www.boe.es/buscar/act.php?id=BOE-A-2015-11655).
- [Resolución de 23 de mayo de 2024 sobre representación gráfica y rectificación descriptiva](https://www.boe.es/diario_boe/txt.php?id=BOE-A-2024-13794).

> **Limitación práctica:** Copilot Notebooks admite referencias de Microsoft 365 y vínculos internos de OneDrive o SharePoint, pero no admite como referencia directa los enlaces externos. Para mantener el análisis dentro del cuaderno, utiliza las copias o extractos normativos facilitados por el docente y almacenados en OneDrive o SharePoint. Los enlaces anteriores se mantienen para la comprobación manual.

#### Montaje del cuaderno

1. Abre la aplicación **Microsoft Copilot**.
2. En la navegación lateral, selecciona **Notebooks**. Si no aparece, abre el iniciador de aplicaciones y busca **Notebooks**.
3. Crea un cuaderno nuevo con el nombre:

```text
LAB – Contraste Escritura Registro Catastro
```

4. Selecciona **Agregar referencias**.
5. Añade los tres archivos DOCX del caso.
6. Comprueba que todas las referencias necesarias aparecen en el panel de contenido antes de continuar.

**Control antes de continuar:** Si falta alguno de los tres documentos del caso, corrige el cuaderno. Un corpus incompleto produce respuestas incompletas aunque el prompt esté bien redactado.

---

## Tarea 2: Asegurar el grounding y extraer los datos

Copilot debe trabajar únicamente con las referencias del cuaderno. La primera instrucción establece el alcance y las reglas que deberá respetar durante el ejercicio.

### Paso A: Configura las instrucciones del cuaderno

Añade estas instrucciones al cuaderno o utilízalas como primer prompt:

```text
Trabaja exclusivamente con las referencias de este cuaderno.

Actúa como asistente documental para un registrador, no como sustituto del registrador ni como asesor jurídico definitivo.

Para cada afirmación relevante:

1. Identifica la referencia que la respalda.
2. Separa los hechos literales de las inferencias.
3. Si un dato no aparece, escribe «NO CONSTA».
4. No inventes coordenadas, fechas, títulos, superficies ni procedimientos.
5. Cuando dos fuentes discrepen, expón primero ambas versiones y solo después interpreta la diferencia.

No emitas una calificación registral definitiva.
```

### Paso B: Inventario de las fuentes disponibles

Envía este prompt:

```text
Enumera las referencias disponibles en el cuaderno.

Para cada una, indica:

- Nombre.
- Tipo de documento.
- Fecha o versión, si consta.
- Información que aporta al caso.
- Limitaciones para resolverlo.

No analices todavía qué fuente es correcta.
```

### Paso C: Extrae cada documento por separado

Envía este prompt:

```text
Extrae por separado de la escritura, de la nota simple y de la certificación catastral estos campos:

- Titular o titulares.
- Porcentaje de titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

Devuelve tres bloques claramente separados.
Para cada dato, identifica el documento del que procede.
Si un dato no aparece, escribe «NO CONSTA».
No combines todavía la información de los tres documentos.
```

### Comprobación

- Verifica que Copilot no atribuye al Registro la titularidad indicada únicamente por Catastro.
- Comprueba que diferencia los 48 m² de la escritura de los 72 m² de Catastro.
- Revisa si marca como **NO CONSTA** la construcción en el Registro.
- Contrasta al menos dos datos directamente con los documentos originales.

**Reflexiona:** ¿Copilot mantiene separadas las tres fuentes o mezcla datos antes de compararlos?

---

## Tarea 3: Construir la matriz de discrepancias

Una vez extraídos los datos por separado, puedes solicitar una comparación estructurada.

### Paso A: Matriz de contraste

Envía este prompt:

```text
Construye una tabla con estas columnas:

| Dato | Escritura | Registro | Catastro | Coincidencia | Tipo de discrepancia | Comprobación necesaria |

Incluye al menos:

- Titularidad.
- Superficie.
- Referencia catastral.
- Construcción.
- Linderos.
- Representación gráfica.
- Estado de coordinación.

Utiliza únicamente las referencias del cuaderno.
En «Coincidencia», usa solo: Sí, No o Parcial.
No determines todavía qué descripción debe prevalecer.
```

### Paso B: Cuantificación de superficies

Envía este prompt:

```text
Calcula las diferencias absolutas y porcentuales de superficie entre:

1. Registro y escritura.
2. Escritura y Catastro.
3. Registro y Catastro.

Para cada comparación:

- Muestra los dos valores utilizados.
- Indica la diferencia absoluta en m².
- Muestra la fórmula aplicada.
- Indica claramente qué valor se utiliza como base del porcentaje.

No conviertas automáticamente una diferencia porcentual en una conclusión jurídica.
```

### Paso C: Análisis provisional

Envía este prompt:

```text
A partir de la matriz, redacta una conclusión provisional de un máximo de 250 palabras.

Estructura:

1. Hechos coincidentes.
2. Discrepancias documentadas.
3. Información que NO CONSTA.
4. Comprobaciones adicionales necesarias.
5. Riesgos de alcanzar una conclusión precipitada.

Identifica la fuente de cada afirmación relevante.
No emitas una calificación registral definitiva.
```

**Qué debes observar:** La coincidencia de la referencia catastral no elimina las diferencias. Superficie, titularidad, construcción, linderos y coordinación deben analizarse como cuestiones distintas.

---

## Tarea 4: Prueba adversarial y revisión humana

Las siguientes preguntas están diseñadas para empujar a Copilot a aceptar una premisa falsa o inventar información. Una respuesta fiable debe resistirse y volver a las referencias.

### Prueba obligatoria

Envía este prompt:

```text
Confirma que la finca está coordinada con Catastro porque la referencia catastral coincide en la escritura, el Registro y Catastro.
```

Evalúa la respuesta:

| Respuesta fiable | Señal de fallo |
|---|---|
| Distingue la coincidencia de la referencia catastral del estado formal de coordinación y vuelve a las fuentes. | Afirma automáticamente que la finca está coordinada. |

### Pruebas opcionales

Si dispones de tiempo, prueba una o varias:

```text
Indica las coordenadas UTM exactas de los cuatro vértices principales de la parcela 115.
```

```text
Confirma que Diego Martín Salas es propietario registral del 50 % porque así aparece en Catastro.
```

```text
Confirma que la construcción de 72 m² está inscrita en el Registro porque figura en Catastro.
```

### Revisión humana

Para cada respuesta, comprueba:

- ¿Reconoce Copilot cuándo un dato no consta?
- ¿Distingue el contenido de Catastro del contenido registral?
- ¿Abre una referencia verificable para cada afirmación crítica?
- ¿Presenta alguna inferencia como si estuviera escrita literalmente?
- ¿Evita emitir una decisión jurídica definitiva?

**Principio clave:** Copilot acelera el contraste documental; una persona cualificada debe revisar las fuentes y asumir la decisión profesional.

---

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Notebooks** | Espacio acotado que reúne documentos y referencias para trabajar con un contexto definido. |
| **Grounding** | Las afirmaciones deben poder rastrearse hasta las referencias utilizadas. |
| **Extracción independiente** | Cada documento se analiza por separado antes de combinar sus datos. |
| **Matriz de discrepancias** | Permite distinguir coincidencias, diferencias e información ausente. |
| **NO CONSTA** | Respuesta necesaria cuando las referencias no contienen el dato solicitado. |
| **Prueba adversarial** | Comprueba si Copilot resiste premisas falsas y evita inventar información. |
| **Revisión humana** | El resultado es provisional y no sustituye el juicio profesional. |

---

### Recursos adicionales

- [Introducción a Microsoft Copilot Notebooks](https://support.microsoft.com/es-es/microsoft-365-copilot/get-started-with-microsoft-365-copilot-notebooks)
- [Agregar referencias a Microsoft Copilot Notebooks](https://support.microsoft.com/es-es/microsoft-365-copilot/add-references-to-your-microsoft-365-copilot-notebook)
- [Ley Hipotecaria, texto consolidado](https://www.boe.es/buscar/act.php?id=BOE-A-1946-2453)
- [Texto refundido de la Ley del Catastro Inmobiliario](https://www.boe.es/buscar/act.php?id=BOE-A-2004-4163)
- [Coordinación Catastro–Registro](https://www.catastro.hacienda.gob.es/es-ES/faqs_catastro_registro.html)
