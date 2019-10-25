---
title: css 属性
date: 2018-10-11
categories: web 前端
tags:
- css
- white-space
---
css 的属性有很多，对应的属性值也有很多，下面讲述 css 属性中的 空格处理 及 弹性布局，包括 空格规则、空格字符、white-space 属性等等...
<!-- more -->

## 一、空格处理

### 1、空格规则
HTML 代码的空格通常会被浏览器忽略。
```
<p>  hello  world  </p>
```
上面是一行 HTML 代码，文字的前部、内部和后部各有两个空格。
浏览器的输出结果如下: hello world

可以看到，文字的前部和后部的空格都会忽略，内部的连续空格只会算作一个。这就是浏览器处理空格的基本规则。
如果希望空格原样输出，可以使用`<pre>`标签。
```
<pre>  hello  world  </pre>
另一种方法是，改用 HTML 实体&nbsp;表示空格。
<p>&nbsp;&nbsp;hello&nbsp;&nbsp;world&nbsp;&nbsp;</p>
```

### 2、空格字符
HTML 处理空格的规则，适用于多种字符。除了普通的空格键，还包括制表符（\t）和换行符（\r和\n）。
浏览器会自动把这些符号转成普通的空格键。

```
<p>hello
world</p>
```
上面代码中，文本内部包含了一个换行符，浏览器视同为空格，输出结果如下: hello world

所以，文本内部的换行是无效的（除非文本放在`<pre>`标签内）。
```
<p>hello<br>world</p>
```
上面代码使用<br>标签显式表示换行

### 3、CSS white-space 属性

HTML 语言的空格处理，基本上就是直接过滤。这样的处理过于粗糙，完全忽视了原始文本内部的空格可能是有意义的。

CSS 提供了一个white-space属性，可以提供更精确一点的空格处理方式。该属性共有六个值，除了一个通用的inherit（继承父元素），下面依次介绍剩下的五个值。

* 3.1 white-space: normal

    white-space属性的默认值为normal，表示浏览器以正常方式处理空格。
    ```
    html:
        <p>  hellohellohello hello
        world
        </p>
    style:
        p {
            width: 100px;
            background: red;
        }
    ```
    上面代码中，文本前部有两个空格，内部有一个长单词和一个换行符。

    文首的空格被忽略。由于容器太窄，第一个单词溢出容器，然后在后面一个空格处换行。文本内部的换行符自动转成了空格。

* 3.2 white-space: nowrap

    white-space属性为nowrap时，不会因为超出容器宽度而发生换行。
    ```
    p {
        white-space: nowrap;
    }
    ```
    所有文本显示为一行，不会换行。

* 3.3 white-space: pre

    white-space属性为pre时，就按照`<pre>`标签的方式处理。
    ```
    p {
        white-space: pre;
    }
    ```
    上面结果与原始文本完全一致，所有空格和换行符都保留了。

* 3.4 white-space: pre-wrap

    white-space属性为pre-wrap时，基本还是按照`<pre>`标签的方式处理，唯一区别是超出容器宽度时，会发生换行。
    ```
    p {
        white-space: pre-wrap;
    }
    ```
    文首的空格、内部的空格和换行符都保留了，超出容器的地方发生了折行。

* 3.5 white-space: pre-line

    white-space属性为pre-line时，意为保留换行符。除了换行符会原样输出，其他都与white-space:normal规则一致。
    ```
    p {
        white-space: pre-line;
    }
    ```
    除了文本内部的换行符没有转成空格，其他都与normal的处理规则一致。这对于诗歌类型的文本很有用。
        
## 二、弹性布局 flex

### 1、align-content 父元素属性 - 纵向

    - flex-start
    - flex-end
    - center
    - space-between
    - space-around
    - stretch

### 2、align-self 子元素属性 - 纵向

    - flex-start  - 居上对齐
    - flex-end  - 居下对齐
    - center  - 居中对齐
    - baseline  - 基线对齐
    - stretch  - 填充容器（默认属性，仍保持自身的最大最小宽度）

## 三、纯 CSS 滚动条滚动进度效果

```html
<html>
<head></head>
<body>
    <h1>不可思议的纯 CSS 进度条效果</h1>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <ul>
        <li>1.xxxxxxxxxxxxxxxxxxxxx</li>
        <li>2.xxxxxxxxxxxxxxxxxxxxx</li>
        <li>3.xxxxxxxxxxxxxxxxxxxxx</li>
        <li>4.xxxxxxxxxxxxxxxxxxxxx</li>
    </ul>

    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>

    <p>在继续阅读下文之前，你可以先缓一缓。尝试思考一下上面的效果或者动手尝试一下，不借助 JS ，能否巧妙的实现上述效果。</p>
        
    <p>OK，继续。这个效果是我在业务开发的过程中遇到的一个类似的小问题。其实即便让我借助 Javascript ，我的第一反应也是，感觉很麻烦啊。所以我一直在想，有没有可能只使用 CSS 完成这个效果呢？</p>
    </body>
</html>
```

```css
body {
    position: relative;
    padding: 50px;
    font-size: 24px;
    line-height: 30px;
    background-image: linear-gradient(to right top, #ffcc00 50%, #eee 50%);
    background-size: 100% calc(100% - 100vh + 5px);
    background-repeat: no-repeat;
    z-index: 1;
}

body::after {
    content: "";
    position: fixed;
    top: 5px;
    left: 0;
    bottom: 0;
    right: 0;
    background: #fff;
    z-index: -1;
}

/**
 * Unrelated css
 */

h1 {
    font-size: 32px;
    line-height: 60px;
}

ul {
    margin-top: 30px;
}

p {
    font-size: 24px;
    line-height: 30px;
    margin-top: 30px;
}
```

## 四、修改指定元素的滚动条样式

body, div, textarea, iframe 均可修改其滚动条样式

```css
body, div, textarea, iframe {
    /*三角箭头的颜色*/
    scrollbar-arrow-color: #bbb;
    /*滚动条滑块按钮的颜色*/
    scrollbar-face-color: #999;
    /*滚动条整体颜色*/
    scrollbar-highlight-color: #999;
    /*滚动条阴影*/
    scrollbar-shadow-color: #999;
    /*滚动条轨道颜色*/
    scrollbar-track-color: #eee;
}

/* 最大高度，修改滚动条样式 */
.popover-scroll-size {
    max-width: 270px;
    max-height: 250px;
    overflow: hidden auto;
    margin-right: -10px;
    padding-right: 10px;

    /* 滚动条整体样式 滚动条宽度 */
    &::-webkit-scrollbar {
        width: 8px; /* 对垂直流动条有效 */
        height: 1px; /* 对水平流动条有效 */
    }
    /* 定义滑块颜色、内阴影及圆角 */
    &::-webkit-scrollbar-thumb {
        border-radius: 4px;
        background: #999; /*#EAEAEA*/
    }
    /* 定义滚动条的轨道颜色、内阴影及圆角 */
    &::-webkit-scrollbar-track {
        border-radius: 4px;
        background: #f2f2f2; /*#FFFFFF*/
    }
    /* 定义两端按钮的样式 */
    &::-webkit-scrollbar-button {
        background-color: #999;
    }
    /* 定义右下角汇合处的样式 */
    &::-webkit-scrollbar-corner {
        background: #666;
    }
}
```

### 六、参考链接

[css-tricks](https://css-tricks.com/almanac/properties/w/whitespace/)
[css 小技巧一](https://segmentfault.com/a/1190000003931851)
[css 小技巧二](https://segmentfault.com/a/1190000003932970)
