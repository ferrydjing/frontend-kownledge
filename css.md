# position

## sticky：当元素在屏幕内时相当于为position:relative, 超出范围则表现为position:fixed
- 其父元素不能有overflow:visible以外的值，否则不生效


# css盒模型
一个元素为margin + border + padding + content组成
## IE模型元素的宽高为border+padding+content
## W3C模型元素宽高为contnent
## css 中设置盒模型的方法
默认为content-box

box-sizing: content-box ; //border-box;

# BFC
BFC是块级格式化上下文，是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响

实现方式：
1. float的值不是none。
2. position的值不是static或者relative。
3. display的值是inline-block、table-cell、flex、table-caption或者inline-flex
4. overflow的值不是visible

## BFC作用
1. 避免边界重叠
2. 计算BFC的高度时，浮动元素也参与计算。
3. 清楚浮动。
4. BFC的区域不会与float box重叠。

# css变量
```
.box {
    --primary-color: red;

    color: var(--primary-color)
}


// css to js 
const box = document.querySelector('.box');

// GET
const primaryColor = getComputedStyle(box)
    .getPropertyValue('----primary-color');

// SET
box.style
    .setProperty('----primary-color', 'custom');

```

# h5移动端适配

采用vw和rem结合的方式适配，结合sass函数实现适配

```
html {
  font-size: 13.33333333vw;
}
@media (min-width: 560px) {
  html {
    font-size: 102.4px;
  }
}

@function rem($px) {
  @return $px / 100 * 1rem;
}


// 使用方式
.base {
    width: rem(100);
    height: rem(100);
}

```

# css居中

## 元素固定宽高

```
<div class="container">
    <div class="box size">aasdasd</div>
</div>


.container {
    border: 1px solid red;
    width: 300px;
    height: 300px;
}

.box {
    background: green;
}

.box.size{
    width: 100px;
    height: 100px;
}

```

1. absolute + 负margin
```
.container {
    position:relative;
}
.box.size {
    position: absolute;
    left: 50%;
    top:50%;
    margin-left: -50px;
    margin-top: -50px;
}
```
2. absolute + margin auto
```
.container {
    position:relative;
}
.box.size {
    position: absolute;
    left: 0;
    right: 0;
    top:0;
    bottom: 0;
    margin:auto;
}
```

3. absolute + calc

```
.container {
    position:relative;
}
.box.size {
    position: absolute;
    left: calc(50% - 50px);
    top: 0calc(50% - 50px);
}
```

4. abosulte + transform
5. css table
```
<div class="container">
    <div class="box">1212</div>
</div>

.container {
    display: table-cell;
    text-align: center;
    vertical-align: center;
}

.box {
    display: inline-block;
}
```
6. flex
7. grid
