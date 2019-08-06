---
title: 几道相似相通的DP问题
date: 2019/8/4 
tags:
    - Java
categories:
    - 技术总结
---

## context

当前状态是由前面两个相邻的状态决定的

状态可以用array来保存，或者用常量保存，但是要知道变更状态

dp[i] = option(dp[i - 1], dp[i - 2])


## [fibonacci-number](https://leetcode.com/problems/fibonacci-number/)

```java
class Solution //自上而下
{
    int[] fib_cache = new int[31];
	
	public int fib(int N)
    {
        if(N <= 1)
            return N;
        else if(fib_cache[N] != 0)
            return fib_cache[N];
		else 
            return fib_cache[N] = fib(N - 1) + fib(N - 2);
    }
}

class Solution //自下而上
{
    public int fib(int N)
    {
        if(N <= 1)
            return N;
        
		int a = 0, b = 1;//或者使用array map来存储每个位置的值也是可以的
		
		while(N-- > 1)
		{
			int sum = a + b;
			a = b;
			b = sum;
		}
        return b;
    }
}
```

## [climbing-stairs](https://leetcode.com/problems/climbing-stairs/)


You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

Note: Given n will be a positive integer.

one can reach ith step in one of two ways:

* climb 1 stair from (i - 1)th step

* climb 2 stairs from (i - 2)th step

so the problem of numbers of ways to ith step breaks up into the sum of ways to (i - 1)th step and (i - 2)th step

```java
class Solution {
    public int climbStairs(int n) {
        if(n == 1)
            return 1;
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for(int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
}
```

## [min-cost-climbing-stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)

the i-th step has some non-negative cost cost[i] assigned (0 indexed).

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int f1 = cost[0], f2 = cost[1];
        for(int i = 2; i < cost.length; i++) {
            int f_cur = cost[i] + Math.min(f1, f2);
            f1 = f2;
            f2 = f_cur;
        }
        return Math.min(f1, f2);
    }
}
```

## [Decode Ways](https://leetcode.com/problems/decode-ways/)

从0位置到当前位置长度能够解码出字符串的数目，由当前位置长度减一和减二的数目决定。

如果当前的位置能够解码出一个字母就算上前面一位的数目，如果当前位置和前一位能够组成另一个字母就算上更前面的数目。

```java
class Solution {
    public int numDecodings(String s) {
        if(s.length() < 1 || s.charAt(0) == '0')
            return 0;
        
        int[] dp = new int[s.length()];
        dp[0] = 1;
        
        for(int i = 1; i < s.length(); i++) {
            if(s.charAt(i) != '0')
                dp[i] = dp[i - 1];
            if(s.charAt(i - 1) != '0') {
                int num = (s.charAt(i - 1) - '0') * 10 + (s.charAt(i) - '0');
                if(num <= 26)
                    dp[i] += i - 2 >= 0 ? dp[i - 2] : 1;
            }
        }
        
        return dp[s.length() - 1];
    }
}
```
