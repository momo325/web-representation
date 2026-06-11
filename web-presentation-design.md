# Web Native Presentation 架构设计与技术要点总结

这是一个基于纯前端技术栈（HTML/CSS/Vanilla JS）构建的**零依赖、单文件、全响应式**的网页端幻灯片（Presentation）引擎方案。

该方案放弃了传统的 PPT 或复杂的现代前端框架，旨在提供极致的渲染性能、完全自由的排版能力、以及完美的跨端与导出支持。以下是其核心技术点与通用脱敏模板。

---

## 核心技术要点

### 1. 基于容器查询单元（Container Queries Units）的绝对响应式
不同于传统的 `vw/vh` 或媒体查询，本方案使用 CSS 容器查询单位（`cqw` / `cqh`）。
* **原理**：将幻灯片主容器（`#slideFrame`）声明为 `container-type: inline-size`，内部所有元素的字号、边距、间距全部使用 `cqw` 为单位。
* **效果**：无论外部浏览器窗口如何拉伸，只要主容器保持 4:3 比例缩放，内部所有的排版、文字大小都会**绝对等比缩放**，永远不会出现文字换行错位。

### 2. 完美的 4:3 约束容器
通过 CSS 数学计算，在任何视口比例下都维持 4:3 的居中画幅：
```css
#slideFrame {
  width: 100%; height: 100%;
  max-width: calc(100vh * 4 / 3); /* 限制最大宽度由高度决定 */
  max-height: calc(100vw * 3 / 4); /* 限制最大高度由宽度决定 */
}
```

### 3. 极简的 CSS 状态机与微动效重置
幻灯片的切换由 JS 控制类名（`active`, `exit-left`）完成，通过 CSS Transition 实现平滑的滑动与淡入淡出。
对于幻灯片内部的微动效（如元素依次向上浮现），JS 会在每次切页时触发一次 DOM 重绘（Reflow），从而让 CSS Keyframes 重新执行：
```javascript
el.style.animation = 'none';
el.offsetHeight; // 触发重绘
el.style.animation = '';
```

### 4. 动态主题注入（Theming System）
通过给幻灯片元素添加 `data-theme="dark"` 等自定义属性。CSS 层利用属性选择器（`.slide[data-theme="dark"]`）更改背景。同时 JS 层会读取当前幻灯片的主题，自动反色右上角的导航控制按钮，确保可见性。

### 5. 沉浸式 Letterboxing（上下黑边黑屏动画）
针对特定需要营造视觉反差的幻灯片，系统在主容器上方覆盖两个绝对定位的黑色 DIV。
通过 JS 动态控制这两个黑框的高度（`12.5%`），可以将原有的 4:3 画幅瞬间压缩至 16:9（入场时取消 transition 营造突兀感），切出时再通过 `1.5s` 缓动拉开黑幕，营造强烈的视觉冲击。

### 6. PDF 导出黑科技（异步探针+媒体查询）
系统不仅适配了 `@media print` 强制所有 `.slide` 拥有 `page-break-after: always` 及重置为正常的文档流，还加入了**智能附件扫描**机制：
* 触发导出时，JS 会异步 `new Image()` 探测每页是否有附属的图解（如 `1.png`, `1.1.png`）。
* 将探测到的图片自动生成专属的 `<div class="slide print-only">` 插入 DOM 中。
* 由于 `.print-only` 默认不在屏幕上显示（被 CSS 隐藏），因此完全不影响网页播放，但在 `window.print()` 调用时，PDF 内会自动多出高清的图片页。

---

## 单文件通用模板 (Boilerplate)

