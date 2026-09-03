---
name: daily-legal-snippet
description: "Guía de formato de Quipu Advisors para redactar novedades legislativas y regulatorias diarias (snippets): proyectos de ley y su tramitación en jurisdicciones argentinas y regionales (Chile, Uruguay, Paraguay), y normas vigentes del Poder Ejecutivo del Boletín Oficial (decretos, resoluciones, disposiciones). Usá esta skill SIEMPRE que pidan armar el snippet del día, o pegar/adjuntar proyectos presentados, giros a comisión, dictámenes, media sanción, sanción, publicación en el B.O. de una ley, o normas del Poder Ejecutivo, para redactarlos en el formato de Quipu. Usala también con el mail diario del Boletín Oficial (lista sin organizar) para clasificarlo por sector. Usala aunque no digan 'snippet': si el pedido es redactar movimientos parlamentarios o normas del BO para clientes, es esta skill. Produce el snippet en texto/markdown: NO la uses para WhatsApp, ni para convertir el snippet ya redactado a JSON (para eso existe `snippet-digital-json`)."
---

# Snippet legislativo y regulatorio diario — Quipu Equipo Legal

Redactá cada novedad siguiendo los esquemas de abajo, adaptando la información disponible a los campos.

**Si falta información para completar algún campo, PREGUNTÁ.** No inventes fechas, números de expediente, autores, bloques ni enlaces, y no completes un campo con una suposición: el snippet se comparte con clientes y un dato inventado es un error caro. Es preferible una pregunta corta que un campo mal llenado.

## Hipervínculos

El principio es simple: **cada enlace que provee el usuario se ancla sobre el elemento que describe.** El enlace al texto del proyecto (o de la norma) va sobre el título; el enlace a la ficha del expediente va sobre el número de expediente. El ancla tiene que anticipar lo que el lector encuentra del otro lado.

De ahí salen tres casos:

**1. El usuario pasa enlace del proyecto (con o sin enlace de expediente).**
El título en mayúsculas lleva el ancla al proyecto. Si además pasó enlace de expediente, el número de expediente también se hipervincula, a su propia ficha.

[PROYECTO DE LEY PRESENTADO](url-del-proyecto) | Argentina – Cámara de Diputados de la Nación – [Expte. N° 4166-D-2026](url-del-expediente). El 12 de agosto (…)

**2. El usuario pasa solo enlace del expediente.**
El título queda en texto plano y el ancla va sobre el número de expediente. Se cierra con la aclaración en itálica (ver abajo).

PROYECTO DE LEY PRESENTADO | Salta – Cámara de Senadores – [Expte. N° 90-34.536/2026](url-de-la-ficha). El 13 de agosto (…)

**3. El usuario no pasa ningún enlace.**
Título y expediente en texto plano. Avisale que falta el enlace; no lo busques ni lo construyas.

### Nunca inventes la URL

No armes ni "adivines" un enlace a partir del número de expediente (o de norma), ni lo reconstruyas por analogía con otro que hayas visto. Usá exclusivamente los enlaces que el usuario provea.

### Nunca pongas el enlace al final

No escribas "Proyecto disponible acá", "Link al proyecto", "Proyecto en este enlace" ni ninguna variante como frase suelta al cierre. El enlace no es un dato que se cuelga atrás: es el título o el expediente convertido en ancla. Repetirlo al final ensucia el texto que el cliente reenvía.

❌ Incorrecto: PROYECTO DE LEY PRESENTADO | Catamarca – Cámara de Diputados – Expte. N° \*\*\*. El 12 de agosto (…) tiene por objeto \*\*\*. Proyecto disponible acá.

✅ Correcto: [PROYECTO DE LEY PRESENTADO](url-del-proyecto) | Catamarca – Cámara de Diputados – Expte. N° \*\*\*. El 12 de agosto (…) tiene por objeto \*\*\*.

Esto aplica a todos los esquemas: en giro a comisión, orden del día, dictamen, media sanción y sanción, y también en las normas del Boletín Oficial (esquema 4), el ancla del título es igualmente el texto en mayúsculas del encabezado.

