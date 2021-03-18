# 二叉树

## 二叉树遍历

**前序遍历**：[144. 二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

先访问根节点，再前序遍历左子树，再前序遍历右子树

**中序遍历**：[94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

先中序遍历左子树，再访问根节点，再中序遍历右子树

**后序遍历**：[145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

先后序遍历左子树，再后序遍历右子树，再访问根节点

注意点

- 以根访问顺序决定是什么遍历
- 左子树都比右子树优先遍历

### 递归遍历

```java
// 递归遍历写法，以前序遍历为例
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new LinkedList<>();
    traverse(root, result);
    return result;
}

public void traverse(TreeNode p, List<Integer> result) {
    if (p == null) {
        return;
    }
    // 其他遍历调整这里的语句顺序即可
    result.add(p.val);
    traverse(p.left, result);
    traverse(p.right, result);
}
```

### 前序非递归

```java
// 通过非递归遍历
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
    TreeNode p = root;
    while (p != null || ! stack.isEmpty()) {
        while (p != null) {
            // 前序遍历，所以先保存结果
            result.add(p.val);
            stack.push(p);
            p = p.left;
        }
        // pop出栈顶元素
        if (! stack.isEmpty()) {
            p = stack.pop();
            p = p.right;
        }
    }
    return result;
}
```

### 中序非递归

```java
// 思路：通过stack 保存已经访问的元素，用于原路返回
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new LinkedList<>();
    Stack<TreeNode> stack = new Stack<>();
    TreeNode p = root;
    while (p != null || ! stack.isEmpty()) {
        if (p != null) {
            stack.push(p);
            p = p.left;
        } else {
            TreeNode node = stack.pop();
            result.add(node.val);
            p = node.right;
        }
    }
    return result;
}
```

### 后序非递归

```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode p = root;
    // 通过lastVisit标识右子节点是否已经弹出
    TreeNode lastVisit = root;
    while (p != null || !stack.isEmpty()) {
        while (p != null) {
            stack.push(p);
            p = p.left;
        }
        //查看当前栈顶元素
        p = stack.peek();
        //如果其右子树也为空，或者右子树已经访问，则可以访问
        if (p.right == null || p.right == lastVisit) {
            result.add(p.val);
            stack.pop();
            // 标记当前这个节点已经弹出过
            lastVisit = p;
            p = null;
        } else {
            //否则继续遍历右子树
            p = p.right;
        }
    }
    return result;
}
```

注意点

- 核心就是：根节点必须在右节点弹出之后，再弹出

### DFS 深度搜索

> [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)
>
> 给定一个二叉树，返回所有从根节点到叶子节点的路径。

```java
public List<String> binaryTreePaths(TreeNode root) {
    StringBuilder path = new StringBuilder();
    List<String> paths = new LinkedList<>();
    dfs(root, path, paths);
    return paths;
}

public void dfs(TreeNode p, StringBuilder path, List<String> paths) {
    if (p == null) {
        return;
    }
    path.append(p.val);
    // 当前节点是叶子节点
    if (p.left == null && p.right == null) {
         // 把路径加入结果
        paths.add(path.toString());
    } else {
        path.append("->");
        // 这里需要复制创建新的StringBuilder对象
        dfs(p.left, new StringBuilder(path), paths);
        dfs(p.right, new StringBuilder(path), paths);
    }
}
```

### BFS 层次遍历

```java
public List<Integer> levelOrder(TreeNode root) {
    List<Integer> result = new LinkedList<>();
    if (root == null) {
        return result;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        TreeNode p = queue.pop();
        result.add(p);
        if (p.left != null) {
            queue.add(p.left);
            numList.add(p.left.val);
        }
        if (p.right != null) {
            queue.add(p.right);
            numList.add(p.right.val);
        }
    }
    return result
}
```

## 二叉树分治

先分别处理局部，再合并结果

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```java
public ResultType traversal(TreeNode root) {
    // null or leaf
    if (root == null) {
        // do something and return
    }
    // Divide
    ResultType left = traversal(root.Left)
    ResultType right = traversal(root.Right)
    // Conquer
    ResultType result = Merge from left and right
    return result
}
```

### 典型示例

> [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)
>
> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

```java
public boolean isValidBST(TreeNode root) {
    return divideAndConquer(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

private boolean divideAndConquer(TreeNode p, long min, long max) {
    if (p == null) return true;
    // 返回条件
    if (p.val <= min || max <= p.val) {
        return false;
    }
    // 分治(Divide)
    boolean left = divideAndConquer(p.left, min, p.val);
    boolean right = divideAndConquer(p.right, p.val, max);
    // 合并结果(Conquer)
    return left && right;
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过参数传入，后者一般递归返回结果最后合并

### 常见题目

#### 二叉树的最大深度

> [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
>
> 给定一个二叉树，找出其最大深度。
> 
> 二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

思路：分治法

```java
public int maxDepth(TreeNode root) {
    // 返回条件处理
    if (root == null) {
        return 0;
    }
    // divide：分左右子树分别计算
    // conquer：合并左右子树结果，即取二者中的最大值加一
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

#### 平衡二叉树

> [110. 平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)
>
> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，因为需要返回是否平衡及高度，要么返回两个数据，要么合并两个数据，所以用-1 表示不平衡，>0 表示树高度（二义性：一个变量有两种含义）。

```java
public boolean isBalanced(TreeNode root) {
    return maxDepth(root) >= 0;
}

private int maxDepth(TreeNode p) {
    if (p == null) {
        return 0;
    }
    int left = maxDepth(p.left);
    int right = maxDepth(p.right);
    if (left < 0 || right < 0 || Math.abs(left - right) > 1) {
        return -1;
    } else {
        return Math.max(left, right) + 1;
    }
}
```

注意

> 一般工程中，结果通过两个变量来返回，不建议用一个变量表示两种含义

#### 二叉树中的最大路径和

> [124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
>
> **路径** 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。
>
> **路径和** 是路径中各节点值的总和。
>
> 给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

思路：分治法，分为三种情况：左子树最大路径和最大，右子树最大路径和最大，左右子树最大加根节点最大，需要保存两个变量：一个保存子树最大路径和，一个保存左右加根节点和，然后比较这个两个变量选择最大值即可

```java
public int maxPathSum(TreeNode root) {
    return maxSum(root)[1];
}

// 返回的数组意义：int[0]表示单边最大值，int[1]表示所有情况的最大值（单边或者两个单边+根的值）
private int[] maxSum(TreeNode p) {
    // check
    if (p == null) {
        return new int[]{0, Integer.MIN_VALUE};
    }
    // Divide
    int[] left = maxSum(p.left);
    int[] right = maxSum(p.right);
    int singleSum, bothSum;
    // Conquer
    // 求单边最大值
    if (left[0] > right[0]) {
        singleSum = Math.max(left[0] + p.val, 0);
    } else {
        singleSum = Math.max(right[0] + p.val, 0);
    }
    // 求所有情况（两个子树的所有情况、两个单边最大值与根之和）的最大值
    bothSum = Math.max(left[1], right[1]);
    bothSum = Math.max(bothSum, left[0] + right[0] + p.val);
    return new int[]{singleSum, bothSum};
}
```

#### 二叉树的最近公共祖先

> [235. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
> 
> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // check
    if (root == null) {
        return null;
    }
    // 相等 直接返回root节点即可
    if (root == p || root == q) {
        return root;
    }
    // Divide
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    // Conquer
    // 左右两边都不为空，则根节点为祖先
    if (left != null && right != null) {
        return root;
    } else if (left != null) {
        return left;
    } else {
        return right;
    }
}
```

## BFS 应用

### 常见题目

#### 二叉树的层序遍历

> [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
> 
> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new LinkedList<>();
    if (root == null) {
        return result;
    }
    LinkedList<TreeNode> queue = new LinkedList<>();
    LinkedList<Integer> numList = new LinkedList<>();
    queue.add(root);
    numList.add(root.val);
    while (! queue.isEmpty()) {
        // 保存这一层的元素
        result.add(numList);
        numList = new LinkedList<>();
        // 记录当前层有多少元素（遍历当前层，再添加下一层）
        int len = queue.size();
        for (int i = 0; i < len; i++) {
            // 出队列
            TreeNode p = queue.pop();
            if (p.left != null) {
                queue.add(p.left);
                numList.add(p.left.val);
            }
            if (p.right != null) {
                queue.add(p.right);
                numList.add(p.right.val);
            }
        }
    }
    return result;
}
```

#### 二叉树的层序遍历 II

> [107. 二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
> 
> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可，在往result内添加元素之前使用`Collections.reverse`方法翻转列表。

#### 二叉树的锯齿形层序遍历

> [103. 二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
> 
> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

思路：在层级遍历的基础上，判断这一层是否需要翻转，在往result内添加元素之前加入以下代码：

```java
if (result.size() % 2 != 0) {
    Collections.reverse(numList);
}
```

## 二叉搜索树应用

### 常见题目

#### 二叉搜索树中的插入操作

> [701. 二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
> 
> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```java
// DFS查找插入位置
public TreeNode insertIntoBST(TreeNode root, int val) {
    if (root == null) {
        return new TreeNode(val);
    }
    if (root.val > val) {
        root.left = insertIntoBST(root.left, val);
    } else {
        root.right = insertIntoBST(root.right, val);
    }
    return root;
}
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层次遍历
