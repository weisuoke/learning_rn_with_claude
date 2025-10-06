# React Native入门 - 第22天：导航概念与堆栈导航器 | React Native Introduction - Day 22: Navigation Concepts & Stack Navigator

## 学习目标 | Learning Objectives
- 理解移动端导航与Web路由的核心区别 | Understand the core differences between mobile navigation and web routing
- 掌握React Navigation的心智模型和架构设计 | Master React Navigation's mental model and architecture design
- 能够安装和配置React Navigation核心依赖 | Be able to install and configure React Navigation core dependencies
- 熟练创建和使用Stack Navigator实现屏幕导航 | Proficiently create and use Stack Navigator for screen navigation
- 掌握NavigationContainer的作用和配置方法 | Master the role and configuration of NavigationContainer
- 理解navigation prop和基本导航方法的使用 | Understand navigation prop and basic navigation methods

## 详细内容 | Detailed Content

### 1. 移动导航 vs Web路由核心差异 | Mobile Navigation vs Web Routing Core Differences (1小时 | 1 hour)

- **移动导航的独特性 | Uniqueness of Mobile Navigation**

  **概念定义 | Concept Definition:**
  移动导航是在移动应用中管理屏幕之间转换的机制，与Web路由根本不同。在移动端，没有URL地址栏，导航状态完全由JavaScript管理，用户期望的是原生过渡动画和手势操作（如侧滑返回）。React Navigation通过组件化方式模拟原生导航体验。

  Mobile navigation is the mechanism for managing transitions between screens in mobile applications, fundamentally different from web routing. In mobile apps, there's no URL address bar, navigation state is entirely managed by JavaScript, and users expect native transition animations and gesture operations (like swipe-to-go-back). React Navigation simulates native navigation experience through a component-based approach.

  **核心特征 | Key Characteristics:**
  - 无URL依赖：移动端导航不依赖浏览器URL，而是基于堆栈数据结构管理屏幕历史 | URL-independent: Mobile navigation doesn't rely on browser URLs, but manages screen history based on stack data structure
  - 原生手势支持：用户可以通过滑动手势返回上一屏，这是Web无法原生实现的 | Native gesture support: Users can swipe back to previous screens, which web cannot natively implement
  - 平台特定动画：iOS和Android有不同的默认过渡动画（iOS滑动、Android淡入淡出） | Platform-specific animations: iOS and Android have different default transition animations (iOS slide, Android fade)
  - 屏幕即组件：每个屏幕都是一个React组件，导航器管理这些组件的渲染和生命周期 | Screens are components: Each screen is a React component, navigators manage their rendering and lifecycle

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 移动应用中是否有类似Web的URL地址栏？| Is there a URL address bar like web in mobile apps?
     **答案 | Answer:** 否 | No - 移动应用通过导航堆栈管理屏幕，不需要URL | Mobile apps manage screens through navigation stack, no URL needed
  2. 用户能否通过手势操作进行导航？| Can users navigate through gesture operations?
     **答案 | Answer:** 是 | Yes - 这是移动导航的核心特性，如iOS的侧滑返回 | This is a core feature of mobile navigation, like iOS swipe-back
  3. iOS和Android的导航动画是相同的吗？| Are navigation animations the same on iOS and Android?
     **答案 | Answer:** 否 | No - iOS默认从右侧滑入，Android默认淡入淡出 | iOS defaults to slide from right, Android defaults to fade in/out
  4. React Navigation中屏幕是什么？| What are screens in React Navigation?
     **答案 | Answer:** React组件 | React components - 每个屏幕就是一个可以接收navigation props的组件 | Each screen is a component that can receive navigation props

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 移动导航示例 - 屏幕作为组件 | Mobile navigation example - screens as components
  // 这与Web的Route组件概念不同 | This differs from web's Route component concept

  // Web路由方式 (React Router - 仅作对比) | Web routing approach (React Router - for comparison only)
  // <Route path="/details" component={DetailsScreen} />
  // URL: https://example.com/details

  // React Native导航方式 | React Native navigation approach
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  // 定义屏幕组件 | Define screen components
  function HomeScreen({ navigation }) {
    return (
      <View>
        <Text>Home Screen</Text>
        {/* 导航通过JavaScript方法，不是URL | Navigation via JavaScript method, not URL */}
        <Button
          title="Go to Details"
          onPress={() => navigation.navigate('Details')}
        />
      </View>
    );
  }

  function DetailsScreen() {
    return (
      <View>
        <Text>Details Screen</Text>
      </View>
    );
  }

  const Stack = createNativeStackNavigator();

  // 导航器管理屏幕堆栈 | Navigator manages screen stack
  function App() {
    return (
      <NavigationContainer>
        <Stack.Navigator>
          {/* 注册屏幕到导航堆栈 | Register screens to navigation stack */}
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Details" component={DetailsScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，导航是通过什么方式触发的？| How is navigation triggered in this code?
    **答案 | Answer:** navigation.navigate('Details') JavaScript方法调用 | navigation.navigate('Details') JavaScript method call
  - HomeScreen如何获得navigation对象？| How does HomeScreen get the navigation object?
    **答案 | Answer:** 作为props自动注入给Stack.Screen的component | Automatically injected as props to Stack.Screen's component

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以像Web一样通过修改URL来导航？| Can we navigate by modifying URL like in web?
    **答案 | Answer:** 不能 - 移动端没有地址栏，必须使用navigation.navigate()方法 | No - Mobile apps have no address bar, must use navigation.navigate() method
  - Stack.Screen的name属性是否等同于Web的路由路径？| Is Stack.Screen's name attribute equivalent to web route path?
    **答案 | Answer:** 部分正确 - name是标识符，但不是URL路径，仅用于navigation.navigate()引用 | Partially correct - name is an identifier, but not a URL path, only used for navigation.navigate() reference

- **React Navigation的心智模型 | React Navigation Mental Model**

  **概念定义 | Concept Definition:**
  React Navigation基于"导航器-屏幕"的层级结构，导航器（Navigator）是容器组件，屏幕（Screen）是内容组件。导航状态作为一个树形数据结构存储在内存中，每次导航操作都会修改这个状态树，触发屏幕组件的重新渲染。这种设计使得导航完全可预测和可控。

  React Navigation is based on a "Navigator-Screen" hierarchical structure, where Navigator is a container component and Screen is a content component. Navigation state is stored in memory as a tree data structure, and each navigation operation modifies this state tree, triggering screen component re-rendering. This design makes navigation completely predictable and controllable.

  **核心特征 | Key Characteristics:**
  - 声明式API：通过JSX声明屏幕结构，而非命令式配置路由表 | Declarative API: Declare screen structure via JSX, not imperative route configuration
  - 导航状态树：整个导航历史存储为嵌套对象，类似Redux state | Navigation state tree: Entire navigation history stored as nested object, similar to Redux state
  - Prop注入机制：所有屏幕组件自动接收navigation和route props | Prop injection mechanism: All screen components automatically receive navigation and route props
  - 导航器组合：可以嵌套多个导航器（Stack嵌套Tab，Tab嵌套Drawer等） | Navigator composition: Multiple navigators can be nested (Stack in Tab, Tab in Drawer, etc.)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 导航状态存储在哪里？| Where is navigation state stored?
     **答案 | Answer:** 内存中的JavaScript对象 | JavaScript object in memory - 不依赖URL或浏览器历史 | Not reliant on URL or browser history
  2. 屏幕组件会自动接收哪些props？| What props do screen components automatically receive?
     **答案 | Answer:** navigation和route | navigation and route - 无需手动传递 | No manual passing needed
  3. 可以嵌套多个导航器吗？| Can multiple navigators be nested?
     **答案 | Answer:** 是 | Yes - 这是构建复杂导航结构的核心模式 | This is the core pattern for building complex navigation structures
  4. React Navigation的API风格是命令式还是声明式？| Is React Navigation's API imperative or declarative?
     **答案 | Answer:** 声明式 | Declarative - 使用JSX组件定义导航结构 | Using JSX components to define navigation structure

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 导航状态树示例 | Navigation state tree example
  // 这是React Navigation内部维护的数据结构 | This is the data structure maintained internally by React Navigation

  const navigationState = {
    index: 1, // 当前活动屏幕索引 | Current active screen index
    routes: [
      {
        key: 'Home-1',
        name: 'Home'
      },
      {
        key: 'Details-2',
        name: 'Details',
        params: { itemId: 42 } // 屏幕参数 | Screen parameters
      }
    ]
  };

  // 声明式导航器定义 | Declarative navigator definition
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function App() {
    return (
      // NavigationContainer管理整个导航状态树 | NavigationContainer manages entire navigation state tree
      <NavigationContainer>
        {/* Stack.Navigator是导航容器 | Stack.Navigator is navigation container */}
        <Stack.Navigator
          initialRouteName="Home" // 初始屏幕 | Initial screen
          screenOptions={{
            headerStyle: { backgroundColor: '#f4511e' } // 全局配置 | Global configuration
          }}
        >
          {/* Stack.Screen注册屏幕 | Stack.Screen registers screens */}
          <Stack.Screen
            name="Home"
            component={HomeScreen}
            options={{ title: 'My Home' }} // 屏幕特定配置 | Screen-specific configuration
          />
          <Stack.Screen
            name="Details"
            component={DetailsScreen}
          />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }

  // 屏幕组件自动接收props | Screen components automatically receive props
  function HomeScreen({ navigation, route }) {
    console.log('Navigation prop:', navigation); // 包含navigate, goBack等方法 | Contains navigate, goBack methods
    console.log('Route prop:', route); // 包含name, params等信息 | Contains name, params info

    return (
      <View>
        <Text>Home Screen</Text>
        <Button
          title="Navigate"
          onPress={() => {
            // 导航操作修改导航状态树 | Navigation operation modifies navigation state tree
            navigation.navigate('Details', { itemId: 42 });
          }}
        />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Stack.Navigator和Stack.Screen是什么关系？| What's the relationship between Stack.Navigator and Stack.Screen?
    **答案 | Answer:** 父子关系 - Navigator是容器，Screen是其子组件，定义了堆栈中的各个屏幕 | Parent-child relationship - Navigator is container, Screen is its child component defining screens in stack
  - initialRouteName的作用是什么？| What is the purpose of initialRouteName?
    **答案 | Answer:** 指定应用启动时显示的第一个屏幕 | Specifies the first screen to display when app launches

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要在屏幕组件中手动导入navigation对象？| Do we need to manually import navigation object in screen components?
    **答案 | Answer:** 不需要 - navigation作为prop自动注入，只需在函数参数中解构 | No - navigation is auto-injected as prop, just destructure in function parameters
  - screenOptions和options有什么区别？| What's the difference between screenOptions and options?
    **答案 | Answer:** screenOptions应用于所有屏幕（全局），options仅应用于单个屏幕 | screenOptions applies to all screens (global), options applies to single screen only

### 2. 安装与配置React Navigation | Installing and Configuring React Navigation (1小时 | 1 hour)

- **核心依赖安装 | Core Dependencies Installation**

  **概念定义 | Concept Definition:**
  React Navigation是模块化设计，核心包(@react-navigation/native)提供基础API，具体导航器（如Stack、Tab）作为独立包安装。此外需要安装原生依赖(react-native-screens和react-native-safe-area-context)以支持原生导航体验和安全区域适配。Expo项目使用expo install确保版本兼容。

  React Navigation has a modular design where the core package (@react-navigation/native) provides base APIs, and specific navigators (like Stack, Tab) are installed as separate packages. Additionally, native dependencies (react-native-screens and react-native-safe-area-context) are needed to support native navigation experience and safe area adaptation. Expo projects use expo install to ensure version compatibility.

  **核心特征 | Key Characteristics:**
  - 模块化架构：核心包+导航器包分离，按需安装减少bundle大小 | Modular architecture: Core package + navigator packages separated, install on-demand to reduce bundle size
  - 原生依赖要求：react-native-screens优化性能，safe-area-context处理刘海屏等 | Native dependency requirements: react-native-screens optimizes performance, safe-area-context handles notches
  - Expo兼容性：使用expo install而非npm install确保原生模块版本匹配 | Expo compatibility: Use expo install instead of npm install to ensure native module version matching
  - 版本同步：所有@react-navigation包应保持相同主版本号 | Version synchronization: All @react-navigation packages should maintain same major version

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 是否只需要安装@react-navigation/native就能使用导航？| Can we use navigation by only installing @react-navigation/native?
     **答案 | Answer:** 否 | No - 还需要安装具体导航器包（如@react-navigation/native-stack）和原生依赖 | Still need to install specific navigator packages and native dependencies
  2. react-native-screens的作用是什么？| What is the purpose of react-native-screens?
     **答案 | Answer:** 使用原生屏幕容器提升性能和内存管理 | Uses native screen containers to improve performance and memory management
  3. Expo项目中应该使用npm install还是expo install？| Should we use npm install or expo install in Expo projects?
     **答案 | Answer:** expo install | expo install - 确保原生模块版本与Expo SDK兼容 | Ensures native module versions are compatible with Expo SDK
  4. 为什么需要safe-area-context？| Why do we need safe-area-context?
     **答案 | Answer:** 处理iPhone刘海屏、圆角等安全区域，避免内容被遮挡 | Handles safe areas like iPhone notches and rounded corners to prevent content from being obscured

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # 步骤1: 安装核心包 | Step 1: Install core package
  # 这是React Navigation的基础API | This is React Navigation's base API
  npm install @react-navigation/native

  # 步骤2: 安装原生依赖 (Expo项目) | Step 2: Install native dependencies (Expo project)
  # expo install会自动选择与Expo SDK兼容的版本 | expo install automatically selects versions compatible with Expo SDK
  expo install react-native-screens react-native-safe-area-context

  # 如果是纯React Native项目 (非Expo) | If using bare React Native (non-Expo)
  # npm install react-native-screens react-native-safe-area-context
  # cd ios && pod install && cd .. # iOS需要安装CocoaPods依赖 | iOS requires CocoaPods installation

  # 步骤3: 安装Stack Navigator | Step 3: Install Stack Navigator
  npm install @react-navigation/native-stack

  # 验证安装 - 查看package.json | Verify installation - check package.json
  # 应该看到以下依赖 | Should see the following dependencies:
  # "@react-navigation/native": "^6.x.x"
  # "@react-navigation/native-stack": "^6.x.x"
  # "react-native-screens": "~3.x.x"
  # "react-native-safe-area-context": "~4.x.x"
  ```

  ```javascript
  // 配置原生屏幕优化 | Configure native screen optimization
  // 在App.js顶部添加 | Add at the top of App.js
  import { enableScreens } from 'react-native-screens';

  // 启用原生屏幕 - 提升性能 | Enable native screens - improves performance
  enableScreens();

  // 导入SafeAreaProvider | Import SafeAreaProvider
  import { SafeAreaProvider } from 'react-native-safe-area-context';
  import { NavigationContainer } from '@react-navigation/native';

  function App() {
    return (
      // SafeAreaProvider包裹整个应用，提供安全区域信息 | SafeAreaProvider wraps entire app, provides safe area info
      <SafeAreaProvider>
        <NavigationContainer>
          {/* 导航器配置 | Navigator configuration */}
        </NavigationContainer>
      </SafeAreaProvider>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要在App.js顶部调用enableScreens()？| Why call enableScreens() at the top of App.js?
    **答案 | Answer:** 尽早启用原生屏幕优化，减少JavaScript线程负担 | Enable native screen optimization as early as possible to reduce JavaScript thread burden
  - SafeAreaProvider应该放在什么位置？| Where should SafeAreaProvider be placed?
    **答案 | Answer:** 应用的最外层，包裹NavigationContainer | At the outermost layer of the app, wrapping NavigationContainer

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在Expo项目中使用npm install安装原生依赖？| Can we use npm install to install native dependencies in Expo projects?
    **答案 | Answer:** 不推荐 - 可能导致版本不兼容，应使用expo install | Not recommended - may cause version incompatibility, should use expo install
  - 安装完依赖后是否需要重启Metro bundler？| Do we need to restart Metro bundler after installing dependencies?
    **答案 | Answer:** 是 - 特别是安装原生依赖后，需要重新运行expo start | Yes - especially after installing native dependencies, need to re-run expo start

- **NavigationContainer配置 | NavigationContainer Configuration**

  **概念定义 | Concept Definition:**
  NavigationContainer是React Navigation的根组件，负责管理整个导航状态树、监听导航事件、处理深度链接。它必须包裹所有导航器，在应用中只需要一个NavigationContainer实例。它提供了onReady、onStateChange等回调函数，可用于导航状态持久化或分析追踪。

  NavigationContainer is the root component of React Navigation, responsible for managing the entire navigation state tree, listening to navigation events, and handling deep linking. It must wrap all navigators, and only one NavigationContainer instance is needed in the app. It provides callbacks like onReady and onStateChange for navigation state persistence or analytics tracking.

  **核心特征 | Key Characteristics:**
  - 单例模式：一个应用只需一个NavigationContainer实例 | Singleton pattern: Only one NavigationContainer instance per app
  - 状态管理：维护整个导航状态树，类似Redux的Provider | State management: Maintains entire navigation state tree, similar to Redux Provider
  - 事件监听：提供导航生命周期钩子（onReady, onStateChange） | Event listening: Provides navigation lifecycle hooks (onReady, onStateChange)
  - 深度链接：处理外部URL打开应用时的导航跳转 | Deep linking: Handles navigation when app is opened from external URLs

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 一个应用需要多少个NavigationContainer？| How many NavigationContainers does an app need?
     **答案 | Answer:** 一个 | One - 它是整个导航系统的根容器 | It is the root container of the entire navigation system
  2. NavigationContainer必须包裹什么？| What must NavigationContainer wrap?
     **答案 | Answer:** 所有导航器组件（Stack.Navigator, Tab.Navigator等） | All navigator components (Stack.Navigator, Tab.Navigator, etc.)
  3. onStateChange回调函数的作用是什么？| What is the purpose of onStateChange callback?
     **答案 | Answer:** 导航状态改变时触发，可用于状态持久化或分析 | Triggered when navigation state changes, can be used for state persistence or analytics
  4. NavigationContainer是否可以嵌套？| Can NavigationContainer be nested?
     **答案 | Answer:** 否 | No - 只能有一个根级别的NavigationContainer | Only one root-level NavigationContainer is allowed

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // App.js - NavigationContainer基础配置 | App.js - NavigationContainer basic configuration
  import React from 'react';
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function App() {
    // 导航就绪时触发 | Triggered when navigation is ready
    const handleNavigationReady = () => {
      console.log('Navigation is ready!');
    };

    // 导航状态改变时触发 | Triggered when navigation state changes
    const handleStateChange = (state) => {
      console.log('Navigation state changed:', state);
      // 可用于分析追踪或状态持久化 | Can be used for analytics tracking or state persistence
    };

    return (
      <NavigationContainer
        onReady={handleNavigationReady}
        onStateChange={handleStateChange}
        // 可选：自定义主题 | Optional: custom theme
        theme={{
          dark: false,
          colors: {
            primary: '#1E88E5',
            background: '#FFFFFF',
            card: '#FFFFFF',
            text: '#000000',
            border: '#E0E0E0',
          },
        }}
      >
        {/* 导航器必须作为NavigationContainer的直接或间接子组件 | Navigator must be direct or indirect child of NavigationContainer */}
        <Stack.Navigator>
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Details" component={DetailsScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }

  export default App;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - NavigationContainer的theme属性有什么作用？| What is the purpose of NavigationContainer's theme prop?
    **答案 | Answer:** 定义全局导航主题色，包括背景色、文字色、边框色等 | Defines global navigation theme colors including background, text, borders, etc.
  - 为什么onReady回调很重要？| Why is the onReady callback important?
    **答案 | Answer:** 确保导航完全初始化后再执行操作（如深度链接处理） | Ensures operations (like deep linking handling) execute only after navigation is fully initialized

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在每个导航器外包裹单独的NavigationContainer？| Can we wrap each navigator with separate NavigationContainer?
    **答案 | Answer:** 不可以 - 只能有一个根NavigationContainer，多个导航器嵌套在其内部 | No - Only one root NavigationContainer allowed, multiple navigators nested inside it
  - NavigationContainer是否必须放在App组件的最外层？| Must NavigationContainer be at the outermost layer of App component?
    **答案 | Answer:** 不一定 - 可以在外层包裹Provider（如Redux或SafeAreaProvider） | Not necessarily - Can be wrapped by Providers (like Redux or SafeAreaProvider) at outer layer

### 3. 创建Stack Navigator | Creating Stack Navigator (1.5小时 | 1.5 hours)

- **Stack Navigator核心概念 | Stack Navigator Core Concept**

  **概念定义 | Concept Definition:**
  Stack Navigator基于堆栈数据结构（LIFO - Last In First Out）管理屏幕历史，类似浏览器的历史记录。当导航到新屏幕时，新屏幕被"推入"堆栈顶部；返回时，顶部屏幕被"弹出"。这种模式符合移动应用的导航直觉，支持原生的过渡动画和手势返回。

  Stack Navigator manages screen history based on stack data structure (LIFO - Last In First Out), similar to browser history. When navigating to a new screen, the new screen is "pushed" onto the top of the stack; when going back, the top screen is "popped". This pattern matches mobile app navigation intuition and supports native transition animations and gesture-based back navigation.

  **核心特征 | Key Characteristics:**
  - 堆栈结构：后进先出，最新屏幕在顶部 | Stack structure: Last-In-First-Out, newest screen on top
  - 自动头部导航栏：默认提供带返回按钮的原生导航栏 | Automatic header bar: Provides native navigation bar with back button by default
  - 平台差异化：iOS使用滑动动画，Android使用淡入淡出 | Platform differentiation: iOS uses slide animation, Android uses fade
  - 手势支持：iOS默认支持从左侧边缘滑动返回 | Gesture support: iOS supports swipe-from-left-edge to go back by default

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Stack Navigator使用什么数据结构管理屏幕？| What data structure does Stack Navigator use to manage screens?
     **答案 | Answer:** 堆栈（Stack） | Stack - LIFO后进先出原则 | LIFO Last-In-First-Out principle
  2. 当从屏幕A导航到屏幕B时，屏幕A会被销毁吗？| When navigating from Screen A to Screen B, is Screen A destroyed?
     **答案 | Answer:** 否 | No - 屏幕A保留在堆栈中，只是被B覆盖 | Screen A remains in stack, just covered by B
  3. Stack Navigator是否自动提供返回按钮？| Does Stack Navigator automatically provide a back button?
     **答案 | Answer:** 是 | Yes - 在非首屏自动显示平台原生样式的返回按钮 | Automatically shows platform-native back button on non-initial screens
  4. iOS和Android的Stack导航动画是否相同？| Are Stack navigation animations the same on iOS and Android?
     **答案 | Answer:** 否 | No - iOS滑动，Android淡入淡出（可自定义） | iOS slides, Android fades (customizable)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Stack Navigator完整示例 | Stack Navigator complete example
  import React from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  // 创建Stack Navigator实例 | Create Stack Navigator instance
  // 注意：这是工厂函数，调用后返回包含Navigator和Screen的对象 | Note: This is a factory function returning object with Navigator and Screen
  const Stack = createNativeStackNavigator();

  // 屏幕组件1：首页 | Screen component 1: Home
  function HomeScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>Home Screen</Text>
        <Text style={styles.subtitle}>This is the first screen in the stack</Text>

        {/* 导航到Details屏幕 | Navigate to Details screen */}
        <Button
          title="Go to Details"
          onPress={() => {
            // navigation.navigate() 将Details推入堆栈 | navigation.navigate() pushes Details onto stack
            navigation.navigate('Details');
          }}
        />

        {/* 也可以使用push方法 - 区别见下文 | Can also use push method - difference explained below */}
        <Button
          title="Push Details (allows duplicate)"
          onPress={() => navigation.push('Details')}
        />
      </View>
    );
  }

  // 屏幕组件2：详情页 | Screen component 2: Details
  function DetailsScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>Details Screen</Text>

        {/* 返回上一屏 | Go back to previous screen */}
        <Button
          title="Go Back"
          onPress={() => {
            // navigation.goBack() 从堆栈弹出当前屏幕 | navigation.goBack() pops current screen from stack
            navigation.goBack();
          }}
        />

        {/* 返回首屏 | Go back to initial screen */}
        <Button
          title="Go to Home"
          onPress={() => {
            // navigate('Home') 会弹出到Home，而不是推入新的Home | navigate('Home') pops to Home, not push new Home
            navigation.navigate('Home');
          }}
        />

        {/* 可以再次推入Details（创建堆栈：Home -> Details -> Details） | Can push Details again (stack: Home -> Details -> Details) */}
        <Button
          title="Push Details Again"
          onPress={() => navigation.push('Details')}
        />
      </View>
    );
  }

  // App组件：配置Stack Navigator | App component: Configure Stack Navigator
  function App() {
    return (
      <NavigationContainer>
        {/* Stack.Navigator是导航器容器 | Stack.Navigator is navigator container */}
        <Stack.Navigator
          // initialRouteName指定首屏 | initialRouteName specifies initial screen
          initialRouteName="Home"
          // screenOptions应用于所有屏幕的默认配置 | screenOptions applies default config to all screens
          screenOptions={{
            headerStyle: {
              backgroundColor: '#1E88E5', // 头部背景色 | Header background color
            },
            headerTintColor: '#fff', // 头部文字和按钮颜色 | Header text and button color
            headerTitleStyle: {
              fontWeight: 'bold',
            },
          }}
        >
          {/* Stack.Screen注册屏幕组件 | Stack.Screen registers screen components */}
          <Stack.Screen
            name="Home" // 屏幕标识符，用于导航 | Screen identifier for navigation
            component={HomeScreen} // 屏幕组件 | Screen component
            options={{
              title: 'My Home' // 头部标题（覆盖默认的name） | Header title (overrides default name)
            }}
          />
          <Stack.Screen
            name="Details"
            component={DetailsScreen}
            options={{
              title: 'Details'
            }}
          />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      alignItems: 'center',
      justifyContent: 'center',
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
    },
    subtitle: {
      fontSize: 16,
      color: '#666',
      marginBottom: 20,
      textAlign: 'center',
    },
  });

  export default App;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - navigation.navigate('Details')和navigation.push('Details')有什么区别？| What's the difference between navigation.navigate('Details') and navigation.push('Details')?
    **答案 | Answer:** navigate会检查堆栈中是否已存在Details，存在则跳转；push总是推入新实例 | navigate checks if Details exists in stack and jumps to it; push always pushes new instance
  - 在Details屏幕中调用navigation.navigate('Home')会怎样？| What happens when calling navigation.navigate('Home') in Details screen?
    **答案 | Answer:** 会弹出堆栈中Details之上的所有屏幕，返回到Home | Pops all screens above Details in stack, returns to Home

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要手动实现返回按钮？| Do we need to manually implement a back button?
    **答案 | Answer:** 不需要 - Stack Navigator自动在非首屏提供返回按钮 | No - Stack Navigator automatically provides back button on non-initial screens
  - Stack.Screen的name和options.title是同一个东西吗？| Are Stack.Screen's name and options.title the same thing?
    **答案 | Answer:** 否 - name是导航标识符（必需），title是显示的头部标题（可选） | No - name is navigation identifier (required), title is displayed header title (optional)

- **navigation prop核心方法 | navigation prop Core Methods**

  **概念定义 | Concept Definition:**
  navigation prop是Stack Navigator自动注入给每个屏幕组件的对象，包含导航操作方法（navigate, goBack, push等）和导航状态信息。它类似于React Router的history对象，但专为移动堆栈导航设计，提供了更符合原生体验的API。

  navigation prop is an object automatically injected by Stack Navigator to each screen component, containing navigation action methods (navigate, goBack, push, etc.) and navigation state info. It's similar to React Router's history object but designed specifically for mobile stack navigation, providing APIs more aligned with native experience.

  **核心特征 | Key Characteristics:**
  - 自动注入：无需手动传递，所有Stack.Screen的component自动接收 | Auto-injection: No manual passing needed, all Stack.Screen components automatically receive it
  - 方法丰富：navigate、push、goBack、popToTop等多种导航方法 | Rich methods: navigate, push, goBack, popToTop and other navigation methods
  - 类型安全：配合TypeScript可实现屏幕名和参数的类型检查 | Type safety: Can achieve type checking for screen names and params with TypeScript
  - 状态查询：可获取当前路由状态、是否可以返回等信息 | State query: Can get current route state, whether can go back, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. navigation prop从哪里来？| Where does navigation prop come from?
     **答案 | Answer:** Stack Navigator自动注入给屏幕组件 | Automatically injected by Stack Navigator to screen components
  2. navigate和push方法的本质区别是什么？| What's the essential difference between navigate and push methods?
     **答案 | Answer:** navigate避免重复（存在则跳转），push总是创建新实例 | navigate avoids duplication (jumps if exists), push always creates new instance
  3. 如果当前在首屏，调用goBack()会发生什么？| What happens if calling goBack() on the initial screen?
     **答案 | Answer:** 什么也不做（首屏没有上一屏） | Nothing happens (initial screen has no previous screen)
  4. popToTop方法的作用是什么？| What is the purpose of popToTop method?
     **答案 | Answer:** 弹出堆栈中除首屏外的所有屏幕，返回首屏 | Pops all screens except initial screen from stack, returns to initial screen

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // navigation prop核心方法详解 | navigation prop core methods explained
  import React from 'react';
  import { View, Button, Text, StyleSheet } from 'react-native';

  function ExampleScreen({ navigation }) {
    // 可以从navigation获取多种信息和方法 | Can get various info and methods from navigation

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Navigation Methods Demo</Text>

        {/* 方法1: navigate - 智能导航 | Method 1: navigate - smart navigation */}
        <Button
          title="navigate('Profile')"
          onPress={() => {
            // 如果Profile已在堆栈中，跳转到它；否则推入新的 | If Profile already in stack, jump to it; otherwise push new one
            navigation.navigate('Profile', { userId: 123 }); // 可传递参数 | Can pass params
          }}
        />

        {/* 方法2: push - 总是推入新屏幕 | Method 2: push - always push new screen */}
        <Button
          title="push('Profile')"
          onPress={() => {
            // 总是推入新的Profile实例，即使已存在 | Always push new Profile instance even if exists
            navigation.push('Profile', { userId: 456 });
          }}
        />

        {/* 方法3: goBack - 返回上一屏 | Method 3: goBack - go to previous screen */}
        <Button
          title="goBack()"
          onPress={() => {
            // 从堆栈弹出当前屏幕 | Pop current screen from stack
            navigation.goBack();
          }}
        />

        {/* 方法4: popToTop - 返回首屏 | Method 4: popToTop - go to initial screen */}
        <Button
          title="popToTop()"
          onPress={() => {
            // 弹出除首屏外的所有屏幕 | Pop all screens except initial screen
            navigation.popToTop();
          }}
        />

        {/* 方法5: pop - 弹出指定数量的屏幕 | Method 5: pop - pop specified number of screens */}
        <Button
          title="pop(2)"
          onPress={() => {
            // 弹出2个屏幕（如果堆栈深度足够） | Pop 2 screens (if stack depth is sufficient)
            navigation.pop(2);
          }}
        />

        {/* 方法6: setOptions - 动态修改头部配置 | Method 6: setOptions - dynamically modify header config */}
        <Button
          title="Change Header"
          onPress={() => {
            navigation.setOptions({
              title: 'Updated Title',
              headerStyle: { backgroundColor: '#FF5722' },
            });
          }}
        />

        {/* 方法7: canGoBack - 检查是否可以返回 | Method 7: canGoBack - check if can go back */}
        <Button
          title="Check if can go back"
          onPress={() => {
            const canGoBack = navigation.canGoBack();
            alert(canGoBack ? 'Can go back' : 'Already at initial screen');
          }}
        />

        {/* 方法8: isFocused - 检查当前屏幕是否处于焦点 | Method 8: isFocused - check if current screen is focused */}
        <Button
          title="Check if focused"
          onPress={() => {
            const isFocused = navigation.isFocused();
            alert(isFocused ? 'This screen is focused' : 'This screen is not focused');
          }}
        />
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
  });

  export default ExampleScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果在Details屏幕中连续调用navigation.push('Details')三次，堆栈会是什么状态？| If calling navigation.push('Details') three times consecutively in Details screen, what will the stack state be?
    **答案 | Answer:** Home -> Details -> Details -> Details -> Details (4个Details实例) | Home -> Details -> Details -> Details -> Details (4 Details instances)
  - navigation.pop()和navigation.goBack()有区别吗？| Is there a difference between navigation.pop() and navigation.goBack()?
    **答案 | Answer:** 基本相同，但pop可以指定弹出数量，goBack只弹出1个 | Basically the same, but pop can specify number to pop, goBack only pops 1

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在非屏幕组件中使用navigation prop？| Can we use navigation prop in non-screen components?
    **答案 | Answer:** 不能直接使用 - 需要通过props传递或使用useNavigation hook | Cannot use directly - need to pass via props or use useNavigation hook
  - navigation.navigate('Home')是否会创建新的Home屏幕？| Does navigation.navigate('Home') create a new Home screen?
    **答案 | Answer:** 否 - 会回到堆栈中已存在的Home，不创建新实例 | No - goes back to existing Home in stack, doesn't create new instance

### 4. route prop与屏幕参数 | route prop and Screen Parameters (1小时 | 1 hour)

- **route prop核心概念 | route prop Core Concept**

  **概念定义 | Concept Definition:**
  route prop是Stack Navigator自动注入给屏幕组件的另一个对象，包含当前屏幕的元数据信息，如屏幕名称(name)、传递的参数(params)、路由键(key)等。它类似于React Router的location对象，但专为移动导航设计，params是在屏幕间传递数据的主要方式。

  route prop is another object automatically injected by Stack Navigator to screen components, containing metadata about the current screen such as screen name (name), passed parameters (params), route key (key), etc. It's similar to React Router's location object but designed for mobile navigation, where params is the primary way to pass data between screens.

  **核心特征 | Key Characteristics:**
  - 元数据容器：包含屏幕标识信息（name, key）和传递的数据（params） | Metadata container: Contains screen identification info (name, key) and passed data (params)
  - 参数传递：通过route.params获取上一屏传递的数据 | Parameter passing: Access data passed from previous screen via route.params
  - 类型安全：配合TypeScript可定义params类型，实现编译时检查 | Type safety: Can define params types with TypeScript for compile-time checking
  - 不可变性：route对象在屏幕生命周期内保持稳定引用 | Immutability: route object maintains stable reference throughout screen lifecycle

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. route.params用于什么？| What is route.params used for?
     **答案 | Answer:** 获取从上一屏传递过来的参数数据 | Getting parameter data passed from previous screen
  2. 如果没有传递params，route.params是什么？| What is route.params if no params are passed?
     **答案 | Answer:** undefined | undefined - 需要添加可选链或默认值处理 | Need optional chaining or default value handling
  3. route.name和Stack.Screen的name属性有什么关系？| What's the relationship between route.name and Stack.Screen's name attribute?
     **答案 | Answer:** route.name的值就是Stack.Screen定义的name | route.name's value is the name defined in Stack.Screen
  4. 可以修改route.params的值吗？| Can we modify route.params values?
     **答案 | Answer:** 不应该直接修改 - 使用navigation.setParams()更新参数 | Should not modify directly - use navigation.setParams() to update params

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 屏幕间参数传递完整示例 | Complete example of parameter passing between screens
  import React from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  // 列表屏幕：传递参数 | List screen: passing parameters
  function ItemListScreen({ navigation }) {
    const items = [
      { id: 1, name: 'Item One', description: 'First item description', price: 19.99 },
      { id: 2, name: 'Item Two', description: 'Second item description', price: 29.99 },
      { id: 3, name: 'Item Three', description: 'Third item description', price: 39.99 },
    ];

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Item List</Text>
        {items.map((item) => (
          <Button
            key={item.id}
            title={`View ${item.name}`}
            onPress={() => {
              // 通过第二个参数传递params对象 | Pass params object via second parameter
              navigation.navigate('ItemDetail', {
                itemId: item.id,
                itemName: item.name,
                itemDescription: item.description,
                itemPrice: item.price,
              });
            }}
          />
        ))}
      </View>
    );
  }

  // 详情屏幕：接收参数 | Detail screen: receiving parameters
  function ItemDetailScreen({ route, navigation }) {
    // 从route.params获取传递的参数 | Get passed parameters from route.params
    // 使用可选链避免params为undefined时报错 | Use optional chaining to avoid error when params is undefined
    const { itemId, itemName, itemDescription, itemPrice } = route.params || {};

    // 打印route对象结构 | Print route object structure
    console.log('Route object:', {
      name: route.name, // 'ItemDetail'
      key: route.key,   // 唯一标识符，如'ItemDetail-abc123'
      params: route.params, // { itemId: 1, itemName: 'Item One', ... }
    });

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Item Details</Text>
        <Text style={styles.label}>ID: {itemId}</Text>
        <Text style={styles.label}>Name: {itemName}</Text>
        <Text style={styles.label}>Description: {itemDescription}</Text>
        <Text style={styles.label}>Price: ${itemPrice}</Text>

        {/* 修改当前屏幕的params | Modify current screen's params */}
        <Button
          title="Update Price"
          onPress={() => {
            // 使用navigation.setParams()更新参数 | Use navigation.setParams() to update params
            navigation.setParams({
              itemPrice: (itemPrice * 0.9).toFixed(2), // 打9折 | 10% discount
            });
          }}
        />

        {/* 返回时也可以传递数据 | Can also pass data when going back */}
        <Button
          title="Go Back with Result"
          onPress={() => {
            // 注意：这种方式需要配合navigation.addListener使用 | Note: This approach needs navigation.addListener
            navigation.navigate('ItemList', {
              updatedItemId: itemId,
              timestamp: Date.now()
            });
          }}
        />
      </View>
    );
  }

  // App组件 | App component
  function App() {
    return (
      <NavigationContainer>
        <Stack.Navigator>
          <Stack.Screen
            name="ItemList"
            component={ItemListScreen}
            options={{ title: 'All Items' }}
          />
          <Stack.Screen
            name="ItemDetail"
            component={ItemDetailScreen}
            // 动态设置标题 - 使用params | Dynamically set title - using params
            options={({ route }) => ({
              title: route.params?.itemName || 'Item Detail'
            })}
          />
        </Stack.Navigator>
      </NavigationContainer>
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
    },
    label: {
      fontSize: 16,
      marginBottom: 10,
    },
  });

  export default App;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果导航时没有传递params，如何安全访问route.params？| How to safely access route.params if no params are passed during navigation?
    **答案 | Answer:** 使用可选链和默认值：const { id } = route.params || {} 或 route.params?.id | Use optional chaining and default values: const { id } = route.params || {} or route.params?.id
  - navigation.setParams()会影响上一屏的params吗？| Does navigation.setParams() affect previous screen's params?
    **答案 | Answer:** 否 - 只更新当前屏幕的params，不影响其他屏幕 | No - only updates current screen's params, doesn't affect other screens

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以直接修改route.params.itemId = 2？| Can we directly modify route.params.itemId = 2?
    **答案 | Answer:** 技术上可以但不推荐 - 应使用navigation.setParams()确保状态更新正确触发 | Technically possible but not recommended - should use navigation.setParams() to ensure state updates trigger correctly
  - params传递的数据有大小限制吗？| Is there a size limit for data passed via params?
    **答案 | Answer:** 应避免传递大量数据 - params适合简单数据（ID、字符串），大数据使用全局状态管理 | Should avoid passing large data - params suitable for simple data (IDs, strings), use global state management for large data

