文章开头先放一个传送门[【Java容器源码】LinkedList源码分析](https://blog.csdn.net/weixin_43935927/article/details/108501190)，是作者写的关于LInkedList源码的分析。这篇文章就仿写LinkedList，并实现容器中的核心方法。
## 1.Node
在LinkedList容器中，底层是通过双向链表实现的，所以有next与prev两个指针
```java
public class MyLinkedList<E> {

    // 容器是有泛型的，Node也应该有泛型
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        // 注：LinkedList中的Node构造器是构造时就要传入prev和next
        public Node(Node<E> prev, E element, Node<E> next) {
            this.prev = prev;
            this.item = element;
            this.next = next;
        }
    }

    Node<E> first; // 记录头结点。注：这里并没有为数据null的哨兵节点head
    Node<E> last; // 记录尾结点
    int size = 0;
    int modCount = 0; // 记录操作数，用作线程安全

	// 这里就只保留了空参构造
    public MyLinkedList() { 
    }
}
```
## 2.尾插-add
```java
	/**
     * 尾插,核心逻辑如下：
     * newNode.pre = last
     * last.next = newNode
     * last = newNode
     */
    public boolean add(E e) {
        Node newNode = new Node(last, e, null);
        if (last == null) {
            first = newNode;
        } else {
            last.next = newNode;
        }
        last = newNode;

        size++; // 注意，size++不能忘
        return true;
    }
```
另外，因为LinkedList还实现了Queue接口，所以还会实现offer方法。从LinkedList源码得知，offer方法只是简单调用add方法而已
```java
	/**
     * 同add，直接调用即可
     * @param e
     * @return
     */
    public boolean offer(E e) {
        return add(e);
    }
```
## 3.头插-addFirst
```java
	/**
     * 头插,核心逻辑如下：
     * newNode.next = first;
     * first.prev = newNode;  注：考虑first=null（链表为空，只用更新last即可）
     * first = newNode;
     */
    public boolean addFirst(E e) {
        Node newNode = new Node(null, e, first);
        if (first == null) {
            last = newNode;
        } else {
            first.prev = newNode;
        }
        first = newNode;

        size++; // size++勿忘
        return true;
    }
```
## 4.删除指定元素-remove
```java
 	/**
     * 删除指定元素
     * 1.找到要删除元素的位置
     * 2.o.pre = o.next（双链表，删除方便）
     */
    public boolean remove(Object o) {
        Node tmp = first;
        while (tmp != null) {
            if (tmp.item.equals(o)) {
                unlink(tmp);
                return true;
            }
        }
        return false; // 链表为空，或无要删除元素
    }
```
真正执行删除的逻辑在**unlink**方法中：
```java
	/**
     * 执行删除
     * x.prev.next = x.next        注：要考虑x.prev==null(x是first，直接更新first）
     * x.next.prev = x.prev.prev   注：要考虑x.next==null(x是last，直接更新last）
     */
    private E unlink(Node<E> x) {
        E element = x.item;
        Node prev = x.prev;
        Node<E> next = x.next;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;

        size--;
        return  element;
    }
```
## 5.删除头(队首)节点-removeFirst
同理，还是因为LinkedList实现了Queue接口，所以也要提供删除队首元素的方法。注意一点，这里的remove是空参，而删除指定元素时需要传入一个Object作为参数。
```java
	/**
     * 删除头节点，链表为空时抛出异常
     * first.next.pre = null;    注：考虑first=null（链表为空）， first.next=null（尾结点，即链表只有一个节点）
     * first = first.next;
     */
    public boolean remove() {
        if (first == null) {
            throw new RuntimeException("List is Empty");
        }
        return removeFirst();
    }
```
具体的删除逻辑在**removeFirst**方法中
```java
	public boolean removeFirst() {
        if (first.next == null) {
            last = null;
        } else {
            first.next.prev = null;
        }
        first = first.next;

        size--;
        return true;
    }
```
删除队首的另一个方法**poll**的实现如下，与remove的区别就是如果链表为空返回null，而不是抛异常
```java
	/**
     *删除头节点，链表为空时返回null
     * @param o
     * @return
     */
    public boolean poll(Object o) {
        if (first == null) {
            return false;
        }
        return removeFirst();
    }
```
## 6.根据索引查找元素-get
```java
	/**
     * 根据索引查找元素。由于是双链表，所以可以分成前后两部分进行查找
     * 1.在前半部分，就从前向后遍历
     * 2.在后半部分，从后向前遍历
     */
    public E get(int index) {
        if (index >= size || index < 0) {
            throw new IllegalArgumentException("Index Out Of Range");
        }
        return node(index).item;
    }
```
具体的实现逻辑在**node**方法中：
```java
	public Node<E> node(int index) {
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++) {
                x = x.next;
            }
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--) {
                x = x.prev;
            }
            return x;
        }
    }
```
## 7.迭代器-LisItr
首先是通过**listIterator**方法获取到迭代器LisItr
```java
	public LisItr<E> listIterator(int index) {
        return new LisItr(index);
    }
```
然后就是内部类 **LisItr** 的迭代器具体实现。LisItr 可以从任一位置，向前或者向后迭代（依靠双链表的优势）
```java
private class LisItr<E> {

        int cursor; // 当前索引
        Node<E> curNode; // 当前节点
        Node<E> lastNode; // 上一个节点
        int expectedModCount = modCount; // 记录迭代前的操作数，若当前modCount与expectedModCount就会抛异常

		// 可以从任一位置开始迭代，不像数组必须从头（索引0）开始迭代
        public LisItr(int index) { 
            cursor = index;
            curNode = (Node<E>) node(index); // 通过node方法拿到当前节点
        }
		
		// 是否到尾了
        public boolean hasNext() {
            return cursor < size; 
        }

        /**
         * 返回当前节点的数据，并后移
         * @return 
         */
        public E next() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            E res = curNode.item;
            curNode = curNode.next;
            cursor++;

            return res;
        }
		
		// 是否到头了
        public boolean hasPrevious() {
            return cursor > 0;
        }
		
		/**
         * 返回当前节点的数据，并前移
         * @return 
         */ 
        public E previous() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            E res = curNode.item;
            curNode = curNode.prev;
            cursor--;

            return res;
        }
    }
```
## 完整代码如下

```java
public class MyLinkedList<E> {

    // 注：容器是有泛型的，Node也应该有泛型
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        // 注：LinkedList中的Node构造器是构造时就要传入prev和next
        public Node(Node<E> prev, E element, Node<E> next) {
            this.prev = prev;
            this.item = element;
            this.next = next;
        }
    }

    Node<E> first;
    Node<E> last;
    int size = 0;
    int modCount = 0;

    public MyLinkedList() {
    }

    /**
     * 尾插
     * newNode.pre = last
     * last.next = newNode
     * last = newNode
     * @param e
     * @return
     */
    public boolean add(E e) {
        Node newNode = new Node(last, e, null);
        if (last == null) {
            first = newNode;
        } else {
            last.next = newNode;
        }
        last = newNode;

        size++;
        return true;
    }

    /**
     * 同add，直接调用即可
     * @param e
     * @return
     */
    public boolean offer(E e) {
        return add(e);
    }

    /**
     * 头插
     * newNode.next = first;
     * first.prev = newNode;  注：考虑first=null（链表为空，只用更新last即可）
     * first = newNode;
     * @return
     */
    public boolean addFirst(E e) {
        Node newNode = new Node(null, e, first);
        if (first == null) {
            last = newNode;
        } else {
            first.prev = newNode;
        }
        first = newNode;

        size++;
        return true;
    }

    /**
     * 删除指定元素
     * 1.找到要删除元素的位置
     * 2.o.pre = o.next
     * @param o
     * @return
     */
    public boolean remove(Object o) {
        Node tmp = first;
        while (tmp != null) {
            if (tmp.item.equals(o)) {
                unlink(tmp);
                return true;
            }
        }
        return false; // 链表为空，或无要删除元素
    }

    /**
     * 执行删除
     * x.prev.next = x.next        注：要考虑x.prev==null(x是first，直接更新first）
     * x.next.prev = x.prev.prev   注：要考虑x.next==null(x是last，直接更新last）
     * @param x
     */
    private E unlink(Node<E> x) {
        E element = x.item;
        Node prev = x.prev;
        Node<E> next = x.next;

        if (prev == null) {
            first = next;
        } else {
            prev.next = next;
            x.prev = null;
        }

        if (next == null) {
            last = prev;
        } else {
            next.prev = prev;
            x.next = null;
        }

        x.item = null;

        size--;
        return  element;
    }

    /**
     * 删除头节点，链表为空时抛出异常
     * first.next.pre = null;    注：考虑first=null（链表为空）， first.next=null（尾结点，即链表只有一个节点）
     * first = first.next;
     * @return
     */
    public boolean remove() {
        if (first == null) {
            throw new RuntimeException("List is Empty");
        }
        return removeFirst();
    }

    public boolean removeFirst() {
        if (first.next == null) {
            last = null;
        } else {
            first.next.prev = null;
        }
        first = first.next;

        size--;
        return true;
    }

    /**
     *删除头节点，链表为空时返回null
     * @param o
     * @return
     */
    public boolean poll(Object o) {
        if (first == null) {
            return false;
        }
        return removeFirst();
    }


    /**
     * 根据索引查找元素
     * 1.在前半部分，就从前向后遍历
     * 2.在后半部分，从后向前遍历
     * @param index
     * @return
     */
    public E get(int index) {
        if (index >= size || index < 0) {
            throw new IllegalArgumentException("Index Out Of Range");
        }
        return node(index).item;
    }

    public Node<E> node(int index) {
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++) {
                x = x.next;
            }
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--) {
                x = x.prev;
            }
            return x;
        }
    }

    public LisItr<E> listIterator(int index) {
        return new LisItr(index);
    }

    private class LisItr<E> {

        int cursor; // 当前索引
        Node<E> curNode; // 当前节点
        Node<E> lastNode; // 上一个节点
        int expectedModCount = modCount; // 记录迭代前的操作数，若当前modCount与expectedModCount就会抛异常

		// 可以从任一位置开始迭代，不像数组必须从头（索引0）开始迭代
        public LisItr(int index) { 
            cursor = index;
            curNode = (Node<E>) node(index); // 通过node方法拿到当前节点
        }
		
		// 是否到尾了
        public boolean hasNext() {
            return cursor < size; 
        }

        /**
         * 返回当前节点的数据，并后移
         * @return 
         */
        public E next() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            E res = curNode.item;
            curNode = curNode.next;
            cursor++;

            return res;
        }
		
		// 是否到头了
        public boolean hasPrevious() {
            return cursor > 0;
        }
		
		/**
         * 返回当前节点的数据，并前移
         * @return 
         */ 
        public E previous() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            E res = curNode.item;
            curNode = curNode.prev;
            cursor--;

            return res;
        }
    }
}

```
