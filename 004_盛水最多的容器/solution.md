[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

中等

提示

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

 

**示例 1：**

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

**示例 2：**

```
输入：height = [1,1]
输出：1
```

**提示：**

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1, v = 0;
        while (l < r) {
            if (height[l] < height[r]) {
                v = max(v, height[l] * (r - l));
                ++l;
            }
            else {
                v = max(v, height[r] * (r - l));
                --r;
            }
        }
        return v;
    }
};
```

解题思路基于**双指针贪心策略**，核心要点如下：

- **初始化**：左指针 `l` 指向数组起点，右指针 `r` 指向终点，最大水量 `v` 初始为 0。
- **循环条件**：当 `l < r` 时，持续计算当前容器面积 `min(height[l], height[r]) * (r - l)`，并更新 `v`。
- **指针移动规则**：
	- 若 `height[l] < height[r]`，则移动左指针 `++l`（舍弃较短的左边线）；
	- 否则移动右指针 `--r`（舍弃较短的右边线）。
- **正确性依据（单调性）**：每次移动高度较小的指针，是因为宽度一定减小，只有提升短板高度才有机会获得更大面积，而移动长板只会让宽度变小且高度不变或更小，不可能得到更优解。该策略保证不遗漏最优解。
- **复杂度**：时间 O(n)，空间 O(1)，满足题目 `n ≤ 10^5` 的约束。