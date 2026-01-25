---
title: Home
layout: home
nav_order: 1
description: "The homepage to the auerbenji github project."
permalink: /
---

# Welcome, stranger!
{: .no_toc }

On this blog, I write about my life.
{: .fs-6 .fw-300 }

---

[Add me on LinkedIn]{: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View my GitHub]{: .btn .fs-5 .mb-4 .mb-md-0 }

---

## What to expect

This site  is a personal Jekyll-based documentation site. It serves as a repository of my writings, technical projects, publications, blog posts, and miscellaneous utilities.
The **[About me]** section introduces me and outlines my background and interests.
The **[Projects]** section holds optimization work and code related to real-world problems.
**[Publications]** lists academic work.
The **[Blog]** section contains personal posts including essays and recipes.
Lastly, the **[Recipes]** section contains recipes that I cook rephrased for easier following.

{: .note }
> The work and views presented on this page reflect my individual position and are provided in a personal capacity. They are independent of, and not affiliated with any roles, mandates, or functions that I currently hold or may pursue in the future, whether referenced in the [diagramm] or elsewhere. The affiliations shown in the [diagramm] are for orientation purposes only.

## What not to expect

This blog is not a platform for political views, activism, or ideological positioning. It does not express consent, dissent, or commentary regarding my current or past professional roles, projects, or affiliations. It also does not aim for narrative storytelling or aesthetic presentation; it follows the maxim that content dominates style, favoring analytical structure over prose, with few to no images. Feel free to reach out to me, whenever you come to believe I deviate from my own principles.

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"></a>
  </li>
{% endfor %}
</ul>

[^1]: The [source file for this page] uses all three markup languages.

[^2]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).


[diagramm]: {% link docs/about.md %}#affiliations
[About me]: {% link docs/about.md %}
[Projects]: {% link docs/projects/index.md %}
[Blog]: {% link docs/blog/index.md %}
[Publications]: {% link docs/publications/index.md %}
[Recipes]: {% link docs/recipes/index.md %}

[Add me on LinkedIn]: https://www.linkedin.com/in/auerbenjamin/
[View my GitHub]: https://github.com/auerbenji