[209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

已解答

中等

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其总和大于等于 `target` 的长度最小的 **子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

 

**示例 1：**

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

**示例 2：**

```
输入：target = 4, nums = [1,4,4]
输出：1
```

**示例 3：**

```
输入：target = 11, nums = [1,1,1,1,1,1,1,1]
输出：0
```

 

**提示：**

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 104`

 

```c++
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int len = INT_MAX, sz = nums.size();
        int left = 0, right = 0,sum = 0;
        for (right; right < sz; ++right) {
            sum += nums[right];
            while (sum >= target) {
                len = std::min(len, right - left + 1);
                sum -= nums[left++];
            }
        }
        return len == INT_MAX ? 0 : len;
    }
};
```

### 核心原理

- 窗口由左指针 `left` 和右指针 `right` 界定，初始都指向 0。
- 右指针不断右移扩大窗口，累加元素和 `sum`。
- 一旦 `sum ≥ target`，说明当前窗口可行，尝试收缩左边界（减小窗口长度），同时更新最短长度。
- 由于数组元素均为**正整数**，窗口和具有单调性：扩大窗口和增加，缩小窗口和减少，保证滑动窗口正确性。

### 算法步骤

1. 初始化 `left = 0`, `sum = 0`, `len = INT_MAX`。
2. `for` 循环枚举右指针 `right` 从 0 到 `n-1`：
	- `sum += nums[right]` 将当前元素加入窗口。
	- 用 `while (sum >= target)` 循环收缩左边界：
		- 更新 `len = min(len, right - left + 1)`。
		- `sum -= nums[left]`，然后 `left++`。
3. 若 `len` 仍为 `INT_MAX`，返回 0；否则返回 `len`。

### 关键细节

- **while 收缩**：必须用循环而非单次 `if`，因为左指针可能连续移动多次才能让 `sum` 降到 `target` 以下。例如 `target=7, nums=[2,3,1,2,4,3]`，当 `right=4` 时窗口 `[2,3,1,2,4]` 和为 12，需要连续左移直到和为 6（窗口变为 `[4]`），才能找到真正的局部最小窗口。
- **更新时机的顺序**：先更新长度，再减去左指针值并移动——保证更新使用的窗口正是满足条件的最小窗口（对当前右边界而言）。
- **左指针移动**：只移一位，不跨位，因为每次 `while` 迭代都可能产生一个新的可行窗口。
- **边界处理**：若所有元素和仍小于 `target`，则 `len` 保持 `INT_MAX`，最终返回 0。

### 复杂度

- **时间**：O(n)，每个元素最多入窗口一次、出窗口一次。
- **空间**：O(1)，仅使用常数个变量。

### 正确性保证

- 对于每个右边界，我们找到以该右边界为结尾的、和 ≥ target 的最短窗口（通过尽可能收缩左边界）。
- 遍历所有右边界后，全局最短窗口一定被记录过，因此答案正确。





**进阶：**

- 如果你已经实现 `O(n)` 时间复杂度的解法, 请尝试设计一个 `O(n log(n))` 时间复杂度的解法。