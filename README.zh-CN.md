

<h1 align="center">PhotoCraft</h1>

<p align="center">
  <b>开源、纯 Rust 的图像编辑器；从零 clean-room 实现，目标对齐 Adobe Photoshop。</b><br>
  图层、蒙版、调整层、图层样式、文字、矢量、画笔与真实 PSD；原生桌面 + Web（WASM）。
</p>

<p align="center">
  <img alt="100% Rust" src="https://img.shields.io/badge/100%25-Rust-b7410e?style=flat-square&logo=rust">
  <img alt="macOS · Windows · Linux · FreeBSD · Web" src="https://img.shields.io/badge/macOS%20%C2%B7%20Windows%20%C2%B7%20Linux%20%C2%B7%20FreeBSD%20%C2%B7%20Web-native-2f7bf5?style=flat-square">
  <img alt="License: MIT OR Apache-2.0" src="https://img.shields.io/badge/license-MIT%20%2F%20Apache--2.0-3a3a3a?style=flat-square">
  <img alt="Status: early alpha" src="https://img.shields.io/badge/status-early%20alpha-d69e2e?style=flat-square">
</p>

<p align="center">
  <a href="README.md">English</a> ·
  <a href="https://getartcraft.com/apps/photocraft">官网</a> ·
  <a href="https://discord.gg/artcraft">Discord</a>
</p>

<br>

| | | | |
|---|---|---|---|
| **界面熟悉** | **原生且快** | **真实 PSD** | **可被 Agent 驱动** |
| 菜单、快捷键、面板与工具布局贴近 Photoshop | wgpu GPU 合成（Metal / Vulkan / DX12 / WebGPU）；COW 分块瓦片；多线程滤镜；无 Electron | 打开、编辑、保存分层 PSD；psd-tools 测试集 309 个文件中 307 个再保存后渲染一致 | 一切皆命令；UI / CLI / JSON 控制通道 / MCP 共用同一引擎 |

<br>

## 功能概览

- **调整图层（非破坏编辑）**：Levels、Curves（分通道）、Vibrance、Hue/Saturation 等共 **16** 种；可蒙版、重排、关闭，不改原像素。另有 Shadows/Highlights、Replace Color、Match Color、HDR Toning 等直接作用于像素的命令。
- **图层样式**：投影、内阴影、内外发光、斜面与浮雕、绸缎、描边、颜色/渐变/图案叠加；支持图案库与 PSD `Patt`；样式可复制粘贴。
- **选区**：选框、套索、魔棒；快速选择、对象选择、选择主体；选择并遮住；羽化/扩展/收缩/平滑等；本地智能选区，无云端。
- **文字**：点文字与段落文字，画布内编辑；字符/段落面板；可编辑、可套样式、PSD 往返。
- **矢量**：矩形/椭圆/多边形/线、钢笔系列；形状图层、路径、矢量蒙版；合并路径（相加/相减/相交/排除）。语料库中 116/116 形状层像素对齐 Photoshop。
- **滤镜**：对话框内实时预览；模糊（含 Blur Gallery）、锐化、降噪、扭曲、像素化、风格化、渲染等；智能对象上滤镜可再编辑。大半径模糊用多核 running-sum box：约 3.6 MP、半径 180 的高斯通常 &lt; 1 秒。
- **变换与历史**：自由变换（缩放/旋转/斜切/扭曲/透视）、再变换；完整历史、切换上一状态、历史画笔。
- **导出与主题**：Export As（格式/质量/透明/缩放 + 预览与体积估算）；一键 Quick Export PNG；Pro / Studio / Classic 主题。

## 开箱能力

### 工具与引擎

- **68 个工具**：移动、画板、各类选框/套索、对象/快速选择、魔棒、裁剪、取色、修复/污点/修补、画笔/铅笔/混合器、仿制/图案图章、历史画笔、橡皮、渐变/油漆桶、模糊/锐化/涂抹、减淡/加深/海绵、钢笔与锚点、文字与文字蒙版、路径选择、形状工具、抓手/旋转视图/缩放等。
- **画笔引擎**：形状动态、散布、纹理、双重画笔、颜色动态、传递、笔姿、湿边、堆积、平滑（含 Pulled String）；支持压感、倾斜、旋转、方向；预设与可回放笔触。
- **图层模型**：组、剪贴蒙版、像素/矢量蒙版、填充层、调整层、智能对象（智能滤镜、无损变换/变形）、多选对齐/分布/链接、Alpha 通道与快速蒙版、27 种 Photoshop 混合模式 + 6 种 Paint.NET 变体、不透明度/填充、锁定、颜色标签、合并/拼合/栅格化等。

