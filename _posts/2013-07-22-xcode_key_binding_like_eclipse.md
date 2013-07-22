---
layout: post
title: "Xcode自定义Eclipse中常用的快捷键"
description: ""
category: 
tags: []
---
{% include JB/setup %}
之前在用Eclipse写Java的时候，有几个常用的快捷键，比如删除当前行，在当前行下面插入空行，向上/下移动当前行等等，到了Xcode里怎么也找不到这些快捷键，一直觉得Xcode自带的快捷键不够强大，直到今天才知道不借助第三方的插件，在Xcode下完全也可以实现这些功能，下面就说一下如何来做。


首先找到Xcode中的自带的配置文件  
`/Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Versions/A/Resources/IDETextKeyBindingSet.plist`
这个文件里配置了一些可以设置快捷键的操作, 使用常用的编辑器打开它（需要root权限）。

然后看看下面这段配置, (来自[gist](https://gist.github.com/gdavis/2829437),感谢作者[@gdavis](https://gist.github.com/gdavis) )


	<key>GDI Commands</key>
	<dict>
	  	<key>GDI Duplicate Current Line</key>
	  	<string>selectLine:, copy:, moveToEndOfLine:, insertNewline:, paste:, deleteBackward:</string>
	  	<key>GDI Delete Current Line</key>
	  	<string>moveToEndOfLine:, deleteToBeginningOfLine:, deleteBackward:, moveDown:, moveToEndOfLine:</string>
	  	<key>GDI Move Current Line Up</key>
	  	<string>selectLine:, cut:, moveUp:, moveToBeginningOfLine:, insertNewLine:, paste:, moveBackward:</string>
	  	<key>GDI Move Current Line Down</key>
	  	<string>selectLine:, cut:, moveDown:, moveToBeginningOfLine:, insertNewLine:, paste:, moveBackward:</string>
	  	<key>GDI Insert Line Above</key>
	  	<string>moveUp:, moveToEndOfLine:, insertNewline:</string>
	  	<key>GDI Insert Line Below</key>
	  	<string>moveToEndOfLine:, insertNewline:</string>
	</dict>

这个dict是一组可以设置快捷键的操作，里面的key是名称，对应的string是对应的一组操作，从名字本身也可以看出是什么意思，而且也可以根据这些自由装配成自己的别的快捷操作。

* GDI Duplicate Current Line	复制当前行到下面一行
* GDI Delete Current Line  		删除当前行
* GDI Move Current Line Up 		把当前行往上移动一行
* GDI Move Current Line Down 	把当前行往下移动一行
* GDI Insert Line Above 		在当前行上面增加一空行
* GDI Insert Line Below 		在当前行下面增加一空行（不管光标是否在行尾）

把这段配置放到上面提到的`IDETextKeyBindingSet.plist`里，放在文件的最后的这两行之前：

		</dict>
	</plist>

重启Xcode，在Xcode菜单中，打开`Preferences`，选中`Key Binding`，在右上方搜索`GDI`, 会出现类似下图的显示，如果没有的话，请检查上面的每步操作。

![img ](/assets/resources/key_binding.png) 

双击右边的空白处，就可以为每个功能设置不同的快捷键，我设置和Eclipse里的一致，感受了下，非常爽，Cooool

Have fun!~  

