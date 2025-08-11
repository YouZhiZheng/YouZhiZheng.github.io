# 48. 旋转图像


## [题目](https://leetcode.cn/problems/rotate-image/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/48/question.png)

## 思路

这道题的难点主要在于不能使用辅助数组，空间复杂度必须为$O(1)$。通过观察示例，我们可以发现以下规律：

- 「第 $i$ 行」元素旋转到「第 $n−1−i$ 列」元素。
- 「第 $j$ 列」元素旋转到「第 $j$ 行」元素。

即 $(i, j)$ 要放置的位置为 $(j, n - 1 - i)$，只要我们提前使用一个临时变量$temp$来存储$matrix[j][n - 1 - i]$，即可完成$(i, j)$这个位置的旋转操作。那么$(j, n - 1 - i)$又旋转到哪个位置呢？继续应用规律，发现其旋转的位置为$(n - 1 - i, n - 1 - j)$，同样的，如果使用$temp$来存储$matrix[n - 1 - i][n - 1 - j]$，即可完成$(i, j)$和$(j, n - 1 - i)$这两个位置的旋转。按照前面的逻辑继续推理，我们可以发现：

- $(i, j)  → (j, n - 1 - i)$
- $(j, n - 1 - i) → (n - 1 - i, n - 1 - j)$
- $(n - 1 - i, n - 1 - j) → (n - 1 - j, i)$
- $(n - 1 - j, i) → (i, j)$

$A→B$表示位置A的元素顺时针旋转$90°$后的位置为B。观察上面的式子，发现这四项处于一个循环中，使用一个临时变量$temp$即可完成这四个元素的原地交换！

发现怎样进行原地交换后，还剩下一个难点就是「应该遍历哪些初始点才能完成全部点的旋转呢」？我们注意到，旋转是分层进行的：

- 最外层的元素会旋转到最外层的另一个位置。
- 次外层的元素会旋转到次外层的另一个位置。
- 依次类推，直到到达矩阵的中心（如果是奇数阶矩阵，中心元素不需要移动）。

因此，我们的遍历方式可以分为 **层级遍历** 和 **每层内的元素遍历**。

通过举例的方式可以得到以下表格：

|   n    | 要遍历的层数 |
| :----: | :----------: |
|   1    |      0       |
|   2    |      1       |
|   3    |      1       |
|   4    |      2       |
|   5    |      2       |
|   6    |      2       |
| ...... |    ......    |

观察发现，需要遍历的层数为 $\left \lfloor n / 2 \right \rfloor $。同样的通过观察即可发现第 i 层(i从0开始)的处理起点和终点分别为 $(i, i)、(i, n - 1 - i)$。

## 代码

```cpp
class Solution
{
 public:
  void rotate(vector&lt;vector&lt;int&gt;&gt;&amp; matrix)
  {
    int n = matrix.size();

    // 这里使用整除就是向下取整
    for (int i = 0; i &lt; n / 2; &#43;&#43;i)
    {
      for (int j = i; j &lt; (n - 1 - i); &#43;&#43;j)
      {
        int temp = matrix[i][j];
        matrix[i][j] = matrix[n - 1 - j][i];
        matrix[n - 1 - j][i] = matrix[n - 1 - i][n - 1 - j];
        matrix[n - 1 - i][n - 1 - j] = matrix[j][n - 1 - i];
        matrix[j][n - 1 - i] = temp;
      }
    }
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/fa20807/  

