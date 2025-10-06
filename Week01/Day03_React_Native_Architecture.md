# React Native入门 - 第3天：理解React Native架构 | React Native Introduction - Day 3: Understanding React Native Architecture

## 学习目标 | Learning Objectives
- 理解React Native的桥接(Bridge)架构及其工作原理 | Understand React Native's Bridge architecture and how it works
- 掌握JavaScript线程与原生线程之间的通信机制 | Master the communication mechanism between JavaScript and native threads
- 了解Metro打包工具与Webpack的核心差异 | Learn the core differences between Metro bundler and Webpack
- 理解React Native的新架构(JSI/Fabric/TurboModules) | Understand React Native's New Architecture (JSI/Fabric/TurboModules)
- 能够分析和调试跨线程通信问题 | Be able to analyze and debug cross-thread communication issues
- 掌握性能优化的基础架构知识 | Master foundational architecture knowledge for performance optimization

## 详细内容 | Detailed Content

### 1. React Native的三线程模型 | React Native's Three-Thread Model (1小时 | 1 hour)

- **三线程架构 | Three-Thread Architecture**

  **概念定义 | Concept Definition:**
  React Native应用运行在三个独立的线程上：JavaScript线程(执行你的React代码)、原生UI线程(处理原生组件渲染和用户交互)、Shadow线程(计算布局)。这与Web应用只有单一主线程完全不同。 | React Native apps run on three separate threads: the JavaScript thread (executes your React code), the Native UI thread (handles native component rendering and user interactions), and the Shadow thread (calculates layout). This is completely different from web apps which have a single main thread.

  **核心特征 | Key Characteristics:**
  - JavaScript线程与原生线程异步通信，通过Bridge传递序列化消息 | JavaScript thread communicates asynchronously with native threads via serialized messages through the Bridge
  - UI线程必须保持60fps以确保流畅体验，不能被阻塞 | UI thread must maintain 60fps for smooth experience and cannot be blocked
  - Shadow线程使用Yoga布局引擎计算Flexbox布局 | Shadow thread uses Yoga layout engine to calculate Flexbox layouts
  - 线程间通信是异步的，存在延迟，这影响性能优化策略 | Inter-thread communication is asynchronous with latency, affecting performance optimization strategies

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native应用是否在单一线程上运行？| Does a React Native app run on a single thread?
     **答案 | Answer:** 否 | No - React Native使用三个主要线程：JavaScript线程、原生UI线程和Shadow线程 | React Native uses three main threads: JavaScript thread, Native UI thread, and Shadow thread
  2. JavaScript代码能否直接操作原生UI组件？| Can JavaScript code directly manipulate native UI components?
     **答案 | Answer:** 否 | No - JavaScript必须通过Bridge发送异步消息到原生线程 | JavaScript must send asynchronous messages through the Bridge to the native thread
  3. 当你在JavaScript中调用`setState()`时，UI会立即更新吗？| When you call `setState()` in JavaScript, does the UI update immediately?
     **答案 | Answer:** 否 | No - 更新需要通过Bridge传递到原生线程，然后触发重新渲染，存在微小延迟 | Updates must be passed through the Bridge to the native thread, then trigger re-render, with a small delay
  4. 哪个线程负责响应用户的触摸事件？| Which thread is responsible for responding to user touch events?
     **答案 | Answer:** 原生UI线程首先捕获事件，然后将事件数据通过Bridge发送到JavaScript线程处理 | Native UI thread first captures the event, then sends event data through the Bridge to JavaScript thread for handling

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';

  // 演示跨线程通信的延迟 | Demonstrating cross-thread communication latency
  const ThreadDemoComponent = () => {
    const [count, setCount] = useState(0);
    const [logMessages, setLogMessages] = useState([]);

    // 记录时间戳以观察延迟 | Log timestamps to observe latency
    const handlePress = () => {
      const jsTimestamp = Date.now();
      console.log(`[JS Thread] Button pressed at: ${jsTimestamp}`);

      // setState触发跨线程通信 | setState triggers cross-thread communication
      setCount(prev => {
        const newCount = prev + 1;
        console.log(`[JS Thread] State updated to: ${newCount}`);
        return newCount;
      });

      setLogMessages(prev => [...prev, `Press at ${jsTimestamp}`]);
    };

    useEffect(() => {
      // useEffect在JavaScript线程执行，但UI更新在原生线程
      // useEffect executes on JS thread, but UI updates on native thread
      console.log(`[JS Thread] useEffect: Count is now ${count}`);
    }, [count]);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>跨线程通信演示 | Cross-Thread Demo</Text>
        <Text style={styles.count}>点击次数 | Count: {count}</Text>
        <Button title="点击我 | Press Me" onPress={handlePress} />

        {/* 日志显示 | Log display */}
        <View style={styles.logContainer}>
          {logMessages.map((msg, index) => (
            <Text key={index} style={styles.logText}>{msg}</Text>
          ))}
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 10,
    },
    count: {
      fontSize: 24,
      marginVertical: 20,
    },
    logContainer: {
      marginTop: 20,
      padding: 10,
      backgroundColor: '#f0f0f0',
    },
    logText: {
      fontSize: 12,
      fontFamily: 'monospace',
    },
  });

  export default ThreadDemoComponent;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当你点击按钮时，`console.log`的输出出现在哪里？| Where does the `console.log` output appear when you press the button?
    **答案 | Answer:** 在Metro bundler的终端窗口中，不在浏览器控制台 | In the Metro bundler terminal window, not in a browser console
  - 如果在`handlePress`中执行一个耗时5秒的同步操作会发生什么？| What happens if you execute a synchronous operation that takes 5 seconds in `handlePress`?
    **答案 | Answer:** JavaScript线程会被阻塞5秒，UI会冻结无法响应，但原生动画仍可能继续 | JavaScript thread will be blocked for 5 seconds, UI freezes and becomes unresponsive, but native animations may still continue

  **常见误区检查 | Common Misconception Checks:**
  - React Native是否像Web一样在单线程上运行所有代码？| Does React Native run all code on a single thread like the web?
    **答案 | Answer:** 否 - 这是最大的架构差异。RN使用多线程，JS和原生代码在不同线程上异步通信 | No - This is the biggest architectural difference. RN uses multiple threads, with JS and native code communicating asynchronously on different threads
  - setState是否同步更新UI？| Does setState update the UI synchronously?
    **答案 | Answer:** 否 - setState触发的UI更新需要通过Bridge传递到原生线程，是异步过程 | No - UI updates triggered by setState must pass through the Bridge to the native thread, it's an asynchronous process

### 2. Bridge架构的工作原理 | How the Bridge Architecture Works (1小时 | 1 hour)

