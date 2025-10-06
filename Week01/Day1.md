# React Native入门 - 第1天：选择开发路径 - Expo vs React Native CLI | React Native Introduction - Day 1: Choosing Your Path - Expo vs React Native CLI

## 学习目标 | Learning Objectives
- 理解Expo和React Native CLI的核心区别及各自的优势 | Understand the core differences between Expo and React Native CLI and their respective advantages
- 掌握Node.js环境的安装与验证方法 | Master Node.js installation and verification methods
- 能够使用Expo CLI创建并运行第一个移动应用 | Be able to create and run your first mobile application using Expo CLI
- 理解React Native项目结构与React Web项目的关键差异 | Understand key differences between React Native and React Web project structures
- 熟悉移动开发环境的基本工具链 | Familiarize yourself with the basic toolchain for mobile development
- 能够在iOS模拟器和Android模拟器上运行应用 | Be able to run applications on iOS Simulator and Android Emulator

## 详细内容 | Detailed Content

### 1. 开发框架选择：Expo vs React Native CLI | Development Framework Choice: Expo vs React Native CLI (1小时 | 1 hour)

- **Expo框架概念 | Expo Framework Concept**

  **概念定义 | Concept Definition:**
  Expo是一个围绕React Native构建的框架和平台，提供了一套工具和服务，使得React Native开发更加简单和快速。它通过预先配置好的环境和丰富的API，让开发者无需直接处理原生代码就能构建功能完整的移动应用。 | Expo is a framework and platform built around React Native that provides a set of tools and services to make React Native development simpler and faster. Through pre-configured environments and rich APIs, it allows developers to build fully-functional mobile applications without directly handling native code.

  **核心特征 | Key Characteristics:**
  - 特征1：开箱即用的开发环境，无需配置Xcode或Android Studio即可开始开发 | Characteristic 1: Out-of-the-box development environment - start developing without configuring Xcode or Android Studio
  - 特征2：提供丰富的预构建API（相机、位置、通知等），无需安装原生依赖 | Characteristic 2: Provides rich pre-built APIs (camera, location, notifications, etc.) without installing native dependencies
  - 特征3：支持通过Expo Go应用在真实设备上即时预览，无需编译 | Characteristic 3: Supports instant preview on real devices via Expo Go app without compilation
  - 特征4：有一定的限制，某些高级原生功能可能需要"ejecting"或使用开发构建 | Characteristic 4: Has certain limitations - some advanced native features may require "ejecting" or using development builds

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Expo是否可以让你在不安装Xcode的情况下开发iOS应用？| Can Expo allow you to develop iOS apps without installing Xcode?
     **答案 | Answer:** 是的 | Yes - Expo提供了托管的开发环境，初期开发阶段不需要Xcode | Expo provides a managed development environment that doesn't require Xcode in the initial development phase
  2. 使用Expo时是否需要编写原生代码（Swift/Objective-C/Java/Kotlin）？| Do you need to write native code (Swift/Objective-C/Java/Kotlin) when using Expo?
     **答案 | Answer:** 不需要 | No - Expo的核心优势就是让开发者只用JavaScript/TypeScript就能开发移动应用 | Expo's core advantage is allowing developers to build mobile apps using only JavaScript/TypeScript
  3. Expo Go应用的主要作用是什么？| What is the main purpose of the Expo Go app?
     **答案 | Answer:** 它是一个运行在真实设备上的客户端应用，可以扫描二维码或通过局域网加载你的Expo项目，实现即时预览和热重载 | It's a client application running on real devices that can scan QR codes or load your Expo project via LAN, enabling instant preview and hot reload
  4. 所有React Native功能都可以在Expo中使用吗？| Are all React Native features available in Expo?
     **答案 | Answer:** 不是全部 | Not all - Expo默认环境有一些限制，但可以通过"prebuild"或自定义开发构建来访问更多原生功能 | Expo's default environment has some limitations, but you can access more native features through "prebuild" or custom development builds

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # Expo项目创建示例 | Expo project creation example
  # 全局安装Expo CLI（新版本推荐使用npx，无需全局安装）
  # Global install Expo CLI (newer versions recommend using npx without global install)
  npm install -g expo-cli

  # 或者直接使用npx创建项目（推荐方式）
  # Or directly use npx to create project (recommended approach)
  npx create-expo-app@latest MyFirstApp

  # 进入项目目录 | Navigate to project directory
  cd MyFirstApp

  # 启动开发服务器 | Start development server
  npx expo start
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行`npx expo start`后，终端会显示什么？| What will the terminal display after running `npx expo start`?
    **答案 | Answer:** 会显示一个二维码、本地开发服务器地址（如http://localhost:8081）以及多个快捷键选项（如按'i'打开iOS模拟器，按'a'打开Android模拟器）| Will display a QR code, local development server address (like http://localhost:8081), and multiple shortcut key options (like pressing 'i' for iOS simulator, 'a' for Android emulator)
  - 如何在真实iPhone设备上预览Expo应用？| How do you preview an Expo app on a real iPhone device?
    **答案 | Answer:** 在iPhone上安装Expo Go应用，然后使用相机扫描终端显示的二维码，或者确保手机和电脑在同一WiFi网络下，在Expo Go中输入开发服务器地址 | Install the Expo Go app on iPhone, then use the camera to scan the QR code displayed in terminal, or ensure phone and computer are on the same WiFi network and enter the development server address in Expo Go

  **常见误区检查 | Common Misconception Checks:**
  - Expo是否是React Native的替代品？| Is Expo a replacement for React Native?
    **答案 | Answer:** 不是，Expo是基于React Native构建的框架，它是React Native的增强版本，而非替代品。Expo项目本质上仍然是React Native项目 | No, Expo is a framework built on React Native - it's an enhanced version of React Native, not a replacement. Expo projects are essentially still React Native projects
  - 使用Expo是否意味着永远不能使用原生模块？| Does using Expo mean you can never use native modules?
    **答案 | Answer:** 不是，现代Expo支持"config plugins"和自定义开发构建，可以添加任何原生模块，只是不能在Expo Go应用中直接使用这些自定义原生模块 | No, modern Expo supports "config plugins" and custom development builds, allowing you to add any native modules - you just can't use these custom native modules directly in the Expo Go app