### 5. 屏幕选项配置 | Screen Options Configuration (45分钟 | 45 minutes)

- **静态选项 vs 动态选项 | Static Options vs Dynamic Options**

  **概念定义 | Concept Definition:**
  屏幕选项(options)用于配置导航头部(header)的外观和行为。静态选项在Screen组件的options属性中直接定义，适用于固定配置；动态选项使用函数形式，可根据route params或其他状态动态计算，适用于需要根据数据调整的场景（如动态标题）。

  Screen options are used to configure the appearance and behavior of navigation header. Static options are defined directly in Screen component's options prop, suitable for fixed configuration; dynamic options use function form and can be calculated dynamically based on route params or other state, suitable for scenarios requiring adjustment based on data (like dynamic titles).

  **核心特征 | Key Characteristics:**
  - 层级覆盖：screenOptions(全局) < Screen.options(屏幕级) < navigation.setOptions()(运行时) | Hierarchical override: screenOptions (global) < Screen.options (screen-level) < navigation.setOptions() (runtime)
  - 函数式动态：options可以是函数，接收{ route, navigation }参数 | Functional dynamic: options can be a function receiving { route, navigation } parameters
  - 丰富配置：支持标题、颜色、按钮、手势等数十种配置项 | Rich configuration: Supports dozens of config items like title, color, buttons, gestures
  - 平台差异：某些选项仅在特定平台生效（如iOS的headerLargeTitle） | Platform differences: Some options only work on specific platforms (like iOS headerLargeTitle)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. screenOptions和options的区别是什么？| What's the difference between screenOptions and options?
     **答案 | Answer:** screenOptions应用于所有屏幕（全局），options仅应用于单个屏幕 | screenOptions applies to all screens (global), options applies to single screen only
  2. 如果同时设置了screenOptions和options，哪个优先级更高？| If both screenOptions and options are set, which has higher priority?
     **答案 | Answer:** options优先级更高，会覆盖screenOptions的相同配置 | options has higher priority, overrides same config in screenOptions
  3. 如何实现动态标题（基于params）？| How to implement dynamic title (based on params)?
     **答案 | Answer:** 使用函数形式的options：options={({ route }) => ({ title: route.params.name })} | Use function form of options: options={({ route }) => ({ title: route.params.name })}
  4. navigation.setOptions()在何时使用？| When to use navigation.setOptions()?
     **答案 | Answer:** 运行时动态修改选项，如用户操作后改变标题或按钮 | Dynamically modify options at runtime, like changing title or buttons after user action

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 屏幕选项配置完整示例 | Complete example of screen options configuration
  import React, { useLayoutEffect, useState } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  // 示例1: 静态选项配置 | Example 1: Static options configuration
  function HomeScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>Home Screen</Text>
        <Button
          title="Go to Profile"
          onPress={() => navigation.navigate('Profile', { userName: 'John Doe' })}
        />
      </View>
    );
  }

  // 示例2: 运行时动态选项 | Example 2: Runtime dynamic options
  function ProfileScreen({ navigation, route }) {
    const [count, setCount] = useState(0);

    // useLayoutEffect确保在渲染前设置头部 | useLayoutEffect ensures header is set before rendering
    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <Button
            title="Update"
            onPress={() => setCount((c) => c + 1)}
          />
        ),
        title: `Profile (${count})`, // 动态标题 | Dynamic title
      });
    }, [navigation, count]);

    const userName = route.params?.userName || 'Guest';

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Welcome, {userName}!</Text>
        <Text>Count: {count}</Text>
      </View>
    );
  }

  // App组件：多层级选项配置 | App component: Multi-level options configuration
  function App() {
    return (
      <NavigationContainer>
        <Stack.Navigator
          // 全局默认选项 | Global default options
          screenOptions={{
            headerStyle: {
              backgroundColor: '#6200EE',
            },
            headerTintColor: '#fff',
            headerTitleStyle: {
              fontWeight: 'bold',
            },
            // 禁用iOS的侧滑返回手势 | Disable iOS swipe-back gesture
            gestureEnabled: false,
          }}
        >
          <Stack.Screen
            name="Home"
            component={HomeScreen}
            // 屏幕级静态选项 - 覆盖全局配置 | Screen-level static options - override global config
            options={{
              title: 'My Home',
              headerStyle: {
                backgroundColor: '#03DAC6', // 覆盖全局背景色 | Override global background color
              },
              headerLeft: () => (
                <Button title="Menu" onPress={() => alert('Menu')} />
              ),
            }}
          />

          <Stack.Screen
            name="Profile"
            component={ProfileScreen}
            // 动态选项函数 - 基于route params | Dynamic options function - based on route params
            options={({ route, navigation }) => ({
              title: route.params?.userName || 'Profile',
              // 动态头部右侧按钮 | Dynamic header right button
              headerRight: () => (
                <Button
                  title="Edit"
                  onPress={() => alert('Edit profile')}
                />
              ),
              // 自定义返回按钮文本（仅iOS） | Custom back button text (iOS only)
              headerBackTitle: 'Back',
            })}
          />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      alignItems: 'center',
      justifyContent: 'center',
    },
    title: {
      fontSize: 24,
      marginBottom: 20,
    },
  });

  export default App;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用useLayoutEffect而不是useEffect来设置options？| Why use useLayoutEffect instead of useEffect to set options?
    **答案 | Answer:** useLayoutEffect在渲染前同步执行，避免头部闪烁 | useLayoutEffect executes synchronously before rendering, avoids header flickering
  - headerRight返回的组件有什么限制？| What are the limitations of components returned by headerRight?
    **答案 | Answer:** 应返回简单组件（如Button），避免复杂布局，注意平台差异 | Should return simple components (like Button), avoid complex layouts, mind platform differences

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在屏幕组件的render中直接调用navigation.setOptions()？| Can we directly call navigation.setOptions() in screen component's render?
    **答案 | Answer:** 不推荐 - 会导致无限渲染循环，应在useLayoutEffect中调用 | Not recommended - causes infinite render loop, should call in useLayoutEffect
  - headerLeft设置为null会怎样？| What happens if headerLeft is set to null?
    **答案 | Answer:** 会移除默认的返回按钮（谨慎使用，可能困住用户） | Removes default back button (use cautiously, may trap users)

