## 接口  interface

接口

```go
package main

import "fmt"

type myInterFace interface {
	Start()
	Stop()
}

type phone struct {
	name string
}

func (p *phone) Start() {
	fmt.Println(p.name + " start")
}
func (p *phone) Stop()  {
	fmt.Println(p.name + " stop")
}

type device struct {
	Name string
}

func (d device) init(myifc myInterFace) {
	fmt.Println(d.Name)
	myifc.Start()
	myifc.Stop()
}

func main(){
	var d = device{"computer"}
	d.init(&phone{"iphone"})
  //computer
  //iphone start
  //iphone stop

}
```

定义一组方法， 不需要实现，**不能包含任何变量**。到某个自定义类型要使用时，根据情况把这些方法实现了。

基本语法

```go
type 接口名 interface {
  方法1(参数列表) 返回值列表
  方法2(参数列表) 返回值列表
}
```

被传入接口方法调用的结构体需要**实现全部方法**

**小节说明：**

接口里的方法都没有实现，接口体现了多态和**高内聚低耦合的思想**。

golang接口不需要显式的实现。只要传入的结构体符合口类型中的所有方法，那么这个变量就实现这个接口，因此，**go中没有implement这种关键字**

### 接口细节

1. 接口本身是不能创建实例，但是可以指向一个实现了接口的自定义类型的变量

   ```go
   type phone struct {
   	name string
   }
   
   func (p *phone) Start() {
   	fmt.Println(p.name + " start")
   }
   func (p *phone) Stop()  {
   	fmt.Println(p.name + " stop")
   }
   
   //直接掉接口变量报错
   //var my myInterFace
   //my.Start() //panic: runtime error: invalid memory address or nil pointer dereference
   
   //给接口变量传递一个有接口方法的结构体，因p *phone是指针类型，所以需要用new 或者  &phone{} 传递地址
   var my2 myInterFace  = new(phone)
   my2.Start()
   my2.Stop()
   
   ```

   

2. 接口的方法都没有实际方法。

3. 自定义类型，需要实现某个接口的全部方法，才能说自定义类型实现了该方法

4. 只要是自定义类数据类型，都可以实现接口，不仅仅是结构体类型

   ```go
   type myInt int
   
   func (m myInt) Start(){
   	fmt.Println("hello")
   
   }
   
   func (m myInt) Stop(){
   	fmt.Println("world")
   }
   
   func main(){
   	var my myInt = 10
   	var b myInterFace = my
   	b.Start()
   	b.Stop()
     //hello
     //world
   }
   ```

   

5. 一个自定义类型，可以实现多个接口

6. 接口**中不能有任何变量**

7. 一个接口，**可以继承**多个别的接口，此时**如果要实现接口**，需要**将别的接口方法也全部实现**。

   ```go
   package main
   
   type intface1 interface {
   	fun1 ()
   }
   
   type intface2 interface {
   	fun2()
   }
   
   type MasterFace interface {
   	intface1
   	intface2
   
   	fun3()
   }
   
   type stu struct {
   }
   
   
   func (stu stu) fun1() {
   }
   
   func (stu stu) fun2() {
   }
   func (stu stu) fun3() {
   }
   
   func main(){
   	var ma MasterFace = stu{}
   	ma.fun1()
   }
   ```

   

8. 接口类型默认是一个指针类，如果没有对interface 初始化就调用，就会输出nil

9. 空间口 interface{} 没有任何方法，所以**所有类型都实现了空接口**

### 接口最佳实践

结构体切片排序

