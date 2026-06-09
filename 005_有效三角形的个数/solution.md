[611. 有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/)

中等

给定一个包含非负整数的数组 `nums` ，返回其中可以组成三角形三条边的三元组个数。

**示例 1:**

```
输入: nums = [2,2,3,4]
输出: 3
解释:有效的组合是: 
2,3,4 (使用第一个 2)
2,3,4 (使用第二个 2)
2,2,3
```

**示例 2:**

```
输入: nums = [4,2,3,4]
输出: 4
```

**提示:**

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 1000`

```c++
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        std::sort(nums.begin(), nums.end());
        int ret = 0, sz = nums.size();
        for (int i = sz - 1; i >= 2; --i) {
            int left = 0, right = i - 1;
            while (left < right) {
                if (nums[left] + nums[right] > nums[i]) {
                    ret += right - left;
                    --right;
                }
                else {
                    ++left;
                }
            }
        }
        return ret;
    }
};
```

## 解题原理

**三角形判定**：三个数 `a ≤ b ≤ c` 能构成三角形 ⇔ `a + b > c`。
当 `c` 固定为最大值时，只需在剩余数中找两数之和大于 `c`。

### 算法步骤

1. **排序** `nums`，得到非递减序列。
2. **倒序固定最大边** `nums[i]`（`i` 从 `n-1` 向下到 2）。
3. 在 `[0, i-1]` 范围内用双指针 `left, right` 寻找满足 `nums[left] + nums[right] > nums[i]` 的 `(left, right)` 对。

### 双指针的正确性（关键原理）

- 初始时 `left = 0, right = i-1`，区间内元素有序。
- **情况1**：`nums[left] + nums[right] > nums[i]`
	由于 `nums` 升序，对任意 `k ∈ [left, right-1]`，`nums[k] + nums[right] ≥ nums[left] + nums[right] > nums[i]`。
	因此以 `right` 为较大边、`left`…`right-1` 为较小边的所有组合均有效，共 `(right - left)` 对。
	此时将 `right` 左移，尝试更小的较大边，继续计数。
- **情况2**：`nums[left] + nums[right] ≤ nums[i]`
	和不够大，需要增大较小边，故 `left` 右移。

**单调性保证**：`left` 和 `right` 只单向移动，每个 `(left, right)` 组合最多被检查一次，时间复杂度 O(n²)。

### 为什么不会漏解？

- 固定 `i` 后，所有有效边对必然满足 `left < right < i`。双指针遍历覆盖了所有可能的 `(left, right)` 对：
	当发现当前 `(left, right)` 有效时，直接累加所有以 `right` 为较大边的组合，无需再枚举 `left` 更大的情况（它们已被右移过程覆盖）；
	当无效时，增大 `left` 直到有效或 `left ≥ right`。
	过程等价于在每个 `right` 位置找到最小的 `left` 使得和大于 `nums[i]`，然后累加 `right - left`。

### 边界与优化

- 元素可为 0，但 0 无法参与构成三角形（最小两边和至少为 0+正数，无法大于最大边），排序后自动置于左端，不影响算法正确性。
- 排序后若 `nums[i] == 0` 可直接跳过，因为不可能有更小的两边和大于 0。

**总结**：排序 + 固定最长边 + 双指针利用单调性批量计数，将三重循环降为 O(n²)。