title: 一道能综合考察js的Array方法的题
date: 2015-03-13 17:48:06
categories:
- IT
tags:
- javascript
- array
- slice
- splice
- unshift
---
leetcode部分题目支持用javascript来答了，去尝试了一下，结果有[一道题](https://leetcode.com/problems/rotate-array/)，看起来很简单，写起来却总是忘记这忘记那的。

写完之后才觉得这一题其实挺考验你对Array的若干个常用方法的掌握程度的。

我写了下面的解答，通过了所有测试。

{% code lang:js %}
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    var n = nums.length;
    var shift = k % n;
    if (k === 0 || n === 0 || shift === 0) return;
    var firstHalf = nums.slice(n - shift);
    nums.splice(n - shift, shift);
    Array.prototype.unshift.apply(nums, firstHalf);
};
{% endcode %}

回过头来看

1. 需要注意各种边界和极端情况，做出特殊处理。

2. .slice()方法两个参数分别是start和end，不会对原数组更改，返回的是一个新的复制的数组。

3. 而.splice()方法的前两个参数一个是start，另一个是deleteCount，即删除的个数，会对原数组更改，更改后的是去掉指定范围内的元素之后的数组。

4. 想往一个数组前部插入另一个数组时，可以用unshift()方法，但是要注意的是，不能以数组形式传入第一个参数，因为这样会被当成一个数组元素，而不是若干个元素插入数组前部。所以这里我用到了apply方法，利用了它的第二个参数的特点。
