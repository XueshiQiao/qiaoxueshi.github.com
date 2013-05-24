---
layout: post
title: "UINavigationController的setViewControllers方法"
description: ""
category: iOS 
tags: [iOS]
---
{% include JB/setup %}

在iOS开发中，UINavigationController是很常用的Controller，对它的一般操作就像操作一个栈，push和pop。但也经常会遇到pop和push无法优雅的完成的操作，比如退回到中间的某个VC上，或者在第一个VC之前添加一个VC等，更甚者要重新构造整个VC的顺序，这时候setViewControllers方法就排上用场了，它使对VC栈的操作不再局限于push和pop，而是构造整个VC栈并应用到当前的UINavigationController中，这个方法支持iOS3.0+，放心使用。      
<br />
#Sample

	NSMutableArray * viewControllers = [self.navigationController.viewControllers mutableCopy];
	[viewControllers removeLastObject];
	[viewControllers addObject:newController];

	[self.navigationController setViewControllers:viewControllers animated:YES];
	// [viewControllers relase] // if non-arc


感谢 Allen([Weibo](http://www.weibo.com/122678100)) 提供的代码和思路
<br />
<br />
#说明  
下面这段摘自Api文档   

	You can use this method to update or replace the current view controller stack without pushing or popping each controller explicitly. In addition, this method lets you update the set of controllers without animating the changes, which might be appropriate at launch time when you want to return the navigation controller to a previous state.

	If animations are enabled, this method decides which type of transition to perform based on whether the last item in the items array is already in the navigation stack. 

	1.If the view controller is currently in the stack, but is not the topmost item, this method uses a pop transition; 
	2.if it is the topmost item, no transition is performed. 
	3.If the view controller is not on the stack, this method uses a push transition. 

	Only one transition is performed, but when that transition finishes, the entire contents of the stack are replaced with the new view controllers. For example, if controllers A, B, and C are on the stack and you set controllers D, A, and B, this method uses a pop transition and the resulting stack contains the controllers D, A, and B.
<br />
Have fun！
<br />
<br />






















