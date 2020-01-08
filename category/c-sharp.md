---
layout: default
---
<h1 class="page-heading">Categorie: C Sharp</h1>
<ul class="post-list">
  {%- for post in site.categories.C-Sharp -%}
    <li>
      {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
      <span class="post-meta">{{ post.date | date: date_format }}</span>
      <h3><a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
      {%- if site.show_excerpts -%}
        {% if post.excerpt %}{{ post.excerpt | strip_html | strip_newlines | truncate: 160 }}{% endif %}
      {%- endif -%}
    </li>
  {%- endfor -%}
</ul>