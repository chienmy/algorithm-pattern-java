# 回溯算法

## 背景

回溯法（backtrack）常用于遍历列表所有子集，是 DFS 深度搜索一种，一般用于全排列，穷尽所有可能，遍历的过程实际上是一个决策树的遍历过程。时间复杂度一般 O(N!)，它不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高。

## 模板

```go
result = []
func backtrack(选择列表,路径):
    if 满足结束条件:
        result.add(路径)
        return
    for 选择 in 选择列表:
        做选择
        backtrack(选择列表,路径)
        撤销选择
```

核心就是从选择列表里做一个选择，然后一直递归往下搜索答案，如果遇到路径不通，就返回来撤销这次选择。

## 常见例题

### 集合类

#### 子集

> [78. 子集](https://leetcode-cn.com/problems/subsets/)
>
> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

```java
public List<List<Integer>> subsets(int[] nums) {
	// 保存中间结果
    List<Integer> subSet = new ArrayList<>();
    // 保存最终结果
    List<List<Integer>> result = new ArrayList<>();
    backtrack(nums, 0, subSet, result);
    return result;
}

// nums 给定的集合
// pos 下次添加到集合中的元素位置索引
// subSet 临时结果集合(每次需要复制保存)
// result 最终结果
private void backtrack(int[] nums, int pos, List<Integer> subSet, List<List<Integer>> result) {
    // 把临时结果复制出来保存到最终结果
    result.add(new ArrayList<>(subSet));
    for (int i = pos; i < nums.length; i++) {
        // 选择、处理结果、再撤销选择
        subSet.add(nums[i]);
        backtrack(nums, i+1, subSet, result);
        subSet.remove(subSet.size() - 1);
    }
}
```

#### 子集 II

>  [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)
>
> 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。说明：解集不能包含重复的子集。

```java
public List<List<Integer>> subsets(int[] nums) {
	// 保存中间结果
    List<Integer> subSet = new ArrayList<>();
    // 保存最终结果
    List<List<Integer>> result = new ArrayList<>();
	// 先排序
    Arrays.sort(nums);
    backtrack(nums, 0, subSet, result);
    return result;
}

// nums 给定的集合
// pos 下次添加到集合中的元素位置索引
// subSet 临时结果集合(每次需要复制保存)
// result 最终结果
private void backtrack(int[] nums, int pos, List<Integer> subSet, List<List<Integer>> result) {
    // 把临时结果复制出来保存到最终结果
    result.add(new ArrayList<>(subSet));
    for (int i = pos; i < nums.length; i++) {
        // 排序之后，如果再遇到重复元素，则不选择此元素
        if (i != pos && nums[i] == nums[i-1]) {
            continue;
        }
        // 选择、处理结果、再撤销选择
        subSet.add(nums[i]);
        backtrack(nums, i+1, subSet, result);
        subSet.remove(subSet.size() - 1);
    }
}
```

### 排列类

#### 全排列

> [46. 全排列](https://leetcode-cn.com/problems/permutations/)
>
> 给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

思路：需要记录已经选择过的元素，满足条件的结果才进行返回。这里要注意在做选择时记录，回溯时撤销。

```java
public List<List<Integer>> permute(int[] nums) {
    List<Integer> list = new ArrayList<>();
    List<List<Integer>> result = new ArrayList<>();
    // 标记这个元素是否已经添加到结果集
    boolean[] visited = new boolean[nums.length];
    backtrack(nums, visited, list, result);
    return result;
}

// nums 输入集合
// visited 当前递归标记过的元素
// list 临时结果集(路径)
// result 最终结果
private void backtrack(int[] nums, boolean[] visited, List<Integer> list, List<List<Integer>> result) {
    if (list.size() == nums.length) {
        result.add(new ArrayList<>(list));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
         // 已经添加过的元素，直接跳过
        if (visited[i]) {
            continue;
        }
        // 添加元素
        list.add(nums[i]);
        visited[i] = true;
        backtrack(nums, visited, list, result);
        // 移除元素
        list.remove(list.size() - 1);
        visited[i] = false;
    }
}
```

#### 全排列 II

> [47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)
>
> 给定一个可包含重复数字的序列，返回所有不重复的全排列。

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<Integer> list = new ArrayList<>();
    List<List<Integer>> result = new ArrayList<>();
	// 标记这个元素是否已经添加到结果集
    boolean[] visited = new boolean[nums.length];
    // 先排序
    Arrays.sort(nums);
    backtrack(nums, visited, list, result);
    return result;
}

// nums 输入集合
// visited 当前递归标记过的元素
// list 临时结果集
// result 最终结果
private void backtrack(int[] nums, boolean[] visited, List<Integer> list, List<List<Integer>> result) {
    if (list.size() == nums.length) {
        result.add(new ArrayList<>(list));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
		// 已经添加过的元素，直接跳过
        if (visited[i]) {
            continue;
        }
        // 上一个元素和当前相同，并且没有访问过就跳过
        if (i != 0 && nums[i] == nums[i-1] && !visited[i-1]) {
            continue;
        }
        list.add(nums[i]);
        visited[i] = true;
        backtrack(nums, visited, list, result);
        list.remove(list.size() - 1);
        visited[i] = false;
    }
}

```

### 组合类

#### 组合总和

> [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)
>
> 给定一个**无重复元素**的数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。
>
> `candidates` 中的数字可以无限制重复被选取。
>
> **说明：**
>
> - 所有数字（包括 `target`）都是正整数。
> - 解集不能包含重复的组合。 

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<Integer> answer = new ArrayList();
    List<List<Integer>> result = new ArrayList();
    // 先排序
    Arrays.sort(candidates);
    backtrack(candidates, 0, target, answer, result);
    return result;
}

// candidates 输入集合
// pos 当前标记位置，标记前的元素不再考虑
// target 求和目标
// answer 临时解法
// result 最终结果
private void backtrack(int[] candidates, int pos, int target, List<Integer> answer, List<List<Integer>> result) {
    if (target == 0) {
        result.add(new ArrayList<>(answer));
    }
    for (int i = pos; i < candidates.length; i++) {
        // 剪枝：后续元素都比目标大，直接break（比continue要快）
        if (candidates[i] > target) {
            break;
        }
        // 添加元素
        answer.add(candidates[i]);
        // 元素可以重复取，所以从当前位置继续
        backtrack(candidates, i, target - candidates[i], answer, result);
        // 移除元素
        answer.remove(answer.size() - 1);
    }
}
```

#### 电话号码的字母组合

> [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
>
> 给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。
>
> 数字到字母的映射与电话按键相同

```java
// 记录数字到字母的映射
private final static Map<Character, String> map = new HashMap<>();

static {
    map.put('2', "abc");
    map.put('3', "def");
    map.put('4', "ghi");
    map.put('5', "jkl");
    map.put('6', "mno");
    map.put('7', "pqrs");
    map.put('8', "tuv");
    map.put('9', "wxyz");
}

public List<String> letterCombinations(String digits) {
    StringBuilder builder = new StringBuilder();
    List<String> result = new ArrayList<>();
    backtrack(digits, 0, builder, result);
    return result;
}

private void backtrack(String digits, int pos, StringBuilder builder, List<String> result) {
    // 结束条件：到达末尾
    if (pos == digits.length()) {
        // 如果原字符串为空则没有对应的字母组合
        if (pos != 0) {
            result.add(builder.toString());
        }
        return;
    }
    for (char c : map.get(digits.charAt(pos)).toCharArray()) {
        builder.append(c);
        backtrack(digits, pos + 1, builder, result);
        builder.deleteCharAt(builder.length() - 1);
    }
}
```
