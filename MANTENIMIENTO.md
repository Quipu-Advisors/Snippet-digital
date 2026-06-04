# Mantener Smart Snippet con Claude Code (guía para Camila)

Esta guía es para retomar y mantener Smart Snippet vos misma, desde tu compu, usando **Claude Code**.
Vos ya sos dueña de todo lo necesario (el repo de GitHub, el proyecto de Supabase y la skill), así
que no dependés de nadie. Una vez configurado, los cambios se piden **en lenguaje natural** y se
publican solos.

> Contexto técnico completo del proyecto (qué es cada cosa, dónde tocar): ver **[CLAUDE.md](CLAUDE.md)**.
> Claude Code lo lee automáticamente al abrir la carpeta, así que tiene todo el contexto sin que
> se lo expliques.

---

## 1. Instalar (una sola vez)

1. **Node.js** (LTS): https://nodejs.org → instalar.
2. **Claude Code**: en una terminal (PowerShell), correr
   `npm install -g @anthropic-ai/claude-code`
   (o seguí https://docs.claude.com/claude-code). Después escribí `claude` y seguí el login con tu
   cuenta de Anthropic (la misma del plan Claude).
3. **Git** + **GitHub CLI**: https://git-scm.com y https://cli.github.com
   - Autenticá GitHub: `gh auth login` → GitHub.com → HTTPS → con el navegador, **con tu cuenta
     `camila509`** (sos la dueña del repo, así que tenés acceso total para publicar).
   - Conectá git con esa credencial: `gh auth setup-git`

## 2. Bajar el código (una sola vez)

En la carpeta donde quieras tenerlo:
```
gh repo clone camila509/Snippet-digital
cd Snippet-digital
```

## 3. Trabajar / mantener (cada vez)

```
cd Snippet-digital
claude
```
Y pedile el cambio en lenguaje natural. Ejemplos:
- "cambiá la contraseña del equipo a tal"
- "agregá a Fulano en Public Affairs"
- "agregá el cliente X / sacá el cliente Y"
- "cambiá tal texto del modal de bienvenida"
- "el botón tal está descolocado, arreglalo"

Claude edita el `index.html`, y cuando le digas que publique (o si le pedís que lo haga directo),
hace **commit + push**. GitHub Pages republica solo en ~1 minuto en
https://camila509.github.io/Snippet-digital/ . En el navegador, **Ctrl+F5** para ver los cambios
(saltea el cache).

> Si no querés instalar Claude Code, también podés editar el `index.html` directo en GitHub
> (ícono del lápiz → Commit) — pero Claude Code es mucho más cómodo para cambios no triviales.

## 4. Los "elementos" de Smart Snippet y dónde están

| Elemento | Dónde | Quién lo controla |
|---|---|---|
| **App** (`index.html`, `mulita.png`) | este repo (GitHub Pages) | vos (push directo) |
| **Documentación** | `CLAUDE.md`, `README.md`, este archivo | este repo |
| **Skill** de extracción Word→JSON | carpeta `skill/` (fuente + `.skill`) **y** tu panel de Claude (Settings → Skills) | vos |
| **Base de datos** (proyectos, selecciones) | Supabase, proyecto `xyqmtqsczscdejcwusce` | tu cuenta de Supabase |
| **Hosting** | GitHub Pages del repo | tu cuenta de GitHub |

Notas:
- La **skill** vive en tu cuenta de Claude (panel). El fuente está versionado en `skill/SKILL.md`:
  si la cambiás, actualizá también el contenido en el panel (Settings → Skills) — el panel no se
  sincroniza solo con el repo.
- La **base** la administrás desde el panel de Supabase (tu cuenta). La app usa la clave pública;
  no hay nada que tocar ahí para el día a día.

## 5. Antes de publicar (recomendado)

`index.html` es autocontenido: podés abrirlo en el navegador (o pedirle a Claude Code que levante
un preview) para probar antes de subir. No hay tests automáticos; Claude Code puede correr un chequeo
de sintaxis del JavaScript si hacés un cambio grande.
