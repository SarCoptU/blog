---
title: "Hugo Cheatsheet"
date: 2026-01-16T12:31:43Z
tags: ["Hugo", "webdev", "cheatsheet"]
---
# Hugo Cheatsheet

A quick reference for the most useful Hugo commands, flags, and tips.

---  

## Core Commands

| Command | Description |
|---------|-------------|
| `hugo new site <directory>` | Create a new Hugo site in the specified directory. |
| `hugo new <section>/<filename>.md` | Add a new content file (e.g., `hugo new posts/my-first-post.md`). |
| `hugo server` | Run a local development server with live reload (defaults to `http://localhost:1313`). |
| `hugo server -D` | Include drafts (`draft: true`) when serving locally. |
| `hugo` | Build the site for production (outputs to `public/`). |
| `hugo -d <output-dir>` | Specify a custom output directory for the generated site. |
| `hugo --minify` | Minify HTML/CSS/JS during the build for smaller payloads. |
| `hugo --gc` | Run garbage collection to remove unused files from the cache. |
| `hugo version` | Show the installed Hugo version. |
| `hugo env` | Display environment variables (useful for debugging). |

---  

## Useful Flags & Options

| Flag | Purpose |
|------|---------|
| `-s, --source <path>` | Set the working directory (useful when running from outside the site root). |
| `-b, --baseURL <url>` | Override the site’s base URL (good for staging environments). |
| `-t, --theme <name>` | Force a specific theme (overrides the one set in `config.toml`). |
| `--cleanDestinationDir` | Delete everything inside the destination folder before building. |
| `--ignoreCache` | Skip the cache and reprocess all files (helps when you suspect stale data). |
| `--verbose` | Show detailed processing information (great for troubleshooting). |
| `--logLevel <level>` | Control log output (`debug`, `info`, `warn`, `error`). |

---  

## Content Management Tips

1. **Front Matter**  
   - Use YAML, TOML, or JSON at the top of each content file.  
   - Common fields: `title`, `date`, `draft`, `tags`, `categories`, `slug`.

2. **Drafts & Future Posts**  
   - Set `draft: true` or a future `date` to keep a page unpublished until you’re ready.  
   - Serve drafts locally with `hugo server -D`; they won’t appear in production builds.

3. **Taxonomies**  
   - Define custom taxonomies (e.g., `tags`, `categories`, `authors`) in `config.*`.  
   - Access them in templates via `.Site.Taxonomies.<name>`.

4. **Shortcodes**  
   - Reusable snippets like <code>{{&lt; figure src="image.jpg" &gt;}}</code> simplify embedding media.  
   - Create custom shortcodes in `layouts/shortcodes/`.

---  

## Theme Development Quick‑Start

1. **Create a Theme Skeleton**  
   ```bash
   hugo new theme mytheme
   ```

2. **Directory Layout**
    - `layouts/` – HTML templates.
    - `static/` – Assets (CSS, JS, images).
    - `assets/` – SCSS/JS processed by Hugo Pipes.
    - `exampleSite/` – Sample site to test the theme.

3. **Enable the Theme**
In `config.toml` (or `.yaml/.json`):
    ```bash
    theme = "mytheme"
    ```

4. **Live‑Reload While Developing**
Run `hugo server -t mytheme` to preview changes instantly.

---

## Handy Tips & Tricks

- **Partial Caching**: Wrap expensive partials with `{{ partialCached "partial.html" . "uniqueKey" }}` to improve build speed.

- **Image Processing**: Use Hugo Pipes for resizing, cropping, and optimizing images on the fly:
```bash
{{ $img := resources.Get "images/photo.jpg" }}
{{ $thumb := $img.Fit "300x200" }}
<img src="{{ $thumb.RelPermalink }}" alt="Thumbnail">
```

- **Multilingual Sites**: Define languages in `config.*` and use `.Lang` in templates to switch content.
- **Deploy with GitHub Actions**: A typical workflow runs `hugo` to generate the site, then pushes the `public/` folder to the `gh-pages` branch.
- **Debugging**: Insert `{{ printf "%#v" . }}` in a template to dump the current context and inspect available variables.

**Blogpost written with AI**
