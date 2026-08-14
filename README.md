# Screen Translator Build

CI builds the overlay-translator app automatically via GitHub Actions (no local Android SDK needed).

- `.github/workflows/build.yml` — self-clones `overlay-translator`, pulls the `llama.cpp` submodule, applies `translator-offline-toggle.patch` for the **modified** variant, then compiles debug APKs for **official** and **modified** in parallel.
- `translator-offline-toggle.patch` — sets the default translator engine to offline (`LOCAL_HY_MT2`) and adds a tap-to-toggle (原文/译文) floating button.

Output: two APKs in the workflow **Artifacts** (`screen-translator-official-apk`, `screen-translator-modified-apk`).

## On-device setup
1. Install the APK (arm64).
2. Grant: screen capture (MediaProjection), floating-window, notification.
3. OCR engine = ML Kit (on-device); translator engine = local Hy-MT2 (downloads ~1.1 GB model once, then fully offline). Requires Android 13+.
4. Pick target language among 中文 / English / 한국어 / 日本語; tap the "译/原" button to switch between translation and original.
