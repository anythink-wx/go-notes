## 运算符

### 算数运算符

+，-，*，/，++，--，%

```go
//如果参加运算的都是整数，那么返回的也是整数部分
fmt.Println(10 / 4) //2

var n1 float64 = 10 / 4
fmt.Println(n1) //2

var n1 float64 = 10.0 / 4
fmt.Println(n1) //2.5

//公式 a%b = a - a / b * b
fmt.Println(-10 % 3) // -1

var i int = 10
i++
fmt.Println(i)
i--
fmt.Println(i)
//加减乘是一个道理

weeek := 97 / 7
day := 97 % 7
fmt.Println("week", weeek, "day", day) //week 13 day 6

var hua float32 = 184
var she float32 = 5.0 / 9 * (hua - 100)  //除法带小数 除数如果是整数要加小数点
fmt.Println("hua", hua, "she", she) //hua 184 she 46.666668
```

细节说明

1. 对于除号，整数除和小数除是有区别的，整数之间做除法时，只保留整数部分，舍去小数部分。
2. 当对一个数取模时，可以等价于 a%b = a-a/b*b
3. go的自增自减只能当做一个独立的语句使用，不能 b:=a++ 或者 b:= a--没有 ++b，--b
4. golang设计者去除了c/java中自增自减容易混淆的写法，让golang更简洁统一。

### 赋值运算符

复合运算符=，+=，-=，*=，/=，%=*，运算顺序从右往左

*a=b，a=a+b，a=a-b，a=a*b，a=a/b，a=a%b

位运算符 <<=，>>=，&=，^=，|=

a=a<<2，a=a>>2，a=a&2，a=a^2，a=a|2

```go
var a int = 9
var b int = 3
fmt.Println("a=", a, "b=", b)
a, b = b, a
fmt.Println("a=", a, "b=", b)
//a= 9 b= 3
//a= 3 b= 9
```

### 关系运算符

结果为bool，用在if结构或者循环结构

==，!=，<，>，<=，>=

```go
var a int = 9
var b int = 8

fmt.Println(a > b)//true
fmt.Println(a == b)//false
fmt.Println(a < b)//false
fmt.Println(a != b)//true
fmt.Println(a >= b)//true
fmt.Println(a <= b)//false

flag := a > b
fmt.Println("flag=", flag) //flag= true
```

### 逻辑运算符

&&，||，！

```go
var a bool = true
var b bool = false

fmt.Println(a && b) //false
fmt.Println(a || b)//true
fmt.Println(!(a && b))//true
var age int = 40
if age > 30 && age < 50 {
  fmt.Println("ok") //ok
}
if age > 30 || age < 50 {
		fmt.Println("ok") //ok
}
if !(age > 30) {
  fmt.Println("ok") // no run 
}
```

- && 也叫做短路与， 如果第一个条件已经为false，则第二个条件就不会判断，最终返回false
- ||也叫短路或，如果第一个条件已经为true，则第二个条件不会判断，最终返回true

```go
func test1() bool {
	fmt.Println("test1")
	return true
}
func test2() bool {
	fmt.Println("test2")
	return false
}

if test2() && test1() {
	fmt.Println("ok")
}
//test2 因为test2已经返回false，test1 不会执行

if test2() || test1() {
  fmt.Println("ok")
}
//test2 因为test2返回false，所以会继续执行test1
//test1 因为test1返回true，所以短路或最终返回true
//ok

if test1() || test2() {
  fmt.Println("ok")
}
//test1 因为test1 已经返回 true， 所以test2不会执行
//ok  最终返回ok
```



### 其他运算符

& 返回变量的存储地址。

*指针变量，取指针变量的值

```go
var a int = 100

var ptr *int = &a
fmt.Println("ptr=", ptr)//ptr= 0xc0000b2008
fmt.Println("ptr=", *ptr) //ptr= 100
*ptr = 101
fmt.Println("ptr=", *ptr) //ptr= 101
```

### 特别说明

go 没有三元运算符， 需要用 if  else 去解决

### 运算符优先级

后缀，单目，乘法，加法，位移，关系，关系，按位AND，按位XOR，按位OR，逻辑AND，逻辑OR，赋值，逗号。

![image-20200414105804027](/Users/anythink/Library/Application Support/typora-user-images/image-20200414105804027.png)



### 位运算符

