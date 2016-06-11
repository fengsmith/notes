视觉格式模型

# 9.1 视觉格式模型介绍
本章和下一章会描述视觉格式模型：user agents 是怎样为视觉媒体处理 dom 树的。
在视觉格式模型中，dom 树中的每个元素根据 box model 产生 0 个或多个 boxes 。
这些 boxes 的布局是由：
- box 尺寸和类型
- positioning scheme(normal flow, float, and absolute positioning)
- dom 树元素之间的关系
- 外部信息（例如，viewport 大小，图片本身的尺寸等）

## 9.1.1 视区(viewport)
continuous media 的 user agents 通常会为用户提供一个视区（一个窗口或者是屏幕上的其他的可视区域），
通过视区用户可以访问文档。视区的大小发生改变时，user agents 可能会改变文档的布局（查看初始化 containing block）。

画布是渲染 dom 的地方，当视区比画布区域小的时候，user agents 会提供一个滚动机制。每个画布最多只有一个视区，但是
user agents 可能会渲染多个画布（例如，为同一个 dom 提供不同的视图）。

## 9.1.2 containing blocks
在 CSS 2.1 中，许多 box 位置和大小是用一个矩形 box 的相应的边计算得到的。这个矩形 box 叫做 containing box 。
通常，生成的 boxes 作为后代 boxes 的 containing blocks ，我们说一个 box 为它的子孙建立了 containing block 。
一个 box 的 containing block 意思是这个 box 位于的 containing block 而不是这个 box 产生的 containing block 。

每个 box 都会被给定一个位置，这个位置与这个 box 的 containing block 相关，但是这个 box 并不会被 containing block 所限定；这个 box 可能会溢出（overflow）。
containing block 的尺寸是怎样计算出来的详细情况将会在下一章中描述。

## 9.2 控制 box 的生成
下面的部分描述了 CSS 2.1 可能产生的 boxes 的种类，在视觉模型中，box 的种类会部分地影响其行为。
下面描述的 'display' 属性定义了 box 的种类。
### 9.2.1 block-level 元素 和 block boxes
在视觉上被格式化为块状（例如，段落）的那些源文档的元素就是 block-level 元素。可以设置一个元素的 'display' 属性值为：'block'、'list-item'或'table'从而使之成为一个 block-level 元素。

block-level boxes 是参与到 block formatting context 的 boxes 。每个 block-level 元素产生一个最重要的 block-level box ，这个 block-level box 包含了子孙 boxes 和 generated content ，这个 block-level box 还涉及到 any positioning scheme 。
一些 block-level 元素除了产生一个最重要的 block-level box 之外还可能产生一些额外的 boxes ：'list-item' 元素。These additional boxes are placed with respect to the principal box.
 
除了 table boxes（将会在后面的章节中描述），replaced 元素，一个 block-level box 也是一个 block container box 。
一个 block container box 要么只包含 block-level boxes 要么建立一个 inline formatting context因此只包含 inline-level boxes 。并不是所有的
block container boxes 都是 block-level boxes： non-replaced inline blocks 和 non-replaced table cells 都是 block containers 但是它们并不是 block-level boxes 。同时是 block-level box 和 block container box 的所有的 boxes 叫做 block boxes 。

在不引起歧义的情况下，"block-level box"，"block container box"，和"block box"都简称为 "block" 。

#### 9.2.1.1 匿名 block boxes
下面是 dom 中的一个 div ：

    <div>
      Some text
      <p>More text</p>
    </div>
      
（假设 div 和 p 的 display 的属性值都是 block），div 看起来同时有 inline content 和 block content 两种 content 。为了更容易的定义格式
，我们假设在 "Some text" 的周围环绕着一个匿名 block 。

换句话说：如果一个 block container box （例如，上面的为 div 生成的 block container box）内部有一个 block-level box （例如，上面的 p），然后我们强迫这个 block container box 内部只能有 block-level boxes 。

当一个 inline box 包含一个 in-flow block-level box 时，这个 inline box （和位于同一个 line box 内的 inline 祖先）被破坏掉了，围绕着 block-level box (和任何连续或只被 collapsible 空格 and/or out-of-flow 元素的兄弟元素)，把 inline box 分离为两个 boxes （即使两条边都是空），block-level box(es) 的每边一个。
在破裂处的前面的 line boxes 和破裂处后面的 line boxes 被封闭于匿名的 block boxes，那个 block-level box 成为了这些匿名 boxes 的一个兄弟。当这样的一个 inline box 被相对定位所应影响时，任何结果的转换也会影响 inline box 内部的 block-level box 。

如果有下列的这些 CSS 规则，这种模式就会应用到下面的例子中：
      
    p    { display: inline }      
    span { display:block }

上面的 CSS 规则应用于下面的这个 HTML 文档：
    
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
    <HEAD>
    <TITLE>Anonymous text interrupted by a block</TITLE>
    </HEAD>
    <BODY>
    <P>
    This is anonymous text before the SPAN.
    <SPAN>This is the content of SPAN.</SPAN>
    This is anonymous text after the SPAN.
    </P>
    </BODY>
    
