# Catálogo Editorial UNGRD — Quarto site

Landing page (Quarto static site) for the UNGRD Subdirección para el Conocimiento
del Riesgo's publication catalog. Live at
https://scr-ungrd.github.io/catalogo-publicaciones/ (repo:
`scr-ungrd/catalogo-publicaciones`).

## What this project is

The InDesign catalog is the source of truth for cover art —
`/Users/mauricio/Documents/Data-science-local/SCR/catalogo-portadas/` (the
design package: `CATALOGO SCR 2026.{indd,idml,pdf}` + a `Links/` folder with
every linked image asset). **This is a different, newer file than
`catalogo-scr.pdf` in this repo** — the repo's PDF is a point-in-time export
kept only as the hero cover image source, not re-synced automatically. When
the design PDF gets a new edition, re-check `catalogo-portadas` for
added/removed/renamed items before touching cards — see the 2026-08-02 sync
below for what that involved last time.

`index.qmd` has a hero + sidebar-filter section (`#catalogo`) + footer. All
57 cards live in one flat grid (`#catalog-grid`); each card carries
`data-linea` (one of `identificacion|analisis|monitoreo|comunicacion`, the
4 "líneas temáticas"), `data-tipo` (one of `informes|cartillas|guias|
herramientas|educativos|multimedia|memorias`, a coarser product-type facet),
and `data-amenaza` (one of `biosanitario|movimientos-masa|
avenidas-torrenciales|erosion-costera|erosion-fluvial|inundaciones|
volcanico|sequia|incendios|ciclones-tropicales|climatico|
tecnologico-natech|transversal` — added 2026-08-02, classified from title +
actual cover content, not title text alone: e.g. "Informe Segunda Temporada
Seca 2024" is tagged `incendios`, not `sequia`, because its cover photo and
caption are specifically about wildfires in Huila, not drought in general.
`transversal` is the catch-all for the ~half of the catalog that's
education/communication/tools content with no single natural hazard —
**no publication is primarily about sismos or incendios forestales as a
dedicated topic**, so those two hazards the user might expect (by analogy
to inundaciones/biosanitario) intentionally have no dedicated facet value
of their own beyond the one `incendios` card. All three facets follow the
same single-value-per-card model — a card can't carry two `data-linea` or
two `data-amenaza` values, so borderline items got the single best-fit tag,
same as the original `data-tipo` classification call.

The filtering, live result counts, dynamic per-facet counts, and collapsible
facet groups are vanilla JS in a `<script>` block at the bottom of the
file — no build step, no framework. `applyFilters()` is written generically
over a `GROUPS = ['linea', 'tipo', 'amenaza']` array (not 3 copy-pasted
code paths) — adding a 4th facet later means adding one entry to `GROUPS`
plus the matching sidebar markup, not touching the filter logic.
- `custom.scss` — theme. Colors and type follow the UNGRD brand manual
  (`/Users/mauricio/Documents/Data-science-local/SCR/Manual-de-Imagen-y-Unidad-Grafica-UNGRD.pdf`):
  primary navy `#222D5D` / yellow `#FFD100`; secondary blue `#1A4D9B`,
  celeste `#3480E4`, orange `#F28B37`, gold `#D4A83F` (used as the 4 topic
  accent colors, `--topic-1..4`); typography is Helvetica Neue/Arial only
  (no serif — the manual doesn't allow one).
- **Navbar is a fixed light "masthead", by design, in both color modes.**
  `images/logo-ungrd.png` has its "UNGRD" wordmark baked in as dark navy on
  a transparent background — there's no reversed/white logo asset anywhere
  in the SCR projects. So the top navbar stays a translucent light panel
  (`rgba(255,255,255,.85)` light mode / `rgba(241,243,250,.92)` dark mode,
  both with `backdrop-filter: blur(12px)`) regardless of the page's
  light/dark toggle — same visual language (glass panel + blur + hairline
  border) as the sibling HUB SCR project's header
  (`/Users/mauricio/Documents/Data-science-local/SCR/hub/css/variables.css`),
  but deliberately *not* going dark in dark mode like HUB's does, because
  that would repeat the exact contrast problem being fixed here. Don't
  "restore" a solid dark navbar background — that's what made the logo
  unreadable before. Nav-link text/icon colors in this block are
  hardcoded hex (`#222D5D`, `#F28B37`, `#C96E22`), not the `--ungrd-*`
  custom properties, because those variables intentionally swap to
  lighter tones in dark mode (for text on dark backgrounds) — using them
  here would wash out against the still-light navbar.
- `images/cards/` — the 57 cover images, one per publication, named by
  content slug (e.g. `hidroituango-informe.jpg`), **not** by PDF page number
  (`pN-M.jpg`, the old convention) — page numbers shift every time the
  catalog gets re-paginated, which made the old names meaningless almost
  immediately. Most of these (56 of 57) are direct exports from
  `catalogo-portadas/Links/*` (the actual finished cover art InDesign placed
  on the page), resized to 700px wide — not screenshots of the assembled
  PDF page. `Links/` filenames are mostly opaque (`CUB_*` = "cubierta" for
  some, meaningless stock-photo codes like `9988cc.png` for others) and
  don't map 1:1 to the 59-title list by name alone — matching required
  opening each candidate file and comparing it against the page it's placed
  on. The one exception, `territorios-resilientes.jpg`, has no standalone
  export in `Links/` and was cropped directly from a 300dpi render of
  `catalogo-portadas/CATALOGO SCR 2026.pdf` page 18 (`pdftoppm -png -r 300`).
  `images/cover-catalogo.jpg` (hero cover) is untouched by this and still
  comes from the older `catalogo-scr.pdf`.

### 2026-08-02 content sync (57 items, was 59)

Comparing `catalogo-portadas/CATALOGO SCR 2026.pdf` (2026-07-31, 25pp)
against the previously-used `catalogo-scr.pdf` (2026-07-29, 26pp) via
`pdftotext -layout` diff found real content changes, not just a re-export:
removed *"Caracterización de elementos expuestos"* and *"Cartilla de
monitoreo comunitario"* (both were "pendiente de publicación" placeholders),
removed *"Identificación de instalaciones industriales como insumo..."*,
and added *"Lineamientos para la incorporación de escenarios de riesgo de
desastres asociados a riesgo tecnológico, riesgo Natech y accidente
mayor..."* (effectively supersedes the removed instalaciones-industriales
item, same topic). Net 59 → 57. If `catalogo-portadas` gets a newer edition
again, diff it the same way before assuming it's just image refresh.

## Two non-obvious Quarto/browser gotchas hit while building this

1. **`position: sticky` silently does nothing inside `#quarto-content`.**
   Quarto's own wrapper div ships with `overflow-y: auto`, which breaks the
   sticky containing-block chain. The sidebar's "stick while scrolling, stop
   before the footer" behavior is done manually in JS
   (`updateStickySidebar()` in `index.qmd`), not via CSS `position: sticky`.
2. **Never use an `<aside>` tag for custom layout.** Quarto has special
   handling for `<aside>` (its margin-note/margin-sidebar feature) that
   hijacks the parent's `grid-template-columns` into extra implicit tracks,
   breaking any custom CSS grid the `<aside>` sits in. Use
   `<div role="complementary">` instead.
3. **`data-bs-theme="dark"` on the `<nav>` is static, not the real dark-mode
   flag.** Quarto's website navbar template
   (`nav-before-body.ejs`) hardcodes `data-bs-theme="dark"` on the `<nav>`
   regardless of the `navbar: background/foreground` config — it never
   reflects whether the visitor is actually in light or dark mode. Any CSS
   meant to respond to the real toggle must key off `body.quarto-dark` /
   `body.quarto-light` (the classes the color-scheme-toggle script actually
   flips), not `[data-bs-theme="dark"]`. Several older rules in
   `custom.scss` still carry a redundant (harmless but dead)
   `[data-bs-theme="dark"] .foo` selector alongside the working
   `body.quarto-dark .foo` one — leftover from before this was understood.

## Deployment

GitHub Actions (`.github/workflows/quarto-publish.yml`, copied from the
sibling site `investigaciones-grd-colombia`) renders and publishes to the
`gh-pages` branch on every push to `main`. That workflow **requires the
`gh-pages` branch to already exist** — if it doesn't (e.g. fresh repo), the
first publish must be bootstrapped manually (push an orphan `gh-pages`
branch, or run `quarto publish gh-pages` locally) before CI can take over.

Local dev: `quarto preview` (repo is *not* usually running a preview server
between sessions — start a fresh one rather than assuming an old port is
still alive; `quarto render` output can go stale if `.quarto/` isn't
cleared after big structural changes to `index.qmd`).

## Open design recommendations (not yet implemented)

Reviewed against the current build, 5 things flagged as worth doing next:
1. Introduce real type hierarchy using Helvetica weight/width variants
   (Condensed/Bold for display, Regular for body) instead of one weight
   everywhere.
2. Replace the generic "book cover on grey" hero with a subject-specific
   graphic (Colombia silhouette + the 4 líneas temáticas, or hazard icons).
3. Give each línea temática tag an icon, not just a color pill.
4. Replace `--section-bg-alt: #F5F4F0` (generic off-white) with a neutral
   derived from the brand navy.
5. Redesign the card chrome away from generic SaaS-card styling (white
   rounded rect + shadow + top color bar) toward something that reads as a
   catalog/dossier card.
