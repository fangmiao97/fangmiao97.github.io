---
title: Kadane算法
date: 2019/05/27
categories: 
    - 算法
---
## context
[53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
[121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/discuss/?currentPage=1&orderBy=most_posts&query=)

## 最大子数组之和

algorithm that operates on arrays: it starts at the left end (element A[1]) and scans through to the right end (element A[n]), keeping track of the maximum sum subvector seen so far. The maximum is initially A[0]. Suppose we've solved the problem for A[1 .. i - 1]; how can we extend that to A[1 .. i]? The maximum
sum in the first I elements is either the maximum sum in the first i - 1 elements (which we'll call MaxSoFar), or it is that of a subvector that ends in position i (which we'll call MaxEndingHere).
MaxEndingHere is either A[i] plus the previous MaxEndingHere, or just A[i], whichever is larger.

```java
public static int maxSubArray(int[] A) {
    int maxSoFar=A[0], maxEndingHere=A[0];
    for (int i=1;i<A.length;++i){
    	maxEndingHere= Math.max(maxEndingHere+A[i],A[i]);
    	maxSoFar=Math.max(maxSoFar, maxEndingHere);	
    }
    return maxSoFar;
}
```

## 最大利润

最大利润是一段时间的利润总和，如果我们知道了每天相对于前一天的利润，也就是每天卖出一次，再买进一次。
计算出一个表，表中的每一项为prices[i] - prices[i - 1]。

求这个以天为维度的利润序列的最大子数组之和（用上面一题的算法），就是最大利润。

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int max_ending_here, max_so_far;
        max_ending_here = max_so_far =0;
        for(int n = 1; n < prices.length; n++){
            max_ending_here = Math.max(max_ending_here + prices[n] - prices[n-1], prices[n] - prices[n-1]);
            max_so_far = Math.max(max_ending_here, max_so_far);
        }
        return max_so_far;
        }
    }
```

将上面的流程精简
```java
public int maxProfit(int[] prices) {
        int maxCur = 0, maxSoFar = 0;
        for(int i = 1; i < prices.length; i++) {
            maxCur = Math.max(0, maxCur += prices[i] - prices[i-1]);
            maxSoFar = Math.max(maxCur, maxSoFar);
        }
        return maxSoFar;
    }
```
maxCur是当前一段时间的最大利润，当利润降为0时，就不再计算这个时间段的利润，重新开始一个新的序列
maxSoFar就是所有天中，利润最大的时间序列的利润值

### 解释
A more clear explanation on why sum of subarray works.:

Suppose we have original array:
[a0, a1, a2, a3, a4, a5, a6]

what we are given here(or we calculate ourselves) is:
[b0, b1, b2, b3, b4, b5, b6]

where,
b[i] = 0, when i == 0
b[i] = a[i] - a[i - 1], when i != 0

suppose if a2 and a6 are the points that give us the max difference (a2 < a6)
then in our given array, we need to find the sum of sub array from b3 to b6.

b3 = a3 - a2
b4 = a4 - a3
b5 = a5 - a4
b6 = a6 - a5

adding all these, all the middle terms will cancel out except two
i.e.

b3 + b4 + b5 + b6 = a6 - a2

a6 - a2 is the required solution.

so we need to find the largest sub array sum to get the most profit



