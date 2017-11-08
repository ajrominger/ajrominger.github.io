---
layout: page
title: Notebook
menu: main
permalink: /notebook/
weight: 3
---

<ul class="post-list">
  {% for post in site.posts %}
    <li>
      {{ post.date | date: "%b %-d, %Y" }}
      <h2>
      <a href="{{ post.url }}">{{ post.title }}</a>
      </h2>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
