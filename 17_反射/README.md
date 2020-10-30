# 反射常用方法
	• reflect.TypeOf(v) // 获取类型
	• reflect.ValueOf(v) // 获取值
	• reflect.ValueOf(v).Kind() // 获取类型，可能和TypeOf不一样
	• reflect.ValueOf(v).Int() // 获取值转换为需要的类型，reflect.Value和其他类型的转换，必须是一样的类型
	• reflect.ValueOf(&v).Elem().SetInt(v2) //设置变量的值，必须使用地址
```
package main
import "fmt"
import "reflect"
func main() {
    intChan := make(chan int, 1)
    fmt.Println(reflect.TypeOf(intChan))         // chan int
    fmt.Println(reflect.ValueOf(intChan))        // 0xc000096000
    fmt.Println(reflect.ValueOf(intChan).Kind()) // chan
    num := 5
    fmt.Println(reflect.TypeOf(num))         // int
    fmt.Println(reflect.ValueOf(num))        // 5
    fmt.Println(reflect.ValueOf(num).Kind()) // int
    fmt.Println(reflect.ValueOf(num).Int())  // 5
    newNum := reflect.ValueOf(&num)          // 使用Elem，之前的ValueOf需要取地址
    newNum.Elem().SetInt(4)
    fmt.Println(num) // 4
}
```

# 小练习
1. 给出一个变量 var v float64 = 1.2，请使用反射类的到它的 reflect Value，然后获取对应的Type，Kind和值，并将reflect.Value转换成interface{}，再将interface{}转换成float64
	```
    package main
	import (
	    "fmt"
	    "reflect"
	)
	func main() {
	    var v = 1.2
	    vv := reflect.ValueOf(v)
	    fmt.Printf("%+v\n", vv) // 1.2
	    tt := vv.Type()
	    fmt.Printf("%+v\n", tt) // float64
	    kk := vv.Kind()
	    fmt.Printf("%+v\n", kk) // float64
	    ii := vv.Interface()
	    fmt.Printf("%+v %T\n", ii, ii) // 1.2 float64
	    if vf, ok := ii.(float64); ok {
	        fmt.Printf("%+v\n", vf)
	    }
	    switch t := ii.(type) {
	    case float64:
	        fmt.Printf("%+v", t) // 1.2
	    default:
	        fmt.Println("类型不正确")
	    }
	}
	```

2. 使用反射来遍历结构体的字段，调用结构体的方法，并获取结构体标签的值
	```
    package main
	import "fmt"
	import "reflect"
	type Monster struct {
	    Name  string  `json:"monster_name"`
	    Age   int     `json:"monster_age"`
	    Score float32 `json:"战斗力"`
	    Sex   string
	}
	func (m *Monster) GetSum(n1, n2 float64) float64 {
	    return n1 + n2
	}
	func (m *Monster) Set(name string, age int, score float32, sex string) {
	    m.Name = name
	    m.Age = age
	    m.Score = score
	    m.Sex = sex
	}
	func (m *Monster) Print() {
	    fmt.Println("--------start--------")
	    fmt.Println(*m)
	    fmt.Println("---------end---------")
	}
	func main() {
	    m := &Monster{
	        Name:  "黄鼠狼精",
	        Age:   500,
	        Score: 76.4,
	        Sex:   "公",
	    }
	    m.Print()
	    /*
	        --------start--------
	        {黄鼠狼精 500 76.4 公}
	        ---------end---------
	    */
	    typ := reflect.TypeOf(m)
	    fmt.Printf("%+v %T\n", typ, typ) // *main.Monster *reflect.rtype
	    typ1 := reflect.TypeOf(*m)
	    fmt.Printf("%+v %T\n", typ1, typ1) // main.Monster *reflect.rtype
	    kd := reflect.ValueOf(*m).Kind()
	    fmt.Printf("%+v %T\n", kd, kd) // struct reflect.Kind
	    fnum := reflect.ValueOf(*m).NumField()
	    fmt.Printf("%+v\n", fnum) // 字段 4
	    for i := 0; i < fnum; i++ {
	        fmt.Println(reflect.ValueOf(*m).Field(i))
	        /*
	            黄鼠狼精
	            500
	            76.4
	            公
	        */
	    }
	    for i := 0; i < fnum; i++ {
	        fmt.Println(reflect.TypeOf(*m).Field(i).Name)
	        /*
	            Name
	            Age
	            Score
	            Sex
	        */
	    }
	    for i := 0; i < fnum; i++ {
	        fmt.Println(reflect.TypeOf(*m).Field(i).Tag.Get("json"))
	        /*
	            monster_name
	            monster_age
	            战斗力
	        */
	    }
	    mnum := reflect.ValueOf(*m).NumMethod()
	    fmt.Printf("%+v\n", mnum) // 普通方法 0
	    mpnum := reflect.ValueOf(m).NumMethod()
	    fmt.Printf("%+v\n", mpnum) // 指针方法 3
	    for i := 0; i < mpnum; i++ {
	        // 方法按照方法名排序
	        fmt.Println(reflect.TypeOf(m).Method(i).Name)
	        /*
	            GetSum
	            Print
	            Set
	        */
	    }
	    result := reflect.ValueOf(m).Method(0).Call([]reflect.Value{reflect.ValueOf(10.3), reflect.ValueOf(20.3)})
	    fmt.Println(result[0].Float()) // 30.6
	    reflect.ValueOf(m).Method(1).Call([]reflect.Value{})
	    /*
	        --------start--------
	        {黄鼠狼精 500 76.4 公}
	        ---------end---------
	    */
	}
	```

3. 反射练习
	a. 编写一个Cal结构体，有两个字段Num1和Num2  
	b. 方法GetSub(name string)  
	c. 使用反射遍历Cal结构体所有的字段信息  
	d. 使用反射机制完成对GetSub的调用，输出形式为“tom完成了剑法运算，8 - 3 = 5”  
	```
    package main
	import (
	    "fmt"
	    "reflect"
	)
	type Cal struct {
	    Num1 int
	    Num2 int
	}
	func (c Cal) GetSub(name string) {
	    fmt.Printf("%s 完成了减法运算，%d - %d = %d", name, c.Num1, c.Num2, c.Num1-c.Num2)
	}
	func main() {
	    c := Cal{
	        Num1: 8,
	        Num2: 3,
	    }
	    tpe := reflect.TypeOf(c)
	    val := reflect.ValueOf(c)
	    fnums := tpe.NumField()
	    for i := 0; i < fnums; i++ {
	        fmt.Println(tpe.Field(i).Name)
	    }
	    val.Method(0).Call([]reflect.Value{reflect.ValueOf("tom")})
	}
	```
	
