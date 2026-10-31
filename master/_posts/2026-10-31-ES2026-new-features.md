---
title: ES2026(ES17)的新功能
date: 2026-10-31
image: /assets/img/blog/es7-es8-cover.png
description: >
  ECMA2026 New Features!
author: author2
comments: true
---

**在本文中，我将介绍通过ES2026(ES17)引入的JavaScript的新功能，以及它们的用途及如何使用它们。**

2026 年 6 月 30 日，Ecma International 正式批准了 ECMA-262 第 17 版，即 **ECMAScript 2026（ES2026）**。和 ES2024/ES2025 的"小步快跑"不同，这一版终于啃下了几块硬骨头：`Date` 的继任者 **Temporal** 转正、**显式资源管理 `using`** 落地，再加上一批"把天天手写的轮子收编进标准库"的 API——`Math.sumPrecise`、`Uint8Array` 原生 Base64/Hex、`Error.isError`、`Array.fromAsync`、`Iterator.concat`、`Map.getOrInsert` 以及 **JSON 源码文本访问**。没有新语法大爆炸，但每一个都瞄准真实痛点。

先给一个全景表，后文逐个展开：

| # | 特性 | 一句话 |
|---|---|---|
| 1 | Temporal | 现代日期时间库，替代 `Date` |
| 2 | using / await using | 作用域结束自动释放资源 |
| 3 | JSON.rawJSON | 解析/序列化时保留原始文本 |
| 4 | Math.sumPrecise | 无误差累积的浮点求和 |
| 5 | Uint8Array Base64/Hex | 原生二进制编解码 |
| 6 | Error.isError | 跨 realm 的错误判定 |
| 7 | Array.fromAsync | 异步迭代器一步转数组 |
| 8 | Iterator.concat | 惰性合并多个迭代器 |
| 9 | Map.getOrInsert | "不存在才插入"一步到位 |

## 一、Temporal：Date 的历史债，终于开始还了

### Date 到底错在哪

`Date` 对象背负了三十年的设计债，随便就能列出七宗罪：

1. **可变对象**：`d.setMonth(...)` 直接改自身，共享引用时防不胜防；
2. **月份从 0 开始**：`new Date(2026, 9, 31)` 是 10 月不是 9 月；
3. **只有毫秒精度**：科学计算、高精度日志不够用；
4. **时区 = UTC 偏移**：`+08:00` 是"死偏移"，夏令时切换瞬间就错；没有 IANA 时区数据库；
5. **没有日历概念**：只有公历（还是美式解析习惯）；
6. **解析全靠猜**：`new Date('2026-10-31')` 与 `new Date('10/31/2026')` 行为不一致，各引擎还有差异；
7. **API 缺口**：想算"两个日期差几天""下个月 1 号"都要手写。

社区为此打了十几年的补丁（Moment、Day.js、date-fns、Luxon），Temporal 则是 TC39 牵头、历时六年的官方答案，2026 年 3 月到达 Stage 4。

### 类型系统：一个场景一个类型

Temporal 的核心设计是**类型边界清晰**——你要什么，就取什么类型，互相不越界：

| 类型 | 含义 | 有无时区 |
|---|---|---|
| `PlainDate` | 纯日期（生日、截止日） | ✗ |
| `PlainTime` | 纯时间（闹钟 07:30） | ✗ |
| `PlainDateTime` | 日期 + 时间（"挂钟时间"） | ✗ |
| `PlainYearMonth` | 年月（"2026 年 10 月"） | ✗ |
| `PlainMonthDay` | 月日（"10 月 31 日"，节日） | ✗ |
| `Instant` | 精确时间点（纳秒，UTC） | UTC |
| `ZonedDateTime` | 带完整时区的时间点 | ✓ IANA |
| `Duration` | 时间量（"2 小时 30 分"） | — |

```javascript
const today = Temporal.Now.plainDateISO();       // 2026-10-31
const now = Temporal.Now.instant();              // 精确时间点
console.log(now.epochNanoseconds);               // 纳秒精度
```

### 日常用法示例

