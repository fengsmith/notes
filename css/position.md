relative：相对的是元素正常应该出现的位置。
向左：负值
向右：正值
向上：负值
向下：正值

相对定位会保留原来的位置，由上到下先渲染正常流的元素最后从上到下渲染相对布局的元素。由左到右先渲染正常流的元素最后从从到右再渲染相对布局的元素。

absolute：相对最近的祖先定位元素，如果没有祖先定位元素则相对于 body （画布上的某一个店）。

fixed：固定在页面上的某处，不受滚动屏幕的影响。相对于 viewPort 上的某一个位置（点）进行平移（视区上的某个点）。


1）块状元素在position(relative/static)的情况下width为100%，但是设置了position: absolute之后，会将width变成auto（会受到父元素的宽度影响）。

2）元素设置了position: absolute之后，如果没有设置top、bottom、left、right属性的话，浏览器会默认设置成auto，而auto的值则是该元素的“默认位置”。即设置position: absolute前后的offsetTop和offsetLeft属性值不变。
特殊情况：

1）应用了position: relative/absolute的元素，margin属性仍然有效，以position:relative来举例。如果设置了left、top、bottom、right的属性，建议大家不要设置margin数据，因为很难精确元素的定位，尽量减少干扰因素。


2）position: absolute忽略根元素的padding。

行内元素在应用了position：absolute之后会改变display

因此，要注意到relative是并没有改变行内元素的呈现模式，而absolute是会改变行内元素的呈现模式，如果设置了absolute并不需要显式的的将元素display改成block

应用了position: absolute / relative之后，会覆盖其他非定位元素（即position为static的元素），如果你不想覆盖到其他元素，也可以将z-index设置成-1。