---
layout: post
title: "UINC的setViewControllers方法"
description: ""
category:iOS 
tags: [iOS]
---
{% include JB/setup %}

#Sample

```
NSMutableArray * viewControllers = [self.navigationController.viewControllers mutableCopy];
[viewControllers removeLastObject];
[viewControllers addObject:newController];

[self.navigationController setViewControllers:viewControllers animated:YES];
// [viewControllers relase] // if non-arc
```
感谢 Allen([Weibo](http://www.weibo.com/122678100)) 提供的代码和思路

#API文档

##setViewControllers:animated:
Replaces the view controllers currently managed by the navigation controller with the specified items.

```-(void)setViewControllers:(NSArray *)viewControllers animated:(BOOL)animated```

##Parameters  

###viewControllers  
The view controllers to place in the stack. The ```front-to-back``` order of the controllers in this array represents the new ```bottom-to-top``` order of the controllers in the navigation stack. Thus, **the last item added to the array becomes the top item of the navigation stack**.

###animated  
If ```YES```, animate the pushing or popping of the top view controller. If ```NO```, replace the view controllers without any animations.

##Discussion  
You can use this method to update or replace the current view controller stack without pushing or popping each controller explicitly. In addition, this method lets you update the set of controllers without animating the changes, which might be appropriate at launch time when you want to return the navigation controller to a previous state.

If animations are enabled, this method decides which type of transition to perform based on whether the last item in the items array is already in the navigation stack. 

* If the view controller is currently in the stack, but is not the topmost item, this method uses a pop transition; 
* if it is the topmost item, no transition is performed. 
* If the view controller is not on the stack, this method uses a push transition. 

Only one transition is performed, but when that transition finishes, the entire contents of the stack are replaced with the new view controllers. For example, **if controllers A, B, and C are on the stack and you set controllers D, A, and B, this method uses a pop transition and the resulting stack contains the controllers D, A, and B**.

##Availability  
Available in iOS 3.0 and later.  
##Declared In  
UINavigationController.h


