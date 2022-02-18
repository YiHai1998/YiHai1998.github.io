视频：[最新Go语言急速入门视频教程（七米出品）](https://www.bilibili.com/video/BV1ZJ411W7jG)

思维导图：https://www.processon.com/view/link/5ecb3925f346fb6907154970#map

# 基础

## 快速开始

配置代理，win、macos都可

```
go version
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```





依旧是`Hello,World!`

新建main.go

```go
package main

import "fmt"

func main() {
    // 单行注释，和Java一样
	fmt.Println("Hello,World!")	
}
```

生成`.exe`

```
go build main.go
```

### 常量和变量

> var
>
> const







### 函数

```go
func test(num int) string{
	s := "aaa"
	return s
}
```



















```go
package main

import (
	"fmt"
	"os"
)

func main() {
	var s, sep string
	//os.Args

	for i := 1; i < len(os.Args); i++ {
		s += sep + os.Args[i]
		sep = " "
	}
	fmt.Println(s)
}
```







## 指针

```go
var ip *int		// 声明一个指针，这个指针用于指向int类型
var fp *float32
```

```go
var num int	= 10		// 声明一个int类型的变量并赋值为10
var intpoint *int	// 声明一个int类型的指针，用于存储内存地址
intpoint = &num		// num的内存地址放到指针中

fmt.Println(*intpoint)	// *intpoint 用于访问内存地址的内容
```



## 包的使用

### 结构体

```go
type Person struct{
    name string	// 如果开头首字母是小写，外部不能访问
    Age int
    Sex int 
}
```



### init()函数

先执行init函数

```go	
func init(){
    fmt.Println("this is init() function")
}
```





### time包

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	now := time.Now()
	fmt.Println(now)
}
```







