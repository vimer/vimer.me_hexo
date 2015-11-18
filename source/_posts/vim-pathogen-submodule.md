title: "让vim插件管理更加灵活:pathogen结合submodule"
date: 2015-04-14 16:40:43
tags: [vim, Git]
---
使用pathogen管理插件已经非常方便, 但是还不够给力 ，尤其换了新环境 , 如果直接把插件直接commit到自己库下，显然太暴力.

使用git submodule可以让插件管理更加灵活.
添加插件:
```
git submodule add git@github.com:fatih/vim-go.git /vim/bundle/vim-go
```
新环境配置:
```
git submodule init
git submodule update
```

删除submodule:
```
git rm --cached vim-javascript
rm -rf bundle/vim-javascript
```

添加新的:
```
git submodule add --force git@github.com:InfoGeeker/vim-javascript.git vim-javascript
```

<!--more-->
只需要携带.gitmodules就可以了, 一起用起来是那么nice :)

![submodule](http://ww4.sinaimg.cn/large/744e593bgw1er6dnuc1obj20dn0fvn0m.jpg)

\#注意，迁移到新的git库时，需要git submodule add 每条插件一一添加.

