# 240. 搜索二维矩阵Ⅱ


## [题目](https://leetcode.cn/problems/search-a-2d-matrix-ii/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/240/question.png)

## 思路

看到这题直接暴力法，但时间复杂度为$O(m*n)$，不够快。考虑到每一行的元素都是升序排列，那么可以在遍历每一行的元素时使用二分查找法，时间复杂度为$O(m * log_{}{n})$（当然也可以在遍历每一列时使用二分查找法）。

二分查找法我们只用到了行有序或列有序这两个中的一个条件，能不能同时应用这两个条件来实现更快的算法呢？观察发现：

- 如果一个数比$target$**大**，那么该元素所在**列**的后续元素都将比$target$大，所以可以直接抛弃掉这些元素。
- 如果一个数比$target$**小**，那么该元素所在**行**的前驱元素都将比$target$小，所以可以直接抛弃掉这些元素。

这意味着：**我们可以一次性跳过当前搜索范围中的一整行或一整列，快速减少搜索空间！** 每次移动会排除一行或一列，最多移动$m&#43;n$次，时间复杂度为 **$O(m&#43;n)$**

理清了思路，那么起始点该选择哪里呢？根据前面的分析可知，我们的起始点只能选择矩阵的**四个角**，因为只有这样才能在开始时覆盖全部的值。但四个角都可以吗？分析一下：

- **左上角**
  - 值比target大时：target不存在。
  - 值比target小时：无法跳过当前行和列。
- **左下角**
  - 值比target大时：可以跳过当前行。
  - 值比target小时：可以跳过当前列。
- **右上角**
  - 值比target大时：可以跳过当前列。
  - 值比target小时：可以跳过当前行。
- **右下角**
  - 值比target大时：无法跳过当前行和列。
  - 值比target小时：target不存在。

通过分析发现，起始点只能选择**右上角或左下角**。示例代码选择以右上角为起始点。

## 代码

```cpp
class Solution
{
 public:
  bool searchMatrix(vector&lt;vector&lt;int&gt;&gt;&amp; matrix, int target)
  {
    int x = 0, y = matrix[0].size() - 1;

    while (x &lt; matrix.size() &amp;&amp; y &lt; matrix[0].size())
    {
      if (matrix[x][y] == target)
      {
        return true;
      }
      else
      {
        if (matrix[x][y] &gt; target)
        {
          --y;
        }
        else
        {
          &#43;&#43;x;
        }
      }
    }

    return false;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/77cc956/  

