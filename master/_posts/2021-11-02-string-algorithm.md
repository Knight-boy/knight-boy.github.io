---
title: string algorithm
date: 2021-11-02
image: /assets/img/blog/default.png
description: >
  常见字符串求解法
author: author2
comments: true
---

**双指针法**(快慢指针法)在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作题，都使用双指针法。

**KMP算法**解决字符串匹配问题。

**滑动窗口**解决最长子串问题。

### 字符串去重

#### 从数组中移除重复元素 [LeetCode_27](https://leetcode-cn.com/problems/remove-element/)

题目：给你一个数组 **nums** 和一个值 **val**，你需要 原地 移除所有数值等于 **val** 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 **O(1)** 额外空间并原地修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

思路：快慢指针，**双指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。**

![快慢指针删除重复元素](https://tva1.sinaimg.cn/large/008eGmZEly1gntrds6r59g30du09mnpd.gif)

```c++
int removeElement(vector<int>& nums, int val) {
    int slowIndex = 0;
    for (auto fastIndex = 0; fastIndex < nums.size(); fastIndex++) {
        if (val != nums[fastIndex]) {
            nums[slowIndex++] = nums[fastIndex];
        }
    }
    return slowIndex;
}
```

#### 移除多余的空格

```c++
// 移除冗余空格：使用双指针（快慢指针法）O(n)的算法
void removeExtraSpaces(string& s) {
    // 定义快指针，慢指针
    int slowIndex = 0;
    int fastIndex = 0;
    // 去掉字符串前面的空格
    while ((s.size() > 0) && (fastIndex < s.size()) && (s[fastIndex] == ' ')) {
        fastIndex++;
    }
    for (; fastIndex < s.size(); fastIndex++) {
        // 去掉字符串中间部分的冗余空格
        if (fastIndex - 1 > 0 &&
            s[fastIndex - 1] == s[fastIndex] &&
            s[fastIndex] == ' ') {
            continue;
        } else {
            s[slowIndex++] = s[fastIndex];
        }
    }
    // 去掉字符串末尾的空格
    if ((slowIndex - 1 > 0) && (s[slowIndex - 1] == ' ')) {
        s.resize(slowIndex - 1);
    } else {
        s.resize(slowIndex); // 重新设置字符串大小
    }
}
```

### 字符串反转

#### 反转字符串 [LeetCode_344](https://leetcode-cn.com/problems/reverse-string/)

思路：双指针反转单词

![反转单词hello](https://tva1.sinaimg.cn/large/008eGmZEly1gp0fvi91pfg30de0akwnq.gif)

```c++
void reverseString(vector<char>& s) {
    for (int i = 0, j = s.size() - 1; i < s.size()/2; i++, j--) {
        swap(s[i],s[j]);
    }
}
```

#### 反转字符串II [LeetCode_541](https://leetcode-cn.com/problems/reverse-string-ii/)

题目：给定一个字符串 s 和一个整数 k，从字符串开头算起，每计数至 2k 个字符，就反转这 2k 字符中的前 k 个字符。

如果剩余字符少于 k 个，则将剩余字符全部反转。
如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。

> **示例 1：**
>
> 输入：s = "abcdefg", k = 2
> 输出："bacdfeg"
>
> **示例 2：**
>
> 输入：s = "abcd", k = 2
> 输出："bacd"



```c++
string reverseStr(string s, int k) {
    for (auto i = 0; i < s.size(); i += 2 * k) {
        if (i + k <= s.size()) {
            // 1. 每隔 2k 个字符的前 k 个字符进行反转
            // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
            std::reverse(s.begin() + i, s.begin() + i + k);
            continue;
        }
        // 3. 剩余字符少于 k 个，则将剩余字符全部反转
        std::reverse(s.begin() + i, s.end());
    }
    return s;
}
```

#### 反转字符串中的单词III [LeetCode_557](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)

给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

**示例：**

```bash
输入："Let's take LeetCode contest"
输出："s'teL ekat edoCteeL tsetnoc"
```

#### 翻转字符串中的单词 [LeetCode_151](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

如果字符串为："  the     sky is   blue       "

- 移除多余空格 : "the sky is blue"
- 字符串反转："eulb si yks eht"
- 反转单词："blue is sky the"

```c++
class Solution {
public:
    // 反转字符串s中左闭又闭的区间[start, end]
    void reverse(string& s, int start, int end) {
        for (int i = start, j = end; i < j; i++, j--) {
            swap(s[i], s[j]);
        }
    }

    // 移除冗余空格：使用双指针（快慢指针法）O(n)的算法
    void removeExtraSpaces(string& s) {
        // 定义快指针，慢指针
        int slowIndex = 0;
        int fastIndex = 0;
        // 去掉字符串前面的空格
        while ((s.size() > 0) && (fastIndex < s.size()) && (s[fastIndex] == ' ')) {
            fastIndex++;
        }
        for (; fastIndex < s.size(); fastIndex++) {
            // 去掉字符串中间部分的冗余空格
            if (fastIndex - 1 > 0 &&
                s[fastIndex - 1] == s[fastIndex] &&
                s[fastIndex] == ' ') {
                continue;
            } else {
                s[slowIndex++] = s[fastIndex];
            }
        }
        // 去掉字符串末尾的空格
        if ((slowIndex - 1 > 0) && (s[slowIndex - 1] == ' ')) {
            s.resize(slowIndex - 1);
        } else {
            s.resize(slowIndex); // 重新设置字符串大小
        }
    }

    string reverseWords(string s) {
        removeExtraSpaces(s); // 去掉冗余空格
        reverse(s, 0, s.size() - 1); // 将字符串全部反转
        int start = 0; // 反转的单词在字符串里起始位置
        int end = 0; // 反转的单词在字符串里终止位置
        bool entry = false; // 标记枚举字符串的过程中是否已经进入了单词区间
        for (int i = 0; i < s.size(); i++) { // 开始反转单词
            if (!entry) {
                start = i; // 确定单词起始位置
                entry = true; // 进入单词区间
            }
            // 单词后面有空格的情况，空格就是分隔符
            if (entry && (s[i] == ' ') && (s[i - 1] != ' ')) {
                end = i - 1; // 确定单词终止位置
                entry = false; // 结束单词区间
                reverse(s, start, end);
            }
            // 最后一个结尾单词之后没有空格的情况
            if (entry && (i == (s.size() - 1)) && (s[i] != ' ' )) {
                end = i;// 确定单词终止位置
                entry = false; // 结束单词区间
                reverse(s, start, end);
            }
        }
        return s;
    }

    // 精简之后
    string reverseWords(string s) {
        removeExtraSpaces(s);
        reverse(s, 0, s.size() - 1);
        for(int i = 0; i < s.size(); i++) {
            int j = i;
            // 查找单词间的空格，翻转单词
            while(j < s.size() && s[j] != ' ') {
                j++;
            }
            reverse(s, i, j - 1);
            i = j;
        }
        return s;
    }
};
```

### 字符串修改

#### 替换空格 [Offer_05](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

题目：请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

 **示例 1：**

> 输入：s = "We are happy."
> 输出："We%20are%20happy."

思路：

![替换空格](https://tva1.sinaimg.cn/large/e6c9d24ely1go6qmevhgpg20du09m4qp.gif)

```c++
string replaceSpace(string s) {
    int count = 0;
    int sOldSize = s.size();
    for (int i = 0; i < s.size(); i++) {
        if (s[i] == ' ') {
            // 统计空格的个数
            count++;
        }
    }
    // 扩充字符串s的大小，也就是每个空格替换成"%20"之后的大小
    s.resize(s.size() + count * 2);
    int sNewSize = s.size();
    // 从后先前将空格替换为"%20"
    for (int i = sNewSize - 1, j = sOldSize - 1; j < i; i--, j--) {
        if (s[j] != ' ') {
            s[i] = s[j];
        } else {
            s[i] = '0';
            s[i - 1] = '2';
            s[i - 2] = '%';
            i -= 2;
        }
    }
    return s;
}
```

#### 左旋转字符串 [Offer_58](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/submissions/)

题目：字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

> 示例 1：
>
> 输入: s = "abcdefg", k = 2
> 输出: "cdefgab"
> 示例 2：
>
> 输入: s = "lrloseumgh", k = 6
> 输出: "umghlrlose"



```c++
string reverseLeftWords(string s, int n) {
    std::reverse(s.begin(), s.begin() + n); // bacdefg
    std::reverse(s.begin() + n, s.end()); // bagfedc
    std::reverse(s.begin(), s.end()); // cdefgab
    return s;
}
```



```c++
string reverseLeftWords(string s, int n) {
    std::rotate(s.begin(), s.begin() + n, s.end());
    return s;
}
```

### 字符串匹配

#### KMP算法

KMP的主要思想是 **当出现字符串不匹配时，可以知道一部分之前已经匹配的文本内容，可以利用这些信息避免从头再去做匹配。**

如下图：从文本串 **aabaabaafa** 中匹配模式串 **aabaaf**，返回匹配到模式串的first_index

![使用前缀表KMP匹配](https://code-thinking.cdn.bcebos.com/gifs/KMP%E7%B2%BE%E8%AE%B22.gif)

前缀 是指**不包含最后一个字符的所有以第一个字符开头的连续子串**；

后缀 是指**不包含第一个字符的所有以最后一个字符结尾的连续子串**；

**前缀表** 前缀表就是一系列由**最长相同前后缀**长度组合的表。前缀表对应位置的数字表示的就是：**下标i之前(包括i)的字符串中，最长的相同前缀后缀长度是多少。**

**next数组** 可以既是前缀表，又可以把前缀表统一减一(右移一位，初始位置为-1)之后作为next数组。还可以把前缀表用成下标i之前(不包括包括i)的字符串中，最长的相同前缀后缀长度加1表示，见[求解next数组](https://www.bilibili.com/video/BV16X4y137qw/?spm_id_from=autoNext)。

![使用next数组KMP匹配](https://code-thinking.cdn.bcebos.com/gifs/KMP%E7%B2%BE%E8%AE%B24.gif)

构造next数组

```c++
int GetNext(string str, int *next) {
    next[1] = 0;
    int i = 1;
    int j = 0;
    while (i <= str.size()) {
        if ((j == 0) || (str[i] == str[j])) {
            next[++i] = ++j;
        } else {
            j = next[j];
        }
    }
}
```



[求解NEXT数组](https://www.bilibili.com/video/BV16X4y137qw/?spm_id_from=autoNext)

<video src="https://upos-sz-mirrorcos.bilivideo.com/upgcxcode/62/98/311669862/311669862-1-208.mp4?e=ig8euxZM2rNcNbRVhwdVhwdlhWdVhwdVhoNvNC8BqJIzNbfq9rVEuxTEnE8L5F6VnEsSTx0vkX8fqJeYTj_lta53NCM=&uipk=5&nbs=1&deadline=1641803411&gen=playurlv2&os=cosbv&oi=1866715013&trid=15a75dc0c13c41cc9da7906b7270f17bT&platform=html5&upsig=8bf41aea597a6bfc8c2b36068e6bb6b7&uparams=e,uipk,nbs,deadline,gen,os,oi,trid,platform&mid=0&bvc=vod&nettype=0&bw=65128&orderid=0,1&logo=80000000" controls preload></video>
#### 实现strStr() [LeetCode_28](https://leetcode-cn.com/problems/implement-strstr/)

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

#### 重复的子字符串 [LeetCode_459](https://leetcode-cn.com/problems/repeated-substring-pattern/)

给定一个非空的字符串，判断它是否可以由它的一个子串重复多次构成。给定的字符串只含有小写英文字母，并且长度不超过10000。

> 示例 1:
>
> 输入: "abab"
>
> 输出: True
>
> 解释: 可由子字符串 "ab" 重复两次构成。
> 示例 2:
>
> 输入: "aba"
>
> 输出: False
> 示例 3:
>
> 输入: "abcabcabcabc"
>
> 输出: True
>
> 解释: 可由子字符串 "abc" 重复四次构成。 (或者子字符串 "abcabc" 重复两次构成。)

### 子串问题

#### 无重复字符的最长子串 [LeetCode_3](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例 1:**

> 输入: s = "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。



滑动窗口求解

题中的 **abcabcbb**，进入这个队列（窗口）为 `abc` 满足题目要求，当再进入 a，队列变成了 `abca`，这时候不满足要求。此时，我们要移动这个队列！

我们只要把队列的左边的元素移出就行了，直到满足题目要求

一直维持这样的队列，找出队列满足题目要求时出现最长的长度时候，求出解
