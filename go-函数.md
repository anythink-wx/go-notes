## 函数

```go
func 函数名称 (形参列表) (返回值列表) {
	//执行语句
  return 返回值列表
}

func cacl(n1 float64, n2 float64) float64 {
	return n1 * n2
}
func main() {
	a := cacl(15.4, 13.2)
	fmt.Println(a)
}
```

### 包

go的每一个文件都属于一个包，go以包的形式来管理文件和项目目录结构

作用

1. 区分相同名字的函数，变量等标识符
2. 组织代码结构
3. 控制函数变量等访问范围，即作用域

#### **声明包的基本语法**

package util

#### **引入包的基本语法**

import "包的路径"

```go
package main

import (
	"blog/pk1" //import 的路径从环境变量 gopath 的 src 下开始
	"fmt"
)

func main() {

	fmt.Println("hello")
  res := pk1.Test() //调用包里的方法 是  包名(跟文件名没啥关系).函数名
	fmt.Println(res)
	fmt.Println(pk1.Pk1Num)
}

//pk1 package
package pk1

var Pk1Num int = 1

func Test() bool { //公开的方法需要首字母大写
	return true
}


```

#### **包别名**

```go
import (
  hehe "blog/pk1"  //可以使用别名，起了后原先的就不能使用
	"fmt"
)

func main() {

	fmt.Println("hello")
	res := hehe.Test() //使用别名
	fmt.Println(res)
	fmt.Println(hehe.Pk1Num) //使用别名
}
```

注意事项和细节说明

1. 一般 package 包名和目录名保持一致
2. 包的函数，变量，要被外部访问需要首字母大写。
3. 当一个文件要使用其他包的函数或变量时，需要先import包
4. package 需要在第一行，然后是import
5. 在 import 包时，路径从 $GOPATH 的src 下开始，不用写src
6. 可以给包起别名，起了后原先的就不能使用
7. 同一包下(package)，不能定义相同的函数，定义在多个文件也不行，package
8. 如果要编译一个可执行文件，需要有且只有一个 main 包

### 编译一个可执行文件

切换到GOPATH目录，然后执行 go build [-o bin/myexec] 项目路径

GOPATH： /Users/anythink/go

项目路径：/Users/anythink/go/src/blog/main

 编译命令：go build [-o bin/myexec]  blog/main

-o 将可执行文件存到其他地方

默认生成到命令执行的目录下



包代码会生成库文件，$GOPATH/pkg/darwin_amd64/blog，该库文件可以直接使用不需要提供源代码

### 函数返回值

go函数支持多个返回值，超过1个返回值需要在返回值声明中增加括号

```go
//package pk1 as hehe
package pk1
func Test() (bool, bool) { //超过1个返回值需要在返回值声明中增加括号
	return true, true
}
//package main
package main
import (
	hehe "blog/pk1"
	"fmt"
)
func main() {
	fmt.Println("hello")
	res, res2 := hehe.Test()
	fmt.Println(res)
	fmt.Println(res2)
}
```

```go
func main() {
	fmt.Println("hello")
	res, _ := hehe.Test() //如果有不需要的返回值，使用_忽略编译变量检查
	fmt.Println(res)
}
```

### 函数递归

函数执行递归时，会产生一个新栈，数据独立，局部变量是独立的，互不影响。

递归必向退出递归条件逼近，否则会卡死

当函数自行完毕， 或者遇到return，就会返回，遵守谁调用，就将结果返回给谁，当函数执行完毕返回时，函数空间会被销毁

```go
func test(n int) {
	if n > 2 {
		n--
		test(n)
	}
	println(n)
}

func main() {
	test(4)
  //2
	//2
	//3
}
```

### 函数细节总结

1. 函数形参可以使多个，返回值列表也可以是多个

2. 形参列表，和返回值列表的数据类型可以是值类型和引用类型

3. 命名规范同变量，首字母不能为数字，首字母大写表示公开，小写代表私有

4. 函数内的变量是局部的，外部不生效

5. 基本数据类型，和数组默认都是值传递（拷贝）。在函数内修改，不会影响到原值

6. 如果需要函数内变量修改外部棉量，可以掺入变量的地址func test(n1 \*int)，函数内以指针*的方式操作变量，类似引用 外部传值 test(&n1)

   ```go
   func test(n *int) int {
   	*n += 10
   }
   
   func main() {
   	var n int = 10
   	fmt.Println(test(&n))
   }
   ```

   

7. 不支持重载

8. 函数也是一种数据类型，可以赋值给一个变量，则该变量则是函数类型的变量，通过该变量可以对函数调用

   ```go
   func test(n *int) {
   	*n += 10
   }
   
   func main() {
   	var n int = 10
   	newVar := test
   	newVar(&n)
   	fmt.Println(n)
   }
   
   ```

   

9. 函数既然是一种数据类型， 因此在go中，函数可以作为形参，并且调用

   ```go
   func getSum(n1 int, n2 int) int { //如果参数都是同一类型，也可以用 n1,n2 int
   	return n1 + n2
   }
   
   func myFun(_getSum func(int, int) int, num1 int, num2 int) int {
   	return _getSum(num1, num2)
   }
   func main() {
   	res := myFun(getSum, 1, 1)
   	fmt.Println(res)
   }
   ```

   

