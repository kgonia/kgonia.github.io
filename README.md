# kgonia.github.io

Personal site of Krzysztof Gonia — built with [Jekyll](https://jekyllrb.com/)
and styled after the [AcademicPages](https://github.com/academicpages/academicpages.github.io)
theme. Deployed automatically by GitHub Pages.

## Structure

```
_config.yml            Site + author config (name, bio, social links, nav)
_layouts/              default.html, talk.html
_includes/             head, masthead (top nav), sidebar (profile), footer
_talks/                One Markdown file per talk (a Jekyll collection)
assets/css/style.css   Styles
assets/images/         profile.svg placeholder (swap in your own photo)
index.html             About / home page
talks.html             Talks listing
projects.md            Projects page
support.md             Support page
```

## Customise

Edit `_config.yml`:

- `author.avatar` — replace `profile.svg` with your own `profile.jpg` in `assets/images/`.
- `author.email`, `author.linkedin`, `author.twitter` — fill in your handles
  (leave a value blank to hide that link). `author.github` is set to `kgonia`.
- `navigation` — add/reorder top-menu items.

## Add a talk

Create `_talks/YYYY-MM-DD-slug.md`:

```yaml
---
title: "My Talk Title"
date: 2025-01-15
venue: Conference Name
description: One or two sentence summary.
---

Body text, plus an embed if you have slides:

<div class="talk-embed">
  <iframe src="https://docs.google.com/presentation/d/e/.../embed" allowfullscreen loading="lazy"></iframe>
</div>
```

## Run locally

```bash
bundle install
bundle exec jekyll serve
# open http://localhost:4000
```
