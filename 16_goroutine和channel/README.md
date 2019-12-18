# 需求：要求统计1-9000000000的数字中，那些是素数？
	1. 传统方法，使用一个循环，循环判断各个数是不是素数
	2. 使用并发或者并行的方式，将统计素数的任务分配给多个goroutine去完成
	```
    package main
	import "fmt"
	import "runtime"
	import "time"
	func main() {
	    num := runtime.NumCPU()
	    runtime.GOMAXPROCS(num)
	    for i := 0; i < 9000; i++ {
	        go func(m int) {
	            for j := 0; j < 1000; j++ {
	                go func(n int) {
	                    for k := 1; k < 1000; k++ {
	                        for l := 2; l < i*1000+j*1000+k; l++ {
	                            if (i*1000+j*1000+k)%l == 0 {
	                                break
	                            }
	                        }
	                        fmt.Println(i*1000 + j*1000 + k)
	                    }
	                }(j)
	            }
	        }(i)
	    }
	    time.Sleep(300 * time.Second)
	}
	```

# 并行与并发
多线程程序在单核上运行，就是并发  
多线程程序在多核上运行，就是并行  
    num := runtime.NumCPU()  
    runtime.GOMAXPROCS(num) // Go1.8以后不用设置了，默认运行在多核上  

# Goroutine 协程的特点
	1. 有独立的栈空间
	2. 共享程序堆空间
	3. 调度由用户控制
	4. 协程是轻量级的线程

# Goroutine 的调度模型
	• MPG 模式
		1. M：操作系统的主线程（是物理线程）
		2. P：协程执行需要的上下文
		3. G：协程

# Channel 管道
• channel先进先出
• channel线程安全，不需要加锁
• channel是有类型的
• chnnel必须先初始化后使用，make
• channel中只能存放指定的数据类型
• channel中数据放满后，继续放入 deadlock
• channel中没有值时，取值 deadlock
• channel关闭后，不能写数据，但可以读数据
• 遍历channel前，先关闭channel，防止deadlock
```
package main
import (
    "errors"
    "fmt"
)
func main() {
    intChan := make(chan int, 3)
    fmt.Printf("%+v %+v\n", intChan, &intChan)          // 0xc000096080 0xc00008e018
    fmt.Printf("%+v %+v\n", len(intChan), cap(intChan)) // 0 3
    intChan <- 10
    intChan <- 20
    intChan <- 30
    fmt.Printf("%+v %+v\n", len(intChan), cap(intChan)) // 3 3
    // intChan <- 40 // 超过管道容量 deadlock
    fmt.Printf("%+v %+v\n", intChan, &intChan)   // 0xc000096080 0xc00008e018
    fmt.Println(<-intChan, <-intChan, <-intChan) // 10 20 30 先进先出
    // fmt.Println(<-intChan)                       // 管道中无值，取值 deadlock
    allChan := make(chan interface{}, 5)
    allChan <- 10
    allChan <- "abc"
    allChan <- 12.34
    allChan <- errors.New("error string")
    allChan <- struct{ Age int }{Age: 12}
    // count := len(allChan)
    // for i := 0; i < count; i++ {
    //  fmt.Printf("%+v\n", <-allChan)
    // }
    close(allChan) // 遍历前不关闭 deadlock
    for {
        if a, ok := <-allChan; ok {
            fmt.Printf("%+v\n", a)
        } else {
            break
        }
    }
}
```



# 需求：现在要计算1-200的各个数的阶乘，并且把各个数的阶乘放入到map中。最后显示出来，要求使用goroutine完成
	1. 不用goroutine写法，数据太大会溢出
	```
    package main
	import "fmt"
	func main() {
	    maps := make(map[int]int)
	    for i := 1; i <= 20; i++ { // int64也不够长也会溢出
	        maps[i] = func(n int) int {
	            var result = 1
	            for j := 1; j <= i; j++ {
	                result *= j
	            }
	            return result
	        }(i)
	    }
	    fmt.Printf("%+v", maps)
	}
	```
	
	2. goroutine写法
	资源竞争
	```
    go run .\test.go
	map[3:6 6:720 7:5040 9:362880 15:1307674368000 17:355687428096000 21:-4249290049419214848]
	package main
	import (
	    "fmt"
	    "time"
	)
	func main() {
	    maps := make(map[int]int)
	    for i := 1; i <= 20; i++ { // int64也不够长也会溢出
	        go func(maps map[int]int, key int) {
	            maps[i] = func(n int) int {
	                var result = 1
	                for j := 1; j <= i; j++ {
	                    result *= j
	                }
	                return result
	            }(i)
	        }(maps, i)
	    }
	    time.Sleep(5 * time.Second)
	    fmt.Printf("%+v", maps)
	}
	```

	3. 不同goroutine之间通信
		1. 全局变量的互斥锁
		2. 使用管道channel来解决
		```
        package main
		import (
		    "fmt"
		    "sync"
		    "time"
		)
		var maps = make(map[int]int)
		var mux = sync.Mutex{}
		func main() {
		    for i := 1; i <= 20; i++ {
		        go func(key int) {
		            mux.Lock()
		            maps[key] = func(n int) int {
		                var result = 1
		                for j := 1; j <= n; j++ {
		                    result *= j
		                }
		                return result
		            }(key)
		            mux.Unlock()
		        }(i)
		    }
		    time.Sleep(5 * time.Second)
		    fmt.Printf("%+v", maps)
		}
		```
        ```
		package main
		import (
		    "fmt"
		    "sync"
		)
		var maps = make(map[int]int)
		var mux = sync.Mutex{}
		var wg = sync.WaitGroup{}
		func main() {
		    wg.Add(20)
		    for i := 1; i <= 20; i++ {
		        go func(key int) {
		            mux.Lock()
		            maps[key] = func(n int) int {
		                var result = 1
		                for j := 1; j <= n; j++ {
		                    result *= j
		                }
		                return result
		            }(key)
		            mux.Unlock()
		            wg.Done()
		        }(i)
		    }
		    wg.Wait()
		    fmt.Printf("%+v", maps)
		}
		```

