title: Go中strings的常用方法
date: 2020-09-16 20:39:05
tags: [Go]
---
#### Compare
- func Compare(a, b string) int

按照字典序比较两个字符串，通常情况下直接使用=，>，<会更快一些。

#### Contains，ContainsAny 和 ContainsRune

- func Contains(s, substr string) bool
- func ContainsAny(s, chars string) bool
- func ContainsRune(s string, r rune) bool

<!-- more -->
字符串s中是否包含substr，返回true或者false。

```go
fmt.Println(strings.Contains("seafood", "foo")) // true
fmt.Println(strings.Contains("seafood", "bar")) // false
fmt.Println(strings.Contains("seafood", "")) // true 
fmt.Println(strings.Contains("", "")) // true 
```

ContainsAny用于判断子串中是否具有一个字符在源串s中。子串为空，返回false。

```go
fmt.Println(strings.ContainsAny("team", "i")) // false
fmt.Println(strings.ContainsAny("fail", "ui")) // true
fmt.Println(strings.ContainsAny("ure", "ui")) // true 
fmt.Println(strings.ContainsAny("failure", "ui")) // true 
fmt.Println(strings.ContainsAny("foo", "")) // false
fmt.Println(strings.ContainsAny("", "")) // false
```

ContainsRune用于判断Ascall码代表的字符是否在源串s中。

```go
// Finds whether a string contains a particular Unicode code point.
// The code point for the lowercase letter "a", for example, is 97.
fmt.Println(strings.ContainsRune("aardvark", 97))
fmt.Println(strings.ContainsRune("timeout", 97))
```

#### Count

- func Count(s, substr string) int

判断子串在源串中的数量，如果子串为空，则长度为源串的长度+1。

```go
fmt.Println(strings.Count("cheese", "e")) // 3
fmt.Println(strings.Count("five", "")) // before & after each rune 5=4+1
```

#### EqualFold

- func EqualFold(s, t string) bool

在不区分大小写的情况下，判断两个字符串是否相同。

#### Fields

- func Fields(s string) []string
- func FieldsFunc(s string, f func(rune) bool) []string

Fields：使用空白分割字符串。 FieldsFunc：根据传入的函数分割字符串，如果当前参数c不是数字或者字母，返回true作为分割符号。

```go
fmt.Printf("Fields are: %q", strings.Fields("  foo bar  baz   ")) //  ["foo" "bar" "baz"]

f := func(c rune) bool {
    return !unicode.IsLetter(c) && !unicode.IsNumber(c)
}
fmt.Printf("Fields are: %q", strings.FieldsFunc("  foo1;bar2,baz3...", f)) // ["foo1" "bar2" "baz3"]
```

#### HasPrefix 和 HasSuffix

- func HasPrefix(s, prefix string) bool
- func HasSuffix(s, suffix string) bool

判断字符串是否是以某个子串作为开头或者结尾。

```go
fmt.Println(strings.HasPrefix("Gopher", "Go")) // true
fmt.Println(strings.HasPrefix("Gopher", "C")) // false 
fmt.Println(strings.HasPrefix("Gopher", "")) // true 

fmt.Println(strings.HasSuffix("Amigo", "go")) // true 
fmt.Println(strings.HasSuffix("Amigo", "O")) // false
fmt.Println(strings.HasSuffix("Amigo", "Ami")) // false
fmt.Println(strings.HasSuffix("Amigo", "")) // true 
```

#### Join

func Join(elems []string, sep string) string

使用某个sep，连接字符串。

```go
s := []string{"foo", "bar", "baz"}
fmt.Println(strings.Join(s, ", ")) // foo,bar,baz
```

#### Index，IndexAny，IndexByte，IndexFunc，IndexRune

- func Index(s, substr string) int
- func IndexAny(s, chars string) int
- func IndexByte(s string, c byte) int
- func IndexFunc(s string, f func(rune) bool) int
- func IndexRune(s string, r rune) int

Index，IndexAny，IndexByte，IndexFunc，IndexRune都是返回满足条件的第一个位置，如果没有满足条件的数据，返回-1。

