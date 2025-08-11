# 239. 滑动窗口最大值


## [题目](https://leetcode.cn/problems/sliding-window-maximum/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/239/question.png)

## 思路

直接暴力法，发现超时，怎么进一步优化呢？每个窗口我们都要从头到尾扫描一遍来寻找最大值，这是耗时所在，因为是寻找最大值，直接想到一种非常适合的数据结构——大根堆，堆顶就是最大值。每往右移动一次，就将新的元素加入堆中，再判断堆顶元素是否在窗口内，如果不是就移除，直到在当前窗口的元素成为堆顶。因为将一个元素加入到堆中的时间复杂度为$O(log n)$，所以算法的时间复杂度为$O(n log n)$。

能不能进一步优化呢？当然可以，在**一个窗口**中，假设两个元素的**下标**分别为$i, j$且$i &lt; j$，那么「只要$i$在窗口中，$j$也必定在窗口中」。若$nums[i] &lt; nums[j]$， 那么$nums[i]$必定不可能成为最大值，我们就可以不必存储类似$nums[i]$的值。所以，我们只需要用一个**双端队列**来存储「在当前窗口中可能在以后成为最大值的值的对应**下标**」。存储下标是因为我们后续要判断**该值是否超过窗口范围**。

根据前面的分析可知，该队列中的元素是递增的（因为是从左往后扫描的），元素(下标)对应的值是递减的。每次移动窗口就只需要将新元素与队尾元素对应的值进行比较，如果队尾元素对应的值小于新加元素对应的值，则将其移除（因为其对应的值不可能成为最大值了），不断地进行此项操作，直到队列为空或者新元素对应的值小于队尾元素对应值。

显然，队首元素对应的值就是当前窗口的最大值，但需要注意队首元素是否超过了窗口的范围。

这样我们只需要$O(n)$时间内就能完成此题。

## 代码

```cpp
class Solution
{
 public:
  vector&lt;int&gt; maxSlidingWindow(vector&lt;int&gt;&amp; nums, int k)
  {
    deque&lt;int&gt; dq;

    // 初始化, 即处理第一个窗口中的值
    for (int i = 0; i &lt; k; &#43;&#43;i)
    {
      // 将不可能成为最大值的移除队列
      while (!dq.empty() &amp;&amp; nums[dq.back()] &lt; nums[i])
      {
        dq.pop_back();
      }

      dq.push_back(i);
    }

    vector&lt;int&gt; ans = { nums[dq.front()] };
    for (int i = 1; i &lt;= (nums.size() - k); &#43;&#43;i)
    {
      // 将不可能成为最大值的移除队列
      while (!dq.empty() &amp;&amp; nums[dq.back()] &lt; nums[i &#43; k - 1])
      {
        dq.pop_back();
      }
      dq.push_back(i &#43; k - 1);

      // 将超过窗口范围的元素移除队列
      while (!dq.empty() &amp;&amp; dq.front() &lt; i)
      {
        dq.pop_front();
      }

      ans.push_back(nums[dq.front()]);
    }

    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/f524d2b/  

