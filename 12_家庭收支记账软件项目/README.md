# 项目开发流程
需求分析 -> 设计 -> 实现 <-> 测试 -> 部署 ->维护

# 项目需求说明
1. 模拟实现基于文本界面的家庭记账软件
2. 该软件能够记录家庭的收入、支出，并能够打印收支明细表

# 项目的界面
```
----------------家庭收支记账软件----------------
                         1 收支明细
			2 登记收入
			3 登记支出
			4 退        出 
			
			请选择（1-4）：1

----------------当前收支明细记录----------------
收支        账户金额        收支金额        说明
```
```
----------------家庭收支记账软件----------------
                         1 收支明细
			2 登记收入
			3 登记支出
			4 退        出 
			
			请选择（1-4）：2

本次收入金额：1000
本次收入说明：有人发红包
```
```
----------------家庭收支记账软件----------------
                         1 收支明细
			2 登记收入
			3 登记支出
			4 退        出 
			
			请选择（1-4）：1

----------------当前收支明细记录----------------
收支        账户金额        收支金额        说明
收入        11000            1000              有人发红包
```

# 代码实现
1. 实现显示主菜单，并且可以退出
	```
    package main
	import (
	    "fmt"
	    "os"
	)
	var (
	    // 字符串处理使用空格而不是tab
	    home = `----------------家庭收支记账软件----------------
	                1 收支明细
	                2 登记收入
	                3 登记支出
	                4 退    出
	                
	                请选择（1-4）：`
	    op int
	)
	func main() {
	    for {
	        fmt.Print(home)
	        fmt.Scanln(&op)
	        switch op {
	        case 4:
	            os.Exit(0)
	        }
	    }
	}
	```

2. 完成可以显示明细和登记收支的功能
    ```
    package main
    import (
        "fmt"
        "os"
    )
    var (
        // 字符串处理使用空格而不是tab
        homeStr = `
    ----------------家庭收支记账软件----------------
                    1 收支明细
                    2 登记收入
                    3 登记支出
                    4 退    出
                    
                    请选择（1-4）：`
        detailsStr = "----------------当前收支明细记录----------------\n收支\t账户金额\t收支金额\t说明"
        op         int
        details    []Detail
        balance    int64
    )
    type Detail struct {
        Type        string
        Balance     int64
        Amount      int64
        Description string
    }
    func DispalyDetails() {
        fmt.Println(detailsStr)
        for _, detail := range details {
            // fmt.Printf("%-10s%-12d%-12d%-s", detail.Type, detail.Balance, detail.Amount, detail.Description)
            fmt.Printf("%s\t%d\t\t%d\t\t%s\n", detail.Type, detail.Balance, detail.Amount, detail.Description)
        }
    }
    func LogDetail(logType string) {
        var amount int64
        var description string
        var flag int64
        if logType == "收入" {
            flag = 1
            fmt.Print("本次收入金额：")
            fmt.Scanln(&amount)
            fmt.Print("本次收入说明：")
            fmt.Scanln(&description)
        } else {
            flag = -1
            fmt.Print("本次支出金额：")
            fmt.Scanln(&amount)
            fmt.Print("本次支出说明：")
            fmt.Scanln(&description)
        }
        balance = balance + (amount * flag)
        details = append(details, Detail{
            Type:        logType,
            Balance:     balance,
            Amount:      amount,
            Description: description,
        })
        return
    }
    func main() {
        for {
            fmt.Print(homeStr)
            fmt.Scanln(&op)
            switch op {
            case 1:
                DispalyDetails()
            case 2:
                LogDetail("收入")
            case 3:
                LogDetail("支出")
            case 4:
                os.Exit(0)
            }
        }
    }
    ```

3. 扩展一个登录功能，只有输入正确的用户名和密码才能操作
    ```
    package main
    import (
        "fmt"
        "os"
    )
    var (
        // 字符串处理使用空格而不是tab
        homeStr = `
    ----------------家庭收支记账软件----------------
                    1 收支明细
                    2 登记收入
                    3 登记支出
                    4 退    出
                    
                    请选择（1-4）：`
        detailsStr = "----------------当前收支明细记录----------------\n收支\t账户金额\t收支金额\t说明"
        op         int
        details    []Detail
        balance    int64
        accounts = map[string]string{
            "admin": "pwd",
            "lisi":  "zdd",
        }
        user, password string
    )
    type Detail struct {
        Type        string
        Balance     int64
        Amount      int64
        Description string
    }
    func DispalyDetails() {
        fmt.Println(detailsStr)
        for _, detail := range details {
            // fmt.Printf("%-10s%-12d%-12d%-s", detail.Type, detail.Balance, detail.Amount, detail.Description)
            fmt.Printf("%s\t%d\t\t%d\t\t%s\n", detail.Type, detail.Balance, detail.Amount, detail.Description)
        }
    }
    func LogDetail(logType string) {
        var amount int64
        var description string
        var flag int64
        if logType == "收入" {
            flag = 1
            fmt.Print("本次收入金额：")
            fmt.Scanln(&amount)
            fmt.Print("本次收入说明：")
            fmt.Scanln(&description)
        } else {
            flag = -1
            fmt.Print("本次支出金额：")
            fmt.Scanln(&amount)
            fmt.Print("本次支出说明：")
            fmt.Scanln(&description)
        }
        balance = balance + (amount * flag)
        details = append(details, Detail{
            Type:        logType,
            Balance:     balance,
            Amount:      amount,
            Description: description,
        })
        return
    }
    func main() {
        for {
            fmt.Print("请输入用户名：")
            fmt.Scanln(&user)
            fmt.Print("请输入密码：")
            fmt.Scanln(&password)
            if pwd, ok := accounts[user]; !ok || pwd != password {
                fmt.Println("用户名或密码错误，请重新输入。")
                continue
            }
            fmt.Print(homeStr)
            fmt.Scanln(&op)
            switch op {
            case 1:
                DispalyDetails()
            case 2:
                LogDetail("收入")
            case 3:
                LogDetail("支出")
            case 4:
                os.Exit(0)
            }
        }
    }
    ```