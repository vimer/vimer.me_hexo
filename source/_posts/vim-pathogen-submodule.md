title: "让Vim插件管理更加灵活:pathogen结合submodule"
date: 2015-04-14 16:40:43
tags: [vim, Git]
---
使用pathogen管理插件已经非常方便, 但是还不够给力 ，尤其换了新环境 , 如果直接把插件直接commit到自己库下，显然太暴力.

使用git submodule可以让插件管理更加灵活, 提几点稍微注意的地方.

- 第一使用插件(会在根[config]目录下生成.gitmodules):
<pre>
<code class="bash">
git submodule add git@github.com:fatih/vim-go.git /vim/bundle/vim-go
git submodule add ...
git submodule init
git submodule update
</code>
</pre>

- 配置迁移到新环境(.vim目录下执行):
<pre>
<code class="bash">
git submodule init
git submodule update
</code>
</pre>

- 删除submodule:
<pre>
<code class="bash">
git rm --cached vim-javascript
rm -rf bundle/vim-javascript
</code>
</pre>

- 添加新的:
<pre>
<code class="bash">
git submodule add --force git@github.com:InfoGeeker/vim-javascript.git vim-javascript
</code>
</pre>

<!--more-->
只需要携带.gitmodules就可以了, 一切用起来是那么nice :)

![submodule](http://ww4.sinaimg.cn/large/744e593bgw1er6dnuc1obj20dn0fvn0m.jpg)

> 注意，迁移到新的Git库时，需要git submodule add 每条插件一一添加.

