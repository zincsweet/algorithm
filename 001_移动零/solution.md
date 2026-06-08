```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int dest = -1;
        int cur = 0;
        for (cur; cur < nums.size(); ++cur) {
            if (nums[cur] != 0) {
                ++dest;
                std::swap(nums[dest], nums[cur]);
            }
        }
    }
};
```

解题思路可以精炼为以下步骤：

1. **初始化双指针**：`dest` 指向已处理非零区域的最后一个位置（初始为 -1），`cur` 用于遍历数组（初始为 0）。
2. **遍历数组**：对每个 `cur` 位置的元素进行判断：
	- 如果当前元素不为零，则 `dest` 前进一步，并交换 `nums[dest]` 与 `nums[cur]`。
	- 如果当前元素为零，则不做交换，继续前进。
3. **结果**：遍历结束后，所有非零元素已按原顺序移动到数组前部，零则被交换到尾部。

核心思想：通过一次遍历，将非零元素逐步“挤”到前面，零自动沉底，且不改变非零元素的相对顺序。