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







