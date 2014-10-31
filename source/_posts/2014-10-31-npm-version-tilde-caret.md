title: npm模块版本的~和^标识
date: 2014-10-31 12:18:38
categories:
- IT
tags:
- npm
- nodejs
---
* npm模块的版本由三个部分组成[major, minor, patch]，例如1.3.10。

版本的范围的标识语法

X.Y.Z - A.B.C (Hyphen Ranges)

和

1.2.x， 1.2.* (X-Ranges)

这两种形式可能都是比较一目了然的，另外两种形式可能就比较不直观了，我经常忘记具体的含义。

这两种分别是～(Tilde Ranges)和^(Caret Ranges)

### Tilde Ranges

{% code %}
~1.2.3          >=1.2.3  <1.3.0
~1.2            >=1.2.0  <1.3.0 (等价于1.2.x)
~1              >=1.0.0  <2.0.0 (等价于1.x)
~0.2.3          >=0.2.3  <0.3.0
~0.2            >=0.2.0  <0.3.0 (等价于0.2.x)
~0              >=0.0.0  <1.0.0 (等价于0.x)
~1.2.3-beta.2   >=1.2.3-beta.2 <1.3.0
{% endcode %}

* 当patch部分指定了，允许patch部分范围的变动
* 当patch未指定时，允许minor部分范围的变动

### Caret Ranges

{% code %}
^1.2.3          >=1.2.3  <2.0.0
^0.2.3          >=0.2.3  <0.3.0
^0.0.3          >=0.0.3  <0.0.4
^1.2.3-beta.2   >=1.2.3-beta.2 <2.0.0
^0.0.3-beta.2   >=0.0.3-beta.2 <0.0.4
{% endcode %}

* 允许[major, minor, patch]从左起第一个不是0的部分之后的部分的范围变动


- References:
    1. [semver](https://www.npmjs.org/doc/misc/semver.html)