```go
fmt.Println(strings.Index("chicken", "ken")) // 4 
fmt.Println(strings.Index("chicken", "dmr")) // -1 

// 子串中的任意字符在源串出现的位置
fmt.Println(strings.IndexAny("chicken", "aeiouy")) // 2
fmt.Println(strings.IndexAny("crwth", "aeiouy")) // -1 

// IndexByte，字符在字符串中出现的位置
fmt.Println(strings.IndexByte("golang", 'g')) // 0 
fmt.Println(strings.IndexByte("gophers", 'h')) // 3
fmt.Println(strings.IndexByte("golang", 'x')) // -1

// IndexFunc 满足条件的作为筛选条件 
f := func(c rune) bool {
    return unicode.Is(unicode.Han, c)
}
fmt.Println(strings.IndexFunc("Hello, 世界", f)) // 7 
fmt.Println(strings.IndexFunc("Hello, world", f)) // -1 

// 某个字符在源串中的位置
fmt.Println(strings.IndexRune("chicken", 'k')) // 4 
fmt.Println(strings.IndexRune("chicken", 'd')) // -1 
```

#### LastIndex，LastIndexAny，LastIndexByte和LastIndexFunc

- func LastIndex(s, substr string) int
- func LastIndexAny(s, chars string) int
- func LastIndexByte(s string, c byte) int
- func LastIndexFunc(s string, f func(rune) bool) int

LastIndex，LastIndexAny，LastIndexByte，LastIndexFunc和Index，IndexAny，IndexByte，IndexFunc，IndexRune用法保持一致，从右往前计数。

#### Map

- func Map(mapping func(rune) rune, s string) string

对字符串s中每一个字符执行map函数中的操作。

```go
rot13 := func(r rune) rune { // r是遍历的每一个字符
    switch {
    case r >= 'A' && r <= 'Z':
        return 'A' + (r-'A'+13)%26
    case r >= 'a' && r <= 'z':
        return 'a' + (r-'a'+13)%26
    }
    return r
}
fmt.Println(strings.Map(rot13, "'Twas brillig and the slithy gopher..."))
```

#### Repeat

- func Repeat(s string, count int) string


重复一下s，count是重复的次数，不能传负数。

```go
fmt.Println("ba" + strings.Repeat("na", 2))
```

#### Replace和ReplaceAll

- func Replace(s, old, new string, n int) string
- func ReplaceAll(s, old, new string) string

使用new来替换old，替换的次数为n。如果n为负数，则替换所有的满足条件的子串。


```go
fmt.Println(strings.Replace("oink oink oink", "k", "ky", 2)) // oinky oinkky oink
fmt.Println(strings.Replace("oink oink oink", "oink", "moo", -1)) moo moo moo 
```

ReplaceAll使用new替换所有的old，相当于使用Replace时n<0。

#### Split，SplitN，SplitAfter和SplitAfterN

- func Split(s, sep string) []string
- func SplitAfter(s, sep string) []string
- func SplitAfterN(s, sep string, n int) []string
- func SplitN(s, sep string, n int) []string

```go
fmt.Printf("%q\n", strings.Split("a,b,c", ",")) // ["a","b","c"]
fmt.Printf("%q\n", strings.Split("a man a plan a canal panama", "a ")) // ["" "man " "plan " "canal panama"]
fmt.Printf("%q\n", strings.Split(" xyz ", "")) // [" " "x" "y" "z" " "]
fmt.Printf("%q\n", strings.Split("", "Bernardo O'Higgins")) // [""] 

// SplitN 定义返回之后的切片中包含的长度，最后一部分是未被处理的。
fmt.Printf("%q\n", strings.SplitN("a,b,c", ",", 2)) // ["a", "b,c"]
z := strings.SplitN("a,b,c", ",", 0) 
fmt.Printf("%q (nil = %v)\n", z, z == nil)  // [] (nil = true) 

// 使用sep分割，分割出来的字符串中包含sep，可以限定分割之后返回的长度。
fmt.Printf("%q\n", strings.SplitAfterN("a,b,c", ",", 2)) // ["a,", "b,c"]

// 完全分割 
fmt.Printf("%q\n", strings.SplitAfter("a,b,c", ",")) // ["a,","b,", "c"]
```

对于SplitN和SplitAfterN的第二个n说明。

```go
n > 0: at most n substrings; the last substring will be the unsplit remainder.
n == 0: the result is nil (zero substrings)
n < 0: all substrings
```

