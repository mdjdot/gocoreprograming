# 单元测试框架和工具
testing测试框架，go test工具

# 单元测试解决问题
	1. 确保每个函数是可运行，并且运行结果是正确的
	2. 确保代码的性能是好的
	3. 及时发现程序设计或实现的逻辑错误

# 单元测试总结
	1. 测试文件名必须以 _test.go 结尾
	2. 测试函数必须以 Test开头
	3. t.Fatalf 格式化输出错误信息，并退出程序
	t.Logf格式化输出日志，继续执行测试
	4. go test -v 输出更多信息
	
	go test -timeout 300s git.windimg.com\giantthong\accounting\datasource -coverprofile=coverage  
	go tool cover -html=coverage -o coverage.html


# 小练习
	1. 单元测试案例
		1. 编写一个Monster结构体，字段Name，Age，Skill
		2. 给Monster绑定方法Store，可以将一个Monster变量序列化后保存到文件中
		3. 给Monster绑定方法ReStore，可以将一个序列化的Monster，从文件中读取，并反序列化为Monster对象，编写测试用例函数TestStore和TestRestore进行测试
	
	./test.go
    ```
	package main
	import (
	    "bufio"
	    "encoding/json"
	    "io/ioutil"
	    "os"
	)
	type Monster struct {
	    Name  string `json:"name,omitempty"`
	    Age   int    `json:"age,omitempty"`
	    Skill string `json:"skill,omitempty"`
	}
	func (m *Monster) Store() error {
	    file, err := os.OpenFile("./monster.dat", os.O_CREATE|os.O_WRONLY, 0666)
	    if err != nil {
	        return err
	    }
	    defer file.Close()
	    data, err := json.Marshal(*m)
	    if err != nil {
	        return err
	    }
	    writer := bufio.NewWriter(file)
	    _, err = writer.Write(data)
	    if err != nil {
	        return err
	    }
	    writer.Flush()
	    return nil
	}
	func (m *Monster) Restore(fileName string) error {
	    data, err := ioutil.ReadFile("./monster.dat")
	    if err != nil {
	        return err
	    }
	    err = json.Unmarshal(data, m)
	    if err != nil {
	        return err
	    }
	    return nil
	}
	```

	./test_test.go
    ```
	package main
	import (
	    "io/ioutil"
	    "testing"
	    "github.com/stretchr/testify/assert"
	)
	func TestStore(t *testing.T) {
	    m := &Monster{
	        Name:  "zhangsan",
	        Age:   15,
	        Skill: "chichihehe",
	    }
	    err := m.Store()
	    assert.NoError(t, err)
	    t.Logf("%+v\n", *m)
	    content, err := ioutil.ReadFile("./monster.dat")
	    assert.NoError(t, err)
	    t.Log(string(content))
	}
	func TestMonster_Restore(t *testing.T) {
	    m := &Monster{}
	    err := m.Restore("./monster.dat")
	    assert.NoError(t, err)
	    t.Logf("%+v", *m)
	}
    ```
	