- **React Native CLI概念 | React Native CLI Concept**

  **概念定义 | Concept Definition:**
  React Native CLI是React Native的官方命令行工具，提供了一个更"原生"的开发体验。它需要完整配置原生开发环境（Xcode和Android Studio），但提供了对原生代码的完全控制权和灵活性。 | React Native CLI is React Native's official command-line tool that provides a more "native" development experience. It requires full configuration of native development environments (Xcode and Android Studio) but offers complete control and flexibility over native code.

  **核心特征 | Key Characteristics:**
  - 特征1：需要完整的原生开发环境配置（macOS上需要Xcode，Android开发需要Android Studio和JDK）| Characteristic 1: Requires full native development environment setup (Xcode on macOS, Android Studio and JDK for Android development)
  - 特征2：可以直接访问和修改原生代码（iOS的Objective-C/Swift，Android的Java/Kotlin）| Characteristic 2: Direct access to and modification of native code (Objective-C/Swift for iOS, Java/Kotlin for Android)
  - 特征3：对第三方原生库没有限制，可以使用任何npm包或原生模块 | Characteristic 3: No restrictions on third-party native libraries - can use any npm package or native module
  - 特征4：需要运行原生编译过程，首次构建时间较长 | Characteristic 4: Requires native compilation process with longer initial build times

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 使用React Native CLI是否需要安装Xcode？| Do you need to install Xcode when using React Native CLI?
     **答案 | Answer:** 如果要开发iOS应用，是的 | Yes, if you want to develop iOS apps - Xcode是iOS开发的必需工具，包含iOS模拟器和编译工具 | Xcode is a required tool for iOS development, including iOS Simulator and compilation tools
  2. React Native CLI项目中是否可以直接编辑原生代码？| Can you directly edit native code in React Native CLI projects?
     **答案 | Answer:** 可以 | Yes - 项目包含`ios/`和`android/`文件夹，其中包含完整的原生代码，可以在Xcode或Android Studio中打开和修改 | Projects include `ios/` and `android/` folders containing complete native code that can be opened and modified in Xcode or Android Studio
  3. React Native CLI和Expo CLI哪个启动速度更快？| Which starts faster - React Native CLI or Expo CLI?
     **答案 | Answer:** Expo CLI | Expo CLI - 因为Expo不需要编译原生代码，而React Native CLI每次启动都需要构建原生部分 | Because Expo doesn't need to compile native code, while React Native CLI needs to build native parts each time it starts
  4. 是否可以在已有的React Native CLI项目中添加任何原生库？| Can you add any native library to an existing React Native CLI project?
     **答案 | Answer:** 可以 | Yes - React Native CLI不受限制，可以通过CocoaPods（iOS）或Gradle（Android）添加任何原生依赖 | React Native CLI has no restrictions - you can add any native dependencies via CocoaPods (iOS) or Gradle (Android)

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # React Native CLI项目创建示例 | React Native CLI project creation example
  # 首先确保已安装Node.js、Watchman（Mac推荐）、Xcode、Android Studio
  # First ensure Node.js, Watchman (recommended for Mac), Xcode, Android Studio are installed

  # 使用React Native CLI创建新项目 | Create new project using React Native CLI
  npx react-native@latest init MyRNApp

  # 进入项目目录 | Navigate to project directory
  cd MyRNApp

  # iOS开发：安装iOS依赖（仅Mac）| iOS development: install iOS dependencies (Mac only)
  cd ios && pod install && cd ..

  # 运行iOS应用（仅Mac）| Run iOS app (Mac only)
  npx react-native run-ios

  # 运行Android应用 | Run Android app
  npx react-native run-android
  ```

  **项目结构对比 | Project Structure Comparison:**
  ```
  # Expo项目结构（简化）| Expo project structure (simplified)
  MyFirstApp/
  ├── App.js              # 主应用组件 | Main app component
  ├── app.json            # Expo配置 | Expo configuration
  ├── package.json        # 依赖管理 | Dependency management
  ├── node_modules/       # NPM包 | NPM packages
  └── assets/            # 图片、字体等资源 | Images, fonts, and other assets

  # React Native CLI项目结构（简化）| React Native CLI project structure (simplified)
  MyRNApp/
  ├── android/           # Android原生代码 | Android native code
  ├── ios/              # iOS原生代码 | iOS native code
  ├── App.tsx           # 主应用组件 | Main app component
  ├── index.js          # 应用入口 | App entry point
  ├── package.json      # 依赖管理 | Dependency management
  └── node_modules/     # NPM包 | NPM packages
  ```

  **实践检查问题 | Practice Checking Questions:**
  - React Native CLI项目中的`android/`和`ios/`文件夹包含什么？| What do the `android/` and `ios/` folders in React Native CLI projects contain?
    **答案 | Answer:** 包含完整的原生应用代码 - `android/`包含Java/Kotlin代码和Gradle配置，`ios/`包含Objective-C/Swift代码和Xcode项目文件 | Contain complete native application code - `android/` includes Java/Kotlin code and Gradle configuration, `ios/` includes Objective-C/Swift code and Xcode project files
  - 为什么Expo项目没有`android/`和`ios/`文件夹？| Why don't Expo projects have `android/` and `ios/` folders?
    **答案 | Answer:** 因为Expo采用"托管工作流"（managed workflow），原生代码被Expo SDK管理和抽象化了。如果需要原生代码，可以运行`npx expo prebuild`生成这些文件夹 | Because Expo uses a "managed workflow" where native code is managed and abstracted by Expo SDK. If you need native code, you can run `npx expo prebuild` to generate these folders

  **常见误区检查 | Common Misconception Checks:**
  - React Native CLI是否比Expo更"专业"或更"正式"？| Is React Native CLI more "professional" or more "official" than Expo?
    **答案 | Answer:** 不是，两者都是官方支持的开发方式。Expo在许多生产环境应用中被使用（包括大公司），选择应基于项目需求而非"专业程度" | No, both are officially supported development approaches. Expo is used in many production applications (including by large companies) - the choice should be based on project requirements, not "professionalism"
  - 是否需要从Expo "eject"才能发布应用到App Store？| Do you need to "eject" from Expo to publish apps to the App Store?
    **答案 | Answer:** 不需要，Expo提供了EAS Build服务，可以直接构建并提交应用到App Store和Google Play，无需eject | No, Expo provides EAS Build service that can directly build and submit apps to App Store and Google Play without ejecting

### 2. 选择决策框架 | Decision Framework for Choosing (30分钟 | 30 minutes)

- **何时选择Expo | When to Choose Expo**

  **概念定义 | Concept Definition:**
  Expo最适合快速原型开发、标准功能应用、以及希望专注于JavaScript开发而非原生开发的项目。对于有React经验但移动开发新手的开发者，Expo提供了最平滑的学习曲线。 | Expo is best suited for rapid prototyping, standard-feature applications, and projects where you want to focus on JavaScript development rather than native development. For developers with React experience but new to mobile development, Expo provides the smoothest learning curve.

  **适用场景 | Suitable Scenarios:**
  - 场景1：你是移动开发新手，想快速开始构建应用 | Scenario 1: You're new to mobile development and want to start building apps quickly
  - 场景2：项目不需要复杂的原生模块或自定义原生代码 | Scenario 2: Project doesn't require complex native modules or custom native code
  - 场景3：需要快速迭代和原型验证 | Scenario 3: Need rapid iteration and prototype validation
  - 场景4：团队主要由JavaScript/React开发者组成，缺少原生开发经验 | Scenario 4: Team primarily consists of JavaScript/React developers with limited native development experience

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果你需要使用一个需要原生代码的特殊蓝牙库，Expo是最佳选择吗？| If you need to use a special Bluetooth library that requires native code, is Expo the best choice?
     **答案 | Answer:** 取决于情况 | It depends - 如果该库有Expo config plugin支持，可以使用Expo。如果没有，可能需要创建自定义开发构建或选择React Native CLI | If the library has Expo config plugin support, you can use Expo. If not, you might need to create a custom development build or choose React Native CLI
  2. Expo是否适合需要快速MVP（最小可行产品）验证的创业项目？| Is Expo suitable for startup projects that need rapid MVP (Minimum Viable Product) validation?
     **答案 | Answer:** 非常适合 | Very suitable - Expo的快速开发能力和无需原生环境配置的特点使其成为MVP开发的理想选择 | Expo's rapid development capability and no-need-for-native-environment-setup make it ideal for MVP development
  3. 使用Expo是否意味着无法发布到应用商店？| Does using Expo mean you cannot publish to app stores?
     **答案 | Answer:** 不是 | No - Expo应用可以通过EAS Build完全正常地发布到Apple App Store和Google Play Store | Expo apps can be fully published to Apple App Store and Google Play Store via EAS Build

- **何时选择React Native CLI | When to Choose React Native CLI**

  **概念定义 | Concept Definition:**
  React Native CLI最适合需要深度原生集成、使用大量原生模块、或需要在现有原生应用中集成React Native的项目。它提供了最大的灵活性和控制权，但需要更多的配置和原生开发知识。 | React Native CLI is best suited for projects requiring deep native integration, heavy use of native modules, or integration of React Native into existing native apps. It provides maximum flexibility and control but requires more configuration and native development knowledge.

  **适用场景 | Suitable Scenarios:**
  - 场景1：需要使用许多自定义原生模块或第三方原生库 | Scenario 1: Need to use many custom native modules or third-party native libraries
  - 场景2：需要直接修改原生代码以优化性能或添加特定功能 | Scenario 2: Need to directly modify native code for performance optimization or specific features
  - 场景3：在现有的iOS或Android应用中集成React Native | Scenario 3: Integrating React Native into existing iOS or Android applications
  - 场景4：团队有原生开发经验，能够处理原生代码问题 | Scenario 4: Team has native development experience and can handle native code issues

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果项目需要使用没有Expo支持的原生SDK，应该选择什么？| If a project needs to use a native SDK without Expo support, what should you choose?
     **答案 | Answer:** React Native CLI或带自定义开发构建的Expo | React Native CLI or Expo with custom development builds - CLI提供更直接的原生访问，但现代Expo也可以通过config plugins和开发构建支持大多数原生SDK | CLI provides more direct native access, but modern Expo can also support most native SDKs through config plugins and development builds
  2. React Native CLI是否需要团队有原生开发知识？| Does React Native CLI require the team to have native development knowledge?
     **答案 | Answer:** 强烈推荐但非必须 | Strongly recommended but not mandatory - 基础的React Native开发可以只用JavaScript，但当遇到原生问题、需要升级依赖或配置原生模块时，原生知识会很关键 | Basic React Native development can be done with JavaScript only, but native knowledge becomes crucial when encountering native issues, upgrading dependencies, or configuring native modules

**选择建议总结 | Choice Recommendation Summary:**

| 考虑因素 Factor | Expo | React Native CLI |
|---|---|---|
| 学习曲线 Learning Curve | 平缓，适合初学者 Gentle, suitable for beginners | 陡峭，需要原生知识 Steep, requires native knowledge |
| 开发速度 Development Speed | 快速，即时预览 Fast, instant preview | 较慢，需编译 Slower, requires compilation |
| 原生功能访问 Native Feature Access | 丰富但有限制 Rich but limited | 完全无限制 Completely unrestricted |
| 项目大小 Project Size | 较大（包含Expo SDK）Larger (includes Expo SDK) | 较小，按需添加 Smaller, add as needed |
| 适合场景 Suitable For | 快速开发、标准应用 Rapid development, standard apps | 复杂集成、定制需求 Complex integration, custom requirements |

**对于本课程的建议 | Recommendation for This Course:**
作为一个有5年React经验但移动开发新手的学习者，我们将从Expo开始。这能让你专注于理解React Native的核心概念，而不被原生环境配置分散注意力。随着学习深入，我们会探索如何使用Expo的高级特性，以及何时考虑使用React Native CLI。 | As a learner with 5 years of React experience but new to mobile development, we'll start with Expo. This allows you to focus on understanding React Native's core concepts without being distracted by native environment configuration. As we progress, we'll explore how to use Expo's advanced features and when to consider using React Native CLI.

### 3. Node.js环境设置 | Node.js Environment Setup (30分钟 | 30 minutes)

- **Node.js版本管理 | Node.js Version Management**

  **概念定义 | Concept Definition:**
  Node.js是运行JavaScript的运行时环境，是React Native开发的基础依赖。合理的版本管理能确保项目兼容性和团队协作顺畅。推荐使用LTS（长期支持）版本，并使用版本管理工具如nvm或fnm。 | Node.js is a JavaScript runtime environment and a fundamental dependency for React Native development. Proper version management ensures project compatibility and smooth team collaboration. LTS (Long Term Support) versions are recommended, along with version management tools like nvm or fnm.

  **核心特征 | Key Characteristics:**
  - 特征1：React Native项目通常需要Node.js 18或更高版本 | Characteristic 1: React Native projects typically require Node.js 18 or higher
  - 特征2：不同项目可能需要不同的Node.js版本，版本管理工具（nvm/fnm）可以轻松切换 | Characteristic 2: Different projects may require different Node.js versions - version management tools (nvm/fnm) allow easy switching
  - 特征3：npm（Node Package Manager）会随Node.js一起安装，用于管理JavaScript包 | Characteristic 3: npm (Node Package Manager) is installed with Node.js and used to manage JavaScript packages

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Node.js和npm是同一个东西吗？| Are Node.js and npm the same thing?
     **答案 | Answer:** 不是 | No - Node.js是运行时环境，npm是包管理器。npm会随Node.js自动安装，但它们是不同的工具 | Node.js is a runtime environment, npm is a package manager. npm is automatically installed with Node.js, but they are different tools
  2. 是否必须使用nvm或fnm来管理Node.js？| Is it mandatory to use nvm or fnm to manage Node.js?
     **答案 | Answer:** 不是必须的 | Not mandatory - 可以直接安装单一版本的Node.js，但版本管理工具在处理多个项目时非常有用，是专业开发的最佳实践 | You can install a single version of Node.js directly, but version management tools are very useful when handling multiple projects and are a best practice for professional development
  3. 安装Node.js后如何验证安装成功？| How do you verify Node.js installation is successful?
     **答案 | Answer:** 在终端运行`node --version`和`npm --version`，如果显示版本号（如v18.17.0），说明安装成功 | Run `node --version` and `npm --version` in terminal - if version numbers are displayed (like v18.17.0), installation is successful

  **安装步骤与代码示例 | Installation Steps and Code Examples:**
  ```bash
  # 方法1：使用官方安装器（适合初学者）| Method 1: Using official installer (suitable for beginners)
  # 访问 https://nodejs.org 下载LTS版本
  # Visit https://nodejs.org to download LTS version

  # 方法2：使用Homebrew安装（Mac推荐）| Method 2: Using Homebrew (recommended for Mac)
  brew install node

  # 方法3：使用nvm安装（推荐用于管理多版本）| Method 3: Using nvm (recommended for managing multiple versions)
  # 首先安装nvm | First install nvm
  # Mac/Linux:
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

  # 使用nvm安装Node.js LTS版本 | Install Node.js LTS version using nvm
  nvm install --lts

  # 设置默认版本 | Set default version
  nvm alias default node

  # 验证安装 | Verify installation
  node --version    # 应显示 v18.x.x 或更高 | Should show v18.x.x or higher
  npm --version     # 应显示 9.x.x 或更高 | Should show 9.x.x or higher

  # 查看已安装的Node.js版本 | View installed Node.js versions
  nvm list

  # 切换到特定版本 | Switch to specific version
  nvm use 18.17.0
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行`node --version`后没有输出或显示"command not found"，可能是什么原因？| If running `node --version` produces no output or shows "command not found", what could be the reason?
    **答案 | Answer:** Node.js未正确安装或环境变量PATH未正确配置。需要重新安装Node.js或重启终端，确保安装路径被添加到系统PATH中 | Node.js is not properly installed or environment variable PATH is not correctly configured. Need to reinstall Node.js or restart terminal, ensuring installation path is added to system PATH
  - 如果项目需要Node.js 16但系统安装的是Node.js 18，应该怎么办？| If a project requires Node.js 16 but the system has Node.js 18 installed, what should you do?
    **答案 | Answer:** 使用nvm安装Node.js 16（`nvm install 16`），然后在该项目目录下切换版本（`nvm use 16`），或创建`.nvmrc`文件指定版本 | Use nvm to install Node.js 16 (`nvm install 16`), then switch versions in that project directory (`nvm use 16`), or create a `.nvmrc` file to specify the version