- **Bridge通信机制 | Bridge Communication Mechanism**

  **概念定义 | Concept Definition:**
  Bridge是React Native旧架构中JavaScript与原生代码之间的通信通道。所有跨语言调用都必须序列化为JSON消息，通过Bridge传递，然后在另一端反序列化。这是一个异步、批处理的过程。 | The Bridge is the communication channel between JavaScript and native code in React Native's old architecture. All cross-language calls must be serialized to JSON messages, passed through the Bridge, then deserialized on the other end. This is an asynchronous, batched process.

  **核心特征 | Key Characteristics:**
  - 消息传递是单向的：JavaScript → Bridge → Native 或 Native → Bridge → JavaScript | Message passing is unidirectional: JavaScript → Bridge → Native or Native → Bridge → JavaScript
  - 数据必须可序列化为JSON，不能传递函数、循环引用等 | Data must be JSON-serializable, cannot pass functions, circular references, etc.
  - Bridge会批处理多个消息以提高效率 | Bridge batches multiple messages for efficiency
  - Bridge是性能瓶颈的主要来源之一 | Bridge is one of the main sources of performance bottlenecks

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 能否通过Bridge传递一个JavaScript函数到原生代码？| Can you pass a JavaScript function through the Bridge to native code?
     **答案 | Answer:** 否 | No - Bridge只能传递可序列化为JSON的数据，函数不能序列化 | Bridge can only pass JSON-serializable data, functions cannot be serialized
  2. Bridge通信是同步还是异步的？| Is Bridge communication synchronous or asynchronous?
     **答案 | Answer:** 异步 | Asynchronous - 所有Bridge调用都是异步的，没有阻塞等待 | All Bridge calls are asynchronous with no blocking wait
  3. 为什么频繁的Bridge通信会导致性能问题？| Why does frequent Bridge communication cause performance issues?
     **答案 | Answer:** 每次通信都需要序列化/反序列化开销，频繁调用会累积延迟并可能导致丢帧 | Each communication requires serialization/deserialization overhead, frequent calls accumulate latency and may cause frame drops
  4. 在一次渲染周期中，React Native会如何优化Bridge调用？| How does React Native optimize Bridge calls in a single render cycle?
     **答案 | Answer:** Bridge会批处理同一周期内的多个UI更新，打包成单个消息传递 | Bridge batches multiple UI updates in the same cycle, packaging them into a single message

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, Text, Button, StyleSheet, Alert } from 'react-native';

  // 演示Bridge序列化限制 | Demonstrating Bridge serialization limitations
  const BridgeSerializationDemo = () => {
    const [data, setData] = useState({ count: 0 });

    // 可以传递的数据类型 | Data types that can be passed
    const validDataExample = () => {
      const validData = {
        string: 'Hello',
        number: 42,
        boolean: true,
        array: [1, 2, 3],
        nestedObject: { key: 'value' },
        nullValue: null,
      };

      console.log('[Bridge] Sending valid data:', validData);
      // Alert是一个原生模块调用，数据会通过Bridge传递
      // Alert is a native module call, data passes through Bridge
      Alert.alert('Valid Data', JSON.stringify(validData, null, 2));
    };

    // 不能直接传递的数据类型 | Data types that cannot be passed directly
    const invalidDataExample = () => {
      const invalidData = {
        // 函数不能序列化 | Functions cannot be serialized
        callback: () => console.log('This will fail'),
        // Date对象会被转为字符串 | Date objects are converted to strings
        date: new Date(),
        // undefined会被忽略 | undefined is ignored
        undefinedValue: undefined,
      };

      console.log('[Bridge] Original data:', invalidData);
      console.log('[Bridge] Serialized:', JSON.stringify(invalidData));
      // 注意：callback和undefinedValue在序列化后消失
      // Note: callback and undefinedValue disappear after serialization
    };

    // 演示批处理优化 | Demonstrating batch optimization
    const batchUpdateExample = () => {
      console.log('[Bridge] Starting batch updates...');
      const startTime = Date.now();

      // 在同一个事件循环中多次调用setState
      // Multiple setState calls in the same event loop
      setData({ count: 1 });
      setData({ count: 2 });
      setData({ count: 3 });
      setData({ count: 4 });
      setData({ count: 5 });

      // React Native会批处理这些更新为单个Bridge调用
      // React Native batches these updates into a single Bridge call
      console.log(`[Bridge] Batch completed in ${Date.now() - startTime}ms`);
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Bridge序列化演示 | Bridge Serialization Demo</Text>

        <Text style={styles.dataText}>当前数据 | Current Data: {JSON.stringify(data)}</Text>

        <Button
          title="发送有效数据 | Send Valid Data"
          onPress={validDataExample}
        />

        <Button
          title="尝试无效数据 | Try Invalid Data"
          onPress={invalidDataExample}
        />

        <Button
          title="批处理更新 | Batch Updates"
          onPress={batchUpdateExample}
        />

        <View style={styles.infoBox}>
          <Text style={styles.infoText}>
            💡 打开终端查看console.log输出{'\n'}
            💡 Open terminal to see console.log output
          </Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    dataText: {
      fontSize: 16,
      marginBottom: 20,
      padding: 10,
      backgroundColor: '#e8f4f8',
    },
    infoBox: {
      marginTop: 20,
      padding: 15,
      backgroundColor: '#fff3cd',
      borderRadius: 5,
    },
    infoText: {
      fontSize: 14,
      color: '#856404',
    },
  });

  export default BridgeSerializationDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当你在`invalidDataExample`中序列化包含函数的对象时，输出是什么？| What is the output when you serialize an object containing a function in `invalidDataExample`?
    **答案 | Answer:** 函数属性会被完全忽略，不会出现在JSON字符串中 | Function properties are completely ignored and don't appear in the JSON string
  - 如果你在一个按钮点击中连续调用5次`setData`，Bridge会传递几次消息？| If you call `setData` 5 times consecutively in one button press, how many messages does the Bridge pass?
    **答案 | Answer:** 通常只有1次，React Native会批处理同一事件循环中的多个setState调用 | Usually only 1 time, React Native batches multiple setState calls in the same event loop

  **常见误区检查 | Common Misconception Checks:**
  - 可以通过Bridge传递回调函数吗？| Can you pass callback functions through the Bridge?
    **答案 | Answer:** 不能直接传递，但可以使用"回调ID"模式：JS传递一个唯一ID，原生代码完成后用这个ID调用回调 | Cannot pass directly, but can use "callback ID" pattern: JS passes a unique ID, native code uses this ID to invoke callback when complete
  - Bridge是双向同时通信的吗？| Does the Bridge support bidirectional simultaneous communication?
    **答案 | Answer:** 否 - 虽然可以双向传递消息，但每次调用都是单向的，需要回调机制实现请求-响应模式 | No - While messages can pass both ways, each call is unidirectional, requiring callback mechanisms for request-response patterns

### 3. Metro打包工具 vs Webpack | Metro Bundler vs Webpack (45分钟 | 45 minutes)

- **Metro打包工具的特点 | Metro Bundler Characteristics**

  **概念定义 | Concept Definition:**
  Metro是React Native的官方JavaScript打包工具，专为移动开发优化。与Web开发常用的Webpack不同，Metro采用不同的打包策略：快速启动、增量构建、按需加载模块，并针对移动设备的约束进行了优化。 | Metro is React Native's official JavaScript bundler, optimized specifically for mobile development. Unlike Webpack commonly used in web development, Metro adopts different bundling strategies: fast startup, incremental builds, on-demand module loading, and optimizations for mobile device constraints.

  **核心特征 | Key Characteristics:**
  - 使用单一入口点，不需要复杂的配置文件 | Uses a single entry point without complex configuration files
  - 支持快速刷新(Fast Refresh)，保留组件状态 | Supports Fast Refresh, preserving component state
  - 增量构建：只重新编译改变的文件 | Incremental builds: only recompiles changed files
  - 默认不进行代码分割，生成单一bundle文件 | No code splitting by default, generates a single bundle file
  - 原生支持React Native的特殊模块解析(.ios.js/.android.js) | Native support for React Native's special module resolution (.ios.js/.android.js)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Metro是否需要像Webpack一样的复杂配置？| Does Metro require complex configuration like Webpack?
     **答案 | Answer:** 否 | No - Metro遵循约定优于配置的原则，大多数项目使用默认配置即可 | Metro follows convention over configuration, most projects use default settings
  2. 当你修改一个文件并保存时，Metro会重新编译整个应用吗？| When you modify and save a file, does Metro recompile the entire app?
     **答案 | Answer:** 否 | No - Metro只重新编译改变的文件和依赖它们的模块(增量构建) | Metro only recompiles changed files and modules that depend on them (incremental builds)
  3. Metro默认支持代码分割吗？| Does Metro support code splitting by default?
     **答案 | Answer:** 否 | No - Metro默认生成单一bundle，但可以通过RAM bundles或Hermes实现优化 | Metro generates a single bundle by default, but can be optimized with RAM bundles or Hermes
  4. 为什么Metro的启动速度通常比Webpack快？| Why is Metro's startup usually faster than Webpack?
     **答案 | Answer:** Metro使用增量构建和缓存策略，只在首次启动时编译全部代码，后续只处理变更 | Metro uses incremental builds and caching strategies, compiling all code only on first startup, then only processing changes

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Metro的平台特定文件解析 | Metro's platform-specific file resolution

  // utils.ios.js - iOS特定实现 | iOS-specific implementation
  export const getPlatformInfo = () => {
    return {
      platform: 'iOS',
      message: '这是iOS特定的实现 | This is iOS-specific implementation',
      features: ['Face ID', 'Apple Pay', 'HealthKit'],
    };
  };

  // utils.android.js - Android特定实现 | Android-specific implementation
  export const getPlatformInfo = () => {
    return {
      platform: 'Android',
      message: '这是Android特定的实现 | This is Android-specific implementation',
      features: ['Google Pay', 'Android Auto', 'Play Services'],
    };
  };

  // App.js - Metro会自动选择正确的平台文件
  // App.js - Metro automatically selects the correct platform file
  import React from 'react';
  import { View, Text, StyleSheet, Platform } from 'react-native';
  import { getPlatformInfo } from './utils'; // .ios.js 或 .android.js

  const MetroPlatformDemo = () => {
    const platformInfo = getPlatformInfo();

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Metro平台解析演示 | Metro Platform Resolution Demo</Text>

        <View style={styles.infoCard}>
          <Text style={styles.label}>检测到的平台 | Detected Platform:</Text>
          <Text style={styles.value}>{platformInfo.platform}</Text>
        </View>

        <View style={styles.infoCard}>
          <Text style={styles.label}>消息 | Message:</Text>
          <Text style={styles.value}>{platformInfo.message}</Text>
        </View>

        <View style={styles.infoCard}>
          <Text style={styles.label}>平台特性 | Platform Features:</Text>
          {platformInfo.features.map((feature, index) => (
            <Text key={index} style={styles.featureText}>• {feature}</Text>
          ))}
        </View>

        <View style={styles.metroInfo}>
          <Text style={styles.metroTitle}>Metro如何工作 | How Metro Works:</Text>
          <Text style={styles.metroText}>
            1. Metro检测到import './utils'{'\n'}
            2. 根据运行平台选择utils.ios.js或utils.android.js{'\n'}
            3. 只将选中的文件打包进bundle{'\n'}
            4. 支持Fast Refresh，保存后立即看到更新{'\n\n'}
            1. Metro detects import './utils'{'\n'}
            2. Selects utils.ios.js or utils.android.js based on platform{'\n'}
            3. Only bundles the selected file{'\n'}
            4. Supports Fast Refresh, see updates immediately after save
          </Text>
        </View>

        {/* 演示Platform API */}
        <View style={styles.platformAPI}>
          <Text style={styles.platformTitle}>Platform API信息 | Platform API Info:</Text>
          <Text style={styles.platformText}>Platform.OS: {Platform.OS}</Text>
          <Text style={styles.platformText}>Platform.Version: {Platform.Version}</Text>
          {Platform.OS === 'ios' && (
            <Text style={styles.platformText}>是否iPad | Is iPad: {Platform.isPad ? 'Yes' : 'No'}</Text>
          )}
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    infoCard: {
      backgroundColor: '#fff',
      padding: 15,
      marginBottom: 10,
      borderRadius: 8,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    label: {
      fontSize: 14,
      color: '#666',
      marginBottom: 5,
    },
    value: {
      fontSize: 16,
      fontWeight: '600',
      color: '#333',
    },
    featureText: {
      fontSize: 14,
      color: '#333',
      marginTop: 5,
    },
    metroInfo: {
      backgroundColor: '#e8f4f8',
      padding: 15,
      marginTop: 20,
      borderRadius: 8,
    },
    metroTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#0066cc',
    },
    metroText: {
      fontSize: 13,
      color: '#333',
      lineHeight: 20,
    },
    platformAPI: {
      backgroundColor: '#fff3cd',
      padding: 15,
      marginTop: 10,
      borderRadius: 8,
    },
    platformTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#856404',
    },
    platformText: {
      fontSize: 14,
      color: '#856404',
      marginTop: 5,
    },
  });

  export default MetroPlatformDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果你同时创建了utils.js、utils.ios.js和utils.android.js三个文件，Metro会选择哪个？| If you create utils.js, utils.ios.js, and utils.android.js simultaneously, which one will Metro select?
    **答案 | Answer:** 在iOS上选择utils.ios.js，在Android上选择utils.android.js，平台特定文件优先于通用文件 | Selects utils.ios.js on iOS, utils.android.js on Android, platform-specific files take precedence over generic files
  - 当你修改组件代码并保存时，Fast Refresh会保留组件的state吗？| When you modify component code and save, does Fast Refresh preserve component state?
    **答案 | Answer:** 是的，大多数情况下会保留，除非修改涉及到组件的export或导致语法错误 | Yes, in most cases it preserves state, unless changes involve component exports or cause syntax errors

  **常见误区检查 | Common Misconception Checks:**
  - Metro是否像Webpack一样可以进行tree-shaking和代码分割？| Does Metro perform tree-shaking and code splitting like Webpack?
    **答案 | Answer:** Metro的tree-shaking能力有限，默认不支持代码分割。但可以通过Hermes引擎和RAM bundles优化 | Metro has limited tree-shaking, no code splitting by default. Can be optimized with Hermes engine and RAM bundles
  - 需要像Webpack一样手动配置loader吗？| Do you need to manually configure loaders like Webpack?
    **答案 | Answer:** 否 - Metro内置支持JS、TypeScript、图片等常见资源，很少需要额外配置 | No - Metro has built-in support for JS, TypeScript, images and other common assets, rarely needs extra configuration

