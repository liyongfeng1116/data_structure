# 链表题
1. 合并两个有序链表（[leetcode21](https://leetcode-cn.com/problems/merge-two-sorted-lists/)）
    
* 双指针用来解决单链表问题
* 注意哑节点的使用
```
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode dump = new ListNode(-1);   // 默认所有节点的数值为正
        ListNode p = dump;
        ListNode p1 = list1, p2 = list2;
        // 比较两个列表的值，将小的append到后面
        while (p1 != null && p2 != null) {
            if (p1.val < p2.val) {
                p.next = p1;
                p1 = p1.next;
            } else {
                p.next = p2;
                p2 = p2.next;
            }
            p = p.next;  // 指针向前移动
        }
        // 循环结束后必有一个为空
        if (p1 != null) {
            p.next = p1;
        }
        if (p2 != null) {
            p.next = p2;
        }
        return dump.next;
    }
}
```
2. 合并K个有序链表（[leetcode23](https://leetcode-cn.com/problems/merge-k-sorted-lists/)）

- 需要学二叉堆

3. 删除单链表倒数第K个节点（[leetcode19](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)) 类似的题：[面试题02.02](https://leetcode-cn.com/problems/kth-node-from-end-of-list-lcci/)   
- 如果遍历第K个则直接for循环，要想遍历倒数第K个，如果知道链表长度N，则N-k即为该点，为此，需要先遍历一遍知道链表的长度。
- 双指针只需要遍历一遍即可。**先将一个指针p1放在head，然后先走K步。再将P2放在head，p1和p2一起走，当p1走到最后N，p2刚好到N-k。因为两个指针始终相差k。**
- 删除链表中的节点，将该节点的上一个next指向本节点的next。**x.next = x.next.next**
- 哑节点可以防止溢出，要删除倒数第N个，需要找到N+1的值，该值用dump表示。
```
class Solution {
     private ListNode findFromEnd(ListNode head, int n) {
        ListNode p1 = head;
        // p1先走K步
        for (int i = 0; i < n; i++) {
            p1 = p1.next;
        }
        // p1和p2一起走N-k步，p2刚好从head走到N-k，即倒数第k
        ListNode p2 = head;
        while (p1 != null) {
            p1 = p1.next;
            p2 = p2.next;
        }
        return p2;
    }
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // dump防止溢出，因为要找到倒数第K个的前一个值。
        // 如果一共10个值，找到倒数第10个删除，也即第一个，则要找倒数第11个数进行next操作
        ListNode dump = new ListNode(-1);
        dump.next = head;
        ListNode x = findFromEnd(dump, n + 1);
        x.next = x.next.next;    
        return dump.next;
    }
}
```    
4. 单链表的中点 （[leetcode876](https://leetcode-cn.com/problems/middle-of-the-linked-list/)）   
- 遍历找某个值
- 找到单链表长度n，取出n/2
- **快慢指针**，快指针每次步长为2，慢指针步长为1，快指针到最后，慢指针指向中
```
class Solution {
    //遍历
    public ListNode middleNode(ListNode head) {
        int n = 0;
        ListNode p = head;
        while (p.next != null) {
            n += 1;
            p = p.next;
        }
        ListNode q = head;
        for (int i = 0; i < (n/2); i++) {
            q = q.next;
        }
        return q;
    }
    //快慢指针
    public ListNode middleNode(ListNode head) {
        ListNode q = head;
        ListNode s = head;
        while (q.next != null && q.next.next != null) {
            q = q.next.next;
            s = s.next;
        }
        return s;
    }
}
```
5. 判断链表是否包含环 （[leetcode141](https://leetcode-cn.com/problems/linked-list-cycle/)）
- **环形链表快指针一定会追上慢指针**
- 边界条件：（1）快指针为空 （2）快指针等于慢指针
```
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode q = head;
        ListNode m = head;
        while (q.next != null && q.next.next != null) {
            q = q.next.next;
            m = m.next;
            if (q == m) {
                return true;
            }
        }
        return false;
    }
}
```
6. 求出环形链表中环的起点（[leetcode142](https://leetcode-cn.com/problems/linked-list-cycle-ii/)）
- 快慢指针必定相遇，相遇时快指针（2k）走的距离是慢指针（k）的2倍
- 快指针比慢指针多走k，k为环长度的整数倍（快指针在环里转圈）
- 环的入口到相遇点的距离为m。则链表头到环起点的距离为k-m，相遇点到环起点的距离也为k-m
- **快慢指针中的任意一个指向head，两个指针同速前进，k-m后必定相遇，相遇点为环的起点**
![环的表示](https://labuladong.gitee.io/algo/images/%E5%8F%8C%E6%8C%87%E9%92%88/2.jpeg "环")      
```
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode q, m;
        q = m = head;
        // 先找相遇点,跳出while的边界是快慢指针任一为空，或者找到相遇点
        while (q.next != null && q.next.next != null) {
            q = q.next.next;
            m = m.next;
            if (q == m) {
                break;
            }
        }
        // 没有环的情况
        if (q.next == null || q.next.next == null) {
            return null;
        }
        // 将快慢指针任一放到起点，以同一速度前进，下一次相遇点就是环的起点
        m = head;
        while (m != q) {
            m = m.next;
            q = q.next;
        }
        return m;
    }
}
```
7. 两个链表相交（[leetcode160](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)）
- 两个链表长度不同，但长度和是相同的。
- 为了同时遍历到相交点，遍历完一个链表让其进入第二个，则同时到达交点
- **反向遍历，直到最后一个不一样的值出现**
![链表相交](https://labuladong.gitee.io/algo/images/%e9%93%be%e8%a1%a8%e6%8a%80%e5%b7%a7/6.jpeg "链表相交")    
```
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        ListNode p1 = headA;
        ListNode p2 = headB;
        // 两个节点相等则退出
        while (p1 != p2) {
            // 如果一个节点为空，则遍历另一个，不为空，则还在该节点遍历
            if (p1 == null) {
                p1 = headB;
            } else {
                p1 = p1.next;
            }
            
            if (p2 == null) {
                p2 = headA;
            } else {
                p2 = p2.next;
            }
        }
        return p1;
    }
}
```
8. 反转链表 （[leetcode206](https://leetcode-cn.com/problems/reverse-linked-list/)） 相似题目 [leetcode92](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- 遍历将每个节点指向前一个节点
- 第一个节点指向null
- 注意**缓存当前节点的下一个**，因为需要将当前节点的下一个指向前前一个节点  
**（1）迭代法**
```
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode curr = head;
        ListNode pre = null;
        while (curr != null) { //直到最后一个为空
            ListNode next = curr.next; // 先把下一个节点保存
            curr.next = pre; // curr.next已经改变，指向前一个
            pre = curr;
            curr = next;
        }
        return pre;  // 当最后一个curr == null,之后pre刚好在最后一个结点
    }
}
```
**（2）递归法（没理解）**
```
// 递归法 1 -> 2 -> 3 -> 4 -> 5 -> null
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) {//终止条件
        return head;
    }
    ListNode node = reverseList(head.next);  // 到5终止，5 = 4.next node = 5
    head.next.next = head; // 4.next.next = 4
    head.next = null; // 4.next本来是5，改成null head = 4
    return node; // 返回5
}
```
**（3）栈**
```
// 用栈反转链表
public ListNode reverseList(ListNode head) {
    Stack<ListNode> stack = new Stack<ListNode>();
    ListNode start = new ListNode(0, new ListNode(0));
    ListNode temp = start;
    // 先全部压栈
    ListNode curr = head;
    while (curr != null) {
        stack.push(curr);
        curr = curr.next;
    }
    // 全部出栈
    while (!stack.isEmpty()) {
        temp.next = stack.pop();
        temp = temp.next;
    }
    temp.next = null;
    return start.next;
}
```
*反转区间的链表*
```
(1)迭代法
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        // 1.切断要反转区间的链表
        // 2.反转链表
        // 3.连接链表
        ListNode dump = new ListNode(0,head);
        ListNode pre = dump;
        // 找到左边的点
        for (int i = 0; i < left - 1; i++) {
            pre = pre.next;
        }
        // 找到右边的点
        ListNode rightNode = pre;
        for (int i = 0; i < right - left + 1; i++) {
            rightNode = rightNode.next;
        }

        ListNode leftNode = pre.next;
        ListNode curr = rightNode.next;
        // 断开反转的链
        pre.next = null;
        rightNode.next = null;
        // 反转单链
        reverse(leftNode);
        // 拼接
        pre.next = rightNode;
        leftNode.next = curr;
        return dump.next;
    }

    public void reverse(ListNode head) {
        ListNode curr = head;
        ListNode pre = null;
        while(curr != null) {
            ListNode next = curr.next;
            curr.next = pre;
            pre = curr;
            curr = next;
        }
    }
}
```
穿针引线法
- curr：指向待反转区域的第一个节点 left；
- next：永远指向 curr 的下一个节点，循环过程中，curr 变化以后 next 会变化；
- pre：永远指向待反转区域的第一个节点 left 的前一个节点，在循环过程中不变。
![穿针引线法](https://pic.leetcode-cn.com/1615105242-ZHlvOn-image.png "穿针引线")
![穿针引线法2](https://pic.leetcode-cn.com/1615105296-bmiPxl-image.png "穿针引线法2")
```
(2)穿针引线法
// 三指针穿针引线法
public ListNode reverseBetween(ListNode head, int left, int right) {
    ListNode dump = new ListNode(-1);
    dump.next = head;
    ListNode pre = dump;
    for (int i = 0; i < left - 1; i++) {
        pre = pre.next;
    }
    ListNode curr = pre.next;
    ListNode next;
    for (int i = 0; i < right - left; i++) { //每次交换两个值，所以不需要+1
        next = curr.next;
        curr.next = next.next;
        next.next = pre.next;
        pre.next = next;
    }
    return dump.next;
}
```

```
（3）头插法
public ListNode reverseBetween(ListNode head, int left, int right) {
    ListNode dump = new ListNode(-1);
    dump.next = head;
    ListNode g = dump;
    ListNode p = dump.next;
    for (int i = 0; i < left - 1; i ++) {
        g = g.next;
        p = p.next;
    }
    
    for (int i = 0; i < right - left; i ++) {
        ListNode removed = p.next;
        p.next = p.next.next;
        removed.next = g.next;
        g.next = removed;
    }
    return dump.next;
}
```
9. 删除有序链表中的重复元素 （[leetcode83](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)） 相似的题目（[leetcode82 中等难度](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)）
- 有序链表很重要
- 确定不能为空
```
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode curr = head;
        while (curr != null && curr.next != null) { //用谁谁不能为空
            if (curr.val == curr.next.val) {
                curr.next = curr.next.next;
            } else {
                curr = curr.next;
            }
         }
         return head; // curr是改变的，相当于遍历中的i
    }
}
```     
删除所有重复的节点
```
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return head;
        }
        ListNode dump = new ListNode(0,head);
        ListNode curr = dump;
        while (curr.next != null && curr.next.next != null) {
            // 如果当前的下一个和当前下一个的下一个相等
            // 说明发生了重复，记录重复的值
            if (curr.next.val == curr.next.next.val) {
                Integer x = curr.next.val;
                // 当前的下一个指向不相等的
                while (curr.next != null && curr.next.val == x) {
                    curr.next = curr.next.next;
                }
            } else {
                curr = curr.next;
            }   
        }
        return dump.next;
    }
}
```
10. 对链表进行插入排序（[leetcode147](https://leetcode-cn.com/problems/insertion-sort-list/)） 相似题目（[leetcode148](https://leetcode-cn.com/problems/sort-list/)）
- 插入排序时间复杂度为n^2
- 插入排序的思想是每次将一个值插到已排序的数组中。
- 需要遍历两遍（最差），找每个数组扫描一遍，给每个数组找位置再扫描一遍。
- 在链表插入排序时，维护三个指针，一个curr指向我们要遍历的点；一个lastSorted指向已排序的最后一个点（因为需要把curr连接到前面，从而将lastSorted和新的连接）；一个pre指向插入位置的前一个元素（所以需要dump哑节点）

```
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode dump = new ListNode(-1);
        dump.next = head;
        ListNode lastSorted = head;
        ListNode curr = head.next;
        while (curr != null) {
            if (lastSorted.val <= curr.val) {
                lastSorted = lastSorted.next;
            } else {
                ListNode pre = dump;
                while (pre.next.val <= curr.val) {
                    pre = pre.next;
                }
                lastSorted.next = curr.next;  // 把curr插入到pre之后，lastSorted连接下一个curr
                curr.next = pre.next; 
                pre.next = curr;
            }
            curr = lastSorted.next; // curr 往后移一位
        }
        return dump.next;
    }
}
```


