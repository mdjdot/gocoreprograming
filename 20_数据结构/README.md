# 稀疏数组sparsearray
当一个数组中大部分元素为相同值时，可以使用稀疏数组来保存该数组。  
	1. 记录数组一共有几行几列，有多少个不同的值  
	2. 把具有不同值的元素的行列及值记录在一个小规模的数组中，从而缩小程序的规模  
```
0	0	0	0	0	0	0	0	0	0
0	0	1	0	0	0	0	0	0	0
0	0	0	2	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
0	0	0	0	0	0	0	0	0	0
```

# 小练习
1. 使用稀疏数组来保留二维数组（棋盘、地图等）
```
package main
import (
    "fmt"
    "io/ioutil"
    "os"
    "strconv"
    "strings"
)
type valueNode struct {
    row int
    col int
    val int
}
func main() {
    // 原始数据
    var array [11][11]int
    array[1][2] = 1
    array[2][3] = 2
    for _, arr := range array {
        for _, value := range arr {
            fmt.Printf("%d ", value)
        }
        fmt.Println()
    }
    // 稀疏数组
    var sparseArr []valueNode
    // 第一条数据用来记录规模
    sparseArr = append(sparseArr, valueNode{
        row: 11,
        col: 11,
        val: 0,
    })
    for i, v := range array {
        for j, v2 := range v {
            sparseArr = append(sparseArr, valueNode{
                row: i,
                col: j,
                val: v2,
            })
        }
    }
    file, err := os.OpenFile("./result.dat", os.O_TRUNC|os.O_CREATE, 0666)
    if err != nil {
        panic(err)
    }
    defer file.Close()
    for _, node := range sparseArr {
        file.WriteString(fmt.Sprintf("%d %d %d\n", node.row, node.col, node.val))
    }
    content, err := ioutil.ReadFile("./result.dat")
    if err != nil {
        panic(err)
    }
    lines := strings.Split(string(content), "\n")
    lines = lines[1:121]
    var newArr [11][11]int
    for _, node := range lines {
        row, err := strconv.Atoi(strings.Split(node, " ")[0])
        if err != nil {
            panic(err)
        }
        col, err := strconv.Atoi(strings.Split(node, " ")[1])
        if err != nil {
            panic(err)
        }
        val, err := strconv.Atoi(strings.Split(node, " ")[2])
        if err != nil {
            panic(err)
        }
        newArr[row][col] = val
    }
    for _, arr := range newArr {
        for _, value := range arr {
            fmt.Printf("%d ", value)
        }
        fmt.Println()
    }
}
```

# 队列queue
	• 队列是一个有序列表，可以用数组或是链表来实现
	• 遵循先入先出的原则
	• 使用数组模拟队列
	
# 小练习
1. 使用数组模拟非环形队列
```
package main
import (
    "errors"
    "fmt"
    "os"
)
// Queue 结构体
type Queue struct {
    maxSize int
    array   [5]int
    front   int
    rear    int
}
// AddQueue 方法
func (q *Queue) AddQueue(val int) error {
    if q.rear == q.maxSize-1 {
        return errors.New("queue full")
    }
    q.rear++
    q.array[q.rear] = val
    return nil
}
// GetQueue 方法
func (q *Queue) GetQueue() (int, error) {
    if q.rear == q.front {
        return -1, errors.New("queue empty")
    }
    q.front++
    val := q.array[q.front]
    return val, nil
}
// ShowQueue 方法
func (q *Queue) ShowQueue() {
    fmt.Println("队列当前的情况是：")
    for i := q.front + 1; i <= q.rear; i++ {
        fmt.Printf("array[%d]=%d\t", i, q.array[i])
    }
    fmt.Println()
}
func main() {
    queue := &Queue{
        maxSize: 5,
        front:   -1,
        rear:    -1,
    }
    var key string
    var val int
    for {
        fmt.Println(`
