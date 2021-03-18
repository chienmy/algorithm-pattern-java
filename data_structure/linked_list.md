# 链表

## 提纲

链表相关的核心点

- null 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 基本操作

### 链表删除

#### 删除排序链表中的重复元素

> [83. 删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
> 
> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```java
public ListNode deleteDuplicates(ListNode head) {
    ListNode p = head;
    while (p != null) {
        // 全部删除完再移动到下一个元素
        while (p.next != null && p.val == p.next.val) {
            p.next = p.next.next;
        }
        p = p.next;
    }
    return head;
}
```

#### 删除排序链表中的重复元素 II

> [82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
> 
> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) {
        return null;
    }
    ListNode newHead = new ListNode(-1, head);
    ListNode p = newHead;
    int n = 0;
    while (p.next != null && p.next.next != null) {
        if (p.next.val == p.next.next.val) {
            // 记录已经删除的值，用于后续节点判断
            n = p.next.val;
            while (p.next != null && p.next.val == n) {
                p.next = p.next.next;
            }
        } else {
            p = p.next;
        }
    }
    return newHead.next;
}
```

注意点

- A->B->C 删除 B，A.next = C
- 删除用一个 Dummy Node 节点辅助（允许头节点可变）
- 访问 X.next 、X.value 一定要保证 X != nil

### 链表反转

#### 反转链表

> [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)
>
> 反转一个单链表。

思路：用一个 prev 节点保存向前指针，temp 保存向后的临时指针

```java
public ListNode reverseList(ListNode head) {
    ListNode pre = null, p = head;
    while (p != null) {
        // 保存当前head.Next节点，防止重新赋值后被覆盖
        // 一轮之后状态：nil<-1 2->3->4
        //               prev p
        ListNode temp = p.next;
        p.next = pre;
        // pre 移动
        pre = p;
        // p 移动
        p = temp;
    }
    return pre;
}
```

#### 反转链表 II

> [92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
> 
> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：先遍历到 m 处，翻转，再拼接后续，注意指针处理

```java
public ListNode reverseBetween(ListNode head, int m, int n) {
    // 思路：先遍历到m处，翻转，再拼接后续，注意指针处理
    // 输入: 1->2->3->4->5->null, m = 2, n = 4
    ListNode newHead = new ListNode(0, head);
    ListNode p = newHead;
    // 最开始：0(p)->1->2->3->4->5->null
    for (int i = 0; i < m-1; i++) {
        p = p.next;
    }
    // 遍历之后： 0->1(p)->2(cur)->3->4->5->null
    ListNode pre = null;
    ListNode cur = p.next;
    for (int i = m; i <= n; i++) {
        ListNode next = cur.next;
        cur.next = pre;
        pre = cur;
        cur = next;
    }
    // 循环结束：0->1(p)->2->null 5(cur)->null 4(pre)->3->2->null
    p.next.next = cur;
    p.next = pre;
    return newHead.next;
}
```

### 链表合并

#### 合并两个有序链表

> [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
>
> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：通过 dummy node 链表，连接各个元素

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode head = new ListNode(0);
    ListNode p = head;
    while (l1 != null && l2 != null) {
        if (l1.val < l2.val) {
            p.next = l1;
            l1 = l1.next;
        } else {
            p.next = l2;
            l2 = l2.next;
        }
        p = p.next;
    }
    // 连接未处理完节点
    p.next = l1 == null ? l2 : l1;
    return head.next;
}
```

####  合并K个升序链表

> [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)
>
> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。

思路：使用分治的方法两个两个地合并链表

```java
public ListNode mergeKLists(ListNode[] lists) {
    return merge(lists, 0, lists.length - 1);
}

public ListNode merge(ListNode[] lists, int begin, int end) {
    if (begin == end) return lists[begin];
    if (begin > end) return null;
    int mid = (begin + end) >> 1;
    return mergeTwoLists(merge(lists, begin, mid), merge(lists, mid + 1, end));
}

public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    // 同上
}
```

## 快慢指针

### 链表中点

使用两个指针变量，慢指针每次前进一步，快指针每次前进两步。这样当快指针到达链表末尾时，慢指针恰好在链表的中间位置。要注意链表长度为偶数的情况。

> [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)
> 
> 给定一个头结点为 head 的非空单链表，返回链表的中间结点。
> 
> 如果有两个中间结点，则返回第二个中间结点。

```java
public ListNode middleNode(ListNode head) {
    ListNode p = head;
    ListNode q = head;
    while (q != null && q.next != null) {
        p = p.next;
        q = q.next.next;
    }
    return p;
}
```

