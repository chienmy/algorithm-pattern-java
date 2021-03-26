# 滑动窗口

## 模板

```cpp
/* 滑动窗口算法框架 */
void slidingWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for (char c : t) need[c]++;

    int left = 0, right = 0;
    int valid = 0;
    while (right < s.size()) {
        // c 是将移入窗口的字符
        char c = s[right];
        // 右移窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            // 左移窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```

需要变化的地方

- 1、右指针右移之后窗口数据更新
- 2、判断窗口是否要收缩
- 3、左指针右移之后窗口数据更新
- 4、根据题意计算结果

## 示例

### 最小覆盖子串

> [76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)
>
> 给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串

```java
public String minWindow(String s, String t) {
    // 技巧：用数组代替Map
    // 保存滑动窗口字符集
    int[] winMap = new int[256];
    // 保存需要出现的字符集
    int[] tMap = new int[256];
    for (char c : t.toCharArray()) {
        tMap[c]++;
    }
    // 计算共出现了多少不同的字符
    int charNum = 0;
    for (int n : tMap) {
        if (n > 0) {
            charNum++;
        }
    }
    // 滑动窗口左右边界
    int left = 0;
    int right = 0;
    // 匹配数
    int match = 0;
    // 窗口调整前暂存原窗口边界
    int start = 0;
    int end = 0;
    // 窗口长度的最小值
    int minValue = Integer.MAX_VALUE;
    while (right < s.length()) {
        char c = s.charAt(right);
        // 在需要的字符集里面，添加到窗口字符集里面
        if (tMap[c] != 0) {
            winMap[c]++;
            // 如果当前字符的数量匹配需要的字符的数量，则match值+1
            if (tMap[c] == winMap[c]) {
                match++;
            }
        }
        right++;
        // 当所有字符数量都匹配之后，开始缩紧窗口
        while (match == charNum) {
            // 获取结果
            if (right - left < minValue) {
                minValue = right - left;
                start = left;
                end = right;
            }
            char leftChar = s.charAt(left);
            // 左指针指向不在需要字符集则直接跳过
            if (tMap[leftChar] != 0) {
                // 左指针指向字符数量和需要的字符相等时，右移之后match值就不匹配则减一
                if (winMap[leftChar] == tMap[leftChar]) {
                    match--;
                }
                winMap[leftChar]--;
            }
            left++;
        }
    }
    if (minValue == Integer.MAX_VALUE) {
        return "";
    }
    return s.substring(start, end);
}
```

### 字符串的排列

> [567. 字符串的排列](https://leetcode-cn.com/problems/permutation-in-string/)
>
> 给定两个字符串  **s1**  和  **s2**，写一个函数来判断  **s2**  是否包含  **s1 **的排列。

```java
public boolean checkInclusion(String s1, String s2) {
    // 保存滑动窗口字符集
    int[] winMap = new int[256];
    // 保存需要出现的字符集
    int[] tMap = new int[256];
    for (char c : s1.toCharArray()) {
        tMap[c]++;
    }
    // 计算共出现了多少不同的字符
    int charNum = 0;
    for (int n : tMap) {
        if (n > 0) {
            charNum++;
        }
    }
    // 左右边界
    int left = 0;
    int right = 0;
    // 已经匹配的字母数
    int match = 0;

    while (right < s2.length()) {
        char c = s2.charAt(right);
        right++;
        if (tMap[c] != 0) {
            winMap[c]++;
            if (winMap[c] == tMap[c]) {
                match++;
            }
        }
        // 窗口收缩
        while (match == charNum) {
            c = s2.charAt(left);
            if (tMap[c] != 0) {
                if (winMap[c] == tMap[c]) {
                    match--;
                }
                winMap[c]--;
            }
            left++;
            // 子串是一个排列，即子串长度等于s1
            if (right - left + 1 == s1.length()) {
                return true;
            }
        }
    }
    return false;
}
```

### 找到字符串中所有字母异位词

> [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)
>
> 给定一个字符串  **s **和一个非空字符串  **p**，找到  **s **中所有是  **p **的字母异位词的子串，返回这些子串的起始索引。

```java
public List<Integer> findAnagrams(String s, String p) {
    int[] pMap = new int[256];
    int[] winMap = new int[256];
    int charNum = 0;
    for (char c : p.toCharArray()) {
        if (pMap[c] == 0) charNum++;
        pMap[c]++;
    }
    int left = 0;
    int right = 0;
    int match = 0;
    List<Integer> result = new ArrayList<>();
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        if (pMap[c] != 0) {
            winMap[c]++;
            if (pMap[c] == winMap[c]) {
                match++;
            }
        }
        // 缩紧窗口
        while (match == charNum) {
            c = s.charAt(left);
            if (pMap[c] != 0) {
                if (pMap[c] == winMap[c]) {
                    match--;
                }
                winMap[c]--;
            }
            // 当窗口长度和字符串长度匹配时，满足条件
            if (right - left == p.length()) {
                result.add(left);
            }
            left++;
        }
    }
    return result;
}
```

### 无重复字符的最长子串

> [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
> 
>给定一个字符串，请你找出其中不含有重复字符的**最长子串**的长度。

```java
public int lengthOfLongestSubstring(String s) {
    if (s.length() < 2) {
        return s.length();
    }
    int[] winMap = new int[256];
    int left = 0;
    int right = 0;
    int maxLen = 0;
    // 1、右指针右移 
    while (right < s.length()) {
        char c = s.charAt(right);
        right++;
        winMap[c]++;
        // 2、根据题意收缩窗口  
        while (winMap[c] > 1) {
            // 3、左指针右移更新窗口
            char d = s.charAt(left);
            left++;
            winMap[d]--;
        }
        // 4、根据题意计算结果
        maxLen = Math.max(right - left, maxLen);
    }
    return maxLen;
}
```

## 总结

- 和双指针题目类似，更像双指针的升级版，滑动窗口核心点是维护一个窗口集，根据窗口集来进行处理
- 核心步骤
  - right 右移
  - 收缩
  - left 右移
  - 求结果
