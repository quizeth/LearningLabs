# Laboratorio 1: Primeros pasos con Microsoft 365 Copilot Chat

## Introducción

Microsoft 365 Copilot Chat permite investigar, generar ideas, crear contenido visual, redactar documentos y analizar archivos desde una única conversación. En este laboratorio recorrerás ese flujo completo con un caso sencillo: la organización de un evento para un cliente.

Trabajarás en la oficina de proyectos de **Corvalia Consultoría Tecnológica**, una consultora ficticia que moderniza la plataforma de seguimiento de envíos de **Grupo Alcora**, una empresa de logística también ficticia. No necesitas conocimientos del sector: Copilot te ayudará a investigarlo.

!!! note "Revisa siempre lo que genera Copilot"
    Copilot acelera el trabajo, pero sus respuestas pueden contener errores o afirmaciones sin fuente. En cada tarea comprobarás al menos un aspecto del resultado antes de darlo por bueno. En los laboratorios siguientes aprenderás a hacerlo de forma sistemática, clasificando la información como HECHO, CÁLCULO, INFERENCIA, CONCLUSIÓN PROVISIONAL o NO CONSTA.

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Navegar por Microsoft 365 Copilot Chat y enviar prompts.
- Investigar un tema con búsqueda web y comprobar las fuentes que cita Copilot.
- Generar ideas y mejorarlas con prompts de seguimiento.
- Crear una tabla y una imagen a partir de una descripción.
- Convertir una respuesta de Copilot en un documento de Word.
- Analizar un archivo subido y generar una comunicación a partir de él.

## Información del laboratorio

**Duración estimada:** 20 minutos. La Tarea 6 es opcional.

**Material necesario:**

