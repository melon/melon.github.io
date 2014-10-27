title: "form标签的enctype属性"
date: 2014-08-26 15:32
categories:
- IT
tags:
- frontend
- html
---
我之前在制作[专治强迫症头像生成器ヽ(✿ﾟ▽ﾟ)ノ](http://www.bpzufang.com/ocd)的时候，遇到了一个和html的form标签有关的问题，最初是百思不得其解，后来总算搞清楚了。

有必要说下背景，后端是新起了一个express服务，专门用来实现这个小工具，从而尽量和原网站功能分离开来。express用的是较早的第2代版本，为了实现对上传文件的解析，引入了express.bodyParser()中间件，它的功能概括来说就是帮助获取到用户上传的文件的信息，并将这些信息保存到req.files对象中去，以便后续使用。

{% code lang:js %}
app.use(express.bodyParser({
    keepExtensions: true,
    uploadDir: '/tmp'
}));
{% endcode %}

然而我遇到的问题是，尽管客户端上传文件了，但是服务器端死活都获取不到信息，/tmp文件夹下没有对应的上传文件，req.files仍是一个空对象。我的前端form标签是这么写的

{% code lang:html %}
<form action="/ocd/upload" id="picSubmitForm" method="POST">
    <input id="upload-input" type="file" name="pic">
</form>
{% endcode %}

乍看之下似乎没问题，我是很久之后才意识到，form在传文件时还需要加上一个enctype属性才行，并且值必须是multipart/form-data。只有按这种类型编码之后，传给后端，后端才能顺利地解析出这个刚上传的文件。

为什么这么神奇？这个小小的改动为何是最终结果发生这样的变化？

首先，我们先了解一下html中form标签的enctype属性的可选值。

<table style="margin: 10px;">
    <thead>
        <tr>
            <td style="width:40%">值</td>
            <td style="width:60%">描述</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>application/x-www-form-urlencoded</td>
            <td><strong>默认值</strong>。所有字符在发送之前都被转码（空格被转换成“+”号，特殊字符被转换“%xx”的形式）。</td>
        </tr>
        <tr>
            <td>multipart/form-data</td>
            <td>所有字符都不进行转码。上传文件时此属性值是<strong>必须的</strong>。</td>
        </tr>
        <tr>
            <td>text/plain</td>
            <td>空格被转换成“+”号，但特殊字符不转换。</td>
        </tr>
    </tbody>
</table>
<br>

先来分析默认的application/x-www-form-urlencoded，假设form表单是这个样子的

{% code lang:html %}
<form action="/xxx" id="xxx" method="POST">
    <input type="text" value="ff f" name="ab">
    <input type="text" value="g gg" name="c e">
    <button type="submit">submit</button>
</form>
{% endcode %}

这种情况下提交表单之后，浏览器发送给服务器的form数据内容应该是下面这个样子（在Chrome的Developer tools里可以看到）

{% code lang:html %}
ab=ff+f&c+e=g+gg
{% endcode %}

可以看到空格都已经用“+”转换。另外，在实际传输过程中，这些数据占用了16字节（按utf-8格式编码成二进制）。接下来我们加一特殊字符

{% code lang:html %}
<form action="/xxx" id="xxx" method="POST">
    <input type="text" value="ff f" name="ab">
    <input type="text" value="g gg©" name="c e">
    <button type="submit">submit</button>
</form>
{% endcode %}

转码后

{% code lang:html %}
ab=ff+f&c+e=g+gg%C2%A9
{% endcode %}

可以看到多了“%C2%A9”这些字符。那“%C2%A9”到底是怎么出来的？这个就涉及到Unicode和UTF-8的一些知识了，我们以后再详细讲。现在稍微解释一下：©这个字符的Unicode码是00A9，通常写作U+00A9，它的UTF-8形式就是C2 A9。所以，实际上，form如果遇到特殊字符，就会利用这个字符的UTF-8形式，辅以若干“%”，转换成最终传输前的形式。细心一点我们就会发现，©这个字符本来以UTF-8形式传输只需占用两个字节，经过form的urlencode处理之后，变成了“%C2%A9”共6个字节。用tcpdump抓包工具侦听，与之前的例子相比验证确实数据增加了6个字节，进一步说明©这个特殊字符在传送之前是经过multipart/x-www-form-urlencoded转换了的。

接着，我们再分析一下multipart/form-data。针对multipart/x-www-form-urlencode增加字节数的问题，当传送的数据量比较大的时候，为了提高效率，应该用multipart/form-data。具体的原因就在于这种enctype对上传文件的内容完全不进行转码，传输文件所耗费的流量和文件大小是完全一样的。举两个例子，

{% code lang:html %}
<form action="/ocd/upload" id="picSubmitForm" method="POST" enctype="multipart/form-data">
    <input type="file" name="text">
    <button type="submit">submit</button>
</form>
{% endcode %}

当form表单是如上形式，根据Chrome Developer tools，form提交的数据形式如下所示，有Content-Disposition, Content-Type等类型字段，可以用来为每个部分标识。另外，文件的具体内容并未直接展示出来。

{% code lang:html %}
------WebKitFormBoundaryVlNvPmPc1JCG0f6B
Content-Disposition: form-data; name="text"; filename="goodnight.txt"
Content-Type: text/plain


------WebKitFormBoundaryVlNvPmPc1JCG0f6B--
{% endcode %}

当form表单中不只含一个name项时

{% code lang:html %}
<form action="/ocd/upload" id="picSubmitForm" method="POST" enctype="multipart/form-data">
    <input type="file" name="text">
    <input type="hidden" value="晚安" name="hidden-text">
    <button type="submit">submit</button>
</form>
{% endcode %}

则有

{% code lang:html %}
------WebKitFormBoundarydbbXeqOBAJxmyvi2
Content-Disposition: form-data; name="text"; filename="goodnight.txt"
Content-Type: text/plain


------WebKitFormBoundarydbbXeqOBAJxmyvi2
Content-Disposition: form-data; name="hidden-text"

晚安
------WebKitFormBoundarydbbXeqOBAJxmyvi2--
{% endcode %}

第三种text-plain的enctype，由于用得比较少，在这就不赘述了。

- References:
    1. [HTML \<form\> enctype Attribute](http://www.w3schools.com/tags/att_form_enctype.asp)
    2. [application/x-www-form-urlencoded or multipart/form-data?](http://stackoverflow.com/questions/4007969/application-x-www-form-urlencoded-or-multipart-form-data)