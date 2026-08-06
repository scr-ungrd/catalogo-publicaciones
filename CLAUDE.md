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
cards (57 as of the 2026-08-02 sync below, 59 as of the 2026-08-05 repo-link/
año-facet pass — check the hero `stat-num` and `#results-count` for the
current count, both must be updated together whenever a card is added or
removed) live in one flat grid (`#catalog-grid`); each card carries
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
  on. `territorios-resilientes.jpg` originally had no standalone export in
  `Links/` and was cropped directly from a 300dpi render of
  `catalogo-portadas/CATALOGO SCR 2026.pdf` page 18 (`pdftoppm -png -r 300`)
  — **superseded 2026-08-06** by an official cover the user supplied
  directly (PNG from Desktop, converted to 700px-wide JPEG with `sips`,
  same as every other card), once the link and año became available (see
  below); the PDF-crop version is no longer used anywhere in the repo.
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

## Repository links (`hdl.handle.net`) and the "Año" facet (2026-08-05)

Cards can carry an `href` pointing at the item's persistent identifier in the
UNGRD institutional repository (DSpace 6.4 JSPUI at
`repositorio.gestiondelriesgo.gov.co:8443`), in `http://hdl.handle.net/20.500.11762/<id>`
form — not the `repositorio.gestiondelriesgo.gov.co/handle/...` form (that
form is only still used by the two pre-existing IDRiM cards from before this
pass; new links use the `hdl.handle.net` resolver per explicit instruction).

**Only add a repo link when you've confirmed the match, not from a keyword
search alone.** The DSpace `/discover?query=` search is a fulltext OR/AND
mix over abstracts too, not just titles — a plain-language query for e.g.
"Marco teórico de fenómenos amenazantes de origen biosanitario" returns
unrelated legal documents that merely contain "biosanitario" in an abstract.
Confirm a hit by opening `/handle/20.500.11762/<id>` and checking
`citation_title` matches, then read `citation_date` (not
`DCTERMS.dateAccepted`, which is the workflow-acceptance timestamp, not the
publication date) for the year. Of the 51 catalog items checked this way
against the live repository search on 2026-08-05, only **8 had a confirmed,
working match** (`animales-caracterizacion`→41663, Natech→41601 — a
duplicate upload also exists at 41624, purged in favor of 41601's cleaner
filename and earlier `dateAccepted`, avenidas torrenciales→41660, which
holds *both* the "Libro y anexo" and "Anexo técnico" catalog cards as two
bitstreams under one handle so both cards point at the same handle,
posdesastre→40732, lluvias 2016→20822, nichos y semilleros→41778,
calendario 2026-2027→41761). A ninth match, Ciclones tropicales 2018→27854,
looked confirmed the same way (`citation_title` matched exactly) but the
user reported the resulting `hdl.handle.net` link actually resolved to a
blank page — the handle was pulled 2026-08-05 rather than investigated
further, since a newer, better-sourced 2026 edition of the same report
(Google Drive link, see below) already existed as a separate card. **Handle
resolution isn't fully trustworthy even after a metadata match** — if a
newer edition of the same title exists as a card already, prefer that over
chasing why an old handle 404s/blanks.  The other 42 items genuinely
aren't in the repository yet — most of the "identificación" línea's
technical reports and all of the "programa educativo" / viewer / podcast
entries have no repository record, consistent with this catalog including
recent/forthcoming works not yet deposited. Don't assume a future re-check
will find them without re-running the search — but also don't assume 0
results means "never will be," since new items get deposited continuously
(e.g. 41663 was accepted 2026-03-30, mid-catalog-assembly).

