---
title: "OpenCode Skills 机制详解"
date: 2026-11-05
modified: 2026-11-05
description: >
  OpenCode 的 Skills 扩展机制：按需加载的说明书如何节省上下文、SKILL.md 的结构与 front matter、发现位置与优先级、权限控制，以及与 Agent、命令、AGENTS.md 的分工。附本站真实技能案例。
author: author2
comments: true
---

[OpenCode 入门教程](/ai/2026-11-05-OpenCode入门教程/)里我们用过它的自定义命令和子代理，这篇补上第三种扩展机制：**Skills（技能）**。它解决的是所有 Agent 都要面对的一个问题——[上下文是有限的](/ai/2026-11-05-AI-Agent工作原理/)，而“怎么干活”的知识越来越多。

## 一、Skill 是什么：按需加载的说明书

一个 Skill 就是一个文件夹，核心是一份 `SKILL.md`：用 front matter 声明名字和**什么时候该用我**，正文写这类任务的标准流程。

```text
.opencode/skills/
└── blog-publish/
    ├── SKILL.md              ← 主文件：触发条件 + 工作流程
    └── scripts/
        └── check-post.js     ← 配套脚本
```

它和把流程写进系统提示词的区别只有一个词：**按需**。没有 Skill 时，所有规范要么常驻上下文（每轮对话都占预算），要么散落在对话里（说到才想起）。有了 Skill，模型平时只看到一行目录——“有个 blog-publish 技能，用于发布文章”——直到真的要发文章了，才把完整说明书加载进来。

这正是[上下文组装](/ai/2026-11-05-AI-Agent工作原理/)的思路在扩展机制上的应用：**常驻的是目录，细节按需取回**。

## 二、加载机制：三步走

``` mermaid
flowchart LR
    A[任务到达] --> B{匹配某个技能的 description?}
    B -- 是 --> C[调用 skill 工具加载正文]
    C --> D[按正文指引读取配套文件]
    D --> E[按流程执行任务]
    B -- 否 --> F[常规对话]
```

1. **advertise（挂目录）**：每一步对话时，OpenCode 把所有可用技能的 ID、名称、description 列给模型——注意只有目录，没有正文；
2. **加载**：模型判断当前任务匹配，调用 `skill` 工具传入技能 ID，OpenCode 校验权限后把 SKILL.md 的正文（去掉 front matter）注入对话，同时附上技能所在目录和配套文件清单；
3. **按需读取**：配套脚本和参考文件**不会**自动加载——正文里写“第 1 步：运行 scripts/check-post.js”，模型执行到那一步才去读。

你也可以手动触发：在提示里写 `@技能ID`，跳过模型判断直接加载。

## 三、创建一个技能

SKILL.md 的 front matter 只有两个字段是关键：

```markdown
---
name: Blog Publish
description: 发布或修改博客文章：规范、自检、提交、验证。用户要求发文章时使用。
---

## 流程

1. 先问再提交……
2. 发布前自检……
```

| 字段 | 作用 |
| --- | --- |
| `name` | 显示名，仅用于界面展示 |
| `description` | **模型靠它决定何时加载这个技能**——没有它技能不会被自动发现 |
| `metadata.opencode/autoinvoke: false` | 从模型的可用列表里隐藏（仍可手动 `@ID` 调用） |

写 description 的手艺和[给模型写工具描述](/ai/2026-11-05-AI-Agent核心推理逻辑详解/)是同一门：它本质上是“这个技能的触发器”，写清**任务场景**而不是罗列内容——“用户要求发布、修改、移动文章时使用”远好于“包含 front matter 规范和自检脚本”。

配套文件放在 SKILL.md 旁边，技能内引用路径都相对技能目录——整个技能是一个自包含的包，可以整个目录拷给同事或放进仓库共享。

## 四、发现位置与优先级

OpenCode 自动搜索这些位置：

| 范围 | 位置 |
| --- | --- |
| 项目级 | `.opencode/skills/` |
| 全局 | `~/.config/opencode/skills/` |
| 兼容 Claude Code | `.claude/skills/`、`~/.claude/skills/` |
| 兼容通用 agents | `.agents/skills/` |
| 自定义 | `opencode.json` 的 `skills` 数组：本地目录或 **HTTP 目录** |

