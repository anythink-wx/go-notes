## Map

map 是 key-value 数据结构，**key不重复**，**map是无序的**

语法结构

```go
var mapName map[keyType]ValueType //key 通常是 int string

//map声明案例
var a map[string]string
var a map[string]int
var a map[int]string
var a map[string]map[string][string]
```

Slice，map，function不可以作为key，因为这几个没法用== 做判断

valueType 通常为数字，string，map，struct

注意：声明后不会分配内存，**初始化需要使用make**，然后才可以赋值和使用过

### 使用方式

1. 先声明，再make
2. 声明时即make
3. 声明时就赋值

```go
//方式1
var m1 map[int]string //panic: assignment to entry in nil map
m1[0] = "233"
fmt.Println(m1)

//方式2
var m = make(map[int]string)//key 重复会覆盖旧值 map 无序的
m[0] = "233"
fmt.Println(m) //map[0:233]

//方式3
var m = map[int]string{433: "qq", 0: "hello", 233: "world"}
fmt.Println(m)//map[0:hello 233:world 433:qq]

//demo map 套 map
var m = make(map[string]map[string]string)
m["0"] = map[string]string{"name": "2333", "sex": "1"}
fmt.Println(m)
```

### 增删改查

```go
map["key"] = value  //不存在 相当于增加， 存在了相当于修改
```

func [delete](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#146) 删除map元素，要删除的key如果不存在，也不会报错

```
func delete(m map[Type]Type1, key Type)
```

内建函数delete按照指定的键将元素从映射中删除。若m为nil或无此元素，delete不进行操作。

```go
delete(map,"key") //删除map元素
```

#### 判断key是否存在

```go
val,ok := map["key"] //if ok 返回 false 就说明没有key 

var m = make(map[string]map[string]string)
m["a"] = map[string]string{"name": "2333", "sex": "1"}

me, ok := m["a"]
fmt.Println(me["name"]) //map[name:2333 sex:1]
fmt.Println(ok)// true

```

### map删除

遍历key删除， 或者让map make一个新的空间，让原来的变成垃圾

```go
var m = make(map[string]map[string]string)
m["a"] = map[string]string{"name": "2333", "sex": "1"}
m["b"] = map[string]string{"name": "2333", "sex": "1"}
m["c"] = map[string]string{"name": "2333", "sex": "1"}
m["d"] = map[string]string{"name": "2333", "sex": "1"}
m["e"] = map[string]string{"name": "2333", "sex": "1"}

for k, v := range m {
  fmt.Printf("k=%v, v=%v\n", k, v["name"])
  delete(m, k)
}

fmt.Println(m)//map[]
//或者
m = make(map[string]map[string]string) //重新make 分配空间
```

#### map长度

也用 len 统计有多少对kv



### map切片

切片的类型如果是map，则成为 slice of map ，这样使用则可以动态增加map

```go
mapVal := []map[string]string
//demo

m := make([]map[int]string, 10)
m[0] = map[int]string{1: "b"}
m = append(m, map[int]string{9999999: "2333333"})
fmt.Println(m)//

```

### map排序


go map默认无序，排序一般是先将key进行排序，然后根据key遍历输出value

func [Ints](https://github.com/golang/go/blob/master/src/sort/sort.go?name=release#270) Ints函数将a排序为递增顺序

```
func Ints(a []int)
```

```go
ma1 := make(map[int]int)
ma1[10] = 100
ma1[1] = 13
ma1[4] = 56
ma1[8] = 80
//ma1 := map[int]int{10: 100, 1: 13, 4: 56, 8: 90} 两种初始化方法都试了
fmt.Println(ma1) //map[1:13 4:56 8:90 10:100] <-- 排序过的

//下面演示的是用key 排序，然后再取值
var keys []int
for k := range ma1 {
  keys = append(keys, k)
}

sort.Ints(keys)
fmt.Println(keys)

for _, v := range keys {
  fmt.Printf("%v=%v\n", v, ma1[v])
}

```

### map使用细节



1. 引用类型数据，遵守引用类型传递纸质，在一个函数接收map，修改后会改变原值

2. map容量达到后，再想map增加元素，会自动扩容，**切片需要使用append**

3. map的value也经常用struct类型，更适合管理复杂的数据。比前面value是map要好

   ```go
   //....
   type stu struct {
   	name string
   	age  int
   }
   //....
   stuMap := make(map[int]stu)
   stuMap[1] = stu{name: "123", age: 456}
   fmt.Println(stuMap)
   fmt.Println(stuMap[1].name)//123
   ```

4. 1

