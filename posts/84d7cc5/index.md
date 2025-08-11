# 394. 字符串解码


## [题目](https://leetcode.cn/problems/decode-string/description/?envType=study-plan-v2&amp;envId=top-100-liked)

![图1](/PostsImgs/LeetCode/394/question.png)

## 思路

该题就是给定一个编码后的字符串，然后返回解码后的字符串。

由题可知要处理嵌套的情况，即要先解决内层的$k[encoded_string]$，再解决外层，即后进先出，很适合用**栈**来解决。而且其结构为$k[encoded_string]$，很适合用双栈来解决，一个为**数字栈**用来存储重复几次，一个为**字符串栈**用来存储每一层进入 $[$ 时，之前构造的字符串。

## 代码

```cpp
class Solution
{
 public:
  string decodeString(string s)
  {
    string cur_str, pre_str, temp;
    stack&lt;int&gt; s_n;     // 数字栈，存储重复几次
    stack&lt;string&gt; s_s;  // 字符串栈，存储前面的字符串

    for (size_t i = 0; i &lt; s.size(); &#43;&#43;i)
    {
      if (s[i] &gt;= &#39;a&#39; &amp;&amp; s[i] &lt;= &#39;z&#39;)
      {
        cur_str &#43;= s[i];
      }
      else
      {
        // 处理当前层
        if (s[i] == &#39;]&#39;)
        {
          pre_str = s_s.top();
          s_s.pop();

          int repeatTimes = s_n.top();
          s_n.pop();

          temp.clear();
          for (int i = 0; i &lt; repeatTimes; &#43;&#43;i)
          {
            temp &#43;= cur_str;
          }

          cur_str = pre_str &#43; temp; // 构造当前处理完的字符串
        }
        else
        {
          // 数字
          int t_num = 0;
          while (s[i] != &#39;[&#39;)
          {
            t_num = t_num * 10 &#43; (s[i] - &#39;0&#39;);
            &#43;&#43;i;
          }

          s_n.push(t_num);
          s_s.push(cur_str);
          cur_str.clear();
        }
      }
    }

    return cur_str;
  }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/84d7cc5/  

