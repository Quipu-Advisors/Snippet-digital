# Smart Snippet — Guía del proyecto (handoff)

App interna de Quipu Advisors para que el equipo revise y califique los proyectos de ley del
monitoreo legislativo diario. Reemplaza al "Daily Legal Snippet" por correo (un bodoque de texto)
por una lista limpia y escaneable.

> **Para quien lo mantiene (Camila / Research):** esto está pensado para ajustarse sobre la marcha
> con **Claude Code**. Abrí esta carpeta en Claude Code y pedile el cambio en lenguaje natural
> ("cambiá la contraseña", "agregá un cliente", "sacá a tal persona"). Esta guía le da (y te da)
> todo el contexto. Casi todos los cambios comunes son una línea — están listados abajo.

---

## Principio de diseño (no romper)

El objetivo es **bajar la carga visual** del que revisa. Por eso:
- Lo más **minimalista** posible, con mucho espacio en blanco.
- **Mínima info a primera vista**: la tarjeta colapsada muestra solo el título + el tema. Todo el
  detalle (resumen, organismo, autor, links, calificación) aparece al **hacer click** para expandir.
- Color **solo cuando aporta** (impactos Alto/Medio/Bajo, "Nuevo", el tema).

Cualquier cambio de UI debería respetar esto.

---

## Arquitectura (simple a propósito)

- **Un solo archivo:** `index.html` (HTML + CSS + JS, sin build, sin frameworks).
- **Íconos:** Tabler Icons vía CDN (`<link>` en el `<head>`), clase `ti ti-<nombre>`. **Ojo con la
  versión:** hasta 2026-09-04 el link apuntaba a `tabler-icons/3.31.0/iconfont/tabler-icons.min.css`
  — esa ruta **nunca existió** en cdnjs (404 silencioso, sin error visible), así que NINGÚN ícono
  se renderizó nunca, en ningún lado de la app, probablemente desde el origen del proyecto. Se
  corrigió a `tabler-icons/3.46.0/tabler-icons.min.css` (sin el segmento `/iconfont/`, cambió en
  versiones nuevas). Antes de fijar una versión nueva del CDN, verificar que la URL responda 200
  (`curl -sI <url>`) — un 404 de un `<link>` no rompe nada visible ni tira error de consola, así
  que pasa desapercibido fácil.
- **Datos:** Supabase (PostgreSQL en la nube). Proyecto ref `xyqmtqsczscdejcwusce`.
- **Hosting:** Vercel (`snippet-digital.vercel.app`), conectado al repo `Quipu-Advisors/Snippet-digital`
  — cada push a `main` redeploya solo. Migrado desde GitHub Pages.
- **Repo público de nuevo desde 2026-09-02** (revertido tras un incidente, ver abajo). No pongas
  nada sensible en el código más allá de lo ya aceptado (`TEAM_PASSWORD`, `SB_ANON`) — es lo mismo
  que ya era visible para cualquiera con la URL del sitio, público o privado.

> **Incidente 2026-09-02 (resuelto):** el repo había pasado a privado el 09-01 por una razón real
> (la clave `SB_ANON` de la base interna es visible en el código y **`selections` no tiene RLS**
> — cualquiera con la clave puede leer/escribir impactos por cliente pegándole directo a la API
> de Supabase, sin pasar por `TEAM_PASSWORD`). Pero el plan Hobby de Vercel **no deploya repos
> privados de una organización de GitHub** (solo de cuentas personales) — desde ese momento todos
> los pushes fallaron en silencio y producción quedó congelada 13 commits atrás, sirviendo
> **la contraseña vieja ya rotada por exposición** (`bachacero` en vez de `bachacero2026`) sin que
> nadie lo notara hasta que un cliente nuevo (CIQyP) no aparecía. Se decidió volver el repo a
> público para desbloquear ya (la privacidad del repo no tapaba el hueco real: lo que sirve el
> sitio siempre fue público, público o privado el repo, así funciona cualquier hosting estático).
> **Pendiente de fondo (no urgente, pero real):** agregarle RLS + funciones RPC a la base interna
> del Smart Snippet, mismo patrón que ya usa `daily-legislative-snippet` (`selections` y
> `projects` sin policies para `anon`, todo detrás de RPCs `SECURITY DEFINER` con contraseña
> hasheada). Con eso arreglado, la clave `SB_ANON` deja de ser un problema y el repo puede quedar
> público sin este riesgo de fondo.
- **Extracción del snippet → JSON:** una **skill de Claude** (no corre dentro de la app). Ver abajo.

