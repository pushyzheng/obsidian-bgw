---
{"dg-publish":true,"dg-permalink":"删除排序链表中的重复元素 II","permalink":"/删除排序链表中的重复元素 II/","title":"删除排序链表中的重复元素 II","tags":["链表","双指针"]}
---


## 思路

由于给定的链表是排序的，因此重复的元素在链表中出现的位置是连续的，所以只需要遍历一次，

步骤：
1. 由于链表的**头节点**可能会被删除，需要使用 dummy 结点指向链表的头部
2. 如果当前 `cur.next` 与 `cur.next.next` 对应的**元素相同**
	- 将 `cur.next` 以及所有后面拥有相同元素值的链表节点全部删除

## 代码

### go

```go
func deleteDuplicates(head *ListNode) *ListNode {
	dummy := &ListNode{Next: head}
	cur := dummy

	for cur != nil && cur.Next != nil && cur.Next.Next != nil {
		val := cur.Next.Val
		if val == cur.Next.Next.Val { // 后两个节点值相同
			// 开始删除所有的重复结点，直到下一个结点的值不与 x 相同
			for cur.Next != nil && cur.Next.Val == val {
				cur.Next = cur.Next.Next
			}
		} else {
			cur = cur.Next
		}
	}
	return dummy.Next
}
```

### java

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