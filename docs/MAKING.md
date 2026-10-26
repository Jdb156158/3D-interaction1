# 制作步骤与复现



1. 从下方源站核实五种交互，写场景、短提示词和带时间码分镜，见 [treatment.md](treatment.md)。源站不同控件可能采用 CSS 或 Canvas，本片独立用 Three.js 重绘机制。
2. 安装 Node.js、Python 3/numpy、Chrome 和 FFmpeg。执行 `npm install`。
3. `scene3d.js` 创建几何、字体纹理、材质和光照；`index.html` 负责 Bento 布局、字幕、时间驱动与操作指针。字体和库全部在本地。
4. `python3 make_audio.py` 生成 20 秒原创配乐和音效：120 BPM、2/4 拍、20 小节，18 秒终止和弦后留尾音。
5. `npm run stills` 导出九个关键时刻至 `previews/`，检查几何、文字正反、曝光和遮挡。Chrome 默认使用 macOS 安装路径，其他系统通过 `CHROME_PATH` 指定。
6. `npm run render` 逐帧调用 `draw(i/60)`，生成 1,200 张 JPG 到 `frames/`。浏览器需支持 WebGL。
7. `sh encode.sh` 合成 `spatial-microinteractions-20s.mp4`，设置 H.264 Main/yuv420p、AAC、响度处理、faststart、每秒关键帧。
8. 从最终 MP4 抽帧核对，完整播放并跳转。验收记录见 [QA.md](QA.md)。
9. 可使用 huashu-motion 的 `om studio . --no-open` 打开时间轴工作室；`om check .` 检查确定性，`om qa .` 检查突帧与节奏。

`index.html` 是影片合成，不是响应真实拖动的生产组件页面；操作由时间轴演示。完整组件提示词用于后续制作交互网页。


## 可操作网页

- 根目录 index.html 与 site/style.css 负责响应式黑色 Bento 页面。
- site/models.js 基于影片的原始 Three.js 模型，增加真实输入状态；film/scene3d.js 保留确定性影片实现。
- site/app.js 接收 Pointer Events、键盘与原生输入，处理开合、旋转、翻页、吸附和动画。
- 五种模型共用一个 WebGLRenderer；仅在操作、动画和尺寸变化时重绘。
- 触屏用户可拖动模型，或使用下方按钮/原生选择框。横向模型保留页面纵向滚动；笔记本与滚筒区域捕获纵向拖动。
- 字体和 Three.js 固定在仓库内，不依赖远程 CDN。
- prefers-reduced-motion 下直接切换至操作结果，保留当前状态。

## 发布

GitHub Pages 从 main 分支根目录发布，不需要 npm 构建；.nojekyll 保留静态资源路径。影片复现独立在 film/ 目录进行。

本地运行：`python3 -m http.server 4856`，打开 `http://localhost:4856/`。
