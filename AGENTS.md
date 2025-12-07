# Repository Guidelines

## Project Structure & Module Organization
This site is a customized fork of the al-folio Jekyll theme. Site-level metadata and toggles live in `_config.yml`, while navigation/data models live in `_data/*.yml`. Readable content is grouped by type: `_pages` for standalone screens, `_posts` for blog entries sorted by `YYYY-MM-DD-title.md`, `_projects` and `_news` for cards, and `_bibliography` for BibTeX inputs. Layouts, includes, and resume partials sit in `_layouts/` and `_includes/`. Styling resides in `_sass/` and `assets/css`, scripts in `assets/js`, and media under `assets/{img,video,audio}`. Tooling scripts live in `bin/` alongside the compose files.

## Build, Test, and Development Commands
Use Docker for a dependency-free dev loop (auto reload on `http://localhost:8080`):
```
docker compose pull
docker compose up
docker compose -f docker-compose-slim.yml up
```
Native Ruby workflow:
```
bundle install && npm install
bundle exec jekyll serve --livereload --incremental --port 4000
bundle exec jekyll build
bin/cibuild
npx prettier --check .
```

## Coding Style & Naming Conventions
Prettier (`@shopify/prettier-plugin-liquid`) is authoritative for Liquid, Markdown, and SCSS—run `npx prettier --write .` before committing. Keep indentation at two spaces in templates and avoid tabs. Use `kebab-case` file names in assets and `_posts/` dates for ordering. YAML front matter should declare `layout`, `title`, and `permalink`; data keys favor `snake_case` to mirror `_data`. CSS variables belong in `_sass/_variables.scss`, while component overrides go in `_sass/_layout.scss` or section partials.

## Testing Guidelines
Every change must at least pass `bin/cibuild` (or `docker compose run --rm jekyll bundle exec jekyll build`) to ensure the static site compiles without Liquid errors. When editing search scripts or plugins, run `bundle exec jekyll serve` locally and spot-check affected URLs plus RSS output to catch runtime JS issues. Image-heavy updates should confirm optimized assets exist in `assets/img` and that generated HTML remains Lighthouse-clean; run Lighthouse locally only when you touch layout or asset loading.

## Commit & Pull Request Guidelines
Recent commits (e.g., `Revise index.html for new layout and profile details`) use short, imperative subjects without trailing punctuation; keep that style and group related file edits together. Reference an issue number in the body when applicable and describe configuration impacts explicitly. PRs should include a summary paragraph, screenshots or GIFs for visual tweaks, the commands you ran (`docker compose up`, `bin/cibuild`, `npx prettier --check`), and notes on docs/data migrations (e.g., `_data/socials.yml`). Prefer small, reviewable commits and let the deploy workflow rebuild `gh-pages`.
