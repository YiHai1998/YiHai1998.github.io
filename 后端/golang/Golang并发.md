线程可以理解为轻量级进程

协程可以理解为轻量级线程

```go
func main() {
	go printNum()

	for i := 0; i <= 1000; i++ {
		fmt.Println("\t主goroutine", i)
	}
	fmt.Println("over")
}

func printNum(){
	for i := 0; i < 1000; i++ {
		fmt.Println("子goroutine", i)
	}
}
```

交替执行



### chan

```go
func main() {
	var ch1 chan bool
	ch1 = make(chan bool)

	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println("子goroutine", i)
		}
		ch1 <- true
		fmt.Println("over")
	}()

	var data bool = <-ch1	// 阻塞的，只有上面ch1 <- true 完成写操作，才能解除阻塞
	fmt.Println("main...data->", data)

	fmt.Println("main over...")
}
```

