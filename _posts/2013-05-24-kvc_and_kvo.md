---
layout: post
title: "KVC and KVO"
description: ""
category: 
tags: [iOS]
---
{% include JB/setup %}

##KVC 
KVC是Key-value coding的缩写，是一种通过key-value的方式获取对象属性的机制。
这个key是一个String的唯一标示符，这个key的name约定是必须是ASCII码、小写字母开头、中间不能有空格。

让一个类实现KVO的方式是遵循NSKeyValueCoding这个协议，该协议中定义了2个方法：```valueForKey:``` and ```setValue:forKey:```.这两个方法用来通过key访问和获取对象属性。

[More about KVC](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/KeyValueCoding/Articles/KeyValueCoding.html#//apple_ref/doc/uid/10000107i) 


##KVO

[Apple  Document about KVO](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)

[KVO的实现方式:isa-swizzing](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueObserving/Articles/KVOImplementation.html#//apple_ref/doc/uid/20002307-BAJEAIEE)

[Lightweight Key-Value Observing](http://chris.eidhof.nl/post/63590250009/lightweight-key-value-observing)





