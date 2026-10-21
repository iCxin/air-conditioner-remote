<div align="right"><sub><code>iCxin / air-conditioner-remote</code></sub></div>

# 空调遥控器 · AC Remote

> 一个纯前端的拟物空调遥控器：单个 HTML 文件，零依赖，双击就能用。
> A skeuomorphic air-conditioner remote in one HTML file, with no dependencies and no build step.

| | |
|:--|:--|
| **状态** | 活跃维护 |
| **栈** | `HTML` · `CSS` · `Vanilla JavaScript`（无构建） |
| **平台** | 浏览器（Chrome / Edge / Firefox / Safari） |
| **许可** | MIT |

---

## 01 · 这是什么

打开一个 HTML 文件，得到一个手感接近实体的空调遥控器。没有构建步骤、没有包管理器、没有 CDN 请求，全部逻辑与样式都封装在单个 `index.html` 里，拷到 U 盘里也能跑。

界面按实体遥控器来做：红外发射窗、电源指示灯、带玻璃反光的液晶屏、按下会下沉的圆形按键。切换模式时整页主题跟着变色，制冷偏冷蓝，制热偏暖橙。它不能真的控制空调，原因见「常见问题」。

## 02 · 特性

- **五种模式**：制冷 / 除湿 / 送风 / 制热 / 自动循环切换，每种模式有独立的主题色。
- **16–30 °C 调温**：带弹跳动画，支持长按连续调节，到边界响提示音而不是静默失灵。
- **四档风速**：自动 / 低风 / 中风 / 高风，液晶屏用三根渐高竖条实时指示，自动档走呼吸动画。
- **扫风、定时、睡眠、节能**：定时支持关 / 0.5 / 1 / 2 / 3 / 4 / 5 / 6 / 8 / 10 / 12 小时，各项状态在屏幕右下角以小旗标点亮。
- **合成按键音**：Web Audio API 实时生成，不加载任何音频文件；电源键是双音，开机与关机的音高相反。
- **全局主题联动**：一个 CSS 变量同时驱动液晶屏文字色、温度辉光、按键激活色与整页环境光，过渡 1 秒。
- **键盘全可控**：方向键调温、空格开关机，触发时对应按键播放按压动画。
- **零依赖自适应**：无框架、无字体请求，窄屏自动收缩尺寸，手机上也能用。

## 03 · 开始使用

下载 `index.html`，双击即可。

从源码运行：

```bash
git clone https://github.com/iCxin/air-conditioner-remote.git
cd air-conditioner-remote
```

克隆后在 Windows 上双击 `index.html`（或执行 `start index.html`），macOS 上执行 `open index.html`。也可以起一个本地服务（直接打开已经足够）：

```sh
python -m http.server 8000
```

预期结果：浏览器显示一台白色遥控器，屏幕亮起「制冷 26 °C」。

嵌入已有项目：`index.html` 中的 `<style>`、`<main>`、`<script>` 三块可以整体搬进任何页面，彼此不依赖外部资源；所有样式都限定在遥控器自身的类名下，不会污染宿主页面。

## 04 · 操作

| 按键 | 行为 |
| --- | --- |
| 电源 | 开关机；关机后液晶屏淡出、指示灯熄灭，页面环境光一并转灰 |
| 模式 | 制冷 → 除湿 → 送风 → 制热 → 自动 循环 |
| 风速 | 自动 → 低风 → 中风 → 高风 循环 |
| 升温 / 降温 | 16–30 °C 步进 1 °C；长按 0.46 秒后进入连续调节 |
| 扫风 | 导风板上下扫风开关 |
| 定时 | 关 / 0.5 / 1 / 2 / 3 / 4 / 5 / 6 / 8 / 10 / 12 小时循环 |
| 睡眠 | 睡眠模式开关 |
| 节能 | 节能模式开关 |

关机状态下按任意功能键会自动开机，与实体遥控器的唤醒行为一致。

键盘：方向键调温，空格开关机，`M` 模式，`F` 风速，`S` 扫风，`T` 定时，`N` 睡眠，`E` 节能。

## 05 · 设计说明

主题色只做一件事：把当前模式的颜色写进根元素的自定义属性。

```js
document.documentElement.style.setProperty('--accent', MODES[state.mode].color);
```

液晶屏文字、温度辉光、激活按键的描边，以及覆盖整页的那片 150 px 模糊光斑，全部引用 `var(--accent)`，所以换主题不需要逐处改样式。

温度辉光用 `filter: drop-shadow(0 0 13px var(--accent))`，而不是 `text-shadow`：后者要给颜色附加透明度，只能靠 `color-mix()` 从变量派生，而 `drop-shadow` 直接吃变量，兼容面更宽。

液晶屏是外层浅色边框加内层 `radial-gradient` 深底，用 `inset` 阴影制造凹陷，再用 `::after` 伪元素铺一层斜向白色渐变模拟玻璃反光。关机时屏内内容整体降低不透明度并加 4 px 模糊，而不是直接消失。

## 06 · 文件结构

```text
air-conditioner-remote/
├── index.html                      # 全部实现，可直接运行
├── air-conditioner-remote.html     # 同一文件的副本，保留原文件名便于识别
├── docs/
│   ├── screenshot-cool.png         # 制冷模式截图
│   └── screenshot-heat.png         # 制热模式截图
├── LICENSE
└── README.md
```

## 07 · 常见问题

| 问题 | 回答 |
| --- | --- |
| 能真的控制空调吗？ | 不能。浏览器无法发射红外信号，这是交互与视觉的模拟；接真实空调需要后端加红外发射硬件（如 ESP8266 红外模块），页面发指令、硬件负责发射。 |
| 点了按键没有声音？ | 浏览器要求页面先有用户交互才允许播放音频，随便按一下即可激活；仍无声则检查页面底部的「声音」开关。 |
| 想改默认温度或模式？ | 在 `index.html` 里搜 `var state = {`，初始状态集中在那里。 |
| 浏览器支持？ | 只依赖 CSS 自定义属性、CSS Grid、`pointerdown` 与 Web Audio API，近年任何版本的 Chrome / Edge / Firefox / Safari 都能渲染，IE 不支持；音频走 `try/catch`，`AudioContext` 创建失败不影响其余交互。 |

## 08 · 许可

[MIT](LICENSE)，2026 iCxin。

---

<div align="right"><sub>从像素到混凝土 · <a href="https://github.com/iCxin">iCxin</a></sub></div>
