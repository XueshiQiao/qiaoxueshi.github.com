---
layout: post
title: "Swift之 @auto_closure"
image: "/assets/resources/swift-hero.png"
image-width: ""
image-height: ""
description: ""
category: iOS
tags: [Swift]
---
{% include JB/setup %}

用C实现一个`assert()`，通常是这么做的：

{% highlight objc %}
#ifdef NDEBUG
#define assert(e)  ((void)0)
#else
#define assert(e)  \
    ((void) ((e) ? ((void)0) : __assert (#e, __FILE__, __LINE__)))
#define __assert(e, file, line) \
    ((void)printf ("%s:%u: failed assertion `%s'\n", file, line, e), abort())
#endif
{% endhighlight %} 
assert就是断言，这里采用条件编译，作用是如果在调试情况下，检查参数e，如果是false，就给出错误提示并终止程序执行，如果是非DEBUG情况下，就什么都不做。这种宏实现的方式是没有运行时性能影响的，因为我们知道宏展开基本是直接替换的，没有对表达式求值的过程。

比如这样简单的一个宏，用来返回两个数中的较大值:
{% highlight objc %}
#define MAX(A,B) (A >= B ? A : B)
{% endhighlight %} 
当我们使用的时候，比如`MAX(10, 20)`,宏展开后的结果是`(10 >= 20 ? 10  : 20)`, 而不是计算到最终的结果`20`. 但是在方法调用中，参数值是直接求值的，比如我们有个判断一个数是否偶数的函数:
{% highlight objc %}
func isEven(num : Int) -> Bool {
    return num % 2 == 0;
}
{% endhighlight %} 
当我们调用`isEven(10 + 20)`的时候，先计算`10 + 20`的结果，然后把`30`作为参数传递到`isEven`函数中。

OK. 在Swift里也实现了这样一个功能的assert()函数，而且没有用到宏(你骗人，明明用到了啊?!， 就是`#if !NDEBUG`啊。 好吧，相信苹果Swift官方Blog在下一篇文章中应该会有相应的机制来判断当前的环境的，这里的意思是没用宏来实现表达式的延迟求值。)，是怎么实现的呢？

首先在Swift里没有办法写一个函数，它接受一个表达式作为参数，但是却不执行它。比如，我们想这么实现:

{% highlight objc %}
func assert(x : Bool) {
    #if !NDEBUG

        /*noop*/
    #endif
}
{% endhighlight %} 
然后这么用：
{% highlight objc %}
assert(someExpensiveComputation() != 42)
{% endhighlight %} 
我们发现，总是要计算一遍表达式`someExpensiveComputation() != 42`的值，是真是假, 然后把这个值传递到assert函数中。即便我们在非Debug的情况下编译也是一样，那怎么样条件执行呢，像上面的使用宏的方式，当条件满足的时候才对表达式求值? 还是有办法的，就是修改这个方法，把参数类型改为一个闭包，像这样：
{% highlight objc %}
func assert(predicate : () -> Bool) {
    #if !NDEBUG
        if predicate() {
            abort()
        }
    #endif
}
{% endhighlight %} 
然后调用的时候创建一个匿名闭包，然后传给assert函数:
{% highlight objc %}
assert({ someExpensiveComputation() != 42 })
{% endhighlight %} 
这样当我们禁用assert的时候，表达式`someExpensiveComputation() != 42 ` 就不会被计算，减少了性能上的消耗，但是显而易见，调用的代码就显的不那么清爽优雅了。

于是乎Swift引入了一个新的`@auto_closure`属性，它可以用在函数的里标记一个参数，然后这个参数会先被隐式的包装为一个closure，再把closure作为参数给这个函数。好绕啊，直接看代码吧，使用@auto_closure,上面的assert函数可以改为：
{% highlight objc %}
func myassert(predicate : @auto_closure () -> Bool) {
    #if !NDEBUG
        if predicate() {
            abort()
        }
    #endif
}
{% endhighlight %} 
然后我们就可以这么调用了：
{% highlight objc %}
assert(someExpensiveComputation() != 42)
{% endhighlight %} 
哇。好神奇！

仔细看一下myassert()函数的参数:
{% highlight objc %}
predicate : @auto_closure () -> Bool
{% endhighlight %} 
predicate加上了@auto_closure的属性，后面是个closure类型`() -> Bool`。其实predicate还是`() -> Bool`类型的，只是在调用者可以传递一个普通的值为Bool表达式，，然后RunTime会自动把这个表达式包装为一个`() -> Bool`类型的闭包作为参数传给myassert()函数，简而言之就是中间多了一个由表达式到闭包的自动转换过程。

`@auto_closure`的功能非常强大和实用，有了它，我们就可以根据具体条件来对一个表达式求值，甚至多次求值。在Swift的其他地方也有`@auto_closure`的身影，比如实现短路逻辑操作符时，下面是`&&`操作符的实现：
{% highlight objc %}
func &&(lhs: LogicValue, rhs: @auto_closure () -> LogicValue) -> Bool {
    return lhs.getLogicValue() ? rhs().getLogicValue() : false
}
{% endhighlight %} 
如果lhs已经是false了，rhs也就没有必要计算了，因为整个表达式肯定为false。这里使用`@auto_closure`就轻松实现了这个功能。

最后，正如宏在C中的地位一样，`@auto_closure`的功能也是非常强大的，但同样应该小心使用，因为调用者并不知道参数的计算被影响(推迟)了。`@auto_closure`故意限制closure不能有任何参数（比如上面的`() -> Bool`），这样我们就不会把它用于控制流中。


编译自Swift的官方Blog [Building assert() in Swift, Part 1: Lazy Evaluation](https://developer.apple.com/swift/blog/?id=4)一文


