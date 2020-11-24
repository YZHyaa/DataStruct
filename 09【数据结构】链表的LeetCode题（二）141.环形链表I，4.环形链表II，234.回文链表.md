## [141.环形链表¹](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://img-blog.csdnimg.cn/img_convert/4a18acda10c1606aa5d1132b9de26d61.png)

**示例 2：**
```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://img-blog.csdnimg.cn/img_convert/1a6fcfc68d7340c39151075f7fa53150.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

![img](https://img-blog.csdnimg.cn/img_convert/3039274e08a9385ea77b20a81060ed40.png)

### 解法一：枚举法
* 思路：用一个表保存所有结点，若结点已经在表中，则有环
	* 这里的表用HashSet最好，因为 set 的 contains 方法与 add 方法都是基于hash表，复杂度 O(1）--> 5ms
	* 若用ArrayList，则 contains 方法的复杂度是 O(n）--> 372ms
* 复杂度
	* Time：O（n）
	* Space：O（n），链表中的每个节点都要放到容器中
```java
public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;

        ListNode node = head;
    	// 这里太合适用set了
        Set<ListNode> set = new HashSet<>();
        while(node.next != null) {
            if (set.contains(node)) 
                return true;
                set.add(node);
                node = node.next;
        }
        return false;
}
```

### 解法二：双指针（快慢）
* 思路：如果有环，走的快（一次两步）的指针一定能追上走的慢的指针（一次一步）
* 复杂度
	* Time：O（n）
	* Space：O（1）
```java
public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;

        ListNode qn = head,sn = head; // quickNode，slowNode
    	// 关键是这个判断条件
    	// qn != null 保证了 qn.next 不会出现空指针异常；
    	// qn.next != null 保证了 qn.next.next 不会出现空指针异常；
        while (qn != null && qn.next != null) { 
            sn = sn.next;
            qn = qn.next.next;
            if (sn == qn) {
                return true;
            }
        }
        return false;
}
```
----
## [4.环形链表 II²](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

**说明**：不允许修改给定的链表。

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://img-blog.csdnimg.cn/img_convert/4a18acda10c1606aa5d1132b9de26d61.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：tail connects to node index 0
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://img-blog.csdnimg.cn/img_convert/1a6fcfc68d7340c39151075f7fa53150.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：no cycle
解释：链表中没有环。
```

![img](https://img-blog.csdnimg.cn/img_convert/3039274e08a9385ea77b20a81060ed40.png)

### 解法一：双指针（快慢）

* 思路：这其实是一个规律，**快慢指针相遇后，快指针从头再与慢相遇处即环始点**
* 复杂度
	* Time：O（n）
	* Space：O（1）
	
```java
public ListNode detectCycle(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        // 让快慢指针相遇
        while (true) {
            // 无环判断
            // 注意：这里不能放到循环条件，假设，那么退出循环那部分如何操作？
            if (fast == null || fast.next == null) {
                return null;
            }
            fast = fast.next.next;
            slow = slow.next;
            // 定位到两指针相遇处
            if (slow == fast) {
                break;
            }
        }
    	// 将快指针从头开始，慢指针还在相遇处
        fast = head;
    	// 两指针再次相遇位置就是环始点
        while (fast != slow) {
            fast = fast.next;
            slow = slow.next;
        }

        return fast;
}
```
----
## [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

请判断一个链表是否为回文链表。

**示例 1:**

```
输入: 1->2
输出: false
```

**示例 2:**

```
输入: 1->2->2->1
输出: true
```

### 解法一：栈

- 思路：遍历链表，将所有节点压入栈，再遍历一遍进行对比
  注：栈里应该存的是val，而不是ListNode，因为比较的时候顺序是相反的，ListNode肯定都不相同。
- 复杂度
  - Time：O（n）
  - Space：O（n），用了个Stack

```java
public boolean isPalindrome(ListNode head) {
        // 注：栈里存的是具体value，是Stack<Integer>，不是Stack<ListNode>
        Stack<Integer> stack = new Stack<>();
        // 注：链表用for遍历更简洁
        for (ListNode p = head; p != null; p = p.next) 
            stack.push(p.val);
        for (ListNode p = head; p != null; p = p.next)
            if (p.val != stack.pop())
                return false;
        return true;
    }
```
### 2.解法二：折半对比

- 思路：先将链表前一半反转，然后从中间开始同时进行遍历比较（双指针）。但是要注意一点，若长度是奇数情况，则要将后面的指针（cur）后移一位
-  复杂度
	* Time：O（n）
	* Space：O（1）

```java
public boolean isPalindrome(ListNode head) {
        // 1.计算链表长度
        int len = 0;
        for (ListNode p = head; p != null; p = p.next) len++;
		
        // 2.将链表前一半倒序，反转链表的模板
        ListNode cur = head, pre = null;
        for (int i = 0; i < len / 2; i++) {
            ListNode tmp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = tmp;
        }
		
    	// 注：若长度是奇数，则要将cur后移一位再比较
        if (len % 2 == 1) cur = cur.next;
    	// 3.从中间开始同时遍历前一半与后一半，比较val
        for (; pre != null && cur != null; pre = pre.next, cur = cur.next) 
            if (pre.val != cur.val)
                return false;

        return true;
    }
```
