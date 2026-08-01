# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this is

A static example website: four HTML pages sharing one stylesheet, served directly by
nginx. See `README.md` for the file layout and nginx config.

## Hard constraints

**No JavaScript.** This is the defining constraint of the project, not a preference.
Do not add `<script>` tags, inline handlers, or JS files. Interactive behavior must be
achieved with CSS — the mobile nav, for example, is a hidden checkbox plus a sibling
selector. If something genuinely can't be done without JS, say so rather than quietly
adding it.

**No build step and no dependencies.** No npm, no bundler, no preprocessor, no CSS
framework. Editing a file and reloading the browser is the entire workflow. Don't add
`package.json`, a Sass pipeline, or a CDN `<link>` — the site must work offline and
from a bare directory.

**One stylesheet.** All CSS lives in `css/styles.css`. Don't add per-page stylesheets
or `<style>` blocks in the HTML.

## Conventions

- **Styling goes through custom properties.** Colors, radii, shadows, and spacing are
  defined in the `:root` block at the top of `css/styles.css`. Reach for an existing
  variable before writing a literal value; add a new variable if a value will be
  reused.
- **Dark mode is not optional.** Any new color must work in both themes. Define it in
  `:root` and override it in the `prefers-color-scheme: dark` block right below.
- The stylesheet is organized in labeled sections (`/* ---- Header ---- */`) roughly
  following page order, with responsive overrides collected at the bottom. Put new
  rules in the matching section rather than appending to the end.
- Two-space indentation in both HTML and CSS.
- Semantic HTML: real landmarks (`header`, `main`, `footer`, `nav`), heading levels in
  order, `aria-label` on each `nav`.
- Accessibility is expected to hold: every page keeps its skip link, form inputs stay
  paired with `<label for>`, focus rings (`:focus-visible`) stay visible, and the
  `prefers-reduced-motion` block at the bottom of the stylesheet stays intact.
- Layout uses CSS grid with `repeat(auto-fit, minmax(...))` and `clamp()` for type, so
  most things are responsive without a media query. Add breakpoints only when the
  intrinsic approach won't do it.

## Adding a page

Copy an existing page and change three things: the `<title>`, the contents of
`<main>`, and which nav link carries `class="active"`. Header and footer markup are
duplicated across pages by design — there's no templating, so a change to the nav has
to be applied to every HTML file including `404.html`.

## Path rules

Regular pages use **relative** links (`css/styles.css`, `about.html`).

`404.html` uses **absolute** links (`/css/styles.css`, `/index.html`) because nginx
serves it for requests at any depth, and relative paths would break its styling on a
URL like `/foo/bar`. Keep this distinction when editing.

## Known non-issues

- The contact form posts to `#`. This is intentional — a static site has no backend.
  Don't "fix" it by adding a JS handler. The page carries a visible note explaining it.
- Header and footer markup is repeated in every page. Intentional; see above.

## Verifying changes

There are no tests. Check work by serving the directory and loading the pages:

```sh
python3 -m http.server 8000
```

Confirm each page returns 200, the stylesheet loads, and the layout holds at both a
narrow (~375px) and wide viewport. If you change copy that states a fact about the
site — file counts, total size, the feature list on `index.html` — verify it against
the actual files rather than leaving a stale number in place.
