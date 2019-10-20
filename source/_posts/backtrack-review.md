---
title: 典型backtrack回顾
date: 2019/9/18
categories:
    - 技术总结
tags:
    - Java
---

## context

我觉得面试很大概率会让写回溯的题目，这边摘抄一些别人的解答。

* [78. Subsets](https://leetcode.com/problems/subsets/)

```bash
Input: nums = [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        backtrack(res, new ArrayList<>(), nums, 0);
        return res;
    }
    
    public void backtrack(List<List<Integer>> res, List<Integer> tempList, int[] nums, int start) {
        res.add(new ArrayList<>(tempList));
        for(int i = start; i < nums.length; i++) {
            tempList.add(nums[i]);
            backtrack(res, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

* [90. Subsets II](https://leetcode.com/problems/subsets-ii/)

```bash
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

```java
class Solution {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        backtrack(res, new ArrayList<>(), nums, 0);
        return res;
    }
    
    public void backtrack(List<List<Integer>> res, List<Integer> tempList, int[] nums, int start) {
        res.add(new ArrayList<>(tempList));
        for(int i = start; i < nums.length; i++) {
            if(i > start && nums[i] == nums[i - 1])
                continue;
            tempList.add(nums[i]);
            backtrack(res, tempList, nums, i + 1);
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

* [46. Permutations](https://leetcode.com/problems/permutations/)

```bash
Input: [1,2,3] distinct
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if(nums.length == 0)
            return res;
        backtrack(nums, res, new ArrayList<Integer>());
        return res;
    }
    
    public void backtrack(int[] nums, List<List<Integer>> res, List<Integer> temp) {
        if(temp.size() == nums.length) {
            res.add(new ArrayList(temp));
        }else {
            for(int i = 0; i < nums.length; i++) {
                if(temp.contains(nums[i]))
                    continue;
                temp.add(nums[i]);
                backtrack(nums, res, temp);
                temp.remove(temp.size() - 1);
            }
        }
    }
}
```

* [47. Permutations II](https://leetcode.com/problems/permutations-ii/)

```bash
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        boolean[] seen = new boolean[nums.length];
        List<List<Integer>> res = new ArrayList();
        backtrack(nums, seen, new ArrayList(), res);
        return res;
    }
    
    public void backtrack(int[] nums, boolean[] seen, List<Integer> tempList, List<List<Integer>> res) {
        if(tempList.size() == nums.length) {
            res.add(new ArrayList(tempList));
            return;
        }
        else {
            for(int i = 0; i < nums.length; i++) {
                if(seen[i])
                    continue;
                if(i > 0 && nums[i] == nums[i - 1] && !seen[i - 1])
                //if(i > 0 && nums[i] == nums[i - 1] && seen[i - 1]) is also work
                    continue;
                seen[i] = true;
                tempList.add(nums[i]);
                backtrack(nums, seen, tempList, res);
                tempList.remove(tempList.size() - 1);
                seen[i] = false;
            }
        }
    }
}
```

![](https://image.ibb.co/njHR7f/permutation-II-1.jpg)

![](https://image.ibb.co/czdYnf/permutation-II-2.jpg)

* [39. Combination Sum](https://leetcode.com/problems/combination-sum/)

```bash
Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]
//can use duplicated factors
```

```java
public List<List<Integer>> combinationSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
    if(remain < 0) return;
    else if(remain == 0) list.add(new ArrayList<>(tempList));
    else{ 
        for(int i = start; i < nums.length; i++){
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i); // not i + 1 because we can reuse same elements
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

* [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

```bash
Input: candidates = [2,5,2,1,2], target = 5,
numbers in candidates use once
A solution set is:
[
  [1,2,2],
  [5]
]
```

```java
public List<List<Integer>> combinationSum2(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
    
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start){
    if(remain < 0) return;
    else if(remain == 0) list.add(new ArrayList<>(tempList));
    else{
        for(int i = start; i < nums.length; i++){
            if(i > start && nums[i] == nums[i-1]) continue; // skip duplicates since we sort nums before
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i + 1);
            tempList.remove(tempList.size() - 1); 
        }
    }
} 
```