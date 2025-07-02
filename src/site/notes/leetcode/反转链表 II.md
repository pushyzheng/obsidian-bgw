---
{"dg-publish":true,"dg-permalink":"反转链表 II","permalink":"/反转链表 II/","title":"反转链表 II","tags":["链表"]}
---


#算法

如图需要反转 2→5→4→3，先遍历 `left - 1` 次到前驱结点，再遍历 `right - left` 进行一个一个反转：
1. cur.next = next.next
2. next.next = pre.next
3. pre.next = next

![Pasted image 20220914111215.png](/img/user/attachments/images/Pasted%20image%2020220914111215.png)

参考代码：

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
	ListNode dummy = new ListNode(-1);
	dummy.next = head;
	ListNode pre = dummy;
	for(int i =0;i < left -1;i++) {
		pre=pre.next;
	}
	
	ListNode cur = pre.next;
	ListNode next;
	for (int i = 0; i < right - left; i++) {
		// 使用 pre 进行头插法
		next = cur.next;
		cur.next = next.next;
		next.next = pre.next;
		pre.next = next;
	}
	return dummy.next;
}
```