1. 输入 add 表示添加数据到队列
2. 输入 get 表示从队列获取数据
3. 输入 show 表示显示队列
4. 输入exit表示退出程序`)
        fmt.Scanln(&key)
        switch key {
        case "add":
            fmt.Println("输入你要入队列的数：")
            fmt.Scanln(&val)
            err := queue.AddQueue(val)
            if err != nil {
                fmt.Println(err)
                continue
            }
            fmt.Println("加入队列成功")
        case "get":
            val, err := queue.GetQueue()
            if err != nil {
                fmt.Println(err)
                continue
            }
            fmt.Println("从队列中取出一个数=", val)
        case "show":
            queue.ShowQueue()
        case "exit":
            os.Exit(0)
        default:
            fmt.Println("输入命令错误，请重新输入")
        }
    }
}
```


2. 使用数组模拟环形队列
```
package main
import (
    "errors"
    "fmt"
    "os"
)
// CircleQueue 环形队列结构
type CircleQueue struct {
    maxSize int
    array   [5]int
    head    int
    tail    int
}
func (c *CircleQueue) isFull() bool {
    return (c.tail+1)%c.maxSize == c.head
}
func (c *CircleQueue) isEmpty() bool {
    return c.tail == c.head
}
func (c *CircleQueue) sizer() int {
    return (c.tail + c.maxSize - c.head) % c.maxSize
}
// Push 环形队列插入数据
func (c *CircleQueue) Push(val int) error {
    if c.isFull() {
        return errors.New("queue full")
    }
    c.array[c.tail] = val
    c.tail = (c.tail + 1) % c.maxSize
    return nil
}
// Pop 环形队列取出数据
func (c *CircleQueue) Pop() (int, error) {
    if c.isEmpty() {
        return 0, errors.New("queue empty")
    }
    val := c.array[c.head]
    c.head = (c.head + 1) % c.maxSize
    return val, nil
}
// ListQueue 显示环形队列数据
func (c *CircleQueue) ListQueue() {
    fmt.Println("环形队列情况如下：")
    size := c.sizer()
    if size == 0 {
        fmt.Println("队列为空")
    }
    tempHead := c.head
    for i := 0; i < size; i++ {
        fmt.Printf("arr[%d]=%d\t", tempHead, c.array[tempHead])
        tempHead = (tempHead + 1) % c.maxSize
    }
    fmt.Println("")
}
func main() {
    queue := &CircleQueue{
        maxSize: 5,
        head:    0,
        tail:    0,
    }
    var val int
    var key string
    var errkey string
    for {
        fmt.Println(`
1. 输入 add 表示添加数据到队列
2. 输入 get 表示从队列获取数据
3. 输入 show 表示显示队列
4. 输入 exit 表示退出队列`)
        fmt.Scanln(&key)
        switch key {
        case "add":
            fmt.Println("请输入要添加的数据")
            _, err := fmt.Scanln(&val)
            if err != nil {
                fmt.Println("输入数据不正确，错误：", err)
                fmt.Scanln(&errkey)
                continue
            }
            err = queue.Push(val)
            if err != nil {
                fmt.Println(err)
                continue
            }
            fmt.Println("加入队列成功")
        case "get":
            val, err := queue.Pop()
            if err != nil {
                fmt.Println(err)
                continue
            }
            fmt.Println("从队列中取出", val)
        case "show":
            queue.ListQueue()
        case "exit":
            os.Exit(0)
        default:
            fmt.Println("输入命令错误，请重新输入")
            continue
        }
    }
}
```

# 链表
	• 单向链表查找的方向只能是一个方向，双向链表可以向前或者向后查找
	• 单向链表不能自我删除，需要靠辅助节点，双向链表可以自我删除

