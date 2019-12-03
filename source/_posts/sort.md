---
title: 算法系列——排序算法
date: {{ date }}
tags: [算法, JavaScript]
categories: 
- 算法
series: 算法
---

Git地址：[https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/sort](https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/sort)

### 选择排序

> 遍历数组，每次遍历，把较小数的索引存起来。第一次遍历，找到了最小的数，和数组的第一个元素进行交换，第二次遍历，从第二个开始，找到了剩余最小的数，和数组的第二个元素进行交换。以此类推。

下面展示我的代码和注释：

```js
// 选择排序算法实现
function selectSort(arr) {
  const arrCopy = [...arr]; // 复制一份数组
  const len = arrCopy.length;
  let tempIndex; // 保存最小的数的索引
  for (let i = 0; i < len - 1; i++) {
    tempIndex = i;
  // j是从i+1开始，因为每次趟下来最小的已经确定了，
    for (let j = i + 1; j < len; j++) {
      if (arrCopy[tempIndex] > arrCopy[j]) {
        tempIndex = j;
      }
    }
    // 一趟for循环，找到了最小的元素的索引，并且把它存在tempIndex中，下面就是交换这个最小的元素和第i个元素
    [arrCopy[tempIndex], arrCopy[i]] = [arrCopy[i], arrCopy[tempIndex]];
  }
  return arrCopy;
}

const arr = [3, 1, 4, 5, 2, 7, 9, 6, 8];
selectSort(arr);
```

这里有一些操作，我会在这里慢慢展示。

#### 交换的实现

我们平常想到交换两个数的方式就是弄一个临时变量，其实可以一行搞定。这里使用了ES6的数组解构赋值。

```js
let a = 1;
let b = 2;
[a, b] = [b, a];
```

### 冒泡排序

> 遍历数组，比较相邻的两个数，如果前面的较大，就交换，这样一趟下来，最大的数必然在最后一个。最大的数慢慢沉下去，我称之为“石头算法”。

```js
// 冒泡排序算法实现
function bubbleSort(arr) {
  const arrCopy = [...arr];
  const len = arrCopy.length;
  for (let i = 0; i < len - 1; i++) {
    // 每次for循环，最大的元素都会沉底，所以内层的j每次都会减少
    for (let j = 0; j < len - 1 - i; j++) {
      if (arrCopy[j] > arrCopy[j + 1]) {
        [arrCopy[j], arrCopy[j + 1]] = [arrCopy[j + 1], arrCopy[j]];
      }
    }
  }
  return arrCopy;
}
const arr = [3, 1, 4, 5, 2, 7, 9, 6, 8];
console.log(bubbleSort(arr)); // arr已经排完序
```

#### 为什么要在一开始复制一份数组？

我在同一个JS文件里面写了两个排序算法，在没有复制数组，调用的时候，我是这样操作的：

```js
function selectSort () {...} // 没有复制数组
function bubbleSort () {...} // 没有复制数组
const arr = [3, 1, 4, 5, 2, 7, 9, 6, 8];
console.log(selectSort(arr));
console.log(bubbleSort(arr)); // arr已经排完序
```

如果我们不去复制一份数组，每次调用排序算法之后，原数组也被改变了。第二次调用的时候，传入的数组本来就是有序的。这是浅拷贝的问题，造成外部变量无意之间发生了改变。

###  归并排序

归并排序的思想就是分治法，先分再合。先说“合”：我们需要写一个函数，这个函数可以把两个有序的数组合并成一个有序的数组。合并的方法是，给两个数组准备两个指针，并且准备一个空数组用于存储合并后的值。比较两个指针对应位置的值，较小的值存入准备的空数组中，然后较小的值对应的指针向后移动。简单举个例子：

```js
var A = [1, 3, 5];
var B = [2, 4];
// 比较1和2，发现1较小，把1存在数组中：[1]
// A的指针向后移动，比较3和2，把2存在数组中：[1,2]
// B的指针向后移动；比较3和4，把3存在数组中：[1,2,3]
// ...
```

有了这个方法，我们接下来就是去“分”。很显然，如果数组中只有一个元素，那么它肯定是有序的。所以我们每次把数组一分为二，直到不能分的时候，我们再去执行合并的操作，这样到最后，就是有序的数组了。

在代码里，我没有真正意义上去分割数组，数组永远还是这个数组，我使用了变量去分割，也就是说，我的合并函数，是把前半部分和后半部分都是有序的，这样的数组“合并”成一个有序的数组，举个例子：

```js
var arr = [1, 3, 5, 7, 2, 4, 6, 8];
```

这个数组的前半部分是1、3、5、7，后半部分是2、4、6、8，都是有序的，我把这个数组“合并”成[1,2,3,4,5,6,7,8]，下面看看代码：

