---
layout: post
title: "动态加载FLEX的越狱插件 - FLEXLoader"
image: "/assets/resources/2014-08-12-tweak-flexloader.png"
image-width: ""
image-height: ""
description: ""
category: 
tags: [iOS]
---
{% include JB/setup %}

##介绍
[FLEXLoader](https://github.com/Flipboard/FLEX)是一个我在上周末写的一个可以动态加载FLEX的开源越狱插件，它以加载动态库的方式注入到系统App和用户的App中(欢迎使用star, fork, clone等一切方法蹂躏我~~)。FLEX全称是"Flipboard Explorer"，是Flipboard团队开发一组调试和探测App的开源工具，功能非常强大，比如查看和修改View的层级结构，查看和修改堆内存中的对象信息等等，更多FLEX介绍和使用信息参考[这里](https://github.com/Flipboard/FLEX)。

FLEXLoader参考了[RevealLoader](https://github.com/heardrwt/RevealLoader)，顾名思义，它是一个加载Reveal动态库的越狱插件，是一款非常方便的插件，如果你经常用Reveal来查看和调试，一定不要错过。我把它的源码做了一些修改，把Reveal的动态库改成了FLEX的动态库，因为FLEX官方只提供了源代码，所以我参考了Tony的这篇[文章](http://itony.me/774.html)编译了一个动态库，如有有兴趣，也可以直接用我已经构建好的Xcode工程[FLEXDynamicLibProject](https://github.com/qiaoxueshi/FLEXDynamicLibProject)来编译。

##安装FLEXLoader
有下面两种安装方式：  
1) 在Cydia中搜索`Flipboard FLEX loader`并安装(BigBoss源)  
2) 如果安装有越狱的开发环境，比如theos，可以自己来编译安装，配好环境变量后，`make package install`一下(也可以自己编译FLEX的动态库替换掉工程中的`FLEXDylib.dylib`).


##使用方法
安装后，打开“设置”-> "FLEXLoader"->“Enabled Applications”, 勾选上你想要注入FLEX的App，打开App就能看到FLEX的身影了，简直不能再简单了，:]

##后记
写完这个tweak后，不敢也不能独享，心怀忐忑地放到了GitHub上，然后就打算放到Cydia上。Cydia的诸多源中，感觉BigBoss最值得信赖一点，所以就打算传到BigBoss上，后来证明这个选择是非常正确的。从搜索BigBoss的网址，到填写表单上传完成，前后不到10分钟，甚至都没要求我注册，这个体验还是蛮爽的。

BigBoss承诺24小时之后会处理，到了第二天，BigBoss的审核员[@0ptimo](https://twitter.com/0ptimo)就给我发邮件，说tweak被拒掉了，原因是我没有把FLEX的license加上，这个确实是我疏忽了，我把RevealLoader的license加上却忘了FLEX的，于是就速度加上，然后名字和现有的一个叫Flex比较相似，建议我改一下名字，还有一些细节比如icon的名字直接叫icon.png容易被别人覆盖掉，动态库的位置放到`/Library/Application Support/FLEXLoader`比较好等等。我表示了感谢，然后都一一修改之后提交，过了不到一天就通过审核了。

如果你有好的想法或者问题，欢迎PR或者联系我. 最后感谢下面REF中的各位开源项目和文章的作者，他们才是创造者，我只是开源代码的组装工~~

##REF

* [FLEX](https://github.com/Flipboard/FLEX)
* [RevealLoader](https://github.com/heardrwt/RevealLoader)
* [iOS 使用 Flipboard/FLEX 分析第三方 App](http://itony.me/774.html)
* [Xcode4.6创建和使用iOS的dylib动态库](http://blog.csdn.net/hursing/article/details/8951958)

欢迎小伙伴在[微博](http://weibo.com/2js3)上关注我, :]，Enjoy! 

