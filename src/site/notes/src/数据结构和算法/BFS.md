---
{"dg-publish":true,"dg-permalink":"BFS","permalink":"/BFS/"}
---


#算法

## 概念

BFS（*Breadth first search*）即广度优先遍历，通常配合**队列**来实现。

常用的应用：找出从**根结点**到**目标结点**的最短路径。

例如：找出根结点 A 和目标结点 G 之间的最短路径。

即：
- 与树的层序遍历类似，**越是接近根结点**的结点将**越早**地遍历。

队列的入队和出队时机和顺序：
1. 将**根结点**入队；
2. 将**邻居结点**逐个入队；
3. **新添加的节点不会立即遍历**，而是在下一轮中处理；

## 例题

| File                                               | Number | URL                                                                       |
| -------------------------------------------------- | ------ | ------------------------------------------------------------------------- |
| [[leetcode/填充每个节点的下一个右侧节点指针\|填充每个节点的下一个右侧节点指针]] | 116    | https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/ |
| [[leetcode/二叉树的右视图\|二叉树的右视图]]                   | 199    | https://leetcode.cn/problems/binary-tree-right-side-view/                 |
| [[leetcode/求二叉树的最大宽度\|求二叉树的最大宽度]]               | 662    | https://leetcode-cn.com/problems/maximum-width-of-binary-tree/            |

{ .block-language-dataview}

