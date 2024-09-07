---
title: "DFS Algorithm"
date: 2020-05-11
image: /assets/img/blog/IMG_0289.JPG
description: >
  use DFS easily!
author: author2
comments: true
---

深度优先搜索算法(英文：**Depth-First-Search, DFS**) 是一种用于遍历或搜索树和图的算法。其过程简单来说是对每一个可能的分支路径深入到不能继续为止，而且每个节点只能访问一次。

## 经典DFS举例

### 全排列问题

给定一个不含数字的数组`nums`，返回其所有可能的全排列。

```c++
class Solution1 {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        st.resize(nums.size());
        path.resize(nums.size());
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return res;
    }

    void dfs(vector<int> nums, int u) {
        if (u == nums.size()) {
            res.emplace_back(path);
        }

        for (auto i = 0; i < nums.size(); i++) {
            if (!st[i]) {
                st[i] = true;
                path[i] = nums[u];
                dfs(nums, u + 1);
                st[i] = false;
            }
        }
    }

    vector<bool> st;
    vector<int> path;
    vector<vector<int>> res;
};

class Solution2 {
public:
    vector<vector<int>> permute(vector<int> &nums)
    {
        sort(nums.begin(), nums.end());
        dfs(nums, 0, nums.size() - 1);
        return res;
    }
    
    void dfs(vector<int> nums, int lfs, int rhs)
    {
        if (lfs == rhs) {
            res.emplace_back(nums);
        }
        
        for (auto i = lfs; i <= rhs; i++) {
            swap(nums[i], nums[lfs]);
            dfs(nums, lfs + 1, rhs);
        }
    }
    
    vector<vector<int>> res;
};
```

### 子集问题

举例：给一个整数数组，数组中的元素互不相同，返回该数组中所有可能的子集(幂集)

实例1：

> **输入**：nums = [1,2,3];
>
> **输出**：[[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]

```c++
class Solution {
public:
    void handle(const vector<int> &nums, int u, int &k)
    {
        if (path.size() == k) {
            result.push_back(path);
            return;
        }
        
        for (int i = u; i < nums.size(); i++) {
            path.push_back(nums[i]);
            handle(nums, i + 1, k);
            path.pop_back();
        }
    }

    vector<vector<int>> SubSets(vector<int> &nums)
    {
        for (int i = 0; i <= nums.size(); i++) {
        	handle(nums, 0, i);
        }
        return result;
    }
    
private:
    vector<int> path;
    vector<vector<int>> result;
};
```

举例：一个文稿转换问题

给定一个文本字符串 `text`，通过规则集 `patterns` 进行转换，通过pattern中的一个或多个 **a->b** 字符转换规则，将转换后的 `text` 字符的 `ASSIC` 码求和，与给定 `checkSum` 值进行匹配，若能匹配成功，则返回该文本 `text` 。

**输入**：

{% raw %}

> text: "   a   "
>
> checkSum: 97
>
> patterns: {{‘t’, 'f'}, {'b', 'l'}}

{% endraw %}

**输出**：

> ‘   a   ’



{% raw %}

```c++
class Solution {
public:
    bool IsMatchOk(const string &text, int checkSum, const vector<pair<char, char>> &patterns)
    {
		string temp = text;
        for (auto &[o_, n_] : patterns) {
            for (int i = 0; i < temp.size(); i++) {
                if (temp[i] == o_) {
                    temp[i] = n_;
                }
            }
        }
        
        int res = 0;
        for (const auto &c : temp) {
            if (c != ' ') {
                res += (int)c;
            }
        }
        return res == checkSum;
    }

    void handle(const vector<pair<char, char>> &patterns, int idx, int &k)
    {
        if (path.size() == k) {
            res.push_back(path);
            return;
        }
        
        for (int i = idx; i < patterns.size(); i++) {
            path.push_back();
            handle(patterns, i + 1, k);
            path.pop_back();
        }
    }

    string Translate(const string &text, int checkSum, const vector<pair<int, int>> &patterns)
    {
        for (size_t i = 0; i <= patterns.size(); i++) {
            handle(patterns, 0, i);
        }
        
        for (const auto &p : res) {
            if (IsMatchOk(text, checkSum, p)) {
                return text;
            }
        }
        return "";
    }
    
    vector<pair<int, int>> path;
    vector<vector<pair<int, int>>> res;
};
```

{% endraw %}
