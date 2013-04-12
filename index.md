---
layout: page
---
{% include JB/setup %}

###My Blog
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

###Contact me
weibo:[@学士_](http://weibo.com/2js3)

email:qiaoxueshi#gmail.com

