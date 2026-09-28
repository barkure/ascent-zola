# Ascent

[![License: MIT](https://img.shields.io/badge/License-MIT-informational.svg)](LICENSE)
[![Zola: ≥ 0.23](https://img.shields.io/badge/Zola-%E2%89%A5%200.23-blue.svg)](https://www.getzola.org)

> An opinionated theme for blogs with long blocks of text. It aims to give the
> reader as smooth a reading experience as possible --- posts should read like
> magazine articles or newspaper features.
> --- [Ascent](https://github.com/cjquines/hexo-theme-ascent), by CJ Quines

The [Zola](https://www.getzola.org/) port of Ascent: the original design,
stylesheets and behaviour, ported 1:1 from the Hexo original.

**[Live demo](https://blog.barku.re/)** --- built with this theme.

![screenshot](screenshot.png)

## Features

* Magazine-style typography: configurable font stacks (Lora / Lato / Playfair
  Display SC by default), an optional serif ⇄ sans toggle, and a light ⇄ dark
  toggle that remembers its choice in `localStorage`.
* Month-grouped archives with per-month accordions and a "Toggle all" switch.
* Excerpts on listings, with headings, images, rules and code blocks
  automatically kept out of the summary.
* Paginated home page and newer/older navigation between posts.
* Word counts and reading time.
* Tag and category pages reusing the archive listing.
* Per-page MathJax (client-side, v4) for posts that ask for it.
* Chinese-friendly dates: `date_locale = "zh-cn"` +
  `cjk_meridiem = true` reproduce moment.js'
  凌晨/早上/上午/中午/下午/晚上 day periods.
* Optional IntenseDebate comments and Google Analytics, as in the original.

## Requirements

* Zola ≥ 0.23.0 (Tera 2 components, Giallo syntax highlighting, absolute
  `get_url`/`permalink`).

## Installation

```bash
cd your-zola-site
git submodule add https://github.com/barkure/ascent-zola themes/ascent
```

```toml
# zola.toml
theme = "ascent"

[markdown.highlighting]
# the theme expects class-based highlighting
theme = "github-light"
style = "class"
```

### Content structure

The templates are built around a "transparent" posts section; create these
files in your site to get started:

```
content/
├── _index.md          # the home page: paginates the posts below
├── archives/_index.md # every post, grouped by month
└── posts/             # transparent + non-rendered: URLs stay /posts/<slug>/
    ├── _index.md
    └── my-post.md
```

```toml
# content/_index.md
+++
sort_by = "date"
paginate_by = 10
paginate_path = "page"
+++
```

```toml
# content/archives/_index.md
+++
title = "Archives"
template = "archive.html"
sort_by = "none"
+++
```

## Writing a post

```toml
+++
title = "My Post"
date = 2026-01-01T12:00:00+08:00

[extra]
mathjax = true            # load MathJax for $inline$ and $$display$$ math
og_image = "https://…"   # optional social image (or set the site-wide default)
photos = ["https://…"]   # optional image gallery
layout = "page"          # optional, changes the article id/class
+++
```

* `<!-- more -->` marks the end of the excerpt.
* Fenced code blocks get the original theme's line-number gutter with
  `,linenos`:

  ````markdown
  ```bash,linenos
  echo "hello"
  ```
  ````

* Taxonomies are opt-in; uncomment `taxonomies` in your `zola.toml` to enable
  tag and category pages.

## Options

Site-level, in your `zola.toml` under `[extra]` (defaults shown; they come
from the theme's `theme.toml`):

| key | default | meaning |
| --- | --- | --- |
| `favicon` | `""` | path to your favicon (the theme ships none), e.g. `/favicon.ico` |
| `menu` | Home, Archives | header links; `http(s)://` URLs automatically get `target="_blank" rel="noopener"` |
| `settings` | `true` | show the lower-right toggles |
| `sans_toggle` | `true` | include the serif ⇄ sans toggle (swaps `body_stack` ⇄ `sans_stack`) |
| `highlight` | `true` | load `css/highlight.css` |
| `excerpt_link` | `read more` | label of the read-more link, `""` to hide |
| `rss` | `""` | feed path for the header link, e.g. `/atom.xml` |
| `word_count` | `false` | show "N words × M minutes" |
| `date_locale` | `en` | locale passed to Zola's `date` filter (BCP-47) |
| `cjk_meridiem` | `false` | use 凌晨/早上/上午/中午/下午/晚上 instead of AM/PM |
| `og_image` | `""` | fallback `og:image` / `twitter:image` |
| `og_locale` | `""` | `og:locale` value, e.g. `zh_CN` |
| `intensedebate_acct` | `""` | IntenseDebate site key; enables comments when set |
| `google_analytics_acct` | `""` | Universal Analytics id; enables the snippet when set |
| `mathjax_url` | jsDelivr `mathjax@4/tex-svg.js` | MathJax bundle for `mathjax = true` pages |
| `mathjax_config` | TeX delimiters, `enableMenu: false` | inline `MathJax = {...}` object |
| `fonts_url` | Google Fonts Lora / Lato / Playfair Display SC / Roboto Mono | `@font-face` stylesheet, or a list of them; `""` loads no webfonts |
| `fonts_preconnect` | googleapis + gstatic | origins to preconnect before the stylesheets |
| `body_stack` | `"Lora", … serif` | body text |
| `title_stack` | `"Playfair Display SC", … serif` | site name and post titles |
| `secondary_stack` | `"Lato", … sans-serif` | nav, dates, metadata |
| `highlight_stack` | `"Roboto Mono", … monospace` | code and `pre` |
| `sans_stack` | `"Lato", … sans-serif` | what the serif ⇄ sans toggle switches to |

Page-level, in a post's front matter under `[extra]`: `mathjax`, `og_image`,
`photos`, `layout`.

### Fonts

Nothing is hardcoded: the theme links `fonts_url` from `partials/head.html`
(no `@import`, which would serialise the font fetch behind the stylesheet) and
emits the four stacks as an inline `<style>` that overrides the `:root`
defaults. `fonts_url` takes one stylesheet URL or a list of them; a leading `/`
is resolved with `get_url`. Change the families without touching CSS, e.g. a
serif-only site:

```toml
[extra]
body_stack = '"Noto Serif SC", "Songti SC", "SimSun", serif'
title_stack = '"Noto Serif SC", "Songti SC", "SimSun", serif'
secondary_stack = '"Noto Serif SC", "Songti SC", "SimSun", serif'
sans_toggle = false
```

Values are raw CSS, so quote family names containing spaces and keep the generic
family last.

Google Fonts is unreachable from mainland China. Swap the host for the `.cn`
mirror and preconnect to the matching font-file host:

```toml
[extra]
fonts_url = "https://fonts.googleapis.cn/css2?family=Noto+Serif+SC:wght@200..900&display=swap"
fonts_preconnect = ["https://fonts.googleapis.cn", "https://fonts.gstatic.cn"]
```

One caveat with CJK families: Google Fonts splits them into ~100
`unicode-range` subsets, so a Chinese page pulls only the slices its glyphs land
in — several dozen small requests instead of one file. That is fine for a blog,
but self-hosting a subset is better if you care about first paint.

A family no webfont host carries goes in as a second entry. Maple Mono CN, for
instance, is packaged on npm as `unicode-range` slices, so the browser fetches
only the slices a page's glyphs fall in:

```toml
[extra]
fonts_url = [
  "https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@200..900&display=swap",
  # cdn.jsdelivr.net has low availability in mainland China; these mirrors
  # carry the same package and gzip the CSS just the same.
  "https://jsd.onmicrosoft.cn/npm/@automann/maple-mono-cn@7.9.2/dist/regular.css",
]
fonts_preconnect = [
  "https://fonts.googleapis.com", "https://fonts.gstatic.com",
  "https://jsd.onmicrosoft.cn",
]
highlight_stack = '"Maple Mono CN", "Inconsolata", "Consolas", ui-monospace, monospace'
```

Note that sliced CJK packaging is not automatically lighter than one bespoke
subset: each slice carries glyphs for a whole range, so a page that touches many
ranges pays for all of them. Measure before assuming.

### Overriding templates

Site templates win over theme templates. To change the MathJax loader for
example, create `templates/partials/mathjax.html` in the site root; the theme
falls back to its own everywhere else.

## Credits & license

Design, CSS and behaviour by [CJ Quines](https://cjquines.com/), from
[cjquines/hexo-theme-ascent](https://github.com/cjquines/hexo-theme-ascent);
the port changes nothing user-visible. The Zola port keeps the same licence
and attribution.

[MIT](LICENSE) © 2020 Carl Joshua Quines, 2026 Barkure.
