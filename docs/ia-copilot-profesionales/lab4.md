# Laboratorio 4: Crear y probar un agente con Microsoft 365 Copilot

## Introducción

Los agentes de Microsoft 365 Copilot permiten configurar un comportamiento especializado y reutilizable mediante instrucciones, fuentes de conocimiento e indicaciones sugeridas. En este laboratorio crearás un agente para realizar una primera revisión interna de contratos SaaS, comprobarás su comportamiento con un caso de riesgo y reutilizarás el mismo método con un segundo expediente.

!!! note "Requisitos de acceso"
    Para completar este laboratorio necesitas acceso a Microsoft 365 Copilot y permisos para crear agentes con Agent Builder. Utiliza únicamente los archivos ficticios proporcionados. No cargues contratos reales ni información confidencial.

## Objetivos de aprendizaje

Al finalizar el laboratorio, podrás:

- Crear un agente mediante lenguaje natural con Agent Builder.

- Configurar instrucciones, conocimiento permanente e indicaciones sugeridas.

- Distinguir entre conocimiento permanente reutilizable y archivos asociados a un expediente concreto.

- Aplicar un método de revisión jurídica y financiera a contratos SaaS.

- Verificar que el agente calcula la exposición económica y clasifica los riesgos según las reglas internas.

- Detectar un fallo observable y corregir las instrucciones del agente.

- Reutilizar el mismo agente con expedientes diferentes sin incorporar los contratos al conocimiento permanente.

- Comprobar las fuentes, los cálculos y los límites que requieren revisión humana.

## Información del laboratorio

**Duración estimada:** 60 minutos.

**Material necesario:**

- Acceso a Microsoft 365 Copilot.

- Permisos para crear agentes con Agent Builder.

- Archivos de trabajo disponibles en la carpeta `/Files` del curso.

- Una de las fuentes de conocimiento permanente previstas para el laboratorio.

| Archivo | Uso |
|---|---|
| [01_Playbook_Juridico_SaaS.docx](recursos/files/01_Playbook_Juridico_SaaS.docx) | Conocimiento permanente del agente |
| [02_Politica_Financiera_Aprobaciones_SaaS.docx](recursos/files/02_Politica_Financiera_Aprobaciones_SaaS.docx) | Conocimiento permanente del agente |
| [03_Contrato_OrionCloud.docx](recursos/files/03_Contrato_OrionCloud.docx) | Primer caso de prueba |
| [04_Solicitud_Compra_OrionCloud.docx](recursos/files/04_Solicitud_Compra_OrionCloud.docx) | Datos económicos y de negocio del primer caso |
| [05_Contrato_NovaDesk.docx](recursos/files/05_Contrato_NovaDesk.docx) | Segundo caso de prueba |
| [06_Solicitud_Compra_NovaDesk.docx](recursos/files/06_Solicitud_Compra_NovaDesk.docx) | Datos económicos y de negocio del segundo caso |

!!! note "Usuarios sin acceso a conocimiento permanente"
    Algunas licencias de Microsoft 365 Copilot no permiten adjuntar documentos al conocimiento permanente del agente.

    En ese caso, no cargues los archivos de conocimiento en el agente. Crea primero el agente y, después, utiliza `01_Playbook_Juridico_SaaS.docx` y `02_Politica_Financiera_Aprobaciones_SaaS.docx` como archivos de trabajo en las conversaciones de prueba.

    Cuando ejecutes cada caso, adjunta estos dos documentos junto con los archivos del expediente correspondiente. No intentes adjuntarlos durante la configuración del agente: primero debes crearlo.

    Esto no cambia el objetivo didáctico del laboratorio. El agente sigue utilizando el mismo playbook jurídico y la misma política financiera. La única diferencia es que el conocimiento se proporciona durante cada conversación en lugar de almacenarse como conocimiento permanente.

!!! warning "Separación entre reglas y expedientes"
    **Conocimiento permanente = reglas reutilizables.**

    **Archivos de sesión = expediente concreto.**

    Con licencia completa, las reglas se almacenan como conocimiento permanente del agente.

    Sin acceso a conocimiento permanente, las mismas reglas se adjuntan en cada ejecución como archivos de trabajo.

    Los contratos y solicitudes de compra de OrionCloud y NovaDesk siempre son archivos de sesión. Nunca deben añadirse al conocimiento permanente.

## Caso práctico

Lumen Retail España, S.A. compra con frecuencia aplicaciones SaaS. Los equipos de Legal y Finanzas necesitan una primera revisión homogénea antes de dedicar tiempo a una negociación.

El agente no aprobará contratos ni autorizará gasto. Su función será:

