## 1.基本概念
### 1.1 节点
这里放一张图，我们对着这张图来介绍：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005215556877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
* A 节点就是 B 节点的**父节点**，B 节点是 A 节点的子节点
* B、C、D 这三 个节点的父节点是同一个节点，所以它们之间互称为**兄弟节点**
* 我们把没有父节点的节点叫 作**根节点**，也就是图中的节点 E
* 我们把没有子节点的节点叫作**叶子节**点或者叶节点，比如 图中的 G、H、I、J、K、L 都是叶子节点

### 1.2 高 / 深度
* 节点的高度：节点到叶子节点的最长路径（边数）
  树的高度：根节点的高度
* 节点的深度：根节点到这个节点所经历的边数个数
* 节点的层数：节点的深度+1
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005222121916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)


## 2.二叉树分类
### 2.1 满二叉树
定义：叶子节点全都在最底层，除了叶子节点之外，每个节点都有左 右两个子节点，这种二叉树就叫作满二叉树

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005222424516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
### 2.2 完全二叉树
定义：叶子节点全都在最底层，除了叶子节点之外，每个节点都有左 右两个子节点，这种二叉树就叫作满二叉树

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005223331714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
完全二叉树说白了，就是按顺序一层一层，从左到右铺下来...

## 3.二叉树存储
### 3.1 链式存储
任何树都可以
  * 二叉树：Node left ，right
  * 三叉树：Node left，down，right
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005224648647.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
### 3.2 顺序存储
如果节点 X 存储在数组中下标为 i 的位置
  * 下标为 2 * i 的位置存储的就是左子节点
  * 下标为 2 * i + 1 的位置存储的就是右子节点
*   下标为 i/2 的位置存储 就是它的父节点

通过这种方式，我们只要知道根节点存储的位置（一般情况下，为了方便 计算子节点，根节点会存储在下标为 1 的位置），这样就可以通过下标计算，把整棵树都串起来。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005225134249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
## 4.深度优先遍历
在采用深度优先遍历时，二叉树的遍历一般分为三种：前序遍历，中序遍历，后序遍历。这个前中后怎么理解呢？**前中后其实是对于每棵(子)树而言的输出顺序**，过程如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/202010052254160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
因此后序遍历最复杂，因为不能直接打印或者左子树遍历完就打印，而是需要先把左右树都遍历完再打印。具体的代码实现有两种方式：
  * 递归：内部维护了一个调用栈，易理解，易编码
  * 迭代：需手动维护一个栈，控制其出入栈

时间复杂度为 O(n) ，因为操作次数与节点个数成正比。
### 4.1 先序遍历
```
前序遍历的递推公式： preOrder(r) = print r->preOrder(r->left)->preOrder(r->right)
```

```java
void preOrder(Node root) {
    if (root == null) return;
    
    System.out.println(root);
    preOrder(root.left);
    preOrder(root.right);
}
```

```java
void preOrder(Node root) {
    Stack<Node> stack = new Stack<>();
    while (root != null || !stack.isEmpty()) {
        while (root != null) {
            System.out.println(root);
            stack.push(root);
            root = root.left;
        }
        root = stack.pop().right;
    }
}
```

### 4.2 中序遍历
```
中序遍历的递推公式： inOrder(r) = inOrder(r->left)->print r->inOrder(r->right)
```

````java
void inOrder(Node root) {
    if (root == null) return;
    
    inOrder(root.left);
    System.out.println(root);
    inOrder(root.right);
}
````

```java
void inOrder(Node root) {
    Stack<Node> stack = new Stack<>();
    // 这里判断了，不用提前判断root==null
    while (root != null || !stack.isEmpty()) {
        // 遍历完当前节点所有左子树
        while (root != null) {
            // 先push
		   stack.push(root);
            root = root.left;
        }
        // 栈顶元素，注：这里是root
	    root = stack.pop();
        System.out.Println(root);
        // 去其右节点 
        root = root.right;
	}
}
```

### 4.3 后序遍历
```
后序遍历的递推公式： postOrder(r) = postOrder(r->left)->postOrder(r->right)->print r
```

```java
void postOrder(Node root) {
    if (root == null) return;
    
    postOrder(root.left);
    postOrder(root.right);
    System.out.println(root);
}
```
```java
public List<Integer> postOrder(TreeNode root) {

    List<Integer> res = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
		
    stack.push(root);
    while(!stack.empty()){
        root = stack.pop();
        res.add(0, root.val); // 头插
        if(root.left != null) stack.push(root.left);
        if(root.right != null) stack.push(root.right);
    }
    return res;
}
```
## 5.广度优先遍历
广度优先遍历，可以通俗的理解成从上到下一层一层的遍历
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005232440716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
### 5.1 点序遍历
在代码实现时需要借助一个队列，通过它的FIFO特性，最后将先遍历的节点先打印。
```java
public void levelOrder(Node root) {、
    // 需要提前做非空判断
    if (root == null) return root;
    Queue<Node> queue = new LinkedList<>();
    // 与DFS相比，需要先将根节点入队
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        Node node = queue.poll();
        System.out.Println(node);
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
}
```

### 5.2 层序遍历
除了上面点序遍历逐层挨个打印节点外，还可以将遍历结果划分出层次，比如：
```
				1
			   / \
			  2   3     ===> [[1],[2,3],[4,5,6]]
			 / \  /
		    4	5 6
```
```java
public List<List<Integer>> levelOrder(TreeNode root) {

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        List<List<Integer>> res = new ArrayList<>();

        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res.add(list);
        }
        return res;
    }
```
## 6.N叉树遍历
N叉树与二叉树区别就是子节点的个数不同，下面是N叉树的Node定义：
```java
// N叉树定义node
class Node {
    public int val;
    public List<Node> children; // 它不再是rigth和left，而是一个保存了子节点的list

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
}
```
所以，N叉树获取子节点的方式也会有所不同：
- 二叉树：root.left + root.right
- n叉树：for (Node n : node.children) n 
### 6.1 深度优先遍历
```java
public void order(Node root) {
    if (root == null) return;
    System.out.Println(root);
    for (Node node : root.children)
    	order(node);
}
```

### 6.2 广度优先遍历
```java
public void order(Node root) {
    if (root == null) return root;
    Queue<Node> queue = new LinkedList<>();
    queue.add(root);
    
    while (! queue.isEmpty()) {
        Node node = queue.poll();
       	System.out.println(node);
        for(Node n : node.children)
             queue.offer(n);  
    }
}
```