进制变量的定义

```go
var a int = 1000
fmt.Printf("%b 二进制\n", a) //1111101000 二进制

//8进制需要 0 开头
var b int = 011
fmt.Printf("%d 十进制\n", b) //9 十进制
//16 进制需要  0x 开头
var c int = 0x11
fmt.Printf("%d 十进制\n", c) //17 十进制
```

**其他进制到10进制的转换**

2进制转10进制规则：从最低位开始（右侧）将每个位上的数取出，乘以2的（位数-1次方），然后求和

1011转成10进制： 1 + 1*2^1 + 0\*2^2 + 1\*2^3 = 1 + 2 + 0 + 8 = 11

8进制转10进制规则：从最低位开始（右侧）将每个位上的数取出，乘以8的（位数-1次方），然后求和

0123 = 3*8^0 + 2\*8^1 + 1\*8^2 = 3 + 16 +64 = 83

16进制转10进制：从最低位开始（右侧）将每个位上的数取出，乘以16的（位数-1次方），然后求和

0x34A = 10*16^0 + 4\*16^1 + 3\*16^2 = 10 + 64 +768 = 842

以此类推



**10进制到其他进制的转换**

**10进制转二进制**

规则：将该数不断除以2，直到商为0为止，然后将每步得到的余数倒过来。

56转为2进制： 56/2 = 28 y0， 28/2 = 14 y0， 14/2 = 7 y0，7/2 =3 y1， 3/2 =1 y1 ，结果：111000

**10进制转8进制**

规则：将该数不断除以8，直到商为0为止，然后将每步得到的余数倒过来。

156转为8进制：156/8 = 19 y4， 19/8 = 2  y3    结果：234

345转为8进制：345/8=43 y1，43/8=5 y=3 结果：531

**10进制转16进制**

将该数不断除以16，直到商为0为止，然后将每步得到的余数倒过来。

356转成16进制： 356/16=22 y4， 22/16= 1 y6 结果：0x164

**2进制转8进制**

将二进制数每三位（能表示8 ，2^3）一组（从最低位开始组合）转成对应的八进制数即可。

11010101= 101 <->8 =5 ，010 <->8 =2，11 <->8 = 3   结果：0325

**2进制转16进制**

将二进制数每4位（能表示16，2^4）一组（从最低位开始组合）转成对应的16进制数即可。

11010101= 0101 <->16=5 ，1101  <->16=13 结果：0xD5

**8进制转换2进制**

将8进制每一位，转换成对应的一个三位的2进制数即可（从最低位开始组合）。

0237 = 10  011 111

**16进制转2进制**

将16进制每一位，转换成对应的一个四位的2进制数即可（从最低位开始组合）。

0x237=  10 0011 0111

**原码，反码，补码**

对于有符号的数而言：

1. 二进制的最高位是符号位， 0整数，1负数 1=> [0000 0001] -1 > [1000 0001]

2. 正数的原码，反码，补码都一样

3. 负数的反码= 原码符号位不变，其他位取反 1 => [0000 0001] 反码 [0000 0001] 补码 [0000 0001]

   -1 => [1000 0001] 反码 [1111 1110] 

4. 负数的补码=它的反码+1 补码 [1111 1111] 计算机运算的时候都是以补码的方式运算

5. 0的反码，补码都是0

### 位运算符和移位运算符

**按位与： & 两位全为1，则为1，否则为0**

2:       0000 0010    

3:       0000 0011

2&3   0000 0010

**按位或：|  有一位为1，则为1，否则为0**

2:       0000 0010    

3:       0000 0011

2|3   0000 0011

**按位异或：^ 两位一个为0，一个为1，则为1，否则为0**

2:       0000 0010    

3:       0000 0011

2^3   0000 0001

--------

-2：  1000 0010 反码 1111 11101  补码 1111 1110

 -2     1111 1110 (负数需要用补码来操作)

  2     0000 0010

-2^2 1111 1100 (补码要返回成原码)

补码到反码（-1）   11111011  反码到原码取反  1000 0100 = -4

### 移位运算(都是补码)

\<< 位左移 低位溢出，符号位不变，并用符号位补溢出的高位

\>> 位右移，符号位不变，低位补0

1>>2

0000 0001 => 0000 0000 

1<<2

0000 0001 

0000 0100

1<<4

0000 0001

0000 1000

