---
title: 两道DP类比
date: 2020/01/30
tags:
    - LeetCode
categories:
    - 技术总结
---

## [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

这里相同组合，不同顺序被认为是不同的

![](https://pic.downk.cc/item/5e32a8442fb38b8c3cffcc3e.jpg)

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for(int i = 1; i <= target; i++) {
            for(int num: nums) {
                if(i - num >= 0) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}
```

## [518. Coin Change 2](https://leetcode.com/problems/coin-change-2/)

![](https://pic.downk.cc/item/5e32a9882fb38b8c3cfff06f.jpg)

这里有顺序，也就是说同一种组合只能有一种顺序，所以外层是coins的循环

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        for(int coin: coins) {
            for(int i = 1; i <= amount; i++) {
                if(i - coin >= 0) {
                    dp[i] += dp[i - coin];
                }
            }
        }
        return dp[amount];
    }
}
```
![](https://pic.downk.cc/item/5e32f5f22fb38b8c3c089367.jpg)