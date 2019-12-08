---
layout: default
---
<br>
<h3>Categorie: Ordinary Life</h3>
<br>
<hr>

<ul class="article-list">
  {% for post in site.categories.Ordinary-Life %}
    <li class="article-list-item">
      <a href="{{ post.url | prepend: site.baseurl }}" title="{{ post.title }}">
        <h5>{{ post.title }} <span class="icon icon-arrow-right"></span></h5>
      </a>
      <p>{% if post.excerpt %}{{ post.excerpt | strip_html | strip_newlines | truncate: 160 }}{% endif %}</p>
      <div class="article-list-footer">
        <span class="article-list-date">{{ post.date | date: "%Y년 %-m월 %-d일" }}</span>
        <span class="article-list-divider">-</span>
        <div class="article-list-tags">
          <a href="/category/{{ post.category | slugify | prepend: site.baseurl }}.html">{{ post.category | replace:'-', ' ' }}</a>
        </div>
      </div>
    </li>
  {% endfor %}
</ul>
