---
{"dg-publish":true,"dg-permalink":"背包 DP","permalink":"/背包 DP/"}
---


#算法 #动态规划

## 0-1 背包

> 题意概要：有  n 个物品和一个容量为 W 的背包，每个物品有重量 W(i) 和价值 V(i) 两种属性。
> 
> 要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量。

由于每个物理只有两种状态，取或者不取（对应 0-1），所以被称为 0-1 背包问题（*Knapsack problem*）。

### 分析

1. 概念
	- 状态：
		- 背包的剩余容量
		- 可选择的物品
	- 选择（只有选和不选，所以称之为 0/1）：
		- 选当前的物品
		- 不选当前的物品
	- base case（都为 0）
		- 当剩余容量为 0 时
		- 当可选择的物品为 0 时
2. 定义二维 dp 数组：对于前 i 个物品，当背包容量为 w 时，可以装的最大价值为 `dp[i][w]`
3. 状态转移方程：
	- 不选：`dp[i][w] = dp[i-1][w]`
	- 选：`dp[i][w] = vals[i - 1] + dp[i-1][w - wt[i - 1]]`

### 代码

- `vals[i - 1]` 和 `wt[i - 1]` 分别代表当前物品的价值和重量（需要下标偏移 1）
- 需要处理 `wt[i - 1] > w` 的边界情况即：当前的物品的重量要 > 背包剩余容量

```java
public int solve(int W, int N, int[] wt, int[] vals) {
	int[][] dp = new int[N + 1][W + 1];

	for (int i = 1; i <= N; i++) {
		for (int w = 1; w <= W; w++) {
			int max;
			if (wt[i - 1] > w) {
				// 可选的最大价值大于当前的物品, 无法选择
				max = dp[i - 1][w];
			} else {
				// 不选第 i 个物品
				int unselected = dp[i - 1][w];
				// 选第 i 个物品
				int selected = vals[i - 1] + dp[i - 1][w - wt[i - 1]];
				max = Math.max(unselected, selected);
			}
			dp[i][w] = max;
		}
	}
	return dp[N][W];
}
```

## 完全背包

完全背包和 0-1 背包的区别在于：
- 0-1：有限个物品，一个物品只能被选一次，或者不选
- 完全：物品是**无限**个，一个物品可以不选、选一次、选两次 ...

例题（[322. 零钱兑换](https://leetcode.cn/problems/coin-change/)）：

> 给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。
> 
> 计算并返回可以凑成总金额所需的**最少**的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。
> 
> 你可以认为每种硬币的数量是**无限的**。

### 分析

1. 概念
	- 状态：
		- 允许使用的硬币
		- 需要凑成的总金额
	- 选择：
		- 不选
		- 选一次
		- 选两次
		- ...
	- base case：
		- 当 amount = 0 时，最小硬币数为 0
2. 定义二维 dp 数组：对于前 i 个硬币，需要凑齐金额为 amount，使用的最小硬币数为 `dp[i][j]`
3. 状态转移方程：
	- 不选：`dp[i][j] = dp[i - 1]dp[j]`
	- 选：`dp[i][j] = dp[i][j - coins[i - 1]] + 1`（因为硬币是**可以被重复选择的**, 所以这里不是 `dp[i - 1]`）

### 代码

```java
public int coinChange(int[] coins, int amount) {
	int MAX_VALUE = Integer.MAX_VALUE - 1;
	int n = coins.length;
	int[][] dp = new int[n + 1][amount + 1];
	for (int i = 0; i <= n; i++) {
		for (int j = 1; j <= amount; j++) {
			dp[i][j] = MAX_VALUE;
		}
	}
	// base case: dp[...][0] = 0

	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= amount; j++) {
			int coin = coins[i - 1];
			int min;
			if (coin > j) {
				min = dp[i - 1][j];
			} else {
				min = Math.min(dp[i - 1][j], // unselected
						dp[i][j - coin] + 1);  // selected
			}
			dp[i][j] = min;
		}
	}
	// 判断是否能凑齐
	return (dp[n][amount] == MAX_VALUE) ? -1 : dp[n][amount];
}
```