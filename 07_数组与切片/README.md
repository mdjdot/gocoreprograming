# 数组
数组的首个元素的地址是数组的首地址
```
package main
import "fmt"
func main() {
    numArrry := []int32{1, 2, 3}
    fmt.Println(&numArrry[0])   // 0xc0000120c0
    fmt.Println(&numArrry)      // &[1 2 3]
    fmt.Printf("%p", &numArrry) // 0xc000004480
}
```
数组长度固定
```
package main
import "fmt"
func printArgs(args ...string) []string {
    for _, arg := range args {
        fmt.Println(arg)
    }
}
```
```
func main() {
    nums := [...]int{1, 2, 3, 4}
    fmt.Println(nums, len(nums))
    printArgs("a", "b", "c")
    args := []string{"ii", "jj", "kk"}
    printArgs(args...) // 接受 切片... 方式传入参数
}
```

# 切片
```
var slic:=array[:]
var slic2:= make([]int,12)
```
```
package main
import "fmt"
func main() {
    var slice = make([]int, 5)
    slice = []int{1, 2, 3, 4}
    slice1 := append(slice, 5)
    fmt.Println(slice1) // [1 2 3 4 5]
    slice2 := append(slice, slice1...)
    fmt.Println(slice2) // [1 2 3 4 1 2 3 4 5]
    copy(slice2, slice)
    fmt.Println(slice2) // [1 2 3 4 1 2 3 4 5]
}
```
```
package main
import "fmt"
func main() {
    var a = []int{1, 2, 3, 4, 5}
    var slice = make([]int, 1)
    fmt.Println(slice) // [0]
    copy(slice, a)
    fmt.Println(slice) // [1] // copy不能增加容量
}
```

string底层是一个byte数组，因此也可以进行切片处理  
string不可修改  

# 小练习
	1. 创建一个byte类型的26个元素的数组，分别放置'A'-'Z'。使用for循环访问所有元素并打印出来。
    ```
    package main
	import "fmt"
	func main() {
	    array := [26]byte{}
	    for i := 0; i < 26; i++ {
	        array[i] = 'A' + byte(i)
	    }
	    for _, c := range array {
	        fmt.Printf("%c\n", c)
	    }
	}
	```

	2. 求出一个数组的最大值，并得到对应的下标
	```
    package main
	import "fmt"
	func getMax(nums []int) (int, int) {
	    var index, max int
	    for i, n := range nums {
	        if n > max {
	            index, max = i, n
	        }
	    }
	    return index, max
	}
	func main() {
	    nums := [...]int{1, 2, 3, 9, 7, 5, 6}
	    fmt.Println(getMax(nums[:])) // 数组转切片
	}
	```

	3. 求出一个数组的和和平均值
	```
    package main
	import "fmt"
	func getSumAndAvg(nums []int) (int, int) {
	    var sum int
	    for _, n := range nums {
	        sum += n
	    }
	    return sum, sum / len(nums)
	}
	func main() {
	    nums := [...]int{1, 2, 3, 9, 7, 5, 6}
	    fmt.Println(getSumAndAvg(nums[:])) // 数组转切片
	}
	```

	4. 随机生成五个数，并将其反转打印
	```
    package main
	import (
	    "fmt"
	    "math/rand"
	    "time"
	)
	func main() {
	    var nums [5]int
	    rand.Seed(time.Now().UnixNano()) // 种子应该在循环外，只设置一次
	    // rand.Seed(123)
	    for i := 0; i < len(nums); i++ {
	        nums[i] = rand.Intn(100)
	    }
	    fmt.Println(nums)
	    for i := 0; i <= len(nums)/2; i++ {
	        nums[i], nums[len(nums)-i-1] = nums[len(nums)-i-1], nums[i]
	    }
	    fmt.Println(nums)
	}
	```

	5. 编写一个 fbn(n int)，要求完成
		1. 可以接收一个 n int
		2. 能够将斐波那契的数列放到切片中
		3. 斐波那契数列 1，1，2，3，5，8，13
	```
    package main
	import "fmt"
	func fb(n int) int {
	    if n == 2 || n == 1 {
	        return 1
	    }
	    return fb(n-2) + fb(n-1)
	}
	func fbn(n int) []int {
	    var slice []int
	    for i := 1; i <= n; i++ {
	        slice = append(slice, fb(i))
	    }
	    return slice
	}
	func main() {
	    fmt.Println(fbn(10))
	}
	```
