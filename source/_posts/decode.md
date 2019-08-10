---
title: 拆解/组合
date: 2019/8/8
tags:
    - LeetCode
categories:
    - 技术总结
---

## context

有一些题目是给你一个数字/字符，再给你字典，让你把这些给定的东西拆开，通常会问能否拆开，或者能拆开的话是否有最优的一个值（比方说，最小的数目）

## [ugly-number](https://leetcode.com/problems/ugly-number/)

ugly number是2，3,5乘积的结果，这三个数没有数目限制。

```java
class Solution {
    public boolean isUgly(int num) {
        if(num <= 0)
            return false;
        
        int[] factors = {2, 3, 5};
        for(int d : factors) {
            while(num % d == 0)
                num /= d;
        }
        
        return num == 1;
    }
}
```

* [ugly-number-ii](https://leetcode.com/problems/ugly-number-ii/)

反向从大到小组成ugly number，后面出现的number一定是前面出现的ugly number乘2/3/5得到的

## [coin change](https://leetcode.com/problems/coin-change/)

给定一定的数字集合，再给一个数，判断这个数是否可以由前面的数字集合组成，会出现很多种组合，但是找出分解数目最小的值。

对于一个数A能否被正确分解，需要看A - 数字集合中某个数能否被分解（子问题）。数字集中的数字本身可以被分解。

找最小的分解数目，需要从A-数字集合中出现的数的分解数目中找到最小的值。初始数字0的分解数目为0。记录数组需要将所有的位置初始化为一个大值。
如果最后的结果还是那个大值，就说明不能分解。

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        for(int i = 1; i <= amount; i++) {
            for(int coin : coins) {
                if(i - coin >= 0)
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
        if(dp[amount] == amount + 1)
            return -1;
        return dp[amount];
    }
}
```

## [word-break](https://leetcode.com/problems/word-break/)

字符串能够被字典中的数字分解？

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        if(s.length() == 0 || s == null)
            return false;
        
        int n = s.length();
        boolean[] dp = new boolean[n];
        
        for(int i = 0; i < n; i++) {
            for(int j = 0; j <= i; j++) {
                String sub = s.substring(j, i + 1);
                if(wordDict.contains(sub) && (j == 0 || dp[j - 1])) {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n - 1];
    }
}
```

## [343. Integer Break](https://leetcode.com/problems/integer-break/)

```java
class Solution {
    public int integerBreak(int n) {
        if(n == 2)
            return 1;
        if(n == 3)
            return 2;
        int tnum = n / 3;
        if(n % 3 == 1){
            return (int)Math.pow(3, tnum - 1) * 4;
        }
        return n % 3 == 0 ? (int)Math.pow(3, tnum) : (int)Math.pow(3, tnum) * (n % 3);
        
    }
}
```