10. go支持自定义数据类型。基本语法 type 上自定义数据类型名  数据类型// 相当于一个别名

    ```go
    //demo 01
    type myint int
    var a myint = 10
    fmt.Printf("%T %v", a, a)
    //main.myint 10
    
    
    //demo 02
    func getSum(n1 int, n2 int) int {
    	return n1 + n2
    }
    
    type myFunType func(int, int) int
    
    func myFun(_myfun myFunType, num1 int, num2 int) int {
    	return _myfun(num1, num2)
    }
    
    func main() {
    	res := myFun(getSum, 1, 1)
    	fmt.Println(res)
    }
    ```

    

11. 支持对函数返回值命名

    ```go
    func getSum(n1 int, n2 int) (sum int) { //提前设置好返回变量，下面用到sum变量就不用再声明了
    	sum = n1 + n2
    	return
    }
    
    func main() {
    	sum := getSum(1, 3)
    	fmt.Println("sum", sum)
    }
    ```

    

12. 使用_下划线标识符，忽略返回值

13. 函数支持可变参数

    ```go
    //args 是slice切片 ，用来接收多个参数 可以自定义，只要后面是...
    func sum(args ...int) (sum int) {
    	for i := 0; i < len(args); i++ {
    		sum += args[i]
    		fmt.Println(args[i])
    	}
    	return
    }
    
    func main() {
    	sum := sum(1, 3, 4, 5, 6, 7, 8)
    	fmt.Println("sum", sum)
    }
    
    
    
    //支持多个参数， 但是可变参数必须放在最后
    func sum(begin int, args ...int) (sum int) {
    	sum += begin
    	for i := 0; i < len(args); i++ {
    		sum += args[i]
    		fmt.Println(args[i])
    	}
    	return
    }
    
    func main() {
    	sum := sum(100, 1)
    	fmt.Println("sum", sum) //101
    }
    ```

    

### init函数，调用main方法前调用

每一个源文件，都可以包含一个init函数， 会在main方法前被初始化。如果import了包， 会**先执行包里的init 方法**，再执行main包里的init方法。

```go
func init() {
	fmt.Println("main invoke")
}
```

**细节**

如果一个文件同时包含全局变量定义， init 函数， 和main 函数。则执行的流程先定义全局变量，init，最后是main。

```go

var age = sum(1, 2) //age全局变量会先调用函数 sum 再执行init 

func init() {
	fmt.Println("main.init invoke age", age)
}

func main() {
	fmt.Println("main ")
}
//main.init invoke age 3
//main
```

### 匿名函数

调用方式：在匿名函数调用时，或将匿名函数传递给一个变量

```go
//demo1
res1 := func(n1, n2 int) int { //在定义时直接调用
  return n1 + n2
}(10, 20)

fmt.Println("res=", res1)

//demo2 
res2 := func(n1, n2 int) int { //将匿名函数传递给一个变量，再调用
  return n1 + n2
}
fmt.Println("res2=", res2(10, 20))
```

**全局匿名函数**

```go
var fun1 = func(n1, n2 int) int {
	return n1 * n2
}

func main() {
	fmt.Println("fun1=", fun1(2, 2))
}
```

### 闭包

闭包是一个函数和与其相关的引用环境组个的一个整体

```go
func add() func(int) int { //有点像 类
	var n int = 10 //有点像 类成员属性

	return func(x int) int {//有点像 类方法 //匿名函数和n变量形成整体 构成闭包
		n = n + x
		return n
	}
}

func main() {

	addHand := add()                 //  相当于执行了 var n int = 10
	fmt.Println("fun1=", addHand(1)) //相当于 执行了 return func(x int) int
	fmt.Println("fun1=", addHand(2)) //当反复调用addHand 时，因为n是初始化了一次，因为每次调用一次就进行累加
	fmt.Println("fun1=", addHand(3))
//fun1= 11
//fun1= 13
//fun1= 16
}
```



```go
//接收一个后缀，然后返回函数， 函数继续可以传参数进行判断是否符合该后缀名
func makeSuffix(suffix string) func(string) string {
	return func(name string) string {
		if !strings.HasSuffix(name, suffix) {
			return name + suffix
		}
		return name
	}
}

func main() {
	suffixAotuComplete := makeSuffix(".jpg") //先将一个匿名函数返回给变量，然后.jpg 就被维持下来了
	res := suffixAotuComplete("2333") //直接进行判断，.jpg 已经被赋值的时候就已经存起来了
	res2 := suffixAotuComplete("244433")

	fmt.Println(res)
	fmt.Println(res2)
}
```

### 函数内关键词 defer

函数中创建资源，比如数据库，文件句柄，锁，为了在函数执行完毕后及时释放资源，提供defer延时机制。

在defer语句放入到栈时，也会将相关的值拷贝同时入栈。

```go
func main() {
	defer fmt.Println("defer") //defer 声明后的语句，最后执行。如有多个defer按照栈的逻辑，先入后出
	fmt.Println("hello")
	fmt.Println("hello")
  //hello
  //hello
  //defer
}
```

在defer语句放入到栈时，**也会将相关的值拷贝同时入栈**。

```go
func test(n int) int {
	defer fmt.Println("defer n=", n)
	n++
	fmt.Println(" n=", n)
	return n
}

func main() {
	test(2)
// n= 3 n加1了，但是defer入栈值拷贝的时候n还是2
//defer n= 2 //所以defer时n还是2
}
```

defer最佳实践，当函数执行完毕后，可以及时的释放函数创建的资源。

