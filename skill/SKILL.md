---
name: snippet-digital-json
description: >-
  Convierte un Daily Legal Snippet (newsletter de monitoreo legislativo y regulatorio argentino,
  con eventual contenido regional de Chile/Uruguay/Paraguay) en un array JSON de proyectos de ley
  y normas del Boletín Oficial listo para pegar en la app interna "Snippet Digital" (campo
  "Importar proyectos (JSON)"). Usar SIEMPRE que el usuario pegue el contenido de un snippet o
  newsletter legislativo/regulatorio, adjunte el .eml del Daily Legal Snippet, o pida "extraer
  proyectos para Snippet Digital", "armar el JSON del snippet", "generar el JSON del snippet",
  "pasar el snippet a JSON", "procesar el snippet del día", o quiera convertir novedades
  legislativas/regulatorias argentinas o regionales al formato de Snippet Digital. También activar
  cuando haya un .eml o texto de newsletter legislativo y se mencione Snippet Digital, importar
  proyectos o cargar los proyectos del día. Funciona desde claude.ai, la app de Claude y el plugin
  de Office/Word.
---

# Snippet Digital — extractor a JSON

Esta skill toma un **Daily Legal Snippet** (el newsletter de monitoreo legislativo y regulatorio
de Quipu, en texto plano, HTML, o el contenido de un `.eml`) y devuelve un **array JSON** con un
objeto por ítem (proyecto de ley, norma del Boletín Oficial, o resumen de sesión/comisión), en el
formato exacto que importa la app Snippet Digital.

El usuario va a copiar tu salida y pegarla tal cual en el campo **"Importar proyectos (JSON)"**.
Por eso el formato tiene que ser exacto: la app es estricta con los nombres de campos y con los
valores de `tipo`, `sector` y `jur`.

## Qué hacer

1. Leé todo el snippet e identificá **cada ítem** mencionado: proyectos de ley (incluye cualquier
   movimiento de su ciclo — presentado, giro, dictamen, media sanción, sanción/rechazo, etc.),
   normas publicadas del Boletín Oficial (decretos/resoluciones/disposiciones, tag `NORMA
   PUBLICADA (...)`), y resúmenes de sesión/comisión (tag `RESUMEN DE SESIÓN | ...`). Ignorá lo
   que no sea ninguno de estos tres: saludos, índices, encabezados del email, pies de página,
   publicidad.
2. Para cada ítem, armá un objeto con los campos de abajo — el set de campos relevantes cambia
   según `tipo` (ver "Campos por tipo").
3. Devolvé un único array JSON con todos los ítems, sin separarlos por tipo.

## Formato de salida

Devolvé **solo** un bloque de código ```json con el array — sin texto antes ni después, sin
comentarios, sin explicaciones. El usuario necesita copiar y pegar; cualquier texto extra le
ensucia el copiado. (La app igual tolera el bloque ```json y lo limpia sola.)

Cada objeto tiene EXACTAMENTE estos campos, siempre todos presentes:

| Campo | Regla |
|---|---|
| `tipo` | EXACTAMENTE uno de: `proyecto_ley`, `norma`, `resumen_sesion`. Ver "Cómo identificar el tipo" abajo. |
| `num` | Depende del tipo — ver "Campos por tipo" abajo. Si no aparece, `""`. |
| `sector` | EXACTAMENTE uno de la lista de sectores (ver abajo). Aplica a los tres tipos. |
| `jur` | EXACTAMENTE una de la lista de jurisdicciones (ver abajo). Aplica a los tres tipos. |
| `tema` | Etiqueta temática corta (1–3 palabras) que describe de qué trata. Libre — ver "Tema" abajo. |
| `org` | Organismo / cámara de origen. Ver "Campos por tipo" para la regla exacta según `tipo`. |
| `giro` | SOLO para `proyecto_ley`: comisión/es a las que fue girado (el "giro a comisión"), tal como aparece en el snippet, ej. `"Legislación General"`. Si hay varias, separadas por `; `. Si no aparece, `""`. La app lo usa para armar el Estado del Master Tracker. Para `norma`/`resumen_sesion`, siempre `""` (no tienen ese ciclo). |
| `autor` | SOLO para `proyecto_ley`: autor/es del proyecto. Para `norma`/`resumen_sesion`, siempre `""`. |
| `title` | Título descriptivo, **máximo 150 caracteres**. Resumí si hace falta. |
| `resumen` | El resumen/texto **COPIADO TEXTUAL** del snippet — ver regla abajo. |
| `linkExpediente` | URL a la ficha/expediente parlamentario si aparece. Si no, `""`. |
| `linkTexto` | URL al PDF / texto completo / link de referencia si aparece. Si no, `""`. |
| `fecha` | `"DD/MM/YYYY"`. Ver regla de fecha abajo. |

