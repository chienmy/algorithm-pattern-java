 # 栈和队列

## 简介

栈的特点是后入先出

根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于树的非递归遍历、 DFS 深度搜索。

队列常用于BFS广度搜索，很少单独考察。

## 基本应用

### 逆波兰表达式求值

> [150. 逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
>
> **波兰表达式计算** > **输入:** `["2", "1", "+", "3", "*"]` > **输出:** 9
>
> **解释:** `((2 + 1) * 3) = 9`

```java
public int evalRPN(String[] tokens) {
    Stack<Integer> stack = new Stack<>();
    for (String s : tokens) {
        if ("+".equals(s) || "-".equals(s) || "*".equals(s) || "/".equals(s)) {
            int a = stack.pop();
            int b = stack.pop();
            if ("+".equals(s)) stack.push(b + a);
            else if ("-".equals(s)) stack.push(b - a);
            else if ("*".equals(s)) stack.push(b * a);
            // 注意：b为被除数，a为除数
            else if ("/".equals(s)) stack.push(b / a);
        } else {
            // 转为数字
            stack.push(Integer.parseInt(s));
        }
    }
    return stack.pop();
}
```

### 有效的括号

> [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)
>
> 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。
>
> 有效字符串需满足：
>
> 1. 左括号必须用相同类型的右括号闭合。
> 2. 左括号必须以正确的顺序闭合。

```java
public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    for (char c : s.toCharArray()) {
        if (c == '(') stack.push(')');
        else if (c == '[') stack.push(']');
        else if (c == '{') stack.push('}');
        else if (stack.isEmpty() || c != stack.pop()) {
            return false;
        }
    }
    return stack.isEmpty();
}
```

## 单调栈

单调栈：栈内元素保持单调递增或单调递减的栈

**（以单调递增栈为例）**

入栈规则：

- 新元素比栈顶元素小：直接入栈

- 新元素比栈顶元素大：弹出栈内元素知道栈顶比新元素小（或空栈）

出栈意义：

- 需要出栈时，入栈的新元素是出栈元素右方第一个比出栈元素小的元素

- 出栈后，新的栈顶是出栈元素左侧最大的数

技巧：最后添加一个值为0的哨兵节点，可以在最后强制所有元素出栈。

以下分别使用了单调递增栈和单调递减栈

### 柱状图中最大的矩形

> [84. 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)
>
> 给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
>
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。

```java
public int largestRectangleArea(int[] heights) {
    if (heights.length == 0) {
        return 0;
    }
    Stack<Integer> stack = new Stack<>();
    int max = 0;
    // 当前高度小于栈，则将栈内元素都弹出计算面积
    for (int i = 0; i <= heights.length; i++) {
        int cur = 0;
        if (i < heights.length) {
            cur = heights[i];
        }
        while (stack.size() != 0 && cur <= heights[stack.peek()]) {
            int index = stack.pop();
            int h = heights[index];
            // 计算宽度
            int w = i;
            if (stack.size() != 0) {
                int peek = stack.peek();
                w = i - peek - 1;
            }
            max = Math.max(max, h * w);
        }
         // 记录索引即可获取对应元素
        stack.push(i);
    }
    return max;
}
```

### 接雨水

> [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)
>
> 给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```java
public int trap(int[] height) {
    int sum = 0;
    Stack<Integer> stack = new Stack<>();
    int current = 0;
    while (current < height.length) {
        //如果栈不空并且当前指向的高度大于栈顶高度就一直循环
        while (!stack.empty() && height[current] > height[stack.peek()]) {
            int h = height[stack.peek()]; //取出要出栈的元素
            stack.pop(); //出栈
            if (stack.empty()) { // 栈空就出去
                break;
            }
            int distance = current - stack.peek() - 1; //两堵墙之前的距离。
            int min = Math.min(height[stack.peek()], height[current]);
            sum = sum + distance * (min - h);
        }
        stack.push(current); //当前指向的墙入栈
        current++; //指针后移
    }
    return sum;
}
```

## 设计类问题

> [2. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

```java
class MyQueue {

    private Stack<Integer> stack1 = new Stack<>();
    private Stack<Integer> stack2 = new Stack<>();

    /** Initialize your data structure here. */
    public MyQueue() {
        
        
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        while (! stack2.isEmpty()) {
            int val = stack2.pop();
            stack1.push(val);
        }
        stack1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        while (! stack1.isEmpty()) {
            int val = stack1.pop();
            stack2.push(val);
        }
        if (stack2.isEmpty()) return -1;
        return stack2.pop();
    }
    
    /** Get the front element. */
    public int peek() {
         while (! stack1.isEmpty()) {
            int val = stack1.pop();
            stack2.push(val);
        }
        if (stack2.isEmpty()) return -1;
        return stack2.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

