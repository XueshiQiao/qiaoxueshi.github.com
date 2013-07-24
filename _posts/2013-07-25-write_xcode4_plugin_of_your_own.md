---
layout: post
title: "写个自己的Xcode4插件"
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}

刚写iOS程序的时候就知道Xcode支持开发者编写自己的插件，比如[ColorSense](https://github.com/omz/ColorSense-for-Xcode)等很实用的插件，但Xcode的插件开发没有官方的文档支持，一直觉得很神秘，那今天就来揭开它的面纱。

在Xcode启动的时候，Xcode会检查一下插件目录(`~/Library/Application Support/Developer/Shared/Xcode/Plug-ins`)下的插件(扩展名为`.xcplugin`的bundle文件)并加载他们。其实到这里我们就猜到了，我们做的插件最终会是一个扩展名为`.xcplugin`的bundle文件，并放在插件目录下供Xcode加载。

我们先做一个简单的插件，需要很简单的几个步骤即可完成，我的环境是Xcode 4.6.3 (4H1503)。

##1. 创建一个新的Xcode Project

Xcode插件其实就是一个Mac OS X bundle，所以像下图一样创建一个Bundle。
![Image1 icon](/assets/resources/xcode_plugin_1.png)

给工程名起个名字，并确保***不要***勾选`Use automatic reference counting`，因为Xcode是使用GC来管理内存的，所以Xcode的插件也需要是用GC来管理内存的。Framework选择`Cocoa`。

![Image2 icon](/assets/resources/xcode_plugin_2.png)


##2. 设置Target Info
像下图一样设置下面这些信息

* `XC4Compatible` = `YES`
* `XCPluginHasUI` = `NO`
* `XCGCReady` = `YES`
* `Principal Class` = `Plugin`  (这个设置为你`插件的名字`，本例中命名为`Plugin`)

前三个可能Info里缺省没有，可以自己添加，都选`Boolean`类型，最后一个`Principal Class`是`String`类型。
![Image3 icon](/assets/resources/xcode_plugin_3.png)

##3. 设置Build Settings
然后打开Build Setting 这个Tab，修改这些：

* 设置`Installation Build Products Locatio`为`${HOME}`，Xcode会自动转换为你当前用户的Home路径
* 设置`Installation Directory` 为 `/Library/Application Support/Developer/Shared/Xcode/Plug-ins`， Xcode会把拼接`Installation Build Products Locatio`和`Installation Directory`两个路径为一个绝对路径来查找你的插件
* 设置`Deployment Location` 为 `YES`
* 设置`Set Wrapper extension` 为 `xcplugin`

![Image4 icon](/assets/resources/xcode_plugin_4.png)
![Image5 icon](/assets/resources/xcode_plugin_5.png)
##4. 添加 USER-DEFINED 设置

* 设置`GCC_ENABLE_OBJC_GC` 为 `supported`
* 设置`GCC_MODEL_TUNING` 为 `G5`

![Image6 icon](/assets/resources/xcode_plugin_6.png)

有了这些设置，每次build这个Projct的时候，Xcode就会把我们的插件copy到plugin文件夹下，然后通过重启Xcode来让Xcode重新加载新的插件，所以能看出来开发插件是相对来说简单一些的，调试插件就比较纠结了，唯一的办法就是build之后，重启Xcode，来加载最新build的插件。

准备工作已经结束，下面开始实现我们的插件。

我们在第二步的时候设置了一个`Principal Class`，在Xcode里新建和这个名字一样的Objective-C类。在实现文件中添加上`+ (void) pluginDidLoad: (NSBundle*) plugin`方法。 该方法会在Xcode加载插件的时候被调用，可以用来做一些初始化的操作。通常这个类是一个单例，并添加了Observe了`NSApplicationDidFinishLaunchingNotification`这个通知，用来获得Xcode加载完毕的通知。

	+ (void) pluginDidLoad: (NSBundle*) plugin {
		static id sharedPlugin = nil;
		static dispatch_once_t once;
		dispatch_once(&once, ^{
			sharedPlugin = [[self alloc] init];
		});
	}
	
	- (id)init {
		if (self = [super init]) {
			[[NSNotificationCenter defaultCenter] addObserver:self 
	                        selector:@selector(applicationDidFinishLaunching:) 
	                            name:NSApplicationDidFinishLaunchingNotification 
	                          object:nil];
		}
		return self;
	}


一旦接收到Xcode加载完毕的通知，就可以建立其他plugin需要的notification或者添加额外的菜单项或者访问像Code Editor一类的界面元素。

在我们的这个简单例子中，我们在`Edit`下添加一个叫做`Custom Plugin`的菜单项，并设置一个`⌥ + c`快捷键。它的功能是在激活的情况下，使用NSAlert显示出我们选中的文本。我们需要通过观察`NSTextViewDidChangeSelectionNotification`并访问接收参数中的`NSTextView`，来获得被选中的文本。

	- (void) applicationDidFinishLaunching: (NSNotification*) notification {
	    [[NSNotificationCenter defaultCenter] addObserver:self 
	                        selector:@selector(selectionDidChange:) 
	                            name:NSTextViewDidChangeSelectionNotification 
	                          object:nil];
	
	    NSMenuItem* editMenuItem = [[NSApp mainMenu] itemWithTitle:@"Edit"];
	    if (editMenuItem) {
	        [[editMenuItem submenu] addItem:[NSMenuItem separatorItem]];
	
	       NSMenuItem* newMenuItem = [[NSMenuItem alloc] initWithTitle:@"Custom Plugin" 
	                                            action:@selector(showMessageBox:) 
	                                     keyEquivalent:@"c"];
	       [newMenuItem setTarget:self];
	       [newMenuItem setKeyEquivalentModifierMask: NSAlternateKeyMask];
	       [[editMenuItem submenu] addItem:newMenuItem];
	       [newMenuItem release];
	   }
	}
	
	- (void) selectionDidChange: (NSNotification*) notification {
	    if ([[notification object] isKindOfClass:[NSTextView class]]) {
	        NSTextView* textView = (NSTextView *)[notification object];
	
	        NSArray* selectedRanges = [textView selectedRanges];
	        if (selectedRanges.count==0) {
	            return;
	        }
	
	        NSRange selectedRange = [[selectedRanges objectAtIndex:0] rangeValue];
	        NSString* text = textView.textStorage.string;
	        selectedText = [text substringWithRange:selectedRange];
	   }
	}
	
	- (void) showMessageBox: (id) origin {
	    NSAlert *alert = [[[NSAlert alloc] init] autorelease];
	    [alert setMessageText: selectedText];
	    [alert runModal];
	}

你会发现会报selectedText不存在的错，就实现里增加一个`NSString *selectedText`即可。

	@implementation Plugin {
	    NSString *selectedText;
	}
 
##5. 需要注意的
* Plugin不能使用Arc，注意管理好内存
* 不能Debug，不过可以在程序里通过NSLog打印出日志，并通过`tail -f /var/log/system.log`	命令来查看输出的日志
* 如果Xcode突然启动不起来了，可能是我们的插件有问题，跑到`~/Library/Application Support/Developer/Shared/Xcode/Plug-ins`目录下，把插件删掉，restart Xcode，查找问题在哪

##6. 总结
这只是一个简单的Xcode插件的入门编写示例，不过“麻雀虽小，五脏俱全”，可以了解到Xcode的插件一些东西，比如Xcode插件本质上其实就是一个Mac OS X bundle等等，而且因为没有Apple官方的文档的支持，很多东西只能去Google，或者参考别人插件的一些实现。


##7. REF
本文编译自[WRITING YOUR OWN XCODE 4 PLUGINS](http://blacksmithsoftware.com/blog/2012/11/19/writing-your-own-xcode4-plugins)，感谢原作者[Blacksmith Software](http://twitter.com/#!/BlacksmithSW)

---

另：
前两天我们的小伙伴[@onevcat](http://weibo.com/onevcat)写了一个Xcode插件[VVDocumenter](https://github.com/onevcat/VVDocumenter-Xcode?source=c)，作用是在方法、类等前面输入三个/就会自动生成规范的JavaDoc文档(Xcode5中将支持JavaDoc类型的文档，对于我这样从Java转过来的来说是真是雪中送炭)，赶紧clone了一个，用起来很方便，很好很强大，强烈推荐！ 赶紧把我们的项目代码文档化起来，迎接Xcode5的到来吧,:)


Enjoy!!!