### 4. React Native新架构：JSI、Fabric、TurboModules | React Native New Architecture: JSI, Fabric, TurboModules (1小时 | 1 hour)

- **新架构的核心改进 | Core Improvements of New Architecture**

  **概念定义 | Concept Definition:**
  React Native的新架构旨在解决旧Bridge架构的性能瓶颈。核心包括三个部分：JSI(JavaScript Interface，允许JS和原生代码直接同步通信)、Fabric(新的UI渲染系统)、TurboModules(按需加载的原生模块)。这些改进消除了序列化开销，支持同步调用，并实现了更好的类型安全。 | React Native's new architecture aims to solve performance bottlenecks of the old Bridge architecture. The core includes three parts: JSI (JavaScript Interface, allows direct synchronous communication between JS and native code), Fabric (new UI rendering system), and TurboModules (lazy-loaded native modules). These improvements eliminate serialization overhead, support synchronous calls, and achieve better type safety.

  **核心特征 | Key Characteristics:**
  - JSI允许JavaScript直接调用C++函数，无需JSON序列化 | JSI allows JavaScript to directly call C++ functions without JSON serialization
  - 支持同步方法调用，消除某些场景下的异步延迟 | Supports synchronous method calls, eliminating async latency in certain scenarios
  - Fabric实现了并发渲染，类似React 18的Concurrent Features | Fabric implements concurrent rendering, similar to React 18's Concurrent Features
  - TurboModules按需加载，减少启动时间和内存占用 | TurboModules load on demand, reducing startup time and memory usage
  - 更好的类型安全性，通过Codegen自动生成类型定义 | Better type safety through auto-generated type definitions via Codegen

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 新架构是否完全移除了Bridge？| Does the new architecture completely remove the Bridge?
     **答案 | Answer:** 是的 | Yes - JSI取代了Bridge，JavaScript可以直接持有原生对象的引用 | JSI replaces the Bridge, JavaScript can directly hold references to native objects
  2. JSI是否允许同步调用原生代码？| Does JSI allow synchronous calls to native code?
     **答案 | Answer:** 是的 | Yes - 这是新架构的重要改进，某些操作可以同步执行而不阻塞 | This is a key improvement, certain operations can execute synchronously without blocking
  3. TurboModules和旧的Native Modules有什么区别？| What's the difference between TurboModules and old Native Modules?
     **答案 | Answer:** TurboModules按需延迟加载，只在首次使用时初始化，而旧模块在启动时全部加载 | TurboModules are lazy-loaded on demand, initialized only on first use, while old modules load entirely at startup
  4. Fabric渲染器如何改进UI更新？| How does the Fabric renderer improve UI updates?
     **答案 | Answer:** Fabric支持并发渲染、优先级调度和中断渲染，可以更智能地处理UI更新，避免丢帧 | Fabric supports concurrent rendering, priority scheduling and interruptible rendering, smarter handling of UI updates to avoid frame drops
  5. 新架构是否向后兼容旧的Native Modules？| Is the new architecture backward compatible with old Native Modules?
     **答案 | Answer:** 是的 | Yes - 可以在同一应用中混合使用TurboModules和旧的Native Modules，逐步迁移 | Can mix TurboModules and old Native Modules in the same app, migrate gradually

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 新架构概念演示 - 需要React Native 0.68+启用新架构
  // New Architecture concept demo - requires React Native 0.68+ with new architecture enabled
  import React, { useState, useEffect } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';

  const NewArchitectureDemo = () => {
    const [architectureInfo, setArchitectureInfo] = useState({
      isNewArchEnabled: false,
      isFabricEnabled: false,
      isTurboModulesEnabled: false,
    });

    useEffect(() => {
      // 检测新架构是否启用 | Detect if new architecture is enabled
      // 注意：这些API在旧架构中可能不可用
      // Note: These APIs may not be available in old architecture
      try {
        // 新架构检测方法（示例，实际API可能不同）
        // New architecture detection method (example, actual API may differ)
        const isNewArch = global.nativeFabricUIManager != null;
        const isFabric = global.nativeFabricUIManager != null;
        const isTurbo = global.__turboModuleProxy != null;

        setArchitectureInfo({
          isNewArchEnabled: isNewArch,
          isFabricEnabled: isFabric,
          isTurboModulesEnabled: isTurbo,
        });

        console.log('[New Architecture] Detection results:', {
          isNewArch,
          isFabric,
          isTurbo,
        });
      } catch (error) {
        console.log('[New Architecture] Detection failed:', error);
      }
    }, []);

    // 新架构的优势演示 | Demonstrating new architecture advantages
    const demonstrateJSI = () => {
      console.log('=== JSI优势 | JSI Advantages ===');
      console.log('1. 直接调用原生函数，无需序列化');
      console.log('1. Direct native function calls, no serialization');
      console.log('2. 支持同步返回值');
      console.log('2. Supports synchronous return values');
      console.log('3. 可以传递函数和对象引用');
      console.log('3. Can pass functions and object references');
      console.log('4. 更好的性能，更低的延迟');
      console.log('4. Better performance, lower latency');
    };

    const demonstrateFabric = () => {
      console.log('=== Fabric优势 | Fabric Advantages ===');
      console.log('1. 并发渲染，支持中断和恢复');
      console.log('1. Concurrent rendering, supports interruption and resumption');
      console.log('2. 优先级调度，重要更新优先处理');
      console.log('2. Priority scheduling, important updates processed first');
      console.log('3. 简化的异步渲染流程');
      console.log('3. Simplified async rendering pipeline');
      console.log('4. 更快的启动和交互时间');
      console.log('4. Faster startup and interaction time');
    };

    const demonstrateTurboModules = () => {
      console.log('=== TurboModules优势 | TurboModules Advantages ===');
      console.log('1. 按需加载，不用的模块不加载');
      console.log('1. Load on demand, unused modules not loaded');
      console.log('2. 减少启动时间和内存占用');
      console.log('2. Reduced startup time and memory usage');
      console.log('3. 强类型接口，自动代码生成');
      console.log('3. Strongly-typed interfaces, automatic code generation');
      console.log('4. 更好的开发者体验和调试');
      console.log('4. Better developer experience and debugging');
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>新架构特性演示 | New Architecture Features Demo</Text>

        {/* 架构检测结果 | Architecture detection results */}
        <View style={styles.statusCard}>
          <Text style={styles.statusTitle}>当前架构状态 | Current Architecture Status:</Text>
          <View style={styles.statusRow}>
            <Text style={styles.statusLabel}>新架构启用 | New Arch Enabled:</Text>
            <Text style={[styles.statusValue, architectureInfo.isNewArchEnabled ? styles.enabled : styles.disabled]}>
              {architectureInfo.isNewArchEnabled ? '✓ 是 | Yes' : '✗ 否 | No'}
            </Text>
          </View>
          <View style={styles.statusRow}>
            <Text style={styles.statusLabel}>Fabric启用 | Fabric Enabled:</Text>
            <Text style={[styles.statusValue, architectureInfo.isFabricEnabled ? styles.enabled : styles.disabled]}>
              {architectureInfo.isFabricEnabled ? '✓ 是 | Yes' : '✗ 否 | No'}
            </Text>
          </View>
          <View style={styles.statusRow}>
            <Text style={styles.statusLabel}>TurboModules启用 | TurboModules Enabled:</Text>
            <Text style={[styles.statusValue, architectureInfo.isTurboModulesEnabled ? styles.enabled : styles.disabled]}>
              {architectureInfo.isTurboModulesEnabled ? '✓ 是 | Yes' : '✗ 否 | No'}
            </Text>
          </View>
        </View>

        {/* 新架构组件演示 | New architecture components demo */}
        <View style={styles.demoSection}>
          <Text style={styles.sectionTitle}>新架构组件 | New Architecture Components:</Text>

          <Button
            title="JSI优势 | JSI Advantages"
            onPress={demonstrateJSI}
            color="#4CAF50"
          />

          <View style={styles.buttonSpacer} />

          <Button
            title="Fabric优势 | Fabric Advantages"
            onPress={demonstrateFabric}
            color="#2196F3"
          />

          <View style={styles.buttonSpacer} />

          <Button
            title="TurboModules优势 | TurboModules Advantages"
            onPress={demonstrateTurboModules}
            color="#FF9800"
          />
        </View>

        {/* 架构对比 | Architecture comparison */}
        <View style={styles.comparisonCard}>
          <Text style={styles.comparisonTitle}>架构对比 | Architecture Comparison:</Text>
          <Text style={styles.comparisonText}>
            <Text style={styles.bold}>旧架构 | Old Architecture:{'\n'}</Text>
            • Bridge异步通信 | Bridge async communication{'\n'}
            • JSON序列化开销 | JSON serialization overhead{'\n'}
            • 所有模块启动时加载 | All modules load at startup{'\n'}
            • 类型安全较弱 | Weaker type safety{'\n\n'}
            <Text style={styles.bold}>新架构 | New Architecture:{'\n'}</Text>
            • JSI直接通信 | JSI direct communication{'\n'}
            • 零序列化开销 | Zero serialization overhead{'\n'}
            • 按需加载模块 | Lazy load modules{'\n'}
            • 强类型安全 | Strong type safety
          </Text>
        </View>

        <View style={styles.noteBox}>
          <Text style={styles.noteText}>
            💡 查看终端输出了解更多细节{'\n'}
            💡 Check terminal output for more details{'\n\n'}
            📖 新架构在React Native 0.68+默认可用{'\n'}
            📖 New architecture available by default in React Native 0.68+
          </Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
      color: '#333',
    },
    statusCard: {
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    statusTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#333',
    },
    statusRow: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      paddingVertical: 8,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    statusLabel: {
      fontSize: 14,
      color: '#666',
    },
    statusValue: {
      fontSize: 14,
      fontWeight: '600',
    },
    enabled: {
      color: '#4CAF50',
    },
    disabled: {
      color: '#F44336',
    },
    demoSection: {
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#333',
    },
    buttonSpacer: {
      height: 10,
    },
    comparisonCard: {
      backgroundColor: '#e8f4f8',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
    },
    comparisonTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#0066cc',
    },
    comparisonText: {
      fontSize: 13,
      lineHeight: 22,
      color: '#333',
    },
    bold: {
      fontWeight: 'bold',
    },
    noteBox: {
      backgroundColor: '#fff3cd',
      padding: 15,
      borderRadius: 8,
    },
    noteText: {
      fontSize: 13,
      color: '#856404',
      lineHeight: 20,
    },
  });

  export default NewArchitectureDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 新架构中，JavaScript能直接持有原生对象的引用吗？| In the new architecture, can JavaScript directly hold references to native objects?
    **答案 | Answer:** 是的，通过JSI可以持有C++对象的引用，不需要序列化 | Yes, through JSI you can hold references to C++ objects without serialization
  - 启用新架构后，应用的启动速度会变快还是变慢？| After enabling the new architecture, will app startup be faster or slower?
    **答案 | Answer:** 通常会变快，因为TurboModules按需加载，减少了启动时的初始化工作 | Usually faster, because TurboModules load on demand, reducing initialization work at startup

  **常见误区检查 | Common Misconception Checks:**
  - 新架构是否向后兼容，可以在旧项目中直接启用？| Is the new architecture backward compatible, can it be directly enabled in old projects?
    **答案 | Answer:** 大部分兼容，但某些第三方库可能需要更新。建议在新项目中使用或仔细测试后迁移 | Mostly compatible, but some third-party libraries may need updates. Recommended for new projects or migrate after careful testing
  - JSI是否意味着所有操作都应该是同步的？| Does JSI mean all operations should be synchronous?
    **答案 | Answer:** 否 - JSI支持同步调用，但不意味着应该滥用。耗时操作仍应异步执行以避免阻塞UI线程 | No - JSI supports synchronous calls, but doesn't mean they should be overused. Time-consuming operations should still be async to avoid blocking the UI thread

