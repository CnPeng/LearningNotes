* [原文链接](https://www.liwenzhou.com/posts/Go/LeetCode/)
* [视频](https://www.bilibili.com/video/BV17Q4y1P7n9?p=143)

用 Go 语言刷 LeetCode 记录，只是为了练习 Go 语言，能力有限不保证都是最优解，只能在此抛转引玉了。

## 27.1 两数之和

### 27.1.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

### 27.1.2 我的解法

```go
func twoSum(nums []int, target int) []int {
    l := len(nums)
    for i:=0;i<l;i++{
        for j:=i+1;j<l;j++{
            if nums[i]+nums[j] == target{
                return []int{i,j}
            }
        }
    }
    return []int{}
}
```

## 27.2 两数相加

### 27.2.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/add-two-numbers/)

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

### 27.2.2 我的解法

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    var ret = new(ListNode)
    current := ret
    carry := 0
    for l1 != nil || l2 != nil {
        x, y := 0, 0
        if l1 != nil {
            x = l1.Val
            l1 = l1.Next
        }
        if l2 != nil {
            y = l2.Val
            l2 = l2.Next
        }
        sum := x + y + carry
        current.Next = &ListNode{Val:sum%10}
        current = current.Next
        carry = sum/10  
    }
    if carry > 0{
        current.Next=&ListNode{Val:carry}
    }
    return ret.Next
}
```

## 27.3 无重复字符的最长子串

### 27.3.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串，请你找出其中不含有重复字符的最长子串的长度。

示例1：

```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

示例2：

```
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

示例3：

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```    

### 27.3.2 我的解法

```go
func lengthOfLongestSubstring(s string) int {
	i := 0
	max := 0
	a := []rune(s)
	for m, c := range a {
		for n := i; n < m; n++ {
			if a[n] == c {
				i = n + 1
			}
		}
		if m-i+1 > max {
			max = m - i + 1
		}
	}
	return max
}
```

## 27.4 寻找两个有序数组的中位数

### 27.4.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

给定两个大小为 m 和 n 的有序数组 nums1 和 nums2 。

请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 nums1 和 nums2 不会同时为空。 

示例1：

```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```

示例2：

```
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

### 27.4.2 我的解法

```go
func findMedianSortedArrays(nums1 []int, nums2 []int) float64 {
   
    len1 := len(nums1)
    len2 := len(nums2)
    lenSum := len1+len2
    if lenSum == 0 {
        return float64(0)
    }
    l, r := 0, 0 
    a := make([]int,0,lenSum)
    for l <len1 && r < len2{
        if nums1[l] < nums2[r]{
            a = append(a,nums1[l])
            l++
        }else {
            a = append(a, nums2[r])
            r++
        }
    }
    a = append(a, nums1[l:]...)
    a = append(a, nums2[r:]...)
    if lenSum%2 != 0 {
        return float64(a[lenSum/2])
    }else {
        return (float64(a[lenSum/2-1]) + float64(a[lenSum/2]))/2
    }
}
```

## 27.5 整数反转

### 27.5.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/reverse-integer/)

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:

```
输入: 123
输出: 321
```

示例 2:

```
输入: -123
输出: -321
```

示例 3:

```
输入: 120
输出: 21
```

注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231, 231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

### 27.5.2 我的解法

```go
func reverse(x int) int {
    var recv int
    for x != 0 {
        pop := x % 10
        x /= 10
        if recv*10 > 1<<31-1 || (recv*10 == 1<<31-1 && pop > 7){
            return 0
        }
        if recv*10 < -1<<31 || (recv*10 == -1<<31 && pop < 8) {
            return 0
        }
        recv = recv*10+pop
    }
    return recv
}
```

## 27.6 回文数

### 27.6.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/palindrome-number/)

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:

```
输入: 121
输出: true
```

示例 2:

```
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

示例 3:

```
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
```

### 27.6.2 我的解法

```go
func isPalindrome(x int) bool {
    // 如果是负数或类似10、100、1000这种就直接返回false
    if x < 0 || (x != 0 && x %10 == 0) {
        return false
    }
    // 反转后半部分的数字，和前半部分做比较
    var right int
    for x > right{
        right = right*10 + x%10
        x /= 10
    }
    // 注意前半部分和后半部分刚好相等（1221）或正好差一位（121）
    return x == right || x == right/10  
}
```

## 27.7 盛最多水的容器

### 27.7.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/container-with-most-water/)

给你 n 个非负整数 a1，a2，…，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

### 27.7.2 我的解法

```go
func maxArea(height []int) int {
	i := 0
	j := len(height) - 1
	var ret int
	for i < j {
        var area int
		if height[i] > height[j]{
			area = (j-i)* height[j]
		}else {
			area = (j-i)* height[i]
		}
		if area > ret {
			ret = area
		}
		if height[i] < height[j] {
			i++
		}else {
			j--
		}
	}
	return ret
}
```

## 27.8 罗马数字转整数

### 27.8.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/roman-to-integer/submissions/)

罗马数字包含以下七种字符: I， V， X， L，C，D 和 M。

字符    |      数值
---|---
I      |     1
V      |     5
X      |     10
L      |    50
C      |     100
D      |     500
M      |     1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

* I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
* X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
* C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。 

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

示例 1:

```
输入: "III"
输出: 3
```

示例 2:

```
输入: "IV"
输出: 4
```

示例 3:

```
输入: "IX"
输出: 9
```

示例 4:

```
输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
```

示例 5:

```
输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

### 27.8.2 我的解法

```go
func romanToInt(s string) int {
    m := map[string]int{
        "I": 1,
        "IV": 4,
        "V": 5,
        "IX": 9,
        "X": 10,
        "XL": 40,
        "L": 50,
        "XC": 90,
        "C": 100,
        "CD": 400,
        "D": 500,
        "CM": 900,
        "M": 1000,
    }
    var ret int
    for i:=0;i<len(s);{
        // 先尝试读两个字符，注意索引不要越界
        if i+2<=len(s)&&m[s[i:i+2]]!=0{
            ret +=m[s[i:i+2]]
            i+=2
            continue
        }else{
            ret +=m[s[i:i+1]]
            i++
            continue
        }
    }
    return ret
}
```

## 27.9 最长公共前缀

### 27.9.1 题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/longest-common-prefix/)

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ““。

示例 1:

```
输入: ["flower","flow","flight"]
输出: "fl"
```

示例 2:

```
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。
```

### 27.9.2 我的解法

```go
func longestCommonPrefix(strs []string) string {
    if len(strs) == 0{
        return ""
    }
	prefix := strs[0]
	for i := 1; i < len(strs); i++ {
		for !strings.HasPrefix(strs[i], prefix) {
			prefix = strs[0][0 : len(prefix)-1]
			if prefix == "" {
				return ""
			}
		}
	}
	return prefix
}
```

## 27.10 三数之和

### 27.10.1 题目描述

[力扣（LeetCode）链接](https://leetcode-cn.com/problems/3sum/submissions/)

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

```
例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

### 27.10.2 我的解法

```go
func threeSum(nums []int) [][]int {
    lenNums := len(nums)
    ret := make([][]int, 0, 0)
    if lenNums < 3{
        return ret
    }
    // 排序
    sort.Ints(nums)
    
    for i:=0;i<lenNums;i++{
        if nums[i] > 0{break}
        if i > 0 && nums[i] == nums[i-1]{continue} // 去重
        l, r := i+1, lenNums-1
        for l < r {
            sum := nums[i] + nums[l] + nums[r]
            if sum == 0 {
                ret = append(ret, []int{nums[i],nums[l], nums[r]})
                for l< r && nums[l] == nums[l+1]{l++} // 左边去重
                for r < r && nums[r] == nums[r-1]{r--} // 后边去重
                l++
                r--
            }
            if sum > 0 {r--}
            if sum < 0 {l++}
        }
    }
    return ret
}
```

排序+双指针解法：

```go
func threeSum(nums []int) [][]int {
	var ret [][]int
	l := len(nums)
	if l < 3 {
		return ret
	}
	sort.Ints(nums) // 排序

	for k := 0; k < l; k++ {
		if nums[k] > 0 {
			break
		}
		if k > 0 && nums[k] == nums[k-1] {  // 去重
			continue
		}
		for i, j := k+1, l-1; i < j; {
			if j < l-1 && nums[j] == nums[j+1] {  // 去重
				j--
				continue
			}
			sum := nums[k] + nums[i] + nums[j]
			if sum > 0 {
				j--
			} else if sum < 0 {
				i++
			} else {
				ret = append(ret, []int{nums[k], nums[i], nums[j]})
				i++
				j--
			}
		}
	}
	return ret
}
```

## 27.11 有效的括号

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/valid-parentheses/)

