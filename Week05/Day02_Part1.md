# React Native入门 - 第32天：认证流程导航模式 (Part 1) | React Native Introduction - Day 32: Authentication Flow Navigation Pattern (Part 1)

## 学习目标 | Learning Objectives
- 理解移动应用中认证流程的导航架构模式 | Understand navigation architecture patterns for authentication flows in mobile apps
- 掌握基于认证状态的条件渲染导航结构 | Master conditional rendering of navigation structures based on auth state
- 学会创建和管理独立的AuthStack和AppStack导航器 | Learn to create and manage separate AuthStack and AppStack navigators
- 实现React Context进行全局认证状态管理 | Implement React Context for global authentication state management
- 理解移动应用中导航结构如何随认证状态动态变化 | Understand how navigation structure changes dynamically based on authentication state
- 掌握登录、登出功能的实现及其对导航的影响 | Master implementation of sign in/sign out functionality and its impact on navigation

## 详细内容 | Detailed Content

### 1. 认证流程导航模式基础 | Authentication Flow Navigation Pattern Fundamentals (1.5小时 | 1.5 hours)

- **认证流程导航模式 | Authentication Flow Navigation Pattern**

  **概念定义 | Concept Definition:**
  认证流程导航模式是移动应用中处理已登录和未登录用户状态的行业标准架构方法。它通过在根级别使用条件渲染，根据用户的认证状态动态切换完全不同的导航结构（如AuthStack和AppStack），而不是在单一导航器中隐藏或显示某些屏幕。| Authentication Flow Navigation Pattern is the industry-standard architectural approach for handling logged-in vs logged-out user states in mobile apps. It uses conditional rendering at the root level to dynamically switch between completely different navigation structures (like AuthStack and AppStack) based on user authentication state, rather than hiding or showing certain screens within a single navigator.

  **核心特征 | Key Characteristics:**
  - 特征1：使用两个独立的导航栈（AuthStack用于登录/注册，AppStack用于主应用） | Uses two separate navigation stacks (AuthStack for login/signup, AppStack for main app)
  - 特征2：根据认证状态在根级别条件渲染不同的导航器 | Conditionally renders different navigators at the root level based on auth state
  - 特征3：认证状态改变时，整个导航结构会完全替换，而不是导航到另一个屏幕 | When auth state changes, the entire navigation structure is completely replaced, not navigated to another screen
  - 特征4：防止未认证用户访问受保护的应用内容 | Prevents unauthenticated users from accessing protected app content
  - 特征5：提供清晰的关注点分离和可维护的架构 | Provides clear separation of concerns and maintainable architecture

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在认证流程模式中，AuthStack和AppStack是在同一个导航器中的不同屏幕吗？| In the authentication flow pattern, are AuthStack and AppStack different screens within the same navigator?
     **答案 | Answer:** 否 | No - 它们是两个完全独立的导航器，根据认证状态条件渲染其中一个 | They are two completely separate navigators, with one conditionally rendered based on auth state
  2. 当用户登录时，应用是"导航"到主应用还是"替换"整个导航结构？| When a user logs in, does the app "navigate" to the main app or "replace" the entire navigation structure?
     **答案 | Answer:** 替换 | Replace - 整个导航树从AuthStack替换为AppStack，这不是导航动作 | The entire navigation tree is replaced from AuthStack to AppStack, this is not a navigation action
  3. 为什么不能在单一导航器中简单隐藏/显示登录屏幕？| Why can't we simply hide/show login screens within a single navigator?
     **答案 | Answer:** 因为这样会保留导航历史，用户可能通过返回按钮访问不应访问的屏幕；而独立导航栈确保完全隔离和清晰的导航流程 | Because this would preserve navigation history, allowing users to potentially navigate back to screens they shouldn't access; separate navigation stacks ensure complete isolation and clear navigation flows
  4. 认证状态应该存储在哪里才能在根级别控制导航器的渲染？| Where should authentication state be stored to control navigator rendering at the root level?
     **答案 | Answer:** 在全局状态管理中（如React Context、Redux或AsyncStorage），以便根组件可以访问并根据它条件渲染导航器 | In global state management (like React Context, Redux, or AsyncStorage) so the root component can access it and conditionally render navigators based on it

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 认证流程导航模式的基本结构 | Basic structure of authentication flow navigation pattern
  import React from 'react';
  import { NavigationContainer } from '@react-navigation/native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  // 创建两个独立的导航栈 | Create two separate navigation stacks
  const AuthStack = createNativeStackNavigator();
  const AppStack = createNativeStackNavigator();

  // 未认证用户看到的导航结构 | Navigation structure for unauthenticated users
  function AuthNavigator() {
    return (
      <AuthStack.Navigator>
        <AuthStack.Screen name="Login" component={LoginScreen} />
        <AuthStack.Screen name="Signup" component={SignupScreen} />
      </AuthStack.Navigator>
    );
  }

  // 已认证用户看到的导航结构 | Navigation structure for authenticated users
  function AppNavigator() {
    return (
      <AppStack.Navigator>
        <AppStack.Screen name="Home" component={HomeScreen} />
        <AppStack.Screen name="Profile" component={ProfileScreen} />
      </AppStack.Navigator>
    );
  }

  // 根级别的条件渲染 | Conditional rendering at root level
  function RootNavigator() {
    const [isSignedIn, setIsSignedIn] = React.useState(false); // 模拟认证状态 | Simulated auth state

    return (
      <NavigationContainer>
        {isSignedIn ? <AppNavigator /> : <AuthNavigator />}
      </NavigationContainer>
    );
  }

  export default RootNavigator;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，如果isSignedIn从false变为true会发生什么？| What happens in this code when isSignedIn changes from false to true?
    **答案 | Answer:** 整个导航树从AuthNavigator（包含Login和Signup屏幕）替换为AppNavigator（包含Home和Profile屏幕），用户立即看到主应用界面 | The entire navigation tree is replaced from AuthNavigator (containing Login and Signup screens) to AppNavigator (containing Home and Profile screens), and the user immediately sees the main app interface
  - 如果用户在AppNavigator中按下返回按钮，能返回到LoginScreen吗？| If a user presses the back button in AppNavigator, can they go back to LoginScreen?
    **答案 | Answer:** 不能，因为AuthNavigator和AppNavigator是完全独立的导航栈，没有共享的导航历史 | No, because AuthNavigator and AppNavigator are completely separate navigation stacks with no shared navigation history

  **常见误区检查 | Common Misconception Checks:**
  - 新手可能认为应该使用navigation.navigate('Home')来实现登录后跳转，这样对吗？| Beginners might think they should use navigation.navigate('Home') to jump to the main app after login. Is this correct?
    **答案 | Answer:** 不对。正确方式是改变认证状态（如setIsSignedIn(true)），让根组件重新渲染并替换整个导航结构。navigation.navigate只能在同一导航器的屏幕间导航 | No. The correct approach is to change the auth state (like setIsSignedIn(true)), causing the root component to re-render and replace the entire navigation structure. navigation.navigate only works between screens within the same navigator
  - 有人可能会问：为什么不把Login屏幕放在AppStack中并设置headerShown: false？| Someone might ask: Why not put the Login screen in AppStack and set headerShown: false?
    **答案 | Answer:** 这违反了安全和架构原则：1) 用户仍可通过开发工具或返回手势访问受保护屏幕 2) 导航历史混乱 3) 难以管理不同用户状态的导航流程。独立导航栈提供了清晰的边界和安全隔离 | This violates security and architectural principles: 1) Users could still access protected screens via dev tools or back gestures 2) Navigation history becomes confusing 3) It's hard to manage navigation flows for different user states. Separate navigation stacks provide clear boundaries and security isolation

