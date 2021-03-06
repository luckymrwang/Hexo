title: ' 闭包(closure)与协程共用时要注意的事情'
date: 2016-06-13 13:20:18
tags: [Go]
toc: true
---

闭包是一种可以让你用非常舒服的方式来编程的小技巧，Go也支持闭包。如果从来没有接触过闭包，想在一开始就弄懂什么是闭包(closure)是非常困难的，就像递归一样，直到你真正写过、用过它，你才能真正的对它有一个更具体的认识。

闭包就是一个函数，这个函数包含了运行它所需的上下文环境，这个环境可能是几个变量或者也会是其他的(通常就是变量)。说闭包是一个函数不正确，更确切地说，闭包是一个打包了其作用域外部的上下文环境的一段运行环境。如果一时间没有理解这段闭包的含义也不要紧，这是一个循序渐进的过程。
<!-- more -->
那么我们来看一个网上最通用的讲解闭包的例子：

```go
package main

import "fmt"

func A() func() int {
	value := 0
	return func() int {
		value++
		return value
	}
	
	// A()到这里时按理说应该已经销毁掉了局部变量value
}

func main() {
	B := A()
	fmt.Println(B())
	fmt.Println(B())
	fmt.Println(B())
}
```

运行结果为：

```go
1
2
3
```
 
好好看下这段代码，我们定义一个函数A()，在A中我们又定义了另一个函数B()，而且B将会作为返回值返回给我们。B的作用就是每次调用都会给A的局部变量value增1。但是当A函数运行完之后，按理说局部变量value应该已经被销毁了，B无法再对其进行操作，但是从运行结果来看，value却还"活着"。没错，这就是所谓的打包了上下文环境的函数，B就是一个闭包函数，它不仅定义了自己的操作流程，而且附带着它的上文环境A中的value变量。

从这个例子你可能没太看出闭包有什么作用，但是其实闭包这个东西或多或少会要求编程语言支持匿名函数并且函数在该语言中式第一类型(可以把函数赋值给变量，可以用函数当参数和返回值)。闭包的作用我也不大好说，像JS这种缺陷比较多的语言使用闭包可以带来保护变量的访问权限、更好的模块化这样的好处，而对于Go，我觉得闭包最大的好处就是：

1. 不用特意给某个函数取名字了，省事儿~
2. 可以把某个临时使用的函数定义在最近的地方
3. 和goroutine使用的时候可以直接用go func() {...}() 这样的写法，这就不用把每个要并发的函数都在当前运行环境的外部预先定义一遍了。

讲完了闭包的基本内容，接下来就讲一讲使用闭包时应该注意的问题了。原则上讲，闭包用起来的最大优点就是方便，但是不要在任何情况下都首先想到使用闭包，因为如果你对闭包缺乏了解，那你写出的代码很可能会有意外的运行效果。来看一下下面的例子：

```go
package main

import (
	"fmt"
)

func main() {
	done := make(chan bool, 3)
	for i := 0; i < 3; i++ {
		go func() { //这里是有问题的，每个routine都打包了外层运行环境中的变量i
			fmt.Println(i)
			done <- true
		}()
	}
	for i := 0; i < 3; i++ {
		<-done
	}
}
```

运行结果是：

```go
3
3
3
```

如果你仔细看下代码的话，应该会觉得代码看上去没有任何问题。但是结果为甚不是1 2 3呢？其实Go的官方指南也给出了相关的例子让开发者们注意闭包和goroutine一起使用时要注意的这个问题。
这个例子中其实第一个循环中的三个goroutine在创建之后都不会立刻运行，因为在他们都绑定了外层运行环境中的变量i，因为外层的运行环境随时会更改i的值，所以知道这个循环结束，三个routine都不能开始运行。

这时的解决办法就是把要用到的外层上下文环境作为参数传递给闭包函数，像这样：

```go
package main

import (
	"fmt"
)

func main() {
	done := make(chan bool, 3)
	for i := 0; i < 3; i++ {
		go func(index int) { // 把外层环境中的i当做参数传进来
			fmt.Println(index)
			done <- true
		}(i) // 把i传进去
	}
	for i := 0; i < 3; i++ {
		<-done
	}
}
```

这样的话闭包函数就不需要再绑定外层环境中的那个变量i了。这个问题一定要注意，因为Go里会经常将goroutine和闭包配合使用，如果没有处理好上下文打包的问题，就很可能引发意外的运行效果。

引自[这里](http://blog.csdn.net/gophers/article/details/40505683)
