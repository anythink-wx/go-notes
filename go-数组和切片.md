## 数组和切片

数组可以存放**多个统一类型数据**。在go中，**数组是值类型**。

```go
//定义一个float64的数组
var e [6]float64
fmt.Println(e) //[0 0 0 0 0 0]
e[3] = 233.222
fmt.Println(e) //[0 0 0 233.222 0 0]

var all float64
for i := 0; i < len(e); i++ {
  all += e[i]
}
//求平均数
fmt.Println(all / float64(len(e))) //59.446333333333335
```

1. 数组的地址可通过数组名来获取 &e ，
2. 数组的第一个元素地址，也就是数组的地址
3. 第二个元素的地址，就是第一个元素的地址+变量类型的字节数，是连续的 （int64 -8 int32 -4）(%p 取地址)

#### 数组初始化方式

```go
var numArr [3]int = [3]int{1, 2, 3}
var numArr2 = [3]int{1, 2, 3}
var numArr3 = [...]int{1, 2, 3} //不指定具体数量
var numArr4 = [...]int{1:100, 0:900, 2:300} //指定下标


//可以指定元素对应的下标
var names = [3]string{1: "123", 0: "456", 2: "bbb"}
```

#### 数组遍历for-range

index，val，只能在for循环内部使用，因为是局部变量

```go
var numArr4 = [...]int{1: 100, 0: 900, 2: 300} //指定下标
var numArr4 = [...]int{1: 100, 0: 900, 2: 300} //指定下标

for index, val := range numArr4 {
  fmt.Printf("index %v, val %v\n", index, val)
}
//for _, val := range numArr4 { //忽略index
//  fmt.Printf("val %v\n",  val)
//}
```

#### 注意事项及细节

1. 数组是多个**相同类型的**数据组合，一旦声明，**长度固定，不能动态变化**

2. var arr []int 不写大小，就是切片，不是数组。

3. 数组中的元素可以是任何数据类型，包括值类型和引用类型，**但是不能混用**

4. 数组创建后，没有赋值有默认值， 就是零值（数值0，字符空，布尔假）

5. 使用步骤，1申明数组开辟内存空间，2给各个元素复制，3使用数组

6. 数组下标从0开始

7. 下标必须在指定范围内使用，否则报panic，数组越界

8. 数组为**值类型**，**默认情况下是值传递**，因此会进行值拷贝。数组间不会相互影响。

   ```go
   //如果需要改变，则需要使用指针
   func test(arr *[3]int) {
   	arr[0] = 0
   }
   
   func main() {
   	var num = [3]int{9, 8, 7}
   	test(&num)
   	fmt.Println(num) //[0 8 7]
   }
   ```

   

9. **长度是数组类型的一部分**，在**传递函数参数**时，**需要考虑数组的长度**。

------

### 切片 slice

1. 切片是数组的一个引用，因此切片是引用类型，在进行传递时，遵守引用传递的机制
2. 切片的使用和**数组类似**， 遍历切片，访问元素，求长度
3. 切片的长度是可以变化的，因此是一个可以动态变化的数组

切片定义

```go
//var 切片名 []类型
var numSlice []int


var intArr = [5]int{1, 2, 3, 4, 5}
slice := intArr[1:3]
fmt.Println("intArr=", intArr) //intArr= [1 2 3 4 5]
fmt.Printf("%T %v", slice, slice) //[]int [2 3]
fmt.Printf("\n cap:%v", cap(slice)) //cap:4


slice := intArr[1:3] //引用类型
fmt.Printf("%T %v\n", slice, slice)
fmt.Printf(" cap:%v\n", cap(slice))
slice[0] = 999 //会直接改变原值

fmt.Println("new intArr=", intArr)  //new intArr= [1 999 3 4 5]
```

#### 切片的使用

1. 定义一个切片，然后让切片去引用一个已经创建好的数组，比如上面的方式

2. 使用make来创建切片 （也会创建数组，由切片在底层进行维护，不可见，只能操作切片）

   ```go
   var slice []int = make([]type,len,[cap]) //数据类型，长度，容量
   ```

   

3. 定义切片，直接制定个具体数据

   ```go
   var slice []int = []int{1, 2, 3, 4, 5, 6, 7}
   fmt.Println(slice)
   ```

#### 切片遍历

和数组遍历一样

#### 切片追加

