title: 平方根迭代算法
date: 2014-11-25 17:12:36
categories:
- IT
tags:
- algorithm
- math
---
在[学习golang](http://golang.org/doc/code.html)的时候教程里有个例子引起了我的注意

{% code lang:go %}
// Package newmath is a trivial example package.
package newmath

// Sqrt returns an approximation to the square root of x.
func Sqrt(x float64) float64 {
	z := 1.0
	for i := 0; i < 1000; i++ {
		z -= (z*z - x) / (2 * z)
	}
	return z
}
{% endcode %}

代码在算平方根的时候，不是用了现成的类似Math.sqrt()之类的方法，而是直接用最基本的加减乘除代码来实现的，我的第一感觉就是回想起大学里学的《数值计算》这门课程，隐约记得里面有什么牛顿、莱布尼兹、迭代算法等一些列乱七八糟的东西，想到这儿我知道我已经完全忘了大学里学的东西了，于是乎去网上找了相关重新复习了一遍，这一查不得了，我突然觉得当时大学里讲得这些晦涩难懂的东西竟然这么有意思～

其实求平方根可以用下面的数学语言来描述

<!--more-->

**给定一个x>0，找到一个y，使得y<sup>2</sup>=x成立**

虽然描述很简单，但是我们改怎么做呢？其实只需要一个小小的变换，如果y<sup>2</sup>=x，那么x/y=y,这样就给了我们一种启示：

- 猜测一下y的值，比如说y的值为g
- 计算一下x/g的值
- 如果x/g和g的值特别接近，那么就可以认为这个g就是我们想要的答案；否则，继续寻找一个更优的解

这样一想问题就简化了。对于判断两个值是否特别接近，应该没什么难度，判断两个值的差的绝对值是否小于某一个下限。对于需要找新值这个问题，解决办法就很多了。最直观的估计就是两个值相加取平均了，即（x/g+g)/2。但本文要重点讲的是著名的牛顿-拉弗逊迭代算法。

牛顿-拉弗逊迭代算法不仅能解决平方根问题，它能解决任意正数次方根。求根问题可以用下图表示

![](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/rootFinder.gif)

换句话说，我们希望找到一个x，使得x<sup>n</sup>=w，也即x<sup>n</sup>-w=0。所以我们其实是希望找到x值使得函数f(x)=x<sup>n</sup>-w的值等于零，从图形上看就是这个函数的曲线与x轴相交的那个点的x值。

![](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/newton1.gif)

然后就随便猜测一个点(g,f(g))，如果猜测的值跟实际解偏差较大，则采用下图所示方法获得新的猜测点的x方向的值

![](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/newton2.gif)

这条直线是曲线在该点处的切线，它的斜率为f′(g)（如果这里不明白，请自行查阅更多资料）

下面重点来了，这个具体的迭代过程是怎样的

![](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/new-guess.gif)
![](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/newguess-formula.gif)

根据上面的推理，我们得到了

g′ = g - f(g) / f′(g)

下一个猜测点的x轴方向的值g′总是能根据上面的公式迭代出来

如果说

f(g) = g<sup>n</sup> - w

那么

f′(g) = ng<sup>n-1</sup>

另外，判断两个值是否足够接近，其实与其设定一个特别特别小的数，还不如采用这样一种判断方式：当每两次迭代的x值足够近的时候，也就是说新的迭代值比旧迭代值小一定的倍数时，比如说0.00001倍时，可以近似认为新旧迭代值已经足够接近了。

下面现学现用，用go语言写了个package，以供参考

{% code lang:go %}
package newmath

import (
    "fmt"
    "math"
)

func f(w float64, n float64, g float64) float64 {
    return math.Pow(g, n) - w
}

func fPrime(w float64, n float64, g float64) float64 {
    return n*math.Pow(g, n-1)
}

func closeEnough(newg float64, g float64) bool {
    return math.Abs(newg - g) < math.Abs(g * 0.00001)
}

func findRoot(w float64, n float64, g float64) float64 {
    fmt.Printf("guessing %v\n", g)
    newg := g - f(w, n, g) / fPrime(w, n, g)
    fmt.Printf("new %v\n", newg)
    if (closeEnough(newg, g)) {
        return newg
    } else {
        return findRoot(w, n, newg)
    }
}

func Root(w float64, n float64) float64 {
    return findRoot(w, n, 1)
}
{% endcode %}

- References:
	1. [Recursive Algorithms: Square Root](http://www.cse.wustl.edu/~kjg/cse131/Notes/SquareRoot/sqrt.html)
	2. [How to Write Go Code](http://golang.org/doc/code.html)