P 元素包含一块匿名文本（C1）紧接着是一个 block-level 元素再接着是另一个匿名文本块（C2）。
结果 boxes 将是一个 block box 代表了 BODY ，包含一个环绕着 C1 的匿名 block box，SPAN block box，和另外一个环绕着 C2 的匿名 block box 。
 
匿名 boxes 的属性是从环绕着它的非匿名 box 继承而来的（例如，在9.2.1.1节标题"匿名 block boxes" 下面的这个例子中的 DIV 的 box）。非继承属性有它们自己的初始值。例如，匿名 box 的字体继承自 DIV ，但是 margins 却是 0 。

Properties set on elements that cause anonymous block boxes to be generated still apply to the boxes and content of that element. 例如，在上面的例子中，如果给 P 元素设置一个 border ，将会在 C1(行末是开着的)和 c2(行首是开着的) 周围绘制 border 。

有些 user agents 对 inlines containing blocks 上的 borders 给出了另外的实现方式，例如，通过包裹 "匿名 line boxes"内部的嵌套 blocks，因此在这些 boxes 周围绘制 inline borders 。由于，CSS1 和 CSS2 没有定义这个行为，CSS1-only 和 CSS-only 的 user agents 可能会实现这种模型并且仍然宣称遵守 CSS2.1 的这部分规范。
这并不适用于本规范发布之后开发的 user agents 。

匿名 block boxes 会被忽略，当解析百分比的值时可能会引用到它：最近的非匿名祖先 box 会被替代。例如，上面的例子中的 DIV 内部的匿名的 block box 的孩子需要知道它的 containing block 的高度来解析一个百分比的高度，然后它将会用 DIV 形成的 containing block 的高度而不是匿名的 block box 。

### 9.2.2 inline-level 元素和 inline boxes
Inline-level elements are those elements of the source document that do not form new blocks of content;
内容是以行状分布（例如，在一个段落内强调一块文本，inline 图片等）。'display' 属性的下列的值使一个元素成为 inline-level 元素：'inline'，'inline-table'，'inline-block'。inline-level 元素产生 inline-level boxes，inline boxes 是参加到一个 inline formatting context 的元素。

一个 inline box 是一个 inline-level box 同时其内容参与到 containing inline formatting context 。一个具有 'display' 属性的值是 'inline' 的 non-replaced 元素产生一个 inline box 。inline-level boxes 并不是 inline boxes （例如，replaced inline-level 元素，inline-block 元素，inline-table 元素），这样的 inline-level boxes 叫做原子 inline-level boxes ，因为它们是作为一个单独地不透明的盒子参与到它们的 inline formatting context 中的。

#### 9.2.2.1 匿名 inline boxes
被一个 block container（而不是 inline） 元素直接包含在内部的任何文本都必须作为一个匿名 inline 元素来对待。

有着像下面这样的 HTML 标记的 dom:

    <p>Some <em>emphasized</em> text</p>

<P> 产生一个 block box，这个 block box 内部有几个 inline boxes 。inline 元素（<em>）为 "emphasized" 产生了一个 inline box，但是其他的 boxes（"Some" 和 "text"）是被 block-level 元素（<p>）产生的 inline boxes 。后者叫匿名 inline boxes ，因为它们没有与之相关联的 inline-level 元素。

这样的匿名 inline boxes 从它们的父 block box 继承了可继承的属性。非可继承的属性有它们自己的初始值。在例子中，匿名 inline boxes 的颜色继承自 P ，但是其背景是透明的。
    
空格内容根据 'white-space' 属性不产生任何匿名 inline boxes 的原则，将会相继地被收缩到空。

匿名 box 的类型在上下文清楚的情况下，在本规范中匿名 inline boxes 和匿名 block boxes 都简称为匿名 boxes 。

在格式化表格的时候会出现更多种类的匿名 boxes 。
    
### 9.2.3 run-in boxes
    
### 9.2.4 'display' 属性
    
'display'：
这个属性的值有以下的意思：
    
block
    引起一个元素产生一个 block box 。
inline-block
    引起一个元素产生一个 inline-level block container 。一个 inline-block 的内部被作为一个 block box 来格式化，元素本身被作为一个原子 inline-level box 来格式化。
inline
    引起一个元素产生一个或多个 inline boxes 。
list-item
    引起一个元素（例如，HTML 中的 LI 元素）产生一个最重要的 block box 和一个 marker box 。想要查看 list 格式化的例子，可以查阅 lists 部分。
none
    引起一个元素不会出现在格式化结构中（例如，在视觉媒体中元素不产生任何 boxes 并且对布局没有任何影响）。后代元素也不会产生任何 boxes ；元素和它的内容被格式化结构完全移除。这个行为不会通过设置其后代的 display 属性而被覆盖。