### 5. 性能影响与调试策略 | Performance Impact and Debugging Strategies (45分钟 | 45 minutes)

- **架构对性能的影响 | Architecture's Impact on Performance**

  **概念定义 | Concept Definition:**
  React Native的架构直接影响应用性能。理解线程模型、Bridge通信、渲染流程对于识别性能瓶颈至关重要。常见的性能问题包括：JavaScript线程阻塞导致的UI冻结、频繁的Bridge通信导致的丢帧、大型列表渲染导致的内存问题。 | React Native's architecture directly impacts app performance. Understanding the thread model, Bridge communication, and rendering pipeline is crucial for identifying performance bottlenecks. Common performance issues include: UI freeze from blocked JavaScript thread, frame drops from frequent Bridge communication, memory issues from large list rendering.

  **核心特征 | Key Characteristics:**
  - JavaScript线程上的长时间同步操作会冻结UI | Long synchronous operations on JavaScript thread freeze UI
  - 频繁的setState和Bridge调用会累积延迟 | Frequent setState and Bridge calls accumulate latency
  - 使用console.log查看不同线程的输出位置 | Use console.log to see output locations from different threads
  - Performance Monitor和Flipper是主要调试工具 | Performance Monitor and Flipper are primary debugging tools
  - 理解60fps目标：每帧必须在16.67ms内完成 | Understanding 60fps target: each frame must complete within 16.67ms

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果JavaScript线程被阻塞1秒，用户能否滚动列表？| If JavaScript thread is blocked for 1 second, can users scroll a list?
     **答案 | Answer:** 取决于实现 - 如果使用原生驱动的滚动(如FlatList)，可以；如果是JavaScript控制的滚动，不能 | Depends on implementation - if using natively-driven scroll (like FlatList), yes; if JavaScript-controlled scroll, no
  2. 为什么过度使用console.log会影响性能？| Why does excessive console.log usage affect performance?
     **答案 | Answer:** 每个console.log都需要序列化数据并通过Bridge发送到调试器，频繁调用会增加开销 | Each console.log requires serializing data and sending through Bridge to debugger, frequent calls add overhead
  3. 如何判断丢帧是由JavaScript线程还是UI线程引起的？| How to determine if frame drops are caused by JavaScript thread or UI thread?
     **答案 | Answer:** 使用Performance Monitor：JS帧率低表示JavaScript线程问题，UI帧率低表示原生UI线程问题 | Use Performance Monitor: low JS frame rate indicates JavaScript thread issues, low UI frame rate indicates native UI thread issues
  4. setState是否应该在循环中频繁调用？| Should setState be frequently called in loops?
     **答案 | Answer:** 否 - 应该批处理更新，在循环结束后一次性setState，减少Bridge通信次数 | No - should batch updates, setState once after loop ends, reducing Bridge communication frequency

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, Text, Button, StyleSheet, Alert } from 'react-native';

  const PerformanceDebugDemo = () => {
    const [result, setResult] = useState('');
    const [jsFrameRate, setJsFrameRate] = useState(60);

    // 演示阻塞JavaScript线程的不良实践 | Demonstrating bad practice that blocks JavaScript thread
    const blockingOperation = () => {
      console.log('[Performance] Starting blocking operation...');
      const startTime = Date.now();

      // 模拟耗时的同步计算 | Simulating time-consuming synchronous calculation
      let sum = 0;
      for (let i = 0; i < 100000000; i++) {
        sum += i;
      }

      const duration = Date.now() - startTime;
      console.log(`[Performance] Blocking operation took ${duration}ms`);
      console.log('[Performance] UI was frozen during this time!');

      setResult(`计算完成，耗时${duration}ms | Completed in ${duration}ms`);
      Alert.alert(
        '性能警告 | Performance Warning',
        `UI被冻结了${duration}ms！在真实应用中这会导致糟糕的用户体验。\n\nUI was frozen for ${duration}ms! This causes poor UX in real apps.`
      );
    };

    // 更好的做法：使用setTimeout分块处理 | Better approach: chunked processing with setTimeout
    const nonBlockingOperation = () => {
      console.log('[Performance] Starting non-blocking operation...');
      const startTime = Date.now();
      let sum = 0;
      let i = 0;
      const chunkSize = 10000000; // 每次处理1000万次 | Process 10 million at a time

      const processChunk = () => {
        const chunkEnd = Math.min(i + chunkSize, 100000000);
        for (; i < chunkEnd; i++) {
          sum += i;
        }

        // 更新进度 | Update progress
        const progress = Math.round((i / 100000000) * 100);
        setResult(`处理中... ${progress}% | Processing... ${progress}%`);

        if (i < 100000000) {
          // 使用setTimeout让出控制权，避免阻塞 | Use setTimeout to yield control, avoid blocking
          setTimeout(processChunk, 0);
        } else {
          const duration = Date.now() - startTime;
          console.log(`[Performance] Non-blocking operation took ${duration}ms`);
          console.log('[Performance] UI remained responsive!');
          setResult(`计算完成(非阻塞)，耗时${duration}ms | Completed (non-blocking) in ${duration}ms`);
        }
      };

      processChunk();
    };

    // 演示过度的Bridge通信 | Demonstrating excessive Bridge communication
    const excessiveBridgeCalls = () => {
      console.log('[Performance] Starting excessive Bridge calls...');
      const startTime = Date.now();

      // 不良实践：在循环中多次调用setState | Bad practice: multiple setState calls in loop
      for (let i = 0; i < 100; i++) {
        setResult(`更新 ${i} | Update ${i}`);
        // 每次setState都会触发Bridge通信和重新渲染
        // Each setState triggers Bridge communication and re-render
      }

      const duration = Date.now() - startTime;
      console.log(`[Performance] Excessive Bridge calls took ${duration}ms`);
      Alert.alert(
        '性能问题 | Performance Issue',
        `进行了100次setState调用，每次都通过Bridge通信！\n\nMade 100 setState calls, each communicates through Bridge!`
      );
    };

    // 更好的做法：批处理更新 | Better approach: batched updates
    const batchedUpdates = () => {
      console.log('[Performance] Starting batched updates...');
      const startTime = Date.now();

      // 先在本地累积更新 | First accumulate updates locally
      let updates = [];
      for (let i = 0; i < 100; i++) {
        updates.push(`更新 ${i} | Update ${i}`);
      }

      // 一次性更新，只触发一次Bridge通信 | Single update, only one Bridge communication
      setResult(updates[updates.length - 1]);

      const duration = Date.now() - startTime;
      console.log(`[Performance] Batched updates took ${duration}ms`);
      console.log('[Performance] Only 1 Bridge call instead of 100!');
    };

    // 性能监控提示 | Performance monitoring tips
    const showPerformanceTips = () => {
      Alert.alert(
        '性能调试技巧 | Performance Debugging Tips',
        '1. 使用Performance Monitor查看帧率 (开发菜单 → Perf Monitor)\n' +
        '2. 使用Flipper查看详细性能数据\n' +
        '3. console.log输出在Metro终端，不是浏览器\n' +
        '4. 避免在渲染方法中进行耗时计算\n' +
        '5. 使用useMemo和useCallback减少重新渲染\n\n' +
        '1. Use Performance Monitor to check frame rate (Dev Menu → Perf Monitor)\n' +
        '2. Use Flipper for detailed performance data\n' +
        '3. console.log outputs in Metro terminal, not browser\n' +
        '4. Avoid time-consuming calculations in render methods\n' +
        '5. Use useMemo and useCallback to reduce re-renders'
      );
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>性能调试演示 | Performance Debugging Demo</Text>

        <View style={styles.resultBox}>
          <Text style={styles.resultText}>{result || '点击按钮开始测试 | Click button to start test'}</Text>
        </View>

        <View style={styles.section}>
          <Text style={styles.sectionTitle}>JavaScript线程阻塞 | JavaScript Thread Blocking:</Text>
          <Button
            title="❌ 阻塞操作 | Blocking Operation"
            onPress={blockingOperation}
            color="#F44336"
          />
          <View style={styles.buttonSpacer} />
          <Button
            title="✅ 非阻塞操作 | Non-blocking Operation"
            onPress={nonBlockingOperation}
            color="#4CAF50"
          />
        </View>

        <View style={styles.section}>
          <Text style={styles.sectionTitle}>Bridge通信优化 | Bridge Communication Optimization:</Text>
          <Button
            title="❌ 过度Bridge调用 | Excessive Bridge Calls"
            onPress={excessiveBridgeCalls}
            color="#F44336"
          />
          <View style={styles.buttonSpacer} />
          <Button
            title="✅ 批处理更新 | Batched Updates"
            onPress={batchedUpdates}
            color="#4CAF50"
          />
        </View>

        <View style={styles.section}>
          <Button
            title="💡 性能调试技巧 | Performance Tips"
            onPress={showPerformanceTips}
            color="#2196F3"
          />
        </View>

        <View style={styles.monitorInfo}>
          <Text style={styles.monitorTitle}>📊 启用性能监控 | Enable Performance Monitor:</Text>
          <Text style={styles.monitorText}>
            1. 摇晃设备或按Cmd+D(iOS)/Cmd+M(Android){'\n'}
            2. 选择"Perf Monitor"或"Show Perf Monitor"{'\n'}
            3. 观察JS帧率和UI帧率{'\n'}
            4. 目标：保持两者都在60fps{'\n\n'}
            1. Shake device or press Cmd+D(iOS)/Cmd+M(Android){'\n'}
            2. Select "Perf Monitor" or "Show Perf Monitor"{'\n'}
            3. Observe JS frame rate and UI frame rate{'\n'}
            4. Goal: Keep both at 60fps
          </Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
      color: '#333',
    },
    resultBox: {
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
      marginBottom: 20,
      minHeight: 60,
      justifyContent: 'center',
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    resultText: {
      fontSize: 14,
      color: '#333',
      textAlign: 'center',
    },
    section: {
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#333',
    },
    buttonSpacer: {
      height: 10,
    },
    monitorInfo: {
      backgroundColor: '#e8f4f8',
      padding: 15,
      borderRadius: 8,
      marginTop: 10,
    },
    monitorTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#0066cc',
    },
    monitorText: {
      fontSize: 13,
      color: '#333',
      lineHeight: 20,
    },
  });

  export default PerformanceDebugDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 运行阻塞操作时，你能否点击其他按钮？| When running blocking operation, can you click other buttons?
    **答案 | Answer:** 不能，UI完全冻结直到操作完成 | No, UI is completely frozen until operation completes
  - 非阻塞操作期间，UI是否仍然响应？| During non-blocking operation, is UI still responsive?
    **答案 | Answer:** 是的，因为使用setTimeout分块处理，定期让出控制权给UI线程 | Yes, because using setTimeout for chunked processing, regularly yielding control to UI thread

  **常见误区检查 | Common Misconception Checks:**
  - console.log输出会出现在浏览器控制台吗？| Does console.log output appear in browser console?
    **答案 | Answer:** 否 - 在Metro bundler的终端中，因为JavaScript运行在不同的环境 | No - in Metro bundler terminal, because JavaScript runs in a different environment
  - 所有性能问题都是由JavaScript代码引起的吗？| Are all performance issues caused by JavaScript code?
    **答案 | Answer:** 否 - 也可能是原生UI线程问题、复杂布局、大图片渲染等原生侧的问题 | No - could also be native UI thread issues, complex layouts, large image rendering, etc. on the native side

