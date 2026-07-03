---
layout: page
title: Projects
permalink: /projects/
---

<ul class="post-list">
  {%- for project in site.project_posts -%}
  <li>
    <span class="post-meta">{{ project.date | date: "%b %-d, %Y" }}</span>
    <h3>
      <a class="post-link" href="{{ project.url | relative_url }}">
        {{ project.title | escape }}
      </a>
    </h3>
    {{ project.excerpt }}
  </li>
  {%- endfor -%}
</ul>
