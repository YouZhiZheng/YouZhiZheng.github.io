# 86. 分隔链表

**题目描述([力扣](https://leetcode.cn/problems/partition-list/)):**  

![图1](/PostsImgs/algorithm_note_1_imgs/picture1.png)

**思路:** 创造两个指针，分别指向大于x的链表和小于x的链表，然后依次遍历初始链表的每个节点进行判断将其添加到对应的新链表中，最后将两个链接进行连接后返回。

**代码:**

```c&#43;&#43;
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* partition(ListNode* head, int x)
    {
        ListNode *h1 = new ListNode(-1); //指向小于x的节点组成的链表的头节点
        ListNode *h2 = new ListNode(-1); //指向大于x的节点组成的链表的头节点
        ListNode *t1, *t2; //分别指向各自链表的尾结点
        t1 = h1;
        t2 = h2;

        while(head != nullptr) //遍历每个节点，与x进行比较
        {
            if(head-&gt;val &lt; x)
            {
                t1-&gt;next = head;
                t1 = head;
            }
            else
            {
                t2-&gt;next = head;
                t2 = head;
            }
            head = head-&gt;next;
        }

        t1-&gt;next = h2-&gt;next; //将两个链表进行连接
        t2-&gt;next = nullptr;
        return h1-&gt;next;
    }
};
```


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: http://localhost:1313/posts/6fc9142/  

