title: Mac借用secureCRT传输服务端文件
date: 2015-08-05 15:59:13
tags: [Mac]
---

一直在使用iTerm作为终端，唯一缺陷是不支持Zmoderm，在Mac上在服务器上上传和下载文件就只能通过ftp，但是这样相对来说不是特别爽，且不安全。

这又让我想到win下的SecureCRT，只能曲线救国，也算Mac用户福音了, 下面是Mac下的SecureCRT破解步骤。

到[SecureCRT官网](https://www.vandyke.com/download/prevreleases.html)下载，亲测7.2.6有效：

下载完后，先安装，然后把应用拖动到Application目录下。

下载下面的这个[脚本](https://github.com/InfoGeeker/securecrt_mac_crack/blob/master/securecrt_mac_crack.pl)

<!--more-->
执行
<pre>
<code class="bash">
sudo perl ~/Downloads/securecrt_mac_crack.pl /Applications/SecureCRT.app/Contents/MacOS/SecureCRT
</code>
</pre>

顺利的话，会报crack成功。

Name:           bleedfly
Company:        bleedfly.com
Serial Number:  03-29-002542
License Key:    ADGB7V 9SHE34 Y2BST3 K78ZKF ADUPW4 K819ZW 4HVJCE P1NYRC
Issue Date:     09-17-2013

Zmodem Upload:

![Zmodem](http://ww1.sinaimg.cn/large/744e593bgw1eursfaaz0ej20ec0bs3za.jpg)


![Zmodem](http://ww3.sinaimg.cn/large/744e593bgw1eursfsmawsj20qq04wwfb.jpg)
