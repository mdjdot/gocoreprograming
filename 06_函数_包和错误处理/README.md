# init函数
执行流程  
全局变量定义 -> init函数 -> main函数  
引用包变量定义 -> 引用包init函数 -> 执行包变量定义 -> 执行包init函数 ->  执行包main函数  

defer  
defer先进后出  
defer会把相关值一起入栈，不会随之后值变化  
```
package main
import (
    "fmt"
)
func main() {
    n1 := 10
    defer fmt.Println(n1) // 10
    n1 = 20
    fmt.Println(n1) // 20
}
```

# 字符串常用函数
```
len(str)
n,err:=strconv.Atoi("123")
str:=strconv.Itoa(123)
bytes:=[]byte("hello")
str:=string([]byte{97,98,99})
str:=strconv.FormatInt(123,2)  // 数字转换二进制
b:=strings.Contains("seafood","foo")
couint:=string.Count("ceheese","e")
b:=strings.EqualFold("abc","ABC")
index:=strings.Index("NLT_abc","abc")
index:=strings.LastIndex("go golang","go")
strings.Replace("go go hello","go","golang",-1)
strArray:=strings.Split("hello,wprld,ok",",")
str:=strings.ToUpper("go")
str:=strings.Trim("!hello!","!")
str:=strings.TrimLeft("!hello!","!")
str:=strings.TrimRight("!hello!","!")
b:=strings.HasPrefix("http://abc.com","http")
b:=strings.HasSuffix("NLT_abd.jpg","jpg")
```

# 时间和日期相关函数
1. 时间类型 time.Time
2. 格式化时间 
    ```
	fmt.Printf 和 fmt.Sprintf
	fmt.Sprintf("%d/%d/%", time.Now().Year(),time.Now().Month(),time.Now().Day())

	time.Format
	Fmt.Printf(time.Now().Format("2006-01-02"))
    ```

3. 小时 -> 分钟 -> 秒 -> 毫秒 -> 微秒 -> 纳秒
	```
    time.Sleep(5*1e9)
	time.Sleep(5*time.Second)
	const (
	    Nanosecond  Duration = 1
	    Microsecond          = 1000 * Nanosecond
	    Millisecond          = 1000 * Microsecond
	    Second               = 1000 * Millisecond
	    Minute               = 60 * Second
	    Hour                 = 60 * Minute
	)
    ```
4. 时间戳 从 January 1, 1970 UTC时间到此时的秒或纳秒数
	```
    time.Unix()
	time.UnixNano()

	package main
	import "time"
	import "fmt"
	func main() {
	    var start, end int64
	    start = time.Now().Unix()
	    for i := 1; i < 1000; i++ {
	        time.Sleep(5 * time.Millisecond)
	        fmt.Println(i)
	    }
	    end = time.Now().Unix()
	    fmt.Println(end - start)
	    fmt.Println(time.Now())                    // 2019-12-12 15:39:47.2357748 +0800 CST m=+0.003996201
	    fmt.Println(time.Now().Local())            // 2019-12-12 15:39:47.2547567 +0800 CST
	    fmt.Println(time.Now().Local().Unix())     // 1576136387
	    fmt.Println(time.Now().Local().Location()) // Local
	    fmt.Println(time.Now().UTC())              // 2019-12-12 07:39:47.2547567 +0000 UTC
	    fmt.Println(time.Now().UTC().Unix())       // 1576136387
	    fmt.Println(time.Now().UTC().Location())   // UTC
	}
	```

# 内置函数
len：length:=len(str) 获取string、array、slice、map、channel长度  
new：ptr:=new(int) 分配值类型，返回指针  
make：ch:=make(chan int,2) 分配引用类型，返回类型对象  

# 错误处理
1. defer-recover
	```
    defer func(){
		err:=recover()
		if err!=nil{
			fmt.Println(err)
		}
	}
    ```
2. 自定义错误
	```
    package main
	import "errors"
	import "fmt"
	func isFive(num int) error {
	    if num != 5 {
	        return errors.New("Not 5")
	    }
	    return nil
	}
	func main() {
	    err := isFive(6)
	    fmt.Printn(err)
	}
    ```
	
# 小练习
1. 斐波那契数  
使用递归的方式，求出斐波那契数 1，1，2，3，5，8，13，给你一个正数n，求出它的斐波那契数是多少？  
	```
    package main
	import "fmt"
	func fbn(n int) int {
	    if n == 1 || n == 2 {
	        return 1
	    }
	    return fbn(n-2) + fbn(n-1)
	}
	func main() {
	    fmt.Println(fbn(7))
	}
	```

2. 求函数值  
已知 f(1)=3;f(n)=2*f(n-1)+1，使用递归的思想编程，求出f(n)的值  
	```
    package main
	import "fmt"
	func f(n int) int {
	    if n == 1 {
	        return 3
	    }
	    return 2*f(n-1) + 1
	}
	func main() {
	    fmt.Println(f(7))
	}
	```

3. 猴子吃桃子问题  
有一堆桃子，猴子第一天吃了其中的一半，并再多吃了一个。以后每天猴子都吃其中的一般，然后多吃一个。当到第十天时，想再吃时，发现只有1个桃子了。问题：最初共多少个桃子？
	```
    package main
	import "fmt"
	func f(n int) int {
	    if n == 10 {
	        return 1
	    }
	    return 2 * (f(n+1) + 1)
	}
	func main() {
	    fmt.Println(f(1))
	}
	```

4. 编写一个函数 swap(n1,n2 *int) 可以交换 n1 和 n2 的值
	```
    package main
	import "fmt"
	func swap(n1, n2 *int) {
	    *n1, *n2 = *n2, *n1
	}
	func main() {
	    num1, num2 := 12, 15
	    fmt.Println(num1, num2)
	    swap(&num1, &num2)
	    fmt.Println(num1, num2)
	}
	```

5. 编写一个程序，要求如下  
编写一个函数 makeSuffix(suffix string) 可以接收一个文件后缀名（比如.jpg），并返回一个闭包。调用闭包，可以传入一个文件名，如果该文件名没有指定的后缀（比如.jpg），则返回 文件名.jpg，如果已经有 .jpg后缀，则返回源文件名
	```
    package main
	import (
	    "fmt"
	    "strings"
	)
	func makeSuffix(suffix string) func(string) string {
	    return func(name string) string {
	        if strings.HasSuffix(name, suffix) {
	            return name
	        }
	        return fmt.Sprintf("%s%s", name, suffix)
	    }
	}
	func main() {
	    fmt.Println(makeSuffix(".jpg")("abc"))
	    fmt.Println(makeSuffix(".jpg")("def.jpg"))
	    fmt.Println(makeSuffix(".txt")("jjj"))
	    fmt.Println(makeSuffix(".txt")("kkk.txt"))
	}
	```

6. 编写一个函数，从终端输入一个整数打印出对应的金字塔
	```
    package main
	import "fmt"
	func printPy(n int) {
	    for i := 1; i <= n; i++ {
	        for k := 1; k <= n-i; k++ {
	            fmt.Print(" ")
	        }
	        for j := 1; j <= 2*i-1; j++ {
	            fmt.Print("*")
	        }
	        fmt.Println()
	    }
	}
	func main() {
	    printPy(10)
	}
	```




	
	
	
