 # 三向切分快速排序

## 使用场景

待排序数组中含有类别较少但大量重复的元素

## 算法思路

除前后两个指针外再使用两个额外的指针指向与待选元素相同的元素，遍历后将与待选元素相同的元素置换到数组中间，再对两侧分别进行排序。通过这一方法可以节省元素交换次数。

## 算法实现

```java
public void sort(int[] nums) {
    sort(nums, 0, nums.length - 1);
}

private void sort(int[] nums, int left, int right) {
    if (left < right) {
        // 选择首元素作为比较标志
        int temp = nums[left];
        // 设置两个指针指向与temp相同的元素范围
        int left_p = left;
        int right_p = right;
        for (int i = left + 1; i <= right_p; ) {
            // 根据比较结果进行元素交换
            if (nums[i] < temp) {
                swap(nums, left_p, i);
                left_p++;
                i++;
            } else if (nums[i] > temp) {
                swap(nums, i, right_p);
                right_p--;
            } else {
                i++;
            }
        }
        // 对两侧元素分别进行排序
        sort(nums, left, left_p - 1);
        sort(nums, right_p + 1, right);
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

## 经典例题

> [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)
>
> 给定一个包含红色、白色和蓝色，一共 `n` 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 此题中，我们使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。

由于只有三种颜色，所以不需要再对比较标志两侧元素分别排序，对实现代码稍作修改即可。

```java
public void sortColors(int[] nums) {
    int left = 0;
    int mid = 0;
    int right = nums.length - 1;
    while (mid <= right) {
        if (nums[mid] == 0) {
            swap(nums, left, mid);
            mid++;
            left++;
        } else if (nums[mid] == 2) {
            swap(nums, right, mid);
            right--;
        } else {
            mid++;
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

