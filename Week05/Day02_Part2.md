# React Native入门 - 第32天：认证流程导航模式 (Part 2) | React Native Introduction - Day 32: Authentication Flow Navigation Pattern (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 3. 实现根级别条件渲染 | Implementing Root-Level Conditional Rendering (1小时 | 1 hour)

- **根导航组件的条件渲染逻辑 | Conditional Rendering Logic in Root Navigation Component**

  **概念定义 | Concept Definition:**
  根级别条件渲染是指在NavigationContainer内部，根据全局认证状态使用三元运算符或条件语句来决定渲染AuthStack还是AppStack。这个渲染决策发生在应用的最顶层，确保整个导航树根据用户的认证状态完全替换，而不是在导航树内部进行切换。同时需要处理应用启动时的加载状态（isLoading），在恢复认证状态期间显示启动画面。| Root-level conditional rendering refers to using ternary operators or conditional statements inside NavigationContainer to decide whether to render AuthStack or AppStack based on global authentication state. This rendering decision occurs at the top level of the app, ensuring the entire navigation tree is completely replaced based on user authentication state, rather than switching within the navigation tree. It also needs to handle loading state (isLoading) during app startup, showing a splash screen while restoring auth state.

  **核心特征 | Key Characteristics:**
  - 特征1：条件渲染发生在NavigationContainer内部，是导航系统的根部 | Conditional rendering occurs inside NavigationContainer, at the root of the navigation system
  - 特征2：使用三元运算符或if语句基于认证状态选择导航器 | Uses ternary operator or if statement to select navigator based on auth state
  - 特征3：需要三种渲染状态：加载中（Loading/Splash）、未认证（AuthStack）、已认证（AppStack） | Requires three render states: loading (Loading/Splash), unauthenticated (AuthStack), authenticated (AppStack)
  - 特征4：认证状态改变时，React自动卸载旧导航器并挂载新导航器 | When auth state changes, React automatically unmounts old navigator and mounts new navigator
  - 特征5：导航器切换是即时的，不涉及动画或过渡（因为是组件替换，不是导航） | Navigator switching is instant with no animations or transitions (because it's component replacement, not navigation)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么需要isLoading状态？直接根据isSignedIn渲染不行吗？| Why do we need isLoading state? Can't we just render based on isSignedIn directly?
     **答案 | Answer:** 不行 | No - 应用启动时需要从AsyncStorage恢复认证状态（异步操作），这需要时间。如果直接根据isSignedIn渲染，会先显示Login（因为初始是false），然后闪烁切换到主应用。isLoading让我们在恢复期间显示启动画面 | On app startup, we need to restore auth state from AsyncStorage (async operation), which takes time. If we render directly based on isSignedIn, it will first show Login (because initial is false), then flicker to main app. isLoading allows us to show a splash screen during restoration
  2. 条件渲染中，AuthStack和AppStack是否会同时存在于内存中？| In conditional rendering, do AuthStack and AppStack exist in memory simultaneously?
     **答案 | Answer:** 否 | No - 三元运算符确保任何时刻只有一个导航器被渲染和存在于内存中。React会完全卸载未渲染的导航器 | Ternary operator ensures only one navigator is rendered and exists in memory at any moment. React completely unmounts the navigator that's not rendered
  3. 如果在AppStack中signOut改变了认证状态，导航会发生什么？| If signOut changes auth state while in AppStack, what happens to navigation?
     **答案 | Answer:** 根组件检测到认证状态改变，重新渲染并用AuthStack替换AppStack。整个AppStack（包括所有屏幕、状态、导航历史）被卸载和销毁，用户看到Login屏幕 | Root component detects auth state change, re-renders and replaces AppStack with AuthStack. Entire AppStack (including all screens, state, navigation history) is unmounted and destroyed, user sees Login screen
  4. NavigationContainer应该在条件渲染之内还是之外？| Should NavigationContainer be inside or outside conditional rendering?
     **答案 | Answer:** 之外（包裹条件渲染）| Outside (wrapping conditional rendering) - NavigationContainer应该始终存在以提供导航上下文，条件渲染决定其内部显示哪个导航器 | NavigationContainer should always exist to provide navigation context, conditional rendering decides which navigator to show inside it

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 根导航组件的完整实现 | Complete Root Navigation Component Implementation
  import React from 'react';
  import { View, ActivityIndicator, StyleSheet } from 'react-native';
  import { NavigationContainer } from '@react-navigation/native';
  import { AuthProvider, useAuth } from './context/AuthContext';
  import AuthNavigator from './navigation/AuthNavigator';
  import AppNavigator from './navigation/AppNavigator';

  // 启动画面组件 | Splash Screen Component
  function SplashScreen() {
    return (
      <View style={styles.splashContainer}>
        <ActivityIndicator size="large" color="#6200ee" />
      </View>
    );
  }

  // 导航根组件（需要访问AuthContext） | Navigation Root Component (needs access to AuthContext)
  function RootNavigator() {
    const { isSignedIn, isLoading } = useAuth(); // 从Context获取认证状态 | Get auth state from Context

    // 加载状态：显示启动画面 | Loading state: show splash screen
    if (isLoading) {
      return <SplashScreen />;
    }

    // 根据认证状态条件渲染导航器 | Conditional render navigator based on auth state
    return (
      <NavigationContainer>
        {isSignedIn ? <AppNavigator /> : <AuthNavigator />}
      </NavigationContainer>
    );
  }

  // App入口组件 | App Entry Component
  export default function App() {
    return (
      // AuthProvider必须包裹RootNavigator以提供认证状态
      // AuthProvider must wrap RootNavigator to provide auth state
      <AuthProvider>
        <RootNavigator />
      </AuthProvider>
    );
  }

  const styles = StyleSheet.create({
    splashContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#fff',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，为什么RootNavigator需要单独定义，而不是直接在App组件中使用useAuth？| Why does RootNavigator need to be defined separately, instead of using useAuth directly in App component?
    **答案 | Answer:** 因为useAuth必须在AuthProvider内部使用（Context的规则）。App组件渲染AuthProvider，所以不能在App中使用useAuth。RootNavigator作为AuthProvider的子组件可以访问Context | Because useAuth must be used inside AuthProvider (Context rule). App component renders AuthProvider, so can't use useAuth in App. RootNavigator as a child of AuthProvider can access Context
  - 如果去掉isLoading检查，直接渲染{isSignedIn ? <AppNavigator /> : <AuthNavigator />}，用户体验会如何？| If we remove isLoading check and directly render {isSignedIn ? <AppNavigator /> : <AuthNavigator />}, how would user experience be?
    **答案 | Answer:** 已登录用户会在应用启动时先看到Login屏幕闪烁（因为isSignedIn初始是false），然后AsyncStorage恢复token后才切换到主应用，造成不好的视觉体验 | Logged-in users would see a flash of the Login screen on app startup (because isSignedIn is initially false), then switch to main app after AsyncStorage restores token, causing poor visual experience

  **常见误区检查 | Common Misconception Checks:**
  - 有人可能认为应该使用navigation.navigate在AuthStack和AppStack间切换，这样对吗？| Someone might think they should use navigation.navigate to switch between AuthStack and AppStack. Is this correct?
    **答案 | Answer:** 不对 | No - AuthStack和AppStack是独立的导航树，不是同一导航器中的屏幕，无法用navigate连接。正确方式是改变认证状态触发React重新渲染和组件替换 | No - AuthStack and AppStack are independent navigation trees, not screens within the same navigator, so navigate can't connect them. Correct approach is to change auth state to trigger React re-render and component replacement
  - 新手可能会问：能否在NavigationContainer外面条件渲染整个NavigationContainer？| Beginners might ask: Can we conditionally render the entire NavigationContainer outside it?
    **答案 | Answer:** 可以但不推荐 | Possible but not recommended - 这会在切换时卸载和重新创建NavigationContainer，可能导致导航状态丢失和性能问题。推荐做法是保持NavigationContainer稳定，只在内部条件渲染导航器 | This would unmount and recreate NavigationContainer on switch, potentially losing navigation state and causing performance issues. Recommended approach is to keep NavigationContainer stable and only conditionally render navigators inside

- **登录和登出功能的实现 | Implementing Sign In and Sign Out Functionality**

  **概念定义 | Concept Definition:**
  登录和登出功能是通过调用AuthContext提供的signIn和signOut方法来实现的，这些方法改变全局认证状态（通常是保存或删除userToken），从而触发根导航组件重新渲染并切换导航结构。登录功能在认证成功后保存token到state和AsyncStorage；登出功能清除token并可选择性重置应用状态。这些操作不涉及导航API，而是通过React的状态管理和重新渲染机制实现导航结构的切换。| Sign in and sign out functionality is implemented by calling signIn and signOut methods provided by AuthContext, which change global authentication state (typically saving or removing userToken), thereby triggering the root navigation component to re-render and switch navigation structures. Sign in functionality saves token to state and AsyncStorage after successful authentication; sign out functionality clears token and optionally resets app state. These operations don't involve navigation API but achieve navigation structure switching through React's state management and re-rendering mechanism.

  **核心特征 | Key Characteristics:**
  - 特征1：登录时调用signIn(token)保存认证凭证，触发isSignedIn变为true | On sign in, call signIn(token) to save auth credentials, triggering isSignedIn to become true
  - 特征2：登出时调用signOut()清除认证凭证，触发isSignedIn变为false | On sign out, call signOut() to clear auth credentials, triggering isSignedIn to become false
  - 特征3：状态改变自动触发根组件重新渲染和导航器切换，无需手动导航 | State change automatically triggers root component re-render and navigator switch, no manual navigation needed
  - 特征4：登录通常包含表单验证、API请求、错误处理等步骤 | Sign in usually includes form validation, API requests, error handling steps
  - 特征5：登出可能需要清除其他应用状态（如Redux store、缓存数据）和执行清理操作 | Sign out may need to clear other app state (like Redux store, cached data) and perform cleanup operations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在LoginScreen的"登录"按钮处理函数中，调用signIn后需要手动navigate到主应用吗？| In LoginScreen's "Sign In" button handler, do we need to manually navigate to main app after calling signIn?
     **答案 | Answer:** 否 | No - signIn改变认证状态后，根组件会自动重新渲染并显示AppStack。手动导航是不必要的且会出错（因为Home不在AuthStack中）| After signIn changes auth state, root component automatically re-renders and shows AppStack. Manual navigation is unnecessary and would error (because Home is not in AuthStack)
  2. 如果登录API请求失败，应该调用signIn吗？| If login API request fails, should we call signIn?
     **答案 | Answer:** 否 | No - 只有在成功获得有效token时才调用signIn。失败时应该显示错误消息给用户，不改变认证状态 | Only call signIn when successfully obtaining a valid token. On failure, show error message to user without changing auth state
  3. 登出按钮应该放在哪个导航器中？| Which navigator should the sign out button be placed in?
     **答案 | Answer:** AppStack | AppStack - 因为只有已认证用户需要登出功能。通常放在Profile或Settings屏幕 | Because only authenticated users need sign out functionality. Usually placed in Profile or Settings screen
  4. 登出后，用户在AppStack中的导航历史（如在某个详情页）会保留吗？| After sign out, is the user's navigation history in AppStack (like being on a detail page) preserved?
     **答案 | Answer:** 否 | No - 登出触发AppStack被完全卸载并用AuthStack替换，所有AppStack的状态和导航历史都被销毁。用户下次登录时从头开始 | Sign out triggers AppStack to be completely unmounted and replaced with AuthStack, all AppStack state and navigation history are destroyed. User starts fresh on next login

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // LoginScreen实现 | LoginScreen Implementation
  import React, { useState } from 'react';
  import { View, Text, TextInput, Button, StyleSheet, Alert } from 'react-native';
  import { useAuth } from '../context/AuthContext';

  function LoginScreen({ navigation }) {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [isLoading, setIsLoading] = useState(false);

    const { signIn } = useAuth(); // 从Context获取signIn方法 | Get signIn method from Context

    // 登录处理函数 | Sign in handler
    const handleSignIn = async () => {
      // 简单的表单验证 | Simple form validation
      if (!email || !password) {
        Alert.alert('错误 | Error', '请填写所有字段 | Please fill all fields');
        return;
      }

      setIsLoading(true);

      try {
        // 模拟API请求 | Simulate API request
        // 实际应用中，这里会调用登录API | In real app, this would call login API
        const response = await fakeLoginAPI(email, password);

        if (response.success) {
          // 登录成功：调用signIn保存token | Sign in successful: call signIn to save token
          // 这会自动触发导航切换到AppStack | This automatically triggers navigation switch to AppStack
          await signIn(response.token);
        } else {
          Alert.alert('登录失败 | Sign In Failed', response.message);
        }
      } catch (error) {
        Alert.alert('错误 | Error', '登录时发生错误 | An error occurred during sign in');
      } finally {
        setIsLoading(false);
      }
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>欢迎登录 | Welcome</Text>

        <TextInput
          style={styles.input}
          placeholder="邮箱 | Email"
          value={email}
          onChangeText={setEmail}
          autoCapitalize="none"
          keyboardType="email-address"
        />

        <TextInput
          style={styles.input}
          placeholder="密码 | Password"
          value={password}
          onChangeText={setPassword}
          secureTextEntry
        />

        <Button
          title={isLoading ? '登录中... | Signing In...' : '登录 | Sign In'}
          onPress={handleSignIn}
          disabled={isLoading}
        />

        <Button
          title="还没有账号？注册 | Don't have an account? Sign Up"
          onPress={() => navigation.navigate('Signup')}
        />
      </View>
    );
  }

  // 模拟登录API | Fake login API
  const fakeLoginAPI = (email, password) => {
    return new Promise((resolve) => {
      setTimeout(() => {
        // 简单的模拟逻辑 | Simple simulation logic
        if (email === 'test@example.com' && password === 'password') {
          resolve({ success: true, token: 'fake-jwt-token-12345' });
        } else {
          resolve({ success: false, message: '邮箱或密码错误 | Invalid email or password' });
        }
      }, 1000);
    });
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    input: {
      height: 50,
      borderWidth: 1,
      borderColor: '#ddd',
      borderRadius: 8,
      paddingHorizontal: 15,
      marginBottom: 15,
    },
  });

  export default LoginScreen;
  ```

  ```javascript
  // SettingsScreen with Sign Out | 包含登出功能的设置屏幕
  import React from 'react';
  import { View, Text, Button, StyleSheet, Alert } from 'react-native';
  import { useAuth } from '../context/AuthContext';

  function SettingsScreen() {
    const { signOut, userToken } = useAuth(); // 从Context获取signOut方法 | Get signOut method from Context

    // 登出处理函数 | Sign out handler
    const handleSignOut = () => {
      Alert.alert(
        '确认登出 | Confirm Sign Out',
        '确定要登出吗？| Are you sure you want to sign out?',
        [
          {
            text: '取消 | Cancel',
            style: 'cancel',
          },
          {
            text: '登出 | Sign Out',
            style: 'destructive',
            onPress: async () => {
              // 可以在这里执行额外的清理操作 | Can perform additional cleanup here
              // 例如：清除Redux store、取消网络请求等
              // e.g., clear Redux store, cancel network requests, etc.

              await signOut(); // 登出会自动切换到AuthStack | Sign out automatically switches to AuthStack
            },
          },
        ]
      );
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>设置 | Settings</Text>

        <View style={styles.infoContainer}>
          <Text style={styles.label}>当前Token | Current Token:</Text>
          <Text style={styles.value} numberOfLines={1}>
            {userToken}
          </Text>
        </View>

        <Button
          title="登出 | Sign Out"
          onPress={handleSignOut}
          color="#d32f2f"
        />
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 30,
    },
    infoContainer: {
      backgroundColor: '#f5f5f5',
      padding: 15,
      borderRadius: 8,
      marginBottom: 20,
    },
    label: {
      fontSize: 14,
      color: '#666',
      marginBottom: 5,
    },
    value: {
      fontSize: 16,
      fontWeight: '500',
    },
  });

  export default SettingsScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - LoginScreen中，为什么"注册"按钮使用navigation.navigate('Signup')而不是改变认证状态？| In LoginScreen, why does the "Sign Up" button use navigation.navigate('Signup') instead of changing auth state?
    **答案 | Answer:** 因为Signup屏幕在同一个AuthStack导航器中，是正常的屏幕间导航。只有当用户实际完成认证（获得token）时才改变认证状态触发导航器切换 | Because Signup screen is in the same AuthStack navigator, it's normal screen-to-screen navigation. Only when user actually completes authentication (obtains token) should auth state change to trigger navigator switch
  - SettingsScreen的登出确认对话框为什么必要？| Why is the sign out confirmation dialog in SettingsScreen necessary?
    **答案 | Answer:** 因为登出是破坏性操作（会清除用户状态和导航历史），误触会影响用户体验。确认对话框防止意外登出，符合用户体验最佳实践 | Because sign out is a destructive operation (clears user state and navigation history), accidental taps would impact user experience. Confirmation dialog prevents accidental sign out, following UX best practices

  **常见误区检查 | Common Misconception Checks:**
  - 有人可能认为登录成功后应该用navigation.replace('Home')替换导航栈，这样对吗？| Someone might think they should use navigation.replace('Home') after successful login. Is this correct?
    **答案 | Answer:** 不对 | No - Home屏幕不在AuthStack中（在AppStack中），无法导航到。正确方式是调用signIn改变认证状态，让根组件替换整个导航树 | No - Home screen is not in AuthStack (it's in AppStack), can't navigate to it. Correct approach is to call signIn to change auth state, letting root component replace entire navigation tree
  - 新手可能会问：为什么不把token存储在LoginScreen的state中？| Beginners might ask: Why not store token in LoginScreen's state?
    **答案 | Answer:** 因为LoginScreen的state在组件卸载时会丢失（切换到AppStack时AuthStack被卸载）。token需要全局可访问（用于API请求）和持久化（关闭应用后仍保留），必须用Context和AsyncStorage | Because LoginScreen's state is lost when component unmounts (AuthStack is unmounted when switching to AppStack). Token needs to be globally accessible (for API requests) and persistent (retained after app closes), must use Context and AsyncStorage

### 4. 实践项目：完整的认证流程应用 | Practical Project: Complete Authentication Flow App (2小时 | 2 hours)

### 目标 | Objective
构建一个完整的React Native应用，演示行业标准的认证流程导航模式，包括独立的AuthStack（Login、Signup）和AppStack（Tab Navigator包含Home和Profile），使用React Context管理认证状态，实现登录、登出功能，并处理应用启动时的状态恢复。| Build a complete React Native app demonstrating industry-standard authentication flow navigation pattern, including separate AuthStack (Login, Signup) and AppStack (Tab Navigator with Home and Profile), using React Context to manage auth state, implementing sign in/sign out functionality, and handling state restoration on app startup.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 认证流程模式中，AuthStack和AppStack的关系是什么？| In authentication flow pattern, what is the relationship between AuthStack and AppStack?
   **答案 | Answer:** 它们是两个独立的导航树，根据认证状态在根级别条件渲染其中一个，不是同一导航器中的屏幕 | They are two independent navigation trees, conditionally rendered at root level based on auth state, not screens within the same navigator
2. 用户登录成功后，应该如何触发从AuthStack到AppStack的切换？| After successful login, how should the switch from AuthStack to AppStack be triggered?
   **答案 | Answer:** 调用signIn方法改变Context中的认证状态（如保存token），根组件检测到状态变化后自动重新渲染并替换导航器 | Call signIn method to change auth state in Context (like saving token), root component detects state change and automatically re-renders to replace navigator
3. 为什么需要isLoading状态和启动画面？| Why do we need isLoading state and splash screen?
   **答案 | Answer:** 应用启动时需要从AsyncStorage异步恢复认证状态，isLoading防止在恢复期间显示错误的导航器（如已登录用户看到Login闪烁），启动画面提供流畅的用户体验 | On app startup, we need to asynchronously restore auth state from AsyncStorage, isLoading prevents showing wrong navigator during restoration (like logged-in users seeing Login flash), splash screen provides smooth user experience

### 步骤 | Steps

1. **项目结构设置 | Project Structure Setup**
   - 创建项目目录结构：context/、navigation/、screens/
   - 安装必要依赖：`@react-navigation/native`、`@react-navigation/native-stack`、`@react-navigation/bottom-tabs`、`@react-native-async-storage/async-storage`
   - 设置基本的App.js作为入口

2. **创建AuthContext | Create AuthContext**
   - 实现AuthContext和AuthProvider，包含isSignedIn、userToken、isLoading状态
   - 实现signIn、signOut方法，操作AsyncStorage
   - 实现应用启动时的状态恢复逻辑（useEffect）

3. **构建认证相关屏幕 | Build Authentication Screens**
   - LoginScreen：包含邮箱、密码输入，"登录"和"前往注册"按钮
   - SignupScreen：包含注册表单和"返回登录"按钮
   - 实现表单验证和模拟API调用

4. **构建应用主屏幕 | Build Main App Screens**
   - HomeScreen：显示欢迎消息和当前用户token
   - ProfileScreen：显示用户信息
   - SettingsScreen：包含登出按钮

5. **创建导航结构 | Create Navigation Structure**
   - AuthNavigator：Stack Navigator包含Login和Signup
   - HomeStack：Stack Navigator包含Home及可能的详情页
   - ProfileStack：Stack Navigator包含Profile和Settings
   - AppNavigator：Tab Navigator包含HomeTab和ProfileTab（每个Tab是一个Stack）

6. **实现根级别条件渲染 | Implement Root-Level Conditional Rendering**
   - 创建RootNavigator组件，使用useAuth访问认证状态
   - 实现三种渲染状态：加载中（SplashScreen）、未认证（AuthNavigator）、已认证（AppNavigator）
   - 在App.js中用AuthProvider包裹RootNavigator

7. **测试认证流程 | Test Authentication Flow**
   - 测试登录：输入凭证→调用signIn→自动切换到AppStack
   - 测试登出：点击登出按钮→确认对话框→调用signOut→自动切换到AuthStack
   - 测试状态持久化：登录后关闭应用→重新打开→应保持登录状态

### 示例代码 | Example Code

```javascript
// App.js - 应用入口 | App Entry Point
import React from 'react';
import { AuthProvider } from './context/AuthContext';
import RootNavigator from './navigation/RootNavigator';

export default function App() {
  return (
    <AuthProvider>
      <RootNavigator />
    </AuthProvider>
  );
}
```

```javascript
// context/AuthContext.js - 认证Context | Authentication Context
import React, { createContext, useContext, useState, useEffect } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

const AuthContext = createContext(null);

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
        console.error('恢复token失败:', e);
      } finally {
        setIsLoading(false);
      }
    };

    bootstrapAsync();
  }, []);

  const signIn = async (token) => {
    try {
      await AsyncStorage.setItem('userToken', token);
      setUserToken(token);
    } catch (e) {
      console.error('保存token失败:', e);
    }
  };

  const signOut = async () => {
    try {
      await AsyncStorage.removeItem('userToken');
      setUserToken(null);
    } catch (e) {
      console.error('删除token失败:', e);
    }
  };

  return (
    <AuthContext.Provider
      value={{
        isSignedIn: !!userToken,
        userToken,
        signIn,
        signOut,
        isLoading,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth必须在AuthProvider内部使用');
  }
  return context;
}
```

```javascript
// navigation/RootNavigator.js - 根导航组件 | Root Navigator Component
import React from 'react';
import { View, ActivityIndicator, StyleSheet } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { useAuth } from '../context/AuthContext';
import AuthNavigator from './AuthNavigator';
import AppNavigator from './AppNavigator';

function SplashScreen() {
  return (
    <View style={styles.splashContainer}>
      <ActivityIndicator size="large" color="#6200ee" />
    </View>
  );
}

export default function RootNavigator() {
  const { isSignedIn, isLoading } = useAuth();

  if (isLoading) {
    return <SplashScreen />;
  }

  return (
    <NavigationContainer>
      {isSignedIn ? <AppNavigator /> : <AuthNavigator />}
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  splashContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
});
```

```javascript
// navigation/AuthNavigator.js - 认证导航栈 | Authentication Navigation Stack
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import LoginScreen from '../screens/LoginScreen';
import SignupScreen from '../screens/SignupScreen';

const Stack = createNativeStackNavigator();

export default function AuthNavigator() {
  return (
    <Stack.Navigator
      screenOptions={{
        headerStyle: { backgroundColor: '#6200ee' },
        headerTintColor: '#fff',
        headerTitleStyle: { fontWeight: 'bold' },
      }}
    >
      <Stack.Screen
        name="Login"
        component={LoginScreen}
        options={{ title: '登录', headerLeft: () => null }}
      />
      <Stack.Screen
        name="Signup"
        component={SignupScreen}
        options={{ title: '注册' }}
      />
    </Stack.Navigator>
  );
}
```

```javascript
// navigation/AppNavigator.js - 主应用Tab导航 | Main App Tab Navigation
import React from 'react';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { Ionicons } from '@expo/vector-icons';
import HomeScreen from '../screens/HomeScreen';
import ProfileScreen from '../screens/ProfileScreen';
import SettingsScreen from '../screens/SettingsScreen';

const Tab = createBottomTabNavigator();
const HomeStack = createNativeStackNavigator();
const ProfileStack = createNativeStackNavigator();

function HomeNavigator() {
  return (
    <HomeStack.Navigator>
      <HomeStack.Screen name="HomeMain" component={HomeScreen} options={{ title: '首页' }} />
    </HomeStack.Navigator>
  );
}

function ProfileNavigator() {
  return (
    <ProfileStack.Navigator>
      <ProfileStack.Screen name="ProfileMain" component={ProfileScreen} options={{ title: '个人中心' }} />
      <ProfileStack.Screen name="Settings" component={SettingsScreen} options={{ title: '设置' }} />
    </ProfileStack.Navigator>
  );
}

export default function AppNavigator() {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          const iconName = route.name === 'HomeTab'
            ? (focused ? 'home' : 'home-outline')
            : (focused ? 'person' : 'person-outline');
          return <Ionicons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: '#6200ee',
        tabBarInactiveTintColor: 'gray',
        headerShown: false,
      })}
    >
      <Tab.Screen name="HomeTab" component={HomeNavigator} options={{ title: '首页' }} />
      <Tab.Screen name="ProfileTab" component={ProfileNavigator} options={{ title: '我的' }} />
    </Tab.Navigator>
  );
}
```

### 项目完成检查 | Project Completion Check
1. 应用是否正确实现了独立的AuthStack和AppStack？| Does the app correctly implement separate AuthStack and AppStack?
2. 登录功能是否通过改变认证状态（而非navigation.navigate）触发导航切换？| Does sign in trigger navigation switch by changing auth state (not navigation.navigate)?
3. 应用启动时是否正确恢复认证状态并显示适当的导航器？| Does the app correctly restore auth state on startup and show appropriate navigator?
4. 登出功能是否正确清除状态并切换回AuthStack？| Does sign out correctly clear state and switch back to AuthStack?
5. Tab导航是否正确嵌套Stack Navigator以支持深层导航？| Does Tab navigation correctly nest Stack Navigators to support deep navigation?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **忘记密码流程练习 | Forgot Password Flow Exercise**
   - **练习描述 | Exercise Description:** 在AuthStack中添加ForgotPasswordScreen，实现密码重置流程（输入邮箱→显示确认消息→返回登录）
   - **概念检查 | Concept Check:** ForgotPassword屏幕应该在AuthStack还是AppStack中？为什么？
   - **学习目标 | Learning Objective:** 巩固对认证流程导航结构的理解

2. **首次登录引导练习 | First-Time Onboarding Exercise**
   - **练习描述 | Exercise Description:** 添加OnboardingStack用于首次使用引导，实现三种状态：未引导（OnboardingStack）、未登录（AuthStack）、已登录（AppStack）
   - **概念检查 | Concept Check:** 如何在根组件中实现三个独立导航栈的条件渲染？
   - **学习目标 | Learning Objective:** 理解多状态导航架构的实现方式

3. **Token自动刷新练习 | Auto Token Refresh Exercise**
   - **练习描述 | Exercise Description:** 实现token过期检测和自动刷新机制，token即将过期时自动调用刷新API，失败时自动登出
   - **概念检查 | Concept Check:** Token刷新逻辑应该放在哪里？失败后如何触发登出？
   - **学习目标 | Learning Objective:** 掌握认证状态的高级管理模式

4. **保护路由练习 | Protected Routes Exercise**
   - **练习描述 | Exercise Description:** 在AppStack中添加AdminScreen，仅特定角色（如admin）可访问，其他用户导航时显示"无权限"提示
   - **概念检查 | Concept Check:** 基于角色的访问控制应该如何与认证流程结合？
   - **学习目标 | Learning Objective:** 理解认证与授权在导航中的区别和实现

5. **多账户切换练习 | Multi-Account Switching Exercise**
   - **练习描述 | Exercise Description:** 支持用户在应用内切换多个已登录账户，Context存储账户列表，切换时更新当前账户和token
   - **概念检查 | Concept Check:** 切换账户时导航结构会如何变化？需要切换导航器吗？
   - **学习目标 | Learning Objective:** 掌握复杂认证场景的状态管理

6. **社交登录集成练习 | Social Login Integration Exercise**
   - **练习描述 | Exercise Description:** 添加"使用Google登录"和"使用Apple登录"按钮，模拟OAuth流程（打开WebView→获取token→调用signIn）
   - **概念检查 | Concept Check:** 社交登录获得token后，触发导航切换的流程与普通登录有何不同？
   - **学习目标 | Learning Objective:** 理解不同登录方式在认证流程架构中的统一处理

7. **认证状态持久化策略练习 | Auth State Persistence Strategy Exercise**
   - **练习描述 | Exercise Description:** 实现"记住我"功能：勾选时token永久保存，不勾选时token仅在应用会话期间有效（关闭应用清除）
   - **概念检查 | Concept Check:** 如何区分"会话token"和"持久token"？应用启动逻辑如何调整？
   - **学习目标 | Learning Objective:** 掌握认证持久化的不同策略和实现方式

## 学习资源 | Learning Resources
- [React Navigation - Authentication Flow](https://reactnavigation.org/docs/auth-flow)
- [React Navigation - Nesting Navigators](https://reactnavigation.org/docs/nesting-navigators)
- [React Context API 官方文档](https://react.dev/reference/react/useContext)
- [AsyncStorage - React Native](https://react-native-async-storage.github.io/async-storage/)
- [Authentication Best Practices for Mobile Apps](https://auth0.com/docs/best-practices/mobile-authentication)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解认证流程导航模式的核心原理：独立导航栈+条件渲染 | Understand core principle of auth flow pattern: separate nav stacks + conditional rendering
- [ ] 能够创建独立的AuthStack（登录、注册）和AppStack（主应用功能） | Can create separate AuthStack (login, signup) and AppStack (main app features)
- [ ] 掌握使用React Context管理全局认证状态 | Master using React Context to manage global auth state
- [ ] 能够实现登录功能：验证→获取token→调用signIn→触发导航切换 | Can implement sign in: validation → get token → call signIn → trigger nav switch
- [ ] 能够实现登出功能：调用signOut→清除状态→触发导航切换 | Can implement sign out: call signOut → clear state → trigger nav switch
- [ ] 理解根级别条件渲染的三种状态：加载、未认证、已认证 | Understand three states in root-level conditional rendering: loading, unauthenticated, authenticated
- [ ] 掌握在AppStack中嵌套Tab Navigator和Stack Navigator的架构 | Master architecture of nesting Tab Navigator and Stack Navigator in AppStack
- [ ] 能够实现应用启动时的认证状态恢复（AsyncStorage） | Can implement auth state restoration on app startup (AsyncStorage)
- [ ] 理解为什么认证导航切换是组件替换而非导航API调用 | Understand why auth navigation switching is component replacement, not navigation API call
- [ ] 能够独立构建完整的认证流程应用并测试所有场景 | Can independently build complete auth flow app and test all scenarios

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，能够清晰解释认证流程导航模式与传统单导航器方法的区别，以及为什么移动应用采用这种架构。你应该能够向他人演示：为什么登录不是"导航到主应用"，而是"用主应用导航树替换登录导航树"。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain the difference between authentication flow navigation pattern and traditional single-navigator approach, and why mobile apps adopt this architecture. You should be able to demonstrate to others: why login is not "navigating to main app" but "replacing login navigation tree with main app navigation tree".
