---
layout: default
---

<header class="page-header">
  <h1 class="page-title">카테고리: Linux</h1>
</header>
{% for post in site.categories.Linux %}
<article class="post type-post status-publish format-standard hentry">
  <header class="entry-header">
    <h2 class="entry-title">
      <a href="{{ post.url | prepend: site.baseurl }}" rel="bookmark">{{ post.title }}</a>
    </h2>
  </header>
  <div class="entry-content">
    <p>{{ post.content | strip_html | truncatewords: 160 }}  <a href="{{ post.url | prepend: site.baseurl }}" class="more-link"><span class="screen-reader-text">{{ post.title }}</span> 더보기</a></p>
  </div>
  <footer class="entry-footer">
    <span class="posted-on">
      <span class="screen-reader-text">작성일자 </span>
      <a href="{{ post.url | prepend: site.baseurl }}" rel="bookmark">
        <time class="entry-date published" datetime="{{ post.date }}">{{ post.date | date: "%Y년 %-m월 %-d일" }}</time>
        <time class="updated" datetime="{{ post.date }}">{{ post.date | date: "%Y년 %-m월 %-d일" }}</time>
      </a>
    </span>
    <span class="cat-links">
      <span class="screen-reader-text">카테고리 </span>
      <a href="/category/{{ post.category | slugify | prepend: site.baseurl }}.html" rel="category">{{ post.category | replace:'-', ' ' }}</a>
    </span>
  </footer>
</article>
{% endfor %}
