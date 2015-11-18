title: curl模拟测试服务端form请求
date: 2015-04-23 12:00:20
tags: [curl]
---
这个算是技巧，不知道其他人以前怎么测试的，我以前测试Form的POST一般自己写个HTML来做测试，今天使用Go的Gin框架时发现curl既然还有这功能, 相比之下，以前的测试方法效率低很多.

```bash
 curl -v --form user=user --form password=password http://localhost:8080/login
```

就拿下面这段程序做测试.

```golang
package main
import (
    "github.com/gin-gonic/gin"
    "github.com/gin-gonic/gin/binding"
)
type LoginForm struct {
    User     string `form:"user" binding:"required"`
    Password string `form:"password" binding:"required"`
}
func main() {
    r := gin.Default()
    r.POST("/login", func(c *gin.Context) {
        var form LoginForm
        c.BindWith(&form, binding.MultipartForm)
        if form.User == "user" && form.Password == "password" {
            c.JSON(200, gin.H{"status": "you are logged in"})
        } else {
            c.JSON(401, gin.H{"status": "unauthorized"})
        }
    })
    r.Run(":8080")
}
```
<!--more-->

![result](http://ww3.sinaimg.cn/large/744e593bgw1erfcxke570j20kk0aljt4.jpg)

