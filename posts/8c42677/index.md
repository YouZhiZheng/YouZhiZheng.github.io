# 33. 搜索旋转排序数组


## [题目](https://leetcode.cn/problems/search-in-rotated-sorted-array/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/33/question.png)

## 思路

最开始想的是先通过二分来找到最大值位置，确定$right$和$left$的值，再使用一次二分来进行查询。但仔细观察题目发现只需进行一次二分查找算法就可以解决问题了，因为 **「该旋转后的数组从中间分开后，左右部分中必定有一个部分是有序的」**。

那么我们就可以借助这个有序数组来直接进行二分查找，采用下面的方式来更新$left$和$right$的值：

- 左边部分有序
  - target在这个有序区间内，则可以直接抛弃右边部分。反之，抛弃左边部分。
- 右边部分有序
  - target在这个有序区间内，则可以直接抛弃左边部分。反之，抛弃右边部分。

## 代码

```cpp
class Solution
{
 public:
  int search(vector&lt;int&gt;&amp; nums, int target)
  {
    int mid, left = 0;
    int right = nums.size() - 1;

    while (left &lt;= right)
    {
      mid = ((right - left) &gt;&gt; 1) &#43; left;
      if (target == nums[mid])
      {
        return mid;
      }

      // 判断左边是否有序, 这里是 &lt;= 是为了处理左边只有一个元素的情况
      if (nums[left] &lt;= nums[mid])
      {
        // 左边有序
        if (nums[left] &lt;= target &amp;&amp; nums[mid] &gt; target)
        {
          // 在区间内
          right = mid - 1;
        }
        else
        {
          left = mid &#43; 1;
        }
      }
      else
      {
        // 右边有序
        if (nums[mid] &lt; target &amp;&amp; target &lt;= nums[right])
        {
          // 在区间内
          left = mid &#43; 1;
        }
        else
        {
          right = mid - 1;
        }
      }
    }

    return -1;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/8c42677/  

