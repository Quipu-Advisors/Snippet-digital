---
name: bora-snippet
description: >-
  Convierte el resumen normativo diario del Boletín Oficial argentino (mail de Erika, ~6am,
  lista sin organizar de decretos/resoluciones/disposiciones) en texto organizado por sector con
  el tag `NORMA PUBLICADA (...)`, en el mismo estilo que el Daily Legal Snippet, listo para que
  Erika lo pegue en el Word/mail combinado antes de que Camila lo envíe al mediodía. Usar SIEMPRE
  que el usuario pegue o adjunte el mail/resumen diario del Boletín Oficial (decretos,
  resoluciones, disposiciones) y pida "organizar el BO por sector", "armar el BORA-snippet",
  "clasificar el Boletín Oficial de hoy", o convertir esa lista al formato del snippet. No
  produce JSON — para eso, una vez que el texto ya está en el Word combinado, se usa la skill
  `snippet-digital-json`.
---

# BORA-snippet — organizar el Boletín Oficial por sector

Esta skill toma el **resumen normativo diario del Boletín Oficial** (la lista de decretos,
resoluciones y disposiciones que Erika manda por mail cada mañana, sin organizar) y devuelve
**texto plano organizado por sector**, con el mismo tag y estilo que ya usa el equipo para el
Daily Legal Snippet — para que Erika lo pegue en el Word/mail combinado antes de que Camila lo
mande al mediodía.

**No devuelve JSON.** El JSON para la app lo genera después la skill `snippet-digital-json`,
cuando procese el Word combinado completo (proyectos de ley + esto). Esta skill es un paso
intermedio: reorganiza y clasifica, no carga nada a ningún lado.

## Por qué existe

El mail del Boletín Oficial trae las normas del día en una lista simple, sin clasificar por
sector ni con el formato del snippet. Esta skill hace ese trabajo de reorganización — clasificar
cada norma en un sector y darle el formato/tag correcto — para que Erika no tenga que hacerlo a
mano todos los días. **La clasificación por sector es un primer corte automático, no definitivo:
Erika revisa el resultado y mueve lo que no encaje antes de pegarlo.**

## Qué hacer

1. Leé cada norma del mail: decretos, resoluciones (incluye "Resolución General") y
   disposiciones. Ignorá lo que no sea una norma (saludos, firma del mail, etc.).
2. Para cada una, identificá:
   - **Tipo**: Decreto / Resolución (o Resolución General) / Disposición.
   - **Número**, ej. `829/2026`.
   - **Organismo**: si es un **Decreto**, siempre `Poder Ejecutivo Nacional` (confirmado: en
     Argentina un decreto lo firma el PEN, no varía según el ministerio que lo refrenda). Si es
     **Resolución** o **Disposición**, el organismo específico tal cual aparece en el mail (ej.
     "Ministerio de Justicia", "INASE", "ARCA") — **no** un tema o rubro (ej. "Combustibles") en
     su lugar.
   - **Resumen**: el texto descriptivo tal cual viene, sin acortar ni parafrasear.
   - **Link**: la URL al detalle del Boletín Oficial (viene como hipervínculo detrás del número
     de la norma — ver "Links embebidos" abajo).
3. Clasificá cada norma en **un sector** de la lista fija (ver abajo). Es la parte donde más
   conviene tener margen de error — elegí el que mejor encaje, y dejá claro en tu respuesta (fuera
   del texto formateado) cuáles te generaron duda, para que Erika las revise primero.
4. Agrupá la salida por sector, en el mismo formato que usa el Daily Legal Snippet: encabezado de
   sector, sub-encabezado "NACIONAL" (el Boletín Oficial nacional siempre cae ahí), y debajo cada
   norma con su tag.

## Links embebidos (IMPORTANTE)

En el mail del BO los links están como **hipervínculos** detrás del número de la norma (ej.
"Decreto 829/2026"), no como una URL escrita en el cuerpo. Necesitás el **archivo** (`.eml` o el
mail reenviado con formato) para capturarlos — si te pasan solo texto plano copiado/pegado, los
links se pierden. En ese caso generá el texto igual, dejá los links afuera, y **avisá al final**
que conviene volver a generar adjuntando el mail original para no perder los links.

