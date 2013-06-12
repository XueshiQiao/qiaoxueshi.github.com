---
layout: post
title: "关于revoke certificate和备份的这点事"
description: ""
category: 
tags: []
---
{% include JB/setup %}

事情是这样的，前几天电脑崩溃，硬盘数据全部丢失，重装系统和Xcode之后，从Develop Center的Certificates里重新下载证书，安装到新电脑上，在真机上运行时，提示报错:"A valid signing identity matching this profile could not be found in your keychain", 按照字面意思查了一下，是因为本地KeyChain里丢失了private key的缘故，得到的结论是要么从之前的备份中恢复，或者重新生成新的证书。遗憾的是我之前并没有保存备份，无奈只好重新生成。一个问题马上出现在脑海，就是重新生成新的证书是否会对线上的App产生影响。查了一下官方的文档，发现有这部分详细的说明，消除了我的顾虑，内容如下：

	Important: Members of the Standard iOS Developer Program can be assured that replacing either your developer or distribution certificate will not affect any existing apps that you've published in the iOS App Store, nor will it affect your ability to update those apps.
	Notes before beginning:

	Replacing your distribution certificate won't affect your developer certificate or development profiles.
	Similarly, replacing your developer certificate won't affect your distribution certificate or distribution profiles.
	Replace only your development certificate if you are troubleshooting an issue running your app on device through Xcode.
	Replace only your distribution certificate if you are troubleshooting an issue creating, submitting or installing a distribution build.
	After replacing your certificate(s) you are required to update and reinstall any provisioning profiles that were bound to the old certificate.
	
搞清楚问题和解决办法，就开工：
1. 首先在iOS Provisioning Portal[1]里revoke掉当前失效的Certificates，并创建一个新的Certificates(参考[2])

2. 通过import和export Developer Profile备份和恢复(参考[3]和[4])

Links: 
[1] [iOS Provisioning Portal](https://developer.apple.com/account/overview.action)  
[2] [How do I delete/revoke my certificates and start over fresh?](http://developer.apple.com/library/ios/#technotes/tn2250/_index.html#//apple_ref/doc/uid/DTS40009933-CH1-TNTAG6)  
[3] [Exporting and  Importing Developer Profile ](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificatesandProvisioningAssets/MaintainingCertificatesandProvisioningAssets.html)  
[4] [Transferring Your Identities](http://developer.apple.com/library/ios/#technotes/tn2250/_index.html#//apple_ref/doc/uid/DTS40009933-CH1-TNTAG24)  