给定一个只包括 ‘(‘，’)‘，’{‘，’}‘，’[‘，’]‘ 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。 注意空字符串可被认为是有效字符串。
示例 1:

输入: "()"
输出: true
示例 2:

输入: "()[]{}"
输出: true
示例 3:

输入: "(]"
输出: false
示例 4:

输入: "([)]"
输出: false
示例 5:

输入: "{[]}"
输出: true


我的解法

```go
func isValid(s string) bool {
    m := map[byte]byte{
        ')':'(',
        ']':'[',
        '}':'{',
    }
    a := make([]byte, 0, len(s)/2)
    for _, b := range []byte(s){
        if b == '(' || b == '{' || b == '['{
            a = append(a, b)
            continue
        }
        if b == ')' || b == '}' || b == ']'{
            if len(a) >0 && m[b] == a[len(a)-1]{
                a = a[:len(a)-1]
                continue
            }else {
                return false
            }
        }
    }
    if len(a) == 0 {
        return true
    }else {
        return false
    }
}
```

## 27.12 合并两个有序链表

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/submissions/)

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

示例：

输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

我的解法

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode { 
    var ret = new(ListNode) // 前置虚拟节点法
    cur := ret // 定义一个保存当前节点的变量
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val{
            cur.Next = l1
            l1 = l1.Next
        }else {
            cur.Next = l2
            l2 = l2.Next
        }
        cur = cur.Next // 当前节点向后移
    }
    // 追加没遍历到的链表
    if l1 != nil {
        cur.Next = l1
    }
    if l2 != nil {
        cur.Next = l2
    }
    return ret.Next
}
```

## 27.13 删除排序数组中的重复项

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

示例 1:

给定数组 nums = [1,1,2], 

函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,0,1,1,1,2,2,3,3,4],

函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。

你不需要考虑数组中超出新长度后面的元素。
说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}


我的解法
注意与14题解题思路的比较。

```go
func removeDuplicates(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    ret := 1
    for i:=0;i<len(nums)-1;{
        if nums[i+1] == nums[i]{
            nums = append(nums[:i], nums[i+1:]...)
            continue
        }
        i++
        ret++
    }
    return ret
}
```

## 27.14 移除元素

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/remove-element/)

给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

示例 1:

给定 nums = [3,2,2,3], val = 3,

函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。

你不需要考虑数组中超出新长度后面的元素。
示例 2:

给定 nums = [0,1,2,2,3,0,4,2], val = 2,

函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。

注意这五个元素可为任意顺序。

你不需要考虑数组中超出新长度后面的元素。
说明:

为什么返回数值是整数，但输出的答案是数组呢?

请注意，输入数组是以“引用”方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

// nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
int len = removeElement(nums, val);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}


我的解法

```go
func removeElement(nums []int, val int) int {
    ret := 0
    for i:=0;i<len(nums);{
        if nums[i] != val {
            nums[ret] = nums[i]
            ret++
        }
        i++
    }
    return ret
}
```

## 27.15 删除排序链表中的重复元素

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

示例 1:

输入: 1->1->2
输出: 1->2
示例 2：

输入: 1->1->2->3->3
输出: 1->2->3


我的解法

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func deleteDuplicates(head *ListNode) *ListNode {
    cur := head
    for cur != nil && cur.Next != nil {
        if cur.Val == cur.Next.Val {
            cur.Next = cur.Next.Next
        }else {
            cur = cur.Next
        }
    }
    return head
}
```