```go
package main

import (
	"fmt"
	"math/rand"
	"sort"
	"time"
)

type Hero struct {
	Name string
	Age  int
}

type heloSlice []Hero

func (h heloSlice) Less(i, j int) bool {
	return h[i].Age > h[j].Age
}

func (h heloSlice) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h heloSlice) Len() int {
	return len(h)
}

func main() {

	var heros heloSlice

	rand.Seed(time.Now().UnixNano())

	for i:= 0; i<10;i++{
		heros = append(heros,Hero{"yy",rand.Intn(100)})
	}

	for _, v := range heros {
		fmt.Println(v.Name,v.Age)
	}

	sort.Sort(heros)
	fmt.Println("---------")
	
	for _, v := range heros {
		fmt.Println( v.Name,v.Age )
	}
/**
yy 0
yy 13
yy 50
yy 0
yy 52
yy 91
yy 71
yy 93
yy 52
yy 14
---------
yy 93
yy 91
yy 71
yy 52
yy 52
yy 50
yy 14
yy 13
yy 0
yy 0
**/
}

//修改成名字的排序
//func (h heloSlice) Less(i, j int) bool {
//	return h[i].Name < h[j].Name
//}
```

### 接口VS继承

继承

```go
package main

import "fmt"

type Monkey struct {
	Name string
}

type LittleMonkey struct { //继承一个 Monkey 结构体， 然后 LittleMonkey 进行功能扩展 Flying
	Monkey
}

type birdAble interface {
	Flying()
}

func (this *Monkey) pashu() { //Monkey 结构体的 方法
	fmt.Println(this.Name,"会爬树")
}

func (this *LittleMonkey) Flying(){ //LittleMonkey 结构体 扩展一个flying 功能，但不影响上面的 Monkey 结构体,实现了 birdAble接口
	fmt.Println(this.Name,"会飞了")
}

func main() {
	l := LittleMonkey{Monkey {Name:"小猴子"}}
	l.pashu()
	l.Flying()
  //直接用接口变量，调用 Flying()
  var bird birdAble = &LittleMonkey{Monkey{Name: "dd"}}
	bird.Flying()
}

```

- 当 LittleMonkey 继承了 Monkey，那么LittleMonkey就继承了 Monkey 的字段和方法， 可以直接使用
- 当 LittleMonkey 结构体需要扩展功能， 那么就定义 func (this *LittleMonkey)的功能，不会影响 Monkey

**接口和继承解决的问题不同**

继承，**主要解决代码的复用性**， 和可维护性

接口，主要在于**设计好的规范方法**，让其他自定义类型去实现这些方法。

接口比继承更加灵活

接口在一定程度上实现代码解耦

### 多态

go 多态，是通过接口来实现的。

#### 多态参数

可以按照统一的接口来调用不同的实现，接口变量就呈现出不同的形态。

```go
package main

import "fmt"

type myInterFace interface {
	Start()
	Stop()
}

type phone struct {
	name string
}

func (p *phone) Start() {
	fmt.Println(p.name + " start")
}
func (p *phone) Stop()  {
	fmt.Println(p.name + " stop")
}

type device struct {
	Name string
}

func (d device) init(myifc myInterFace) { //此处即为多态
	fmt.Println(d.Name)
	myifc.Start()//d.init 直接调myInterFace 接口的方法，不关心 myifc 是谁
	myifc.Stop()
}

func main(){
	var d = device{"computer"}
	d.init(&phone{"iphone"}) //传phone  则d.init 直接调myInterFace 接口的 方法 start stop
  //computer
  //iphone start
  //iphone stop

}
```

#### 多态数组

一个数组中， 存放 phone 结构体和  mp3 结构体

```go

type myInterFace interface {
	Start()
	Stop()
}

type phone struct {
	name string
}

type mp3 struct {
	name string
}
func (p *mp3) Start() {
	fmt.Println(p.name + " play")
}
func (p *mp3) Stop()  {
	fmt.Println(p.name + " end")
}


func (p *phone) Start() {
	fmt.Println(p.name + " start")
}
func (p *phone) Stop()  {
	fmt.Println(p.name + " stop")
}

type device struct {
	Name string
}

func (d device) init(myifc myInterFace) { //此处即为多态
	fmt.Println(d.Name)
	myifc.Start()//d.init 直接调myInterFace 接口的方法，不关心 myifc 是谁
	myifc.Stop()
}

var deviceArr [3]myInterFace //定义一个接口数组
deviceArr[0] = &mp3{name: "mp3"}  //接口是引用，所以要传地址
deviceArr[1] = &phone{name: "phone"}
deviceArr[0].Start()
deviceArr[1].Start()
```

