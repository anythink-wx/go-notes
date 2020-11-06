## struct

结构体

go也支持面向对象编程（OOP），但是和传统面向对象有区别，**不是纯粹的面向对象语言**。所以应该说，**go支持面向对象编程特性**。

go没有类（class）go语言的结构体和其他编程语言的类有同等地位，可以理解为go基于struct来实现OOP。

去掉了OOP语言继承，方法重载，构造函数，析构函数，影藏的this指针

go仍然有面向对象的继承，封装和多态的特性，只是实现的方式和其他OOP语言不一样。

go中**面向接口编程，是非常重要的特性**。

struct直接指向的是数据，**是值类型**

```go
type Cat struct {
	Name  string //大写表示可以公开访问
	Age   int
	Color string
}

func main() {

	var maomao Cat //设置变量类型
	maomao.Name = "hh" //赋值
	maomao.Color = "baise"

	fmt.Println(maomao)
}

```

### 结构体声明

```go
type 标识符 struct {
	field type //字段类型可以是一般数据类型，数组，也可以是引用类型
  field type
}
```

- 字段类型可以是一般数据类型，数组，也可以是引用类型
- 创建结构体没有赋值，都对应一个零值。
- 不同结构体变量的字段是独立，互不影响，一个结构体变量字段的更改，不影响另外一个
- 指针，slice，map零值是nil，如果要使用，需要先make

```go
type Cat struct {
	Name  string
	Age   int
	Color string
	ptr   *Cat
	slice []int
	map1  map[string]string
}

func main() {

  //声明方式1
	var maomao Cat
	maomao.Name = "hh"
	maomao.Color = "baise"
	maomao.map1 = make(map[string]string)
	maomao.map1["hello"] = "world"

	maomao.slice = make([]int, 10)
	maomao.ptr = &maomao
	fmt.Println(maomao)
  
  
  //声明方式2
  var maomao = Cat{}
  //{hh 0 baise 0xc0000b4000 [0 0 0 0 0 0 0 0 0 0] map[hello:world]}
}


// ....
type Cat struct {
	Name string
	Age  int
}

func main() {

	var maomao = Cat{}
	fmt.Println(maomao)
//声明方式3
  var maomao2 = Cat{"name", 17} // Cat{Name:"asd",Age:26}
	fmt.Println(maomao2)

  //声明方式4 引用-指针方式
  mao3 := new(Cat) // &cat{}
	// fmt.Printf("%T", mao3)
	mao3.Name = "namnam"
	(*mao3).Age = 16 //原生写法
	mao3.Age = 16    //go 会优化一下

	fmt.Println(mao3)
  
  //声明方式5 引用-指针方式
  //也可以赋值，比如 &Cat{"name",18}
  var mao4 *Cat = &Cat{}
	(*mao4).Age = 123
	mao4.Age = 16 //go 会优化一下
	fmt.Println(mao4)
}
```

### 

结构体的字段在内存中是连续的

结构体可以取别名，但是要强制类型转换

字段上可以写上一个tag，tag可以通过反射机制获取

```go
type Cat struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}
```

### 方法

go中 方法是作用在指定数据类型的上，因此 自定义类型（type），都可以有方法， 不仅仅是**struct**。

#### 方法的申明

```go
type Cat struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func (recevier type) methodName (参数列表) (返回值列表)
//给类型绑定方法
func (cat *Cat) test() {  //func (标识名称 type) 方法名称() 返回值  { }
	(*cat).Name = "vvv"
}
func main() {

	cat := Cat{}
	cat.test()
	fmt.Println(cat.Name)
```

1. recevier type方法绑定到哪个类型，可以是类型，也可以是结构体

2. receiver 是类型的实例（区分引用和值传递），如果需要在方法里修改结构体本身变量，就使用引用方式

3. **结构体的方法一般使用指针**

4. retunr 语句不是必须的

5. 非struct的方法

   ```go
   type myint int
   
   func (myint *myint) println() {
   	*myint++
   	fmt.Println(*myint)
   }
   
   func main() {
   	var my myint = 10
   	my.println()
   	my.println()
   	my.println()
   	my.println()
   	my.println() //15
   }
   ```

   

6. 方法名小写，只能在当前包使用，方法名大写，可以被其他包调用

7. 如果一个类型，实现了 String() 方法， 那么fmt.println 默认会调用该变量的String 方法进行输出(类似__toString)

   ```go
   type Cat struct {
   	Name string `json:"name"`
   	Age  int    `json:"age"`
   }
   
   func (cat Cat) String() string {
   	return fmt.Sprintf("cat: [name=\"%v\"], [age=\"%v\"]", cat.Name, cat.Age)
   }
   var cat = Cat{"nihao", 123}
   fmt.Println(cat)//cat: [name="nihao"], [age="123"]
   fmt.Println(&cat)//cat: [name="nihao"], [age="123"]
   ```

