---
layout: page
title: Project demo
permalink: /blog/categories/projectdemo/
---

<h5> Posts by Category : {{ page.title }} </h5>

<div class="card">
{% for post in site.categories.projectdemo %}
 <li class="category-posts"><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="/info/{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</div>