### 色彩与位深

- 色彩模式：**RGB / Grayscale / CMYK / Lab**
- 位深：**8 / 16 / 32 bit/通道**（运行时数据；工具按深度工作）
- 纯 Rust ICC：嵌入配置文件、指定/转换配置（四种渲染意图 + 黑点补偿）、软打样（⌘Y）、色域警告（⇧⌘Y，GPU）

### 格式

| 类型 | 支持 |
|---|---|
| 主格式 | PSD / PSB；原生 `.pcraft` |
| 分层互通 | 分层 TIFF；[PDN3](docs/pdn.md)（读）；[OpenRaster](docs/ora.md)（读写）；[GIMP XCF](docs/xcf.md)（读）；[Krita .kra](docs/kra.md)（读）；SVG→形状层 / 矢量智能对象 |
| 平面格式 | PNG、JPEG、TIFF、WebP、GIF、BMP、TGA、ICO、QOI、PNM、OpenEXR、Radiance HDR、FITS |
| 可选 | HEIC（只读，官方包内置；源码 `--features heif`）；AVIF 写入（`--features avif`）；无 AVIF 解码 / JPEG XL |
| Affinity | `.af` / `.afdesign` / `.afphoto` / `.afpub` 只读打开；不支持部分会警告；无导出 |