### 6. 最佳实践与常见问题 | Best Practices and Common Issues (30分钟 | 30 minutes)

- **导航器组织最佳实践 | Navigator Organization Best Practices**

  **概念定义 | Concept Definition:**
  随着应用复杂度增加，应将导航器配置拆分到独立文件，使用TypeScript定义导航参数类型，避免在一个文件中定义所有屏幕。推荐的组织方式是：每个导航器一个文件夹，包含navigator定义和相关屏幕组件，顶层App.js仅负责组合各导航器。

  As app complexity increases, navigator configuration should be split into separate files, use TypeScript to define navigation parameter types, and avoid defining all screens in one file. Recommended organization: one folder per navigator containing navigator definition and related screen components, with top-level App.js only responsible for combining navigators.

  **核心特征 | Key Characteristics:**
  - 模块化：每个导航器及其屏幕作为独立模块 | Modularization: Each navigator and its screens as independent module
  - 类型安全：使用TypeScript的ParamList类型确保参数正确 | Type safety: Use TypeScript ParamList types to ensure parameter correctness
  - 关注点分离：导航逻辑、屏幕组件、业务逻辑分离 | Separation of concerns: Navigation logic, screen components, business logic separated
  - 可维护性：易于扩展新屏幕、修改导航结构 | Maintainability: Easy to add new screens, modify navigation structure

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么要使用TypeScript的ParamList类型？| Why use TypeScript's ParamList type?
     **答案 | Answer:** 实现编译时类型检查，避免传递错误的参数或导航到不存在的屏幕 | Achieve compile-time type checking, avoid passing wrong parameters or navigating to non-existent screens
  2. 应该在哪里定义导航器？| Where should navigators be defined?
     **答案 | Answer:** 独立的navigator文件或模块中，而非App.js | In separate navigator files or modules, not in App.js
  3. 如何避免导航器配置过于臃肿？| How to avoid navigator configuration becoming too bloated?
     **答案 | Answer:** 拆分为多个子导航器，使用嵌套结构 | Split into multiple sub-navigators, use nested structure

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 推荐的项目结构 | Recommended project structure
  /*
  src/
    navigation/
      RootNavigator.tsx         // 根导航器
      StackNavigator.tsx        // Stack导航器定义
      types.ts                  // 导航类型定义
    screens/
      HomeScreen.tsx
      DetailsScreen.tsx
    App.tsx
  */

  // types.ts - 导航参数类型定义 | types.ts - Navigation parameter type definitions
  export type RootStackParamList = {
    Home: undefined; // Home屏幕不需要参数 | Home screen requires no params
    Details: {
      itemId: number;
      itemName: string;
    };
    Profile: {
      userId: number;
    } | undefined; // Profile屏幕参数可选 | Profile screen params optional
  };

  // StackNavigator.tsx - Stack导航器定义 | StackNavigator.tsx - Stack navigator definition
  import React from 'react';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { RootStackParamList } from './types';
  import HomeScreen from '../screens/HomeScreen';
  import DetailsScreen from '../screens/DetailsScreen';
  import ProfileScreen from '../screens/ProfileScreen';

  const Stack = createNativeStackNavigator<RootStackParamList>();

  export function RootStackNavigator() {
    return (
      <Stack.Navigator
        initialRouteName="Home"
        screenOptions={{
          headerStyle: { backgroundColor: '#6200EE' },
          headerTintColor: '#fff',
        }}
      >
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: 'Home' }}
        />
        <Stack.Screen
          name="Details"
          component={DetailsScreen}
          options={({ route }) => ({ title: route.params.itemName })}
        />
        <Stack.Screen
          name="Profile"
          component={ProfileScreen}
        />
      </Stack.Navigator>
    );
  }

  // App.tsx - 应用入口 | App.tsx - App entry
  import React from 'react';
  import { NavigationContainer } from '@react-navigation/native';
  import { RootStackNavigator } from './navigation/StackNavigator';

  export default function App() {
    return (
      <NavigationContainer>
        <RootStackNavigator />
      </NavigationContainer>
    );
  }

  // HomeScreen.tsx - 带类型安全的屏幕组件 | HomeScreen.tsx - Screen component with type safety
  import React from 'react';
  import { View, Button } from 'react-native';
  import { NativeStackScreenProps } from '@react-navigation/native-stack';
  import { RootStackParamList } from '../navigation/types';

  type Props = NativeStackScreenProps<RootStackParamList, 'Home'>;

  export default function HomeScreen({ navigation }: Props) {
    return (
      <View>
        {/* TypeScript会检查参数类型 | TypeScript will check parameter types */}
        <Button
          title="Go to Details"
          onPress={() => {
            navigation.navigate('Details', {
              itemId: 42,
              itemName: 'Sample Item',
              // itemPrice: 19.99, // ❌ TypeScript会报错 - 参数不存在 | TypeScript error - parameter doesn't exist
            });
          }}
        />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - RootStackParamList中undefined的含义是什么？| What does undefined mean in RootStackParamList?
    **答案 | Answer:** 该屏幕不接受参数或参数可选 | The screen accepts no parameters or parameters are optional
  - 为什么使用NativeStackScreenProps类型？| Why use NativeStackScreenProps type?
    **答案 | Answer:** 为navigation和route props提供类型安全 | Provides type safety for navigation and route props

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在不定义ParamList的情况下使用TypeScript？| Can we use TypeScript without defining ParamList?
    **答案 | Answer:** 可以但失去类型安全 - 强烈推荐定义ParamList | Possible but loses type safety - strongly recommended to define ParamList
  - 所有屏幕是否都需要在ParamList中定义？| Do all screens need to be defined in ParamList?
    **答案 | Answer:** 是 - 即使不需要参数，也应定义为undefined | Yes - even if no parameters needed, should be defined as undefined

## 实践项目：双屏导航应用 | Practical Project: Two-Screen Navigation App

### 目标 | Objective
创建一个包含列表屏幕和详情屏幕的应用，应用Day 1学习的所有导航概念：Stack Navigator、参数传递、动态头部配置、导航方法使用。这个项目将巩固移动导航的核心模式，为后续复杂导航奠定基础。

Create an app with a list screen and detail screen, applying all navigation concepts learned in Day 1: Stack Navigator, parameter passing, dynamic header configuration, navigation method usage. This project will consolidate core mobile navigation patterns and lay the foundation for complex navigation ahead.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. Stack Navigator使用什么数据结构管理屏幕？| What data structure does Stack Navigator use to manage screens?
   **答案 | Answer:** 堆栈（Stack），后进先出LIFO | Stack, Last-In-First-Out LIFO
2. 如何在导航时传递参数？| How to pass parameters when navigating?
   **答案 | Answer:** navigation.navigate('Screen', { param1: value1 }) | navigation.navigate('Screen', { param1: value1 })
3. 如何在目标屏幕获取传递的参数？| How to get passed parameters in target screen?
   **答案 | Answer:** route.params.param1 或 route.params?.param1 | route.params.param1 or route.params?.param1

### 步骤 | Steps
1. **环境准备 | Environment Setup**
   - 创建新的Expo项目：`npx create-expo-app NavigationBasics`
   - 安装依赖：`npm install @react-navigation/native @react-navigation/native-stack`
   - 安装原生依赖：`expo install react-native-screens react-native-safe-area-context`

2. **创建屏幕组件 | Create Screen Components**
   - HomeScreen：显示5-10个书籍的列表（使用FlatList或ScrollView）
   - BookDetailScreen：显示选中书籍的详细信息（标题、作者、简介）

3. **配置导航器 | Configure Navigator**
   - 在App.js中设置NavigationContainer和Stack.Navigator
   - 注册两个屏幕到Stack
   - 设置全局头部样式（自定义颜色）

4. **实现参数传递 | Implement Parameter Passing**
   - 点击书籍时导航到BookDetailScreen，传递书籍ID和标题
   - 在BookDetailScreen中获取并显示参数

5. **动态头部配置 | Dynamic Header Configuration**
   - BookDetailScreen的头部标题显示书籍名称（来自params）
   - 添加头部右侧按钮："收藏"（点击时console.log）

### 示例代码 | Example Code
```javascript
// App.js
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from './screens/HomeScreen';
import BookDetailScreen from './screens/BookDetailScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="Home"
        screenOptions={{
          headerStyle: { backgroundColor: '#1E88E5' },
          headerTintColor: '#fff',
          headerTitleStyle: { fontWeight: 'bold' },
        }}
      >
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: 'Book Library' }}
        />
        <Stack.Screen
          name="BookDetail"
          component={BookDetailScreen}
          options={({ route }) => ({
            title: route.params?.bookTitle || 'Book Detail',
            headerRight: () => (
              <Button
                title="Favorite"
                onPress={() => console.log('Add to favorites')}
                color="#fff"
              />
            ),
          })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// screens/HomeScreen.js
import React from 'react';
import { View, Text, FlatList, TouchableOpacity, StyleSheet } from 'react-native';

const BOOKS = [
  { id: '1', title: 'React Native in Action', author: 'Nader Dabit', description: 'Comprehensive guide to React Native development' },
  { id: '2', title: 'Learning React Native', author: 'Bonnie Eisenman', description: 'Building mobile apps with JavaScript' },
  { id: '3', title: 'React Native Cookbook', author: 'Dan Ward', description: 'Practical recipes for React Native' },
];

export default function HomeScreen({ navigation }) {
  const renderBookItem = ({ item }) => (
    <TouchableOpacity
      style={styles.bookItem}
      onPress={() => {
        navigation.navigate('BookDetail', {
          bookId: item.id,
          bookTitle: item.title,
          bookAuthor: item.author,
          bookDescription: item.description,
        });
      }}
    >
      <Text style={styles.bookTitle}>{item.title}</Text>
      <Text style={styles.bookAuthor}>by {item.author}</Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={BOOKS}
        keyExtractor={(item) => item.id}
        renderItem={renderBookItem}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  bookItem: {
    padding: 20,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  bookTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 5,
  },
  bookAuthor: {
    fontSize: 14,
    color: '#666',
  },
});

// screens/BookDetailScreen.js
import React from 'react';
import { View, Text, StyleSheet, Button } from 'react-native';

export default function BookDetailScreen({ route, navigation }) {
  const { bookId, bookTitle, bookAuthor, bookDescription } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>{bookTitle}</Text>
      <Text style={styles.author}>by {bookAuthor}</Text>
      <Text style={styles.description}>{bookDescription}</Text>

      <View style={styles.buttonContainer}>
        <Button
          title="Go Back to Library"
          onPress={() => navigation.goBack()}
        />
      </View>
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
    marginBottom: 10,
  },
  author: {
    fontSize: 18,
    color: '#1E88E5',
    marginBottom: 20,
  },
  description: {
    fontSize: 16,
    lineHeight: 24,
    color: '#333',
  },
  buttonContainer: {
    marginTop: 30,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确应用了Stack Navigator？| Does the project correctly apply Stack Navigator?
2. 参数传递是否通过route.params实现？| Is parameter passing implemented via route.params?
3. 头部标题是否根据书籍名称动态更新？| Does the header title dynamically update based on book name?
4. 是否能够通过goBack()和头部返回按钮返回？| Can you go back via goBack() and header back button?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **navigate vs push深度理解 | Deep Understanding of navigate vs push**
   - **练习描述 | Exercise Description:** 修改BookDetailScreen，添加"查看更多相关书籍"按钮，分别使用navigate和push实现，观察堆栈变化
   - **概念检查 | Concept Check:** navigate会跳转到已存在的屏幕还是创建新实例？push呢？
   - **学习目标 | Learning Objective:** 理解两种导航方法的本质区别及适用场景

2. **导航状态持久化 | Navigation State Persistence**
   - **练习描述 | Exercise Description:** 使用NavigationContainer的onStateChange监听导航变化，将状态保存到console
   - **概念检查 | Concept Check:** 导航状态是什么数据结构？包含哪些信息？
   - **学习目标 | Learning Objective:** 深入理解导航状态树结构

3. **自定义返回行为 | Custom Back Behavior**
   - **练习描述 | Exercise Description:** 在BookDetailScreen中使用beforeRemove事件，离开时弹出确认对话框
   - **概念检查 | Concept Check:** 如何拦截默认的返回行为？
   - **学习目标 | Learning Objective:** 掌握导航生命周期事件的使用

4. **类型安全导航 | Type-Safe Navigation**
   - **练习描述 | Exercise Description:** 将项目转换为TypeScript，定义RootStackParamList类型
   - **概念检查 | Concept Check:** ParamList如何确保参数类型安全？
   - **学习目标 | Learning Objective:** 学习TypeScript在导航中的应用

5. **头部按钮状态管理 | Header Button State Management**
   - **练习描述 | Exercise Description:** 实现"收藏"按钮的切换状态（已收藏/未收藏），使用navigation.setOptions动态更新
   - **概念检查 | Concept Check:** 如何在屏幕内部修改头部配置？
   - **学习目标 | Learning Objective:** 掌握动态头部配置的最佳实践

6. **多屏幕参数传递链 | Multi-Screen Parameter Chain**
   - **练习描述 | Exercise Description:** 添加第三个屏幕AuthorScreen，实现Home->BookDetail->Author的参数传递链
   - **概念检查 | Concept Check:** 如何在多级导航中传递数据？
   - **学习目标 | Learning Objective:** 理解深层导航的数据流动

7. **性能优化：屏幕预渲染 | Performance Optimization: Screen Pre-rendering**
   - **练习描述 | Exercise Description:** 使用navigation.preload()预加载BookDetailScreen
   - **概念检查 | Concept Check:** 预渲染如何提升用户体验？
   - **学习目标 | Learning Objective:** 学习导航性能优化技巧

## 学习资源 | Learning Resources
- [React Navigation Official Documentation](https://reactnavigation.org/docs/getting-started)
- [React Navigation - Hello React Navigation](https://reactnavigation.org/docs/hello-react-navigation)
- [Stack Navigator API Reference](https://reactnavigation.org/docs/native-stack-navigator)
- [Navigation Prop Reference](https://reactnavigation.org/docs/navigation-prop)
- [Route Prop Reference](https://reactnavigation.org/docs/route-prop)
- [TypeScript Guide for React Navigation](https://reactnavigation.org/docs/typescript)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解移动导航与Web路由的核心区别
- [ ] 能够安装和配置React Navigation及其依赖
- [ ] 理解NavigationContainer的作用和配置方法
- [ ] 能够创建Stack Navigator并注册屏幕
- [ ] 掌握navigation.navigate()、push()、goBack()等方法
- [ ] 理解route.params的作用并能正确传递参数
- [ ] 能够配置静态和动态头部选项
- [ ] 完成双屏导航实践项目
- [ ] 理解导航器组织的最佳实践
- [ ] 能够使用TypeScript实现类型安全的导航（可选）

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释Stack Navigator的工作原理、navigation和route props的区别、以及参数传递机制。

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others how Stack Navigator works, the difference between navigation and route props, and the parameter passing mechanism.