# 小练习
1. 单向链表实例
使用带head头的单向链表实现 - 水浒英雄排行榜管理
完成对英雄人物的增效删改查操作
```
package main
import "fmt"
// HeroNode 英雄节点结构
type HeroNode struct {
    no       int
    name     string
    nickname string
    next     *HeroNode
}
// InsertHeroNode 插入英雄节点
func InsertHeroNode(head *HeroNode, newHeroNode *HeroNode) {
    temp := head
    for {
        if temp.next == nil {
            break
        }
        temp = temp.next
    }
    temp.next = newHeroNode
}
// InsertHeroNode2 插入英雄节点
func InsertHeroNode2(head *HeroNode, newHeroNode *HeroNode) {
    temp := head
    flag := true
    for {
        if temp.next == nil {
            break
        } else if temp.next.no >= newHeroNode.no {
            break
        } else if temp.next.no == newHeroNode.no {
            flag = false
            break
        }
        temp = temp.next
    }
    if !flag {
        fmt.Println("已经存在no=", newHeroNode.no)
        return
    }
    newHeroNode.next = temp.next
    temp.next = newHeroNode
}
// DelHeroNode 删除英雄节点
func DelHeroNode(head *HeroNode, id int) {
    temp := head
    flag := false
    for {
        if temp.next == nil {
            break
        } else if temp.next.no == id {
            flag = true
            break
        }
        temp = temp.next
    }
    if flag {
        temp.next = temp.next.next
    } else {
        fmt.Println("要删除的id不存在")
    }
}
// ListHeroNode 列出所有英雄节点
func ListHeroNode(head *HeroNode) {
    temp := head
    if temp.next == nil {
        fmt.Println("空链表")
        return
    }
    for {
        fmt.Printf("[%d,%s,%s]==>", temp.next.no, temp.next.name, temp.next.nickname)
        temp = temp.next
        if temp.next == nil {
            break
        }
    }
}
func main() {
    head := &HeroNode{}
    hero1 := &HeroNode{no: 1, name: "宋江", nickname: "及时雨"}
    hero2 := &HeroNode{no: 2, name: "卢俊义", nickname: "玉麒麟"}
    hero3 := &HeroNode{no: 3, name: "林冲", nickname: "豹子头"}
    hero4 := &HeroNode{no: 4, name: "吴用", nickname: "智多星"}
    InsertHeroNode2(head, hero1)
    InsertHeroNode2(head, hero2)
    InsertHeroNode2(head, hero3)
    InsertHeroNode2(head, hero4)
    ListHeroNode(head)
}
```

2. 双向链表的实例
使用带head头的单向链表实现 - 水浒英雄排行榜管理
完成对英雄人物的增效删改查操作
```
package main
import "fmt"
// HeroNode 英雄节点结构
type HeroNode struct {
    no       int
    name     string
    nickname string
    pre      *HeroNode
    next     *HeroNode
}
// InsertHeroNode 插入英雄节点
// 把新英雄插入到最后面
func InsertHeroNode(head *HeroNode, newHeroNode *HeroNode) {
    temp := head
    for {
        if temp.next == nil {
            break
        }
        temp = temp.next
    }
    temp.next = newHeroNode
    newHeroNode.pre = temp
}
// InsertHeroNode2 插入英雄节点
// 根据英雄no插入 n
func InsertHeroNode2(head *HeroNode, newHeroNode *HeroNode) {
    temp := head
    flag := true
    for {
        if temp.next == nil {
            break
        } else if temp.next.no >= newHeroNode.no {
            break
        } else if temp.next.no == newHeroNode.no {
            flag = false
            break
        }
        temp = temp.next
    }
    if !flag {
        fmt.Println("已经存在no=", newHeroNode.no)
        return
    }
    newHeroNode.next = temp.next
    newHeroNode.pre = temp
    if temp.next != nil {
        temp.next.pre = newHeroNode
    }
    temp.next = newHeroNode
}
// DelHeroNode 删除英雄节点
func DelHeroNode(head *HeroNode, id int) {
    temp := head
    flag := false
    for {
        if temp.next == nil {
            break
        } else if temp.next.no == id {
            flag = true
            break
        }
        temp = temp.next
    }
    if flag {
        temp.next = temp.next.next
        if temp.next != nil {
            temp.next.pre = temp
        }
    } else {
        fmt.Println("要删除的id不存在")
    }
}
// ListHeroNode 列出所有英雄节点
func ListHeroNode(head *HeroNode) {
    temp := head
    if temp.next == nil {
        fmt.Println("空链表")
        return
    }
    for {
        fmt.Printf("[%d,%s,%s]==>", temp.next.no, temp.next.name, temp.next.nickname)
        temp = temp.next
        if temp.next == nil {
            break
        }
    }
}
// ListHeroNode2 列出所有英雄节点
func ListHeroNode2(head *HeroNode) {
    temp := head
    if temp.next == nil {
        fmt.Println("空链表...")
        return
    }
    for {
        if temp.next == nil {
            break
        }
        temp = temp.next
    }
    for {
        fmt.Printf("[%d,%s,%s]==>", temp.no, temp.name, temp.nickname)
        temp = temp.pre
        if temp.pre == nil {
            break
        }
    }
}
func main() {
    head := &HeroNode{}
    hero1 := &HeroNode{no: 1, name: "宋江", nickname: "及时雨"}
    hero2 := &HeroNode{no: 2, name: "卢俊义", nickname: "玉麒麟"}
    hero3 := &HeroNode{no: 3, name: "林冲", nickname: "豹子头"}
    hero4 := &HeroNode{no: 4, name: "吴用", nickname: "智多星"}
    InsertHeroNode2(head, hero1)
    InsertHeroNode2(head, hero2)
    InsertHeroNode2(head, hero3)
    InsertHeroNode2(head, hero4)
    ListHeroNode(head)
    fmt.Println()
    ListHeroNode2(head)
}
```

