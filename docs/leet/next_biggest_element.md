# 556. 下一个更大元素 III

给你一个正整数 `n` ，请你找出符合条件的最小整数，其由重新排列 `n` 中存在的每位数字组成，并且其值大于 `n` 。如果不存在这样的正整数，则返回 `-1` 。

注意 ，返回的整数应当是一个 **32 位整数** ，如果存在满足题意的答案，但不是 **32 位整数** ，同样返回 `-1` 。

source: <https://leetcode.cn/problems/next-greater-element-iii>

## 解析

首先题目要求找出一个数字，它大于原来的数字，是最小的，且是原来数字每一位的一个排序。

那么会考虑到 `std::to_string()` 将数字转换成字符串处理。不过这个需要一定的额外空间存储。

接下来做如下考虑：

1. 做的变化尽量保持在最右侧
2. 做的变化需要让数字变化尽可能小

那么，接下来考虑一个长为 `n` 的字符串。

```
[0] [1] [2] [3] [4] [5] ... [i] [i+1] [i+2] ... [n-1]
```

如果对于任意的 `0 <= i < j < n` 均有 `[i] >= [j]` 成立，那么该数字应该是当前状态下的最大值。也就是一组数字按照从大到小排序组成的数字比其他排序大。

这个可以通过[排序不等式](https://brilliant.org/wiki/rearrangement-inequality/#:~:text=The%20rearrangement%20inequality%20is%20a%20statement%20about%20the,illustrates%20the%20practical%20power%20of%20greedy%20algorithms.%20Contents)简单证明出来。

这就是其中一个没有满足题意的答案的情况，具有启发性的发现如果考虑排序的逆序数，就会发现该排序的逆序数是 `n(n-1)/2`。

有了以上内容，不难想到最小数字是从小到大排序，逆序数为 `0` 的排序。

可是排序一共有 `n!` 个，这必然会导致逆序数是有重复的，所以按照逆序数找答案会出现问题。

那么还是考虑一下下一个数字吧。

如果逆序数为 `0` 的情况下，找下一个数字，尽量修改最少的数字进行尝试。

例如交换任意 `i` 和 `j` 看看变化。

```
[0] [1] [2] [3] [4] [5] ... [i] = a ... [j] = b ... [n-1]
->
[0] [1] [2] [3] [4] [5] ... [i] = b ... [j] = a ... [n-1]
```

那么变化值大约为：

$$
(b - a) \times 10^{n-1-i} + (a - b) \times 10^{n-1-j}
$$

如果记 `b = a + k`，且 `j = i + d`。那么可以化为。

$$
k \times 10^{n-1-i} - k \times 10^{n-1-i-d}
$$

如果期望这个变化越小，那么就需要 `d` 足够小，而 `k` 可以再做一点尝试。

如果交换 `i` 和 `r`，其中 `i < r < j`。那么令 `[r] = c`，`c = a + k + m`，`r = i + d + l`。

$$
(m + k) \times 10^{n-1-i} - (m + k) \times 10^{n-1-i-d-l}
$$

做差，下面的减去上面：

$$
\begin{align*}
  & \space m\times 10^{n-1-i} - (m + k) \times 10^{n-1-i-d-l} + k \times 10^{n-1-i-d} \\
> & \space m\times 10^{n-1-i} - (m + k) \times 10^{n-1-i-d-l} + k \times 10^{n-1-i-d-l} \\
= & \space m\times 10^{n-1-i} - m \times 10^{n-1-i-d-l} \\
> & \space 0
\end{align*}
$$

可以看出如果之前一个考虑是对的，也即 `做的变化尽量保持在最右侧`。

在理解 n 进制数字之后，上面所做的计算则只是显而易见的内容了。

推广以上结论就是，得到最右侧的一组 `i` 和 `j`，使得 `[i] < [j]` 然后交换二者的值。

这样子变大的数值是“最小的”。

但是，这还没做到真正意义上的最小。还可以找到比它更小的数字。考虑之前所说的逆序数为 `0` 的情况，以及 n 进制数的特点。

将 `[i]` 右侧的数字从小到大进行排序，这样子，`[i]` 的右侧也就是最小的了。这个时候，该排序就满足了比上一个排序大，但是增长最小。

以上逻辑总结如下 `C++` 代码：

``` cpp
class Solution {
public:
    int nextGreaterElement(int n) {
        std::string s = std::to_string(n);
        int lptr = s.size() - 2;
        int rptr = s.size() - 1;
        while (lptr > -1) {
            while (rptr > lptr) {
                if (s[lptr] < s[rptr]) {
                    goto EXIT;
                }
                rptr--;
            }
            lptr--;
            rptr = s.size() - 1;
        }
        if (lptr == -1) {
            return -1;
        }
EXIT:
        long long x;
        std::swap(s[lptr], s[rptr]);
        std::sort(s.begin()+lptr, s.end());
        x = std::stoll(s);
        if (x > INT32_MAX) {
            return -1;
        }
        return x;
    }
};
```

## 总结

该问题实际上是 [下一个排列](https://leetcode.cn/problems/next-permutation/) 问题。

使用 `C++` 的 `STL` 可以直接得出结果：

``` cpp
class Solution {
public:
    int nextGreaterElement(int n) {
        auto&& s = to_string(n);
        return !next_permutation(s.begin(), s.end()) || stol(s) > INT_MAX ? -1 : stoi(s);
    }
};
```
