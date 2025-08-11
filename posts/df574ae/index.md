# 15. 三数之和


## [题目](https://leetcode.cn/problems/3sum/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/15/question.png)

## 思路

题目中要求找到所有 **「不重复」且和为0**的三元组，这个「不重复」的要求使得无法直接使用三重循环枚举所有的三元组找到答案。当然，可以直接用三重循环枚举每个答案，再使用哈希表进行去重(即先对得到的答案进行排序，比如从小到大，再转化成string后存放进unordered_set)，得到最终答案。这种做法时间复杂度和空间复杂度都很高，因此需要换一种思路来考虑这个问题。

重复的答案形式为：$(a, b, c)、(a, c, b)、(b, a, c)$...... 假设$a \le b \le c$，如果能保证只有$(a, b, c)$这种形式的答案会被枚举，就能够直接去除掉重复的答案，而不必再使用哈希表来进行去重了。

要实现这一点，就可以通过将数组中的元素从小达到进行**排序**，随后使用三重循环进行遍历，且 **「每重循环相邻两次枚举的元素不能相同」**，否则也会造成重复。例如：一个排完序后的数组为$[-1, 0, 1, 1, 2, 3]$，第一次枚举到的三元组为$(-1, 0, 1)$ ，如果第三重循环继续枚举下一个相邻元素，那么仍然为 $(-1, 0, 1)$，这就产生了重复，因此就需要将第三重的循环跳到下一个不相同的元素，即数组中的最后一个元素3，枚举三元组$(-1, 0, 3)$。

至此，我们已经对初始版本进行了空间上的优化，下面给出伪代码：

```cpp
nums.sort()
for first = 0 .. n-1
    // 只有和上一次枚举的元素不相同，我们才会进行枚举
    if first == 0 or nums[first] != nums[first-1] then
        for second = first&#43;1 .. n-1
            if second == first&#43;1 or nums[second] != nums[second-1] then
                for third = second&#43;1 .. n-1
                    if third == second&#43;1 or nums[third] != nums[third-1] then
                        // 判断是否有 a&#43;b&#43;c==0
                        check(first, second, third)
```

空间上进行了优化，那么**时间**上能不能进行优化呢？求$a &#43; b &#43; c = 0$可以看作 **「给定一个数 $a$，找到剩余两个数的和为$-a$ 」**，设$target = 0 - a$，那么里面的两重循环做的事就是找到两个数$b, c$使得$b &#43; c = target$，而我们的数组又是**有序**的，那就可以使用**双指针法**来替换这两重循环，从而使得时间复杂度降低为$O(n^2)$。

## 代码

```cpp
class Solution
{
 public:
  vector&lt;vector&lt;int&gt;&gt; threeSum(vector&lt;int&gt;&amp; nums)
  {
    vector&lt;vector&lt;int&gt;&gt; result;
    // 按从小到大的顺序进行排序
    sort(nums.begin(), nums.end());

    // 遍历数组
    for (int i = 0; i &lt; nums.size(); i&#43;&#43;)
    {
      // 避免重复的 nums[i] 值
      if (i &gt; 0 &amp;&amp; nums[i] == nums[i - 1])
      {
        continue;
      }

      // 双指针法查找三元组
      int left = i &#43; 1, right = nums.size() - 1;
      while (left &lt; right)
      {
        int sum = nums[i] &#43; nums[left] &#43; nums[right];
        if (sum == 0)
        {
          // 找到一个满足条件的三元组
          result.push_back({ nums[i], nums[left], nums[right] });

          // 避免重复的 nums[left] 和 nums[right] 值
          while (left &lt; right &amp;&amp; nums[left] == nums[left &#43; 1])
          {
            left&#43;&#43;;
          }
          while (left &lt; right &amp;&amp; nums[right] == nums[right - 1])
          {
            right--;
          }

          left&#43;&#43;;
          right--;
        }
        else if (sum &lt; 0)
        {
          left&#43;&#43;;
        }
        else
        {
          right--;
        }
      }
    }

    return result;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/df574ae/  

