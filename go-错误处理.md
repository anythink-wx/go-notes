## 错误处理

### 系统错误

go不支持，try，catch，finally的处理方式。

go 引入的处理方式为：defer，panic，recover。



**可以抛出panic异常，然后在defer中通过recover捕获这个异常，然后正常处理。**

```go
func test() int {
	num1 := 10
	num2 := 0
	//panic: runtime error: integer divide by zero（崩溃）
	res := num1 / num2
	return res
}

func main() {
	res := test()
	fmt.Println("res=", res)
}
```

使用 defer，recover

```go
func test() int {
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println("err", err)
		}
	}()
  //或者
  //defer func() {
	//	if err := recover(); err != nil {
	//		fmt.Println("err", err)
	//	}
	//}()
	num1 := 10
	num2 := 0
	//panic: runtime error: integer divide by zero（崩溃）
	res := num1 / num2
	return res
}

func main() {
	res := test()
	fmt.Println("res=", res)
}
```

### 自定义错误

使用 errors.New 和 panic 内置函数

1. errors.New("错误说明") 会返回一个error 类型的值，标识一个错误
2. panic内置函数，接收一个interface{}类型的值，作为参数，可以接收error类型，**删除错误信息，并终止程序执行**

```go
func test(num1, num2 int) (res int, err error) {

	if num2 == 0 {
		res = 0
		err = errors.New("参数错误")
		return
	}
	res = num1 / num2
	return
}

func main() {
	res, err := test(10, 0)
	if err != nil {
		//fmt.Println("err", err)
		panic(err)
	}
	fmt.Println("res=", res)
}
```