- Detectar condiciones jurídicas y financieras relevantes.

- Calcular la exposición económica.

- Comparar el expediente con las reglas internas.

- Clasificar los hallazgos.

- Señalar qué debe negociar o escalar una persona.

El alumno debe aprender a configurar un método de revisión reutilizable, separar las reglas permanentes de los documentos de cada expediente y validar que el agente mantiene sus límites de actuación.

### Tarea 1: Configurar el agente

#### Objetivo

Crear mediante lenguaje natural un agente que aplique un método reutilizable de revisión jurídica y financiera de contratos SaaS.

#### Pasos

1. Abre Microsoft 365 Copilot.

2. Selecciona **Agentes**.

3. Selecciona **Nuevo agente** o **Crear agente**.

4. Utiliza la creación mediante lenguaje natural.

5. Copia y envía la siguiente descripción:

```text
Crea un agente llamado «Revisor Legal-Financiero SaaS» para realizar una primera revisión interna de contratos de proveedores SaaS de Lumen Retail España.
Debe:
1. Extraer las condiciones jurídicas y financieras relevantes del contrato y de la solicitud de compra que adjunte el usuario.
2. Compararlas con el playbook jurídico y la política financiera incluidos en su conocimiento.
3. Separar claramente HECHO, CÁLCULO e INTERPRETACIÓN.
4. Calcular el coste recurrente anual y el compromiso total cuando existan datos suficientes, mostrando la fórmula.
5. Clasificar cada hallazgo como VERDE, ÁMBAR o ROJO según las reglas internas.
6. Indicar qué aprobación interna o escalado sería necesario.
7. Preparar una tabla final con estas columnas: tema, condición encontrada, regla interna, clasificación, recomendación y fuente.
8. Terminar con «Preguntas abiertas», «Siguientes pasos» y «Revisión humana necesaria».
Nunca debe aprobar contratos, autorizar gasto, afirmar que un contrato es legal o seguro ni sustituir a Legal, Finanzas, DPO, Seguridad o Compras.
Si falta información, debe escribir «NO CONSTA» y solicitarla.
Debe identificar la fuente concreta de cada conclusión importante.
```

6. Abre la pestaña **Configurar**.

7. Revisa el nombre, la descripción y las instrucciones generadas.

8. Corrige cualquier formulación que permita al agente aprobar, autorizar o decidir.

#### Comprobación

Comprueba que:

- Agent Builder ha generado una primera configuración.

- El agente se llama **Revisor Legal-Financiero SaaS**.

- La descripción identifica la revisión interna de contratos SaaS como finalidad del agente.

- Las instrucciones exigen separar HECHO, CÁLCULO e INTERPRETACIÓN.

- El agente debe mostrar las fórmulas financieras e identificar sus fuentes.

- Las instrucciones impiden que el agente apruebe contratos o autorice gasto.

### Tarea 2: Configurar el conocimiento permanente

#### Objetivo

Proporcionar al agente las reglas jurídicas y financieras reutilizables sin incorporar los contratos específicos de OrionCloud o NovaDesk al conocimiento permanente.

#### Pasos

1. En la pestaña **Configurar**, localiza **Conocimiento**.

2. Selecciona la ruta correspondiente a tu licencia.

3. Si dispones de acceso completo a la carga de conocimiento permanente, añade estos dos archivos:

    1. `01_Playbook_Juridico_SaaS.docx`

    2. `02_Politica_Financiera_Aprobaciones_SaaS.docx`

4. Comprueba que los archivos 01 y 02 aparecen como fuentes de conocimiento permanente.

!!! note "Usuarios sin acceso a conocimiento permanente"
    Algunas licencias de Microsoft 365 Copilot no permiten adjuntar documentos al conocimiento permanente del agente.

    En ese caso, no cargues los archivos de conocimiento en el agente. Una vez creado, conserva `01_Playbook_Juridico_SaaS.docx` y `02_Politica_Financiera_Aprobaciones_SaaS.docx` para adjuntarlos como archivos de trabajo en cada conversación de prueba.

    Esto no cambia el objetivo didáctico del laboratorio. El agente utilizará el mismo playbook jurídico y la misma política financiera. Únicamente cambia la forma de proporcionar las reglas: durante cada conversación, en lugar de almacenarlas como conocimiento permanente.

5. No añadas los contratos ni las solicitudes de compra de OrionCloud o NovaDesk al conocimiento permanente.

6. En **Indicaciones sugeridas**, añade:

```text
Revisa un contrato SaaS y dime qué debo negociar o escalar.
Calcula el compromiso económico y dime qué aprobaciones financieras requiere.
Resume los riesgos jurídicos y financieros para una reunión con Compras.
```

7. Si utilizas conocimiento permanente, confirma que contiene reglas aplicables a todos los expedientes y no información específica de un contrato concreto.

8. Si no tienes acceso a conocimiento permanente, confirma que conservarás los archivos 01 y 02 para adjuntarlos durante la ejecución de cada caso.

9. Cuando hayas terminado la configuración, selecciona **Crear** para crear el agente.

10. Espera a que se cree y comprueba que aparece en **Agentes**. A partir de este momento podrás abrir una conversación y adjuntar los archivos de cada caso.

#### Comprobación

Comprueba que:

- El agente tiene acceso al playbook jurídico.

- El agente tiene acceso a la política financiera.

- Las reglas se proporcionan mediante una de estas rutas:

    - los archivos `01_Playbook_Juridico_SaaS.docx` y `02_Politica_Financiera_Aprobaciones_SaaS.docx` almacenados como conocimiento permanente;

    - o esos mismos archivos adjuntados como archivos de trabajo durante cada ejecución.

- Los contratos OrionCloud y NovaDesk no forman parte del conocimiento permanente.

- Las solicitudes de compra de OrionCloud y NovaDesk no forman parte del conocimiento permanente.

- Las indicaciones sugeridas aparecen en la configuración.

- El agente se ha creado antes de adjuntar documentación a una conversación.

#### Reflexiona

- ¿Qué problema produciría añadir un contrato concreto al conocimiento permanente?

- ¿Qué información podrá reutilizar el agente en todos los expedientes?

- ¿Por qué adjuntar las reglas durante cada conversación mantiene el objetivo didáctico aunque no exista conocimiento permanente?

### Tarea 3: Probar el agente con OrionCloud

#### Objetivo

Comprobar que el agente aplica el método configurado al caso de mayor riesgo, utiliza las reglas internas y evita emitir decisiones definitivas.

#### Pasos

1. En **Agentes**, abre **Revisor Legal-Financiero SaaS**, que ya has creado.

2. Inicia una conversación nueva con el agente.

3. Adjunta los siguientes archivos como archivos de sesión a esa conversación:

    1. `03_Contrato_OrionCloud.docx`

    2. `04_Solicitud_Compra_OrionCloud.docx`

4. Si no tienes acceso a conocimiento permanente, adjunta también estos archivos de reglas a la conversación:

    1. `01_Playbook_Juridico_SaaS.docx`

    2. `02_Politica_Financiera_Aprobaciones_SaaS.docx`

5. Verifica que el contrato y la solicitud de compra de OrionCloud se utilizan únicamente para la ejecución del caso y no se incorporan al conocimiento permanente.

6. Envía el siguiente prompt:

```text
Haz la revisión completa de este caso.
Incluye:
- Resumen ejecutivo.
- Tabla de riesgos Legal + Finanzas.
- Cálculos financieros con fórmula.
- Aprobaciones necesarias.
- Cinco puntos prioritarios para negociar con el proveedor.
- Preguntas abiertas.
- Revisión humana necesaria.
Aplica estrictamente tu conocimiento y muestra la fuente de cada conclusión importante.
```

7. Revisa la respuesta utilizando la siguiente validación mínima:

| Comprobación | Resultado esperado |
|---|---|
| Coste anual | 220.000 EUR; requiere CFO según la política financiera |
| Compromiso total inicial | 220.000 × 3 + 60.000 = 720.000 EUR |
| Plazo | 36 meses no cancelables; condición no estándar |
| Incremento | 6 % anual; supera el límite indicado |
| Renovación | 30 días; inferior al estándar de 60 días |
| Cambios unilaterales | Riesgo ROJO |
| Responsabilidad | 50 % de la tarifa anual; riesgo ROJO |
| Privacidad | DPA pendiente y transferencias no cerradas |
| Incidentes | Notificación en cinco días laborables; supera el estándar de 72 horas |
| Propiedad intelectual | No existe indemnidad específica |
| Jurisdicción | Nueva York; requiere escalado |

8. Evalúa el método aplicado. No busques una redacción idéntica.

#### Comprobación

Comprueba que:

- El agente ha detectado los riesgos principales.

- El agente ha calculado un coste anual de 220.000 EUR.

- El agente ha mostrado la fórmula `220.000 × 3 + 60.000 = 720.000 EUR`.

- El agente ha separado hechos, cálculos e interpretaciones.

- El agente ha identificado las fuentes de las conclusiones importantes.

- El agente ha indicado las aprobaciones o los escalamientos necesarios.

