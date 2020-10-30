# 创始人
- Ken Thompson
- Rob Pike
- Robert Griesemer

# Google 创造 Golang 的原因
- 合理利用多核 CPU
- 简洁高效的编程语言
- 运行速度快的语言
- 编译速度快的语言
- 安全的语言

# Golang 的特点
1. 弱化的指针
2. 包的概念
3. 自动垃圾回收
4. 天然并发
5. 管道通信机制
6. 简洁的函数
7. 切片的使用
8. 延时执行 defer

# 小练习
1. 开发一个 hi.go 程序，可以输出“hello,world!”
	```
    package main
	
	import "fmt"
	
	func main(){
		fmt.Println("hello,world!")
	}
    ```
	
2. 使用一句输出语句，达到输出如下效果：  
姓名	年龄	籍贯	住址  
john	12	    河北	北京
	```
    package main
	
	import "fmt"
	
	func main(){
		fmt.Println("姓名\t年龄\t籍贯\t住址\njohn\t12\t河北\t北京")
	}
    ```
	
3. 切换到 E 盘下，新建一个目录 atguigu100 ，切换到该目录，新建 a、b、c 三个目录，然后切换到 a，新建文件 news.txt，然后复制文件到 b 下，最后都删除
	```
    E:
	md atguigu100
	cd atguigu100
	md a b c
	# md b
	# md c
	cd a
	echo >news.txt
	copy news.txt ..\b
	cd ..\..
    rd /s atguigu100
	```