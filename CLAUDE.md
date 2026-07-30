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
- **Datos:** Supabase (PostgreSQL en la nube). Proyecto ref `xyqmtqsczscdejcwusce`.
- **Hosting:** GitHub Pages, repo `camila509/Snippet-digital`.
  URL pública: https://camila509.github.io/Snippet-digital/
- **Extracción del snippet → JSON:** una **skill de Claude** (no corre dentro de la app). Ver abajo.

No hay servidor propio ni proceso de build. Se edita el `index.html` y se publica.

---

## Cómo editar y publicar

**Opción A — desde GitHub (sin instalar nada):** entrá al repo → `index.html` → ícono del lápiz →
editás → **Commit changes**. GitHub Pages republica en ~1 minuto.

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
| Cambiar la **contraseña** del equipo | `var TEAM_PASSWORD = 'bachacero';` |
| Agregar/quitar gente de **Research** (acceso admin) | `var RESEARCH = [...]` |
| Agregar/quitar gente de **Public Affairs** (solo califican) | `var PUBLIC_AFFAIRS = [...]` |
| Agregar/quitar **clientes** (columnas de impacto) | `var CLIENTS = [...]` |
| Agregar/quitar **sectores** | `var SECTORES = [...]` **y** actualizar la lista en la skill |
| Agregar/quitar **provincias/jurisdicciones** | `var PROVINCIAS = [...]` |
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

> **Export a Sheets:** `buildRows` arma las filas tabuladas alineadas a las 34 columnas del
> **Master Tracker 2.0** (hoja `Sheet1`). Las columnas de impacto (19-34) usan su propio orden y
> nombres de la planilla (Microsoft, Amazon, Tiktok, Worldcoin, Didi…), mapeados a los clientes de
> la app en `SHEET_IMPACT_COLS`. Si cambia la planilla (orden, nombres o columnas), ajustá ahí.

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

## La skill `snippet-to-json`

- Convierte el Daily Legal Snippet (Word/.eml/texto) en el JSON que importa la app.
- Corre desde la cuenta de Claude (panel **Settings → Skills**). El fuente (`SKILL.md`) y el `.skill`
  empaquetado están en la carpeta `skill/` de este repo.

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

Campos de cada proyecto: `id, num, sector, jur, tema, org, autor, title, resumen, linkExpediente,
linkTexto, fecha`. El `id` arranca con el timestamp (ms) de cuándo se cargó — de ahí sale el
destaque **"Nuevo"** (cargado hoy).

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

---

## Seguridad y limitaciones (conocidas y aceptadas)

- La **contraseña está en el HTML** (control liviano, decisión tomada porque la URL solo circula en
  el equipo). **No** es seguridad fuerte: quien tenga la URL podría leer/escribir la base con la
  clave pública. La base **no** tiene RLS.
- **Sin login real por persona:** se elige el nombre de una lista (no verifica identidad).
- **Preferencias y "sesión"** viven en el navegador de cada uno (localStorage). En otra compu /
  navegador limpio, se vuelve a pedir la contraseña y los filtros vuelven al default.
- El **bloqueo de impacto** no es tiempo real (ver arriba).

Si en algún momento se quiere endurecer (RLS + login compartido con Supabase Auth, o proxy serverless
para la IA), está la opción — pedíselo a Claude Code y te guía.

---

## Verificar un cambio antes de publicar

`index.html` es autocontenido: abrilo en el navegador (o usá el preview de tu editor) y probá. No hay
tests automáticos. Para chequear que el JavaScript no tenga errores de sintaxis tras un cambio
grande, Claude Code puede correr `node --check` sobre el script.
