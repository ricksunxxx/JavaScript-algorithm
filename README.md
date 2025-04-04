# JavaScript-algorithm
前端算法


# 1、最接近的三数之和
给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个整数，使它们的和与 target 最接近。
返回这三个数的和。
假定每组输入只存在恰好一个解。
```javascript {0,100}
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var threeSumClosest = function(nums, target) {
    nums.sort((a, b) => a - b); // 先排序
    let closestSum = Infinity;  // 记录最接近 target 的三数之和

    for (let i = 0; i < nums.length - 2; i++) {
        let left = i + 1, right = nums.length - 1;

        while (left < right) {
            let sum = nums[i] + nums[left] + nums[right];

            // 如果找到完全匹配的 target，直接返回
            if (sum === target) return sum;

            // 更新最接近的和
            if (Math.abs(sum - target) < Math.abs(closestSum - target)) {
                closestSum = sum;
            }

            // 移动指针
            if (sum > target) {
                right--;
            } else {
                left++;
            }
        }
    }

    return closestSum;
};
```
时间复杂度：  
排序：O(n log n)  
双指针查找：O(n²)  
总复杂度：O(n²)

# 2、无重复字符的最长子串
给定一个字符串 s ，请你找出其中不含有重复字符的 最长 子串 的长度。  
示例 1:  
输入: s = "abcabcbb"  
输出: 3   
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。  
```javascript  {0,100}
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function (s) {
    let len = 0;
    let left = 0;
    let map = new Map()
    for (let right = 0; right < s.length; right++) {
        if (map.has(s[right])) {
            left = Math.max(left, map.get(s[right]) + 1)
        }
        map.set(s[right], right)
        len = Math.max(len, right - left + 1)
    }

    return len
};

```
时间复杂度：O(n)：left 和 right 都只移动一次。  
空间复杂度：O(n)： 用于存储窗口内的字符  

