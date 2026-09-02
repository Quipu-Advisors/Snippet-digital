---
name: whatsapp-resumen-sesion
description: >-
  Convierte una alerta informativa de WhatsApp (resumen de sesión o reunión de comisión del
  Congreso, con formato Título/Fecha/Organismo/Link/Resumen) en un array JSON de un solo ítem
  tipo `resumen_sesion`, listo para pegar en la app interna "Snippet Digital" (campo "Importar
  proyectos (JSON)"). Usar SIEMPRE que el usuario pegue el texto de una "Alerta informativa"
  enviada por WhatsApp sobre una sesión o reunión de comisión, o pida "convertir esta alerta de
  WhatsApp", "pasar el resumen de sesión a JSON", "cargar este resumen a Snippet Digital". No usar
  para el Daily Legal Snippet (newsletter diario) ni para normas del Boletín Oficial — para eso
  está la skill `snippet-digital-json`.
---

# WhatsApp — resumen de sesión a JSON

Esta skill toma el texto de una **alerta informativa de WhatsApp** sobre una sesión o reunión de
comisión del Congreso (Argentina u otro país cubierto) y devuelve un **array JSON de un solo
objeto**, `tipo: "resumen_sesion"`, en el mismo formato que usa la app Snippet Digital.

El usuario va a copiar tu salida y pegarla tal cual en el campo **"Importar proyectos (JSON)"**
de `admin.html` (pestaña "Cargar proyectos"), junto con lo que venga del Daily Legal Snippet — la
app no distingue el origen, solo el campo `tipo`.

## Formato de entrada esperado

La alerta suele tener esta forma (con o sin el encabezado gráfico "Alerta informativa"):

```
[Título de la novedad]

[DD de mes, AAAA]

Organismo: [comisión/es + cámara]

Link: [URL]

Resumen: [texto narrativo largo, puede citar a varios legisladores]
```

No siempre viene perfectamente rotulado — a veces el bloque "Organismo:"/"Link:"/"Resumen:" está,
a veces hay que inferir cuál línea es cuál por posición (título arriba, fecha después, resumen al
final). Usá criterio.

## Qué hacer

1. Identificá título, fecha, organismo, link y el resumen narrativo completo.
2. Buscá en el texto del resumen cualquier mención a un **número de expediente o proyecto de ley**
   (ej. "S-702/26", "4336-D-2023"). Copialos tal cual a `num`, separados por `; ` si hay varios.
   **No** hagas ninguna otra cosa con ese número — no lo valides, no lo busques, no intentes
   vincularlo a nada: es solo referencia textual para quien lea la tarjeta.
3. Elegí `sector` de la lista fija de abajo (la misma que usa el Daily Legal Snippet — no viene en
   el texto de la alerta, hay que inferirlo del contenido).
4. Elegí `jur` de la lista fija de abajo.
5. Devolvé el array JSON de un solo objeto — nada de texto antes ni después.

## Formato de salida

Devolvé **solo** un bloque ```json con un array de un objeto (o varios, si pegaron más de una
alerta junta) — sin comentarios ni explicaciones.

| Campo | Regla |
|---|---|
| `tipo` | SIEMPRE `"resumen_sesion"`. |
| `num` | Expediente(s)/proyecto(s) de ley mencionados en el resumen, separados por `; `. Si no se menciona ninguno, `""`. |
| `sector` | EXACTAMENTE uno de: `Coyuntura general`, `Consumo masivo`, `Medios de pago`, `Tecnologia`, `Automotriz`, `Agroindustria`, `Petroquimicos`, `Mineria`, `Turismo`, `Salud`, `Ambiente`. Si ninguno encaja con claridad, `Coyuntura general`. |
| `jur` | EXACTAMENTE una de: `Nacional`, `CABA`, `Buenos Aires`, `Catamarca`, `Chaco`, `Chubut`, `Cordoba`, `Corrientes`, `Entre Rios`, `Formosa`, `Jujuy`, `La Pampa`, `La Rioja`, `Mendoza`, `Misiones`, `Neuquen`, `Rio Negro`, `Salta`, `San Juan`, `San Luis`, `Santa Cruz`, `Santa Fe`, `Santiago del Estero`, `Tierra del Fuego`, `Tucuman`, `Municipal`, `Chile`, `Uruguay`, `Paraguay`. Si es del Congreso de la Nación → `Nacional`. Si no podés determinarla, `Nacional`. |
| `tema` | Etiqueta corta (1–3 palabras) del asunto tratado en la sesión. Nunca "Otro"/"Varios"/vacío. |
| `org` | El organismo/comisión(es) + cámara, tal cual aparece (puede ser un texto largo con varias comisiones). |
| `giro` | Siempre `""` (no aplica a este tipo). |
| `autor` | Siempre `""` (no aplica a este tipo). |
| `title` | El título de la alerta, máximo 150 caracteres. |
| `resumen` | El texto narrativo **COPIADO TEXTUAL**, completo — no lo acortes ni lo parafrasees. |
| `linkExpediente` | Siempre `""`. |
| `linkTexto` | El link de la alerta (video/registro de la sesión), si aparece. Si no, `""`. |
| `fecha` | `"DD/MM/YYYY"` — la fecha de la SESIÓN (la que trae la alerta), no la de hoy. |

## Reglas importantes

- **No inventes datos.** Lo que no esté en el texto va `""`.
- **No vincules el/los expediente/s de `num` a nada** — es solo texto de referencia, la app no
  hace ningún cruce automático con eso.
- Este tipo **no lleva calificación de impacto por cliente** en la app — es puramente
  informativo. No hace falta que el `resumen` incluya ningún juicio de relevancia para clientes.
- Mantené el contenido fiel al texto original; no agregues análisis ni opinión propia.

## Ejemplo

**Entrada:**
```
El Senado inició el tratamiento del proyecto para el Régimen de Incentivo para Inversiones
Relevantes

26 de agosto, 2026

Organismo: Comisiones de Presupuesto y Hacienda, Legislación General y Economías Regionales,
Economía Social, Micro, Pequeña y Mediana Empresa, Senado de la Nación

Link: https://www.youtube.com/watch?v=lu2aXPqxn0U

Resumen: El día de ayer, las comisiones de Presupuesto y Hacienda, Legislación General y
Economías Regionales, Economía Social, Micro, Pequeña y Mediana Empresa llevaron a cabo una
reunión plenaria para debatir sobre el proyecto S-702/26, presentado por el senador Eduardo
Vischi (Corrientes, UCR). El proyecto crea el Régimen de Incentivos para Inversiones Relevantes
(RIIR)... [resto del resumen]
```

**Salida:**
```json
[
  {
    "tipo": "resumen_sesion",
    "num": "S-702/26",
    "sector": "Coyuntura general",
    "jur": "Nacional",
    "tema": "Incentivos a la inversión",
    "org": "Comisiones de Presupuesto y Hacienda, Legislación General y Economías Regionales, Economía Social, Micro, Pequeña y Mediana Empresa, Senado de la Nación",
    "giro": "",
    "autor": "",
    "title": "El Senado inició el tratamiento del proyecto para el Régimen de Incentivo para Inversiones Relevantes",
    "resumen": "El día de ayer, las comisiones de Presupuesto y Hacienda, Legislación General y Economías Regionales, Economía Social, Micro, Pequeña y Mediana Empresa llevaron a cabo una reunión plenaria para debatir sobre el proyecto S-702/26, presentado por el senador Eduardo Vischi (Corrientes, UCR). El proyecto crea el Régimen de Incentivos para Inversiones Relevantes (RIIR)... [resto del resumen]",
    "linkExpediente": "",
    "linkTexto": "https://www.youtube.com/watch?v=lu2aXPqxn0U",
    "fecha": "26/08/2026"
  }
]
```