func [append](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#134)

```
func append(slice []Type, elems ...Type) []Type
```

内建函数append将元素追加到切片的末尾。若它有足够的容量，其目标就会重新切片以容纳新的元素。否则，就会分配一个新的基本数组。append返回更新后的切片，因此必须存储追加后的结果。

```go
var slice []int = []int{1, 2, 3}
slice = append(slice, 4,5,6,7,8)
slice = append(slice, slice...)//必须带...

var slice []int = []int{1, 2, 3, 4, 5, 6, 7}
slice = append(slice, []int{4, 5, 6}...) //直接追加切片， 不能是数组
fmt.Println(slice)
```

追加原理

1. 切片append操作的本质就是使用新数组（扩容后的）替代老数组
2. go底层会创建一个新的数组
3. 将slice原来包含的元素拷贝到新数组，然后重新引用到新数组

#### 切片拷贝操作

func [copy](https://github.com/golang/go/blob/master/src/builtin/builtin.go?name=release#141) 将切片从src拷贝到dst中

```
func copy(dst, src []Type) int
```

内建函数copy将元素从来源切片复制到目标切片中，也能将字节从字符串复制到字节切片中。copy返回被复制的元素数量，它会是 len(src) 和 len(dst) 中较小的那个。来源和目标的底层内存可以重叠。

```go
//demo
var slice []int = []int{1, 2, 3, 4, 5, 6, 7}
var slice2 []int = make([]int, 10)

fmt.Println(slice) //[1 2 3 4 5 6 7]
copy(slice2, slice)
fmt.Println(slice2) //[1 2 3 4 5 6 7 0 0 0] 如果dst 比 src 长，剩下的会保持原状
```

拷贝的切片是相互独立的

#### 注意事项及细节

1. 切片初始化时，var slice = arr[start:end]，如0：3不包含3
2. 切片初始化时不能越界。范围在 0- len(arr)之间，但是可以动态增长
3. 切片索引使用方法
   - Var slice = arr[0:end] 可以写成 arr[:end]
   - var slice = arr[start:len(arr)] 可以写成arr[start:]
   - var slice = arr[0:len(arr)] 可以写成 arr[:]
4. Cap 是一个内置函数，用于拥挤切片容量，即最大可以存放多少个元素
5. 切片定义完后，不能直接使用，需要初始化（比如make 创建引用，或者切一个数组）
6. 切片可以继续切片

### string 和slice

- String 底层是一个byte数组，因此string也可以进行切片处理

  ```go
  str := "hello world"
  slice := str[:5]//hello
  ```

- String （也是切片，但是不可改）如果要改变里面元素，需要转换成切片

  ```go
  //demo1
  str := "hello world"
  arr1 := []byte(str) //转成byte 可以处理英文，不能处理中文
  arr1[0] = 'p'
  str = string(arr1)
  fmt.Println(str) //pello world
  
  //demo2
  str := "hello 世界" //中文需要使用 []rune 类型， 按照字符处理，兼容汉字
  arr1 := []rune(str)
  arr1[6] = '哈'
  str2 := string(arr1)
  fmt.Println(str2)//hello 哈界
  ```

  练习

  ```go
  //获取一个n长度的斐波那契数列
  func fbn(n int) []uint64 {
  	var size []uint64 = make([]uint64, n)
  	size[0] = 1
  	size[1] = 1
  
  	for i := 2; i < n; i++ {
  		size[i] = size[i-1] + size[i-2]
  	}
  	return size
  }
  
  func main() {
  	res := fbn(10)
  	fmt.Println(res)//[1 1 2 3 5 8 13 21 34 55]
  }
  ```

  



### 排序和查找（数组）

内部排序，将需要处理的数据都加载到内存进行排序（**交换式排序法，选择式排序法，插入式排序法**）

​	**交换式排序**：冒泡排序法，快速排序法

```go
func main() {

	var num [5]int = [5]int{24, 69, 80, 57, 13}
	var numSlice = num[:]

	for j := len(numSlice) - 1; j > 0; j-- {
		fmt.Println(j)
		for i := 0; i < j; i++ {
			if numSlice[i] < numSlice[i+1] {
				numSlice[i], numSlice[i+1] = numSlice[i+1], numSlice[i]
			}
			fmt.Println(numSlice)
		}
	}
}
	
```

外部排序，数据过大，无法全部加载到内存，需要借助外部存储进行排序（**合并排序反，直接合并排序法**）

### 查找

顺序查找

```go
for i,v := range num {
  if(num[i] == "xxx"){
    return num[i]
  }
}
```

二分查找

需要对一个有序数列，才能使用二分查找

```go
func binaryfind(arr *[6]int, left int, right int, find int) {
	if left > right {
		fmt.Println("not find:")
		return
	}
	m := (left + right) / 2

	if arr[m] > find {
		binaryfind(arr, left, m-1, find)
	} else if arr[m] < find {
		binaryfind(arr, m+1, right, find)
	} else {
		fmt.Println("find:", arr[m])
	}
}

func main() {
	//二分查找 被查找值 find
	//先找到中间的下标  m = (left + right / 2)
	// 如果  num[m] > find  就应该从 left ~ m-1
	// 如果 num[m] < find 应该从  m+1 ~right
	//分析出 退出递归的条件 left > right
	var num = [6]int{1, 8, 10, 89, 1000, 1024}
	binaryfind(&num, 0, len(num), 1)
}
```

### 二维多维数组

使用方式 行， 列。先声明再赋值，再使用

```go
//输出以下数据
//0 0 0 0 0 0 
//0 0 1 0 0 0 
//0 2 0 3 0 0 
//0 0 0 0 0 0 
var arr [4][6]int //一个4行6列的数组

arr[1][2] = 1
arr[2][1] = 2
arr[2][3] = 3

for _, v := range arr {
  for _, vv := range v {
    fmt.Printf("%v ", vv)
  }
  fmt.Println()
}
```

直接初始化

```go
var arr = [3][3]int{{1, 2, 3}, {1, 2, 3}, {1, 2, 3}}
fmt.Println(arr)
//直接赋值时可以省略前面
var arr2 = [...][3]int{{1, 2, 3}, {1, 2, 3}, {1, 2, 3}}
fmt.Println(arr2)
```

