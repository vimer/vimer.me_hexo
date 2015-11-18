title: Go之简洁、灵活
date: 2015-04-21 17:45:35
tags: [Golang,闭包]
---
>看了两晚Go，大部分特性还是喜欢的，最令人"恶心"的是 类型写在变量后面了，真不知道设计人员怎么考虑的. 非要搞这么特殊?下面是一些自己比较喜欢的地方，计划下个项目开始用Go作为主力开发语言。


1.去掉C++的public和private，使用大小写字母进行区分,大写字母开头的变量是public，也就是可以被其他package读取；小写字母开头的相当于private，相当于此package的私有变量(公有/私有函数同理)
- - -
2.定义变量可以不声明类型，非常灵活(第一行代码就是"恶心"的把类型写在变量后面)
```golang
var i int = 1
var i = 1
i := 1
```
<!--more-->
- - -
3.可以看到上面代码，语句结尾可以不写分号
- - -

4.switch的case中不用写break 
```golang
i := 10
switch i {
case 1:
    fmt.Println("i is equal to 1")
case 2, 3, 4:
    fmt.Println("i is equal to 2, 3 or 4")
case 10:
    fmt.Println("i is equal to 10")
default:
    fmt.Println("All I know is that i is an integer")
}
```
- - -

5.函数可以一次性返回多个值
```golang
func funcName(input1 type1, input2 type2) (output1 type1, output2 type2) {
     return value1, value2
}
```
- - -
6.defer关键字更好的释放资源
```javascript
/*C写法*/
file.Open("file")
if failureX {
    file.Close()
    return false
}

if failureY {
   file.Close()
   return false
}
file.Close()

/*go的defer写法*/
file.Open("file")
defer file.Close()
if failueX {
	return false 
}

if failueY {
	return false
}

return true

```
- - -
7.更简单的多重赋值
```golang
x, y = 1, 2
//交换两个变量的值可以这样了:
a, b = b, a
```
8.没有while，只有for, 比如以前的while(1)可以用没有条件的for{}代替
- - -
9.switch中的case可以写表达式:
```golang
switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
}
```
- - -
10.自带Map结构, 这相对于C来说会方便很多吧
- - -
11.Go函数支持闭包
什么? 你问什么是闭包? 你可以运行下面程序.
```golang
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}
func main() {
	pos := adder()
	fmt.Println(pos(1))
	fmt.Println(pos(2))
	fmt.Println(pos(3))
}
```
你会发现值不是1，2，3而是1，3，6
简言之:
* 闭包就是函数的局部变量集合，只是这些局部变量在函数返回后会继续存在。
* 闭包就是就是函数的“堆栈”在函数返回后并不释放，我们也可以理解为这些函数堆栈并不在栈上分配而是在堆上分配
* 当在一个函数内定义另外一个函数就会产生闭包
- - -
12.Go没有类，但是支持面向对象，比如下面这段代码可以在结构体定义方法
```golang
type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```
- - -
13.指针更自动化
```golang
(*h).name 可以写为 h.name
```
- - -
14.自动垃圾回收机制

15.逃离Makefile、CMake等组建工具，工程层面解决组建
```golang
<calcproj> 
├─<src>
	├─<calc> 
		├─calc.go
	├─<simplemath>
   		├─add.go
		├─add_test.go 
		├─sqrt.go 
		├─sqrt_test.go
	├─<bin>
    ├─<pkg>#包将被安装到此处
```
- - -
16.以前自己使用C实现面向对象，现在就已经成为Go的一部分
- - -

17.语言层面解决并发问题，这也是暂时抛弃选择Go的原因
- - -