### 类型断言 var.(type)

类型断言，由于空接口可以包括任何类型，如果要转成具体类型，需要使用类型断言

```go
package main

import "fmt"

type  Point struct {
	x ,y int
}
func main(){

	var a interface{}
	var point = Point{1,2}
	a = point //因为a声明的时候是 空接口 所以赋值时可以的
	var b Point
  //b = a  如果把 a赋值给b，类型不一样
	//a 是 空接口类型， b 是Point 类型， 如果要把a 复赋值给b，需要使用类型断言
	b = a.(Point) //断言a 是否可以转换为 Point 类型，能转就赋值，不能转就报错

	fmt.Println(b)
}
```

```go
package main

import "fmt"

type  Point struct {
	x ,y int
}
func main(){

	var x interface{}
	var b float32 = 1.1
	x = b
	//y := x.(float64) //panic: interface conversion: interface {} is float32, not float64
	y := x.(float32)
	fmt.Printf("%T %v",y,y)
}
```

在断言时要确保类型是正确的，否则会异常退出。

如果需要判断是否断言成功

```go
y,flag := x.(float64) //不能放到if里，y会变成if里的局部变量并报错
if flag {
  fmt.Printf("%T %v",y,y)
}else{
  fmt.Println("转换失败")
}
//或者
if v, ok := varI.(T); ok {  // checked type assertion
    Process(v)
    return
}
```

### 类型判断：type-switch

接口变量的类型也可以使用一种特殊形式的 `switch` 来检测：**type-switch** （下面是示例 11.4 的第二部分）：

```
switch t := areaIntf.(type) {
case *Square:
	fmt.Printf("Type Square %T with value %v\n", t, t)
case *Circle:
	fmt.Printf("Type Circle %T with value %v\n", t, t)
case nil:
	fmt.Printf("nil value: nothing to check?\n")
default:
	fmt.Printf("Unexpected type %T\n", t)
}
```

输出：

```
Type Square *main.Square with value &{5}
```

可以用 `type-switch` 进行运行时类型分析，但是在 `type-switch` 不允许有 `fallthrough` 。

下面的代码片段展示了一个类型分类函数，它有一个可变长度参数，可以是任意类型的数组，它会根据数组元素的实际类型执行不同的动作：

```
func classifier(items ...interface{}) {
	for i, x := range items {
		switch x.(type) {
		case bool:
			fmt.Printf("Param #%d is a bool\n", i)
		case float64:
			fmt.Printf("Param #%d is a float64\n", i)
		case int, int64:
			fmt.Printf("Param #%d is a int\n", i)
		case nil:
			fmt.Printf("Param #%d is a nil\n", i)
		case string:
			fmt.Printf("Param #%d is a string\n", i)
		default:
			fmt.Printf("Param #%d is unknown\n", i)
		}
	}
}
```

以这样调用此方法：`classifier(13, -14.3, "BELGIUM", complex(1, 2), nil, false)` 。

在处理来自于外部的、类型未知的数据时，比如解析诸如 JSON 或 XML 编码的数据，类型测试和转换会非常有用。

### 测试一个值是否实现了某个接口

 类型断言中的一个特例：假定 `v` 是一个值，然后我们想测试它是否实现了 `Stringer` 接口，可以这样做：

```
type Stringer interface {
    String() string
}

if sv, ok := v.(Stringer); ok {
    fmt.Printf("v implements String(): %s\n", sv.String()) // note: sv, not v
}
```

`Print` 函数就是如此检测类型是否可以打印自身的。

### 类型断言

```go
var.(type) //a.(Point)  a是否可以转成Point类型， 如果不行会报错

```

