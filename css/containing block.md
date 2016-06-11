containing block 的定义：
元素的 box(es) 的位置和大小有时是通过相对一个特定的矩形来计算的。这个特定的矩形就叫做
这个元素的 containing block 。元素的 containing block 的定义如下：

- 根元素所在的 containing block 是一个矩形，这个 containing block 叫做初始化 containing block。
对 continuous media 来说，containing block 的尺寸和 viewport 的尺寸是一样大的，并被固定在画布（什么是画布）的原点；
对 paged media 来说，containing block 就是 page area 。初始化 containing block 的 'direction' 属性和根元素的 direction 是一样的。
- 对其他元素来说，如果元素的 position 是 'relative' 或 'static' ，containing block 是由最近的祖先 block container box 的 content 边组成的。
- 如果元素的 position 属性值是 fixed ，如果是 continuous media 的话，containing block 是由 viewport 建立的，如果是 paged media 的话，containing block 是由 page area 建立的。
- 如果元素的 position 属性值是 absolute ，containing block 是由 position 属性值是 absolute 或者 relative 或者是 fixed 的最近的祖先，以下面的方式建立的：
  1、祖先是 inline 元素的情形下，containing block 是由环绕着第一个和最后一个的 inline boxes 的 padding boxes 的边界 box 。
  2、其他情形下，containing block 是由祖先的 padding 边组成的。
  如果没有这样的祖先的话，containing block 就是初始化 containing block 。
在 paged media 中，一个绝对定位的元素相对于它的 containing block 定位而忽略了任何的分页（就好像是文档是连续的一样）。这个元素可能相继地被分为多个页面。
对于绝对定位的内容来说，解析一个页面上的位置而不是被布局的页面（当前页），或者是解析当前页的一个位置，当前页是已经渲染好了，准备打印中，打印机可能替换内容。
- 当前页的另外一个位置
- 随后的一页
- 可能忽略它

> 注意，一个 block-level 元素被分割为几个页面，这几个页面每个页面可能都有不同的宽度。这些宽度可能是 device-specific 限制的。

计算 widths 和 margins