#### Trim，TrimFunc，TrimLeft，TrimLeftFunc，TrimPrefix，TrimSuffix，TrimRight，TrimRightFunc

- func Trim(s string, cutset string) string
- func TrimFunc(s string, f func(rune) bool) string
- func TrimLeft(s string, cutset string) string
- func TrimLeftFunc(s string, f func(rune) bool) string
- func TrimPrefix(s, prefix string) string
- func TrimSuffix(s, suffix string) string
- func TrimRight(s string, cutset string) string
- func TrimRightFunc(s string, f func(rune) bool) string

```go
// Trim 包含在cutset中的元素都会被去掉
fmt.Print(strings.Trim("¡¡¡Hello, Gophers!!!", "!¡")) // Hello, Gophers

// TrimFunc去掉满足条件的字符
fmt.Print(strings.TrimFunc("¡¡¡Hello, Gophers!!!", func(r rune) bool {
    return !unicode.IsLetter(r) && !unicode.IsNumber(r)
}))

// TrimLeft 去掉左边满足包含在cutset中的元素，直到遇到不在cutset中的元素为止
fmt.Print(strings.TrimLeft("¡¡¡Hello, Gophers!!!", "!¡")) // Hello, Gophers!!!

// TrimLeftFunc 去掉左边属于函数返回值部分，直到遇到不在cutset中的元素为止
fmt.Print(strings.TrimLeftFunc("¡¡¡Hello, Gophers!!!", func(r rune) bool {
    return !unicode.IsLetter(r) && !unicode.IsNumber(r) 
})) // Hello, Gophers!!!

// TrimPrefix 去掉开头部分；TrimSuffix 去掉结尾部分 
var s = "¡¡¡Hello, Gophers!!!"
s = strings.TrimPrefix(s, "¡¡¡Hello, ")
s = strings.TrimPrefix(s, "¡¡¡Howdy, ")
fmt.Print(s)
```

TrimRight，TrimRightFunc和TrimLeft，TrimLeftFunc功能保持一直，无需赘述。

#### 使用strings.Builder操作

>A Builder is used to efficiently build a string using Write methods. It minimizes memory copying. The zero value is ready to use. Do not copy a non-zero Builder.

strings.Builder使用Write方法来高效的构建字符串。它最小化了内存拷贝，耗费零内存，不要拷贝非零的Builder。


```go
var b strings.Builder
for i := 3; i >= 1; i-- {
    fmt.Fprintf(&b, "%d...", i)
}
b.WriteString("ignition")
fmt.Println(b.String())
```

输出结果：

```
3...2...1...ignition
```

strings.Builder作为字符串拼接的利器，建议加大使用力度。

```go
func (b *Builder) Cap() int // 容量，涉及批量内存分配机制
func (b *Builder) Grow(n int) // 手动分配内存数量
func (b *Builder) Len() int // 当前builder中含有的所有字符长度
func (b *Builder) Reset() // 清空builder
func (b *Builder) String() string // 转化为字符串输出 
func (b *Builder) Write(p []byte) (int, error) // 往builder写入数据 
func (b *Builder) WriteByte(c byte) error // 往builder写入数据 
func (b *Builder) WriteRune(r rune) (int, error) // 往builder写入数据  
func (b *Builder) WriteString(s string) (int, error) // 往builder写入数据 
```

#### 使用strings.Reader

```go
type Reader struct {
	s        string //对应的字符串
	i        int64  // 当前读取到的位置
	prevRune int   
}
```

>A Reader implements the io.Reader, io.ReaderAt, io.Seeker, io.WriterTo, io.ByteScanner, and io.RuneScanner interfaces by reading from a string. The zero value for Reader operates like a Reader of an empty string.

Reader通过读取字符串的方式，实现了接口io.Reader, io.ReaderAt, io.Seeker, io.WriterTo, io.ByteScanner和io.RuneScanner。零值Reader操作起来就像操作空字符串的io.Reader一样。

