## 变量

### 整型

查看数据类型 fmt.Printf 占位符 %T 查看变量的占用字节数 unsafe.Sizeof()

```go
package main
import (
    "fmt"
    _"unsafe" //下划线标识导入，如果没有就忽略未使用的保存
)


//函数外部定义的变量就是全局变量

// var g1 = 100
//多个全局变量也可以这样定义，等价

var (
    g1 = 100
)



func main() {

//常规赋值
var a int = 10
fmt.Println("i= ", a)

//不赋值 使用默认值 0
var b int
fmt.Println("f=", b)


//类型自动推导
var c = 20
fmt.Println("i= ", c)
  
//省略 var， 声明时同时赋值。 左侧变量需要是新变量， 等于  var c = "hello"
d := "hello"
fmt.Println("c= ", d)

//多变量声明
var n1, n2, n3 int
fmt.Println("n1=", n1, "n2=", n2, "n3=", n3)
fmt.Printf("n1 %d n2 %d n3 %d \r\n", n1, n2, n3)



//多变量赋值
var m1, m2, m3 = 1, "b", "c"
fmt.Println("m1=", m1, "m2=", m2, "m3=", m3)



// 和下面等价
// m1, m2, m3 := 1, "b", "c"
// fmt.Println("m1=", m1, "m2=", m2, "m3=", m3)
fmt.Println("g1", g1)

//变量数据类型
//基本数据类型 和 派生/复杂数据类型
//基本数据类型

//数值型  整数 int(默认看系统位数)  int8(1字节)    int16 (2字节) int32 (4字节) int64(8字节)
//无符号   uint(默认看系统位数)   uint8 uint16    uint32  uint64
//浮点类型 float32 (4字节单精度) float64(8字节双精度)
//rune 是int32别名， 存储的是Unicode码 rune 和int32一样
//byte uint8 的别名

fmt.Printf("%f\n", 0.3)
fmt.Printf("%.10f\n", 0.3)
fmt.Printf("%.20f\n", 0.3)

//看上上面demo浮点慎用
fmt.Printf("%f\n", 0.5)
fmt.Printf("%.10f\n", 0.5)
fmt.Printf("%.20f\n", 0.5)

var aa = 0.1
var bb = 0.2
fmt.Println(0.1+0.2, aa+bb)


//查看数据类型
fmt.Printf("数据类型为：%T", aa)

//查看变量占用字节大小，和数据类型
fmt.Printf("数据类型为 %T占用字节数为，%d", aa, unsafe.Sizeof(aa))
//整数类型占用范围 有符号
//int8  -128 -127
//int16 -2^15 - 2^15-1
//int 32 -2^31 - 2^31-1
//int 64 -2^63 - 2^63-1


//无符号
//uint8 0 - 2^8-1
//uint16 0 - 2^16-1
//uint32 0 - 2^32-1
//uint64 0 - 2^64-1
//int  看系统是32位还是64位
//rune 和int32一样，  -2^31  - 2^31-1
//byte 和 uint8  一样  0-255
//字符串  无专门类型， 使用 byte 保存单个字母字符 (汉字存不进去至少需要3个byte)
//布尔类型 bool
//字符串 string  go 认为是一个基本数据类型
//指针 pointer
//数组
//结构体 struct
//管道  channel
//函数 channel
//切片 slice
//接口  interface
//集合 map
}
```

### 浮点

如果使用float，需要使用float64位

```go
num := 5.12
num2 := .123
//科学计数法
num3 := 5.123E2 //表示 5.123 x 10^2 次方
num4 := 5.1234E-2 //表示 5.1234 / 10^2次方
```

### 字符类型

golang 没有专门的字符类型，如果需要存储单个字符，一般使用byte保存（ASCII码）。

**字符串是由固定长度的字符连接起来的字符序列**，Go的字符串不同，是由字节组成的

```go
var c1 byte = 'a'
var c2 byte = 'b'
fmt.Println("c1=", c1, "c2=", c2) //c1= 97 c2= 98
```

byte直接打印是是ASCII码，如果需要输出字符，需要使用格式化输出

