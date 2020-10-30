# Redis
	• redis是nosql数据库
	• Remote dictionary server性能非常高，单机能够达到15万 qps
	• redis适合做缓存，也可以做持久化

# Redis 命令
	• redis-cli // 启动redis客户端
	• redis-cli -h ip -p 6379 -a password --raw 
	• select 0 // redis默认有16个数据库，编号0~15
	• keys * // 查看所有key
	• set name zhangsan // 添加或修改key
	• setex name 10 zhangsan // 设置有过期时间的key
	• mset name zhangsan age 14 // 批量设置key value
	• hset user name zhangsan
	• hmset user gender man jon coder
	• expire name 10 // 10秒后过期
	• get name // 获取value
	• mget name age // 批量获取value
	• hget user name
	• hmget user name age
	• hgetall user // 获取user的字段和值
	• del name // 删除key
	• exists name user // key是否存在
	• hexists user age // key是否存在字段
	• hlen user // key有几个字段
	• dbsize // 查看key总数
	• flushdb // 清空当前数据库
	• flushall // 清空所有数据库

# Redis 数据类型
	• String // value最大是512M
		○ set  zhangsan zhangsanvalue
		○ get zhangsan
		○ mset  lisi lisivalue wangwu wangwuvalue
		○ mget lisi wangwu
	• Hash // 适合存储对象
		○ hset user name zhangsan
		○ hmset user age 16 job coder
		○ hget user name
		○ hgetall user
		○ hmget user nam age
	• List // 有序列表，可以从首尾添加数据
		○ lpush name zhangsan lisi // lisi zhangsan
		○ rpush name wangwu // lisi zhangsan wangwu
		○ lrange name 0 3 // lisi zhangsan wangwu
		○ lpop name // lisi        // zhangsan wangwu // 如果value全部移除，key自动删除
		○ rpop name // wangwu        //zhangsan
		○ del name
	• Set
		○ set是string类型的无序集合，元素的值也不能重复
		○ sadd email 123@123.com 456@456.com // 添加元素
		○ smembers email // 查看元素
		○ sismember email 123@123.com 是否存在元素
		○ srem eamil 123@123.com // 删除元素
	• Zset
		○ zadd names 1 zhangsan 2 lisi 3 wangwu
		○ zrange names 0 2
		○ zrange names 0 2 withscores
		○ zrank names lisi // 返回元素索引
		○ zrem name lisi // 移除元素

# Golang 操作 redis
1. 安装redis包 gihub.com/garyburd/redigo/redis
2. 操作string
	```
	package main
	import (
	    "fmt"
	    "github.com/garyburd/redigo/redis"
	)
	func main() {
	    conn, err := redis.Dial("tcp", "127.0.0.1:6379")
	    if err != nil {
	        fmt.Println("redis.Dial err=", err)
	        return
	    }
	    defer conn.Close()
	    n, err := conn.Do("set", "code", 1234)
	    if err != nil {
	        fmt.Println("redis set err=", err)
	        return
	    }
	    fmt.Println(n)
	    r, err := conn.Do("get", "code")
	    // r, err := redis.String(conn.Do("get", "code"))
	    if err != nil {
	        fmt.Println("redis get err=", err)
	        return
	    }
	    sr, _ := redis.String(r, err)
	    fmt.Println(sr)
	}
	```

3. 操作hash
	```
	package main
	import (
	    "fmt"
	    "github.com/garyburd/redigo/redis"
	)
	func main() {
	    conn, err := redis.Dial("tcp", "127.0.0.1:6379")
	    if err != nil {
	        fmt.Println("redis Dial err=", err)
	        return
	    }
	    defer conn.Close()
	    _, err = conn.Do("hmset", "user01", "name", "john", "age", 18, "gender", "male")
	    if err != nil {
	        fmt.Println("redis hmset err=", err)
	        return
	    }
	    r, err := redis.StringMap(conn.Do("hgetall", "user01"))
	    if err != nil {
	        fmt.Println("redis hgetall err=", err)
	        return
	    }
	    fmt.Println(r)
	}
	```

4. 操作list
	```
	_, err := conn.Do("lpush", "heroList", "zhangsan ", 30, "lisi", 40)
	r, err := redis.String(conn.Do("rpop", "heroList"))
	```

5. Redis 链接池
	a. 事先初始化移动数量的链接，放入链接池
	b. Go 需要操作redis时，直接从redis链接池取出链接即可
	c. 这样可以节省临时获取redis链接的时间，从而提高效率
	```
    package main
	import (
	    "fmt"
	    "github.com/garyburd/redigo/redis"
	)
	var pool *redis.Pool
	func init() {
	    pool = &redis.Pool{
	        MaxIdle:     8,   // 最大空闲链接数
	        MaxActive:   0,   // 和数据库的最大连接数，0 表示没有限制
	        IdleTimeout: 100, // 最大空闲时间
	        Dial: func() (redis.Conn, error) {
	            return redis.Dial("tcp", "127.0.0.1:6379")
	        },
	    }
	}
	func main() {
	    conn1 := pool.Get()
	    _, err := conn1.Do("set", "name", "霸天🐱")
	    if err != nil {
	        fmt.Println("conn.Do err=", err)
	        return
	    }
	    conn1.Close()
	    conn2 := pool.Get()
	    defer conn2.Close()
	    r, err := redis.String(conn2.Do("get", "name"))
	    if err != nil {
	        fmt.Println("conn.Do err=", err)
	        return
	    }
	    fmt.Println(r)
	}
	```

# 小练习
1. 存放一个student信息：
	```
    stu1 name 张三 age 30 score 80 address 北京
	hmset stu1 name 张三 age 30 score 80 address 北京
	```

2. 存放一个商品信息包括商品名、价格、生产日期完成对应的添加、删除操作
	```
    sadd product pork  48 2019/12/23
	smembers product
	sismember product 48
	srem product 48
    ```