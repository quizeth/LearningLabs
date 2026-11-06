# Laboratorio 2: Técnicas de prompting aplicadas a datos de proyecto

## Introducción

Una misma tarea puede dar resultados muy distintos según el contexto, los ejemplos, las restricciones y la estructura que incluya el prompt. En este laboratorio aprenderás a controlar el comportamiento de Microsoft 365 Copilot Chat con cinco estrategias: **instrucciones personalizadas**, **zero-shot**, **few-shot**, **razonamiento verificable** y **superprompt**.

Trabajarás con el dossier de un proyecto ficticio de **Corvalia Consultoría Tecnológica** para su cliente **Grupo Alcora**. No necesitas conocimientos financieros ni jurídicos: los datos son los habituales en el seguimiento de cualquier proyecto.

!!! note "Criterio de evaluación"
    No siempre hay una única respuesta válida. Una respuesta es correcta si está justificada por los datos del dossier, respeta las restricciones del prompt y aplica un método coherente.

Durante todo el curso usamos estas categorías para clasificar la información:

| Etiqueta | Significado |
|---|---|
| **HECHO** | Dato que aparece expresamente en la fuente. |
| **CÁLCULO** | Resultado de aplicar una operación explícita a hechos. |
| **INFERENCIA** | Interpretación que no aparece literalmente en la fuente. |
| **CONCLUSIÓN PROVISIONAL** | Valoración sujeta a revisión humana. |
| **NO CONSTA** | El dato no aparece en la fuente. Es una respuesta válida. |

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Configurar instrucciones personalizadas y reconocer su efecto en segundo plano.
- Aplicar un prompt zero-shot e identificar los supuestos que añade Copilot.
- Usar ejemplos few-shot para controlar criterios y formato, y detectar el sesgo que introducen.
- Exigir fórmulas, sustituciones y resultados intermedios para verificar un cálculo.
- Construir un superprompt con rol, contexto, tarea, restricciones y formato.
- Distinguir HECHO, CÁLCULO, INFERENCIA y NO CONSTA en una respuesta.
- Elegir la técnica adecuada según el riesgo y el nivel de control necesario.

## Información del laboratorio

**Duración estimada:** 30 minutos.

| Tarea | Tiempo orientativo |
|---|---|
| 1. Instrucciones personalizadas | 3 min |
| 2. Zero-shot | 4 min |
| 3. Few-shot | 5 min |
| 4. Razonamiento verificable | 7 min |
| 5. Superprompt | 7 min |
| 6. Prompt propio *(opcional)* | 10 min |
| 7. Comparación final y cierre | 4 min |

**Material necesario:**

- Microsoft 365 Copilot Chat (licencia básica o premium).
- El archivo [Dossier_Proyecto_ALC-2026-014.docx](recursos/files/Dossier_Proyecto_ALC-2026-014.docx), que contiene:
    - Anexo 1: Presupuesto y coste real del proyecto.
    - Anexo 2: Registro de incidencias del servicio gestionado.
    - Anexo 3: Ofertas de subcontratación.

!!! note "Licencias"
    Todas las tareas usan funciones disponibles en Copilot Chat sin licencia premium: chat y carga de archivos. Si tienes licencia premium y Copilot incorpora datos de tu organización, tus respuestas pueden diferir de los resultados esperados. Trabaja siempre con el dossier adjunto.

!!! warning "Abre una conversación nueva cuando se indique"
    Los ejemplos y las instrucciones de una conversación condicionan las respuestas siguientes. Para comparar técnicas de forma justa, cada tarea empieza con un chat nuevo.

## Caso práctico

Corvalia Consultoría Tecnológica moderniza la plataforma de seguimiento de envíos de Grupo Alcora, una empresa de logística. El proyecto **ALC-2026-014** acaba de cerrar su primera fase y formas parte de la oficina de proyectos.

Antes del próximo comité, tienes que hacer tres cosas:

- Clasificar las incidencias abiertas del servicio.
- Explicar las desviaciones de coste.
- Comparar tres ofertas para reforzar el equipo.

Para hacerlo, usarás varias técnicas de prompting y compararás el control que te da cada una.

---

### Tarea 1: Instrucciones personalizadas

