# 小练习
	1. 冒泡排序算法
	有一个数列，按从小到大排序后输出
	```
    package main
	import "fmt"
	// 执行N-1轮
	// 第1轮找到最大值
	// 第N-1轮找到次小值
	func bubbleSort(nums []int) []int {
	    for i := 0; i < len(nums)-1; i++ {
	        for j := 0; j < len(nums)-1-i; j++ {
	            if nums[j] > nums[j+1] {
	                nums[j], nums[j+1] = nums[j+1], nums[j]
	            }
	        }
	    }
	    return nums
	}
	func main() {
	    var nums = []int{5, 2, 7, 4, 3, 6}
	    fmt.Println(nums)
	    fmt.Println(bubbleSort(nums))
	}
	```

	2. 顺序查找
	有一个数列：白眉鹰王、金毛狮王、紫衫龙王、青翼福王
	从键盘任意输入一个名称，判断数列中是否包含此名称
	```
    package main
	import "fmt"
	func quickFind(list []string) bool {
	    var input string
	    fmt.Println("请输入一个名字：")
	    fmt.Scanln(&input)
	    for _, item := range list {
	        if input == item {
	            return true
	        }
	    }
	    return false
	}
	func main() {
	    var list = []string{"白眉鹰王", "金毛狮王", "紫衫龙王", "青翼福王"}
	    fmt.Println(quickFind(list))
	}
	```

	3. 对一个有序数组进行二分查找 {1, 8, 10, 89, 1000, 1234}，输入一个数看看该数组是否存在此数，并求出下标，如果没有就提示“没有这个数”
	```
    package main
	import "fmt"
	// 二分查找，最多找len(list)/2轮
	//   二分查找，要求数组有序
	func binaryFind(list []int, num int) int {
	    var index = -1
	    var start = 0
	    var mid = len(list) / 2
	    var end = len(list) - 1
	    for i := 0; i <= len(list)/2; i++ {
	        mid = (start + end) / 2
	        if list[mid] == num {
	            return mid
	        }
	        if list[mid] > num {
	            end = mid - 1
	            continue
	        }
	        if list[mid] < num {
	            start = mid + 1
	            continue
	        }
	    }
	    return index
	}
	func main() {
	    var list = []int{1, 8, 10, 89, 1000, 1234}
	    fmt.Println(list)
	    fmt.Println("请输入一个要查找的数值：")
	    var num int
	    fmt.Scanln(&num)
	    index := binaryFind(list, num)
	    if index == -1 {
	        fmt.Println("没有这个数")
	    } else {
	        fmt.Println(index)
	    }
	}
	```

	4. 请用二维数组输出如下图形
	0 0 0 0 0 0  
	0 0 1 0 0 0  
	0 2 0 3 0 0  
	0 0 0 0 0 0   
	```
    package main
	import "fmt"
	func main() {
	    // for i := 0; i < 4; i++ {
	    //  for j := 0; j < 6; j++ {
	    //      if i == 1 && j == 2 {
	    //          fmt.Print(1)
	    //      } else if i == 2 && j == 1 {
	    //          fmt.Print(2)
	    //      } else if i == 2 && j == 3 {
	    //          fmt.Print(3)
	    //      } else {
	    //          fmt.Print(0)
	    //      }
	    //  }
	    //  fmt.Println()
	    // }
	    var demArray [4][6]int
	    demArray[1][2] = 1
	    demArray[2][1] = 2
	    demArray[2][3] = 3
	    for i := 0; i < 4; i++ {
	        for j := 0; j < 6; j++ {
	            fmt.Print(demArray[i][j])
	        }
	        fmt.Println()
	    }
	}
	```

	5. 定义二维数组，用于保存三个班，每个班无名同学成绩，
	并求出每个班级平均分以及所有班级平均分
	```
    package main
	import "fmt"
	func main() {
	    var demArray = [3][5]int{
	        {50, 60, 70, 80, 90},
	        {51, 61, 71, 81, 91},
	        {52, 62, 72, 82, 92},
	    }
	    var sum int
	    for i := 0; i < 3; i++ {
	        var subSum int
	        for j := 0; j < 5; j++ {
	            sum += demArray[i][j]
	            subSum += demArray[i][j]
	        }
	        fmt.Println(subSum / 5)
	    }
	    fmt.Println(sum / (3 * 5))
	}
	```
