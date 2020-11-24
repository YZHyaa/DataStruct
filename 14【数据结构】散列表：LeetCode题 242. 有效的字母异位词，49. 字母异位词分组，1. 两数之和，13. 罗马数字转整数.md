在看散列表相关LeetCode题之前，先放个传送门[【数据结构】散列表，从特性分析到散列冲突再到应用总结](https://blog.csdn.net/weixin_43935927/article/details/108918699)...
## [242. 有效的字母异位词¹](https://leetcode-cn.com/problems/valid-anagram/)
给定两个字符串 *s* 和 *t* ，编写一个函数来判断 *t* 是否是 *s* 的字母异位词。

**示例 1:**
```
输入: s = "anagram", t = "nagaram"
输出: true
```

**示例 2:**

```
输入: s = "rat", t = "car"
输出: false
```

**说明:**
你可以假设字符串只包含小写字母。

异位词：组成相同，顺序不同

### 解法一：双重循环
* 思路：很简单，就是判断两个字符串的组成是否完全相同。落实在代码上就是嵌套for循环：外层遍历s，内层遍历t寻找与s当前字符相同的字符，若存在就将t中相同字符置为空（注：这里用 replaceFirst）
* 复杂度
	* Time：O(m*n)，m是s的长度，n是t的长度
	* Space：O(1)

```java
public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        boolean res = true;
    
    	// 遍历s
        for (int i = 0; i < s.length(); i++) {
            // 遍历t
            for (int j = 0; j < t.length(); j++) {
                res = false;
                if (s.charAt(i) == t.charAt(j)) {
                    res = true;
                    // replace是替换全部，所以这里用replaceFirst
                    // replaceFirst的第一个参数是String，所以要String.valueOf
                    // 注：一定要再 t = t.re，因为String是不可变的
                    t = t.replaceFirst(String.valueOf(t.charAt(j)),"");
                    break;
                }
            }
            if (!res) break;
        }
        return res;
}
```

### 解法二：散列表
* 思路：记录每个字符的出现次数，这其实是一种K-V思想，所以可以用到散列表
	* 因为总共才26个字母，所以一个数组（alph[26]）就够了。其中，key是数组索引，v是字符出现次数的计数
	* 当s中有某个字符时 alph[s.charAt(i) - 'a']++，而当t中有某个字符时 alph[t.charAt(i) - 'a']--，最后看aplh中是否所有字符的计数为0
* 复杂度
	* Time：O(n)
	* Space：O(1)，因为只有26个字母，所以数组的大小与数据规模无关
```java
public boolean isAnagram(String s, String t) {
        int[] alph = new int[26];
        for(int i = 0; i < s.length(); i++)  alph[s.charAt(i) - 'a']++;
        for(int i = 0; i < t.length(); i++)  alph[t.charAt(i) - 'a']--;
        for(int i : alph) if(i != 0) return false;
        return true;
}
```
这里再强调一下，散列表一般用与需要**K-V**思想的场景。落实在代码上分为两种情况：当散列表只需要保存value时，用数组就行；而当要同时保存key和value时，一般用HashMap。
## [49. 字母异位词分组²](https://leetcode-cn.com/problems/group-anagrams/)
给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**示例:**
```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
**说明：**

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。

### 解法：散列表（HashMap）
* 思路：由于所有 list 的字母组成相同，所以本题其实也包含了 K-V 的思想，可以用到散列表
  * 这里可以使用HashMap，key是String表示公有字符，value是List<String>表示key组成的不同字符串
  * 若当前字符串与key相同组成，则取出key的List然后add
  * 若不存在，则将这个key放入map，并新建一个List
* 细节
  * key：必须要将字符串，先化为数组进行排序（toCharrArray)，然后再化为字符串作为key(String.valueOf)
  * 在返回List<List<String>>时，new ArrayList<List<String>>(map.values())
```java
 public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>(); 
        for (int i = 0; i < strs.length; i++) {
            // string -> array -> string
            char[] c = strs[i].toCharArray();
            Arrays.sort(c);
            String key = String.valueOf(c);
            // 如果包含这个key，就将当前字符串加入相应list
            if (map.containsKey(key)) {
                map.get(key).add(strs[i]);
            // 如果不包含，就新建一个list，然后放入当前字符串
            }else {
                List<String> l = new ArrayList<>();
                l.add(strs[i]);
                map.put(key,l);
            }
        }
        // 注：这里要用map的value构造一个ArrayList
        return new ArrayList<List<String>>(map.values());
}
```

**代码优化**，优化了if-else，代码更简洁可读：
```java
public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>();
        for (int i = 0; i < strs.length; i++) {
            char[] c = strs[i].toCharArray();
            Arrays.sort(c);
            String key = String.valueOf(c);
            // 若不存在直接就新建一个空List
            if (!map.containsKey(key)) 
                map.put(key, new ArrayList<String>());   
            // 不管key有没有，都取出然后加入其List
            map.get(key).add(strs[i]);
        }
        return new ArrayList<List<String>>(map.values());
}
```

## [1. 两数之和¹](https://leetcode-cn.com/problems/two-sum/)
给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**示例:**
```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