### Aclaración cuando no hay texto del proyecto

Muchas legislaturas provinciales publican la ficha del expediente pero no el articulado. En el caso 2, cerrá el snippet con:

*Por el momento el proyecto no se encuentra disponible en el portal oficial.*

Va al final de todo, después del giro a comisiones, y es lo único del snippet que se escribe en itálica. Sirve para que el cliente sepa por qué no puede llegar al texto y no lo interprete como un error del snippet.

**Identificación del proyecto**: puede ser por N° de proyecto o por N° de expediente bajo el que tramita. Mantené coherencia a lo largo de todas las actualizaciones de un mismo proyecto: una vez que lo identificaste con el N° de proyecto, usá ese mismo formato para el resto de sus novedades.

## Formato de fecha

Escribí las fechas con el día en número y el mes en palabras, **sin año**: "El 21 de agosto". No uses formato numérico (21/08/26, 21-08-2026) ni antepongas "el pasado".

El año nunca va. El snippet se actualiza semanalmente y siempre cubre las últimas novedades, así que el año se sobreentiende y agregarlo solo alarga el encabezado.

## Jurisdicción

El campo de jurisdicción lleva **un solo nombre**:

- Para el ámbito nacional (Argentina): `Argentina`.
- Para el ámbito provincial: **el nombre de la provincia sola** — `Catamarca`, `Tucumán`, `Mendoza`, `Buenos Aires`.
- Para el monitoreo regional: `Chile`, `Uruguay` o `Paraguay` (u otro país de la región que corresponda), sin desagregación subnacional.

**Nunca encadenes país y provincia.** No escribas "Argentina – Catamarca", "Argentina (Tucumán)" ni "Argentina, Provincia de Mendoza". La provincia ya identifica la jurisdicción sin ambigüedad, y anteponerle el país agrega un nivel que el cliente no necesita y que rompe la lectura del encabezado.

❌ Incorrecto: `| Argentina – Catamarca – Cámara de Diputados – Expte. N° \*\*\*`
✅ Correcto: `| Catamarca – Cámara de Diputados – Expte. N° \*\*\*`

Después del guion va el organismo (Cámara de Diputados, Senado, Legislatura, Ministerio, según corresponda), no una segunda referencia geográfica.

## Resumen ampliado (solo a pedido)

Por defecto, el snippet se redacta **con la información que aporta el usuario y nada más**. No abras el texto del proyecto (o de la norma) ni agregues contenido que el usuario no haya provisto: el snippet estándar es corto y se limita a la descripción del objeto.

Incorporá un resumen ampliado **solo cuando el usuario lo pida expresamente**. Recién ahí corresponde ir al texto del proyecto y sintetizar lo que va más allá de la descripción pegada.

Cuando lo pida:

- **Extensión:** hasta 800 caracteres.
- **Contenido:** cuál es el gran cambio que trae el proyecto (o la norma) y qué obligaciones involucra, sin mucho detalle.
- **Estilo:** usá conectores para que el texto sea autosuficiente cuando el cliente lo reenvíe a otra persona. No pueden ser ideas sueltas encadenadas como si fueran titulares.

### Dónde va

El resumen se **integra al mismo párrafo**, como continuación de la oración del objeto. No es un bloque aparte, ni un párrafo separado, ni lleva título: el snippet es un texto corrido que el cliente copia y pega entero.

El orden dentro del párrafo es siempre: encabezado y objeto → resumen ampliado (si fue pedido) → giro a comisiones (si aplica).

**Ejemplo con resumen ampliado:**

