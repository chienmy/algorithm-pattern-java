# 二进制

## 常见二进制操作

### 基本操作

a=0^a=a^0

0=a^a

由上面两个推导出：a=a^b^b

### 交换两个数

a=a^b

b=a^b

a=a^b

### 移除最后一个 1

a=n&(n-1)

### 获取最后一个 1

diff=(n&(n-1))^n

## 常见例题

### 只出现一次的数字

> [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)
>
> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

```java
public int singleNumber(int[] nums) {
    // 10 ^ 10 == 00
    // 两个相同的数异或变成0
    int result = 0;
    for (int n : nums) {
        result = result ^ n;
    }
    return result;
}
```

### 只出现一次的数字 II

> [137. 只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)
>
> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

```java
public int singleNumber(int[] nums) {
    int result = 0;
    // 统计每位1的个数
    for (int i = 0; i < 64; i++) {
        int sum = 0;
        for (int n : nums) {
			// 统计1的个数
            sum += ((n >> i) & 1);
        }
        // 还原
        result |= ((sum % 3) << i);
    }
    return result;
}
```

### 只出现一次的数字 III

> [260. 只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/)
>
> 给定一个整数数组  `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。

```java
public int[] singleNumber(int[] nums) {
    // 关键点怎么把a^b分成两部分,方案：可以通过diff最后一个1区分
    int diff = 0;
    for (int n : nums) {
        diff ^= n;
    }
    int[] result = new int[]{diff, diff};
    // 去掉末尾的1后异或diff就得到最后一个1的位置
    diff = (diff & (diff - 1)) ^ diff;
    for (int n : nums) {
        if ((diff & n) == 0) {
            result[0] ^= n;
        } else {
            result[1] ^= n;
        }
    }
    return result;
}
```

### 位1的个数

> [191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)
>
> 编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’  的个数（也被称为[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)）。

```java
public int hammingWeight(int n) {
    int result = 0;
    while (n != 0) {
        if ((n & 1) == 1) result++;
        n = n >>> 1;
    }
    return result;
}
```

### 颠倒二进制位

> [190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)
>
> 颠倒给定的 32 位无符号整数的二进制位。

思路：依次颠倒即可

```java
public int reverseBits(int n) {
    int result = 0;
    int p = 31;
    while (n != 0) {
        result += ((n & 1) << p);
        n >>>= 1;
        p--;
    }
    return result;
}
```

### 数字范围按位与

> [201. 数字范围按位与](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)
>
> 给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。

问题转化为求两个给定数字二进制状态下的最长公共前缀，可以用移位判断的思想来做，这里使用另一种`Brian Kernighan`算法：`number`和 `number−1`之间进行按位与运算后，`number`中最右边的1会被抹去变成0。

```java
public int rangeBitwiseAnd(int m, int n) {
    while (m < n) {
        // 抹去最右边的 1
        n = n & (n - 1);
    }
    return n;
}
```

## 注意点

- Java中区分右移运算和无符号右移运算
- 注意运算顺序，不确定时尽量加上括号