最后一种是团队共享的关键：把一个 `index.json` 放在 HTTP 服务上，OpenCode 会按清单下载技能文件并缓存——改了文件就递增 `version` 字段触发刷新。公司级的统一规范可以这样做成分发。

两条命名规则：

- **技能 ID 由路径决定**：`blog-publish/SKILL.md` 的 ID 是 `blog-publish`，front matter 的 `name` 只是显示名；
- **同 ID 后注册者胜**：项目 `.opencode/skills/` 会覆盖全局同名技能——可以故意用它做“官方默认技能的项目级定制版”。

配置改完即生效——OpenCode 监听配置文件，保存后自动重载技能，不用重启。

## 五、权限：技能也能配刹车

[权限门控](/ai/2026-11-05-AI-Agent核心推理逻辑详解/)同样适用于技能加载，`opencode.json` 里按资源 ID 配规则：

```jsonc
{
  "permissions": [
    { "action": "skill", "resource": "*", "effect": "allow" },
    { "action": "skill", "resource": "internal-*", "effect": "deny" }
  ]
}
```

- `allow`：自动发现并加载；
- `ask`：能发现，加载前先问你；
- `deny`：从模型面前完全隐藏，手动加载也被拒。

规则按顺序匹配、最后一条生效，也可以只挂在某个 Agent 上。比如给“只读探索型”子代理 deny 掉所有会改文件的技能，是合理的防线。

## 六、Skill、Agent、命令、AGENTS.md：怎么分工

OpenCode 有四种“注入知识/行为”的机制，各有位置：

| 机制 | 形态 | 适合放什么 |
| --- | --- | --- |
| **AGENTS.md** | 常驻上下文 | 全项目都该知道的规则：代码风格、目录约定 |
| **Skill** | 按需加载 | 特定任务的完整流程：发布、重构、排查 |
| **Agent** | 独立会话角色 | 需要隔离上下文或专属权限模型的工作：探索、发布子代理 |
| **命令** | 快捷入口 | 固定提示词模板，一个斜杠触发 |

经验法则：**所有对话都该遵守的进 AGENTS.md；一类任务才用的做成 Skill；要独立上下文或权限边界的做成 Agent；只是省打字的做成命令**。Skill 和 Agent 还能组合——Agent 可以配置自己能用哪些技能。

## 七、实战：本站的 blog-publish 技能

拿这个博客的真实例子走一遍。发布 Jekyll 文章有一堆踩坑得来的规则：Liquid 字面量会炸构建、直引号会被 smartypants 拧坏、mermaid 标签有字符禁忌、移动分类必须补 `redirect_from`、验证要等 Actions 构建再 curl……这些写成技能：

- **SKILL.md**：第 0 步“先问再提交”铁律、文章规范、自检命令、提交风格、构建等待与线上验证清单、图片规则——六节流程，每节都是实战教训；
- **scripts/check-post.js**：配套自检脚本，查 Liquid 字面量、front matter 外直引号、mermaid 高危字符，`CHECK PASSED` 才准提交。

效果：任何会话里说“发一篇文章”，模型自动加载这套流程，第 0 步就是展示 diff 等确认——不会再出现“默默推上去了”的事故。**技能的本质，是把“人肉盯着的流程”固化成模型可执行的说明书。**

## 八、小结

| 概念 | 一句话 |
| --- | --- |
| Skill | SKILL.md 声明的任务说明书，按需加载 |
| 加载 | 目录常驻 → description 匹配 → skill 工具注入正文 → 配套文件按需读 |
| description | 技能的触发器，写场景不写内容 |
| 来源 | 项目、全局、兼容目录、配置数组、HTTP 目录五路汇聚 |
| ID | 路径决定，同 ID 后注册者覆盖 |
| 权限 | allow / ask / deny 三挡，可按 Agent 收窄 |
| 分工 | 常驻规则进 AGENTS.md，任务流程做 Skill，隔离执行做 Agent，省打字做命令 |

至此工具层的扩展机制齐了：[命令](/ai/2026-11-05-OpenCode入门教程/)管快捷入口，Skill 管任务流程，Agent 管独立执行。配合前面几篇的原理层，你已经有完整的地图——剩下的，就是把手头的重复劳动一个个做成技能。
