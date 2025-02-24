# 3. 无重复字符的最长子串

## [题目](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/3/question.png)

## 思路

使用两个指针$l, r$分别指向当前字串的左右边界，通过不断将 r 指针往后移使得子串不断变长，在移动r指针的同时需要判断新增的字符是否已经在窗口中出现了，如果已出现则需要移动l指针来**将重复的字符移动出窗口**，这样通过一次遍历就能找到无重复字符的最长子串。

## 代码

```cpp
class Solution
{
 public:
  int lengthOfLongestSubstring(string s)
  {
    /** 
     *    记录元素最后一次出现的位置，用于判断r指向的字符是否已经在当前窗口已经存在
     *    以及帮助 l 指针进行移动
     */
    unordered_map&lt;char, int&gt; hash; 
    int ans, l, r;
    for (l = r = ans = 0; r &lt; s.size(); &#43;&#43;r)
    {
      // 判断当前窗口是否存在重复字符
      // 需要注意的是 hash[s[r]] &gt;= l，这样才能确保当前元素是存在于当前窗口的
      // 若没有这个条件，只能说明 s[r] 字符在前面出现过，但不能确保它是在当前窗口中的
      // 比如 abba 字符串，当我们的窗口内字符为 ba 时，hash[s[3]] 的值为 0 但其并不在当前窗口内
      if (hash.find(s[r]) != hash.end() &amp;&amp; hash[s[r]] &gt;= l)
      {
        ans = max(ans, r - l);
        l = hash[s[r]] &#43; 1; // 将重复的字符串移除窗口
      }

      // 更新字符最后一次出现的下标
      hash[s[r]] = r;
    }

    // r - l 计算最后一个窗口的长度
    return max(ans, r - l);
  }
};
```
&lt;!--more--&gt;


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/2aaae8d/  