### 6. 架构知识在实际开发中的应用 | Applying Architecture Knowledge in Real Development (30分钟 | 30 minutes)

- **架构意识驱动的开发实践 | Architecture-Aware Development Practices**

  **概念定义 | Concept Definition:**
  理解React Native架构不仅是理论知识，更应该指导日常开发决策。架构意识帮助你：选择合适的状态管理策略、优化组件渲染、避免性能陷阱、更有效地调试问题。将架构知识应用到实际编码中是高级React Native开发者的标志。 | Understanding React Native architecture is not just theoretical knowledge, but should guide daily development decisions. Architecture awareness helps you: choose appropriate state management strategies, optimize component rendering, avoid performance pitfalls, debug issues more effectively. Applying architecture knowledge to actual coding is the hallmark of advanced React Native developers.

  **架构驱动的最佳实践 | Architecture-Driven Best Practices:**
  - 最小化Bridge通信：批处理状态更新，使用原生驱动的动画 | Minimize Bridge communication: batch state updates, use natively-driven animations
  - 保持JavaScript线程畅通：避免同步耗时操作，使用异步API | Keep JavaScript thread clear: avoid synchronous time-consuming operations, use async APIs
  - 理解渲染流程：优化组件树，使用memo和useMemo减少不必要渲染 | Understand rendering pipeline: optimize component tree, use memo and useMemo to reduce unnecessary renders
  - 平台特定优化：利用Metro的平台文件分离，针对iOS/Android优化 | Platform-specific optimization: leverage Metro's platform file separation, optimize for iOS/Android separately

  **实践验证问题 | Practice Verification Questions:**
  1. 在开发一个复杂列表时，应该考虑哪些架构因素？| When developing a complex list, what architecture factors should be considered?
     **答案 | Answer:** 使用FlatList的原生优化、虚拟化避免渲染所有项、keyExtractor优化、避免在renderItem中创建新函数 | Use FlatList's native optimizations, virtualization to avoid rendering all items, keyExtractor optimization, avoid creating new functions in renderItem
  2. 为什么动画应该使用`useNativeDriver: true`？| Why should animations use `useNativeDriver: true`?
     **答案 | Answer:** 动画在原生线程执行，不经过Bridge，即使JavaScript线程繁忙也能保持流畅 | Animations execute on native thread without going through Bridge, remain smooth even when JavaScript thread is busy
  3. 何时应该考虑将代码拆分到.ios.js和.android.js文件？| When should you consider splitting code into .ios.js and .android.js files?
     **答案 | Answer:** 当平台间差异显著、需要不同的实现逻辑或导入不同的原生模块时 | When platform differences are significant, need different implementation logic, or import different native modules

  **综合应用检查 | Comprehensive Application Check:**
  - 如何设计一个性能优化的图片浏览应用？| How to design a performance-optimized image viewer app?
    **答案 | Answer:** 使用FlatList虚拟化、Image组件的缓存、懒加载、原生驱动的缩放手势、避免在JS中处理大图 | Use FlatList virtualization, Image component caching, lazy loading, natively-driven zoom gestures, avoid processing large images in JS
  - 在什么情况下应该优先考虑使用TurboModules？| In what situations should you prioritize using TurboModules?
    **答案 | Answer:** 需要频繁调用原生功能、启动性能至关重要、需要同步返回值的场景 | Scenarios requiring frequent native function calls, where startup performance is critical, or synchronous return values are needed

