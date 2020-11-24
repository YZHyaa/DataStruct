在讲LeetCode关于数组的习题之前，先讲述两个思想：空间换时间和时间换空间
* **空间换时间**
	* 用空间换时间的设计思想。当内存空间充足的时候，如果我们更加追求代码的执行速度，我们就可以选择空间复杂度相对较高、但时间复杂度相对很低的算法或者数据结构。
	* 缓存实际上就是利用了空间换时间的设计思想。如果我们把数据存储 在硬盘上，会比较节省内存，但每次查找数据都要询问一次硬盘，会比较慢。但如果我们通 过缓存技术，事先将数据加载在内存中，虽然会比较耗费内存空间，但是每次数据查询的速 度就大大提高了
	* 数据结构中双向链表正是用了空间换时间，提高了crud的效率
* 时间换空间
	* 如果内存比较紧缺，比如代码跑在 手机或者单片机上，这个时候，就要反过来用时间换空间的设计思路

**对于执行较慢的程序，可以通过消耗更多的内存（空间换时间）来进行优化；而消耗过多内存的程序，可以通过消耗更多的时间（时间换空间）来降低内存的消耗**

注：下面每题的右上角的数字表示难易度：1-简单，2-中等，3-困难
## [11.盛水最多的容器²](https://leetcode-cn.com/problems/container-with-most-water/)
给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200918202550320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)
图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

示例：
```
输入：[1,8,6,2,5,4,8,3,7]
输出：49
```
### 解法一：枚举
* 思路：枚举出左右两边的所有情况
* 复杂度
  * Time：O(n^2)，448ms
  * Space：O（1）

```java
public int maxArea(int[] height) {
        int capcity = 0;
        for (int i = 0; i < height.length - 1; i++) {
            for (int j = i + 1; j < height.length; j++) {
                capcity = Math.max(capcity, Math.min(height[i],height[j]) * (j-i));
            }
        }
        return capcity;
}
```

### 解法二：双指针（夹逼）

* 思路：双指针列举两边，高度小的一边向内移动

  ====>**空间换时间(升维)，将两重循环，变成一次循环+夹逼指针**

* 复杂度

  * Time：O（n）, 4ms
  * Space：O（1）

```java
public int maxArea(int[] height) {
        public int maxArea(int[] height) {
        int capcity = 0;
        // 双指针    
        int i = 0, j = height.length - 1;
        while (i < j) {
            capcity = Math.max(capcity, (j - i) * Math.min(height[i],height[j]));
            // i，j小的那个向中间移动
            if (height[i] <= height[j])
                i++;
            else 
                j--;
        }
        return capcity;
    }
}
```

**代码优化**：这种比较后要移动索引的，可以优化成条件表达式

```java
public int maxArea(int[] height) {
     
        int capcity = 0;
        int i = 0, j = height.length - 1;
        while (i < j) {
            // 在为高度比较大小的时候就移动指针
            int minheigh = height[i] <= height[j] ? height[i++] : height[j--];
            // 注：这里要j - i + 1,因为已经向内移动了
            capcity = Math.max(capcity, (j - i + 1) * minheigh);
        }
        return capcity;
}
```
----
## [283.移动零¹](https://leetcode-cn.com/problems/move-zeroes)（数组删除问题）

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

### 解法一：双指针（真假）
可以引申到数组一次删除多个元素，避免了多次数组copy

```java
public void moveZeroes(int[] nums) {
        // 双指针，i记录下标++，j记录赋值下标
        int i , j;
        for (i = 0, j = 0; i < nums.length; i++) {
            if (nums[i] != 0) nums[j++] = nums[i];
        }
    	// 将剩余元素置0
        while (j < nums.length) {
            nums[j++] = 0;
        }
}
```
----
## [15. 三数之和²](https://leetcode-cn.com/problems/3sum/)

给你一个包含 *n* 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有满足条件且不重复的三元组。

**注意**：答案中不可以包含重复的三元组。

**示例：**

```
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

### 解法一：循环枚举（超时）

- 去重：数组排序 --> list.contains
- 注意每次循环的边界条件：length - 2；length - 1；length

```java
public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> list = new ArrayList<>();
	    // 注意每次循环的结束条件
        for (int i = 0; i < nums.length - 2; i++) {
            for (int j = i + 1; j < nums.length - 1; j++) {
                for (int k = j + 1; k < nums.length; k++) {
                    if (nums[i] + nums[j] + nums[k] == 0) {
                        List addlist = Arrays.asList(nums[i],nums[j],nums[k]);
                        // 必须先排序，否则每次contains都是fasle
                        if (!list.contains(addlist))
                        list.add(addlist);
                    }
                }
            }
        }
        return list;
}
```

### 解法二：双指针（夹逼）

**空间换时间（升维）：一次循环+夹逼指针 = 两重循环**

- 排序，去重的基础
- 循环取第一位，i < length - 2
  - 判断第一位是否重复
- 双指针找二三位（代替了两重循环）
  - 找到了
    - 判断二三位的下一个是否重复
    - l++,r--
  - 小于sum，l++
  - 大于sum，r--;

**优化**：若第一位（nums[i]) 大于0，那么直接就不用找了，和肯定大于0了，break

```java
public List<List<Integer>> threeSum(int[] nums) {
        if (nums.length < 3 || nums == null) return new ArrayList();
        Arrays.sort(nums);
        List<List<Integer>> list = new ArrayList<>();
        // 优化：若nums[i]>0，sum肯定大于0，不用找了
        for(int i = 0; i < nums.length - 2 && nums[i] <= 0; i++) {
            // 第一位去重
            if (i > 0 && nums[i]  == nums[i - 1]) continue;
            int sum = 0, left = i + 1, right = nums.length-1;
			
            while(left < right) {
                if ((sum = nums[i] + nums[left] + nums[right]) == 0) {
                    list.add(Arrays.asList(nums[i],nums[left],nums[right]));
                    // 第二位去重，while可能后面有多个相同
                    while(left < right && nums[left] == nums[left + 1] ) left++;
                    // 第三位去重，left
                    while(left < right && nums[right] == nums[right - 1]) right--;
                   
                    left++;
                    right--;
                }else if (sum < 0) {
                    left++;
                }else {
                    right--;
                }
            }
        } 
        return list;
}
```


