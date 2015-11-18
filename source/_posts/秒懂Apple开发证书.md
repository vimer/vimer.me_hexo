title: 秒懂Apple开发证书
date: 2015-04-07 23:36:57
tags: [Apple, iOS]
---
之所以说这个，是因为之前很长一段时间团队缺乏iOS开发人员, 所以只能靠自己来快速学习了,  这个过程最麻烦的地方就是制作证书了, 每次制作都要请教下朋友. 制作后一段时间又忘记了.  如此反复. 应该也会有很多人和我一样被证书搞晕吧 : )

 现在正好有点时间，摸索了下，系统的以问答形式写个简易概念+流程. (这里我以开发证书为例, 发布证书雷同，不懂的可以留言. )

一.为什么需要开发者证书 ?
  真机调试 / 上架

二.没有开发者证书Xcode会显示什么 ?
<!--more-->
  如图: ![No_Signing](http://ww4.sinaimg.cn/large/744e593bgw1eqxfjtmzudj20d404874q.jpg)

三.制作证书必须知道哪几个概念 ?
* Certificates : 证明你是开发者身份用的
* App ID :  可以简单理解为应用的身份证号( 关键还要记住一点, 还有一种身份证号是有特权, 如: com.XXX.*, 以*结尾的可以匹配多个应用)
*  Devices :  添加要测试机的uuid (可以理解为要测试哪台设备，添加哪个uuid即可)
* Provisioning Profiles :  可以简单理解为捆绑Certificates、App ID、Devics用的

四.生成证书步骤 :
1. 先制作Certificates, 登陆Developer中心后,点击:
![Cetificates_Step_1](http://ww1.sinaimg.cn/large/744e593bgw1eqxfko4ko7j20qt06hjsc.jpg)
2. 进去后分Development(开发)和Production(发布)，我们选择 Development中的iOS App Development,![Cetificates_Step_2](http://ww2.sinaimg.cn/large/744e593bgw1eqxflfmahyj20k50k440x.jpg)
3. 在生成证书之前，需要上传本地证书: 
![Cetificates_Step_3](http://ww2.sinaimg.cn/large/744e593bgw1eqxflv8l2pj20la0c4jsi.jpg)

(
如何制作本地证书 ?
->通过Spotlight搜索keychain Access, 然后在
![Keychain Access](http://ww1.sinaimg.cn/large/744e593bgw1eqxfolt8thj20k007udhh.jpg)
继续下一步直接 Saved to disk .
)

4.继续第三步，直接把选择刚才生成到桌面的证书, 生成即可. 
![Cetificates_Step_4](http://ww1.sinaimg.cn/large/744e593bgw1eqxfp3r6xxj20jh08vmxx.jpg)
并且点击下载, 下载完安装后可以在keychain Access中看到.
![Cetificates_Step_5](http://ww1.sinaimg.cn/large/744e593bgw1eqxfpn4rfmj2082060dga.jpg)

五.建立支持多个应用的App ID
![AppID](http://ww2.sinaimg.cn/large/744e593bgw1eqxfpw3nzcj20ks0mlmzc.jpg)
根据图中提示填写. 

六.添加Devices uuid
这边比较简单, 把测试设备连接到itunes上，会显示UUID 直接拷贝即可:
![Devices](http://ww2.sinaimg.cn/large/744e593bgw1eqxfqd5vx9j20bk061q37.jpg)

七.生成Provisioning Profiles: 
1.点击添加后，选择iOS App Development, 进入下一步.
2.选择Select App ID，选择刚才生成的, 如图:
![Provisioning](http://ww1.sinaimg.cn/large/744e593bgw1eqxfqpri4ij20je0engn1.jpg)继续下一步
3.选择刚才生成的开发者证书
4.选择刚才添加uuid测试的设备
5.最后命名生成, 下载安装. 
6.安装完后，在Xcode->Preferences->Accounts右侧下面有个View Details 可以看到刚才安装的provisioning profile![Provisioning](http://ww1.sinaimg.cn/large/744e593bgw1eqxfr3x4vrj20ff083jrw.jpg)

OK ! Easy to way .

Q&A:

1.当需要推送时，比如信鸽推送，需要再建立一个推送证书，也就是总共两个证书：一个是开发者证书用于开发的，一个是推送证书用于推送的

2.Provisioning Profiles对于不需要推送的账号只要建立与Wildcard APP ID绑定就可以了，但是需要推送的需要和有推送的APP ID绑定.

3.如果出现以下错误(信鸽)：
Error Domain=NSCocoaErrorDomain Code=3000 "未找到应用程序的“aps-environment”的权利字符串"
请在自己的项目里这样设置：
provisioning profile文件需要包含正在调试的设备，并且provisioning profile文件要在app开启APNS之后生成。 

4.若想测试发布时的证书问题,可以使用发布版的Ad Hoc模式来测试.

