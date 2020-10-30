# 打开文件
```
package main
import (
    "fmt"
    "os"
)
func main() {
    file, err := os.Open("./text.txt") // 调OpenFile(name, O_RDONLY, 0)
    if err != nil {
        fmt.Println(err)
    }
    defer file.Close()
    file, err = os.OpenFile("./text.txt", os.O_CREATE, os.ModeAppend)
    if err != nil {
        fmt.Println(err)
    }
    defer file.Close()
}
```

# 读文件
```
file, err := os.Open("")
defer file.Close()

reader := bufio.NewReade(file)
for {
	str, err := reader.ReadString('\n')
	if err!=io.EOF{
		break
	}
	fmt.Println(str)
}
fmt.Println("文件读取结束")
```

文件不大的情况下可以使用ioutil一次把文件读到内存中  
```
content, err := ioutil.ReadFile("") // 已封装Close
if err != nil{
	fmt.Println(err)
}
fmt.Println(string(content)) // []byte
```

# 写文件  
```
File, err := os.OpenFile("",os.O_RDONLY,)
```

# 命令行参数
	• os.Args存储命令行参数
	• flag包解析命令行参数

```
package main
import (
    "fmt"
    "os"
)
func main() {
    for index, arg := range os.Args { //第一个参数是自身
        fmt.Println(index, arg)
    }
}
```

```
package main
import (
    "flag"
    "fmt"
)
func main() {
    var user string
    var password string
    flag.StringVar(&user, "user", "u", "user name")
    flag.StringVar(&password, "password", "p", "user password")
    var host = flag.String("host", "127.0.0.1", "host namr")
    flag.Parse()
    fmt.Println(user, password, *host) // flag.String取值不能在赋值之前
}
```

# json
- Javascript object notation
- Golang --序列化--> json字符串 --网络传输--> 程序 --反序列化--> 其他语言
- json解析网站
https://www.json.cn/  
    ```
	[{"name":"zhangsan","Age":12,"Gender":null},{"name":"zhangsan","Age":12,"Gender":null}]
    ```

# 小练习
1. 创建一个新的文件，写入内容 5句“hello，Gardon”
	```
    package main
	import "os"
	func main() {
	    // file, err := os.Open("./test.txt") // 不存在会报错
	    file, err := os.OpenFile("./test.txt", os.O_CREATE|os.O_APPEND, os.ModeAppend)
	    if err != nil {
	        panic(err)
	    }
	    defer file.Close()
	    for i := 0; i < 5; i++ {
	        file.WriteString("hello, Gardon\n")
	    }
	}
	```

2. 打开一个存在的文件，将原来的内容覆盖成新的内容10句，“你好，尚硅谷！”
	```
    package main
	import "os"
	func main() {
	    file, err := os.OpenFile("./test.txt", os.O_CREATE|os.O_TRUNC, os.ModeAppend)
	    if err != nil {
	        panic(err)
	    }
	    defer file.Close()
	    for i := 0; i < 10; i++ {
	        file.WriteString("你好，尚硅谷！\n")
	    }
	}
	```

3. 打开一个文件，使用带缓存的方式写入5句。“你好，尚硅谷！”
	```
    package main
	import "os"
	import "bufio"
	func main() {
	    file, err := os.OpenFile("./test.dat", os.O_WRONLY|os.O_CREATE, os.ModeAppend)
	    if err != nil {
	        panic(err)
	    }
	    defer file.Close()
	    writer := bufio.NewWriter(file)
	    for i := 0; i < 5; i++ {
	        writer.WriteString("你好，尚硅谷！")
	    }
	    writer.Flush()
	}
	```

4. 编写一个程序，将一个文件的内容，写入到另一个文件。
	```
    package main
	import "io/ioutil"
	import "os"
	func main() {
	    content, err := ioutil.ReadFile("./test.txt")
	    if err != nil {
	        panic(err)
	    }
	    err = ioutil.WriteFile("./test.dat", content, os.ModeAppend)
	    if err != nil {
	        panic(err)
	    }
	}
	```

5. 将一个文件拷贝到其他路径
	使用bufio 需要Flush，Flush需要在File Close之前
	```
    package main
	import (
	    "bufio"
	    "fmt"
	    "io"
	    "os"
	)
	func main() {
	    f1, err := os.Open("./read.dat")
	    if err != nil {
	        fmt.Println(err)
	    }
	    defer f1.Close()
	    reader := bufio.NewReader(f1)
	    f2, err := os.OpenFile("./write.dat", os.O_WRONLY|os.O_CREATE, os.ModeAppend)
	    // f2, err := os.OpenFile("./write.dat", os.O_WRONLY|os.O_CREATE, 0666)
	    if err != nil {
	        fmt.Println(err)
	    }
	    defer f2.Close()
	    writer := bufio.NewWriter(f2) // 使用bufio 需要Flush，Flush需要在File Close之前
	    defer writer.Flush()
	    _, err = io.Copy(writer, reader)
	    if err != nil {
	        fmt.Println(err)
	    }
	}
    ```

6. 统计一个文件中含有的英文、数字、空格及其他字符数量
	```
    package main
	import (
	    "fmt"
	    "io/ioutil"
	)
	var (
	    charCount  int
	    numCount   int
	    spaceCount int
	    otherCount int
	)
	func main() {
	    content, err := ioutil.ReadFile("./test.txt")
	    if err != nil {
	        panic(err)
	    }
	    fmt.Println(content)
	    fmt.Println(string(content))
	    for _, v := range string(content) {
	        switch {
	        case v >= 'A' && v <= 'Z', v >= 'a' && v <= 'z':
	            charCount++
	        case v >= '0' && v <= '9':
	            numCount++
	        case v == ' ':
	            spaceCount++
	        default:
	            otherCount++
	        }
	    }
	    fmt.Printf("字母：%d\n数字：%d\n空格：%d\n其他：%d", charCount, numCount, spaceCount, otherCount)
	}
	```

7. 序列化和反序列化实践 
	```
    package main
	import "encoding/json"
	import "fmt"
	type Person struct {
	    Name string `json:"name,omitempty"`
	    Age  int    `json:"age,omitempty"`
	}
	func main() {
	    ps := []Person{
	        {Name: "zhangsan", Age: 12},
	        {Name: "lisi", Age: 15},
	    }
	    jsonByte, err := json.Marshal(ps)
	    if err != nil {
	        fmt.Println(err)
	    }
	    fmt.Println(jsonByte)
	    fmt.Println(string(jsonByte)) // [{"name":"zhangsan","age":12},{"name":"lisi","age":15}]
	    ps1 := make([]Person, 2)
	    err = json.Unmarshal(jsonByte, &ps1)
	    fmt.Printf("%+v", ps1) // [{Name:zhangsan Age:12} {Name:lisi Age:15}]
    }
    ```