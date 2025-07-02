---
{"dg-publish":true,"dg-permalink":"TopK 问题","permalink":"/TopK 问题/"}
---


#算法 

## 概念

https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/

TopK 问题即从数组中找到最大或者最小的几个数字，如：

> 输入整数数组 *arr* ，找出其中最小的 *k* 个数。
> 
> 例如：输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

## 解法

### 堆

通过 Java 的 *PriorityQueue* 来实现

```java
public int findKthLargest(int[] nums, int k) {
	// 倒序排列的堆
	PriorityQueue<Integer> queue =
			new PriorityQueue<>(Comparator.comparingInt(num -> -num));

	for (int num : nums) {
		queue.offer(num);
	}
	int[] result = new int[k];
	for (int i = 1; i < k; i++) {  // 遍历 K - 1 遍
		queue.poll();
	}
	return queue.peek();
}
```

### 快排思想

根据快排的思想，每次 [partition](partition.md) 后，判断基准数在数组中的索引是否等于 k ，若为 true 则直接返回此时数组的前 k 个数字即可，否则：
- *j > k*：代表第 k+1 小的数字在**左**子数组中：则递归左子数组
- *j < k*：代表第 k+1 小的数字在**右**子数组中：则递归右子数组

![](屏幕录制2022-03-26%2022.10.58.mov)

代码：

```java
public int[] getLeastNumbers(int[] arr, int k) {
    if (k == 0) return new int[]{};
    return quickSort(arr, 0, arr.length - 1, k - 1);
}

private int[] quickSort(int[] arr, int low, int high, int k) {
    int pivot = partition(arr, low, high);
    if (pivot == k) {
        return Arrays.copyOf(arr, pivot + 1);
    } else if (pivot < k) {
        return quickSort(arr, pivot + 1, high, k);
    } else {
        return quickSort(arr, low, pivot - 1, k);
    }
}

private int partition(int[] arr, int low, int high) {
    int pivot = arr[low];
    while (low < high) {
        while (low < high && arr[high] >= pivot) high--;
        arr[low] = arr[high];

        while (low < high && arr[low] <= pivot) low++;
        arr[high] = arr[low];
    }
    arr[low] = pivot;
    return low;
}
```