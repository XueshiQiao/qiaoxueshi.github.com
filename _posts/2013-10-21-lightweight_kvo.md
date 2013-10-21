---
layout: post
title: "轻量级KVO[译]"
image: "/assets/resources/2013-10-21-lightweight_kvo.png"
image-width: ""
image-height: ""
description: ""
category: 
tags: [iOS]
---
{% include JB/setup %}

在这篇文章中，我会实现一个自己用的简单KVO类，我认为KVO非常棒，然而对于我大部分的使用场景来说，有这两个问题：  
1. 我不喜欢在`observeValueForKeyPath:ofObject:change:context:`方法里通过keyPath值来做调度，当Observe比较多的对象时，会使得代码变得杂乱和迷惑。
2. 必须手动的来注册和删除一个观察者，如果能自动做就好了。

So，我们开始这个实现。这个技巧我第一次是在[THObserversAndBinders](https://github.com/th-in-gs/THObserversAndBinders)项目中见到，本篇内容也仅仅描述了一下里面的做法，同时做了简化。

首先，我们定义一下我们的这个类，我们这个帮助类的类名是`Observer`:

{% highlight objc %}
@interface Observer : NSObject
+ (instancetype)observerWithObject:(id)object
                           keyPath:(NSString*)keyPath
                            target:(id)target
                          selector:(SEL)selector;
@end
{% endhighlight %}

Observer类的这个类方法有四个参数，每个参数都是自解释的，我选择使用target/action模式，当然也可以使用block，但是那样的话需要做weakSelf/strongSelf的转换，你懂的，通常来说分来来做比较好。

我们做的是在初始化方法中设置KVO，并在dealloc方法中移除。这意味着一旦Observer对象被retain，我们就有了一个观察者，下面这段代码是从我的一个ViewCOntroller中拿来的：

{% highlight objc %}
self.usernameObserver = [Observer observerWithObject:self.user
                                             keyPath:@"name"
                                              target:self
                                            selector:@selector(usernameChanged)];
{% endhighlight %}

把这个Observer对象作为一个属性放在ViewController中来保证被retain，一旦我们的Viewcontroller被释放，就会设置它为nil，observer就停止观察了。

在这个实现中，使用一个weak引用指向被观察对象和观察者(target)是很重要的，如果两个中的其中一个是nil，我们就停止向观察者发送消息。

{% highlight objc %}
@interface Observer ()
@property (nonatomic, weak) id target;
@property (nonatomic) SEL selector;
@property (nonatomic, weak) id observedObject;
@property (nonatomic, copy) NSString* keyPath;
@end
{% endhighlight %}

初始化器里设置KVO通知，使用self作为context，如果我们会有一个子类也添加类似的观察者时就很有必要了。

{% highlight objc %}
- (id)initWithObject:(id)object keyPath:(NSString*)keyPath target:(id)target selector:(SEL)selector
{
  if (self) {
    self.target = target;
    self.selector = selector;
    self.observedObject = object;
    self.keyPath = keyPath;
    [object addObserver:self forKeyPath:keyPath options:0 context:self];
  }
  return self;
}
{% endhighlight %}

一旦被观察者发生变化，我们就通知观察者（target），如果它还存在的话：

{% highlight objc %}
- (void)observeValueForKeyPath:(NSString*)keyPath ofObject:(id)object change:(NSDictionary*)change context:(void*)context
{
if (context == self) {
  id strongTarget = self.target;
  if ([strongTarget respondsToSelector:self.selector]) {
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
    [strongTarget performSelector:self.selector];
#pragma clang diagnostic pop
  }
}
}
{% endhighlight %}

最后在`dealloc`方法中移除观察者对象:

{% highlight objc %}
- (void)dealloc
{
    id strongObservedObject = self.observedObject;
    if (strongObservedObject) {
        [strongObservedObject removeObserver:self forKeyPath:self.keyPath];
    }
}
{% endhighlight %}

这就是全部内容了。还有很多可以扩展的地方，比如增加block的支持，或者我比较喜欢的trick：再增加爱一个方便的构造方法用来第一次直接调用action。然而，我想的是展现出这个技术的核心部分，你可以根据自己的需求来调整它。

这个技术的优点是在使用KVO的时候不需要记住太多东西，仅仅retain住Observer对象，然后在完成的试试置为nil即可，剩下的会自动完成。

原文作者是`Chris Eidhof`，[objc.io](http://objc.io/)的创办者  
原文地址:[Lightweight Key-Value Observing](http://chris.eidhof.nl/post/63590250009/lightweight-key-value-observing)


