---
title: css样式
date: 2025-10-20 23:37:23
tags: css
---
```javascript
/deep/.el-sub-menu__title:hover {
        color:white!important;
        background: #1890FF!important;
    }
```

子目录的鼠标悬停样式强制覆盖



Prism库用来高亮代码	



这段代码不能放在App.vue, 要放在需要高亮样式的页面下面, 否则页面刷新的时候样式不会生效

我也没想到highlightAll是个一次性函数

```javascript
setup() {
      onMounted(() => {
        Prism.highlightAll();
      })
}
```



# flex容器

display: flex

这样就可以把一个div容器变成flex容器



# 引入CSS文件

 <link rel="stylesheet" href="styles.css">



# 标签内的文字颜色

color



# 给元素加上块 

display: block



## flex-item的宽度

flex-basis



# 边框设置

border: dotted 3px grey;

dotted就是虚线



padding: 内容与框的内边距

padding: 10px; // 与四个方向都是10px的距离

padding: 20px 10px; // 第一个值代表上下边距, 第二个值代表左右边距



width: 100%; // 占父元素的百分比



box-sizing: border-box; // 改变样式, 就是width为100%的时候, 加上padding会超出页面最大宽度, 加上这个就不会超过边界了



padding // 外边距 

\* 通配符, 代表所有元素



样式是从上到下地解释, 所以对同一个物件的设置, 后面的会覆盖前面的



margin-left: auto和margin-right: auto搭配使用会呈现第一种水平居中的效果



margin也是有上下和左右的两参数设置

margin: 0, auto



#  背景设置

background-image: 背景图片

e.g.

background-image: url(./bg.jpg);



background-image和background-color不能同时使用



box-shadow: 5px 5px 5px grey;

第一个参数是向右偏, 第二个是向下偏, 第三个是阴影晕染的宽度



# 文本处理

.box p {

}

上面表示的是class="box" 的元素里面的p标签



## 段落的缩进

text-indent



## em

1em是一个字符的距离



## 字体大小

font-size

## 字体

font-family



## 字体的粗细

font-weight

# 文字的对齐

text-align



## 行高

line-height

当line-height和height一致的时候, 可以实现垂直方向上的居中对齐



## 文字阴影

text-shadow: 2px 2px 2px black; // 和上面的box-shadow同理

## 文字装饰

text-decoration: dashed underline; // 其中dashed是虚线的意思

## 纵向显示

 writing-mode: vertical-lr; // lr是从左到右的意思

## 英文在纵向上的横向显示(每个字符都横向显示)

text-orientation: upright; 



# 位置

position: absolute; // 绝对定位, 根据最外部来进行定位(body标签)

position: relative; // 相对定位, 根据初始位置来进行偏移

position: fixed; // 根据可视区域来定位, 就是你拉滚动条的时候, 这玩意是不动的

position // 应该就是用来定义位置的定位模式, 有浮动和定位



一些定位的样例

top: 200px;

left: 5px;

# flex布局

每一个flex-item独占一个div标签f

div标签被定义为flex-container(display: flex)之后, 里面的flex-item就不会像原本那样独占一行了, 而是自适应地填充空间



max-width

min-width

两个属性限制了在页面大小变动的时候的组件大小



align-items: center; // 容器内部的物件对齐方式

align-items: flex-start; // 顶部

align-items: flex-end; // 底部



justify-content: center; // 调整水平方向的对齐方式

justify-content: flex-start; // 左边, 有一个水平轴

justify-content: flex-end; // 右边



.flex-item:nth-child(2) { // 第二个class="flex-item"的元素

}



属性flex: 2; // 这个flex-item所占比例



flex-item:nth-child(2n+1) { // 获取第1, 3, 5, 7, ...个元素

}

# grid布局

display: grid

grid-template-columns: 200px 300px 200px; // 给了三个参数, 就代表列有三个, 然后已经知道容器内的元素总数了, 所以可以很轻松地计算出一行有多少个

fr 一个用来写比例的单位

grid-template-columns: 1fr 1fr 1fr;

repeat(3, 1fr) = 1fr 1fr 1fr //

repeat(auto-fill, 200px) // 优先填满一行, 每个item的宽度为200px

grid-template-columns: 200px auto 300px; // 左右固定, 中间自适应

grid-template-rows // 对于行的设置, 和列的设置都是差不多的



overflow: hidden // 超出宽度或高度的部分将被隐藏



# 动画

.transition-box:hover { // hover是鼠标悬浮在组件上面的时候

}

transition: width 1s; // 还有一个运动方式的参数, 不写也有默认样式, 第一个参数是在什么属性发生变化的时候插入动画, 第二个参数是变化动画的时间

transition: width 1s, height 2s; // 可以连写

transition: all 1s; // 指定所有动画



transition: all 1s linear; // linear的动画是匀速的



为什么第一个的位置是绝对的, 也会影响到第二个, 难道是因为只要用了绝对位置, 这样其他物件就会自动忽略它吗



transform: translate(100px, 20px); // 离左边的距离和离上边的距离

transform: translateX(100px) translateY(20px) // 一种等价但是更麻烦的写法, 单独移动X轴或者Y轴的时候会更有用吧

transform: rotate(30deg); // 旋转, 顺时针, 又多了一个单位

transform不能复合, 最后一个transform会覆盖前面的



transform-origin: right bottom; // 改变变换中心

transform: scale(.5); // 缩放

transform: scale(.5, 2); // 水平和垂直方向的缩放f



transform: skew(10deg); // X轴做一些偏移, 变成平行四边形

transform: skew(10deg, 20deg); // Y轴也可以来点



# 3D效果

展示3D效果的item需要有一个container包住它先

transform: preserve-3d; // 定义在container里面

perspective: 500px; // 3d模式下与物件的距离?

transform: translate3d(100px, 100px, -100px); // 三维运动

transform: rotate3d(0, 1, 0, 100deg); // 沿着(0,, 1, 0)这个向量转100deg



# 逐帧动画

```css
@keyframes changeColor {
    0% {
        background-color: tomato;
    }

    50% {
        background-color: yellow;
    }

    100% {
        background-color: tomato;
    }
}
```

无限循环

```css
animation: changeColor 1s infinite;
```



# 尽可能占据更多左边空间(靠右对齐)

margin-left: auto;



# 真正实现水平居中

```css
.search-box {
    display: flex;
    justify-content: center;
}
```



# 在flex容器当中使得某一个item独占一行

首先在flex容器当中设置flex-wrap: wrap; // 允许换行

然后在包住item的容器里面设置flex-basis: 100%;



# 消除li的前面的点

li就有令组件独占一行的作用

但是每个组件前面都会带上一个点

这是因为ul和li自带了样式,我们把样式消除掉即可

```css
body li{
    list-style: none;
}
```



# 鼠标悬浮变成手型

```css
cursor: pointer;
```



# 靠左靠右对齐

float: left;

float: right;

# 组件固定在页面的某一位置,即使滚动也不会改变

```css
position: fixed;
```

# rgba(含透明度参数)

```css
rgba(0, 0, 0, 0.5)
```

第四个参数是透明度参数