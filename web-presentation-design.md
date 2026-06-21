# Web Presentation Design Guidelines
**基于 Starbucks 规范与放映机视觉调整的最佳实践**

这份指导文件汇总了我们在本项目中确立的 Web 幻灯片设计规范。后续开发可以直接参考此文档，确保样式、布局、字体和色彩符合高可用性、高对比度的工效学要求。

---

## 0. AI Agent 生成强制规则 (STRICT RULES FOR AGENT)

**在接手任何新幻灯片生成或修改任务时，AI Agent 必须严格遵守以下约定：**

1. **必要信息获取（未提供必须提问）**：
   - 用户必须指定：**幻灯片尺寸**（如 16:9 或 4:3）、**汇报主题**、**汇报人姓名**。
   - **强制阻断**：如果用户在提示词中未主动提供上述信息，Agent **必须主动向用户提问**获取，未确认前不允许盲目生成。
2. **Title 与主题一致**：网页的 `<title>` 标签内容必须与用户指定的**汇报主题**保持完全一致。
3. **禁止篡改基础属性**：本规范中确立的所有字体大小（`cqw` 单位）、排版比例等底层参数，**不能随意变动**。
4. **严禁深色背景**：所有幻灯片背景统一使用护眼的浅色背景（`White` 或 `Cream`）。**严禁采用深色背景**。
5. **严禁图片直接嵌入页面**：**绝不允许**将图片直接写死在幻灯片卡片内占位。所有图片的展示**一定要采用“按 ↓ 键呼出”的全局遮罩（Modal）方式**。如果大纲提及需要展示图片，页面上只需通过文字提示即可。

---

## 1. 核心布局规范 (The 1/7 Rule)

为了避免幻灯片内容顶满屏幕带来的“紧张提肛感”，并保证所有页面的视觉连贯性，我们采用了**绝对稳定的百分比布局**，核心原则是上下各留白约 `1/7 (14%)`。

- **安全边界**：页面顶部 `0-14%` 和 底部 `86%-100%` 属于安全留白区（仅用于背景或进度条，不可放主内容）。
- **标题区 (Slide Header)**：
  - `position: absolute; top: 14%; left: 7%; width: 86%; height: 12%;`
  - 使用 `flex-end` 底部对齐，确保即使标题行数变化，正文的起始线也绝对统一。
- **内容区 (Slide Content)**：
  - `position: absolute; top: 30%; left: 7%; width: 86%; height: 56%;`
  - 占据中间约 `5/7` 的区域，内容自适应排布。
- **绝对稳定**：摒弃让外层容器 `justify-content: center` 导致的跳动，采用固定 `top` 值锁定每一页的基准线。

---

## 2. 颜色体系 (Color Palette)

色彩融合了 Starbucks 的质感，但针对**放映机低对比度**的痛点进行了增强：

### 核心背景 (Canvas)
放映机对深色背景非常不友好（吸光、对比度差）。
- 🟢 **推荐使用**：
  - **Cream (奶油暖白)**: `var(--canvas-warm)` `#f2f0eb` — 首选主背景，给人温和不刺眼的呼吸感。
  - **White (纯白)**: `var(--surface-white)` `#ffffff` — 用于卡片或次要交替背景。
- ❌ **严禁使用**：
  - 纯黑或深色主背景（如原来的 `data-theme="dark"`）。

### 品牌主色 (Greens)
- **Starbucks Green**: `#006241` — 用于大标题、强调重点。
- **Green Accent**: `#00754A` — 用于进度条、重点强调的边框、激活状态。
- **Green House**: `#1E3932` — 用于极少部分需要重色压阵的文本或图表头。
- **Green Light**: `#d4e9e2` — 用于卡片底色、提示框背景（浅色护眼）。

### 高对比度文本 (Text Colors)
**彻底弃用细灰字体**（Slim Grey），提升字重视认度：
- **Primary Text**: `rgba(0, 0, 0, 0.95)` (不再是原来的 0.87) — 近乎纯黑，确保投屏绝对清晰。
- **Secondary Text**: `rgba(0, 0, 0, 0.85)` (不再是原来的 0.58) — 作为副文本，依旧保持极高的对比度。

---

## 3. 字体与排版 (Typography)

- **字体栈**: `'Inter', 'Noto Sans SC', -apple-system, sans-serif`
- **字间距**: `letter-spacing: -0.01em` (紧凑的质感)
- **字重策略**（向上升档）：
  - 标题：`800` 或 `900`
  - 小标题/强调用语：`700` 或 `800`
  - 正文文本：最低 `500` 或 `600`（不要使用 `400` 常规体，容易在强光下发虚）。

