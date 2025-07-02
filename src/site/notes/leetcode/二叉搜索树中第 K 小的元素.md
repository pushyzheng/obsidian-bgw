---
{"dg-publish":true,"dg-permalink":"二叉搜索树中第 K 小的元素","permalink":"/二叉搜索树中第 K 小的元素/","title":"二叉搜索树中第 K 小的元素","tags":["树","二叉树","二叉搜索树"]}
---


#算法 

根据 BST 中序遍历的规律（升序数组），对根结点进行中序遍历，并通过 rank 成员变量来记录当前遍历到的序号。

当序号与 K 相等时，即找到第 K 个小的元素。

```java
int rank = 0;
int res = 0;

public int kthSmallest(TreeNode root, int k) {
    traverse(root, k);
    return res;
}

void traverse(TreeNode root, int k) {
    if (root == null) return;
    traverse(root.left, k);
    rank++;
    if (rank == k) {
        res = root.val;
    }
    traverse(root.right, k);
}
```