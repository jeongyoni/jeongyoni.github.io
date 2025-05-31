---
layout: single
title: "Categories"
permalink: /categories/
---

<h1>📂 Categories</h1>

<ul>
  {% assign sorted_categories = site.categories | sort %}

  {% for category in sorted_categories %}
    {% assign cat_name = category[0] %}

    {% if cat_name == "projects" %}
      <li>
        <strong>📁 Projects</strong> ({{ category[1].size }})
        <ul>
          <li>
            <a href="/categories/projects/">전체 보기</a>
          </li>
          {% for sub in sorted_categories %}
            {% if sub[0] == "stock-prediction" %}
              <li>
                <a href="/categories/stock-prediction/">📈 Stock Prediction ({{ sub[1].size }})</a>
              </li>
            {% endif %}
          {% endfor %}
        </ul>
      </li>
    {% elsif cat_name != "stock-prediction" %}
      <li>
        <a href="{{ '/categories/' | append: cat_name | slugify | append: '/' }}">
          {{ cat_name | capitalize }} ({{ category[1].size }})
        </a>
      </li>
    {% endif %}
  {% endfor %}
</ul>
