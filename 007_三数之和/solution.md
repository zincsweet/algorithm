[15. 三数之和](https://leetcode.cn/problems/3sum/)

中等

提示

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

**示例 1：**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

**示例 2：**

```
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

**示例 3：**

```
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

 

**提示：**

- `3 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ret;
        std::sort(nums.begin(), nums.end());
        int i = 0, sz = nums.size();
        for (i; i < sz - 2;) {
            if (nums[i] > 0) break;
            int target = -nums[i], j = i + 1, k = sz - 1;
            while (j < k) {
                if (target < nums[j] + nums[k]) {
                    --k;
                }
                else if (target > nums[j] + nums[k]) {
                    ++j;
                }
                else {
                    ret.push_back({nums[i], nums[j], nums[k]});
                    --k;
                    ++j;
                    while (j < k && nums[j] == nums[j - 1]) ++j;
                    while (j < k && nums[k] == nums[k + 1]) --k;
                }
            }
            ++i;
            while (i < sz - 2 && nums[i] == nums[i - 1]) ++i;
        }
        return ret;
    }
};
```

## 解题思路（排序 + 双指针）

### 核心原理

要在无序数组中找到所有不重复的 `(a, b, c)` 使 `a + b + c = 0`，等价于 `b + c = -a`。
先排序，然后固定一个元素 `nums[i]` 作为 `a`，在剩余的有序区间 `[i+1, n-1]` 中使用双指针寻找两数之和等于 `-a`。

### 算法步骤

1. **排序**
	将 `nums` 升序排序，这是双指针收缩和去重的基础。
2. **枚举第一个元素**
	- 遍历 `i` 从 `0` 到 `n-3`。
	- **剪枝**：若 `nums[i] > 0`，则后续三数和必 `> 0`，直接 `break`。
	- **去重**：若 `i > 0` 且 `nums[i] == nums[i-1]`，跳过该 `i`（避免相同三元组）。
3. **双指针查找**
	设 `target = -nums[i]`，左指针 `j = i+1`，右指针 `k = n-1`。
	- 若 `nums[j] + nums[k] < target`，需要增大和 → `j++`。
	- 若 `nums[j] + nums[k] > target`，需要减小和 → `k--`。
	- 若相等，记录三元组 `{nums[i], nums[j], nums[k]}`，然后：
		- 同时移动 `j++`, `k--`。
		- **跳过重复**：移动后若 `nums[j] == nums[j-1]`，继续 `j++`；若 `nums[k] == nums[k+1]`，继续 `k--`。
4. **继续循环**
	处理完 `i` 后，移动 `i` 并再次跳过重复。

### 细节与重点

- **双指针正确性**：排序后区间内指针向中间收缩，单调判断和与目标的关系，保证 O(n) 查找所有解。
- **去重策略**：
	- 对 `i`：只处理每个值的第一次出现。
	- 对 `j/k`：找到一组解后，跳过与当前 `nums[j]` / `nums[k]` 相等的后续元素，避免重复三元组。
- **早期终止**：`nums[i] > 0` 直接退出，因为最小的数已大于 0，后续和不可能为 0。
- **边界条件**：`i < n-2` 保证至少有三个元素；`j < k` 保证双指针有效。

### 复杂度分析

- **时间**：O(n²)
	- 排序 O(n log n)
	- 外层循环 O(n)，内层双指针 O(n)，总 O(n²)
- **空间**：O(1)（不计输出数组），排序使用 O(log n) 栈空间（原地排序）。