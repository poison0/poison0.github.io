---
layout: post
title: 算法题
date: 2020-09-20
Author: AzureWind
tags: [算法]
comments: true
---
刷算法题(leetcode-cn.com)
<!-- more -->

***

## 两数之和（简单）
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

示例:
```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
#### 1.暴力解法
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i=0;i<nums.length;i++){
            for(int j=i+1;j<nums.length;j++){
                if((nums[i]+nums[j])==target){
                    return new int[]{i,j};
                }
            }
        }
        return new int[2];
    }
}
```
空间复杂度 O(1)   
时间复杂度 O(n<sup>2</sup>)

***

## 子集(中等)

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:
```
输入: nums = [1,2,3]
输出:
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
#### 1.循环枚举
```java
public class test33 {
    public static void main(String[] args) {
        System.out.println(subsets(new int[]{1, 2, 3}));
    }
    public static List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        res.add(new ArrayList<>());
        for(int num: nums){
            int size = res.size();
            for (int j = 0; j < size; j++) {
                List<Integer> item = new ArrayList<>(res.get(j));
                item.add(num);
                res.add(item);
            }
        }
        return res;
    }
}
```