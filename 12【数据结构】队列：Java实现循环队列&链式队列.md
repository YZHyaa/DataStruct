## 1.顺序队列(循环)
基于数组的循环队列其实很简单，就是当数组满后重置**入队**和**出队**位置到数组头部。
```java
public class CircleQueue<E> {
	
    // Object保存E，因为E不能E[] --> (E)obj
    private Object[] items;
    private int putIdx; // 实际入队位置
    private int takeIdx; // 实际出队位置
    private int count; // 队列中元素打小，注：队列的容量在数组初始化后就不再改变
	
	// 指定容量初始化
    public CircleQueue(int size) {
        this.items = new Object[size];
        this.count = 0;
        this.putIdx = 0;
        this.takeIdx = 0;
    }
	
    public boolean enqueue(E e) {
        // 满了就返回
        if (count == items.length)  return false;
        // 放入
        items[putIdx] = e;
        // 在放入后更新下一次入队的位置putIdx（++）
        // 若putIdx到了数组最后一个元素，就归0
        if (++putIdx == items.length) putIdx = 0;
        count++;

        return true;
    }

    public E dequeue() {
    	// 队列为空返回null
        if (count == 0) return null;
        E res = (E) items[takeIdx];
        // 在取出后，更新下一次取出位置takeIdx（++）
        // 若takeIdx到了数组最后一个元素，就归0
        if (++takeIdx == items.length) takeIdx = 0;
        count--;
        return res;
    }

    public E peek() {
    	// 注意要非空判断
        return count == 0 ? null : (E)items[takeIdx];
    }
}
```

## 2.链式队列
在看代码之前，先注意几个非空判断：

* 可以判断队列为空的条件：
	* head = null 
	* tail = null
	* head = tail = null 
* 入队/出队时需要的非空判断：
  * enqueue（入队）：若为空 tail = head = node
  * dequeue（出队）
    * 出队前空：return null
    * 出队后空：tail = head = null

```java
public class LinkedQueue<E> {

    class Node<E> {
        E item;
        Node<E> next;

        public Node(E e) {
            this.item = e;
        }
    }

    private Node head; // 队首
    private Node tail; // 队尾

    public LinkedQueue() {
    	// 队列为空时 head = tail = null
        head = tail = null; 
    }
	
    public void enqueue(E e) {
        Node node = new Node(e);
		
        // 队列为空
        if(tail == null) {
            head = tail = node;
        }else {
            tail.next = node;
            tail = node;
        }
    }

    public E dequeue() {
        // 出队前为空
        if (head == null) {
            return null;
        }
        E item = (E)head.item;
        // 出队后空
        if (head.next == null) {
            head = tail = null;
        }
        return item;
    }

    public E peek() {
        return this.head == null ? null : (E)this.head.item;
    }
}
```
从上面代码不难看出，链式队列其实就是**链表的基本操作**，所以LinkedList也是Queue的一个实现，这块的更多细节可以参考[【Java容器源码】LinkedList源码分析](https://blog.csdn.net/weixin_43935927/article/details/108501190)。
