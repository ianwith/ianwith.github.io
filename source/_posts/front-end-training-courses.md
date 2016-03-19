title: Front-end Training Courses
date: 2016-03-16 22:45:39
tags:
- work
- front end
- courses
- css
categories: work
---

Recently, Our team opened a series of front-end training courses inside the company. These courses aim to teach everyone the basics of front-end programming skills. We encourage all interested colleagues especially Back-end developers, iOS developers, Android developers to enroll in the course. By offering a series of lessons featured with lectures, real-world cases, and hands-on exercises, we covered practical topics like basic HTML, SCSS Introduction, Exploring ECMAScript, Workflow of Front-end in Douban, etc.

Front-end technology is widely used in desktop and mobile applications. Learning and applying fundamental concepts benefits for enhancing the efficiency of development and cooperation.

<!-- more -->

My topic was about the Layouts for Mobile Screens. Although the following demos are simple, they helped me recall W3C standards. We had a lot of fun with CSS.

## Table of contents:

### Media Query

- Usage:
```html
<!-- CSS media query on a link element -->
<link rel="stylesheet" media="(max-width: 800px)"
href="example.css" />

<!-- CSS media query within a stylesheet -->
<style>
@media (max-width: 800px) {
}
</style>
```

- Syntax:
```css
@media (min-width: 500px)
@media only screen and (min-width: 500px)
media_type: screen, print, tv, all
media_feature: min-width, max-width, min-height,  max-height
```

- Example
<p data-height="275" data-theme-id="dark" data-slug-hash="VXYdbm" data-default-tab="css,result" data-user="ianwith" data-pen-title="media" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/VXYdbm/">media</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Fluid Grids

1. **fixed width**

- px + float
<p data-height="275" data-theme-id="dark" data-slug-hash="BryVad" data-default-tab="css,result" data-user="ianwith" data-pen-title="px_float" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/BryVad/">px_float</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

- px + inline-block
<p data-height="275" data-theme-id="dark" data-slug-hash="zWxaGQ" data-default-tab="css,result" data-user="ianwith" data-pen-title="px_inlineblock" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/zWxaGQ/">px_inlineblock</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

2. **percentage based**

- % + float
<p data-height="275" data-theme-id="dark" data-slug-hash="YaPvwG" data-default-tab="css,result" data-user="ianwith" data-pen-title="percent_float" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/YaPvwG/">percent_float</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

- % + inline-block
<p data-height="275" data-theme-id="dark" data-slug-hash="eMmKJX" data-default-tab="css,result" data-user="ianwith" data-pen-title="percent_inlineblock" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/eMmKJX/">percent_inlineblock</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Rem

- Syntax
px — Absolute Lengths
em — Relative Lengths (font size of the element)
rem — Relative Lengths (font size of the root element)

- Example
<p data-height="275" data-theme-id="dark" data-slug-hash="WzbyjK" data-default-tab="css,result" data-user="ianwith" data-pen-title="rem" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/WzbyjK/">rem</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Flex

- Properties applied to flex items
<p data-height="275" data-theme-id="dark" data-slug-hash="gebKeo" data-default-tab="css,result" data-user="ianwith" data-pen-title="flex_box" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/gebKeo/">flex_box</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

- Alignment along the main axis
<p data-height="275" data-theme-id="dark" data-slug-hash="wmBXmX" data-default-tab="css,result" data-user="ianwith" data-pen-title="alignment" class="codepen">See the Pen <a href="https://codepen.io/ianwith/pen/wmBXmX/">alignment</a> by Ian Gong (<a href="https://codepen.io/ianwith">@ianwith</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
