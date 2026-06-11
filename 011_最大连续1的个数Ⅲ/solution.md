[1004. 最大连续1的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/)

中等



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



提示



给定一个二进制数组 `nums` 和一个整数 `k`，假设最多可以翻转 `k` 个 `0` ，则返回执行操作后 *数组中连续 `1` 的最大个数* 。

 

**示例 1：**

```
输入：nums = [1,1,1,0,0,0,1,1,1,1,0], K = 2
输出：6
解释：[1,1,1,0,0,1,1,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 6。
```

**示例 2：**

```
输入：nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
输出：10
解释：[0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 10。
```

 

**提示：**

- `1 <= nums.length <= 105`
- `nums[i]` 不是 `0` 就是 `1`
- `0 <= k <= nums.length`

```c++
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int len = 0, left = 0, right = 0, cnt = 0;
        int sz = nums.size();
        for (right; right < sz; ++right) {
            if (nums[right] == 0) {
                ++cnt;
            }
            while (cnt > k) {
                if (nums[left] == 0) --cnt;
                ++left;
            }
            len = max(len, right - left + 1);
        }
        return len;
    }
};
```

采用**滑动窗口**，在 O(n) 时间内找到最多翻转 k 个 0 后最长的连续 1 子数组长度。

### 核心原理

- 问题等价于：寻找一个最长的子数组，其中 **0 的个数 ≤ k**（因为可以把这些 0 翻转为 1）。
- 维护一个窗口 `[left, right]`，用变量 `cnt` 统计窗口内 0 的数量。
- 右指针 `right` 每次右移，遇到 0 则 `cnt++`。
- 当 `cnt > k` 时，需要收缩左边界：左指针右移，若移出的是 0 则 `cnt--`，直到窗口内 0 的个数 ≤ k。
- 此时窗口内所有 0 都可以被翻转，窗口长度即为一个可行解，更新答案 `len = max(len, right - left + 1)`。

### 算法步骤

1. 初始化 `left = 0`, `cnt = 0`, `len = 0`。
2. 遍历 `right` 从 0 到 `n-1`：
	- 若 `nums[right] == 0`，则 `cnt++`。
	- 若 `cnt > k`，则循环移动 `left`：
		- 若 `nums[left] == 0`，则 `cnt--`。
		- `left++`。
	- 此时窗口合法，更新 `len = max(len, right - left + 1)`。
3. 返回 `len`。

### 关键细节

- **为什么用 `while` 而不是 `if`**：因为一次移动左指针可能不足以让 `cnt ≤ k`（例如窗口内连续多个 0 时），需要循环直到条件满足。
- **计数与翻转的对应**：我们不需要真正修改数组，只统计 0 的个数即可。翻转操作是虚拟的，等价于允许窗口内最多 k 个 0。
- **更新时机**：先收缩窗口保证合法，再计算长度，确保 `len` 总是基于合法窗口。
- **复杂度**：每个元素最多入窗口和出窗口各一次，时间 O(n)，空间 O(1)。

### 正确性保证

- 滑动窗口维护了以每个 `right` 为结尾的最长合法子数组（0 的个数 ≤ k）。遍历所有右端点后，全局最大值一定被记录。