---
title: "语义样式与自然样式"
date: 2018-07-21
image: /assets/img/blog/COLOURlovers.com-Hydejack.png
description: >
  No pain, no gains!
author: author2
comments: true
---

由于中文与英文的字义表达本身就存在差异，因此不要盲目地仅仅把英文术语靠牵强地翻译的中文意思来理解。

我们知道，可以把HTML中的一部分标签分为**自然样式标签**(Physical Style Elements)和**语义样式标签**(Semantic Style Elements)；其中：

**Physical Style Elements**: **`b`**,**`i`**,**`u`**,**`s`**,**`pre`**

**Semantic Style Elements**: **`strong`**,**`em`**,**`ins`**,**`del`**,**`code`**

> - `<i>`: [A span of text in an alternate voice or mood, or otherwise offset from the normal prose in a manner indicating a different quality of text, such as a taxonomic designation, a technical term, an idiomatic phrase from another language, transliteration, a thought, or a ship name in Western texts]——无强调或着重意味的斜体（italic），比如生物学名、术语、外来语（比如英语里常用的拉丁语短语）。
> - `<b>`:[a span of text to which attention is being drawn for utilitarian purposes without conveying any extra importance and with no implication of an alternate voice or mood, such as key words in a document abstract, product names in a review, actionable words in interactive text-driven software, or an article lede]——无强调或着重意味的粗体（bold），比如文章摘要中的关键词、评测文章中的产品名称、文章的导言。
> - `<em>`:[stress emphasis of it's contents]——强调内容
> - `<strong>`:[strong importance for it's contents]——着重内容；"Strong" could (and often does) mean "bold" in a browser, but it could also mean a lower tone for a speaking program like Jaws (for blind people) or be represented by an underline (since you can't bold a bold) on a Palm Pilot. **FORM** [Stackoverflow](https://stackoverflow.com/questions/271743/whats-the-difference-between-b-and-strong-i-and-em)

语义样式标签`<strong>`,`<em>`不一定对应自然样式中的粗体`<b>`和斜体`<i>`,可以是特别的颜色和特别的字号。

注意：从网站优化方面分析，最主要的区别应该是`<strong> XX</strong>`内的内容比起`<b>XX</b>`的内容应该更容易被网络蜘蛛抓取，也就是更容易使网站被用户搜索到。
{: .message}

[HTML_element](https://en.wikipedia.org/wiki/HTML_element)