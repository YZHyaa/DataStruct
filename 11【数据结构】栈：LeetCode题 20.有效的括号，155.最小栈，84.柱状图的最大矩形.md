## [20.有效的括号¹](https://leetcode-cn.com/problems/valid-parentheses/)
给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**示例 1:**

```
输入: "()"
输出: true
```

**示例 2:**

```
输入: "()[]{}"
输出: true
```

**示例 3:**

```
输入: "(]"
输出: false
```

**示例 4:**

```
输入: "([)]"
输出: false
```

**示例 5:**

```
输入: "{[]}"
输出: true
```

### 解法一：栈（思路一）
这种思路很简单直接，就是将所有左半部分（，[，{ 压入栈，碰到右半部分就一一匹配。
```java
public boolean isValid(String s) {
       Stack<Character> stack = new Stack<>();
       for (char c : s.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') 
                stack.push(c);
           // 当右括号匹配上了出栈
           // 注：isEmpty判断很关键
            else if (c == ')' && !stack.isEmpty() && stack.peek() == '(' ) 
                stack.pop();
            else if (c == ']' && !stack.isEmpty() && stack.peek() == '[' ) 
                stack.pop();
            else if (c == '}' && !stack.isEmpty() && stack.peek() == '{' ) 
                stack.pop();
            else 
                // 右括号匹配不上，返回false
                return false;
       }
       // ")))))"
       return stack.isEmpty();
}
```
### 解法二：栈（思路二）
这种思路是碰到左半部分入栈，不过是 (入栈)，[入栈]，{入栈}，碰到又半部分比较是否相等。可能说起来比较晦涩，但代码一看就能明白：
```java
¹public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for(char c : s.toCharArray()) {
            if(c == '(')
                stack.push(')');
            else if(c == '{') 
                stack.push('}');
            else if(c == '[')
                stack.push(']');
            else if(stack.isEmpty() || stack.pop() != c) // 这里比较的是是否相等
                return false;
        }

        return stack.isEmpty();
}
```
相较于思路一，这种解法的代码更加整洁。
## [155. 最小栈¹（维持最小值）](https://leetcode-cn.com/problems/min-stack/)

设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

- push(x) -- 将元素 x 推入栈中。
- pop() -- 删除栈顶的元素。
- top() -- 获取栈顶元素。
- getMin() -- 检索栈中的最小元素。

**示例:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

### 解法一：辅助栈
首先要明白，这里没有做特殊要求，所以**可以直接使用jdk提供的栈**，即我们不必自己动手实现一个栈，只需解决如何找最小值的问题。

解决方案是再创建一个辅助栈，用来保存每次 push/pop 操作后的最小值。

```java
class MinStack {
	
    // 保存按序放入数据的栈，直接使用jdk的栈就行
    Stack<Integer> items;
    // 保存最小值的栈
    Stack<Integer> min;

    public MinStack() {
        this.items = new Stack<>();
        this.min = new Stack<>();
    }
    
    public void push(int x) {
        // 自动装箱
        items.push(x);
        // 注：一定要先判断，栈空的就直接放入
        if (min.isEmpty())  
            min.push(x);
        // ！注：这里的<=，必须等于也要入栈，不然到时候相等的值就存一个，出栈后就错了
        else if(x <= min.peek()) 
            min.push(x);
    }
    
    public void pop() {
        // ！！注：一定要intValue取出int，不然自动装箱在-127-128外的值地址不一样，肯定false
        // 如 item-push：-128（addr1）min-push：-128（addr2）
         if (min.peek() == items.pop().intValue()) 
            min.pop();
    }
    
    public int top() {
        return items.peek();
    }
    
    public int getMin() {
        return min.peek();
    }
}
```

## [84.柱状图中最大的矩形³](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

![img](https://img-blog.csdnimg.cn/img_convert/12014ed25f50104297d630614f58eb46.png)

以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 `[2,1,5,6,2,3]`。

![img](https://img-blog.csdnimg.cn/img_convert/22fab85baf2899679fa354abf25f6e21.png)

图中阴影部分为所能勾勒出的最大矩形面积，其面积为 `10` 个单位。

**示例:**

```
输入: [2,1,5,6,2,3]
输出: 10
```

### 解法一：枚举法
* 思路：矩形的面积无非就是长乘宽，所以我们可以将所有的长宽情况枚举出来，然后再找到面积最大值。落实到代码上，就是所有两根柱子的组合枚举出来，很显然是两层循环。
* 复杂度
	* Time：O（n^2）
	* Space：O（1）
```java
 public int largestRectangleArea(int[] heights) {
        if (heights == null) return 0;
        int max = 0;
        for (int i = 0; i < heights.length; i++) {
            // min：这段矩形的最小高度
            // ！！注意：每个格子是有宽度的，所以内层循环从i开始 --> 初始值：max=h[i]，min=h[i]
            for (int j = i，min = 0; j < heights.length; j++) {
                min = Math.min(min, heights[j]);
                max = Math.max(max,(j - i + 1) * min);
            }
        }
        return max;
}
```
### 解法二：单调栈*
单调栈+哨兵。哨兵的作用是减少对空栈的判断，简化代码逻辑
```java
    public  int largestRectangleArea2(int[] heights){
        int  len = heights.length;
        if(len == 0)return 0;
        if(len == 1) return heights[0];
        int[] newHeights = new int[len+2];
        for (int i = 0; i <len ; i++) {
            newHeights[i+1] = heights[i];
        }
        len+=2;
        int area = 0;
        heights = newHeights;
        Deque<Integer> stack = new ArrayDeque<>();
        //加入哨兵元素，在循环中不用做非空判断
        stack.addLast(0);
        //数组下标入栈
        for (int i = 1; i <len ; i++) {
            //当前元素小于栈顶元素
            while(heights[i] < heights[stack.peekLast()]){
                int curHeight = heights[stack.pollLast()];
                int width = i - stack.peekLast()-1;
                area = Math.max(area,curHeight*width);
            }
            stack.addLast(i);
        }
        return area;
    }
```
