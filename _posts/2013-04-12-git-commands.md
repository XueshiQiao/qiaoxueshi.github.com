---
layout: post
title: "Git命令备忘"
description: ""
category: Tech
tags: [git]
---
{% include JB/setup %}

##使用diff查看文件更改信息
{% highlight shell linenos %}
查看未暂存文件的变化（与最近一次的暂存/提交比较）
$ git diff
查看已暂存文件的变化（与最近一次提交比较）
$ git diff --cached
查看与版本库中任一版本的变化
$ git diff 3e4e
查看任意两个版本间的变化
$ git diff 3e4e 5d5a
具体到某个文件
$ git diff 3e4e 5d5a index.md
{% endhighlight %}
##查看任意版本下的某个文件
//查看某个版本下某个文件内容
$ git show 5d5a index.md
