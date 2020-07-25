# css 面试题

## 目录

- [1. display 有哪些值及其区别](#1)
- [2. position 有哪些值及其区别](#2)
- [3. px、em、rem 区别](#3)
- [4. 移动端适配 1px 的问题](#4)
- [5. 说一下 css3 的 animation](#5)
- [6. BFC 是什么？触发 BFC 的条件是什么？有哪些应用场景？](#6)

## 题解

#### <a href="#1" id="1">1. display 有哪些值及其区别</a>

- `none` 隐藏元素，不占据空间
- `block` 块状元素，前后有换行符，能设置宽高
- `inline` 行内元素，没有换行符，不能设置宽高
- `inline-block`行内块状元素，没有换行符，能设置宽高
- `table`、`flex`、`grid` 布局方式

<br>
<br>

#### <a href="#2" id="2">2. position 有哪些值及其区别</a>

- `static` 是默认值
- `inherit` 继承父元素的 `position` 值
- `relative` 相对定位，相对于自身原有位置进行偏移，仍处于标准文档流中
- `absolute` 绝对定位 相对于最近的已定位的祖先元素，有已定位（指 `position` 不是 `static`的元素）祖先元素，以最近的祖先元素为参考标准。如果没有已定位祖先元素，以 `body` 元素为偏移参照基准，完全脱离了标准文档流。
- `fixed` 固定定位的元素会相对于视窗来定位，这意味着即便页面滚动，它还是会停留在相同的位置。一个固定定位元素不会保留它原本在页面应有的空隙。
- `sticky` 新的属性值，兼容性不好， 元素偏移在达到特定阈值前为相对定位，达到后为固定定位，常用于做悬浮效果

<br>
<br>

#### <a href="#3" id="3">3. px、em、rem 区别</a>

- `px` 固定像素，一旦设置后就无法自适应页面，随着页面的大小而改变
- `em` 相对父元素来设置字体大小
- `rem` 相对根元素来设置大小

#### <a href="#4" id="4">4. 移动端适配 1px 的问题</a>

首先说下造成 `1px` 的边框在移动端上变粗的原因：

因为 `css` 中的 `1px` 并不等于移动设备的 `1px`，这是由于不同的的手机有不同的像素密度。在 `window` 对象中有一个 `devicePixelRatio` 属性，他可以反应 `css` 中的像素与设备的像素比。

> devicePixelRatio 的官方定义为：设备物理像素和设备独立像素的比例

我了解的解决方法有四种，实际项目中灵活运行

1. WWDC 对 IOS 的建议：直接使用 0.5px 边框

   缺点： 仅支持 IOS 8+，不支持安卓。

2. 使用边框图片：border-image

   ```css
   .border-image-1px {
     border: 1px solid transparent;
     border-image: url('../img/hairline_gray.png') 2 repeat;
   }
   ```

   优点：可以设置单条、多条边框  
   缺点：修改颜色麻烦，圆角需要特殊处理

3. 使用 box-shadow 模拟边框

   ```css
   .box-shadow-1px {
     box-shadow: inset 0 -1px 1px -1px #e5e5e5;
   }
   ```

   优点：使用简单，圆角也可以实现  
   缺点：边框有阴影，百分百过不了视觉走查

4. **伪类 + transform + 绝对定位 实现**

   ```css
   .scale-1px {
     position: relative;
     &::after {
       content: '';
       width: 100%;
       height: 1px; /* no */
       background: #e5e5e5;
       position: absolute;
       left: 0;
       bottom: 0;
       transform: scaleY(0.5);
     }
   }
   ```

   优点：所有场景都能满足，支持圆角  
    缺点：伪类冲突 - -

#### <a href="#5" id="5">5. 说一下 css3 的 animation</a>

- `css3` 的 `animation` 是 `css3` 新增的动画属性，这个 `css3` 动画的每一帧是通过 `@keyframes` 来声明的， `keyframes` 声明了动画的名称，通过 `from` 、 `to` 或者是百分比来定义

- 每一帧动画元素的状态，通过 `animation-name` 来引用这个动画，同时 `css3` 动画也可以定义动画运行的时长、动画开始时间、动画播放方向、动画循环次数、动画播放的方式

- 这些相关的动画子属性有：`animation-name` 定义动画名、 `animation-duration` 定义动画播放的时长、 `animation-delay` 定义动画延迟播放时间、 `animation-direction`
定义动画的播放方向、 `animation-iteration-count` 定义播放次数、 `animation-fill-mode` 定义动画播放之处的状态、 `animation-play-state` 定义播放状态，如暂停运行等、`animation-timeing-function` 定义播放的方式，如恒速播放、艰涩播放等

  <br/>
<br/>

#### <a href="#6" id="6">6. BFC 是什么？触发 BFC 的条件是什么？有哪些应用场景？</a>

- 什么是 BFC

  MDN 对 BFC 的定义是：**块格式化上下文**是 web 页面的可视 css 渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

- 如何触发 BFC

  - 根元素
  - 浮动元素：float 除了 none 以外的属性值
  - 绝对定位元素（元素的 position 为 absolute 或 fixed）
  - display 为 inline-block, table-cells, flex, table
  - overflow 除了 visible 以外的属性值。

- 应用场景

  - 避免外边距重叠
  - 防止浮动导致父元素高度塌陷
  - 清除内部浮动
  - 嵌套元素的 margin 重叠

<br/>
<br/>
