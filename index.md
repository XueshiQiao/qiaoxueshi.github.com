---
layout: page
title: 代码手工艺人
tagline: Java & iOS Dev
---
{% include JB/setup %}

###My Blog
<ul>
  {% for post in site.posts %}
    <li style="list-style:none; margin-bottom:3px;">
    	<div style="text-align:left; display:block; width:90px; float:left;">
    		<span>{{ post.date | date_to_string }}</span> 
    	</div>
    	<span>&raquo;</span>
    	 
    	<a style="margin-left:3px;" href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

###Contact me
<ul>
	<li style="list-style:none; margin-bottom:3px;">Weibo : <a href="http://weibo.com/2js3">@拓词Joey</a>  </li>
	<li style="list-style:none; margin-bottom:3px;">Twitter : <a href="https://twitter.com/XueshiQiao">@XueshiQiao</a>  </li>
	<li style="list-style:none; margin-bottom:3px;">Email : qiaoxueshi#gmail.com</li>
</ul>

