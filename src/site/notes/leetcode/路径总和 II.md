---
{"dg-publish":true,"dg-permalink":"路径总和 II","permalink":"/路径总和 II/","tags":["二叉树","回溯","树"]}
---


#算法 

## 思路

题中所指路径为根结点 -> 叶结点，可以通过**深度优先遍历DFS （前序遍历）+ 回溯算法**来实现。具体操作为：

- 每遍历一个结点添加到路径列表 `path` 当中，当到达根结点时如果正好等于目标结点的值，即为合适的路径
- 若没到达根结点，则继续遍历左子树和右子树
- 另外，到达根节点后无论是否符合目标路径，都将回溯到父结点，继续寻找下一个路径

![](/img/user/attachments/images/Pasted image 20220320114122.png)

## 代码

需要特别注意是，将添加到答案的 res 时，需要将当前路径的数组**深拷贝**一份。

### Go

```go
func pathSum(root *TreeNode, targetSum int) [][]int {
	var res [][]int

	var backtrack func(root *TreeNode, path []int, target int)
	backtrack = func(root *TreeNode, path []int, target int) {
		if root == nil {
			return
		}
		path = append(path, root.Val)
		if root.Left == nil && root.Right == nil && root.Val == target {
			// 需要特别注意的是，这里需要将答案的 path 重新深拷贝一份
			// 否则后续的回溯操作会影响到，也可以使用 slices.Clone
			res = append(res, append([]int{}, path...))
		} else {
			backtrack(root.Left, path, target-root.Val)
			backtrack(root.Right, path, target-root.Val)
		}
		path = path[:len(path)-1]
	}

	backtrack(root, []int{}, targetSum)
	return res
}
```

### Java

```java
private List<List<Integer>> result;  
  
public List<List<Integer>> pathSum(TreeNode root, int targetSum) {  
    result = new ArrayList<>();  
    List<Integer> path = new ArrayList<>();  
    find(root, targetSum, path);  
    return result;  
}  
  
private void find(TreeNode root, int target, List<Integer> path) {  
    if (root == null) return;  
    path.add(root.val);  
    if (root.left == null && root.right == null && root.val == target) { 
		// 到达根结点，并且路径等于总和，复制新的 path 添加到 result 中
        result.add(new ArrayList<>(path));  
    }  
    if (root.left != null) {  
        find(root.left, target - root.val, path);  
    }  
    if (root.right != null) {  
        find(root.right, target - root.val, path);  
    }  
	// 回溯
    path.remove(path.size() - 1);  
}
```