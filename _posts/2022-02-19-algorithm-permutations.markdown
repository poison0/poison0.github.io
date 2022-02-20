---
layout: post
title: 全排列
keywords: 技术,算法,leetCode,力扣
date: 2022-02-20
Author: AzureWind
tags: [算法]
comments: true
---
#### 46. 全排列
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。
<!-- more -->

> 示例 1：
```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```
> 示例 2：
```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```
```java
回溯法
class Solution {
	List<List<Integer>> res = new ArrayList<>();
	public List<List<Integer>> permute(int[] nums) {
		ArrayList<Integer> track = new ArrayList<>();
		for (int num : nums) {
			track.add(num);
		}

		permute(track,new ArrayList<>());
		return res;
	}

	private void permute(List<Integer> output, List<Integer> per) {
		if (output.size() == per.size()) {
			res.add(new ArrayList<>(per));
			return;
		}
		for (int i = 0; i < output.size(); i++) {
			if (!per.contains(output.get(i))) {
				per.add(output.get(i));
				permute(output,per);
				per.remove(per.size() - 1);
			}
		}
	}
}
```
