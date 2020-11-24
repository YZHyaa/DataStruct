堆在逻辑上一棵完全二叉树，所以可以通过数组进行数据存储，而其余的树大多采用链式结构进行数据存储
* 堆分类：
	* 大顶堆：大顶堆就是无论在任何一棵（子）树中，父节点都是最大的
	* 小顶堆：小顶堆就是无论在任何一棵（子）树中，父节点都是最小的
* 堆的两种操作：
	* 上浮：一般用于向堆中添加新元素后的堆平衡
	* 下沉：一般用于取出堆顶并将堆尾换至堆顶后的堆平衡
* 堆排序：利用大顶堆和小顶堆的特性，不断取出堆顶，取出的元素就是堆中元素的最值，然后再使堆平衡

下面的文章以大顶堆为例，拿Java实现堆的各种操作。

## 1.MaxHeap
大顶堆：对于任一个（子）堆，堆顶最大
```java
// 这里Comparable保证所有结点可比，是成堆基础
public class MaxHeap <E extends Comparable<E>> {
	// 完全二叉树，排列整齐（相当于一层一层放入），可以用数组存储
    private ArrayList<E> data;

    public MaxHeap(int capcity) {
        data = new ArrayList<>(capcity);
    }

    public MaxHeap() {
        data = new ArrayList<>();
    }

    // 堆是否为空
    public boolean isEmpty() {
        return data.isEmpty();
    }
    // 堆中元素个数
    public int size() {
        return this.data.size();
    }
	
	//......
}
```
## 2.操作一：获取父/子节点
### parent()
```java
// 返回idx位置元素的父节点
// 注：这里从0放起（parent = (i - 1）/2 )，若是从1放起（parent = i / 2)
private int parent(int idx) {
    if (idx == 0) {
        throw new IllegalArgumentException("index-0 doesn't have parent");
    }
    return (idx - 1) / 2;
}
```

### leftChild() / rightChild()
```java
// 返回idx位置元素的孩子节点
// 注：这里从0放起
private int leftChild(int idx) {
    // 若从1放起，leftChild = idx * 2
    return idx * 2 + 1;
}
private int rightChild(int idx) {
    // 若从1放起，rightChild= idx * 2 + 1
    return idx * 2 + 2;
}
```

## 2.操作二：添加元素
### add()
1.  将元素放到堆尾，即数组最后一个元素
2.  加入到最后了，上浮

```java
public void add(E e) {
    data.add(e);
    // 传入需要上浮的索引
    siftUp(data.size() - 1);
}
```

### siftUp()
* 上浮：子节点与（小于自己的）父节点交换
* Time：O（logn），获取parent是不断二分（/2) 的

```java
private void siftUp(int k) {
    // 只要是父节点(data[parent]) 比 子节点（data[k])小，就进行交换
    while (k > 0 && data.get(parent(k)).compareTo(data.get(k)) < 0) {
        // 1.交换数组中位置
        // 注：这里是
        Collections.swap(k, parent(k));
        // 2.更新子节点，进行下一轮上浮
        // 注：也可以data.set进行三步走
        k = parent(k);
    }
}
```

## 3.操作三：取出堆顶（最大元素）
### extractMax()

取出堆顶

1. 拿到堆顶元素
2. 删除堆顶
   1. 将堆顶（0）与堆尾（size-1）交换，因为要产生新堆顶
   2. 删除堆尾
   3. 堆顶下沉

```java
public E extractMax() {
    // 1.获取堆顶元素
    E ret = findMax() ;
    
	// 2.1 将堆顶换到堆尾
    Collections.swap(0, data.size() - 1);
    // 2.2 删除堆尾
    data.remove(data.size() - 1);
	// 2.3 下沉堆顶
    siftDown(0):
    
    return ret;
}
```

### findMax()
获取堆中最大元素
```java
public E findMax() {
    if (data.size() == 0) {
        throw new IllegalArgumentException("Can not findMax when heap is empty");
    }
    // 堆顶最大 = 数组第一个元素（0）
    return data.get(0);
}
```

