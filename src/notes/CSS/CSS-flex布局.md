# Flex 布局
- [Flex 布局](#flex-布局)
  - [一、什么是 Flex 布局？](#一什么是-flex-布局)
  - [二、基本概念](#二基本概念)
  - [三、容器属性](#三容器属性)
    - [3.1 flex-direction属性](#31-flex-direction属性)
    - [3.2 flex-wrap属性](#32-flex-wrap属性)
    - [3.3 flex-flow](#33-flex-flow)
    - [3.4 justify-content属性](#34-justify-content属性)
    - [3.5 align-items属性](#35-align-items属性)
    - [3.6 align-content属性](#36-align-content属性)
  - [四、项目属性](#四项目属性)
    - [4.1 order属性](#41-order属性)
    - [4.2 flex-grow属性](#42-flex-grow属性)
    - [4.3 flex-shrink属性](#43-flex-shrink属性)
    - [4.4 flex-basis属性](#44-flex-basis属性)
    - [4.5 flex属性](#45-flex属性)
    - [4.6 align-self属性](#46-align-self属性)
## 一、什么是 Flex 布局？
`Flex` 是 Flexible Box 的缩写，意为 `弹性布局`

任何一个容器都可以指定为 Flex 布局
```css
.box{
  display: flex;
}
```
行内元素也可以使用 Flex 布局
```css
.box{
  display: inline-flex;
}
```
**注意**，设为 Flex 布局以后，子元素的 `float`、`clear` 和 `vertical-align` 属性将失效
## 二、基本概念
<img src="../../images/flex.png" width="600" />

**容器**：采用 Flex 布局的元素，称为 Flex 容器（flex container）

**项目**：Flex 容器的所有子元素自动成为容器成员，称为 Flex 项目（flex item）

**轴线**：
- 主轴(main axis)：主轴的开始位置叫做`main start`，结束位置叫做`main end`，项目默认沿主轴排列，单个项目占据的主轴空间叫做`main size`（默认 `0`）
- 交叉轴(cross axis)：交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`，占据的交叉轴空间叫做`cross size`（默认 `100%`）
## 三、容器属性
### 3.1 flex-direction属性
`flex-direction` 属性决定 `主轴` 的方向（即项目的排列方向）
```css
.box {
  flex-direction: row | row-reverse | column | column-reverse
}
```
- row（默认）：主轴为水平方向，起点在左端
- row-reverse：主轴为水平方向，起点在右端
- column：主轴为垂直方向，起点在上沿
- column-reverse：主轴为垂直方向，起点在下沿

<img src="../../images/flex-direction.svg" width="400" />

### 3.2 flex-wrap属性
默认情况下，项目都排在一条线（又称"轴线"）上。 `flex-wrap` 属性定义，如果一条轴线排不下，如何换行
```css
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```
- nowrap（默认）：不换行  
<img src="../../images/flex-wrap_nowrap.png" width="500" />
- wrap：换行，第一行在上方  
<img src="../../images/flex-wrap_wrap.jpg" width="500" />
- wrap-reverse：换行，第一行在下方  
<img src="../../images/flex-wrap_wrap-%20reverse.jpg" width="500" />

### 3.3 flex-flow
`flex-flow` 属性是 `flex-direction` 属性和 `flex-wrap` 属性的简写形式，默认值为 `row nowrap`
```css
.box {
  flex-flow: <flex-direction> <flex-wrap>;
}
```

### 3.4 justify-content属性
`justify-content` 属性定义了项目在 `主轴` 上的对齐方式
```css
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly;
}
```
- flex-start（默认值）：`main start` 对齐
- flex-end：`main end` 对齐
- center：居中
- space-between：两端对齐，项目之间的间隔都相等
- space-around：每个项目两侧的间隔相等
- space-evenly：任意两个项目之间的间距（以及到边缘的空间）都是相等的

<img src="../../images/justify-content.svg" width="350" />

### 3.5 align-items属性
align-items属性定义项目在 `交叉轴` 上如何对齐
```css
.box {
  align-items: stretch | flex-start | flex-end | center | baseline;
}
```
- stretch（默认值）：如果项目未设置 `高/宽` 或设为 `auto`，将占满整个容器的 `高/宽`
- flex-start：`交叉轴` 的起点对齐
- flex-end：`交叉轴`  的终点对齐
- center：`交叉轴` 的中点对齐
- baseline: 项目的第一行文字的基线对齐

<img src="../../images/align-items.svg" width="350" />

### 3.6 align-content属性
`align-content` 属性定义了多根轴线(换行情况下)的对齐方式。如果项目只有一根轴线，该属性不起作用
```css
.box {
  align-content: stretch | flex-start | flex-end | center | space-between | space-around;
}
```
- stretch（默认值）：轴线占满整个 `交叉轴`
- flex-start：`交叉轴` 的起点对齐
- flex-end：`交叉轴` 的终点对齐
- center：`交叉轴` 的中点对齐
- space-between：与 `交叉轴` 两端对齐，轴线之间的间隔平均分布
- space-around：每根轴线两侧的间隔都相等

<img src="../../images/align-content.svg" width="350" />

## 四、项目属性
### 4.1 order属性
`order` 属性定义项目的排列顺序。数值越小，排列越靠前，默认为 `0`
```css
.item {
  order: <integer>;
}
```
<img src="../../images/order.svg" width="400" />

### 4.2 flex-grow属性
`flex-grow` 属性定义项目的放大比例，默认为 `0`，即如果存在剩余空间，也不放大
```css
.item {
  flex-grow: <Positive number>;
}
```
如果所有项目的 `flex-grow` 属性都为 `1`，则它们将等分剩余空间（如果有的话）。如果一个项目的 `flex-grow` 属性为 `2`，其他项目都为 `1`，则前者占据的剩余空间将比其他项多一倍  

<img src="../../images/flex-grow.svg" width="400" />

### 4.3 flex-shrink属性
`flex-shrink` 属性定义了项目的缩小比例，默认为 `1`，即如果空间不足，该项目将缩小
```css
.item {
  flex-shrink: <Positive number>;
}
```
如果所有项目的 `flex-shrink` 属性都为 `1`，当空间不足时，都将等比例缩小。如果一个项目的 `flex-shrink` 属性为 `0`，其他项目都为 `1`，则空间不足时，前者不缩小  

<img src="../../images/flex-shrink.jpg" width="600" />

### 4.4 flex-basis属性
`flex-basis` 属性定义了在 `分配多余空间` 之前，项目占据的 `主轴` 空间。浏览器根据这个属性，计算 `主轴` 是否有多余空间。它的默认值为 `auto`（项目本来大小）
```css
.item {
  flex-basis: <length> | auto;
}
```

**优先级**
```JavaScript
max-width/min-height > flex-basis > width/height
```
### 4.5 flex属性
`flex` 属性是 `flex-grow`, `flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto`，后两个属性可选
```css
.item {
  flex: auto | none | [ <flex-grow> ?<flex-shrink> ?<flex-basis> ]
}
```
该属性有两个快捷值：
- `auto`：`(1 1 auto)`
- `none`：`(0 0 auto)`
### 4.6 align-self属性
`align-self` 属性允许 `单个项目` 有与 `其他项目` 不一样的对齐方式，可覆盖 `align-items` 属性。  
默认值为 `auto`，表示继承父元素的 `align-items` 属性，如果没有父元素，则等同于 `stretch`

```css
.item {
  align-self: auto | stretch | flex-start | flex-end | center | baseline;
}
```
- auto：继承父元素的 `align-items` 属性
- stretch：占满整个容器的 `交叉轴`
- flex-start：`交叉轴` 的起点对齐
- flex-end：`交叉轴`  的终点对齐
- center：`交叉轴` 的中点对齐
- baseline: 项目的第一行文字的基线对齐

<img src="../../images/align-self.svg" width="400" />

