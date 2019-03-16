# 比较少用的语义化标签

## [blockquote](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/blockquote)

段落级引述内容，用于引述较长的内容

```html
<blockquote cite="https://www.huxley.net/bnw/four.html">
    <p>Words can be like X-rays, if you use them properly – they'll go through anything. You read and you're pierced.</p>
</blockquote>
```

其中 `cite` 用于标明引述资源

## [q](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/q)

行内引述内容，用于引述较短的内容

```html
<p>According to Mozilla's website,
  <q cite="https://www.mozilla.org/en-US/about/history/details/">
    Firefox 1.0 was released in 2004 and became a big success.
  </q>
</p>
```

其中 `cite` 用于标明引述资源，与 `blockquote` 相同

## [cite](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/cite)

引述的作品名等

```html
<p>
  More information can be found in <cite>[ISO-0000]</cite>.
</p>
```

## [time](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/time)

用于描述日期

```html
<p>
  The Cure will be celebrating their 40th anniversary on <time datetime="2018-07-07">July 7</time> in London's Hyde Park.
</p>
```

其中，`time` 的文本节点内容应为面向人类的日期表示，而属性 `datetime` 中应为面向机器的日期表示

## [dfn](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dfn)

用于表示一个名词的定义

```html
<p>
  A <dfn id="def-validator">validator</dfn> is a program that checks for syntax errors in code or documents.
</p>
```

## [pre](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/pre)

表示文本节点的内容不需要浏览器排版了，直接原样显示文本内容，其中的空格也会照样输出

## [samp](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/samp)

表示内容是计算机程序输出的样本，会默认使用浏览器的 `monospace` 字体

## [code](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/code)

表示内容是计算机程序代码，默认使用浏览器的 `monospace` 字体