字符常量是单引号，中文是unicode需要用int存储

```go
fmt.Printf("c1=%c c2=%c", c1, c2)
c1=a c2=b
var c3 int = '北'
fmt.Printf("c1=%c c2=%c c3=%c ", c1, c2, c3)
fmt.Printf("c3 code %d", c3)
c1=a c2=b c3=北c3 code 21271

c4 := 22269
fmt.Printf("%c\n", c4) //国
```

字符类型可以进行运算，相当于一个整数(比如可以遍历字符)

```go
var n1 = 10 + 'a'
fmt.Println("n1=", n1) //n1= 107
```

### 布尔类型

bool类型，占1个字节，只允许true 和 false，用在逻辑运算

```go
var b bool = false //bool 只能使用 true false
fmt.Println("b=", b, "byte:", unsafe.Sizeof(b)) //b= false byte: 1
```

if 条件控制语句

for循环控制语句



### 字符串

字符串是由单个字节连接起来的

```go
var c string = "helloworld"
fmt.Println(c, unsafe.Sizeof(c)) //helloworld 16
c[0] = 'a' //报错，字符串定义后里面的字符是不可变的
```

字符串一旦赋值了，整个字符串是不能修改的。

**双引号**，会识别转义字符

**反引号**，以原生形式输出，不进行任何的处理

**加号** 字符串拼接，➕两边都是字符串

```go
str := "hell" + "o wor"
str += "ld"
fmt.Println(str) //hello world
```

```go
str := "hell" + "o wor" +
		"o wor" +
		"o wor" +
		"o wor"
	str += "ld"
	fmt.Println(str)
```

**多字符串拼接换行，需要把加号放置到前面**

### 指针

指针类型，存储的是变量所在内存的地址。通过& 获取变量的指针（内存地址），通过*int 类型变量保存指针，通过 \* 变量，获取指针指向的变量原值。

```go
var i int = 23333333
fmt.Print("i=", &i) //i=0xc000014090

//定义了一个变量， 类型是 *int  代表指向了一个int类型的指针
var ptr *int = &i
fmt.Println("ptr=", ptr) //ptr=0xc000014090
//指针变量也有自己的内存地址，可以通过&查看指针变量的地址
fmt.Println("ptr的地址=", &ptr) //ptr的地址= 0xc000100020
fmt.Println("ptr指向的i值=", *ptr) //ptr指向的i值= 23333333

//通过指针修改变量i的值
*ptr = 100
fmt.Println("new i=", i)

```

- 所有的值类型，都有对应的指针类型，形式为\*数据类型，比如int的指针就是*int，float32对应的指针类型就是*float32。

- 值类型包括，基本数据类型，int系列，float系列，bool，string，**数组**和**结构体 struct**。

### 值类型和引用类型

1. 值类型，基本数据类型 int系列，float系列，bool，string，数组和结构体struct

   ​	值类型，变量直接存储至，内存通常在栈中分配

2. 引用类型，指针，slice切片，map，管道chan，interface等都是引用类型

   ​	变量存储的是地址，内幕才能通常在堆上分配，当没有任何引用时，该地址对应的数据空间就变成垃圾，由GC来回收



### 标识符的命名规范

变量，方法，函数等命名时使用的字符序列，称为标识符

命名规则

1. 英文字母，大小写，数字，下划线

2. 数字不能开头

3. 不能包含空格

4. 大小写敏感

5. 下划线是go的特殊标识符，称为空标识符，仅作为占位符，不能使用

6. 保留字25个关键字不能用 break，default，func，interface，select，case，defer，go，map，struct，chan，else，goto，package，switch，const，fallthrough，if，range，type，continue，for，import，return，var

7. 预定义标识符，其中包含基础数据类型，和系统内嵌函数

   append，bool，byte，cap，close，complex，complex64，complex128，uint16，copy，false，float32，float64，imag，int，int8，int16，int32，int64，iota，len，make，new，nil，panic，uint64，print，println，real，recover，string，true，uint，uint8，uintprt。

