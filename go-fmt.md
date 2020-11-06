## fmt包

### 接收键盘输入语句

func [Sscanln](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#109)

```
func Sscanln(str string, a ...interface{}) (n int, err error)
```

Sscanln类似Sscan，但会在换行时才停止扫描。最后一个条目后必须有换行或者到达结束位置。

```go
var name string
var age byte
var sal float32
var isPass bool

fmt.Print("请输入姓名:")
fmt.Scanln(&name)
fmt.Print("请输入年龄:")
fmt.Scanln(&age)
fmt.Print("请输入薪水:")
fmt.Scanln(&sal)
fmt.Print("请输入是否通过:")
fmt.Scanln(&isPass)
fmt.Println("name=", name, "age=", age, "sal=", sal, "isPass=", isPass)
```

func [Sscanf](https://github.com/golang/go/blob/master/src/fmt/scan.go?name=release#116)

```
func Sscanf(str string, format string, a ...interface{}) (n int, err error)
```

Sscanf从字符串str扫描文本，根据format 参数指定的格式将成功读取的空白分隔的值保存进成功传递给本函数的参数。返回成功扫描的条目个数和遇到的任何错误。

```go
fmt.Println("请输入姓名 年龄 薪水 是否通过， 空格分隔:")
fmt.Scanf("%s %d %f %t", &name, &age, &sal, &isPass)
fmt.Println("name=", name, "age=", age, "sal=", sal, "isPass=", isPass)
```

### 字符串相关

### 包buildin 内建函数不需要导包

#### 字符串长度

func [len](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#155)

```go
func len(v Type) int //按字节返回，如果带1个中文统计会多3 带中文的需要转为切面 []rune(str)
```

内建函数len返回 v 的长度，这取决于具体类型：

```
数组：v中元素的数量
数组指针：*v中元素的数量（v为nil时panic）
切片、映射：v中元素的数量；若v为nil，len(v)即为零
字符串：v中字节的数量
通道：通道缓存中队列（未读取）元素的数量；若v为 nil，len(v)即为零
```

```go
strconv.Atoi("12") //字符串转整数
strconv.Itoa(12) //整数转字符串
```

func [new](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#187)

```
func new(Type) *Type
```

内建函数new分配内存。其第一个实参为类型，而非值。其返回值为指向该类型的新分配的零值的指针。

```go
//给值类型分配内存
num1 := 100
fmt.Printf("%T %v %v\n", num1, num1, &num1)
//int 100 0xc000014090

num2 := new(int) //*int new出来的是指针类型
fmt.Printf("%T ,%v ,%v ,%v\n", num2, num2, &num2, *num2)
//*int ,0xc0000140b0 ,0xc00000e030 ,0
```



func [make](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#182)

```
func make(Type, size IntegerType) Type
```

内建函数make分配并初始化一个类型为切片、映射、或通道的对象。其第一个实参为类型，而非值。make的返回类型与其参数相同，而非指向它的指针。其具体结果取决于具体的类型：

```
切片：size指定了其长度。该切片的容量等于其长度。切片支持第二个整数实参可用来指定不同的容量；
     它必须不小于其长度，因此 make([]int, 0, 10) 会分配一个长度为0，容量为10的切片。
映射：初始分配的创建取决于size，但产生的映射长度为0。size可以省略，这种情况下就会分配一个
     小的起始大小。
通道：通道的缓存根据指定的缓存容量初始化。若 size为零或被省略，该信道即为无缓存的。
```

### strconv包

#### func [Atoi](https://github.com/golang/go/blob/master/src/strconv/atoi.go?name=release#195) 字符串转整数

```
func Atoi(s string) (i int, err error)
```

Atoi是ParseInt(s, 10, 0)的简写。

#### func [Itoa](https://github.com/golang/go/blob/master/src/strconv/itoa.go?name=release#24) 整数转字符串

```
func Itoa(i int) string
```

Itoa是FormatInt(i, 10) 的简写。

#### 字符串转 byte 切片

```go
var byte = []byte("hello")
```

#### byte转字符串

```go
var str = string([]byte{97,98,99}) //abc
```

#### 进制转换 strconv.FormatInt

进制转换func [FormatInt](https://github.com/golang/go/blob/master/src/strconv/itoa.go?name=release#18)

```
func FormatInt(i int64, base int) string
```

返回i的base进制的字符串表示。base 必须在2到36之间，结果中会使用小写字母'a'到'z'表示大于10的数字。

func [FormatUint](https://github.com/golang/go/blob/master/src/strconv/itoa.go?name=release#10)`func FormatUint(i uint64, base int) string`是FormatInt的无符号整数版本。

### strings包

func [Contains](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#112) 判断字符串s是否包含子串substr。

```
func Contains(s, substr string) bool
```



func [Count](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#65)返回字符串s中有几个不重复的sep子串。

```
func Count(s, sep string) int
```



func [EqualFold](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#674)判断两个utf-8编码字符串区部分大小写的比较

```
func EqualFold(s, t string) bool
```



func [Index](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#127) 子串sep在字符串s中第一次出现的位置，不存在则返回-1

```
func Index(s, sep string) int
```



func [LastIndex](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#164)子串sep在字符串s中最后一次出现的位置，不存在则返回-1

```
func LastIndex(s, sep string) int
```



func [Replace](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#638) 返回将s中前n个不重叠old子串都替换为new的新字符串，如果n<0会替换所有old子串

```
func Replace(s, old, new string, n int) string
```



func [Split](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#294) 用去掉s中出现的sep的方式进行分割，会分割到结尾，并返回生成的所有片段组成的切片

```
func Split(s, sep string) []string
```

（每一个sep都会进行一次切割，即使两个sep相邻，也会进行两次切割）。如果sep为空字符，Split会将s切分成每一个unicode码值一个字符串。



func [ToLower](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#437) 返回将所有字母都转为对应的小写版本的拷贝。

```
func ToLower(s string) string
```

func [ToUpper](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#434)返回将所有字母都转为对应的大写版本的拷贝。

```
func ToUpper(s string) string
```



func [TrimSpace ](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#613) 返回将s前后端所有空白（unicode.IsSpace指定）都去掉的字符串

```
func TrimSpace(s string) string
```

func [Trim](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#586)返回将s左右两边包含有cutset的字符串都去除

```
func Trim(s string, cutset string) string
```



func [TrimLeft](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#595)返回将s左边包含有cutset的字符串都去除

```
func TrimLeft(s string, cutset string) string
```



func [TrimRight](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#604)返回将s右边包含有cutset的字符串都去除

```
func TrimRight(s string, cutset string) string
```



func [HasPrefix](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#371) 判断s是否prefix开头

```
func HasPrefix(s, prefix string) bool
```



func [HasSuffix](https://github.com/golang/go/blob/master/src/strings/strings.go?name=release#376) 判断s是否suffix 结尾

```
func HasSuffix(s, suffix string) bool
```



### time 时间和日期包

```go
now := time.Now()

fmt.Printf("%v\n", now.Year())
fmt.Printf("%v\n", int(now.Month()))
fmt.Printf("%v\n", now.Day())
fmt.Printf("%v\n", now.Hour())
fmt.Printf("%v\n", now.Minute())
fmt.Printf("%v\n", now.Second())
//2020
//4
//20
//16
//21
//35
```

#### **格式化日期和时间**

```go
fmt.Printf("%02d-%02d-%02d %02d:%02d:%02d", now.Year(), now.Month(), now.Day(), now.Hour(),
		now.Minute(), now.Second())
//2020-04-20 16:25:11
```



func (Time) [Format](https://github.com/golang/go/blob/master/src/time/format.go?name=release#414)

```
func (t Time) Format(layout string) string
```

Format根据layout指定的格式返回t代表的时间点的格式化文本表示。layout定义了参考时间：

```
Mon Jan 2 15:04:05 -0700 MST 2006
```

格式化后的字符串表示，它作为期望输出的例子。同样的格式规则会被用于格式化时间。

```go
fmt.Println(now.Format("2006-01-02 15:04:05"))
//2006-01-02 15:04:05 是固定写法， 2006代表年， 01代表月， 02代表日， 15代表时，04代表分05代表秒
```

#### 时间常量

```go
type Duration
type Duration int64
Duration类型代表两个时间点之间经过的时间，以纳秒为单位。可表示的最长时间段大约290年。

const (
    Nanosecond  Duration = 1  //纳秒
    Microsecond          = 1000 * Nanosecond //微秒
    Millisecond          = 1000 * Microsecond  //毫秒
    Second               = 1000 * Millisecond //秒
    Minute               = 60 * Second //分
    Hour                 = 60 * Minute //时
)
常用的时间段。没有定义一天或超过一天的单元，以避免夏时制的时区切换的混乱。

要将Duration类型值表示为某时间单元的个数，用除法：
```

func [Sleep](https://github.com/golang/go/blob/master/src/time/sleep.go?name=release#9) Sleep阻塞当前go程至少d代表的时间段。d<=0时，Sleep会立刻返回。

```
func Sleep(d Duration)
```

```go
time.Sleep(time.Millisecond * 100) //100毫秒执行一次
```



func (Time) [Unix](https://github.com/golang/go/blob/master/src/time/time.go?name=release#830)Unix将t表示为Unix时间，即从时间点January 1, 1970 UTC到时间点t所经过的时间（单位秒）。

```
func (t Time) Unix() int64
```



func (Time) [UnixNano](https://github.com/golang/go/blob/master/src/time/time.go?name=release#838)

```
func (t Time) UnixNano() int64
```

UnixNano将t表示为Unix时间，即从时间点January 1, 1970 UTC到时间点t所经过的时间（单位纳秒）。如果纳秒为单位的unix时间超出了int64能表示的范围，结果是未定义的。注意这就意味着Time零值调用UnixNano方法的话，结果是未定义的。

```go
start := time.Now().Unix()

var str string = ""
for i := 0; i < 100000; i++ {
  str += "hello" + strconv.Itoa(i)
}
end := time.Now().Unix()
fmt.Println("run time: (s)", end-start)
```

