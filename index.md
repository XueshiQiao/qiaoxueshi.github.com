---
layout: page
title: 代码手工艺人
tagline: 关注Java服务端和iOS开发
---
{% include JB/setup %}

###My Blog
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

###Contact me
weibo:@学士_

email:qiaoxueshi#gmail.com

