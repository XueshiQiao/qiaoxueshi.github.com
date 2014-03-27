---
layout: post
title: "Run loop和Thread"
image: "/assets/resources/2014-03-28-runloop-vs-thread.png"
image-width: ""
image-height: ""
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}

###Run-loop是什么？
首先考虑这个问题：你的Cocoa程序大部分的时间什么都没做，更具体点，是在等待输入。然而，一旦你触摸屏幕，相应的事件被触发，就可能会执行你的一段代码。同理，socket中返回一些数据，或者计时器触发等也是一样的情况。而且更重要的是，一旦触发事件的代码执行完，程序就会回到等待状态。在很多情况下，代码执行的时间要远小于程序等待输入的时间。

我认为run loop就是较好的利用了这个事实的一种机制。一个run loop就是跑在单个线程上进行事件处理的循环。你在run loop上注册输入源，并指定当这些源有输入时应该执行的代码。当特定的源上有输入时，run loop就会执行对应的代码，然后继续等待下一个输入事件。如果在run loop正在执行处理代码时，另外一个源的输入到了，run loop会在执行完正当前的处理后处理这个输入事件。好处是虽然你不知道具体的输入顺序，但你知道它们最终会一个接一个地被串行处理。这就是说你不会遇到多线程的问题，这也是run loop非常有用的原因。

###和线程的关系？
每个线程，包括应用的主线程都有一个相关联的run loop对象，在应用中你不需要显式的创建run loop对象。在Carbon和Cocoa应用中，主线程会把自动设置和运行它的run loop作为应用启动过程的一部分。

###Run loop的使用
默认情况下，iPhone上的所有触摸事件都会被main run loop放在队列里等待处理，所以你不需要对UI组件做额外的事情，而其他输入源需要一些额外的编码。比如在run loop上schedule一个NSInputStream，你需要像下面这样：

{% highlight objc %}
[iStream setDelegate:self];
[iStream scheduleInRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
{% endhighlight %}

在上面的代码中，一旦iStream有输入数据，就会执行`self`的`stream:handleEvent`的方法。而且这个stream可以是任意类型的输入源，包括socket.

另外，timer对象也可以被schedule在run loop上，比如：

{% highlight objc %}
[NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(doStuff) userInfo: nil repeats:YES];
{% endhighlight %}

上面的代码把计时器schedule到当前的run loop上，每2秒就会调用`self`的`doStuff`方法。

###不适用run loop的情况
那什么时候不适合使用run loop呢？根据run loop的特点，输入事件会一个接一个的被串行处理，那么如果一个事件的处理需要的时间特别长的话，就会导致在这个事件处理完之前，app无法响应别的输入事件。在这种情况下，新开一个线程处理更合适。 然而，大部分情况下，我们的代码处理屏幕、socket或者计时器事件都非常快，这时使用main run loop处理起来更简单，也更安全。

编译自[Run-loops vs. Threads in Cocoa](http://blog.shinetech.com/2009/06/02/run-loops-vs-threads-in-cocoa/)

配图来自苹果官方文档[Run Loops](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html)




