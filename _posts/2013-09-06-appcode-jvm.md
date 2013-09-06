---
layout: post
title: "AppCode JVM参数优化"
image: ""
image-width: ""
image-height: ""
description: ""
category: 
tags: []
---
{% include JB/setup %}

昨晚花了2个小时熟悉了一下AppCode,和IDEA系列给人的感觉一样：很卡很强大。就打算优化一下JVM的设置，AppCode的JVM参数配置文件在 `/Applications/AppCode EAP.app/bin/idea.vmoptions`

使用默认的参数，用一段AppCode，观察了一下GC的情况:


	➜  ~  jstat -gcutil 50991 1s
	  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT
	 79.31   0.00  37.61  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.63  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.65  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.66  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.67  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.69  88.64  60.84   6654   57.031   137    3.017   60.048
	 79.31   0.00  37.70  88.64  60.84   6654   57.031   137    3.017   60.048


发现YoungGC有6654次，可以说是非常多，耗时57s，FullGC有137次，3s多，花在GC上的时间有60s，按每次卡一次1s的话，只是GC就让人感觉到60次明显卡顿，确实让人受不了。

查了一下默认的参数，内存设置的太保守，所以我改成了下面这个方案:
的配置是8G内存，我给AppCode分配1.5G

	-Xms1500m
	-Xmx1500m
	-XX:NewSize=600m  
	-XX:MaxNewSize=600m
	-XX:SurvivorRatio=8
	-XX:PermSize=200m
	-XX:MaxPermSize=400m
	-XX:ReservedCodeCacheSize=96m
	-XX:+UseCompressedOops
	-XX:+DisableExplicitGC

使用后：

	➜  jstat -gcutil 58835 1s

	  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066
	 61.70   0.00  48.84  15.60  52.92     12    1.066     0    0.000    1.066

YGC降低到了12次,GC时间是1s，没有FullGC,没有感觉到卡顿的情况。

这个主要是从内存分配方面优化，GC算法上也可以优化，但是需要多测试每种GC算法的情况，也可能会因人而异，等我慢慢找到一个不错的方案再分享出来。

至于上面参数的意思，可以查看我在iteye上以前的一篇Blog:[10s启动MyEclipse/Eclipse的JVM参数（含Mac下）](http://287854442.iteye.com/admin/blogs/1159689)