---
title: Launching Jekyll and Kramdown
parent: Utilities
---

# Launching Jekyll
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Run Jekyll on local host

Clearing the jekyll cache when running locally
```bash
bundle exec jekyll clean
```
Running the page on auto-update
```bash
bundle exec jekyll serve --livereload
```
Pushing an empty commit to github to trigger rebuild
```bash
git commit --allow-empty -m "trigger rebuild"
git push origin main
```
Open in your web browser
```bash
http://localhost:4000/
```

# Kramdown $$\LaTeX$$ syntax

## Inline
```yaml
# The equation is displayed inline when using double dollar signs not separated by a spaceline.
$$E = mc^2$$
```
creates $$E = mc^2$$.

## Line break

{: .note }
The following equation is separeted by a spaceline, so its shown large and centered/middle orientated

```yaml
# The equation is displayed with a line break when using line break after double dollar signs.
$$
\int_{a}^{b} f(x)\, dx = F(b) - F(a)
$$
```
creates

$$
\int_{a}^{b} f(x)\, dx = F(b) - F(a)
$$
