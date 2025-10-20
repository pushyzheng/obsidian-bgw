---
{"dg-publish":true,"dg-permalink":"DFS","permalink":"/DFS/"}
---


#算法

## 概念

DFS（*Depth first search*，即深度优先遍历），通常配合**栈或递归**来实现。

```java
boolean DFS(Node cur, Node target, Set<Node> visited) {  
    if (cur == target) {  
        return true;  
    }  
    for (Node next : each neighbor of cur){  
        if (!visited.contains(cur)) {  
            visted.add(next);  
            if (DFS(next, target, visited)) {  
                return true;  
            }  
        }  
    }  
    return false;  
}
```

网络遍历的模板：

```java
void dfs(int[][] grid, int r, int c) {  
    // 判断 base case, 如果坐标 (r, c) 超出了网格范围，直接返回  
    if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length) {  
        return;  
    }  
    // 访问上、下、左、右四个相邻结点  
    dfs(grid, r - 1, c);  
    dfs(grid, r + 1, c);  
    dfs(grid, r, c - 1);  
    dfs(grid, r, c + 1);  
}
```

## 例题

| File                               | Number | URL                                                                      |
| ---------------------------------- | ------ | ------------------------------------------------------------------------ |
| [[leetcode/子结构判断\|子结构判断]]       | \-     | https://leetcode-cn.com/submissions/detail/291811377/                    |
| [[leetcode/二叉树的中序遍历\|二叉树的中序遍历]] | 94     | https://leetcode.cn/problems/binary-tree-inorder-traversal/description/  |
| [[leetcode/路径总和\|路径总和]]         | 112    | https://leetcode-cn.com/problems/path-sum/                               |
| [[leetcode/单词拆分\|单词拆分]]         | 139    | https://leetcode.cn/problems/word-break/                                 |
| [[leetcode/二叉树的前序遍历\|二叉树的前序遍历]] | 144    | https://leetcode.cn/problems/binary-tree-preorder-traversal/description/ |
| [[leetcode/二叉树的后序遍历\|二叉树的后序遍历]] | 145    | https://leetcode.cn/problems/binary-tree-postorder-traversal/            |
| [[leetcode/岛屿数量\|岛屿数量]]         | 200    | https://leetcode.cn/problems/number-of-islands/                          |
| [[leetcode/二叉树的直径\|二叉树的直径]]     | 543    | https://leetcode.cn/problems/diameter-of-binary-tree/                    |

{ .block-language-dataview}

