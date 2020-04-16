## 流程控制

### 顺序控制

默认执行顺序，Golang 定义变量时，**采用和合法的前向引用**



### 分支控制

单分支，双分支，多分支

#### 单分支

```go
if 条件表达式 {
	//执行代码块
}
var age byte
fmt.Println("请输入你的年龄")
fmt.Scanf("%d", &age)
if age >= 18 {
  fmt.Println("年满18")
}
```

**条件判断语句里，允许声明一个变量，该变量作用域只能在该条件逻辑内，其他地方就不起作用了。**

```go
if age := 20; age >= 18 {
	fmt.Println(age)//20
}
fmt.Println(age) //编译错误，undefined age
```

#### 双分支

```go
if 条件表达式 {
	//执行代码块
} else { //不能换行
 //执行代码块
}

//判断闰年  能被4 整除， 不能被100 整除， 或者 能被400 整除
var year int = 2020
if (year%4 == 0 && year%100 != 0) || year%400 == 0 {
  fmt.Println("yes", year)
} else {
  fmt.Println("no", year)
}
```

#### 多分支

```go
var score int
fmt.Println("请输入成绩")
fmt.Scanln(&score)
if score == 100 {
  fmt.Println("score 100", score)
} else if score >= 80 && score <= 99 {
  fmt.Println("score 80", score)
} else if score >= 60 && score < 80 {
  fmt.Println("score 60", score)
} else {
  fmt.Println("score no", score)
}
```

#### 嵌套分支

一般不宜超过3层

```go
var a, b, c bool = true, true, true

if a == true {
  if b == true {
    if c == true {

    }
  }
}
```



### switch分支

1. switch分支不用break
2. case 后面是一个表达式（常量，变量，一个有返回值的函数）
3. case 后面的数据类型，必须要和switch的数据类型一致
4. case 如果是字面量，不能重复
5. switch可以不带条件表达式

```go
var a int = 10
var b int = 50
switch {
  case a == 10: // a >= 5 && a<= 10 也可以
  fmt.Println("true")
  case b == 20:
  fmt.Println("false")
}
```

**Switch 穿透**

```go
var a int = 10
var b int = 50
switch {
  case a == 10:
  fmt.Println("true")
  fallthrough //增加fallthrogh 下一个case 也会执行
  case b == 20:
  fmt.Println("false")
}
```

Type Switch 可以用来判断某个 interface 变量中实际指向的变量类型

```go
var x interface{}
var y = 10.0
x = y
switch i := x.(type) {
case float64:
fmt.Printf("x= %T", i)
}
```

适用范围，具体数值不多，而且符合证书浮点数字符字符串几种类型，建议使用switch

如果是对区间判断，和结果为bool类型的判断，使用if。

### 循环控制

基本语法， for 循环变量初始化；循环条件，循环变量迭代



```go
for i := 0; i <= 100; i++ {
  fmt.Println(i)
}

//也可以放到外面，让i作用域变成for循环之外
i := 0
for ; i <= 100; i++ {
  fmt.Println(i)
}
fmt.Println(i)

i := 0
for i <= 100 { //也可以直接设置条件表达式
  fmt.Println(i)
  i++
}
fmt.Println(i)

for {
  fmt.Println("死循环") //通常需要配合 break 语句
}
```

#### For-range 遍历字符串和数组

```go
var str string = "hello world"

for i := 0; i < len(str); i++ { //普通遍历 按照字节遍历，如果有中文会乱码汉字是占3个字节，需要用到切片
  fmt.Printf("%c \n", str[i])
}

for idx, val := range str { //range 遍历  按照字符方式遍历，如果有中文正常显示
  fmt.Printf("idx:%d, val:%c \n", idx, val)
}
```



### go 没有 while do..while  语法

```go
//while
var i int = 0
for {
  if i > 10 {
    break
  }
  i++
  fmt.Println("i=", i)
}

//do while
i = 0
for {
  fmt.Println("i=", i)
  i++
  if i > 10 {
    break
  }
}
```

### 多重循环控制

```go
//打印金字塔
var level int = 20
for i := 1; i <= level; i++ {
  for k := 1; k <= level-i; k++ {
    fmt.Print(" ")
  }
  for j := 1; j <= 2*i-1; j++ {
    fmt.Print("*")
  }
  fmt.Println("")
}
//空心金字塔
var level int = 20

for i := 1; i <= level; i++ {
  for k := 1; k <= level-i; k++ {
    fmt.Print(" ")
  }
  for j := 1; j <= 2*i-1; j++ {
    if j == 1 || j == 2*i-1 || i == level {
      fmt.Print("*")
    } else {
      fmt.Print(" ")
    }
  }
  fmt.Println("")
}
```

```go
var num int = 1
rand.Seed(time.Now().Unix())
for {
  if num >= 99 {
    break
  }
  res := rand.Intn(100) + 1 // [0,99]
  num++
  fmt.Println(res)

}
```

### 跳转控制语句break

如果break语句出现在多层嵌套的语句块中，可以通过标签指明要终止的是哪一层。

设置标签  label1:，跳出标签  break label1

```go
var sum int
out:
	for i := 1; i <= 100; i++ {
		for j := 1; j <= 100; j++ {
			sum = i + j
			if i+j > 20 {
				fmt.Println("num=", i, "+", j, "=", sum)
				break out
			}
		}
	}
//num= 1 + 20 = 21
```

### 跳转控制语句continue 

结束本次循环，继续下次循环

如果continue语句出现在多层嵌套的语句块中，可以通过标签指明要循环哪一层。

设置标签  label1:，跳出标签  break label1

```go
var sum int
out:
for i := 1; i <= 100; i++ {
  for j := 1; j <= 100; j++ {
    sum = i + j
    if i+j > 20 {
      fmt.Println("num=", i, "+", j, "=", sum)
      continue out
    }
  }
}
```

### 跳转控制语句 goto

可以无条件地转移到程序中指定的行，goto语句可用来实现条件转移，跳出循环体等功能

```go
//label1:
//goto label1

var n = 1
fmt.Println("1")
if n < 10 {
  goto label1
}
fmt.Println("2")

label1:
	fmt.Println("3")
	fmt.Println("4")
```

### 跳转控制语句 return

跳出所在的方法或者函数