[PROYECTO DE LEY PRESENTADO](url) | Argentina – Cámara de Diputados de la Nación – Expte. N° 4166-D-2026. El [fecha], Kelly Olmos ([bloque], [distrito]) y otros presentaron un proyecto de ley que tiene por objeto crear un régimen integral de protección y desendeudamiento de las familias argentinas, modificando el DNU 70/2023 y las leyes 24.240 y 25.065. El proyecto declara la emergencia por sobreendeudamiento de personas humanas y crea un régimen obligatorio de reestructuración de deudas de consumo de hasta tres canastas básicas, con condonación total de los punitorios, planes de doce a treinta y seis meses, suspensión de embargos y ejecuciones, y una tasa tope de TAMAR más 25%. Un régimen equivalente se aplica a las deudas hipotecarias sobre vivienda única. Para los emisores de tarjetas, modifica la Ley 25.065: limita el interés compensatorio, topea los cargos no financieros en 3% del saldo, acota los punitorios y amplía los deberes de información. Deroga los artículos del DNU 70/2023 que habían desregulado el régimen de tarjetas. Giro a las comisiones de Finanzas, de Defensa del Consumidor, del Usuario y de la Competencia y de Presupuesto y Hacienda.

## Giro a comisiones

Cuando el insumo indique a qué comisiones se giró el proyecto, cerrá el párrafo con esa información: "Giro a la comisión de \*\*\*." o "Giro a las comisiones de \*\*\*, de \*\*\* y de \*\*\*." Va siempre al final, después del objeto y del resumen ampliado si lo hubiera. Solo aplica a proyectos de ley (esquemas 1-3) — las normas del Boletín Oficial (esquema 4) no tienen giro, ya están vigentes.

## Autores y bloques

Incluí entre paréntesis el bloque y distrito de quien presentó el proyecto. **Si lo presentó más de un legislador, mencioná solo al primero seguido de "y otros".**

### Verificación obligatoria en portales oficiales

El bloque y el distrito se toman **únicamente de la nómina oficial de la cámara correspondiente**, o del propio expediente cuando la fuente oficial lo consigna:

- Diputados de la Nación: https://www.hcdn.gob.ar/diputados/

**Si no podés acceder al portal oficial, avisale al usuario y dejá el campo sin completar.** No lo llenes con lo que aparezca en Google, en notas periodísticas, en Wikipedia ni con lo que recuerdes de otro snippet: la composición de los bloques cambia durante el mandato —hay pases, rupturas y monobloques nuevos— y un bloque desactualizado en un documento que va al cliente es un error difícil de detectar y fácil de propagar, porque después se arrastra a todas las novedades siguientes del mismo proyecto.

Decilo de forma explícita, por ejemplo: *"No pude acceder a la nómina oficial de Diputados; dejé el bloque de X sin completar para que lo verifiques."* Es preferible entregar el snippet con un campo pendiente y señalado que con un dato plausible pero sin respaldo.

El mismo criterio vale para las legislaturas provinciales: usá el portal oficial de la legislatura respectiva y, si no está disponible o no publica la nómina, avisá en lugar de completar.

### Discrepancias entre fuentes

La información que pega el usuario sale de los portales oficiales, así que **prevalece sobre la carátula del PDF del proyecto**. Redactá el snippet con el dato pegado.

Igualmente, **cuando detectes una diferencia, señalala** —nombre del legislador, bloque, objeto, fecha— fuera del snippet, en una nota al pie de tu respuesta. No la escondas ni la resuelvas en silencio: la carátula puede reflejar un bloque anterior, un nombre de pila distinto o un título que cambió, y al usuario le sirve saberlo aunque el criterio ya esté decidido. No frenes el trabajo para preguntar: entregá el snippet completo con el dato del portal y dejá la observación abajo.

Ejemplo: *"Nota: la carátula del PDF firma como Gabriela Lizana y consigna el bloque Frente Renovador; usé María Lizana y Renovador Mendoza Línea Nacional, según la ficha."*

---

# Esquemas

## 1. Presentación de proyecto nuevo

**Esquema:** [PROYECTO DE LEY PRESENTADO](url) | [Jurisdicción: Argentina, o el nombre de la provincia sola] – [Organismo] – Expte. N° \*\*\*. El [dd de mes] [Nombre autor] (Bloque, Distrito) presentó un proyecto de ley que tiene por objeto \*\*\*. [Resumen ampliado de hasta 800 caracteres, solo si el usuario lo pidió, en el mismo párrafo.] Giro a la comisión de \*\*\*.