3. 单项环形链表实例
```
package main
import "fmt"
// CatNode 猫结构体
type CatNode struct {
    No   int
    Name string
    Next *CatNode
}
// InsertCatNode 插入猫节点
func InsertCatNode(head *CatNode, newCatNode *CatNode) {
    // 如果是添加第一只猫
    if head.Next == nil {
        head.No = newCatNode.No
        head.Name = newCatNode.Name
        head.Next = head
        fmt.Println(newCatNode, "加入到环形的链表")
        return
    }
    temp := head
    for {
        if temp.Next == head {
            break
        }
        temp = temp.Next
    }
    temp.Next = newCatNode
    newCatNode.Next = head
}
// ListCircleLink 输出这个环形的链表
func ListCircleLink(head *CatNode) {
    fmt.Println("环形链表的情况如下：")
    temp := head
    if temp.Next == nil {
        fmt.Println("空链表...")
        return
    }
    for {
        fmt.Printf("猫的信息为=[id=%d name=%s]->\n", temp.No, temp.Name)
        if temp.Next == head {
            break
        }
        temp = temp.Next
    }
}
// DelCatNode 删除猫节点
func DelCatNode(head *CatNode, id int) *CatNode {
    temp := head
    helper := head
    // 空链表
    if temp.Next == nil {
        fmt.Println("空链表...")
        return head
    }
    // 只有一个节点
    if temp.Next == head {
        if temp.No == id {
            temp.Next = nil
        }
        return head
    }
    // 将helper定位到链表最后
    for {
        if helper.Next == head {
            break
        }
        helper = helper.Next
    }
    flag := true
    for {
        if temp.Next == head {
            break
        }
        if temp.No == id {
            if temp == head {
                head = head.Next
            }
            helper.Next = temp.Next
            fmt.Printf("猫猫=%d\n", id)
            flag = false
            break
        }
        temp = temp.Next
        helper = helper.Next
    }
    if flag {
        if temp.No == id {
            helper.Next = temp.Next
            fmt.Printf("猫猫=%d\n", id)
        } else {
            fmt.Printf("对不起，没有no=%d\n", id)
        }
    }
    return head
}
func main() {
    head := &CatNode{}
    cat1 := &CatNode{No: 1, Name: "tom"}
    cat2 := &CatNode{No: 2, Name: "tom2"}
    cat3 := &CatNode{No: 3, Name: "tom3"}
    InsertCatNode(head, cat1)
    InsertCatNode(head, cat2)
    InsertCatNode(head, cat3)
    ListCircleLink(head)
    head = DelCatNode(head, 30)
    ListCircleLink(head)
}
```

