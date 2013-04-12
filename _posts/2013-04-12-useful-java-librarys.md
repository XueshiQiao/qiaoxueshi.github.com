---
layout: post
title: "鲜为人知的但很有用的Java类"
description: ""
category: Java 
tags: [java]
---
{% include JB/setup %}
作者：[Dustin Marx](http://www.javaworld.com/community/user/185)  发表日期：Fri, 03/02/2012 - 23:10.

Reddit Java网站最近有一个题目为“[分享Java标准类库中一些有用的类](http://www.reddit.com/r/java/comments/qepq5/share_a_useful_class_from_the_standard_java_class/)”的讨论话题，注解栏为“有很多平常我们没有认识到的类，分享一些你经常使用而我们可能没有意识到的类吧！”。在这篇文章中，我看到下面回复超过40的一些类（大部分是JDK中的）。

有一些回复者分享的是与并发相关的Java类，如 [Executors](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executor.html), [java.util.concurrent.CountDownLatch](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html), [java.util.concurrent.atomic.AtomicInteger](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/atomic/AtomicInteger.html), [ThreadLocal](http://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html), [java.util.concurrent](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/package-frame.html) 以及包下的所有类以及[java.util.concurrent.atomic](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/atomic/package-frame.html).

还有一些与 String 处理相关的类也被提到了，包括 [StringBuffer](http://docs.oracle.com/javase/7/docs/api/java/lang/StringBuffer.html), 和[StringBuilder](http://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html). 我在博文 [String, StringBuffer, and StringBuilder: There Is A Performance Difference](http://marxsoftware.blogspot.com/2008/05/string-stringbuffer-and-stringbuilder.html) 中也提到了这些与String相关的类。其他与String相关并被提到的包括[java.util.StringTokenizer](http://docs.oracle.com/javase/7/docs/api/java/util/StringTokenizer.html) 和 [Apache Commons‘ StringUtils](http://commons.apache.org/lang/api-2.5/org/apache/commons/lang/StringUtils.html) (在我的文章 [Checking for Null or Empty or White Space Only String in Java](http://marxsoftware.blogspot.com/2011/09/checking-for-null-or-empty-or-white.html)中也有提到).  [java.util.Scanner](http://docs.oracle.com/javase/7/docs/api/java/util/Scanner.html) 类也可以让简化文本解析。

在用户界面上， [java.awt.geom](http://docs.oracle.com/javase/7/docs/api/java/awt/geom/package-summary.html) 包， [java.awt.Desktop](http://docs.oracle.com/javase/7/docs/api/java/awt/Desktop.html) 以及[javax.swing.SwingUtilities](http://docs.oracle.com/javase/7/docs/api/javax/swing/SwingUtilities.html)被提到。 [java.awt.Point](http://docs.oracle.com/javase/7/docs/api/java/awt/Point.html) 被高亮显示，原因总结为：”任何两个int值对可以很简单的被用来代替数组传递给函数，或者从函数中返回，都可以使用Point类”。[java.awt.Robot](http://docs.oracle.com/javase/7/docs/api/java/awt/Robot.html) 类也在文中被提到，我之前也在我的一篇文章[Screen Snapshots with Java’s Robot](http://marxsoftware.blogspot.com/2010/08/screen-snapshots-with-javas-robot.html) 中提到。

不出所料，一些Java集合类也在列在其中。包括 [java.util.EnumSet](http://docs.oracle.com/javase/7/docs/api/java/util/EnumSet.html) 和 [EnumMap](http://docs.oracle.com/javase/7/docs/api/java/util/EnumMap.html) (参考我的文章[The Sleek EnumMap and EnumSet](http://marxsoftware.blogspot.com/2010/07/sleek-enummap-and-enumset.html)), [java.util.ArrayDeque](http://docs.oracle.com/javase/7/docs/api/java/util/ArrayDeque.html) (参考我的文章[The Java SE 6 Deque](http://marxsoftware.blogspot.com/2008/10/java-se-6-deque.html)), [java.util.PriorityQueue](http://docs.oracle.com/javase/7/docs/api/java/util/PriorityQueue.html),[java.util.Arrays](http://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html), 以及 [java.util.Collections](http://docs.oracle.com/javase/7/docs/api/java/util/Collections.html) (参考我的文章 [The Java Collections Class](http://marxsoftware.blogspot.com/2009/03/java-collections-class.html)).

以我之见，[java.lang.ClassLoader](http://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html), [java.util.ServiceLoader](http://docs.oracle.com/javase/7/docs/api/java/util/ServiceLoader.html) 和 [java.nio.file.FileVisitor](http://docs.oracle.com/javase/7/docs/api/java/nio/file/FileVisitor.html) 是Reddit Java话题中提到的更精心构思和特别的类。我们大部分的情况下都是用强引用（strong reference），但[java.lang.ref.WeakReference](http://docs.oracle.com/javase/7/docs/api/java/lang/ref/WeakReference.html) （弱引用）和 [java.lang.ref.SoftReference](http://docs.oracle.com/javase/7/docs/api/java/lang/ref/SoftReference.html) （软引用）也在讨论中被提到。

最后，我经常使用的一个类和一个方法分别是 [BigDecimal](http://docs.oracle.com/javase/7/docs/api/java/math/BigDecimal.html) 类（我的一边文章中顺便提到了该类： [Caution: Double to BigDecimal in Java](http://marxsoftware.blogspot.com/2010/01/caution-double-to-bigdecimal-in-java.html) ）和 [System.nanoTime()](http://docs.oracle.com/javase/7/docs/api/java/lang/System.html#nanoTime()) 。

结论：

我喜欢列表中的很多类，也能想到一些其他的例子。特别的，我想JDK7中的 [Objects](http://marxsoftware.blogspot.com/2011/03/jdk-7-new-objects-class.html) 类也能称得上很有用但鲜为人知的一个类把。我同样同意其中一个评论的说法：“把Google guava库添加进来吧！”，我也写过[一些博文](http://marxsoftware.blogspot.com/search/label/Guava)关于 [Guava](http://code.google.com/p/guava-libraries/) 。

原文：[http://marxsoftware.blogspot.com/](http://marxsoftware.blogspot.com/)

翻译自:[http://www.javaworld.com/community/node/8335](http://marxsoftware.blogspot.com/)