### Cómo identificar el tipo

- **`proyecto_ley`**: cualquier ítem sobre un proyecto de ley en cualquier etapa de su ciclo
  (presentado, asignado a comisión, giro, orden del día, dictamen, media sanción, sanción/rechazo,
  imprenta, publicación en B.O. de una LEY sancionada, solicitud de retiro, etc. — todos los tags
  de la "Guía de redacción Daily Snippet" del equipo). Es el tipo por default si no encaja con los
  otros dos.
- **`norma`**: el tag es `NORMA PUBLICADA (DECRETO)`, `NORMA PUBLICADA (RESOLUCIÓN)` o `NORMA
  PUBLICADA (DISPOSICIÓN)`. Es un decreto/resolución/disposición del Poder Ejecutivo — **nunca
  pasó por el Congreso**, no confundir con `PUBLICACIÓN B.O.` de una ley sancionada (eso sigue
  siendo `proyecto_ley`).
- **`resumen_sesion`**: el tag es `RESUMEN DE SESIÓN | <organismo>`. Es un resumen narrativo
  extenso de una sesión o reunión de comisión (con intervenciones de varios legisladores), no un
  movimiento corto de estado. Puede mencionar un proyecto de ley por su número — copialo en `num`
  tal cual aparece (ver abajo), pero **no** intentes vincularlo al proyecto real: no hay cruce
  automático, son ítems independientes.

### Campos por tipo

**`norma`** (decretos/resoluciones/disposiciones del Boletín Oficial):
- `num`: número de la norma, ej. `"829/2026"`.
- `org`: si es `NORMA PUBLICADA (DECRETO)` → SIEMPRE `"Poder Ejecutivo Nacional"` (un decreto lo
  firma el PEN, no varía). Si es `RESOLUCIÓN` o `DISPOSICIÓN` → el organismo específico tal cual
  aparece (ej. `"Ministerio de Justicia"`, `"INASE"`, `"ARCA"`). **No** uses un tema o rubro
  (ej. "Combustibles") como si fuera el organismo — si el snippet no da el organismo real para una
  resolución/disposición, dejalo en `""` y avisá al final, fuera del JSON.
- `giro`, `autor`: siempre `""`.
- `title`: descripción corta de qué hace la norma (no repitas el tag `NORMA PUBLICADA...`).
- `resumen`: el texto completo tal cual viene en el snippet.

**`resumen_sesion`** (resúmenes largos de sesión/comisión):
- `num`: el/los número/s de expediente o proyecto de ley **mencionados en el texto** (ej.
  `"S-702/26"`), separados por `; ` si son varios. Es solo referencia textual — no genera ningún
  vínculo automático con ese proyecto en la app. Si no se menciona ninguno, `""`.
- `org`: el organismo/comisión(es) + cámara, tal cual aparece (puede ser largo).
- `giro`, `autor`: siempre `""`.
- `resumen`: el texto narrativo completo, sin resumir ni acortar (misma regla que para
  `proyecto_ley` — ver "Resumen textual" abajo).

### Resumen textual (IMPORTANTE)

`resumen` NO es un resumen tuyo: es el texto del proyecto **tal cual viene en el snippet**,
palabra por palabra. No lo acortes, no lo parafrasees, no lo "mejores", no le cambies el orden.
El equipo necesita leer exactamente lo que reportó el snippet.

Únicos retoques permitidos:
- Sacar el arranque "tiene por objeto" / "El proyecto de ley tiene por objeto" si está (la app
  ya antepone esa frase al mostrarlo; si la dejás, saldría duplicada). El resto queda igual,
  empezando por el verbo/sustantivo que sigue (ej. "Regular el uso de…").
- Limpiar saltos de línea o espacios dobles del copiado.

Si la entrada del snippet trae varias oraciones para un proyecto, van TODAS en `resumen`.

### Links embebidos (IMPORTANTE)

