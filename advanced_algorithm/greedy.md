# 贪心算法

贪心算法在每一步选择中都采取在当前状态下最好或最优的选择，从而使得结果也是最优解，适合去解决具有最优子结构的问题。

## 应用场景

最优子结构的特征：

- 每一次操作对结果直接产生影响
- 不依赖于之前的操作
- 子问题的最优解是全局最优解的一部分

## 常见例题

### 跳跃游戏

> [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)
>
> 给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 判断你是否能够到达最后一个下标。

思路：每个格子`i`作为起跳点最远能跳到`nums[i]`处，所以遍历数组得到最远距离并与数组长度进行比较即可。

这题只需要判断能否到达终点而无需给出具体路径，所以不需要用回溯的方法。

```java
public boolean canJump(int[] nums) {
    int maxLen = 0;
    for (int i = 0; i < nums.length; i++) {
        // 当前格子已经无法跳到
        if (i > maxLen) return false;
        // 更新能跳到的最远距离
        maxLen = Math.max(maxLen, i + nums[i]);
    }
    return true;
}
```

### 跳跃游戏 II

>  [45. 跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)
>
>给定一个非负整数数组，你最初位于数组的第一个位置。
>
>数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
>你的目标是使用最少的跳跃次数到达数组的最后一个位置。

思路：确保每次跳跃选择的格子都有最远的跳跃范围。

```java
public int jump(int[] nums) {
    int steps = 0;
    int start = 0;
    int end = 1;
    while (end < nums.length) {
        // 确定最远的跳跃范围
        int maxPosition = 0;
        for (int i = start; i < end; i++) {
            maxPosition = Math.max(maxPosition, i + nums[i]);
        }
        start = end;
        end = maxPosition + 1;
        // 步数增加
        steps++;
    }
    return steps;
}
```

### 分发饼干

> [455. 分发饼干](https://leetcode-cn.com/problems/assign-cookies/)
>
> 对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是尽可能满足越多数量的孩子，并输出这个最大数值。

思路：先排序，再贪心，让饼干优先满足胃口更多的孩子

```java
public int findContentChildren(int[] g, int[] s) {
    Arrays.sort(g);
    Arrays.sort(s);
    int p = 0;
    int q = 0;
    while (p < g.length && q < s.length) {
        if (g[p] <= s[q]) {
            p++;
        }
        q++;
    }
    return p;
}
```

