# JavaScript-algorithm
前端算法


# 1、最接近的三数之和
给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个整数，使它们的和与 target 最接近。
返回这三个数的和。
假定每组输入只存在恰好一个解。

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

