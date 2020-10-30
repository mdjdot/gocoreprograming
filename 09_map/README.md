# map的使用
通常key为int和string类型，其他类型无法用==判断  
引用声明后无法使用，需要使用make初始化  
map中的key是无序的  

添加或修改value，amap[key]=value  
删除key，delete(amap，key)  

# 小练习
1. 演示一个value是map的map
	我们要存放3个学生信息，每个学生有name和sex信息
	```
    package main
	import "fmt"
	func main() {
	    studentsInfo := map[int]map[string]string{
	        1: map[string]string{
	            "name": "zhangsan",
	            "sex":  "man",
	        },
	        2: map[string]string{
	            "name": "zhangsan",
	            "sex":  "man",
	        },
	    }
	    fmt.Println(studentsInfo)
	    fmt.Println(studentsInfo[2]["sex"])
	}
	```

2. 演示一个map的切片
	使用一个map来记录monster的信息name和age，一个monster对应一个map，并且妖怪的个数可以动态的增加
	```
    package main
	import "fmt"
	func main() {
	    var monstersInfo = []map[string]string{
	        map[string]string{
	            "name": "zan",
	            "age":  "14",
	        },
	    }
	    monstersInfo = append(monstersInfo, map[string]string{
	        "name": "li",
	        "age":  "20",
	    })
	    fmt.Println(monstersInfo)
	}
	```
	
3. 通过排序key对map进排序
	```
    package main
	import (
	    "fmt"
	    "sort"
	)
	func main() {
	    var infos = map[int]string{
	        3: "zhangsan",
	        1: "lisi",
	        2: "wanguwu",
	    }
	    var keys []int
	    for key, _ := range infos {
	        keys = append(keys, key)
	    }
	    sort.Ints(keys)
	    for _, key := range keys {
	        fmt.Println(infos[key])
	    }
	}
    ```