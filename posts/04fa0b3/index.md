# 131. 分割回文串


## [题目](https://leetcode.cn/problems/palindrome-partitioning/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/131/question.png)

## 思路

这道题挺简单的，但是一开始想复杂了，故在此记录一下。

题目的意思就是将字符串$s$进行分割，且分割出来的每一个子串都必须时**回文串**。最简单有效的思路就是依次分割，先分割出第一个子串，在**未分割的子串**中再分割出第二个子串，......，依次这样下去，直到不存在未分割的子串。而对于每个要分割出来的子串我们都需要遍历其全部形式。

具体来说，设$s = &#34;abcde&#34;$，那么分割出来的第一个子串的形式有$a, ab, abc, abcd, abcde$，其对应的未分割子串依次为$bcde, cde, de, e, null$。所以，直接采用回溯法来做，直接看代码。

**PS:** 仔细思考会发现存在大量的字符串被反复判断是否是回文串，故可以采用**动态划规**（$dp[i][j]$ 表示$s[i]$~ $s[j]$组成的子串是否是回文串）来提前记录每个子串是否是回文串，加速运行速度。这里就不给出具体代码了，请读者自行编写。

## 代码

```cpp
class Solution
{
 public:
  vector&lt;vector&lt;string&gt;&gt; ans;
  vector&lt;string&gt; output;
  string str;

  bool judge(const string&amp; s)
  {
    int f = 0, b = s.size() - 1;
    while (f &lt;= b)
    {
      if (s[f] == s[b])
      {
        &#43;&#43;f;
        --b;
      }
      else
      {
        return false;
      }
    }

    return true;
  }

  void backtrace(const string&amp; s, size_t start_i)
  {
    // 判断是否已经将s划分完
    if (start_i == s.size())
    {
      ans.emplace_back(output);
      return;
    }

    // 遍历当前未划分子串中的第一个子串
    for (size_t i = start_i; i &lt; s.size(); &#43;&#43;i)
    {
      str = s.substr(start_i, i - start_i &#43; 1);

      if (judge(str))
      {
        // 当str是回文时才去划分下一个子串

        output.emplace_back(str);
        // 处理剩下未被划分的子串
        backtrace(s, i &#43; 1);
        output.pop_back();  // 回退
      }
    }
  }

  vector&lt;vector&lt;string&gt;&gt; partition(string s)
  {
    ans.clear();
    output.clear();

    backtrace(s, 0);
    return ans;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/04fa0b3/  

