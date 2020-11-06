# Goroutine 和 channel

go主线程（或主进程），可以起多个协程，协程是轻量级的线程

## 特点

独立站空间

共享对空间

调度由用户控制

协程是轻量级的线程(可以轻松可以开启上万个协程，其他的编程语言基于线程，开启过多线程资源耗费大)

### 设置运行的cpu数

```go
fmt.Println(runtime.NumCPU()) //查看cpu数量
fmt.Println(runtime.NumGoroutine()) //查看协程数量
runtime.GOMAXPROCS(1)//设置协程使用的cpu数 go 1.8 后默认就是多核
```

### 一个协程的并发写入问题

```go
package h1

import (
   "sync"
   "testing"
   "time"
)

var mymap = make(map[int]int)
func test(n int) {
   res := 1
   for i := 1; i <= n; i++ {
      res *= i
   }
   //fatal error: concurrent map writes go 多个协程操作一个全局变量会出现异常
   mymap[n] = res
}
func TestH1(t *testing.T) {

   ch := make(chan map[int]int)
   for i := 0; i <= 200; i++ {
     //放入协程去后台执行
      go test(i, ch)
   }
   time.Sleep(time.Second * 10)
}
```

### 采用低水平的互斥锁解决

```go
var mymap = make(map[int]int)
var mymapLock sync.Mutex //设置一个锁

func test(n int, ch chan map[int]int) {

   res := 1
   for i := 1; i <= n; i++ {
      res *= i
   }
   mymapLock.Lock()
   mymap[n] = res
   mymapLock.Unlock()
}
func TestH1(t *testing.T) {

   ch := make(chan map[int]int)
   for i := 0; i <= 20; i++ {
      go test(i, ch)
   }
   time.Sleep(time.Second ) //需要等待协程执行完成

   var ints []int
   for k,_ := range mymap {
      ints = append(ints,k)
   }

   sort.Ints(ints)
   for k,_ := range ints {
      fmt.Println(k,mymap[k])
   }
}
```

## 使用channel管道

channel 有数据类型，线程安全（多协程访问时不需要加锁），是一个管道队列 先进先出 FIFO

channel引用类型

必须初始化才能使用

在没有使用协程的情况下，取出或写入的数量超过cap n容量主线程会fatal 死锁deadlock

```go
func TestH1(t *testing.T) {
   var intChan chan int
   intChan = make(chan int, 3) //创建管道

   intChan <- 10
   intChan <- 20 //设置值

   fmt.Println(cap(intChan), len(intChan))
   i := <-intChan
   <-intChan
   <-intChan //在没有使用协程的情况下，取出或写入的数量超过cap n容量主线程会fatal 死锁deadlock

   fmt.Println(i)
   fmt.Println(cap(intChan), len(intChan))

}
```

### 多种类型的管道

```go
var intChan chan int
intChan = make(chan int, 3)  //int
var mapChan chan map[string]string //map
mapChan = make(chan map[string]string,1)
mapChan<- map[string]string{"hello":"world"}


catChan := make(chan Cat,1) //struct
catChan <- Cat{"sss"}

ifaceChan := make(chan interface{},1) //interface
ifaceChan<- "2333"
d := <-ifaceChan
fmt.Println("ifaceChan=",d.(string))//类型断言
```

### 管道关闭

close函数可以关闭channel，关闭后就不可写，只能读

```go
x, ok <-chan //如果管道关闭 ok 返回 false
```

### 管道 遍历for range

遍历时， channel没关闭， 则会出现 死锁

遍历时，channel已关闭， 会正常遍历

```go
intChan := make(chan int,100)

for i:= 0;i<100;i++{
   intChan<- i*2
}
//不关闭管道，在协程外会报死锁
close(intChan)

for k := range intChan{
   fmt.Println(k)
}
```

## 协程发送消息接收消息

发送接收消息，主线程等待

```go

func TestH1(t *testing.T) {
	intChan := make(chan int, 50)
	waitChan := make(chan bool)

	go writeData(intChan)
	go readData(intChan, waitChan)

	for true {
		_,ok := <-waitChan //等待通道被close
		if ok {
			break
		}
	}
}

func writeData(ch chan int) {
	for i := 0; i <= 50; i++ {
		ch <- i
	}
	close(ch)//写完关闭 否则read 会死锁
}

func readData(ch chan int, waitChan chan bool) {

	for {
		v, ok := <-ch
		if ok {
			fmt.Println("readData", v)
		}else{
			break
		}
	}
	time.Sleep(time.Second)
	waitChan <- true
	close(waitChan) //读完了关闭管道，让主进程break
}
```

### 阻塞

如果只向通道写入数组，没有读取，口会出现阻塞而deadlock

### 生产者，消费者

有限的生产者消费者模式，生产者发送万全部任务后 关闭， 消费者消费完任务后关闭， 最后获取结果

```go
func putNumber(inputChan chan int) {
	for i := 1; i <= 60000; i++ {
		inputChan <- i
	}
  close(inputChan) //生成完全部数据后关闭通道 (2)
}

func TestH2(t *testing.T) {
   inputChan := make(chan int)
   resultChan := make(chan int, 100)
   exitChan := make(chan bool, 4)

   go putNumber(inputChan) //生产int 给 inputChan

   for i := 0; i <= 4; i++ {
      go func() {
        for num := range inputChan {  //(2)此处才能使用for range 获取所有数， 再计算
            flag := true
            for i := 2; i < num; i++ {
               if num%i == 0 {
                  flag = false
               }
            }
            if flag {
               resultChan <- num
            }
         } //inputChan 调用close后该 for 退出， 发送退出消息
         exitChan <- true 
         fmt.Println("go", os.Getgid())
      }()
   }

   go func() {
      for i := 0; i < 4; i++ {
         <-exitChan //再起一个go程  等待所有其他的go程退出通信
      }
      close(resultChan)
   }()

   for _ = range resultChan { //最后在主线程等待并收集所有的结果
      //fmt.Println(v)
   }
}
```