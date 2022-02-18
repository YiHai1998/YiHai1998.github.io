# server

## 内容

```markdown
1. 初始化
2. 配置文件
	1.  如果没有内容，使用默认配置
	2.  否则调用 newConfigFromFile() 函数
		- 使用了ioutil 读取文件配置文件
3. 监听
	- 新建db服务	rosedb.Open
```

## main.go

```go
package main

import (
	"flag"
	"fmt"
	"github.com/pelletier/go-toml"
	"github.com/roseduan/rosedb"
	"github.com/roseduan/rosedb/cmd"
	"io/ioutil"
	"log"
	"os"
	"os/signal"
	"syscall"
)

func init() {
	// print banner
	banner, _ := ioutil.ReadFile("../../resource/banner.txt")
	fmt.Println(string(banner))
}

// 	flag 包实现命令行标签解析.
var config = flag.String("config", "", "the config file for rosedb")
var dirPath = flag.String("dir_path", "", "the dir path for the database")

func main() {
	/**
		在所有flag都注册之后，调用：flag.Parse()来解析命令行参数写入注册的flag里。
		解析之后，flag的值可以直接使用。如果你使用的是flag自身，它们是指针；如果你绑定到了某个变量，它们是值。
	 */
	flag.Parse()


	//set the config
	var cfg rosedb.Config
	// 判断配置是否为空
	if *config == "" {
		log.Println("没有设置配置, 将使用默认配置.")
		cfg = rosedb.DefaultConfig()
	} else {
		c, err := newConfigFromFile(*config)	// 调用最下面的那个函数
		if err != nil {
			log.Printf("load config err : %+v\n", err)
			return
		}
		cfg = *c	// 取值
	}

	if *dirPath == "" {
		log.Println("没有设置路径, 将使用默认路径.")
	} else {
		cfg.DirPath = *dirPath
	}

	//listen the server
	// signal包实现了对输入信号的访问。
	sig := make(chan os.Signal, 1)
	signal.Notify(sig, os.Interrupt, os.Kill, syscall.SIGHUP,
		syscall.SIGINT, syscall.SIGTERM, syscall.SIGQUIT)

	server, err := cmd.NewServer(cfg)	// 现在正式开启了dbserver！
	if err != nil {
		log.Printf("创建数据库shu: %+v\n", err)
		return
	}
	go server.Listen(cfg.Addr)

	<-sig
	server.Stop()
	log.Println("数据库关闭, bye...")
}

func newConfigFromFile(config string) (*rosedb.Config, error) {
	data, err := ioutil.ReadFile(config)
	if err != nil {
		return nil, err
	}

	var cfg = new(rosedb.Config)
	err = toml.Unmarshal(data, &cfg)
	if err != nil {
		return nil, err
	}
	return cfg, nil
}
```



## 初始化

```go
func init() {
   // 打印 banner
   banner, _ := ioutil.ReadFile("../../resource/banner.txt")
   fmt.Println(string(banner))
}
```

banner可以自己设计

```
 ___       ________  ________   ________          ________  ________
|\  \     |\   __  \|\   ___  \|\   ____\        |\   ___ \|\   __  \
\ \  \    \ \  \|\  \ \  \\ \  \ \  \___|        \ \  \_|\ \ \  \|\ /_
 \ \  \    \ \  \\\  \ \  \\ \  \ \  \  ___       \ \  \ \\ \ \   __  \
  \ \  \____\ \  \\\  \ \  \\ \  \ \  \|\  \       \ \  \_\\ \ \  \|\  \
   \ \_______\ \_______\ \__\\ \__\ \_______\       \ \_______\ \_______\
    \|_______|\|_______|\|__| \|__|\|_______|        \|_______|\|_______|

```

[Spring Boot banner在线生成工具，制作下载banner.txt，修改替换banner.txt文字实现自定义，个性化启动banner-bootschool.net](https://www.bootschool.net/ascii)

