---
title: 1月算法复习
date: 2020/01/19
categories:
    - 算法
tags:
    - LeetCode
---

## Path Sum 3

* 二叉树中任意起点和终点上的路径和等于sum

使用前缀和，每次把一个路径看成一个数组，计算前缀和，两个点的差值为sum时即找到。

```
    public int pathSum(TreeNode root, int sum) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);  //Default sum = 0 has one count，一个节点的val刚好等于sum也是答案
        return backtrack(root, 0, sum, map); 
    }
    //BackTrack one pass
    public int backtrack(TreeNode root, int sum, int target, Map<Integer, Integer> map){
        if(root == null)
            return 0;
        sum += root.val;
        int res = map.getOrDefault(sum - target, 0);//See if there is a subarray sum equals to target
        map.put(sum, map.getOrDefault(sum, 0)+1);
        //Extend to left and right child
        res += backtrack(root.left, sum, target, map) + backtrack(root.right, sum, target, map);
        map.put(sum, map.get(sum)-1);   //Remove the current node so it wont affect other path
        return res;
    }
```

最后一定要注意有一个将map中sum对应值减一的操作

## Binary Tree Maximum Path Sum

找到二叉树里面具有最大和（max sum）的路径，这个路径不一定要从上到下，就是二叉树中随便一条路径，但是不能有分叉，也就是说路径是一条，不会分开。

```java
class Solution {
    //全局最大值，最终返回的值
    int max = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        helper(root);
        return max;
    }
    
    //返回包括这个root在内的最大路径，3中选择：root本身，root+left的值，root+right的值
    public int helper(TreeNode root) {
        if(root == null)
            return 0;
        
        //默认都会有返回值，最小值为0
        int left = Math.max(0, helper(root.left));
        int right = Math.max(0, helper(root.right));
        
        //更新max
        max = Math.max(max, root.val + left + right);
        return root.val+Math.max(left,right);//只能返回一边，而且包括root
    }
}
```
