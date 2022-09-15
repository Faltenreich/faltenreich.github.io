---
layout: page
title: Poems
permalink: /poems/
---

<ul class="post-list">
  {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
  {% for poem in site.poems %}
  <li>
  <p><a href="{{ poem.url }}"><img src="{{ poem.header }}"></a></p>
  <span class="post-meta">{{ poem.date | date: date_format }}</span>
  <h3>
      <a class="post-link" href="{{ poem.url | relative_url }}">
      {{ poem.title | escape }}
      </a>
  </h3>
  </li>
  {% endfor %}
</ul>