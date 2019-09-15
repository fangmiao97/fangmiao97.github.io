---
title: 计数排序变型
date: 2019/9/15
categories:
    - 技术总结
tags:
    - LeetCode
---

## context

[75. Sort Colors](https://leetcode.com/problems/sort-colors/)

这是一道以计数排序为基本解法的题，而且最大值为2，比较好实现。

## count sum

```java
class Solution {
    public void sortColors(int[] nums) {
        int[] counts = new int[3];
        for(int num: nums) {
            counts[num]++;
        }
        
        for(int i = 0; i < nums.length; i++) {
            if(counts[0] > 0) {
                nums[i] = 0;
                counts[0]--;
                continue;
            }
            else if(counts[1] > 0) {
                nums[i] = 1;
                counts[1]--;
                continue;
            }else if(counts[2] > 0) {
                nums[i] = 2;
                counts[2]--;
                continue;
            }
        }
    }
}
```

## 改造的countsum

```java
class Solution {
    public void sortColors(int[] nums) {
        int n0 = -1, n1 = -1, n2 = -1;
        for(int i = 0; i < nums.length; i++) {
            if(nums[i] == 0) {
                nums[++n2] = 2;
                nums[++n1] = 1;
                nums[++n0] = 0;
            }else if(nums[i] == 1) {
                nums[++n2] = 2;
                nums[++n1] = 1;
            }else{
                nums[++n2] = 2;
            }
        }
    }
}
```

## 交换

由于只有0/1/2这三个数，将0从最前面开始放，2从最后面开始放。

```java
class Solution {
    public void sortColors(int[] nums) {
        int zeroIndex = 0, twoIndex = nums.length - 1;
        for(int i = 0; i <= twoIndex;) {
            if(nums[i] == 0) {
                nums[i] = nums[zeroIndex];
                nums[zeroIndex] = 0;
                i++;
                zeroIndex++;
            }else if(nums[i] == 2) {
                nums[i] = nums[twoIndex];
                nums[twoIndex] = 2;
                twoIndex--;
            }else
                i++;
        }
    }
}
```