---
layout: post
title: "WWDC 2013 视频英文字幕下载"
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}

不卖关子，这是一个[git repo](https://github.com/qiaoxueshi/WWDC_2013_Video_Subtitle) ，可以从这里下载到WWDC 2013公开的100个视频的英文字幕。
如果觉得有用的话，不妨star一下，或者在微博上[@我](http://weibo.com/2js3)满足一下我的虚荣心 :-)，这都不重要，重要的是一定要**坚持看完这100个视频**。

我发起这个项目以及抓取到这些字幕的的原因是这样的，一个是英语的听力太差，基本上听不懂苹果的传道士们在视频中说的是什么，没有字幕真是很难受，然后是发现在iPad上使用WWDC这个App看视频的时候是有字幕的，但在iPad上看屏幕不够大，看起来也很费劲。就想既然在iPad上有字幕，一定有办法抓取出来，于是就开工，用`burpsuite`之类的抓Http请求包的App很容易就能探测到字幕文件的地址，在准备写代码的时候，Google了一下，发现一个python写的[gist](https://gist.github.com/nriley/5874460)正是做这个的，于是就用这个脚本把一部分视频的字幕下载下来，自己又现学了点ruby写了个[gist](https://gist.github.com/qiaoxueshi/5992949)脚本来把分散的字幕文件按照顺序合并起来。 刚开始下载的比较慢，因为这个脚本是单线程的，后来自己改了一下，分10个线程，每个线程下载10个视频的字幕，这样就快很多，这个代码因为比较简单，就没放出来，有兴趣的童鞋自己也可以实现。

另外[@lexrus](https://gist.github.com/lexrus)同学的这个[gist](https://gist.github.com/lexrus/5792296)里提供了所有视频的HD和SD的版本，以及文件序号和视频名称的对应关系，可以直接放在迅雷里下载，完了再配上字幕，可以像欣赏好莱坞大片一样的欣赏WWDC2013带来的新技术盛宴了！


 
  
   
   
   





