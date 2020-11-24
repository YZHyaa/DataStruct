## [100. 相同的树¹](https://leetcode-cn.com/problems/same-tree/)

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

**示例 1:**

```
输入:       1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

输出: true
```

**示例 2:**

```
输入:      1          1
          /           \
         2             2

        [1,2],     [1,null,2]

输出: false
```

**示例 3:**

```
输入:       1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

输出: false
```

### 1.递归*
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        if (p.val != q.val) return false;
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
```

### 2.BFS
```java
// 双队列，没必要
// Time：O（n）， Space：O（n）
public boolean isSameTree(TreeNode p, TreeNode q) {
    	// 注意：这里的判断条件
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        Queue<TreeNode> queueP = new LinkedList<>();
        Queue<TreeNode> queueQ = new LinkedList<>();
        queueP.offer(p);
        queueQ.offer(q);

        while (!queueP.isEmpty() || !queueQ.isEmpty()) {
            TreeNode p1 = queueP.poll();
            TreeNode q1 = queueQ.poll();
            // 1.两个都空
            if (p1 == null && q1 == null) continue;
            // 2.一个空
            if (p1 == null || q1 == null) return false;
            // 3.两个都不空
            if (p1.val != q1.val ) return false;
			
            // 注：这里的入队顺序：同一队中先左后右
            queueP.offer(p1.left);
            queueP.offer(p1.right);
            queueQ.offer(q1.left);
            queueQ.offer(q1.right);
        }

        return true;
    }
```

```java
// Time：O（n）， Space：O（n）
public boolean isSameTree(TreeNode p, TreeNode q) {
        
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(p);
        queue.offer(q);

        while (!queue.isEmpty()) {
            // 一次出两个节点就行了
            TreeNode p1 = queue.poll();
            TreeNode q1 = queue.poll();
            
            if (p1 == null && q1 == null) continue;
            if (p1 == null || q1 == null) return false;
            if (p1.val != q1.val ) return false;
			
            // 注：这里的入队顺序：先左后右
            queue.offer(p1.left);
            queue.offer(q1.left);
            queue.offer(p1.right);
            queue.offer(q1.right);
        }

        return true;
    }
```
另外，Queue换成stack用迭代式的深度优先也可


## [98. 验证二叉搜索树²](https://leetcode-cn.com/problems/validate-binary-search-tree/)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含**小于**当前节点的数。
- 节点的右子树只包含**大于**当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

**示例 1:**

```
输入:
    2
   / \
  1   3
输出: true
```

**示例 2:**

```
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```

### 解法一：中序遍历

* 思路：二分搜索树中序遍历是，从小到大有序的
  * 通过迭代形式，对二叉树遍历然后依次前后比较，当前每个节点的值都要大于之前遍历一个节点的值
  * 注意：保存前一个值的变量，int会发生溢出，因此设为double且初始化为-MAX
* 复杂度
  * Time：O（n）
  * Space：O（n）

```java
public boolean isValidBST(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        double inOrder = -Double.MAX_VALUE;

        while (root != null || !stack.isEmpty()) {
            while (root != null) {
                stack.push(root);
                root = root.left;
            }
            root = stack.pop();
            if (root.val > inOrder) {
                inOrder = root.val;
                root = root.right;
            }else 
                return false;
        }
        return true;
}
```

### 解法二：递归
```java
public boolean isValidBST(TreeNode root) {
    return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
    if (root == null)
        return true;
    // 每个节点如果超过这个范围，直接返回false
    if (root.val >= maxVal || root.val <= minVal)
        return false;
    // 这里再分别以左右两个子节点分别判断，
    // 左子树范围的最小值是minVal，最大值是当前节点的值，也就是root的值，因为左子树的值要比当前节点小
    // 右子数范围的最大值是maxVal，最小值是当前节点的值，也就是root的值，因为右子树的值要比当前节点大
    return isValidBST(root.left, minVal, root.val) && isValidBST(root.right, root.val, maxVal);
}
```

## [28. 对称的二叉树¹](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
```
    1
   / \
  2   2
   \   \
   3    3
```

**示例 1：**

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例 2：**

```
输入：root = [1,2,2,null,3,null,3]
输出：false
```

**限制：**

`0 <= 节点个数 <= 1000`

注意：本题与主站 101 题相同：<https://leetcode-cn.com/problems/symmetric-tree/>

### 解法一：递归*

* 思路：明确对称特征，使用递归（只宏观去想，不要深入递归树）
  * 根节点：为空时对称
  * 第二级节点：必须相同
  * 第 >= 三级节点：left.left = right.right && left.right = right.left
```java
// Time：O（n）， Space：O（n）
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isSymmetric(root.left, root.right);
    }

    private boolean isSymmetric(TreeNode s, TreeNode t) {
        if (s != null && t != null) 
            return s.val == t.val && isSymmetric(s.left, t.right) && isSymmetric(s.right, t.left);
        else 
            return s == null && t == null;
    }
}
```

### 解法二：迭代（栈）

迭代借助栈实现更好理解一点

```java
// Time：O（n）， Space：O（n）
public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root.left);
        stack.push(root.right);

        while (!stack.isEmpty()) {
            TreeNode r = stack.pop(), l = stack.pop();
            // 1.两个节点都为空，则当前节点对称
            if (r == null && l == null) continue;
            // 2.一个节点为空，部队称
            if (r == null || l == null) return false;
            // 3.两个节点都非空，判断这两个节点是否相同
            // 3.1 不同则不对称
            if (r.val != l.val) return false;
			
            // 3.2 相同，再按序入栈（这里的顺序必要一边left，一边right）
            stack.push(r.left);
            stack.push(l.right);
            stack.push(r.right);
            stack.push(l.left);
        }
        return true;
    }
```




