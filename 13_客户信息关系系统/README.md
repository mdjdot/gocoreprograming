# 项目需求分析
1. 模拟实现基于文本界面的客户信息管理软件
2. 该软件能够实现对客户对象的插入、修改和删除，并能够打印客户明细表

# 项目的界面设计
主菜单界面  
```
----------------客户信息管理系统----------------
			1 添加客户
			2 修改客户
			3 删除客户
			4 客户列表
			5 退       出
			
			请选择（1-5）：
```

添加客户界面  
```
----------------客户信息管理系统----------------
			1 添加客户
			2 修改客户
			3 删除客户
			4 客户列表
			5 退       出
			
			请选择（1-5）：1
			
----------------添加客户----------------
姓名：张三
性别：男
年龄：30
电话：010-56253825
邮箱：zhang@abc.com
----------------添加完成----------------
```

修改客户界面  
```
----------------客户信息管理系统----------------
			1 添加客户
			2 修改客户
			3 删除客户
			4 客户列表
			5 退       出
			
			请选择（1-5）：2
			
----------------修改客户----------------
请选择待修改客户编号（-1退出）：1
姓名（张三）：<直接回车表示不修改>
性别（男）：
年龄（30）：
电话（010-56253825）：
邮箱（zhang@abc.com）：zsan@abc.com
----------------修改完成----------------
```

删除客户界面  
```
----------------客户信息管理系统----------------
			1 添加客户
			2 修改客户
			3 删除客户
			4 客户列表
			5 退       出
			
			请选择（1-5）：3
			
----------------修改客户----------------
请选择待删除客户编号（-1退出）：1
确认是否删除（Y/N）：y
----------------删除完成----------------
```

客户列表界面  
```
----------------客户信息管理系统----------------
			1 添加客户
			2 修改客户
			3 删除客户
			4 客户列表
			5 退       出
			
			请选择（1-5）：4
			
----------------客户列表----------------
编号	姓名	性别	年龄	电话	邮箱
1	张三	男	30	010-56253825	abc@mail.com
2	李四	女	23	010-56253825	lisi@mail.com
3	王芳	女	26	010-56253825	wangfang@mail.com
----------------客户列表完成----------------
```

