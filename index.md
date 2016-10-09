---
layout: page
title: Han Chen's Blog
tagline: Another learner
---
{% include JB/setup %}

## Welcome! This is Han Chen's Blog

### Recent blogs
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


