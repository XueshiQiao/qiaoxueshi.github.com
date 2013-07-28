---
layout: post
title: "NSOperation"
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}
<div style="text-align:center; margin-bottom:10px;">
  <img src="/assets/resources/nsoperation.png"  
     height="400" 
     width="600"> 
</div>

几乎每个开发者都知道，让App快速响应的秘诀是把耗时的计算丢到后台线异步去做。于是，Modern Objective-C开发者有两个选择：[GCD](http://en.wikipedia.org/wiki/Grand_Central_Dispatch)和[NSOperation](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/NSOperation_class/Reference/Reference.html).

由于GCD已经发展的比较主流了，我们稍后再说它，先说说面向对象的NSOperation.

NSOperation表示一个单独的计算单元，它是一个抽象类（很类似Java里的Runnable接口），给子类提供了一些非常有用且线程安全的特性，比如**状态**(`state`),**优先级**(`priority`),**依赖**(`dependencies`)以及**取消**(`cancellation`). 如果你不想子类化NSOperation，可以选择使用NSBlockOperation这个NSOperation的子类，它可以把一个block包装成为一个NSOperation.

非常适合使用NSOperation的任务例子包括[network requests](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking/AFURLConnectionOperation.h), 图片的缩放，语言处理或者其他一些重复的、结构化的以及需要运行较长时间来处理数据的任务。

但是，仅仅把计算包装成一个对象，没有一些监管也不会非常的有用，这时`NSOperationQueue`就出现了。

NSOperationQueue控制各个operation的并发执行.它像是一个优先级队列，operation大致的会按FIFO的方式被执行，不过带有高优先级的会跳到低优先级前面被执行（用NSOperation的queuePriority方法来设置优先级）。 NSOperationQueue支持并发的执行operations，通过maxConcurrentOperationCount来指定最大并发数，就是同时有最多有多少个operation同时被运行。

可以通过调用-start方法来启动一个NSOperation，或者把它放到NSOperationQueue里，当到达队列最前端时也会被自动的执行。

现在来看看NSOperation的几个不同的特性，以及如何如果使用和子类化它：

##状态 State
NSOperation构建了一个非常优雅的状态机来描述一个operation的执行过程：  

	isReady -> isExecuting -> isFinished
State是通过这些keypath的KVO通知来隐式的得到，而不是显式的通过一个state的属性。就是说，当一个operation已经准备就绪，将要被执行时，它会为`isReady`keyPath发送一个KVO的通知，对应的属性值也会变为YES.

为了构造一致的状态，下面每个属性都与其他属性相互排斥：

* isReady: 如果operation已经做好了执行的准备返回YES，如果它所依赖的操作存在一些未完成的初始化步骤则返回NO。
* isExecuting:如果operation正在执行它的任务返回YES，否则返回NO。
* isFinished: 任务成功的完成了执行，或者中途被Cancel，返回YES。NSOperationQueue只会把isFinished为YES的operation踢出队列，isFinished为NO的永远不会被移除，所以实现时一定要保证其正确性，避免死锁的情况发生。

##取消 Cancellation
如果正在进行的operation所做的工作不再有意义，尽早的取消掉是非常有必要的。取消一个operation可以是显式的调用cancel方法，也可以是operation依赖的其他operation执行失败。

和state类似，当NSOperation的被取消，是通过`isCancelled`keypath的KVO来获得。当NSOperation的子类覆写cancel方法时，注意清理掉内部分配的资源。特别注意的是，这时isCancelled和isFinished的值都变为了YES，isExecuting为值变为NO。

一个需要格外注意的地方是和单词“cancel”有关的两个词：

* cancel : 带一个"l" 表示方法 （动词）
* isCancelled : 带两个"l"表示属性（形容词）

##优先级 Priority
所有的operation在NSOperationQueue中未必都是一样的重要，设置`queuePriority`属性就可以提升和降低operation的优先级，`queuePriority`属性可选的值如下：

* NSOperationQueuePriorityVeryHigh
* NSOperationQueuePriorityHigh
* NSOperationQueuePriorityNormal
* NSOperationQueuePriorityLow
* NSOperationQueuePriorityVeryLow

另外，operation可以指定一个`threadPriority`值，它的取值范围是0.0到1.0，1.0代表最高的优先级。`queuePriority`决定执行顺序的优先级，`threadPriority`决定当operation开始执行之后分配的计算资源的多少。

##依赖 Dependencies
取决于你的App的复杂性，可能会需要把一个大的任务分成多个子任务，这时NSOperation依赖就排上用场了。

比如从服务器上下载和缩放图片的过程，你可能会想把下载图片作为一个operation，缩放作为另外一个（这样也可以复用下载图片和缩放图片的代码）。然后，一个图片在从服务器上下载下来之前是没有办法缩放的，于是我们说缩放图片的operation依赖从服务器上下载图片的operation，后者必须先完成，前者才能开始执行。用代码表示是这样的：
{% highlight objc %}
[resizingOperation addDependency:networkingOperation];
[operationQueue addOperation:networkingOperation];
[operationQueue addOperation:resizingOperation];
{% endhighlight %}
一个operation只有在它依赖的所有的operation的isFinished都为YES的时候才会开始执行。要记住添加到queue里的所有的operation的依赖关系，并避免循环依赖，比如A依赖B，B依赖A，这样会产生死锁。

##completionBlock
`completionBlock`是在iOS4和Snow Leopard中添加的一个非常有用的特性。当一个NSOperation完成之后，就会精确地只执行一次`completionBlock`。我们需要在operation完成之后想做点什么的时候这个属性就会非常有用。比如当一个网络请求结束之后，可以在`completionBlock`里处理返回的数据。


##总结
NSOperation依然是Modern Objective-C程序员杀手锏里的重要工具。相对于GCD非常适用于in-line的异步处理，NSOperation提供了更综合的、面向对象的计算模型，非常适用于封装结构化的数据，重复性的任务。把它加到你的下个项目中，给你的用户和你自己都带来乐趣吧！

##译者注
本文编译自[NSHipster](http://nshipster.com)里的[NSOperation](http://nshipster.com/nsoperation/)一文，感谢作者[Mattt Thompson](http://mattt.me/), 来头很大，这是他的简介：

> Mattt Thompson is the Mobile Lead at [Heroku](), and the creator & maintainer of [AFNetworking](https://github.com/afnetworking/afnetworking) and other popular [open-source projects](https://github.com/mattt), including [Postgres.app](http://postgresapp.com/) & [Induction](http://inductionapp.com/). He also writes about obscure & overlooked parts of Cocoa on [NSHipster](http://nshipster.com/).

最上面的图片是来自于WWDC2013中的“Hidden Gems in Cocoa and Cocoa Touch”（228）中Mattt讲NSOperation时的截图，这个视频一共有30个tips，这是第8个tip，大部分的内容我是第一次知道，非常值得看，而且如果有条件的话，建议下载HD版本的视频来看，效果比SD好太多。字幕文件在我的这个[repo](https://github.com/qiaoxueshi/WWDC_2013_Video_Subtitle)里, :) 

如有文中有不准确的地方，欢迎留言指正 :)

Enjoy!