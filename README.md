## Media Player Classic Qute Theater（[项目主页][homepage]）

MPC-QT 是基于 Qt 与 libmpv 实现的 Media Player Classic 风格播放器。

![项目截图][screenshot]
更多截图请见：https://mpc-qt.github.io/gallery

[MPC-HC][mpc-hc]（Media Player Classic Home Cinema）一直是 Windows 平台上广受欢迎的经典播放器。MPC-QT（Media Player Classic Qute Theater）旨在复刻 MPC-HC 的主要界面与使用体验，同时使用 [libmpv] 作为播放后端，而非 DirectShow。

- [发布与下载](#发布与下载)
- [核心特性](#核心特性)
- [Fork 之后的改动整理](#fork-之后的改动整理)
- [贡献代码与翻译](#贡献代码与翻译)
- [编译指南](#编译指南)
- [Windows 编译](#windows-编译)
- [常见问题](#常见问题)

## 发布与下载

- 项目提供多个平台的软件包，可在 [下载页面][Packages are available] 获取。
- Windows 与 Linux 可在 [Release 页面][release page] 下载稳定构建版本（采用时间版本号，例如 17.07 表示 2017 年 7 月）。
- 也提供 Windows / Linux（AppImage、Flatpak、原生构建）的 [开发版构建][development builds]。

说明：选项对话框中尚未实现的功能会被隐藏；如果无法隐藏则会禁用。这样做是为了在功能逐步补齐期间，尽可能保留 MPC-HC 的选项布局与使用习惯。

## 核心特性

- **MPC-HC 风格复刻**：覆盖普通用户高频使用的大部分功能。
- **多播放列表**：可为不同剧集或任务维护独立播放列表，并分别记录各自上次播放位置。
- **进度条视频预览（缩略图）**。
- **在线播放支持**（依赖 [yt-dlp]）。
- **快速插播（Quick Queue）**：支持类似 xmms/qmmp 的乱序临时播放，不破坏原播放列表结构。
- **播放列表搜索**：支持多线程搜索，提高大型列表检索效率。
- **截图模板**：可自定义截图命名模板，仅保留你需要的字段。
- **自定义元数据展示**：播放列表窗口可显示更多自定义媒体信息。
- **界面样式定制**：支持应用内样式个性化。

### 可能的后续功能

以下为愿望清单（取决于开发者时间）：

- **类似 VirtualDub 的编码能力**：可在播放器内快速导出片段（需要额外编码后端支持）。

欢迎提交建议。

## Fork 之后的改动整理

以下内容基于本仓库自 Fork 以来的主要提交（以当前分支历史为准）整理：

### 1) 发布流程与版本元数据统一
- 优化 GitHub Release 工作流，改为支持手动输入标签。
- 统一版本相关元数据，减少多平台发布流程中的重复配置与维护成本。

### 2) 新增“章节索引 / 主题卡片”能力
- 在播放列表相关界面中新增章节索引标签页（Topic Index Tab）。
- 增强章节卡片解析逻辑，提升 Markdown 章节导航可用性。

### 3) 章节 Markdown 编辑与校验
- 新增可编辑章节 Markdown 的模式。
- 在保存/处理前进行格式校验，减少无效内容进入流程。

### 4) 兼容性修复与稳定性改进
- 修复章节 Markdown 后缀识别问题，兼容大写扩展名以及 `.markdown` 文件。
- 修复部分章节卡片跳转与预览流程中的错误，提升章节预览功能稳定性。

### 5) 涉及的核心文件（便于后续追踪）
- `.github/workflows/release.yml`
- `src/playlistwindow.h`
- `src/playlistwindow.cpp`
- `src/manager.cpp`

## 贡献代码与翻译

- 如果你希望贡献代码，欢迎先遵循现有代码风格后提交 PR。
- 如果你希望贡献翻译，可通过 [Weblate] 参与协作，也可以直接提交翻译 PR。

[![Translation status]][Weblate]

## 编译指南

[![C/C++ CI]][C/C++ CI link]

通常建议直接使用最新代码进行构建。源码编译可让你更快体验到新功能及预发布依赖版本。

### 依赖要求

需要安装 Qt6 SDK 与较新的 libmpv。以 Ubuntu 为例，常见依赖包括：

- `build-essential`
- `cmake`
- `qt6-base-dev`
- `qt6-l10n-tools`
- `libqt6svg6-dev`
- `libmpv-dev`
- `boost`

建议 libmpv 至少为 0.37.0，或直接使用最新源码版本。

### 基础构建流程（Linux/Unix）

```bash
mkdir -p ~/src
cd ~/src
git clone https://github.com/mpc-qt/mpc-qt.git
cd mpc-qt
```

使用 CMake + Make：

```bash
cmake .
make -j"$(nproc)"
```

安装 / 卸载：

```bash
sudo make install
sudo make uninstall
```

开发建议使用 CMake + Ninja（子目录构建）：

```bash
cmake -B build -G Ninja
cmake --build build -t update_translations
cmake --build build
```

安装 / 卸载 / 清理：

```bash
sudo cmake --install build
sudo cmake --build build -t uninstall
cmake --build build -t clean
```

后续更新：

```bash
git pull origin master
```

### 遇到编译或链接错误？

部分发行版仓库中的 mpv/libmpv 版本可能较旧。可参考 [mpv-build repo] 的说明自行构建较新版本。

## Windows 编译

[![Msys2 CI]][Msys2 CI link]

项目可在 MSYS2 环境下编译。建议在 MSYS2 MINGW64 中执行：

```bash
pacman -Syu
# 重启终端后再次执行
pacman -Syu
```

安装构建依赖：

```bash
pacman -S base-devel mingw-w64-x86_64-cmake git mingw-w64-x86_64-toolchain \
mingw-w64-x86_64-qt6 mingw-w64-x86_64-qt-creator \
mingw-w64-x86_64-imagemagick mingw-w64-x86_64-boost
```

可选择：
1. 使用与 MSYS2 ffmpeg 链接的 libmpv；或
2. 使用预编译 libmpv（见 [shinchiro's release page]）。

如果采用预编译包，请将文件放入对应目录后，使用：

```bash
cmake -DENABLE_LOCAL_MPV=ON .
make -j"$(nproc)"
```

## 常见问题

另见 [Wiki Q&A] 及 Wiki 其他页面。

### 是否兼容 SVP（SmoothVideo Project）？

兼容。请在 SVP 中设置：

- mpv JSON IPC 路径为 `/tmp/cmdrkotori.mpc-qt.mpv`
- 播放器路径为 `/usr/bin/mpc-qt`

[homepage]:https://mpc-qt.github.io/
[screenshot]:https://raw.githubusercontent.com/mpc-qt/mpc-qt-screenshots/refs/heads/master/Screenshot_20250602_151626.png
[mpc-hc]:https://mpc-hc.org/
[libmpv]:https://github.com/mpv-player/mpv
[release page]:https://github.com/mpc-qt/mpc-qt/releases/latest
[Packages are available]:https://mpc-qt.github.io/downloads
[development builds]:https://mpc-qt.github.io/downloads#dev-builds
[yt-dlp]:https://github.com/yt-dlp/yt-dlp
[Weblate]:https://hosted.weblate.org/engage/mpc-qt/
[Translating]:https://hosted.weblate.org/projects/mpc-qt/
[Translation status]:https://hosted.weblate.org/widgets/mpc-qt/-/svg-badge.svg
[C/C++ CI]:https://github.com/mpc-qt/mpc-qt/actions/workflows/c-cpp.yml/badge.svg
[C/C++ CI link]:https://github.com/mpc-qt/mpc-qt/actions/workflows/c-cpp.yml
[aur]:https://aur.archlinux.org/packages/mpc-qt-git/
[ports]:https://github.com/freebsd/freebsd-ports/tree/main/multimedia/mpc-qt
[mpv-build repo]:https://github.com/mpv-player/mpv-build
[Msys2 CI]:https://github.com/mpc-qt/mpc-qt/actions/workflows/msys2.yml/badge.svg
[Msys2 CI link]:https://github.com/mpc-qt/mpc-qt/actions/workflows/msys2.yml
[shinchiro's release page]:https://sourceforge.net/projects/mpv-player-windows/files/libmpv/
[Wiki Q&A]:https://github.com/mpc-qt/mpc-qt/wiki/Q%26A