## 实践项目：架构探索器 | Practical Project: Architecture Explorer

### 目标 | Objective
构建一个"架构探索器"应用，通过实际代码演示React Native的架构概念，包括线程模型、Bridge通信、Metro打包、性能监控。这个项目将所有今日学习的概念整合到一个可运行的应用中，帮助你在实践中巩固理解。 | Build an "Architecture Explorer" app that demonstrates React Native architecture concepts through actual code, including thread model, Bridge communication, Metro bundling, and performance monitoring. This project integrates all today's learned concepts into a runnable app to help solidify understanding through practice.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. React Native应用运行在几个主要线程上？各自的职责是什么？| How many main threads does a React Native app run on? What are their responsibilities?
   **答案 | Answer:** 三个主要线程 - JavaScript线程(执行React代码)、原生UI线程(渲染和用户交互)、Shadow线程(布局计算) | Three main threads - JavaScript thread (executes React code), Native UI thread (rendering and user interaction), Shadow thread (layout calculation)

2. Bridge通信的主要限制是什么？| What are the main limitations of Bridge communication?
   **答案 | Answer:** 异步通信、必须JSON序列化、不能传递函数、存在性能开销 | Asynchronous communication, must be JSON-serializable, cannot pass functions, has performance overhead

3. 为什么console.log不会出现在浏览器控制台？| Why doesn't console.log appear in browser console?
   **答案 | Answer:** React Native的JavaScript代码运行在独立的环境中(通过Metro)，不是浏览器环境 | React Native's JavaScript code runs in a standalone environment (via Metro), not a browser environment

### 步骤 | Steps

1. **项目初始化 | Project Initialization**
   - 创建新的Expo项目或在现有项目中创建新组件 | Create new Expo project or new component in existing project
   - 设置基础导航结构(如果需要多个演示页面) | Set up basic navigation structure (if multiple demo pages needed)

2. **实现线程演示模块 | Implement Thread Demo Module**
   - 创建按钮触发JavaScript线程上的操作 | Create buttons to trigger operations on JavaScript thread
   - 使用console.log记录操作时间戳 | Use console.log to log operation timestamps
   - 演示阻塞vs非阻塞操作 | Demonstrate blocking vs non-blocking operations

3. **实现Bridge通信演示 | Implement Bridge Communication Demo**
   - 演示可序列化和不可序列化的数据类型 | Demonstrate serializable and non-serializable data types
   - 展示批处理优化的效果 | Show effects of batch optimization
   - 使用Alert等原生模块触发Bridge调用 | Use native modules like Alert to trigger Bridge calls

4. **实现Metro特性演示 | Implement Metro Features Demo**
   - 创建平台特定文件(.ios.js/.android.js) | Create platform-specific files (.ios.js/.android.js)
   - 展示Platform API的使用 | Show usage of Platform API
   - 演示Fast Refresh功能 | Demonstrate Fast Refresh functionality

5. **性能监控集成 | Performance Monitoring Integration**
   - 添加性能提示和最佳实践建议 | Add performance tips and best practice recommendations
   - 创建性能测试按钮(阻塞操作、频繁更新等) | Create performance test buttons (blocking operations, frequent updates, etc.)
   - 引导用户使用Performance Monitor | Guide users to use Performance Monitor

### 示例代码 | Example Code

