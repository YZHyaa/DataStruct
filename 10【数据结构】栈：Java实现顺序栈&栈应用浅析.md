## 1.栈是什么
* 定义：后进者先出，先进者后出，这就是典型的“栈”结构
* 操作特性：栈是一种“操作受限”的线性表，只允许在一端插入和删除数据。
* 使用场景；当某个数据集合只涉及在一端插入和删除数据，并且满足后进先出、先进后出的特性，就应该首选“栈”这种数据结构。

## 2.Java实现顺序栈
用数组实现的栈，我们叫作顺序栈（效率高），而用链表实现的栈，我们叫作链式栈。
### 2.1 固定大小的栈
  * 时间复杂度：O（1），每次都只操作count（-1）位，与数据规模无关
  * 空间复杂度：O（1），最开始申请了固定大小数组后运行时不再改变大小，与数据规模无关
```java
public class MyStack {
    // 为了简单，这里直接定义成int
    private int[] items;
    private int size; // 栈容量
    private int count; // 栈中实际元素多少
    
    // 指定大小构造
    public MyStack(int size) {
        this.size = size; 
        this.items = new int[size];
        this.count = 0;
    }

    // push:每次都给items[count]
    public boolean push(int item) {
        // 非满判断
        if (count == size) return false;
        this.items[count] = item; // 关键
        count++;
        return true;
    }

    // pop:items[count-1]
    // 这里count是数组实际元素多少，若不减一返回0
    public int pop() {
        // 非空判断
        if (count == 0) return 0;
        int item = this.items[count-1]; // 关键
        count--;
        return item;
    }

    public int peek() {
    	// 这里注意要判断栈中是否有元素
        return count == 0 ? 0 : this.items[count-1];
    }
}
```
上面是基于数组实现的栈，是一个固定大小的栈，也就是说，在初始化栈时需要事先指定栈的大小。当栈满之后，就无法再往栈里添加数据了。
### 2.2 动态扩容的栈
尽管链式栈的大小不受限，但要存储 next 指针，内存消耗相对较多。因而实现一个基于数组的动态扩容的栈
  * 最好情况复杂度：O(1）
  * 最坏情况复杂度：O(n)，满了需要扩容
  * 均摊时间复杂度（if{for}）：O(1)，平均到每次push相当于每次只扩容一个
```java
public boolean push(int item) {
       // 满了就扩容，只有push涉及扩容
       if (count == size) resize();
       this.items[count] = item;
       count++;
       return true;
}

private void resize() {
    int[] newItem = new int[size << 1]; // 将容量变为两倍
    System.arraycopy(items,0,newItem,0,size); // 关键，所有数组的动态扩容本质上都是数组拷贝
    this.items = newItem; // 修改指针指向新数组
}
```

## 3.栈的应用
### 3.1 函数调用栈

* 操作系统给每个线程分配了一块独立的内存空间，这块内存被组织成“栈”这种 结构, 用来存储函数调用时的临时变量
* 每进入一个函数，就会将临时变量作为一个**栈帧入栈**，当被调用函数执行完成返回之后，将这个函数对应的**栈帧出栈**

```java
int main() {   
    int a = 1;  
    int ret = 0; 
    int res = 0;  
    ret = add(3, 5);
    res = a + ret; 
    printf("%d", res);   
    reuturn 0;\
}
 
int add(int x, int y) {   
    int sum = 0;
    sum = x + y; 
    return sum; 
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200926013846375.png#pic_center)

### 3.2 实现计算器
* 使用两个栈：操作数栈和符号栈
* 操作数和符号依次入栈，当 - 和 ÷ 要入栈时清空栈（操作数先出栈，符号再出栈 ---> 结果）

比如现在要计算 3 + 5 x 8 - 6 的结果，过程其实很简单：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200926015607236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)


### 3.3 浏览器后退前进

**使用两个栈：X(后退栈) 和 Y（前进栈）**

* 把首次浏览的页面依次压入栈 X。比如你顺序查看了 a，b，c 三个页面，我们就依次把 a，b，c 压入栈，这个时候，两个栈的数据就是这个样子：

![<img src="03 线性表：栈.assets/1584934266220.png" width="44%">](https://img-blog.csdnimg.cn/20200926012837929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

* 当点击**后退**按钮时，再依次从栈 X 中出栈，并将出栈的数据依次放入栈 Y。比如从页面 c 后退到页面 a 之后，就依次把 c 和 b 从栈 X 中 弹出，并且依次放入到栈 Y

 ![\[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-bA59r5rb-1601053506076)(03 线性表：栈.assets/1584934365362.png)\]](https://img-blog.csdnimg.cn/20200926012954300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
* 当我们点击**前进**按钮时，我们依次从栈 Y 中取出数据，放入栈 X 中。比如这个时候你又想看页面 b，于是你又点击前进按钮回到 b 页面，我们就把 b 再从栈 Y 中出 栈，放入栈 X 中
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200926013037935.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

* 当栈 X 中没有数据时，那就说明没有页面可以继续后退浏览 了。当栈 Y 中没有数据，那就说明没有页面可以点击前进按钮浏览了。比如，你通过页面 b 又跳转到新的页面 d 了，页面 c 就无法再通过前进、后退按钮重复查看了，所以需要清空栈 Y

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200926013302343.png#pic_center)
