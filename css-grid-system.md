# 创建你自己的CSS网格系统

>原文链接: http://j4n.co/blog/Creating-your-own-css-grid-system

----
CSS网格系统的使用已经有很长时间了，他们经常捆绑在各种框架之中，如Bootstrap。我不讨厌Bootstrap，但是如果你仅仅因需要一个网格系统去使用框架，就显得大材小用了。
这里，我将告诉大家如何从无到有创建自己的CSS网格系统。

----
## CSS网格元素
![CSS网格元素示意图](http://j4n.co/content/4-blog/10-Creating-your-own-css-grid-system/grid-elements.png)

正如你所看到的，一个基础的网格系统只包含了很少的元素
- container
- rows
- columns
- gutters（列之间的空间）

---
## container
![container示意图](http://j4n.co/content/4-blog/10-Creating-your-own-css-grid-system/container.png)

使用container的目的是要设置整个网格的宽度，通常来说宽度为100%，但对于较大宽度的显示来说，我们应该要设置一个最大宽度。
```css
.grid-container {
  width: 100%;
  max-width: 1200px;
}
```

----
## The Row
![Row示意图](http://j4n.co/content/4-blog/10-Creating-your-own-css-grid-system/row.png)

row元素的使用为保证其中的列不会溢出到其他行，因而我们会使用清除浮动来确保这一点。
```css
/*-- our cleafix hack -- */ 
.row:before, 
.row:after {
  content: "";
  display: table ;
  clear:both;
}
```

---
## The Column
![Column示意图](http://j4n.co/content/4-blog/10-Creating-your-own-css-grid-system/column.png)

column绝对是网格中最为复杂的部分。首先，在CSS中有几种不同的方式去定位columns；其次，要考虑宽度及相应设计等因素。本教程中，我们只对columns定位并给定宽度，响应式会留到后面。

### Column Positioning
在CSS中，`Floats`, `inline-blocks`, `display-table`, `display-flex` 都可以定位columns，从个人的经验来看，这些方法中错误发生的最小几率和最广泛使用的是`float`方法。但是如果我们的列为空，浮动列将会堆叠到一起，为防止这种情况发生，我们在设置float同时会把`min-heigth`设置成1px。
```css
[class*='col-'] {
  float: left;
  min-height: 1px;
}
```
### Column Widths
一列的宽度就是将container的宽度除以列的总数。在这里，宽度为100%，如果我们要6个标准列，就可以得到每列宽度为16.66%（100/6=16.6）。
```css
[class*='col-'] {
  float: left;
  min-height: 1px;
  width: 16.66%;
}
```
当然，这只是开始，如果我们希望一个部分是两个标准列宽，我们就需要创建一个 2-column-wide 的列。这种计算非常简单。
```css
.col-1{
  width: 16.66%; 
}
.col-2{
  width: 33.33%; 
}
.col-3{
  width: 50%; 
}
.col-4{
  width: 66.664%;
}
.col-5{
  width: 83.33%;
}
.col-6{
  width: 100%;
}
```
我们使用这些列组合唯一需要考虑的是最大的标准列总数

### Column Gutters
![Gutters示意图](http://j4n.co/content/4-blog/10-Creating-your-own-css-grid-system/column-gutters.png)

在`box-sizing`属性出现之前，给百分比宽度元素一个静态的`padding`是非常痛苦的。幸运的是，我们现在可以很轻松地通过`box-sizing`来创建gutters。
```css
/*-- setting border box on all elements inside the grid --*/
.grid-container *{
  box-sizing: border-box; 
}

[class*='col-'] {
  float: left;
  min-height: 1px; 
  width: 16.66%; 
  /*-- our gutter --*/
  padding: 12px;
}
```
（事实上，我会将`* { box-sizing: border-box; }`应用到所有页面中。）

---
## 这就是我们标准的网格系统
HTML:
```HTML
<div class="grid-container outline">
  <div class="row">
    <div class="col-1"><p>col-1</p></div> 
    <div class="col-1"><p>col-1</p></div> 
    <div class="col-1"><p>col-1</p></div> 
    <div class="col-1"><p>col-1</p></div> 
    <div class="col-1"><p>col-1</p></div> 
    <div class="col-1"><p>col-1</p></div> 
  </div> 
  <div class="row">
    <div class="col-2"><p>col-2</p></div> 
    <div class="col-2"><p>col-2</p></div> 
    <div class="col-2"><p>col-2</p></div> 
  </div> 
  <div class="row">
    <div class="col-3"><p>col-3</p></div> 
    <div class="col-3"><p>col-3</p></div> 
  </div> 
</div>
```

CSS:
```CSS
.grid-container{
  width: 100%; 
  max-width: 1200px;      
}

    /*-- our cleafix hack -- */ 
.row:before, 
.row:after {
  content:"";
  display: table ;
  clear:both;
}

[class*='col-'] {
  float: left; 
  min-height: 1px; 
  width: 16.66%; 
  /*-- our gutter -- */
  padding: 12px; 
  background-color: #FFDCDC;
}

.col-1{ width: 16.66%; }
.col-2{ width: 33.33%; }
.col-3{ width: 50%;    }
.col-4{ width: 66.66%; }
.col-5{ width: 83.33%; }
.col-6{ width: 100%;   }
.outline, .outline * {
  outline: 1px solid #F6A1A1; 
}

/*-- some extra column content styling --*/
[class*='col-'] > p {
  background-color: #FFC2C2; 
  padding: 0;
  margin: 0;
  text-align: center; 
  color: white; 
}
```

---
## 响应式网格

- 调整我们的网格的移动布局很简单，只需要调整列的宽度。
- 为简单起见，我将为800px下的屏幕把列的宽度加倍。
- 唯一需要注意的是一些特殊情况：在row中的最后一列。如在`.col-2`和`.col-1`在`.col-5`的旁边。
- 为解决这个问题，要将列最后的`.col-2`和`.col-1`设置为`width: 100%`。
```CSS
@media all and (max-width: 800px) {
  .col-1{ width: 33.33%; }
  .col-2{ width: 50%; }
  .col-3{ width: 83.33%; }
  .col-4{ width: 100%; }
  .col-5{ width: 100%; }
  .col-6{ width: 100%; }
  
  .row .col-2:last-of-type {
    width: 100%;
  }
  
  .row .col-5 ~ .col-1 {
    width: 100%;
  }
}
```
对于小于800px的屏幕，我们会将出了最小的列以外的都设置成100%。
```CSS
@media all and (max-width: 650px) {
  .col-1{ width: 50%; }
  .col-2{ width: 100%; }
  .col-3{ width: 100%; }
  .col-4{ width: 100%; }
  .col-5{ width: 100%; }
  .col-6{ width: 100%; }
}
```

以上，我们就可以创建属于我们自己的响应式网格系统了。
  
**但要注意，此教程只是创建自己的系统的开端，这不是一个框架或者说一个完整的解决方案，但我希望它能为大家揭开创建CSS网格神秘的面纱**
