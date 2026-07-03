---
layout: post
title:  "How to: Separating Jekyll posts"
date:   2026-01-05 12:58:37 +0100
categories: how-to github
---

By default, Jekyll displays all posts stored in the `_posts` folder of your GitHub repository. But what if you want to organize posts into separate folders?

Let’s say your site has a Home tab and a Projects tab. On Home, you want to show your regular articles, while on Projects, you only want to list posts related to a specific project. You may also want to separate these files in your repository so your daily posts and project posts are not stored together.

## Define collection in `_config.yml`

```yaml
collections:
  project_posts:
    output: true
    permalink: /projects/:path/
```

- `output: true`: Ensures that Jekyll generates a separate HTML page for each entry (just like with regular posts).
- `permalink`: Determines the URL structure—the entries will be located here, for example, under `/projects/my-project/`

## Create folder

```text
_project_posts/
├─ 2025-12-27-my-first-project.md
├─ 2026-01-15-my-second-project.md
```

**Important**: The folder name must begin with an underscore and match the collection name exactly: `_project_posts` (not `_project-posts` with a hyphen. Jekyll does not allow hyphens in collection names, only underscores and letters).

Unlike with `_posts`, the date in the filename is optional for collections, you can simply name the files `my-project.md` if you don’t need them sorted chronologically.

## Front matter in files

```yaml
---
layout: post
title: "My first Projekt"
date: 2025-12-27
---

Lorem Ipsum Yada Yada here...
```

## List `_project_posts` in `projects.md`

```markdown
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
```

`site.project_posts` is automatically available as soon as the collection is defined in `_config.yml`.

## Advantages Over the Category Solution

- Completely separate directory structure—no mixing with `_posts`.
- Custom URL structure (`/projects/`... instead of `/2025/12/27/`...).
- You can assign custom layouts, a custom front matter schema, and custom sorting logic to the collection without affecting the regular blog posts.
- `site.posts` (your `index.md`) remains unaffected—project entries do not automatically appear there, which is exactly what you want.
