# survibecoding

Minimal Mistakes blog source for GitHub Pages.

## Authoring

Local preview requires Ruby 4.0.5. The GitHub Pages workflow uses the same Ruby version.

Create a new post:

```bash
bin/new-post "My Post Title"
```

Edit the generated Markdown file under `_posts/`, then preview locally:

```bash
bundle install
bundle exec jekyll serve --livereload
```

The site is published by the GitHub Actions workflow in `.github/workflows/pages.yml`.
In the repository settings, configure GitHub Pages to use **GitHub Actions** as the source.

## Content Layout

- `_posts/` contains dated blog entries.
- `_pages/` contains standing pages such as About, Categories, and Tags.
- `_data/navigation.yml` controls the top navigation.
- `_config.yml` controls site-wide Minimal Mistakes settings and post defaults.
