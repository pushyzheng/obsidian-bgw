---
{"dg-publish":true,"dg-permalink":"数据结构 - 二叉搜索树 BST","permalink":"/数据结构 - 二叉搜索树 BST/"}
---


#数据结构 #算法 #树 

## 概念

[LeetBook - 二叉搜索树简介](https://leetcode-cn.com/leetbook/read/introduction-to-data-structure-binary-search-tree/xp8ny7/)

二叉搜索树（*Binary Search Tree*，BST）又称有序二叉树、排序二叉树。

定义，每个结点的值：
1. 都要**大于**左子树**所有**结点的值
2. 都要**小于**右子树所有结点的值

![Pasted image 20220326130539.png|300](/img/user/attachments/images/Pasted%20image%2020220326130539.png)

> [!INFO] 
> BST 的**中序遍历**比较有特点，是一个**升序递增的数组**，反之则是倒序的数组

如下图所示：

![](/img/user/attachments/images/WX20220327-104910.png)

```java
// 升序遍历
static void traverse(TreeNode root) {
	if (root == null) {
		return;
	}
	traverse(root.left);
	// operate root val
	traverse(root.right);
}

// 倒序遍历
static void traverseDesc(TreeNode root) {
	if (root == null) {
		return;
	}
	traverseDesc(root.right);
	// operate root val
	traverseDesc(root.left);
}
```

## 例题

| File                                           | Number | URL                                                                                |
| ---------------------------------------------- | ------ | ---------------------------------------------------------------------------------- |
| [[leetcode/二叉搜索树的操作\|二叉搜索树的操作]]             | \-     | https://leetcode-cn.com/problems/search-in-a-binary-search-tree/                   |
| [[leetcode/把二叉搜索树转换为累加树\|把二叉搜索树转换为累加树]]     | \-     | https://leetcode.cn/problems/convert-bst-to-greater-tree/                          |
| [[leetcode/验证二叉搜索树\|验证二叉搜索树]]               | \-     | https://leetcode-cn.com/problems/validate-binary-search-tree/                      |
| [[leetcode/不同的二叉搜索树\|不同的二叉搜索树]]             | 96     | https://leetcode.cn/problems/unique-binary-search-trees/                           |
| [[leetcode/二叉搜索树中第 K 小的元素\|二叉搜索树中第 K 小的元素]] | 228    | https://leetcode.cn/problems/kth-smallest-element-in-a-bst/                        |
| [[leetcode/前序遍历构造二叉搜索树\|前序遍历构造二叉搜索树]]       | 1008   | https://leetcode.cn/problems/construct-binary-search-tree-from-preorder-traversal/ |

{ .block-language-dataview}
