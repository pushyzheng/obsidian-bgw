---
{"dg-publish":true,"dg-permalink":"最近公共祖先 LCA","permalink":"/最近公共祖先 LCA/","title":"最近公共祖先 LCA","tags":["树","二叉树"]}
---


#算法 

> [!QUOTE] 最近公共祖先的定义
> 对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。

套用 [在二叉树中寻找两个目标值结点模板](https://jihulab.com/learning/interview/-/blob/main/src/main/java/org/example/interview/algorithm/templates/FindTwoNode.java)，比较特殊的情况是正好 q 是 p 的祖先，即 q 是 LCA，如下图所示：

![Pasted image 20220902170015.png|400](/img/user/attachments/images/Pasted%20image%2020220902170015.png)

针对这种情况，可以在前序时判断 `root.val == v1 || root.val == v2`，则能断定 **p 一定在 q 的子树中**，则 q 为 LCA。

否则，在左右子树中查找：
- 如果在左右子树中，如果同时找到，则**当前结点为 LCA**（遍历到结点 5 的情况）
- 否则为左子树或右子树（遍历到根结点 3 的情况）

即：

![Pasted image 20220902170305.png|400](/img/user/attachments/images/Pasted%20image%2020220902170305.png)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    return find(root, p.val, q.val);
}

TreeNode find(TreeNode root, int v1, int v2) {
    if (root == null) return null;
    if (root.val == v1 || root.val == v2) {
        return root;
    }
    TreeNode left = find(root.left, v1, v2);
    TreeNode right = find(root.right, v1, v2);
    if (left != null && right != null) {
        return root;
    }
    return left != null ? left : right;
}
```

另一种思路：[计算 p、q 两个路径后再比较](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/solution/by-pushy-hzvb/)