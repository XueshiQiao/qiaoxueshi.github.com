---
layout: post
title: "Xcode的iOS项目的版本号设置"
description: ""
category: iOS 
tags: [iOS]
---
{% include JB/setup %}
##Version & Build 号

![Image1 icon](/assets/resources/version1.jpg)

今天对Xcode里iOS的版本号又有了新的认识，一个叫做Version，一个叫做Build，这两个值都可以在Xcode中选中target，点击“Summary”后看到。 Version在plist文件中的key是“CFBundleShortVersionString”，和AppStore上的版本号保持一致，Build在plist中的key是“CFBundleVersion”，代表build的版本号，该值每次build之后都应该增加1。这两个值都可以在程序中通过下面的代码获得：


	[[[NSBundle mainBundle] infoDictionary] valueForKey:@"key"]
<br />
<br />
##Archive后自动增长build号
除此之外，如果我们想在Archive后build号自动增长，就可以使用到Xcode的run script来实现，步骤是

1. 选中项目的target，点击“Build Phases“
2. 点击右下角的”Add Build Phrase“，选择”Add run script“，会产生一个新的Run Script项
3. 拖拽新生成的Run Script项到最上面
4. 点开该项，copy下面的shell代码进去，代码来自[这里](http://stackoverflow.com/questions/9855955/xcode-increment-
build-number-only-during-archive?answertab=active#tab-top)，如下图所示


		if [ $CONFIGURATION == Release ]; then
		    echo "Bumping build number..."
		    plist=${PROJECT_DIR}/${INFOPLIST_FILE}

			#increment the build number (ie 115 to 116)
		    buildnum=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "${plist}")
		    if [[ "${buildnum}" == "" ]]; then
		        echo "No build number in $plist"
		        exit 2
		    fi

		    buildnum=$(expr $buildnum + 1)
		    /usr/libexec/Plistbuddy -c "Set CFBundleVersion $buildnum" "${plist}"
		    echo "Bumped build number to $buildnum"

		else
		    echo $CONFIGURATION " build - Not bumping build number."
		fi


这段shell脚本的意思就是说，如果当前的配置是Release（Archive时该值为Release，直接在模拟器上运行是Debug），就设置build值为当前build值+1， 否则什么都不干。  

这样在build的时候就会看到build号会自动加1的，想看build时输出的信息，可以通过"View -> Navigators -> Log"来查看最新的build时产生的log。
<br /><br />
##Ref:

1. [Concurrent Debug, Beta and App Store Builds](http://swwritings.com/post/2013-05-20-concurrent-debug-beta-app-store-builds)
2. [stackoverflow: Xcode-Increment build number only during ARCHIVE?](http://stackoverflow.com/questions/9855955/xcode-increment-build-number-only-during-archive?answertab=active#tab-top)

Have fun！