 # 快速选择

## 使用场景

- 最大/最小的K个数
- 最大/最小的第K个数

## 算法思路

和快速排序的思路类似，但是不需要得出精确的比较顺序。首先从数组中随机或指定挑选一个元素作为标志，然后将所有比这个标志小的元素放在左边，比标志大的放在右边，最后会有以下三种可能的情况：

1. 这个位置就是K，直接返回即可
2. 这个位置小于K，说明K位置的数在右边
3. 这个位置大于K，说明K位置的数在左边

## 算法实现

> [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)
>
>在未排序的数组中找到第 **k** 个最大的元素。请注意，需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

```java
public int findKthLargest(int[] nums, int k) {
    int l = 0;
    int r = nums.length - 1;
    while (l < r) {
        // 对标志两边分别进行处理
        int pivot = partition(nums, l, r);
        // 标志就是k
        if (pivot == nums.length - k) {
            break;
        }
        // k位置在标志的右侧
        else if (pivot < nums.length - k) {
            l = pivot + 1;
        }
        // k位置在标志的左侧
        else {
            r = pivot - 1;
        }
    }
    return nums[nums.length - k];
}

private int partition(int[] nums, int start, int end) {
    int l = start;
    int r = end + 1;
    // 选择首元素作为比较标志
    // 比标志小的放在左边，比标志大的放在右边
    while (true) {
        while (nums[++l] < nums[start] && l < end);
        while (nums[--r] > nums[start] && r > start);
        if (l >= r) {
            break;
        }
        swap(nums, l, r);
    }
    // 将标志交换到中间
    swap(nums, start, r);
    return r;
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```



