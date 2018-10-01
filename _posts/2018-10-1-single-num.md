---
title: 只出现一次的数字
---

> 本文为golang leetcode 系列文章，涉及：*位运算*

## I [136 只出现一次的数字](https://leetcode-cn.com/problems/single-number/description/)

### 原题描述：
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？
```
示例 1:

输入: [2,2,1]
输出: 1
```
```
示例 2:

输入: [4,1,2,1,2]
输出: 4
```

### 题目分析
考虑到位运算中对数字a有 `a^a = 0`, `a^0 = a`, `a^b = b^a`的特性，因而只需要将数组里面所有数字做异或运算，则最后结果就是答案

### AC 代码
```golang
func singleNumber(nums []int) int {
	x := nums[0]
	for i := 1; i < len(nums); i++ {
		x ^= nums[i]
	}
	return x
}
```

## II [137 只出现一次的数字](https://leetcode-cn.com/problems/single-number-ii/description/)

### 原题描述
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？
```
示例 1:

输入: [2,2,3,2]
输出: 3
```
```
示例 2:

输入: [0,1,0,1,0,1,99]
输出: 99
```

### 题目分析
考虑不存在这个只出现一次的数字情况下，把所有数以二进制表示，这样他们的每一位单独加起来应该能被 3 整除

当加入一个额外数字 x 后，把每一位加起来后就会有两种情况：
* % 3 = 0，表示 x 在这一位应该为 0
* % 3 = 1，表示 x 在这一位应该为 1

### AC 代码
```golang
func singleNumber(nums []int) int {
	m := 0
	for i := 0; i < 32; i++ {
		x := 0
		for _, n := range nums {
			x += (n >> uint(i)) & 1
		}
		m += (x % 3) << uint(i)
	}

	exist := false
	for _, n := range nums {
		if n == m {
			exist = true
			break
		}
	}

	if exist == false {
		return m - (1 << 32)
	}

	return m
}
```
代码还有可待优化的地方，再补充。。。

## III [260 只出现一次的数字](https://leetcode-cn.com/problems/single-number-iii/)

### 原题描述
给定一个整数数组 nums，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。
```
示例 :

输入: [1,2,1,3,2,5]
输出: [3,5]
```
注意：
- 结果输出的顺序并不重要，对于上面的例子， [5, 3] 也是正确答案。
- 你的算法应该具有线性时间复杂度。你能否仅使用常数空间复杂度来实现？

### 题目分析

假设只出现一次的两个数字分别为 a, b. 那么全部异或运算后的结果应为 a^b. 由于 a 和 b 是不同的两个数，二进制表示中必然从低位往上至少有一位是不同的。根据这不同的一位，我们可以想办法将数组分割成两部分，分别包含 a, b.

考虑通过 a^b 的值构造一个 flag 值，`flag=(a^b) & (^((a^b) - 1))`. 这个 flag 除了 a 与 b 中最开始不同的那一位为 1，其他位都置为 0. 如：
```golang
a, b := 2, 4 // 0010, 0100
ab := a^b // 0110
flag := ab & (^(ab-1)) // 0010
```

用计算得到的 flag 与数组中的每一项做 & 运算，则根据结果为 0 或者 1 来分离得到只包含 a 或 b 的数组。从而求解

### AC 代码
```golang

func singleNumber(nums []int) []int {
	xy := 0
	for _, n := range nums {
		xy ^= n
	}

	flag := xy & (^(xy - 1))
	fmt.Printf("%b\t%b\n", xy, flag)
	res := make([]int, 2)
	for _, n := range nums {
		if flag&n == 0 {
			res[0] ^= n
			continue
		}
		res[1] ^= n
	}
	return res
}
```