**Licencia:** no requiere licencia premium.

#### Objetivo

Configurar reglas que Copilot aplique en segundo plano a todas tus conversaciones y entender su alcance: funcionan como un prompt de sistema que tú defines.

#### Pasos

1. Abre Microsoft 365 Copilot Chat.
2. Ve a **Configuración** > **Personalización** > **Instrucciones personalizadas**.
3. Si ya tienes instrucciones, **cópialas en un documento aparte** para restaurarlas al terminar.
4. Sustitúyelas por este texto y guarda:

    ```text
    Responde siempre en español de España.
    Cuando trabajes con un documento adjunto, indica de qué anexo o sección procede cada dato.
    Si un dato no aparece en el documento, escribe NO CONSTA en lugar de suponerlo.
    ```

5. Abre una conversación nueva y escribe: `¿Qué reglas aplicas a mis respuestas?`
6. Comprueba si Copilot describe las reglas que acabas de configurar.

!!! note "Si no encuentras la opción"
    La ubicación del menú puede variar según la versión y la configuración de tu organización. Si no aparece, pega el mismo texto como **primer mensaje** de cada conversación nueva de este laboratorio. Conseguirás el mismo objetivo, aunque tendrás que repetirlo.

!!! warning "Por qué estas instrucciones no incluyen criterios de clasificación"
    Las instrucciones personalizadas afectan a **todas** las conversaciones. Si incluyeran categorías o formatos, la Tarea 2 dejaría de ser un zero-shot real.

#### Comprobación

- Las instrucciones están guardadas o tienes el texto preparado para pegarlo.
- Tienes una copia de tus instrucciones anteriores, si las había.
- Las instrucciones solo regulan el idioma, la trazabilidad y el uso de NO CONSTA.

#### Reflexiona

- ¿Qué diferencia hay entre una instrucción personalizada y una instrucción escrita en el prompt?
- ¿Qué riesgo tiene olvidar que hay instrucciones personalizadas activas?

---

### Tarea 2: Zero-shot prompting

**Licencia:** no requiere licencia premium.

#### Objetivo

Plantear una tarea sin ejemplos y detectar qué criterios, supuestos o formato elige Copilot por su cuenta.

#### Pasos

1. Abre una **conversación nueva**.
2. Selecciona **+** > **Cargar** > **Cargar imágenes y archivos** y adjunta el dossier.
3. Envía este prompt:

    ```text
    Utiliza el Anexo 2 del documento adjunto.
    Clasifica las incidencias INC-205, INC-206, INC-207 e INC-208 por categoría y severidad.
    ```

4. Anota:
    - Las categorías que usa Copilot.
    - Los niveles de severidad que asigna y cuántos niveles distintos emplea.
    - El formato que elige.
    - Cualquier criterio que Copilot supone y que no aparece en el prompt.

#### Comprobación

- Se clasifican las cuatro incidencias.
- Puedes relacionar cada clasificación con la descripción de la incidencia en el Anexo 2.
- Has identificado al menos un supuesto que Copilot añade por su cuenta, como la escala de severidad, los nombres de las categorías o el formato.
- Has valorado si el criterio es coherente entre las cuatro incidencias.

#### Reflexiona

- ¿Qué información faltaba en el prompt para controlar la clasificación?
- ¿Podrías reproducir el mismo criterio con otras incidencias usando solo este prompt?

---

### Tarea 3: Few-shot prompting

**Licencia:** no requiere licencia premium.

#### Objetivo

Usar ejemplos resueltos para fijar categorías, severidades y formato, y comprobar si los ejemplos también introducen sesgos.

#### Pasos

