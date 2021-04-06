 # 并查集

## 简介

并查集用于处理不相交集合的合并与查询问题，常见操作有：

- 查询：查询元素属于哪个集合，可用于判断元素是否在一个集合中
- 合并：合并两个集合

应用场景：动态连通性的判断，且不需要给出具体路径。

## 数据结构

### 初始化

id数组存放的是节点的组号，初始化时将每个节点单独分为一组。

```java
private int[] id;

public DisjoinSet(int size) {
    id = new int[size];
    for(int i = 0; i < size; i++)
	    id[i] = i;
}
```

### Quick-Find

由于使用整数表示节点，可以通过数组实现节点到组编号的映射。

```java
public void union(int p, int q) { 
    // 获得p和q的组号
    int pID = id[p];
    int qID = id[q];
    // 如果两个组号相等，直接返回
    if (pID == qID) return;
    // 遍历一次，改变组号使他们属于一个组
    for (int i = 0; i < id.length; i++)
        if (id[i] == pID) id[i] = qID;
    count--;
}
```

### Quick-Union

id数组存放的是节点的父节点索引，根节点的父节点是自身，通过这点判断能到达根节点。

```java
private int find(int p) { 
	// 寻找p节点所在组的根节点，根节点具有性质id[root] = root
	while (p != id[p]) p = id[p];
	return p;
}
public void union(int p, int q) { 
	// Give p and q the same root.
	int pRoot = find(p);
	int qRoot = find(q);
	if (pRoot == qRoot) 
		return;
	// 将一棵树(即一个组)变成另外一课树(即一个组)的子树
	id[pRoot] = qRoot;    
	count--;
}
```

### Weighted Quick Union

保存两棵树的大小，每次将小的合并到大的树中。

## 常见例题

### 冗余连接

> [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)
>
> 在本问题中, 树指的是一个连通且无环的**无向**图。
>
> 输入一个图，该图由一个有着N个节点 (节点值不重复1, 2, ..., N) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。
>
> 结果图是一个以`边`组成的二维数组。每一个`边`的元素是一对`[u, v]` ，满足 `u < v`，表示连接顶点`u` 和`v`的**无向**图的边。
>
> 返回一条可以删去的边，使得结果图是一个有着N个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 `[u, v]` 应满足相同的格式 `u < v`。

```java
private int[] parent;
private int[] size;

private int find(int p) {
    while (p != parent[p]) {
        parent[p] = parent[parent[p]];
        p = parent[p];
    }
    return p;
}

private boolean union(int p, int q) {
    int pRoot = find(p);
    int qRoot = find(q);
    // 在合并前判断是否属于相同的连通分量
    if (pRoot == qRoot) {
        return true;
    }
    // Weighted Quick Union
    if (size[pRoot] < size[qRoot]) { 
        parent[pRoot] = qRoot; 
        size[qRoot] += size[pRoot]; 
    } else { 
        parent[qRoot] = pRoot; 
        size[pRoot] += size[qRoot]; 
    }
    return false;
}

public int[] findRedundantConnection(int[][] edges) {
    parent = new int[edges.length + 1];
    size = new int[edges.length + 1];
    // 并查集初始化
    for (int i = 0; i < parent.length; i++) {
        parent[i] = i;
        size[i] = 1;
    }
    for (int[] arr : edges) {
        if (union(arr[0], arr[1])) {
            // 如果已经连通说明当前这条边是多余的
            return arr;
        }
    }
    return new int[]{};
}
```

### 打砖块