**Ejemplo:** PROYECTO DE LEY PRESENTADO | Argentina – Cámara de Diputados de la Nación – Expte. N° 7854-D-2024. El 17 de febrero, Martin Tetaz (UCR, Ciudad de Buenos Aires) presentó un proyecto de ley que tiene por objeto la regulación de la publicidad y promoción de negocios a los fines de la prevención de estafas piramidales.

## 2. Cambios de estado

### 2.1 Asignación a comisión
**Esquema:** [PROYECTO ASIGNADO A COMISIÓN](url) | [Jurisdicción] [Organismo] Expte. N° \*\*\*. El [dd de mes] el proyecto de ley [identificación del proyecto] fue asignado a la comisión \*\*\* y se recibió el proyecto.

### 2.2 Giro a comisión
**Esquema:** [GIRO A COMISIÓN. PROYECTO DE LEY N°/EXPTE. N° \*\*\*](url) | [Jurisdicción]. El [dd de mes] el proyecto de ley [identificación del proyecto] tuvo giro (o ampliación de giro) a la comisión \*\*\*.

**Ejemplo:** GIRO A COMISIÓN. PROYECTO DE LEY 4336-D-2023 | Argentina – Cámara de Diputados de la Nación. El 2 de septiembre el proyecto de ley sobre apuestas online, de la diputada Danya Tavela (UCR), tuvo giro a la comisión de prevención de adicciones y control del narcotráfico en primer término.

### 2.3 Debate / orden del día
**Esquema:** [PROYECTO INGRESADO EN ORDEN DEL DÍA](url) | [Jurisdicción] Expte. N° \*\*\*. El proyecto de ley [identificación del proyecto] ingresó al orden del día y debate de la Comisión de \*\*\*.

**Ejemplo:** Paraguay. Debate proyecto de ley D-2481038. El 10 de diciembre se postergó por 8 días el debate del proyecto de ley D-2481038 que modifica los artículos 09, 11, 14, 15, 17, 19, 21, 22, 23 y 24 de la ley 6534/20 de protección de datos personales crediticios y establece sanciones penales por el uso abusivo y distorsionado de las informaciones personales crediticias.

### 2.4 Consulta a organismo
**Esquema:** [SOMETIDO A CONSULTA DE ORGANISMO PROYECTO N° \*\*\*](url) | [País/Provincia]. El [dd de mes] fue sometido a consulta al [nombre del organismo] el proyecto de ley [descripción del objeto del proyecto].

**Ejemplo:** MOVIMIENTO DE PROYECTO DE LEY N° 24616. Costa Rica. El 11 de diciembre fue sometido a consulta al Ministerio de Planificación el proyecto de ley sobre protección del consumidor de servicios o productos financieros.

### 2.5 Dictamen
**Esquema:** [DICTAMEN PROYECTO DE LEY N° \*\*\*](url) | [País/Provincia]. El [dd de mes] el proyecto de ley [descripción del objeto del proyecto] obtuvo entrada en la sesión ordinaria de [fecha] / obtuvo dictamen de la comisión \*\*\* en [fecha], el cual [aprueba/rechaza] dicho proyecto.

**Ejemplo:** DICTAMEN PROYECTO DE LEY D-2476898. Paraguay. El 11 de marzo el proyecto de ley que regula el régimen general de la unidad monetaria virtual, de su interacción e integración con la moneda física nacional y de los criptoactivos en el territorio nacional de la república obtuvo entrada en la sesión ordinaria de esa misma fecha. También obtuvo un dictamen de la Comisión de Energía y Minería en la misma fecha, el cual rechaza dicho proyecto.

### 2.6 Observaciones
**Esquema:** [OBSERVACIÓN EXPTE. N° \*\*\*](url) | [Jurisdicción]. El [dd de mes] el proyecto de ley [descripción del objeto del proyecto] tuvo observaciones en la Cámara \*\*\*.

