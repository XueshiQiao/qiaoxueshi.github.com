---
layout: post
title: "如何在一个设备上安装一个App的两个不同版本"
image: "/assets/resources/2013-08-16-how-to-have-two-versions-of-the-same-app-on-your-device.png"
image-width: "144px"
image-height: "144px"
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}

最近干了件蠢事，事情是这样的，我们App有2套图标，一套是测试版图标用于发布OTA的内部测试版，一套是正式版用于发布到AppStore，每次打包，我都会检查图标，结果上次粗心搞错了，把测试版的图标打包发布到AppStore了，发现之后想死的心都有了。马上修改了一版，申请紧急审核，结果你可能猜到了，没有通过。这是个很大的教训，像这一类的手动来改都不靠谱，毕竟有忘掉的概率存在，能不能自动处理呢？ 在[这篇Blog](http://nilsou.com/blog/2013/07/29/how-to-have-two-versions-of-the-same-app-on-your-device/)上找到了答案,我大概的翻译一下。

iOS系统区分两个App是否相同的根据是App的Bundle ID是否相同，在安装一个程序时，系统是根据Bundle ID来判断是全新安装还是升级。那想在一个系统上安装一个App的两个不同版本，其实是需要两个不同的Bundle ID。就是说正式版一个Bundle ID，OTA版本/Debug版本用一个Bundle ID，假设AppStore版的ID是`com.mycompany.myapp`，OTA版的是`com.mycompany.myapp-beta`。同时为了直观的区分两个App，一般也会使用两套图标, 假设AppStore版的图标名称为`Icon.png, Icon@2x.png`, OTA版是`Icon-beta.png, Icon-beta@2x.png`. 那如果做到自动化的配置呢？答案在Build设置(`Build Setting`)里。

默认Xcode会提供2个Build配置(`Build Configuration`)：`Debug`和`Release`，我们再加一个`AppStore`,这样来用：

* Debug： 用来直接连机调试
* Release：用于发布OTA的测试版
* AppStore：用户提交到AppStore

下一步我们来在项目的`Build Setting`里添加两个自定义的设置，一个命名为`BUNDLE_IDENTIFIER`, 另一个命名为`APP_ICON_NAME`，如下图这样设置：


![add_user_define_setting](http://f.cl.ly/items/3v3T3W230B1v2K0M2m1c/Screen%20Shot%202013-07-23%20at%2011.12.16%20AM.png)



这两个值分别定义个Bundle ID和图标的名称，下一步需要在Info.plist(名字格式是YourAppName-Info.plist)中修改BundleId 和Icon图标名称，把`bundle identifier`值设置为`${BUNDLE_IDENTIFIER}`，把图标值设置为`${APP_ICON_NAME}@2x.png` 和 `${APP_ICON_NAME}.png`，如果提供了72px和144px等图标也类似这样。

`${xxx}`语法是预处理语法，都会被替换为`xxx`对应的真实值，在刚才的设置的基础上，在Debug的时候，实际的Bundle ID会替换为`com.mycompany.myapp-beta`,图标对应的为`Icon-beta.png`和`Icon-beta@2x.png`，Cooool

实际上我自己实践的时候，新建了一个叫`myApp-AppStore`的`Schema`，在不同的Schema里的Archive里是用不同的Build配置，`myApp-AppStore`的Schema里Archive的Build配置为"AppStore"，原来的`myApp`这个Schema的Build配置为Release，这样当我想发布OTA的时候，选择`myApp-AppStore`这个Schema，然后Archive，就能使用AppStore的自定义的配置来打包，用来提交AppStore；当选择`myApp`这个Schema的时候，Archive得到的是使用Release的自定义配置来打包的，用来上传到OTA测试。整个过程是自动化的，包括BundleId和图标文件的名称，如果你有别的类似的需要，也可以参考着来。

总之，麻麻再也不用担心我的图标会搞错了。



这篇文章编译自：[How to Have Two Versions of the Same App on Your Device]((http://nilsou.com/blog/2013/07/29/how-to-have-two-versions-of-the-same-app-on-your-device/)) ，原作者Blog上还有其他精彩的文章等你发现。




