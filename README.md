# PaddleOCR Android App

基于 Paddle Lite 的 Android OCR 应用，使用 PP-OCRv3 模型进行文字检测与识别。

## 特性

- **PP-OCRv3 模型**: 最新一代 PaddleOCR 模型
- **Paddle Lite v1.12**: 高性能推理引擎
- **Android 原生**: 基于 Java + JNI + C++ 开发
- **功能丰富**: 支持拍照识别、相册选图、检测+分类+识别全流程

## 技术栈

- Paddle Lite (推理引擎)
- OpenCV 4.2.0 (图像处理)
- Android SDK 24+
- CMake + NDK (JNI native 编译)

## 项目结构

```
paddle/
├── app/
│   ├── src/main/
│   │   ├── java/com/paddle/ocr/app/    # Java 源码
│   │   ├── cpp/                          # C++ JNI 原生代码
│   │   ├── res/                          # Android 资源
│   │   ├── assets/                       # 模型和字典文件
│   │   └── AndroidManifest.xml
│   ├── build.gradle                      # 模块构建配置
│   └── proguard-rules.pro
├── gradle/
├── build.gradle
├── settings.gradle
└── README.md
```

## 构建说明

### 环境要求
- Android Studio 2022+
- JDK 17+
- Android SDK 34
- NDK (已包含在项目构建中)
- CMake 3.22.1+

### 构建步骤
1. Clone 项目
```bash
git clone https://github.com/jbzmm/paddle.git
cd paddle
```

2. 打开 Android Studio，导入项目

3. 首次构建时，Gradle 会自动下载：
   - Paddle Lite 预编译库
   - OpenCV Android SDK
   - PP-OCRv3 检测/识别/分类模型
   - 中文字典文件

4. 构建 APK
```bash
./gradlew assembleDebug
```

5. 生成的 APK 位于：
```
app/build/outputs/apk/debug/app-debug.apk
```

## 模型信息

| 模型 | 版本 | 说明 |
|------|------|------|
| 检测模型 (det) | PP-OCRv3 | ch_PP-OCRv3_det_slim_infer |
| 识别模型 (rec) | PP-OCRv3 | ch_PP-OCRv3_rec_slim_infer |
| 分类模型 (cls) | v2.0 | ch_ppocr_mobile_v2.0_cls_slim_infer |

## License

Apache License 2.0
