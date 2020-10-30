# Golang的OOP
- 继承通过匿名字段实现
- 多态通过接口实现
- 结构体是值类型，结构体中的所有字段在内存中是连续的

# Golang的方法
结构体是值类型，如果修改结构体变量的值，可以通过传递结构体指针的方式

# 工厂模式
使用创建实例函数做构造函数
```
package main
import (
    "fmt"
)
type aint int
func (a aint) ShowSomething() {
    fmt.Println("something")
}
func main() {
    var n1 int = 10
    var n2 aint = 20
    n2 = aint(n1)
    n1 = int(n2)
    fmt.Println(n1)
    fmt.Println(n2)
    aint(5).ShowSomething()
}
```

```
package main
import "encoding/json"
import "fmt"
type monster struct {
    Name string `json:"monstername"` // 结构体属性不能小写，不然json.Marshal不出来
    Age  int    `json:"Age"`
}
func main() {
    var amonster = monster{
        Name: "zhangsan",
        Age:  12,
    }
    jsonStr, err := json.Marshal(amonster)
    if err != nil {
        panic(err)
    }
    fmt.Printf("%s\n", jsonStr)
    var oneMonster monster
    err = json.Unmarshal(jsonStr, &oneMonster)
    fmt.Println(oneMonster)
    fmt.Printf("%+v", oneMonster)
}
```

```
package main
import "fmt"
type monster struct {
    Name string `json:"monstername"` // 结构体属性不能小写，不然json.Marshal不出来
    Age  int    `json:"Age"`
}
func (m *monster) UpdateAge() {
    m.Age = 20
}
func (m monster) UpdateName() {
    m.Name = "kk"
}
func main() {
    var amonster = monster{
        Name: "zhangsan",
        Age:  12,
    }
    fmt.Println(amonster)
    amonster.UpdateAge()
    amonster.UpdateName()
    fmt.Println(amonster)
}
```

类型实现了String() string 方法，如果是指针方法，只有类型指针和显式调用，才能使用；如果是普通方法，不用显示调用，普通类型和指针都能调用。
```
package main
import "fmt"
type monster struct {
    Name string `json:"monstername"` // 结构体属性不能小写，不然json.Marshal不出来
    Age  int    `json:"Age"`
}
// func (m monster) String() string {
func (m *monster) String() string {
    return "monster string method"
}
func (m *monster) UpdateAge() {
    m.Age = 20
}
func (m monster) UpdateName() {
    m.Name = "kk"
}
func main() {
    var amonster = monster{
        Name: "zhangsan",
        Age:  12,
    }
    fmt.Println(&amonster) // monster string method
    amonster.UpdateAge()
    amonster.UpdateName()
    fmt.Println(amonster)          // {zhangsan 20}
    fmt.Println(amonster.String()) // monster string method
}
```

# 小练习
1. 编写结构体，编写一个方法，方法不需要参数，在方法中打印一个 10*8 的矩形，在main方法中调用该方法
	```
    package main
	import "fmt"
	type Person struct {
	}
	func (p *Person) AMethod() {
	    for i := 0; i <= 9; i++ {
	        for j := 0; j <= 7; j++ {
	            if i == 0 || i == 9 {
	                fmt.Print("*")
	                continue
	            }
	            if j == 0 || j == 7 {
	                fmt.Print("*")
	                continue
	            }
	            fmt.Print(" ")
	        }
	        fmt.Println()
	    }
	}
	func main() {
	    (&Person{}).AMethod()
	    var p = Person{}
	    p.AMethod()
	}
	```

2. 编写一个方法，提供m和n两个参数，方法中打印一个m*n的矩形
	```
    package main
	import "fmt"
	type Person struct {
	}
	func (p *Person) AMethod(m, n int) {
	    for i := 0; i < m; i++ {
	        for j := 0; j < n; j++ {
	            if i == 0 || i == m-1 {
	                fmt.Print("*")
	                continue
	            }
	            if j == 0 || j == n-1 {
	                fmt.Print("*")
	                continue
	            }
	            fmt.Print(" ")
	        }
	        fmt.Println()
	    }
	}
	func main() {
	    (&Person{}).AMethod(5, 3)
	    var p = Person{}
	    p.AMethod(3, 5)
	}
	```
	