En el Daily Legal Snippet los links suelen estar como **hipervínculos** detrás de un texto
(ej. la palabra "Texto", "PDF", "Expediente", el número de expediente, o un "ver"), **no** como
una URL escrita en el cuerpo. Tenés que capturar esas URLs igual:

- Si te pasan el **archivo** (`.docx` / `.eml` / HTML), extraé el **destino de cada hipervínculo**
  y asignalo al proyecto correspondiente (el que está en ese mismo párrafo / entrada).
- Un PDF del texto del proyecto (típicamente termina en `.pdf`, ej. dominios `hcdn.gob.ar`,
  `senado.gob.ar`, intranets provinciales) → `linkTexto`.
- Una ficha de seguimiento del trámite parlamentario → `linkExpediente`.
- Si solo te pasan **texto plano** (copiado/pegado), lo más probable es que las URLs se hayan
  perdido en el copiado: en ese caso dejá los links en `""` y **avisá** al final, fuera del JSON,
  que conviene volver a generar adjuntando el archivo original para conservar los links.

> Recomendación para quien carga: **adjuntá el `.docx` o `.eml` original** (no copies y pegues el
> texto), así los hipervínculos viajan y los proyectos quedan con su link al texto.

### Valores válidos de `sector` (elegí uno)
`Coyuntura general`, `Consumo masivo`, `Medios de pago`, `Tecnologia`, `Automotriz`,
`Agroindustria`, `Petroquimicos`, `Mineria`, `Turismo`, `Salud`, `Ambiente`

> Sin tildes (es "Tecnologia", "Petroquimicos", "Mineria"), tal cual figuran. Elegí el sector
> temático que mejor describe el proyecto. Si ninguno encaja con claridad, usá `Coyuntura general`.

### Valores válidos de `jur` (elegí uno)
`Nacional`, `CABA`, `Buenos Aires`, `Catamarca`, `Chaco`, `Chubut`, `Cordoba`, `Corrientes`,
`Entre Rios`, `Formosa`, `Jujuy`, `La Pampa`, `La Rioja`, `Mendoza`, `Misiones`, `Neuquen`,
`Rio Negro`, `Salta`, `San Juan`, `San Luis`, `Santa Cruz`, `Santa Fe`, `Santiago del Estero`,
`Tierra del Fuego`, `Tucuman`, `Municipal`, `Chile`, `Uruguay`, `Paraguay`

> Sin tildes y con la grafía exacta de la lista (es "Cordoba", "Neuquen", "Rio Negro", "Tucuman",
> "Entre Rios"). Si el ítem es del Congreso de la Nación (o del PEN, para una `norma`) → `Nacional`
> (la app lo muestra como "Argentina", no hace falta escribirlo así). Si es de una legislatura
> provincial → la provincia. Si es de un concejo deliberante municipal → `Municipal`. Si es de
> Chile, Uruguay o Paraguay → el país correspondiente (son valores planos, sin desagregación
> subnacional). Si no podés determinarla, usá `Nacional` (es lo más frecuente).

### Tema (libre)

`tema` es una etiqueta corta (1–3 palabras) que resume el asunto del proyecto. Es la única
metadata que ve el manager en la vista colapsada de la app, así que tiene que ser precisa y útil.

- Primero fijate si encaja con alguno de estos temas recurrentes (usalos tal cual para mantener
  consistencia): Datos personales, Inteligencia artificial, Grooming, Plataformas digitales,
  Derechos laborales, Redes sociales, Educación, Impuestos, Ambiental, Medicamentos,
  Biocombustibles, Litio, Semillas, Turismo, Lobby, Salud mental, Restricciones comerciales.
- Si **ninguno** encaja, **creá un tema nuevo y conciso** que describa bien el proyecto
  (ej. "Agroquímicos", "Drones", "Etiquetado de alimentos", "Código Penal", "Cannabis").
  Tenés libertad para nombrar temas fuera de la lista cuando haga falta.
- **Nunca** uses "Otro", "Varios", "General" ni dejes el campo vacío. Siempre poné un tema real.

### Regla de fecha
- Si el proyecto trae su propia fecha (de presentación/ingreso), usá esa.
- Si no, usá la fecha del snippet (suele estar en el asunto o encabezado del email/newsletter).
- Si no hay ninguna, usá la fecha de hoy.
- Siempre en formato `DD/MM/YYYY` (ej. `02/06/2026`).

