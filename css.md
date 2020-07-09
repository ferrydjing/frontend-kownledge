# position

## sticky：当元素在屏幕内时相当于为 position:relative, 超出范围则表现为 position:fixed

- 其父元素不能有 overflow:visible 以外的值，否则不生效

# css 盒模型

一个元素为 margin + border + padding + content 组成

1. IE 模型元素的宽高为 border+padding+content
2. W3C 模型元素宽高为 contnent
3. css 中设置盒模型的方法
   默认为 content-box

box-sizing: content-box ; //border-box;

# BFC

BFC 是块级格式化上下文，是一个独立的渲染区域，让处于 BFC 内部的元素与外部的元素相互隔离，使内外元素的定位不会相互影响

实现方式：

1. float 的值不是 none。
2. position 的值不是 static 或者 relative。
3. display 的值是 inline-block、table-cell、flex、table-caption 或者 inline-flex
4. overflow 的值不是 visible

## BFC 作用

1. 避免边界重叠
2. 计算 BFC 的高度时，浮动元素也参与计算。
3. 清楚浮动。
4. BFC 的区域不会与 float box 重叠。

# css 变量

```css
.box {
  --primary-color: red;

  color: var(--primary-color);
}
```

```javascript
// css to js
const box = document.querySelector('.box')

// GET
const primaryColor = getComputedStyle(box).getPropertyValue('----primary-color')

// SET
box.style.setProperty('----primary-color', 'custom')
```

# h5 移动端适配

采用 vw 和 rem 结合的方式适配，结合 sass 函数实现适配

```css
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

# css 居中

```html
<div class="container">
  <div class="box size">aasdasd</div>
</div>
```

```css
.container {
  border: 1px solid red;
  width: 300px;
  height: 300px;
}

.box {
  background: green;
}

.box.size {
  width: 100px;
  height: 100px;
}
```

1. absolute + 负 margin

```css
.container {
  position: relative;
}
.box.size {
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
}
```

2. absolute + margin auto

```css
.container {
  position: relative;
}
.box.size {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
}
```

3. absolute + calc

```css
.container {
  position: relative;
}
.box.size {
  position: absolute;
  left: calc(50% - 50px);
  top: 0calc (50% - 50px);
}
```

4. abosulte + transform
5. css table

```html
<div class="container">
  <div class="box">1212</div>
</div>
```

```css
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