### 1.解法一：枚举法
* 思路：没啥说的，就是枚举出所有结果
* 复杂度
	* Time：O(n^2)，66ms
	* Space：O(1)
```java
public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length - 1; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) 
                    return new int[]{i,j};
            }
        }
        return new int[0];
}
```
### 2.解法二：散列表（HashMap）
* 思路：为了将复杂度降到O(n)，可以通过空间换时间的思路，即引入一个容器。那要引入什么合适呢？List？Set？Map？因为要返回的是索引，所以这里其实包含了一个K-V关系，因此可以用一个HashMap，key是值，value是数组索引。
* 复杂度
	* Time：O(n），2ms
	* Space：O(n)，需要一个Map将数组中元素保存进去

```java
public int[] twoSum(int[] nums, int target) {
       HashMap<Integer,Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) 
        	// 如果map有与当前值匹配的，就直接取出索引返回，否则将当前值和索引加入map
            if (map.containsKey(target - nums[i])) 
                return new int[]{map.get(target - nums[i]),i};
            else 
                map.put(nums[i],i);
        return null;
}
```

## [13. 罗马数字转整数¹](https://leetcode-cn.com/problems/roman-to-integer/)

罗马数字包含以下七种字符: `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。

```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

例如， 罗马数字 2 写做 `II` ，即为两个并列的 1。12 写做 `XII` ，即为 `X` + `II` 。 27 写做 `XXVII`, 即为 `XX` + `V` + `II` 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 `IIII`，而是 `IV`。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 `IX`。这个特殊的规则只适用于以下六种情况：

- `I` 可以放在 `V` (5) 和 `X` (10) 的左边，来表示 4 和 9。
- `X` 可以放在 `L` (50) 和 `C` (100) 的左边，来表示 40 和 90。 
- `C` 可以放在 `D` (500) 和 `M` (1000) 的左边，来表示 400 和 900。

给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。

 

**示例 1:**

```
输入: "III"
输出: 3
```

**示例 2:**

```
输入: "IV"
输出: 4
```

**示例 3:**

```
输入: "IX"
输出: 9
```

**示例 4:**

```
输入: "LVIII"
输出: 58
解释: L = 50, V= 5, III = 3.
```

**示例 5:**

```
输入: "MCMXCIV"
输出: 1994
解释: M = 1000, CM = 900, XC = 90, IV = 4.
```

**提示：**

- 题目所给测试用例皆符合罗马数字书写规则，不会出现跨位等情况。
- IC 和 IM 这样的例子并不符合题目要求，49 应该写作 XLIX，999 应该写作 CMXCIX 。
- 关于罗马数字的详尽书写规则，可以参考 [罗马数字 - Mathematics ](https://b2b.partcommunity.com/community/knowledge/zh_CN/detail/10753/罗马数字#knowledge_article)。

### 解法：散列表

* 思路
  1. 本题一看就能想到K-V关系，而key是没关联的字符们，所以这里可以使用HahsMap。同时，核心问题变成了对 IV，IX，XL，XC，CD，CM的处理。
  2. 找规律发现，IV 整体会比 I+V 小2，IX 会比 I+X 小 2，XL 会比 X+L 小20，XC会比 X+C 小20，CD会比 C+D 小200，CM会比 C+M 小200。
  3. 所以就可以先对结果减去这些 2,20,200，然后再一个一个加
* 复杂度
  * Time：O（n），与字符串长度正相关
  * Space：O（1），罗马字符就8个，所以HashMap的大小是确定的

```java
class Solution {
    Map<Character, Integer> map = new HashMap<>(){ 
        // 在构造时进行初始化，{put}
        {
            put('I', 1);
            put('V', 5);
            put('X', 10);
            put('L', 50);
            put('C', 100);
            put('D', 500);
            put('M', 1000);
        }
    }; // 这里注意别忘记 ;
    

    public int romanToInt(String s) {
        int sum = 0;
		
        // 通过indexOf判断是否存在特殊，然后减去相应值
        if(s.indexOf("IV")!=-1){sum-=2;}
        if(s.indexOf("IX")!=-1){sum-=2;}
        if(s.indexOf("XL")!=-1){sum-=20;}
        if(s.indexOf("XC")!=-1){sum-=20;}
        if(s.indexOf("CD")!=-1){sum-=200;}
        if(s.indexOf("CM")!=-1){sum-=200;}
        
        // 遍历所有字符，累加即可
        for (char c : s.toCharArray()) {
            sum += map.get(c);
        }

        return sum;
    }
}
```