### 2.7 Aprobación en Cámara de Origen (media sanción)
**Esquema:** [MEDIA SANCIÓN PROYECTO DE LEY N° \*\*\*](url) | [Jurisdicción]. El [dd de mes] fue aprobado en la Cámara de Origen el proyecto de ley [descripción del objeto del proyecto].

**Ejemplo:** República Dominicana. El 11 de diciembre fue aprobado en la Cámara de Origen con modificaciones el proyecto de ley 02892-2024-PLO-SE que modifica la ley núm. 358-05, del 9 de septiembre de 2005, ley general de protección de los derechos del consumidor o usuario.

### 2.8 Sanción o rechazo por la Cámara Revisora
**Esquema:** [SANCIÓN/RECHAZO LEY \*\*\*](url) | [Jurisdicción] [Organismo]. El [dd de mes] fue aprobado/no aprobado en la Cámara Revisora el proyecto de ley [descripción del objeto del proyecto].

### 2.9 Imprenta
**Esquema:** [ENVÍO A IMPRENTA LEY N° \*\*\*](url) | [Jurisdicción]. El [dd de mes] el proyecto de ley [descripción del objeto del proyecto] pasó a ser enviado a imprenta nacional para su publicación.

### 2.10 Publicación y entrada en vigencia (de una LEY sancionada)
**Esquema:** [PUBLICACIÓN B.O.](url) [Ley N° — Jurisdicción]. El [dd de mes] se publicó en el Boletín Oficial la Ley N° \*\*\* [descripción del objeto], que entrará en vigencia el próximo \*\*\*.

Este esquema es la última etapa del ciclo de un proyecto de ley que **sí pasó por el Congreso**
y llegó a sancionarse. **No confundir con el esquema 4** (Normas del Boletín Oficial): una norma
del Poder Ejecutivo (decreto, resolución, disposición) nunca fue proyecto de ley ni pasó por el
Congreso — usa un tag deliberadamente distinto (`NORMA PUBLICADA`, no `PUBLICACIÓN B.O.`) para
que no se confundan al redactar.

## 3. Solicitud de retiro

**Esquema:** [SOLICITUD DE RETIRO. PROYECTO DE LEY N°/EXPTE. N° \*\*\*](url) | [País/Provincia]. El [dd de mes] [nombre legislador] solicitó el retiro del proyecto de ley [descripción del objeto del proyecto].

## 4. Normas del Boletín Oficial (novedades regulatorias)

A diferencia de los esquemas 1 a 3 —sobre un proyecto de ley y su trámite en el Congreso o una
legislatura—, este esquema es para una **norma ya vigente** del Poder Ejecutivo: un decreto, una
resolución o una disposición publicada en el Boletín Oficial (de Argentina o de otro país de la
región). Nunca pasó por el Congreso, así que no tiene giro a comisión, dictamen ni media sanción
— y no se confunde con el esquema 2.10 (Publicación B.O.), que es la etapa final de una LEY que
sí nació como proyecto.

**Esquema:** [NORMA PUBLICADA (DECRETO/RESOLUCIÓN/DISPOSICIÓN)](url) | [Organismo]. El [dd de
mes] se publicó [el Decreto/la Resolución/la Disposición] N° \*\*\* que tiene por objeto \*\*\*.

**Organismo:**
- Si es un **Decreto de Argentina** → siempre `Poder Ejecutivo Nacional` (confirmado: en
  Argentina el decreto lo firma el PEN, no varía según qué ministerio lo refrenda).
- En cualquier otro caso —Resolución o Disposición de Argentina, o **cualquier norma de Chile,
  Uruguay o Paraguay, decreto incluido**— no hay organismo fijo: usá el que indique la fuente
  (ej. "Ministerio de Justicia", "INASE"). No está confirmado que el Poder Ejecutivo de esos
  países firme sus decretos de forma uniforme como en Argentina — no lo asumas.
- Nunca uses un tema o rubro (ej. "Combustibles") como si fuera el organismo. Si la fuente no da
  el organismo real, dejalo sin completar y avisá, mismo criterio que con bloques/autores.

