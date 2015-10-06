title: LeetCode中的Single Number III这道题的思路
date: 2015-10-06 09:07:48
categories:
- IT
tags:
- javascript
- algorithm
- leetcode
- bit manipulation
---
LeetCode中有道题，我觉得还蛮有意思的，摘录过来和大家分享一下思路
<blockquote style="text-align:left">
Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.

For example:

Given nums = [1, 2, 1, 3, 2, 5], return [3, 5].

Note:
The order of the result is not important. So in the above example, [5, 3] is also correct.
Your algorithm should run in linear runtime complexity. Could you implement it using only constant space complexity?
</blockquote>

大致意思是有一列数，其中除了两个不同的数以外，其他数都是成双成对的，现在目标是需要找出这两个特别的数，并且要求算法的达到线性时间复杂度和常数空间复杂度。

乍看之下似乎不太可能，但是知道解法之后我们就会发现，其实很多信息以及蕴藏在数字自身当中，只是我们没有利用起来。而这些所谓的信息，其实就是这些数字的二进制形式中包含的一些特点。

下面是我用js实现的解法

{% code lang:js %}
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var singleNumber = function(nums) {
    var xor = nums[0];
    for (var i = 1; i < nums.length; i++) {
        xor ^= nums[i];
    }

    // choose one number to indicate which bit of the two desired numbers are different
    var diffBitMarker = 1;
    while ((diffBitMarker & xor) !== diffBitMarker) {
        diffBitMarker = diffBitMarker << 1;
    }

    var target1 = 0;
    // choose number that has bit 1 at the certain position specified by diffBitMarker
    for (var j = 0; j < nums.length; j++) {
        if ((diffBitMarker & nums[j]) === diffBitMarker) {
            target1 ^= nums[j];
        }
    }

    target2 = xor ^ target1;

    return [target1, target2];
};
{% endcode %}

主要提两个要点：

1. 相同的数字经过XOR运算之后的值为0
2. 两个不同的数字，它们的二进制值必然至少有一位上的数值一个是0，一个是1，依据这个可以把这一列数按这个标准分为两堆，使得这两个数分别位于两堆数中，这样，每一堆数中，就只有唯一一个“落单”的数了，根据上面的要点1，就能把这个数揪出来。

另外值得一提的是我在应用Javascript的位操作符时遇到操作符优先级的问题，Bitwise XOR（其他如Bitwise AND、Bitwise OR、Logical AND、Logical OR）的操作优先级均比比较运算符低，因此 (diffBitMarker & xor) !== diffBitMarker 这语句里的括号是不能缺省的，否则实际的执行顺序就不是我们所期望的了。

- References:
  1. [Operator precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
