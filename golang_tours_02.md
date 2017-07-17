
# [Golang Tours - 02](https://tour.golang.org/moretypes/1)

## 更多数据类型

### struct

结构体可以部分初始化，另外也能直接取结构体的地址赋值给p：

```go
type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)
```

### array

可以var定义，也可以直接`:=`加赋值：

```go
	var a [2]string
	a[0], a[1] = "Foo", "bar"

	primes := [6]int{2, 3, 5, 7, 11, 13}
```

### slices

类似python的截取方式，对数组进行操作（注意slices是对数组的引用）：

```go
primes := [6]int{2, 3, 5, 7, 11, 13}
var s []int = primes[1:4]
s[0] = 0
// 此时primes是 [2 0 5 7 11 13]
```

ps: go不支持`-1`这样取尾部的操作

当然slices也可以创建匿名结构，并且适应传入数组的大小：

```go
    s := []struct {
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
		{7, true},
		{11, false},
		{13, true},
	}
```

一般slices是静态大小的，越界会抛出`panic: runtime error: index out of range`。需要动态创建可以调用`make`：

```go
a := make([]int, 5)
```

往其中追加元素可以使用`a = append(a, 1)`，不过值得注意的是，slices是先分配一块更大cap的内存，然后将地址指向新内存来实现扩容的，如果可以尽可能先分配好大小。

关于slices的内存管理和内部实现，可以见[Go Slices: usage and internals](https://blog.golang.org/go-slices-usage-and-internals)。另外，学完后强烈建议练习下Slices的使用：[Exercise: Slices](https://tour.golang.org/moretypes/18)

### 二维slices

```go
	board := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	board[0][0] = "X"
	board[2][2] = "O"
	board[1][2] = "X"
	board[1][0] = "O"
	board[0][2] = "X"

	for i := 0; i < len(board); i++ {
		fmt.Printf("%s\n", strings.Join(board[i], " "))
	}
```

### range

range类似python的`enumerate`，返回数组下标和value：

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
    // for _, v := range pow 也是可以的，只访问value
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
```

### map

map的写法有点复杂，先是直接初始化的map

```go
type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
```

也可以用make初始化后再赋值：

```go
    m := make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
```

探测一个key是否存在可以

```go
	delete(m, "Answer")
	v, ok := m["Answer"]
```

不存在时ok会返回false

另外有个[关于词频统计的练习](https://tour.golang.org/moretypes/23)，能够帮助理解map的用法。这里直接给出答案：

```go
func WordCount(s string) map[string]int {
	resp := make(map[string]int)
	for _, v := range strings.Split(s, " ") {
		resp[v] += 1
	}
	return resp
}
```

### 闭包

go中闭包还挺难理解的，首先要明确几个特性。猜猜下面这段代码输出的是什么？

```go
    s := []string{"a", "b", "c"}                             
    for _, v := range s { 
        defer func() {
            fmt.Println(v)
        }()                 
    }
```

答案显然不是`a b c`。有记忆力好的同学不禁会说，不是说defer的`外部变量取值，会在调用defer时刻固定下来`么？

不过仔细看会发现，defer的func并没有传入任何参数，闭包内的v实际是对外部变量v的引用。因此三个defer输出的，都是v的最终值`c c c`

-----------------------------------

未完待续...

> Written with [StackEdit](https://stackedit.io/).