**Ejemplo (Argentina, decreto):** [NORMA PUBLICADA (DECRETO)](url) | Poder Ejecutivo Nacional. El
31 de agosto se publicó el Decreto N° 829/2026 que tiene por objeto postergar hasta el 1° de
octubre la aplicación de los incrementos remanentes en los montos del impuesto sobre los
combustibles líquidos y el impuesto al dióxido de carbono.

Aplican las mismas reglas generales que para proyectos de ley: el hipervínculo ancla sobre el
título en mayúsculas (nunca "link acá" al final, nunca una URL inventada — ver "Hipervínculos"
arriba), la fecha sin año, y la jurisdicción como un solo nombre (ver "Jurisdicción" arriba).

### Cuando el insumo es una lista sin clasificar (ej. el mail diario del Boletín Oficial)

El mail que reporta las normas del día suele traer una lista simple, sin organizar por sector.
Cuando el pedido sea justamente ordenar esa lista (no redactar una norma puntual que ya te
identificaron), además de aplicar el esquema de arriba a cada norma:

1. Clasificá cada una en **un sector** de esta lista: `Coyuntura general`, `Consumo masivo`,
   `Medios de pago`, `Tecnologia`, `Automotriz`, `Agroindustria`, `Petroquimicos`, `Mineria`,
   `Turismo`, `Salud`, `Ambiente`. Sin tildes, grafía exacta. Si ninguno encaja con claridad, usá
   `Coyuntura general`.
2. Agrupá la salida por sector: encabezado del sector, sub-encabezado con la jurisdicción
   (`NACIONAL` para Argentina; `CHILE`/`URUGUAY`/`PARAGUAY` para regional), y debajo cada norma
   con el esquema de arriba. Un salto de línea en blanco entre normas. Solo incluí sectores que
   tengan al menos una norma ese día — no listes sectores vacíos.
3. La clasificación por sector es un primer corte automático, no definitivo. Al final de tu
   respuesta, **fuera** del texto formateado, listá las normas cuyo sector te generó duda, para
   que las revisen antes de sumarlas al snippet del día.

**Links embebidos:** igual que con cualquier otro insumo (ver "Hipervínculos"), necesitás el
archivo (`.eml` o el mail reenviado con formato) para capturar los hipervínculos — si te pasan
solo texto plano copiado, los links se pierden. Generá el texto igual, sin links, y avisá al
final que conviene volver a generar adjuntando el original.

**Al pegar el resultado:** copiá la respuesta tal como se ve renderizada acá (el título de cada
norma como link azul clickeable), no el markdown crudo — así el link viaja como hipervínculo
real al pegarlo en Word, igual que con el resto del snippet.

**Ejemplo de salida agrupada:**

```
Petroquimicos
NACIONAL

[NORMA PUBLICADA (DECRETO)](url) | Poder Ejecutivo Nacional. El 31 de agosto se publicó el
Decreto N° 829/2026 que tiene por objeto postergar hasta el 1° de octubre la aplicación de los
incrementos remanentes en los montos del impuesto sobre los combustibles líquidos.

Agroindustria
NACIONAL

[NORMA PUBLICADA (RESOLUCIÓN)](url) | INASE. El 31 de agosto se publicó la Resolución N°
173/2026 que tiene por objeto ordenar la inscripción en el Registro Nacional de la Propiedad de
Cultivares de la creación fitogenética de soja de denominación 47A90.
```

*Dudas de clasificación: [listar acá, fuera del bloque formateado, las normas que no encajaron con claridad en ningún sector.]*

---

# Agenda de comisión (Diputados y Senadores)

Cuando el pedido sea la agenda de comisiones y no novedades de proyectos, armá la tabla o el listado de reuniones y usá este modelo de email para el envío:

> Estimados, buenos días.
>
> Espero encontrarlos muy bien. A continuación acompaño la agenda de comisión de \*\*\*.
>
> Saludos cordiales,
