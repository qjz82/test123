# 屏译（overlay-translator）云编译工具包

让你**不用装 Android Studio**，也能编译出可安装的 APK（官方原版 / 修改版各一份）。

## 为什么需要云端编译

- 本 App 的离线翻译模块 `:llama-android` 是一个 **git 子模块**（llama.cpp），
  编译时要一并用 **NDK + CMake + Vulkan** 把原生代码编进 APK，本地构建链很重。
- 本机沙盒环境没有 JDK/Android SDK，且无法完整 clone 仓库，**无法直接编译**。
- 因此把编译交给 **GitHub Actions 云端**（自带 JDK + 安卓 SDK），你只下载成品。

## 工具包内容

- `translator-offline-toggle.patch` —— 修改版源码补丁（已用 git apply 验证可干净应用）：
  - `Settings.kt`：默认翻译引擎改为 `LOCAL_HY_MT2`（端侧离线，支持中/英/韩/日）
  - `OverlayManager.kt`：右上角常驻「译/原」悬浮按钮，点一下隐藏译文露出原屏、再点恢复
- `.github/workflows/build.yml` —— 云端编译流水线（自动处理子模块、安装 NDK/CMake、构建并上传 APK）
- `setup.sh` —— 一键克隆+打补丁+放入流水线+提交

## 使用步骤

1. 在本机（能正常联网的电脑，非当前沙盒）用 Git Bash / 终端运行：
   ```bash
   bash setup.sh
   ```
2. 按脚本提示，在 GitHub 新建空仓库，然后：
   ```bash
   git remote add origin https://github.com/<用户名>/<仓库名>.git
   git branch -M main
   git push -u origin main
   ```
3. push 到 `main` 后 **Actions 会自动同时构建 modified(修改版) 与 official(官方原版) 两份 APK**，
   无需手动点 Run workflow（手动触发时在 variant 里选一种即可）。
4. 构建完成后在 **Artifacts** 下载 `app-debug-modified.apk` / `app-debug-official.apk`，装到手机。

## 重要前提

- **需要 GitHub 账号**（免费）。公开仓库的 Actions 免费，私有仓库每月有免费额度，足够一次性构建。
- 构建耗时约 **15–40 分钟**（主要花在 llama.cpp 原生编译），请耐心等。
- 装到手机后**首次使用需联网下载约 1.1GB 的本地翻译模型**（仅一次），之后完全离线。
- 手机需 **Android 13+**（本地翻译引擎 `LOCAL_HY_MT2` 的硬要求）；64 位 ARM 设备。
- 若云端编译因 Vulkan 后端报错，把 `llama-android/build.gradle.kts` 里的
  `-DGGML_VULKAN=ON` 改成 `=OFF` 再重跑（CPU 后端照常工作，速度略降但够自用）。