8. 不管方法的调用形式如何，真正确定是值拷贝还是地址拷贝的，是看方法是引用类型还是值类型。如果是 c Cat 则是值拷贝， c *Cat 则是地址拷贝。

### OOP开发实例

1. 声明结构体，确定结构体名称
2. 编写结构体字段
3. 编写结构体方法

创建结构体实例的时候，可以直接指定字段的值

1. 按照字段顺序赋值
2. 按照字段kv 赋值

### 工厂模式

相当于构造函数的功能。

如果**结构体的方法是小写的**， 又**希望在别的包**来创建这个实例，需要**使用工厂模式**

```go
// package
package factory

type  student struct {
	Name string
	Score float64
}


func NewStudent(name string ,score float64) *student {
	return  &student{Name: name,Score: score}
}

//给结构体字段封装 private ，类似javabean
func (s *student) GetScore() int {
	return s.age
}


// package main
package main

import (
	"factory"
	"fmt"
)

func main (){
	stu := factory.NewStudent("hello",88.99)
  stu.Score = 66.66
	//(*stu).Score = 77 指针等同于这种方式
	fmt.Println(*stu)//{hello 88.99}  默认返回的是指针类型& 需要进行取值操作
}

```

封装性，对结构体中的属性进行封装

通过方法，包，实现封装

### 继承

继承可**以解决代码复用**，golang中，如果一个struce 嵌套了另一个匿名结构体，那么这个结构体可以直接访问你们结构体的字段，和方法，从而实现了继承特性。



```go

package factory

type master struct { //如果匿名结构体被嵌入的是首字母大写则可以使用 stu.Master.GetAge()...
	Name string
	age  int
}

type student struct {
	master //嵌入匿名结构体,此时 student 可以使用master的 Name age 字段，和SetAge ，GetAge 方法
	Score float64
}

func NewStudent( score float64) *student {
	return &student{ Score: score}
}

func (s *student) GetScroe() float64 {
	return  s.Score
}

func (m *master) SetAge(age int) {
	m.age = age
}
func(m *master) GetAge() int {
	return m.age
}

```

当结构体和匿名结构体都与相同的字段护着方法是， 编译器采用，**就近访问原则**

```go
//...
func (s *student) SetAge(age int) { //student的
	s.age = age
}
func (m *Master) SetAge(age int) { //master的 
	m.age = age
}
//...
stu.Master.SetAge(1) //就近是执行strudent.SetAge  如果要执行master的 需要指明
```

```go

package factory

type A struct {
	 Name string
}

type B struct {
	Name string
	age int
}

type Ccccc struct {
	A
	B
}
//..
var c = factory.Ccccc{}
c.A.Name = "a"
fmt.Println(c.A.Name) //如果结构体有一样的字段，需要明确指出调用哪个，否则会报错
```

对于有重名的方法也是一样

##### 有名的结构体

```go
/*
 * Copyright (c) 2020.  anythink
 */

package factory

type A struct {
	 Name string
}

type B struct {
	Name string
	age int
}

type Ccccc struct {
	aaaaa A //如果是有名的结构体， 调用时必须使用 aaaaa
	B
}

//main...

var c = factory.Ccccc{}
c.Aaaaa.Name = "123" //如果不是匿名结构体， 那么就不会往匿名的方向上找
fmt.Println(c.Aaaaa.Name)



```

##### 给匿名结构体赋值

```go

package factory

type A struct {
	 Name string
}

type B struct {
	Name string
	age int
}

type Ccccc struct {
	Aaaaa A
	B
}

type Ddddd struct {
	Aaaaa *A
	*B
}

//引用方式
type Ddddd struct {
	Aaaaa *A
	*B
}


ccccc := factory.Ccccc{factory.A{Name: "hello"}, factory.B{Name: "world"}}
fmt.Println(ccccc)//{{hello} {world 0}}


ddddd := factory.Ddddd{&factory.A{Name: "hello"}, &factory.B{Name: "world"}}
fmt.Println(ddddd)//{0xc00008e1e0 0xc0000a6020}
fmt.Println(*ddddd.Aaaaa,*ddddd.B)//{hello} {world 0}
```

结构体也可以使用基本数据类型，匿名字段，但是只能出现一次（不能对外暴露）

```go

type Ddddd struct {
	int
}


func main() {
ddddd := Ddddd{}
fmt.Println(ddddd.int)
}
```

#### 多重继承

如果一个struct 嵌套了多个匿名结构体，那么这种方式就是多重继承。一般情况下**不建使用多重继承**

