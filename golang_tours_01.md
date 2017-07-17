
# [Golang Tours - 01](https://tour.golang.org/basics/1)

2017/07/15 23:00，开始记录学习Go中觉得不错的语法特性

### 关于IDE

First, use [GoSublime](https://github.com/DisposaBoy/GoSublime) to make live easier..

![GoSublime](https://raw.githubusercontent.com/DisposaBoy/GoSublime/master/ss/2.png)

如果一定要用vim，可以考虑带语意补全的[gocode](https://github.com/nsf/gocode)

## 包、变量和函数

### 支持多值返回

```go
func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

当然还有种对return返回值进行命名的方法，这样就可以直接操作返回值变量了：

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

不过过长的函数还是慎用，官方都说 `They can harm readability in longer functions.`

### 变量定义

```go
// 批量初始化
var i, j int = 1, 2
// 不同类型同时初始化
var c, python, java = true, false, "no!"
```

函数内还可以使用`:=`省略var来定义变量，但这些变量在函数外无法使用：

```go
// k只能在函数foo内访问
func foo() {
	k := 3
	...
}
```

还有种全局变量初始化带计算的，以及`complex128`的变量类型：

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)
```

更多数据类型可以看[Basic types](https://tour.golang.org/basics/11)一节

### 类型转换

类似python的语法，不过注意第二行Sqrt输入的浮点数转换，必须显示的进行：

```go
	var x, y int = 3, 4
	var f float64 = math.Sqrt(float64(x*x + y*y))
	var z uint = uint(f)
```

特别的，使用`:=`或未指定类型的var，会根据value自动选择数据类型

### 常量定义

`const Pi = 3.14`

也可以一次定义多个数值型常量

```go
const (
	// Create a huge number by shifting a 1 bit left 100 places.
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	// Shift it right again 99 places, so we end up with 1<<1, or 2.
	Small = Big >> 99
)
```

## 数据流控制

### for循环写法

类似C语言但不加括号，花括号也是必须的

```go
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
```

同样的，循环控制也可以拆开写：

```go
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
```

类似`while`的写法，可以省略头尾的分号：

```go
	sum := 1
	for sum < 1000 {
		sum += sum
	}
```

### if语句

可以先赋值后进行判断，不过此时v的作用域仅限于if内：

```go
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
	    return v+1
	}
```

### switch语句

switch除了类似if的赋值写法，还可以在case语句中执行计算操作：

```go
	today := time.Now().Weekday()
	switch time.Saturday {
	case today + 0:
		fmt.Println("Today.")
	case today + 1:
		fmt.Println("Tomorrow.")
	case today + 2:
		fmt.Println("In two days.")
	default:
		fmt.Println("Too far away.")
	}
```

进一步的，switch还可以省略条件（`switch true`），从而方便写较长的if判断：

```go
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
```

### defer

defer可以在当前函数返回时执行代码，比如用在关闭打开的文件句柄等

不过想使用好defer，需要了解其两个特性：

1. 执行顺序是类似栈的方式，先进后出的
2. 外部变量取值，会在调用defer时刻固定下来

```go
// 输出>> 4321
func main() {
    defer fmt.Print(1)
    defer fmt.Print(2)
    defer fmt.Print(3)
    defer fmt.Print(4)
    fmt.Print(">> ")
}
```

```go
// 输出>> 54321
func main() {
    i := 1
    for i <= 4 {
	    defer fmt.Print(i)
	    i += 1
	}
    fmt.Print(">> ")
    fmt.Print(i)
}
```

defer配合返回变量还能修改return的内容，猜猜看`test()`的返回值是多少？

```go
func test() (i int) {
    defer func() { i += 100 }()
    return 1
}
```

关于defer的详细测试，可以参考这篇文章：[Golang中defer的那些事](https://xiaozhou.net/something-about-defer-2014-05-25.html)。

-----------------------------------

未完待续...

> Written with [StackEdit](https://stackedit.io/).