### 4. 创建第一个Expo应用 | Creating Your First Expo App (1小时 | 1 hour)

- **项目初始化流程 | Project Initialization Process**

  **概念定义 | Concept Definition:**
  使用`create-expo-app`命令可以快速创建一个包含基础配置和示例代码的Expo项目。这个过程会自动设置项目结构、安装必要依赖，并配置开发环境。 | Using the `create-expo-app` command quickly creates an Expo project with basic configuration and sample code. This process automatically sets up project structure, installs necessary dependencies, and configures the development environment.

  **核心特征 | Key Characteristics:**
  - 特征1：`npx create-expo-app@latest`命令会下载最新的Expo模板并初始化项目 | Characteristic 1: `npx create-expo-app@latest` command downloads the latest Expo template and initializes the project
  - 特征2：项目创建后会自动运行`npm install`安装所有依赖 | Characteristic 2: After project creation, `npm install` automatically runs to install all dependencies
  - 特征3：可以选择不同的模板（blank, tabs, blank-typescript等）| Characteristic 3: Can choose different templates (blank, tabs, blank-typescript, etc.)
  - 特征4：创建的项目立即可运行，无需额外配置 | Characteristic 4: Created project is immediately runnable without additional configuration

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `npx`和`npm`有什么区别？| What's the difference between `npx` and `npm`?
     **答案 | Answer:** `npm`用于安装和管理包，`npx`用于执行包中的命令而不需要全局安装该包。`npx create-expo-app`会临时下载create-expo-app并执行，执行完后自动清理 | `npm` is used to install and manage packages, `npx` is used to execute commands from packages without globally installing them. `npx create-expo-app` temporarily downloads create-expo-app and executes it, then automatically cleans up
  2. 创建Expo项目时是否需要互联网连接？| Do you need an internet connection when creating an Expo project?
     **答案 | Answer:** 是的 | Yes - 需要下载Expo模板和安装npm依赖包，这些都需要网络连接 | Need to download Expo template and install npm dependencies, both requiring network connection
  3. 创建项目后，首先应该运行什么命令？| After creating a project, what command should you run first?
     **答案 | Answer:** `cd <项目名>`进入项目目录，然后运行`npx expo start`启动开发服务器 | `cd <project-name>` to enter project directory, then run `npx expo start` to start development server
  4. 如果想创建TypeScript版本的Expo项目，应该怎么做？| If you want to create a TypeScript version of an Expo project, what should you do?
     **答案 | Answer:** 运行`npx create-expo-app@latest -t expo-template-blank-typescript`，或在创建时选择TypeScript模板 | Run `npx create-expo-app@latest -t expo-template-blank-typescript`, or select TypeScript template during creation

  **完整创建流程 | Complete Creation Process:**
  ```bash
  # 步骤1：创建新的Expo项目 | Step 1: Create new Expo project
  npx create-expo-app@latest MyFirstApp

  # 在创建过程中会看到以下提示：| During creation, you'll see these prompts:
  # ✔ What would you like to name your app? › MyFirstApp
  # ✔ Choose a template: › blank (TypeScript)

  # 步骤2：进入项目目录 | Step 2: Navigate to project directory
  cd MyFirstApp

  # 步骤3：查看项目结构 | Step 3: View project structure
  ls -la
  # 你会看到：| You'll see:
  # - App.js (或App.tsx)  # 主应用文件 | Main app file
  # - app.json            # Expo配置文件 | Expo configuration file
  # - package.json        # 项目依赖配置 | Project dependency configuration
  # - node_modules/       # 已安装的依赖 | Installed dependencies
  # - assets/            # 静态资源（图片、图标等）| Static assets (images, icons, etc.)

  # 步骤4：启动开发服务器 | Step 4: Start development server
  npx expo start

  # 启动后会看到：| After starting, you'll see:
  # › Metro waiting on exp://192.168.1.100:8081
  # › Scan the QR code above with Expo Go (Android) or the Camera app (iOS)
  #
  # › Press a │ open Android
  # › Press i │ open iOS simulator
  # › Press w │ open web
  ```

  **App.js文件详解 | App.js File Explanation:**
  ```javascript
  // App.js - Expo项目的主入口文件 | App.js - Main entry file of Expo project
  import { StatusBar } from 'expo-status-bar';  // Expo提供的状态栏组件 | Status bar component provided by Expo
  import { StyleSheet, Text, View } from 'react-native';  // React Native核心组件 | React Native core components

  export default function App() {
    // 这是标准的React函数组件 | This is a standard React function component
    // 返回的JSX将被渲染到移动设备上 | The returned JSX will be rendered on mobile device
    return (
      <View style={styles.container}>  {/* View相当于web中的div | View is equivalent to div in web */}
        <Text>Open up App.js to start working on your app!</Text>  {/* 所有文本必须包裹在Text组件中 | All text must be wrapped in Text component */}
        <StatusBar style="auto" />  {/* 控制设备状态栏样式 | Controls device status bar style */}
      </View>
    );
  }

  // StyleSheet.create创建样式对象（类似CSS-in-JS）| StyleSheet.create creates style objects (similar to CSS-in-JS)
  const styles = StyleSheet.create({
    container: {
      flex: 1,  // flex布局，占满整个屏幕 | Flex layout, fills entire screen
      backgroundColor: '#fff',  // 背景颜色 | Background color
      alignItems: 'center',  // 水平居中（在列方向）| Horizontally center (in column direction)
      justifyContent: 'center',  // 垂直居中 | Vertically center
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 修改App.js中的`<Text>`内容后需要刷新页面吗？| Do you need to refresh the page after modifying the `<Text>` content in App.js?
    **答案 | Answer:** 不需要 | No - Expo默认启用了热重载（Hot Reload），保存文件后更改会自动反映在应用中 | Expo has Hot Reload enabled by default - changes automatically reflect in the app after saving the file
  - 如果删除`StyleSheet`中的`flex: 1`会发生什么？| What happens if you remove `flex: 1` from the `StyleSheet`?
    **答案 | Answer:** View将不再占满整个屏幕，而是根据其子元素的大小来决定自身大小，居中效果可能会失效 | View will no longer fill the entire screen but will size itself based on its children, and centering effect may fail
  - 在React Native中可以使用`<div>`标签吗？| Can you use `<div>` tags in React Native?
    **答案 | Answer:** 不能 | No - React Native不使用HTML标签，必须使用React Native组件如`<View>`、`<Text>`等 | React Native doesn't use HTML tags - must use React Native components like `<View>`, `<Text>`, etc.

  **常见误区检查 | Common Misconception Checks:**
  - App.js是否等同于React Web中的index.js？| Is App.js equivalent to index.js in React Web?
    **答案 | Answer:** 功能上类似但不完全相同 | Functionally similar but not exactly the same - 在Expo项目中，实际入口是`node_modules/expo/AppEntry.js`，它会加载你的App.js。在React Web中，index.js是真正的入口文件 | In Expo projects, the actual entry is `node_modules/expo/AppEntry.js` which loads your App.js. In React Web, index.js is the true entry file

### 5. 在模拟器/模拟器上运行应用 | Running the App on Simulators/Emulators (45分钟 | 45 minutes)

- **iOS模拟器使用（仅Mac）| iOS Simulator Usage (Mac Only)**

  **概念定义 | Concept Definition:**
  iOS模拟器是Xcode提供的工具,可以在Mac上模拟iPhone和iPad设备。它能够准确模拟iOS设备的行为和性能,是iOS开发的重要测试工具。 | iOS Simulator is a tool provided by Xcode that can simulate iPhone and iPad devices on Mac. It can accurately simulate iOS device behavior and performance, making it an important testing tool for iOS development.

  **核心特征 | Key Characteristics:**
  - 特征1：仅在macOS上可用，需要安装Xcode（从App Store免费下载）| Characteristic 1: Only available on macOS, requires Xcode installation (free download from App Store)
  - 特征2：可以模拟不同的iPhone和iPad型号及iOS版本 | Characteristic 2: Can simulate different iPhone and iPad models and iOS versions
  - 特征3：支持热重载，修改代码后自动刷新 | Characteristic 3: Supports hot reload - automatically refreshes after code modifications
  - 特征4：不需要Apple Developer账号即可使用模拟器 | Characteristic 4: Doesn't require Apple Developer account to use simulator

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Windows或Linux用户可以使用iOS模拟器吗？| Can Windows or Linux users use iOS Simulator?
     **答案 | Answer:** 不能 | No - iOS模拟器仅在macOS上可用。Windows/Linux用户可以使用Expo Go在真实iOS设备上测试，或使用云端Mac服务 | iOS Simulator is only available on macOS. Windows/Linux users can test on real iOS devices using Expo Go or use cloud Mac services
  2. 在模拟器上运行应用需要互联网连接吗？| Do you need an internet connection to run the app on simulator?
     **答案 | Answer:** 不一定 | Not necessarily - 启动模拟器和运行本地应用不需要网络，但如果应用需要获取网络数据或使用某些Expo服务则需要 | Starting simulator and running local apps doesn't require network, but if the app needs to fetch network data or use certain Expo services, then yes
  3. iOS模拟器能完全替代真实设备测试吗？| Can iOS Simulator completely replace testing on real devices?
     **答案 | Answer:** 不能 | No - 模拟器无法测试某些硬件功能（如Touch ID、相机、蓝牙的完整功能），性能表现也与真实设备不同。真实设备测试仍然必要 | Simulator cannot test certain hardware features (like Touch ID, camera, full Bluetooth functionality), and performance differs from real devices. Real device testing is still necessary

  **iOS模拟器使用步骤 | iOS Simulator Usage Steps:**
  ```bash
  # 前置条件：确保已安装Xcode | Prerequisites: Ensure Xcode is installed
  # 从App Store安装Xcode（约12GB，需要较长下载时间）
  # Install Xcode from App Store (about 12GB, requires significant download time)

  # 安装Xcode命令行工具 | Install Xcode Command Line Tools
  xcode-select --install

  # 验证安装 | Verify installation
  xcode-select -p  # 应显示Xcode路径 | Should show Xcode path

  # 方法1：通过Expo CLI启动iOS模拟器 | Method 1: Launch iOS Simulator via Expo CLI
  cd MyFirstApp
  npx expo start
  # 然后按 'i' 键 | Then press 'i' key
  # Expo会自动打开iOS模拟器并安装应用 | Expo will automatically open iOS Simulator and install the app

  # 方法2：手动打开模拟器 | Method 2: Manually open simulator
  open -a Simulator
  # 然后在Expo开发服务器按 'i' | Then press 'i' in Expo dev server

  # 选择不同的设备型号 | Choose different device models
  # 在模拟器中：菜单栏 → File → Open Simulator → iOS 17.0 → iPhone 15 Pro
  # In Simulator: Menu Bar → File → Open Simulator → iOS 17.0 → iPhone 15 Pro

  # 常用模拟器快捷键 | Common simulator shortcuts
  # Cmd + D: 打开React Native开发菜单 | Open React Native dev menu
  # Cmd + R: 重新加载应用 | Reload app
  # Cmd + Shift + H: 返回主屏幕 | Return to home screen
  # Cmd + Shift + H + H: 打开多任务视图 | Open multitasking view
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 按下'i'键后如果没有反应，可能是什么原因？| If pressing 'i' key has no response, what could be the reason?
    **答案 | Answer:** 可能的原因：1) Xcode未正确安装 2) 命令行工具未安装 3) 首次启动需要较长时间，请耐心等待 4) 查看终端是否有错误信息 | Possible reasons: 1) Xcode not properly installed 2) Command line tools not installed 3) First launch requires significant time, please wait patiently 4) Check terminal for error messages
  - 如何在模拟器中打开开发菜单（Dev Menu）？| How do you open the dev menu in simulator?
    **答案 | Answer:** 按Cmd + D或在Expo开发服务器中按'm'键 | Press Cmd + D or press 'm' key in Expo dev server

