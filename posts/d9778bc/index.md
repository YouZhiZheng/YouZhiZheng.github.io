# 46. 全排列


## [题目](https://leetcode.cn/problems/permutations/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/46/question.png)

## 思路

做这道题首先要知道什么是[回溯法](https://baike.baidu.com/item/%E5%9B%9E%E6%BA%AF%E7%AE%97%E6%B3%95/9258495)。

设数组大小为$n$，这个问题可以视为有$n$个排成一行的空格，依次为每个位置选择一个未被选的数来填充。那么可以直接想到一种穷举法，就是从左往右依次往空格中填入数字，全部空格填完就是一种答案。这里我们就可以使用回溯法来完成这一过程。

## 代码

```cpp
class Solution
{
 public:
  vector&lt;vector&lt;int&gt;&gt; ans;
  vector&lt;int&gt; output;  // 存储当前的组合
  void backtrace(vector&lt;int&gt;&amp; nums, vector&lt;bool&gt;&amp; used)
  {
    // 判断是否已经组合完毕
    if (output.size() == nums.size())
    {
      ans.emplace_back(output);
      return;
    }

    // 依次将每个元素放到当前位置
    for (size_t i = 0; i &lt; nums.size(); &#43;&#43;i)
    {
      if (used[i])
      {
        continue;
      }

      used[i] = true;             // 标记该元素被选
      output.push_back(nums[i]);  // 放到当前位置
      backtrace(nums, used);      // 递归, 去选择下一个位置的元素
      output.pop_back();          // 回退
      used[i] = false;
    }
  }

  vector&lt;vector&lt;int&gt;&gt; permute(vector&lt;int&gt;&amp; nums)
  {
    vector&lt;bool&gt; used(nums.size(), false);  // 用于判断某个元素是否已经被使用了
    backtrace(nums, used);
    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/d9778bc/  

