# 二分搜索

## 简介

给一个**有序数组**和目标值，找第一次/最后一次/任何一次出现的索引，如果没有出现返回-1

模板四点要素

- 1、初始化：start=0、end=len-1
- 2、循环退出条件：start + 1 < end
- 3、比较中点和目标值：A[mid] ==、 <、> target
- 4、判断最后两个元素是否符合：A[start]、A[end] ? target

时间复杂度 O(logn)，使用场景一般是有序数组的查找

### 典型示例

> [704. 二分查找](https://leetcode-cn.com/problems/binary-search/)
>
> 给定一个  n  个元素有序的（升序）整型数组  nums 和一个目标值  target  ，写一个函数搜索  nums  中的 target，如果目标值存在返回下标，否则返回 -1。

```java
// 二分搜索最常用模板
public int search(int[] nums, int target) {
    // 1、初始化left、right
    int left = 0;
    int right = nums.length - 1;
    // 2、处理for循环
    while (right - left > 1) {
        int mid = left + (right - left) / 2;
        // 3、比较nums[mid]和target值
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }
    // 4、最后剩下两个元素，手动判断
    if (nums[left] == target) {
        return left;
    } else if (nums[right] == target) {
        return right;
    } else {
        return -1;
    }
}
```

### 模板

大部分二分查找类的题目都可以用这个模板，然后做一点特殊逻辑即可

另外二分查找还有一些其他模板如下图，大部分场景模板#3 都能解决问题，而且还能找第一次/最后一次出现的位置，应用更加广泛

![binary_search_template](https://img.fuiboom.com/img/binary_search_template.png)

所以用模板#3 就对了，详细的对比可以这边文章介绍：[二分搜索模板](https://leetcode-cn.com/explore/learn/card/binary-search/212/template-analysis/847/)

如果是最简单的二分搜索，不需要找第一个、最后一个位置、或者是没有重复元素，可以使用模板#1，代码更简洁常见题目

## 常见题目

### 搜索插入位置

> [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)
>
> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

```java
public int searchInsert(int[] nums, int target) {
    // 思路：找到第一个 >= target 的元素位置
    int left = 0;
    int right = nums.length - 1;
    while (right - left > 1) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }
    if (nums[left] >= target) {
        return left;
    } else if (nums[left] < target && target <= nums[right]) {
        return left + 1;
    } else {
        // 目标值比所有值都大
        return right + 1;
    }
}
```

### 搜索二维矩阵

> [74. 搜索二维矩阵](https://leetcode-cn.com/problems/search-a-2d-matrix/)
>
> 编写一个高效的算法来判断  m x n  矩阵中，是否存在一个目标值。该矩阵具有如下特性：
>
> - 每行中的整数从左到右按升序排列。
> - 每行的第一个整数大于前一行的最后一个整数。

```java
public boolean searchMatrix(int[][] matrix, int target) {
    // 思路：将2维数组转为1维数组 进行二分搜索
    if (matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    int row = matrix.length;
    int col = matrix[0].length;
    int left = 0;
    int right = row * col - 1;
    while (right - left > 1) {
        int mid = left + (right - left) / 2;
         // 获取2维数组对应值
        int val = matrix[mid/col][mid%col];
        if (val < target) {
            left = mid;
        } else if (val > target) {
            right = mid;
        } else {
            return true;
        }
    }
    if (matrix[left/col][left%col] == target || matrix[right/col][right%col] == target) {
        return true;
    }
    return false;
}
```

### 寻找旋转排序数组中的最小值

> [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)
>
> 假设按照升序排序的数组在预先未知的某个点上进行了旋转( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。

```java
public int findMin(int[] nums) {
    // 思路：最后一个值作为target，然后往左移动，最后比较start、end的值
    if (nums.length == 0) {
        return -1;
    }
    int left = 0;
    int right = nums.length - 1;
    while (right - left > 1) {
        int mid = left + (right - left) / 2;
        // 最后一个元素值为target
        if (nums[mid] > nums[right]) {
            left = mid;
        } else {
            right = mid;
        }
    }
    return Math.min(nums[left], nums[right]);
}
```

### 寻找旋转排序数组中的最小值 II

> [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
>
> 假设按照升序排序的数组在预先未知的某个点上进行了旋转
> ( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。(包含重复元素)

```java
public int findMin(int[] nums) {
    // 思路：跳过重复元素，mid值和end值比较，分为两种情况进行处理
    if (nums.length == 0) {
        return -1;
    }
    int left = 0;
    int right = nums.length - 1;
    while (right - left > 1) {
        // 去除重复元素
        while (left < right && nums[right] == nums[right - 1]) {
            right--;
        }
        while (left < right && nums[left] == nums[left + 1]) {
            left++;
        }
        int mid = left + (right - left) / 2;
        // 中间元素和最后一个元素比较（判断中间点落在左边上升区，还是右边上升区）
        if (nums[mid] > nums[right]) {
            left = mid;
        } else {
            right = mid;
        }
    }
    return Math.min(nums[left], nums[right]);
}
```

### 搜索旋转排序数组

> [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
>
> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,1,2,4,5,6,7]  可能变为  [4,5,6,7,0,1,2] )。
> 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回  -1 。
> 你可以假设数组中不存在重复的元素。

```java
public int search(int[] nums, int target) {
     // 思路：四种情况判断
    if (nums.length == 0) {
        return -1;
    }
    int left = 0;
    int right = nums.length - 1;
    while (right - left > 1) {
        int mid = left + (right - left) / 2;
        // 相等直接返回
        if (nums[mid] == target) {
            return mid;
        }
        // 判断在哪个区间，可能分为四种情况
        if (nums[left] < nums[mid]) {
            if (nums[left] <= target && target <= nums[mid]) {
                right = mid;
            } else {
                left = mid;
            }
        } else if (nums[right] > nums[mid]) {
            if (nums[right] >= target && target >= nums[mid]) {
                left = mid;
            } else {
                right = mid;
            }
        }
    }
    if (nums[left] == target) {
        return left;
    } else if (nums[right] == target) {
        return right;
    }
    return -1;
}
```

注意点

> 面试时，可以直接画图进行辅助说明，空讲很容易让大家都比较蒙圈

### 搜索旋转排序数组 II

> [81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
>
> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,0,1,2,2,5,6]  可能变为  [2,5,6,0,0,1,2] )。
> 编写一个函数来判断给定的目标值是否存在于数组中。若存在返回  true，否则返回  false。(包含重复元素)

```java
public boolean search(int[] nums, int target) {
     // 思路：四种情况判断
    if (nums.length == 0) {
        return false;
    }
    int left = 0;
    int right = nums.length - 1;
    while (right - left > 1) {
        // 去除重复元素
        while (left < right && nums[right] == nums[right - 1]) {
            right--;
        }
        while (left < right && nums[left] == nums[left + 1]) {
            left++;
        }
        int mid = left + (right - left) / 2;
        // 相等直接返回
        if (nums[mid] == target) {
            return true;
        }
        // 判断在哪个区间，可能分为四种情况
        if (nums[left] < nums[mid]) {
            if (nums[left] <= target && target <= nums[mid]) {
                right = mid;
            } else {
                left = mid;
            }
        } else if (nums[right] > nums[mid]) {
            if (nums[right] >= target && target >= nums[mid]) {
                left = mid;
            } else {
                right = mid;
            }
        }
    }
    return (nums[left] == target || nums[right] == target);
}
```

## 总结

二分搜索核心四点要素（必背&理解）

- 1、初始化：start=0、end=len-1
- 2、循环退出条件：start + 1 < end
- 3、比较中点和目标值：A[mid] ==、 <、> target
- 4、判断最后两个元素是否符合：A[start]、A[end] ? target