### 尺寸参考 (基于 `cqw`)
为了实现绝对锁定的 `4:3` 容器内自适应缩放，并保证完美的 PDF 打印输出，本项目使用 **`cqw` (Container Query Width)** 作为排版单位，容器为 `#slideFrame`：
- **主标题 (Slide Title)**: `4cqw` 左右，极简设计中可放大至 `6.67cqw`
- **内容块标题 (Card Value/Label)**: `2cqw` 或 `2.13cqw`
- **正文内容 (Card Desc/List/Flow)**: `2cqw` 到 `2.4cqw` 之间（保障会议室远距离可读性）
- **微注释/徽章 (Badge/Small)**: `1.47cqw` 到 `1.6cqw`

---

## 4. 组件细节与坑点排雷 (Components & Gotchas)

### 卡片与列表 (Cards & Lists)
- **圆角**: `var(--card-radius)` 统一为 `12px`。
- **阴影**: `0px 0px .5px 0px rgba(0,0,0,0.14), 0px 1px 1px 0px rgba(0,0,0,0.24)`（极轻微阴影，依靠背景色差体现层级）。
- **⚠️ 列表溢出坑点**：不要在 `.clist` (Card List) 内部强行使用 `flex: 1; justify-content: space-evenly;`。如果在小尺寸屏幕上，等距分散会直接撑爆卡片底边。应使用 `margin-top: 1.5cqw; gap: 1cqw;` 让其自然自上而下排布。

### 流程与逻辑链 (Flow Rows)
- 表示步骤的 `A -> B -> C` 流程块，务必设置 `flex-wrap: nowrap`，并适度调整内边距（如 `padding: 1.33cqw 2cqw`）及字号，确保它们永远在一行显示，不可折行。

### 并排对比布局 (Two-Column Compare)
- `人优于机器` vs `机器优于人` 这类对比，使用 `display: grid; grid-template-columns: 1fr 1fr;` (`.two-col`)，而不是垂直的弹性布局。

### 动画 (Animations)
- 仅使用低干预的过渡动效。
- `.ani.d1`, `.ani.d2` 依次类推的 `fadeUp` (上浮渐出)，位移不要超过 `15px`，以不干扰用户的阅读预期为核心原则。

---

## 5. 设计审查清单 (Checklist for New Slides)

1. [ ] 内容是否限定在屏幕纵向 `14%` 到 `86%` 之间？
2. [ ] 是否使用了深色文字 (`opacity >= 0.85`) 和浅色背景 (`Cream` 或 `White`)？
3. [ ] 正文字重是否达到 `500` 以上？
4. [ ] 逻辑链或双列内容是否做好了防溢出处理（`nowrap`, 适度的 `gap`）？
5. [ ] 主标题是否对齐在左上角固定的基准线上？

---

## 6. 原生高清 PDF 导出 (Native PDF Export)

为了保证最极致的**“所见即所得”**，并且保留真正的矢量高清文字（文字可选中、可无限放大），本项目彻底重构了导出架构，结合 `@media print` 与 `cqw` 容器查询，实现完美的浏览器原生打印。

**全新导出流程：**
1. **一键触发**：点击页面导航栏右上角的 **[PDF]** 按钮，系统会弹出前置参数指引。
2. **调起打印**：点击确定后，会自动调起浏览器的原生打印预览面板。
3. **参数配置（极其关键）**：
   - **目标打印机**：`另存为pdf`
   - **边距**：选择 `无`
   - **选项**：勾选 `背景图形`
4. **无损导出**：点击保存即可生成 4:3 画幅的汇报级 PDF！所有基于 `cqw` 的字号比例、元素排版都会完美映射到物理纸张尺寸上，绝不会发生缩小或错乱。
5. **图片PDF导出支持**：虽然图片平时通过按 ↓ 键浮层展示，但系统在后台自动为带有图片的页面插入了专属的 `.print-image-slide` 打印隐藏页，导出 PDF 时会自动展示在对应页面的正后方。

---

## 7. 图片弹窗方案 (全局按 ↓ 键呼出，禁止页面内嵌)

为应对复杂的演示场景并保持幻灯片本体排版整洁，**所有相关图表和配图强制通过全局弹窗展示，禁止在 HTML 的页面结构内直接使用 `<img src="...">` 标签占位**。设计逻辑如下：
- **触发机制**：点击右上角 👁 按钮或按键盘 **向下方向键 (↓)**。
- **单图模式**：图片需放在与 HTML 同级的目录下，命名格式为 `X.png` (X 为当前页码，即展示的 `13/16` 中的 13，例如 `6.png`)。
- **多图轮换**：若当前页需要展示多张图片，请严格按递增后缀命名：`X.png`, `X.1.png`, `X.2.png`, `X.3.png`... (例如：`6.png`, `6.1.png`, `6.2.png`...)。在弹窗打开状态下，继续按 **向下方向键 (↓)** 将自动轮换至下一张。当穷尽所有图片时，弹窗自动优雅关闭。
- **静默防尴尬**：系统在每次调起时会自动探测对应文件。如果某页没有配置图片，系统将**完全静默，不触发任何操作或报错**，保障汇报的绝对流畅。

