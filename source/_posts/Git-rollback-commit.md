title: Git回滚commit
date: 2015-08-04 16:42:32
tags: [Git]
---

场景如下： 
当你在某个工程下想回滚到以前的一个commit的时候，对此commit的版本做了许多修改，并想废弃commit之后的代码，以此为代码提交到master上。

![两个提交版本](http://ww3.sinaimg.cn/large/744e593bgw1euqnjuytixj21kw0fn40f.jpg)

第一个commit 78e9434c9 只有一个file1

第二个commit 072c87740  有file1 和 file2

现在回退到第一个commit, 且以第一个commit更名为master ：
<!--more-->

```
$>>git checkout 78e9434c9                                                                                                                                                                                                             Note: checking out '78e9434c9'.

HEAD is now at 78e9434... fix
```

```
$>>git branch                                                                                                                                                                                                                         * (detached from 78e9434)
  master
```

```
$>>git checkout -b test                                                                                                                                                                                                               Switched to a new branch 'test'
```

```
$>>git branch -m master old-master                                                                                 ```                                    

```
$>>git branch -m test master                                                                                       ```  

```
$>>git push -f origin master                                                                                                                                                                                                          Total 0 (delta 0), reused 0 (delta 0)

To git@XXXXXXX:XXXX/test.git
 + 072c877...78e9434 master -> master (forced update)
```

还有一种情况是，新的branch也可以直接合并到master上
```
git merge test
```
不过这种处理情况相对知晓的比较多。