4. 单向环形链表解决约瑟夫环问题
设编号为1，2，…，n的n个人围坐一圈，约定编号为k（1<=k<=n）的人从1开始报数，数到m的那个人出列，它的下一位又从1开始报数，数到m的那个人又出列，一次类推，知道所有人出列未知，由此产生一个出列编号的序列。
```
package main
import "fmt"
// Boy 小孩的结构体
type Boy struct {
    No   int
    Next *Boy
}
// AddBoy 添加小孩
func AddBoy(num int) *Boy {
    first := &Boy{}
    curBoy := &Boy{}
    if num < 1 {
        fmt.Println("num的值不对")
        return first
    }
    for i := 1; i <= num; i++ {
        boy := &Boy{
            No: i,
        }
        if i == 1 {
            first = boy
            curBoy = boy
            curBoy.Next = first
        } else {
            curBoy.Next = boy
            curBoy = boy
            curBoy.Next = first
        }
    }
    return first
}
// ShowBoy 显示小孩的链表
func ShowBoy(first *Boy) {
    if first.Next == nil {
        fmt.Println("链表为空，没有小孩")
        return
    }
    curBoy := first
    for {
        fmt.Printf("小孩编号=%d->", curBoy.No)
        if curBoy.Next == first {
            break
        }
        curBoy = curBoy.Next
    }
}
// PlayGame 约瑟夫环游戏
func PlayGame(first *Boy, startNo int, countNum int) {
    if first.Next == nil {
        fmt.Println("空链表...")
        return
    }
    tail := first
    for {
        if tail.Next == first {
            break
        }
        tail = tail.Next
    }
    for i := 1; i <= startNo-1; i++ {
        first = first.Next
        tail = tail.Next
    }
    fmt.Println()
    for {
        for i := 1; i <= countNum-1; i++ {
            first = first.Next
            tail = tail.Next
        }
        fmt.Printf("小孩编号为%d 出圈\n", first.No)
        first = first.Next
        tail.Next = first
        if tail == first {
            break
        }
    }
    fmt.Printf("小孩编号为%d出圈\n", first.No)
}
func main() {
    first := AddBoy(500)
    ShowBoy(first)
    PlayGame(first, 20, 31)
}
```

# 常见排序
	• 冒泡排序
	• 选择排序
	• 插入排序
	• 快速排序

# 冒泡排序
	• 第一轮排序把最大值或最小值放到最右边
	• 第二轮排序把第二大值或第二小值放到最右边
	• 需要排N-1轮
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

# 选择排序
	• 第一轮把R[0]到R[n-1]中的最小值放到R[0]
	• 第二轮把R[1]到R[n-1]中的最小值放到R[1]
	• 需要排n-1轮
	```
    package main
	import "fmt"
	// SelectSort 选择排序
	func SelectSort(arr *[6]int) {
	    for i := 0; i < len(arr)-1; i++ {
	        max := arr[i]
	        maxIndex := i
	        for j := i + 1; j < len(arr); j++ {
	            if max < arr[j] {
	                max = arr[j]
	                maxIndex = j
	            }
	        }
	        if maxIndex != i {
	            arr[i], arr[maxIndex] = arr[maxIndex], arr[i]
	        }
	        fmt.Printf("第%d次 %v\n", i+1, *arr)
	    }
	}
	func main() {
	    arrSix := [6]int{3, 1, 6, 4, 2, 5}
	    SelectSort(&arrSix)
	}
	```