---

## 8. 附录：HTML 幻灯片脱敏模板 (Appendix: Sanitized HTML Template)

此模板包含了所有必需的 CSS 和 JS 逻辑（包括自动注入图片打印页的逻辑 `.print-image-slide`），以及封面、双列、图片呼出引导等经典页面的结构参考。下次生成幻灯片时，可直接复用此基础代码框架，只需替换并扩充 `.slide` 节点即可。

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[填写主题/Title]</title>
  <link
    href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=Noto+Sans+SC:wght@400;500;600;700;900&display=swap"
    rel="stylesheet">
  <style>
    :root {
      --green-starbucks: #006241;
      --green-accent: #00754A;
      --green-house: #1E3932;
      --green-uplift: #2b5148;
      --green-light: #d4e9e2;
      --gold: #cba258;
      --gold-light: #dfc49d;
      --canvas-warm: #f2f0eb;
      --canvas-ceramic: #edebe9;
      --surface-white: #ffffff;
      --surface-cool: #f9f9f9;
      --text-black: rgba(0, 0, 0, 0.95);
      /* Darker black */
      --text-black-soft: rgba(0, 0, 0, 0.85);
      /* Much darker, no slim gray */
      --text-white: #ffffff;
      --text-white-soft: rgba(255, 255, 255, 0.9);
      --red: #c82014;
      --card-radius: 12px;
      --btn-radius: 50px;
      --card-shadow: 0px 0px .5px 0px rgba(0, 0, 0, 0.14), 0px 1px 1px 0px rgba(0, 0, 0, 0.24);
      --font-sans: 'Inter', 'Noto Sans SC', -apple-system, sans-serif;
      --tracking: -0.01em;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html,
    body {
      width: 100%;
      height: 100%;
      overflow: hidden;
      font-family: var(--font-sans);
      background: #000;
      color: var(--text-black);
      -webkit-font-smoothing: antialiased;
      letter-spacing: var(--tracking);
    }

    #presentation {
      position: relative;
      width: 100vw;
      height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #000;
    }

    #slideFrame {
      container-type: inline-size;
      position: relative;
      width: 100%;
      height: 100%;
      max-width: calc(100vh * 4 / 3);
      max-height: calc(100vw * 3 / 4);
      overflow: hidden;
      background: var(--canvas-warm);
    }

    .slide {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      opacity: 0;
      transform: translateX(40px);
      transition: opacity 0.45s cubic-bezier(0.4, 0, 0.2, 1),
        transform 0.45s cubic-bezier(0.4, 0, 0.2, 1);
      pointer-events: none;
      background: var(--canvas-warm);
      overflow: hidden;
    }

    .slide.active {
      opacity: 1;
      transform: translateX(0);
      pointer-events: all;
    }

    .slide.exit-left {
      opacity: 0;
      transform: translateX(-40px);
    }

    /* ====== STABLE LAYOUT SYSTEM ====== */
    /* Header at ~1/7 (14%), Content in middle 5/7 (71%) */
    .slide-header {
      position: absolute;
      top: 14%;
      /* 1/7 from top */
      left: 7%;
      width: 86%;
      height: 12%;
      /* Ends at 26% */
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      border-bottom: 2px solid transparent;
    }

    .slide-content {
      position: absolute;
      top: 30%;
      /* Start after header + gap */
      left: 7%;
      width: 86%;
      height: 56%;
      /* Ends at 86%, leaving exactly 14% (1/7) at bottom */
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      gap: 3%;
    }

    .slide-content.stretch {
      justify-content: space-between;
    }

    /* ===== NAVIGATION - TOP RIGHT ===== */
    .nav-container {
      position: absolute;
      top: 3%;
      right: 3%;
      display: flex;
      align-items: center;
      gap: 6px;
      z-index: 100;
    }

    .nav-btn {
      width: 40px;
      height: 40px;
      border: 1px solid rgba(0, 0, 0, 0.15);
      border-radius: 50%;
      background: rgba(255, 255, 255, 0.7);
      backdrop-filter: blur(8px);
      color: var(--text-black);
      font-size: 18px;
      font-weight: 600;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.2s ease;
      font-family: var(--font-sans);
      line-height: 1;
    }

    .nav-btn:hover {
      background: var(--green-accent);
      color: var(--text-white);
      border-color: var(--green-accent);
      transform: scale(1.05);
    }

    .nav-btn:active {
      transform: scale(0.95);
    }

    .nav-btn:disabled {
      opacity: 0.25;
      cursor: not-allowed;
      transform: none;
    }

    .nav-dark .nav-btn {
      background: rgba(255, 255, 255, 0.15);
      border-color: rgba(255, 255, 255, 0.2);
      color: var(--text-white);
    }

    .nav-dark .nav-btn:hover {
      background: rgba(255, 255, 255, 0.3);
      border-color: rgba(255, 255, 255, 0.4);
    }

    .page-num {
      font-size: 13px;
      font-weight: 600;
      color: var(--text-black);
      min-width: 36px;
      text-align: center;
      font-variant-numeric: tabular-nums;
    }

    .nav-dark .page-num {
      color: var(--text-white);
    }

    .progress-bar {
      position: absolute;
      bottom: 0;
      left: 0;
      height: 4px;
      background: var(--green-accent);
      transition: width 0.4s cubic-bezier(0.4, 0, 0.2, 1);
      z-index: 100;
    }

    /* ===== SLIDE THEMES ===== */
    .slide[data-theme="white"] {
      background: var(--surface-white);
    }

    .slide[data-theme="cream"] {
      background: var(--canvas-warm);
    }

    .slide[data-theme="dark"] {
      background: var(--green-house);
      color: var(--text-white);
    }

    /* ===== TYPOGRAPHY ===== */
    .slide-badge {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      padding: 5px 16px;
      border-radius: var(--btn-radius);
      font-size: 1.6cqw;
      font-weight: 700;
      letter-spacing: 0.06em;
      text-transform: uppercase;
      margin-bottom: 1.5%;
      align-self: flex-start;
    }

    .badge-green {
      background: var(--green-light);
      color: var(--green-starbucks);
    }

    .badge-dark {
      background: rgba(255, 255, 255, 0.12);
      color: var(--text-white);
    }

    .slide-title {
      font-size: 4.53cqw;
      font-weight: 800;
      line-height: 1.2;
      color: var(--green-starbucks);
    }

    .slide[data-theme="dark"] .slide-title {
      color: var(--text-white);
    }

    h3.sub-heading {
      font-size: 2.4cqw;
      font-weight: 700;
      margin-bottom: 1.5%;
      color: var(--green-starbucks);
    }

    .slide[data-theme="dark"] h3.sub-heading {
      color: var(--green-light);
    }

    /* ===== CARDS ===== */
    .card {
      background: var(--surface-white);
      border-radius: var(--card-radius);
      padding: 3%;
      box-shadow: var(--card-shadow);
      display: flex;
      flex-direction: column;
    }

    .card.stretch {
      flex: 1;
    }

    /* Stretches card vertically */

    .slide[data-theme="dark"] .card {
      background: rgba(255, 255, 255, 0.08);
      box-shadow: none;
    }

    .card-grid {
      display: grid;
      gap: 2%;
    }

    .card-grid.stretch {
      flex: 1;
    }

    .g2 {
      grid-template-columns: 1fr 1fr;
    }

    .g3 {
      grid-template-columns: 1fr 1fr 1fr;
    }

    .g4 {
      grid-template-columns: 1fr 1fr 1fr 1fr;
    }

    .g5 {
      grid-template-columns: repeat(5, 1fr);
    }

    .card-label {
      font-size: 1.47cqw;
      font-weight: 700;
      color: var(--green-starbucks);
      margin-bottom: 1.07cqw;
      text-transform: uppercase;
      letter-spacing: 0.05em;
    }

    .slide[data-theme="dark"] .card-label {
      color: var(--green-light);
    }

    .card-value {
      font-size: 2.4cqw;
      font-weight: 800;
      color: var(--text-black);
      line-height: 1.3;
    }

    .slide[data-theme="dark"] .card-value {
      color: var(--text-white);
    }

    .card-desc {
      font-size: 2cqw;
      color: var(--text-black-soft);
      font-weight: 500;
      /* No slim gray */
      line-height: 1.6;
      margin-top: 1.07cqw;
      flex: 1;
    }

    .slide[data-theme="dark"] .card-desc {
      color: var(--text-white-soft);
    }

    /* ===== LISTS ===== */
    .clist {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 1.6cqw;
    }

    .clist li {
      display: flex;
      gap: 1.33cqw;
      font-size: 2.13cqw;
      color: var(--text-black-soft);
      font-weight: 500;
      line-height: 1.6;
    }

    .clist li .d {
      flex-shrink: 0;
      width: 0.8cqw;
      height: 0.8cqw;
      border-radius: 50%;
      background: var(--green-accent);
      margin-top: 0.93cqw;
    }

    .clist li strong {
      color: var(--text-black);
      font-weight: 700;
    }

    .slide[data-theme="dark"] .clist li {
      color: var(--text-white-soft);
    }

    .slide[data-theme="dark"] .clist li strong {
      color: var(--text-white);
    }

    .slide[data-theme="dark"] .clist li .d {
      background: var(--green-light);
    }

    /* ===== TABLE ===== */
    .dtable {
      width: 100%;
      border-collapse: separate;
      border-spacing: 0;
      font-size: 2cqw;
      border-radius: var(--card-radius);
      overflow: hidden;
      border: 1px solid rgba(0, 0, 0, 0.08);
      font-weight: 500;
    }

    .dtable thead th {
      background: var(--green-house);
      padding: 1.6cqw 2cqw;
      text-align: left;
      font-weight: 700;
      color: var(--text-white);
    }

    .dtable tbody td {
      padding: 1.33cqw 2cqw;
      border-bottom: 1px solid rgba(0, 0, 0, 0.06);
      color: var(--text-black-soft);
      line-height: 1.5;
    }

    .dtable tbody td strong {
      color: var(--text-black);
      font-weight: 700;
    }

    /* ===== OTHER BLOCKS ===== */
    .quote-block {
      padding: 3%;
      border-left: 5px solid var(--green-accent);
      background: var(--green-light);
      border-radius: 0 var(--card-radius) var(--card-radius) 0;
      font-size: 2.13cqw;
      line-height: 1.6;
      font-weight: 600;
      color: var(--green-starbucks);
    }

    .hbox {
      padding: 2% 3%;
      border-radius: var(--card-radius);
      background: var(--green-light);
      font-size: 2.4cqw;
      line-height: 1.5;
      color: var(--green-house);
      text-align: center;
      font-weight: 600;
    }

    .stat-num {
      font-size: 6cqw;
      font-weight: 900;
      color: var(--green-starbucks);
      line-height: 1;
    }

    .stat-lab {
      font-size: 1.6cqw;
      color: var(--text-black-soft);
      font-weight: 600;
      margin-top: 1.07cqw;
    }

    .two-col {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 4%;
    }

    .two-col.stretch {
      height: 100%;
    }

    /* Fill available space */
    .col-flex {
      display: flex;
      flex-direction: column;
      gap: 4%;
      height: 100%;
    }

    /* ===== TIMELINE ===== */
    .timeline {
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding-left: 2.67cqw;
      position: relative;
      height: 100%;
    }

    .timeline::before {
      content: '';
      position: absolute;
      left: 0.67cqw;
      top: 1.33cqw;
      bottom: 1.33cqw;
      width: 3px;
      background: var(--green-accent);
      border-radius: 3px;
    }

    .tl-item {
      position: relative;
      padding: 0.67cqw 0 0.67cqw 2.67cqw;
    }

    .tl-item::before {
      content: '';
      position: absolute;
      left: -2.33cqw;
      top: 1.6cqw;
      width: 1.07cqw;
      height: 1.07cqw;
      border-radius: 50%;
      background: var(--green-accent);
      border: 2px solid var(--surface-white);
    }

    .tl-period {
      font-size: 1.47cqw;
      font-weight: 800;
      color: var(--green-accent);
      text-transform: uppercase;
      margin-bottom: 0.4cqw;
    }

    .tl-title {
      font-size: 2cqw;
      font-weight: 700;
      color: var(--text-black);
      margin-bottom: 0.4cqw;
    }

    .tl-desc {
      font-size: 1.87cqw;
      color: var(--text-black-soft);
      font-weight: 500;
      line-height: 1.5;
    }

    /* ===== COMPARE COLS ====== */
    .compare-col {
      border-radius: var(--card-radius);
      padding: 3%;
      flex: 1;
      display: flex;
      flex-direction: column;
    }

    .compare-col.human {
      background: var(--green-light);
      border: 1px solid rgba(0, 100, 65, 0.15);
    }

    .compare-col.machine {
      background: var(--surface-cool);
      border: 1px solid rgba(0, 0, 0, 0.06);
    }

    .compare-col h4 {
      font-size: 2.13cqw;
      font-weight: 800;
      margin-bottom: 2cqw;
      color: var(--green-starbucks);
    }

    .compare-col.machine h4 {
      color: var(--text-black);
    }

    .compare-col ul {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 1.33cqw;
      flex: 1;
      justify-content: space-between;
    }

    .compare-col li {
      font-size: 2cqw;
      color: var(--text-black-soft);
      font-weight: 600;
      padding-left: 2cqw;
      position: relative;
      line-height: 1.5;
    }

    .compare-col li::before {
      content: '→';
      position: absolute;
      left: 0;
      color: var(--green-accent);
      font-size: 1.47cqw;
    }

    /* ===== FLOW ===== */
    .flow-row {
      display: flex;
      align-items: center;
      gap: 1.33cqw;
      flex-wrap: nowrap;
      justify-content: center;
    }

    .flow-item {
      padding: 1.33cqw 2cqw;
      border-radius: var(--btn-radius);
      font-size: 2.4cqw;
      font-weight: 700;
      background: var(--surface-white);
      border: 1px solid rgba(0, 0, 0, 0.1);
      box-shadow: var(--card-shadow);
      white-space: nowrap;
    }

    .flow-arrow {
      color: var(--text-black-soft);
      font-size: 2.6cqw;
      font-weight: 800;
    }

    /* ANIMATIONS */
    .slide.active .ani {
      animation: fadeUp 0.5s ease forwards;
    }

    .ani {
      opacity: 0;
      transform: translateY(15px);
    }

    .d1 {
      animation-delay: 0.1s !important;
    }

    .d2 {
      animation-delay: 0.2s !important;
    }

    .d3 {
      animation-delay: 0.3s !important;
    }

    @keyframes fadeUp {
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }

    /* Print Image Slides (Hidden on screen) */
    @media screen {
      .print-image-slide {
        display: none !important;
      }
    }
    @media print {
      .print-image-slide {
        display: flex !important;
        align-items: center;
        justify-content: center;
        background: var(--surface-white) !important;
      }
    }

    /* Special Center Layouts */
    .slide-center {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100%;
      width: 100%;
      text-align: center;
    }

    .slide-center .main-title {
      font-size: 6.67cqw;
      font-weight: 900;
      color: var(--green-starbucks);
      margin: 2% 0;
    }

    .title-line {
      width: 8cqw;
      height: 5px;
      background: var(--green-accent);
      border-radius: 5px;
      margin: 3% auto;
    }

    /* ===== PRINT STYLES FOR PDF EXPORT ===== */
    @media print {
      @page {
        size: 16in 12in;
        /* Force exactly 16x12in (4:3 ratio) so the print dialog defaults are perfect */
        margin: 0;
      }

      body,
      html,
      #presentation {
        width: 100% !important;
        height: auto !important;
        overflow: visible !important;
        background: white !important;
        -webkit-print-color-adjust: exact;
        print-color-adjust: exact;
      }

      #slideFrame {
        width: 100% !important;
        height: auto !important;
        max-width: none !important;
        max-height: none !important;
        overflow: visible !important;
      }

      .nav-container,
      .progress-bar {
        display: none !important;
      }

      .slide {
        position: relative !important;
        opacity: 1 !important;
        transform: none !important;
        page-break-after: always;
        height: 12in !important;
        width: 16in !important;
        overflow: hidden !important;
        background: var(--canvas-warm) !important;
      }

      .slide[data-theme="white"] {
        background: var(--surface-white) !important;
      }

      .slide[data-theme="dark"] {
        background: var(--green-house) !important;
      }

      .slide[data-theme="bad"] {
        background: #ffffcc !important;
      }

      .ani,
      .awful-blink,
      .awful-marquee,
      marquee {
        opacity: 1 !important;
        transform: none !important;
        animation: none !important;
      }
    }
  </style>
