# 实例
QQ、迅雷、百度网盘客户端、、、  
新浪网站、京东商城、淘宝、、、

# 网络编程
	1. TCP socket 编程，是网络编程的主流。底层是基于TCP/IP协议的，比如QQ
	2. b/s 结构的http编程，使用的是http协议。底层也是用TCP socket实现的，比如京东商城

# TCP/IP 协议
	• Transmission Control Protocol/Internet Protocol 传输控制协议，互联网协议
	• Internet国际互联网络基础就是由网络层 IP协议和传输层的TCP协议组成的

# OSI 模型和TCP/IP模型
	• OSI模型（理论）  
	应用层 	application  
	表示层 	presention  
	会话层 	session  
	传输层 	transport  
	网络层 	ip  
	数据链路层 	link  
	物理层 	physical  
	
	• TCP/IP模型（现实）
	应用层	applicaton	smtp、ftp、telnet、http  
	传输层	transport	解释数据  
	网络层	ip	定位ip地址和确定连接路径  
	链路层	link	与硬件驱动对话  
	
# IP地址
	• internet上的主机和路由器都有IP地址
	• IP地址包含网络号和主机号
	• IPV4（32位）、IPV6（128位）

# 端口：
	• IP地址端口可以有256*256=65536个（0-65536）
	• 服务程序需要监听和占用一个端口
	• 0是保留端口
	• 1-1024是固定端口（一般被一些程序固定使用，22：SSH，23：telnet，21：ftp，25：smtp，80：iis，7：echo）
	• 1025-65535是动态端口，程序员可以使用
	• netstat -an 查看本机哪些端口在监听
	• netstat -anb 查看监听端口的pid
	• netstat -ano 查看所有端口的进程
	• taskkill -pid nnnn -f 强制关闭进程

# TCP socket编程的客户端和服务器端
	• 服务器端 --tcp链接--> 多个客户端
	• 服务端的处理流程
		1. 监听端口 nnnn
		2. 接收客户端的tcp链接，建立客户端和服务器端的链接
		3. 创建groutine，处理该链接的请求（通常客户端会通过链接发送请求包）
	• 客户端的处理流程
		1. 建立与服务端的链接
		2. 发送请求数据，接收服务器端返回的结果数据
		3. 关闭链接
	```
    server.go
	package main
	import "fmt"
	import "net"
	func process(conn net.Conn) {
	    defer conn.Close()
	    for {
	        buf := make([]byte, 1024)
	        fmt.Printf("服务器在等待客户端%s发送消息\n", conn.RemoteAddr().String())
	        n, err := conn.Read(buf)
	        if err != nil {
	            fmt.Println("从客户端退出 err=", err)
	            return
	        }
	        fmt.Println(string(buf[:n]))
	    }
	}
	func main() {
	    fmt.Println("服务器开始监听...")
	    listen, err := net.Listen("tcp", "0.0.0.0:8888")
	    if err != nil {
	        fmt.Println("listen err=", err)
	        return
	    }
	    defer listen.Close()
	    for {
	        fmt.Println("等待客户端来链接...")
	        conn, err := listen.Accept()
	        if err != nil {
	            fmt.Println("Accept() err=", err)
	            continue
	        }
	        fmt.Printf("Accept() success conn=%v 客户端ip=%v\n", conn, conn.RemoteAddr().String())
	        go process(conn)
	    }
	}
	
	client.go
	package main
	import (
	    "bufio"
	    "fmt"
	    "net"
	    "os"
	    "strings"
	)
	func main() {
	    conn, err := net.Dial("tcp", "192.168.31.147:8888")
	    if err != nil {
	        fmt.Println("client dial err=", err)
	        return
	    }
	    defer conn.Close()
	    reader := bufio.NewReader(os.Stdin)
	    for {
	        line, err := reader.ReadString('\n')
	        if err != nil {
	            fmt.Println("readString err=", err)
	        }
	        line = strings.Trim(line, "\r\n")
	        if line == "exit" {
	            fmt.Println("客户端退出...")
	            break
	        }
	        _, err = conn.Write([]byte(line))
	        if err != nil {
	            fmt.Println("conn write err=", err)
	        }
	    }
	}
	```
	
# 海量用户即时通信系统项目
需求分析-->设计阶段-->编码实现-->测试阶段-->实施  

	1. 需求分析
		1. 用户注册
		2. 用户登录
		3. 显示在线用户列表
		4. 群聊（广播）
		5. 点对点聊天
		6. 离线留言

	2. 界面设计
	----------------欢迎登陆多人聊天系统：----------------  
				1 登录聊天系统  
				2 注册用户  
				3 退出系统  
				  
				请选择（1-3）：  
	---------------------------------------------------------  
	1  
	登录…  
	请输入用户id：  
	100  
	请输入用户密码：  
	200  
	  
	你输入的 userid=10 pwd=200  

	3. 项目开发前技术准备
	server（进程，协程，端口） -->  数据库（mysql，oracle，redis，mongodb，memcache）  
		|  
		|  
		V  
	clientA  clientB  clientN      
	
	4. 实现显示客户端登录菜单
    ```
	----------------欢迎登陆多人聊天系统：----------------
				1 登录聊天系统
				2 注册用户
				3 退出系统
				
				请选择（1-3）：
	---------------------------------------------------------
	1
	登录…
	请输入用户id：
	100
	请输入用户密码：
	200
	
	你输入的 userid=10 pwd=200
    ```

	5. 实现用户登录
	完成用户验证，用户id=100，pwd=123456可以登录，其他用户不能登录  
		1. 完成客户端可以发送消息长度，服务器端可以正常收到该长度值
		2. 完成客户端可以发送消息本身，服务器端可以正常接受到消息，并根据客户端发送的消息（LoginMes）判断用户的合法性，并返回相应的LoginResMes
		3. 发送流程
			i. 先创建一个Message 的结构体
			ii. Mes.Type=登录消息类型
			iii. Mes.Data=登录消息的内容（序列化）
			iv. 对mes进行序列化
			v. 在网络传输中，防止丢包
				1) 先给服务器发送mes的长度有多少个字节n
				2) 再发送消息本身
		4. 接收流程
			i. 接收到客户端发送的长度len
			ii. 根据接收到的长度len，再接收消息本身
			iii. 接收时要判断实际接收到的消息内容是否等于len
			iv. 如不相等，需要纠错
			v. 取到反序列化的mes
			vi. 取出mes.Data 中的LoginMes
			vii. 根据比较结果返回Mess
			viii. 发送给客户端
		5. 重构
		Client/
			main/
			model/
			process/
			utils/
				
	6. 实现注册用户
	7. 实现登录时能返回当前的在线用户
	8. 实现登录后可以群聊
	9. 扩展功能
		1. 实现私聊（点对点聊天）
		2. 如果一个登录用户离线，就把这个人从在先列表去掉
		3. 实现离线留言，在群聊时，如果某个用户没有在线，当登录后，可以接收离线的消息
		4. 发送一个文件
