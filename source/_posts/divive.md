---
title: 算法系列——分治法
date: 2019-12-04
tags: [算法, JavaScript]
categories: 
- 算法
---

项目Git地址：[https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/divide-and-conquer](https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/divide-and-conquer)

### 分治法简介

先分，再解决，最后合。

### 再探归并排序

归并排序合快速排序也是分治法的一种应用。传送门：[https://beat-the-buzzer.github.io/2019/12/03/sort/#%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F](https://beat-the-buzzer.github.io/2019/12/03/sort/#%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F)

这里重新讲解一下归并排序：

归并排序，首先我们写一个方法，把两个有序的数组合并成一个有序的数组。

我们把原数组对半分割，生成的子数组如果可以分割，就继续分割。到最后，每个子数组只有一个元素。

很显然，只有一个元素的数组必然是有序的，所以我们把最后分割得到的数组进行合并操作，最终合并完全之后，得到了有序的数组。

我们对于分治法，最简单的应用就是查找，二分查找，也叫折半查找。就是在一个有序的数组中寻找某个元素，我们按照常理来，就是遍历数组，其实，如果数组本身是有序的，我们就可以使用效率更高的二分查找。就是拿数组中间的数和要找的数进行比较，这样可以缩小查找的范围，直到找到或者找不到为止。下面是详细代码

```js
// 二分查找的实现
function binaryQuery (arr, x) {
  var low = 0;
  var high = arr.length - 1;
  while (low <= high) {
    var middle = parseInt((low + high) / 2);
    if (x === arr[middle]) {
      return middle; // 查到了，返回索引值
    } else if (x < arr[middle]) {
      high = middle - 1; // 如果要查的值比中点小，就去前半部分查
    } else {
      low = middle + 1; // 如果要查的数比中点大，就去后半部分查
    }
  }
  return -1; // 查不到，返回-1，类似indexOf方法
}

var arrTest = [1, 2, 4, 6, 8, 9, 11, 15, 22, 32, 44, 56, 62, 77, 86, 99, 100];
console.log(binaryQuery(arrTest, 32));
console.log(binaryQuery(arrTest, 10));
```

显然，对于这样的问题，我们还可以使用递归的方式进行，问题很简单，直接看代码，绝对能看懂。

```js
// 二分查找的递归实现
function binaryQueryRe (arr, x, low, high) {
  if (low > high) {
    return -1;
  }
  var mid = parseInt((low + high) / 2);
  if (x === arr[mid]) {
    return mid;
  } else if (x > arr[mid]) {
    return binaryQueryRe(arr, x, mid + 1, high);
  } else {
    return binaryQueryRe(arr, x, low, mid - 1);
  }
}

var arrTestRe = [1, 2, 4, 6, 8, 9, 11, 15, 22, 32, 44, 56, 62, 77, 86, 99, 100];

console.log(binaryQueryRe(arrTestRe, 32, 0, arrTestRe.length - 1)); // 9
console.log(binaryQueryRe(arrTestRe, 10, 0, arrTestRe.length - 1)); // -1
```

### 二分查找

#### 非递归实现

```js
// 二分查找的实现

function binaryQuery(arr, x) {
  var low = 0;
  var high = arr.length - 1;
  while (low <= high) {
    var middle = parseInt((low + high) / 2);
    if (x === arr[middle]) {
      return middle; // 查到了，返回索引值
    } else if (x < arr[middle]) {
      high = middle - 1; // 如果要查的值比中点小，就去前半部分查
    } else {
      low = middle + 1; // 如果要查的数比中点大，就去后半部分查
    }
  }
  return -1; // 差不到，返回-1，类似indexOf方法
}

var arrTest = [1, 2, 4, 6, 8, 9, 11, 15, 22, 32, 44, 56, 62, 77, 86, 99, 100];

console.log(binaryQuery(arrTest, 32)); // 9
console.log(binaryQuery(arrTest, 10)); // -1
```

#### 递归实现

```js
// 二分查找的递归实现
function binaryQueryRe(arr, x, low, high) {
  if (low > high) {
    return -1;
  }
  var mid = parseInt((low + high) / 2);
  if (x === arr[mid]) {
    return mid;
  } else if (x > arr[mid]) {
    return binaryQueryRe(arr, x, mid + 1, high);
  } else {
    return binaryQueryRe(arr, x, low, mid - 1);
  }
}

var arrTestRe = [1, 2, 4, 6, 8, 9, 11, 15, 22, 32, 44, 56, 62, 77, 86, 99, 100];

console.log(binaryQueryRe(arrTestRe, 32, 0, arrTestRe.length - 1)); // 9
console.log(binaryQueryRe(arrTestRe, 10, 0, arrTestRe.length - 1)); // -1
```