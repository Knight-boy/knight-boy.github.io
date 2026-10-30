---
title: 什么是 Vibe Coding
date: 2026-10-31
image: /assets/img/blog/Rock-roll.gif
description: >
  当 AI 会写代码之后，编程的方式正在被重新定义
author: author2
comments: true
---

## 什么是 Vibe Coding

**Vibe Coding**（氛围编程）是 AI 领域大神 **Andrej Karpathy** 在 2025 年 2 月提出的概念。他这样描述：

> There's a new kind of coding I call "vibe coding", where you fully give in to the vibes, embrace exponentials, and forget that the code even exists.

简单来说，**Vibe Coding 就是用自然语言和 AI 对话，让 AI 直接生成代码，而人几乎不逐行审查代码**的一种编程方式。你负责描述"想要什么"，AI 负责"怎么实现"，开发者更像一个产品经理/需求方，而不是传统的码农。

## 为什么会流行起来

Vibe Coding 的兴起背后有三个关键因素：

1. **大模型能力的指数级提升**。如今的 LLM（GPT、Claude、Gemini 等）已经可以独立完成大部分常见功能的开发，代码正确率远超早期版本。
2. **Agent 工具的成熟**。Cursor、Claude Code、Copilot Agent 等工具让 "一句话开工、自动改文件、自动跑测试" 成为现实，AI 不再只是补全代码，而是能完整地执行任务。
3. **需求描述比语法知识更稀缺**。对于个人开发者来说，把一个想法清楚地说出来，往往比手写几百行样板代码更高效。

## 一个典型的 Vibe Coding 工作流

以开发一个网页小工具为例，典型流程是这样的：

```text
1. 用自然语言描述需求
   "帮我做一个番茄钟网页，可以设定时间、暂停、有进度条动画"

2. AI 生成全部代码并直接创建文件

3. 打开浏览器看效果，发现问题就用嘴"修"
   "进度条太快了，改成实时平滑动画"
   "保存的记录刷新后就没了，加上 localStorage"

4. 重复第 2~3 步，直到满意为止

5. 满意就 commit → 部署上线（甚至可以让 AI 自己来）
```

整个过程几乎不打字写代码，靠 **对话 + 反馈闭环** 驱动开发。Karpathy 的原话是：*我会说"把对数 loss 再减小 1e-4"，或者"这段代码看不懂，直接删掉"……项目从设计到完成，全程基本只用说*。

## Vibe Coding 的适用与不适用场景

它不是万能的，搞清楚边界很重要：

**适合：**

- 一次性小工具、脚本、demo、原型
- 个人小工具、周末项目
- 快速验证一个想法是否可行
- 传统编程里"体力活"为主的重复性开发

**不适合：**

- 核心业务系统、金融、医疗等高可靠性要求的项目
- 性能敏感、算法复杂的底层开发
- 安全相关代码（鉴权、加密、支付）
- 需要**长期可维护**的大型代码库——团队读不懂 AI 写的代码时，维护成本会爆炸

## 常见的坑

- **幻觉 API**：AI 可能"编造"不存在的函数或参数，跑起来才发现不对。
- **测试盲区**：不审查代码、不写测试，等于把正确性寄托在运气上。
- **安全漏洞**：AI 生成的代码可能包含硬编码密钥、SQL 注入、XSS 等问题。
- **债务累积**：能跑 ≠ 优雅，反复"修"出来的代码结构很容易失控，改一处崩全局。

因此业界也催生了对立的口号：**"Code, don't vibe, when production is on the line."**（要上线的代码，别只靠氛围。）

## 个人看法

Vibe Coding 并不是"不需要学计算机基础"的借口。恰恰相反，**越是懂代码的人，用 Vibe Coding 越高效**：你知道该问什么、能一眼看出结果哪里不对、能引导 AI 走向正确方案。

把 AI 当作一个极速但偶尔犯迷糊的结对工程师，清醒而审慎地用，而不是完全放弃思考——这大概才是 Vibe Coding 正确的打开方式。

## 参考

- [Karpathy 的原始推文（2025-02）](https://x.com/karpathy/status/1886192184808144633)
- [Vibe coding — Wikipedia](https://en.wikipedia.org/wiki/Vibe_coding)
- [Simon Willison 对 vibe coding 的评论](https://simonwillison.net/2025/Mar/19/vibe-coding/)
