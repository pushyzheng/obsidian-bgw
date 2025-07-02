---
{"dg-publish":true,"dg-permalink":"删除排序链表中的重复元素 II","permalink":"/删除排序链表中的重复元素 II/","title":"删除排序链表中的重复元素 II","tags":["链表"]}
---


代码：

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) {
        return head;
    }
    ListNode dummy= new ListNode(-1);
    dummy.next = head;
    ListNode cur = dummy;
    while(cur != null && cur.next != null && cur.next.next != null) {
        if (cur.next.val == cur.next.next.val) {
            int x = cur.next.val;
            while(cur.next != null && cur.next.val == x) {
                cur.next = cur.next.next; // remove cur.next
            }
        } else {
            cur = cur.next;
        }
    }
    return dummy.next;
}
```