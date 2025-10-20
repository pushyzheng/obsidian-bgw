---
{"dg-publish":true,"dg-permalink":"环形链表 II","permalink":"/环形链表 II/","title":"环形链表 II","tags":["链表","Floyd"]}
---


#算法

思路：通过   [[src/数据结构和算法/Floyd 判圈算法\|Floyd 判圈算法]]来解决，即找到环的入口：

1. 让快慢指针 *slow* 和 *fast* 相遇
2. 让 *fast* 指向**头结点**，再以**相同速度**向前移动，**再次相遇**即环的入口

代码：

```java
public ListNode detectCycle(ListNode head) {  
    var meet = findMeetNode(head);  
    if (meet == null) return null;  
  
    ListNode slow = head;  
    while (slow != meet) {  
        slow = slow.next;  
        meet = meet.next;  
    }  
    return slow;  
}  
  
ListNode findMeetNode(ListNode head) {  
    ListNode slow = head, fast = head;  
    while (fast != null && fast.next != null) {  
        slow = slow.next;  
        fast = fast.next.next;  
  
        if (slow == fast) {  
            return fast;  
        }
    }
    return null;  
}
```