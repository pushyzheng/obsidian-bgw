---
{"dg-publish":true,"dg-permalink":"合并 K 个升序链表","permalink":"/合并 K 个升序链表/","title":"合并 K 个升序链表","tags":["链表"]}
---


#算法

借助[[leetcode/合并两个有序链表\|合并两个有序链表]]，我们可以依次对 K 个链表进行合并的操作。

```java
public ListNode mergeKLists(ListNode[] lists) {
	ListNode res = null;
	for (ListNode node : lists) {
		res = mergeTwoLists(res, node);
	}
	return res;
}

public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
	...
}
```