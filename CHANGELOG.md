# Changelog

PassKey-FileCrypt 版本日志

## [2026-11-07] 重命名与文档校正

- 仓库从 `PassKey-PassWord` / `Passkey-Password` 重命名为 **PassKey-FileCrypt**（GitHub + Gitee）。
- README、CHANGELOG、CONTRIBUTING、Issue 模板按**实际功能**重写：
  - 明确它是文件批量加解密/混淆工具，不是密码管理器，也不是 WebAuthn Passkey。
  - 删除“密钥云端备份、跨设备同步”等代码中不存在的描述。
  - 明确 v2 是 XOR，v1 是 Caesar/反转/Base64/URL 编码，不是强密码学加密。
- 修复 Python 拖拽带空格路径解析错误：改用 `tk.splitlist`。
- 修复 Python 后台线程直接弹 `messagebox` 的线程安全问题：改到 `root.after` 中执行。
- Web manifest / 页面品牌改为 PassKey-FileCrypt；修复 Web 加密函数参数命名（`useFixedKey` → `useRandomKey`，行为不变）。
- Gitee 仓库把误放在 `main/` 下的桌面源码统一到根目录。
- GitHub 补上 LICENSE，移除只有占位符的 `PasskyForAndroid` 文件，新增 `docs/ANDROID.md`、`SECURITY.md`。
- 新增真实的 Android 说明：HBuilderX 工程才是 Android 源码；`PasskyForAndroid` 是 Windows MAUI WebView 外壳；GitHub Actions 的 Cordova 构建是实验性的。

> 说明：v2 的 XOR 和 v1 的编码方案都**不能**提供现代密码学安全性；这次只修正文档和明显代码问题，没有把算法升级成 AES。

## [v2.1.1] - 2026-09

> ⚠️ v2.1.1 与 v2.0 密文互通，**不兼容 v1.x**。

- 新增 Web/PWA 版（`V2.1.1_WEB`）：HTML + CSS + 原生 JavaScript，浏览器本地运算。
- 新增 Android APK 的两种打包：离线版（无网络权限）和联网版（保留网络权限）。
- Web/Android 与 v2.0 桌面版共用同一套 v2 密文/密钥格式。
- Web 端支持批量加密、批量解密、自定义密文/密钥文件名、运行日志。
- **实际没有实现云端同步/密钥备份**：网页和 APK 代码中没有网络请求；联网版只是保留网络权限。

## [v2.0] - 2026-09

> ⚠️ v2.0 更换算法，**不兼容 v1.x**。

- Windows Python + Tkinter 桌面版，批量加密/解密。
- 所有文件统一使用 16 字节 XOR 密钥；不再按后缀选择不同方案。
- 支持随机密钥（每次生成 16 字节）和固定测试密钥（写死在代码中，仅调试）。
- 支持无口令模式；口令只用于 XOR 包装密钥。
- 纯白 UI、进度条、日志、批量导出时保留原文件名/后缀。
- 仅适配 Windows；Linux/macOS 继续使用 v1.1.1-beta。

## [v1.1] - 2026-09

- Windows Python + Tkinter 深色 UI，核心算法与 v1.0 相同。
- 文本按后缀做 Caesar / 反转 / Base64 / URL 编码；二进制文件用 XOR。
- 彩色按钮、状态文字、窗口缩放等界面优化。
- 与 v1.0、v1.1.1-beta 密文互通。

## [v1.1.1-beta-for-Linux / Mac] - 2026-09

- v1 核心的 Linux / macOS 源码适配。
- 移除 Windows 专属 bat/cmd，增加 sh、plist、mobileconfig 等格式处理。
- 提供本地构建脚本，不提供预编译二进制。
- 与 v1.0、v1.1 密文互通。

## [v1.0] - 2026-09

- 初始版本：文本/二进制文件加解密、拖拽导入、进度条、Windows EXE 打包。
- 文本使用 Caesar/反转/Base64/URL 编码，二进制使用 XOR。