以下是完全脱敏的完整单文件模板，直接复制保存为 `index.html` 并在浏览器中打开，即可获得所见即所得的极简幻灯片体验。

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Presentation Title</title>
  <style>
    :root {
      --bg-color: #f7f7f8;
      --text-color: #333333;
      --accent-color: #0078D4;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }
    
    body, html {
      width: 100%; height: 100%;
      overflow: hidden; background: #000;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      display: flex; align-items: center; justify-content: center;
    }

    /* 4:3 主容器 */
    #slideFrame {
      container-type: inline-size;
      position: relative;
      width: 100%; height: 100%;
      max-width: calc(100vh * 4 / 3);
      max-height: calc(100vw * 3 / 4);
      background: var(--bg-color);
      overflow: hidden;
    }

    /* 幻灯片基础样式 */
    .slide {
      position: absolute; top: 0; left: 0;
      width: 100%; height: 100%;
      opacity: 0; transform: translateX(50px);
      transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
      pointer-events: none;
      background: var(--bg-color);
    }
    
    .slide.active { opacity: 1; transform: translateX(0); pointer-events: all; }
    .slide.exit-left { opacity: 0; transform: translateX(-50px); }

    /* 主题化 */
    .slide[data-theme="dark"] { background: #1a1a1a; color: #ffffff; }

    /* 布局排版与 cqw 适配 */
    .content-box {
      position: absolute;
      top: 15%; left: 10%; width: 80%; height: 70%;
      display: flex; flex-direction: column; justify-content: center;
    }
    
    h1 { font-size: 6cqw; margin-bottom: 2cqw; }
    p { font-size: 2.5cqw; line-height: 1.6; }

    /* 入场动画类 */
    .ani {
      animation: fadeUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) both;
    }
    .d1 { animation-delay: 0.2s; }
    .d2 { animation-delay: 0.4s; }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(3cqw); }
      to { opacity: 1; transform: translateY(0); }
    }

    /* 导航栏 */
    .nav-container {
      position: absolute; top: 3%; right: 3%;
      display: flex; gap: 8px; z-index: 100;
    }
    .nav-btn {
      width: 40px; height: 40px; border-radius: 50%;
      border: none; background: rgba(0,0,0,0.1); cursor: pointer;
      font-size: 16px; font-weight: bold; transition: 0.2s;
    }
    .nav-btn:hover { background: var(--accent-color); color: white; }
    .nav-dark .nav-btn { background: rgba(255,255,255,0.2); color: white; }

    /* 进度条 */
    .progress-bar {
      position: absolute; bottom: 0; left: 0;
      height: 4px; background: var(--accent-color);
      width: 0%; transition: width 0.3s ease; z-index: 100;
    }

    /* 打印与导出样式 */
    @media print {
      @page { size: 16in 12in; margin: 0; }
      body, html, #slideFrame {
        width: 100% !important; height: auto !important;
        max-width: none !important; max-height: none !important;
      }
      .nav-container, .progress-bar { display: none !important; }
      .slide {
        position: relative !important; opacity: 1 !important; transform: none !important;
        page-break-after: always; height: 12in !important; width: 16in !important;
      }
    }
  </style>
</head>

<body>
  <div id="slideFrame">
    <!-- Slide 1 -->
    <div class="slide" data-index="0">
      <div class="content-box">
        <h1 class="ani">Web-Native Presentation</h1>
        <p class="ani d1">A zero-dependency, single-file HTML presentation engine.</p>
      </div>
    </div>

    <!-- Slide 2 -->
    <div class="slide" data-index="1" data-theme="dark">
      <div class="content-box">
        <h1 class="ani">Features</h1>
        <p class="ani d1">✓ Container Query Responsiveness</p>
        <p class="ani d2">✓ PDF Export Support</p>
        <p class="ani d2" style="animation-delay:0.6s">✓ Dark/Light Themes</p>
      </div>
    </div>

    <!-- 进度与导航 -->
    <div class="progress-bar" id="progressBar"></div>
    <div class="nav-container" id="navContainer">
      <button class="nav-btn" onclick="exportPDF()" style="width: auto; padding: 0 15px; border-radius: 20px;">PDF</button>
      <button class="nav-btn" onclick="prevSlide()">‹</button>
      <button class="nav-btn" onclick="nextSlide()">›</button>
    </div>
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
      
      // 重置微动效
      slides[cur].querySelectorAll('.ani').forEach(el => {
        el.style.animation = 'none';
        el.offsetHeight; 
        el.style.animation = '';
      });
      
      updateUI();
    }

    function nextSlide() { if (cur < total - 1) showSlide(cur + 1, 'next'); }
    function prevSlide() { if (cur > 0) showSlide(cur - 1, 'prev'); }

    function updateUI() {
      document.getElementById('progressBar').style.width = (cur / (total - 1)) * 100 + '%';
      
      const theme = slides[cur].dataset.theme;
      const nav = document.getElementById('navContainer');
      if (theme === 'dark') {
        nav.classList.add('nav-dark');
      } else {
        nav.classList.remove('nav-dark');
      }
    }

    function exportPDF() {
      alert(`Print Setup:\n\n1. Destination: Save as PDF\n2. Paper size: Custom 16x12in (or leave default if forced by CSS)\n3. Margins: None\n4. Background graphics: Checked`);
      window.print();
    }

    // 键盘绑定
    document.addEventListener('keydown', e => {
      if (e.key === 'ArrowRight' || e.key === ' ' || e.key === 'Enter') { e.preventDefault(); nextSlide(); }
      if (e.key === 'ArrowLeft' || e.key === 'Backspace') { e.preventDefault(); prevSlide(); }
      if (e.key === 'f' || e.key === 'F') {
        if (!document.fullscreenElement) document.documentElement.requestFullscreen();
        else document.exitFullscreen();
      }
    });

    // 触摸绑定
    let tx = 0;
    document.addEventListener('touchstart', e => { tx = e.changedTouches[0].screenX; });
    document.addEventListener('touchend', e => {
      const diff = tx - e.changedTouches[0].screenX;
      if (Math.abs(diff) > 50) { diff > 0 ? nextSlide() : prevSlide(); }
    });

    updateUI();
    showSlide(0); // 初始渲染
  </script>
</body>
</html>
```