- **React Context进行认证状态管理 | React Context for Authentication State Management**

  **概念定义 | Concept Definition:**
  React Context是React提供的全局状态管理机制，在认证流程中用于在整个应用树中共享认证状态（如isSignedIn、userToken、user信息）和认证操作方法（如signIn、signOut）。它允许根导航组件和应用内任何深层嵌套的组件访问和修改认证状态，而无需通过props逐层传递。| React Context is React's built-in global state management mechanism, used in authentication flows to share authentication state (like isSignedIn, userToken, user info) and authentication operation methods (like signIn, signOut) throughout the entire app tree. It allows both the root navigation component and any deeply nested components within the app to access and modify auth state without prop drilling.

  **核心特征 | Key Characteristics:**
  - 特征1：提供全局访问认证状态，无需props逐层传递 | Provides global access to auth state without prop drilling
  - 特征2：集中管理认证相关的状态和操作方法 | Centrally manages auth-related state and operation methods
  - 特征3：状态改变时自动触发依赖组件的重新渲染 | Automatically triggers re-rendering of dependent components when state changes
  - 特征4：可以持久化认证状态到AsyncStorage以实现"记住登录" | Can persist auth state to AsyncStorage for "remember me" functionality
  - 特征5：为认证流程提供单一数据源（Single Source of Truth） | Provides a Single Source of Truth for authentication flows

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果在LoginScreen中调用signIn方法改变Context中的认证状态，根导航组件会重新渲染吗？| If we call the signIn method in LoginScreen to change auth state in Context, will the root navigation component re-render?
     **答案 | Answer:** 是 | Yes - 所有使用该Context的组件（包括根导航组件）都会在Context状态改变时重新渲染 | All components using that Context (including the root navigation component) will re-render when Context state changes
  2. AuthContext应该放在NavigationContainer之内还是之外？| Should AuthContext be placed inside or outside NavigationContainer?
     **答案 | Answer:** 之外（包裹NavigationContainer）| Outside (wrapping NavigationContainer) - 因为根导航组件需要访问认证状态来决定渲染哪个导航器 | Because the root navigation component needs to access auth state to decide which navigator to render
  3. 使用Context存储用户token和直接存储在组件state中有什么区别？| What's the difference between storing user token in Context vs directly in component state?
     **答案 | Answer:** Context中的token可以在整个应用中访问（如API请求、Profile屏幕），而组件state只能在该组件及其子组件中访问，需要大量prop传递 | Token in Context can be accessed throughout the entire app (like API requests, Profile screen), while component state is only accessible in that component and its children, requiring extensive prop drilling
  4. 如果应用关闭后重新打开，Context中的认证状态会保留吗？| If the app is closed and reopened, will the auth state in Context be preserved?
     **答案 | Answer:** 否，除非结合AsyncStorage持久化 | No, unless combined with AsyncStorage for persistence - Context状态在应用重启时会丢失，需要在应用加载时从AsyncStorage读取并恢复认证状态 | Context state is lost when the app restarts; you need to read from AsyncStorage and restore auth state when the app loads

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 创建认证Context | Creating Authentication Context
  import React, { createContext, useContext, useState, useEffect } from 'react';
  import AsyncStorage from '@react-native-async-storage/async-storage';

  // 定义Context的类型结构 | Define Context type structure
  const AuthContext = createContext({
    isSignedIn: false,
    userToken: null,
    signIn: (token) => {},
    signOut: () => {},
    isLoading: true, // 用于应用启动时检查已存储的认证状态 | For checking stored auth state on app startup
  });

  // AuthContext Provider组件 | AuthContext Provider component
  export function AuthProvider({ children }) {
    const [userToken, setUserToken] = useState(null);
    const [isLoading, setIsLoading] = useState(true);

    // 应用启动时恢复认证状态 | Restore auth state on app startup
    useEffect(() => {
      const bootstrapAsync = async () => {
        try {
          const token = await AsyncStorage.getItem('userToken');
          setUserToken(token);
        } catch (e) {
          console.error('恢复token失败 | Failed to restore token:', e);
        } finally {
          setIsLoading(false);
        }
      };

      bootstrapAsync();
    }, []);

    // 登录方法：保存token到状态和AsyncStorage | Sign in method: save token to state and AsyncStorage
    const signIn = async (token) => {
      try {
        await AsyncStorage.setItem('userToken', token);
        setUserToken(token);
      } catch (e) {
        console.error('保存token失败 | Failed to save token:', e);
      }
    };

    // 登出方法：清除token | Sign out method: clear token
    const signOut = async () => {
      try {
        await AsyncStorage.removeItem('userToken');
        setUserToken(null);
      } catch (e) {
        console.error('删除token失败 | Failed to remove token:', e);
      }
    };

    // Context值包含状态和操作方法 | Context value includes state and operation methods
    const authContextValue = {
      isSignedIn: !!userToken, // !! 将token转换为布尔值 | !! converts token to boolean
      userToken,
      signIn,
      signOut,
      isLoading,
    };

    return (
      <AuthContext.Provider value={authContextValue}>
        {children}
      </AuthContext.Provider>
    );
  }

  // 自定义Hook简化Context使用 | Custom Hook to simplify Context usage
  export function useAuth() {
    const context = useContext(AuthContext);
    if (!context) {
      throw new Error('useAuth必须在AuthProvider内部使用 | useAuth must be used within AuthProvider');
    }
    return context;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，isLoading的作用是什么？为什么需要它？| What is the purpose of isLoading in this code? Why do we need it?
    **答案 | Answer:** isLoading用于表示应用正在从AsyncStorage恢复认证状态的过程。在这个过程中（通常很短暂），我们不应该渲染任何导航器，而应该显示启动画面（Splash Screen），避免在已登录用户看到登录屏幕的闪烁 | isLoading indicates the app is in the process of restoring auth state from AsyncStorage. During this period (usually brief), we shouldn't render any navigator but should show a Splash Screen to avoid a flicker where logged-in users see the login screen
  - signIn方法为什么既设置state又保存到AsyncStorage？| Why does the signIn method both set state and save to AsyncStorage?
    **答案 | Answer:** 设置state（setUserToken）立即更新UI，使应用能立即切换到AppStack；保存到AsyncStorage则持久化认证状态，使用户下次打开应用时仍保持登录状态 | Setting state (setUserToken) immediately updates the UI, allowing the app to switch to AppStack right away; saving to AsyncStorage persists the auth state so users remain logged in when they reopen the app

  **常见误区检查 | Common Misconception Checks:**
  - 有人可能认为应该在每个需要认证状态的组件中都读取AsyncStorage，这样做对吗？| Someone might think they should read AsyncStorage in every component that needs auth state. Is this correct?
    **答案 | Answer:** 不对。这样会导致大量重复代码、性能问题（AsyncStorage读取是异步操作）和状态不一致。正确方式是在应用启动时读取一次到Context，之后所有组件从Context获取 | No. This would lead to massive code duplication, performance issues (AsyncStorage reads are async operations), and state inconsistency. The correct approach is to read once on app startup into Context, then all components get it from Context
  - 新手可能会问：如果signIn失败（如网络错误），应该怎么处理？| Beginners might ask: If signIn fails (like network error), how should we handle it?
    **答案 | Answer:** signIn方法应该返回成功/失败状态或抛出错误。调用组件（LoginScreen）应该使用try-catch捕获错误并向用户显示错误消息。认证状态只有在成功时才应该改变 | The signIn method should return success/failure status or throw an error. The calling component (LoginScreen) should use try-catch to catch errors and show error messages to users. Auth state should only change on success

### 2. 构建AuthStack和AppStack导航器 | Building AuthStack and AppStack Navigators (1小时 | 1 hour)

- **AuthStack导航器结构 | AuthStack Navigator Structure**

  **概念定义 | Concept Definition:**
  AuthStack是专门为未认证用户设计的独立导航栈，包含登录（Login）和注册（Signup）等认证相关屏幕。它通常使用Stack Navigator实现，提供简洁的认证流程，不包含Tab导航或其他复杂导航模式，确保用户专注于完成认证。| AuthStack is a dedicated navigation stack designed for unauthenticated users, containing authentication-related screens like Login and Signup. It's typically implemented using Stack Navigator, providing a clean authentication flow without Tab navigation or other complex navigation patterns, ensuring users focus on completing authentication.

  **核心特征 | Key Characteristics:**
  - 特征1：只包含认证相关屏幕（Login、Signup、ForgotPassword等） | Contains only authentication-related screens (Login, Signup, ForgotPassword, etc.)
  - 特征2：通常使用简洁的header样式或无header设计 | Usually uses clean header styles or headerless design
  - 特征3：Login通常是初始屏幕（initialRouteName） | Login is usually the initial screen (initialRouteName)
  - 特征4：导航流程简单直接（Login ↔ Signup ↔ ForgotPassword） | Navigation flow is simple and direct (Login ↔ Signup ↔ ForgotPassword)
  - 特征5：完成认证后不会"导航"到AppStack，而是通过改变认证状态触发根组件重新渲染 | After completing auth, doesn't "navigate" to AppStack but changes auth state to trigger root component re-render

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. AuthStack中应该包含HomeScreen或ProfileScreen吗？| Should AuthStack include HomeScreen or ProfileScreen?
     **答案 | Answer:** 否 | No - 这些是应用主功能屏幕，应该在AppStack中。AuthStack只包含认证相关屏幕 | These are main app feature screens and should be in AppStack. AuthStack only contains auth-related screens
  2. Login屏幕的"登录"按钮应该调用navigation.navigate('Home')吗？| Should the "Sign In" button on Login screen call navigation.navigate('Home')?
     **答案 | Answer:** 否 | No - 应该调用signIn方法改变认证状态，让根组件替换整个导航结构为AppStack | It should call signIn method to change auth state, causing the root component to replace the entire navigation structure with AppStack
  3. AuthStack需要Tab Navigator吗？| Does AuthStack need Tab Navigator?
     **答案 | Answer:** 通常不需要 | Usually no - AuthStack通常是简单的Stack Navigator，因为认证流程是线性的。Tab Navigator用于应用主功能的并行访问，应该在AppStack中 | AuthStack is usually a simple Stack Navigator because the auth flow is linear. Tab Navigator is for parallel access to main app features and should be in AppStack
  4. 如果用户在SignupScreen完成注册，下一步应该做什么？| If a user completes signup on SignupScreen, what should happen next?
     **答案 | Answer:** 调用signIn保存认证token，这会自动触发根组件渲染AppStack。或者导航回Login让用户手动登录，取决于产品需求 | Call signIn to save auth token, which automatically triggers root component to render AppStack. Or navigate back to Login for manual login, depending on product requirements

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // AuthStack导航器实现 | AuthStack Navigator Implementation
  import React from 'react';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import LoginScreen from '../screens/LoginScreen';
  import SignupScreen from '../screens/SignupScreen';
  import ForgotPasswordScreen from '../screens/ForgotPasswordScreen';

  const Stack = createNativeStackNavigator();

  function AuthNavigator() {
    return (
      <Stack.Navigator
        initialRouteName="Login" // Login作为初始屏幕 | Login as initial screen
        screenOptions={{
          headerStyle: {
            backgroundColor: '#6200ee', // 统一的header背景色 | Unified header background
          },
          headerTintColor: '#fff', // header文字颜色 | Header text color
          headerTitleStyle: {
            fontWeight: 'bold',
          },
          // 可以设置headerShown: false实现完全自定义的认证UI
          // Can set headerShown: false for completely custom auth UI
        }}
      >
        <Stack.Screen
          name="Login"
          component={LoginScreen}
          options={{
            title: '登录 | Sign In',
            // Login屏幕通常隐藏返回按钮 | Login screen usually hides back button
            headerLeft: () => null,
          }}
        />
        <Stack.Screen
          name="Signup"
          component={SignupScreen}
          options={{
            title: '注册 | Sign Up',
          }}
        />
        <Stack.Screen
          name="ForgotPassword"
          component={ForgotPasswordScreen}
          options={{
            title: '忘记密码 | Forgot Password',
          }}
        />
      </Stack.Navigator>
    );
  }

  export default AuthNavigator;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么Login屏幕设置了headerLeft: () => null？| Why is headerLeft: () => null set for the Login screen?
    **答案 | Answer:** 这隐藏了返回按钮，因为Login是认证流程的起点，用户无需返回到之前的屏幕。这也防止用户尝试返回到不存在的屏幕导致应用崩溃 | This hides the back button because Login is the starting point of the auth flow, and users don't need to go back to a previous screen. It also prevents users from attempting to go back to a non-existent screen, which could crash the app
  - 如果所有认证屏幕都不需要header，应该如何配置？| If all auth screens don't need headers, how should we configure?
    **答案 | Answer:** 在Stack.Navigator的screenOptions中设置headerShown: false，这会应用到所有屏幕。个别屏幕如需header可在其options中覆盖 | Set headerShown: false in Stack.Navigator's screenOptions, which applies to all screens. Individual screens can override in their options if needed

- **AppStack导航器结构（含Tab Navigator）| AppStack Navigator Structure (with Tab Navigator)**

  **概念定义 | Concept Definition:**
  AppStack是为已认证用户设计的主应用导航结构，通常包含Tab Navigator作为主要导航方式，每个Tab又可以包含自己的Stack Navigator以支持深层导航（如列表→详情）。这种嵌套结构（Tab Navigator包含多个Stack Navigator）是移动应用的标准架构模式，提供了既能并行访问主要功能（Tab）又能深入内容层次（Stack）的用户体验。| AppStack is the main app navigation structure designed for authenticated users, typically containing Tab Navigator as the primary navigation method, with each Tab containing its own Stack Navigator to support deep navigation (like List → Detail). This nested structure (Tab Navigator containing multiple Stack Navigators) is a standard mobile app architecture pattern, providing a user experience that allows both parallel access to main features (Tabs) and deep content hierarchy (Stack).

  **核心特征 | Key Characteristics:**
  - 特征1：通常以Tab Navigator为根，提供主要功能的并行访问 | Usually has Tab Navigator as root, providing parallel access to main features
  - 特征2：每个Tab包含自己的Stack Navigator以支持该功能域的多级导航 | Each Tab contains its own Stack Navigator to support multi-level navigation within that feature domain
  - 特征3：包含应用的核心功能屏幕（Home、Profile、Settings等） | Contains core app feature screens (Home, Profile, Settings, etc.)
  - 特征4：提供登出功能，触发认证状态改变并返回AuthStack | Provides sign out functionality that triggers auth state change and returns to AuthStack
  - 特征5：Tab间切换时，每个Tab保持自己的导航状态和历史 | When switching between Tabs, each Tab maintains its own navigation state and history

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在AppStack中，Tab Navigator和Stack Navigator应该如何组织？| In AppStack, how should Tab Navigator and Stack Navigator be organized?
     **答案 | Answer:** Tab Navigator在外层（根），每个Tab Screen内部是一个Stack Navigator。例如：HomeTab包含HomeStack（HomeList → HomeDetail） | Tab Navigator at outer level (root), with each Tab Screen containing a Stack Navigator inside. For example: HomeTab contains HomeStack (HomeList → HomeDetail)
  2. 如果用户在HomeTab的详情页，然后切换到ProfileTab，再切回HomeTab，用户会看到什么？| If a user is on a detail page in HomeTab, switches to ProfileTab, then switches back to HomeTab, what will the user see?
     **答案 | Answer:** 用户会回到之前的详情页 | User will return to the previous detail page - 每个Tab保持自己的导航栈，切换Tab不会重置导航历史 | Each Tab maintains its own navigation stack, switching Tabs doesn't reset navigation history
  3. 登出按钮应该调用navigation.navigate('Login')吗？| Should the sign out button call navigation.navigate('Login')?
     **答案 | Answer:** 否 | No - 应该调用signOut方法清除认证状态，这会触发根组件用AuthStack替换整个AppStack | It should call signOut method to clear auth state, which triggers root component to replace entire AppStack with AuthStack
  4. Tab Navigator应该放在AuthStack还是AppStack中？| Should Tab Navigator be placed in AuthStack or AppStack?
     **答案 | Answer:** AppStack | AppStack - Tab Navigator用于组织应用主功能，只有已认证用户才能访问这些功能 | Tab Navigator organizes main app features, which should only be accessible to authenticated users

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // AppStack导航器实现（含Tab Navigator） | AppStack Navigator Implementation (with Tab Navigator)
  import React from 'react';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Ionicons } from '@expo/vector-icons';

  // 导入屏幕组件 | Import screen components
  import HomeListScreen from '../screens/HomeListScreen';
  import HomeDetailScreen from '../screens/HomeDetailScreen';
  import ProfileScreen from '../screens/ProfileScreen';
  import SettingsScreen from '../screens/SettingsScreen';

  const Tab = createBottomTabNavigator();
  const HomeStack = createNativeStackNavigator();
  const ProfileStack = createNativeStackNavigator();

  // Home功能的Stack Navigator | Stack Navigator for Home feature
  function HomeNavigator() {
    return (
      <HomeStack.Navigator>
        <HomeStack.Screen
          name="HomeList"
          component={HomeListScreen}
          options={{ title: '首页 | Home' }}
        />
        <HomeStack.Screen
          name="HomeDetail"
          component={HomeDetailScreen}
          options={{ title: '详情 | Details' }}
        />
      </HomeStack.Navigator>
    );
  }

  // Profile功能的Stack Navigator | Stack Navigator for Profile feature
  function ProfileNavigator() {
    return (
      <ProfileStack.Navigator>
        <ProfileStack.Screen
          name="ProfileMain"
          component={ProfileScreen}
          options={{ title: '个人中心 | Profile' }}
        />
        <ProfileStack.Screen
          name="Settings"
          component={SettingsScreen}
          options={{ title: '设置 | Settings' }}
        />
      </ProfileStack.Navigator>
    );
  }

  // AppStack的主Tab Navigator | Main Tab Navigator for AppStack
  function AppNavigator() {
    return (
      <Tab.Navigator
        screenOptions={({ route }) => ({
          tabBarIcon: ({ focused, color, size }) => {
            let iconName;

            // 根据route名称选择图标 | Select icon based on route name
            if (route.name === 'HomeTab') {
              iconName = focused ? 'home' : 'home-outline';
            } else if (route.name === 'ProfileTab') {
              iconName = focused ? 'person' : 'person-outline';
            }

            return <Ionicons name={iconName} size={size} color={color} />;
          },
          tabBarActiveTintColor: '#6200ee',
          tabBarInactiveTintColor: 'gray',
          headerShown: false, // Tab Navigator不显示header，由内部Stack显示
          // Tab Navigator doesn't show header, inner Stacks show their headers
        })}
      >
        <Tab.Screen
          name="HomeTab"
          component={HomeNavigator}
          options={{ title: '首页 | Home' }}
        />
        <Tab.Screen
          name="ProfileTab"
          component={ProfileNavigator}
          options={{ title: '我的 | Profile' }}
        />
      </Tab.Navigator>
    );
  }

  export default AppNavigator;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么Tab.Navigator设置了headerShown: false？| Why is headerShown: false set in Tab.Navigator?
    **答案 | Answer:** 因为每个Tab内部的Stack Navigator会显示自己的header。如果Tab Navigator也显示header，会有两个header重叠，造成UI问题 | Because each Stack Navigator inside the Tabs shows its own header. If Tab Navigator also shows a header, there would be two overlapping headers, causing UI issues
  - 如果用户在HomeDetail屏幕点击ProfileTab，导航历史会发生什么？| If a user is on HomeDetail screen and taps ProfileTab, what happens to navigation history?
    **答案 | Answer:** HomeTab的导航历史（HomeList → HomeDetail）会保留，用户切换到ProfileTab看到ProfileMain。切回HomeTab时仍在HomeDetail | HomeTab's navigation history (HomeList → HomeDetail) is preserved, user switches to ProfileTab and sees ProfileMain. When switching back to HomeTab, still on HomeDetail

  **常见误区检查 | Common Misconception Checks:**
  - 有人可能认为应该在AppStack根部使用Stack Navigator包含所有屏幕，然后在某个屏幕显示Tab，这样对吗？| Someone might think they should use Stack Navigator at AppStack root containing all screens, then show Tabs on one screen. Is this correct?
    **答案 | Answer:** 不对。这会导致Tab只在某个屏幕可见，而不是持续存在。正确架构是Tab Navigator在根，每个Tab包含Stack Navigator。这样Tab始终可见，用户可随时切换 | No. This would make Tabs only visible on one screen, not persistent. Correct architecture is Tab Navigator at root, each Tab containing Stack Navigator. This keeps Tabs always visible and users can switch anytime
  - 新手可能会问：为什么不直接在Tab中显示HomeDetailScreen，而要嵌套Stack？| Beginners might ask: Why not directly show HomeDetailScreen in Tab, why nest Stack?
    **答案 | Answer:** 因为Tab提供的是并行功能访问，不支持"后退"导航。Stack Navigator提供导航历史和返回功能。没有Stack，用户从Detail无法返回List | Because Tab provides parallel feature access and doesn't support "back" navigation. Stack Navigator provides navigation history and back functionality. Without Stack, users can't go back from Detail to List

