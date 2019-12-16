# 程序控制流程
	• 顺序控制
	• 分支控制（嵌套分支最好控制在三层内） 
	• 循环控制（多重循环最好控制三层内）

#switch 分支
fallthrough：穿透下一层，执行下一层，不考虑是否满足条件


# 小练习
	1. 判断一个年份是否是闰年，闰年的条件是符合下面二者之一：
	年份能被4整除，但不能被100整除；
	能被400整除
    ```
	package main
	import "fmt"
	func main() {
	    var year int
	    fmt.Print("请输入一个年份：")
	    fmt.Scanln(&year)
	    if year%400 == 0 || (year%4 == 0 && year%100 != 0) {
	        fmt.Printf("%d 是闰年", year)
	    } else {
	        fmt.Printf("%d 不是闰年", year)
	    }
	}
	```

	2. switch 分支四种写法
	```
    package main
	import "fmt"
	func main() {
	    var a = 10
	    switch {
	    case a == 11:
	        fmt.Println(11)
	        fallthrough // 穿透下一层，执行下一层，不考虑是否满足条件
	    case a < 11:
	        fmt.Println(10)
	        fallthrough
	    default:
	        fmt.Println(12)
	    }
	    switch a {
	    case 11:
	        fmt.Println(11)
	    case 10:
	        fmt.Println(10)
	    default:
	        fmt.Println(12)
	    }
	    switch b := 12; {
	    case b == 11:
	        fmt.Println(11)
	    case b < 11:
	        fmt.Println(10)
	    default:
	        fmt.Println(12)
	    }
	    var x interface{}
	    x = "astring"
	    switch t := x.(type) {
	    case nil:
	        fmt.Printf("%T\n", t)
	        fmt.Println("nil")
	    case string:
	        fmt.Printf("%T\n", t)
	        fmt.Print("string")
	    default:
	        fmt.Println(t)
	    }
	}
	```
	
	3. 使用 switch 把小写类型的 char 转为大写（键盘输入）。只转换 a、b、c、d。其他的输出“other”
	```
    package main
	import "fmt"
	func main() {
	    var c byte
	    fmt.Println("请输入一个字符：")
	    fmt.Scanf("%c\n", &c)
	    switch c {
	    case 'a':
	        fmt.Println("A")
	    case 'b':
	        fmt.Println("B")
	    case 'c':
	        fmt.Println("C")
	    case 'd':
	        fmt.Println("D")
	    default:
	        fmt.Println("other")
	    }
	}
	```

	4. for 四种写法
	```
    package main
	import "fmt"
	func main() {
	    for i := 1; i <= 10; i++ {
	        fmt.Println("hello i", i)
	    }
	    j := 1
	    for ; j <= 10; j++ {
	        fmt.Println("hello j", j)
	    }
	    m := 1
	    for j <= 10 {
	        fmt.Println("hello m", m)
	        m++
	    }
	    for k := range []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10} {
	        fmt.Println("hello k", k)
	    }
	    l := 1
	    for {
	        if l > 10 {
	            break
	        }
	        fmt.Println("hello l", l)
	        l++
	    }
	}
	```

	5. 字符串遍历，汉字在utf8编码中对应三个字节，所以需要把 []byte 转换为 []rune
	```
    package main
	import "fmt"
	import "unsafe"
	func main() {
	    s := "abcdef"
	    for index, value := range s {
	        fmt.Printf("%d: %c %d\n", index, value, unsafe.Sizeof(value))
	    }
	    s1 := "abc中文字符串"
	    for index, value := range s1 {
	        fmt.Printf("%d: %c %d\n", index, value, unsafe.Sizeof(value))
	    }
	    for index, value := range []byte(s1) { // []byte 类型字符串中文长度不够显示乱码
	        fmt.Printf("%d: %c %d\n", index, value, unsafe.Sizeof(value))
	    }
	    for index, value := range []rune(s1) {
	        fmt.Printf("%d: %c %d\n", index, value, unsafe.Sizeof(value))
	    }
	}
    ```
	
	6. 打印九九乘法表
	```
    package main
	import "fmt"
	func main() {
	    /*
	        1 * 1 = 1
	        1 * 2 = 2   2 * 2 = 4
	        1 * 3 = 3   2 * 3 = 6   3 * 3 = 9
	    */
	    for i := 1; i < 10; i++ {
	        for j := 1; j < i+1; j++ {
	            fmt.Printf("%d * %d = %d\t", j, i, i*j)
	        }
	        fmt.Println()
	    }
	}
	```

	7. 打印随机数
	```
    package main
	import (
	    "fmt"
	    "math/rand"
	)
	func main() {
	    count := int64(0)
	    for {
	        // rand.Seed(time.Now().UnixNano())
	        // rand.Seed(123)
	        rand.Seed(count)
	        n := rand.Intn(100) + 1
	        fmt.Println(n)
	        count++
	        if n == 99 || count > 100 {
	            break
	        }
	    }
	    fmt.Println(count)
	}
	```
