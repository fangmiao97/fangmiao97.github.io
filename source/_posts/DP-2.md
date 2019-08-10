---
title: DP-当前状态与前面所有状态都有关
date: 2019/8/8 
tags:
    - LeetCode
categories:
    - 技术总结
---

## Context

这几天一直刷LeetCode的DP题，刷了挺多的，其实还有很多题想不出来思路。还是刷的太少。

今天遇到一题[300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)，乍一看和之前的最大子数组看起来挺像，但是这个是最大子序列。

也就是说，对于当前位置，前面的长度可以形成很多种组合。但是我受相似题的影响，一直在寻找当前位置与前一个位置的关系，这样是不行的。

因为是子序列不一定就是前一个序列加/不加当前位置的值得到的，必须考虑前面的全部情况。

此时dp数组保存的是从头到对应位置最长的子序列长度，如果当前位置的值大于前面某个位置的值，当前位置的最长子序列长度就是比较位置的值加一。对于当前位置前所有位置都要比较一次，找到最大的。

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums.length == 0)
            return 0;
        
        int[] dp = new int[nums.length];
        dp[0] = 1;
        int res = 1;
        for(int i = 1; i < dp.length; i++) {
            int maxv = 0;
            for(int j = 0; j < i; j++) {
                if(nums[i] > nums[j])
                    maxv = Math.max(maxv, dp[j]); 
            }
            dp[i] = maxv + 1;
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```

这道题有个很巧妙的解法（也很难想到）。

用一个数组记录一个可能成立的子序列。从前往后扫描输入数组，如果当前值比记录数组的最后一个数字大，就追加在这个数组后面。如果不能追加，就使用二分查找寻找到比其大的所有数中的最小值，将其替换。这个的目的是为了，使得之后出现的潜在的更长的子序列不被忽略。

```java
public class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        int[] dp = new int[nums.length];
        dp[0] = nums[0];
        int len = 0;
        for (int i = 1; i < nums.length; i++) {
            int pos = binarySearch(dp,len,nums[i]);
            if (nums[i] < dp[pos]) dp[pos] = nums[i];
            if (pos > len) {
                len = pos;
                dp[len] = nums[i];
            }
        }
        return len+1;
    }
    private int binarySearch(int[] dp, int len, int val) {
        int left = 0;
        int right = len;
        while(left+1 < right) {
            int mid = left + (right-left)/2;
            if (dp[mid] == val) {
                return mid;
            } else {
                if (dp[mid] < val) {
                    left = mid;
                } else {
                    right = mid;
                }
            }
        }
        if (dp[right] < val) return len+1;
        else if (dp[left] >= val) return left;
        else return right;
    }
}
```