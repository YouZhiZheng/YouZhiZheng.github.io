# 238. 除自身以外数组的乘积


## [题目](https://leetcode.cn/problems/product-of-array-except-self/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/238/question.png)

## 思路

看题第一眼想到的是先计算给定数组所有元素的乘积，然后对数组中的每个元素 $x$，将总的乘积除以 $x$ 来求得除自身值的以外数组的乘积。但当数组中出现$0$时，这个方法就失效了，且题目说明了不能使用除法。

不能使用除法，又怎么计算除自身值以外数组的乘积呢？考虑到 **该数对应的乘积 =其左边的乘积 * 其右边的乘积**。所以，可以通过两次遍历依次计算每个数的**左右乘积**即可得到答案。

## 代码

```cpp
class Solution {
public:
    vector&lt;int&gt; productExceptSelf(vector&lt;int&gt;&amp; nums)
    {
        vector&lt;int&gt; ans;

        // 计算每个数的左侧乘积
        for(int i = 0; i &lt; nums.size(); &#43;&#43;i)
        {
            if(i == 0)
            {
                ans.push_back(1);
            }
            else
            {
                ans.push_back(ans[i - 1] * nums[i - 1]);
            }
        }

        // 计算每个数的右侧乘积, 并直接计算答案
        for(int rc = 1, i = nums.size() - 1; i &gt;= 0; --i)
        {
            if(i != (nums.size() - 1))
            {
                rc *= nums[i &#43; 1];
            }

            ans[i] *= rc;
        }

        return ans;
    }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/0ee95a7/  

