---
{"dg-publish":true,"dg-permalink":"链表中倒数第 K 个节点","permalink":"/链表中倒数第 K 个节点/","title":"链表中倒数第 K 个节点","tags":["链表"]}
---


#算法

根据[算法编程技巧-操作链表地 N 个节点](算法编程技巧.md#操作链表倒数第%20N%20个结点)的技巧，找到倒数地 K 个的前驱结点后，再执行删除的操作。

![Pasted image 20220321222453.png|400](/img/user/attachments/images/Pasted%20image%2020220321222453.png)

代码参考：

```java
public ListNode getKthFromEnd(ListNode head, int k) {
	if (head==null) {
		return null;
	}
	ListNode fast=head,slow=head;
	for(int i=0;i<k;i++){
		fast=fast.next;
	}
	if(fast==null){
		return head;
	}
	while(fast!=null){
		fast=fast.next;
		slow=slow.next;
	}
	return slow;
}
```