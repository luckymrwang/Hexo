title: Go读写锁RWMutex
date: 2016-09-29 10:19:22
tags: [Go笔记]
---

读写锁是针对于读写操作的互斥锁。

基本遵循两大原则：

1、可以随便读。多个gorouting同时读。

2、写的时候，啥都不能干。不能读，也不能写。

解释：

在32位的操作系统中，针对int64类型值的读操作和写操作不可能只由一个CPU指令完成。如果一个写的操作刚执行完了第一个指令，时间片换给另一个读的协程，这就会读到一个错误的数据。

<!-- more -->
RWMutex提供四个方法：

```go
func (*RWMutex) Lock //写锁定

func (*RWMutex) Unlock //写解锁

func (*RWMutex) RLock //读锁定

func (*RWMutex) RUnlock //读解锁
```

代码实例：

1、可以随便读：

```go
package main

 

import (

"sync"

"time"

)

 

var m *sync.RWMutex

 

func main() {

m = new(sync.RWMutex)

 

//可以多个同时读

go read(1)

go read(2)

 

time.Sleep(2 * time.Second)

}

 

func read(i int) {

println(i, "read start")

 

m.RLock()

println(i, "reading")

time.Sleep(1 * time.Second)

m.RUnlock()

 

println(i, "read end")

}
```

运行结果：

```go
1 read start

1 reading

2 read start

2 reading

1 read end

2 read end
```

可以看到1读还没结束（倒数第二行）的时候，2已经在读（倒数第三行）了。

2、写的时候啥也不能干：

```go
package main

 

import (

"sync"

"time"

)

 

var m *sync.RWMutex

 

func main() {

m = new(sync.RWMutex)

 

//写的时候啥都不能干

go write(1)

go read(2)

go write(3)

 

time.Sleep(4 * time.Second)

}

 

func read(i int) {

println(i, "read start")

 

m.RLock()

println(i, "reading")

time.Sleep(1 * time.Second)

m.RUnlock()

 

println(i, "read end")

}

 

func write(i int) {

println(i, "write start")

 

m.Lock()

println(i, "writing")

time.Sleep(1 * time.Second)

m.Unlock()

 

println(i, "write end")

}
```

输出：

```go
1 write start

1 writing

2 read start

3 write start

1 write end

2 reading

2 read end

3 writing

3 write end

```
可以看到：

1、1 write end结束之后，2才能reading

2、2 read end结束之后，3 才能writing

