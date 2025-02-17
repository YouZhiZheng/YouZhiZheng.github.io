# 1. 两数之和


[题目描述](https://leetcode.cn/problems/two-sum/description/?envType=study-plan-v2&amp;envId=top-100-liked)：

![图1](/PostsImgs/LeetCode/1/question.png)

## 思路

时间复杂度为 **$O(n^2)$** 的算法思路很简单，直接采用暴力法使用两层循环遍历每一种可能，判断是否存在答案。

这里详细说明一下时间复杂度为 **$O(n)$** 的算法，暴力法之所以慢是因为寻找 `target - x` 的时间复杂度过高。那有没有什么方法可以加速寻找元素 `target - x` 是否存在且存在时可以知道其索引呢？有的，那就是使用哈希表来做，以`target - x` 为`key`，以索引为`value`。这样，寻找`target - x`的时间复杂度就变为了 **$O(1)$**，极大地提高了效率。

具体来说，对于遍历的每个元素，先判断其`target - x`是否存在，若存在，则直接返回对应的下标；若不存在，则将`x`存入哈希表，方便后续的查找。

## 代码

```cpp
// 暴力法
class Solution
{
 public:
  vector&lt;int&gt; twoSum(vector&lt;int&gt;&amp; nums, int target)
  {
    for (int i = 0; i &lt; nums.size(); &#43;&#43;i)
    {
      for (int j = i &#43; 1; j &lt; nums.size(); &#43;&#43;j)
      {
        if ((nums[i] &#43; nums[j]) == target)
        {
          return { i, j };
        }
      }
    }
    return {};
  }
};

// 哈希表法
class Solution
{
 public:
  vector&lt;int&gt; twoSum(vector&lt;int&gt;&amp; nums, int target)
  {
    unordered_map&lt;int, int&gt; hashtable;
    for (int i = 0; i &lt; nums.size(); &#43;&#43;i)
    {
      unordered_map&lt;int, int&gt;::iterator it = hashtable.find(target - nums[i]);
      if (it != hashtable.end())
      {
        return { it-&gt;second, i };
      }
      hashtable[nums[i]] = i;
    }
    return {};
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/7d4248c/  