位深保留规则与完整矩阵见 [`crates/codecs/README.md`](crates/codecs/README.md#capability-matrix-default-build)。

## PSD

独立 crate，按 Adobe 公开规范实现，并用真实文件语料测试。

- **往返**：psd-tools 309 中 **307**、自混语料 170 中 **169** 再保存后渲染一致（`crates/io/tests/it/corpus.rs`；`cargo xtask corpus --psd-tools`）。未建模块会原样携带，不丢弃。再保存文件字节不要求与源相同（资源/图层记录/合成图会重写）。仅独立 `photocraft-psd` 解析写回可做到可解析语料字节级一致。
- **像素对齐**：与 Photoshop 合并图做合成 oracle（渐变插值、图层效果、形状描边、剪贴与填充不透明度等）。
- **大文件**：PSB、16/32 位、CMYK/Lab 原生打开。

## Agent / 自动化

500+ 命令统一注册表；菜单、工具、对话框都走命令。UI、CLI、控制通道、MCP 调用同一套。

```sh
# 无界面：打开 → 编辑 → 导出
photocraft-cli run wave.psd \
  --cmd filter.sharpen.smartSharpen     --params '{"amount":80}' \
  --cmd layer.newAdjustmentLayer.curves --params '{"points":[[0,0],[64,48],[192,212],[255,255]]}' \
  --out wave-final.png

# 对目录批量应用动作
photocraft-cli batch --actions grade.json --in ./raw --out ./graded

photocraft-cli batch --help
photocraft-cli mcp   # MCP（无界面，或桥接到正在跑的桌面端）
```

桌面端还可开本机回环控制通道：`photocraft --control`（查 UI 状态、指针事件驱动工具、离屏截图）。协议见 [`docs/control-protocol.md`](docs/control-protocol.md)。

## 架构要点

- **引擎优先**：纯数据文档模型 + 命令引擎；egui UI 很薄；约 24 个 crate，分层在构建期强制检查。
- **双合成器**：CPU 合成作参考 oracle；wgpu 合成上屏；二者互测。
- **Copy-on-write 瓦片**：256² 稀疏瓦片，便宜 undo、大画布更轻；效果图按图层状态缓存。
- **WASM**：引擎与 UI 可编译到浏览器。
- **Clean-room**：公开规范 + 黑盒观察行为；无专有代码/着色器/素材。
- **测试**：1700+ 用例（PSD 往返、合成 oracle、多位深等）。

## 快速开始

```sh
git clone https://github.com/storytold/photocraft
cd photocraft
cargo run --release -p photocraft -- image.psd
cargo test --workspace
```

日文字体可选，来自 [craft-fonts](https://github.com/storytold/craft-fonts)（桌面 release 通常已嵌入；未设置时用系统 CJK）：

```sh
git clone https://github.com/storytold/craft-fonts ../craft-fonts
CRAFT_FONTS_DIR="$PWD/../craft-fonts" cargo run --release -p photocraft
```

贡献与 Agent 请先读 [`AGENTS.md`](AGENTS.md)，再看 [`docs/`](docs/)。

安装包见 [GitHub Releases](https://github.com/storytold/photocraft/releases)。Linux Flatpak 示例：

```sh
flatpak install --user photocraft-<version>-linux-x86_64.flatpak
flatpak run ai.storyteller.photocraft
```

AppImage 可直接跑；`--cli` / 名为 `photocraft-cli` 的符号链接走 CLI。Wayland 下窗口拖放暂不可用（winit #386），请用「文件 › 打开」或 Ctrl+V；需要拖放可用 XWayland（`WAYLAND_DISPLAY=` 或偏好设置里选 X11）。细节见英文 README 与 [`docs/releasing.md`](docs/releasing.md)、[`docs/development.md`](docs/development.md)。

> [!IMPORTANT]
> **状态：早期 alpha。** 大量 Photoshop 功能面已接线，但**尚不能替代日常专业工作**。主要缺口：生成式/AI、约 15 个缺失工具、排版与专业流程深度、插件兼容。菜单接线清单见 [`docs/parity-checklist.md`](docs/parity-checklist.md)（测的是接线，不是完整行为）。进展与差距见 [ROADMAP.md](ROADMAP.md)、[parity 评估](docs/target-app-parity.md)。

## 文档与安全

- 开发/架构/自动化/格式/安全：[文档书](book/)
- Web Docker 自托管：[packaging/web/README.md](packaging/web/README.md#docker)
- 安全策略：[SECURITY.md](SECURITY.md)、[book/src/security/](book/src/security/)
- 测试语料：`cargo xtask corpus --all` → `cargo xtask test-corpus`（[photocraft-corpus](https://github.com/storytold/photocraft-corpus) 等，sha256 固定）

## 下载

最简安装：[getartcraft.com/apps/photocraft](https://getartcraft.com/apps/photocraft)  
全部构建：[最新 Release](https://github.com/storytold/photocraft/releases/latest)（含 `SHA256SUMS.txt`）

| 平台 | 常见产物 |
|---|---|
| Windows | `photocraft-<ver>-windows-{x64,arm64,x86}.msi` / `*-portable.zip`（代码签名） |
| macOS | `photocraft-<ver>-macos-universal.dmg`；CLI：`photocraft-cli-<ver>-macos-universal.zip`（签名并公证） |
| Linux | AppImage / Flatpak / `.deb` / `.rpm` / `.tar.gz`（x86_64、aarch64；riscv64 仅 tar.gz） |
| FreeBSD | `photocraft-<ver>-freebsd-x86_64.tar.gz` |
| Web | `photocraft-web-<ver>.zip`（静态站点，现代浏览器） |

## Crafting Apps

同系列纯 Rust、clean-room 创意工具（节选）：

| 应用 | 用途 |
|---|---|
| [PhotoCraft](https://github.com/storytold/photocraft) | 图像编辑 / PSD |
| [VectorCraft](https://github.com/storytold/vectorcraft) | 矢量插画 |
| [FilmCraft](https://github.com/storytold/filmcraft) | 视频剪辑 |
| [LightCraft](https://github.com/storytold/lightcraft) | 图库与 RAW |
| [PdfCraft](https://github.com/storytold/pdfcraft) | PDF |
| [CADCraft](https://github.com/storytold/cadcraft) | CAD |
| 更多 | [getartcraft.com/apps](https://getartcraft.com/apps) |

约定一致：纯 Rust、macOS/Windows/Linux 原生、WASM 上浏览器、命令驱动可被 Agent 操控。

## 许可与声明

- 双重许可：[MIT](LICENSE-MIT) 或 [Apache-2.0](LICENSE-APACHE)
- 素材署名：[ATTRIBUTION.md](ATTRIBUTION.md)；演示图来源：[docs/images/SOURCES.md](docs/images/SOURCES.md)
- ArtCraft 品牌：[docs/brand/LICENSE-brand.txt](docs/brand/LICENSE-brand.txt)（fork 须移除）
- Adobe / Photoshop 等为 Adobe 商标；本项目独立开源，无隶属或背书关系。

完整英文版（含截图、发行细节与社区说明）：[`README.md`](README.md)
