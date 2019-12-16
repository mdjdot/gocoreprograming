# 变量的使用
	1. 声明变量
	2. 变量赋值
	3. 使用变量

# 变量声明方式
```
var s string # 声明不赋值，使用默认值
var num int = 10
var num = 1.1 # 自推断类型，浮点型默认声明为float64类型
var a, b, c int
var a, b, c int = 1, 2, 3
num  := 11
num := 10 # 自推断类型
```

# 字符类型使用
[字符对应utf8码值](www.mytju.com/classcode/tools/encode_utf8.asp)

```
package main
import "fmt"
func main() {
    var c1 byte = 'a'
    var c2 byte = '0'
    var c3 int = '中'
    fmt.Println(c1) // 97
    fmt.Println(c2) // 48
    fmt.Println(c3) // 20013
    fmt.Println(string(c2)) // 0
    fmt.Println(string(c2)) // 0
    fmt.Println(string(c3)) // 中
    fmt.Printf("%c\n", c1) // a
    fmt.Printf("%c\n", c2) // 0
    fmt.Printf("%c", c3)   // 中
}
```
```
package main
import "fmt"
import "unsafe"
func main() {
    var a = 10
    var b = true
    var c = 'A'
    var c1 = "中"
    var s = "abc"
    var s1 = "中国人"
    fmt.Println(unsafe.Sizeof(a))  // 8
    fmt.Println(unsafe.Sizeof(b))  // 1
    fmt.Println(unsafe.Sizeof(c))  // 4
    fmt.Println(unsafe.Sizeof(c1)) // 16
    fmt.Println(unsafe.Sizeof(s))  // 16
    fmt.Println(unsafe.Sizeof(s1)) // 16
}
```

# string 类型使用
	1. 字符串赋值后不可修改
	2. 双引号标识字符串
	3. 反引号保持字符串格式

# 数据类型转换
	1. 强制转换，type(v)，转换为范围小的类型会溢出
	2. strconv包转换
	3. fmt.Sprinf("%s", v)转换

# 值类型和引用类型
值类型：intx、floatx、bool、string、数组、结构体  
引用类型：指针、切片、map、管道、interface等

# 标识符命名注意事项
	1. package 名和目录保持一致，package 名简短、有意义，不要和标准库重名
	2. 变量名、函数名、常量名采用驼峰命名法
	3. 大写字母开头变量可以被引用包使用，小写字母开头变量只能在本包中使用

# 小练习
	1. 写一个程序，获取一个 int 变量 num 的地址，并显示到终端  
	将 num 的地址赋给指针 ptr，并通过ptr去修改 num 的值
	```
    package main
	import "fmt"
	func main() {
	    var num int = 10
	    ptr := &num
	    fmt.Println(num) // 10
	    fmt.Println(ptr) // 0xc0000120a8
	    *ptr = 20
	    fmt.Println(num) // 20
	}
    ```
