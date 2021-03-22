# 排序

## 常考排序

### 快速排序

```java
public void quickSort(int[] nums) {
    // 思路：把一个数组分为左右两段，左段小于右段
    quickSort(nums, 0, nums.length - 1);
}

// 原地交换，所以传入交换索引
private void quickSort(int[] nums, int start, int end) {
    if (start < end) {
        // 分治法：divide
        int pivot = partition(nums, start, end);
        quickSort(nums, 0, pivot - 1);
        quickSort(nums, pivot + 1, end);
    }
}

// 分区
private int partition(int[] nums, int start, int end) {
    // 选取最后一个元素作为基准pivot
    int p = nums[end];
    int i = start;
    // 最后一个值就是基准所以不用比较
    for (int j = start; j < end; j++) {
        if (nums[j] < p) {
            swap(nums, i, j);
            i++;
        }
    }
    // 把基准值换到中间
    swap(nums, i, end);
    return i;
}

// 交换两个元素
private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

### 归并排序

```java
public void mergeSort(int[] nums) {
    mergeSort(nums, 0, nums.length);
}

private void mergeSort(int[] nums, int start, int end) {
    if (end - start <= 1) {
        return;
    }
    // 分治法：divide 分为两段
    int mid = start + (end - start) / 2;
    mergeSort(nums, start, mid);
    mergeSort(nums, mid, end);
    // 合并两段数据
    merge(nums, start, mid, end);
}

private void merge(int[] nums, int start, int mid, int end) {
    int[] temp = new int[end - start];
    // 两边数组合并游标
    int l = start;
    int r = mid;
    int k = 0;
    // 注意不能越界
    while (l < mid && r < end) {
        // 谁小合并谁
        if (nums[l] < nums[r]) {
            temp[k++] = nums[l++];
        } else {
            temp[k++] = nums[r++];
        }
    }
    // 剩余部分合并
    while (l < mid) {
        temp[k++] = nums[l++];
    }
    while (r < end) {
        temp[k++] = nums[r++];
    }
    // 复制到原数组
    for (int i = 0; i < temp.length; i++) {
        nums[i + start] = temp[i];
    }
}
```

### 堆排序

用数组表示的完全二叉树 complete binary tree

> 完全二叉树 VS 其他二叉树

![image.png](https://img.fuiboom.com/img/tree_type.png)

[动画展示](https://www.bilibili.com/video/av18980178/)

![image.png](https://img.fuiboom.com/img/heap.png)

核心代码

```java
public void heapSort(int[] nums) {
    // 1、无序数组nums
	// 2、将无序数组nums构建为一个大根堆
    for (int i = nums.length / 2 - 1; i >= 0; i--) {
        sink(nums, i, nums.length);
    }
    // 3、交换nums[0]和nums[len(a)-1]
	// 4、然后把前面这段数组继续下沉保持堆结构，如此循环即可
    for (int i = nums.length - 1; i >= 0; i--) {
        // 从后往前填充值
        swap(nums, 0, i);
        // 前面的长度也减一
        sink(nums, 0, i);
    }
}

private void sink(int[] nums, int i, int length) {
    while (true) {
        // 左节点索引(从0开始，所以左节点为i*2+1)
        int l = i * 2 + 1;
        // 右节点索引
        int r = i * 2 + 2;
        // 保存根、左、右三者之间较大值的索引
        int index = i;
        // 存在左节点，左节点值较大，则取左节点
        if (l < length && nums[l] > nums[index]) {
            index = l;
        }
        // 存在右节点，且值较大，取右节点
        if (r < length && nums[r] > nums[index]) {
            index = r;
        }
        // 如果根节点较大，则不用下沉
        if (index == i) {
            break;
        }
        // 如果根节点较小，则交换值，并继续下沉
        swap(nums, i, index);
        i = index;
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

## 参考

[十大经典排序](https://www.cnblogs.com/onepixel/p/7674659.html)

[二叉堆](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/er-cha-dui-xiang-jie-shi-xian-you-xian-ji-dui-lie)