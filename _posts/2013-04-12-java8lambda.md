---
layout: post
title: "初探Java8新特性之lambda表达式"
description: ""
category: 
tags: []
---
{% include JB/setup %}


Java8带有Lambda表达式的预览版的JDK已经放出来了（地址在最下面），新特性有以下四个：

1.Lambda表达式（或称之为“闭包”或者“匿名函数”）

2.扩展的目标类型

3.方法和构造器引用

4.接口默认方法

本文先介绍一下Java8中很值得期待的Lambda表达式，lambda表达式，等同于大多说动态语言中常见的闭包、匿名函数的概念。其实这个概念并不是多么新鲜的技术，在C语言中的概念类似于一个函数指针，这个指针可以作为一个参数传递到另外一个函数中。由于Java是相对较为面向对象的语言，一个Java对象中可以包含属性和方法（函数），方法（函数）不能孤立于对象单独存在。这样就产生了一个问题，有时候需要把一个方法（函数）作为参数传到另外一个方法中的时候（比如回调功能），就需要创建一个包含这个方法的接口，传递的时候传递这个接口的实现类，一般是用匿名内部类的方式来。如下面代码，首先创建一个Runnable的接口，在构造Thread时，创建一个Runnable的匿名内部类作为参数：
<pre>
<code>
new Thread(new Runnable() {  
    public void run() {  
        System.out.println("hello");  
    }  
}).start();
</code>
</pre>
类似这种情况的还有swing中button等控件的监听器，如下面代码所示，创建该接口的一个匿名内部类实例作为参数传递到button的addActionListener方法中。
{% highlight java %}

public interface ActionListener {   
    void actionPerformed(ActionEvent e);  
}  

button.addActionListener(new ActionListener() {   
  public void actionPerformed(ActionEvent e) {   
    ui.dazzle(e.getModifiers());  
  }  
});

{% endhighlight %}

这样的代码的缺点是有代码笨重，可读性差，不能引用外面的非final的变量等。lambda表达式就是为了解决这类问题而诞生的。

在介绍Java8中的Lambda表达式之前，首先介绍一个概念叫“函数式接口”（functional interfaces）。对于任意一个Java接口，如果接口中只定义了唯一一个方法，那么这个接口就称之为“函数式接口”。比如JDK中的ActionListener、Runnable、Comparator等接口。

先来看一下Java8中的lambda表达式的使用示例：

创建一个线程：
{% highlight java %}
new Thread(() -> {System.out.println("hello");}).start();
{% endhighlight %}

可以看到这段代码比上面创建线程的代码精简了很多，也有很好的可读性。
`() -> {System.out.println(“hello”);} `就是传说中的lambda表达式，等同于上面的`new Runnable()`,lambda大体分为3部分：

1.最前面的部分是一对括号，里面是参数，这里无参数，就是一对空括号

2.中间的是 -> ，用来分割参数和body部分

3.是body部分，可以是一个表达式或者一个语句块。如果是一个表达式，表达式的值会被作为返回值返回；如果是语句块，需要用return语句指定返回值。如下面这个lambda表达式接受一个整形的参数a，返回a的平方
{% highlight java %}
(int a) -> a^2   
    等同于

(int a) -> {return a^2;}
{% endhighlight %}
继续看更多的例子：
{% highlight java %}
(int x, int y) -> x + y  

() -> 42  

(String s) -> { System.out.println(s); }
{% endhighlight %}
创建一个FileFilter，文件过滤器：
{% highlight java %}
FileFilter java = (File f) -> f.getName().endsWith(".java")
{% endhighlight %}

创建一个线程：
{% highlight java %}
new Thread(() -> {  
  //do sth here...  
}).start()
{% endhighlight %}

创建一个Callable：
{% highlight java %}
Callable<String> c = () -> "done";
{% endhighlight %}

而且lambda表达式可以赋值给一个变量：
{% highlight java %}
Comparator<String> c;  
c = (String s1, String s2) -> s1.compareToIgnoreCase(s2);
{% endhighlight %}
当然还可以作为方法的返回值：
{% highlight java %}
public Runnable toDoLater() {  
  return () -> {  
    System.out.println("later");  
  };  
}
{% endhighlight %}
从上面可以看到，一个lambda表达式被作为一个接口类型对待，具体对应哪个接口，编译器会根据上下文环境推断出来，如下面的lambda表达式就表示一个`ActionListener`.
{% highlight java %}
ActionListener l = (ActionEvent e) -> ui.dazzle(e.getModifiers());
{% endhighlight %}
这有可能会造成一个表达式在不同的上下文中被作为不同的类型，如下面的这种情况，尽管两个表达式是相同的，上面的表达式被推断为Callable的类型，下面的会被推断为PrivilegedAction类型。
{% highlight java %}
Callable<String> c = () -> "done";  
PrivilegedAction<String> a = () -> "done";
{% endhighlight %}
那么编译器是根据哪些因为决定一个表达式的类型呢？

如果一个表达式被推断为是T类型的，需要满足以下4个条件:

1. T是函数式接口类型（只声明唯一一个方法）
2. 表达式和T中声明的方法的参数个数一致，参数类型也一致
3. 表达式和T中声明的方法的返回值类型一致
4. 表达式和T中声明的方法抛出的异常一致
有了这个准则，上面的疑问就迎刃而解了

 

参考：

1.[State of the Lambda](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-4.html)

2.Java8带有Lambda表达式版本的[JDK下载地址](http://jdk8.java.net/lambda/)


