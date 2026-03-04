# WebIDE

一个集成 NDK 原生代码（C++）的 Android 示例应用，展示如何在 Kotlin 中通过 JNI 调用原生函数。

---

## 项目概览

| 属性 | 值 |
|------|-----|
| 包名 | `com.ting.webide` |
| 版本 | 1.0 (versionCode: 1) |
| 最低 SDK | 21 (Android 5.0) |
| 目标 SDK | 34 (Android 14) |
| 编译 SDK | 36 |
| 主要语言 | Kotlin + C++ |

---

## 技术栈

- **Kotlin** 2.1.0
- **Android Gradle Plugin** 8.13.0
- **Gradle** 9.0.0
- **NDK** 27.1.12297006
- **CMake** 3.22.1
- **Java/JVM 目标版本** Java 17
- **Material Design 3**（Day/Night 主题）
- **View Binding**

### 主要依赖

| 依赖 | 版本 | 用途 |
|------|------|------|
| androidx.core:core-ktx | 1.17.0 | Kotlin Android 扩展 |
| androidx.appcompat:appcompat | 1.7.1 | 向后兼容支持 |
| com.google.android.material:material | 1.13.0 | Material Design 组件 |
| androidx.constraintlayout:constraintlayout | 2.2.1 | 响应式布局 |

---

## 项目结构

```
WebIDE/
├── app/
│   └── src/main/
│       ├── AndroidManifest.xml          # 应用清单
│       ├── kotlin/com/ting/webide/
│       │   └── MainActivity.kt          # 主 Activity
│       ├── cpp/
│       │   ├── CMakeLists.txt           # CMake 构建配置
│       │   └── native-lib.cpp           # C++ 原生代码
│       └── res/
│           ├── layout/
│           │   └── activity_main.xml    # 主界面布局
│           ├── values/                  # 浅色主题资源
│           │   ├── strings.xml
│           │   ├── colors.xml
│           │   └── themes.xml
│           ├── values-night/            # 深色主题资源
│           │   ├── colors.xml
│           │   └── themes.xml
│           ├── drawable/                # 矢量图标资源
│           ├── mipmap-*/                # 多分辨率应用图标
│           └── xml/
│               ├── backup_rules.xml
│               └── data_extraction_rules.xml
├── gradle/
│   ├── libs.versions.toml               # 依赖版本目录
│   └── wrapper/
├── build.gradle.kts                     # 根构建脚本
├── settings.gradle.kts                  # 模块配置
├── gradle.properties                    # Gradle 属性配置
└── gradlew / gradlew.bat               # Gradle Wrapper
```

---

## 核心模块说明

### MainActivity.kt

位于 `app/src/main/kotlin/com/ting/webide/MainActivity.kt`

- 继承 `AppCompatActivity`
- 使用 **View Binding** 安全访问视图
- 通过 JNI 调用 C++ 函数 `stringFromJNI()`，并将返回字符串展示在 TextView 上
- 在 `onDestroy()` 中正确释放 binding 引用，避免内存泄漏
- 通过 `System.loadLibrary("myapplication")` 加载原生库

### native-lib.cpp

位于 `app/src/main/cpp/native-lib.cpp`

- 实现 JNI 导出函数，返回字符串 `"Hello from C++"`
- 遵循标准 JNI 命名规范（`Java_包名_类名_方法名`）

### CMakeLists.txt

位于 `app/src/main/cpp/CMakeLists.txt`

- 项目名：`myapplication`
- 将 `native-lib.cpp` 编译为共享库（`.so`）
- 链接 `android` 和 `log` 系统库

### activity_main.xml

- 使用 `ConstraintLayout` 居中布局
- 单个 `TextView` 显示来自 JNI 的字符串
- 字体大小 24sp

---

## 架构设计

本项目采用**单 Activity 架构**，核心分层如下：

```
Kotlin 层（UI）
     ↓ JNI 桥接
C++ 原生层（逻辑/性能）
```

**设计亮点：**
- **View Binding** — 替代 `findViewById()`，编译期类型安全
- **JNI Bridge 模式** — Kotlin 与 C++ 之间的标准接口
- **生命周期管理** — 在 `onDestroy` 中正确清理资源
- **Material3 主题** — 支持浅色/深色模式自动切换

---

## 功能特性

- [x] JNI 原生代码集成（Kotlin ↔ C++）
- [x] Material Design 3 UI 主题
- [x] 深色模式（Dark Mode）支持
- [x] View Binding 类型安全视图访问
- [x] 自适应图标（Adaptive Icons）
- [x] 多分辨率屏幕适配（mdpi ~ xxxhdpi）
- [x] RTL 语言方向支持
- [x] 数据备份与提取规则配置

---

## 构建与运行

### 前置条件

- Android Studio Hedgehog 或更高版本
- Android NDK 27.1.12297006
- CMake 3.22.1+
- JDK 17+

### 构建步骤

```bash
# 克隆项目
git clone <repo-url>
cd WebIDE

# 使用 Gradle Wrapper 构建
./gradlew assembleDebug

# 安装到设备/模拟器
./gradlew installDebug
```

### Gradle 配置

`gradle.properties` 中的关键配置：

```properties
org.gradle.jvmargs=-Xmx2048m   # Gradle 守护进程最大堆内存 2GB
org.gradle.configuration-cache=true
android.useAndroidX=true
android.nonTransitiveRClass=true
```

---

## 权限声明

| 权限 | 说明 |
|------|------|
| `FOREGROUND_SERVICE_DATA_SYNC` | 前台服务（数据同步） |

---

## 主题与样式

| 场景 | 主题 |
|------|------|
| 浅色模式 | `Theme.WebIDE`（基于 Material3.DayNight.NoActionBar）|
| 深色模式 | `Theme.WebIDE`（自动切换深色色板）|

---

## 开发说明

- 项目为**模板/示例工程**，适合作为集成 NDK 原生代码的 Android 项目起点
- 如需扩展，可在 `native-lib.cpp` 中添加更多 JNI 函数，并在 `MainActivity.kt` 中声明对应的 `external` 方法
- 已启用 Non-transitive R class，减少资源命名冲突

---

## 许可证

本项目暂未声明开源许可证，请联系作者获取使用授权。
