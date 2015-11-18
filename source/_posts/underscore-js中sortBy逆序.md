title: Underscore.js中sortBy逆序
date: 2014-12-20 00:51:44
tags: [underscore.js,Node.js]
---
Underscore.js中有个sortBy函数,可以支持数组和对象按照某一个字段的排序:
```javascript
var ret = _.sortBy([5,2,3,4,1], function(num) {
	return num;
});

>>
[1,2,3,4,5]
```
但是如果要进行逆序显示的话,underscore.js还没透明的支持reverse操作.这里其实可以借用对要排序的元素取负
```javascript
var ret = _.sortBy([5,2,3,4,1], function(num) {
	return -num;
});

>>
[5,4,3,2,1]
```
这样就可以很方便得实现逆序操作.

sortBy的强大之处在于对于对象其中某个元素的排序,如:

```javascript
var ret = _.sortBy([{name:"提", index:2}, {name:"琴", index:1}, {name:"疯",index:4},{name:"之",index:3}], function(data) {
	return -data.index;
});

>>
[ { name: '疯', index: 4 },
  { name: '之', index: 3 },
  { name: '提', index: 2 },
  { name: '琴', index: 1 } ]
```