Every card carries `data-anio="<year>"` or `data-anio="sin-fecha"` (facet
value `sin-fecha`, but the sidebar **label** reads "Por registrar", not
"Sin fecha registrada" — renamed 2026-08-05 per explicit request; keep the
attribute/value as `sin-fecha`, only the display label changed). For cards
with a repo match, the year is the repo's `citation_date` year, which can
differ from any year printed in the title (e.g. the Natech "2011–2024"
card is dated 2025 in the repo, not 2024 — that's the analysis period, not
the publication date). For unmatched cards with a year or year-range
literally in the title, the heuristic used was: a single year → use it; a
forward-looking range for a season/cycle not yet started (e.g. "Fenómeno
El Niño en Colombia 2026-2027", "Calendario Climático 2024–2025") → use
the **start** year, since these are published ahead of the period they
cover; a backward-looking/ongoing-monitoring range (e.g. "Informe
Hidroituango 2018–2026") → use the **end** year, since these are
retrospective/current-status documents. A companion item with no year of
its own but an obvious sibling (e.g. "Cartillas: Lecciones aprendidas de
recuperación posdesastre" next to the dated "Lecciones aprendidas de
recuperación posdesastre" report) inherited the sibling's year by
inference, not from any independent source. Everything else — no year
anywhere, no repo match — is `sin-fecha`. `GROUPS` in the filter script is
`['linea', 'tipo', 'amenaza', 'anio']`; the generic `applyFilters()` logic
needed no changes, only the new facet-group markup in the sidebar and one
array entry.

The static count badges (`data-count-for="anio:2026"` etc.) are cosmetic
fallbacks — `applyFilters()` overwrites them from the live DOM on load —
but keep them numerically correct anyway for anyone reading page source
before JS runs. Recompute by hand if you add/remove cards; it's easy to
undercount (this pass initially miscounted 2025 and 2024 by forgetting the
avenidas-torrenciales pair and the two IDRiM cards share a year with other
matches).

### "Todos" / "Ninguna" per-facet select buttons (2026-08-06)

Each `.facet-options` block starts with a `.facet-select-actions` row (two
`.facet-select-btn` buttons, `data-select="all"`/`data-select="none"`) that
check/uncheck every checkbox in that one facet-group and re-run
`applyFilters()` — wired generically in the script via
`document.querySelectorAll('.facet-select-btn')` + `btn.closest('.facet-group')`,
no per-facet-specific JS needed, so a 5th facet would pick this up for free
the same way it already does for `GROUPS`.

Adding this exposed a latent bug in `cardMatchesGroup()`: it used to treat
zero checkboxes selected in a group as "no constraint" (`values.length ===
0` short-circuited to show everything), which is *never* what a user
means by unchecking every box — it made "Ninguna" a visible no-op (0
checkboxes checked, but still all 59 cards showing). Fixed by dropping
that short-circuit so an empty selection now correctly matches nothing.
This was reachable before (a user could always hand-uncheck every box in
a group), but "Ninguna" made it a one-click path, so the wrong behavior
became obvious immediately. If you ever need "empty = show all" back for
some reason, that's a deliberate behavior change from what's now shipped,
not a revert of an accidental one.

### Card title casing is inconsistent, and that's pre-existing

Most catalog titles use sentence case ("Boletín la prevención es de todos:
Erosión Costera"). The 2026-08-05 addition ("Ciclones Tropicales
Caracterización de Escenarios de Riesgo", the June-2026 edition of the
ciclones-tropicales report, sourced from a Google Drive link rather than
the repository, cover art supplied directly by the user) was added in
Title Case per explicit instruction for that card — don't normalize it to
sentence case to "match" the rest, and don't assume Title Case is now the
house style for future additions unless told again. This card *replaced*
the older sentence-case "Ciclones tropicales. Caracterización de
escenarios de riesgo" card (2018, `hdl.handle.net/.../27854`) — that
older card was removed the same day once its handle turned out to resolve
to a blank page (see the repo-links section above); don't re-add it
without first confirming the handle actually works.

### "Territorios resilientes" card completed (2026-08-06)

This card previously had no `href` and `data-anio="sin-fecha"` (see the
`territorios-resilientes.jpg` note above — cover art was a PDF-page crop
because no standalone export existed). The user supplied all three missing
pieces at once: an official cover image, `data-anio="2026"`, and a
flippingbook link (`https://online.flippingbook.com/view/374796701/`) —
same external-viewer pattern as the "Juntanzas para Cuidar la Vida" card,
not a `hdl.handle.net` repository link, so no repo lookup was attempted for
this one. `anio:2026` count went 6→7, `anio:sin-fecha` went 39→38; total
card count (59) and the hero `stat-num` / `#results-count` are unchanged
since this updated an existing card rather than adding a new one.

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