> [803. 打砖块](https://leetcode-cn.com/problems/bricks-falling-when-hit/)
>
>有一个 `m x n` 的二元网格，其中 `1` 表示砖块，`0` 表示空白。砖块 **稳定**（不会掉落）的前提是：
>
>- 一块砖直接连接到网格的顶部，或者
>- 至少有一块相邻（4 个方向之一）砖块 **稳定** 不会掉落时
>
>给你一个数组 `hits` ，这是需要依次消除砖块的位置。每当消除 `hits[i] = (rowi, coli)` 位置上的砖块时，对应位置的砖块（若存在）会消失，然后其他的砖块可能因为这一消除操作而掉落。一旦砖块掉落，它会立即从网格中消失（即，它不会落在其他稳定的砖块上）。
>
>返回一个数组 `result` ，其中 `result[i]` 表示第 `i` 次消除操作对应掉落的砖块数目。
>
>**注意**，消除可能指向是没有砖块的空白位置，如果发生这种情况，则没有砖块掉落。

思路：并查集是用于合并连通分量，而砖块消失实质上是拆分连通分量，因此这题应当逆向考虑，即先打碎所有砖块，再从后向前添加砖块（合并连通分量），添加后计算会增加多少个节点与根节点相连。

首先给出并查集的定义，`size`既表示连通分量的大小，也用于合并时的权重判断。

```java
class DisJoinSet {

    private final int[] parent;
    private final int[] size;

    // 初始化并查集，根节点为自身，大小为1
    public DisJoinSet(int len) {
        parent = new int[len];
        size = new int[len];
        for (int i = 0; i < len; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    // 查找连通分量的根节点
    public int find(int p) {
        while (p != parent[p]) {
            parent[p] = parent[parent[p]];
            p = parent[p];
        }
        return p;
    }

    // 合并两个节点对应的连通分量
    public void merge(int p, int q) {
        int pRoot = find(p);
        int qRoot = find(q);
        // 在合并前判断是否属于相同的连通分量
        if (pRoot != qRoot) {
            if (size[pRoot] < size[qRoot]) {
                parent[pRoot] = qRoot;
                size[qRoot] += size[pRoot];
            } else {
                parent[qRoot] = pRoot;
                size[pRoot] += size[qRoot];
            }
        }
    }

    // 获取连通分量的大小
    public int getSize(int n) {
        int root = find(n);
        return size[root];
    }

}
```

实际使用中将二维数组映射为一维数组，并在最后增加一项作为“房顶节点”，与其相连的节点均不会下落。下面是算法逻辑：

```java
public int[] hitBricks(int[][] grid, int[][] hits) {
    int h = grid.length;
    int w = grid[0].length;
    int[] result = new int[hits.length];
    // 保存当前的砖块状态
    int[][] status = new int[h][w];
    DisJoinSet disJoinSet = new DisJoinSet(h * w + 1);
    // 将status初始化为最终的状态
    for (int i = 0; i < h; i++) {
        status[i] = grid[i].clone();
    }
    for (int[] pos : hits) {
        status[pos[0]][pos[1]] = 0;
    }
    // 根据最后的状态构造并查集
    for (int i = 0; i < h; i++) {
        for (int j = 0; j < w; j++) {
            if (status[i][j] == 0) {
                continue;
            }
            if (i == 0) {
                // 一块砖直接连接到网格的顶部
                disJoinSet.merge( h * w, j);
            } else {
                // 上方有相邻砖块
                if (status[i - 1][j] == 1) {
                    disJoinSet.merge((i - 1) * w + j, i * w + j);
                }
                // 左侧有相邻砖块
                if (j > 0 && status[i][j - 1] == 1) {
                    disJoinSet.merge(i * w + j - 1, i * w + j);
                }
            }
        }
    }
    // 从后向前把砖块补上
    int[][] directions = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
    for (int i = hits.length - 1; i >= 0; i--) {
        int r = hits[i][0];
        int c = hits[i][1];
        if (grid[r][c] == 0) {
            result[i] = 0;
        } else {
            // 添加砖块前与房顶相连通的节点数目
            int prev = disJoinSet.getSize(h * w);
            // 顶部第一行的情况
            if (r == 0) {
                disJoinSet.merge(c, h * w);
            }
            // 处理四周的节点
            for (int[] direction : directions) {
                int nr = r + direction[0];
                int nc = c + direction[1];

                if (nr >= 0 && nr < h && nc >= 0 && nc < w && status[nr][nc] == 1) {
                    disJoinSet.merge(r * w + c, nr * w + nc);
                }
            }
            // 获得增加的节点数，即为正向操作时这一步下落的节点数
            result[i] = Math.max(0, disJoinSet.getSize(h * w) - prev - 1);
            status[r][c] = 1;
        }
    }
    return result;
}
```

