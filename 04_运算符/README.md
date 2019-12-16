# 算数运算符
+、-、*、/、%、++、--  
Golang 没有++i、--i、i++ >4

#关系运算符
==、!=、<、>、<=、>=  
关系运算符结果都是bool型

# 逻辑运算符
&&、||、!  
Golang支持短路

# 赋值运算符
=、+=、-=、*=、/=、%=、<<=、>>=、&=、^=、|=

# 位运算符
&、|、^、<<、>>

# 其他运算符
&、*


# 原码、反码、补码
	• 二进制的最高位是符号位：0 表示正数，1 表示负数  
	1：0000 0001  
	-1：1000 0001
	• 正数原码、反码、补码都一样  
	1：0000 0001、0000 0001、0000 0001
	• 负数的反码符号位不变，其他位取反
	• 负数的补码是反码+1
	• 0 的反码和补码都是0
	• 计算机运算时，是以补码的方式来运算的

# 位运算和移位运算
	• 右移分为有符号右移(>>)和无符号右移(>>>)两种，前者补符号位，后者补0  
	fmt.Println(-1 >> 2) // -1 
	• 位运算使用补码计算，负数的反码符号位不变，其他位取反。补码是反码+1  
	fmt.Println(-3 ^ 3)  // -2 


# 小练习
	1. 假如还有97天放假，问：xx个星期零xx天
	```
    package main
	import "fmt"
	func main() {
	    num := 97
	    fmt.Printf("%d 个星期零 %d 天", num/7, num%7)
	}
	```

	2. 定义一个变量保存华氏温度，华氏温度转换摄氏温度的公式为：5/9*(华氏温度-100)，请求出华氏温度对应的摄氏温度
	```
    package main
	import "fmt"
	func main() {
	    temp := 134.2
	    fmt.Printf("%.2f 对应的摄氏温度为 %.2f", temp, 5.0/9.0*(temp-100.0))
	}
	```

	3. 有两个变量，a 和 b，要求将其进行交换，但是不允许使用中间变量，最终打印结果
	```
    package main
	import "fmt"
	func main() {
	    a, b := 12, 14
	    fmt.Printf("a=%d, b=%d\n", a, b)
	    a = a + b
	    b = a - b
	    a = a - b
	    fmt.Printf("a=%d, b=%d\n", a, b)
	    a, b = b, a
	    fmt.Printf("a=%d, b=%d\n", a, b)
	}
	```

	4. 求两个数的最大值
	```
    package main
	import (
	    "fmt"
	    "math"
	)
	func main() {
	    a, b, max := 12, 14, 0
	    if a > b {
	        max = a
	    } else {
	        max = b
	    }
	    fmt.Printf("max=%d", max)
	    fmt.Printf("max=%.0f", math.Max(float64(a), float64(b)))
	}
	```

	5. 求三个数的最大值
	```
    package main
	import (
	    "fmt"
	    "math"
	)
	func main() {
	    a, b, c, max := 12, 14, 15, 0
	    if a > b {
	        max = a
	    } else {
	        max = b
	    }
	    if c > max {
	        max = c
	    }
	    fmt.Printf("max=%d", max)
	    fmt.Printf("max=%.0f", math.Max(float64(a), float64(b)))
	}
	```

	6. 要求：可以从控制台接收用户信息，【姓名，年龄，薪水，是否通过考试】
	```
    package main
	import (
	    "fmt"
	)
	func main() {
	    var name string
	    var age int
	    var salary int
	    var pass bool
	    fmt.Print("请输入姓名：")
	    fmt.Scanln(&name)
	    fmt.Print("请输入年龄 薪水：")
	    fmt.Scanf("%d %d\n", &age, &salary) // fmt.Scanf后面不加换行，会击穿之后的Scan
	    fmt.Print("请输入是否通过考试：")
	    fmt.Scanln(&pass)
	    fmt.Printf("姓名：%s\n年龄：%d\n薪水：%d\n是否通过考试：%t\n", name, age, salary, pass)
	}
	```

	7. 位运算
	```
    package main
	import (
	    "fmt"
	)
	func main() {
	    fmt.Println(1 >> 2)  // 0
	    fmt.Println(1 << 2)  // 4
	    fmt.Println(-1 >> 2) // -1 右移分为有符号右移(>>)和无符号右移(>>>)两种，前者补符号位，后者补0
	    fmt.Println(-1 << 2) // -4
	    fmt.Println(-3 ^ 3)  // -2 位运算使用补码计算，负数的反码符号位不变，其他位取反。补码是反码+1
	    /*
	        数值  原码      反码      补码
	        -3      1000 0011   1111 1100   1111 1101
	        3       0000 0011   0000 0011   0000 0011
	        结果  1000 0010   1111 1101   1111 1110
	    */
	}
	```
