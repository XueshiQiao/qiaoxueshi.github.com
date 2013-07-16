---
layout: page
title: 代码手工艺人
tagline: Java & iOS Dev
---
{% include JB/setup %}

###My Blog
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

###Contact me
Weibo : [@拓词Joey ](http://weibo.com/2js3)  
Twitter : [@XueshiQiao](https://twitter.com/XueshiQiao)  
Email : [qiaoxueshi#gmail.com](mailto:qiaoxueshi@gmail.com)  

