# WaveDrom 常用语法参考

需要查 WaveJSON token、`node/edge`、`period/phase`、图级配置或 HTML 集成时读取本文件。生成与审阅流程以主 `SKILL.md` 为准。

## 顶层结构

```json5
{
  signal: [],
  edge: [],
  head: {},
  foot: {},
  config: {}
}
```

- `signal`：信号、分组和间隔组成的数组。
- `edge`：节点间关系或测量箭头。
- `head`、`foot`：标题、脚注和时间刻度。
- `config`：水平缩放和皮肤等渲染配置。

WaveDrom CLI 接受 JSON5。具体 Markdown 插件可能只接受严格 JSON，按目标渲染器选择引号和尾逗号等写法。

## 信号字段

常用字段包括：

- `name`：信号名。
- `wave`：波形 token 字符串。
- `data`：数据区段的标签，可使用数组或空格分隔字符串。
- `period`：拉伸该信号的周期比例。
- `phase`：水平移动该信号，可使用小数表达亚周期偏移。
- `node`：与波形位置对应的节点标记，供 `edge` 引用。

```json5
{
  name: 'data',
  wave: 'x.34.5x',
  data: ['A5', '3C', 'idle']
}
```

## `wave` token

### 电平与状态

- `0`、`1`：确定的低、高电平。
- `x`：未知或无关状态。
- `z`：高阻状态。
- `u`、`d`：上拉、下拉状态。
- `=`、`2` 至 `9`：不同样式的数据区段，与 `data` 配合使用。

### 时钟与电平片段

- `p`、`n`：正、负极性时钟。
- `P`、`N`：带工作边沿标记的正、负极性时钟。
- `h`、`l`：时钟相关的高、低电平片段。
- `H`、`L`：带边沿标记的高、低电平片段。

### 延续、间隔与子周期

- `.`：延续前一状态一个时间单位。
- `|`：延续时间轴并显示间隔标记。
- `<`、`>`：进入和退出子周期表达；使用前在目标版本中实际渲染验证。

不要仅统计字符串字符数判断多条信号是否对齐。`period`、`phase`、间隔和子周期都会影响最终时间位置。

## 分组与空白

使用嵌套数组定义命名分组：

```json5
{
  signal: [
    ['Master',
      { name: 'req', wave: '0.1..0' },
      { name: 'data', wave: 'x.3..x', data: 'D0' }
    ],
    {},
    ['Slave',
      { name: 'ack', wave: '0...10' }
    ]
  ]
}
```

- 数组第一个元素是组名，后续元素是信号或子组。
- 空对象 `{}` 插入垂直空白。
- 避免仅为视觉效果创建多层嵌套。

## `node` 与 `edge`

`node` 字符串中的非点字符声明节点；节点位置与该信号的时间位置对应。节点名应在一张图内保持唯一。

```json5
{
  signal: [
    { name: 'req', wave: '0.1..', node: '..a..' },
    { name: 'ack', wave: '0...1', node: '....b' }
  ],
  edge: ['a->b response']
}
```

常见连线形式包括：

- `a->b`：带箭头的直线。
- `a~>b`：带箭头的曲线。
- `a-|>b`：带折角的箭头。
- `a<->b`：双向箭头。
- 在表达式后添加空格和文字作为标签。

复杂连线语法容易受节点位置和版本影响，应在目标渲染器中确认，不要凭视觉猜测连接目标。

## `period` 与 `phase`

```json5
{
  signal: [
    { name: 'clk', wave: 'P.......', period: 2 },
    { name: 'cmd', wave: 'x.3x=4x', data: 'READ NOP WRITE', phase: 0.5 }
  ]
}
```

- `period` 改变单个 lane 的水平周期比例。
- `phase` 水平移动单个 lane；负值或小数值需要在目标版本验证可见范围。
- 对齐检查应比较渲染后的边沿、区段和节点，而不是原始字符串长度。

## `config`、`head` 与 `foot`

- `config.hscale`：水平缩放倍数，使用正整数。
- `config.skin`：选择目标环境提供的皮肤，例如 `default` 或 `narrow`。
- `head.text`、`foot.text`：标题或脚注。
- `tick`：在时间边界显示刻度值。
- `tock`：在时间区间中间显示刻度值。
- `every`：每隔 N 个周期显示刻度。

`tick`、`tock` 和 `every` 可用于 `head` 或 `foot`；不同版本和宿主的布局可能不同，应通过实际渲染确认。

```json5
{
  signal: [
    { name: 'clk', wave: 'p.......' }
  ],
  head: { text: 'SPI Frame', tick: 0, every: 2 },
  foot: { text: 'mode 0', tock: 1 },
  config: { hscale: 2 }
}
```

## 渲染入口

### Markdown 围栏

只有宿主明确支持 WaveDrom 扩展时才会渲染：

```wavedrom
{ "signal": [{ "name": "clk", "wave": "p...." }] }
```

如果宿主只显示代码块，改用其支持的插件、生成 SVG，或选择完整 HTML 集成。

### CLI

环境已安装 WaveDrom CLI 时，将 JSON5 源文件渲染为 SVG：

```sh
wavedrom --input source.json5 > output.svg
```

### HTML

下面是依赖 CDN 的最小完整结构；离线或受控发布环境应改为加载本地脚本。

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <script src="https://cdn.jsdelivr.net/npm/wavedrom@3/wavedrom.min.js"></script>
</head>
<body onload="WaveDrom.ProcessAll()">
  <script type="WaveDrom">
  { signal: [
    { name: "clk", wave: "p...." }
  ]}
  </script>
</body>
</html>
```

## 查错顺序

1. 检查 JSON 或 JSON5 是否符合目标入口支持的格式。
2. 检查 `wave` 是否含非法 token。
3. 检查数据区段与 `data` 标签是否按预期对应。
4. 检查 `node` 位置、节点名与 `edge` 引用。
5. 检查 `period`、`phase`、间隔和刻度造成的时间偏移。
6. 在最小图中复现后，再逐项恢复分组、箭头和配置。

## 参考来源

- [WaveDrom Tutorial](https://wavedrom.com/tutorial.html)
- [WaveDrom 官方仓库 README](https://github.com/wavedrom/wavedrom)