# 只读和只写 channel
```
package main
import "fmt"
var (
    ch         = make(chan int, 1)
    exitSendCh = make(chan bool, 1)
    exitRecvCh = make(chan bool, 1)
)
func send(ch chan<- int) {
    ch <- 5
    close(ch)
    exitSendCh <- true
}
func recv(ch <-chan int) {
    for {
        <-exitSendCh
        if v, ok := <-ch; ok {
            fmt.Println(v)
            exitRecvCh <- true
            break
        }
    }
}
func main() {
    go send(ch)
    go recv(ch)
    <-exitRecvCh
    fmt.Println("done")
}
```

# select 解决管道阻塞
```
package main
import (
    "fmt"
    "time"
)
func main() {
    intChan := make(chan int, 1)
    strChan := make(chan string, 1)
    boolChan := make(chan bool, 1)
    go func() {
        time.Sleep(1 * time.Second)
        intChan <- 3
    }()
    strChan <- "abc"
    boolChan <- true
    // 管道都有值时，随机取一个
    select {
    case <-intChan:
        fmt.Println("intChan")
    case <-strChan:
        fmt.Println("strChan")
    case <-boolChan:
        fmt.Println("boolChan")
    case <-time.After(2 * time.Second):
        fmt.Println("timeout")
        // default:
        //  fmt.Println("None")
    }
}
```

# recover 捕获panic，但不能解决死锁
```
package main
import "fmt"
func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()
    panic("an error")
}
```


# 小练习
	1. 编写一个程序，在进程中开启一个goroutine，该协程每隔1秒输出“hello,world”，在进程中也每隔一秒输出“hello,golang”，输出10次后，退出程序
	```
    package main
	import (
	    "fmt"
	    "time"
	)
	func main() {
	    go func() {
	        for {
	            time.Sleep(time.Second)
	            fmt.Println("hello,world")
	        }
	    }()
	    for i := 0; i < 10; i++ {
	        time.Sleep(time.Second)
	        fmt.Println("hello,golang")
	    }
	}
	```

	2. 应用实例
		1. 开启一个writeData协程，相关到intChan中写入50个整数
		2. 开启一个readData协程，从iniChan中读取writeData写入的数据
		3. 主线程需要等待writeData和readData协程都完成工作才能退出
	```
    package main
	import "fmt"
	func main() {
	    intChan := make(chan int, 50)
	    exitChan := make(chan bool, 1)
	    go func() {
	        for i := 1; i <= 50; i++ {
	            intChan <- i
	        }
	    }()
	    go func() {
	        for i := 1; i <= 50; i++ {
	            fmt.Println(<-intChan)
	        }
	        exitChan <- true
	    }()
	    <-exitChan
	    fmt.Println("done")
	}
	```

	3. 应用实例
	要求统计1-200000的数字中，哪些是素数
	```
    package main
	import "fmt"
	import "math"
	func checkNum(n int) bool {
	    if n < 2 {
	        return false
	    }
	    // for i := 2; i < n; i++ {
	    end := int(math.Sqrt(float64(n))) + 1
	    for i := 2; i < end; i++ {
	        if n%i == 0 {
	            return false
	        }
	    }
	    return true
	}
	func main() {
	    for i := 1; i <= 80000; i++ {
	        if checkNum(i) {
	            fmt.Println(i)
	        }
	    }
	}
	```
	
    ```
	package main
	import "math"
	import "fmt"
	func checkNum(n int) bool {
	    if n < 2 {
	        return false
	    }
	    // for i := 2; i < n; i++ {
	    end := int(math.Sqrt(float64(n))) + 1
	    for i := 2; i < end; i++ {
	        if n%i == 0 {
	            return false
	        }
	    }
	    return true
	}
	func main() {
	    num := 8000
	    exitChan := make(chan bool, 1)
	    numChan := make(chan int, num)
	    go func(n int) {
	        for i := 1; i <= n; i++ {
	            if checkNum(i) {
	                numChan <- i
	            }
	        }
	        close(numChan)
	        exitChan <- true
	    }(num)
	    <-exitChan
	    for {
	        if num, ok := <-numChan; ok {
	            fmt.Println(num)
	            continue
	        }
	        break
	    }
	}
	```
