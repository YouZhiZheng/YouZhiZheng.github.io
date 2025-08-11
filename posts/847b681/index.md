# 560. 和为 K 的子数组


## [题目](https://leetcode.cn/problems/subarray-sum-equals-k/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/560/question.png)

## 思路

直接暴力法，遍历每一个子数组查看其和是否为k，发现会超时。暴力法的时间复杂度为$O(n^2)$，瓶颈在于对于每个 $i$，我们需要枚举所有以$nums[i]$作为结尾的子数组来判断其和是否等于k，这一步是否可以优化呢？当然是可以的，考虑到子数组的连续性特性，可以通过 **前缀和** 来优化。

前缀和是指数组中从第一个元素到当前元素的和。假设 $pre[i]$ 为 $[0, ..., i]$里所有数的和，那么 $pre[i]$ 就是前缀和。有了前缀和我们就可以用其来计算子数组的和, **“子数组 [j, i] 的和为k”** 就可以转化为以下等式：
$$
pre[i] - pre[j - 1] = k
$$
通过变形就可以得到：
$$
pre[i]  - k =  pre[j - 1]
$$
这说明，想要知道以 $nums[i]$ 结尾的和为 $k$ 的连续子数组的 **个数** 时只要统计在位置$i$之前有多少个前缀和为 $pre[i]  - k$的$pre[j]$ 。所以，考虑建立哈希表，以前缀和为`key`，该前缀和出现次数为`value`，从左往右边更新哈希表边计算答案，即可在$O(n)$时间内完成此题。

需要注意的是，我们在构造哈希表时需要先插入键值对{0, 1} 且在遍历时必须先判断是否有符合条件的前缀和再往哈希表里记录当前前缀和。

### 为什么可以从左往右依次更新哈希表？

因为$pre[i]$的值可以由 $pre[i - 1]$递推而来，即：
$$
pre[i] = pre[i - 1] &#43; nums[i]
$$

### 为什么在构造哈希表时需要先插入键值对{0, 1}?

这是为了处理特殊情况。当我们处理到位置 $i$ 时，我们想要知道的是全部以 $nums[i]$ 结尾的和为 $k$ 的连续子数组的个数，这肯定包括由[0, ..., i]组成的连续子数组，它们的前缀和即为 $pre[i]$，如果不先插入键值对{0, 1}就会漏掉 $pre[i] = k$ 这一情况，导致漏算答案。

### 为什么要先处理再记录？

因为我们的哈希表记录的是在该位置之前的前缀和有哪些，以及这些前缀和对应的出现次数。

### 为什么会想到这一思路？

因为在以前做到过使用前缀和的题，学习到了在计算子数组和时可以采用前缀和的差来进行计算。因此，在第一次做到这种题想不到这种思路是很正常的，只要多做题，定期复盘，就一定会越来越强！

## 代码

```cpp
class Solution
{
 public:
  int subarraySum(vector&lt;int&gt;&amp; nums, int k)
  {
    unordered_map&lt;int, int&gt; unmap = { { 0, 1 } }; 

    int pre = 0;
    int ans = 0;
    for (const auto num : nums)
    {
      pre &#43;= num;  // 计算当前 前缀和 即pre[i]

      auto it = unmap.find(pre - k);
      if (it != unmap.end())
      {
        ans &#43;= it-&gt;second;
      }

      &#43;&#43;unmap[pre];  // 将当前 前缀和 进行记录
    }

    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/847b681/  

