[438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/)

中等



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

 

**示例 1:**

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

 **示例 2:**

```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

 

**提示:**

- `1 <= s.length, p.length <= 3 * 104`
- `s` 和 `p` 仅包含小写字母

```c++
class Solution {
public:

    bool check(int hash1[], int hash2[]) {
        for (int i = 0; i < 26; ++i) {
            if (hash1[i] != hash2[i]) {
                return false;
            }
        }
        return true;
    }

    vector<int> findAnagrams(string s, string p) {
        std::vector<int> ret;
        int s_sz = s.size(), p_sz = p.size();
        int hash1[26];
        int hash2[26];
        for (auto& ch : p) {
            ++hash2[ch - 'a'];
        }
        int right = 0, left = 0;
        for (right; right < s_sz; ++right) {
            ++hash1[s[right] - 'a'];
            if ((right - left + 1) > p_sz) {
                --hash1[s[left] - 'a'];
                ++left;
            }
            if (check(hash1, hash2)) {
                ret.emplace_back(left);
            }
        }
        return ret;
    }
};
```

解法采用**固定长度滑动窗口 + 哈希计数**，在 O(n) 时间内找到 s 中所有与 p 互为字母异位词（字母种类和数量相同）的子串起始索引。

### 核心原理

- **异位词**：两个字符串包含相同字符且每个字符出现次数相同，与顺序无关。
- **固定窗口**：由于 p 的长度固定，只需在 s 上维护一个长度等于 `p.size()` 的滑动窗口，并比较窗口内字符计数与 p 的字符计数是否相等。
- **哈希计数**：用两个大小为 26 的数组 `hash1`（窗口内）、`hash2`（p 内）统计字母出现次数，比较即可。

### 算法步骤

1. 若 `s.size() < p.size()`，直接返回空（代码未显式处理，但窗口长度不会超过 s，逻辑自然无匹配）。
2. 初始化 `hash1[26] = {0}`，`hash2[26] = {0}`，遍历 p 填充 `hash2`。
3. 使用左右指针 `left`、`right`，初始均为 0。
4. 遍历 `right` 从 0 到 `s.size()-1`：
	- 将当前字符 `s[right]` 加入窗口：`hash1[s[right]-'a']++`。
	- 若窗口长度（`right - left + 1`）大于 `p.size()`，则移除左端字符：`hash1[s[left]-'a']--`，并 `left++`。
	- 此时窗口长度等于 `p.size()`，调用 `check(hash1, hash2)` 比较两个计数数组。
	- 若相等，则记录起始索引 `left`。
5. 返回结果列表。

### 关键细节

- **固定窗口维护**：通过 `if (right - left + 1 > p_sz)` 保证窗口长度始终不超过 p 的长度，一旦超长就收缩左边界。这是固定长度滑动窗口的标准写法。
- **比较方式**：逐个比较 26 个字母计数，时间复杂度 O(26)，视为常数，因此总复杂度 O(26*n) ≈ O(n)。
- **check 函数**：每次窗口滑动后调用，由于窗口大小固定，也可以优化为仅比较变动字符的差异（如使用 diff 变量），但本题 n ≤ 3e4，26 次比较开销可接受。

### 复杂度

- **时间**：O(n * 26) = O(n)，n 为 s 的长度。每个字符入窗口一次、出窗口一次，每次比较固定 26 次。
- **空间**：O(1)，两个固定大小的数组。

### 正确性保证

- 滑动窗口遍历了 s 中所有长度为 p.size() 的连续子串，并通过哈希计数精确比较字符组成，不会遗漏或错误。