1. Abre una **conversación nueva** y adjunta de nuevo el dossier.
2. Envía este prompt:

    ```text
    Eres un clasificador de incidencias de un servicio gestionado.
    Clasifica cada incidencia con estos campos:
    - categoria
    - severidad: Crítica, Alta, Media o Baja
    - justificacion

    Sigue el patrón de estos ejemplos del Anexo 2:

    [EJEMPLO 1]
    Entrada: INC-201. El portal de seguimiento de envíos devuelve error 503 a todos los clientes desde hace 15 minutos.
    Salida: {"categoria":"Disponibilidad","severidad":"Crítica","justificacion":"Caída total del servicio en producción para todos los usuarios"}

    [EJEMPLO 2]
    Entrada: INC-202. Solicitud de acceso de consulta al panel de informes para una nueva analista, necesario antes del lunes.
    Salida: {"categoria":"Gestión de accesos","severidad":"Baja","justificacion":"Solicitud planificada sin interrupción del servicio"}

    [EJEMPLO 3]
    Entrada: INC-203. La API de consulta de estado de envíos responde en 4,5 segundos de media en horas punta (objetivo: 1 segundo). No se registran errores.
    Salida: {"categoria":"Rendimiento","severidad":"Media","justificacion":"Degradación parcial sin pérdida de servicio"}

    [EJEMPLO 4]
    Entrada: INC-204. 2.300 intentos fallidos de inicio de sesión contra el portal de clientes en una hora, desde un mismo rango de direcciones IP.
    Salida: {"categoria":"Seguridad","severidad":"Alta","justificacion":"Actividad hostil activa contra un servicio expuesto"}

    Ahora procesa individualmente INC-205, INC-206, INC-207 e INC-208.
    Devuelve únicamente un array JSON válido, sin introducción ni conclusión.
    ```

3. Compara el resultado con el de la Tarea 2.
4. Fíjate especialmente en **INC-207**: ¿encaja en alguna de las cuatro categorías de los ejemplos o Copilot la fuerza en una de ellas?

#### Comprobación

- Se procesan las cuatro incidencias y todas tienen los mismos campos.
- Solo aparecen las severidades permitidas.
- El resultado es un JSON válido, sin texto alrededor.
- Cada justificación se basa en el texto de su incidencia, no solo en el parecido con un ejemplo.
- Has registrado si alguna clasificación parece condicionada por los ejemplos.

#### Reflexiona

- ¿Qué ha mejorado respecto al zero-shot: el formato, el criterio o ambos?
- ¿Los ejemplos han limitado categorías que también serían válidas?
- ¿Qué pasaría si uno de los ejemplos estuviera mal clasificado?

---

### Tarea 4: Razonamiento verificable

**Licencia:** no requiere licencia premium.

#### Objetivo

Pedir datos de entrada, fórmula, operación y resultado para poder auditar un cálculo, y separar HECHO, CÁLCULO e INFERENCIA.

En tareas cuantitativas, una conclusión sin operaciones visibles no se puede comprobar. Esta técnica no pide a Copilot que «piense más», sino que **muestre resultados verificables**.

#### Pasos

1. Abre una **conversación nueva** y adjunta el dossier.
2. Envía este prompt:

    ```text
    Utiliza exclusivamente el Anexo 1 del documento adjunto.
    Identifica las partidas de coste cuya desviación porcentual, en valor absoluto, entre el Presupuesto y el Real sea estrictamente superior al 5 %.

    Para cada partida:
    1. Muestra el presupuesto y el importe real.
    2. Aplica esta fórmula:
       Desviación (%) = (Real − Presupuesto) / Presupuesto × 100
    3. Muestra la sustitución numérica y el resultado redondeado a dos decimales.
    4. Indica si supera o no el umbral del 5 % en valor absoluto.
    5. Incluye la causa solo si aparece expresamente en la Nota de la Oficina de Proyectos. Si no aparece, escribe "NO CONSTA".

    Presenta el resultado en una tabla con estas columnas:
    | Partida | Presupuesto | Real | Operación | Desviación (%) | ¿Supera el 5 %? | Causa documentada |

    No incluyas ingresos, margen ni totales. No añadas causas inferidas.
    ```

3. Verifica a mano este cálculo de referencia:

    ```text
    Infraestructura cloud: (78.400 − 64.000) / 64.000 × 100 = 22,50 %
    ```

4. Comprueba cómo trata Copilot la **Subcontratación**: su desviación es exactamente del −5,00 %.
5. Revisa la causa de **Desplazamientos y dietas**. ¿Copilot la deduce de algún punto de la nota?
6. Marca cada elemento de la tabla como **HECHO**, **CÁLCULO** o **INFERENCIA**.

#### Comprobación

