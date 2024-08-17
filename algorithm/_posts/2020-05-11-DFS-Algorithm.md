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



