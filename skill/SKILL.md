---
name: snippet-digital-json
description: >-
  Convierte un Daily Legal Snippet (newsletter de monitoreo legislativo argentino) en un array
  JSON de proyectos de ley listo para pegar en la app interna "Snippet Digital" (campo "Importar
  proyectos (JSON)"). Usar SIEMPRE que el usuario pegue el contenido de un snippet o newsletter
  legislativo, adjunte el .eml del Daily Legal Snippet, o pida "extraer proyectos para Snippet
  Digital", "armar el JSON del snippet", "generar el JSON del snippet", "pasar el snippet a JSON",
  "procesar el snippet del día", o quiera convertir novedades legislativas argentinas al formato de
  Snippet Digital. También activar cuando haya un .eml o texto de newsletter legislativo y se
  mencione Snippet Digital, importar proyectos o cargar los proyectos del día. Funciona desde
  claude.ai, la app de Claude y el plugin de Office/Word.
---

# Snippet Digital — extractor a JSON

Esta skill toma un **Daily Legal Snippet** (el newsletter de monitoreo legislativo argentino de
Quipu, en texto plano, HTML, o el contenido de un `.eml`) y devuelve un **array JSON** con un
objeto por proyecto de ley, en el formato exacto que importa la app Snippet Digital.

El usuario va a copiar tu salida y pegarla tal cual en el campo **"Importar proyectos (JSON)"**.
Por eso el formato tiene que ser exacto: la app es estricta con los nombres de campos y con los
valores de `sector` y `jur`.

## Qué hacer

1. Leé todo el snippet e identificá **cada proyecto de ley / expediente** mencionado. Ignorá lo que
   no sea un proyecto: saludos, índices, encabezados del email, pies de página, publicidad.
2. Para cada proyecto, armá un objeto con los campos de abajo.
3. Devolvé un único array JSON con todos los proyectos.

## Formato de salida

Devolvé **solo** un bloque de código ```json con el array — sin texto antes ni después, sin
comentarios, sin explicaciones. El usuario necesita copiar y pegar; cualquier texto extra le
ensucia el copiado. (La app igual tolera el bloque ```json y lo limpia sola.)

Cada objeto tiene EXACTAMENTE estos campos, siempre todos presentes:

| Campo | Regla |
|---|---|
| `num` | Número de expediente, ej. `"1234-D-2026"`. Si no aparece, `""`. |
| `sector` | EXACTAMENTE uno de la lista de sectores (ver abajo). |
| `jur` | EXACTAMENTE una de la lista de jurisdicciones (ver abajo). |
| `tema` | Etiqueta temática corta (1–3 palabras) que describe de qué trata. Libre — ver "Tema" abajo. |
| `org` | Organismo / cámara de origen, ej. `"Cámara de Diputados de la Nación"`. |
| `giro` | Comisión/es a las que fue girado el proyecto (el "giro a comisión"), tal como aparece en el snippet, ej. `"Legislación General"`. Si hay varias, separadas por `; `. Si no aparece, `""`. La app lo usa para armar el Estado del Master Tracker. |
| `autor` | Autor/es del proyecto. Si no aparece, `""`. |
| `title` | Título descriptivo, **máximo 150 caracteres**. Resumí si hace falta. |
| `resumen` | El objeto del proyecto, **SIN** arrancar con "tiene por objeto". Empezá directo por el verbo/sustantivo (ej. "Regular el uso de…", "Crear un régimen de…"). |
| `linkExpediente` | URL a la ficha/expediente parlamentario si aparece. Si no, `""`. |
| `linkTexto` | URL al PDF / texto completo del proyecto si aparece. Si no, `""`. |
| `fecha` | `"DD/MM/YYYY"`. Ver regla de fecha abajo. |

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
`Tierra del Fuego`, `Tucuman`, `Municipal`

> Sin tildes y con la grafía exacta de la lista (es "Cordoba", "Neuquen", "Rio Negro", "Tucuman",
> "Entre Rios"). Si el proyecto es del Congreso de la Nación → `Nacional`. Si es de una legislatura
> provincial → la provincia. Si es de un concejo deliberante municipal → `Municipal`. Si no podés
> determinarla, usá `Nacional` (es lo más frecuente).

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

- **No inventes datos.** Si un dato no está, usá `""` (para `num`, `autor`, links). `sector` y `jur`
  sí o sí tienen que tener un valor de las listas — elegí el más razonable.
- **No inventes links.** Solo poné URLs que estén textualmente en el snippet.
- Mantené el contenido fiel al snippet; no agregues análisis ni opinión.
- Si el snippet no tiene ningún proyecto, devolvé un array vacío: `[]`.
- Es normal que un snippet tenga muchos proyectos (10, 20 o más). Extraelos todos.

## Ejemplo

**Entrada (fragmento de snippet):**
```
Daily Legal Snippet — 02/06/2026

Cámara de Diputados de la Nación
Expte. 1234-D-2026 — Diputada Pérez, Juana
Proyecto de ley que tiene por objeto regular el uso de inteligencia artificial en el sector público.
Texto: https://hcdn.gob.ar/expedientes/1234-D-2026.pdf

Legislatura de Córdoba
Expte. 567-L-2026 — Bloque Frente Cívico
Crea un régimen provincial de promoción de biocombustibles.
```

**Salida:**
```json
[
  {
    "num": "1234-D-2026",
    "sector": "Tecnologia",
    "jur": "Nacional",
    "tema": "Inteligencia artificial",
    "org": "Cámara de Diputados de la Nación",
    "autor": "Diputada Pérez, Juana",
    "title": "Regulación del uso de inteligencia artificial en el sector público",
    "resumen": "Regular el uso de inteligencia artificial en el sector público.",
    "linkExpediente": "",
    "linkTexto": "https://hcdn.gob.ar/expedientes/1234-D-2026.pdf",
    "fecha": "02/06/2026"
  },
  {
    "num": "567-L-2026",
    "sector": "Petroquimicos",
    "jur": "Cordoba",
    "tema": "Biocombustibles",
    "org": "Legislatura de Córdoba",
    "autor": "Bloque Frente Cívico",
    "title": "Régimen provincial de promoción de biocombustibles",
    "resumen": "Crear un régimen provincial de promoción de biocombustibles.",
    "linkExpediente": "",
    "linkTexto": "",
    "fecha": "02/06/2026"
  }
]
```

Notá: el `resumen` no arranca con "tiene por objeto"; `sector`/`jur` salen de las listas con la
grafía exacta; los campos sin dato van como `""`; y la salida es solo el bloque JSON.