```javascript
// 1. 解析与格式化（只认 ISO 8601，不再猜格式）
const due = Temporal.PlainDate.from('2026-10-01');

// 2. 日期差：以前要 setHours(0,0,0,0) 再除 86400000，现在直接问
console.log(due.until(today).days);              // 30

// 3. 日期算术：add / subtract，返回新对象（不可变！）
const deadline = today.add({ days: 7 });
const nextMonth = today.with({ day: 1 }).add({ months: 1 });

// 4. 带时区的时间点，IANA 名称，夏令时自动正确
const zdt = Temporal.Now.zonedDateTimeISO('Asia/Shanghai');
console.log(zdt.toString());
// 2026-10-31T21:40:59.649781982+08:00[Asia/Shanghai]

// 5. 时区换算：同一时刻在上海与纽约各是什么挂钟时间
const ny = zdt.withTimeZone('America/New_York');
console.log(ny.toString());  // 2026-10-31T09:40:59...-04:00[America/New_York]

// 6. Duration 做时间算术
const dur = Temporal.Duration.from({ hours: 2, minutes: 30 });
console.log(zdt.add(dur).toString());
```

### 夏令时：Date 时代的隐形炸弹

经典错误：用 `Date` 加 24×3600×1000 毫秒来"加一天"，在夏令时切换日会得到错误的挂钟时间。Temporal 区分了这两种语义：

```javascript
const springForward = Temporal.PlainDateTime.from('2026-03-08T01:00')
  .toZonedDateTime('America/New_York');

// 加"自然日"：跟随日历，跳过消失的一小时
springForward.add({ days: 1 }).toString();  // 2026-03-09T01:00:00-04:00
// 加"绝对时长"：真实的 24 小时
springForward.add({ hours: 24 }).toString();  // 2026-03-09T02:00:00-04:00
```

### 其它值得知道的点

- **比较**：`Temporal.PlainDate.compare(a, b)` 返回 -1/0/1，可以直接喂给 `sort()`；
- **取整**：`zdt.round({ smallestUnit: 'hour' })`，比手写分钟秒清零干净；
- **日历**：`Temporal.PlainDate.from('2026-10-31[u-ca=chinese]')` 可选非公历日历；
- **迁移**：`Temporal.Instant.fromEpochMilliseconds(date.getTime())` 与旧 `Date` 互转。

## 二、显式资源管理：using / await using

### 以前我们怎么收拾资源

```javascript
function readConfig() {
  const handle = openFile('config.json');
  try {
    return parse(handle.read());
  } finally {
    handle.close();  // 忘写这行就是资源泄漏
  }
}
```

`try/finally` 的本质问题是：**清理义务完全靠自觉**。数据库连接、文件句柄、锁、WebSocket、定时器……每个都靠人肉 finally，嵌套一深就开始互相套娃。

### using：把"清理"写进作用域语义

```javascript
function handle() {
  using handle = getResource();   // 声明即注册
  handle.doSomething();
}  // 块结束 → 自动调用 handle[Symbol.dispose]()
```

任何对象只要实现 `Symbol.dispose` 就能用：

```javascript
const timer = {
  [Symbol.dispose]() { clearTimeout(this.id); },
  id: setTimeout(log, 1000),
};

function tick() {
  using t = timer;
  /* ... */
}  // 离开作用域自动 clearTimeout
```

### await using：异步资源

```javascript
async function run() {
  await using conn = await getConnection();
  await conn.query('SELECT 1');
}  // 自动 await conn[Symbol.asyncDispose]()
```

### 细节与高级用法

- **释放顺序**：多个 `using` 按**声明的逆序**释放（LIFO），与栈语义一致；
- **错误处理**：块内抛错时依然执行释放；如果释放本身也抛错，会以 `SuppressedError` 挂在原始错误的 `cause` 上，不会吞掉业务异常；
- **DisposableStack**：把多个资源打包装箱，还支持 `defer`（注册任意清理函数）与 `move()`（移交所有权）：

```javascript
function transaction() {
  using stack = new DisposableStack();
  const db = stack.use(openDb());        // use: 需要 Symbol.dispose 的资源
  const lock = stack.adopt(acquire(), release);  // adopt: 自定义释放函数
  stack.defer(() => console.log('last'));        // defer: 纯回调
  db.write(...);
}  // 逆序执行：last → release(lock) → db.dispose()
```

- **for-of 也能用**：`for (using res of pool) { ... }` 每轮迭代结束即释放。

和 Python 的 `with`、C# 的 `using`、Java 的 try-with-resources 是同一个思想：**资源生命周期跟随词法作用域**，编译期（解析期）就确定，不依赖运行时约定。

## 三、JSON.rawJSON：解析时不再丢信息

### 两个老问题

