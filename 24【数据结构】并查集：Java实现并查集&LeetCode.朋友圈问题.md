并查集是一种树形的数据结构，顾名思义，它用于处理一些不交集的 **合并** 及 **查询** 问题。 它支持两种操作：
* 查找（Find）：确定某个元素处于哪个子集，或者判断某一元素是否属于当前集合
* 合并（Union）：将两个子集合并成一个集合

下面我们就来看看如何用Java实现并查集...

## 1.初始化
```java
public class UnionFind{
    // 记录有多少个小集合
    private int count = 0;
    // 元素 i 的父元素（有点桶排序的意思，即下标有实际意义）
    private int[] parent;
    
    // 初始化并查集，即构建子集们。这里传入的参数 n 表示size，即有多少个单独的子集
    // 注：在应用时，并查集只是用于处理集合间关系，并不具体保存集合内容
    public UnionFind(int n) {
        this.count = n;
        parent = new int[n];
        // 当前所有元素都是一个集合
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    //...
}
```
## 2.find：查找
通俗地讲一个故事：几个家族进行宴会，但是家族普遍长寿，所以人数众多。由于长时间的分离以及年龄的增长，这些人逐渐忘掉了自己的亲人，只记得自己的爸爸是谁了，而最长者（称为「祖先」）的父亲已经去世，他只知道自己是祖先。为了确定自己是哪个家族，他们想出了一个办法，只要问自己的爸爸是不是祖先，一层一层的向上问，直到问到祖先。如果要判断两人是否在同一家族，只要看两人的祖先是不是同一人就可以了。
```java
 	// 寻找指定元素的根元素
    public int find(int p) {
        // 根元素的特征：元素 = parent【元素】
        while (p != parent[p]) {
            // 路径压缩，让p的父元素升级，因为当前元素（parent[p]）可能只是p的一级父而已
            parent[p] = parent[parent[p]];
            // 改变p，继续向上寻找
            p = parent[p];
        }
        return p;
    }
```
只不断寻找父亲就好了，为什么还要有路径压缩压缩那行代码呢？因为我们使用了太多没用的信息，我的祖先是谁与我父亲是谁没什么关系，这样一层一层找太浪费时间，不如我直接当祖先的儿子，问一次就可以出结果了。甚至祖先是谁都无所谓，只要这个人可以代表我们家族就能得到想要的效果。 把在路径上的每个节点都直接连接到根上 ，这就是路径压缩
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006172120203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
## 3.union：合并
宴会上，一个家族的祖先突然对另一个家族说：我们两个家族交情这么好，不如合成一家好了。另一个家族也欣然接受了。我们之前说过，并不在意祖先究竟是谁，所以只要其中一个祖先变成另一个祖先的儿子就可以了。
```java
	// 元素合并，实际就是将两个元素所处集合合并，而 p，q就是连接通道
    public void union(int p, int q) {
        // 寻找到这两个元素的根元素
		int rootP = find(p);
        int rootQ = find(q);
        
        // 已经在同一个集合了，返回
        if (rootP == rootQ) return;
        // 否则，将任一个集合拜到另一个集合下
        parent[rootQ] = rootP;
        // 合并了，集合数就-1
        count--；
    }
```
## 4.完整代码
```java
public class UnionFind{
    // 记录有多少个小集合
    private int count = 0;
    // 元素 i 的父元素（有点桶排序的意思，即下标有实际意义）
    private int[] parent;
    
    // 初始化并查集，即构建子集们。这里传入的元素 n 表示size，即有多少个要构建多少个子集
    // 注：在应用时，并查集只是用于处理集合间关系，并不具体保存集合内容
    public UnionFind(int n) {
        this.count = n;
        parent = new int[n];
        // 当前所有元素都是一个集合
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    // 2.寻找指定元素的根元素
    public int find(int p) {
        // 根元素的特征：元素 = parent【元素】
        while (p != parent[p]) {
            // 路径压缩，让p的父元素升级，因为当前元素（parent[p]）可能只是p的一级父而已
            parent[p] = parent[parent[p]];
            p = parent[p];
        }
        return p;
    }
    
    // 3.元素合并，实际就是将两个元素所处集合合并，而 p，q就是连接通道
    public void union(int p, int q) {
        // 寻找到这两个元素的根元素
		int rootP = find(p);
         int rootQ = find(q);
        
        // 已经在同一个集合了，返回
         if (rootP == rootQ) return;
        // 否则，将任一个集合拜到另一个集合下
         parent[rootQ] = rootP;
        // 合并了，集合数就-1
         count--；
    }
}
```

## LeetCode题示例：[547. 朋友圈](https://leetcode-cn.com/problems/friend-circles/)
班上有 **N** 名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友。所谓的朋友圈，是指所有朋友的集合。

给定一个 **N \* N** 的矩阵 **M**，表示班级中学生之间的朋友关系。如果M[i][j] = 1，表示已知第 i 个和 j 个学生**互为**朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。

**示例 1:**

```
输入: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
输出: 2 
说明：已知学生0和学生1互为朋友，他们在一个朋友圈。
第2个学生自己在一个朋友圈。所以返回2。
```

**示例 2:**

```
输入: 
[[1,1,0],
 [1,1,1],
 [0,1,1]]
输出: 1
说明：已知学生0和学生1互为朋友，学生1和学生2互为朋友，所以学生0和学生2也是朋友，所以他们三个在一个朋友圈，返回1。
```

**注意：**

1. N 在[1,200]的范围内。
2. 对于所有学生，有M[i][i] = 1。
3. 如果有M[i][j] = 1，则有M[j][i] = 1。

**解法：并查集**
* 思路：这个题其实与岛屿问题一样，方案有二：
  1. DFS
  2. 并查集，每个学生都可以看做一个单独的集合，然后通过并查集去处理集合间关系（这里主要是合并）
* 复杂度
  * Time：O（n^3），访问整个矩阵一次，并查集操作需要最坏 O(n)O(n) 的时间
  * Space：O（n），并查集中需要一个一维数组来保存父元素

```java
class Solution {
    // 实现一个并查集
    class UnionFind {
        private int count;
        private int[] parent;

        public UnionFind(int n) {
            this.count = n;
            this.parent = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
            }
        }

        int find(int p) {
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];
                p = parent[p];
            }
            return p;
        }
        void union (int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;
            parent[rootP] = rootQ;
                count--;
        }
        int count() {
            return this.count;
        }
    }
	
    // 具体使用并查集
    public int findCircleNum(int[][] M) {
        int n = M.length;
        // 1.创建并查集，这里是初始化n（总人数）个子集，表示每个人都是一个单独的子集
        UnionFind uf = new UnionFind(n);
        for (int i = 0; i < n - 1; i++) 
            for (int j = i + 1; j < n; j++) 
                // 2.把有关系的i，j合并
                if (M[i][j] == 1)
                    uf.union(i, j);
        // 3.返回集合数
        return uf.count();
    }
}
```

