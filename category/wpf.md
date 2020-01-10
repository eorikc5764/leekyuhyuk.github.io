---
title: "Category : WPF"
layout: default
---
{%- if site.posts.size > 0 -%}
<ul class="posts">
  <li>
    <h1 id="posts-label">{{ page.title }}</h1>
  </li>

  {%- for post in site.categories.WPF -%}
  <li>
    {%- assign date_format = site.plainwhite.date_format | default: "%b %-d, %Y" -%}
    <a class="post-link" href="{{ post.url | relative_url }}">
      <h2 class="post-title">{{ post.title | escape }}</h2>
    </a>
    <div class="post-meta">
      <ul class="post-categories">
        <li>
          <a class="category" href="/category/{{ post.category | slugify | prepend: site.baseurl }}.html">{{ post.category | replace:'-', ' ' }}</a>
        </li>
      </ul>
      <div class="post-date">
        {{ post.date | date: date_format }}</div>
    </div>
    <div class="post">
      {%- if site.show_excerpts -%}
      {% if post.excerpt %}{{ post.excerpt | strip_html | strip_newlines | truncate: 160 }}{% endif %}
      {%- endif -%}
    </div>
  </li>
  {%- endfor -%}
</ul>

<!-- <p class="feed-subscribe"><svg class="svg-icon orange">
		<use xlink:href="{{ '/assets/minima-social-icons.svg#rss' | relative_url }}"></use>
	</svg><a href="{{ "/feed.xml" | relative_url }}">Subscribe</a></p> -->
{%- endif -%}