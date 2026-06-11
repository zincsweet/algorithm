[1658. 将 x 减到 0 的最小操作数](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/)

中等



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



提示



给你一个整数数组 `nums` 和一个整数 `x` 。每一次操作时，你应当移除数组 `nums` 最左边或最右边的元素，然后从 `x` 中减去该元素的值。请注意，需要 **修改** 数组以供接下来的操作使用。

如果可以将 `x` **恰好** 减到 `0` ，返回 **最小操作数** ；否则，返回 `-1` 。

 

**示例 1：**

```
输入：nums = [1,1,4,2,3], x = 5
输出：2
解释：最佳解决方案是移除后两个元素，将 x 减到 0 。
```

**示例 2：**

```
输入：nums = [5,6,7,8,9], x = 4
输出：-1
```

**示例 3：**

```
输入：nums = [3,2,20,1,1,3], x = 10
输出：5
解释：最佳解决方案是移除后三个元素和前两个元素（总共 5 次操作），将 x 减到 0 。
```

 

**提示：**

- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 104`
- `1 <= x <= 109`

```c++
class Solution {
public:
    int minOperations(vector<int>& nums, int x) {
        int dif = 0, left = 0, right = 0, sum = 0, cmp = 0; 
        int sz = nums.size();
        int ret = INT_MAX;
        for (int i = 0; i < sz; ++i) {
            sum += nums[i];
        }
        dif = sum - x;
        if (dif < 0) {
            return -1;
        }
        for (right; right < sz; ++right) {
            cmp += nums[right];
            while (cmp > dif) {
                cmp -= nums[left];
                ++left;
            }
            if (cmp == dif) {
                ret = min(ret, sz - (right - left + 1));
            }
        }
        return ret == INT_MAX ? -1 : ret;
    }
};
```

通过**问题转换 + 滑动窗口**，在 O(n) 时间内找到将 x 减到 0 的最小操作数。

### 核心原理

- **逆向思维**：每次从数组两端移除元素，相当于保留一个**连续的中间子数组**。设数组总和为 `sum`，若移除元素之和等于 `x`，则保留的子数组之和为 `sum - x`。
- **转化目标**：找**最长的连续子数组**，其和等于 `target = sum - x`。因为保留的越长，移除的越少（操作数 = 总长度 - 保留长度）。
- **可行性**：若 `target < 0` 直接返回 -1。否则，问题变为经典的“和为 target 的最长子数组”问题，可用滑动窗口（所有数为正整数）求解。

### 算法步骤

1. 计算数组总和 `sum`，得到 `target = sum - x`。若 `target < 0`，返回 -1。
2. 初始化左指针 `left = 0`，当前窗口和 `cur = 0`，最小操作数 `ret = INT_MAX`。
3. 遍历右指针 `right` 从 0 到 n-1：
	- `cur += nums[right]`。
	- 若 `cur > target`，则循环移动左指针：`cur -= nums[left++]`。
	- 若 `cur == target`，则当前窗口长度为 `right - left + 1`，对应的操作数为 `n - 窗口长度`，更新 `ret`。
4. 若 `ret` 仍为 `INT_MAX`，返回 -1；否则返回 `ret`。

### 关键细节

- **为什么转换**：直接模拟两端移除指数级复杂，而转换后利用滑动窗口 O(n) 解决。
- **滑动窗口的单调性**：因为所有数 >0，窗口和随右移单调增、随左移单调减，保证 `while` 收缩正确。
- **while 循环必要性**：一次左移可能不足以使 `cur ≤ target`（例如窗口内有多个大数），必须循环直到条件满足。
- **操作数计算**：保留子数组长度为 `len`，则移除元素个数为 `n - len`，即最少操作数。
- **边界处理**：当 `target == 0` 时，和为 0 的子数组只能是空数组（因所有数 ≥1）。你的滑动窗口会自然产生空窗口：每次右移后，`while` 循环将窗口收缩至 `left = right+1`，此时长度为 0，操作数为 `n`，正确。无需额外判断。

### 复杂度

- **时间**：O(n)，每个元素最多入窗口一次、出窗口一次。
- **空间**：O(1)。

### 正确性保证

- 原问题等价于找最长子数组和为 `sum - x`，滑动窗口因正整数单调性可找到所有和为 `target` 的子数组，记录最长长度，从而得到最小操作数。若不存在这样的子数组，则返回 -1。