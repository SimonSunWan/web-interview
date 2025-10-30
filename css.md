## 盒模型
问：请说说 CSS 盒模型？
答：分为标准盒模型与 IE 盒模型。标准盒宽高仅含 content；IE 盒含 content+padding+border。用 `box-sizing` 切换：`content-box` 为标准，`border-box` 为 IE。

## 布局（水平垂直居中）
问：如何实现水平垂直居中？
答：常用方法：
- Flex：父元素 `display: flex; justify-content: center; align-items: center;`
- Grid：父元素 `display: grid; place-items: center;`
- 绝对定位+transform：`top:50%; left:50%; transform: translate(-50%, -50%);`
- 绝对定位+margin:auto：`top:0; right:0; bottom:0; left:0; margin:auto;`

## 选择器与优先级
问：CSS 选择器与优先级？
答：常见：元素、类、ID、属性、伪类、伪元素。
优先级：`!important` > 内联(1000) > ID(100) > 类/属性/伪类(10) > 元素/伪元素(1) > 通配符(0)。权重相加不进位。

## 浮动与清除
问：浮动原理与清除方式？
答：浮动脱离文档流，左右贴边/元素。清除浮动以防父级塌陷：
- 空元素：尾部插入元素设 `clear: both;`
- 伪元素：父级 `.clearfix::after { content:''; display: table; clear: both; }`
- BFC：父级 `overflow: auto|hidden;` 等

## BFC
问：什么是 BFC、如何创建、有何特性？
答：BFC 为独立渲染区域，内外互不影响。
- 创建：根元素、`float != none`、`position: absolute|fixed`、`display: inline-block|table-cell|table-caption|flex|inline-flex|grid|inline-grid`、块级且 `overflow != visible`。
- 特性：同一 BFC 内块级元素垂直排列且相邻 margin 合并；左外边距贴包含块左边；计算高度时浮动参与。

## Flexbox 常用属性
问：Flex 有哪些常用属性？
答：
- 容器：`display:flex`，`flex-direction`，`justify-content`，`align-items`，`flex-wrap`
- 项目：`order`，`flex-grow`，`flex-shrink`，`flex-basis`，`align-self`

## 响应式设计
问：如何实现响应式？
答：媒体查询、流式布局、弹性图片。
- 媒体查询：按设备特性切换规则
- 流式布局：用百分比代替固定宽度
- 弹性图片：`img { max-width: 100%; height: auto; }`

## CSS3 新特性
问：列举常见 CSS3 新特性？
答：选择器（`:not()`、`:nth-child()`）、`box-sizing`、`border-radius`、`box-shadow`、`background-size`、渐变、`transition`、`animation`、`transform`、Flexbox、Grid、媒体查询等。

## 伪类 vs 伪元素
问：区别？
答：伪类选中状态（`:hover`、`:active`、`:first-child`）；伪元素生成不在文档树中的元素（`::before`、`::after`、`::first-line`）。CSS3 建议伪元素双冒号，兼容时单冒号亦可。

## 重排与重绘
问：重排与重绘？如何优化？
答：重排（布局/几何变更）必触发重绘；重绘（外观变更）不一定重排。
- 优化：合并样式变更（改 class），使用文档片段，先隐藏后修改，动画脱离文档流（绝对定位），启用 GPU（`transform`、`opacity`）。

## 单位
问：px、em、rem、vw/vh 区别？
答：`px` 绝对像素；`em` 相对父字体；`rem` 相对根字体；`vw|vh` 视口宽/高的 1%。

## 媒体查询示例
问：宽度 <600px 时背景变蓝？
答：
```css
@media (max-width: 600px) {
  body { background-color: blue; }
}
```

## 图形：三角形与扇形
问：如何实现？
答：
- 三角形（向下）：
```css
.triangle {
  width: 0; height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 100px solid red;
}
```
- 扇形（90°）：
```css
.sector {
  width: 100px; height: 100px;
  border-radius: 100px 0 0 0;
  background: red;
  /* 或使用 clip-path */
}
```

## 三栏布局
通用结构：
```html
<div class="layout">
  <aside class="l"></aside>
  <main class="m"></main>
  <aside class="r"></aside>
  </div>
```

1) Flex：左右定宽，中间自适应
```css
.layout { display: flex; }
.l { width: 200px; }
.r { width: 200px; }
.m { flex: 1; min-width: 0; }
```

2) Grid：语义清晰
```css
.layout { display: grid; grid-template-columns: 200px 1fr 200px; }
.l { grid-column: 1; }
.m { grid-column: 2; }
.r { grid-column: 3; }
```

3) Float（双飞翼简版）：中间优先渲染
```html
<div class="layout-f">
  <main class="m">
    <div class="inner"></div>
  </main>
  <aside class="l"></aside>
  <aside class="r"></aside>
</div>
```
```css
.layout-f .l { float: left; width: 200px; }
.layout-f .r { float: right; width: 200px; }
.layout-f .m { overflow: hidden; }
.layout-f .inner { margin: 0 200px; }
```

## 左右布局：等高列
答：用 Flex 或 Grid，父容器让子项拉伸即可。

1) Flex：
```css
.row { display: flex; align-items: stretch; }
.left { width: 240px; }
.right { flex: 1; }
```

2) Grid：
```css
.row { display: grid; grid-template-columns: 240px 1fr; align-items: stretch; }
```