# rikkahub适配器

这是一个独立的 Android 应用，用于将上游 RikkaHub 数据库备份转换为 RikkaHub 可以识别的固定版本 `.rhk` 转换格式。

适配器在手机本地运行。它通过 Android 文件选择器读取选中的备份，创建新的 `.rhk` 文件，并且不会打开或写入 RikkaHub 应用数据库。

## 使用方法

1. 从 GitHub 发布页安装适配器 APK。
2. 选择上游 RikkaHub 备份。
3. 导出生成的 `.rhk` 文件。
4. 在 RikkaHub 中依次打开“设置 → 备份与恢复 → 本地 → 从其他app导入 → 导入转换后的 RikkaHub 数据”。

## 转换格式

转换包是一个 ZIP 压缩文件，包含以下内容。

- `manifest.json`，记录格式版本、来源信息、数量和警告。
- `conversations.json`，记录标准化后的会话和消息节点。
- `diagnostics.json`，记录来源数据库结构和转换诊断信息。

应用的导入约定固定为 `rikkahub-transfer` 格式版本 `1`。上游数据库结构发生变化时，应在此适配器中处理，不要修改这个约定。无法识别或无法读取的数据行会被记录，不会被静默丢弃。

## 本地构建

项目使用仓库缓存的 Gradle 9.4.1 和 JDK 21。调试 APK 的构建命令如下。

```text
gradle -p import-adapter :app:assembleDebug
```

如需发布到 GitHub，请在 `local.properties` 或 CI 密钥中配置固定的 Android 发布版签名密钥。不要提交签名密钥或 `local.properties`。Android 应用更新要求所有版本使用同一签名密钥。

本地签名配置如下。

```properties
adapter.storeFile=path/to/import-adapter-release.jks
adapter.storePassword=...
adapter.keyAlias=...
adapter.keyPassword=...
```

使用以下命令构建已签名的发布 APK。

```text
gradle -p import-adapter :app:assembleRelease
```

将 `import-adapter/app/build/outputs/apk/release/app-release.apk` 及其 SHA-256 值上传到 GitHub 发布页，例如 `adapter-v1.0.0`。适配器版本标签与主应用版本标签分开管理。