# 插入排序
	• 需要一个有序表和一个无序表
	• 依次把无序表中的数放到有序表中
	```
    package main
	import "fmt"
	// InsertSort 插入排序
	func InsertSort(arr *[7]int) {
	    for i := 1; i < len(arr); i++ {
	        insertVal := arr[i]
	        insertIndex := i - 1
	        for insertIndex >= 0 && arr[insertIndex] < insertVal {
	            arr[insertIndex+1] = arr[insertIndex]
	            insertIndex--
	        }
	        if insertIndex+1 != i {
	            arr[insertIndex+1] = insertVal
	        }
	        fmt.Printf("第%d次插入后 %v\n", i, *arr)
	    }
	}
	func main() {
	    arrSeven := [7]int{3, 1, 6, 4, 2, 5, 0}
	    InsertSort(&arrSeven)
	}
	```

	# 快速排序
	• 一轮排序把数据分成两部分，一部分数据都小于另一部分的数据
	• 再对两部分数据进行快速排序
	```
    package main
	import "fmt"
	// QuickSort 快速排序算法
	func QuickSort(left, right int, array *[9]int) {
	    l := left
	    r := right
	    pivot := array[(left+right)/2]
	    for l < r {
	        for array[l] < pivot {
	            l++
	        }
	        for array[r] > pivot {
	            r--
	        }
	        if l >= r {
	            break
	        }
	        array[l], array[r] = array[r], array[l]
	        if array[l] == pivot {
	            r--
	        }
	        if array[r] == pivot {
	            l++
	        }
	    }
	    if l == r {
	        l++
	        r--
	    }
	    if left < r {
	        QuickSort(left, r, array)
	    }
	    if right > l {
	        QuickSort(l, right, array)
	    }
	}
	func main() {
	    arr := [9]int{-9, 78, 0, 23, -567, 70, 123, 90, -23}
	    fmt.Println("初始：", arr)
	    QuickSort(0, len(arr)-1, &arr)
	    fmt.Println("main...")
	    fmt.Println(arr)
	}
	```

	# 三种排序算法速度分析

# 栈的介绍
	• Stack
	• FILO
	• 从栈顶放入或删除

入栈
```
->	3	栈顶
->	2	
->	1	栈底
```
出栈  
```
<-	3	栈顶
<-	2	
<-	1	栈底
```

# 栈的应用场景
	1. 子程序调用
	2. 递归调用
	3. 表达式的转值与求值
	4. 二叉树的遍历
	5. 图形的深度优先搜索法（depth first）