- Microsoft 365 Copilot Chat (licencia básica o premium), disponible en [m365.cloud.microsoft/chat](https://m365.cloud.microsoft/chat) o en la aplicación **Microsoft Copilot**.
- No necesitas archivos de apoyo: el documento de Word lo crearás durante el laboratorio.

!!! note "Licencias"
    La búsqueda web, la generación de imágenes y la carga de archivos están disponibles en Copilot Chat con y sin licencia premium. Si tienes licencia premium y Copilot incorpora datos de tu organización, algunas respuestas pueden ser distintas. Para la investigación de la Tarea 1, prioriza las fuentes web.

!!! warning "No envíes nada"
    En este laboratorio redactarás un correo, pero no lo enviarás. Todos los contenidos se refieren a empresas ficticias.

## Caso práctico

Corvalia quiere reforzar su relación con Grupo Alcora y te ha pedido que organices una **Jornada de Innovación en Logística**: un evento de un día en el que Corvalia presentará al cliente las tendencias tecnológicas de su sector.

Tienes que preparar una propuesta inicial: los temas, las sesiones, la agenda, un logotipo, un resumen de planificación y un correo para el equipo de la cuenta. Usarás Copilot Chat en todos los pasos. Las tareas 1 a 4 se hacen en la misma conversación; la Tarea 5, en una conversación nueva.

---

### Tarea 1: Investigar las tendencias del sector

#### Objetivo

Usar Copilot Chat con búsqueda web para identificar tendencias que sirvan de base a la agenda, y comprobar de dónde procede la información.

#### Pasos

1. Abre Microsoft 365 Copilot Chat.
2. Envía este prompt:

    ```text
    ¿Cuáles son las tres principales tendencias de innovación tecnológica en logística y cadena de suministro para los próximos dos años, y cómo podrían dar forma a la agenda de una jornada de innovación con un cliente del sector? Indica la fuente de cada tendencia.
    ```

3. Revisa la respuesta y abre al menos una de las fuentes citadas.
4. Si la respuesta es demasiado general, refina el prompt. Por ejemplo, pide que se centre en empresas de transporte y distribución.

#### Comprobación

- Copilot propone tres tendencias relacionadas con logística o cadena de suministro.
- Cada tendencia tiene al menos una fuente.
- Has abierto una fuente y has comprobado que respalda lo que dice Copilot.

#### Reflexiona

- ¿La fuente que has abierto decía exactamente lo mismo que Copilot o lo ha interpretado?
- ¿Qué tendencia elegirías como eje de la jornada y por qué?

---

### Tarea 2: Generar ideas para las sesiones

#### Objetivo

Convertir las tendencias en propuestas concretas de sesiones y mejorar el resultado con un prompt de seguimiento.

#### Pasos

1. En la misma conversación, envía este prompt:

    ```text
    Basándote en esas tendencias, sugiere 5 títulos de sesiones atractivos y escribe una descripción breve de cada una para una jornada de innovación con una empresa de logística.
    ```

2. Revisa el resultado.
3. Envía este prompt de seguimiento:

    ```text
    Haz que las descripciones sean más atractivas, con un tono dinámico y profesional, y de un máximo de 40 palabras cada una.
    ```

4. Compara las dos versiones.

#### Comprobación

- Hay cinco sesiones, cada una con título y descripción.
- Las sesiones se relacionan con las tendencias de la Tarea 1.
- La segunda versión respeta el límite de 40 palabras por descripción.

#### Reflexiona

- ¿Qué ha cambiado al pedir un tono concreto y un límite de palabras?
- ¿Por qué Copilot ha podido usar las tendencias sin que se las volvieras a escribir?

---

### Tarea 3: Visualizar la agenda y crear un logotipo

#### Objetivo

Convertir las ideas en una tabla de agenda y en una imagen, y comprobar que la tabla respeta las condiciones del prompt.

#### Pasos

1. En la misma conversación, envía este prompt:

    ```text
    Elige cuatro de las sesiones propuestas y crea la agenda de la jornada en formato de tabla, con una bienvenida, un cierre, las cuatro sesiones empezando a las 9:00, 11:00, 13:30 y 15:00, dos descansos breves y una hora para comer.
    ```

2. Revisa la tabla.
3. Envía este prompt de seguimiento:

    ```text
    Crea un logotipo sencillo y moderno para la Jornada de Innovación en Logística. Sin texto.
    ```

4. Revisa la imagen y, si no te convence, pide un cambio concreto. Por ejemplo, otros colores o un estilo más minimalista.

#### Comprobación

- La tabla incluye bienvenida, cierre, cuatro de las sesiones de la Tarea 2, dos descansos y una hora para comer.
- Las sesiones empiezan exactamente a las 9:00, 11:00, 13:30 y 15:00.
- Ningún tramo se solapa con otro.
- Copilot ha generado al menos un logotipo.

#### Reflexiona

- ¿Has encontrado algún error en los horarios? Si es así, ¿lo habrías detectado sin revisarlos?
- ¿Qué criterio ha usado Copilot para elegir cuatro de las cinco sesiones? ¿Lo ha explicado?
- ¿Por qué conviene pedir «sin texto» en una imagen generada?

---

### Tarea 4: Crear el documento de planificación en Word

#### Objetivo

Redactar un resumen de planificación y convertirlo en un documento de Word que sirva de referencia para el equipo.

#### Pasos

1. En la misma conversación, envía este prompt:

    ```text
    Crea un resumen de planificación de una página para la Jornada de Innovación en Logística con Grupo Alcora que incluya: objetivos, público asistente, temas de las sesiones e hitos clave de la preparación.
    ```

2. Envía este prompt de seguimiento:

    ```text
    Añade una sección con los resultados esperados y las métricas de éxito de la jornada.
    ```

3. Envía este prompt:

    ```text
    Crea un documento de Word con este resumen de planificación.
    ```

4. Abre el documento generado y comprueba su contenido.
5. Guárdalo con el nombre `Resumen planificación Jornada Innovación` en tu OneDrive o descárgalo en tu equipo.

!!! note "Si Copilot no crea el documento de Word"
    Según la versión y la configuración de tu organización, es posible que Copilot no genere el archivo directamente. En ese caso, copia la respuesta con el botón **Copiar**, pégala en un documento nuevo de Word y guárdalo con el mismo nombre.

#### Comprobación

- El documento contiene objetivos, público asistente, temas de las sesiones, hitos de preparación, resultados esperados y métricas de éxito.
- Los temas de las sesiones coinciden con los de la agenda de la Tarea 3.
- El documento está guardado y sabes dónde encontrarlo.

#### Reflexiona

- ¿Qué ventaja tiene pasar el resultado a Word en lugar de dejarlo en la conversación?
- ¿Las métricas de éxito que propone Copilot se pueden medir de verdad?

---

### Tarea 5: Analizar el documento y redactar un correo

#### Objetivo

Subir el documento de la Tarea 4, resumirlo y generar a partir de él una comunicación interna.

#### Pasos

1. Abre una **conversación nueva**.
2. Selecciona **+** y la opción para cargar imágenes y archivos (o la de adjuntar archivos de la nube, si lo guardaste en OneDrive) y sube el documento de la Tarea 4.
3. Envía este prompt:

    ```text
    Resume los puntos clave de este resumen de planificación en solo 5 viñetas.
    ```

4. Envía este prompt de seguimiento:

    ```text
    Redacta un correo para el equipo de la cuenta de Grupo Alcora con estos puntos clave y los próximos pasos.
    ```

5. Revisa el correo. No lo envíes.

#### Comprobación

- El resumen tiene exactamente cinco viñetas.
- Todas las viñetas proceden del documento y no añaden información nueva. Lo que no está en el documento no debería aparecer en el resumen.
- El correo incluye los puntos clave y unos próximos pasos concretos.

#### Reflexiona

- Has abierto una conversación nueva: ¿de dónde ha sacado Copilot la información esta vez?
- ¿Qué revisarías del correo antes de enviarlo a personas reales?

---

### Tarea 6 *(opcional)*: Revisión y reflexión

#### Objetivo

Consolidar lo aprendido y relacionarlo con tu trabajo diario.

#### Pasos

1. Vuelve a la conversación de las tareas 1 a 4: búscala en el historial de conversaciones del panel lateral.
2. Envía este prompt, sustituyendo `[rol]` por tu puesto:

    ```text
    Crea una lista de verificación de lo que he practicado hoy con Copilot Chat y de cómo puedo aplicarlo en mi trabajo como [rol].
    ```

#### Comprobación

- La lista recoge las tareas del laboratorio que se hicieron en esa conversación (tareas 1 a 4).
- Al menos una aplicación es útil de verdad para tu puesto.

#### Reflexiona

- ¿Por qué Copilot no puede incluir en la lista lo que hiciste en la Tarea 5?
- ¿Qué tarea de tu semana podrías empezar a hacer con Copilot a partir de mañana?

---

## Comprobación final

Comprueba que:

- Has investigado tendencias con búsqueda web y has abierto al menos una fuente.
- Has generado y mejorado ideas con prompts de seguimiento.
- Has creado una tabla de agenda y has comprobado sus horarios.
- Has generado un logotipo.
- Has creado y guardado un documento de Word.
- Has subido el documento, lo has resumido y has redactado un correo sin enviarlo.

## Resumen

| Concepto | Punto clave |
|---|---|
| **Investigación con Copilot** | La búsqueda web aporta información actual, pero hay que comprobar las fuentes. |
| **Generación de ideas** | Los prompts de seguimiento permiten ajustar el tono, la extensión y el enfoque sin empezar de cero. |
| **Contenido visual** | Copilot crea tablas e imágenes a partir de una descripción; los detalles (como los horarios) se revisan. |
| **Creación de documentos** | Una respuesta de Copilot puede convertirse en un documento de Word para compartirlo y conservarlo. |
| **Análisis de archivos** | Copilot resume archivos subidos y genera comunicaciones a partir de ellos. |
| **Revisión humana** | Copilot propone; tú compruebas y decides qué se envía. |

## Recursos adicionales

- [Microsoft 365 Copilot Chat](https://m365.cloud.microsoft/chat)
- [Información general de Microsoft Copilot Chat (Microsoft Learn, en inglés)](https://learn.microsoft.com/en-us/copilot/overview)
- [Carga de archivos en Microsoft Copilot (Microsoft Support, en inglés)](https://support.microsoft.com/en-us/microsoft-365-copilot/file-upload-in-microsoft-copilot)