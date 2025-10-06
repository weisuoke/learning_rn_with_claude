# React Native入门 - 第3天：头部定制与选项配置 | React Native Introduction - Day 3: Header Customization & Options

## 学习目标 | Learning Objectives
- 掌握如何使用 `options` 属性和 `navigation.setOptions()` 设置屏幕标题 | Master setting screen titles using `options` prop and `navigation.setOptions()`
- 理解静态选项配置与动态选项配置的区别和应用场景 | Understand the difference between static and dynamic options configuration and their use cases
- 学会自定义头部样式，包括背景色、文字颜色和字体 | Learn to customize header styles including background color, text color, and fonts
- 掌握如何添加头部按钮（左侧和右侧按钮）及其交互逻辑 | Master adding header buttons (left and right) and their interaction logic
- 理解如何根据屏幕状态动态更新头部标题和按钮 | Understand how to dynamically update header title and buttons based on screen state
- 学会在特定屏幕隐藏头部以实现自定义导航体验 | Learn to hide headers on specific screens for custom navigation experiences

## 详细内容 | Detailed Content

### 1. 头部选项基础 | Header Options Fundamentals (1.5小时 | 1.5 hours)

- **静态选项配置 | Static Options Configuration**

  **概念定义 | Concept Definition:**
  静态选项配置是在定义屏幕时直接通过 `options` 属性设置的导航头部配置，这些配置在屏幕组件挂载时就已确定，不会随组件状态变化而改变。| Static options configuration refers to navigation header settings defined directly via the `options` prop when defining screens. These configurations are determined when the screen component mounts and don't change with component state.

  **核心特征 | Key Characteristics:**
  - 在 `Screen` 组件上通过 `options` 属性定义 | Defined via the `options` prop on the `Screen` component
  - 配置在组件初始化时确定，性能更优 | Configuration is determined at component initialization, offering better performance
  - 适用于不需要根据状态改变的固定头部样式 | Suitable for fixed header styles that don't need to change based on state
  - 可以是对象或返回对象的函数（接收 `navigation` 和 `route` 参数）| Can be an object or a function returning an object (receives `navigation` and `route` parameters)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 静态选项配置在屏幕组件内部状态改变时会自动更新吗？| Do static options automatically update when the screen component's internal state changes?
     **答案 | Answer:** 否 | No - 静态选项在屏幕初始化时设置，不会随组件状态自动更新。如需动态更新需使用 `navigation.setOptions()` | Static options are set at screen initialization and don't automatically update with component state. Use `navigation.setOptions()` for dynamic updates.

  2. `options` 属性可以接收一个函数吗？| Can the `options` prop accept a function?
     **答案 | Answer:** 是 | Yes - `options` 可以是对象或函数。函数接收 `{ navigation, route }` 参数，返回选项对象。这允许基于路由参数设置选项，但仍是"静态"的（不随组件状态变化）| `options` can be an object or function. Functions receive `{ navigation, route }` parameters and return an options object. This allows setting options based on route params, but is still "static" (doesn't change with component state).

  3. 在 Stack.Navigator 上设置的选项和在 Stack.Screen 上设置的选项，哪个优先级更高？| Which has higher priority: options set on Stack.Navigator or Stack.Screen?
     **答案 | Answer:** Stack.Screen - 屏幕级别的选项会覆盖导航器级别的默认选项 | Stack.Screen - Screen-level options override navigator-level default options.

  4. 使用静态选项配置可以访问屏幕组件的 state 吗？| Can static options configuration access the screen component's state?
     **答案 | Answer:** 否 | No - 静态选项在屏幕组件外部定义，无法直接访问组件内部状态。需要动态更新时应使用 `navigation.setOptions()` 在组件内部调用 | Static options are defined outside the screen component and cannot directly access internal state. For dynamic updates, use `navigation.setOptions()` called from within the component.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 示例1：对象形式的静态选项 | Example 1: Static options as object
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Button } from 'react-native';

  const Stack = createNativeStackNavigator();

  function HomeScreen({ navigation }) {
    return (
      <Button
        title="Go to Profile"
        onPress={() => navigation.navigate('Profile')}
      />
    );
  }

  function App() {
    return (
      <Stack.Navigator>
        {/* 静态选项配置 - 对象形式 | Static options - object form */}
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: '首页 | Home',  // 设置头部标题 | Set header title
            headerStyle: {
              backgroundColor: '#f4511e',  // 头部背景色 | Header background color
            },
            headerTintColor: '#fff',  // 头部文字和按钮颜色 | Header text and button color
            headerTitleStyle: {
              fontWeight: 'bold',  // 标题字体粗细 | Title font weight
            },
          }}
        />
      </Stack.Navigator>
    );
  }

  // 示例2：函数形式的静态选项（基于路由参数）| Example 2: Static options as function (based on route params)
  function ProfileScreen({ route }) {
    return <Text>Profile: {route.params.name}</Text>;
  }

  function App2() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Profile"
          component={ProfileScreen}
          options={({ route }) => ({
            // 根据路由参数设置标题 | Set title based on route params
            title: route.params.name || '默认标题 | Default Title',
          })}
        />
      </Stack.Navigator>
    );
  }

  // 示例3：导航器级别的默认选项 | Example 3: Navigator-level default options
  function App3() {
    return (
      <Stack.Navigator
        // 为所有屏幕设置默认选项 | Set default options for all screens
        screenOptions={{
          headerStyle: {
            backgroundColor: '#6200ee',
          },
          headerTintColor: '#fff',
          headerTitleStyle: {
            fontWeight: 'bold',
          },
        }}
      >
        <Stack.Screen name="Home" component={HomeScreen} />
        {/* Profile 屏幕特定选项会覆盖默认值 | Profile screen-specific options override defaults */}
        <Stack.Screen
          name="Profile"
          component={ProfileScreen}
          options={{
            headerStyle: {
              backgroundColor: '#03dac6',  // 覆盖默认背景色 | Override default background
            },
          }}
        />
      </Stack.Navigator>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在示例1中，如果我在 HomeScreen 组件内部改变某个状态，头部背景色会改变吗？| In Example 1, if I change some state inside the HomeScreen component, will the header background color change?
    **答案 | Answer:** 不会 | No - 静态选项配置在屏幕初始化时设置，不会响应组件内部状态变化。需要使用 `navigation.setOptions()` 来动态更新 | Static options are set at screen initialization and don't respond to internal state changes. Use `navigation.setOptions()` for dynamic updates.

  - 在示例2中，`options` 函数何时被调用？| In Example 2, when is the `options` function called?
    **答案 | Answer:** 当屏幕即将被导航到时调用（在组件挂载前）。因此可以访问 `route.params`，但无法访问组件内部状态 | Called when the screen is about to be navigated to (before component mounts). Therefore can access `route.params` but cannot access component internal state.

  **常见误区检查 | Common Misconception Checks:**
  - 我可以在 `options` 函数中访问组件的 useState 状态吗？| Can I access the component's useState state in the `options` function?
    **答案 | Answer:** 不能 | No - `options` 函数在屏幕组件外部执行，无法访问组件内部的钩子状态。正确做法是使用 `navigation.setOptions()` 在组件内部更新选项 | The `options` function executes outside the screen component and cannot access internal hook state. The correct approach is using `navigation.setOptions()` to update options from within the component.

  - 静态选项配置就意味着完全不能改变吗？| Does static options configuration mean they can never be changed?
    **答案 | Answer:** 不是 | No - "静态"指的是初始配置方式，但可以通过 `navigation.setOptions()` 在组件内部动态更新这些选项 | "Static" refers to the initial configuration method, but these options can be dynamically updated from within the component using `navigation.setOptions()`.

- **动态选项配置 | Dynamic Options Configuration**

  **概念定义 | Concept Definition:**
  动态选项配置是通过 `navigation.setOptions()` 方法在屏幕组件内部根据状态、用户交互或其他运行时条件实时更新导航头部的配置。| Dynamic options configuration refers to updating navigation header settings in real-time from within a screen component using the `navigation.setOptions()` method, based on state, user interactions, or other runtime conditions.

  **核心特征 | Key Characteristics:**
  - 在组件内部通过 `navigation.setOptions()` 调用 | Called from within the component via `navigation.setOptions()`
  - 可以响应组件状态变化和用户交互 | Can respond to component state changes and user interactions
  - 通常在 `useEffect` 或事件处理函数中调用 | Typically called in `useEffect` or event handlers
  - 适用于需要根据数据加载、表单状态等动态更新的场景 | Suitable for scenarios requiring dynamic updates based on data loading, form state, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `navigation.setOptions()` 应该在哪里调用？| Where should `navigation.setOptions()` be called?
     **答案 | Answer:** 在屏幕组件内部，通常在 `useEffect` 钩子中或事件处理函数中 | Inside the screen component, typically in `useEffect` hooks or event handlers.

  2. 当组件状态改变时，`navigation.setOptions()` 会自动调用吗？| Does `navigation.setOptions()` automatically get called when component state changes?
     **答案 | Answer:** 否 | No - 需要在 `useEffect` 中明确调用，并将相关状态作为依赖项 | Needs to be explicitly called in `useEffect` with relevant state as dependencies.

  3. 可以在 `navigation.setOptions()` 中访问组件的 state 吗？| Can you access the component's state in `navigation.setOptions()`?
     **答案 | Answer:** 是 | Yes - 因为 `setOptions()` 在组件内部调用，可以访问所有组件状态和变量 | Yes - since `setOptions()` is called from within the component, it can access all component state and variables.

  4. 如果同时使用静态 `options` 和 `navigation.setOptions()`，哪个优先？| If both static `options` and `navigation.setOptions()` are used, which takes priority?
     **答案 | Answer:** `navigation.setOptions()` - 动态调用会覆盖静态配置 | `navigation.setOptions()` - dynamic calls override static configuration.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect, useLayoutEffect } from 'react';
  import { View, Text, Button, TextInput, StyleSheet } from 'react-native';

  // 示例1：根据状态动态更新标题 | Example 1: Dynamically update title based on state
  function EditScreen({ navigation }) {
    const [title, setTitle] = useState('');
    const [isSaved, setIsSaved] = useState(false);

    // useLayoutEffect 在布局更新前同步执行，避免闪烁 | useLayoutEffect executes synchronously before layout, avoiding flicker
    useLayoutEffect(() => {
      navigation.setOptions({
        // 根据保存状态动态设置标题 | Dynamically set title based on save state
        title: isSaved ? '已保存 | Saved' : '编辑中 | Editing',
        // 动态设置头部右侧按钮 | Dynamically set header right button
        headerRight: () => (
          <Button
            title={isSaved ? '✓' : '保存 | Save'}
            onPress={() => setIsSaved(true)}
            disabled={isSaved}
          />
        ),
      });
    }, [navigation, isSaved]);  // 依赖项：当 isSaved 变化时重新执行 | Dependencies: re-run when isSaved changes

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.input}
          value={title}
          onChangeText={setTitle}
          placeholder="输入标题 | Enter title"
        />
        <Text>状态 | Status: {isSaved ? '已保存 | Saved' : '未保存 | Unsaved'}</Text>
      </View>
    );
  }

  // 示例2：基于数据加载状态更新头部 | Example 2: Update header based on data loading state
  function ArticleScreen({ navigation, route }) {
    const [article, setArticle] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
      // 模拟数据加载 | Simulate data loading
      setTimeout(() => {
        setArticle({
          title: '深入理解 React Navigation | Deep Dive into React Navigation',
          author: 'Felix'
        });
        setLoading(false);
      }, 2000);
    }, []);

    useLayoutEffect(() => {
      navigation.setOptions({
        // 加载完成后显示文章标题 | Show article title after loading
        title: loading ? '加载中... | Loading...' : article?.title || '文章 | Article',
        headerRight: () => loading ? null : (
          <Button
            title="分享 | Share"
            onPress={() => console.log('Share:', article.title)}
          />
        ),
      });
    }, [navigation, loading, article]);

    return (
      <View style={styles.container}>
        {loading ? (
          <Text>加载文章... | Loading article...</Text>
        ) : (
          <>
            <Text style={styles.title}>{article.title}</Text>
            <Text style={styles.author}>作者 | Author: {article.author}</Text>
          </>
        )}
      </View>
    );
  }

  // 示例3：根据表单验证状态更新头部按钮 | Example 3: Update header button based on form validation
  function FormScreen({ navigation }) {
    const [name, setName] = useState('');
    const [email, setEmail] = useState('');

    // 表单验证逻辑 | Form validation logic
    const isValid = name.length > 0 && email.includes('@');

    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <Button
            title="提交 | Submit"
            onPress={() => console.log('Submit:', { name, email })}
            disabled={!isValid}  // 根据验证结果禁用/启用按钮 | Disable/enable based on validation
          />
        ),
      });
    }, [navigation, isValid, name, email]);

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.input}
          placeholder="姓名 | Name"
          value={name}
          onChangeText={setName}
        />
        <TextInput
          style={styles.input}
          placeholder="邮箱 | Email"
          value={email}
          onChangeText={setEmail}
        />
        <Text>表单有效 | Valid: {isValid ? '是 | Yes' : '否 | No'}</Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      padding: 10,
      marginBottom: 10,
      borderRadius: 5,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
    },
    author: {
      fontSize: 16,
      color: '#666',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么示例中使用 `useLayoutEffect` 而不是 `useEffect`？| Why use `useLayoutEffect` instead of `useEffect` in the examples?
    **答案 | Answer:** `useLayoutEffect` 在浏览器布局和绘制之前同步执行，避免头部更新时的闪烁。对于头部更新，`useLayoutEffect` 能提供更流畅的用户体验 | `useLayoutEffect` executes synchronously before browser layout and paint, avoiding flicker when updating the header. For header updates, `useLayoutEffect` provides a smoother user experience.

  - 在示例1中，如果移除依赖数组 `[navigation, isSaved]` 会发生什么？| In Example 1, what happens if we remove the dependency array `[navigation, isSaved]`?
    **答案 | Answer:** 每次组件重新渲染时都会调用 `setOptions`，可能导致性能问题和无限循环。正确做法是明确列出所有依赖项 | `setOptions` would be called on every component re-render, potentially causing performance issues and infinite loops. The correct approach is to explicitly list all dependencies.

  **常见误区检查 | Common Misconception Checks:**
  - 我可以在组件外部（如工具函数）调用 `navigation.setOptions()` 吗？| Can I call `navigation.setOptions()` outside the component (like in a utility function)?
    **答案 | Answer:** 不建议 | Not recommended - `setOptions()` 需要 `navigation` 对象，应该在组件内部调用。如果需要共享逻辑，可以创建自定义钩子（custom hook）封装 `setOptions` 调用 | `setOptions()` requires the `navigation` object and should be called within the component. If logic needs to be shared, create a custom hook to encapsulate the `setOptions` call.

  - `navigation.setOptions()` 会触发组件重新渲染吗？| Does `navigation.setOptions()` trigger component re-render?
    **答案 | Answer:** 不会 | No - `setOptions()` 只更新导航头部，不会触发屏幕组件本身的重新渲染 | `setOptions()` only updates the navigation header and doesn't trigger a re-render of the screen component itself.

### 2. 头部样式自定义 | Header Style Customization (1小时 | 1 hour)

- **头部样式选项详解 | Header Style Options Explained**

  **概念定义 | Concept Definition:**
  头部样式自定义是通过 React Navigation 提供的多种样式相关选项来控制导航头部的外观，包括背景色、文字颜色、字体样式、阴影等视觉属性。| Header style customization involves controlling the navigation header's appearance through various style-related options provided by React Navigation, including background color, text color, font styles, shadows, and other visual properties.

  **核心特征 | Key Characteristics:**
  - `headerStyle` 控制头部容器的样式（背景色、高度、阴影等）| `headerStyle` controls the header container's style (background, height, shadow, etc.)
  - `headerTintColor` 设置头部按钮和标题的默认颜色 | `headerTintColor` sets the default color for header buttons and title
  - `headerTitleStyle` 单独控制标题文字的样式（字体、大小、粗细）| `headerTitleStyle` separately controls title text style (font, size, weight)
  - `headerTitleAlign` 控制标题对齐方式（iOS默认居中，Android默认居左）| `headerTitleAlign` controls title alignment (iOS defaults to center, Android to left)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `headerTintColor` 会影响标题文字颜色吗？| Does `headerTintColor` affect the title text color?
     **答案 | Answer:** 是 | Yes - `headerTintColor` 是一个统一设置头部所有文字和图标颜色的属性，包括标题、返回按钮和自定义按钮 | `headerTintColor` is a unified property that sets the color for all header text and icons, including title, back button, and custom buttons.

  2. 如果同时设置了 `headerTintColor` 和 `headerTitleStyle: { color: 'red' }`，标题会是什么颜色？| If both `headerTintColor` and `headerTitleStyle: { color: 'red' }` are set, what color will the title be?
     **答案 | Answer:** 红色 | Red - `headerTitleStyle` 中的具体样式优先级高于 `headerTintColor` | Specific styles in `headerTitleStyle` have higher priority than `headerTintColor`.

  3. iOS 和 Android 平台上头部标题的默认对齐方式相同吗？| Is the default header title alignment the same on iOS and Android?
     **答案 | Answer:** 否 | No - iOS 默认居中对齐，Android 默认居左对齐。可以使用 `headerTitleAlign` 统一设置 | iOS defaults to center alignment, Android to left. Use `headerTitleAlign` to unify the alignment.

  4. `headerStyle` 中设置 `height` 可以改变头部高度吗？| Can setting `height` in `headerStyle` change the header height?
     **答案 | Answer:** 部分可以 | Partially - 在某些情况下有效，但不推荐。推荐使用 `headerStyle: { height: undefined }` 和平台默认值。强制改变高度可能导致布局问题 | Works in some cases but not recommended. Better to use `headerStyle: { height: undefined }` and platform defaults. Forcing height changes may cause layout issues.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { View, Text, StyleSheet, Platform } from 'react-native';

  const Stack = createNativeStackNavigator();

  // 示例1：完整的头部样式自定义 | Example 1: Complete header style customization
  function HomeScreen() {
    return (
      <View style={styles.screen}>
        <Text>自定义头部样式示例 | Custom Header Style Example</Text>
      </View>
    );
  }

  function App() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{
            title: '精美头部 | Beautiful Header',

            // 头部容器样式 | Header container style
            headerStyle: {
              backgroundColor: '#6200ee',  // 深紫色背景 | Deep purple background
              elevation: 5,  // Android 阴影（高度）| Android shadow (elevation)
              shadowColor: '#000',  // iOS 阴影颜色 | iOS shadow color
              shadowOffset: { width: 0, height: 2 },  // iOS 阴影偏移 | iOS shadow offset
              shadowOpacity: 0.25,  // iOS 阴影透明度 | iOS shadow opacity
              shadowRadius: 3.84,  // iOS 阴影半径 | iOS shadow radius
            },

            // 统一设置文字和图标颜色 | Unified text and icon color
            headerTintColor: '#ffffff',

            // 标题文字样式 | Title text style
            headerTitleStyle: {
              fontWeight: 'bold',  // 粗体 | Bold
              fontSize: 20,  // 字体大小 | Font size
              fontFamily: Platform.OS === 'ios' ? 'Arial' : 'Roboto',  // 平台特定字体 | Platform-specific font
            },

            // 标题对齐方式 | Title alignment
            headerTitleAlign: 'center',  // 两个平台都居中 | Center on both platforms
          }}
        />
      </Stack.Navigator>
    );
  }

  // 示例2：渐变色头部（使用自定义组件）| Example 2: Gradient header (using custom component)
  import { LinearGradient } from 'expo-linear-gradient';

  function ProfileScreen() {
    return (
      <View style={styles.screen}>
        <Text>个人资料 | Profile</Text>
      </View>
    );
  }

  function App2() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Profile"
          component={ProfileScreen}
          options={{
            title: '我的资料 | My Profile',
            headerTintColor: '#fff',

            // 使用自定义头部背景组件 | Use custom header background component
            headerBackground: () => (
              <LinearGradient
                colors={['#4c669f', '#3b5998', '#192f6a']}
                style={{ flex: 1 }}
                start={{ x: 0, y: 0 }}
                end={{ x: 1, y: 0 }}
              />
            ),

            headerTitleStyle: {
              fontWeight: 'bold',
              fontSize: 18,
            },
          }}
        />
      </Stack.Navigator>
    );
  }

  // 示例3：透明头部（用于图片背景）| Example 3: Transparent header (for image backgrounds)
  function PhotoScreen() {
    return (
      <View style={styles.photoScreen}>
        {/* 背景图片会延伸到头部下方 | Background image extends under header */}
        <View style={styles.imageBackground}>
          <Text style={styles.photoText}>透明头部效果 | Transparent Header Effect</Text>
        </View>
      </View>
    );
  }

  function App3() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Photo"
          component={PhotoScreen}
          options={{
            title: '照片详情 | Photo Details',

            // 透明头部配置 | Transparent header configuration
            headerTransparent: true,  // 头部透明 | Transparent header
            headerTintColor: '#fff',  // 白色文字和按钮 | White text and buttons
            headerTitleStyle: {
              fontWeight: 'bold',
              fontSize: 18,
              // 添加文字阴影提高可读性 | Add text shadow for readability
              textShadowColor: 'rgba(0, 0, 0, 0.75)',
              textShadowOffset: { width: -1, height: 1 },
              textShadowRadius: 10,
            },
            headerStyle: {
              backgroundColor: 'transparent',
            },
          }}
        />
      </Stack.Navigator>
    );
  }

  // 示例4：主题切换示例 | Example 4: Theme switching example
  function SettingsScreen() {
    const [darkMode, setDarkMode] = React.useState(false);
    const { navigation } = useNavigation();

    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerStyle: {
          backgroundColor: darkMode ? '#1a1a1a' : '#ffffff',
        },
        headerTintColor: darkMode ? '#ffffff' : '#000000',
      });
    }, [navigation, darkMode]);

    return (
      <View style={[styles.screen, darkMode && styles.darkScreen]}>
        <Button
          title={darkMode ? '切换到亮色 | Switch to Light' : '切换到暗色 | Switch to Dark'}
          onPress={() => setDarkMode(!darkMode)}
        />
      </View>
    );
  }

  const styles = StyleSheet.create({
    screen: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#ffffff',
    },
    darkScreen: {
      backgroundColor: '#1a1a1a',
    },
    photoScreen: {
      flex: 1,
    },
    imageBackground: {
      flex: 1,
      backgroundColor: '#4c669f',  // 模拟图片背景 | Simulate image background
      justifyContent: 'center',
      alignItems: 'center',
      paddingTop: 100,  // 为透明头部留出空间 | Leave space for transparent header
    },
    photoText: {
      color: '#fff',
      fontSize: 24,
      fontWeight: 'bold',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在示例1中，`elevation` 和 `shadowColor` 有什么区别？| In Example 1, what's the difference between `elevation` and `shadowColor`?
    **答案 | Answer:** `elevation` 是 Android 特有的阴影属性（基于 Material Design），值越大阴影越深。`shadowColor`、`shadowOffset`、`shadowOpacity` 等是 iOS 特有的阴影属性。跨平台应用需要同时设置两者 | `elevation` is Android-specific shadow property (based on Material Design), higher values create deeper shadows. `shadowColor`, `shadowOffset`, `shadowOpacity` are iOS-specific shadow properties. Cross-platform apps need to set both.

  - 在示例3中，`headerTransparent: true` 对布局有什么影响？| In Example 3, what layout impact does `headerTransparent: true` have?
    **答案 | Answer:** 屏幕内容会延伸到头部下方，需要手动添加顶部内边距（paddingTop）避免内容被头部遮挡。通常设置为头部高度（约100px包含状态栏）| Screen content extends under the header, requiring manual top padding to prevent content from being obscured. Typically set to header height (approximately 100px including status bar).

  **常见误区检查 | Common Misconception Checks:**
  - 设置 `headerTintColor` 就能改变头部背景色吗？| Does setting `headerTintColor` change the header background color?
    **答案 | Answer:** 不能 | No - `headerTintColor` 只影响文字和图标颜色，背景色需要通过 `headerStyle.backgroundColor` 设置 | `headerTintColor` only affects text and icon colors. Background color must be set via `headerStyle.backgroundColor`.

  - 透明头部（`headerTransparent`）和隐藏头部（`headerShown: false`）是一样的吗？| Are transparent header (`headerTransparent`) and hidden header (`headerShown: false`) the same?
    **答案 | Answer:** 不是 | No - 透明头部仍然存在，占据空间并显示标题和按钮（只是背景透明）。隐藏头部完全不显示，也不占据空间 | Transparent header still exists, occupies space, and shows title and buttons (just with transparent background). Hidden header is completely absent and occupies no space.

### 3. 头部按钮配置 | Header Button Configuration (1.5小时 | 1.5 hours)

- **左侧和右侧按钮 | Left and Right Buttons**

  **概念定义 | Concept Definition:**
  头部按钮配置是通过 `headerLeft` 和 `headerRight` 选项在导航头部添加自定义按钮或覆盖默认按钮（如返回按钮），用于实现搜索、保存、分享等常见操作。| Header button configuration involves adding custom buttons or overriding default buttons (like the back button) in the navigation header using `headerLeft` and `headerRight` options, to implement common actions like search, save, and share.

  **核心特征 | Key Characteristics:**
  - `headerLeft` 控制头部左侧内容（默认是返回按钮）| `headerLeft` controls left side content (defaults to back button)
  - `headerRight` 控制头部右侧内容（默认为空）| `headerRight` controls right side content (defaults to empty)
  - 两者都接收返回 React 元素的函数 | Both accept functions returning React elements
  - 函数接收 `tintColor`、`canGoBack` 等参数 | Functions receive parameters like `tintColor`, `canGoBack`
  - 可以使用内置的 `HeaderBackButton` 组件或完全自定义 | Can use built-in `HeaderBackButton` component or fully customize

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `headerLeft` 和 `headerRight` 接收的是组件还是函数？| Do `headerLeft` and `headerRight` accept components or functions?
     **答案 | Answer:** 函数 | Functions - 它们接收返回 React 元素的函数，而不是直接的组件。函数参数包含 `tintColor`、`pressColor`、`canGoBack` 等有用信息 | They accept functions that return React elements, not direct components. Function parameters include useful information like `tintColor`, `pressColor`, `canGoBack`.

  2. 设置 `headerLeft: null` 和 `headerLeft: () => null` 有区别吗？| Is there a difference between `headerLeft: null` and `headerLeft: () => null`?
     **答案 | Answer:** 是 | Yes - `headerLeft: null` 会隐藏左侧按钮（包括返回按钮），`headerLeft: () => null` 也隐藏但保留按钮占位空间。通常使用 `headerLeft: () => null` 来禁用返回按钮 | `headerLeft: null` hides the left button (including back button), `headerLeft: () => null` also hides but reserves button placeholder space. Typically use `headerLeft: () => null` to disable back button.

  3. 可以在头部按钮中访问 `navigation` 对象吗？| Can you access the `navigation` object in header buttons?
     **答案 | Answer:** 可以，但需要通过闭包 | Yes, but through closure - 在 `options` 函数形式中可以访问 `navigation` 参数，然后在 `headerRight`/`headerLeft` 返回的组件中使用。或者在组件内使用 `setOptions` 时直接访问 `navigation` | In `options` function form, access the `navigation` parameter and use it in components returned by `headerRight`/`headerLeft`. Or when using `setOptions` in the component, access `navigation` directly.

  4. 头部按钮的点击事件应该如何处理导航？| How should header button click events handle navigation?
     **答案 | Answer:** 使用 `navigation.navigate()`、`navigation.goBack()` 等方法 | Use methods like `navigation.navigate()`, `navigation.goBack()` - 头部按钮可以触发任何导航操作，与屏幕内按钮完全相同 | Header buttons can trigger any navigation action, exactly like buttons within screens.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useLayoutEffect } from 'react';
  import { View, Text, Button, TouchableOpacity, StyleSheet, Alert } from 'react-native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Ionicons } from '@expo/vector-icons';

  const Stack = createNativeStackNavigator();

  // 示例1：基础的右侧按钮 | Example 1: Basic right button
  function HomeScreen({ navigation }) {
    return (
      <View style={styles.screen}>
        <Text>主屏幕 | Home Screen</Text>
        <Button
          title="去设置 | Go to Settings"
          onPress={() => navigation.navigate('Settings')}
        />
      </View>
    );
  }

  function App1() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={({ navigation }) => ({
            title: '首页 | Home',
            // 添加右侧按钮 | Add right button
            headerRight: () => (
              <Button
                title="设置 | Settings"
                onPress={() => navigation.navigate('Settings')}
                color="#fff"
              />
            ),
            headerStyle: {
              backgroundColor: '#6200ee',
            },
            headerTintColor: '#fff',
          })}
        />
        <Stack.Screen name="Settings" component={SettingsScreen} />
      </Stack.Navigator>
    );
  }

  // 示例2：图标按钮和多个按钮 | Example 2: Icon buttons and multiple buttons
  function EditScreen({ navigation, route }) {
    const [content, setContent] = useState(route.params?.content || '');
    const [isSaved, setIsSaved] = useState(true);

    useLayoutEffect(() => {
      navigation.setOptions({
        title: '编辑文档 | Edit Document',
        // 右侧多个按钮 | Multiple right buttons
        headerRight: () => (
          <View style={{ flexDirection: 'row', marginRight: 10 }}>
            {/* 分享按钮 | Share button */}
            <TouchableOpacity
              onPress={() => Alert.alert('分享 | Share', content)}
              style={{ marginRight: 15 }}
            >
              <Ionicons name="share-outline" size={24} color="#fff" />
            </TouchableOpacity>

            {/* 保存按钮 | Save button */}
            <TouchableOpacity
              onPress={() => {
                setIsSaved(true);
                Alert.alert('已保存 | Saved');
              }}
              disabled={isSaved}
            >
              <Ionicons
                name={isSaved ? "checkmark-circle" : "save-outline"}
                size={24}
                color={isSaved ? "#4CAF50" : "#fff"}
              />
            </TouchableOpacity>
          </View>
        ),
        // 自定义左侧返回按钮 | Custom left back button
        headerLeft: () => (
          <TouchableOpacity
            onPress={() => {
              if (!isSaved) {
                Alert.alert(
                  '未保存的更改 | Unsaved Changes',
                  '确定要离开吗？| Are you sure you want to leave?',
                  [
                    { text: '取消 | Cancel', style: 'cancel' },
                    { text: '离开 | Leave', onPress: () => navigation.goBack() },
                  ]
                );
              } else {
                navigation.goBack();
              }
            }}
          >
            <Ionicons name="arrow-back" size={24} color="#fff" style={{ marginLeft: 10 }} />
          </TouchableOpacity>
        ),
        headerStyle: {
          backgroundColor: '#6200ee',
        },
        headerTintColor: '#fff',
      });
    }, [navigation, content, isSaved]);

    return (
      <View style={styles.screen}>
        <Text>编辑内容... | Editing content...</Text>
        <Text>保存状态 | Save status: {isSaved ? '已保存 | Saved' : '未保存 | Unsaved'}</Text>
        <Button title="修改内容 | Modify" onPress={() => setIsSaved(false)} />
      </View>
    );
  }

  // 示例3：动态禁用返回按钮 | Example 3: Dynamically disable back button
  function QuizScreen({ navigation }) {
    const [quizStarted, setQuizStarted] = useState(false);
    const [currentQuestion, setCurrentQuestion] = useState(0);

    useLayoutEffect(() => {
      navigation.setOptions({
        title: `问题 ${currentQuestion + 1}/10 | Question ${currentQuestion + 1}/10`,
        // 测验进行中禁用返回按钮 | Disable back button during quiz
        headerLeft: quizStarted ? () => null : undefined,  // undefined 使用默认返回按钮 | undefined uses default back button
        headerRight: () => quizStarted ? (
          <Button
            title="退出测验 | Exit Quiz"
            onPress={() => {
              Alert.alert(
                '确认退出 | Confirm Exit',
                '测验进度将不被保存 | Quiz progress will not be saved',
                [
                  { text: '继续测验 | Continue', style: 'cancel' },
                  {
                    text: '退出 | Exit',
                    onPress: () => {
                      setQuizStarted(false);
                      navigation.goBack();
                    }
                  },
                ]
              );
            }}
            color="#f44336"
          />
        ) : null,
      });
    }, [navigation, quizStarted, currentQuestion]);

    return (
      <View style={styles.screen}>
        {!quizStarted ? (
          <Button
            title="开始测验 | Start Quiz"
            onPress={() => setQuizStarted(true)}
          />
        ) : (
          <>
            <Text>问题 {currentQuestion + 1} | Question {currentQuestion + 1}</Text>
            <Button
              title="下一题 | Next"
              onPress={() => setCurrentQuestion(prev => Math.min(prev + 1, 9))}
            />
          </>
        )}
      </View>
    );
  }

  // 示例4：使用 HeaderBackButton 组件 | Example 4: Using HeaderBackButton component
  import { HeaderBackButton } from '@react-navigation/elements';

  function DetailScreen({ navigation }) {
    return (
      <View style={styles.screen}>
        <Text>详情页 | Detail Screen</Text>
      </View>
    );
  }

  function App4() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Detail"
          component={DetailScreen}
          options={{
            title: '详情 | Details',
            // 使用内置的 HeaderBackButton 并自定义 | Use built-in HeaderBackButton with customization
            headerLeft: (props) => (
              <HeaderBackButton
                {...props}
                label="返回首页 | Back Home"  // 自定义返回文本 | Custom back text
                onPress={() => {
                  Alert.alert('自定义返回 | Custom Back');
                  props.onPress();  // 调用默认返回逻辑 | Call default back logic
                }}
              />
            ),
          }}
        />
      </Stack.Navigator>
    );
  }

  const styles = StyleSheet.create({
    screen: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在示例2中，为什么要使用 `View` 包裹多个按钮？| In Example 2, why wrap multiple buttons with a `View`?
    **答案 | Answer:** `headerRight` 只能返回单个 React 元素。使用 `View` 配合 `flexDirection: 'row'` 可以将多个按钮横向排列 | `headerRight` can only return a single React element. Using `View` with `flexDirection: 'row'` arranges multiple buttons horizontally.

  - 在示例3中，`headerLeft: () => null` 和 `headerLeft: undefined` 有什么区别？| In Example 3, what's the difference between `headerLeft: () => null` and `headerLeft: undefined`?
    **答案 | Answer:** `() => null` 明确移除左侧按钮，`undefined` 使用默认行为（显示返回按钮）。这允许在测验进行中禁用返回，测验未开始时恢复默认 | `() => null` explicitly removes the left button, `undefined` uses default behavior (shows back button). This allows disabling back during quiz, restoring default when quiz hasn't started.

  **常见误区检查 | Common Misconception Checks:**
  - 我可以直接写 `headerRight: <Button ... />` 吗？| Can I write `headerRight: <Button ... />` directly?
    **答案 | Answer:** 不能 | No - `headerRight` 必须是返回组件的函数，不能是直接的 JSX。正确写法：`headerRight: () => <Button ... />` | `headerRight` must be a function returning a component, not direct JSX. Correct syntax: `headerRight: () => <Button ... />`.

  - 头部按钮中的 `navigation` 对象从哪里来？| Where does the `navigation` object in header buttons come from?
    **答案 | Answer:** 从 `options` 函数的参数中获取，或在组件内使用 `setOptions` 时通过闭包访问。不能在头部按钮内部使用 `useNavigation` 钩子 | Obtained from `options` function parameters, or accessed through closure when using `setOptions` in component. Cannot use `useNavigation` hook inside header buttons.