</head>

<body>

  <div id="presentation">
    <div id="slideFrame">

      <!-- ====== SLIDE 1 (封面页) ====== -->
      <div class="slide active" data-index="0" data-theme="cream" style="padding: 0;">
        <div class="slide-center ani">
          <div class="slide-badge badge-green" style="align-self: center;">[左上角或居中徽章，例如：组会汇报 · 日期]</div>
          <h1 class="main-title" style="font-size: 5cqw;">[填写主标题]</h1>
          <div class="title-line"></div>
          <p style="font-size:2.4cqw; color:var(--text-black-soft); font-weight: 600; margin-top: 2%;">
            汇报人：[填写姓名]
          </p>
        </div>
      </div>

      <!-- ====== SLIDE 2 (标准双栏卡片布局) ====== -->
      <div class="slide" data-index="1" data-theme="white">
        <div class="slide-header">
          <div class="slide-badge badge-green ani">[章节序号与名称]</div>
          <div class="slide-title ani d1">[当前页面大标题]</div>
        </div>
        <div class="slide-content stretch">
          <div class="two-col stretch ani d2">
            <div class="card stretch" style="border-left: 5px solid var(--green-starbucks); justify-content: center; padding: 5%;">
              <div class="card-label">[左侧卡片标签]</div>
              <div class="card-value" style="font-size:4cqw;">[左侧卡片核心词/强调内容]</div>
            </div>
            <div class="card stretch" style="border-top: 5px solid var(--green-accent);">
              <div class="card-label">[右侧卡片标签]</div>
              <ul class="clist" style="flex:1; justify-content:space-evenly;">
                <li><span class="d" style="background:var(--green-accent)"></span><span><strong>[列表项1]：</strong>[常规说明]<br><span style="font-size:1.6cqw; color:var(--text-black-soft);">（[微注文字]）</span></span></li>
                <li><span class="d" style="background:var(--gold)"></span><span><strong>[列表项2]：</strong>[常规说明]</span></li>
              </ul>
            </div>
          </div>
        </div>
      </div>

      <!-- ====== SLIDE 3 (呼出图片提示与单列宽布局) ====== -->
      <div class="slide" data-index="2" data-theme="cream">
        <div class="slide-header">
          <div class="slide-badge badge-green ani">[章节序号与名称]</div>
          <div class="slide-title ani d1">[当前页面大标题]</div>
        </div>
        <div class="slide-content stretch">
          <div class="col-flex stretch ani d2">
            <div class="hbox" style="background:var(--surface-white); border:1px solid rgba(0,0,0,0.06); padding: 3%;">
              <strong>[顶部强调句/金句引导]</strong>
            </div>
            <div class="card stretch">
              <ul class="clist" style="flex:1; justify-content:space-evenly;">
                <li><span class="d"></span><span><strong>[核心思路]</strong>：[说明文字...]</span></li>
                <li><span class="d"></span><span><strong>[技术亮点]</strong>：[说明文字...]</span></li>
              </ul>
            </div>
            <!-- 图片呼出文字提示（严禁直接嵌入 img 标签） -->
            <div class="card stretch ani d3" style="align-items:center; justify-content:center; padding: 2%;">
              <div class="card-value" style="font-size: 2.5cqw; color: var(--green-starbucks); font-weight: 800; text-align: center;">
                请按 ↓ 键查看[XXX]图
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- [根据大纲继续添加更多的 Slide...] -->

