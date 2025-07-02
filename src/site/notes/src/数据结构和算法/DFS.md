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

```dataview
TABLE
	number as "Number",
	url as "URL"
FROM "数据结构和算法/questions"
WHERE contains(tags, "DFS")
SORT number
```

