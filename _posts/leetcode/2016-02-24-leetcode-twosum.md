---
layout: post
title: "LeetCode: Two Sum"
date: 2016-02-24 09:07:00 +0800
categories: leetcode
---
### 简单实现：时间复杂度O(n2)

~~~java
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    result[0] = i;
                    result[1] = j;
                    break;
                }
            }
        }
        return result;
    }
}
~~~
