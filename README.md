# 🔐 PassKey-FileCrypt｜本地批量文件加密 / 解密工具

> **先说明：这个项目不是密码管理器，也不是 WebAuthn 的 Passkey。**
> 它的实际功能是：把一批文件打包成 `cipher.txt` + `key.txt`，再用口令解密。
> 仓库原名 `PassKey-PassWord`，2026-11 重命名为 **PassKey-FileCrypt**。

> ## ⚠️ 安全警告（必读）
>
> - **v2 使用的是 XOR（16 字节密钥）+ 口令 XOR 包装**，不是现代密码学加密。
> - **v1 使用的是 Caesar / 反转 / Base64 / URL 编码**，只算混淆。
> - 没有 KDF、盐、Nonce、HMAC/AEAD；不能防篡改，不能抗已知明文、密钥复用等分析。
> - **不要用它保护密码、证件、财务、工作机密等重要资料。**
> - 需要真正的加密，请使用 VeraCrypt、7-Zip（AES-256）、age、GPG 等成熟工具。
> - 口令一旦丢失，文件无法恢复。

---

## ✅ 实际能做什么

- 批量选择多个文件，生成两个文本文件：
  - `cipher.txt`：JSON 密文包，文件内容以 hex 保存；
  - `key.txt`：JSON 密钥文件，包含 16 字节 XOR 密钥（可被口令包装）。
- 解密必须同时有 `cipher.txt`、`key.txt` 和当时的口令；无口令模式则直接使用 `key.txt`。
- 批量解密后按原始文件名、原后缀导出。
- 桌面、Web、Android 的 v2 密文/密钥互通。
- 支持拖拽文件、进度条、日志、随机密钥/固定测试密钥。
- v2 桌面端可打包成单文件 EXE；Web 端浏览器直接运行；Android 端用 SAF 写入自选目录或“下载”目录。

## 🧩 版本与平台

| 版本 | 形态 | 实际说明 | 兼容性 |
| --- | --- | --- | --- |
| v1.0 | Python + Tkinter | 文本按后缀做 Caesar/反转/Base64/URL 编码，二进制用 XOR | 与 v1.1、v1.1.1-beta 互通 |
| v1.1 | Python + Tkinter | 深色 UI，核心算法同 v1.0 | 与 v1.0、v1.1.1-beta 互通 |
| v1.1.1-beta | Linux/macOS 源码 | v1 核心 + 平台打包脚本；不提供预编译二进制 | 与 v1.x 互通 |
| v2.0 | Windows Python + Tkinter | 统一 XOR、批量、随机/固定密钥、纯白 UI | 与 v2.1.1 Web/Android 互通 |
| v2.1.1 Web | HTML + CSS + JavaScript | 浏览器本地运算，批量加解密；与 v2.0 互通 | 与 v2.0/Android 互通 |
| v2.1.1 Android | HBuilderX / 5+ App | 通过 `plus.android` 的 SAF 保存文件；离线/联网测试包 | 与 v2.0/Web 互通 |
| Windows 离线版 | .NET MAUI WebView | 工程名 `PasskyForAndroid`，实际 TFM 是 `net9.0-windows10.0.19041.0`，**不是 Android**；`NoInternet` 是它的发布产物 | v2 |
| Glass Web | Gitee 额外 UI | `V2.1.1_GLASS_WEB`，液态玻璃风格，核心算法同 v2.1.1 | v2 |

> **v1.x 与 v2.x 完全不兼容**：旧密文/旧密钥不能用 v2 解，v2 密文也不能用 v1 解。
> v2 桌面、Web、Android 之间可以互相解密。

## 📥 下载与在线使用

- **GitHub Release**：<https://github.com/WiseWang1013/PassKey-FileCrypt/releases>
- **Gitee Release**：<https://gitee.com/WiseWang1013/PassKey-FileCrypt/releases>
- **Web 在线版**：<https://wisewang1013.github.io/PassKey-FileCrypt/V2.1.1_WEB/index.html>
- 也可以直接打开仓库里的 `V2.1.1_WEB/index.html`，不需要服务器。

Android APK 是测试构建包，未上架应用商店；安装时需要允许“未知来源”。离线包与联网包的区别主要是网络权限，**当前代码没有实现云端同步**，详见下面的“已知限制”。

## 📂 仓库结构

### GitHub

```
PassKey.py                  v2.0 Windows 桌面主程序
V2.1.1_WEB/                 v2.1.1 Web/PWA 源码
v1.0.0/ v1.1.0/             历史版本源码
v1.1.1-beta-for-Linux/      Linux Beta 源码
v1.1.1-beta-for-Mac/        macOS Beta 源码
docs/ANDROID.md             Android 源码/打包说明
SECURITY.md                 安全说明
CHANGELOG.md                版本日志
CONTRIBUTING.md             贡献指南
LICENSE                     MIT
```

### Gitee

