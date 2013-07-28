---
layout: post
title: "Jekyll设置友言为社会化评论组件"
description: ""
category: Jekyll
tags: [Jekyll]
---
{% include JB/setup %}
Jekyll默认的社会化评论组件是disqus，第三方SNS是facebook，twitter等，不方便大陆用户使用，发现国内也有类似的社会化评论组件，比如友言等，经比较发现友言更简单易用。

替换的整个过程很简单，分为两大步：
首先要注册一个友言的账户，点击获取代码，就能获得一段和你用户相关的js代码。类似下面这样：

{% highlight html %}
<!-- UY BEGIN -->
<div id="uyan_frame"></div>
<script type="text/javascript" id="UYScript" src="http://v1.uyan.cc/js/iframe.js?UYUserId=YOUR_USER_ID" async=""></script>
<!-- UY END -->
{% endhighlight %}

然后要切换到本地来，由于Jekyll的评论组件是插件式的，很方便修改，分为下面2个步骤

1. 修改_config.yml文件中comments:下的provider:的值为custom（默认是disqus）
2. 在_includes目录下新建一个目录custom,在custom目录下新建一个文件comments，文件的内容就是上面从友言获得的那段代码。 


push到GitHub，刷新页面查看效果吧

这么做的原理很简单，看一下youname.github.com/_includes/JB/comments文件的
看最后一个when语句，当site.JB.comments.provider的值为custom时，就加载custom/comments文件，那么其实site.JB.comments.provider的值就是刚才在_config.yml中设置的那个provider，这样就能说的通了。


Have fun!











