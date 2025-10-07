# React Native入门 - 第5天：导航状态持久化与状态管理集成 | React Native Introduction - Day 5: Navigation State Persistence & State Management Integration

## 学习目标 | Learning Objectives
- 理解导航状态持久化的原理和应用场景 | Understand the principles and use cases of navigation state persistence
- 掌握使用AsyncStorage实现导航状态的保存和恢复 | Master saving and restoring navigation state using AsyncStorage
- 学习使用navigationRef在组件外部访问导航 | Learn to access navigation outside components using navigationRef
- 理解导航与全局状态管理的集成模式 | Understand integration patterns between navigation and global state management
- 掌握useFocusEffect钩子的使用场景和最佳实践 | Master use cases and best practices for the useFocusEffect hook
- 实现基于状态变化的程序化导航 | Implement programmatic navigation based on state changes

## 详细内容 | Detailed Content

### 1. 导航状态持久化基础 | Navigation State Persistence Basics (1小时 | 1 hour)

- **导航状态持久化概念 | Navigation State Persistence Concept**

  **概念定义 | Concept Definition:**
  导航状态持久化是指将应用的当前导航状态（当前屏幕、导航历史栈、屏幕参数等）保存到持久化存储中，使用户在关闭并重新打开应用后能够返回到之前的屏幕位置，提供无缝的用户体验。| Navigation state persistence refers to saving the application's current navigation state (current screen, navigation history stack, screen parameters, etc.) to persistent storage, allowing users to return to their previous screen position after closing and reopening the app, providing a seamless user experience.

  **核心特征 | Key Characteristics:**
  - 自动捕获：通过onStateChange回调自动捕获导航状态变化 | Automatic capture: Automatically capture navigation state changes through onStateChange callback
  - 选择性保存：可以过滤敏感数据和临时状态 | Selective saving: Can filter sensitive data and temporary state
  - 恢复机制：应用启动时读取并恢复上次的导航状态 | Restoration mechanism: Read and restore previous navigation state on app launch
  - 版本兼容：需要处理导航结构变化时的状态迁移 | Version compatibility: Need to handle state migration when navigation structure changes

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 导航状态持久化会自动保存所有屏幕数据吗？| Does navigation state persistence automatically save all screen data?
     **答案 | Answer:** 否 | No - 它只保存导航结构、路由名称和参数，不包括屏幕内部状态或组件数据 | It only saves navigation structure, route names, and parameters, not internal screen state or component data
  2. 用户关闭应用后，导航状态存储在哪里？| Where is navigation state stored after the user closes the app?
     **答案 | Answer:** 持久化存储（如AsyncStorage）| Persistent storage (such as AsyncStorage) - 需要开发者实现存储逻辑 | Developers need to implement the storage logic
  3. 所有类型的应用都应该启用导航状态持久化吗？| Should all types of applications enable navigation state persistence?
     **答案 | Answer:** 否 | No - 认证流程、临时数据或安全敏感的应用可能不适合持久化导航状态 | Authentication flows, temporary data, or security-sensitive apps may not be suitable for persisting navigation state
  4. 导航状态持久化影响应用首次启动速度吗？| Does navigation state persistence affect app first-launch speed?
     **答案 | Answer:** 可能影响 | Potentially - 需要读取存储并解析状态，但影响通常很小 | Requires reading storage and parsing state, but impact is usually minimal

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 基础的导航状态持久化实现 | Basic navigation state persistence implementation
  import { NavigationContainer } from '@react-navigation/native';
  import AsyncStorage from '@react-native-async-storage/async-storage';
  import { useRef, useEffect, useState } from 'react';

  const PERSISTENCE_KEY = 'NAVIGATION_STATE_V1'; // 版本化的key | Versioned key

  function App() {
    // 是否准备好恢复状态 | Ready to restore state
    const [isReady, setIsReady] = useState(false);
    // 初始导航状态 | Initial navigation state
    const [initialState, setInitialState] = useState();

    useEffect(() => {
      const restoreState = async () => {
        try {
          // 从存储中读取导航状态 | Read navigation state from storage
          const savedStateString = await AsyncStorage.getItem(PERSISTENCE_KEY);

          if (savedStateString) {
            const state = JSON.parse(savedStateString);
            setInitialState(state); // 设置初始状态 | Set initial state
          }
        } catch (error) {
          console.log('Failed to restore navigation state:', error);
        } finally {
          setIsReady(true); // 标记准备完成 | Mark as ready
        }
      };

      if (!isReady) {
        restoreState(); // 恢复状态 | Restore state
      }
    }, [isReady]);

    if (!isReady) {
      return null; // 或显示加载指示器 | Or show loading indicator
    }

    return (
      <NavigationContainer
        initialState={initialState} // 传入恢复的状态 | Pass restored state
        onStateChange={(state) => {
          // 每次导航状态变化时保存 | Save on every navigation state change
          AsyncStorage.setItem(PERSISTENCE_KEY, JSON.stringify(state));
        }}
      >
        {/* 你的导航器 | Your navigators */}
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码在什么时候会读取存储的导航状态？| When will this code read the stored navigation state?
    **答案 | Answer:** 在组件首次挂载时，通过useEffect执行restoreState函数 | On first component mount, through the useEffect executing restoreState function
  - 如果AsyncStorage读取失败会发生什么？| What happens if AsyncStorage read fails?
    **答案 | Answer:** 捕获错误并继续，应用将以默认初始状态启动 | Catch error and continue, app will launch with default initial state

  **常见误区检查 | Common Misconception Checks:**
  - 导航状态持久化会保存屏幕内的用户输入数据吗？| Does navigation state persistence save user input data within screens?
    **答案 | Answer:** 否 | No - 只保存导航元数据，屏幕数据需要单独持久化 | Only saves navigation metadata, screen data needs separate persistence
  - 是否需要在每次状态变化时立即写入存储？| Should we write to storage immediately on every state change?
    **答案 | Answer:** 不一定 | Not necessarily - 可以使用防抖优化写入性能 | Can use debouncing to optimize write performance

- **AsyncStorage与导航状态 | AsyncStorage and Navigation State**

  **概念定义 | Concept Definition:**
  AsyncStorage是React Native提供的持久化键值存储系统，适合存储小型结构化数据如导航状态。它是异步的、跨平台的，在iOS上基于原生的文件系统，在Android上基于SQLite。| AsyncStorage is a persistent key-value storage system provided by React Native, suitable for storing small structured data like navigation state. It's asynchronous, cross-platform, based on native file systems on iOS and SQLite on Android.

  **核心特征 | Key Characteristics:**
  - 异步API：所有操作返回Promise，不阻塞UI线程 | Asynchronous API: All operations return Promises, don't block UI thread
  - 字符串存储：只能存储字符串，需要序列化复杂对象 | String storage: Can only store strings, need to serialize complex objects
  - 有限容量：建议存储小于6MB的数据 | Limited capacity: Recommended to store data less than 6MB
  - 应用私有：每个应用有独立的存储空间 | App-private: Each app has its own storage space

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. AsyncStorage可以直接存储JavaScript对象吗？| Can AsyncStorage directly store JavaScript objects?
     **答案 | Answer:** 否 | No - 必须使用JSON.stringify序列化为字符串 | Must serialize to string using JSON.stringify
  2. AsyncStorage的读写操作会阻塞UI渲染吗？| Do AsyncStorage read/write operations block UI rendering?
     **答案 | Answer:** 否 | No - 所有操作都是异步的，不会阻塞主线程 | All operations are asynchronous, won't block main thread
  3. 不同应用可以共享AsyncStorage数据吗？| Can different apps share AsyncStorage data?
     **答案 | Answer:** 否 | No - 每个应用的存储空间是隔离的 | Each app's storage space is isolated
  4. AsyncStorage适合存储大量数据（如图片）吗？| Is AsyncStorage suitable for storing large data (like images)?
     **答案 | Answer:** 否 | No - 建议用于小型文本数据，大文件应使用文件系统 | Recommended for small text data, large files should use file system

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 优化的导航状态持久化（带防抖和错误处理）| Optimized navigation state persistence (with debounce and error handling)
  import { NavigationContainer } from '@react-navigation/native';
  import AsyncStorage from '@react-native-async-storage/async-storage';
  import { useRef, useCallback } from 'react';

  const PERSISTENCE_KEY = 'NAVIGATION_STATE_V1';
  const DEBOUNCE_TIME = 500; // 500ms防抖 | 500ms debounce

  function App() {
    const [isReady, setIsReady] = useState(false);
    const [initialState, setInitialState] = useState();
    const saveTimeoutRef = useRef<NodeJS.Timeout>(); // 防抖计时器 | Debounce timer

    useEffect(() => {
      const restoreState = async () => {
        try {
          const savedStateString = await AsyncStorage.getItem(PERSISTENCE_KEY);

          if (savedStateString) {
            const state = JSON.parse(savedStateString);
            // 可以添加版本检查和状态验证 | Can add version check and state validation
            if (isValidNavigationState(state)) {
              setInitialState(state);
            }
          }
        } catch (error) {
          console.error('Failed to restore navigation state:', error);
          // 可选：上报错误到错误追踪服务 | Optional: Report error to tracking service
        } finally {
          setIsReady(true);
        }
      };

      if (!isReady) {
        restoreState();
      }
    }, [isReady]);

    // 防抖保存函数 | Debounced save function
    const onStateChange = useCallback((state: any) => {
      // 清除之前的定时器 | Clear previous timer
      if (saveTimeoutRef.current) {
        clearTimeout(saveTimeoutRef.current);
      }

      // 设置新的定时器 | Set new timer
      saveTimeoutRef.current = setTimeout(async () => {
        try {
          const stateString = JSON.stringify(state);
          await AsyncStorage.setItem(PERSISTENCE_KEY, stateString);
        } catch (error) {
          console.error('Failed to save navigation state:', error);
        }
      }, DEBOUNCE_TIME);
    }, []);

    if (!isReady) {
      return <LoadingScreen />; // 显示加载屏幕 | Show loading screen
    }

    return (
      <NavigationContainer
        initialState={initialState}
        onStateChange={onStateChange} // 使用防抖版本 | Use debounced version
      >
        <RootNavigator />
      </NavigationContainer>
    );
  }

  // 状态验证函数 | State validation function
  function isValidNavigationState(state: any): boolean {
    // 基本结构验证 | Basic structure validation
    return (
      state &&
      typeof state === 'object' &&
      'routes' in state &&
      Array.isArray(state.routes) &&
      state.routes.length > 0
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用防抖来保存导航状态？| Why use debouncing to save navigation state?
    **答案 | Answer:** 避免频繁写入存储，提高性能，特别是在快速导航时 | Avoid frequent storage writes, improve performance, especially during rapid navigation
  - isValidNavigationState函数的作用是什么？| What is the purpose of the isValidNavigationState function?
    **答案 | Answer:** 验证从存储读取的状态结构有效，防止无效数据导致应用崩溃 | Validate that the state structure read from storage is valid, prevent invalid data from causing app crashes

### 2. 使用navigationRef实现外部导航 | External Navigation with navigationRef (1小时 | 1 hour)

- **navigationRef概念 | navigationRef Concept**

  **概念定义 | Concept Definition:**
  navigationRef是React Navigation提供的引用机制，允许在React组件树外部（如Redux action、service层、工具函数）访问导航功能。它通过createNavigationContainerRef创建，并传递给NavigationContainer组件。| navigationRef is a reference mechanism provided by React Navigation that allows accessing navigation functionality outside the React component tree (such as Redux actions, service layers, utility functions). It's created via createNavigationContainerRef and passed to the NavigationContainer component.

  **核心特征 | Key Characteristics:**
  - 全局访问：可以在任何JavaScript模块中导入使用 | Global access: Can be imported and used in any JavaScript module
  - 类型安全：支持TypeScript类型定义，提供路由参数类型检查 | Type-safe: Supports TypeScript type definitions, provides route parameter type checking
  - 条件检查：需要检查isReady()确保导航器已挂载 | Conditional check: Need to check isReady() to ensure navigator is mounted
  - 功能完整：提供与navigation prop相同的API | Full functionality: Provides same API as navigation prop

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以在NavigationContainer挂载前使用navigationRef吗？| Can navigationRef be used before NavigationContainer is mounted?
     **答案 | Answer:** 否 | No - 必须先检查navigationRef.isReady()为true | Must first check that navigationRef.isReady() is true
  2. navigationRef与navigation prop有什么区别？| What's the difference between navigationRef and navigation prop?
     **答案 | Answer:** navigationRef可在组件外使用，navigation prop仅在屏幕组件内可用 | navigationRef can be used outside components, navigation prop is only available within screen components
  3. 多个NavigationContainer可以共享同一个navigationRef吗？| Can multiple NavigationContainers share the same navigationRef?
     **答案 | Answer:** 否 | No - 每个NavigationContainer需要独立的ref | Each NavigationContainer needs its own ref
  4. 在Redux action中使用navigationRef是否是最佳实践？| Is using navigationRef in Redux actions a best practice?
     **答案 | Answer:** 视情况而定 | It depends - 简单场景可以，复杂应用建议通过action更新状态，组件响应状态变化导航 | Simple cases yes, complex apps better to update state via actions, components respond to state changes for navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 创建和使用navigationRef | Creating and using navigationRef
  import { NavigationContainer, createNavigationContainerRef } from '@react-navigation/native';

  // 1. 创建全局navigationRef（在单独的文件中）| Create global navigationRef (in separate file)
  // src/navigation/navigationRef.ts
  export const navigationRef = createNavigationContainerRef();

  // 导航辅助函数 | Navigation helper functions
  export function navigate(name: string, params?: object) {
    if (navigationRef.isReady()) {
      navigationRef.navigate(name as never, params as never);
    } else {
      console.warn('Navigation is not ready');
    }
  }

  export function goBack() {
    if (navigationRef.isReady() && navigationRef.canGoBack()) {
      navigationRef.goBack();
    }
  }

  export function resetToScreen(name: string, params?: object) {
    if (navigationRef.isReady()) {
      navigationRef.reset({
        index: 0,
        routes: [{ name, params }],
      });
    }
  }

  // 2. 在App组件中连接ref | Connect ref in App component
  // src/App.tsx
  import { navigationRef } from './navigation/navigationRef';

  function App() {
    return (
      <NavigationContainer ref={navigationRef}>
        <RootNavigator />
      </NavigationContainer>
    );
  }

  // 3. 在任何地方使用导航 | Use navigation anywhere
  // src/services/notificationService.ts
  import * as RootNavigation from './navigation/navigationRef';

  export function handleNotificationPress(notification: Notification) {
    // 根据通知类型导航到不同屏幕 | Navigate to different screens based on notification type
    switch (notification.type) {
      case 'message':
        RootNavigation.navigate('Chat', { chatId: notification.chatId });
        break;
      case 'order':
        RootNavigation.navigate('OrderDetail', { orderId: notification.orderId });
        break;
      default:
        RootNavigation.navigate('Home');
    }
  }

  // 4. 在Redux action中使用 | Use in Redux actions
  // src/store/authSlice.ts
  import { createAsyncThunk } from '@reduxjs/toolkit';
  import * as RootNavigation from '../navigation/navigationRef';

  export const logout = createAsyncThunk(
    'auth/logout',
    async (_, { dispatch }) => {
      // 清除用户数据 | Clear user data
      await clearUserData();

      // 重置导航到登录屏幕 | Reset navigation to login screen
      RootNavigation.resetToScreen('Login');

      return null;
    }
  );
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么在navigate函数中检查isReady()？| Why check isReady() in the navigate function?
    **答案 | Answer:** 确保NavigationContainer已挂载，避免导航失败或错误 | Ensure NavigationContainer is mounted, avoid navigation failures or errors
  - resetToScreen函数与普通navigate有什么区别？| What's the difference between resetToScreen and regular navigate?
    **答案 | Answer:** reset清空导航栈并设置新的初始路由，用户无法返回 | reset clears navigation stack and sets new initial route, user cannot go back

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该在所有情况下都使用navigationRef而不是navigation prop？| Should navigationRef be used instead of navigation prop in all cases?
    **答案 | Answer:** 否 | No - 在屏幕组件内优先使用navigation prop，仅在组件外才使用navigationRef | Prefer navigation prop within screen components, use navigationRef only outside components
  - navigationRef可以替代Redux或其他状态管理吗？| Can navigationRef replace Redux or other state management?
    **答案 | Answer:** 否 | No - 它只负责导航控制，不能管理应用状态 | It only handles navigation control, cannot manage application state

- **TypeScript类型安全 | TypeScript Type Safety**

  **概念定义 | Concept Definition:**
  React Navigation支持TypeScript类型定义，可以为导航参数提供完整的类型检查，包括路由名称、参数结构和navigationRef的类型。通过声明全局类型，可以在整个应用中获得类型安全的导航体验。| React Navigation supports TypeScript type definitions, providing complete type checking for navigation parameters, including route names, parameter structures, and navigationRef types. By declaring global types, you can get type-safe navigation throughout the application.

  **核心特征 | Key Characteristics:**
  - 参数类型检查：编译时验证传递的参数类型正确 | Parameter type checking: Compile-time validation of correct parameter types
  - 路由名称验证：防止导航到不存在的屏幕 | Route name validation: Prevent navigation to non-existent screens
  - 智能提示：IDE提供完整的自动补全和类型提示 | IntelliSense: IDE provides complete auto-completion and type hints
  - 重构安全：重命名屏幕时自动更新所有引用 | Refactoring safety: Automatically update all references when renaming screens

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. TypeScript类型定义可以防止运行时导航错误吗？| Can TypeScript type definitions prevent runtime navigation errors?
     **答案 | Answer:** 部分可以 | Partially - 可以防止类型相关错误，但不能防止所有运行时问题（如导航器未挂载）| Can prevent type-related errors, but not all runtime issues (like navigator not mounted)
  2. 为navigationRef添加类型后，是否还需要检查isReady()？| After adding types to navigationRef, is it still necessary to check isReady()?
     **答案 | Answer:** 是 | Yes - 类型检查不能保证导航器已挂载，仍需运行时检查 | Type checking doesn't ensure navigator is mounted, still need runtime check
  3. 类型定义需要在每个使用导航的文件中重复声明吗？| Do type definitions need to be declared repeatedly in every file using navigation?
     **答案 | Answer:** 否 | No - 通过全局类型声明（如@types文件），在整个项目中复用 | Through global type declarations (like @types files), reuse throughout the project

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // TypeScript类型安全的navigationRef | Type-safe navigationRef with TypeScript

  // 1. 定义导航参数类型（src/types/navigation.ts）| Define navigation parameter types
  export type RootStackParamList = {
    Home: undefined; // 无参数的屏幕 | Screen with no parameters
    Profile: { userId: string }; // 必须参数 | Required parameter
    Settings: undefined;
    ArticleDetail: {
      articleId: string;
      highlight?: boolean; // 可选参数 | Optional parameter
    };
    Chat: {
      chatId: string;
      userName: string;
    };
  };

  // 2. 声明全局类型（让整个项目可用）| Declare global types (available project-wide)
  declare global {
    namespace ReactNavigation {
      interface RootParamList extends RootStackParamList {}
    }
  }

  // 3. 创建类型安全的navigationRef | Create type-safe navigationRef
  // src/navigation/navigationRef.ts
  import { createNavigationContainerRef, NavigationContainerRef } from '@react-navigation/native';
  import type { RootStackParamList } from '../types/navigation';

  export const navigationRef = createNavigationContainerRef<RootStackParamList>();

  // 类型安全的导航函数 | Type-safe navigation functions
  export function navigate<RouteName extends keyof RootStackParamList>(
    name: RouteName,
    ...params: undefined extends RootStackParamList[RouteName]
      ? [RootStackParamList[RouteName]?] // 参数可选 | Parameters optional
      : [RootStackParamList[RouteName]]  // 参数必须 | Parameters required
  ) {
    if (navigationRef.isReady()) {
      navigationRef.navigate(name, params[0]);
    }
  }

  // 4. 使用示例（完全类型安全）| Usage examples (fully type-safe)
  import * as RootNavigation from './navigation/navigationRef';

  // ✅ 正确：无参数屏幕 | Correct: Screen with no parameters
  RootNavigation.navigate('Home');

  // ✅ 正确：带必须参数 | Correct: With required parameters
  RootNavigation.navigate('Profile', { userId: '123' });

  // ✅ 正确：带可选参数 | Correct: With optional parameters
  RootNavigation.navigate('ArticleDetail', {
    articleId: 'abc',
    highlight: true
  });

  // ❌ 错误：缺少必须参数 | Error: Missing required parameter
  // RootNavigation.navigate('Profile'); // TypeScript编译错误 | TypeScript compile error

  // ❌ 错误：参数类型不匹配 | Error: Parameter type mismatch
  // RootNavigation.navigate('Profile', { userId: 123 }); // 应该是string | Should be string

  // ❌ 错误：不存在的屏幕 | Error: Non-existent screen
  // RootNavigation.navigate('NonExistent'); // TypeScript编译错误 | TypeScript compile error
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用条件类型 `undefined extends RootStackParamList[RouteName]`？| Why use the conditional type `undefined extends RootStackParamList[RouteName]`?
    **答案 | Answer:** 区分必须参数和可选参数的屏幕，提供更好的类型检查体验 | Distinguish between screens with required and optional parameters, provide better type checking experience
  - 全局类型声明有什么好处？| What are the benefits of global type declarations?
    **答案 | Answer:** 在使用useNavigation等hooks时自动获得类型推断，无需手动指定类型 | Automatically get type inference when using hooks like useNavigation, no need to manually specify types

### 3. 导航与全局状态管理集成 | Navigation and Global State Management Integration (1.5小时 | 1.5 hours)

- **基于状态的导航模式 | State-Based Navigation Pattern**

  **概念定义 | Concept Definition:**
  基于状态的导航模式是指将导航逻辑与全局状态管理（如Redux、Zustand）集成，通过监听状态变化触发导航操作。这种模式将导航决策集中在状态层，使应用逻辑更清晰、可测试，并支持复杂的导航流程。| State-based navigation pattern refers to integrating navigation logic with global state management (like Redux, Zustand), triggering navigation operations by listening to state changes. This pattern centralizes navigation decisions in the state layer, making application logic clearer, testable, and supporting complex navigation flows.

  **核心特征 | Key Characteristics:**
  - 单向数据流：状态变化 → 导航响应，保持可预测性 | Unidirectional data flow: State change → Navigation response, maintains predictability
  - 声明式导航：基于当前状态决定显示哪个屏幕 | Declarative navigation: Decide which screen to show based on current state
  - 集中管理：导航逻辑与业务逻辑统一管理 | Centralized management: Navigation logic and business logic managed together
  - 可测试：导航行为可通过测试状态变化来验证 | Testable: Navigation behavior can be verified by testing state changes

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应该在Redux action中直接调用navigation.navigate()吗？| Should navigation.navigate() be called directly in Redux actions?
     **答案 | Answer:** 不推荐 | Not recommended - 最好通过更新状态，让组件响应状态变化进行导航 | Better to update state, let components respond to state changes for navigation
  2. 条件渲染导航器和使用navigation.navigate()有什么区别？| What's the difference between conditional navigator rendering and using navigation.navigate()?
     **答案 | Answer:** 条件渲染改变导航结构，navigate在现有结构内导航 | Conditional rendering changes navigation structure, navigate operates within existing structure
  3. 基于状态的导航更容易测试吗？| Is state-based navigation easier to test?
     **答案 | Answer:** 是 | Yes - 只需测试状态逻辑，不需要模拟导航API | Only need to test state logic, no need to mock navigation API
  4. 所有导航都应该通过状态管理吗？| Should all navigation go through state management?
     **答案 | Answer:** 否 | No - 简单的UI导航可以直接使用navigation prop | Simple UI navigation can directly use navigation prop

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 使用Redux实现基于状态的导航 | State-based navigation with Redux

  // 1. 定义认证状态（src/store/authSlice.ts）| Define auth state
  import { createSlice, PayloadAction } from '@reduxjs/toolkit';

  interface AuthState {
    isAuthenticated: boolean;
    user: User | null;
    onboardingCompleted: boolean;
  }

  const initialState: AuthState = {
    isAuthenticated: false,
    user: null,
    onboardingCompleted: false,
  };

  const authSlice = createSlice({
    name: 'auth',
    initialState,
    reducers: {
      loginSuccess: (state, action: PayloadAction<User>) => {
        state.isAuthenticated = true;
        state.user = action.payload;
      },
      logout: (state) => {
        state.isAuthenticated = false;
        state.user = null;
      },
      completeOnboarding: (state) => {
        state.onboardingCompleted = true;
      },
    },
  });

  export const { loginSuccess, logout, completeOnboarding } = authSlice.actions;
  export default authSlice.reducer;

  // 2. 根据状态条件渲染导航器 | Conditionally render navigators based on state
  // src/navigation/RootNavigator.tsx
  import { useSelector } from 'react-redux';
  import { RootState } from '../store';

  function RootNavigator() {
    const { isAuthenticated, onboardingCompleted } = useSelector(
      (state: RootState) => state.auth
    );

    // 基于认证状态决定导航结构 | Decide navigation structure based on auth state
    if (!isAuthenticated) {
      return <AuthStack />; // 未登录：显示认证流程 | Not logged in: Show auth flow
    }

    if (!onboardingCompleted) {
      return <OnboardingStack />; // 首次登录：显示引导流程 | First login: Show onboarding
    }

    return <AppStack />; // 已登录：显示主应用 | Logged in: Show main app
  }

  // 3. 组件中响应状态变化进行导航 | Navigate in response to state changes in components
  // src/screens/LoginScreen.tsx
  import { useDispatch } from 'react-redux';
  import { loginSuccess } from '../store/authSlice';

  function LoginScreen() {
    const dispatch = useDispatch();
    // 不需要navigation prop，状态变化会自动切换导航器 | No need for navigation prop, state change automatically switches navigator

    const handleLogin = async (credentials: Credentials) => {
      try {
        const user = await api.login(credentials);
        dispatch(loginSuccess(user)); // 更新状态 → 触发RootNavigator重新渲染 | Update state → Trigger RootNavigator re-render
        // 导航自动发生，无需手动调用navigate | Navigation happens automatically, no manual navigate call
      } catch (error) {
        // 处理错误 | Handle error
      }
    };

    return (
      <View>
        <LoginForm onSubmit={handleLogin} />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - RootNavigator如何响应状态变化？| How does RootNavigator respond to state changes?
    **答案 | Answer:** 通过useSelector订阅Redux状态，状态变化触发重新渲染和条件判断 | Subscribes to Redux state via useSelector, state changes trigger re-render and conditional logic
  - 为什么LoginScreen不需要使用navigation.navigate()？| Why doesn't LoginScreen need to use navigation.navigate()?
    **答案 | Answer:** 通过dispatch更新状态，RootNavigator根据新状态自动渲染正确的导航器 | Updates state via dispatch, RootNavigator automatically renders correct navigator based on new state

- **使用Zustand的轻量级集成 | Lightweight Integration with Zustand**

  **概念定义 | Concept Definition:**
  Zustand是一个轻量级状态管理库，提供简单的API和最小的样板代码。与React Navigation集成时，可以创建导航相关的store，通过hooks访问和更新导航状态，实现解耦的导航逻辑。| Zustand is a lightweight state management library providing simple API and minimal boilerplate. When integrated with React Navigation, you can create navigation-related stores, access and update navigation state through hooks, achieving decoupled navigation logic.

  **核心特征 | Key Characteristics:**
  - 简洁API：无需provider包装，直接使用hooks | Concise API: No provider wrapper needed, use hooks directly
  - 细粒度更新：只重新渲染订阅了变化状态的组件 | Fine-grained updates: Only re-render components subscribed to changed state
  - TypeScript友好：完整的类型推断支持 | TypeScript friendly: Full type inference support
  - 中间件支持：可添加持久化、日志等中间件 | Middleware support: Can add persistence, logging, etc. middleware

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Zustand需要在根组件添加Provider吗？| Does Zustand require adding a Provider in the root component?
     **答案 | Answer:** 否 | No - Zustand使用模块级单例，无需Provider | Zustand uses module-level singleton, no Provider needed
  2. Zustand的store可以在React组件外部使用吗？| Can Zustand stores be used outside React components?
     **答案 | Answer:** 是 | Yes - 通过`getState()`和`setState()`直接访问 | Access directly via `getState()` and `setState()`
  3. 使用Zustand比Redux更适合小型应用吗？| Is Zustand more suitable than Redux for small applications?
     **答案 | Answer:** 通常是 | Usually yes - 样板代码更少，学习曲线更平缓 | Less boilerplate, gentler learning curve
  4. Zustand可以替代React Navigation的状态管理吗？| Can Zustand replace React Navigation's state management?
     **答案 | Answer:** 否 | No - Zustand管理应用状态，React Navigation管理导航状态，两者互补 | Zustand manages app state, React Navigation manages navigation state, they complement each other

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 使用Zustand管理导航相关状态 | Managing navigation-related state with Zustand

  // 1. 创建导航相关的store（src/store/navigationStore.ts）| Create navigation-related store
  import { create } from 'zustand';
  import { persist, createJSONStorage } from 'zustand/middleware';
  import AsyncStorage from '@react-native-async-storage/async-storage';

  interface NavigationStore {
    // 状态 | State
    pendingDeepLink: string | null; // 待处理的深链接 | Pending deep link
    modalVisible: boolean; // 全局modal状态 | Global modal state
    modalContent: React.ReactNode | null;

    // Actions
    setPendingDeepLink: (url: string | null) => void;
    showModal: (content: React.ReactNode) => void;
    hideModal: () => void;
  }

  export const useNavigationStore = create<NavigationStore>()(
    persist(
      (set) => ({
        pendingDeepLink: null,
        modalVisible: false,
        modalContent: null,

        setPendingDeepLink: (url) => set({ pendingDeepLink: url }),

        showModal: (content) => set({
          modalVisible: true,
          modalContent: content
        }),

        hideModal: () => set({
          modalVisible: false,
          modalContent: null
        }),
      }),
      {
        name: 'navigation-storage', // 持久化key | Persistence key
        storage: createJSONStorage(() => AsyncStorage),
        partialize: (state) => ({
          // 只持久化pendingDeepLink，不持久化modal状态 | Only persist pendingDeepLink, not modal state
          pendingDeepLink: state.pendingDeepLink
        }),
      }
    )
  );

  // 2. 在组件中使用（src/navigation/RootNavigator.tsx）| Use in components
  import { useNavigationStore } from '../store/navigationStore';
  import { useEffect } from 'react';

  function RootNavigator() {
    const navigation = useNavigation();
    const pendingDeepLink = useNavigationStore(state => state.pendingDeepLink); // 细粒度订阅 | Fine-grained subscription
    const setPendingDeepLink = useNavigationStore(state => state.setPendingDeepLink);

    // 处理待处理的深链接 | Handle pending deep link
    useEffect(() => {
      if (pendingDeepLink && navigation) {
        // 解析并导航到深链接 | Parse and navigate to deep link
        const { screen, params } = parseDeepLink(pendingDeepLink);
        navigation.navigate(screen, params);
        setPendingDeepLink(null); // 清除 | Clear
      }
    }, [pendingDeepLink, navigation]);

    return (
      <>
        <Stack.Navigator>
          {/* 你的屏幕 | Your screens */}
        </Stack.Navigator>
        <GlobalModal /> {/* 全局modal组件 | Global modal component */}
      </>
    );
  }

  // 3. 全局modal组件 | Global modal component
  function GlobalModal() {
    const { modalVisible, modalContent, hideModal } = useNavigationStore();

    return (
      <Modal visible={modalVisible} onRequestClose={hideModal} transparent>
        <View style={styles.modalOverlay}>
          <View style={styles.modalContent}>
            {modalContent}
            <Button title="Close" onPress={hideModal} />
          </View>
        </View>
      </Modal>
    );
  }

  // 4. 在任何地方触发导航相关操作 | Trigger navigation-related actions anywhere
  // src/services/notificationService.ts
  import { useNavigationStore } from '../store/navigationStore';

  export function handleNotificationPress(notification: Notification) {
    const { setPendingDeepLink, showModal } = useNavigationStore.getState(); // 在非组件中使用 | Use outside components

    if (notification.type === 'deep_link') {
      setPendingDeepLink(notification.url); // 设置待处理链接 | Set pending link
    } else {
      showModal(<NotificationDetail notification={notification} />);
    }
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用 `useNavigationStore(state => state.pendingDeepLink)` 而不是 `useNavigationStore()`？| Why use `useNavigationStore(state => state.pendingDeepLink)` instead of `useNavigationStore()`?
    **答案 | Answer:** 细粒度订阅，只在pendingDeepLink变化时重新渲染，提高性能 | Fine-grained subscription, only re-render when pendingDeepLink changes, improves performance
  - partialize选项的作用是什么？| What is the purpose of the partialize option?
    **答案 | Answer:** 选择性持久化store的部分状态，避免保存临时或敏感数据 | Selectively persist part of the store state, avoid saving temporary or sensitive data

  **常见误区检查 | Common Misconception Checks:**
  - Zustand可以完全替代React Navigation吗？| Can Zustand completely replace React Navigation?
    **答案 | Answer:** 否 | No - Zustand是状态管理工具，不能提供导航功能（屏幕栈、转场动画等）| Zustand is a state management tool, cannot provide navigation functionality (screen stacks, transition animations, etc.)
  - 是否应该将所有导航逻辑都放在Zustand store中？| Should all navigation logic be put in Zustand stores?
    **答案 | Answer:** 否 | No - 只将需要跨组件共享或持久化的导航状态放入store | Only put navigation state that needs cross-component sharing or persistence in store

