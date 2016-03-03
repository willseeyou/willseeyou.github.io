---
layout: post
title: "LeetCode: Two Sum"
date: 2016-02-24 09:07:00 +0800
categories: leetcode
---
### Question
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution.

~~~
Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
~~~

### 简单实现：时间复杂度O(n2)

~~~java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = new int[2];
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    result[0] = i;
                    result[1] = j;
                    break;
                }
            }
        }
        return ans;
    }
}
~~~

### HashMap实现：时间复杂度O(n)

~~~java
public class Solution {
  public int[] twoSum(int[] nums, int target) {
    int[] ans = null;
    Map<Integer, Integer> maps = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
      Integer index = maps.get(target - nums[i]);
      if (index == null) {
        maps.put(nums[i], i);
      } else {
        ans = new int[] {index, i};
      }
    }
    return ans;
  }
}
~~~
