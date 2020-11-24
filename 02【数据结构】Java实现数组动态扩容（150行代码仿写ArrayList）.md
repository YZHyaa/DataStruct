在文章开头先放一个传送门 [【Java容器源码】ArrayList源码分析](https://blog.csdn.net/weixin_43935927/article/details/108466664)。是作者写的关于LInkedList源码的分析。这篇文章就仿写ArrayList，并实现容器中的核心方法。
## 1.ArrayList基本结构
```java
public class MyArrayList<E> {

    private static final int DEFAULT_CAPACITY = 10; // 第一次扩容时的容量

    private static final Object[] EMPTY_ELEMENTDATA = {}; // 默认初始化数组

    private int size; // 数组实际大小，这里注意一点length是容量

    private int modCount = 0;

    private Object[] elementData; // 核心容器，所有元素都放在这里面。因为要配合泛型，所以是Object[]

    public MyArrayList() {
    	// 此处就是jdk8相较于jdk7优化的地方，对于不指定大小情况，在初始化时是赋空数组，第一次扩容时再变为10
        this.elementData = EMPTY_ELEMENTDATA; 
    }

    public MyArrayList(int capacity) {
        this.elementData = new Object[capacity]; // 指定大小初始化，直接按容量初始化
    }
    
    //.......
}    
```
## 2.增加元素-add
增加元素执行起来非常简单，其实就是对数组赋值（arr[i] = value）
```java
	public boolean add(E e) {
        ensureCapacity(size + 1); // 确保容量足够
        elementData[size++] = e;
        return true;
    }
```
## 3.确保容量足够-ensureCapacity
容量足够的标准很简单，就是 size+1 >= length
```java
	private void ensureCapacity(int minCapacity) {
        modCount++; // 注：modCount不是add方法中++，因为无论add还是addAll都要走到ensureCapacity

        if (minCapacity <= elementData.length) {
            return;
        }
        // 若是第一次放元素，那么就进行初始化，初始化的容量是10
        if (elementData == EMPTY_ELEMENTDATA) { 
            minCapacity = 10;
        }
        grow(minCapacity); 
    }
```
## 4.执行动态扩容-grow
数组在创建时大小就确定了，所以所谓的扩容并不是将当前数组变大了，而是创建一个新的大数组，然后将原来数组元素拷贝过去，最后再将elementData指针指向这个新数组。
```java
	public void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1); // 1.5倍扩容
        newCapacity = Math.min(newCapacity, minCapacity);
		
        elementData = Arrays.copyOf(elementData, newCapacity); // 核心！！
    }
```
## 5.批量增加-addAll
对于一次增加多个元素（集合）的情况，addAll会是一个更明智的选择。因为上面说了，扩容时会先创建新数组然后拷贝，若增加很多个元素时，可能会出现多次扩容操作，势必会浪费更多性能。而addAll方法会根据要添加的集合的大小，最多只扩容一次。更多分析可以参考[【Java容器源码】集合应用总结：迭代器&批量操作&线程安全问题](https://blog.csdn.net/weixin_43935927/article/details/108543312)
```java
	public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        ensureCapacity(size + a.length);
        int numNew = a.length;

        System.arraycopy(a, 0, elementData, size, numNew); // 只拷贝一次
        size += numNew;
        return numNew != 0;
    }
```
## 6.删除指定元素-remove
删除的实质也是数组拷贝
1. 找到要删除元素索引(第一个），注：分为null与非空
2. 通过拷贝进行删除
```java
    public void remove(Object o) {
        modCount++;
        int removeIdx = -1;
        if (o == null) {
            for (int i = 0; i < size; i++) {
                if (elementData[i] == null) {
                    removeIdx = i;
                    break;
                }
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (elementData[i].equals(o)) {
                    removeIdx = i;
                    break;
                }
            }
        }
		
		// 找到索引后通过拷贝进行删除
		// 注：这里用system.arraycopy是因为拷贝前后是同一个数组
        if (removeIdx != -1) {
            int numMoved = size - removeIdx - 1;
            System.arraycopy(elementData, removeIdx+1, elementData, removeIdx, numMoved);
        }

        elementData[size--] = null;
    }
```
## 7.批量删除-removeAll
上面说了删除的实质就是删除，那如果我要删除多个元素，就是要拷贝多次？这里其实可以通过双指针的算法进行动态删除，即一个指针i负责遍历原来元素，一个指针j负责赋值（碰到要被删除的元素时就暂停）。
```java
	/**
     *1.通过双指针进行批量删除，复杂度O（n）
     * 2.将剩余元素置null
     * @param c
     */
    public void removeAll(Collection<?> c) {
        modCount++;
        Object[] a = c.toArray();
        int i = 0, j = 0;
        for (; i < size; i++) {
            if (!c.contains(elementData[i])) {
                elementData[j++] = elementData[i];
            }
        }

        if (j != size) {
            for (int k = 0; k < size; k++) {
                elementData[k] = null;
            }
            size = j;
        }
    }
```
## 8.迭代器-Itr
```java
private class Itr<E> {
        int cursor; // 当前索引
        int expectedModCount = modCount; // 记录迭代前的操作数，若当前modCount与expectedModCount就会抛异常
        int lastRet; // 上一个索引
		
		// 是否还有待迭代元素（即是否已到数组最后一个元素）
        public boolean hasNext() {
            return cursor != size;
        }
		
		// 返回当前元素，并后移一位
        public E next() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }
            int i = cursor;
            Object[] elementData = MyArrayList.this.elementData;
            cursor++;
            return (E)elementData[lastRet = i];
        }

		// 迭代时删除当前元素
        public void remove() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            if (lastRet < 0) {
                throw new IllegalArgumentException("重复删除");
            }

            MyArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;

            expectedModCount = modCount;
        }
    }
```
## 完整代码如下
```java
public class MyArrayList<E> {

    private static final int DEFAULT_CAPACITY = 10;

    private static final Object[] EMPTY_ELEMENTDATA = {};

    private int size;

    private int modCount = 0;

    private Object[] elementData;

    public MyArrayList() {
        this.elementData = EMPTY_ELEMENTDATA;
    }

    public MyArrayList(int capacity) {
        this.elementData = new Object[capacity];
    }

    public boolean add(E e) {
        ensureCapacity(size + 1);
        elementData[size++] = e;
        return true;
    }

    private void ensureCapacity(int minCapacity) {
        modCount++;

        if (minCapacity <= elementData.length) {
            return;
        }
        if (elementData == EMPTY_ELEMENTDATA) {
            minCapacity = 10;
        }
        grow(minCapacity);
    }

    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        ensureCapacity(size + a.length);
        int numNew = a.length;

        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }

    public void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        newCapacity = Math.min(newCapacity, minCapacity);

        elementData = Arrays.copyOf(elementData, newCapacity);
    }


    /**
     * 1.找到要删除元素索引(第一个），注：分为null与非空
     * 2.通过拷贝进行删除
     * @param o
     */
    public void remove(Object o) {
        modCount++;
        int removeIdx = -1;
        if (o == null) {
            for (int i = 0; i < size; i++) {
                if (elementData[i] == null) {
                    removeIdx = i;
                    break;
                }
            }
        } else {
            for (int i = 0; i < size; i++) {
                if (elementData[i].equals(o)) {
                    removeIdx = i;
                    break;
                }
            }
        }

        if (removeIdx != -1) {
            int numMoved = size - removeIdx - 1;
            System.arraycopy(elementData, removeIdx+1, elementData, removeIdx, numMoved);
        }

        elementData[size--] = null;
    }

    /**
     *1.通过双指针进行批量删除，复杂度O（n）
     * 2.将剩余元素置null
     * @param c
     */
    public void removeAll(Collection<?> c) {
        modCount++;
        Object[] a = c.toArray();
        int i = 0, j = 0;
        for (; i < size; i++) {
            if (!c.contains(elementData[i])) {
                elementData[j++] = elementData[i];
            }
        }

        if (j != size) {
            for (int k = 0; k < size; k++) {
                elementData[k] = null;
            }
            size = j;
        }

    }

    public Itr<E> iterator() {
        return new Itr();
    }

    /**
     * 迭代器
     * @param <E>
     */
    private class Itr<E> {
        int cursor;
        int expectedModCount = modCount;
        int lastRet;

        public boolean hasNext() {
            return cursor != size;
        }

        public E next() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }
            int i = cursor;
            Object[] elementData = MyArrayList.this.elementData;
            cursor++;
            return (E)elementData[lastRet = i];
        }


        public void remove() {
            if (modCount != expectedModCount) {
                throw new RuntimeException("迭代时不允许操作");
            }

            if (lastRet < 0) {
                throw new IllegalArgumentException("重复删除");
            }

            MyArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;

            expectedModCount = modCount;
        }
    }


    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }
}
```