### siftDown()
* 堆顶下沉：与左右子节点较大值（且大于自己的）节点进行交换
* Time：O（logn），获取Child是不断二分（/2) 的
```java
private void siftDown(int k) {
    // 1.判断当前节点是否有子节点。下沉到叶节点，就没有儿子了，就不用下沉了
    // 注：因为leftChild索引肯定比rightChild小，所以只要有leftChild就有子节点
    while (leftChild(k) < data.size()) {
        // 2.拿到leftChild与rightChild的大值
        int j = leftChild(k);
        if (j + 1 < data.size() && data.get(j + 1).compareTo(data.get(j)) > 0) {
            j = rightChild(k);
        }
		
        // 3.判断子节较大值是否大于自己（父节点）
        if (data.get(k).compareTo(data.get(j)) >= 0) {
            break;
        }
		
        // 4.若大于，交换数组中两节点位置
        Collections.swap(k, j);
        
        // 更新父节点，进行下一轮下沉
        k = j;
    }
}
```
## 4.操作四：数组构造堆
* 思路一：将已知数组一个一个加入到堆中 ====> Time = O（n*logn）
* 思路二：从第倒数一个非叶子节点开始下沉 ====> Time = O（n）
```java
// 构造函数中传入要构造成堆的数组
public MaxHeap(E[] arr) {
    // 注：这里数组不能直接作为ArrayList参数，要先包装成List
    data = new ArrayList<>(Arrays.asList(arr));
    // 确定倒数第一个非叶子结点：最后一个叶子（length - 1）的父节点 (（i - 1）/ 2)
    for (int i = parent(arr.length - 1); i >= 0; i--) {
        // 逐个下沉
        siftDown(i);
    }
}
```
### => 验证最大堆
在看堆排序前，我们先来验证我们写的代码是否正确的。思路是，向堆中添加一百万个随机数，然后依次取顶取出放入到一个数组中，最后验证看是否从大到小排列的：
```java
public class Test {

    public static void main(String[] args) {
        int n = 10000000;

        MaxHeap<Integer> heap = new MaxHeap<>();
        Random random = new Random();
        // 1.向堆中添加一百万个随机数
        for (int i = 0; i < n; i++)
            heap.add(random.nextInt(Integer.MAX_VALUE));

        // 2.不断从最大堆中取出堆顶  --> 从大到小
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = heap.extractMax();
        }
		
        // 3.验证取出的元素是否按照从大到小排列
        for (int i = 0; i < n - 1; i++)
            if (arr[i] < arr[i + 1])
                throw  new IllegalArgumentException("Error");

        System.out.println("Test MaxHeap Success!");

    }
}
```
这里其实也可以直接创建出一个数组，然后构造成最大堆，再不断取顶。相信经过上面的示例，你已经看到了堆有进行排序的能力。
### ==> 堆排序
堆排序原理：简而言之，就是利用大顶堆和小顶堆的特性，不断取出堆顶（堆中元素的最值），然后再使堆平衡。
1. 构建最大堆：借鉴上面构建堆的思路，从最后一个不是叶子节点的节点开始下沉
2. 取出堆顶：不是真取出，而是通过交换堆顶（arr[0]）到堆尾（arr[i]）实现出堆顶 ==> **大顶堆的排序结果是从小到大**
3. 新堆再平衡：由于上一步已经将堆尾换到了堆首，所以直接再下沉就行

