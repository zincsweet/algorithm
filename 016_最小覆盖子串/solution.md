[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

困难



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



提示



给定两个字符串 `s` 和 `t`，长度分别是 `m` 和 `n`，返回 s 中的 **最短窗口 子串**，使得该子串包含 `t` 中的每一个字符（**包括重复字符**）。如果没有这样的子串，返回空字符串 `""`。

测试用例保证答案唯一。

 

**示例 1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

**示例 2：**

```
输入：s = "a", t = "a"
输出："a"
解释：整个字符串 s 是最小覆盖子串。
```

**示例 3:**

```
输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。
```

 

**提示：**

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 105`
- `s` 和 `t` 由英文字母组成

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        std::string ret;
        int hash1[128], hash2[128];
        for (auto& ch : t) {
            ++hash2[ch];
        }
        int min_len = INT_MAX, start = -1;
        int s_sz = s.size(), t_sz = t.size(), count = 0;
        for (int right = 0, left = 0; right < s_sz; ++right) {
            char in = s[right];
            if (++hash1[in] <= hash2[in]) ++count;
            while (count == t_sz) {
                if (right - left + 1 < min_len) {
                    min_len = right - left + 1;
                    start = left;
                }
                char out = s[left];
                if (hash1[out]-- <= hash2[out]) --count;
                ++left;
            }
        }
        if (start != -1) {
            ret = s.substr(start, min_len);
        }
        return ret;
    }
};
```

## 解题思路

本题要求在字符串 `s` 中找到包含字符串 `t` 中所有字符（包括重复次数）的最短连续子串，返回该子串本身。若不存在则返回空串。

### 核心原理

- **滑动窗口 + 字符计数**：使用两个哈希表（或数组）分别统计窗口内字符的出现次数和目标字符串 `t` 中每个字符的需求次数。通过双指针维护一个动态窗口，不断尝试收缩左边界以找到最小长度。
- **匹配计数优化**：不直接比较两个哈希表，而是维护一个计数器 `count`，记录当前窗口中已经“满足需求”的字符种类数量（注意：是种类，不是个数，但实现技巧上也可以记录满足次数的字符总数）。当某个字符在窗口内的出现次数首次达到需求次数时，`count` 增加；当移出字符后导致其出现次数小于需求次数时，`count` 减少。

### 算法细节

1. **预处理**：
	- 用数组 `hash2[128]` 统计 `t` 中每个字符的需求次数。
	- 初始化窗口左右指针 `left = 0`，`right = 0`，计数器 `count = 0`，最小长度 `min_len = INT_MAX`，结果起始位置 `start = -1`。
2. **扩展右边界**：
	- 每次移动 `right`，取当前字符 `in = s[right]`。
	- 将其在 `hash1` 中的计数加1。
	- 如果加1后 `hash1[in] <= hash2[in]`，说明该字符的当前出现次数仍未超过需求（或者说，这次增加刚好使该字符从不足变为满足），则 `count++`。
3. **尝试收缩左边界**：
	- 当 `count == t.length()` 时（注意：这里 `t.length()` 是字符总个数吗？实际上代码中 `count` 每次最多加1，当 `count` 等于 `t_sz` 时表示每个所需字符的个数都至少达到了需求，因为每个字符每一次从不足到满足只会使 `count` 增加1，且总需求字符数为 `t_sz`，所以 `count == t_sz` 等价于窗口已完全覆盖 `t`）。
	- 更新最小窗口：若当前窗口长度 `right - left + 1` 小于 `min_len`，则记录 `min_len` 和起始位置 `start = left`。
	- 准备移动左指针：取出左边界字符 `out = s[left]`。
	- 如果 `hash1[out] <= hash2[out]`（即移除前该字符刚好满足需求，移除后将变得不足），则 `count--`。
	- 将 `hash1[out]` 减1，然后 `left++`。
	- 重复此过程直到 `count < t_sz`。
4. **返回结果**：若 `start != -1` 则返回 `s.substr(start, min_len)`，否则返回空串。

### 复杂度分析

- **时间复杂度**：O(|s| + |t|)，每个字符最多被左右指针各访问一次。
- **空间复杂度**：O(1)，因为哈希表大小固定为128（ASCII字符集）。