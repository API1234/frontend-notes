## 合并K个排序的链表
给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。


```
示例：

输入：[1->2->4, 1->3->4, 5->6]
输出：1->1->2->3->4->4->5->6
```

注意：本题与主站[ 23 题](https://leetcode-cn.com/problems/merge-k-sorted-lists/) 相同

###  方法一：顺序合并
我们可以想到一种最朴素的方法：用一个变量 $ans$ 来维护以及合并的链表，第 $i$ 次循环把第 $i$ 个链表和 $ans$ 合并，答案保存到 $ans$ 中

#### 复杂度分析：
- 时间复杂度 $O(k^2 n)$：假设每个链表的最长长度是 $n$。在第一次合并后，$ans$ 的长度为 $n$；第二次合并后，$ans$ 的长度为 $2\times n$，第 $i$ 次合并后，$ans$ 的长度为 $i\times n$。第 $i$ 次合并的时间代价是 $O(n + (i - 1) \times n) = O(i \times n)$，那么总的时间代价为 $O(\sum_{i = 1}^{k} (i \times n)) = O(\frac{(1 + k)\cdot k}{2} \times n) = O(k^2 n))$
- 空间复杂度 $O(1)$

#### 代码
```JavaScript
var mergeTwoLists = function(l1, l2) {
  let prehead = new ListNode()
  let pre = prehead

  while (l1 && l2) {
    if (l1.val < l2.val) {
      pre.next = l1
      l1 = l1.next
    } else {
      pre.next = l2
      l2 = l2.next
    }
    pre = pre.next
  }

  pre.next = l1 || l2
  
  return prehead.next
};

var mergeKLists = function(lists) {
  return lists.reduce((acc, val) => {
    return mergeTwoLists(val, acc)
  }, null)
};
```

### 方法二：分治合并
#### 算法思路：
考虑优化方法一，用分治的方法进行合并。

- 将 $k$ 个链表配对并将同一对中的链表合并
- 第一轮合并以后， $k$ 个链表被合并成了 $\frac{k}{2}$ 个链表，平均长度为 $\frac{2n}{k}$，然后是 $\frac{k}{4}$ 个链表， $\frac{k}{8}$ 个链表等等
- 重复这一过程，直到我们得到了最终的有序链表

#### 复杂度分析：
- 时间复杂度 $O(kn \times \log k)$：考虑递归「向上回升」的过程——第一轮合并 $\frac{k}{2}$ 组链表，每一组的时间代价是 $O(2n)$；第二轮合并 $\frac{k}{4}$ 组链表，每一组的时间代价是 $O(4n)......$ 所以总的时间代价是 $O(\sum_{i = 1}^{\infty} \frac{k}{2^i} \times 2^i n) = O(kn \times \log k)$
- 空间复杂度 $O(\log k)$：递归会使用到 $O(\log k)$ 空间代价的栈空间

#### 代码
```JavaScript
var mergeKLists = function(lists) {
  if (lists.length === 0) return null

  const mergeTwoLists = (l1, l2) => {
    let prehead = new ListNode()
    let pre = prehead
    
    while (l1 && l2) {
      if (l1.val < l2.val) {
        pre.next = l1
        l1 = l1.next
      } else {
        pre.next = l2
        l2 = l2.next
      }
      pre = pre.next
    }
    pre.next = l1 || l2

    return prehead.next
  }

  const merge = (left, right) => {
    if(left === right) return lists[left]
    let mid = (left + right) >> 1
    return mergeTwoLists(merge(left, mid), merge(mid + 1, right))
  }

  return merge(0, lists.length - 1)
};
```