## 27.16 删除排序链表中的重复元素 II

懂车帝一面问过这个题。

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

示例 1:

输入: 1->2->3->3->4->4->5
输出: 1->2->5
示例 2:

输入: 1->1->1->2->3
输出: 2->3


我的解法


递归法：

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil {
        return head
    }
    if head.Next != nil && head.Val == head.Next.Val{
        for head!=nil&&head.Next !=nil && head.Val == head.Next.Val{
            head = head.Next
        }
        return deleteDuplicates(head.Next)
    }else {
        head.Next = deleteDuplicates(head.Next)
    }
    return head
}
```

前置虚假节点方法：

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func deleteDuplicates(head *ListNode) *ListNode {
    if head == nil || head.Next==nil {
        return head
    }
    dummy := new(ListNode)
    dummy.Next = head
    
    pre := dummy
    cur := head
    for cur != nil&& cur.Next!=nil{
        if cur.Val == cur.Next.Val{
            for cur.Next!=nil && cur.Val == cur.Next.Val{
                cur = cur.Next
            }
            pre.Next = cur.Next
            cur = cur.Next
        }else {
        pre = cur
        cur = cur.Next
        }
    }
    return dummy.Next
}
```

## 27.17 环形链表

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

我的解法
去映客面试问过这道题。

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func hasCycle(head *ListNode) bool {
    n1 := head  // 慢指针一步一格
    n2 := head  // 快指针一步两格
    if head == nil || head.Next == nil {
        return false
    }
    for n2 != nil && n2.Next != nil {
        n1 = n1.Next
        n2 = n2.Next.Next
        if n1 == n2 {
            return true
        }
    }
    return false
}
```

## 27.18 反转链表

题目描述
[力扣（LeetCode）链接](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

示例:

输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL

我的解法

迭代方式：

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseList(head *ListNode) *ListNode {
    var pre *ListNode
    cur := head
    for cur!= nil{
        tmp := cur.Next
        cur.Next = pre
        pre = cur
        cur = tmp
    }
    return pre
}
```

递归方式：

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseList(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return head
    }
    p := reverseList(head.Next)
    head.Next.Next = head
    head.Next = nil
    return p
}
```

持续更新中…