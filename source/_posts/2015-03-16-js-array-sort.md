title: 关于javascript Array的sort函数
date: 2015-03-16 10:55:38
categories:
- IT
tags:
- javascript
- sort
- Array.prototype.sort
---
Array.prototype.sort方法的第一个参数实际是一个**定义了比较规则**的函数，内部程序在排序时会根据这个给定的定义去排序。

{% code lang:js %}
var list = [4,2,6,7,1,9,3,5,11,8,10];
list.sort(function(a, b) {
  return a > b ? 1 : -1;
});
{% endcode %}

## 规则函数compareFunc

这个定义了比较规则的compareFunc有两个参数(a, b)，返回值有0，大于0，小于0三类。

- 返回值小于0时，将a放在b的前面
- 返回值等于0时，保持原来位置不变
- 返回值大于0时，将b放在a的前面

实际上因为a和b原先的顺序是a在b前面，所以只有当返回大于0时，才会发生交换。

## 排序方式

<!--more-->

至于选择那种排序方式，InsertionSort, MergeSort还是QuickSort，因为ECMA规范里并无指定，所以全看是哪种浏览器了。甚至在同一浏览器下，根据排序对象的长度不同，选择的排序方式也可能不同。

据了解，Firefox主要用MergeSort，Safari主要中SelectionSort，Chrome的V8引擎主要用QuickSort。

以Chrome的V8为例，当数组个数小于等于10的时候，会选择用InsertionSort排序，当大于10时，会首先用QuickSort(或QuickSort的变体)分段，当子数组个数小于10的时候可能又会使用InsertionSort。

## 稳定排序与不稳定排序

所谓稳定与不稳定，是指compareFunc有返回0的情况的时候，在排序结果中这些返回0的数据之间的相对位置，是否和最初数组中的相对位置保持一致。

InsertionSort和MergeSort是稳定的排序算法，QuickSort不是，所以有时候会需要注意这点。对于有些严格的情况，还是自己实现排序算法比较保险。

## 可以窥见排序过程么？

其实这个compareFunc在排序的过程中会被无数次地调用，排序算法在需要比较的时候，就会调用这个compareFunc。来个例子（执行浏览器是Mac OS X下的Chrome 41.0.2272.89 (Official Build) ）

{% code lang:js %}
var list = [4,2,6,7,1,9,3,5,8];
list.sort(function(a, b) {
  console.log(a, b); // 输出每次被调用是的a，b
  return a > b ? 1 : -1;
});
{% endcode %}

输出结果是

{% code lang:js %}
4 2
4 6
6 7
7 1
6 1
4 1
2 1
7 9
9 3
7 3
6 3
4 3
2 3
9 5
7 5
6 5
4 5
9 8
7 8
{% endcode %}

仔细看看就会发现这比较的前后顺序是符合InsertionSort的套路的。

当你把list的个数弄成大于10个时，你会发现那时就有QuickSort的迹象了。

- References:
  1. [Sorting - We're Doing It Wrong](http://blog.rodneyrehm.de/archives/14-Sorting-Were-Doing-It-Wrong.html)
