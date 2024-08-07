给你两个下标从 **0** 开始的二进制字符串 `s1` 和 `s2` ，两个字符串的长度都是 `n` ，再给你一个正整数 `x` 。

你可以对字符串 `s1` 执行以下操作 **任意次** ：

- 选择两个下标 `i` 和 `j` ，将 `s1[i]` 和 `s1[j]` 都反转，操作的代价为 `x` 。
- 选择满足 `i < n - 1` 的下标 `i` ，反转 `s1[i]` 和 `s1[i + 1]` ，操作的代价为 `1` 。

请你返回使字符串 `s1` 和 `s2` 相等的 **最小** 操作代价之和，如果无法让二者相等，返回 `-1` 。

**注意** ，反转字符的意思是将 `0` 变成 `1` ，或者 `1` 变成 `0` 。

**示例 1：**

**输入：**s1 = "1100011000", s2 = "0101001010", x = 2
**输出：**4
**解释：**我们可以执行以下操作：
- 选择 i = 3 执行第二个操作。结果字符串是 s1 = "110_**11**_11000" 。
- 选择 i = 4 执行第二个操作。结果字符串是 s1 = "1101_**00**_1000" 。
- 选择 i = 0 和 j = 8 ，执行第一个操作。结果字符串是 s1 = "_**0**_1010010_**1**_0" = s2 。
总代价是 1 + 1 + 2 = 4 。这是最小代价和。

**示例 2：**

**输入：**s1 = "10110", s2 = "00011", x = 4
**输出：**-1
**解释：**无法使两个字符串相等。

**提示：**

- `n == s1.length == s2.length`
- `1 <= n, x <= 500`
- `s1` 和 `s2` 只包含字符 `'0'` 和 `'1'` 。

## 题解
**如果出现一个能影响下面的仅有 1 位，那么我们可以考虑直接转移到 $f[n-2]$ 即可。

我们把题目进行一个转换，给定你偶数个数字，每次你可以选择数字中的一对，花费一定的代价，问我们最少花费多少让我们的每一对都消去。
我们这一题首先不能用贪心，因为有样例
```
"101101"
"000000"
6
```
如果用贪心，我们的结果就是 7，而实际答案却是 4.

所以我们需要用到我们的动态规划：对于我们的位置 i，我们在每个位置上有下面几种可能的情况：
1. i 和 j 相同：我们直接跳过
2. i 和 j 不同：
	1. 我们选用我们的 1 方案，并把我们的反转次数存储起来作为我们的备选
	2. 我们用一次我们的备选操作
	3. 我们选用我们的 2 方案，然后反转我们的下一位。
于是，我们的总共也就只有上面这三种情况，我们于是考虑我们的转移：
```cpp
class Solution {
public:
    int minOperations(string s1, string s2, int x) {
        if (count(s1.begin(), s1.end(), '1') % 2 != count(s2.begin(), s2.end(), '1') % 2) {
            return -1;
        }
        int n = s1.length();
        int memo[n][n + 1][2];
        memset(memo, -1, sizeof(memo)); // -1 表示没有计算过
        function<int(int, int, bool)> dfs = [&](int i, int j, bool pre_rev) -> int {
            if (i < 0) {
                return j || pre_rev ? INT_MAX / 2 : 0;
            }
            int &res = memo[i][j][pre_rev]; // 注意这里是引用
            if (res != -1) { // 之前计算过
                return res;
            }
            if ((s1[i] == s2[i]) == !pre_rev) { // 无需反转
                return dfs(i - 1, j, false);
            }
            res = min(dfs(i - 1, j + 1, false) + x, dfs(i - 1, j, true) + 1);
            if (j) { // 可以免费反转
                res = min(res, dfs(i - 1, j - 1, false));
            }
            Return res;
        };
        return dfs(n - 1, 0, false);
    }
};
```