```go
func NewReader(s string) *Reader // 初始化reader实例
func (r *Reader) Len() int // 未读字符长度 
func (r *Reader) Read(b []byte) (n int, err error)  
func (r *Reader) ReadAt(b []byte, off int64) (n int, err error)
func (r *Reader) ReadByte() (byte, error)
func (r *Reader) ReadRune() (ch rune, size int, err error)
func (r *Reader) Reset(s string) // 重置以从s中读
func (r *Reader) Seek(offset int64, whence int) (int64, error) // Seek implements the io.Seeker interface. 
func (r *Reader) Size() int64 // 字符串的原始长度
func (r *Reader) UnreadByte() error
func (r *Reader) UnreadRune() error
func (r *Reader) WriteTo(w io.Writer) (n int64, err error) // WriteTo implements the io.WriterTo interface. 
```

#### Len，Size，Read

Len作用: 返回未读的字符串长度。

Size的作用:返回字符串的长度。

Read的作用: 读取字符串信息，读取之后会改变Len的返回值

```go
r := strings.NewReader("abcdefghijklmn")
fmt.Println(r.Len())   // 输出14  初始时，未读长度等于字符串长度
var buf []byte
buf = make([]byte, 5)
readLen, err := r.Read(buf)
fmt.Println("读取到的长度:", readLen) //读取到的长度5
if err != nil {
	fmt.Println("错误:", err)
}
fmt.Println(buf)            //adcde
fmt.Println(r.Len())        //9   读取到了5个 剩余未读是14-5
fmt.Println(r.Size())       //14   字符串的长度
```

#### ReadAt

- func (r *Reader) ReadAt(b []byte, off int64) (n int, err error)


读取偏移off字节后的剩余信息到b中，ReadAt函数不会影响Len的数值。

```go
r := strings.NewReader("abcdefghijklmn")
var bufAt, buf []byte
buf = make([]byte, 5)
r.Read(buf)
fmt.Println("剩余未读的长度", r.Len())   //剩余未读的长度 9
fmt.Println("已读取的内容", string(buf)) //已读取的内容 abcde
bufAt = make([]byte, 256)
r.ReadAt(bufAt, 5)
fmt.Println(string(bufAt))              //fghijklmn

//测试下是否影响Len和Read方法
fmt.Println("剩余未读的长度", r.Len())    //剩余未读的长度 9
fmt.Println("已读取的内容", string(buf))  //已读取的内容 abcde
```

#### ReadByte,UnreadByte

- func (r *Reader) ReadByte() (byte, error)
- func (r *Reader) UnreadByte() error

ReadByte从当前已读取位置继续读取一个字节。

UnreadByte将当前已读取位置回退一位，当前位置的字节标记成未读取字节。

ReadByte和UnreadByte会改变reader对象的长度。

```go
r := strings.NewReader("abcdefghijklmn")
//读取一个字节
b, _ := r.ReadByte()
fmt.Println(string(b))					// a
//int(r.Size()) - r.Len() 已读取字节数
fmt.Println(int(r.Size()) - r.Len())	// 1

//读取一个字节
b, _ = r.ReadByte()
fmt.Println(string(b))					// b
fmt.Println(int(r.Size()) - r.Len())	// 2

//回退一个字节
r.UnreadByte()
fmt.Println(int(r.Size()) - r.Len())	// 1

//读取一个字节
b, _ = r.ReadByte()
fmt.Println(string(b))	
```

#### Seek

- func (r *Reader) Seek(offset int64, whence int) (int64, error)


ReadAt方法并不会改变Len()的值，Seek的移位操作可以改变。offset是偏移的位置，whence是偏移起始位置，支持三种位置：io.SeekStart起始位，io.SeekCurrent当前位，io.SeekEnd末位。
offset可以是负数，当时偏移起始位与offset相加得到的值不能小于0或者大于size()的长度。

```go
r := strings.NewReader("abcdefghijklmn")

var buf []byte
buf = make([]byte, 5)
r.Read(buf)
fmt.Println(string(buf), r.Len()) //adcde 9

buf = make([]byte, 5)
r.Seek(-2, io.SeekCurrent) //从当前位置向前偏移两位 （5-2)
r.Read(buf)
fmt.Println(string(buf), r.Len()) //defgh 6

buf = make([]byte, 5)
r.Seek(-3, io.SeekEnd) //设置当前位置是末尾前移三位
r.Read(buf)
fmt.Println(string(buf), r.Len()) //lmn 0

buf = make([]byte, 5)
r.Seek(3, io.SeekStart) //设置当前位置是起始位后移三位
r.Read(buf)
fmt.Println(string(buf), r.Len()) //defgh 6
```

