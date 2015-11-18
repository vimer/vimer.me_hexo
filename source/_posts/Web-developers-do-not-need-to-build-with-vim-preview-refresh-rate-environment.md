title: 配合vim构建Web开发无需率刷新预览环境
date: 2015-06-28 16:03:12
tags: [vim]
---
&nbsp;&nbsp;这个不是vim插件，但是对于一个偶尔或频繁开发H5的vimer又是个不错的选择。开发页面的时候，经常需要写段代码，比如布局，调整下css颜色，改完后需要再刷新下页面，才能看到效果，对于这么频繁的操作，想必大部分coder是不愿意的。这次带来的福音是grunt-contrib-connect和grunt-contrib-watch 这两个grunt插件，其中grunt-contrib-connect是起了个静态文件服务器的作用，而grunt-contrib-watch 用作监视文件的改变，然后执行指定的任务。

&nbsp;&nbsp;继续自己写作风格，对于这种插件或者tip的东西直接上详细配置。首先在你需要监控的web根目录下面需要两个基本配置文件，一个是package.json这是用来配合npm来安装插件的。

```
{
    "name": "web",
    "version": "0.0.1",
    "devDependencies": {
        "grunt-contrib-connect": "~0.6.0",
        "grunt-contrib-watch": "~0.5.3",
        "load-grunt-tasks": "~0.3.0"
    }
}
```
<!--more-->

上面插件需要npm install来安装，另外一个配置文件是Gruntfile.js，这个在起grunt serve的时候需要加载.
```
module.exports = function(grunt){
    require('load-grunt-tasks')(grunt); //加载所有的任务
    //require('time-grunt')(grunt); 如果要使用 time-grunt 插件
    grunt.initConfig({
        connect: {
            options: {
                port: 9000,
                hostname: '127.0.0.1', //默认就是这个值，可配置为本机某个 IP，localhost 或域名
                livereload: 35729  //声明给 watch 监听的端口
            },
            server: {
                options: {
                    open: true, //自动打开网页 http://
                    base: [
                        './'  //主目录
                    ]
                }
            }
        },
        watch: {
            livereload: {
                options: {
                    livereload: '<%=connect.options.livereload%>'  //监听前面声明的端口  35729
                },

                files: [  //下面文件的改变就会实时刷新网页
                    '*/*.html',
                    './style/{,*/}*.css',
                    './scripts/{,*/}*.js',
                    './images/{,*/}*.{png,jpg}'
                ]
            }
        }
    });
    grunt.registerTask('serve', [
        'connect:server',
        'watch'
    ]);
}
```

到现在为止，一个基本环境已经配置好了，通过运行grunt serve 就可以正常工作了。

