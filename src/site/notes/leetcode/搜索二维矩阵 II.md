---
{"dg-publish":true,"dg-permalink":"搜索二维矩阵 II","permalink":"/搜索二维矩阵 II/","tags":["矩阵","树"]}
---


## 暴力解法

直接遍历矩阵**所有的元素**，判断元素是否与 target 相等。

[代码参考](https://leetcode-cn.com/submissions/detail/286391227/)

## 抽象为树

由于矩阵中每列和每行都是顺序排序，那么将二维数组逆时针旋转 45° ，将其看成一棵二叉树搜索树，即：
- **左**结点 < 当前结点
- **右**结点 > 当前结点

![Pasted image 20220320164349.png|500](/img/user/attachments/images/Pasted%20image%2020220320164349.png)

并且
- 访问左结点：即**列数**在减小，值也减少
- 访问右结点：即**行数**在增大，值也增大

时间复杂度为 O(logN)，代码如下：

### Go



### Java

```java
public boolean findNumberIn2DArray(int[][] matrix, int target) {  
    if (matrix == null || matrix.length == 0
			|| matrix[0].length == 0) {
        return false;
    }
    int n = matrix.length, m = matrix[0].length;
    int row = 0, col = m - 1;
    
	while (row < n && col >= 0) {
        int num = matrix[row][col];
        if (num == target) {
            return true;
        } else if (num > target) {
			// 列减小，即访问左子结点
            col--;
        } else {
			// 行增加，即访问右子结点
            row++;
        }
    }
    return false;
}
```