[704. 二分查找](https://leetcode.cn/problems/binary-search/)

简单



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target` ，写一个函数搜索 `nums` 中的 `target`，如果 `target` 存在返回下标，否则返回 `-1`。

你必须编写一个具有 `O(log n)` 时间复杂度的算法。


**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

 

**提示：**

1. 你可以假设 `nums` 中的所有元素是不重复的。
2. `n` 将在 `[1, 10000]`之间。
3. `nums` 的每个元素都将在 `[-9999, 9999]`之间。

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1, ret = -1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target) {
                right = mid - 1;
            }
            else if (nums[mid] < target) {
                left = mid + 1;
            }
            else {
                ret = mid;
                break;
            }
        }
        return ret;
    }
};
```

## 解题思路

本题要求在有序（升序）数组中查找目标值 `target` 的下标，要求时间复杂度 O(log n)。由于数组有序且元素不重复，可以利用二分查找算法不断缩小搜索区间。

### 核心原理

- **二分查找**：每次取区间中间元素与目标值比较，根据比较结果将搜索范围缩小为左半部分或右半部分，直到找到目标或区间为空。

### 算法细节

1. **初始化边界**：`left = 0`，`right = nums.size() - 1`。使用闭区间 `[left, right]` 表示当前待搜索范围。
2. **循环条件**：`while (left <= right)`，当区间非空时继续搜索。若 `left > right` 则说明目标不存在。
3. **计算中点**：`mid = (left + right) / 2`。注意避免溢出（本题数值范围小，无影响）。
4. **比较与缩小区间**：
	- 若 `nums[mid] > target`，则目标在左半部分，更新 `right = mid - 1`。
	- 若 `nums[mid] < target`，则目标在右半部分，更新 `left = mid + 1`。
	- 若相等，记录 `ret = mid` 并跳出循环。
5. **返回结果**：若找到则返回下标，否则返回 `-1`。

### 复杂度分析

- **时间复杂度**：O(log n)，每轮循环将搜索区间减半。
- **空间复杂度**：O(1)，仅使用常数个变量。