- El agente ha evitado aprobar el contrato o autorizar el gasto.

- Los archivos de OrionCloud se han utilizado como archivos de sesión y no como conocimiento permanente.

- Si tu licencia no permite conocimiento permanente, los archivos 01 y 02 se han adjuntado durante la ejecución del caso.

#### Reflexiona

- ¿Qué hallazgos dependen directamente del contrato y cuáles proceden de las reglas internas?

- ¿La clasificación de riesgos puede comprobarse a partir de las fuentes indicadas?

- ¿Qué conclusiones siguen necesitando la intervención de Legal, Finanzas, DPO, Seguridad o Compras?

### Tarea 4: Corregir el comportamiento

#### Objetivo

Identificar un problema observable en la respuesta del agente y corregirlo mediante una modificación de sus instrucciones.

#### Pasos

1. Identifica un fallo concreto en la respuesta de OrionCloud.

2. Utiliza como referencia uno de los siguientes fallos:

    1. Omite el cálculo del compromiso total.

    2. No identifica las fuentes.

    3. Mezcla hechos e interpretaciones.

    4. Presenta una aprobación como decisión final.

    5. No incluye la revisión humana.

3. Vuelve a la edición del agente.

4. Envía esta instrucción:

```text
Modifica el agente para que nunca emita una aprobación final.
Debe:
- Separar HECHO, CÁLCULO e INTERPRETACIÓN.
- Mostrar las fórmulas financieras.
- Identificar la fuente de cada conclusión importante.
- Terminar siempre con aprobaciones requeridas, preguntas abiertas y revisión humana necesaria.
```

5. Guarda o aplica los cambios si Agent Builder te lo solicita.

6. Regresa a la conversación de OrionCloud y ejecuta de nuevo el caso con los mismos archivos de sesión.

7. Compara la segunda respuesta con la primera.

#### Comprobación

Comprueba que:

- La segunda respuesta corrige el fallo identificado.

- Los elementos que ya funcionaban se mantienen.

- El agente separa HECHO, CÁLCULO e INTERPRETACIÓN.

- Las fórmulas financieras aparecen de forma explícita.

- Las conclusiones importantes identifican su fuente.

- La respuesta termina con las aprobaciones requeridas, las preguntas abiertas y la revisión humana necesaria.

- El agente no emite una aprobación final.

#### Reflexiona

- ¿El fallo procedía de las instrucciones del agente o del contenido del expediente?

- ¿La corrección mejora el comportamiento de forma reutilizable o solo resuelve el caso OrionCloud?

- ¿Qué evidencia permite afirmar que la segunda respuesta es mejor que la primera?

### Tarea 5: Reutilizar el agente con NovaDesk

#### Objetivo

Reutilizar el mismo agente y las mismas reglas internas con un expediente diferente, manteniendo separados el conocimiento permanente y los archivos de sesión.

#### Pasos

1. Abre el agente **Revisor Legal-Financiero SaaS**, que ya has creado.

2. Inicia una conversación nueva.

3. Adjunta los siguientes archivos como archivos de sesión a esa conversación:

    1. `05_Contrato_NovaDesk.docx`

    2. `06_Solicitud_Compra_NovaDesk.docx`

4. Si no tienes acceso a conocimiento permanente, adjunta también estos archivos de reglas a la conversación:

    1. `01_Playbook_Juridico_SaaS.docx`

    2. `02_Politica_Financiera_Aprobaciones_SaaS.docx`

5. Verifica que los archivos de NovaDesk no se incorporan al conocimiento permanente.

6. Envía el siguiente prompt:

```text
Realiza la misma revisión Legal + Finanzas aplicada al caso anterior.
Usa exactamente el mismo método y las mismas reglas internas.
Muestra los cálculos, la clasificación, las fuentes y la revisión humana necesaria.
```

7. Revisa la respuesta utilizando la siguiente validación mínima:

| Tema | Resultado esperado |
|---|---|
| Coste recurrente | 85.000 EUR |
| Coste del primer año | 85.000 + 15.000 = 100.000 EUR |
| Plazo | 12 meses |
| Incremento | 2,5 % |
| Renovación | 90 días |
| Cambios materiales | No hay cambios materiales unilaterales |
| Responsabilidad | 1x general y 2x para datos y propiedad intelectual |
| Datos | DPA incorporado y producción en la UE |
| Incidentes | Notificación en 48 horas |
| Propiedad intelectual | Existe indemnidad |
| Ley aplicable | España/Madrid |

8. Compara el resultado con el caso OrionCloud.

