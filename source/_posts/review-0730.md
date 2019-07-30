---
title: LeetCode复习
date: 2019/7/30 
tags:
    - LeetCode
categories:
    - 技术总结
---

## findDuplicate & findDuplicates

* 都是在长位n的数组里面出现重复的数字，数字在1-n之间

* 第一题重复的数字为一个，可重复一次以上；第二题重复的数字为多个，重复的次数为2次

* 都可以使用遍历的方法，先遍历数组记录每个数字出现的次数，再从记录中找到重复次数大于2的即可。但是对于第一题效率不高，第二题还可以

* 也可采用索引的方法或Cycle Detection

```java
//龟兔赛跑，快的和慢的会在环内相遇，并且从相遇点到环首和从头到环首的路程相等
//[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }while(slow != fast);
        
        int head = nums[0];
        while(slow != head) {
            slow = nums[slow];
            head = nums[head];
        }
        
        return head;
            
    }
}
```

```java
class Solution {
    public List<Integer> findDuplicates(int[] nums) {
        List<Integer> res = new ArrayList<>();
        
        for(int i = 0; i < nums.length; i++) {
            int index = Math.abs(nums[i]) - 1;
            if(nums[index] < 0)
                res.add(index + 1);
            nums[index] = -nums[index];//因为重复两次，所以把遇到过的索引值位置的数字设置成负的
            }
        
        return res;
    }
}
```

## [longest palindromic substring](https://leetcode.com/problems/longest-palindromic-substring/) & [longest palindromic subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)

* substring需要连续的；subsequence可以不连续

* 都可以用DP解决，但是substring主要关注的是当前位置是否可以构成回文的（连续的）；而subsequence则关注的是当前位置的最大回文长度

```java
public String longestPalindrome(String s) {
    int n = s.length();
    String res = null;
    
    //dp represents whether s(i...j) can form a palindromic substring
    //dp[i][j] is true when s.charAt[i] == s.charAt[j] and dp[i+1][j-1] is true
    //when j - i < 3, which means substring's length is 1,2,3 dp can be true without second condition
    boolean[][] dp = new boolean[n][n];
    for(int i = n - 1; i >=0; i--) {
        for(int j = i; j < n; j++) {
            dp[i][j] = s.charAt[i] == s.charAt[j] && (j - i < 3 || dp[i+1][j-1]);
            if(dp[i][j] && (res == null || j - i + 1 > res.length()))
                res = s,substring(i, j + 1);
        }
    }
    return res;
}
```

```java
public int longestPalindromicSubsequence(string s) {
    int n = s.length();
    int[][] dp = new int[n][n];
    
    for(int i = n - 1; i >= 0; i--)
        for(int j = i; j < n; j++) {
            if(i == j) {
                dp[i][j] = 1;
                continue;
            }
            if(s.charAt(i) == s.charAt(j)) {
                dp[i][j] = dp[i + 1][j - 1] + 2;
                continue;
            }
            dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1]);
        }
    
    return dp[0][n - 1];
}
```
## 计数排序count sort

```java
class Solution {
    public int[][] allCellsDistOrder(int R, int C, int r0, int c0) {
        int[] counter = new int[R + C + 1];
        int[][] ans = new int[R * C][2];
        
        for(int r = 0; r < R; r++) {
            for(int c = 0; c < C; c++) {
                int distance = Math.abs(r - r0) + Math.abs(c - c0);
                counter[distance]++;
            }
        }
        
        for(int i = 1; i < counter.length; i++) {
            counter[i] += counter[i - 1];
        }
        
        for(int r = 0; r < R; r++) {
            for(int c = 0; c < C; c++) {
                int distance = Math.abs(r - r0) + Math.abs(c - c0);
                ans[--counter[distance]] = new int[]{r , c};
            }
        }
        
        return ans;
    }
}
```

```java
class Solution {
    public int[] relativeSortArray(int[] arr1, int[] arr2) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        for(int num : arr1)
            map.put(num, map.getOrDefault(num, 0) + 1);
        
        int index = 0;
        for(int num : arr2) {
            while(map.get(num) > 0){
                arr1[index++] = num;
                map.put(num, map.get(num) - 1);
            }
        }
        
        for(int num : map.keySet()) {
            while(map.get(num) > 0) {
                arr1[index++] = num;
                map.put(num, map.get(num) - 1);
            }
        }
        
        return arr1;
    }
}
```

