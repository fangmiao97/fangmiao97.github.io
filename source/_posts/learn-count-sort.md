---
title: 计数排序
date: 2019/07/21
categories: 
    - 算法
---

## 内容

- 找出待排序的数组中最大和最小的元素
 
- 统计数组中每个值为i的元素出现的次数，存入数组C的第i项
 
- 对所有的计数累加（从C 中的第一个元素开始，每一项和前一项相加）

- 反向填充目标数组：将每个元素i放在新数组的第 C[i]项，每放一个元素就将 C[i]减去1

- 场景：要排序的量大，但是取值范围小，比如：高考分数对应的排名

## 实现

```java
public class CountingSort {
    public static void main(String[] argv) {
        int[] A = CountingSort.countingSort(new int[]{16, 4, 10, 14, 7, 9, 3, 2, 8, 1});
        Utils.print(A);
    }

    public static int[] countingSort(int[] A) {
        int[] B = new int[A.length];
        // 假设A中的数据a'有，0<=a' && a' < k并且k=100
        int k = 100;
        countingSort(A, B, k);
        return B;
    }

    private static void countingSort(int[] A, int[] B, int k) {
        int[] C = new int[k];
        // 计数
        for (int j = 0; j < A.length; j++) {
            int a = A[j];
            C[a] += 1;
        }
        Utils.print(C);
        // 求计数和
        for (int i = 1; i < k; i++) {
            C[i] = C[i] + C[i - 1];
        }
        Utils.print(C);
        // 整理
        for (int j = A.length - 1; j >= 0; j--) {
            int a = A[j];
            B[C[a] - 1] = a;
            C[a] -= 1;
        }
    }
}


//针对c数组的大小，优化过的计数排序
public class CountSort{
	public static void main(String []args){
		//排序的数组
		int a[] = {100, 93, 97, 92, 96, 99, 92, 89, 93, 97, 90, 94, 92, 95};
		int b[] = countSort(a);
		for(int i : b){
			System.out.print(i + "  ");
		}
		System.out.println();
	}
	public static int[] countSort(int []a){
		int b[] = new int[a.length];
		int max = a[0], min = a[0];
		for(int i : a){
			if(i > max){
				max = i;
			}
			if(i < min){
				min = i;
			}
		}
		//这里k的大小是要排序的数组中，元素大小的极值差+1
		int k = max - min + 1;
		int c[] = new int[k];
		for(int i = 0; i < a.length; ++i){
			c[a[i]-min] += 1;//优化过的地方，减小了数组c的大小
		}
		for(int i = 1; i < c.length; ++i){
			c[i] = c[i] + c[i-1];
		}
		for(int i = a.length-1; i >= 0; --i){
			b[--c[a[i]-min]] = a[i];//按存取的方式取出c的元素
		}
		return b;
	}
}
```

## 运用

* [1030. Matrix Cells in Distance Order](https://leetcode.com/problems/matrix-cells-in-distance-order/)

* [1122. Relative Sort Array](https://leetcode.com/problems/relative-sort-array/)