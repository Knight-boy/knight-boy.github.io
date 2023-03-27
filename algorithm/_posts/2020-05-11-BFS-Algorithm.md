---
title: "BFS Algorithm"
date: 2020-05-13
image: /assets/img/blog/IMG_0317.JPG
description: >
  an effective bfs algorithm frame!
author: author2
comments: true
---

宽度优先搜索(**B**reath **F**irst **S**earch )

一种有效的宽度优先搜索(**B**reath **F**irst **S**earch)算法框架。

## 填充每个节点的下一个右侧节点指针 [LeetCode_116](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

```c++
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。

例如：

![Example 1](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

> 输入：root = [1,2,3,4,5,6,7]
> 输出：[1,#,2,3,#,4,5,6,7,#]
> 解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。序列化的输出按层序遍历排列，同一层节点由 next 指针连接，'#' 标志着每一层的结束。



```c++
/**
 * class Node {
 * public:
 *    int val;
 *    Node* left;
 *    Node* right;
 *    Node* next;
 *
 *    Node() : val(0), left(NULL), right(NULL), next(NULL) {}
 *    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}
 *    Node(int _val, Node* _left, Node* _right, Node* _next)
 *        : val(_val), left(_left), right(_right), next(_next) {}
 *  };
 **/
Node* connect(Node* root)
{
    if (root == nullptr) {
      	return nullptr;
    }
    queue<Node*>que;
    que.push(root);
    while (!que.empty()) {
        int n = que.size();
        // 存放每层中的前一个元素
        Node *pre = nullptr;
        for (int i = 0; i < n; i++) {
            Node *front = que.front();
            que.pop();
            if (front == nullptr) {
              	continue;
            }
            // pre的next指针指向当前元素
            if (pre != nullptr) {
              	pre->next = front;
            }

            pre = front;
            que.push(front->left);
            que.push(front->right);
        }
    }
    return root;       
}
```

