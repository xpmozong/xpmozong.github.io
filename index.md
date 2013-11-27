---
layout: page
title: 寞踪
tagline: free as wind
---
{% include JB/setup %}


<div class="posts-list">
    <h3>文章列表</h3>
    <ul class="posts">
        {% for post in site.posts %}
            <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
        {% endfor %}
    </ul>
</div>