!!! note "Conclusión didáctica"
    OrionCloud debería producir varios hallazgos ÁMBAR o ROJO. NovaDesk debería resultar mayoritariamente VERDE. La diferencia procede de los expedientes, no del método de revisión.

#### Comprobación

Comprueba que:

- El agente utiliza el mismo método aplicado a OrionCloud.

- El agente aplica las mismas reglas jurídicas y financieras.

- El coste recurrente es de 85.000 EUR.

- El coste del primer año se muestra mediante la fórmula `85.000 + 15.000 = 100.000 EUR`.

- La clasificación refleja las condiciones específicas de NovaDesk.

- La respuesta identifica las fuentes y la revisión humana necesaria.

- Los archivos de NovaDesk se han utilizado como archivos de sesión y no como conocimiento permanente.

- Si tu licencia no permite conocimiento permanente, los archivos 01 y 02 se han adjuntado durante la ejecución del caso.

- Si utilizas conocimiento permanente, este sigue conteniendo únicamente reglas reutilizables.

#### Reflexiona

- ¿Qué elementos de la revisión permanecen constantes entre OrionCloud y NovaDesk?

- ¿Qué diferencias proceden exclusivamente de los expedientes?

- ¿Por qué NovaDesk puede obtener una clasificación distinta sin modificar las instrucciones ni el conocimiento permanente?

### Comprobación final

Comprueba que:

- Has creado un agente reutilizable mediante Agent Builder.

- Has configurado un método de revisión jurídica y financiera.

- El agente tiene acceso al playbook jurídico y a la política financiera.

- Has proporcionado el playbook jurídico y la política financiera mediante conocimiento permanente o adjuntando los archivos 01 y 02 durante cada ejecución, según las capacidades de tu licencia.

- Puedes distinguir entre conocimiento permanente y archivos de sesión.

- OrionCloud y NovaDesk no forman parte del conocimiento permanente.

- Has ejecutado y validado el caso OrionCloud.

- Has identificado y corregido un fallo observable en el comportamiento del agente.

- Has reutilizado el mismo agente con el caso NovaDesk.

- El agente muestra fórmulas, clasificaciones, fuentes, aprobaciones requeridas y revisión humana necesaria.

- El agente no aprueba contratos ni autoriza gasto.

#### Reflexión final

- ¿Qué información permanece configurada en el agente?

- ¿Qué información cambia en cada expediente?

- ¿Por qué los contratos no deben añadirse al conocimiento permanente?

- ¿Qué mejoró después de modificar las instrucciones?

- ¿Qué decisiones siguen necesitando revisión humana?

- ¿Qué diferencia existe entre cambiar las reglas del agente y cambiar los archivos de una sesión?

## Resumen

| Concepto | Punto clave |
|---|---|
| **Copilot Chat** | Adecuado para tareas puntuales y flexibles |
| **Copilot Notebooks** | Adecuado para investigar un conjunto acotado de referencias |
| **Agente** | Mantiene un método reutilizable con instrucciones y conocimiento persistentes |
| **Agent Builder** | Permite crear y configurar agentes declarativos mediante lenguaje natural |
| **Conocimiento permanente** | Contiene reglas estables y reutilizables que deben aplicarse a todos los casos |
| **Archivos de sesión** | Contienen la información variable de cada expediente concreto |
| **Ruta con licencia completa** | Utiliza los archivos 01 y 02 como conocimiento permanente |
| **Ruta sin conocimiento permanente** | Adjunta los archivos 01 y 02 como archivos de trabajo durante cada ejecución |
| **OrionCloud y NovaDesk** | Son expedientes concretos y nunca deben añadirse al conocimiento permanente |
| **Conversación con el agente** | Permite probar el agente creado con los archivos de cada expediente |
| **Revisión humana** | El agente asiste en el análisis, pero no aprueba contratos ni autoriza gasto |

## Recursos adicionales

- [01_Playbook_Juridico_SaaS.docx](recursos/files/01_Playbook_Juridico_SaaS.docx)

- [02_Politica_Financiera_Aprobaciones_SaaS.docx](recursos/files/02_Politica_Financiera_Aprobaciones_SaaS.docx)

- [03_Contrato_OrionCloud.docx](recursos/files/03_Contrato_OrionCloud.docx)

- [04_Solicitud_Compra_OrionCloud.docx](recursos/files/04_Solicitud_Compra_OrionCloud.docx)

- [05_Contrato_NovaDesk.docx](recursos/files/05_Contrato_NovaDesk.docx)

- [06_Solicitud_Compra_NovaDesk.docx](recursos/files/06_Solicitud_Compra_NovaDesk.docx)