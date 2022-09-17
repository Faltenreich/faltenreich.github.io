---
layout: page
title: Software
permalink: /software/
---

Honesty, transparency and community thinking. These three terms describe important values I try to live and work by. For this reason I publish many of my private projects publicly. For me Open Source describes the entrance into a community, for which there are no temporal and spatial barriers.

I have conceptualized, developed, maintained and sometimes published following software in my spare time. My main focus is the development for mobile platforms but I am also experienced in developing small backends or setting up a Backend-as-a-service.

I live and write by Clean Code ♥

<div class="software-list">

  {%- for software in site.data.software -%}

  <div class="software" style="background-color:{{ software.color }};">

    <div>{{- software.platform -}}</div>

    <div>
    {% if software.url %}
    <a href="{{ software.url }}" style="color:white;">
    <h2>{{- software.name -}}</h2>
    </a>
    {% else %}
    <h2>{{- software.name -}}</h2>
    {% endif %}
    </div>

    {% if software.image %}
    <div class="software-image">
    {% if software.url %}
    <a href="{{ software.url }}">
    <img src="{{- software.image -}}">
    </a>
    {% else %}
    <img src="{{- software.image -}}">
    {% endif %}
    </div>
    {% endif %}

    <div>{{- software.summary -}}</div>

  </div>

  {%- endfor -%}

</div>