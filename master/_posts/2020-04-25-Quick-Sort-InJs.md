---
title: JS实现快速排序
date: 2020-04-25
image: /assets/img/blog/IMG_0383.JPG
description: >
  quick-sort!
author: author2
comments: true
---

one day one game!

```js
// 1.选第一个元素作为基准
var a = [2,15,5,63,2,4,57,35];
function quicksort(arr) {
    if(arr.length === 0)
        return [];
    var left = [];
    var right = [];
    var pivot = arr[0];

    for(var i = 1; i < arr.length; i++) {
        if(arr[i] < pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }

    return quicksort(left).concat(pivot,quicksort(right));
}
console.log(quicksort(a));

// 2.选中点元素作为基准
var quickSort = function(arr) {
    if(arr.length <= 1) {return arr;}
    var pivotIndex = Math.floor(arr.length / 2);
    //从数组中删除基准元素(改变原始数组),返回被删除的数组[arr[pivotIndex]]，
    //并将数组中的元素赋给pivot;
    var pivot = arr.splice(pivotIndex,1)[0];
    var left = [];
    var right = [];
    for(var i=0;i<arr.length;i++) {
        if(arr[i]<pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }
    return quickSort(left).concat(pivot,quickSort(right));
};
console.log(quickSort(a));

// 3.随机产生基准
var QuickSort = function(arr) {
    if(arr.length <= 1) {return arr;}
    let start=0;
    let end = arr.length-1;
    let pivotIndex = Math.floor(Math.random()*(end-start+1))+1;
    // console.log(pivotIndex);
    //从数组中删除基准元素(改变原始数组),返回被删除的数组[arr[pivotIndex]]，
    //并将数组中的元素赋给pivot;
    var pivot = arr.splice(pivotIndex,1)[0];
    
    var left = [];
    var right = [];
    for(var i=0;i<arr.length;i++) {
        if(arr[i]<pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }
    return QuickSort(left).concat(pivot,QuickSort(right));
};
console.log(QuickSort(a));
```