<!-- Navigation -->
      <div class="nav-container" id="navContainer">
        <button class="nav-btn" onclick="toggleImageModal()" title="查看对应图片"
          style="width: auto; padding: 0 15px; font-size: 1.6cqw; border-radius: 20px;">👁</button>
        <button class="nav-btn" onclick="exportPDF()" title="导出所见即所得的PDF"
          style="width: auto; padding: 0 15px; font-size: 1.6cqw; border-radius: 20px; font-weight: 700; color: var(--green-accent);">PDF</button>
        <button class="nav-btn" id="prevBtn" onclick="prevSlide()" title="上一页">‹</button>
        <span class="page-num" id="pageNum">1/11</span>
        <button class="nav-btn" id="nextBtn" onclick="nextSlide()" title="下一页">›</button>
      </div>

      <div class="progress-bar" id="progressBar"></div>
      <!-- Letterboxing for 16:9 effect -->
      <div id="letterbox-top"
        style="position: absolute; top: 0; left: 0; width: 100%; height: 0; background: #000; z-index: 9000; transition: height 1.5s cubic-bezier(0.4, 0, 0.2, 1); pointer-events: none;">
      </div>
      <div id="letterbox-bottom"
        style="position: absolute; bottom: 0; left: 0; width: 100%; height: 0; background: #000; z-index: 9000; transition: height 1.5s cubic-bezier(0.4, 0, 0.2, 1); pointer-events: none;">
      </div>
    </div>
  </div>

  <div id="imageModal"
    style="display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.8); z-index:9999; justify-content:center; align-items:center; flex-direction:column;">
    <button onclick="hideImageModal()"
      style="position:absolute; top:30px; right:40px; background:white; border:none; border-radius:50%; width:50px; height:50px; font-size:24px; cursor:pointer; font-weight:bold; color:var(--green-house); box-shadow: 0 4px 12px rgba(0,0,0,0.3); transition: all 0.2s;"
      title="关闭 (Esc)">✕</button>
    <img id="modalImage" src=""
      style="max-width:90%; max-height:85%; border-radius: 12px; box-shadow: 0 8px 32px rgba(0,0,0,0.6); object-fit: contain; display:none;">
  </div>

  <script>
    const slides = document.querySelectorAll('.slide');
    const total = slides.length;
    let cur = 0;

    function showSlide(idx, dir = 'next') {
      if (idx < 0 || idx >= total) return;
      slides.forEach(s => s.classList.remove('active', 'exit-left'));
      if (dir === 'next') slides[cur].classList.add('exit-left');
      cur = idx;
      slides[cur].classList.add('active');
      // Reset animations
      slides[cur].querySelectorAll('.ani').forEach(el => {
        el.style.animation = 'none';
        el.offsetHeight;
        el.style.animation = '';
      });
      updateUI();
      hideImageModal();
    }

    function nextSlide() { if (cur < total - 1) showSlide(cur + 1, 'next'); }
    function prevSlide() { if (cur > 0) showSlide(cur - 1, 'prev'); }

    function updateUI() {
      document.getElementById('pageNum').textContent = `${cur + 1}/${total}`;
      document.getElementById('prevBtn').disabled = cur === 0;
      document.getElementById('nextBtn').disabled = cur === total - 1;
      document.getElementById('progressBar').style.width = (cur / (total - 1)) * 100 + '%';

      // 16:9 letterbox effect for Slide 1 (bad design)
      const topBar = document.getElementById('letterbox-top');
      const bottomBar = document.getElementById('letterbox-bottom');

      if (false) {
        // Disable transition when entering to avoid distracting the audience (instant black bars)
        topBar.style.transition = 'none';
        bottomBar.style.transition = 'none';
        topBar.style.height = '12.5%';
        bottomBar.style.height = '12.5%';
      } else {
        // Enable slow transition when leaving for the "opening up" effect
        topBar.style.transition = 'height 1.5s cubic-bezier(0.4, 0, 0.2, 1)';
        bottomBar.style.transition = 'height 1.5s cubic-bezier(0.4, 0, 0.2, 1)';
        topBar.style.height = '0';
        bottomBar.style.height = '0';
      }

      const theme = slides[cur].dataset.theme;
      const nav = document.getElementById('navContainer');
      if (theme === 'dark' || theme === 'bad') {
        nav.classList.add('nav-dark');
      } else {
        nav.classList.remove('nav-dark');
      }
    }

    function exportPDF() {
      alert(`请在接下来的打印设置中进行如下配置，以获得最佳导出效果：\n\n目标打印机：另存为pdf\n边距：无\n选项：勾选 背景图形\n\n点击确定后将打开打印面板。`);
      window.print();
    }

    let currentImageSubIndex = 0;

    // 这里的数字对应拥有图片的幻灯片 index (即 data-index 的值)
    const slideImages = {
      1: 'example_arch.png',
      2: 'example_chart.png'
    };

    // Prepare print slides for images so they appear in PDF exports
    window.addEventListener('DOMContentLoaded', () => {
      Object.keys(slideImages).forEach(idxStr => {
        const idx = parseInt(idxStr);
        const originalSlide = slides[idx];
        if (originalSlide) {
          const printSlide = document.createElement('div');
          printSlide.className = 'slide print-image-slide';
          printSlide.dataset.theme = 'white';
          printSlide.innerHTML = `<img src="${slideImages[idx]}" style="max-width:90%; max-height:90%; object-fit:contain; border-radius:12px; box-shadow:0 4px 12px rgba(0,0,0,0.1);">`;
          originalSlide.parentNode.insertBefore(printSlide, originalSlide.nextSibling);
        }
      });
    });

    function toggleImageModal() {
      const modal = document.getElementById('imageModal');
      const img = document.getElementById('modalImage');

      if (modal.style.display === 'flex') {
        hideImageModal();
        return;
      }

      if (slideImages[cur]) {
        const src = slideImages[cur];
        const tempImg = new Image();
        tempImg.onload = function () {
          img.src = src;
          img.style.display = 'block';
          modal.style.display = 'flex';
        };
        tempImg.src = src;
      }
    }

    function hideImageModal() {
      document.getElementById('imageModal').style.display = 'none';
      currentImageSubIndex = 0;
    }

    document.getElementById('imageModal').addEventListener('click', function (e) {
      if (e.target === this) {
        hideImageModal();
      }
    });

    document.addEventListener('keydown', e => {
      if (e.key === 'Escape') {
        hideImageModal();
      }
      if (e.key === 'ArrowDown') {
        e.preventDefault();
        toggleImageModal();
      }
    });

    document.addEventListener('keydown', e => {
      if (e.key === 'ArrowRight' || e.key === ' ' || e.key === 'Enter') { e.preventDefault(); nextSlide(); }
      if (e.key === 'ArrowLeft' || e.key === 'Backspace') { e.preventDefault(); prevSlide(); }
      if (e.key === 'Home') { e.preventDefault(); showSlide(0, 'prev'); }
      if (e.key === 'End') { e.preventDefault(); showSlide(total - 1, 'next'); }
      if (e.key === 'f' || e.key === 'F') {
        if (!document.fullscreenElement) document.documentElement.requestFullscreen();
        else document.exitFullscreen();
      }
    });

    // Touch support
    let tx = 0;
    document.addEventListener('touchstart', e => { tx = e.changedTouches[0].screenX; });
    document.addEventListener('touchend', e => {
      const diff = tx - e.changedTouches[0].screenX;
      if (Math.abs(diff) > 50) { diff > 0 ? nextSlide() : prevSlide(); }
    });

    updateUI();
  </script>

</body>

</html>
```
