二叉查找树（BST）：在树中的任意一个节点，其左子树中的每个节点的值，都要小于这个节点的值，而右子树节点的值都大于这个节点的值。
```java
// 基本结构
public class BinarySearchTree {
	
    class Node {
        int item;
        Node left;
        Node right;

        public Node(int item) {
            this.item = item;
        }
    }
	// 标记根节点，相当于树的标识
    private Node root;

	// 方法如下...
}
```
这里注意一点，若要自定义泛型，则需要传入自定义类型的比较器Comparator或者实现Comparable接口。

## 1.增加
新增的逻辑其实很简单，具体如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201005235747769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```java
public void insert(int item) {
		// 若此时树为空，则将当前节点作为root
        if (root == null) {
            root = new Node(item);
            return;
        }
		
		// p辅助遍历，初始值为root
        Node p = root;
        while (p != null) {
        	// 要插入的值大于当前节点值，插入到右边
            if (item > p.item) {
                // 找到了空位，插入
                if (p.right == null) {
                    p.right = new Node(item);
                    return;
                }
                // 没有空位，继续右移
                p = p.right;
            // 要插入的值小于当前节点值，插入到左边
            }else {
                if (p.left == null) {
                    p.left = new Node(item);
                    return;
                }
                p = p.left;
            }
        }
}
```
## 2.删除
删除还是有点复杂的，可以分为三种情况，具体如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020100600223140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```java
public void remove(int item) {
        Node p = root，pp = null;
	    // 寻找要删除的节点p，以及其父节点pp
        while (p != null) {
            if (item == p.item) break;
            else if (item < p.item) {
                pp = p;
                p = p.left;
            }else {
                pp = p;
                p = p.right;
            }
        }
    	// 没找到指定item的节点，返回
        if (p == null) return;
		
    	// 情况1：要删除的节点有左右节点
        if (p.left != null && p.right != null) {
            Node minP = p.right，minPP = p;
            // 寻找右子树最小节点minP，及其父节点minPP
            while (minP != null) {
                minPP = minP;
                minP = minP.left;
            }
            // 交换minP与p的值
            p.item = minP.item;
            // 改变pp和p指向，将问题转化为删除minP（即删除一个叶子节点）
            pp = minPP;
            p = minP;
        }
		   	
        Node child;
		// 情况2：要删除的节点有一个子节点
        if (p.left != null) child = p.left;
        else if (p.right != null) child = p.right;
    	// 情况3：要删除的节点是叶子节点
    	else child = null;
	
    	// 执行删除操作
    	// 一种特殊情况：要删除的是根节点，且该树没有右子树，此时pp=null
        if (pp == root) {
            root = child;
        }
        if (p == pp.left) {
            pp.left = child;
        }else {
            pp.right = child;
        }
}
```

注：还有一种简便的办法，就是不真正删除那个节点，而是将要删除的节点的状态（这需要node里面有个state字段）置为deleted，就可以在形式上删除该节点。

## 3.查找
查找操作也很简单，具体如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006002739346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```java
public Node find(int item) {
        Node p = root;
        while (p != null) {
            if (p.item == item){
                return p;
            }else if (item < p.item) {
                p = p.left;
            }else {
                p = p.right;
            }
        }
        return null;
}
```
## 4.遍历
前序遍历，中序遍历，后序遍历都可以实现遍历，其实也都是死模板。这里注意一点，中序遍历（左->根->右），可以得到树内元素的**有序序列**，时间复杂度O(n)。
```java
public void inOrder() {
        Node p = root;
        inOrder(p);
}

private void inOrder(Node root) {
        if (root == null) return;
        inOrder(root.left);
        System.out.println(root.item);
        inOrder(root.right);
}
```
除了上面基本的CRUD外，还可以有其他的操作，这里就不一一实现了...
* 支持重复数据的二叉查找树
* 快速地查找大节点和小节点、前驱节点和后继节点

## 5.与散列表对比
首先我们来看看二叉查找树CRUD的时间复杂度，具体如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006003448395.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
可以看到，二叉查找树在比较平衡的情况下，插入、删除、查找操作时间复杂度才是 O(logn)。而散列表的插入、删除、查找操作的时间复杂度可以做到常量级的 O(1)，非常高效。那我们为什么还要用二叉查找树呢？

* 散列表中的数据是无序存储的，如果要**输出有序的数据**，需要先进行排序。而对于二叉查找树来说，我们只需要中序遍历，就可以在 O(n) 的时间复杂度内，输出有序的数据序列。
* 散列表**扩容耗时很多**，而且当遇到散列冲突时，性能不稳定，尽管二叉查找树的性能不稳定，但是在工程中，我们常用的平衡二叉查找树的性能非常稳定，时间复杂度稳定在 O(logn)。
* 笼统地来说，尽管散列表的查找等操作的时间复杂度是常量级的，但因为**哈希冲突**的 存在，这个常量不一定比 logn 小，所以实际的查找速度可能不一定比 O(logn) 快。加上哈希函数的耗时，也不一定就比平衡二叉查找树的效率高。
* 散列表的**构造比二叉查找树要复杂**，需要考虑的东西很多。比如散列函数的设计、冲突解决办法、扩容、缩容等。平衡二叉查找树只需要考虑平衡性这一个问题，而且这个问题 的解决方案比较成熟、固定。
* 为了避免过多的散列冲突，**散列表装载因子不能太大**，特别是基于开放寻址法解决冲突的散列表，不然会浪费一定的存储空间。

综合这几点，平衡二叉查找树在某些方面还是优于散列表的，所以，这两者的存在并不冲突。我们在实际的开发过程中，需要结合具体的需求来选择使用哪一个。

