---
{"dg-publish":true,"dg-permalink":"k 个一组翻转链表","permalink":"/k 个一组翻转链表/","title":"k 个一组翻转链表","tags":["链表"]}
---


#算法 

思路：
1. 链表分区为：已翻转部分 + 待翻转部分 + 未翻转部分
2. 每次翻转前，确定翻转链表的范围（且记录翻转链表前驱和后继）
3. 执行翻转链表，然后将三部分链表连接起来

代码参考：

```java
public ListNode reverseKGroup(ListNode head, int k) {
	ListNode dummy = new ListNode(-1);
	dummy.next = head;

	ListNode pre = dummy, end = dummy;
	while (end.next != null) {
		for (int i = 0; i < k && end != null; i++) 
			end = end.next;
		if (end == null) break;
		// 待翻转链表的前驱和后继
		ListNode start = pre.next;
		ListNode next = end.next;

		end.next = null;           // 断开后半部分
		pre.next = reverse(start); // 翻转中间部分，并拼接前半部分

		start.next = next;         // 拼接后半部分
		end = pre = start;         // 重新将 pre/end 归位
	}
	return dummy.next;
}

private ListNode reverse(ListNode head) {
	ListNode pre = null;
	ListNode curr = head;
	while (curr != null) {
		ListNode next = curr.next;
		curr.next = pre;
		pre = curr;
		curr = next;
	}
	return pre;
}
```