```javascript
/**
 * 架构探索器 | Architecture Explorer
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - React Native三线程模型 | React Native three-thread model
 * - Bridge通信机制 | Bridge communication mechanism
 * - Metro打包特性 | Metro bundler features
 * - 性能优化策略 | Performance optimization strategies
 */

import React, { useState } from 'react';
import {
  View,
  Text,
  StyleSheet,
  ScrollView,
  TouchableOpacity,
  Platform,
  Alert,
} from 'react-native';

// 导入平台特定的工具函数 | Import platform-specific utility functions
// Metro会根据平台自动选择正确的文件 | Metro automatically selects the correct file based on platform
import { getPlatformFeatures } from './platformUtils';

const ArchitectureExplorer = () => {
  const [activeDemo, setActiveDemo] = useState('home');
  const [demoResult, setDemoResult] = useState('');
  const [performanceLog, setPerformanceLog] = useState([]);

  // 添加性能日志 | Add performance log
  const addLog = (message) => {
    const timestamp = new Date().toISOString().split('T')[1].slice(0, -1);
    const logEntry = `[${timestamp}] ${message}`;
    console.log(logEntry);
    setPerformanceLog((prev) => [...prev.slice(-9), logEntry]);
  };

  // ========== 线程演示 | Thread Demonstrations ==========

  const demoBlockingOperation = () => {
    addLog('Starting BLOCKING operation on JS thread');
    const start = Date.now();

    // 阻塞JavaScript线程 | Block JavaScript thread
    let sum = 0;
    for (let i = 0; i < 100000000; i++) {
      sum += i;
    }

    const duration = Date.now() - start;
    addLog(`Blocking operation completed in ${duration}ms`);
    setDemoResult(`❌ UI was frozen for ${duration}ms`);

    Alert.alert(
      'JavaScript线程阻塞 | JS Thread Blocked',
      `UI冻结了${duration}ms。注意在执行期间你无法与应用交互！\n\nUI froze for ${duration}ms. Notice you couldn't interact with the app during execution!`,
      [{ text: 'OK' }]
    );
  };

  const demoNonBlockingOperation = () => {
    addLog('Starting NON-BLOCKING operation on JS thread');
    const start = Date.now();
    let sum = 0;
    let i = 0;
    const total = 100000000;
    const chunkSize = 10000000;

    const processChunk = () => {
      const end = Math.min(i + chunkSize, total);
      for (; i < end; i++) {
        sum += i;
      }

      const progress = Math.round((i / total) * 100);
      setDemoResult(`✅ Processing... ${progress}% (UI responsive!)`);

      if (i < total) {
        setTimeout(processChunk, 0); // 让出控制权 | Yield control
      } else {
        const duration = Date.now() - start;
        addLog(`Non-blocking operation completed in ${duration}ms`);
        setDemoResult(`✅ Completed in ${duration}ms, UI stayed responsive!`);
      }
    };

    processChunk();
  };

  // ========== Bridge演示 | Bridge Demonstrations ==========

  const demoBridgeSerialization = () => {
    addLog('Testing Bridge serialization limits');

    const validData = {
      string: 'Hello',
      number: 42,
      boolean: true,
      array: [1, 2, 3],
      nested: { key: 'value' },
    };

    const invalidData = {
      function: () => console.log('This will disappear'),
      date: new Date(),
      undefined: undefined,
    };

    addLog('Valid data: ' + JSON.stringify(validData));
    addLog('Invalid data before: ' + JSON.stringify(invalidData));

    // 演示序列化后的结果 | Show result after serialization
    const serialized = JSON.parse(JSON.stringify(invalidData));
    addLog('Invalid data after: ' + JSON.stringify(serialized));

    setDemoResult(
      '✅ Check logs: function & undefined disappeared!\n' +
      'Bridge只能传递JSON-serializable数据 | Bridge can only pass JSON-serializable data'
    );
  };

  const demoBatchUpdates = () => {
    addLog('Testing batched vs unbatched updates');

    // 不良实践：多次setState | Bad practice: multiple setState
    const start1 = Date.now();
    for (let i = 0; i < 50; i++) {
      setDemoResult(`❌ Unbatched update ${i}`);
    }
    const duration1 = Date.now() - start1;
    addLog(`Unbatched: ${duration1}ms for 50 updates`);

    setTimeout(() => {
      // 良好实践：批处理 | Good practice: batching
      const start2 = Date.now();
      const updates = [];
      for (let i = 0; i < 50; i++) {
        updates.push(i);
      }
      setDemoResult(`✅ Batched update: ${updates.length} updates in one call`);
      const duration2 = Date.now() - start2;
      addLog(`Batched: ${duration2}ms for 50 updates`);
      addLog(`Performance improvement: ${((duration1 - duration2) / duration1 * 100).toFixed(1)}%`);
    }, 100);
  };

  // ========== Metro演示 | Metro Demonstrations ==========

  const demoMetroPlatformResolution = () => {
    const features = getPlatformFeatures();
    addLog(`Metro resolved platform: ${Platform.OS}`);
    addLog(`Platform features: ${features.join(', ')}`);

    setDemoResult(
      `📱 Platform: ${Platform.OS}\n` +
      `✨ Features from platform-specific file:\n` +
      features.map(f => `  • ${f}`).join('\n') +
      '\n\nMetro automatically selected the correct .ios.js or .android.js file!'
    );
  };

  // ========== 渲染主界面 | Render Main UI ==========

  return (
    <ScrollView style={styles.container}>
      {/* Header */}
      <View style={styles.header}>
        <Text style={styles.headerTitle}>架构探索器 | Architecture Explorer</Text>
        <Text style={styles.headerSubtitle}>
          React Native Architecture Demonstration
        </Text>
      </View>

      {/* Demo Categories */}
      <View style={styles.categoriesContainer}>
        <DemoCategory
          title="1️⃣ 线程模型 | Thread Model"
          description="JavaScript, UI, Shadow线程交互"
          active={activeDemo === 'thread'}
          onPress={() => setActiveDemo('thread')}
        />
        <DemoCategory
          title="2️⃣ Bridge通信 | Bridge Communication"
          description="序列化、批处理、性能影响"
          active={activeDemo === 'bridge'}
          onPress={() => setActiveDemo('bridge')}
        />
        <DemoCategory
          title="3️⃣ Metro打包 | Metro Bundler"
          description="平台特定文件、Fast Refresh"
          active={activeDemo === 'metro'}
          onPress={() => setActiveDemo('metro')}
        />
      </View>

      {/* Demo Content */}
      <View style={styles.demoContent}>
        {activeDemo === 'thread' && (
          <View>
            <DemoButton
              title="❌ 阻塞操作 | Blocking Operation"
              description="冻结UI - 不良实践"
              onPress={demoBlockingOperation}
              color="#F44336"
            />
            <DemoButton
              title="✅ 非阻塞操作 | Non-blocking Operation"
              description="保持UI响应 - 最佳实践"
              onPress={demoNonBlockingOperation}
              color="#4CAF50"
            />
          </View>
        )}

        {activeDemo === 'bridge' && (
          <View>
            <DemoButton
              title="🔍 序列化限制 | Serialization Limits"
              description="测试哪些数据可以通过Bridge"
              onPress={demoBridgeSerialization}
              color="#2196F3"
            />
            <DemoButton
              title="📦 批处理优化 | Batch Optimization"
              description="对比批处理vs频繁更新"
              onPress={demoBatchUpdates}
              color="#FF9800"
            />
          </View>
        )}

        {activeDemo === 'metro' && (
          <View>
            <DemoButton
              title="📱 平台解析 | Platform Resolution"
              description="查看Metro如何选择平台文件"
              onPress={demoMetroPlatformResolution}
              color="#9C27B0"
            />
          </View>
        )}

        {activeDemo === 'home' && (
          <View style={styles.welcomeBox}>
            <Text style={styles.welcomeText}>
              👆 选择上方类别开始探索{'\n'}
              👆 Select a category above to start exploring{'\n\n'}
              💡 打开Performance Monitor (Cmd+D → Perf Monitor){'\n'}
              💡 Open Performance Monitor (Cmd+D → Perf Monitor){'\n\n'}
              📊 观察JS和UI帧率的变化{'\n'}
              📊 Observe changes in JS and UI frame rates
            </Text>
          </View>
        )}

        {/* Result Display */}
        {demoResult && (
          <View style={styles.resultBox}>
            <Text style={styles.resultTitle}>结果 | Result:</Text>
            <Text style={styles.resultText}>{demoResult}</Text>
          </View>
        )}
      </View>

      {/* Performance Log */}
      <View style={styles.logContainer}>
        <Text style={styles.logTitle}>📋 性能日志 | Performance Log:</Text>
        {performanceLog.length === 0 ? (
          <Text style={styles.logEmpty}>运行演示后日志将显示在这里 | Logs appear here after running demos</Text>
        ) : (
          performanceLog.map((log, index) => (
            <Text key={index} style={styles.logText}>{log}</Text>
          ))
        )}
      </View>

      {/* Info Footer */}
      <View style={styles.infoFooter}>
        <Text style={styles.infoText}>
          💡 提示 | Tips:{'\n'}
          • 所有日志同时输出到Metro终端 | All logs also output to Metro terminal{'\n'}
          • 尝试在阻塞操作期间点击按钮 | Try clicking buttons during blocking operations{'\n'}
          • 观察非阻塞操作的进度更新 | Observe progress updates in non-blocking operations{'\n'}
          • 检查终端查看更详细的日志 | Check terminal for more detailed logs
        </Text>
      </View>
    </ScrollView>
  );
};

// ========== 辅助组件 | Helper Components ==========

const DemoCategory = ({ title, description, active, onPress }) => (
  <TouchableOpacity
    style={[styles.categoryCard, active && styles.categoryCardActive]}
    onPress={onPress}
  >
    <Text style={[styles.categoryTitle, active && styles.categoryTitleActive]}>
      {title}
    </Text>
    <Text style={[styles.categoryDesc, active && styles.categoryDescActive]}>
      {description}
    </Text>
  </TouchableOpacity>
);

const DemoButton = ({ title, description, onPress, color }) => (
  <TouchableOpacity
    style={[styles.demoButton, { borderLeftColor: color }]}
    onPress={onPress}
  >
    <Text style={styles.demoButtonTitle}>{title}</Text>
    <Text style={styles.demoButtonDesc}>{description}</Text>
  </TouchableOpacity>
);

