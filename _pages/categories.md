---
layout: single
title: "Categories"
permalink: /categories/
---

<h1>📂 Categories</h1>

<ul>
  {% for category in site.categories %}
    <li>
      <a href="/categories/{{ category[0] | slugify }}/">
        {{ category[0] | capitalize }} ({{ category[1].size }})
      </a>
    </li>
  {% endfor %}
</ul>