> Recomendación para Erika: **adjuntá el `.eml` del mail** (no copies y pegues el texto), así los
> links viajan.

## Formato de salida

Texto plano (no JSON, no bloque de código) — un bloque por sector, en este orden y formato exacto:

```
<Nombre del sector>
NACIONAL

NORMA PUBLICADA (DECRETO) | Poder Ejecutivo Nacional
Decreto <número> (<link>): <resumen tal cual, sin acortar>.

NORMA PUBLICADA (RESOLUCIÓN) | <organismo>
Resolución <número> (<link>): <resumen tal cual>.

<Nombre del siguiente sector>
NACIONAL

...
```

- Un salto de línea en blanco entre normas, y entre el sub-encabezado "NACIONAL" y la primera
  norma.
- Solo incluí sectores que tengan al menos una norma ese día — no listes sectores vacíos.
- Si el link no está disponible (ver arriba), escribí el número de la norma sin paréntesis ni
  link: `Decreto 829/2026: ...`.
- Al final, **fuera** del texto formateado, agregá una lista corta de las normas cuya
  clasificación de sector te generó duda, para que Erika las revise primero.

### Valores válidos de sector (elegí uno por norma)
`Coyuntura general`, `Consumo masivo`, `Medios de pago`, `Tecnologia`, `Automotriz`,
`Agroindustria`, `Petroquimicos`, `Mineria`, `Turismo`, `Salud`, `Ambiente`

> Sin tildes, grafía exacta ("Tecnologia", "Petroquimicos", "Mineria"). Si ninguno encaja con
> claridad, usá `Coyuntura general` y marcalo en la lista de dudas.

## Reglas importantes

- **No inventes organismo.** Si una resolución/disposición no trae el organismo real en el mail,
  dejá `<organismo>` como venga (aunque sea genérico) — no lo reemplaces por un tema o rubro.
- **No inventes links.** Solo los que estén textualmente en el `.eml`.
- **No resumas ni parafrasees** el texto de cada norma — copialo tal cual.
- Es normal que el mail tenga varias normas (5, 10 o más) — clasificalas todas.

## Ejemplo

**Entrada (fragmento del mail, adjuntando el `.eml`):**
```
Decretos
- Decreto 829/2026 (link) - Combustibles: modifica el Decreto N° 617/25 para postergar hasta el
  1° de octubre de 2026 la aplicación de los incrementos remanentes en los montos del impuesto
  sobre los combustibles líquidos.

Resoluciones
- Resolución General 5891/2026 (link) - ARCA: modifica la Resolución General N° 1.921 para
  establecer un procedimiento unificado para la modificación y rehabilitación de las
  destinaciones de exportación mediante el Sistema Informático de Trámites Aduaneros (SITA).
```

**Salida:**
```
Petroquimicos
NACIONAL

NORMA PUBLICADA (DECRETO) | Poder Ejecutivo Nacional
Decreto 829/2026 (https://www.boletinoficial.gob.ar/detalleAviso/primera/346556/20260831): modifica el Decreto N° 617/25 para postergar hasta el 1° de octubre de 2026 la aplicación de los incrementos remanentes en los montos del impuesto sobre los combustibles líquidos.

Coyuntura general
NACIONAL

NORMA PUBLICADA (RESOLUCIÓN) | ARCA
Resolución General 5891/2026 (https://www.boletinoficial.gob.ar/detalleAviso/primera/346596/20260831): modifica la Resolución General N° 1.921 para establecer un procedimiento unificado para la modificación y rehabilitación de las destinaciones de exportación mediante el Sistema Informático de Trámites Aduaneros (SITA).
```

*Dudas de clasificación: "Resolución General 5891/2026" (ARCA, trámites aduaneros de
exportación) la puse en Coyuntura general por no encajar claramente en otro sector — revisar si
corresponde a otro.*
