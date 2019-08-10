---
title: minimax
date: 2019/8/9
tags:
    - LeetCode
categories:
    - 技术总结
---
## context

LeetCode上面遇到一题[guess-number-higher-or-lower-ii](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

其中讲到了minimax这个算法。

首先这个题目的意思是说，要付多少钱（多少代价）才能保证猜对数字，这个代价是最小的，但是能够保证，即使是猜序列中的所有数字，这个代价都是够用的。

这里就出现一个零和游戏的状况，也就是说游戏的结果不是双赢的，你赢10元我就输10元。那么这个游戏，我想要花最少的钱，然而你想从我这拿最多的钱。所以我需要考虑最坏的情况下，找到最小的花费。

```java
class Solution {
    public int getMoneyAmount(int n) {
        int[][] dp = new int[n + 1][n + 1];
        return miniMax(1, n, dp);
    }
    
    private int miniMax(int i, int j, int[][] dp) {
        if(i >= j)
            return 0;
        if(dp[i][j] != 0)
            return dp[i][j];
        dp[i][j] = Integer.MAX_VALUE;
        for(int k = i; k <= j; k++) {
            dp[i][j] = Math.min(dp[i][j], Math.max(miniMax(i, k - 1, dp), miniMax(k + 1, j, dp)) + k);
        }
        return dp[i][j];
    }
}
```
