## json正序列化

### 结构体序列化

```go
type demo struct {
	Name  string `json:"name"` //带注释的用于重新定义json序列化后的key
	Age   int `json:"age"`
	Sal   float64 `json:"sal"`
	Skill []string `json:"skill"` //结构体参数如果用小写，不可导出，序列化后就没有了
}
d := demo{"aa", 18, 233.33, []string{"a", "b", "c"}}
	marshaler, err := json.Marshal(d)
	fmt.Println(string(marshaler), err)
d := demo{"aa", 18, 233.33, []string{"a", "b", "c"}}
marshaler, err := json.Marshal(d)
fmt.Println(string(marshaler), err)
//{"Name":"aa","Age":18,"Sal":233.33} 
```

### map序列化

```go
var a map[string]string
a = make(map[string]string)
a["aa"] = "bbbb"
a["bbb"] = "bbb"
marshal, err := json.Marshal(a)
fmt.Println(string(marshal),err)
//{"aa":"bbbb","bbb":"bbb"} <nil
```

### 切片map序列化

```go
var b []map[string]interface{}
_tmp := make(map[string]interface{})
_tmp["aaa"] = "bbb"
b = append(b, _tmp)
_tmp["ccc"] = "ddd"
b = append(b, _tmp)

bytes, err := json.Marshal(b)
fmt.Println(string(bytes))
//[{"aaa":"bbb","ccc":"ddd"},{"aaa":"bbb","ccc":"ddd"}]
```



## json反序列化

### 反序列化为结构体

```go
str := "{\"Name\":\"aa\",\"Age\":18,\"Sal\":233.33}"
var demo demo
err := json.Unmarshal([]byte(str), &demo) //转为byte切片，申明一个demo 结构体的变量用来存放
if err != nil{
   log.Fatal(err)
}
fmt.Println(demo.Name)
```

### 反序列化为map

```go
str := "{\"Name\":\"aa\",\"Age\":18,\"Sal\":233.33}"
var demoMap map[string]interface{}
err := json.Unmarshal([]byte(str), &demoMap) //此处的 map 不需要make， unmarshal 底层会自己make
if err != nil{
   log.Fatal(err)
}
fmt.Printf("%v",demoMap)
```

### 反序列化为slice

```go
tr := "[{\"aaa\":\"bbb\",\"ccc\":\"ddd\"},{\"aaa\":\"bbb\",\"ccc\":\"ddd\"}]"
var demoSlice []map[string]interface{}
err := json.Unmarshal([]byte(str), &demoSlice) //此处的slice 或者 map 都不需要make
if err != nil{
   log.Fatal(err)
}
fmt.Printf("%v",demoSlice)
```