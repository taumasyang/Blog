---
title: 中文排版需求：文本的间距调整
date: 2026-04-15 18:00:00 -05
updated: 2026-04-15 18:00:00 -05
categories: Typesetting
tags: [LaTeX, CSS]
index_img: https://www.thetype.com/wp-content/uploads/2018/10/kongque-greenhero.png
banner_img: https://images.unsplash.com/photo-1455390582262-044cdead277a
---

## 横排的中、西文混排配置

> 横排时，西文字母使用比例字体；阿拉伯数字则常用比例字体或等宽字体。原则上，汉字与西文字母、数字间使用不多于四分之一个汉字宽的字距或空白。但西文、数字出现在行首或行尾时，则无须加入空白。

在 `xeCJK` 中，会自动在中文与西文间插入 `0.25±0.05em` 的可变间距。

CSS Text Level 4 工作草案中，定义了 CSS 属性 `text-autospace`，可自动在中文与西文间插入 `0.125ic` 的固定间距。

推荐的 CSS 配置：

```css
p {
	text-autospace: normal replace;
}
```

其中 `normal` 值将会在表意文字与非表意文字字母、表意文字与非表意文字数字之间插入 1/8 宽度的间距。

未启用 `text-autospace` 属性时，强烈推荐手动插入一个普通空格。启用 `text-autospace` 属性时，`replace` 值将会把表意文字与非表意文字字母、表意文字与非表意文字数字之间的普通空格替换为 1/8 宽度间距。

截至 2025 年，主流浏览器都支持了 `text-autospace` CSS 属性，但是都尚未支持 `replace` 属性值。

事实上，即便浏览器已经可以自动在中西文之间插入间距，绝大部分内容呈现平台与网站的开发者并不追求最新的技术，导致该 CSS 属性被绝大多数网站忽视。此外，CSS 工作草案中指定的 1/8 字宽间距过于保守，在视觉上仅比没有间距好上一点。CSS 考虑在后续级别新增对该间距长度的控制，但在最近可预见的未来毫无希望。基于以上原因，我们目前仍然建议在内容中手动插入中西文之间的空格，待日后 CSS 支持完整，也可以使用 `replace` 属性值来旧内容兼容。
