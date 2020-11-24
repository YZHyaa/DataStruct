## [226. 翻转二叉树¹](https://leetcode-cn.com/problems/invert-binary-tree/)
翻转一棵二叉树。

**示例：**

输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

### 解法一：递归
* 思路：只是宏观的去想，去交换两个节点
* 复杂度：
	* Time：O(n)，会遍历二叉树的每一个节点
	* Space：O(n)，使用的空间由递归栈的深度决定，它等于当前节点在二叉树中的高度
		* 在平均情况下，二叉树的高度与节点个数为对数关系，即 O(\log n)
		* 最坏情况下，树形成链状，空间复杂度为 O(n)


```java
public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;
        invertTree(root.left);
        invertTree(root.right);
        return root;
}
```

### 解法二：BFS

* 思路：不用递归，但是要向两边遍历，所以要有一个数据结构（队列）作为中间量，所有节点都存进去，然后取出交换

* 复杂度：
	* Time：O(n)，需要遍历每个节点
	* Space：O(n)，队列

```java
public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            TreeNode tmp = node.left;
            node.left = node.right;
            node.right = tmp;
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        return root;
}
```

##  [104.二叉树的最大深度¹](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明:** 叶子节点是指没有子节点的节点。

**示例：**
给定二叉树 `[3,9,20,null,null,15,7]`，

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

### 解法一：递归

* 思路：本题需要遍历树的所有结点，因此就有递归与迭代两种想法
  * 递推公式：f(root) = max( f(root.left), f(root.right)) + 1。结束条件：root == null

* 复杂度
  * Time：O(n)   
  * Space：O(n)

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```

### 解法二：BFS

* 思路：层序遍历，每遍历一次depth 加一
* 复杂度
  * Time：O(n)    
  * Space：O(n)

```java
 public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
     	// depth=0
        int depth = 0;
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            depth++;
        }
        return depth;
}
```

##  [111.二叉树的最小深度¹](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**说明:** 叶子节点是指没有子节点的节点。

**示例:**

给定二叉树 `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最小深度  2.

### 解法一：递归 

* 思路：递归

  * 递推公式：**f(root)  = f(left) || f(right) || Math.min(f(left),f(right)) + 1** 。结束条件：root == null

  * 注意：若f(root)  = Math.min(f(left),f(right)) + 1，那么对于[1,2] ，h（left）=1，h（right）=0，最终结果就是0+1=1，错误

     ```
        1
       / 
      2
     ```

* 复杂度

  * Time：O(n)
  * Space：O(n)

```java
public int minDepth(TreeNode root) {
        if (root == null)	return 0;
	    if (root.left == null)	return minDepth(root.right) + 1;
	    else if (root.right == null) return minDepth(root.left) + 1;
	    else return Math.min(minDepth(root.left),minDepth(root.right)) + 1;
}
```

### 解法二：BFS

* 思路：层序遍历，到叶子节点了就停止遍历
  * 注意：初始depth = 1，因为叶子节点那层直接就返回了，没有++
* 复杂度
  - Time：O(n)
  - Space：O(n)

```java
public int minDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
    	// 这里depth=1，可以假设只有根节点，也要返回深度1
        int depth = 1;
        
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                // 已经是叶子节点了，返回depth
                if (node.left == null && node.right == null) return depth;
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            depth++;
        }
        return 1;
}
```