# 代码实现
1. 程序架构图  
customerview <-> customerService -> customer
2. 实现显示主菜单和完成退出软件功能  
	```
    views/customerView.go
	package main
	import (
	    "fmt"
	    "os"
	)
	var (
	    homeStr = `----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op int
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 5:
	            os.Exit(0)
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
	```

3. 完成显示客户列表的功能
	```
    models/customer.go
	package models
	type Customer struct {
	    Code   int
	    Name   string
	    Gender string
	    Age    int
	    Phone  string
	    Email  string
	}
	
	services/customerService.go
	package services
	import (
	    "fmt"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	)
	func DisplayCustomers(customers []models.Customer) {
	    fmt.Println()
	    fmt.Println("--------------------客户列表--------------------")
	    fmt.Println("编号\t姓名\t性别\t年龄\t电话\t\t邮编")
	    for _, customer := range customers {
	        fmt.Printf("%d\t%s\t%s\t%d\t%s\t\t%s\n", customer.Code, customer.Name, customer.Gender, customer.Age, customer.Phone, customer.Email)
	    }
	    fmt.Println("------------------客户列表完成------------------")
	}
	
	views/customerView.go
	package main
	import (
	    "fmt"
	    "os"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "github.com/mdjdot/gocoreprograming/sim/services"
	)
	var (
	    homeStr = `
	----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op        int
	    customers []models.Customer
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 4:
	            services.DisplayCustomers(customers)
	        case 5:
	            os.Exit(0)
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
	```

4. 完成添加客户的功能
	```
    services/customerService.go
	package services
	import (
	    "fmt"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	)
	func DisplayCustomers(customers *[]models.Customer) {
	    fmt.Println()
	    fmt.Println("--------------------客户列表--------------------")
	    fmt.Println("编号\t姓名\t性别\t年龄\t电话\t\t邮编")
	    for _, customer := range *customers {
	        fmt.Printf("%d\t%s\t%s\t%d\t%s\t%s\n", customer.Code, customer.Name, customer.Gender, customer.Age, customer.Phone, customer.Email)
	    }
	    fmt.Println("------------------客户列表完成------------------")
	}
	func AddCustomer(customers *[]models.Customer, code *int) {
	    var customer models.Customer
	    fmt.Println()
	    fmt.Println("--------------------添加客户--------------------")
	    fmt.Print("姓名：")
	    fmt.Scanln(&customer.Name)
	    fmt.Print("性别：")
	    fmt.Scanln(&customer.Gender)
	    fmt.Print("年龄：")
	    fmt.Scanln(&customer.Age)
	    fmt.Print("电话：")
	    fmt.Scanln(&customer.Phone)
	    fmt.Print("邮箱：")
	    fmt.Scanln(&customer.Email)
	    *code++
	    customer.Code = *code
	    *customers = append(*customers, customer)
	    fmt.Println("--------------------添加完成--------------------")
	}
	
	views/customerView.go
	package main
	import (
	    "fmt"
	    "os"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "github.com/mdjdot/gocoreprograming/sim/services"
	)
	var (
	    homeStr = `
	----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op        int
	    customers []models.Customer
	    code      int
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 1:
	            services.AddCustomer(&customers, &code)
	        case 4:
	            services.DisplayCustomers(&customers)
	        case 5:
	            os.Exit(0)
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
	```

5. 完成修改客户的功能
	```
    services/customerService.go
	package services
	import (
	    "errors"
	    "fmt"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	)
	func DisplayCustomers(customers *[]models.Customer) {
	    fmt.Println()
	    fmt.Println("--------------------客户列表--------------------")
	    fmt.Println("编号\t姓名\t性别\t年龄\t电话\t\t邮编")
	    for _, customer := range *customers {
	        fmt.Printf("%d\t%s\t%s\t%d\t%s\t%s\n", customer.Code, customer.Name, customer.Gender, customer.Age, customer.Phone, customer.Email)
	    }
	    fmt.Println("------------------客户列表完成------------------")
	}
	func AddCustomer(customers *[]models.Customer, code *int) {
	    var customer models.Customer
	    fmt.Println()
	    fmt.Println("--------------------添加客户--------------------")
	    fmt.Print("姓名：")
	    fmt.Scanln(&customer.Name)
	    fmt.Print("性别：")
	    fmt.Scanln(&customer.Gender)
	    fmt.Print("年龄：")
	    fmt.Scanln(&customer.Age)
	    fmt.Print("电话：")
	    fmt.Scanln(&customer.Phone)
	    fmt.Print("邮箱：")
	    fmt.Scanln(&customer.Email)
	    *code++
	    customer.Code = *code
	    *customers = append(*customers, customer)
	    fmt.Println("--------------------添加完成--------------------")
	}
	func queryCustomer(customers *[]models.Customer, code int) (*models.Customer, error) {
	    for index, customer := range *customers {
	        if customer.Code == code {
	            return &(*customers)[index], nil
	        }
	    }
	    return nil, errors.New("未找到客户")
	}
	func UpdateCustomer(customers *[]models.Customer) {
	    // var newCustomer models.Customer
	    var code int
	    fmt.Println()
	    fmt.Println("--------------------修改客户--------------------")
	    fmt.Print("请选择待修改客户编号（-1退出）：")
	    fmt.Scanln(&code)
	    if code == 0 || code == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    customer, err := queryCustomer(customers, code)
	    if err != nil {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    fmt.Printf("姓名（%s）:", customer.Name)
	    fmt.Scanln(&(customer.Name))
	    fmt.Printf("性别（%s）:", customer.Gender)
	    fmt.Scanln(&(customer.Gender))
	    fmt.Printf("年龄（%d）:", customer.Age)
	    fmt.Scanln(&(customer.Age))
	    fmt.Printf("电话（%s）:", customer.Phone)
	    fmt.Scanln(&(customer.Phone))
	    fmt.Printf("邮编（%s）:", customer.Email)
	    fmt.Scanln(&(customer.Email))
	    fmt.Println("--------------------修改完成--------------------")
	}
	
	views/customerView.go
	package main
	import (
	    "fmt"
	    "os"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "github.com/mdjdot/gocoreprograming/sim/services"
	)
	var (
	    homeStr = `
	----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op        int
	    customers []models.Customer
	    code      int
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 1:
	            services.AddCustomer(&customers, &code)
	        case 2:
	            services.UpdateCustomer(&customers)
	        case 4:
	            services.DisplayCustomers(&customers)
	        case 5:
	            os.Exit(0)
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
	```

6. 完成删除客户的功能
	```
    services/customerService.go
	package services
	import (
	    "fmt"
	    "strings"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	)
	func DisplayCustomers(customers *[]models.Customer) {
	    fmt.Println()
	    fmt.Println("--------------------客户列表--------------------")
	    fmt.Println("编号\t姓名\t性别\t年龄\t电话\t\t邮编")
	    for _, customer := range *customers {
	        fmt.Printf("%d\t%s\t%s\t%d\t%s\t%s\n", customer.Code, customer.Name, customer.Gender, customer.Age, customer.Phone, customer.Email)
	    }
	    fmt.Println("------------------客户列表完成------------------")
	}
	func AddCustomer(customers *[]models.Customer, code *int) {
	    var customer models.Customer
	    fmt.Println()
	    fmt.Println("--------------------添加客户--------------------")
	    fmt.Print("姓名：")
	    fmt.Scanln(&customer.Name)
	    fmt.Print("性别：")
	    fmt.Scanln(&customer.Gender)
	    fmt.Print("年龄：")
	    fmt.Scanln(&customer.Age)
	    fmt.Print("电话：")
	    fmt.Scanln(&customer.Phone)
	    fmt.Print("邮箱：")
	    fmt.Scanln(&customer.Email)
	    *code++
	    customer.Code = *code
	    *customers = append(*customers, customer)
	    fmt.Println("--------------------添加完成--------------------")
	}
	func queryCustomer(customers *[]models.Customer, code int) (*models.Customer, int) {
	    for index, customer := range *customers {
	        if customer.Code == code {
	            return &(*customers)[index], index
	        }
	    }
	    return nil, -1
	}
	func UpdateCustomer(customers *[]models.Customer) {
	    var code int
	    fmt.Println()
	    fmt.Println("--------------------修改客户--------------------")
	    fmt.Print("请选择待修改客户编号（-1退出）：")
	    fmt.Scanln(&code)
	    if code == 0 || code == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    customer, index := queryCustomer(customers, code)
	    if index == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    fmt.Printf("姓名（%s）:", customer.Name)
	    fmt.Scanln(&(customer.Name))
	    fmt.Printf("性别（%s）:", customer.Gender)
	    fmt.Scanln(&(customer.Gender))
	    fmt.Printf("年龄（%d）:", customer.Age)
	    fmt.Scanln(&(customer.Age))
	    fmt.Printf("电话（%s）:", customer.Phone)
	    fmt.Scanln(&(customer.Phone))
	    fmt.Printf("邮编（%s）:", customer.Email)
	    fmt.Scanln(&(customer.Email))
	    fmt.Println("--------------------修改完成--------------------")
	}
	func DeleteCustomer(customers *[]models.Customer) {
	    var code int
	    var delete string
	    fmt.Println()
	    fmt.Println("--------------------删除客户--------------------")
	    fmt.Print("请选择待删除客户编号（-1退出）：")
	    fmt.Scanln(&code)
	    if code == 0 || code == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    _, index := queryCustomer(customers, code)
	    if index == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    for {
	        fmt.Print("确认是否删除（Y/N）：")
	        fmt.Scanln(&delete)
	        if strings.ToLower(delete) == "n" {
	            fmt.Println("确认不删除，请重新选择")
	            return
	        }
	        if strings.ToLower(delete) == "y" {
	            *customers = append((*customers)[:index], (*customers)[index+1:]...)
	            fmt.Println("--------------------删除完成--------------------")
	            return
	        }
	    }
	}
	
	views/customerView.go
	package main
	import (
	    "fmt"
	    "os"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "github.com/mdjdot/gocoreprograming/sim/services"
	)
	var (
	    homeStr = `
	----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op        int
	    customers []models.Customer
	    code      int
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 1:
	            services.AddCustomer(&customers, &code)
	        case 2:
	            services.UpdateCustomer(&customers)
	        case 3:
	            services.DeleteCustomer(&customers)
	        case 4:
	            services.DisplayCustomers(&customers)
	        case 5:
	            os.Exit(0)
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
	```

7. 完善退出确认功能
	```
    services/customerService.go
	package services
	import (
	    "fmt"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "os"
	    "strings"
	)
	func DisplayCustomers(customers *[]models.Customer) {
	    fmt.Println()
	    fmt.Println("--------------------客户列表--------------------")
	    fmt.Println("编号\t姓名\t性别\t年龄\t电话\t\t邮编")
	    for _, customer := range *customers {
	        fmt.Printf("%d\t%s\t%s\t%d\t%s\t%s\n", customer.Code, customer.Name, customer.Gender, customer.Age, customer.Phone, customer.Email)
	    }
	    fmt.Println("------------------客户列表完成------------------")
	}
	func AddCustomer(customers *[]models.Customer, code *int) {
	    var customer models.Customer
	    fmt.Println()
	    fmt.Println("--------------------添加客户--------------------")
	    fmt.Print("姓名：")
	    fmt.Scanln(&customer.Name)
	    fmt.Print("性别：")
	    fmt.Scanln(&customer.Gender)
	    fmt.Print("年龄：")
	    fmt.Scanln(&customer.Age)
	    fmt.Print("电话：")
	    fmt.Scanln(&customer.Phone)
	    fmt.Print("邮箱：")
	    fmt.Scanln(&customer.Email)
	    *code++
	    customer.Code = *code
	    *customers = append(*customers, customer)
	    fmt.Println("--------------------添加完成--------------------")
	}
	func queryCustomer(customers *[]models.Customer, code int) (*models.Customer, int) {
	    for index, customer := range *customers {
	        if customer.Code == code {
	            return &(*customers)[index], index
	        }
	    }
	    return nil, -1
	}
	func UpdateCustomer(customers *[]models.Customer) {
	    var code int
	    fmt.Println()
	    fmt.Println("--------------------修改客户--------------------")
	    fmt.Print("请选择待修改客户编号（-1退出）：")
	    fmt.Scanln(&code)
	    if code == 0 || code == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    customer, index := queryCustomer(customers, code)
	    if index == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    fmt.Printf("姓名（%s）:", customer.Name)
	    fmt.Scanln(&(customer.Name))
	    fmt.Printf("性别（%s）:", customer.Gender)
	    fmt.Scanln(&(customer.Gender))
	    fmt.Printf("年龄（%d）:", customer.Age)
	    fmt.Scanln(&(customer.Age))
	    fmt.Printf("电话（%s）:", customer.Phone)
	    fmt.Scanln(&(customer.Phone))
	    fmt.Printf("邮编（%s）:", customer.Email)
	    fmt.Scanln(&(customer.Email))
	    fmt.Println("--------------------修改完成--------------------")
	}
	func DeleteCustomer(customers *[]models.Customer) {
	    var code int
	    var delete string
	    fmt.Println()
	    fmt.Println("--------------------删除客户--------------------")
	    fmt.Print("请选择待删除客户编号（-1退出）：")
	    fmt.Scanln(&code)
	    if code == 0 || code == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    _, index := queryCustomer(customers, code)
	    if index == -1 {
	        fmt.Println("待修改客户不存在，请重新选择")
	        return
	    }
	    for {
	        fmt.Print("确认是否删除（Y/N）：")
	        fmt.Scanln(&delete)
	        if strings.ToLower(delete) == "n" {
	            fmt.Println("确认不删除，请重新选择")
	            return
	        }
	        if strings.ToLower(delete) == "y" {
	            *customers = append((*customers)[:index], (*customers)[index+1:]...)
	            fmt.Println("--------------------删除完成--------------------")
	            return
	        }
	    }
	}
	func Quit() {
	    var quit string
	    for {
	        fmt.Print("确认是否退出（Y/N）：")
	        fmt.Scanln(&quit)
	        if strings.ToLower(quit) == "n" {
	            fmt.Println("确认不退出，请重新选择")
	            return
	        }
	        if strings.ToLower(quit) == "y" {
	            os.Exit(0)
	        }
	    }
	}
	
	views/customerView.go
	package main
	import (
	    "fmt"
	    "github.com/mdjdot/gocoreprograming/sim/models"
	    "github.com/mdjdot/gocoreprograming/sim/services"
	)
	var (
	    homeStr = `
	----------------客户信息管理软件----------------
	    
	                1 添加客户
	                2 修改客户
	                3 删除客户
	                4 客户列表
	                5 退    出
	    
	                请选择（1-5）：`
	    op        int
	    customers []models.Customer
	    code      int
	)
	func main() {
	    for {
	        fmt.Print(homeStr)
	        _, err := fmt.Scanln(&op)
	        if err != nil {
	            fmt.Println()
	        }
	        switch op {
	        case 1:
	            services.AddCustomer(&customers, &code)
	        case 2:
	            services.UpdateCustomer(&customers)
	        case 3:
	            services.DeleteCustomer(&customers)
	        case 4:
	            services.DisplayCustomers(&customers)
	        case 5:
	            services.Quit()
	        default:
	            fmt.Println("错误的选择，请重新输入选择")
	        }
	    }
	}
    ```