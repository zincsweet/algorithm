[LCR 179. 查找总价格为目标值的两个商品](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

已解答

简单

购物车内的商品价格按照升序记录于数组 `price`。请在购物车中找到两个商品的价格总和刚好是 `target`。若存在多种情况，返回任一结果即可。

**示例 1：**

```
输入：price = [3, 9, 12, 15], target = 18
输出：[3,15] 或者 [15,3]
```

**示例 2：**

```
输入：price = [8, 21, 27, 34, 52, 66], target = 61
输出：[27,34] 或者 [34,27]
```

**提示：**

- `1 <= price.length <= 10^5`
- `1 <= price[i] <= 10^6`
- `1 <= target <= 2*10^6`

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& price, int target) {
        std::vector<int> ret;
        int left = 0, right = price.size() - 1;
        while (left < right) {
            if (price[left] + price[right] > target) {
                --right;
            }
            else if (price[left] + price[right] < target) {
                ++left;
            }
            else {
                break;
            }
        }
        return ret = {price[left], price[right]};
    }
};
```

### 解题思路（双指针法）

**核心原理**：
数组 `price` 已按升序排列，利用有序性，通过双指针从两端向中间收缩，逐步排除不可能的组合，最终定位到目标值。

**算法细节**：

1. 初始化左指针 `left = 0`，右指针 `right = price.size() - 1`。
2. 当 `left < right` 时循环：
	- 计算当前两数之和 `sum = price[left] + price[right]`。
	- 若 `sum > target`：因为数组升序，右指针向左移动会使和减小，故 `right--`。
	- 若 `sum < target`：左指针向右移动会使和增大，故 `left++`。
	- 若 `sum == target`：直接跳出循环，记录结果。
3. 返回 `{price[left], price[right]}`（题目保证有解，无需额外判断）。

**复杂度**：

- 时间：O(n)，每个元素最多被访问一次。
- 空间：O(1)，仅使用常数个指针变量。

**关键点**：

- 利用**有序数组**特性，避免暴力枚举 O(n²)。
- 双指针从两端逼近，每次移动一步，确保不遗漏解。
- 移动方向由和与 `target` 的比较结果决定，单调收缩搜索区间。