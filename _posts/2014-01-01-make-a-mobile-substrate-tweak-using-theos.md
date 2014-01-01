---
layout: post
title: "使用Theos做一个简单的Mobile Substrate Tweak"
image: "/assets/resources/2014-01-01-make-a-mobile-substrate-tweak-using-theos-pic0.png"
image-width: ""
image-height: ""
description: ""
category: iOS
tags: [iOS]
---
{% include JB/setup %}

###Mobile Substrate和Theos
[Mobile Substrate](http://www.cydiasubstrate.com/)是Cydia的作者Jay Freeman ([@saurik](https://twitter.com/CydiaSubstrate))的另外一个牛X的作品，也叫Cydia Substrate,它的主要功能是hook某个App，修改代码比如替换其中方法的实现，Cydia上的tweak都是基于Mobile Substrate实现的。目前支持iOS和Android平台。

根据github上的介绍，[theos](https://github.com/DHowett/theos)是一个跨平台iPhone Makefile系统。它的主要功能是生成iPhone 越狱App、tweak等程序的框架结构，并提供makefile来编译、打包和安装。   

###需要的准备工作：  
####Mac
* 安装Theos，从Theos的[GitHub](https://github.com/DHowett/theos)上clone下来一份，放到某个目录下，这里我放到了`/opt/`下。
* 安装Xcode Command Line Tools，可以在命令行下执行`xcode-select --install`来安装或者参考[SO](http://stackoverflow.com/questions/19066647/xcode-5-0-error-installing-command-line-tools)来安装，安装完之后再进行下一步。
* 安装dpkg ，首先安装MacPorts，可以通过它的[官网](http://www.macports.org/),根据自己的系统版本来选择。安装好之后，重启Terminal，执行`port version`，显示出版本号说明安装成功。如果提示`command not found`，尝试在`/etc/paths`文件中加入下面两个路径:`/opt/local/bin` `/opt/local/sbin`，需要使用root权限来编辑，比如用Vim的话:`sudo vi /etc/paths`. 重启Terminal，再次输入`port version`就应该会显示版本号了，然后执行`sudo port selfupdate`来更新一下,之后执行`sudo port install dpkg`来安装dpkg. 安装dpkg的目的是把我们写的tweak打成deb包。

####JailBreaked iPhone iOS 5/6
* 安装OpenSSH，打开Cydia的主界面就能看到`OpenSSH Access How-To` 以及`Root Password How-To`的选项，可以按照它的提示一步一步安装，这里不赘述了，需要提醒的是一定要改掉root的密码，防止别人通过SSH连接到你的手机。这一步是为了后面我们通过SSH连接到手机，把deb包安装到手机上准备的。     
  iOS7上的Mobile Substrate还有bug，32位的系统下每次重启后需要重新安装Mobile Substrate才能正常使用, 64位今天貌似才能用。推荐暂时在iOS5/6的机器上测试[2014-01-01]。
* apt. 在cydia中搜索Apt检查是否已经安装，没有安装就安装一下。
* ldid. 全名是Link Identify Editor,也直接可以在Cydia中搜索全名安装。

###创建Tweak并安装到手机上

首先我在桌面上创建一`mytweaks`的文件夹，保存我们要创建的tweak程序。
{% highlight bash %}
➜  ~        cd ~/Desktop
➜  Desktop  mkdir mytweaks
➜  Desktop  cd mytweaks
{% endhighlight %}

然后执行我们刚才的获得的theos来生成一个tweak的模板：
{% highlight bash %}
➜  mytweaks  /opt/theos/bin/nic.pl
NIC 2.0 - New Instance Creator
------------------------------
  [1.] iphone/application
  [2.] iphone/library
  [3.] iphone/preference_bundle
  [4.] iphone/tool
  [5.] iphone/tweak
Choose a Template (required): 5
Project Name (required): FirstTweak
Package Name [com.yourcompany.firsttweak]: com.joeyio.firsttweak
Author/Maintainer Name [Joey]:
[iphone/tweak] MobileSubstrate Bundle filter [com.apple.springboard]:
[iphone/tweak] List of applications to terminate upon installation (space-separated, '-' for none) [SpringBoard]:
Instantiating iphone/tweak in firsttweak/...
Done.
{% endhighlight %}
在创建模板的时候，我们选择5，创建一个iPhone的tweak.其他4个选项可以自己去搜索下。名字输入FirstTweak，包名我输入com.joeyio.firsttweak，下面的三个选项都直接回车使用缺省值。  
`MobileSubstrate Bundle filter`这一项表示要hook的程序，默认是`com.apple.springboard`，就是hook Spring Board，如果你想hook别的App，这里改成那个App的BundleID.  

OK，那么我们的第一个tweak就创建好了，好像一点也不难啊。进入到firsttweak目录下，使用`make`编译一下，可能结果是这样的：
{% highlight bash %}
➜  firsttweak  make
/Users/qiaoxueshi/Desktop/mytweaks/firsttweak/theos/makefiles/targets/Darwin/iphone.mk:41: Deploying to iOS 3.0 while building for 6.0 will generate armv7-only binaries.
Making all for tweak FirstTweak...
 Preprocessing Tweak.xm...
Name "Data::Dumper::Purity" used only once: possible typo at /Users/qiaoxueshi/Desktop/mytweaks/firsttweak/theos/bin/logos.pl line 615.
 Compiling Tweak.xm...
 Linking tweak FirstTweak...
 Stripping FirstTweak...
 Signing FirstTweak...
 /bin/sh: ldid: command not found
{% endhighlight %}
我们看到里面有2个警告，第一个我没有搜索到什么结果，第二个是只要手机上安装ldid就行了,这里不用管它。我自己试了一下，是可以安装到手机上的，可以暂时忽略，如果哪位小伙伴知道什么原因，欢迎告知。

在部署到手机之前确认手机和电脑在一个wifi环境下，并且可以通过SSH连接到手机，方法是在Terminal下，通过SSH连接到手机，之后会提示你输入root密码（上面安装SSH步骤中有提到），确保连接成功再往下进行。手机的IP地址可以在wifi设置中看到。
{% highlight bash %}
ssh root@手机IP地址
{% endhighlight %}
然后把手机IP地址放在`THEOS_DEVICE_IP`环境变量中，这样theos才知道安装到哪里，如下：
{% highlight bash %}
export THEOS_DEVICE_IP=手机IP地址
{% endhighlight %}
然后执行`make package install`打包并安装到手机上 (如果Cydia在前台，把它退到后台，否则安装会失败)：
{% highlight bash %}
➜  firsttweak  make package install
/Users/qiaoxueshi/Desktop/mytweaks/firsttweak/theos/makefiles/targets/Darwin/iphone.mk:41: Deploying to iOS 3.0 while building for 6.0 will generate armv7-only binaries.
Making all for tweak FirstTweak...
make[2]: Nothing to be done for `internal-library-compile'.
Making stage for tweak FirstTweak...
dpkg-deb：正在新建软件包“com.joeyio.firsttweak”，包文件为“./com.joeyio.firsttweak_0.0.1-2_iphoneos-arm.deb”。
install.exec "cat > /tmp/_theos_install.deb; dpkg -i /tmp/_theos_install.deb && rm /tmp/_theos_install.deb" < "./com.joeyio.firsttweak_0.0.1-2_iphoneos-arm.deb"
root@192.168.199.126's password:
Selecting previously deselected package com.joeyio.firsttweak.
(Reading database ... 6250 files and directories currently installed.)
Unpacking com.joeyio.firsttweak (from /tmp/_theos_install.deb) ...
Setting up com.joeyio.firsttweak (0.0.1-2) ...
install.exec "killall -9 SpringBoard"
root@192.168.199.126's password:
{% endhighlight %}

安装过程中需要输入两次手机Root密码，一次是为了把打包后的deb程序文件传到手机上，另外一次是kill掉SpringBoard，使SpringBoard重启。

完成后在Cydia里的“变更”里，往下翻一翻，就能看到一个名字为“FirstTweak”的插件了了，想想接下来出任CEO，迎娶白富美，走向人生巅峰，有木有一点小激动？

###完成一个小功能

到目前为止，我们还没写过一行代码呢。下面我们要完成一个小功能：在锁屏界面增加一个UILabel显示一行文字，可以是你的座右铭或者其他的，这里我们显示`Hello, MobileSubstate!!`。

打开我们刚才创建的firsttweak目录下的`Makefile`文件，在`FirstTweak_FILES = Tweak.xm`下面增加一行`FirstTweak_FRAMEWORKS = UIKit`并保存文件，前缀都是`TWEAK_NAME`的值,也就是`FirstTweak`,注意根据你自己的情况来修改。增加这行的原因很明显，增加UILabel需要用到UIKit Framework。整个文件看起来像这样：

{% highlight bash %}
include theos/makefiles/common.mk

TWEAK_NAME = FirstTweak
FirstTweak_FILES = Tweak.xm
FirstTweak_FRAMEWORKS = UIKit

include $(THEOS_MAKE_PATH)/tweak.mk

after-install::
    install.exec "killall -9 SpringBoard"
{% endhighlight %}

这个步骤完成之后，我们就要找到锁屏界面对应的ViewController，然后替换它的某个方法，把UILabel添加到它的view上。这个ViewController的名字叫`SBAwayController`， SB是`SpringBoard`的缩写，不要想偏了 :).我们要替换它的`- (void)activate`方法。`SBAwayController`类的头文件可以在[iOS6的私有类的头文件](https://github.com/DjKira/iOS-6-Headers)中找到。在SBAwayController里有个叫`_awayView`的`ivar`，获得这个ivar需要一个theos中不存在的方法，好吧，它叫`MSHookIvar`,这个方法在默认的theos的`substrate.h`头文件里没有，可以在[GitHub](https://raw.github.com/hbang/headers/master/substrate.h)得到包含这个方法的头文件。下载到本地，覆盖theos/include下的同名文件（推荐将原有的`substrate.h`头文件重命名）。

OK，到这里万事具备，只欠Coding了。

打开firsttweak目录下的`Tweak.xm`文件并**清空**，添加下面这段代码：

{% highlight objc %}
%hook SBAwayController 
- (void)activate  {
    %orig(); //invoke the orignal method to do what should to do.
    NSLog(@"=========================================================");
    NSLog(@"Hello MobileSubstrate!!");
    NSLog(@"=========================================================");
    
    //get _awayView via MSHookIvar method
    UIView *_awayView = MSHookIvar<*>(self, "_awayView");
    
    //create a lable whose width = 200 and height = 100 and add to _awayView
    float w = 200;
    float h = 100;
    UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake((_awayView.frame.size.width - w)/2,100,w,h)];
    label.text = @"Hello, MobileSubstate!!";
    label.textAlignment = NSTextAlignmentCenter;
    label.backgroundColor = [UIColor clearColor];
    label.textColor = [UIColor whiteColor];
    [_awayView addSubview:label];
}
%end

{% endhighlight %}

大概解释一下，`%hook SBAwayController`以及里面的`- (void)activate`方法，其实就类似swizzling了`SBAwayController`的`activate`方法。当系统执行`SBAwayController`的`activate`方法的时候会执行tweak里的`activate`的方法。 在这里方法里我们先执行了`%orig()`，就是执行原来的`activate`方法，保证原有的方法先执行，再执行我们自己的代码。

这个`activate`方法在第一次进入锁屏界面的时候会执行，在以后每次非锁屏状态下，按关机键也会执行。

接下来就是通过`MSHookIvar`获得`_awayView`。然后就是我们非常熟悉的了，创建一个UILabel，添加到`_awayView`里。到这里就结束了。`make package install`一下(还需要先执行一下`export THEOS_DEVICE_IP=手机IP地址`)，安装到手机上，等SpringBoard重启完，你会看到类似下图的界面：
![Alt text](/assets/resources/2014-01-01-make-a-mobile-substrate-tweak-using-theos-pic1.png)

把手机连接到电脑上，打开Xcode，在Organizer里的Console里能看到程序中使用NSLog打印的信息，用来调试很方便呢。
![Alt text](/assets/resources/2014-01-01-make-a-mobile-substrate-tweak-using-theos-pic2.png)


###总结
本文主要是讲Mobile Substrate的作用以及如何使用Theos开发一个简单的tweak。有了这些入门的基础之后，你就可以根据自己的想法来写自己喜欢的tweak。如果你是在iOS7下越狱的话，可以尝试一下把控制中心的AirDrop和音乐播放器给隐藏掉，让控制中心看起来更简洁。接着可以再进行改进，比如在蓝牙关闭的时候不显示AirDrop，开启的时候依然显示，音乐正在播放的时候显示音乐播放器，否则不显示。  

这个小Demo是前两周写的，一直没有时间整理出来，今天抽时间整理了一下文字发了出来，算是送给自己新年的一件礼物吧！   

Thanks，Have Fun！

###More About Substrate And Theos 
* [iphonedevwiki](http://iphonedevwiki.net/index.php/MobileSubstrate)
* [Theos/Getting Started](http://iphonedevwiki.net/index.php/Theos/Getting_Started)
* [Cydia Substrate](http://www.cydiasubstrate.com/)(Mobile Substrate也叫做Cydia Substrate) 