1. `JSON.parse` 之后，原始文本就没了——想区分 `1` 与 `1.0`、想知道原始书写格式，做不到；
2. 大整数精度丢失：`JSON.parse('{"id": 12345678901234567890}')` 直接得到一个不精确的 Number。

### 解析侧：reviver 拿到 source

```javascript
const json = JSON.parse('{"id": 12345678901234567890}', (key, value, context) => {
  if (context && context.source) {
    return BigInt(context.source);   // 用原始文本转 BigInt，精度无损
  }
  return value;
});
console.log(json.id);  // 12345678901234567890n
```

### 序列化侧：原样写出字面量

```javascript
const payload = { id: JSON.rawJSON("12345678901234567890") };
console.log(JSON.stringify(payload));
// {"id":12345678901234567890}  —— 不带引号，原样输出
```

`JSON.isRawJSON(x)` 可用于判断某值是否为 rawJSON 节点。配合使用，JSON 第一次成为"可无损往返"的格式：**进来什么样，出去还是什么样**。

## 四、Math.sumPrecise：浮点求和不再翻车

```javascript
const prices = [19.99, 5.5, 3.33, 0.1];
const total = prices.reduce((sum, n) => sum + n, 0);
console.log(total);  // 28.920000000000005 😱

console.log(Math.sumPrecise(prices));  // 28.92 ✅
```

### 原理：Neumaier 补偿求和

朴素累加的每一步都会产生舍入误差并传给下一步；Neumaier 算法在每次相加时**把"这一步丢掉的误差"单独记在一个补偿项里**，最后统一回补，误差从 O(n·ε) 降到接近单次舍入的水平。

几个实用细节：

- 接受**任意可迭代对象**——数组、`Map.values()`、生成器都行：

```javascript
function* numbers() { yield* [19.99, 5.5, 3.33, 0.1]; }
Math.sumPrecise(numbers());  // 28.92
```

- 它解决的是**求和路径**的误差，不是十进制表示问题——金额场景如果要严格十进制（分、厘），仍需整数分或 decimal 库；
- 空输入返回 0；`Infinity`/`NaN` 按浮点规则短路。

## 五、Uint8Array 原生 Base64 / Hex：告别 btoa

`btoa`/`atob` 的历史包袱：只认 Latin1，中文直接抛 `InvalidCharacterError`；Node 里又得用 `Buffer`，前后端两套代码。ES2026 把编解码下沉到 `Uint8Array`：

```javascript
// Base64
const bytes = Uint8Array.fromBase64("aGVsbG8gd29ybGQ=");
console.log(new TextDecoder().decode(bytes));  // "hello world"
console.log(bytes.toBase64());                 // "aGVsbG8gd29ybGQ="

// Hex
const hex = Uint8Array.fromHex("48656c6c6f");
console.log(new TextDecoder().decode(hex));    // "Hello"
console.log(hex.toHex());                      // "48656c6c6f"
```

### 选项与实例方法

```javascript
// URL 安全字母表 + 宽容处理被截断的末块
Uint8Array.fromBase64(str, { alphabet: 'base64url', lastChunkHandling: 'loose' });

// 零拷贝风格：写进现有缓冲区（大文件分块处理友好）
const buf = new Uint8Array(1024);
buf.setFromBase64(chunk);
```

编解码错误（非法字符、奇数长度 hex 等）统一抛 `SyntaxError`，比 `atob` 各引擎各表现的错误语义规范得多。

## 六、Error.isError：跨 realm 的错误判定

### instanceof 为什么会失手

每个 realm（页面、iframe、`vm` 模块、Worker）都有自己的 `Error` 构造器。iframe 里抛出的错误对象，在主页面上 `instanceof Error` 是 **false**：

```javascript
const err = iframe.contentWindow.eval('new Error("boom")');
err instanceof Error;      // false ❌（两边 Error 构造器不同）
```

鸭子类型（`err instanceof Object && 'message' in err`）也不可靠——任何带 `message` 的对象都能冒充。

### 现在的写法

```javascript
if (Error.isError(maybeError)) {
  console.error(maybeError.message);  // 任何 realm 的真 Error 都能识别
}
```

`Error.isError` 检查的是对象内部的 **ErrorData 内部槽**，跨 realm 依然有效，且覆盖 `TypeError`/`RangeError` 等子类。典型的适用场景：`window.onerror`、`unhandledrejection`、跨 iframe 的 RPC 错误透传。

