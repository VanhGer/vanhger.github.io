---
layout: page
title: BLOG
permalink: /blog/
---

<ul>
  {% for post in site.blogposts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>