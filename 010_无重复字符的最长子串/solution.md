[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

中等



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



提示



给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长 子串** 的长度。

 

**示例 1:**

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。注意 "bca" 和 "cab" 也是正确答案。
```

**示例 2:**

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3:**

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

 

**提示：**

- `0 <= s.length <= 5 * 104`
- `s` 由英文字母、数字、符号和空格组成

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int hash[128] = {0};
        int sz = s.size();
        int len = 0;
        for (int right = 0, left = 0;right < sz; ++right) {
            hash[s[right]]++;
            while (hash[s[right]] > 1) {
                --hash[s[left]];
                ++left;
            }
            len = std::max(len, right - left + 1);
        }
        return len;
    }
};
```



### 核心原理

- 窗口由左指针 `left` 和右指针 `right` 界定，初始都指向 0。
- 右指针不断右移，将新字符加入窗口，并用哈希数组 `hash` 记录每个字符在当前窗口中的出现次数。
- 当加入的字符导致其计数 >1 时，说明窗口内出现重复。此时需要右移 `left` 并减少对应字符的计数，直到该字符计数重新 ≤1。此步骤保证窗口内始终无重复字符。
- 每次调整后，窗口内都是无重复子串，更新最大长度 `len = max(len, right - left + 1)`。

### 算法步骤

1. 初始化 `hash[128] = {0}`（ASCII 字符集足够覆盖题目输入），`left = 0`，`len = 0`。
2. 遍历 `right` 从 0 到 `s.size()-1`：
	- `hash[s[right]]++` 添加当前字符。
	- 若 `hash[s[right]] > 1`，则循环移动 `left`：
		- `hash[s[left]]--`（移除左端字符）
		- `left++`
			直到 `hash[s[right]] == 1`。
	- 此时窗口 `[left, right]` 内无重复字符，更新 `len = max(len, right - left + 1)`。
3. 返回 `len`。

### 关键细节

- **为什么用 `while` 而不是 `if`**：因为重复的字符可能不是左边界那个，需要持续移动 `left` 直到去掉导致重复的那个字符。例如 `s = "abca"`，当 `right=3` 加入 `'a'` 时，`hash['a']=2`，需移动 `left` 经过 `'a'`（索引0）才能让 `'a'` 计数变回1。
- **计数数组大小**：128 足够覆盖所有 ASCII 字符（英文字母、数字、符号、空格），无需使用 `unordered_map` 或 `vector<int>(256)`。
- **更新时机的顺序**：先收缩窗口（`while` 循环），再计算长度，保证每次记录的长度都是当前右边界下的最长无重复子串。
- **复杂度**：时间 O(n)（每个字符最多入窗口一次、出窗口一次），空间 O(1)（固定数组大小）。

### 正确性保证

- 滑动窗口维护了以每个 `right` 为结尾的最长无重复子串（通过尽可能收缩左边界），遍历所有 `right` 后，全局最长子串必然被记录。