# 437. 路径总和III


## [题目](https://leetcode.cn/problems/path-sum-iii/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/437/question.png)

## 思路

看到题目首先想到的是暴力法，**让每一个点为起点，穷举该点向下延伸的所有路径，将符合要求的路径累加起来，就是以该点为起点满足要求的路径数，将每个点符合要求的路径数累加起来就是答案**。

暴力法的时间复杂度为$O(N^2)$，能不能进行优化呢？有的，兄弟。回顾暴力法，我们发现存在大量重复计算，比如说示例1中的 **5→3** 这一条路，以10为起点会计算一次，以5为起点又会计算一次。那么有什么方法可以让每条路只被计算一次呢？那就是 **前缀和** ，既然以每个点为起点向下探索会存在大量重复计算，那么逆转一下思路，以每个点为**终点**向上探索。

我们想要计算以当前节点为终点的任意路径的和，那么就需要借助[第560题](https://zyzhi.top/posts/847b681/)学习到的**前缀和**方法。用一个哈希表 **$presum$** 来记录当前子路径中每种路径和的个数，当走到结点$node$时，我们只需要获得路径和为 **$curr\\_presum - targetSum$** 的路径数就可知道以$node$为终点，路径和为$targetSum$的路径数了。

那么来整理一下整体思路：使用递归来遍历每个节点，用$currSum$来记录从**根节点到当前节点**的路径和(即前缀和)，同时用哈希表来记录**从根节点到当前节点的父节点的所有路径和的出现次数**。这样，每当走到一个新节点，就可以用减法快速判断以当前节点为**终止节点**的所有路径中有多少是符合要求的。最后，使用一个变量来累加全部结果。

## 代码

```cpp
// 解法一，暴力法：
class Solution
{
 public:
  // 累加每个点符合要求的路径数
  long pathSum(TreeNode* root, long targetSum)
  {
    if (root == nullptr)
    {
      return 0;
    }

    long ret = rootSum(root, targetSum);
    ret &#43;= pathSum(root-&gt;left, targetSum);
    ret &#43;= pathSum(root-&gt;right, targetSum);

    return ret;
  }

  // 计算以root为起点，符合要求的路径数
  long rootSum(TreeNode* root, long targetSum)
  {
    if (!root)
    {
      return 0;
    }

    int ret = 0;
    if (root-&gt;val == targetSum)
    {
      &#43;&#43;ret;
    }

    ret &#43;= rootSum(root-&gt;left, targetSum - root-&gt;val);
    ret &#43;= rootSum(root-&gt;right, targetSum - root-&gt;val);

    return ret;
  }
};

// 解法二，前缀和法：
class Solution
{
 public:
  // key: 当前路径和 value: 和为key的路径数
  unordered_map&lt;int, int&gt; presum;

  int dfs(TreeNode* root, int curr_sum, int targetSum)
  {
    if (!root)
    {
      return 0;
    }

    int ret = 0; // 用于记录符合要求的路径数
    curr_sum &#43;= root-&gt;val;
    /**
     * 这里注意: 要先判断再记录, 而不能先记录再判断, 即在if过后才能presum[curr_sum]&#43;&#43;;
     * 因为我们的 presum 记录的是当前节点前的节点的前缀和
     */
    if (presum.find(curr_sum - targetSum) != presum.end())
    {
      ret &#43;= presum[curr_sum - targetSum];
    }
    presum[curr_sum]&#43;&#43;; // 将当前结点的值进行记录
    ret &#43;= dfs(root-&gt;left, curr_sum, targetSum); // 累加左子树答案
    ret &#43;= dfs(root-&gt;right, curr_sum, targetSum); // 累加右子树是否存在
    presum[curr_sum]--; // 将当前结点的值删除，因为要回退到当前结点的父节点了
    return ret;
  }

  int pathSum(TreeNode* root, int targetSum)
  {
    presum[0] = 1;  // 添加这个值是为了处理 curr_sum 刚好等于 targetSum的情况
    return dfs(root, 0, targetSum);
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/8dee5a0/  

