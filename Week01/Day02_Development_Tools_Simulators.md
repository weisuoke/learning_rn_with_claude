# React Native入门 - 第2天：开发工具与模拟器 | React Native Introduction - Day 2: Development Tools & Simulators

## 学习目标 | Learning Objectives
- 理解React Native开发环境的核心组成部分 | Understand the core components of React Native development environment
- 掌握iOS模拟器和Android模拟器的安装与配置 | Master installation and configuration of iOS Simulator and Android Emulator
- 学会使用React Native调试工具进行问题排查 | Learn to use React Native debugging tools for troubleshooting
- 熟练使用热重载和开发者菜单提高开发效率 | Proficiently use hot reloading and dev menu to improve development efficiency
- 了解不同平台开发工具的差异与最佳实践 | Understand differences and best practices across platform development tools
- 建立高效的移动端开发工作流 | Establish an efficient mobile development workflow

## 详细内容 | Detailed Content

### 1. React Native开发环境核心概念 | Core Concepts of React Native Development Environment (1小时 | 1 hour)

- **开发环境架构 | Development Environment Architecture**

  **概念定义 | Concept Definition:**
  React Native开发环境是一个多层次的系统，包括JavaScript运行时、原生平台工具链、调试工具和构建系统。它连接了JavaScript代码和原生移动应用之间的桥梁。 | The React Native development environment is a multi-layered system including JavaScript runtime, native platform toolchains, debugging tools, and build systems. It bridges JavaScript code and native mobile applications.

  **核心特征 | Key Characteristics:**
  - Metro打包器负责JavaScript代码的打包和热更新 | Metro bundler handles JavaScript bundling and hot updates
  - 需要原生平台SDK（Xcode for iOS, Android Studio for Android）来编译和运行原生代码 | Requires native platform SDKs (Xcode for iOS, Android Studio for Android) to compile and run native code
  - 支持多种调试方式：Chrome DevTools、React DevTools、原生调试器 | Supports multiple debugging methods: Chrome DevTools, React DevTools, native debuggers
  - 提供快速迭代工具：热重载（Hot Reload）和快速刷新（Fast Refresh） | Provides rapid iteration tools: Hot Reload and Fast Refresh

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native开发环境只需要安装Node.js就可以运行吗？| Can React Native development environment run with only Node.js installed?
     **答案 | Answer:** 否 | No - 还需要原生平台SDK（iOS需要Xcode，Android需要Android Studio）来编译和运行应用 | You also need native platform SDKs (Xcode for iOS, Android Studio for Android) to compile and run apps
  2. Metro打包器的主要作用是什么？| What is the main purpose of Metro bundler?
     **答案 | Answer:** Metro负责将JavaScript代码打包、转换并提供给应用，同时支持热更新功能 | Metro bundles and transforms JavaScript code for the app and supports hot updates
  3. 为什么React Native需要原生开发工具？| Why does React Native require native development tools?
     **答案 | Answer:** 因为最终运行的是原生应用，需要原生工具来编译原生代码、管理依赖、运行模拟器 | Because the final output is a native app, native tools are needed to compile native code, manage dependencies, and run simulators
  4. 开发者菜单（Dev Menu）可以在生产环境中使用吗？| Can the Dev Menu be used in production environments?
     **答案 | Answer:** 否 | No - 开发者菜单只在开发模式下可用，生产构建会自动移除这些调试功能 | The Dev Menu is only available in development mode; production builds automatically remove debugging features

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // App.js - 基础React Native应用结构 | Basic React Native app structure
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  // 这是一个简单的组件，用于测试开发环境 | A simple component to test the development environment
  export default function App() {
    return (
      <View style={styles.container}>
        <Text style={styles.text}>
          开发环境测试 | Dev Environment Test
        </Text>
        <Text style={styles.info}>
          修改此文本并保存，观察热重载效果 |
          Modify this text and save to see hot reload
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#f5f5f5',
    },
    text: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 10,
    },
    info: {
      fontSize: 14,
      textAlign: 'center',
      padding: 20,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 修改上面代码中的文本内容并保存，会立即看到变化吗？| If you modify the text in the code above and save, will you see changes immediately?
    **答案 | Answer:** 是的，热重载功能会自动更新应用显示 | Yes, the hot reload feature will automatically update the app display
  - 如果添加了语法错误，应用会崩溃还是显示错误信息？| If you add a syntax error, will the app crash or show an error message?
    **答案 | Answer:** 会在应用中显示红屏错误信息（Red Box），不会完全崩溃 | It will show a red box error message in the app, not completely crash

  **常见误区检查 | Common Misconception Checks:**
  - React Native开发环境和Web开发环境完全相同吗？| Is React Native development environment exactly the same as web development?
    **答案 | Answer:** 不同。React Native需要额外的原生工具链，使用Metro而非Webpack，运行在移动设备而非浏览器 | No. React Native requires additional native toolchains, uses Metro instead of Webpack, and runs on mobile devices rather than browsers
  - 热重载能保存应用的状态吗？| Does hot reload preserve app state?
    **答案 | Answer:** 快速刷新（Fast Refresh）会尽量保存状态，但完全重载会丢失状态 | Fast Refresh tries to preserve state, but full reload will lose state

### 2. iOS开发工具 - Xcode与模拟器 | iOS Development Tools - Xcode & Simulator (1小时 | 1 hour)

- **Xcode安装与配置 | Xcode Installation and Configuration**

  **概念定义 | Concept Definition:**
  Xcode是苹果官方的集成开发环境（IDE），包含iOS SDK、模拟器和构建工具。它是在Mac上开发React Native iOS应用的必备工具。 | Xcode is Apple's official Integrated Development Environment (IDE), containing the iOS SDK, simulators, and build tools. It's essential for developing React Native iOS apps on Mac.

  **核心特征 | Key Characteristics:**
  - 只能在macOS系统上安装和运行 | Can only be installed and run on macOS
  - 包含多个iOS版本的模拟器 | Includes simulators for multiple iOS versions
  - 提供iOS SDK和命令行工具（Command Line Tools） | Provides iOS SDK and Command Line Tools
  - 体积庞大（通常超过10GB），安装需要时间 | Large size (usually over 10GB), installation takes time
  - 支持多种设备模拟（iPhone、iPad各种型号） | Supports multiple device simulations (various iPhone and iPad models)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以在Windows系统上安装Xcode开发iOS应用吗？| Can you install Xcode on Windows to develop iOS apps?
     **答案 | Answer:** 否 | No - Xcode只能在macOS上运行，Windows用户需要使用云服务或虚拟机 | Xcode only runs on macOS; Windows users need cloud services or virtual machines
  2. iOS模拟器和真机有什么区别？| What's the difference between iOS Simulator and real devices?
     **答案 | Answer:** 模拟器运行在Mac上，性能更快但缺少某些硬件功能（如相机、GPS精确度），真机测试更准确 | Simulator runs on Mac, faster but lacks some hardware features (like camera, GPS accuracy); real device testing is more accurate
  3. Xcode命令行工具的作用是什么？| What is the purpose of Xcode Command Line Tools?
     **答案 | Answer:** 提供命令行编译工具（如git、make、clang），React Native需要这些工具来构建iOS应用 | Provides command-line compilation tools (like git, make, clang) that React Native needs to build iOS apps
  4. 为什么需要安装多个iOS模拟器版本？| Why do we need to install multiple iOS simulator versions?
     **答案 | Answer:** 为了测试应用在不同iOS版本上的兼容性，确保支持旧版本用户 | To test app compatibility across different iOS versions and ensure support for users on older versions

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # 检查Xcode是否已安装 | Check if Xcode is installed
  xcode-select --print-path
  # 如果输出路径（如 /Applications/Xcode.app/Contents/Developer），表示已安装
  # If it outputs a path (like /Applications/Xcode.app/Contents/Developer), it's installed

  # 安装Xcode命令行工具 | Install Xcode Command Line Tools
  xcode-select --install

  # 列出所有可用的模拟器 | List all available simulators
  xcrun simctl list devices

  # 启动特定的iOS模拟器 | Launch a specific iOS simulator
  xcrun simctl boot "iPhone 15 Pro"

  # 打开模拟器应用 | Open Simulator app
  open -a Simulator
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行`xcode-select --print-path`后没有输出，这说明什么？| What does it mean if `xcode-select --print-path` returns no output?
    **答案 | Answer:** Xcode或命令行工具未正确安装，需要先安装 | Xcode or Command Line Tools are not properly installed, need installation first
  - 如何在模拟器中测试不同的iPhone型号？| How can you test different iPhone models in the simulator?
    **答案 | Answer:** 使用Xcode的Simulator菜单选择不同设备，或使用`xcrun simctl`命令启动特定设备 | Use Xcode's Simulator menu to select different devices, or use `xcrun simctl` command to launch specific devices

  **常见误区检查 | Common Misconception Checks:**
  - iOS模拟器和Android模拟器是同一种技术吗？| Are iOS Simulator and Android Emulator the same technology?
    **答案 | Answer:** 不是。iOS Simulator模拟iOS环境但运行在x86/ARM架构，Android Emulator是完整的虚拟机，模拟Android硬件 | No. iOS Simulator simulates iOS environment but runs on x86/ARM architecture; Android Emulator is a full virtual machine simulating Android hardware
  - 在模拟器上测试成功就意味着在真机上也能正常运行吗？| Does successful testing on simulator mean it will work properly on real devices?
    **答案 | Answer:** 不一定。某些功能（如蓝牙、相机、性能）在真机上表现不同，需要真机测试 | Not necessarily. Some features (like Bluetooth, camera, performance) behave differently on real devices and require device testing

- **iOS模拟器使用技巧 | iOS Simulator Usage Tips**

  **概念定义 | Concept Definition:**
  iOS模拟器提供了一系列快捷操作和设置选项，帮助开发者高效测试应用的各种场景和设备配置。 | iOS Simulator provides a series of shortcuts and settings to help developers efficiently test various scenarios and device configurations.

  **核心特征 | Key Characteristics:**
  - 支持硬件按钮模拟（Home键、锁屏、音量等） | Supports hardware button simulation (Home, Lock, Volume, etc.)
  - 可以模拟不同网络状态和定位信息 | Can simulate different network conditions and location data
  - 支持截图和录屏功能 | Supports screenshot and screen recording
  - 可以调整设备方向（竖屏/横屏） | Can adjust device orientation (portrait/landscape)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如何在iOS模拟器中触发摇晃手势？| How do you trigger shake gesture in iOS Simulator?
     **答案 | Answer:** Device菜单 → Shake，或使用快捷键 Ctrl+Cmd+Z | Device menu → Shake, or use shortcut Ctrl+Cmd+Z
  2. iOS模拟器可以模拟Face ID吗？| Can iOS Simulator simulate Face ID?
     **答案 | Answer:** 可以，通过Features菜单中的Face ID选项模拟成功或失败的认证 | Yes, through Features menu → Face ID to simulate successful or failed authentication
  3. 如何在模拟器中测试深色模式（Dark Mode）？| How do you test Dark Mode in the simulator?
     **答案 | Answer:** Settings → Developer → Dark Appearance，或在模拟器的Features菜单中切换 | Settings → Developer → Dark Appearance, or toggle in Simulator's Features menu

### 3. Android开发工具 - Android Studio与模拟器 | Android Development Tools - Android Studio & Emulator (1小时 | 1 hour)

- **Android Studio安装与配置 | Android Studio Installation and Configuration**

  **概念定义 | Concept Definition:**
  Android Studio是Google官方的Android开发IDE，包含Android SDK、模拟器管理器（AVD Manager）和构建工具。它是开发React Native Android应用的核心工具，支持Windows、Mac和Linux系统。 | Android Studio is Google's official Android development IDE, containing Android SDK, AVD Manager, and build tools. It's the core tool for developing React Native Android apps, supporting Windows, Mac, and Linux.

  **核心特征 | Key Characteristics:**
  - 跨平台支持（Windows、macOS、Linux） | Cross-platform support (Windows, macOS, Linux)
  - 包含SDK Manager用于管理Android SDK版本和工具 | Includes SDK Manager for managing Android SDK versions and tools
  - AVD Manager创建和管理Android虚拟设备 | AVD Manager creates and manages Android Virtual Devices
  - 需要配置ANDROID_HOME环境变量 | Requires ANDROID_HOME environment variable configuration
  - 支持多种Android版本和设备配置 | Supports multiple Android versions and device configurations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Android Studio可以在Windows上开发React Native应用吗？| Can Android Studio be used on Windows to develop React Native apps?
     **答案 | Answer:** 是的 | Yes - Android Studio支持Windows、Mac和Linux，而Xcode只支持Mac | Android Studio supports Windows, Mac, and Linux, while Xcode only supports Mac
  2. ANDROID_HOME环境变量指向什么？| What does the ANDROID_HOME environment variable point to?
     **答案 | Answer:** Android SDK的安装路径，React Native需要此路径来找到构建工具 | The Android SDK installation path, which React Native needs to locate build tools
  3. AVD（Android Virtual Device）和真实设备有什么区别？| What's the difference between AVD (Android Virtual Device) and real devices?
     **答案 | Answer:** AVD是完整的Android系统虚拟机，性能较慢但可配置多种设备；真实设备性能更好，测试更准确 | AVD is a full Android system virtual machine, slower but configurable for various devices; real devices have better performance and more accurate testing
  4. 为什么需要安装多个Android SDK版本？| Why do we need to install multiple Android SDK versions?
     **答案 | Answer:** 为了支持不同Android版本的设备，确保应用兼容性（如Android 11、12、13等） | To support devices running different Android versions and ensure app compatibility (like Android 11, 12, 13, etc.)

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # 设置ANDROID_HOME环境变量（macOS/Linux） | Set ANDROID_HOME environment variable (macOS/Linux)
  # 添加到 ~/.bash_profile 或 ~/.zshrc | Add to ~/.bash_profile or ~/.zshrc
  export ANDROID_HOME=$HOME/Library/Android/sdk
  export PATH=$PATH:$ANDROID_HOME/emulator
  export PATH=$PATH:$ANDROID_HOME/platform-tools

  # Windows设置方式（在系统环境变量中添加） | Windows setup (add in system environment variables)
  # ANDROID_HOME = C:\Users\YourUsername\AppData\Local\Android\Sdk
  # Path添加 = %ANDROID_HOME%\platform-tools
  # Path添加 = %ANDROID_HOME%\emulator

  # 验证Android SDK配置 | Verify Android SDK configuration
  adb --version
  # 应该输出Android Debug Bridge版本信息 | Should output Android Debug Bridge version

  # 列出所有Android模拟器 | List all Android emulators
  emulator -list-avds

  # 启动Android模拟器 | Start Android emulator
  emulator -avd Pixel_5_API_33

  # 检查连接的设备（模拟器和真机） | Check connected devices (emulators and real devices)
  adb devices
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行`adb devices`显示"command not found"，可能是什么原因？| If `adb devices` shows "command not found", what could be the reason?
    **答案 | Answer:** ANDROID_HOME环境变量未设置，或platform-tools路径未添加到PATH | ANDROID_HOME environment variable not set, or platform-tools path not added to PATH
  - 如何创建一个新的Android模拟器？| How do you create a new Android emulator?
    **答案 | Answer:** 打开Android Studio → Tools → AVD Manager → Create Virtual Device，选择设备类型和Android版本 | Open Android Studio → Tools → AVD Manager → Create Virtual Device, select device type and Android version

  **常见误区检查 | Common Misconception Checks:**
  - 安装Android Studio就自动配置好了所有环境变量吗？| Does installing Android Studio automatically configure all environment variables?
    **答案 | Answer:** 不是，需要手动设置ANDROID_HOME和PATH环境变量 | No, you need to manually set ANDROID_HOME and PATH environment variables
  - Android模拟器为什么比iOS模拟器慢？| Why is Android Emulator slower than iOS Simulator?
    **答案 | Answer:** Android模拟器是完整的虚拟机（需要模拟硬件），iOS模拟器是模拟器（共享Mac硬件） | Android Emulator is a full virtual machine (needs to emulate hardware), iOS Simulator is a simulator (shares Mac hardware)

### 4. React Native调试工具 | React Native Debugging Tools (1.5小时 | 1.5 hours)

- **Chrome DevTools与React DevTools | Chrome DevTools and React DevTools**

  **概念定义 | Concept Definition:**
  React Native支持使用Chrome DevTools进行JavaScript调试，以及React DevTools检查组件树和props/state。这些工具提供了类似Web开发的调试体验。 | React Native supports using Chrome DevTools for JavaScript debugging and React DevTools for inspecting component trees and props/state. These tools provide a debugging experience similar to web development.

  **核心特征 | Key Characteristics:**
  - Chrome DevTools提供断点调试、控制台、网络监控等功能 | Chrome DevTools provides breakpoints, console, network monitoring, etc.
  - React DevTools可以查看组件层级、props、state和hooks | React DevTools can view component hierarchy, props, state, and hooks
  - 调试时JavaScript运行在Chrome V8引擎中，而非原生环境 | During debugging, JavaScript runs in Chrome V8 engine, not native environment
  - 支持Source Maps，可以调试原始代码而非编译后代码 | Supports Source Maps for debugging original code instead of compiled code

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 使用Chrome DevTools调试时，JavaScript代码运行在哪里？| When debugging with Chrome DevTools, where does JavaScript code run?
     **答案 | Answer:** 运行在Chrome浏览器的V8引擎中，通过WebSocket与原生应用通信 | Runs in Chrome browser's V8 engine, communicating with native app via WebSocket
  2. React DevTools可以修改组件的state吗？| Can React DevTools modify component state?
     **答案 | Answer:** 可以，可以直接在DevTools中编辑state和props来测试不同场景 | Yes, you can directly edit state and props in DevTools to test different scenarios
  3. 为什么开启远程调试会降低应用性能？| Why does enabling remote debugging reduce app performance?
     **答案 | Answer:** 因为JavaScript不在原生环境运行，需要网络通信传输数据，增加了延迟 | Because JavaScript doesn't run in native environment, requires network communication to transfer data, adding latency
  4. 生产环境中能使用Chrome DevTools吗？| Can Chrome DevTools be used in production environment?
     **答案 | Answer:** 不能 | No - 调试功能只在开发模式下可用，生产构建会移除这些功能 | Debugging features are only available in development mode, production builds remove these features

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // DebugExample.js - 调试工具演示 | Debugging tools demonstration
  import React, { useState, useEffect } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';

  export default function DebugExample() {
    const [count, setCount] = useState(0);
    const [logs, setLogs] = useState([]);

    // 使用console.log进行调试 | Use console.log for debugging
    useEffect(() => {
      console.log('组件已挂载 | Component mounted');
      console.log('当前计数 | Current count:', count);

      // 可以在Chrome DevTools的Console中看到这些日志 | These logs can be seen in Chrome DevTools Console
      return () => {
        console.log('组件将卸载 | Component will unmount');
      };
    }, [count]);

    const handlePress = () => {
      // 在这里设置断点来调试 | Set breakpoint here to debug
      debugger; // 这会在Chrome DevTools中暂停执行 | This will pause execution in Chrome DevTools

      const newCount = count + 1;
      setCount(newCount);

      // 使用console.table显示结构化数据 | Use console.table for structured data
      console.table({
        previousCount: count,
        newCount: newCount,
        timestamp: new Date().toISOString()
      });

      setLogs([...logs, `Count updated to ${newCount}`]);
    };

    // 测试console的不同方法 | Test different console methods
    console.warn('这是一个警告 | This is a warning');
    console.error('这是一个错误（测试用）| This is an error (for testing)');
    console.info('应用信息 | App info:', { count, logsCount: logs.length });

    return (
      <View style={styles.container}>
        <Text style={styles.title}>调试工具测试 | Debug Tools Test</Text>
        <Text style={styles.count}>Count: {count}</Text>

        <Button
          title="增加计数 | Increment (检查Console) | (Check Console)"
          onPress={handlePress}
        />

        <View style={styles.logsContainer}>
          <Text style={styles.logsTitle}>日志记录 | Logs:</Text>
          {logs.map((log, index) => (
            <Text key={index} style={styles.logItem}>{log}</Text>
          ))}
        </View>

        <Text style={styles.instruction}>
          打开Chrome DevTools查看console.log输出{'\n'}
          Open Chrome DevTools to see console.log output
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    count: {
      fontSize: 32,
      fontWeight: 'bold',
      textAlign: 'center',
      marginVertical: 20,
      color: '#007AFF',
    },
    logsContainer: {
      marginTop: 20,
      padding: 10,
      backgroundColor: '#f5f5f5',
      borderRadius: 8,
    },
    logsTitle: {
      fontWeight: 'bold',
      marginBottom: 10,
    },
    logItem: {
      fontSize: 12,
      marginVertical: 2,
      color: '#666',
    },
    instruction: {
      marginTop: 20,
      textAlign: 'center',
      color: '#999',
      fontSize: 12,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何在React Native应用中打开Chrome DevTools？| How do you open Chrome DevTools in a React Native app?
    **答案 | Answer:** 打开开发者菜单（Cmd+D或Cmd+M），选择"Debug"或"Debug with Chrome"，浏览器会自动打开DevTools | Open dev menu (Cmd+D or Cmd+M), select "Debug" or "Debug with Chrome", browser will automatically open DevTools
  - 在上面的代码中，`debugger`语句会有什么效果？| What effect does the `debugger` statement have in the code above?
    **答案 | Answer:** 当执行到这一行时，如果DevTools已打开，会暂停执行，允许你检查变量和调用栈 | When execution reaches this line, if DevTools is open, it will pause execution, allowing you to inspect variables and call stack

  **常见误区检查 | Common Misconception Checks:**
  - console.log在React Native中的输出位置和Web应用相同吗？| Does console.log output to the same place in React Native as in web apps?
    **答案 | Answer:** 不完全相同。默认输出到Metro bundler终端，启用远程调试后才输出到Chrome控制台 | Not exactly. By default outputs to Metro bundler terminal, only outputs to Chrome console when remote debugging is enabled
  - React DevTools可以调试原生代码吗？| Can React DevTools debug native code?
    **答案 | Answer:** 不能，React DevTools只能检查React组件。原生代码需要使用Xcode或Android Studio的调试器 | No, React DevTools can only inspect React components. Native code requires Xcode or Android Studio debuggers

- **开发者菜单（Dev Menu）功能详解 | Developer Menu Features Explained**

  **概念定义 | Concept Definition:**
  开发者菜单是React Native内置的开发工具面板，提供快速访问调试、重载、性能监控等功能的入口。它是移动开发中最常用的工具之一。 | The Developer Menu is React Native's built-in development tool panel, providing quick access to debugging, reloading, performance monitoring, and other features. It's one of the most commonly used tools in mobile development.

  **核心特征 | Key Characteristics:**
  - iOS上通过Cmd+D打开，Android上通过Cmd+M（Mac）或Ctrl+M（Windows/Linux）打开 | Open with Cmd+D on iOS, Cmd+M (Mac) or Ctrl+M (Windows/Linux) on Android
  - 提供重载（Reload）和启用/禁用Fast Refresh选项 | Provides Reload and Enable/Disable Fast Refresh options
  - 可以切换元素检查器（Element Inspector）查看UI层级 | Can toggle Element Inspector to view UI hierarchy
  - 包含性能监控（Perf Monitor）显示FPS和内存使用 | Includes Performance Monitor showing FPS and memory usage
  - 提供远程调试和网络检查选项 | Provides remote debugging and network inspection options

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 开发者菜单在物理设备上也能使用吗？| Can the Developer Menu be used on physical devices too?
     **答案 | Answer:** 可以 | Yes - 在物理设备上通过摇晃设备来打开（或使用相同的快捷键如果连接到电脑） | On physical devices, shake the device to open it (or use same shortcuts if connected to computer)
  2. Fast Refresh和Hot Reload有什么区别？| What's the difference between Fast Refresh and Hot Reload?
     **答案 | Answer:** Fast Refresh是更智能的热重载，能更好地保持组件状态，减少副作用，是React Native 0.61后的默认选项 | Fast Refresh is smarter hot reloading that better preserves component state and reduces side effects, the default option since React Native 0.61
  3. 元素检查器（Element Inspector）有什么用？| What is the Element Inspector used for?
     **答案 | Answer:** 点击屏幕上的元素，查看其样式、props和在组件树中的位置，类似Web开发中的"检查元素" | Tap elements on screen to view their styles, props, and position in component tree, similar to "Inspect Element" in web development
  4. Perf Monitor显示的FPS代表什么？| What does FPS shown in Perf Monitor represent?
     **答案 | Answer:** Frames Per Second（每秒帧数），显示UI和JavaScript线程的性能，60 FPS是理想值 | Frames Per Second, showing performance of UI and JavaScript threads, 60 FPS is ideal

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // DevMenuExample.js - 开发者菜单功能演示 | Dev Menu features demonstration
  import React, { useState } from 'react';
  import { View, Text, Button, StyleSheet, ScrollView } from 'react-native';

  export default function DevMenuExample() {
    const [renderCount, setRenderCount] = useState(0);
    const [items, setItems] = useState([]);

    // 测试Fast Refresh - 修改这个值并保存，看状态是否保留 | Test Fast Refresh - modify this value and save, see if state persists
    const testMessage = "测试Fast Refresh | Test Fast Refresh";

    const addItem = () => {
      setItems([...items, `Item ${items.length + 1}`]);
      setRenderCount(renderCount + 1);
    };

    const createHeavyLoad = () => {
      // 创建重负载来测试性能监控 | Create heavy load to test performance monitor
      const heavyArray = [];
      for (let i = 0; i < 10000; i++) {
        heavyArray.push({ id: i, value: Math.random() });
      }
      console.log('重负载计算完成 | Heavy load calculation completed');
    };

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.title}>开发者菜单测试 | Dev Menu Test</Text>

        <View style={styles.section}>
          <Text style={styles.sectionTitle}>Fast Refresh测试 | Fast Refresh Test</Text>
          <Text style={styles.info}>{testMessage}</Text>
          <Text style={styles.info}>渲染次数 | Render count: {renderCount}</Text>
          <Text style={styles.hint}>
            修改上面的testMessage，保存后观察renderCount是否保持{'\n'}
            Modify testMessage above, save and observe if renderCount persists
          </Text>
        </View>

        <View style={styles.section}>
          <Text style={styles.sectionTitle}>元素检查器测试 | Element Inspector Test</Text>
          <Button
            title="添加项目 | Add Item (然后检查此元素) | (Then inspect this)"
            onPress={addItem}
          />
          <View style={styles.itemsContainer}>
            {items.map((item, index) => (
              <View key={index} style={styles.item}>
                <Text>{item}</Text>
              </View>
            ))}
          </View>
          <Text style={styles.hint}>
            打开开发者菜单 → Show Element Inspector → 点击按钮查看样式{'\n'}
            Open Dev Menu → Show Element Inspector → Tap button to see styles
          </Text>
        </View>

        <View style={styles.section}>
          <Text style={styles.sectionTitle}>性能监控测试 | Performance Monitor Test</Text>
          <Button
            title="创建重负载 | Create Heavy Load (观察Perf Monitor) | (Watch Perf)"
            onPress={createHeavyLoad}
          />
          <Text style={styles.hint}>
            打开开发者菜单 → Show Perf Monitor → 点击按钮观察FPS变化{'\n'}
            Open Dev Menu → Show Perf Monitor → Tap button to observe FPS changes
          </Text>
        </View>

        <View style={styles.instructions}>
          <Text style={styles.instructionsTitle}>开发者菜单使用指南 | Dev Menu Guide:</Text>
          <Text style={styles.instructionItem}>• iOS: Cmd+D</Text>
          <Text style={styles.instructionItem}>• Android: Cmd+M (Mac) / Ctrl+M (Win)</Text>
          <Text style={styles.instructionItem}>• 物理设备 | Physical device: 摇晃 | Shake</Text>
          <Text style={styles.instructionItem}>• 重载应用 | Reload: 按R两次 | Press R twice</Text>
        </View>
      </ScrollView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      textAlign: 'center',
      marginVertical: 20,
    },
    section: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    sectionTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#007AFF',
    },
    info: {
      fontSize: 16,
      marginVertical: 5,
    },
    hint: {
      fontSize: 12,
      color: '#999',
      marginTop: 10,
      fontStyle: 'italic',
    },
    itemsContainer: {
      marginTop: 10,
    },
    item: {
      padding: 10,
      backgroundColor: '#f0f0f0',
      marginVertical: 5,
      borderRadius: 5,
    },
    instructions: {
      padding: 20,
      backgroundColor: '#f9f9f9',
      margin: 20,
      borderRadius: 8,
    },
    instructionsTitle: {
      fontWeight: 'bold',
      marginBottom: 10,
      fontSize: 16,
    },
    instructionItem: {
      marginVertical: 3,
      fontSize: 14,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何快速重载应用而不打开开发者菜单？| How can you quickly reload the app without opening the Dev Menu?
    **答案 | Answer:** 在模拟器中按R键两次（双击R）即可快速重载 | In simulator, press R key twice (double-tap R) for quick reload
  - 性能监控中JavaScript FPS和UI FPS分别代表什么？| What do JavaScript FPS and UI FPS represent in Performance Monitor?
    **答案 | Answer:** JS FPS显示JavaScript线程性能（业务逻辑），UI FPS显示UI线程性能（渲染）。两者都应接近60 | JS FPS shows JavaScript thread performance (business logic), UI FPS shows UI thread performance (rendering). Both should be close to 60

### 5. 热重载与快速开发迭代 | Hot Reload & Fast Development Iteration (45分钟 | 45 minutes)

- **Fast Refresh工作原理 | Fast Refresh Working Principles**

  **概念定义 | Concept Definition:**
  Fast Refresh是React Native的热更新机制，允许开发者在保存代码后立即看到更改，同时尽可能保留应用状态。它结合了热重载（Hot Reload）和实时重载（Live Reload）的优点。 | Fast Refresh is React Native's hot update mechanism that allows developers to see changes immediately after saving code while preserving app state as much as possible. It combines the advantages of Hot Reload and Live Reload.

  **核心特征 | Key Characteristics:**
  - 编辑React组件时会重新渲染该组件，保留state | When editing React components, re-renders the component while preserving state
  - 编辑非React文件或修改类组件时会完全重载 | Fully reloads when editing non-React files or modifying class components
  - 语法错误会显示在应用界面上（错误覆盖层） | Syntax errors are displayed on the app interface (error overlay)
  - 默认启用，可在开发者菜单中禁用 | Enabled by default, can be disabled in Dev Menu

  **实践验证问题 | Practice Verification Questions:**
  1. Fast Refresh能在修改styles对象后保留state吗？| Can Fast Refresh preserve state after modifying styles object?
     **答案 | Answer:** 能 | Yes - Fast Refresh会重新渲染组件并应用新样式，同时保留state | Fast Refresh will re-render component with new styles while preserving state
  2. 什么情况下Fast Refresh会失败并需要完全重载？| In what situations will Fast Refresh fail and require full reload?
     **答案 | Answer:** 修改类组件、添加新的原生模块、修改app.json等配置文件时 | When modifying class components, adding new native modules, or changing config files like app.json
  3. 如何测试Fast Refresh是否正常工作？| How can you test if Fast Refresh is working properly?
     **答案 | Answer:** 修改组件的文本或样式，保存后应立即看到变化且state保持不变 | Modify component text or styles, changes should appear immediately after saving with state unchanged

  **最佳实践 | Best Practices:**
  - 使用函数组件和Hooks而非类组件，以充分利用Fast Refresh | Use function components and Hooks instead of class components to fully utilize Fast Refresh
  - 将复杂逻辑提取到自定义Hooks中，便于独立测试和热更新 | Extract complex logic into custom hooks for independent testing and hot updates
  - 避免在组件顶层执行副作用代码，使用useEffect管理副作用 | Avoid executing side effects at component top level, use useEffect to manage side effects

### 6. 开发工作流最佳实践 | Development Workflow Best Practices (30分钟 | 30 minutes)

- **高效开发环境配置 | Efficient Development Environment Configuration**

  **关键原则 | Key Principles:**
  - 同时配置iOS和Android环境，便于跨平台测试 | Configure both iOS and Android environments for cross-platform testing
  - 使用版本控制（Git）管理代码，忽略node_modules和构建产物 | Use version control (Git) to manage code, ignore node_modules and build artifacts
  - 配置编辑器（VS Code）安装React Native插件和代码格式化工具 | Configure editor (VS Code) with React Native plugins and code formatting tools
  - 建立明确的调试流程：先检查控制台错误，再使用断点调试 | Establish clear debugging process: check console errors first, then use breakpoint debugging

  **实践验证问题 | Practice Verification Questions:**
  1. 为什么建议使用VS Code的React Native插件？| Why is it recommended to use VS Code's React Native plugin?
     **答案 | Answer:** 提供代码补全、语法高亮、快速启动模拟器、集成调试等功能，提高开发效率 | Provides code completion, syntax highlighting, quick simulator launch, integrated debugging, improving development efficiency
  2. 开发时遇到错误，应该先查看哪里？| When encountering errors during development, where should you look first?
     **答案 | Answer:** 先查看Metro bundler终端输出和应用内的错误覆盖层（Red Box/Yellow Box），它们提供最直接的错误信息 | First check Metro bundler terminal output and in-app error overlays (Red Box/Yellow Box), they provide most direct error information
  3. 为什么要在Git中忽略node_modules？| Why should node_modules be ignored in Git?
     **答案 | Answer:** node_modules体积庞大且可以通过npm install重新生成，不应纳入版本控制 | node_modules is large and can be regenerated via npm install, should not be version controlled

  **综合应用检查 | Comprehensive Application Check:**
  - 如何在一个项目中同时测试iOS和Android？| How can you test both iOS and Android in one project?
    **答案 | Answer:** 打开两个终端，分别运行`npm run ios`和`npm run android`，或使用VS Code的多终端功能 | Open two terminals, run `npm run ios` and `npm run android` separately, or use VS Code's multi-terminal feature
  - 发布应用前需要关闭哪些开发功能？| What development features need to be disabled before releasing the app?
    **答案 | Answer:** Fast Refresh、远程调试、性能监控等都会在生产构建时自动移除，无需手动关闭 | Fast Refresh, remote debugging, performance monitoring are automatically removed in production builds, no manual disabling needed

## 实践项目：开发工具综合应用 | Practical Project: Comprehensive Development Tools Application

### 目标 | Objective
构建一个"开发工具演示应用"，集成本日学习的所有调试工具和技术，通过实际操作掌握React Native开发环境的各项功能。 | Build a "Development Tools Demo App" that integrates all debugging tools and techniques learned today, mastering React Native development environment features through practical operations.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 能否解释Fast Refresh和完全重载的区别？| Can you explain the difference between Fast Refresh and full reload?
   **答案 | Answer:** Fast Refresh保留state和局部修改，完全重载会重置整个应用并重新执行所有初始化代码
2. 如何在iOS和Android模拟器中打开开发者菜单？| How do you open the Developer Menu in iOS and Android simulators?
   **答案 | Answer:** iOS用Cmd+D，Android用Cmd+M（Mac）或Ctrl+M（Windows），物理设备摇晃
3. Chrome DevTools能调试React Native的原生代码吗？| Can Chrome DevTools debug React Native's native code?
   **答案 | Answer:** 不能，只能调试JavaScript代码；原生代码需要使用Xcode或Android Studio

### 步骤 | Steps
1. **创建项目并验证环境 | Create project and verify environment**
   - 使用Expo CLI创建新项目
   - 分别在iOS和Android模拟器上运行
   - 确认Fast Refresh正常工作

2. **集成调试功能 | Integrate debugging features**
   - 添加console.log、console.warn、console.error演示
   - 创建带有debugger语句的函数
   - 添加性能测试代码（创建大量元素）

3. **构建UI组件 | Build UI components**
   - 调试工具测试区（Console、Debugger）
   - 性能监控测试区（FPS测试）
   - 元素检查器演示区（复杂嵌套组件）
   - Fast Refresh测试区（可修改文本）

4. **添加交互功能 | Add interactive features**
   - 按钮触发不同类型的console输出
   - 性能负载测试按钮
   - 错误边界（Error Boundary）演示

5. **测试和优化 | Testing and optimization**
   - 在两个平台上测试所有功能
   - 使用Element Inspector检查布局
   - 使用Perf Monitor监控性能

### 示例代码 | Example Code
```javascript
// DevelopmentToolsApp.js
"""
开发工具综合演示应用 | Development Tools Comprehensive Demo App

本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
- 开发者菜单使用 | Developer Menu usage
- Chrome DevTools调试 | Chrome DevTools debugging
- React DevTools组件检查 | React DevTools component inspection
- Fast Refresh状态保持 | Fast Refresh state preservation
- 性能监控工具 | Performance monitoring tools
"""

import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  Button,
  StyleSheet,
  ScrollView,
  TouchableOpacity,
  SafeAreaView,
} from 'react-native';

// 组件1：Console调试演示 | Component 1: Console debugging demo
function ConsoleDebugSection() {
  const [logCount, setLogCount] = useState(0);

  const testConsole = (type) => {
    const message = `测试${type} | Test ${type}`;
    switch(type) {
      case 'log':
        console.log(message, { count: logCount, timestamp: Date.now() });
        break;
      case 'warn':
        console.warn(message);
        break;
      case 'error':
        console.error(message);
        break;
      case 'table':
        console.table([
          { name: 'iOS', status: 'Ready' },
          { name: 'Android', status: 'Ready' }
        ]);
        break;
    }
    setLogCount(logCount + 1);
  };

  return (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>Console调试 | Console Debugging</Text>
      <View style={styles.buttonRow}>
        <TouchableOpacity
          style={[styles.debugButton, styles.logButton]}
          onPress={() => testConsole('log')}
        >
          <Text style={styles.buttonText}>console.log</Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={[styles.debugButton, styles.warnButton]}
          onPress={() => testConsole('warn')}
        >
          <Text style={styles.buttonText}>console.warn</Text>
        </TouchableOpacity>
      </View>
      <View style={styles.buttonRow}>
        <TouchableOpacity
          style={[styles.debugButton, styles.errorButton]}
          onPress={() => testConsole('error')}
        >
          <Text style={styles.buttonText}>console.error</Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={[styles.debugButton, styles.tableButton]}
          onPress={() => testConsole('table')}
        >
          <Text style={styles.buttonText}>console.table</Text>
        </TouchableOpacity>
      </View>
      <Text style={styles.hint}>日志次数 | Log count: {logCount}</Text>
      <Text style={styles.hint}>打开DevTools查看输出 | Open DevTools to see output</Text>
    </View>
  );
}

// 组件2：Debugger断点演示 | Component 2: Debugger breakpoint demo
function DebuggerSection() {
  const [calculation, setCalculation] = useState(0);

  const runWithDebugger = () => {
    // 在Chrome DevTools中设置断点来检查这里的执行 | Set breakpoint in Chrome DevTools to inspect execution here
    debugger;

    const numbers = [1, 2, 3, 4, 5];
    const result = numbers.reduce((sum, num) => {
      // 可以在这里设置断点来逐步调试 | Can set breakpoint here for step debugging
      debugger;
      return sum + num;
    }, 0);

    setCalculation(result);
    console.log('计算结果 | Calculation result:', result);
  };

  return (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>Debugger断点 | Debugger Breakpoints</Text>
      <Button
        title="运行带断点的代码 | Run Code with Breakpoints"
        onPress={runWithDebugger}
      />
      <Text style={styles.result}>结果 | Result: {calculation}</Text>
      <Text style={styles.hint}>
        启用远程调试后点击按钮{'\n'}
        Enable remote debugging then tap button
      </Text>
    </View>
  );
}

// 组件3：性能监控演示 | Component 3: Performance monitoring demo
function PerformanceSection() {
  const [items, setItems] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  const createHeavyLoad = () => {
    setIsLoading(true);
    // 创建大量元素来测试性能 | Create many elements to test performance
    const newItems = [];
    for (let i = 0; i < 1000; i++) {
      newItems.push({
        id: i,
        value: `Item ${i}`,
        random: Math.random()
      });
    }
    setItems(newItems);
    setIsLoading(false);
    console.log('创建了1000个元素 | Created 1000 items');
  };

  const clearItems = () => {
    setItems([]);
  };

  return (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>性能监控 | Performance Monitor</Text>
      <View style={styles.buttonRow}>
        <TouchableOpacity
          style={[styles.debugButton, styles.perfButton]}
          onPress={createHeavyLoad}
          disabled={isLoading}
        >
          <Text style={styles.buttonText}>
            {isLoading ? '加载中... | Loading...' : '创建1000项 | Create 1000 items'}
          </Text>
        </TouchableOpacity>
        <TouchableOpacity
          style={[styles.debugButton, styles.clearButton]}
          onPress={clearItems}
        >
          <Text style={styles.buttonText}>清除 | Clear</Text>
        </TouchableOpacity>
      </View>
      <Text style={styles.hint}>当前项目数 | Current items: {items.length}</Text>
      <Text style={styles.hint}>
        打开Perf Monitor观察FPS变化{'\n'}
        Open Perf Monitor to observe FPS changes
      </Text>
    </View>
  );
}

// 组件4：Fast Refresh测试 | Component 4: Fast Refresh test
function FastRefreshSection() {
  const [counter, setCounter] = useState(0);

  // 修改这个值并保存，观察counter是否保持 | Modify this value and save, observe if counter persists
  const testText = "Fast Refresh测试文本 | Fast Refresh Test Text";

  return (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>Fast Refresh测试 | Fast Refresh Test</Text>
      <Text style={styles.testText}>{testText}</Text>
      <Button
        title={`计数: ${counter} | Count: ${counter}`}
        onPress={() => setCounter(counter + 1)}
      />
      <Text style={styles.hint}>
        修改testText常量，保存后观察counter是否保持{'\n'}
        Modify testText constant, save and observe if counter persists
      </Text>
    </View>
  );
}

// 组件5：元素检查器演示 | Component 5: Element Inspector demo
function ElementInspectorSection() {
  return (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>元素检查器 | Element Inspector</Text>
      <View style={styles.nestedContainer}>
        <View style={styles.box1}>
          <Text>Box 1 (Flex: 1)</Text>
          <View style={styles.box2}>
            <Text>Box 2 (Nested)</Text>
          </View>
        </View>
        <View style={styles.box3}>
          <Text>Box 3 (Flex: 2)</Text>
        </View>
      </View>
      <Text style={styles.hint}>
        打开Element Inspector并点击上方的Box{'\n'}
        Open Element Inspector and tap boxes above
      </Text>
    </View>
  );
}

// 主应用组件 | Main app component
export default function DevelopmentToolsApp() {
  useEffect(() => {
    console.log('应用已启动 | App started');
    console.log('开发者菜单快捷键 | Dev Menu shortcuts:');
    console.log('iOS: Cmd+D | Android: Cmd+M');

    return () => {
      console.log('应用将卸载 | App will unmount');
    };
  }, []);

  return (
    <SafeAreaView style={styles.container}>
      <ScrollView>
        <Text style={styles.title}>
          开发工具综合演示{'\n'}
          Development Tools Demo
        </Text>

        <View style={styles.instructions}>
          <Text style={styles.instructionsTitle}>使用指南 | Usage Guide:</Text>
          <Text style={styles.instructionItem}>1. 打开Chrome DevTools（开发者菜单 → Debug）</Text>
          <Text style={styles.instructionItem}>2. 打开Perf Monitor（开发者菜单 → Perf Monitor）</Text>
          <Text style={styles.instructionItem}>3. 尝试Element Inspector（开发者菜单 → Show Inspector）</Text>
          <Text style={styles.instructionItem}>4. 测试Fast Refresh（修改代码并保存）</Text>
        </View>

        <ConsoleDebugSection />
        <DebuggerSection />
        <PerformanceSection />
        <FastRefreshSection />
        <ElementInspectorSection />

        <View style={styles.footer}>
          <Text style={styles.footerText}>
            完成所有测试后，检查完成清单{'\n'}
            After completing all tests, check completion checklist
          </Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    textAlign: 'center',
    marginVertical: 20,
    color: '#333',
  },
  instructions: {
    backgroundColor: '#e3f2fd',
    padding: 15,
    margin: 15,
    borderRadius: 8,
    borderLeftWidth: 4,
    borderLeftColor: '#2196F3',
  },
  instructionsTitle: {
    fontWeight: 'bold',
    fontSize: 16,
    marginBottom: 10,
    color: '#1976D2',
  },
  instructionItem: {
    fontSize: 14,
    marginVertical: 3,
    color: '#333',
  },
  section: {
    padding: 20,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 15,
    color: '#007AFF',
  },
  buttonRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 10,
  },
  debugButton: {
    flex: 1,
    padding: 15,
    borderRadius: 8,
    marginHorizontal: 5,
    alignItems: 'center',
  },
  logButton: {
    backgroundColor: '#4CAF50',
  },
  warnButton: {
    backgroundColor: '#FF9800',
  },
  errorButton: {
    backgroundColor: '#F44336',
  },
  tableButton: {
    backgroundColor: '#9C27B0',
  },
  perfButton: {
    backgroundColor: '#2196F3',
  },
  clearButton: {
    backgroundColor: '#757575',
  },
  buttonText: {
    color: '#fff',
    fontWeight: 'bold',
    fontSize: 12,
  },
  hint: {
    fontSize: 12,
    color: '#999',
    marginTop: 10,
    fontStyle: 'italic',
  },
  result: {
    fontSize: 18,
    fontWeight: 'bold',
    marginVertical: 10,
    color: '#4CAF50',
  },
  testText: {
    fontSize: 16,
    marginBottom: 10,
    padding: 10,
    backgroundColor: '#f5f5f5',
    borderRadius: 5,
  },
  nestedContainer: {
    flexDirection: 'row',
    height: 150,
    marginBottom: 10,
  },
  box1: {
    flex: 1,
    backgroundColor: '#FFEBEE',
    padding: 10,
    marginRight: 5,
    borderRadius: 8,
    justifyContent: 'space-between',
  },
  box2: {
    backgroundColor: '#E1F5FE',
    padding: 10,
    borderRadius: 5,
  },
  box3: {
    flex: 2,
    backgroundColor: '#E8F5E9',
    padding: 10,
    marginLeft: 5,
    borderRadius: 8,
    justifyContent: 'center',
    alignItems: 'center',
  },
  footer: {
    padding: 20,
    alignItems: 'center',
  },
  footerText: {
    textAlign: 'center',
    color: '#666',
    fontSize: 14,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 应用能在iOS和Android模拟器上成功运行吗？| Does the app run successfully on both iOS and Android simulators?
2. 所有console方法的输出都能在Chrome DevTools中看到吗？| Can all console method outputs be seen in Chrome DevTools?
3. 使用Element Inspector时能正确显示组件层级和样式吗？| Does Element Inspector correctly show component hierarchy and styles?
4. Fast Refresh是否能在修改testText后保留counter的值？| Does Fast Refresh preserve counter value after modifying testText?
5. 性能监控能显示创建1000个项目时的FPS变化吗？| Does performance monitor show FPS changes when creating 1000 items?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **调试工具深度探索 | Debugging Tools Deep Exploration**
   - **练习描述 | Exercise Description:** 在演示应用中添加Network请求，使用Chrome DevTools的Network标签监控
   - **概念检查 | Concept Check:** 理解React Native中网络请求的调试方式与Web的异同
   - **学习目标 | Learning Objective:** 掌握完整的调试工具链使用

2. **跨平台差异对比 | Cross-platform Differences Comparison**
   - **练习描述 | Exercise Description:** 创建相同功能在iOS和Android上运行，记录性能和视觉差异
   - **概念检查 | Concept Check:** 了解两个平台的模拟器特性差异
   - **学习目标 | Learning Objective:** 培养跨平台测试思维

3. **错误处理机制 | Error Handling Mechanism**
   - **练习描述 | Exercise Description:** 实现Error Boundary组件，捕获并优雅处理运行时错误
   - **概念检查 | Concept Check:** 理解React错误边界的工作原理
   - **学习目标 | Learning Objective:** 提高应用健壮性

4. **性能优化实践 | Performance Optimization Practice**
   - **练习描述 | Exercise Description:** 使用Perf Monitor识别性能瓶颈，优化渲染性能
   - **概念检查 | Concept Check:** 掌握FPS监控和性能分析方法
   - **学习目标 | Learning Objective:** 建立性能优化意识

5. **自定义开发工具 | Custom Development Tools**
   - **练习描述 | Exercise Description:** 创建自定义的调试面板，显示应用状态和日志
   - **概念检查 | Concept Check:** 理解如何扩展开发工具
   - **学习目标 | Learning Objective:** 提升工具定制能力

6. **真机调试配置 | Physical Device Debugging Setup**
   - **练习描述 | Exercise Description:** 在真实iOS和Android设备上配置并运行应用
   - **概念检查 | Concept Check:** 了解真机调试与模拟器调试的区别
   - **学习目标 | Learning Objective:** 掌握完整的设备测试流程

7. **开发环境自动化 | Development Environment Automation**
   - **练习描述 | Exercise Description:** 编写脚本自动启动模拟器并运行应用
   - **概念检查 | Concept Check:** 理解React Native的启动流程
   - **学习目标 | Learning Objective:** 提高开发效率

## 学习资源 | Learning Resources
- [React Native官方文档 - 调试](https://reactnative.dev/docs/debugging)
- [Chrome DevTools官方指南](https://developer.chrome.com/docs/devtools/)
- [React DevTools使用教程](https://react.dev/learn/react-developer-tools)
- [Xcode官方文档](https://developer.apple.com/xcode/)
- [Android Studio用户指南](https://developer.android.com/studio/intro)
- [Metro Bundler配置](https://facebook.github.io/metro/docs/configuration)
- [Flipper调试工具](https://fbflipper.com/) - 高级调试平台

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 成功安装并配置Xcode或Android Studio
- [ ] 能在iOS和Android模拟器上运行React Native应用
- [ ] 掌握开发者菜单的所有核心功能（Reload、Debug、Perf Monitor、Element Inspector）
- [ ] 能使用Chrome DevTools设置断点并调试JavaScript代码
- [ ] 理解Fast Refresh的工作原理和适用场景
- [ ] 能使用React DevTools检查组件层级和state/props
- [ ] 熟练使用console.log、warn、error等调试方法
- [ ] 了解iOS Simulator和Android Emulator的区别
- [ ] 完成实践项目并在两个平台上测试成功
- [ ] 至少完成3个扩展练习

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
1. 向他人清晰解释React Native开发环境的核心组成部分
2. 独立配置新设备的开发环境（包括模拟器和调试工具）
3. 在遇到错误时能够系统性地使用调试工具排查问题
4. 理解不同调试工具的适用场景和限制

Before marking as complete, ensure you can:
1. Clearly explain the core components of React Native development environment to others
2. Independently configure development environment on new devices (including simulators and debugging tools)
3. Systematically use debugging tools to troubleshoot problems when encountering errors
4. Understand the applicable scenarios and limitations of different debugging tools