## 七、Array.fromAsync：异步迭代器一步转数组

```javascript
// 分页拉取、流式读取这类异步迭代器
// 以前：手动 for await 循环 push
const chunks = [];
for await (const page of fetchPages()) chunks.push(...page);

// 现在：
const chunks2 = await Array.fromAsync(fetchPages());
```

它同样接受**同步可迭代对象**（元素是 Promise 也会被逐个 await），并支持 `mapFn`：

```javascript
const ids = await Array.fromAsync(fetchUserIds(), id => id.toString());
```

与 `Promise.all(Array.from(x))` 的区别：后者先把**所有** Promise 同时启动；`fromAsync` 按**迭代顺序逐个 await**——流式/有背压语义的场合用 `fromAsync`，纯并发场合用 `Promise.all`。

## 八、Iterator.concat：惰性合并迭代器

```javascript
const evens = [2, 4, 6].values();
const odds  = [1, 3, 5].values();

for (const n of Iterator.concat(evens, odds)) {
  console.log(n);  // 2 4 6 1 3 5
}
```

要点：

- **惰性**：元素在被消费时才产出，适合大流/无限迭代器拼接，不会像 `[...a, ...b]` 那样先物化整个数组；
- 参数是**任意迭代器**（不限于数组），一次性消费；
- 与 ES2025 的 Iterator helpers（`.map()/.filter()/.take()`）组合使用更香：

```javascript
Iterator.concat(evens, odds).filter(n => n > 2).take(3);
```

## 九、Map.prototype.getOrInsert：Upsert 三行变一行

```javascript
// 以前
if (!map.has(key)) map.set(key, computeDefault(key));
const v = map.get(key);

// 现在：值已存在直接返回，不存在才插入并返回
map.getOrInsert(key, computeDefault(key));
```

两个细节：

- **`computeDefault(key)` 是立即求值的**——即使 key 已存在也会执行。工厂函数本身开销大时用惰性版本：

```javascript
map.getOrInsertComputed(key, () => expensiveCompute(key));
// 回调只在 key 不存在时执行
```

- 典型场景是**缓存/注册表**：memoize 一行搞定，且语义就是"原子化的 check-then-set"。

## 兼容性一览

| 特性 | Node.js 24 LTS | Node.js 26 | Chrome | Firefox |
|---|---|---|---|---|
| Temporal | `--harmony-temporal` | ✅ 默认 | 144+ | 139+ |
| using / await using | ✅ | ✅ | 134+ | 133+ |
| Math.sumPrecise / Error.isError / Iterator.concat | ✅ | ✅ | 133+ | 137+ |
| Uint8Array Base64/Hex | ✅ | ✅ | 140+ | 133+ |
| Array.fromAsync / getOrInsert | ✅ | ✅ | 近期版本 | 141+ |
| JSON.rawJSON | 部分 | ✅ | 142+ | 近期版本 |

## 如何在项目里渐进采用

1. **特性探测**而不是 UA 嗅探：

```javascript
if (typeof Temporal !== 'undefined') { /* 新路径 */ } else { /* 旧路径/垫片 */ }
```

2. **垫片**：core-js 覆盖了标准库类特性（sumPrecise、getOrInsert、fromAsync 等）；Temporal 用官方 `@js-temporal/polyfill`；`using` 语法类特性无法垫，等运行环境或用 Babel 插件转译；
3. **TypeScript**：升级到新版本 TS 并把 `lib` 里加上 `ES2026`（Temporal/using 的类型支持随版本跟进）；
4. **Node**：24 LTS 加 `--harmony-temporal` 灰度，26 起默认全量。

## 小结

ES2026 的主题非常清晰：**不再发明新语法，而是把开发者天天手写的轮子收编进标准库**——合并迭代器 `Iterator.concat()` 一行搞定、Map 的"不存在才插入" `getOrInsert`、浮点求和 `sumPrecise` 保精度、Base64/Hex 原生化、错误判定跨 realm。再加上 **Temporal** 和 **using** 这两块真正的地基升级，说它是 ES2015（ES6）以来含金量最高的版本之一并不夸张。

## 参考

- [ECMAScript 2026 语言规范（ECMA-262 第 17 版）](https://tc39.es/ecma262/2026/)
- [TC39 提案进程](https://github.com/tc39/proposals)
- [MDN Temporal 文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Temporal)
- [Temporal 提案文档（TC39）](https://tc39.es/proposal-temporal/docs/)
