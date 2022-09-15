---
layout: page
title: Poems
permalink: /poems/
---

<ul class="post-list">
  {%- for poem in site.poems -%}
  <li>
  <p><a href="{{ poem.url }}"><img src="{{poem.image}}" height="100"></a></p>
  <h3>
      <a class="post-link" href="{{ poem.url | relative_url }}">
      {{ poem.title | escape }}
      </a>
  </h3>
  {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
  <span class="post-meta">{{ poem.date | date: date_format }}</span>
  {{ poem.content }}
  </li>
  {%- endfor -%}
</ul>