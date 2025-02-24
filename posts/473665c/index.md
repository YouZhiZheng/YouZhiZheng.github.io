# 42. 接雨水


## [题目](https://leetcode.cn/problems/trapping-rain-water/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/42/question.png)

## 思路

接的雨水总量为每个柱子能够接的雨水之和，而每个柱子的接水量计算公式为：
$$
接水量=\min(左右两边最高柱子)−当前柱子的高度
$$
那么直接新建两个数组 **left_max** 和 **right_max** 来存储每个柱子左右两侧的最大高度， **left_max[i]** 和 **right_max[i]** 分别表示柱子i左右两侧的最高柱子高度( **left_max[i]** 从左往右计算，  **right_max[i]** 从右往左计算)，然后再通过遍历计算每个柱子的接水量累加起来即可。

上面的方法时间复杂度和空间复杂度均为$O(n)$。时间已经是最快了，那么空间上还能不能进行优化呢？上面的方法空间耗费主要来自于新建的两个存储数组。由于数组`left_max`是从左往右计算，数组`right_max`是从右往左计算，因此可以使用双指针和两个变量代替两个数组。

对于左指针`left`来说，其左右两侧的最大高度分别为`left_left_max`和 `left_right_max`；同理右指针有`right_left_max`和 `right_right_max`。由于$right &gt; left$，故$right\\_left\\_max \ge left\\_left\\_max$ 且 $left\\_right\\_max \ge right\\_right\\_max$，这两个不等式画个图便可直接看出。

由上面的不等式可知：

- 若$right\\_right\\_max &gt;  left\\_left\\_max$，则必有$left\\_right\\_max &gt; left\\_left\\_max$，又`left_left_max`是可知的， 那么就可以计算left指向的柱子的接水量。
- 若$right\\_right\\_max &lt;  left\\_left\\_max$，则必有$right\\_left\\_max &gt; right\\_right\\_max$，又`right_right_max`是可知的， 那么就可以计算right指向的柱子的接水量。

在处理了left指向的柱子后，`&#43;&#43;left`向右移; 在处理了right指向的柱子后，`--right`向左移，直到全部柱子都被处理。

## 代码

```cpp
class Solution
{
 public:
  int trap(vector&lt;int&gt;&amp; height)
  {
    int ans, left, left_max;
    int right, right_max;

    ans = left = 0;
    right = height.size() - 1;
    left_max = right_max = 0;

    while (left &lt;= right)
    {
      // 更新最大值
      left_max = max(left_max, height[left]);
      right_max = max(right_max, height[right]);

      // 判断左右两边哪边的柱子可以被计算
      if (left_max &gt; right_max)
      {
        ans &#43;= (right_max - height[right]);
        --right;
      }
      else
      {
        ans &#43;= (left_max - height[left]);
        &#43;&#43;left;
      }
    }

    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/473665c/  

