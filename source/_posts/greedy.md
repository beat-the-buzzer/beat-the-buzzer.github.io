---
title: 算法系列——贪心算法
date: 2019-12-15
tags: [算法, JavaScript]
categories: 
- 算法
series: 算法
---

项目Git地址：[https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/greedy](https://github.com/beat-the-buzzer/algorithm-for-javascript/tree/master/greedy)

### 贪心算法简介

一个贪心算法总是做出当前最好的选择，也就是说，它期望通过局部最优选择从而得到全局最优的解决方案。

使用贪心算法的条件：

 - **贪心选择性质**：原问题的整体最优解可以通过一系列局部最优的选择得到

 - **最优子结构**：一个问题的最优解包含其子问题的最优解

冒泡排序、选择排序，是否用到了贪心算法？

拿冒泡排序为例，每一次遍历，找到的最大的元素，下一次遍历，是在剩余的部分再去寻找最大的元素。贪心策略就是每一次从剩下的序列中选一个最大的数，把这些最大的数放在一起，就是排序后的结果。

### 贪心算法demo

#### 最优装载问题

海盗船载重量为C，每件财宝的重量为wi，如何装载最多数量的财宝？

显然，这里我们贪心的东西是数量，所以贪心策略就是从小的开始装，这样会得到最大的数量。

```js
var w = [4, 10, 7, 11, 3, 5, 14, 2]; 
w.sort(function (a, b) { return a - b }); // 首先对重量进行排序
var carry = 30; // 船的载重量
var temp = 0; // 已经在船上的重量
var ans = 0; // 已经在船上的数量

for (var i = 0; i < w.length; i++) {
  temp += w[i]; // 把财宝放到船上
  if (temp <= carry) {
    ans++; // 没超重-> 计数
  } else {
    break; // 超重 -> 直接退出循环
  }
}
console.log('最后带走的财宝数量：' + ans);

var temp1 = 0;
var ans1 = 0;
for (var j = 0; j < w.length; j++) {
  temp1 += w[j];
  if (temp1 >= carry) {
    if (temp1 == carry) {
      ans1 = j + 1; // 正好装潢 -> 最后一个可以放
    } else {
      ans1 = j; // 已经超出，最后一个不能放
    }
  }
}
console.log('最后带走的财宝数量：' + ans1);
```

#### 背包问题

n种宝物，每种宝物都有对应的重量w和对应的价值v，一次只能运走m重量的宝物，一种宝物只能拿一样，宝物可以分割。如何带走最大价值的宝物？

这种情况下的贪心策略就是去装单位重量内最值钱的。

```js
class Treasure {
  constructor(w, v) {
    // 传入重量和价值
    this.w = w;
    this.v = v;
    this.p = v / w;
  }
}

var wArr = [4, 2, 9, 5, 5, 8, 5, 4, 5, 5];
var vArr = [3, 8, 18, 6, 8, 20, 5, 6, 7, 15];
var len = wArr.length;
var treasureArr = [];

for (var i = 0; i < len; i++) {
  treasureArr[i] = new Treasure(wArr[i], vArr[i]);
}
// 得到对象数组

treasureArr.sort(function (a, b) { return b.p - a.p }); // 按照单位重量的价值进行排序（从大到小）

var m = 30; // 总的承重
var sum = 0; // 运走的总价值

for (var j = 0; j < len; j++) {
  if (m > treasureArr[j].w) {
    // 如果可以把第j个物品装满，就去装
    m -= treasureArr[j].w;
    sum += treasureArr[j].v;
  } else {
    // 如果第j个物品装不满，就把剩余的重量用这个物品装满
    sum += m * treasureArr[j].p;
    break;
  }
}
console.log('带走财宝的总价值：' + sum);
```

如果物品不可分割，那么这个问题就不能使用贪心策略，举个例子：

物品列表中，有一个100斤的黄金和100克的废铁。承重量是30斤。那么一开始选择黄金，装不下，由于不可分割，算法结束，啥也没装，不可能是最优解。

不可分割的问题叫做： 0-1背包问题。关于这个问题后续会给出其他算法。

#### 哈夫曼树

在初学编程的时候，我们都做过这样的题：将考试分数转化成等级，90分以上的为A，80~90的为B，70~80的为C，60~70的为D，60以下的为不及格。我们会写出这样的代码：

```js
if(score >= 90) {
  return '优秀';
} else if(score >=80) {
  return '良好';
} else if(score >=70) {
  return '中等';
} else if(score >= 60) {
  return '及格';
} else {
  return '不及格！！！';
}
```

显然这样的代码没有任何问题，但是我们考虑的点不在这里。我们要考虑，学生的分数呈正态分布，我们应该把频率最高的放在第一个，这样比较多的数据只需要比较一次，从而减少总的比较次数，提升效率。

如果这个例子不够清晰，我们再举出其他的例子：如果我们要去猜一个老教授的年龄，我们先猜老教授是否是1岁，如果不是，再猜老教授是否是2岁，以此类推。其实，这个问题和上面分数的问题是同一类问题。我们优化一下上面的分数问题。

假设优秀、良好、中等、及格、不及格这五种情况的频率分别是0.1、0.2、0.4、0.2、0.1。我们可以这样改写程序：

```js
if (score < 80) {
  if (score < 70) {
    if (score < 60) {
      return '不及格';
    } else {
      return '及格';
    }
  } else {
    return '中等';
  }
} else if (score < 90) {
  return '良好';
} else {
  return '优秀';
}
```

根据上面的频率，假设现在有100个学生，那么第一种情况的比较次数大约为：

```
100*0.1*1 + 100*0.2*2 + 100*0.4*3 + 100*0.2*4 + 100*0.1*5 = 300(次)
```

第二种情况的比较次数大约为：

```
100*0.1*3 + 100*0.2*3 + 100*0.4*2 + 100*0.2*2 + 100*0.1*2 = 230(次)
```

两种方法的差别还是不可忽视的。其实我们可以总结规律：把频率大的部分靠近树根，一次成功的概率会大大提升。

哈夫曼编码的思想其实和这个一样，用字符的使用频率作为权构建一颗哈夫曼树。构建方法是自底向上，进行合并。核心思想是`权值越大的元素离根越近`。

贪心策略：`每次从树的集合中取出没有双亲且权值最小的两棵树左右子树，构造出一棵新树。`

下面来看一下哈弗曼树的构建步骤：

以（7,18,3,32,5,26,12,8） 为例：

1、初始状态

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman01.png)

2、找到权值最小的两个节点，进行合并，如下图，我们把3和5合并成8

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman02.png)

3、对剩余节点和新合成的节点，再次寻找权值最小的两个节点，进行合并，7和8可以合成15，这里两个8，随机选择，不影响结果。

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman03.png)

4、下一步操作，选择8和12，合并成20

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman04.png)

5、下一步操作，15和18，合成33

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman05.png)

6、下一步操作，20和26，合并成46

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman06.png)

7、下一步操作，33和32，合并成65

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman07.png)

8、下一步操作，65和46，合并成111，此时只剩一个节点，这个节点作为根节点，算法结束

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman08.png)

看一下根节点的位置：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/algorithm/huffman/huffman09.png)

如图，我们看到，距离根越近的节点，权值越大。
