# 53. 最大子数组和


## [题目](https://leetcode.cn/problems/maximum-subarray/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/53/question.png)

## 思路

看到题目说寻找**最大和的连续子数组**，想到了 **「560. 和为 K 的子数组」** 中的思路 **前缀和**。对于任意一个区间$[i, j]$来说其和为：
$$
sum[i, j] = preSum[j] - preSum[i - 1]
$$
最大和的连续子数组就是找**最大差值**，让$preSum[j]$ 尽可能的大，同时让它减去的$preSum[i - 1]$尽可能的小。所以，我们只需要从左往右依次计算每个位置的前缀和，且记录下截止当前下标之前出现过的最小前缀和$min\\_pre\\_sum$，并用它来和当前前缀和做差，获取一个潜在的最大子数组和。

## 代码

```cpp
class Solution
{
 public:
  int maxSubArray(vector&lt;int&gt;&amp; nums)
  {
    int ans = -1e5;
    int min_pre_sum, pre_sum;
    min_pre_sum = pre_sum = 0;

    for (const auto&amp; num : nums)
    {
      pre_sum &#43;= num; // 计算当前位置的前缀和
      ans = max(ans, pre_sum - min_pre_sum); // 计算最大子数组和
      min_pre_sum = min(min_pre_sum, pre_sum); // 记录已经出现的最小前缀和
    }

    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/3930252/  

