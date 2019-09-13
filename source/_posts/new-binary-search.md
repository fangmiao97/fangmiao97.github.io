---
title: 对Binary search的改良应用
date: 2019/08/16
categories: 
    - 算法
---

## context

binary search运用在sorted array，对于array中出现重复数字或者序列不是单调递增，是分段单调的，就要修改nums[mid] == target时的操作。

## [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums.length == 0)
            return -1;
        
        int minIdx = findMinIdx(nums);
        if(target == nums[minIdx])
            return minIdx;
        int m = nums.length;
        int start = (target <= nums[m - 1]) ? minIdx : 0;
        int end = (target <= nums[m - 1]) ? m - 1 : minIdx;
        
        while(start <= end) {
            int mid = start + (end - start) / 2;
            if(nums[mid] == target)
                return mid;
            else if(target > nums[mid])
                start = mid + 1;
            else
                end = mid - 1;
        }
        return -1;
    }
    
    public int findMinIdx(int[] nums) {
        int start = 0, end = nums.length - 1;
        while(start < end) {
            int mid = start + (end - start) / 2;
            if(nums[mid] > nums[end])
                start = mid + 1;
            else
                end = mid;
        }
        return start;
    }
}
```

## [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] targetRange = {-1 , -1};
        int leftIdx = extremeInsertionIndex(nums, target, true);
        
        if(leftIdx == nums.length || nums[leftIdx] != target)
            return targetRange;
        
        targetRange[0] = leftIdx;
        targetRange[1] = extremeInsertionIndex(nums, target, false) - 1;
        
        return targetRange;
    }
    
    private int extremeInsertionIndex(int[] nums, int target, boolean left) {
        int lo = 0;
        int hi = nums.length;
        while(lo < hi) {
            int mid = (lo + hi) / 2;
            if(nums[mid] > target || (left && target == nums[mid])) {
                hi = mid;
            }
            else {
                lo = mid + 1;
            }
        }
        return lo;
    }
}
```