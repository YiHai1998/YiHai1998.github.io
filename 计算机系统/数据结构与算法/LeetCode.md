# LeetCode

## [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

**提示：**

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

**进阶：**你可以想出一个时间复杂度小于 `O(n2)` 的算法吗？



## [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/01/02/addtwonumber1.jpg)

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例 2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例 3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1] 
```

**提示：**

- 每个链表中的节点数在范围 `[1, 100]` 内
- `0 <= Node.val <= 9`
- 题目数据保证列表表示的数字不含前导零

题解：

> 递归

```java
class Solution {
    // N is the size of l1, M is the size of l2
    // Time Complexity: O(max(M,N))
    // Space Complexity: O(max(M,N)) if dummy is counted else O(1)
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int total = l1.val + l2.val;
        int next1 = total / 10;	// 用来记录是否大于等于10. 如果大于等于了10返回1，没超过返回0
        ListNode res = new ListNode(total % 10);	// 然后创建一个已知首位结点的新链表
        if (l1.next != null || l2.next != null || next1 != 0) {
            l1 = l1.next == null ? new ListNode(0): l1.next ;	// 如果l1为空，进创建一个首结点为0的链表，等待加入递归。
            l2 = l2.next == null ? new ListNode(0): l2.next ;
            l1.val += next1;	// next1不算 0 就是 1。我们只要算给l1就行，给l2也可。
            res.next = addTwoNumbers(l1, l2);
        }
        return res;
    }
}
```

