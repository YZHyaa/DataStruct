在讲解链表的LeetCode题之前先说几点注意：

- head指针与p指针：

  - head 指针指向参数链表的头，用于标识链表
  - p 指针一般是 p = head，用于操作链表
  - 两者都可以变，都可以用来操作当前链表。但若只是内部操作，即操作完后还要返回原链表，比如删除指定节点，然后返回链表，就必须要返回head。
  - **推荐每次链表传来先 p = head ，然后拿p去操作链表，若head无用再考虑将p替换**

- 有头链表与无头链表在遍历时的区别：

  - 有头链表：while (p.next != null)
  - 无头链表：while (p != null)

- 技巧：先画图找出指针变换关系，然后将关系写下来，再补充

- 注意：**next只是指针而已，并不是Node**

## [206.反转链表¹](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

**示例:**
```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

### 解法一：双指针（前后）
* 思路：p & pre 前后指针四步走
* 复杂度：
	* Time：O（n），与链表长度n正相关
	* Space：O（1），没有额外内存空间
```java
public ListNode reverseList(ListNode head) {
        ListNode p = head, pre = null;
        while (p != null) {
            // 四步走
            ListNode tmp = p.next;
            p.next = pre;
            pre = p;
            p = tmp;
        }
    	// 注：这里是返回pre，因为此时p指向null
        return pre;
}
```
以上四步可以死记硬背成反转的定式。
### 解法二：递归*
简单图示：
```
initial:
1 -> 2 -> 3 -> 4 -> 5

after reverseList(2):
1 -> 2 <- 3 <- 4 <- 5
     |
     v
    null
	
after operate on 1:
null <- 1 <- 2 <- 3 <- 4 <- 5
```
代码如下：
```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) return head;
    
    ListNode p = reverseList(head.next);
    
    head.next.next = head;
    head.next = null;
    
    return p;
}
```

----
## [24.两两交换链表中的节点²](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**示例:**

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

### 解法一：哨兵结点
* 思路：看见两两交换可以联想到交换两个数a，b：tmp = a；a = b；b = tmp。放到本题中
	* 第3个节点可以看做a，第四个节点可以看做b，第二个节点可以看做tmp。从而实现两两交换
	* 但是有一个问题，第一个节点和第二个几点的tmp还没有着落 ---> 引入一个**哨兵节点**
* 复杂度
	* Time：O（n）
	* Space：O（1）
```java
public ListNode swapPairs(ListNode head) {
    	// 空，或者只有一个节点，直接返回
        if (head == null || head.next == null) return head;
        
        ListNode p = head;
    	// 因为第一个节点会和第二个节点交换，所以第二个节点会成为最终的head
        head = head.next;
    	// 创建哨兵结点tmp，作为链表的头结点，帮助前两个节点完成交换
        ListNode tmp = new ListNode(-1);
		
    	// 节点偶数个：p == null结束
    	// 节点奇数个：p.next == null 结束
        while(p != null && p.next != null) {
            // 开始：tmp 1 -> 2 -> 3 -> 4
            // tmp -> 2
            tmp.next = p.next;
            // 1 -> 3
            // 这一步很关键，一定要先把两边连好再连中间
            p.next = p.next.next;
            // tmp -> 2 -> 1    
            tmp.next.next = p;
			
            // tmp = 1
            tmp = tmp.next.next;
            // p = 3  
            p = p.next; 
            // 结束：2 -> 1（tmp） -> 3(p) -> 4
        }
        return head;
}
```
### 解法二：递归*
```java
public class Solution {
    public ListNode swapPairs(ListNode head) {
        if ((head == null)||(head.next == null))
            return head;
        ListNode n = head.next;
        head.next = swapPairs(head.next.next);
        n.next = head;
        return n;
    }
}
```
----
## [21. 合并两个有序链表¹](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```
### 解法一：归并

* 思路：新建一个头，链表1，链表2比大小插入即可。就像归并排序中合并两个子数组一样。
注：这里能这么做的前提一定是待合并的两部分是排好序的！！
* 复杂度
  * Time：O（m + n）
  * Space：O（1)

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    	// 创建一个虚拟头结点
        ListNode head = new ListNode(0), p = head;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                p.next = l1;
                l1 = l1.next;
            } else {
                p.next = l2;
                l2 = l2.next;
            }
            p = p.next;
        }
    	// 可能还存在一个链表未遍历完的情况
        if (l1 != null) p.next = l1;
        if (l2 != null) p.next = l2;
		// 注：这里要返回head.next，因为head是我们new的虚拟头结点
        return head.next;
    }
```

