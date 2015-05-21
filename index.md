---
layout: page
title: 代码手工艺人
tagline: Java & iOS Dev
---
{% include JB/setup %}

###My Blog
<div>
{% for post in site.posts %}
  {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
  {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}

  {% if forloop.first %}
    <h4>{{this_year}}</h4>
    <ul>
  {% endif %}

  <li style="list-style:none; margin-bottom:3px; line-height: 1.7; letter-spacing:1px;font-size:16px;">
    <a style="margin-right:3px;" href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    <span style="font-size:12px; color:gray;">{{ post.date | date: "%Y-%m-%d" }}</span> 
  </li>

  {% if forloop.last %}
    </ul>
  {% else %}
    {% if this_year != next_year %}
      </ul>
      <h4>{{next_year}}</h4>
      <ul>
    {% endif %}
  {% endif %}
{% endfor %}
</div>

###Contact me
<ul style="line-height: 1.7; letter-spacing:1px; color:gray;">
	<li style="list-style:none; margin-bottom:3px;">Weibo : <a href="http://weibo.com/2js3">@JoeyBlue_</a>  </li>
	<li style="list-style:none; margin-bottom:3px;">Twitter : <a href="https://twitter.com/XueshiQiao">@XueshiQiao</a>  </li>
	<li style="list-style:none; margin-bottom:3px;">Email : qiaoxueshi#gmail.com</li>
</ul>

