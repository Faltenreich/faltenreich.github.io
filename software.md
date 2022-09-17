---
layout: page
title: Software
permalink: /software/
---

<div class="software-list">
  {%- for software in site.data.software -%}
  <div class="software" style="background-color:{{ software.color }};">
    <div class="post-meta">{{- software.platform -}}</div>
    <div><a href="{{ software.url }}"><h2>{{- software.name -}}</h2></a></div>
    <div><a href="{{ software.url }}"><img src="{{- software.image -}}"></a></div>
    <div>{{- software.summary -}}</div>
  </div>
  {%- endfor -%}
</div>