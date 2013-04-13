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
weibo:[@学士_](http://weibo.com/2js3)

email:qiaoxueshi#gmail.com

<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" id="UYScript" src="http://v1.uyan.cc/js/iframe.js?UYUserId=1736636" async=""></script>
<!-- UY END -->