## Reglas importantes

- **No inventes datos.** Si un dato no está, usá `""` (para `num`, `autor`, `org`, links). `tipo`,
  `sector` y `jur` sí o sí tienen que tener un valor de las listas — elegí el más razonable.
- **No inventes links.** Solo poné URLs que estén textualmente en el snippet.
- Mantené el contenido fiel al snippet; no agregues análisis ni opinión.
- Si el snippet no tiene ningún ítem reconocible, devolvé un array vacío: `[]`.
- Es normal que un snippet tenga muchos ítems (10, 20 o más), mezclando los tres tipos. Extraelos
  todos, en un solo array (no los agrupes ni los separes por tipo).

## Ejemplo

**Entrada (fragmento de snippet):**
```
Daily Legal Snippet — 02/06/2026

Cámara de Diputados de la Nación
Expte. 1234-D-2026 — Diputada Pérez, Juana
Proyecto de ley que tiene por objeto regular el uso de inteligencia artificial en el sector
público. Establece un registro de sistemas de IA utilizados por organismos estatales y crea
una autoridad de aplicación con facultades de auditoría.
Texto: https://hcdn.gob.ar/expedientes/1234-D-2026.pdf

Legislatura de Córdoba
Expte. 567-L-2026 — Bloque Frente Cívico
Crea un régimen provincial de promoción de biocombustibles.

NORMA PUBLICADA (DECRETO) | Poder Ejecutivo Nacional
Decreto 829/2026 - Combustibles: modifica el Decreto N° 617/25 para postergar hasta el 1° de
octubre de 2026 la aplicación de los incrementos remanentes en los montos del impuesto sobre
los combustibles líquidos.
https://www.boletinoficial.gob.ar/detalleAviso/primera/346556/20260831
```

**Salida:**
```json
[
  {
    "tipo": "proyecto_ley",
    "num": "1234-D-2026",
    "sector": "Tecnologia",
    "jur": "Nacional",
    "tema": "Inteligencia artificial",
    "org": "Cámara de Diputados de la Nación",
    "giro": "",
    "autor": "Diputada Pérez, Juana",
    "title": "Regulación del uso de inteligencia artificial en el sector público",
    "resumen": "Regular el uso de inteligencia artificial en el sector público. Establece un registro de sistemas de IA utilizados por organismos estatales y crea una autoridad de aplicación con facultades de auditoría.",
    "linkExpediente": "",
    "linkTexto": "https://hcdn.gob.ar/expedientes/1234-D-2026.pdf",
    "fecha": "02/06/2026"
  },
  {
    "tipo": "proyecto_ley",
    "num": "567-L-2026",
    "sector": "Petroquimicos",
    "jur": "Cordoba",
    "tema": "Biocombustibles",
    "org": "Legislatura de Córdoba",
    "giro": "",
    "autor": "Bloque Frente Cívico",
    "title": "Régimen provincial de promoción de biocombustibles",
    "resumen": "Crea un régimen provincial de promoción de biocombustibles.",
    "linkExpediente": "",
    "linkTexto": "",
    "fecha": "02/06/2026"
  },
  {
    "tipo": "norma",
    "num": "829/2026",
    "sector": "Petroquimicos",
    "jur": "Nacional",
    "tema": "Combustibles",
    "org": "Poder Ejecutivo Nacional",
    "giro": "",
    "autor": "",
    "title": "Postergación de aumento del impuesto a los combustibles líquidos",
    "resumen": "Modifica el Decreto N° 617/25 para postergar hasta el 1° de octubre de 2026 la aplicación de los incrementos remanentes en los montos del impuesto sobre los combustibles líquidos.",
    "linkExpediente": "",
    "linkTexto": "https://www.boletinoficial.gob.ar/detalleAviso/primera/346556/20260831",
    "fecha": "02/06/2026"
  }
]
```

Notá: el `resumen` es **copia textual** del snippet (solo se le saca el "tiene por objeto" del
arranque en `proyecto_ley`; el resto queda palabra por palabra, con todas sus oraciones);
`tipo`/`sector`/`jur` salen de las listas con la grafía exacta; `giro`/`autor` van `""` fuera de
`proyecto_ley`; los campos sin dato van como `""`; y la salida es solo el bloque JSON.