// ========== 样式 | Styles ==========

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  header: {
    backgroundColor: '#2196F3',
    padding: 20,
    paddingTop: Platform.OS === 'ios' ? 60 : 20,
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#fff',
    marginBottom: 5,
  },
  headerSubtitle: {
    fontSize: 14,
    color: '#E3F2FD',
  },
  categoriesContainer: {
    padding: 15,
  },
  categoryCard: {
    backgroundColor: '#fff',
    padding: 15,
    marginBottom: 10,
    borderRadius: 8,
    borderLeftWidth: 4,
    borderLeftColor: '#ddd',
  },
  categoryCardActive: {
    borderLeftColor: '#2196F3',
    backgroundColor: '#E3F2FD',
  },
  categoryTitle: {
    fontSize: 16,
    fontWeight: '600',
    color: '#333',
    marginBottom: 5,
  },
  categoryTitleActive: {
    color: '#1976D2',
  },
  categoryDesc: {
    fontSize: 13,
    color: '#666',
  },
  categoryDescActive: {
    color: '#1976D2',
  },
  demoContent: {
    padding: 15,
  },
  demoButton: {
    backgroundColor: '#fff',
    padding: 15,
    marginBottom: 10,
    borderRadius: 8,
    borderLeftWidth: 4,
  },
  demoButtonTitle: {
    fontSize: 16,
    fontWeight: '600',
    color: '#333',
    marginBottom: 5,
  },
  demoButtonDesc: {
    fontSize: 13,
    color: '#666',
  },
  welcomeBox: {
    backgroundColor: '#fff3cd',
    padding: 20,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: '#ffc107',
  },
  welcomeText: {
    fontSize: 14,
    color: '#856404',
    lineHeight: 22,
  },
  resultBox: {
    backgroundColor: '#e8f5e9',
    padding: 15,
    marginTop: 15,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: '#4CAF50',
  },
  resultTitle: {
    fontSize: 14,
    fontWeight: 'bold',
    color: '#2E7D32',
    marginBottom: 8,
  },
  resultText: {
    fontSize: 13,
    color: '#1B5E20',
    lineHeight: 20,
  },
  logContainer: {
    backgroundColor: '#fff',
    margin: 15,
    padding: 15,
    borderRadius: 8,
    minHeight: 150,
  },
  logTitle: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 10,
  },
  logEmpty: {
    fontSize: 13,
    color: '#999',
    fontStyle: 'italic',
  },
  logText: {
    fontSize: 12,
    fontFamily: Platform.OS === 'ios' ? 'Courier' : 'monospace',
    color: '#333',
    marginBottom: 3,
  },
  infoFooter: {
    backgroundColor: '#e8f4f8',
    padding: 15,
    margin: 15,
    marginTop: 0,
    borderRadius: 8,
  },
  infoText: {
    fontSize: 13,
    color: '#0277BD',
    lineHeight: 20,
  },
});

export default ArchitectureExplorer;
```

**platformUtils.ios.js** (iOS特定文件 | iOS-specific file):
```javascript
/**
 * iOS平台特定功能 | iOS Platform-Specific Features
 * Metro会在iOS上自动导入此文件 | Metro automatically imports this file on iOS
 */

export const getPlatformFeatures = () => {
  console.log('[Metro] Loaded platformUtils.ios.js');
  return [
    'Face ID / Touch ID',
    'Apple Pay',
    'HealthKit Integration',
    'Apple Watch Support',
    'AirDrop',
    'iCloud Sync',
  ];
};

export const getPlatformName = () => 'iOS';

export const getPlatformSpecificMessage = () =>
  'This message comes from the iOS-specific file!';
```

**platformUtils.android.js** (Android特定文件 | Android-specific file):
```javascript
/**
 * Android平台特定功能 | Android Platform-Specific Features
 * Metro会在Android上自动导入此文件 | Metro automatically imports this file on Android
 */

export const getPlatformFeatures = () => {
  console.log('[Metro] Loaded platformUtils.android.js');
  return [
    'Google Pay',
    'Android Auto',
    'Google Play Services',
    'Wear OS Support',
    'Android Beam',
    'Google Fit Integration',
  ];
};

export const getPlatformName = () => 'Android';

export const getPlatformSpecificMessage = () =>
  'This message comes from the Android-specific file!';
```

### 项目完成检查 | Project Completion Check

1. 应用是否成功演示了JavaScript线程阻塞的影响？| Does the app successfully demonstrate the impact of JavaScript thread blocking?
   - ✅ 阻塞操作期间UI应该完全冻结 | UI should freeze completely during blocking operation
   - ✅ 非阻塞操作期间UI保持响应 | UI remains responsive during non-blocking operation

2. Bridge序列化演示是否正确显示了哪些数据类型无法传递？| Does the Bridge serialization demo correctly show which data types cannot be passed?
   - ✅ 函数和undefined应该在序列化后消失 | Functions and undefined should disappear after serialization
   - ✅ 日志应该清楚显示序列化前后的差异 | Logs should clearly show differences before/after serialization

3. Metro是否正确解析了平台特定文件？| Does Metro correctly resolve platform-specific files?
   - ✅ iOS上应该显示iOS特定的功能列表 | Should show iOS-specific feature list on iOS
   - ✅ Android上应该显示Android特定的功能列表 | Should show Android-specific feature list on Android

4. 性能日志是否正确记录了所有操作？| Are performance logs correctly recording all operations?
   - ✅ 每个演示操作都应该在日志中有时间戳记录 | Each demo operation should have timestamped entries in logs
   - ✅ 同时在Metro终端也应该能看到相同的日志 | Same logs should also be visible in Metro terminal

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **线程模型深化 | Thread Model Deepening**
   - **练习描述 | Exercise Description:** 创建一个可视化工具,实时显示三个线程的活动状态(空闲/繁忙)
   - **概念检查 | Concept Check:** 你能否解释为什么某些操作会同时影响多个线程?
   - **学习目标 | Learning Objective:** 深入理解线程间的协作关系

2. **Bridge性能分析 | Bridge Performance Analysis**
   - **练习描述 | Exercise Description:** 编写代码测量100次、1000次、10000次Bridge调用的性能差异
   - **概念检查 | Concept Check:** 频繁的Bridge调用如何影响应用的帧率?
   - **学习目标 | Learning Objective:** 量化Bridge开销,培养性能优化意识

3. **新旧架构对比实验 | Old vs New Architecture Comparison**
   - **练习描述 | Exercise Description:** 在支持的设备上,分别测试启用和禁用新架构的性能差异
   - **概念检查 | Concept Check:** 新架构在哪些场景下优势最明显?
   - **学习目标 | Learning Objective:** 实践验证架构改进的实际效果

4. **Metro配置探索 | Metro Configuration Exploration**
   - **练习描述 | Exercise Description:** 创建metro.config.js文件,自定义resolver和transformer
   - **概念检查 | Concept Check:** Metro的配置如何影响打包过程?
   - **学习目标 | Learning Objective:** 掌握Metro的高级配置能力

5. **跨平台代码优化 | Cross-Platform Code Optimization**
   - **练习描述 | Exercise Description:** 重构一个组件,使用.ios.js和.android.js实现平台特定的优化
   - **概念检查 | Concept Check:** 何时应该拆分平台特定文件?何时应该使用Platform.select()?
   - **学习目标 | Learning Objective:** 学会在代码复用和平台优化之间取得平衡

6. **性能监控仪表板 | Performance Monitoring Dashboard**
   - **练习描述 | Exercise Description:** 构建一个自定义性能监控组件,显示当前帧率、内存使用等指标
   - **概念检查 | Concept Check:** 如何在不影响性能的前提下监控性能?
   - **学习目标 | Learning Objective:** 学习性能监控的最佳实践

7. **架构知识文档 | Architecture Knowledge Documentation**
   - **练习描述 | Exercise Description:** 用自己的语言总结React Native架构,创建一个快速参考指南
   - **概念检查 | Concept Check:** 能否向不熟悉React Native的React开发者解释核心差异?
   - **学习目标 | Learning Objective:** 通过教学巩固对架构的理解

## 学习资源 | Learning Resources

- [React Native官方文档 - 新架构](https://reactnative.dev/docs/the-new-architecture/landing-page) | [React Native Official Docs - New Architecture](https://reactnative.dev/docs/the-new-architecture/landing-page)
- [Metro打包工具文档](https://facebook.github.io/metro/) | [Metro Bundler Documentation](https://facebook.github.io/metro/)
- [React Native性能优化指南](https://reactnative.dev/docs/performance) | [React Native Performance Guide](https://reactnative.dev/docs/performance)
- [深入理解JSI](https://reactnative.dev/docs/the-new-architecture/pillars-turbomodules) | [Understanding JSI](https://reactnative.dev/docs/the-new-architecture/pillars-turbomodules)
- [Fabric渲染器详解](https://reactnative.dev/docs/the-new-architecture/pillars-fabric-components) | [Fabric Renderer Explained](https://reactnative.dev/docs/the-new-architecture/pillars-fabric-components)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解React Native的三线程模型及其职责 | Understand React Native's three-thread model and responsibilities
- [ ] 掌握Bridge架构的工作原理和限制 | Master Bridge architecture's working principles and limitations
- [ ] 了解Metro与Webpack的核心差异 | Know core differences between Metro and Webpack
- [ ] 理解新架构(JSI/Fabric/TurboModules)的改进 | Understand improvements in new architecture (JSI/Fabric/TurboModules)
- [ ] 能够识别和调试跨线程通信问题 | Able to identify and debug cross-thread communication issues
- [ ] 完成架构探索器实践项目 | Complete Architecture Explorer practical project
- [ ] 所有CCQs能够正确回答 | All CCQs answered correctly
- [ ] 理解性能优化的架构基础 | Understand architectural foundations of performance optimization
- [ ] 掌握使用Performance Monitor监控性能 | Master using Performance Monitor to monitor performance
- [ ] 至少完成3个扩展练习 | At least 3 extension exercises completed

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前,请确保能够向一位有React web经验但不熟悉React Native的开发者解释:为什么React Native应用的性能优化策略与Web应用不同?核心差异在哪里?

Before marking as complete, ensure you can explain to a developer with React web experience but unfamiliar with React Native: Why are performance optimization strategies different between React Native and web apps? What are the core differences?
