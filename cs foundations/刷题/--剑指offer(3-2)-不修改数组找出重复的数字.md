--剑指offer(3-2)-不修改数组找出重复的数字

Leecode 287. Find the Duplicate Number

Given an array *nums* containing *n* + 1 integers where each integer is between 1 and *n* (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

**Example 1:**

```
Input: [1,3,4,2,2]
Output: 2
```

**Example 2:**

```
Input: [3,1,3,4,2]
Output: 3
```

**Note:**

1. You **must not** modify the array (assume the array is read only).
2. You must use only constant, *O*(1) extra space.
3. Your runtime complexity should be less than *O*(*n*2).
4. There is only one duplicate number in the array, but it could be repeated more than once.



### 思路1

假设数组没有重复, 那么我们可以做到一点, 就是将数组的下标和1到n每一个数一对一映射起来, 比如数组是213, 则映射关系为0->2, 1->1, 2->3.  假设这个一对一映射关系是一个函数f(n), 其中n是下标, f(n)是映射到的数. 如果我们从下标为0出发, 根据这个函数计算出一个值, 以这个值为新的下标, 再用这个函数计算, 以此类推,直到下标超届.实际上可以产生一个类似链表一样的序列. 比如在这个例子中有2个下标的序列, 0->2->3.



但是如果有重复的话, 这中间就会产生多对一的映射, 比如数组2131, 映射关系为0->2, 2->3, 3->1, 1->1. 这样, 我们推演的序列就一定会有环路了, 这里下标的序列是0->2->3->1->1->1->1->..., **而环的起点就是重复的数.**



所以该题实际上就是找环路起点的题.



快慢指针,快指针走2步,慢指针走1步,直到2个指针相遇. 这时候保持慢下标位置不变，再用一个新的下标从0开始，这两个下标都继续每轮映射一次，当这两个下标相遇时，就是环的起点，也就是重复的数。 ... 不好理解.



### 思路2   [推荐]

二分法

对于某个长度为n+1的数组a, 数组中元素只能是1~n. 先统计a中在[1,$\frac n 2$]区间的元素个数,设为$n_1$, 如果$n_1 $>$\frac n 2$, 那么在[1,$\frac n 2$]区间中必有重复元素. 否则在$(\frac n 2, n]$区间必有重复元素.  从而将搜索区间缩小到$[\frac n 2 + 1, n]$. 依次使用这种二分查找的思想, 缩小查找范围, 最后就可以找到重复的元素.





1.