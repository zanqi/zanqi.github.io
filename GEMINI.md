# Project Context: Critical Section (Zanqi's Personal Website)

## Overview
This project is a personal website and blog titled "Critical Section" hosted on GitHub Pages using [Jekyll](https://jekyllrb.com/). It serves as a platform for the author (Zanqi Liang) to share learning notes, research, and a CV.

## Architecture
*   **Framework:** Jekyll (Static Site Generator)
*   **Templating:** Liquid
*   **Styling:** SASS/SCSS
*   **Hosting:** GitHub Pages
*   **Math Rendering:** KaTeX (enabled via `_config.yml` and `_layouts/default.html`)

## Key Directories & Files
*   `_config.yml`: Main configuration file (site title, author, plugins, settings).
*   `Gemfile`: Ruby dependencies (`jekyll`, `webrick`, `jekyll-feed`).
*   `_layouts/`: HTML templates for different page types (`default`, `page`, `post`).
    *   `default.html`: Base layout including `<head>`, navigation, and footer.
*   `_includes/`: Reusable HTML partials (`menu`, `sidebar`, `meta`).
*   `_posts/`: Blog posts written in Markdown. File name format: `YYYY-MM-DD-title.md`.
*   `_sass/`: SASS partials for styling.
*   `assets/`: Static assets (CSS, fonts, images, JS libraries like KaTeX).
*   `index.md`: Homepage content.
*   `README.md`: Content for the "About" page (not a standard project README).

## Building and Running
To run the site locally, ensure you have Ruby and Bundler installed.

1.  **Install Dependencies:**
    ```bash
    bundle install
    ```

2.  **Serve Locally:**
    ```bash
    bundle exec jekyll serve
    ```
    The site will be available at `http://localhost:4000`.

## Development Conventions
*   **Content:** Write new posts in `_posts/` using Markdown.
*   **Front Matter:** Ensure every Markdown file has YAML front matter defining `title`, `layout`, etc.
*   **Math:** MathJax/KaTeX is enabled. Use standard LaTeX syntax for equations.
*   **Images:** Store images in `images/` or `assets/` and reference them in Markdown.
*   **Styling:** Modify SASS files in `_sass/` for style changes.