# 小练习
	1. 用数组模拟栈的入栈、出栈等操作
	package main
	import (
	    "errors"
	    "fmt"
	)
	// Stack 类型
	type Stack struct {
	    MaxTop int
	    Top    int
	    Arr    [5]int
	}
	// Push 入栈
	func (s *Stack) Push(val int) error {
	    if s.Top == s.MaxTop-1 {
	        fmt.Println("stack full")
	        return errors.New("stack full")
	    }
	    s.Top++
	    s.Arr[s.Top] = val
	    return nil
	}
	// Pop 出栈
	func (s *Stack) Pop() (int, error) {
	    if s.Top == -1 {
	        fmt.Println("stack empty")
	        return 0, errors.New("stack empty")
	    }
	    val := s.Arr[s.Top]
	    s.Top--
	    return val, nil
	}
	// List 列出所有元素
	func (s *Stack) List() {
	    if s.Top == -1 {
	        fmt.Println("stack empty")
	        return
	    }
	    fmt.Println("栈的情况如下：")
	    for i := s.Top; i >= 0; i-- {
	        fmt.Printf("arr[%d]=%d\n", i, s.Arr[i])
	    }
	}

	2. 请输入一个表达式，输出计算结果
	计算式：[7*2*2-5+1-5+3-3]
	package main
	import (
	    "errors"
	    "fmt"
	    "strconv"
	)
	// Stack 类型
	type Stack struct {
	    MaxTop int
	    Top    int
	    Arr    [5]int
	}
	// Push 入栈
	func (s *Stack) Push(val int) error {
	    if s.Top == s.MaxTop-1 {
	        fmt.Println("stack full")
	        return errors.New("stack full")
	    }
	    s.Top++
	    s.Arr[s.Top] = val
	    return nil
	}
	// Pop 出栈
	func (s *Stack) Pop() (int, error) {
	    if s.Top == -1 {
	        fmt.Println("stack empty")
	        return 0, errors.New("stack empty")
	    }
	    val := s.Arr[s.Top]
	    s.Top--
	    return val, nil
	}
	// List 列出所有元素
	func (s *Stack) List() {
	    if s.Top == -1 {
	        fmt.Println("stack empty")
	        return
	    }
	    fmt.Println("栈的情况如下：")
	    for i := s.Top; i >= 0; i-- {
	        fmt.Printf("arr[%d]=%d\n", i, s.Arr[i])
	    }
	}
	// IsOper 是否是操作符
	func (s *Stack) IsOper(val int) bool {
	    if val == '+' || val == '-' || val == '*' || val == '/' {
	        return true
	    }
	    return false
	}
	// Cal 计算方法
	func (s *Stack) Cal(num1, num2, oper int) int {
	    res := 0
	    switch oper {
	    case '+':
	        return num2 + num1
	    case '-':
	        return num2 - num1
	    case '*':
	        return num2 * num1
	    case '/':
	        return num2 / num1
	    default:
	        fmt.Println("运算符错误")
	    }
	    return res
	}
	// Priority 运算符优先级
	func (s *Stack) Priority(oper int) int {
	    var res int
	    if oper == '+' || oper == '-' {
	        res = 0
	    }
	    if oper == '*' || oper == '/' {
	        res = 1
	    }
	    return res
	}
	func main() {
	    numStack := &Stack{
	        MaxTop: 20,
	        Top:    -1,
	    }
	    operStack := &Stack{
	        MaxTop: 20,
	        Top:    -1,
	    }
	    exp := "30+30*6-4-6"
	    index := 0
	    var num1, num2, oper, result int
	    var keepNum string
	    for {
	        ch := exp[index : index+1]
	        temp := int([]byte(ch)[0])
	        if operStack.IsOper(temp) {
	            if operStack.Top == -1 {
	                operStack.Push(temp)
	            } else {
	                if operStack.Priority(operStack.Arr[operStack.Top]) >= operStack.Priority(temp) {
	                    num1, _ = numStack.Pop()
	                    num2, _ = numStack.Pop()
	                    oper, _ = operStack.Pop()
	                    result = operStack.Cal(num1, num2, oper)
	                    numStack.Push(result)
	                    operStack.Push(temp)
	                } else {
	                    operStack.Push(temp)
	                }
	            }
	        } else {
	            keepNum += ch
	            if index == len(exp)-1 {
	                val, _ := strconv.ParseInt(keepNum, 10, 64)
	                numStack.Push(int(val))
	            } else {
	                if operStack.IsOper(int([]byte(exp[index+1 : index+2])[0])) {
	                    val, _ := strconv.ParseInt(keepNum, 10, 64)
	                    numStack.Push(int(val))
	                    keepNum = ""
	                }
	            }
	        }
	        if index+1 == len(exp) {
	            break
	        }
	        index++
	    }
	    for {
	        if operStack.Top == -1 {
	            break
	        }
	        num1, _ = numStack.Pop()
	        num2, _ = numStack.Pop()
	        oper, _ = operStack.Pop()
	        result = operStack.Cal(num1, num2, oper)
	        numStack.Push(result)
	    }
	    res, _ := numStack.Pop()
	    fmt.Printf("表达式%s=%v", exp, res)
	}
	
# 递归介绍
递归就是函数/方法自己调用自己

递归常用场景  
	1. 迷宫问题  
	2. 打印问题  
	3. 阶乘问题  
	4. 八皇后问题  
	5. 汉诺塔问题  
	6. 球和篮子问题  

