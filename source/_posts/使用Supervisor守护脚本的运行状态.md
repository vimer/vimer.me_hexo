title: 使用Supervisor守护脚本的运行状态
date: 2015-04-10 00:45:47
tags: [Supervisor, shell, Mac]
---
Supervisor介绍请移步:
http://supervisord.org/

这里以Mac为例，下面是大概安装命令: 

sudo easy_install pip
sudo pip install supervisor
echo_supervisord_conf > /usr/local/share/supervisord.conf
supervisord -c /usr/local/share/supervisord.conf

\#echo_supervisord_conf 是生成配置文件
\#supervisord 是启动supervisor
\#supervisorctl 是控制supervisord
\#注意:当supervisord.conf不在默认path下时,  执行supervisorctl需要指定此path：
supervisorctl -c /path/supervisord.conf

 打开supervisord.conf 的 [include] 引入 files的配置.
 ```shell
 [include]
 files = /usrlo/local/share/supervisor/*.conf
 ```
 在supervisor建立一个你想守护的进程的文件名, 如sharerpoxy.conf
 <!--more-->
 ```shell
[program:shareproxy]
command=/Users/InfoGeeker/shareproxy.sh
process_name=%(program_name)s
numprocs=1
autostart=true
autorestart=true
startsecs=1
startretries=100
exitcodes=0,2
stopsignal=TERM
stopwaitsecs=1
redirect_stderr=true
stdout_logfile=/usr/local/share/supervisor/shareproxy.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
```

\#command=后面跟着就是你要守护的shell.
其中shell脚本头需要加上 #!/bin/sh

如图，shareproxy脚本正常运行:
![shareproxy](http://ww3.sinaimg.cn/large/744e593bgw1eqzt7fhu2ij20gt02974i.jpg)

supervisor可以用于Nodejs后台程序的守护常见的经常电脑启动需要启动的脚本.

让supervisor能够在Mac下自启.
在~/Library/LaunchAgents/vimer.supervisord.plist
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>KeepAlive</key>
    <dict>
        <key>SuccessfulExit</key>
        <false/>
    </dict>
    <key>Label</key>
    <string>vimer.supervisord</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/supervisord</string>
        <string>-c</string>
        <string>/usr/local/share/supervisord.conf</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```
如果报ownership/permissions错误, 进行如下权限修改:
```
chmod 600 vimer.supervisord.plist
chown root vimer.supervisord.plist
```
然后:
```
sudo launchctl load ~/Library/LaunchAgents/vimer.supervisord.plist
```
就可以自启动了.