注意：这里采取的是数组从0开始放，即 idx 位置的 parent = idx / 2，leftChild = idx * 2，rightChild = idx * 2 + 1
```java
public class HeapSort {

    public void sort(int[] arr) {
        int length = arr.length;
		
		// 1.构建最大堆：从最后一个不是叶子节点的节点开始下沉
		// i=(length-1)/2，表示获取最后一个叶子节点的父亲，即最后一个不是叶子节点的节点
        for (int i = (length - 1) / 2; i >= 0; i--) {
            siftDown(arr,length, i);
        }
		
        for (int i = length - 1; i > 0; i--) {
        	// 2.取出堆顶：通过交换堆顶（arr[0]）到堆尾（arr[i]），实现出堆顶
            swap(arr, 0, i);
            // 3.新堆再平衡：上一步已经将堆尾换到了堆首，所以直接再下沉就行
            // 注：可以看到这里新堆的长度变成了i（比之前少了1）
            siftDown(arr, i, 0);
        }
    }

    private void siftDown(int[] arr, int length, int idx) {
    	// 下沉到叶节点，就没有儿子了，就不用下沉了
    	// 这里用的leftChild（2*idx），因为如果左儿子都超过length了，右儿子也一定超过了
        while (2 * idx < length) {
        	
        	// 判断左儿子和右儿子哪个大
            int j = 2 * idx;
            if (j + 1 < length && arr[j+1] > arr[j])
                j++; // 如果右儿子大，就j++
			
			// 判断父亲是否比儿子大
            if (arr[idx] >= arr[j])
                break;
			
			// 父亲小于儿子，那么就交换父子
            swap(arr, idx, j);
            // 更新父亲idx，继续去下一棵子树判断下沉
            idx = j;
        }
    }

    private void swap(int[] arr, int idx1, int idx2) {
        int t = arr[idx1];
        arr[idx1] = arr[idx2];
        arr[idx2] = t;
    }
}
```
另外，如果对其他排序感兴趣的同学，可以参考这篇文章 [图解十大排序算法及Java实现（详细）](https://blog.csdn.net/weixin_43935927/article/details/108952871)...
## 5.操作五：取出堆顶，再加入一个元素
* 思路一：取出堆顶（extractMax），然后再加入一元素（add） ====> 2 * O（logn）
* 思路二：将堆顶直接修改，然后下沉  ====> O（logn）

### replace()
```java
public E replace(E e) {
        // 1.获取堆顶元素
        E ret = findMax();
    	// 2.修改堆顶，即数组0位置
        data.set(0, e);
        // 3.下沉
        siftDown(0);
    
        return ret;
}
```



## 完整代码

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;

public class MaxHeap <E extends Comparable<E>> {

    private ArrayList<E> data;

    public MaxHeap(int capcity) {
        data = new ArrayList<>(capcity);
    }

    public MaxHeap() {
        data = new ArrayList<>();
    }

    public MaxHeap(E[] arr) {
        data = new ArrayList<>(Arrays.asList(arr));
        for (int i = parent(arr.length - 1); i >= 0; i--) {
            siftUp(i);
        }
    }

    // 堆是否为空
    public boolean isEmpty() {
        return data.isEmpty();
    }
    // 堆中元素个数
    public int size() {
        return this.data.size();
    }

    // 返回idx位置元素的父节点
    // 注：这里从0放起（parent = (i - 1）/2 )，若是从1放起（parent = i / 2)
    private int parent(int idx) {
        if (idx == 0) {
            throw new IllegalArgumentException("index-0 doesn't have parent");
        }
        return (idx - 1) / 2;
    }

    // 返回idx位置元素的孩子节点
    // 注：这里从0放起
    private int leftChild(int idx) {
        // 若从1放起，leftChild = idx * 2
        return idx * 2 + 1;
    }
    private int rightChild(int idx) {
        // 若从1放起，leftChild = idx * 2
        return idx * 2 + 2;
    }

    // 添加元素
    public void add(E e) {
        data.add(e);
        siftUp(data.size() - 1);
    }

    private void siftUp(int k) {
        while (k > 0 && data.get(parent(k)).compareTo(data.get(k)) < 0) {
            Collections.swap(data, k, parent(k));
            k = parent(k);
        }
    }

    // 获取堆中最大元素
    public E findMax() {
        if (data.size() == 0) {
            throw new IllegalArgumentException("Can not findMax when heap is empty");
        }
        // 堆顶最大，
        return data.get(0);
    }

    public E extractMax() {
        E ret = findMax() ;

        Collections.swap(data, 0, data.size() - 1);
        data.remove(data.size() - 1);

        siftDown(0);
        return ret;
    }

    private void siftDown(int k) {
        while (leftChild(k) < data.size()) {
            int j = leftChild(k);
            if (j + 1 < data.size() && data.get(j + 1).compareTo(data.get(j)) > 0) {
                j = rightChild(k);
            }

            if (data.get(k).compareTo(data.get(j)) >= 0) {
                break;
            }

            Collections.swap(data, k, j);
            k = j;
        }
    }

    public E replace(E e) {
        E ret = findMax();
        data.set(0, e);
        siftDown(0);
        return ret;
    }
}
```
最后，对堆在Java中的应用感兴趣的同学看看 PriorityQueue 的源码，它就是通过小顶堆实现的，这里放个传送门 [【Java集合源码】PriorityQueue源码分析](https://blog.csdn.net/weixin_43935927/article/details/108858762)。