- **Android模拟器使用 | Android Emulator Usage**

  **概念定义 | Concept Definition:**
  Android模拟器是Android Studio提供的虚拟设备,可以在任何操作系统(Windows/Mac/Linux)上模拟Android设备。它使用虚拟化技术来运行完整的Android系统。 | Android Emulator is a virtual device provided by Android Studio that can simulate Android devices on any operating system (Windows/Mac/Linux). It uses virtualization technology to run a complete Android system.

  **核心特征 | Key Characteristics:**
  - 特征1：支持Windows、Mac和Linux，比iOS模拟器更通用 | Characteristic 1: Supports Windows, Mac, and Linux - more universal than iOS Simulator
  - 特征2：需要安装Android Studio和配置Android SDK | Characteristic 2: Requires Android Studio installation and Android SDK configuration
  - 特征3：可以模拟不同的Android设备、屏幕尺寸和Android版本 | Characteristic 3: Can simulate different Android devices, screen sizes, and Android versions
  - 特征4：首次启动较慢，但后续启动速度会改善 | Characteristic 4: First launch is slow, but subsequent launches improve

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Android模拟器是否需要虚拟化技术支持？| Does Android Emulator require virtualization technology support?
     **答案 | Answer:** 是的 | Yes - 需要启用CPU虚拟化（Intel VT-x或AMD-V）以获得最佳性能。在BIOS中启用后，模拟器速度会显著提升 | Requires CPU virtualization (Intel VT-x or AMD-V) enabled for best performance. After enabling in BIOS, emulator speed improves significantly
  2. 可以同时运行多个Android模拟器实例吗？| Can you run multiple Android emulator instances simultaneously?
     **答案 | Answer:** 可以 | Yes - 但会消耗大量系统资源（RAM和CPU），建议根据计算机性能决定 | But it consumes significant system resources (RAM and CPU) - recommend deciding based on computer performance
  3. Android模拟器能访问电脑的文件系统吗？| Can Android Emulator access the computer's file system?
     **答案 | Answer:** 有限制地可以 | Yes, with limitations - 可以通过拖放文件到模拟器来传输文件，但不能直接访问宿主机的完整文件系统 | Can transfer files by dragging and dropping to emulator, but cannot directly access complete host machine file system

  **Android模拟器设置步骤 | Android Emulator Setup Steps:**
  ```bash
  # 步骤1：安装Android Studio | Step 1: Install Android Studio
  # 下载地址：https://developer.android.com/studio
  # Download: https://developer.android.com/studio

  # 步骤2：安装后打开Android Studio，进行初始配置 | Step 2: Open Android Studio after installation, perform initial configuration
  # Tools → SDK Manager → 确保安装了以下组件：| Tools → SDK Manager → Ensure these components are installed:
  # - Android SDK Platform (最新版本，如Android 14.0) | Android SDK Platform (latest version, like Android 14.0)
  # - Android SDK Platform-Tools
  # - Android SDK Build-Tools
  # - Android Emulator
  # - Intel x86 Emulator Accelerator (HAXM) - 仅Intel CPU | Intel x86 Emulator Accelerator (HAXM) - Intel CPU only

  # 步骤3：配置环境变量（重要！）| Step 3: Configure environment variables (Important!)
  # Mac/Linux: 编辑 ~/.zshrc 或 ~/.bash_profile | Mac/Linux: Edit ~/.zshrc or ~/.bash_profile
  export ANDROID_HOME=$HOME/Library/Android/sdk  # Mac路径 | Mac path
  # export ANDROID_HOME=$HOME/Android/Sdk  # Linux路径 | Linux path
  # export ANDROID_HOME=C:\Users\YourName\AppData\Local\Android\Sdk  # Windows路径 | Windows path

  export PATH=$PATH:$ANDROID_HOME/emulator
  export PATH=$PATH:$ANDROID_HOME/platform-tools
  export PATH=$PATH:$ANDROID_HOME/tools

  # 使配置生效 | Apply configuration
  source ~/.zshrc  # 或 source ~/.bash_profile | or source ~/.bash_profile

  # 验证环境变量 | Verify environment variables
  echo $ANDROID_HOME  # 应显示Android SDK路径 | Should show Android SDK path
  adb --version       # 应显示adb版本信息 | Should show adb version info

  # 步骤4：创建虚拟设备（AVD）| Step 4: Create Virtual Device (AVD)
  # 在Android Studio中：| In Android Studio:
  # Tools → Device Manager → Create Device
  # 选择设备定义（如Pixel 6）→ Next | Select device definition (like Pixel 6) → Next
  # 选择系统镜像（推荐最新的API 34 - Android 14）→ Next | Select system image (recommend latest API 34 - Android 14) → Next
  # 完成配置 → Finish | Complete configuration → Finish

  # 步骤5：通过Expo启动Android模拟器 | Step 5: Launch Android Emulator via Expo
  cd MyFirstApp
  npx expo start
  # 按 'a' 键 | Press 'a' key
  # Expo会自动启动Android模拟器并安装应用 | Expo will automatically launch Android Emulator and install app

  # 或手动启动模拟器 | Or manually launch emulator
  emulator -list-avds  # 列出所有可用的虚拟设备 | List all available virtual devices
  emulator -avd Pixel_6_API_34  # 启动名为Pixel_6_API_34的模拟器 | Launch emulator named Pixel_6_API_34

  # Android模拟器快捷键 | Android Emulator shortcuts
  # Cmd + M (Mac) 或 Ctrl + M (Windows/Linux): 打开React Native开发菜单 | Open React Native dev menu
  # Cmd + R 或 R + R: 重新加载应用 | Reload app
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行`adb --version`显示"command not found"，应该怎么办？| If running `adb --version` shows "command not found", what should you do?
    **答案 | Answer:** 说明Android SDK的环境变量没有正确配置。需要在shell配置文件（~/.zshrc或~/.bash_profile）中添加ANDROID_HOME和PATH，然后重启终端或运行source命令 | Indicates Android SDK environment variables are not correctly configured. Need to add ANDROID_HOME and PATH to shell configuration file (~/.zshrc or ~/.bash_profile), then restart terminal or run source command
  - 按'a'键后显示"No Android connected device found"，怎么解决？| If pressing 'a' key shows "No Android connected device found", how to solve?
    **答案 | Answer:** 需要先手动启动Android模拟器（通过Android Studio的Device Manager或命令行），确保模拟器完全启动后再按'a'键 | Need to manually launch Android Emulator first (via Android Studio Device Manager or command line), ensure emulator is fully started before pressing 'a' key

  **常见误区检查 | Common Misconception Checks:**
  - Android模拟器速度慢是正常的吗？| Is it normal for Android Emulator to be slow?
    **答案 | Answer:** 首次启动慢是正常的，但如果持续很慢，检查是否启用了硬件加速（HAXM或Hypervisor），是否分配了足够的RAM（建议至少2GB），以及是否使用了x86镜像而非ARM镜像 | Slow first launch is normal, but if consistently slow, check if hardware acceleration is enabled (HAXM or Hypervisor), if sufficient RAM is allocated (recommend at least 2GB), and if using x86 images rather than ARM images

### 6. 探索项目结构 | Exploring Project Structure (30分钟 | 30 minutes)

- **Expo项目结构对比React Web项目 | Expo Project Structure vs React Web Project**

  **概念定义 | Concept Definition:**
  理解Expo项目结构与熟悉的React Web项目的差异,是掌握React Native开发的关键第一步。虽然组件概念相同,但配置文件、资源管理和构建流程有显著不同。 | Understanding the differences between Expo project structure and familiar React Web projects is a key first step in mastering React Native development. While component concepts are the same, configuration files, asset management, and build processes differ significantly.

  **核心文件对比 | Core File Comparison:**

  | 文件/目录 File/Directory | Expo项目 Expo Project | React Web项目 React Web Project |
  |---|---|---|
  | 入口文件 Entry | `App.js` (被AppEntry.js加载) | `index.js` (直接渲染到DOM) |
  | 配置文件 Config | `app.json` (Expo配置) | `package.json` + `webpack.config.js` / `vite.config.js` |
  | 静态资源 Assets | `assets/` (图片、字体等) | `public/` 或 `src/assets/` |
  | 原生代码 Native Code | 无（托管模式）或 `ios/` + `android/` | 不适用 N/A |
  | 样式文件 Styles | 内联或StyleSheet（无CSS文件）| `.css` / `.scss` / `.module.css` |
  | HTML模板 HTML Template | 无（原生渲染）N/A (native rendering) | `public/index.html` |
  | 打包工具 Bundler | Metro (React Native专用) | Webpack / Vite / Parcel |

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Expo项目中有`index.html`文件吗？| Is there an `index.html` file in Expo projects?
     **答案 | Answer:** 没有 | No - React Native应用不运行在浏览器中，不需要HTML文件。UI直接由原生组件渲染 | React Native apps don't run in browsers and don't need HTML files. UI is rendered directly by native components
  2. 在Expo项目中可以导入`.css`文件吗？| Can you import `.css` files in Expo projects?
     **答案 | Answer:** 不能 | No - React Native不支持CSS文件。必须使用StyleSheet API或内联样式对象来定义样式 | React Native doesn't support CSS files. Must use StyleSheet API or inline style objects to define styles
  3. `app.json`文件的主要作用是什么？| What is the main purpose of the `app.json` file?
     **答案 | Answer:** 配置Expo应用的元数据，包括应用名称、图标、启动画面、权限、SDK版本等。它相当于iOS的Info.plist和Android的AndroidManifest.xml的高级抽象 | Configures Expo app metadata including app name, icon, splash screen, permissions, SDK version, etc. It's a high-level abstraction equivalent to iOS Info.plist and Android AndroidManifest.xml
  4. Metro和Webpack有什么不同？| What's different between Metro and Webpack?
     **答案 | Answer:** Metro是React Native专用的打包工具，针对移动端优化，支持快速刷新和增量构建。Webpack是通用的Web打包工具。Metro不支持某些Webpack插件,但对React Native的支持更原生 | Metro is React Native's dedicated bundler, optimized for mobile with fast refresh and incremental builds. Webpack is a general-purpose web bundler. Metro doesn't support some Webpack plugins but has more native React Native support

  **详细项目结构说明 | Detailed Project Structure Explanation:**
  ```
  MyFirstApp/
  ├── .expo/                    # Expo生成的缓存和配置（自动生成，不需提交到Git）
  │                            # Expo-generated cache and config (auto-generated, don't commit to Git)
  ├── assets/                   # 静态资源目录 | Static assets directory
  │   ├── adaptive-icon.png    # Android自适应图标 | Android adaptive icon
  │   ├── favicon.png          # Web版favicon（如果启用了Web支持）| Web favicon (if Web support enabled)
  │   ├── icon.png             # 应用图标（1024x1024）| App icon (1024x1024)
  │   └── splash.png           # 启动画面 | Splash screen
  ├── node_modules/            # npm依赖（自动生成）| npm dependencies (auto-generated)
  ├── App.js                   # 主应用组件（你的代码入口）| Main app component (your code entry)
  ├── app.json                 # Expo应用配置文件 | Expo app configuration file
  ├── babel.config.js          # Babel配置（用于转译JavaScript）| Babel config (for JavaScript transpilation)
  ├── package.json             # 项目依赖和脚本 | Project dependencies and scripts
  └── package-lock.json        # 锁定依赖版本（确保一致性）| Lock dependency versions (ensure consistency)
  ```

  **app.json详细解析 | app.json Detailed Analysis:**
  ```json
  {
    "expo": {
      "name": "MyFirstApp",          // 应用显示名称 | App display name
      "slug": "MyFirstApp",          // URL友好的唯一标识符 | URL-friendly unique identifier
      "version": "1.0.0",            // 应用版本号 | App version number
      "orientation": "portrait",     // 屏幕方向：portrait/landscape/default | Screen orientation
      "icon": "./assets/icon.png",   // 应用图标路径 | App icon path
      "userInterfaceStyle": "light", // 界面风格：light/dark/automatic | Interface style
      "splash": {                    // 启动画面配置 | Splash screen configuration
        "image": "./assets/splash.png",
        "resizeMode": "contain",     // 图片适配模式 | Image fit mode
        "backgroundColor": "#ffffff"
      },
      "assetBundlePatterns": [       // 哪些资源应该打包到应用中 | Which assets to bundle into app
        "**/*"
      ],
      "ios": {                       // iOS特定配置 | iOS-specific configuration
        "supportsTablet": true,      // 是否支持iPad | Whether to support iPad
        "bundleIdentifier": "com.yourcompany.myfirstapp"  // iOS唯一标识符 | iOS unique identifier
      },
      "android": {                   // Android特定配置 | Android-specific configuration
        "adaptiveIcon": {
          "foregroundImage": "./assets/adaptive-icon.png",
          "backgroundColor": "#ffffff"
        },
        "package": "com.yourcompany.myfirstapp"  // Android包名 | Android package name
      },
      "web": {                       // Web特定配置（如果支持）| Web-specific config (if supported)
        "favicon": "./assets/favicon.png"
      }
    }
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果要改变应用的名称（显示在设备上的名称），应该修改哪个文件的哪个字段？| If you want to change the app name (displayed on device), which field in which file should you modify?
    **答案 | Answer:** 修改`app.json`中的`expo.name`字段。修改后需要重新启动开发服务器才能看到效果 | Modify the `expo.name` field in `app.json`. Need to restart development server to see the effect
  - assets文件夹中的图片可以被删除吗？| Can images in the assets folder be deleted?
    **答案 | Answer:** 可以删除不需要的图片，但icon.png和splash.png是必需的（在app.json中被引用）。删除它们会导致构建错误 | Can delete unnecessary images, but icon.png and splash.png are required (referenced in app.json). Deleting them will cause build errors
  - 如何在代码中引用assets文件夹中的图片？| How do you reference images in the assets folder in your code?
    **答案 | Answer:** 使用`require()`：`<Image source={require('./assets/icon.png')} />`。Metro打包器会处理这些本地资源引用 | Use `require()`: `<Image source={require('./assets/icon.png')} />`. Metro bundler will handle these local asset references

## 实践项目：环境验证应用 | Practical Project: Environment Verification App

### 目标 | Objective
创建一个简单的Expo应用来验证你的开发环境设置正确，并展示对React Native基础组件的理解。这个项目将综合应用今天学习的所有概念：项目创建、模拟器运行、项目结构理解和基本组件使用。 | Create a simple Expo app to verify your development environment is set up correctly and demonstrate understanding of React Native basic components. This project will comprehensively apply all concepts learned today: project creation, simulator running, project structure understanding, and basic component usage.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. Expo和React Native CLI的主要区别是什么？| What are the main differences between Expo and React Native CLI?
   **答案 | Answer:** Expo提供托管的开发环境，无需配置原生工具，适合快速开发；React Native CLI需要完整的原生环境，提供更大灵活性和对原生代码的直接访问 | Expo provides managed development environment without native tools configuration, suitable for rapid development; React Native CLI requires complete native environment, offers greater flexibility and direct access to native code
2. 在React Native中，可以使用`<div>`和`<p>`标签吗？| Can you use `<div>` and `<p>` tags in React Native?
   **答案 | Answer:** 不可以，必须使用React Native的组件如`<View>`代替`<div>`，`<Text>`代替`<p>`。React Native不使用HTML | No, must use React Native components like `<View>` instead of `<div>`, `<Text>` instead of `<p>`. React Native doesn't use HTML
3. 如何在iOS模拟器中打开React Native开发菜单？| How do you open React Native dev menu in iOS Simulator?
   **答案 | Answer:** 按Cmd + D，或在Expo开发服务器中按'm'键 | Press Cmd + D, or press 'm' key in Expo dev server

### 步骤 | Steps

1. **创建新项目 | Create New Project**
   ```bash
   npx create-expo-app@latest EnvironmentCheck
   cd EnvironmentCheck
   ```

2. **修改App.js实现环境验证界面 | Modify App.js to implement environment verification UI**
   - 显示应用标题"Environment Check ✅"
   - 显示当前日期和时间
   - 显示设备平台信息（iOS/Android/Web）
   - 显示一个测试按钮，点击时显示警告框
   - 使用不同的样式展示你对StyleSheet的理解

3. **在iOS和Android模拟器上测试 | Test on iOS and Android simulators**
   - 验证应用在两个平台上都能正常运行
   - 测试热重载功能（修改文本后自动更新）
   - 测试开发菜单的打开方式

4. **添加自定义图标 | Add custom icon**
   - 创建或下载一个图标图片（1024x1024）
   - 替换assets/icon.png
   - 在app.json中验证图标配置正确

5. **探索项目结构 | Explore project structure**
   - 查看并理解每个配置文件的作用
   - 尝试修改app.json中的应用名称
   - 重启服务器验证修改生效

### 示例代码 | Example Code

```javascript
/**
 * 环境验证应用 | Environment Verification App
 * 项目描述：验证React Native开发环境正确配置，并展示基础组件使用
 * Project description: Verify React Native development environment is correctly configured and demonstrate basic component usage
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Expo项目结构 | Expo project structure
 * - React Native核心组件（View, Text, Button）| React Native core components (View, Text, Button)
 * - StyleSheet样式系统 | StyleSheet styling system
 * - Platform API使用 | Platform API usage
 */

import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View, Button, Alert, Platform } from 'react-native';
import { useState, useEffect } from 'react';

export default function App() {
  // 使用useState管理当前时间 | Use useState to manage current time
  // 这验证了React hooks在React Native中的使用方式与Web相同
  // This verifies that React hooks work the same way in React Native as in Web
  const [currentTime, setCurrentTime] = useState(new Date().toLocaleString());

  // useEffect实现时间更新 | useEffect implements time updates
  // 每秒更新一次当前时间 | Update current time every second
  useEffect(() => {
    const timer = setInterval(() => {
      setCurrentTime(new Date().toLocaleString());
    }, 1000);

    // 清理定时器 | Cleanup timer
    return () => clearInterval(timer);
  }, []);

  // 按钮点击处理函数 | Button click handler
  // Alert是React Native提供的跨平台弹窗API | Alert is React Native's cross-platform dialog API
  const handlePress = () => {
    Alert.alert(
      '环境测试成功！✅',  // 标题 | Title
      '你的React Native开发环境已正确配置！\n\nYour React Native development environment is correctly set up!',  // 消息 | Message
      [
        {
          text: '太好了！ | Great!',
          onPress: () => console.log('Alert confirmed')  // 控制台日志 | Console log
        }
      ]
    );
  };

  // 获取平台信息 | Get platform information
  // Platform.OS返回'ios'、'android'或'web' | Platform.OS returns 'ios', 'android', or 'web'
  const platformName = Platform.OS;
  const platformVersion = Platform.Version;

  return (
    <View style={styles.container}>
      {/* 应用标题 | App title */}
      <Text style={styles.title}>Environment Check ✅</Text>

      {/* 欢迎信息 | Welcome message */}
      <Text style={styles.subtitle}>
        欢迎来到React Native世界！{'\n'}
        Welcome to React Native!
      </Text>

      {/* 信息卡片区域 | Info card area */}
      <View style={styles.infoCard}>
        {/* 平台信息 | Platform info */}
        <View style={styles.infoRow}>
          <Text style={styles.label}>平台 Platform:</Text>
          <Text style={styles.value}>{platformName.toUpperCase()}</Text>
        </View>

        {/* 平台版本 | Platform version */}
        <View style={styles.infoRow}>
          <Text style={styles.label}>版本 Version:</Text>
          <Text style={styles.value}>{platformVersion}</Text>
        </View>

        {/* 当前时间 | Current time */}
        <View style={styles.infoRow}>
          <Text style={styles.label}>时间 Time:</Text>
          <Text style={styles.value}>{currentTime}</Text>
        </View>
      </View>

      {/* 测试按钮 | Test button */}
      <View style={styles.buttonContainer}>
        <Button
          title="测试环境 | Test Environment"
          onPress={handlePress}
          color="#007AFF"  // iOS风格的蓝色 | iOS-style blue
        />
      </View>

      {/* 说明文本 | Description text */}
      <Text style={styles.description}>
        💡 提示：按 Cmd+D (iOS) 或 Cmd+M (Android) 打开开发菜单{'\n'}
        💡 Tip: Press Cmd+D (iOS) or Cmd+M (Android) to open dev menu
      </Text>

      {/* 状态栏配置 | Status bar configuration */}
      <StatusBar style="auto" />
    </View>
  );
}

// StyleSheet创建样式对象 | StyleSheet creates style objects
// 这类似于CSS-in-JS，但使用的是React Native的样式属性
// This is similar to CSS-in-JS but uses React Native style properties
const styles = StyleSheet.create({
  container: {
    flex: 1,  // 占满整个屏幕 | Fill entire screen
    backgroundColor: '#F5F5F7',  // 浅灰色背景 | Light gray background
    alignItems: 'center',  // 水平居中 | Horizontally center
    justifyContent: 'center',  // 垂直居中 | Vertically center
    padding: 20,  // 内边距 | Padding
  },
  title: {
    fontSize: 32,  // 字体大小 | Font size
    fontWeight: 'bold',  // 粗体 | Bold
    color: '#1D1D1F',  // 深灰色文字 | Dark gray text
    marginBottom: 10,  // 下边距 | Bottom margin
  },
  subtitle: {
    fontSize: 18,
    color: '#6E6E73',  // 中灰色 | Medium gray
    textAlign: 'center',  // 文本居中 | Text centered
    marginBottom: 30,
    lineHeight: 24,  // 行高 | Line height
  },
  infoCard: {
    backgroundColor: '#FFFFFF',  // 白色卡片背景 | White card background
    borderRadius: 12,  // 圆角 | Border radius
    padding: 20,
    width: '100%',
    maxWidth: 400,  // 最大宽度 | Max width
    marginBottom: 30,
    // iOS和Android平台特定的阴影效果 | iOS and Android platform-specific shadow effects
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.1,
        shadowRadius: 8,
      },
      android: {
        elevation: 4,  // Android使用elevation实现阴影 | Android uses elevation for shadows
      },
    }),
  },
  infoRow: {
    flexDirection: 'row',  // 横向布局 | Horizontal layout
    justifyContent: 'space-between',  // 两端对齐 | Space between
    paddingVertical: 10,  // 垂直内边距 | Vertical padding
    borderBottomWidth: 1,  // 底部边框宽度 | Bottom border width
    borderBottomColor: '#E5E5EA',  // 边框颜色 | Border color
  },
  label: {
    fontSize: 16,
    color: '#6E6E73',
    fontWeight: '600',  // 半粗体 | Semi-bold
  },
  value: {
    fontSize: 16,
    color: '#1D1D1F',
    fontWeight: '400',  // 正常字重 | Normal weight
  },
  buttonContainer: {
    width: '100%',
    maxWidth: 400,
    marginBottom: 20,
  },
  description: {
    fontSize: 14,
    color: '#86868B',  // 浅灰色文字 | Light gray text
    textAlign: 'center',
    lineHeight: 20,
    paddingHorizontal: 20,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 应用是否在iOS模拟器上成功运行？| Does the app run successfully on iOS Simulator?
2. 应用是否在Android模拟器上成功运行？| Does the app run successfully on Android Emulator?
3. 点击按钮是否显示Alert弹窗？| Does clicking the button show an Alert dialog?
4. 时间是否每秒自动更新？| Does the time update automatically every second?
5. 修改代码后热重载是否正常工作？| Does hot reload work normally after modifying code?
6. 是否理解了View、Text、Button等组件的作用？| Do you understand the roles of View, Text, Button components?
7. 是否理解了StyleSheet与传统CSS的区别？| Do you understand the differences between StyleSheet and traditional CSS?
8. 是否能够打开并使用开发菜单（Dev Menu）？| Can you open and use the dev menu?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **环境对比分析练习 | Environment Comparison Analysis Exercise**
   - **练习描述 | Exercise Description:** 创建一个对比表格文档，详细列出Expo和React Native CLI在5个方面的差异：学习曲线、开发速度、功能限制、项目大小、部署流程
   - **概念检查 | Concept Check:** 你能说出至少3个场景，在这些场景下Expo比React Native CLI更合适吗？以及3个相反的场景？| Can you name at least 3 scenarios where Expo is more suitable than React Native CLI? And 3 opposite scenarios?
   - **学习目标 | Learning Objective:** 加深对两种开发方式的理解，培养根据项目需求做出技术选型的能力 | Deepen understanding of both development approaches and cultivate ability to make technical choices based on project requirements

2. **Node.js版本管理实践 | Node.js Version Management Practice**
   - **练习描述 | Exercise Description:** 安装nvm，使用它安装两个不同版本的Node.js（如16.x和18.x），练习在两个版本间切换，并创建一个`.nvmrc`文件来自动管理项目的Node版本
   - **概念检查 | Concept Check:** `.nvmrc`文件的内容是什么？它如何帮助团队协作？| What is the content of `.nvmrc` file? How does it help team collaboration?
   - **学习目标 | Learning Objective:** 掌握专业的Node.js版本管理技能，为未来多项目开发做准备 | Master professional Node.js version management skills and prepare for future multi-project development

3. **项目结构探索练习 | Project Structure Exploration Exercise**
   - **练习描述 | Exercise Description:** 分别创建一个Expo项目和一个Create React App（CRA）项目，对比两者的文件结构，创建一个Markdown文档说明每个主要文件/文件夹的作用和差异
   - **概念检查 | Concept Check:** 为什么React Native项目不需要`index.html`文件？| Why don't React Native projects need an `index.html` file?
   - **学习目标 | Learning Objective:** 通过对比加深对Web和Mobile项目结构差异的理解 | Deepen understanding of Web and Mobile project structure differences through comparison

4. **app.json配置实验 | app.json Configuration Experiment**
   - **练习描述 | Exercise Description:** 修改app.json中的各项配置（name, orientation, splash screen, icon），观察每项修改对应用的影响。尝试将orientation改为"landscape"，backgroundColor改为不同颜色
   - **概念检查 | Concept Check:** 修改app.json后需要重启开发服务器吗？| Do you need to restart the dev server after modifying app.json?
   - **学习目标 | Learning Objective:** 理解Expo配置文件的作用和影响范围 | Understand the role and scope of impact of Expo configuration files

5. **跨平台组件测试 | Cross-Platform Component Testing**
   - **练习描述 | Exercise Description:** 扩展环境验证应用，添加`Platform.select()`来为iOS和Android显示不同的样式或内容。例如：iOS显示SF Symbols风格图标，Android显示Material Design图标
   - **概念检查 | Concept Check:** `Platform.OS`和`Platform.select()`有什么区别？| What's the difference between `Platform.OS` and `Platform.select()`?
   - **学习目标 | Learning Objective:** 学习如何处理平台差异，编写真正的跨平台代码 | Learn how to handle platform differences and write truly cross-platform code

6. **模拟器快捷键熟悉练习 | Simulator Shortcuts Familiarization Exercise**
   - **练习描述 | Exercise Description:** 创建一个"快捷键备忘单"文档，列出iOS模拟器和Android模拟器的所有常用快捷键。在实际操作中练习每一个快捷键，直到形成肌肉记忆
   - **概念检查 | Concept Check:** 如何在不关闭应用的情况下重新加载React Native应用？| How do you reload a React Native app without closing the app?
   - **学习目标 | Learning Objective:** 提高开发效率，熟练使用开发工具 | Improve development efficiency and become proficient with development tools

7. **真实设备测试 | Real Device Testing**
   - **练习描述 | Exercise Description:** 在你的真实iPhone或Android设备上安装Expo Go应用，扫描二维码在真实设备上运行你的环境验证应用。对比模拟器和真实设备的性能和体验差异
   - **概念检查 | Concept Check:** 真实设备测试为什么重要？它能发现哪些模拟器无法发现的问题？| Why is real device testing important? What issues can it discover that simulators cannot?
   - **学习目标 | Learning Objective:** 理解真实设备测试的重要性，体验真实的移动开发工作流 | Understand the importance of real device testing and experience real mobile development workflow

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [React Native官方文档 - 环境设置](https://reactnative.dev/docs/environment-setup) | [React Native Official Docs - Environment Setup](https://reactnative.dev/docs/environment-setup)
- [Expo官方文档 - 快速开始](https://docs.expo.dev/get-started/introduction/) | [Expo Official Docs - Get Started](https://docs.expo.dev/get-started/introduction/)
- [Node.js官方网站](https://nodejs.org/) | [Node.js Official Website](https://nodejs.org/)
- [Metro打包工具文档](https://facebook.github.io/metro/) | [Metro Bundler Documentation](https://facebook.github.io/metro/)

### 视频教程 | Video Tutorials
- [Expo vs React Native CLI 2024对比](https://www.youtube.com/results?search_query=expo+vs+react+native+cli+2024) | [Expo vs React Native CLI 2024 Comparison](https://www.youtube.com/results?search_query=expo+vs+react+native+cli+2024)
- [React Native环境设置完整指南](https://www.youtube.com/results?search_query=react+native+environment+setup+2024) | [React Native Environment Setup Complete Guide](https://www.youtube.com/results?search_query=react+native+environment+setup+2024)

### 工具和资源 | Tools and Resources
- [nvm (Node Version Manager)](https://github.com/nvm-sh/nvm) - Node.js版本管理工具 | Node.js version management tool
- [Expo Go应用](https://expo.dev/client) - 真实设备测试工具 | Real device testing tool
- [React Native Express](https://www.reactnative.express/) - 可视化学习指南 | Visual learning guide
- [Expo Snack](https://snack.expo.dev/) - 在线React Native编辑器 | Online React Native editor

### 社区和支持 | Community and Support
- [Expo论坛](https://forums.expo.dev/) - Expo社区讨论 | Expo community discussions
- [React Native Discord](https://discord.gg/react-native) - 实时聊天支持 | Real-time chat support
- [Stack Overflow - React Native标签](https://stackoverflow.com/questions/tagged/react-native) | [Stack Overflow - React Native Tag](https://stackoverflow.com/questions/tagged/react-native)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Expo和React Native CLI的核心区别和适用场景 | Understand core differences and suitable scenarios for Expo and React Native CLI
- [ ] 成功安装Node.js并验证版本（18.x或更高）| Successfully installed Node.js and verified version (18.x or higher)
- [ ] 能够使用Expo CLI创建新项目 | Able to create new projects using Expo CLI
- [ ] iOS模拟器配置完成并能运行应用（Mac用户）| iOS Simulator configured and can run apps (Mac users)
- [ ] Android模拟器配置完成并能运行应用 | Android Emulator configured and can run apps
- [ ] 理解Expo项目结构与React Web项目的主要差异 | Understand main differences between Expo project structure and React Web projects
- [ ] 能够使用热重载功能进行快速开发 | Able to use hot reload for rapid development
- [ ] 熟悉开发菜单的打开方式和基本功能 | Familiar with how to open dev menu and its basic functions
- [ ] 完成环境验证应用项目并在模拟器上测试成功 | Completed Environment Verification App project and tested successfully on simulators
- [ ] 能够修改app.json配置文件并理解其作用 | Able to modify app.json configuration file and understand its purpose
- [ ] 理解React Native不使用HTML和CSS文件的原因 | Understand why React Native doesn't use HTML and CSS files
- [ ] 掌握Platform API的基本使用方法 | Master basic usage of Platform API

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释以下核心概念：
1. Expo托管工作流的优势和限制
2. React Native项目结构与Web项目的本质区别
3. 为什么移动开发需要模拟器而不仅仅是浏览器
4. Metro打包工具在React Native生态中的作用

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain the following core concepts to others:
1. Advantages and limitations of Expo managed workflow
2. Essential differences between React Native project structure and Web projects
3. Why mobile development needs simulators and not just browsers
4. The role of Metro bundler in React Native ecosystem

---

**下一步 | Next Steps:**
恭喜完成第1天的学习！明天我们将深入学习开发工具和模拟器的高级使用，包括调试技巧、性能监控和开发者工具的全面应用。请确保今天的环境配置完全正常后再继续。 | Congratulations on completing Day 1! Tomorrow we'll dive deep into advanced usage of development tools and simulators, including debugging techniques, performance monitoring, and comprehensive application of developer tools. Please ensure today's environment configuration is fully working before continuing.
