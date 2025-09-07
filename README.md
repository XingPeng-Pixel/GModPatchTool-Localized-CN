# GModPatchTool 中文镜像版 <sub>_前身为 GModCEFCodecFix_</sub>

![GModPatchTool](GModPatchToolLogo.png)

***GModPatchTool** 做到了 Facepunch [没有做到的事](https://github.com/Facepunch/gmod-html/pull/5)!*

**由 Solstice Game Studios (www.solsticegamestudios.com) 创建**

**汉化和镜像源由 Xingpeng 修改**

**感谢ghproxy各位的镜像提供**

目前加速源为Cloudflare Workers免费版，免费版可能有速率限制影响，部分地区存在Cloudflare连接问题。

遇到部分文件下载失败不用担心，自动重试方法会重试下载直到过了短时冷却或连接到Cloudflare

待发现到更好的廉价可负担加速源后会跟进更新

|              | Github源                          | Onedrive源                         |
|--------------|-----------------------------------|------------------------------------|
| 下载链接     | [下载地址](https://github.com/XingPeng-Pixel/GModPatchTool-Localized-CN/releases/) | [下载地址](https://1drv.ms/f/c/ed0af904380912b9/EjvQ0Lg3T1xFrdLj_mnq0r4Bjc1X9kRaFKAmMRZh3RD4RQ?e=8Gu2SY)   |
# 🛠️ 我们提供的修复
### 所有平台
- 修复 macOS 和 Linux 上的各种启动/主菜单缺失问题
- 为 GMod 添加 `-chromium_fps_max` 启动选项
  - 允许为所有 CEF 网页面板设置内部最大 FPS 限制
  - 可能会以牺牲网页内容帧率为代价提高游戏帧率
  - 默认值为 60
- 使用我们自定义的 SourceScheme.res 改进经典 VGUI 主题
- 将调试/控制台字体替换为 [PT Mono](https://fonts.google.com/specimen/PT+Mono)，以提高跨平台的一致性和可读性
  - 这对于 Proton 尤其重要，因为在这些环境下，使用这些字体的文本开箱即用就是破损/微小的（没有 Lucida Console）
  - 如果您不喜欢主题更改或字体替换，可以在运行工具时使用 `--no-sourcescheme` 参数禁用这些补丁

### 游戏内网页浏览器（[Chromium Embedded Framework，简称 CEF](https://en.wikipedia.org/wiki/Chromium_Embedded_Framework)）
- 将 CEF 更新至 137.0.10 版本（Chromium 137.0.7151.69）
- 启用[专有视频/音频编解码器](https://www.chromium.org/audio-video)支持，例如 H.264 (MP4) 和 AAC
- 启用 [Widevine](https://www.widevine.com) 支持（但[无 VMP](https://github.com/solsticegamestudios/GModPatchTool/issues/100)，因此 Netflix 等目前无法工作...）
- 启用软件 WebGL
- 启用部分 GPU 加速
- 改进纹理更新性能
- 禁用硬件媒体键对媒体的控制
- 重新启用站点隔离（安全功能；某些网站需要此功能才能运行）

### Linux
- 可以修复 Steam 叠加层/MangoHud/等无法工作的问题
  - 在 GMod 的启动选项中加入 `GMOD_ENABLE_LD_PRELOAD=1 %command%` 来尝试！
  - 默认情况下此功能是禁用的，因为它可能会导致 GMod 崩溃
- 设置 `mesa_glthread=true` 以在使用 Mesa 驱动程序时获得更多 OpenGL 性能
- 设置 `ulimit -n $(ulimit -Hn)` 以修复打开/挂载许多文件时的问题（许多插件、Lua 自动刷新等）
- 在 `hl2.sh` 中添加各种注释掉的导出命令，以帮助多 GPU 用户快速指示 GMod 使用正确的 GPU（通常是笔记本电脑）
  - 查看 [#188](https://github.com/solsticegamestudios/GModPatchTool/issues/188) 了解我们为何默认不启用这些设置

# ❓ 玩家：如何安装/使用
下载 **[最新版本](https://github.com/solsticegamestudios/GModPatchTool/releases)**（或通过我的自建镜像 **[gh-proxy](https://gh.xpcdn.ggff.net/github.com/solsticegamestudios/GModPatchTool/releases)**）并运行应用程序。

需要更深入的指南？请查看 https://www.solsticegamestudios.com/fixmedia/

# 👩‍💻 开发者：如何使用/检测
指引玩家按照上述玩家说明操作。此补丁仅限客户端！

**要检测已修补的 CEF：** 查看我们的 [Lua 检测示例](examples/detection_example.lua)。

> [!警告]
> 我们的 CEF 构建启用了站点隔离，这意味着**您必须注意调用 JavaScript 相关 DHTML 函数的位置！**
>
> 如果您在页面开始加载之前调用 [DHTML.AddFunction](https://wiki.facepunch.com/gmod/DHTML:AddFunction)、[DHTML.QueueJavascript](https://wiki.facepunch.com/gmod/DHTML:QueueJavascript) 或 [DHTML.RunJavascript](https://wiki.facepunch.com/gmod/Panel:RunJavascript)，它将不起作用！请确保您在 [DHTML.OnBeginLoadingDocument](https://wiki.facepunch.com/gmod/Panel:OnBeginLoadingDocument) 或之后调用它们。
>
> 站点隔离会在导航时销毁 JavaScript 状态，就像真正的网页浏览器一样。
>
> 此工具包含一个针对 mainmenu.lua 的补丁，解决了 GMod 自身未使用正确方法的问题，但对于任何未正确处理 HTML 面板状态以进行 JS 操作的插件来说，**这是一个破坏性更改**。

**如果您想更深入：** 查看[我们分叉的 gmod-html](https://github.com/solsticegamestudios/gmod-html) 和[我们的 CEF 构建脚本](cef_build)。

# 📢 需要帮助/联系我们
* 阅读常见问题解答：https://www.solsticegamestudios.com/fixmedia/faq/
* Discord：https://www.solsticegamestudios.com/discord/
* 电子邮件：contact@solsticegamestudios.com

# 💖 帮助我们
该项目是开源的，并为 Garry's Mod 社区免费提供。

**如果您喜欢我们在这里所做的工作，请考虑[向我们捐助几美元](https://www.solsticegamestudios.com/donate/)！** 我们的工作 100% 由该工具的用户资助！