```js
function merge(arr, low, high) {
  const arrCopy = [...arr]; // 复制一份原始数组，因为我们要对传入的数组本身进行操作。
  let i = low; // 上半部分的起始位置
  let mid = parseInt((low + high) / 2); // 上半部分的结束位置
  let j = mid + 1; // 下半部分的起始位置
  // high是下半部分的结束位置
  let k = low;
  while (i <= mid && j <= high) {
    if (arrCopy[i] < arrCopy[j]) {
      arr[k++] = arrCopy[i++];
    } else {
      arr[k++] = arrCopy[j++];
    }
  }
  // 可能后半部分遍历完了，前半部分还有剩余
  while (i <= mid) {
    arr[k++] = arrCopy[i++];
  }
  // 可能前半部分遍历完了，后半部分还有剩余
  while (j <= high) {
    arr[k++] = arrCopy[j++];
  }
}
```

low是数组的初始位置，high是数组的末位置。事实上，上面这个方法可以对数组的连续子集进行部分操作。

“分”的方法就是递归了，能分就分，不能分就开始合，其实就是看初始位置和末位置，如果初始位置小于末位置，说明至少有两个元素，还能继续分，否则，就去执行合并操作。

```js
function mergeSort(arr, low, high) {
  if (low < high) {
    const mid = parseInt((low + high) / 2);
    mergeSort(arr, 0, mid);
    mergeSort(arr, mid + 1, high);
    merge(arr, low, high);
  }
}

const arr = [3, 1, 4, 5, 2, 7, 9, 6, 8];
mergeSort(arr);
console.log(arr);
```

#### 浅拷贝，也可以去利用

我在这里归并排序的写法就是直接对数组进行操作，所以我们可以直接对传入的参数进行操作，由于浅拷贝，这样做的结果就是改变了原数组。

### 快速排序

快速排序的思想其实很简单，就是把在数组中找一个“基准”，然后通过交换元素，把所有比基准小的元素放在基准左边，把所有比基准大的元素放在基准右边，然后对左右两部分分别做上面的操作，这样到最后，数组就有序了。我们就拿数组第一个元素作为基准，看看排序的过程是什么样子的。

```js
function partition (arr, low, high) {
  var i = low;
  var j = high;
  var pivot = arr[low]; // 基准值
  while (i < j) {
    while (i < j && arr[j] > pivot) {
      j--; // 如果后面的元素比基准大，指针就往左走，这样就找到了右边比基准小的元素的索引
    }
    if (i < j) {
      [arr[i], arr[j]] = [arr[j], arr[i]]; // 找到了右边比基准小的元素的索引，此时需要交换，然后左边指针加一
      i++; // 这样最左边的元素必然是比基准小的元素
    }
    while (i < j && arr[i] <= pivot) {
      i++; // 如果前面的元素比基准小，指针就往右走，这样就找到了左边比基准大的元素的索引
    }
    if (i < j) {
      [arr[i], arr[j]] = [arr[j], arr[i]]; // 找到了左边比基准大的元素的索引，此时需要交换，然后右边指针减一
      j--; // 这样最右边的元素必然是比基准大的元素
    }
  }
  return i; // 最终得到基准值所在位置，并且此时基准左边都比基准小，右边都比基准大
}

var arrQuickTest = [5, 1, 7, 3, 9, 2, 8, 4, 6];
partition(arrQuickTest, 0, arrQuickTest.length - 1); // 返回结果是4，也就是说，前四个都是比第五个小的，arrQuickTest变成[4, 1, 2, 3, 5, 9, 8, 7, 6]

// 接下来就是递归了，我们需要对前四和后四这两部分再次划分，当不能再划分的时候，说明已经拍好了
function quickSort1 (arr, low, high) {
  var mid = partition(arr, low, high); // 得到一次排序之后基准的位置
  if (low < high) {
    quickSort1(arr, low, mid - 1); // 比基准小的部分进行快速排序操作
    quickSort1(arr, mid + 1, high); // 比基准大的部分进行快速排序操作
    // mid的位置是正确的，不需要再次排序了
  }
}

quickSort1(arrQuickTest, 0, arrQuickTest.length - 1);
console.log(arrQuickTest);
```

其实重点在于前面的划分函数。我们来慢动作回放：

```js
[5, 1, 7, 3, 9, 2, 8, 4, 6] // 初始值
[4, 1, 7, 3, 9, 2, 8, 5, 6] // 右边向左边走，找到了4，比基准值小，交换，左边指针向右走一步，右边指针指向基准值5
[4, 1, 5, 3, 9, 2, 8, 7, 6] // 左边向右走，找到了7.比基准值大，交换，右边指针向左一步，左边指针指向基准值5
[4, 1, 2, 3, 9, 5, 8, 7, 6] // 同理，5和2交换
[4, 1, 2, 3, 5, 9, 8, 7, 6] // 同理，9和5交换，一趟结束
```

慢动作最终结果和上面的注释是一样的。然后我们递归地对基准左边和基准右边进行同样的操作，最终得到了有序的数组。

### 优化快速排序

