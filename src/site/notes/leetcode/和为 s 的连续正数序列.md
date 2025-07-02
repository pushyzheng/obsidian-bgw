---
{"dg-publish":true,"dg-permalink":"和为 s 的连续正数序列","permalink":"/和为 s 的连续正数序列/","title":"和为s的连续正数序列","tags":["双指针","滑动窗口"]}
---


双指针，滑动窗口实现：

![](/img/user/attachments/images/Pasted image 20220326112543.png)

参考代码：

```java
public int[][] findContinuousSequence(int target) {
	List<int[]> result= new ArrayList<>();

	int i=2,j=1;
	int sum=3;
	while(j<i){
		if(sum==target){
			int[] arr=new int[i-j+1];
			for(int k=j;k<=i;k++){
				arr[k-j]=k;
			}
			result.add(arr);
		}

		if (sum < target){
			i++;
			sum+=i;
		} else {
			sum-=j;       
			j++;
		}
	}
	return result.toArray(new int[0][]);
}
```