# Client

## 内容

```markdown
1. 命令
```



## main.go

```go
package main

import (
	"bufio"
	"encoding/binary"
	"flag"
	"fmt"
	"github.com/peterh/liner"
	"log"
	"net"
	"os"
	"strings"
)

// all supported commands
var commandList = [][]string{
	{"SET", "key value", "STRING"},
	{"GET", "key", "STRING"},
	{"SETNX", "key value", "STRING"},
	{"GETSET", "key value", "STRING"},
	{"APPEND", "key value", "STRING"},
	{"STRLEN", "key", "STRING"},
	{"STREXISTS", "key", "STRING"},
	{"STRREM", "key", "STRING"},
	{"PREFIXSCAN", "prefix limit offset", "STRING"},
	{"RANGESCAN", "start end", "STRING"},
	{"EXPIRE", "key seconds", "STRING"},
	{"PERSIST", "key", "STRING"},
	{"TTL", "key", "STRING"},

	{"LPUSH", "key value [value...]", "LIST"},
	{"RPUSH", "key value [value...]", "LIST"},
	{"LPOP", "key", "LIST"},
	{"RPOP", "key", "LIST"},
	{"LINDEX", "key index", "LIST"},
	{"LREM", "key value count", "LIST"},
	{"LINSERT", "key BEFORE|AFTER pivot element", "LIST"},
	{"LSET", "key index value", "LIST"},
	{"LTRIM", "key start end", "LIST"},
	{"LRANGE", "key start end", "LIST"},
	{"LLEN", "key", "LIST"},

	{"HSET", "key field value", "HASH"},
	{"HSETNX", "key field value", "HASH"},
	{"HGET", "key field", "HASH"},
	{"HGETALL", "key", "HASH"},
	{"HDEL", "key field [field...]", "HASH"},
	{"HEXISTS", "key field", "HASH"},
	{"HLEN", "key", "HASH"},
	{"HKEYS", "key", "HASH"},
	{"HVALUES", "key", "HASH"},

	{"SADD", "key members [members...]", "SET"},
	{"SPOP", "key count", "SET"},
	{"SISMEMBER", "key member", "SET"},
	{"SRANDMEMBER", "key count", "SET"},
	{"SREM", "key members [members...]", "SET"},
	{"SMOVE", "src dst member", "SET"},
	{"SCARD", "key", "key", "SET"},
	{"SMEMBERS", "key", "SET"},
	{"SUNION", "key [key...]", "SET"},
	{"SDIFF", "key [key...]", "SET"},

	{"ZADD", "key score member", "ZSET"},
	{"ZSCORE", "key member", "ZSET"},
	{"ZCARD", "key", "ZSET"},
	{"ZRANK", "key member", "ZSET"},
	{"ZREVRANK", "key member", "ZSET"},
	{"ZINCRBY", "key increment member", "ZSET"},
	{"ZRANGE", "key start stop", "ZSET"},
	{"ZREVRANGE", "key start stop", "ZSET"},
	{"ZREM", "key member", "ZSET"},
	{"ZGETBYRANK", "key rank", "ZSET"},
	{"ZREVGETBYRANK", "key rank", "ZSET"},
	{"ZSCORERANGE", "key min max", "ZSET"},
	{"ZREVSCORERANGE", "key max min", "ZSET"},
}

var host = flag.String("h", "127.0.0.1", "the rosedb server host, default 127.0.0.1")
var port = flag.Int("p", 5200, "the rosedb server port, default 5200")

const cmdHistoryPath = "/tmp/longdb-cli"

func main() {
	flag.Parse()

	addr := fmt.Sprintf("%s:%d", *host, *port)
	conn, err := net.Dial("tcp", addr)	// 建立tcp连接
	if err != nil {
		log.Println("tcp dial err: ", err)
		return
	}

	// line的效果：127.0.0.1:5200>
	line := liner.NewLiner()
	defer line.Close()

	line.SetCtrlCAborts(true)	// 如果为true， 按住ctrl + c会直接退出客户端
	line.SetCompleter(func(li string) (res []string) {
		for _, c := range commandList {		// 返回索引和内容，内容如"SET", "key value", "STRING"
			// c[0]为SET，也就是第一个。HasPrefix判断字符串是否有某个前缀，ToUpper转换为大写
			if strings.HasPrefix(c[0], strings.ToUpper(li)) {	// 如果前缀为LI，如LINDEX
				res = append(res, strings.ToLower(c[0]))	//	命令行全部转为小写！
			}
		}
		return
	})

	// 打开并保存cmd历史，cmdHistoryPath默认为"/tmp/longdb-cli"
	if f, err := os.Open(cmdHistoryPath); err == nil {
		line.ReadHistory(f)
		f.Close()
	}
	defer func() {
		if f, err := os.Create(cmdHistoryPath); err != nil {
			fmt.Printf("writing cmd history err: %v\n", err)
		} else {
			line.WriteHistory(f)
			f.Close()
		}
	}()

	commandSet := map[string]bool{}
	for _, cmd := range commandList {
		commandSet[strings.ToLower(cmd[0])] = true	// 所有cmd命令设置为true
	}

	prompt := addr + "/longdb" + ">"	//	127.0.0.1:5200>
	for {
		cmd, err := line.Prompt(prompt)
		if err != nil {
			fmt.Println(err)
			break
		}

		// TrimSpace 将 cmd 左侧和右侧的间隔符去掉。常见间隔符包括：'\t', '\n', '\v', '\f', '\r', ' ', U+0085 (NEL)
		cmd = strings.TrimSpace(cmd)
		if len(cmd) == 0 {
			continue
		}
		cmd = strings.ToLower(cmd)

		c := strings.Split(cmd, " ")
		if cmd == "help" {
			printCmdHelp()
		} else if cmd == "quit" {
			break
		} else if c[0] == "help" && len(c) == 2 {
			helpCmd := c[1]
			if !commandSet[helpCmd] {
				fmt.Println("command not found")
				continue
			}

			for _, command := range commandList {
				if strings.ToLower(command[0]) == helpCmd {
					fmt.Println()
					fmt.Println(" --usage: " + helpCmd + " " + command[1])
					fmt.Println(" --group: " + command[2] + "\n")
				}
			}
		} else {
			line.AppendHistory(cmd)

			if !commandSet[c[0]] && c[0] != "quit" {
				fmt.Println("command not found")
				continue
			}

			wInfo := wrapCmdInfo(cmd)
			_, err := conn.Write(wInfo)
			if err != nil {
				fmt.Println(err)
			}

			reply := readReply(conn)
			fmt.Println(reply)
		}
	}
}

func printCmdHelp() {
	help := `
 感谢使用RoseDB——rosedb-cli
 如果想要关于命令的帮助：
	Type: "help <command>" for help on command
 如果想要关闭客户端：
	请按住<ctrl+c> 或者 输入<quit>`
	fmt.Println(help)
}

func wrapCmdInfo(cmd string) []byte {
	b := make([]byte, len(cmd)+4)
	binary.BigEndian.PutUint32(b[:4], uint32(len(cmd)))
	copy(b[4:], cmd)
	return b
}

func readReply(conn net.Conn) (res string) {
	reader := bufio.NewReader(conn)

	b := make([]byte, 4)
	_, err := reader.Read(b)
	if err != nil {
		return
	}
	size := binary.BigEndian.Uint32(b)
	if size > 0 {
		data := make([]byte, size)
		reader.Read(data)
		res = string(data)
	}
	return
}

```