在上面的快速排序里，我们每次都是和基准值进行交换，其实这个没有必要。我们的目的是把数组分成以基准值为边界的两个字序列，左边的都比基准值小，右边的都比基准值大。我们可以双向扫描，找到左边的那个比基准值大的元素，找到右边那个比基准值小的元素，这两个进行交换，这样做，比起上面的方法，交换次数少了很多。

我们继续表演慢动作：

```js
[5, 1, 7, 3, 9, 2, 8, 4, 6] // 初始值
[5, 1, 4, 3, 9, 2, 8, 7, 6] // 右边的指针找到了4，左边的指针找到了7，二者交换
[5, 1, 4, 3, 2, 9, 8, 7, 6] // 继续，右边的指针找到了2，左边的指针找到了9，交换
[2, 1, 4, 3, 5, 9, 8, 7, 6] // 右边指针向左走，走到2的位置，此时指针相遇，交换这个相遇的点和基准值，这样一趟排序结束
```

下面是完整代码：貌似书上的代码会导致数组溢出，我在这里进行了处理。

```js
function partition2(arr, low, high) {
  var i = low;
  var j = high;
  var pivot = arr[low];
  while (i < j) {
    while (i < j && arr[j] > pivot) {
      j--;
    }
    while (i < j && arr[i] <= pivot) {
      i++;
    }
    // 得到右边比基准值小的值的索引和左边比基准值大的值的索引
    if (i < j) {
      [arr[i], arr[j]] = [arr[j], arr[i]];
      i++;
      j--;
    }
  }
  // 经过上面的循环，和j应该相遇了，此时我们要看看下标为i的元素是否比基准大
  if (i <= high) {
    // 书中的代码貌似会溢出，所以当i超出数组范围的时候，不作处理
    if (arr[i] > pivot) {
      // 此时交换基准和i-1对应的元素
      [arr[low], arr[i - 1]] = [arr[i - 1], arr[low]];
      return i - 1; // 返回基准位置
    } else {
      [arr[low], arr[i]] = [arr[i], arr[low]];
      return i;
    }
  }
}

function quickSort2(arr, low, high) {
  var mid = partition2(arr, low, high); // 得到一次排序之后基准的位置
  if (low < high) {
    quickSort2(arr, low, mid - 1); // 比基准小的部分进行快速排序操作
    quickSort2(arr, mid + 1, high); // 比基准大的部分进行快速排序操作
    // mid的位置是正确的，不需要再次排序了
  }
}

var arrQuickTest2 = [5, 1, 7, 3, 9, 2, 8, 4, 6];
quickSort2(arrQuickTest2, 0, arrQuickTest2.length - 1);
console.log(arrQuickTest2);
```

快速排序是不稳定的排序，它的效率和基准值有关，如果正好是倒序的数组，就是最差的情况。

### 插入排序

```js
// 插入排序
function insertSort(arr) {
  var arrCopy = [...arr];
  for (var i = 1; i < arrCopy.length; i++) {
    var temp = arr[i]; // 记住这个元素，因为一会这个位置会被替换
    var j = i - 1;
    while (j >= 0 && arrCopy[j] > temp) {
      arrCopy[j + 1] = arrCopy[j];
      j--;
    }
    arrCopy[j + 1] = temp;
  }
  return arrCopy;
}
var insertSortArr = [4, 2, 1, 5, 3];
console.log(insertSort(insertSortArr));
```

其实插入排序的原理十分通俗易懂，我称之为“麻将排序”。排序的过程就和麻将一样，举个最简单的例子：

```js
一万  三万  二万
把三万往右拉一个位置，再把二万放在三万之前的位置
```
整理麻将的过程就是插入排序，下面我将一步一步把上面的例子整理出来：

```js
4 2 1 5 3
=> 记录2
=> 比较4、记录的2 满足循环条件 赋值 => 4 4 1 5 3
=> 循环结束，把记录的2放在指定位置 => 2 4 1 5 3
=> 记录1
=> 比较4、记录的1 满足循环条件 赋值 => 2 4 4 5 3
=> 循环继续，比较2、记录的1 满足循环条件 赋值 2 2 4 5 3
=> 循环结束，把记录的1放在指定位置 => 1 2 4 5 3
=> 记录5
=> 比较4、记录的5，循环直接结束，把记录的5放在指定位置，也就是原位置 => 1 2 4 5 3
=> 记录3
=> 比较5、记录的3 满足循环条件 赋值 => 1 2 4 5 5
=> 循环继续，比较4、记录的3 满足循环条件 赋值 => 1 2 4 4 5
=> 循环结束，把记录的3放在指定位置 => 1 2 3 4 5
结束
```

这里顺便提一下，谷歌浏览器V8引擎，对Array.prototype.sort()的实现用到的就是插入排序和快速排序。当数组元素小于等于22时，使用插入排序，大于22时，使用快速排序，这个有兴趣的话，可以看看V8的代码，GitHub上能搜到。火狐浏览器用的是归并排序。