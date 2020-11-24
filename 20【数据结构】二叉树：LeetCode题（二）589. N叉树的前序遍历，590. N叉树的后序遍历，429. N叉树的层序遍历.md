在上一篇 [二叉树的LeetCode题（一）：94. 二叉树的中序遍历，144. 二叉树的前序遍历，145. 二叉树的后序遍历，102. 二叉树的层序遍历](https://blog.csdn.net/weixin_43935927/article/details/108941855) 中我们看到了LeetCode中关于二叉树的基本问题，本篇我们就来看看N叉树的基本问题。
## [589. N叉树的前序遍历¹](https://leetcode-cn.com/problems/n-ary-tree-preorder-traversal/)

给定一个 N 叉树，返回其节点值的*前序遍历*。

例如，给定一个 `3叉树` :

 

![img](https://img-blog.csdnimg.cn/img_convert/5ac26bc1cec207a0bf63ddce393d6dc9.png)

 

返回其前序遍历: `[1,3,5,6,2,4]`。

### 解法一：递归
```java
class Solution {
    public List<Integer> preorder(Node root) {
        List<Integer> res = new ArrayList<>();
        dfs(root, res);
        return res;
    }

    public void dfs(Node root, List<Integer> res) {
        if(root == null) return;
        res.add(root.val);
        for(Node node : root.children) 
        	dfs(node,res);
    }
}
```

### 解法二：迭代*
```java
public List<Integer> preorder(Node root) {
        
        List<Integer> res = new ArrayList<>();
        Stack<Node> stack = new Stack<>();
        
        stack.add(root);
        while (!stack.empty()) {
            root = stack.pop();
            res.add(root.val);
            for (int i = root.children.size() - 1; i >= 0; i--)
                stack.add(root.children.get(i));
        }
        
        return res;
    }
```

## [590. N叉树的后序遍历¹](https://leetcode-cn.com/problems/n-ary-tree-postorder-traversal/)
给定一个 N 叉树，返回其节点值的*后序遍历*。

例如，给定一个 `3叉树` :

 

![img](https://img-blog.csdnimg.cn/img_convert/5ac26bc1cec207a0bf63ddce393d6dc9.png)

返回其后序遍历: `[5,6,3,2,4,1]`.

### 解法一：递归
```java
class Solution {
    public List<Integer> postorder(Node root) {
        List<Integer> res = new ArrayList<>();
        postOrder(root,res);
        return res;
    }

    private void postOrder(Node root,List<Integer> res) {
        if (root == null) return;
        for (Node node : root.children) postOrder(node,res);
        res.add(root.val);
    }
}
```

### 解法二：迭代*
```java
public List<Integer> postorder(Node root) {
        
        List<Integer> res = new ArrayList<>();
        Stack<Node> stack = new Stack<>();
        
        stack.add(root);
        while(!stack.isEmpty()) {
            root = stack.pop();
            res.add(root.val);
            for(Node node: root.children)
                stack.add(node);
        }
        Collections.reverse(list);
        return res;
    }
```
## [429. N叉树的层序遍历²](https://leetcode-cn.com/problems/n-ary-tree-level-order-traversal/)
给定一个 N 叉树，返回其节点值的*层序遍历*。 (即从左到右，逐层遍历)。

例如，给定一个 `3叉树` :

 

![img](https://img-blog.csdnimg.cn/img_convert/5ac26bc1cec207a0bf63ddce393d6dc9.png)



返回其层序遍历:

```
[
     [1],
     [3,2,4],
     [5,6]
]
```

### 解法：BFS
```java
public List<List<Integer>> levelOrder(Node root) {
        if (root == null) return new ArrayList<>();
        
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        List<List<Integer>> res = new ArrayList<>(); 

        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) { 
                Node node = queue.poll();
                list.add(node.val);
                if (node.children != null)
                    for (Node n : node.children) 
                        queue.offer(n);
            }
            res.add(list);
        }
        return res;
}
```
