>在文章开头我们先放两个链接，因为在实现LRU时会直接复用Java提供的容器：
>* [【Java容器源码】LinkedList源码分析](https://blog.csdn.net/weixin_43935927/article/details/108501190)...
>* [【Java容器源码】LinkedHashMap源码分析](https://blog.csdn.net/weixin_43935927/article/details/108512608)...
## 1.单链表实现LRU（O(n)）

单向链表，从head出，tail进入（理解成队列，左进（tail）右出（head）。
```
* 查找:
  1.查找：遍历得到这个数据对应的结点
  2.移动：将其从原来的位置删除，然后再插入到链表的尾部
* 添加：查找 + （移动/删除）
  - 缓存中已存在：移动到队尾
  - 缓存中不存在：
    - 缓存未满，则将此结点直接插入到链表的尾部
    - 缓存已满，则链表**头结点删除**，将新的数据结点插入链表的尾部
* 删除:
  1.查找：遍历找到要删除节点
  2.删除：用前驱节点删除
```
>这三个操作都要涉及“查找”操作。所以，如果单纯地采用链表的话，时间复杂度只能是 **O(n)**。

```java
// 直接继承Java的LinkedList，就不用再自己实现链表
public class LinkedLRU extends LinkedList<String> {
	
    private int capcity;

    public LinkedLRU (int capcity) {
        this.capcity = capcity;
    }
    
    @Override
    public boolean add(String str) {
    	// 如果队列中包含要添加元素
    	if (contains(str)) {
    		// 删除，然后再插入到队尾
            remove(str);
        // 如果队列中不包含要添加元素
        } else {
        	// 如果队列已经满乐，就将元素队首删除
            if (size() == capcity) {
                removeFirst();
            }
        }
        // 尾插
        return super.add(str);
    }
	
    @Override
    public String get(int index) {
    	// 查出结果
        String item = super.get(index);
        // 将当前元素删除
        remove(index);
        // 然后再插入到尾部
        add(item);
        return item;
    }
}
```

测试如下：
```java
public class TestLRU {

    public static void main(String[] args) {
    	// 设置容量大小为3，并加入3个元素
        LinkedLRU lru = new LinkedLRU(3);
        lru.add("a");
        lru.add("b");
        lru.add("c");
        System.out.println("还未做任何操作：" + lru);
		// 访问其中一个元素
		// 按照lru的逻辑，被访问的元素要被挪动到队尾
        lru.get(0);
        System.out.println("访问已存在元素(a)：" + lru);
        // 添加一个缓存中已经有了的元素
        // 按照lru的逻辑，该元素会被挪到队尾
        lru.add("b");
        System.out.println("添加一个已存在的元素(b)：" + lru);
		// 添加一个缓存没有的元素，但此时队列已经满了
		// 按照lru的逻辑，队首的元素会被删除
        lru.add("d");
        System.out.println("超过容量添加一个元素：" + lru);
    }
}
```
结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201122214503923.png#pic_center)



## 2.散列表 + 双向链表（O(1)）
既然单纯使用链表实现LRU的时间复杂时O(n），那有什么办法优化吗？可以的，因为主要时间都浪费在查找上了，所以我们可以在链表的基础上再加上哈希表，因为哈希表的查询时间复杂度是O(1)，可以极大程度上优化查询时间。

结构图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201122212349377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
```
* 查找：
  1.查找：散列表中查找数据的时间复杂度接近 O(1)，所以通过散列表，我们可以很快地在缓存中找到一个数据。
  2.移动：当找到数据之后，我们还需要将它移动到双向链表的尾部。
* 添加：找 + （移动/删除）
  - 缓存中存在：将此节点移动到双向链表的尾部；
  - 缓存中不存在：
    - 缓存已满：将双向链表头部的结点删除，然后再将数据放到链表的尾部；
    - 缓存未满：直接将数据新添到链表的尾部。
* 删除：
  1.查找：借助散列表，我们可以在 O(1) 时间复杂度里找到要删除的结点。
  2.删除：因为我们的链表是双向链表， 双向链表可以通过前驱指针 O(1) 时间复杂度获取前驱结点，所以在双向链表中，删除结点 只需要 O(1) 的时间复杂度。
```
>这整个过程涉及的查找操作都可以通过散列表来完成。所以其他的操作，比如删除头结点、链表尾部插入数据等，都可以在 O(1) 的时间复杂度内完成。所以，这三个操作的时间复杂度都 是 **O(1)**。

```java
// 通过LinkedListMap来实现（Map + List）
class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        /*
        	if ((v = get(k)) == null)  return -1;
        	else return v;
        */
        // 没找到就返回默认
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
    	// put方法实际上是调用HashMap的，但是LinkedHashMap重写了afterNodeAccess方法，所以无论要添加元素是否存在，都会被移到队尾
        super.put(key, value);
    }
	
    // 重写removeEldestEntry来定义什么时候进行删除操作
    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}
```

至此，实现了一个高效的、支持 LRU 缓存淘汰算法的缓存系统原型。