- Los importes coinciden con el Anexo 1.
- Cada operación muestra la sustitución completa y el signo correcto.
- El umbral se aplica al valor absoluto y de forma estricta: el −5,00 % **no** se incluye.
- No aparecen ingresos, margen ni el total de costes.
- Las causas proceden de la nota. Si una causa solo se deduce, debe figurar **NO CONSTA**.
- Tu cálculo manual coincide con el de Copilot.

#### Reflexiona

- ¿Ver la sustitución numérica te ha ayudado a detectar errores?
- ¿Copilot ha distinguido entre una causa documentada y una causa plausible?
- ¿Podrías defender la tabla ante el comité sin fiarte solo de Copilot?

---

### Tarea 5: Superprompt

**Licencia:** no requiere licencia premium.

#### Objetivo

Construir y evaluar un prompt estructurado que controle el rol, el contexto, la tarea, las restricciones y el formato de una comparación de ofertas.

#### Pasos

1. Abre una **conversación nueva** y adjunta el dossier.
2. Envía este prompt:

    ```text
    [ROL]
    Actúa como responsable de compras de servicios profesionales de una consultora tecnológica.

    [CONTEXTO]
    El proyecto ALC-2026-014 necesita reforzar el equipo durante cuatro meses con dos desarrolladores y un perfil de QA. El Anexo 3 del documento adjunto recoge tres ofertas de subcontratación. La comparación es un borrador para el jefe de proyecto y no una decisión de contratación.

    [TAREA]
    Compara Datanexo Servicios, Ibercode Partners y Kestrel Tech Solutions según estos criterios:
    - Coste mensual del equipo, sin IVA.
    - Plazo de incorporación.
    - Plazo de sustitución de un perfil.
    - Penalización por incumplimiento.
    - Certificaciones declaradas.
    - Preaviso de rescisión.

    [RESTRICCIONES]
    1. Utiliza exclusivamente el Anexo 3.
    2. No uses adjetivos valorativos como "excelente", "sólido" o "mejor".
    3. Si un dato no aparece, escribe "NO CONSTA".
    4. Si obtienes un valor mediante una operación, márcalo como CÁLCULO e indica los supuestos que has usado.
    5. Separa los datos de la valoración final.

    [FORMATO]
    1. Tabla Markdown con una fila por proveedor y una columna por criterio.
    2. Después de la tabla:
       - Opción de menor coste mensual declarado.
       - Opción con menor plazo de incorporación.
       - Principal riesgo contractual de cada proveedor.
    3. Termina con una CONCLUSIÓN PROVISIONAL de coste-riesgo de un máximo de 80 palabras.
    ```

3. Contrasta cada celda de la tabla con el Anexo 3.
4. Revisa el coste de **Kestrel Tech Solutions**: su oferta indica una tarifa diaria, no un coste mensual. ¿Qué hace Copilot con ese dato?

#### Comprobación

- Están los tres proveedores y los seis criterios.
- Los datos ausentes aparecen como **NO CONSTA** y no se completan con conocimiento externo.
- Si Copilot calcula un coste mensual para Kestrel, lo marca como **CÁLCULO** y explica cuántos días ha supuesto.
- La opción de menor coste y la de menor plazo se justifican con datos de la tabla.
- La conclusión no supera las 80 palabras y no presenta una decisión definitiva.

#### Reflexiona

- ¿Qué componente ha tenido más impacto: el rol, el contexto, las restricciones o el formato?
- ¿Qué restricción ha reducido más el riesgo de que Copilot invente datos?
- ¿Qué componente quitarías primero para acortar el prompt sin perder control?

---

### Tarea 6 *(opcional)*: Crea y mejora tu propio prompt

**Licencia:** no requiere licencia premium.

#### Objetivo

Diseñar un prompt propio con la técnica adecuada, detectar un problema concreto en la respuesta y corregirlo.

#### Pasos

1. Elige una opción:
    - **Opción A (Anexo 1):** resumir la situación económica del proyecto para la dirección de Corvalia.
    - **Opción B (Anexo 2):** clasificar incidencias y proponer una acción para cada una.
    - **Opción C (Anexo 3):** comparar las ofertas con los criterios que tú consideres relevantes.
