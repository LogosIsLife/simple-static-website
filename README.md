# Meridian — Example Static Website

A small example website built with nothing but HTML and CSS. No JavaScript, no build
step, no dependencies. Meant to be served directly by nginx from this directory.

## Contents

```
example_website/
├── index.html        Home — hero, feature cards, split section with stats, CTA
├── about.html        Prose page, file-tree code block, team grid
├── contact.html      Contact form plus a details sidebar
├── 404.html          Error page (uses absolute paths — see note below)
├── css/
│   └── styles.css    The only stylesheet (~15 KB)
├── .gitignore
├── CLAUDE.md         Notes for Claude Code sessions
└── README.md
```

Total: about 32 KB uncompressed.

## Viewing it locally

It's just files, so opening `index.html` in a browser works. To exercise it over HTTP
the way nginx will serve it:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Hosting behind nginx

Point the server root at this directory:

```nginx
server {
    listen 80;
    server_name example.com;

    root /path/to/example_website;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
}
```

Reload with `nginx -s reload`. There's nothing to restart or rebuild — deploying is
copying files.

## Customizing

Nearly all visual changes happen in the `:root` block at the top of `css/styles.css`,
which defines every color, radius, shadow, and spacing value as a custom property.

```css
:root {
  --brand:   #3b6ef5;   /* accent used by buttons, links, stats, icons */
  --bg:      #ffffff;   /* page background */
  --radius:  12px;      /* card corners */
  --max-width: 1120px;  /* content column width */
}
```

Dark mode is a `prefers-color-scheme: dark` block that overrides the same variables,
so a new palette only has to be defined twice, not scattered through the file.

Beyond that:

- Replace the `◆` brand mark in each page's header and footer with an `<img>` if you
  have a logo. The favicon is an inline SVG data URI in each `<head>`.
- Edit the copy on each page. The markup is plain and semantic.
- New pages: copy an existing file, change the `<title>` and `<main>`, and move the
  `class="active"` to the matching nav link.

## Things to know

**The contact form doesn't submit anywhere.** It posts to `#` because a static site
has no backend. Point the `<form action>` at your own endpoint or form service to
make it live. The page shows a visible note saying as much — remove it once wired up.

**`404.html` uses absolute paths** (`/css/styles.css`, `/index.html`) while the other
pages use relative ones. This is deliberate: nginx serves the error page for requests
at any depth, and relative paths would break the styling on a URL like `/foo/bar`. The
tradeoff is that this one page assumes the site lives at the server root.

**The mobile menu is a checkbox.** The hamburger at narrow widths is a hidden
`<input type="checkbox">` paired with a `<label>` and a CSS sibling selector — which
is how the site stays functional with JavaScript disabled.

## Browser support

Modern evergreen browsers. The stylesheet uses `color-mix()`, `clamp()`, CSS custom
properties, and `backdrop-filter`. Older browsers degrade to a plainer but readable
layout rather than breaking.

## License

Example content. The names, people, and contact details throughout are fictional.
