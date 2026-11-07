# Android 源码与打包说明

## 结论先说

这个仓库里没有完整的 Android 原生工程可以直接 `gradle build`。实际 Android 源码是一个 **HBuilderX / 5+ App** 工程；`PasskyForAndroid` 这个名字对应的是 **Windows .NET MAUI WebView 外壳**，不是 Android。

## 三个容易混淆的东西

| 名称 | 实际是什么 | 位置 |
| --- | --- | --- |
| HBuilderX 5+ App 工程 | 真正的 Android 壳，`manifest.json` + `index.html` + `crypto.js`，用 `plus.android` 调 SAF 保存文件 | 本地 HBuilderX 工程，未完整上传到本仓库 |
| `PasskyForAndroid` | .NET MAUI WebView 外壳，TFM 是 `net9.0-windows10.0.19041.0`，**Windows only** | 历史本地工程；GitHub 之前的 1 字节占位文件已删除 |
| `NoInternet` | 上面 MAUI 工程的 Windows 自包含发布目录 | 本地构建产物，不应提交到 Git |

## HBuilderX 工程的实际情况

- `manifest.json`：应用名 `PassKey通行密钥`，包名 `com.passkey.tool`，版本 `1.0.0`，`targetSdkVersion: 30`。
- 权限：
  - `READ_EXTERNAL_STORAGE`
  - `WRITE_EXTERNAL_STORAGE`
  - `MANAGE_EXTERNAL_STORAGE`（Android 11+ 的高权限，应用商店审核严格）
- 保存逻辑：
  - 优先用 `plus.android` 的 `ACTION_OPEN_DOCUMENT_TREE` 选择目录，再用 `DocumentsContract.createDocument` 写文件；
  - 如果 SAF 不可用（如 HBuilder 基座），退回到 `plus.io.PUBLIC_DOWNLOADS` 保存到“下载”目录。
- 加密/解密逻辑：与 `V2.1.1_WEB/crypto.js` 相同，v2 密文格式与桌面版互通。
- 本仓库的 `V2.1.1_WEB` 可以直接作为 5+ App 的 `www` 内容；但 Android 壳还需要 HBuilderX 工程里的 `manifest.json` 和权限配置。

## 已知 Android 问题

1. **targetSdk 30 偏旧**：新系统上存储/权限行为与 targetSdk 34/35 不同；上架 Google Play 会被拒。
2. **`MANAGE_EXTERNAL_STORAGE`**：适合侧载/自用，不适合应用商店；更推荐只用 SAF。
3. **APK 是测试包**：未签名或测试签名，安装需允许“未知来源”。
4. **联网版没有云端代码**：当前 Web/Android 源码中没有网络请求；联网版只是保留网络权限，旧 README 的“密钥云端备份/同步”没有实现。
5. **HBuilder 基座与正式 APK 的保存行为不同**：基座没有 SAF 时只能写“下载”目录。
6. **大文件会占内存**：JS 端整文件读入并转 hex，超大文件可能崩溃。

## GitHub Actions 的 Android 工作流

`.github/workflows/build-android.yml` 是一个**实验性 Cordova 构建**：

- 用 Cordova 创建 `com.wisewang.passkey` 工程，把 `V2.1.1_WEB` 复制进 `www`；
- 构建 debug APK，上传为 Actions artifact；
- 它不是 HBuilderX 正式构建，也不等同于 Release 里的 APK；
- 没有做签名、Release 附件、权限收紧。

如果以后要正式维护 Android：

1. 把 HBuilderX 工程完整入库（至少 `manifest.json`、`index.html`、`crypto.js`、图标）；
2. 统一版本号到 `2.1.1`；
3. 评估把 `MANAGE_EXTERNAL_STORAGE` 降到 SAF-only；
4. 用 HBuilderX 正式打包并签名，再上传 Release；
5. 在真机上测试 SAF 写文本/二进制、中文名、带空格名、大文件。