```
PassKey.py                  v2.0 Windows 桌面主程序
V2.1.1_WEB/                 v2.1.1 Web/PWA 源码
V2.1.1_GLASS_WEB/           Gitee 额外的液态玻璃 UI 版本
v1.0.0/ v1.1.0/             历史版本源码
v1.1.1-beta-for-Linux/      Linux Beta 源码
v1.1.1-beta-for-Mac/        macOS Beta 源码
docs/ANDROID.md             Android 源码/打包说明
SECURITY.md                 安全说明
LICENSE                     MIT
```

> 说明：GitHub 和 Gitee 之前出现过结构不一致（Gitee 有 Glass Web，GitHub 缺 LICENSE、`PasskyForAndroid` 只有占位文件）。这次已把 Gitee 源码统一到根目录、给 GitHub 补上 LICENSE，并删除 GitHub 的占位文件，Android 说明放到 `docs/ANDROID.md`。

## 🚀 快速使用

### Windows 桌面版（v2.0）

```powershell
pip install tkinterdnd2
python PassKey.py
```

1. 点“多选待加密文件”，或把文件拖进窗口；
2. 选择随机密钥（推荐）或固定测试密钥；
3. 点“批量加密全部文件”，输入口令（可留空）；
4. 分别导出“密文包”和“统一密钥”两个 txt；
5. 解密时依次选择 `cipher.txt`、`key.txt`，输入口令，点“批量解密”；
6. 点“批量导出解密文件”选择输出目录，文件名/后缀会保留。

### Web 版

直接用浏览器打开 `V2.1.1_WEB/index.html`，或访问上面的在线地址：

1. 选择多个待加密文件；
2. 输入口令，导出 `cipher.txt` / `key.txt`；
3. 解密时选择这两个 txt，输入口令，浏览器会逐个下载还原文件。

### Android

APK 在 Release 里下载。优先使用 SAF 选择保存目录；在 HBuilder 基座中会退回保存到“下载”文件夹。
Android 的打包说明、权限和已知问题见 [`docs/ANDROID.md`](docs/ANDROID.md)。

## ⚠️ 已知限制

- **没有云端备份/同步**：旧 README 写过“密钥云端备份、跨设备同步”，但 Web/Android 代码里没有网络请求或云端实现；当前所有操作都在本机。README 已按实际功能修正。
- **没有完整性校验**：密文包/密钥被篡改或口令错误时，可能得到乱码或直接报错；没有 MAC/AEAD。
- **大文件占用内存高**：Python 和 JS 都是整文件读入内存再转 hex，内存约为文件的 2~3 倍；超大文件可能卡死。
- **多文件下载可能被浏览器拦截**：Web 解密后逐个触发下载，Chrome/Edge 可能要求允许多文件下载。
- **没有口令找回**：口令/密钥丢失即无法恢复。
- **Android 是测试包**：targetSdk 30，申请了 `MANAGE_EXTERNAL_STORAGE` 等权限，适合侧载/自用，不适合直接上架 Google Play。
- **`PasskyForAndroid` 名字有误导**：它实际是 Windows MAUI WebView 外壳；真正 Android 源码是 HBuilderX 工程。
- **GitHub Actions 的 Android 工作流是实验性 Cordova 构建**，产物和 HBuilder 正式 APK 不一定一致，Release 里的 APK 以实际构建记录为准。

## 🛠️ 本次更新（2026-11）

- 仓库从 `PassKey-PassWord` / `Passkey-Password` 重命名为 **PassKey-FileCrypt**（GitHub + Gitee）。
- README/CHANGELOG/CONTRIBUTING/Issue 模板全部按**实际功能**重写，删除“云端同步”等未实现描述。
- 明确标注 XOR/编码只是混淆，不是强加密。
- 修复 Python 拖拽带空格路径解析错误（改用 `tk.splitlist`）。
- 修复 Python 后台线程直接弹 `messagebox` 的线程安全问题（改为 `root.after`）。
- 统一 Gitee 根目录源码结构，补 GitHub LICENSE，移除 GitHub 的 `PasskyForAndroid` 占位文件。
- 新增 `SECURITY.md`、`docs/ANDROID.md`，并把 Web manifest/页面品牌改为 PassKey-FileCrypt。

## 🧑‍💻 开发与构建

- Python 依赖：`tkinterdnd2`；打包：`pyinstaller` + `build.bat` / `PassKey.spec`。
- Web：纯静态 HTML/CSS/JS，直接打开 `V2.1.1_WEB/index.html`。
- Android：HBuilderX 5+ App 工程，见 `docs/ANDROID.md`。
- GitHub Actions：`build-android.yml` 是实验性 Cordova 离线 APK 构建；正式 APK 仍以 HBuilder 构建为准。

## 📄 文档

- [CHANGELOG.md](CHANGELOG.md)
- [CONTRIBUTING.md](CONTRIBUTING.md)
- [SECURITY.md](SECURITY.md)
- [docs/ANDROID.md](docs/ANDROID.md)
- [Linux Beta README](v1.1.1-beta-for-Linux/README.md)
- [macOS Beta README](v1.1.1-beta-for-Mac/README.md)

## 📜 开源协议

MIT。仅供学习、参考和二次练习；禁止用于恶意加密、锁机、勒索等违法违规用途。