No hay servidor propio ni proceso de build. Se edita el `index.html` y se publica.

---

## Cómo editar y publicar

**Opción A — desde GitHub (sin instalar nada):** entrá al repo → `index.html` → ícono del lápiz →
editás → **Commit changes**. Vercel redeploya en ~1 minuto.

**Opción B — con Claude Code (recomendado para cambios no triviales):** abrí esta carpeta, pedí el
cambio, Claude edita `index.html`, hace commit y `git push`. Sale publicado solo en ~1 min.
(La cuenta `lucasdmartinez` tiene permiso de push; si lo mantiene otra persona, que pida acceso de
colaborador al repo, o edite por la Opción A.)

Después de publicar, en el navegador hacé **Ctrl+F5** para saltear el cache.

---

## Cambios comunes (dónde tocar)

Todo esto está al principio del `<script>` en `index.html`, en constantes con nombre claro:

| Quiero… | Buscá / cambiá |
|---|---|
| Cambiar la **contraseña** del equipo | `var TEAM_PASSWORD = 'bachacero2026';` |
| Agregar/quitar gente de **Research** (acceso admin) | `var RESEARCH = [...]` |
| Agregar/quitar gente de **Public Affairs** (solo califican) | `var PUBLIC_AFFAIRS = [...]` |
| Agregar/quitar **clientes** (columnas de impacto) | `var CLIENTS = [...]` |
| Cambiar el **nombre que se muestra/exporta** de un cliente sin tocar sus calificaciones guardadas | `var CLIENT_LABEL = {...}` |
| Agregar/quitar **sectores** | `var SECTORES = [...]` **y** actualizar la lista en la skill |
| Agregar/quitar **provincias/jurisdicciones** | `var PROVINCIAS = [...]` |
| Agregar/quitar **países del monitoreo regional** | `var PAISES_REGIONALES = [...]` **y** actualizar `PROJ_FIELDS`/filtro en `api/sync.js` de `daily-legislative-snippet` |
| Cambiar el nombre que se muestra para una jurisdicción sin tocar el valor guardado | `var JUR_LABEL = {...}` (mismo patrón que `CLIENT_LABEL`) |
| Cambiar la **base de datos** | `var SB_URL` / `var SB_ANON` |
| Colores de marca | variables `--blue` (#395279) y `--accent` (#C0714D) en `:root` |
| Cambiar las **columnas del export a Sheets** (master tracker) | función `buildRows` + array `SHEET_IMPACT_COLS` |
| Agregar/quitar gente que ve el **Consolidado TikTok (Lark)** | `var LARK_USERS = ['Milagros']` |
| Cambiar el cliente o las columnas del **export a Lark** | `var LARK_CLIENT` y función `larkCells` |
| Cambiar los **días/hora de corte** | constantes `CUTOFF_DOWS` (0=Dom..6=Sáb; default `[2,5]`=Mar/Vie) y `CUTOFF_HOUR` (default 12) |

> **Fechas de corte:** para evitar backlog, cada proyecto debe rotularse antes del próximo corte
> (Mar/Vie 12:00 por defecto). `cutoffDeadline(_d)` calcula el deadline = primer corte
> **estrictamente posterior** al día de reporte (NO cuenta el corte del propio día de carga, si no
> lo cargado un viernes a la tarde vencería al instante). Ej.: reportado viernes → vence el martes;
> reportado martes/miércoles/jueves → vence el viernes; reportado lunes → vence el martes.
> `isOverdue(p)` marca si venció. Un proyecto vencido muestra un reloj tachado y,
> al abrirlo, los desplegables quedan deshabilitados con un botón **"Cargar fuera de fecha"** que
> rehabilita la carga y habilita **"Copiar fila para el Master Tracker"** (para que el propio AM la
> pegue). El override es por tarjeta y por sesión (`overrideLate`).

> **Borrar un ítem puntual (`adminDeleteItem`):** botón "Eliminar" en la tarjeta expandida,
> visible solo para Research (`isResearch`). Borra ESE ítem nomás — a diferencia de "Limpiar
> este día" (`clearDayProjects`, en la pestaña Cargar proyectos), que vacía el día completo. Usa
> `p._d` (la fecha que le puso `dbGetAllProjects` al cargarlo) para saber en qué fila de Supabase
> vive; si no tiene `_d` (viendo desde la pestaña Cargar proyectos, sin pasar por el histórico),
> cae a `adminDate`. Después de borrar, `renderList()` refresca — pensado para usarse desde
> "Proyectos cargados"/Consolidado, no cambia si se llama desde otro lado.

> **Vista previa antes de importar (`previewImportJson`/`confirmImportJson`):** el botón de la
> pestaña "Cargar proyectos" ya no importa directo — primero parsea y valida el JSON, y muestra
> cada ítem con la misma tarjeta que va a ver el equipo (`previewItemHTML`, sin impacto por
> cliente todavía) antes de guardar nada. Recién con "Confirmar e importar" se llama a `saveDay`.
> `pendingImport` guarda el resultado parseado entre los dos pasos; `adminChangeDate` lo limpia
> (evita confirmar contra el día equivocado si cambian la fecha a mitad de camino). Es el flujo
> normal para cualquiera con acceso a Cargar proyectos (Research), no una vista de prueba aparte.

> **`linkTextoLabel(p)`:** el campo `linkTexto` no siempre apunta a un documento — en
> `resumen_sesion` es el video/registro de la sesión (lo carga `whatsapp-resumen-sesion`). Por
> eso el link se rotula "Fuente oficial" para ese tipo y "Texto" para el resto, en vez de un
> label fijo. Se usa en `cardHTML`, `projDetail` y `previewItemHTML` — si se agrega un cuarto
> lugar que muestre ese link, usar esta función ahí también en vez de hardcodear "Texto".

> **Nombre para mostrar (`CLIENT_LABEL`):** la clave interna de un cliente en `CLIENTS` es la que
> queda guardada en Supabase en cada calificación (`localImp`/`SELS`). Si hace falta que se
> **muestre** o **exporte** distinto de esa clave (ej. Cami pidió que "Clientes afectados" en el
> Master Tracker diga "Coca-Cola" y no "Coca", porque su filtro de búsqueda necesita el texto
> exacto), NO renombres la entrada en `CLIENTS` — eso rompería el historial de calificaciones ya
> guardadas bajo la clave vieja. Agregá el mapeo en `CLIENT_LABEL` y usá `clientLabel(c)` donde
> se muestra el texto al usuario (dropdown de impacto, consolidado) o se exporta (columna
> "Clientes afectados" de `buildRows`). Las claves de `SHEET_IMPACT_COLS` no se tocan por esto:
> esas son el orden de columnas fijas del Master Tracker, no texto libre.

> **Consolidado dividido en dos tablas (nacional/provincial vs. regional):** desde 2026-09,
> Erika carga los PL de Chile/Uruguay/Paraguay al Master Tracker y Camila los de Argentina —
> antes era una sola tabla combinada y una sola persona. `renderConsolidado` arma `spNacional`
> y `spRegional` (split por `PAISES_REGIONALES.indexOf(p.jur)`) y renderiza dos `admin-card`
> independientes vía `copySheetsCard(...)`, cada una con su propio botón, preview y contador —
> si un grupo queda vacío, esa card no se muestra. `copyConsolidado(grupo)` recibe `'nacional'`
> o `'regional'` y **recalcula el split desde cero** (no depende de la vista ya renderizada), con
> la MISMA regla de impacto efectivo (`!= '-'`) que usa la vista previa — antes la función de
> copiar usaba un filtro más laxo que la preview (cualquier pid con alguna selección, sin chequear
> que el impacto mergeado fuera efectivo), así que lo copiado podía no coincidir exactamente con
> lo previsualizado; quedó unificado. Si se suma un cuarto país regional, alcanza con agregarlo a
> `PAISES_REGIONALES` — el resto de la lógica ya lo toma solo.

> **Export a Sheets:** `buildRows` arma las filas tabuladas alineadas a las 34+ columnas del
> **Master Tracker 2.0** (hoja `Sheet1`). Las columnas de impacto (desde la 19) usan su propio
> orden y nombres de la planilla (Microsoft, Amazon, Tiktok, Worldcoin, Didi…), mapeados a los
> clientes de la app en `SHEET_IMPACT_COLS`. **Al sumar un cliente nuevo (`CLIENTS` +
> `SHEET_IMPACT_COLS`, convención: agregar al final de ambos), la planilla real tiene que tener
> una columna nueva en la MISMA posición** (al final de las de impacto) antes de la primera vez
> que se pegue — si no, el valor de ese cliente cae en la columna que sea que siga en el sheet,
> no en la de él. Si cambia el orden/nombres de la planilla existente, ajustar `SHEET_IMPACT_COLS`
> para que coincida.

> **Export a Lark (TikTok):** los usuarios en `LARK_USERS` (hoy: Milagros) ven una pestaña
> **"Consolidado TikTok"** junto a su vista de revisión. Filtra por **rango de fechas de reporte**
> los proyectos con impacto TikTok vigente (misma lógica de "última corrección gana" que el
> consolidado) y arma **dos tablas** con las 17 columnas del Legislative Tracking de Lark:
> Alto+Medio (pestaña principal) y Bajo. Mapeos: Alto→High, Medio→Normal, Bajo→Low;
> Nacional→National, CABA→City of Buenos Aires, etc. (`larkJur`); organismo→Deputies/Senate/
> Provincial Deputies (`larkChamber`). Las convenciones están **alineadas a la skill de Claude
> "Lark Legislative Tracker" que usa Milagros**: fechas `YYYY-MM-DD`, Update date = día del export,
> Notes autoarmado ("Submitted to Committees | Project presented by [bloque]", partido extraído
> del autor en `larkNotes`), Topic mapeado a la lista fija de la skill (`LARK_TOPIC_MAP`; lo no
> mapeable queda en español y lo resuelve la skill). Status sale "In progress"; Likelihood, Legal
> Analysis y Contacts van vacíos. Hay **dos botones por tabla**: "Copiar para Lark" (TSV + HTML,
> el título pega con hipervínculo — texto en español) y **"Copiar para Claude (Excel en inglés)"**
> (copia las filas + un prompt que invoca la skill: traduce el resumen, pone el título temático,
> completa Likelihood y genera el Excel, SIN tocar el Business Impact que ya calificó el equipo;
> el link viaja como columna 18 para el hipervínculo del Excel). La app **recuerda el último
> "Hasta" copiado** (localStorage por usuario) y el próximo rango arranca al día siguiente, para
> no duplicar ni saltear días.

Notas:
- **Admin = pertenecer a Research.** Quien esté en `RESEARCH` ve los 3 pasos (Cargar proyectos →
  Proyectos cargados → Consolidado). Public Affairs solo ve su vista de selección.
- Si cambiás `SECTORES` o `PROVINCIAS`, actualizá también la skill (sección "Tema/valores válidos")
  para que el JSON que genera coincida.

---

## Flujo de uso diario

1. **Research carga el snippet** (paso "Cargar proyectos"):
   - Botón "Copiar prompt para Claude" → pegar en Claude **adjuntando el `.docx` del snippet**
     (no copiar/pegar el texto, si no se pierden los links). Claude devuelve un JSON.
   - Pegar ese JSON en "Importar proyectos (JSON)" → Importar. (También hay alta manual.)
   - Para reemplazar un día ya cargado: "Limpiar este día" y volver a importar (el import **suma**).
2. **Cada manager** abre la app, elige su nombre, filtra (sector/jurisdicción) y **califica** el
   impacto por cliente. Su selección y filtros quedan recordados.
3. **Research** revisa en "Consolidado" y usa **"Copiar todo para Sheets"** para volcar al master
   tracker (toma la última corrección (el valor más reciente por cliente) entre managers como definitivo).

---

## Cómo está organizada la carpeta `skill/` (leer antes de tocar cualquier skill)

Cada skill tiene DOS cosas con el mismo nombre y distinta extensión — no son duplicados, son
fuente vs. paquete:
- **Carpeta `skill/<nombre>/` → `SKILL.md`**: el fuente, en texto plano, el que se edita.
- **Archivo `skill/<nombre>.skill`**: el paquete (zip) que se sube en **Settings → Skills**. Es
  el único de los dos que se puede importar — la carpeta no sirve para subir directamente.
En Explorer se distinguen por el tipo: "Carpeta de archivos" (fuente) vs. "Archivo SKILL" (paquete).
Después de editar el `SKILL.md`, hay que re-empaquetar el `.skill` — si no, el que se sube queda
desactualizado. `snippet-to-json` es la excepción a la carpeta propia (ver abajo).

## La skill `snippet-to-json`

- Convierte el Daily Legal Snippet (Word/.eml/texto) en el JSON que importa la app: proyectos de
  ley, normas del Boletín Oficial (tag `NORMA PUBLICADA (...)`) y, si se cargan ahí, resúmenes de
  sesión (tag `RESUMEN DE SESIÓN | ...`) — emite el campo `tipo` en cada objeto (ver "Tipos de
  ítem" más arriba).
- Corre desde la cuenta de Claude (panel **Settings → Skills**). **Excepción a la convención de
  arriba:** el fuente es `skill/SKILL.md` directo (no `skill/snippet-to-json/SKILL.md` — no tiene
  carpeta propia), y el paquete es `skill/snippet-to-json.skill`. Además, por dentro el paquete
  se llama `snippet-digital-json` (nombre viejo, no se renombró) — si el panel de Skills lo lista
  como "snippet-digital-json" en vez de "snippet-to-json", es la misma skill, no hay dos.

## La skill `daily-legal-snippet` (redacción — la usa Research: Camila/Erika)

- Guía de formato para **redactar** las novedades del snippet en texto/markdown, con el tag y
  las reglas exactas de Quipu (anclaje de hipervínculos, fecha sin año, jurisdicción como un solo
  nombre, verificación de bloque/autor contra el portal oficial de cada cámara). Existía antes
  solo para proyectos de ley (presentado, giro, dictamen, media sanción, sanción, etc., incluida
  región — Chile/Uruguay/Paraguay y otros países ya tenían ejemplos). **2026-09-02: se le sumó el
  esquema 4** (Normas del Boletín Oficial — decretos/resoluciones/disposiciones, tag `NORMA
  PUBLICADA (...)`), fusionando ahí lo que antes era una skill aparte (`bora-snippet`, dada de
  baja). Cubre Argentina y región: organismo fijo "Poder Ejecutivo Nacional" solo para decretos
  argentinos (confirmado); para el resto (resoluciones/disposiciones argentinas, y CUALQUIER
  norma de Chile/Uruguay/Paraguay, decreto incluido) el organismo es el que traiga la fuente, sin
  asumir uniformidad.
- **No genera JSON** — es el paso de redacción/organización, no de carga. Cuando el pedido es
  ordenar por sector una lista sin clasificar (ej. el mail diario del Boletín Oficial), además
  clasifica cada norma en un sector y agrupa la salida (mismo estilo que el Daily Legal Snippet),
  devolviendo aparte una lista de las normas cuyo sector generó duda, para que las revisen antes
  de sumarlas al Word combinado. El JSON para la app lo genera después `snippet-digital-json`,
  cuando procese ese Word ya combinado.
- Necesita el archivo original (`.eml`/`.docx`) para conservar los hipervínculos — texto plano
  copiado los pierde. Al pegar el resultado en Word hay que copiar la respuesta **renderizada**
  de Claude (no el markdown crudo), para que el link viaje como hipervínculo real.
- Fuente y `.skill` empaquetado en `skill/daily-legal-snippet/` de este repo.

## La skill `whatsapp-resumen-sesion`

- Convierte el texto de una alerta de WhatsApp (formato Título/Fecha/Organismo/Link/Resumen,
  resumen de sesión o reunión de comisión) directo a JSON `tipo: "resumen_sesion"`, mismo
  contrato que `snippet-to-json`. Se pega en el mismo campo "Importar proyectos (JSON)" del
  admin. Si menciona un número de expediente/proyecto en el texto, lo copia a `num` como
  referencia — sin vincularlo a nada (ver "Tipos de ítem" más arriba, "Sin cruce automático").
- Fuente y `.skill` empaquetado en `skill/whatsapp-resumen-sesion/` de este repo.

## La skill `lark-legislative-tracker` (la usa Milagros)

- Cierra el pipeline de TikTok: Smart Snippet → **"Copiar para Claude"** → pegar en Claude →
  la skill traduce el resumen, pone el título temático en inglés, completa Likelihood y genera
  el **Excel listo para subir a Lark** (con hipervínculo en Bill (link)). NO toca el Business
  Impact que ya calificó el equipo. También acepta proyectos sueltos (texto/PDF/URL) como la
  skill original de Milagros, a la que reemplaza.
- Fuente y `.skill` empaquetado en `skill/lark-legislative-tracker*` de este repo. Milagros la
  importa en SU cuenta de Claude (Settings → Skills). Si se actualiza el fuente, re-empaquetar
  el zip y re-importarla en el panel.
- **Importante:** para que salgan los **links** al texto de cada proyecto, hay que **adjuntar el
  `.docx`** (los links son hipervínculos de Word; si se copia el texto plano se pierden).
- El campo `tema` lo genera Claude libremente (si no encaja en la lista, inventa uno corto). En la
  app, si el tema es vacío o "Otro", no se muestra el chip.
- Si actualizás la skill, hay que **re-pegarla/re-importarla en el panel** (el panel no se
  sincroniza con el archivo local).

---

## Tipos de ítem, ámbito y monitoreo regional (2026-09)

Además de proyectos de ley, la app ahora recibe dos fuentes nuevas: el resumen normativo del
Boletín Oficial (mail diario de Erika, ~6am) y resúmenes largos de sesión/comisión (alertas de
WhatsApp). Se suman al mismo Word/mail del Daily Legal Snippet entre las 6am y el mediodía,
clasificados por sector bajo los mismos encabezados que los proyectos de ley (la skill lee esa
organización, no la inventa — ver "La skill `snippet-digital-json`" más abajo).

- **`tipo`** (campo nuevo en cada proyecto, JSONB — no rompe datos viejos: si falta, se asume
  `proyecto_ley`): `proyecto_ley` (default, incluye TODOS los movimientos cortos de la "Guía de
  redacción Daily Snippet" — presentado, giro, dictamen, media sanción, sanción/rechazo,
  publicación en B.O. de una ley, etc.) / `norma` (decreto, resolución o disposición del Boletín
  Oficial — nunca pasó por el Congreso) / `resumen_sesion` (resumen narrativo largo de una sesión
  o reunión de comisión, típicamente de una alerta de WhatsApp).
- **Ámbito** (derivado de `tipo`, filtro nuevo en el toolbar): Legislativa (`proyecto_ley` +
  `resumen_sesion`) / Regulatoria (`norma`).
- **`giro`/`autor` solo aplican a `proyecto_ley`** — van `""` para los otros dos tipos.
  `isOverdue`/el ciclo de vencimiento (cortes Mar/Vie) tampoco aplica fuera de `proyecto_ley`.
- **`resumen_sesion` no lleva calificación de impacto por cliente** (tarjeta "Solo informativo",
  sin dropdown) y no entra al export del Master Tracker. `norma` sí lleva calificación, igual que
  un proyecto. Cuando `!llevaImpacto(p)`, `cardHTML` **no reserva la columna `.cimpact.wide`**
  (no hay nada que poner ahí) — el badge "Solo informativo" va como pill en la fila de metadata y
  el resumen ocupa el ancho completo de la tarjeta. Si se le suma contenido real a esa columna
  para algún tipo sin impacto en el futuro, revisar este atajo.
- **`tipoIconHTML(p)`:** ícono chico antepuesto al título (`.ctitle`, en `cardHTML` y en la vista
  previa de import) para distinguir el tipo sin abrir la tarjeta — `ti-messages` para
  `resumen_sesion`, `ti-gavel` para `norma`. `proyecto_ley` (el caso default/mayoritario) no
  lleva ícono, a propósito, para no sumar ruido visual al caso más común.
- **`.cresumen` usa `white-space:pre-wrap`** para que los `\n\n` que traiga el JSON se vean como
  párrafos separados (si no, un `<div>` los colapsa). Las skills `snippet-digital-json` y
  `whatsapp-resumen-sesion` tienen instrucción explícita de conservar los saltos de párrafo del
  original en `resumen` en vez de aplanarlo — antes decían "limpiar saltos de línea", lo cual
  aplastaba resúmenes largos de `resumen_sesion` en un solo bloque corrido.
- **Tags de carga** en el Word/mail (mismo estilo que "PROYECTO DE LEY PRESENTADO" de la guía):
  - `NORMA PUBLICADA (DECRETO) | Poder Ejecutivo Nacional` (organismo fijo — **solo para
    decretos argentinos**, confirmado que el PEN los firma sin variar según ministerio)
  - `NORMA PUBLICADA (RESOLUCIÓN) | <organismo específico>` / `NORMA PUBLICADA (DISPOSICIÓN) | <organismo específico>`
  - Para normas de Chile/Uruguay/Paraguay (cualquier sub-tipo, incluido decreto): **sin
    organismo fijo** — no está confirmado que el Poder Ejecutivo de esos países firme de forma
    uniforme como en Argentina, así que el organismo se toma tal cual aparece en la fuente
    (mismo criterio flexible que resolución/disposición).
  - `RESUMEN DE SESIÓN | <organismo>` (para las alertas de WhatsApp)
  - Deliberadamente distinto de `PUBLICACIÓN B.O.` de la guía (esa sigue siendo `proyecto_ley`:
    una LEY sancionada que llegó a su publicación final, no una norma del Poder Ejecutivo).
- **Monitoreo regional**: `jur` suma `Chile`/`Uruguay`/`Paraguay` como valores planos (sin
  desagregación subnacional). En el filtro de Jurisdicción, "Nacional" se muestra "Argentina"
  (`JUR_LABEL`, mismo patrón que `CLIENT_LABEL` — el valor interno no cambia), las provincias
  quedan agrupadas bajo "PROVINCIAL ARGENTINA" y Municipal bajo "MUNICIPAL ARGENTINA".
  **Este contenido regional NO se sincroniza a `daily-legislative-snippet`** (se filtra en su
  `api/sync.js`) — decisión de producto, no técnica; ver el `CLAUDE.md` de ese repo.
- **Sin cruce automático**: si un `resumen_sesion` menciona un proyecto de ley por su número, ese
  número se guarda en `num` como referencia textual (separados por `; ` si hay varios) pero la
  app **no** lo vincula a la entrada real del proyecto. Decisión consciente para no
  over-engineerear un v1 — la información para cruzarlo a mano o automatizarlo después ya queda
  guardada.
- Dos skills separadas producen el mismo contrato JSON: `snippet-digital-json` (Word/mail diario:
  proyectos de ley + normas + eventualmente resúmenes de sesión si se cargan ahí) y
  `whatsapp-resumen-sesion` (texto crudo de una alerta de WhatsApp → JSON de un solo ítem). Las
  dos se pegan en el mismo campo "Importar proyectos (JSON)" de `admin.html`.

## Modelo de datos (Supabase)

- **`projects`** `(id, date, data)`: una fila por día. `data` = array JSON de proyectos de ese día.
- **`selections`** `(id, date, manager, data, updated_at)`: **una fila por manager** (date-agnóstica;
  se usa un `date` centinela fijo `SEL_DATE='2000-01-01'`). `data` =
  `{ "<idProyecto>": { impacts: { "<cliente>": "Alto|Medio|Bajo|N/A|-" }, ts } }`. Al guardar se borra
  cualquier fila previa del manager y se reescribe una sola (`dbSaveSelectionAll`); al leer se
  fusionan todas las filas por manager (`dbGetAllSelections`) — esto migra solo los datos viejos que
  estaban partidos por día.

**Vista del manager = histórico completo.** Muestra TODOS los proyectos de todas las fechas
(agrupados por día). El tag **NUEVO** y el toggle **"Solo reportados hoy"** distinguen lo del día.
Las tarjetas arrancan **colapsadas**; las que el manager ya calificó quedan marcadas (check + borde
verde) pero colapsadas. Ojo: "expandido" (`expanded`, UI) está **desacoplado** de "calificado"
(`localSel`, tiene impacto). El **Consolidado** sí es por día: filtra el histórico de selecciones a
los proyectos de la fecha elegida (para exportar la tanda del día al master tracker).

Campos de cada proyecto: `id, num, sector, jur, tema, org, giro, autor, title, resumen,
linkExpediente, linkTexto, fecha`. El `id` arranca con el timestamp (ms) de cuándo se cargó — de
ahí sale el destaque **"Nuevo"** (cargado hoy). `giro` (la comisión a la que fue girado) viene del
JSON de la skill y alimenta el Estado del Master Tracker: con giro → Estado = "Girado a comisión"
(sin el nombre, pedido de Cami) y Último movimiento = "Girado a comisión (nombre de la comisión)";
sin giro → Estado = "Sin giro" y Último movimiento vacío. Fecha de último
movimiento = fecha de ingreso del proyecto. Los proyectos cargados antes de agosto 2026 no tienen
`giro` guardado (la app lo descartaba al importar) — salen como "Sin giro".

---

## Cómo funcionan las features (para no romperlas)

- **Tarjeta colapsada/expandida:** click selecciona+expande. Colapsada = título + tema. Expandida =
  detalle completo + dropdowns de impacto.
- **"Nuevo":** badge en esquina sup. derecha + borde izquierdo terracota, para proyectos cargados
  **hoy** (según el timestamp del `id`). Función `loadedToday(p)`.
- **Filtros recordados por manager:** sector + jurisdicción + toggle "Solo reportados hoy" se guardan
  en `localStorage` del navegador, por nombre. Funciones `saveFilterPrefs` / `applyFilterPrefs`.
- **Impacto por cliente — editable y corregible (sin bloqueo):** el desplegable de cada cliente
  queda **abierto para todos**. Muestra siempre el **valor vigente = la última corrección** (mayor
  timestamp por cliente). Si otro manager lo corrige, su valor pisa al anterior y se muestra la
  **cadena de nombres** debajo del desplegable (ej.: "Carolina - Hernán", del que calificó primero al
  más reciente). Cada cambio sella un timestamp **por cliente** (`localImpTs` → `impTs` en la fila del
  manager). El vigente y la cadena los calcula `clientRatings(pid, client)`.
  **Corregir a "-" también vale:** un "-" con timestamp propio es un borrado deliberado y pisa el
  valor anterior (en pantalla y en el consolidado). Un "-" sin timestamp (default / datos viejos)
  significa "sin calificar" y se ignora — por eso las filas viejas no borran nada. `commitSave`
  persiste también los proyectos cuyo único contenido es un borrado (impTs sin valores).
- **Consolidado:** filas de proyecto expandibles (detalle de solo lectura). Export a Sheets toma el
  impacto más alto entre managers.
- **Token anti-carrera (`renderSeq`):** las cuatro vistas async (manager, admin, consolidado, Lark)
  comparten los globales `PROJECTS`/`SELS`. Cada render incrementa `renderSeq` al arrancar y lo
  chequea después de cada `await`: si otra vista arrancó mientras cargaba, descarta su respuesta.
  Sin esto, cambiar de pestaña rápido pisaba los datos de la vista activa (síntoma típico: el
  filtro por fecha de "Proyectos cargados" quedaba vacío). No sacar el chequeo `_tok !== renderSeq`.

---

## Seguridad y limitaciones (conocidas y aceptadas)

- La **contraseña está en el HTML** (control liviano, decisión tomada porque la URL solo circula en
  el equipo) — hoy `TEAM_PASSWORD='bachacero2026'` (rotada 2026-09-01, la vieja quedó expuesta
  mientras el repo fue público). **No** es seguridad fuerte: quien tenga la clave `anon` de
  Supabase (también en el HTML) podría leer/escribir la base directo por REST, sin pasar por esta
  contraseña. La base **no** tiene RLS.
- **Sin login real por persona:** se elige el nombre de una lista (no verifica identidad).
- **Preferencias y "sesión"** viven en el navegador de cada uno (localStorage). En otra compu /
  navegador limpio, se vuelve a pedir la contraseña y los filtros vuelven al default.
- El **bloqueo de impacto** no es tiempo real (ver arriba).

**Pendiente explícito, en espera (decisión de Lucas, 2026-09-02 — "dejalo en pendientes"):**
activar RLS + reescribir el acceso a datos vía funciones `SECURITY DEFINER` (mismo patrón RPC que
ya usa `daily-legislative-snippet`). Rotar la contraseña de equipo NO alcanza por lo explicado
arriba. Estimado: media jornada a una jornada completa (el cuello de botella es probar con
cuidado en una herramienta que usa todo el equipo a diario, sin ambiente de test). Complicación
extra: **este repo no tiene `setup.sql`** — el esquema de `projects`/`selections` vive solo en
Supabase (lo armó Camila directo en el dashboard), así que el primer paso al retomar esto es
confirmar la estructura exacta de esas tablas antes de escribir las funciones RPC. Detalle
completo en la memoria del proyecto (`snippet-digital.md`).

---

## Verificar un cambio antes de publicar

`index.html` es autocontenido: abrilo en el navegador (o usá el preview de tu editor) y probá. No hay
tests automáticos. Para chequear que el JavaScript no tenga errores de sintaxis tras un cambio
grande, Claude Code puede correr `node --check` sobre el script.