**标识符命名注意事项**

1. 尽量要求包名和文件夹名称保持一致,简短有意义，不要和标准库冲突
2. 变量名，常量名，函数名，采用驼峰法。举例 stuName
3. 如果变量名，函数名，常量名，首字母大写，则可以被其他的包访问；如果是小写，则只能在本包中使用。



## 基本数据类型默认值

```go
var a int //0
var b float32 //0.000000
var c float64 //0.000000
var isBool bool // false
var name string // ""
```





## 数据类型转换

### 基本数据类型转换

必须显示转换**，不能自动转换， 不管是高精度到低精度，还是低精度到高精度

转换表达式 T(v)，将变量 v 转换为类型T

T int32 int64 float32 等

```go
var i int = 100
var n1 = float32(i)
fmt.Printf("%v", n1) //fmt.Printf("%T",i) int32
```

**细节说明**

- 被转换的是变量存储的数据，（即值），变量本身的类型还是原来的类型。
- 转换中，如果是从大范围转换到小范围(int64 转 int8)，编译不会报错，只是结果会溢出

数据类型转换运算的示例：

```go
var n1 int32 = 12
var n2 int64
var n3 int8

n2 = n1 + 20//x  n2 = int64(n1) + 20
n3 = n1 + 20//x   n3 = int8(n2) + 20

```

![image-20200413160523766](/Users/anythink/Library/Application Support/typora-user-images/image-20200413160523766.png)

### 基本数据类型和String的转换

方式1， fmt.Sprintf

```go
foo := fmt.Sprintf("%q\n", num)
//Sprint
fmt.Printf("%s %T\n", foo, foo)
```

方式2，使用 strconv 包函数

## 

```go
import "strconv"
//strconv包实现了基本数据类型和其字符串表示的相互转换。
var num int = 99
str := strconv.FormatInt(int64(num), 10)
fmt.Printf("type %T  str=%q", str, str) //type string  str="99"


//func Itoa
//func Itoa(i int) string
//Itoa是FormatInt(i, 10) 的简写。

str3 := strconv.Itoa(20)
fmt.Printf("type %T  str=%q", str3, str3) //type string  str="20"


//FormatFloat 函数将浮点数表示为字符串并返回。
//bitSize表示f的来源类型（32：float32、64：float64），会据此进行舍入。
//fmt表示格式：'f'（-ddd.dddd）、'b'（-ddddp±ddd，指数为二进制）、'e'（-d.dddde±dd，十进制指数）、'E'（-d.ddddE±dd，十进制指数）、'g'（指数很大时用'e'格式，否则'f'格式）、'G'（指数很大时用'E'格式，否则'f'格式）。
var num2 float64 = 23.456
str2 := strconv.FormatFloat(num2, 'f', 2, 64)
fmt.Printf("type %T  str=%q", str2, str2) //type string  str="23.46" 会自动进行四舍五入
```



### string 转基本数据类型

1，使用strconv 包的函数

```go
//转为bool
func ParseBool(str string) (value bool, err error)
//转为int  参数  字符串，进制，位
func ParseInt(s string, base int, bitSize int) (i int64, err error)
func ParseUint(s string, base int, bitSize int) (n uint64, err error)
func ParseFloat(s string, bitSize int) (f float64, err error)


b, err := strconv.ParseBool("true")
if err == nil {
  fmt.Printf("type %T  str=%v", b, b)
} else {
  fmt.Println("errror", err)
}

n, _ := strconv.ParseInt(str, 10, 0)
fmt.Printf("%T %d=", n, n)//int64 123123
```

**无法将string转换为基本数据类型的，将会转成0**

```go
var str string = "hello"
var n int64 = 3
n, _ = strconv.ParseInt(str, 10, 64)
fmt.Printf("type %T  str=%v", n, n) //type int64  str=0
```

无法将string转换成bool的，也会转为默认值 false

```go
var str string = "hello"
var n bool = true
n, _ = strconv.ParseBool(str)
fmt.Printf("type %T  str=%v", n, n) //type bool  str=false

```











