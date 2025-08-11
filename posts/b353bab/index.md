# 124. 二叉树中的最大路径和


## [题目](https://leetcode.cn/problems/binary-tree-maximum-path-sum/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/124/question.png)

## 思路

读完题后发现：所谓的路径就是指以某一个结点为根，往左和右各走一边，路径和就是这条路径上的结点值之和。要寻找最大路径和就需要计算每个结点为根时的路径和，选取其中的最大值，这就需要遍历，而且很明显应该使用**后序遍历**来处理，因为只有当节点的左右子树都处理完了，我们才能计算以当前节点为根的最大路径和是多少，即：
$$
以当前结点为根的最大路径和 = 当前结点值 &#43; 左边的贡献 &#43; 右边的贡献
$$
那么左右贡献是什么呢？想一想处理完一个结点，要向它的父节点返回什么信息呢？那就是**以当前结点为根时的最大单边路径和**（因为路径不能分叉），这样才能计算其父节点为根时的最大路径和。但需要注意的时，如果最大单边路径和为负，则需要丢弃，因为其会拉低父节点的最大路径和，父节点是不会考虑连接这条路径的。

## 代码

```cpp
class Solution
{
 public:
  int dfs(TreeNode* root, int&amp; ans)
  {
    if (!root)
    {
      return 0;
    }

    int left = dfs(root-&gt;left, ans); // 存储左边的贡献
    int right = dfs(root-&gt;right, ans); // 存储右边的贡献

    ans = max(ans, left &#43; right &#43; root-&gt;val);  // 寻找最大路径和

    /**
     * max({ left, right, 0 }) 是为了找到当前结点的左右最大贡献值(因为只能取一边)，如果都为负，则丢弃
     * max(max({ left, right, 0 }) &#43; root-&gt;val, 0) 是为了判断以当前结点为根时的最大路径和，丢弃负值
     * 因为我们要寻找的是最大路径和，负值就会使得路径和变小
     */
    return max(max({ left, right, 0 }) &#43; root-&gt;val, 0);
  }

  int maxPathSum(TreeNode* root)
  {
    int ans = INT_MIN;
    dfs(root, ans);
    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/b353bab/  

