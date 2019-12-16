# v抽象
把一类事物的共同点提取出来形成一个模型（结构体）

# 封装
公有方法调用私有方法，操作私有变量
既隐藏了实现细节
能进行数据验证，保证了安全合理
```
package models
type person struct {
    Name string
    age  int
}
func NewPerson(name string, age int) *person {
    if age > 0 {
        return &person{
            Name: name,
            age:  age,
        }
    }
    return &person{
        Name: name,
    }
}
```
```
package main
import "github.com/mdjdot/gocoreprograming/models"
import "fmt"
func main() {
    p := models.NewPerson("zhangsan", -5)
    fmt.Println(p)
}
```

# 继承
	• 在Golang中一个struct嵌套了其他的匿名结构体，那么这个结构体可以直接访问匿名结构体的字段和方法，从而实现了继承
	• 结构中的相同字段和方法调用采用就近原则，建议使用structA.structB.Item
	• 结构体嵌套有名结构体是组合
	• 为了代码简洁，不建议使用多重继承

```
package main
import "github.com/mdjdot/gocoreprograming/models"
import "fmt"
type Person struct {
    Name string
    age  int
}
func (p *Person) Say() {
    fmt.Println("Person")
}
type Student struct {
    Person
}
func main() {
    p := models.NewPerson("zhangsan", -5)
    fmt.Println(p)
    s := Student{}
    s.Say()
}
```

# 多态
- Golang多态是通过接口实现的
- 接口可以定义一组方法，但不需要实现。且接口中不包含任何变量。
- 接口高内聚低耦合
- 接口不能创建实例，但可以指向一个实现接口的类型的实例
- 所有类型都实现了空接口interface{}

# 接口断言
如果不知道接口的具体对象，可以使用接口断言，转换为需要的类型的变量。
```
package main
import "fmt"
type P interface {
    Say()
}
type Person struct {
}
func (p *Person) Say() {
    fmt.Println("Person")
}
type Student struct {
}
func (s Student) Say() {
    fmt.Println("Student")
}
func CheckType(p P) {
    p.Say()
}
func main() {
    var t P
    t = &Person{}
    t.Say() // Person
    // b, err := t.(Person)
    if v, ok := t.(*Person); ok { // 接口断言
        fmt.Println(v)  // &{}
        fmt.Println(*v) // {}
    }
    t = Student{}
    t.Say() // Student
    a := t.(Student)
    fmt.Println(a)
    // b := t.(*Person) // 建议使用 v,ok接收接口断言，不然断言错误会panic
    // fmt.Println(b)
    switch c := t.(type) { // <main.Student>
    case *Person:
        fmt.Printf("Tpe *Person:%T\n", c)
    case Student:
        fmt.Printf("Type Student:%T\n", c) // Type Student:main.Student
    default:
        fmt.Printf("Type Unknow\n")
    }
}
```

# 小练习
	1. 编写一个函数，传入多个参数，判断相应类型并输出
	```
    package main
	import "fmt"
	func InputJudge(inputs ...interface{}) {
	    for _, input := range inputs {
	        switch input.(type) {
	        case int, string:
	            fmt.Printf("%T\n", input)
	        default:
	            fmt.Println("Unknow type")
	        }
	    }
	}
	func main() {
	    InputJudge(1, int(2), "kkdd", 'c')
	    /*
	        int
	        int
	        string
	        Unknow type
	    */
	    InputJudge([]interface{}{1, int(2), "kkdd", 'c'})
	    /*
	        Unknow type
	    */
	    InputJudge([]interface{}{1, int(2), "kkdd", 'c'}...)
	    /*
	        int
	        int
	        string
	        Unknow type
	    */
	}
	```
	