#### 重排链表

> [143. 重排链表](https://leetcode-cn.com/problems/reorder-list/)
> 给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
>
> 将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…
>
> 不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。 

```java
public void reorderList(ListNode head) {
    if (head == null || head.next == null) return;
    // 通过快慢指针找中点
    ListNode slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    ListNode p = head;
    // 反转链表
    ListNode q = reverseList(slow.next);
    slow.next = null;
    while (p != null && q != null) {
        ListNode qNext = q.next;
        q.next = p.next;
        p.next = q;
        p = q.next;
        q = qNext;
    }
}
```


#### 回文链表

> [234. 回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)
>
> 请判断一个链表是否为回文链表。

```java
public boolean isPalindrome(ListNode head) {
     // fast如果初始化为head.Next则中点在slow.Next
    // fast初始化为head,则中点在slow
    ListNode slow = head, fast = head, pre = null;
    // 这里顺便做了反转链表的操作
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        ListNode next = slow.next;
        slow.next = pre;
        pre = slow;
        slow = next;
    }
    if (fast != null){
        slow = slow.next;
    }
    // 与另一半链表依次比较
    while (slow != null) {
        if (slow.val != pre.val) return false;
        slow = slow.next;
        pre = pre.next;
    }
    return true;
}
```


### 结构判断

#### 环形链表

> [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)
> 
> 给定一个链表，判断链表中是否有环。
> 
> 如果链表中存在环，则返回 `true`。 否则，返回`false`。

思路：快慢指针，快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减 1
![fast_slow_linked_list](https://img.fuiboom.com/img/fast_slow_linked_list.png)

```java
public boolean hasCycle(ListNode head) {
    ListNode p = head, q = head;
    // 思路：快慢指针 快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减1
    while (p != null && q != null && q.next != null) {
        p = p.next;
        q = q.next.next;
        // 比较指针是否相等（不要使用val比较）
        if (p == q) {
            return true;
        }
    }
    return false;
}
```

#### 环形链表 II

> [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
>
> 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

思路：快慢指针，快慢相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点
![cycled_linked_list](https://img.fuiboom.com/img/cycled_linked_list.png)

```java
public ListNode detectCycle(ListNode head) {
    // 思路：快慢指针，快慢相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点
    ListNode p = head, q = head;
    while (p != null && q != null && q.next != null) {
        p = p.next;
        q = q.next.next;
        if (p == q) {
            // 指针重新从头开始移动
            ListNode m = head;
            // 比较指针对象（不要比对指针Val值）
            while (m != p) {
                m = m.next;
                p = p.next;
            }
            return p;
        }
    }
    return null;
}
```

## 其他

> [19. 删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)
>
> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。(尝试使用一趟扫描实现)

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode newHead = new ListNode(0, head);
    ListNode p1 = newHead;
    ListNode p2 = newHead;
    // 提前前进n个位置
    while (n >= 0) {
        p2 = p2.next;
        n--;
    }
    while (p2 != null) {
        p1 = p1.next;
        p2 = p2.next;
    }
    p1.next = p1.next.next;
    return newHead.next;
}
```

> [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
> 
> 给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
> 
> 要求返回这个链表的 深拷贝。

思路：1、hash 表存储指针，2、复制节点跟在原节点后面

```java
public Node copyRandomList(Node head) {
    if (head == null) {
        return null;
    }
	// 复制节点，紧挨到到后面
	// 1->2->3  ==>  1->1'->2->2'->3->3'
    Node cur = head;
    while (cur != null) {
        Node cloneNode = new Node(cur.val);
        cloneNode.next = cur.next;
        Node temp = cur.next;
        cur.next = cloneNode;
        cur = temp;
    }
    // 处理random指针
    cur = head;
    while (cur != null) {
        if (cur.random != null) {
            cur.next.random = cur.random.next;
        }
        cur = cur.next.next;
    }
    // 分离两个链表
    cur = head;
    Node cloneHead = cur.next;
    while (cur != null && cur.next != null) {
        Node temp = cur.next;
        cur.next = cur.next.next;
        cur = temp;
    }
	// 原始链表头：head 1->2->3
	// 克隆的链表头：cloneHead 1'->2'->3'
    return cloneHead;
}
```

## 总结

链表必须要掌握的一些点，通过下面练习题，基本大部分的链表类的题目都是手到擒来~

- null 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点
- 链表的末尾指针最后指向null
