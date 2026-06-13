[30. 串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/)

困难



相关标签

![premium lock icon](https://static.leetcode.cn/cn-frontendx-assets/production/_next/static/images/lock-a6627e2c7fa0ce8bc117c109fb4e567d.svg)相关企业



给定一个字符串 `s` 和一个字符串数组 `words`**。** `words` 中所有字符串 **长度相同**。

 `s` 中的 **串联子串** 是指一个包含 `words` 中所有字符串以任意顺序排列连接起来的子串。

- 例如，如果 `words = ["ab","cd","ef"]`， 那么 `"abcdef"`， `"abefcd"`，`"cdabef"`， `"cdefab"`，`"efabcd"`， 和 `"efcdab"` 都是串联子串。 `"acdbef"` 不是串联子串，因为他不是任何 `words` 排列的连接。

返回所有串联子串在 `s` 中的开始索引。你可以以 **任意顺序** 返回答案。

 

**示例 1：**

```
输入：s = "barfoothefoobarman", words = ["foo","bar"]
输出：[0,9]
解释：因为 words.length == 2 同时 words[i].length == 3，连接的子字符串的长度必须为 6。
子串 "barfoo" 开始位置是 0。它是 words 中以 ["bar","foo"] 顺序排列的连接。
子串 "foobar" 开始位置是 9。它是 words 中以 ["foo","bar"] 顺序排列的连接。
输出顺序无关紧要。返回 [9,0] 也是可以的。
```

**示例 2：**

```
输入：s = "wordgoodgoodgoodbestword", words = ["word","good","best","word"]
输出：[]
解释：因为 words.length == 4 并且 words[i].length == 4，所以串联子串的长度必须为 16。
s 中没有子串长度为 16 并且等于 words 的任何顺序排列的连接。
所以我们返回一个空数组。
```

**示例 3：**

```
输入：s = "barfoofoobarthefoobarman", words = ["bar","foo","the"]
输出：[6,9,12]
解释：因为 words.length == 3 并且 words[i].length == 3，所以串联子串的长度必须为 9。
子串 "foobarthe" 开始位置是 6。它是 words 中以 ["foo","bar","the"] 顺序排列的连接。
子串 "barthefoo" 开始位置是 9。它是 words 中以 ["bar","the","foo"] 顺序排列的连接。
子串 "thefoobar" 开始位置是 12。它是 words 中以 ["the","foo","bar"] 顺序排列的连接。
```

 

**提示：**

- `1 <= s.length <= 104`
- `1 <= words.length <= 5000`
- `1 <= words[i].length <= 30`
- `words[i]` 和 `s` 由小写英文字母组成

```c++
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        std::vector<int> ret;
        std::unordered_map<std::string, int> hash2;
        int word_len = words[0].size(), s_sz = s.size(), all_len = words.size() * word_len;
        for (auto& s : words) {
            ++hash2[s];
        }
        for (int start = 0; start < word_len; ++start) {
            std::unordered_map<std::string, int> hash1;
            for (int right = start, left = start, count = 0; right + word_len <= s_sz; right += word_len) {
                std::string tmp_r = s.substr(right, word_len);
                if (hash2.count(tmp_r) && ++hash1[tmp_r] <= hash2[tmp_r]) ++count;
                if (right - left + word_len > all_len) {
                    std::string tmp_l = s.substr(left, word_len);
                    if (hash2.count(tmp_l) && hash1[tmp_l]-- <= hash2[tmp_l]) --count;
                    left += word_len;
                }
                if (count == words.size()) {
                    ret.emplace_back(left);
                }
            }
        }
        return ret;
    }
};
```

## 解题思路

本题要求找出字符串 `s` 中所有由给定单词数组 `words` 中所有单词以任意顺序连接而成的子串的起始索引。已知 `words` 中每个单词长度相同，设为 `word_len`，单词个数为 `m`，则目标子串固定长度为 `total_len = m * word_len`。

### 核心原理

- **滑动窗口 + 哈希计数**：维护一个大小固定的窗口（宽度为 `total_len`），在 `s` 上按单词长度 `word_len` 滑动，利用哈希表统计窗口内各单词出现次数，并与 `words` 的统计表比较。
- **多轮起始偏移**：由于单词边界可能不对齐，需从 `0` 到 `word_len-1` 分别作为窗口的起始偏移量，进行 `word_len` 轮独立的滑动窗口扫描，确保不遗漏任何可能的串联子串。

### 算法细节

1. **预处理**：
	- 用哈希表 `hash2` 统计 `words` 中每个单词的出现次数。
	- 设 `s_sz = s.length()`。
2. **多轮扫描**：
	对于每个起始偏移 `start`（`0 ≤ start < word_len`）：
	- 初始化哈希表 `hash1`（当前窗口内单词计数）、计数器 `count = 0`（已完全匹配的单词个数）。
	- 双指针 `left = start`，`right = start`，步长均为 `word_len` 向右移动。
3. **滑动窗口操作**：
	- **扩展右边界**：取 `tmp_r = s.substr(right, word_len)`，将其加入 `hash1`。
		若 `++hash1[tmp_r] <= hash2[tmp_r]`，说明该单词加入后仍未超出目标需求，则 `count++`。
	- **收缩左边界**：若当前窗口长度（`right - left + word_len`）超过 `total_len`，则需移除左边界的单词。
		取 `tmp_l = s.substr(left, word_len)`，若 `hash1[tmp_l]-- <= hash2[tmp_l]`，说明移除前该单词计数未超过目标，移除后匹配数减少，则 `count--`，并移动 `left += word_len`。
	- **检查匹配**：当 `count == m` 时，表示当前窗口内所有单词均恰好出现一次，将 `left` 加入答案数组。
4. **返回结果**：所有记录的起始索引。

### 复杂度分析

- **时间复杂度**：O(`word_len * (s_sz / word_len)`) ≈ O(s_sz)，实际上每个字符被访问常数次（每轮扫描中每个单词边界处理一次），总体与 `s` 长度线性相关。
- **空间复杂度**：O(m + word_len)，主要存放单词计数的哈希表。