2. Elige la técnica: zero-shot, few-shot, razonamiento verificable o superprompt.
3. Redacta el prompt. Si lo necesitas, usa esta plantilla:

    ```text
    [ANEXO O FUENTE]
    [OBJETIVO Y DESTINATARIO]
    [TAREA]
    [RESTRICCIONES]
    [FORMATO DE SALIDA]
    [EJEMPLOS O FÓRMULAS, SI SON NECESARIOS]
    ```

4. Envíalo, localiza **un** problema concreto y decide si afecta al método, la trazabilidad, la justificación, el formato o las restricciones.
5. Modifica el prompt para corregir ese problema, envíalo de nuevo y compara las dos respuestas.

#### Comprobación

- El segundo prompt corrige el problema detectado.
- La mejora se puede observar y no consiste solo en que el prompt sea más largo.
- La respuesta sigue siendo fiel al anexo y marca NO CONSTA cuando corresponde.

#### Reflexiona

- ¿La segunda respuesta corrigió el problema o solo lo ocultó con otra presentación?
- ¿Podrías acortar el segundo prompt sin perder el control que has ganado?

---

### Tarea 7: Comparación final y cierre

**Licencia:** no requiere licencia premium.

#### Objetivo

Comparar las técnicas a partir de lo que has observado y restablecer tu configuración.

#### Pasos

1. Completa la tabla con observaciones de tus propias pruebas:

    | Técnica | Ventaja observada | Limitación observada | Mejor uso |
    |---|---|---|---|
    | Instrucciones personalizadas | | | |
    | Zero-shot | | | |
    | Few-shot | | | |
    | Razonamiento verificable | | | |
    | Superprompt | | | |

2. Vuelve a **Configuración** > **Personalización** > **Instrucciones personalizadas** y elimina las instrucciones de la Tarea 1 o restaura las que tenías.

!!! warning "No olvides el último paso"
    Si dejas activas las instrucciones del laboratorio, seguirán influyendo en tus conversaciones y en los laboratorios siguientes.

#### Comprobación

- Las cinco técnicas aparecen en la tabla.
- Cada ventaja y cada limitación se apoyan en algo que has observado en el laboratorio.
- Las instrucciones personalizadas están restablecidas.

#### Reflexiona

- ¿Hay una técnica universalmente mejor o depende del caso?
- ¿Qué técnica usarías para un cálculo que vaya a revisar el cliente?

---

## Comprobación final

Comprueba que:

- Has configurado, probado y restablecido las instrucciones personalizadas.
- Has completado los ejercicios de zero-shot, few-shot, razonamiento verificable y superprompt.
- Has abierto una conversación nueva en cada tarea.
- Has verificado a mano al menos un cálculo.
- Has distinguido HECHO, CÁLCULO, INFERENCIA y NO CONSTA.
- Has detectado al menos un supuesto o sesgo introducido por Copilot o por los ejemplos.

## Resumen

| Técnica | Punto clave |
|---|---|
| **Instrucciones personalizadas** | Reglas que se aplican a todas las conversaciones. Ahorran repetir contexto, pero actúan aunque no las veas. |
| **Zero-shot** | Rápido y sencillo, pero Copilot decide por su cuenta los criterios y el formato. |
| **Few-shot** | Los ejemplos fijan el patrón y mejoran la consistencia, pero también transmiten sesgos y límites. |
| **Razonamiento verificable** | Fórmulas y operaciones visibles: permite auditar el cálculo y separar el dato de la inferencia. |
| **Superprompt** | Rol, contexto, tarea, restricciones y formato en un solo mensaje. Da más control en tareas con muchos requisitos. |

La técnica adecuada depende del riesgo de la tarea. Cuanto mayor es la consecuencia de un error, más necesario es que la respuesta se pueda verificar.

## Recursos adicionales

- [Dossier_Proyecto_ALC-2026-014.docx](recursos/files/Dossier_Proyecto_ALC-2026-014.docx)
- [Información general de Microsoft Copilot Chat](https://learn.microsoft.com/es-es/copilot/overview)
- [Prompt Gallery de Microsoft](https://adoption.microsoft.com/copilot/prompt-gallery/)
