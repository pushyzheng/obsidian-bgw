---
{"dg-publish":true,"dg-permalink":"和为 s 的两个数字","permalink":"/和为 s 的两个数字/","title":"和为 s 的两个数字","tags":["数组","双指针","滑动窗口","对撞双指针"]}
---


和[[leetcode/两数之和\|两数之和]]区别在于：该数组是**有序**的。

通过两个对撞指针 *left*、*right*：
- *sum > target*：缩小右边界，减少 *right* 值
- *sum < target*：缩小左边界，增大 *left* 值
- *sum == target*：返回结果

```java
public int[] twoSum(int[] nums, int target) {  
    int left = 0, right = nums.length - 1;  
    while (left < right) {  
        int sum = nums[left] + nums[right];  
        if (sum == target) 
            return new int[]{nums[left], nums[right]};  
        else if (sum < target) 
            left++;  
        else 
            right--;  
    }  
    return new int[]{};  
}
```