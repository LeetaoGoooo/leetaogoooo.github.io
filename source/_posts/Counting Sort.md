---
title: Counting Sort
tags: [algorithm]

date: 2017-08-16
published: true
hideInList: false
feature: 
isTop: false
---







# 前言

>In computer science, counting sort is an algorithm for sorting a collection of objects according to keys that are small integers; that is, it is an integer sorting algorithm. It operates by counting the number of objects that have each distinct key value, and using arithmetic on those counts to determine the positions of each key value in the output sequence. Its running time is linear in the number of items and the difference between the maximum and minimum key values, so it is only suitable for direct use in situations where the variation in keys is not significantly greater than the number of items. However, it is often used as a subroutine in another sorting algorithm, radix sort, that can handle larger keys more efficiently.——摘自 Wikipedia [Counting sort](https://en.wikipedia.org/wiki/Counting_sort)

从上述的文字中我们可以了解到以下的信息:
1. 计数排序是一个整数排序算法
2. 计数排序的时间复杂度是线性的
3. 计数排序通常只适用于键值范围不是远大于元素数量的情况
4. 基数排序可以解决**3**不适用的范围

# 思想

这里以数组 [2,5,3,1,2,3,1,3],作为测试数据,这里很容易看出来键值范围为 **0~5(k)**.对于数组中的元素来说,如果我知道有多少元素不大于该元素的话,基于这样的前提条件下,就可以知道排序后该元素在数组中的位置.

# 过程
基于上述的思路,我们尝试去模拟一下整个过程:

![](http://ww1.sinaimg.cn/large/006wYWbGly1filmwmgr8dj30s11fpjsx.jpg)

# 代码

```java
public class CountSort {

	private static int[] countSort(int[] A,int k) {
		int[] B = new int[A.length];
		int[] C = new int[k+1];

		for(int i = 0; i < A.length; i++) {
			C[A[i]]++;
		}

		for(int i = 1; i < C.length; i++) {
			C[i] = C[i] + C[i-1];
		}

		for(int j = A.length -1; j >=0 ; j--) {
			B[C[A[j]]-1] = A[j];
			C[A[j]]--;
		}
		return B;
	}

	public static void main(String[] args) {
		int[] A = new int[]{2,5,3,1,2,3,1,3};
		int[] B = countSort(A, 5);
		for(int i: B) {
			System.out.print(i+" ");
		}
	}
}
```
输出结果如下:
```shell
1 1 2 2 3 3 3 5
```
# 优化
仔细观察一下数组C,我们可以发现对于数组C,其索引为0的元素对应的值为0,有没有可能将数组C的所有元素全部左移一位呢.从而减少开销呢？完全可以的,我们可以将长度由原来的*k+1*，优化为*max-min+1*,其中max,min分别对应为A的最大值和最小值.

```java
public class CountSort {

	private static int[] countSort(int[] A) {
		int[] B = new int[A.length];
		int max = A[0], min = A[0];
		
		// 获取max,min
		for(int a: A) {
			if(a > max) {
				max = a;
			}
			if(a < min ) {
				min = a;
			}
		}
		
		int k = max - min + 1;
		int[] C = new int[k];

		//减少了C数组的大小
		for(int i = 0; i < A.length; i++) {
			C[A[i]-min]++; 
		}

		for(int i = 1; i < C.length; i++) {
			C[i] = C[i] + C[i-1];
		}

		for(int i = A.length - 1; i >= 0; i--) {
			B[C[A[i]-min] - 1] = A[i];
			C[A[i]-min]--;
		}

		return B;
	}

	public static void main(String[] args) {
		int[] A = new int[]{2,5,3,1,2,3,1,3};
		int[] B = countSort(A);
		for(int i: B) {
			System.out.print(i+" ");
		}
	}
}
```

# 参考资料

[CountingSort](https://en.wikipedia.org/wiki/Counting_sort)<p>
[计数排序详解以及java实现](http://www.cnblogs.com/developerY/p/3166462.html)<p>
[计数排序](https://baike.baidu.com/item/%E8%AE%A1%E6%95%B0%E6%8E%92%E5%BA%8F/8518144?fr=aladdin)