# 小练习
	1. 迷宫问题
	package main
	import "fmt"
	// SetWay 找路
	func SetWay(myMap *[8][7]int, i, j int) bool {
	    if myMap[6][5] == 2 {
	        return true
	    }
	    if myMap[i][j] == 0 {
	        myMap[i][j] = 2
	        if SetWay(myMap, i+1, j) {
	            return true
	        }
	        if SetWay(myMap, i, j+1) {
	            return true
	        }
	        if SetWay(myMap, i-1, j) {
	            return true
	        }
	        if SetWay(myMap, i, j-1) {
	            return true
	        }
	    }
	    myMap[i][j] = 3
	    return false
	}
	func main() {
	    // 创建二维数组模拟迷宫
	    // 规则
	    // 1 如果元素值为1，就是墙
	    // 2 如果元素的值为0，就是没有走过的路
	    // 3 如果元素的值为2，是一个通路
	    // 4 如果元素的值为3，是走过的点，但是走不通
	    var myMap [8][7]int
	    // 把地图最上边和最下边设置为1
	    for i := 0; i < 7; i++ {
	        myMap[0][i] = 1
	        myMap[7][i] = 1
	    }
	    // 把地图最左边和最右边设置为1
	    for i := 0; i < 7; i++ {
	        myMap[i][0] = 1
	        myMap[i][6] = 1
	    }
	    myMap[3][1] = 1
	    myMap[3][2] = 1
	    myMap[1][2] = 1
	    myMap[2][2] = 1
	    // 输出地图
	    for i := 0; i < 8; i++ {
	        for j := 0; j < 7; j++ {
	            fmt.Print(myMap[i][j], " ")
	        }
	        fmt.Println()
	    }
	    // 使用测试
	    SetWay(&myMap, 1, 1)
	    fmt.Println("探测完毕...")
	    // 输出地图
	    for i := 0; i < 8; i++ {
	        for j := 0; j < 7; j++ {
	            fmt.Print(myMap[i][j], " ")
	        }
	        fmt.Println()
	    }
	}
	
# 哈希表介绍
Hash table，散列表、哈希表，是根据key value访问的数据结构。通过把关键码值映射到表中的一个位置来访问记录，以加快查找速度。映射函数叫做散列函数，存放记录的数组叫做散列表


# 哈希表应用场景

# 小练习
有一个公司，当有新的员工来报道时，要求将该员工的信息加入（id，性别，年龄，住址…）当输入该员工的id时，要求查找到该员工的所有信息
要求：不适用数据库，尽量节省内存，速度越快越好
```
package main
import "fmt"
type Emp struct {
    Id   int
    Name string
    Next *Emp
}
type EmpLink struct {
    Head *Emp
}
func (e *EmpLink) Insert(emp *Emp) {
    cur := e.Head
    var pre *Emp = nil
    if cur == nil {
        e.Head = emp
        return
    }
    for {
        if cur != nil {
            if cur.Id > emp.Id {
                break
            }
            pre = cur
            cur = cur.Next
        } else {
            break
        }
    }
    pre.Next = emp
    emp.Next = cur
}
func (e *EmpLink) ShowLink(no int) {
    if e.Head == nil {
        fmt.Printf("链表%d 为空\n", no)
        return
    }
    cur := e.Head
    for {
        if cur != nil {
            fmt.Printf("链表%d 雇员id=%d 名字=%s->", no, cur.Id, cur.Name)
            cur = cur.Next
        } else {
            break
        }
    }
    fmt.Println("")
}
type HashTable struct {
    LinkArr [7]EmpLink
}
func (h *HashTable) Insert(emp *Emp) {
    linkNo := h.HashFun(emp.Id)
    h.LinkArr[linkNo].Insert(emp)
}
func (h *HashTable) ShowAll() {
    for i := 0; i < len(h.LinkArr); i++ {
        h.LinkArr[i].ShowLink(i)
    }
}
func (h *HashTable) HashFun(id int) int {
    return id % 7
}
func main() {
    key := ""
    id := 0
    name := ""
    var hashTable HashTable
    for {
        fmt.Println("================雇员系统菜单================")
        fmt.Println("input 表示添加雇员")
        fmt.Println("show 表示显示雇员")
        fmt.Println("find 表示查找雇员")
        fmt.Println("exit 表示退出系统")
        fmt.Println("请输入你的选择")
        fmt.Scanln(&key)
        switch key {
        case "input":
            fmt.Println("输入雇员id")
            fmt.Scanln(&id)
            fmt.Println("输入雇员name")
            fmt.Scanln(&name)
            hashTable.Insert(&Emp{Id: id, Name: name})
        case "show":
            hashTable.ShowAll()
        case "exit":
        default:
            fmt.Println("输入错误")
        }
    }
}
```

	
