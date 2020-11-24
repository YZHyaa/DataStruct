## [88. 合并两个有序数组¹](https://leetcode-cn.com/problems/merge-sorted-array/)

给你两个有序整数数组 *nums1* 和 *nums2*，请你将 *nums2* 合并到 *nums1* 中，使 *nums1* 成为一个有序数组。

**说明:**

- 初始化 *nums1* 和 *nums2* 的元素数量分别为 *m* 和 *n* 。
- 你可以假设 *nums1* 有足够的空间（空间大小大于或等于 *m + n*）来保存 *nums2* 中的元素。

**示例:**

```
输入:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]
```

### 解法一：先合并再排序

* 思路：先将nums2放入nums1，让后将nums1排序
* 复杂度
  * Time：O（n*logn），基于sort
  * Space：O（1）

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
        // 将nums2放入nums1
        for (int i = 0; i < n; i++) {
            nums1[i + m] = nums2[i];
        }
        // 排序
        Arrays.sort(nums1);
    }
```

### 解法二：逆序插入

* 思路：既然按序插入需要移动后面的元素，那么我可以倒着走啊，从后面向前插
* 复杂度
  * Time：O（m + n）
  * Space：O（1）

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    	// -1因为这里是索引
        int i = m - 1, j = n - 1, k = m + n - 1;
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) 
                nums1[k--] = nums1[i--];
            else 
                nums1[k--] = nums2[j--];
        }
		// 注：不用判断i>=0的情况，因为要放入nums1，且nums1已经是有序的
        while (j >= 0) nums1[k--] = nums2[j--];
    }
```

----
## [4. 两个正序数组的中位数³](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
给定两个大小为 m 和 n 的正序（从小到大）数组 `nums1` 和 `nums2`。

请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

你可以假设 `nums1` 和 `nums2` 不会同时为空。 

**示例 1:**

```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```

**示例 2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

### 解法一：归并

* 思路：将nums1和nums2合并到一个数组，采用归并排序里面的合并方法，然后返回即可
* 复杂度
  * Time：O（m + n），但在LeetCode上运行时间与下一种无异
  * Space：O（m + n）

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int total = nums1.length + nums2.length;
        int[] tmp = new int[total]; // 创建一个新的大数组
        int i = 0, j = 0, k = 0;
    	// 核心！
        while (i < nums1.length && j < nums2.length) 
            tmp[k++] = nums1[i] <= nums2[j] ? nums1[i++] : nums2[j++];
    	// 处理未放入的情况
        while (i < nums1.length) tmp[k++] = nums1[i++];
        while (j < nums2.length) tmp[k++] = nums2[j++];

        if ((total & 1) == 1) return tmp[total/2];
    	// 注：这里一定要转成double，因为默认int会舍去除的结果
        else return (double)(tmp[total/2 - 1] + tmp[total/2])/2;
    }
```

### 解法二：二分查找*

* 复杂度
  * Time：O（log(m+n))
  * Space：O（1）

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int total = nums1.length + nums2.length;
        if ((total & 1) == 1) {
            // 这里+1是表示要寻找第k小的的元素
            return findKthSmallestInSortedArrays(nums1, nums2, total/2 + 1);
        } else {
            double a = findKthSmallestInSortedArrays(nums1, nums2, total/2);
            double b = findKthSmallestInSortedArrays(nums1, nums2, total/2 + 1);
            return (a + b) / 2;
        }
    }

    // 寻找第K小的元素
    double findKthSmallestInSortedArrays(int[] nums1, int[] nums2, int k) {
        // len来表示结束条件， base记录基准索引
        int len1 = nums1.length, len2 = nums2.length, base1 = 0, base2 = 0;
        while (true) {
            // 结束条件：1.其中一个数组遍历完了  2.俩数组没走完，k走完了
            // 注：-1 是因为k表示的是第n小，这里要转换为索引
            if (len1 == 0) return nums2[base2 + k - 1];
            if (len2 == 0) return nums1[base1 + k - 1];
            if (k == 1) return Math.min(nums1[base1], nums2[base2]);

            // 寻找第k小：在nums1找k/2小， nums2找k-i小
            int i = Math.min(k/2, len1);
            int j = Math.min(k-i, len2);
            int a = nums1[base1 + i - 1];
            int b = nums2[base2 + j - 1];
		   // 找到k，且元素相等的情况
            if (i + j == k && a == b) return a;

            // 更新两数组中小的 base，len，k
            // 注：其中只有base是向后移动++，len和k都是--
            if (a <= b) {
                base1 += i;
                len1 -= i;
                k -= i;
            }
            if (a >= b) {
                base2 += j;
                len2 -= j;
                k -= j;
            }
        }
    }
}
```

