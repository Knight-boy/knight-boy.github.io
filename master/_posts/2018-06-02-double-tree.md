---
title: 完全二叉树的遍历
date: 2018-06-02
image: /assets/img/blog/IMG_0381.JPG
description: >
  tree-search!
author: author2
comments: true
---

one day one game!

```js
// 遍历完全二叉树
// 三个参数分别是左子树，当前节点，右子树
function Tree(left,label,right) {
    this.left = left;
    this.label = label;
    this.right = right;
}
//中序遍历函数(inorder)
function* inorder(t) {
    if(t) {
        yield* inorder(t.left);
        yield t.label;
        yield* inorder(t.right);
    }
}
//前序遍历
function* preorder(t) {
    if(t) {
        yield t.label;
        yield* preorder(t.left);
        yield* preorder(t.right)
    }
}
//后续遍历
function* lastorder(t) {
    if(t) {
        yield* lastorder(t.left);
        yield* lastorder(t.right)
        yield t.label;
    }
}
//生成完全二叉树
function make(array) {
    //判断是否是叶节点
    if(array.length === 1) return new Tree(null,array[0],null);
    return new Tree(make(array[0]),array[1],make(array[2]));
}
let tree = make([[['a'],'b',['c']],'d',[['e'],'f',['g']]]);

//遍历二叉树
var result1 = [],
    result2 = [],
    result3 = [];
for(let node of preorder(tree)) {
    result1.push(node);
}
for(let node of inorder(tree)) {
    result2.push(node);
}
for(let node of lastorder(tree)) {
    result3.push(node);
}
console.log('先序遍历：',result1);
console.log('中序遍历：',result2);
console.log('后序遍历：',result3);
```

