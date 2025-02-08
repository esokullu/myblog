---
layout: default  # or "home" if using a theme-provided home layout
title: "Welcome"
---
<ul>
  {% for post in site.posts %}
    <li><a href="/{{site.baseurl}}/{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
