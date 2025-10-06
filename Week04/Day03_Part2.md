# React Native入门 - 第3天：头部定制与选项配置（续）| React Native Introduction - Day 3: Header Customization & Options (Continued)

## 详细内容（续）| Detailed Content (Continued)

### 4. 隐藏头部与自定义导航体验 | Hiding Headers & Custom Navigation Experiences (1小时 | 1 hour)

- **条件性显示/隐藏头部 | Conditional Show/Hide Headers**

  **概念定义 | Concept Definition:**
  通过 `headerShown` 选项可以完全隐藏特定屏幕的导航头部，这在需要全屏体验（如启动页、登录页、媒体查看器）或实现自定义导航栏时非常有用。| The `headerShown` option allows completely hiding the navigation header for specific screens, which is useful for full-screen experiences (like splash screens, login pages, media viewers) or when implementing custom navigation bars.

  **核心特征 | Key Characteristics:**
  - `headerShown: false` 完全移除头部，不占据任何空间 | `headerShown: false` completely removes the header, occupying no space
  - 可以在导航器级别（全局）或屏幕级别（单独）设置 | Can be set at navigator level (global) or screen level (individual)
  - 隐藏头部后需要自行处理导航（返回按钮等）| After hiding header, navigation must be handled manually (back buttons, etc.)
  - 与 `headerTransparent: true` 不同，完全不渲染头部组件 | Unlike `headerTransparent: true`, header component is not rendered at all

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `headerShown: false` 和 `headerTransparent: true` 的主要区别是什么？| What's the main difference between `headerShown: false` and `headerTransparent: true`?
     **答案 | Answer:** `headerShown: false` 完全不渲染头部，屏幕内容从顶部开始。`headerTransparent: true` 仍然渲染头部（显示标题和按钮），但背景透明且内容延伸到头部下方 | `headerShown: false` doesn't render the header at all, screen content starts from the top. `headerTransparent: true` still renders the header (showing title and buttons) but with transparent background and content extending under it.

  2. 如果在导航器级别设置 `headerShown: false`，可以在某个屏幕上重新启用头部吗？| If `headerShown: false` is set at navigator level, can the header be re-enabled for a specific screen?
     **答案 | Answer:** 可以 | Yes - 屏幕级别的选项覆盖导航器级别的选项。在特定屏幕设置 `headerShown: true` 即可 | Screen-level options override navigator-level options. Set `headerShown: true` on the specific screen.

  3. 隐藏头部后，用户如何返回上一屏幕？| After hiding the header, how can users return to the previous screen?
     **答案 | Answer:** 需要在屏幕内容中自行实现返回按钮，使用 `navigation.goBack()` 或 `navigation.navigate()` | Must implement a back button in the screen content yourself, using `navigation.goBack()` or `navigation.navigate()`.

  4. 隐藏头部会影响 SafeAreaView 的行为吗？| Does hiding the header affect SafeAreaView behavior?
     **答案 | Answer:** 不会 | No - `SafeAreaView` 独立处理安全区域（刘海屏、状态栏等）。隐藏头部后仍需使用 `SafeAreaView` 避免内容被系统 UI 遮挡 | `SafeAreaView` independently handles safe areas (notches, status bar, etc.). Still need to use `SafeAreaView` after hiding header to avoid content being obscured by system UI.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, TouchableOpacity, StyleSheet, StatusBar, ImageBackground } from 'react-native';
  import { SafeAreaView } from 'react-native-safe-area-context';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Ionicons } from '@expo/vector-icons';

  const Stack = createNativeStackNavigator();

  // 示例1：启动页 - 隐藏头部的全屏体验 | Example 1: Splash screen - full-screen with hidden header
  function SplashScreen({ navigation }) {
    React.useEffect(() => {
      // 3秒后自动跳转到主页 | Auto-navigate to home after 3 seconds
      const timer = setTimeout(() => {
        navigation.replace('Home');
      }, 3000);

      return () => clearTimeout(timer);
    }, [navigation]);

    return (
      <View style={styles.splashContainer}>
        {/* 隐藏头部后，内容从屏幕顶部开始 | With hidden header, content starts from screen top */}
        <StatusBar barStyle="light-content" backgroundColor="#6200ee" />
        <Text style={styles.splashText}>欢迎使用我的应用 | Welcome to My App</Text>
        <Text style={styles.splashSubtext}>加载中... | Loading...</Text>
      </View>
    );
  }

  // 示例2：登录页 - 自定义导航栏 | Example 2: Login page - custom navigation bar
  function LoginScreen({ navigation }) {
    return (
      <SafeAreaView style={styles.loginContainer} edges={['top', 'left', 'right']}>
        {/* 自定义头部替代默认导航栏 | Custom header replacing default navigation bar */}
        <View style={styles.customHeader}>
          <TouchableOpacity
            onPress={() => navigation.goBack()}
            style={styles.backButton}
          >
            <Ionicons name="arrow-back" size={24} color="#000" />
          </TouchableOpacity>
          <Text style={styles.customHeaderTitle}>登录 | Login</Text>
          <View style={{ width: 40 }} />  {/* 占位保持标题居中 | Placeholder to keep title centered */}
        </View>

        <View style={styles.loginContent}>
          <Text style={styles.loginTitle}>欢迎回来 | Welcome Back</Text>
          {/* 登录表单内容... | Login form content... */}
        </View>
      </SafeAreaView>
    );
  }

  // 示例3：图片查看器 - 沉浸式全屏 | Example 3: Image viewer - immersive full-screen
  function ImageViewerScreen({ navigation, route }) {
    const [showControls, setShowControls] = React.useState(true);
    const { imageUrl } = route.params;

    // 点击屏幕切换控制栏显示 | Tap screen to toggle controls
    const toggleControls = () => {
      setShowControls(!showControls);
    };

    return (
      <View style={styles.imageViewerContainer}>
        <StatusBar hidden={!showControls} />  {/* 隐藏状态栏实现完全沉浸 | Hide status bar for full immersion */}

        <TouchableOpacity
          style={styles.imageViewerTouchArea}
          activeOpacity={1}
          onPress={toggleControls}
        >
          <ImageBackground
            source={{ uri: imageUrl || 'https://via.placeholder.com/400' }}
            style={styles.fullScreenImage}
            resizeMode="contain"
          >
            {/* 仅在显示控制栏时渲染按钮 | Only render buttons when controls are shown */}
            {showControls && (
              <>
                <SafeAreaView style={styles.imageViewerTopBar} edges={['top']}>
                  <TouchableOpacity
                    onPress={() => navigation.goBack()}
                    style={styles.imageViewerButton}
                  >
                    <Ionicons name="close" size={30} color="#fff" />
                  </TouchableOpacity>
                </SafeAreaView>

                <SafeAreaView style={styles.imageViewerBottomBar} edges={['bottom']}>
                  <TouchableOpacity style={styles.imageViewerButton}>
                    <Ionicons name="share-outline" size={28} color="#fff" />
                  </TouchableOpacity>
                  <TouchableOpacity style={styles.imageViewerButton}>
                    <Ionicons name="download-outline" size={28} color="#fff" />
                  </TouchableOpacity>
                </SafeAreaView>
              </>
            )}
          </ImageBackground>
        </TouchableOpacity>
      </View>
    );
  }

  // 示例4：条件性显示头部 | Example 4: Conditional header display
  function VideoPlayerScreen({ navigation }) {
    const [isFullscreen, setIsFullscreen] = React.useState(false);

    React.useLayoutEffect(() => {
      navigation.setOptions({
        // 全屏模式隐藏头部 | Hide header in fullscreen mode
        headerShown: !isFullscreen,
      });
    }, [navigation, isFullscreen]);

    return (
      <View style={styles.videoContainer}>
        <View style={[
          styles.videoPlayer,
          isFullscreen && styles.videoPlayerFullscreen
        ]}>
          <Text style={styles.videoText}>
            {isFullscreen ? '全屏视频 | Fullscreen Video' : '普通视频 | Normal Video'}
          </Text>

          {/* 全屏切换按钮 | Fullscreen toggle button */}
          <TouchableOpacity
            onPress={() => setIsFullscreen(!isFullscreen)}
            style={styles.fullscreenButton}
          >
            <Ionicons
              name={isFullscreen ? "contract-outline" : "expand-outline"}
              size={24}
              color="#fff"
            />
          </TouchableOpacity>

          {/* 全屏模式下的关闭按钮 | Close button in fullscreen mode */}
          {isFullscreen && (
            <SafeAreaView style={styles.fullscreenCloseButton} edges={['top', 'left']}>
              <TouchableOpacity onPress={() => setIsFullscreen(false)}>
                <Ionicons name="close" size={30} color="#fff" />
              </TouchableOpacity>
            </SafeAreaView>
          )}
        </View>

        {!isFullscreen && (
          <View style={styles.videoInfo}>
            <Text>视频标题 | Video Title</Text>
            <Text>视频描述... | Video description...</Text>
          </View>
        )}
      </View>
    );
  }

  function App() {
    return (
      <Stack.Navigator>
        {/* 启动页：隐藏头部 | Splash screen: header hidden */}
        <Stack.Screen
          name="Splash"
          component={SplashScreen}
          options={{
            headerShown: false,  // 完全隐藏头部 | Completely hide header
          }}
        />

        {/* 主页：显示头部 | Home: show header */}
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: '首页 | Home' }}
        />

        {/* 登录页：隐藏默认头部，使用自定义头部 | Login: hide default header, use custom */}
        <Stack.Screen
          name="Login"
          component={LoginScreen}
          options={{
            headerShown: false,
            animation: 'slide_from_bottom',  // 从底部滑入动画 | Slide from bottom animation
          }}
        />

        {/* 图片查看器：全屏模式 | Image viewer: fullscreen mode */}
        <Stack.Screen
          name="ImageViewer"
          component={ImageViewerScreen}
          options={{
            headerShown: false,
            animation: 'fade',  // 淡入淡出动画 | Fade animation
          }}
        />

        {/* 视频播放器：条件性显示头部 | Video player: conditional header */}
        <Stack.Screen
          name="VideoPlayer"
          component={VideoPlayerScreen}
          options={{
            title: '视频播放器 | Video Player',
          }}
        />
      </Stack.Navigator>
    );
  }

  const styles = StyleSheet.create({
    splashContainer: {
      flex: 1,
      backgroundColor: '#6200ee',
      justifyContent: 'center',
      alignItems: 'center',
    },
    splashText: {
      fontSize: 32,
      fontWeight: 'bold',
      color: '#fff',
      marginBottom: 20,
    },
    splashSubtext: {
      fontSize: 16,
      color: '#fff',
      opacity: 0.8,
    },
    loginContainer: {
      flex: 1,
      backgroundColor: '#fff',
    },
    customHeader: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
      paddingHorizontal: 16,
      paddingVertical: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
    },
    backButton: {
      width: 40,
      height: 40,
      justifyContent: 'center',
      alignItems: 'center',
    },
    customHeaderTitle: {
      fontSize: 18,
      fontWeight: 'bold',
    },
    loginContent: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
    },
    loginTitle: {
      fontSize: 28,
      fontWeight: 'bold',
      marginBottom: 30,
    },
    imageViewerContainer: {
      flex: 1,
      backgroundColor: '#000',
    },
    imageViewerTouchArea: {
      flex: 1,
    },
    fullScreenImage: {
      flex: 1,
    },
    imageViewerTopBar: {
      position: 'absolute',
      top: 0,
      left: 0,
      right: 0,
      flexDirection: 'row',
      justifyContent: 'flex-end',
      padding: 10,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
    },
    imageViewerBottomBar: {
      position: 'absolute',
      bottom: 0,
      left: 0,
      right: 0,
      flexDirection: 'row',
      justifyContent: 'space-around',
      padding: 20,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
    },
    imageViewerButton: {
      padding: 10,
    },
    videoContainer: {
      flex: 1,
      backgroundColor: '#000',
    },
    videoPlayer: {
      width: '100%',
      aspectRatio: 16 / 9,
      backgroundColor: '#000',
      justifyContent: 'center',
      alignItems: 'center',
    },
    videoPlayerFullscreen: {
      flex: 1,
      width: '100%',
      height: '100%',
    },
    videoText: {
      color: '#fff',
      fontSize: 18,
    },
    fullscreenButton: {
      position: 'absolute',
      bottom: 20,
      right: 20,
      padding: 10,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
      borderRadius: 5,
    },
    fullscreenCloseButton: {
      position: 'absolute',
      top: 0,
      left: 0,
      padding: 10,
    },
    videoInfo: {
      padding: 20,
      backgroundColor: '#fff',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在示例1中，为什么启动页要使用 `navigation.replace()` 而不是 `navigate()`？| In Example 1, why does the splash screen use `navigation.replace()` instead of `navigate()`?
    **答案 | Answer:** `replace()` 替换当前屏幕而不是在堆栈中添加新屏幕，用户无法返回到启动页。这是启动页的标准做法 | `replace()` replaces the current screen instead of adding a new one to the stack, preventing users from going back to the splash screen. This is standard practice for splash screens.

  - 在示例2中，为什么自定义头部需要使用 `SafeAreaView`？| In Example 2, why does the custom header need to use `SafeAreaView`?
    **答案 | Answer:** 隐藏默认头部后，自定义头部需要避免被状态栏、刘海屏等系统 UI 遮挡。`SafeAreaView` 自动添加安全区域内边距 | After hiding the default header, the custom header needs to avoid being obscured by system UI like status bar and notches. `SafeAreaView` automatically adds safe area padding.

  **常见误区检查 | Common Misconception Checks:**
  - 隐藏头部后，Android 的返回键还能工作吗？| After hiding the header, does Android's back button still work?
    **答案 | Answer:** 是 | Yes - Android 硬件/手势返回键由系统处理，与头部显示无关。但 iOS 没有系统返回键，隐藏头部后必须提供屏幕内返回按钮 | Android hardware/gesture back button is handled by the system, independent of header visibility. But iOS has no system back button, so after hiding the header you must provide an in-screen back button.

  - 设置 `headerShown: false` 后，还能使用 `navigation.setOptions()` 更新其他头部选项吗？| After setting `headerShown: false`, can you still use `navigation.setOptions()` to update other header options?
    **答案 | Answer:** 可以，但无效 | Yes, but ineffective - 可以调用 `setOptions()` 设置头部选项，但因为头部不显示，这些选项不会有任何视觉效果。需要先设置 `headerShown: true` | You can call `setOptions()` to set header options, but since the header isn't shown, these options have no visual effect. Need to set `headerShown: true` first.

### 5. 最佳实践与性能优化 | Best Practices & Performance Optimization (45分钟 | 45 minutes)

- **头部配置最佳实践 | Header Configuration Best Practices**

  **关键原则 | Key Principles:**
  - 优先使用静态选项配置，仅在必要时使用动态配置 | Prefer static options, use dynamic configuration only when necessary
  - 将共享的头部样式提取到导航器级别的 `screenOptions` | Extract shared header styles to navigator-level `screenOptions`
  - 使用 `useLayoutEffect` 而不是 `useEffect` 来设置头部选项 | Use `useLayoutEffect` instead of `useEffect` for setting header options
  - 避免在头部按钮中执行昂贵的计算或渲染 | Avoid expensive computations or renders in header buttons
  - 合理使用 `useMemo` 和 `useCallback` 优化头部组件性能 | Properly use `useMemo` and `useCallback` to optimize header component performance

  **实践验证问题 | Practice Verification Questions:**
  1. 为什么应该将共享样式放在 `screenOptions` 而不是每个屏幕的 `options`？| Why should shared styles be placed in `screenOptions` instead of each screen's `options`?
     **答案 | Answer:** 减少代码重复，提高可维护性。在 `screenOptions` 中定义一次，所有屏幕自动继承，修改时只需更改一处 | Reduces code duplication and improves maintainability. Define once in `screenOptions`, all screens automatically inherit, and changes only need to be made in one place.

  2. 什么时候应该使用 `useCallback` 包裹头部按钮的点击处理函数？| When should `useCallback` be used to wrap header button click handlers?
     **答案 | Answer:** 当点击处理函数依赖组件状态或 props，且在 `useLayoutEffect` 中频繁更新头部选项时。`useCallback` 避免每次渲染都创建新函数，减少不必要的头部重新渲染 | When the click handler depends on component state or props and header options are frequently updated in `useLayoutEffect`. `useCallback` prevents creating new functions on every render, reducing unnecessary header re-renders.

  3. 应该在哪里定义应用的通用头部主题？| Where should the application's common header theme be defined?
     **答案 | Answer:** 在根导航器的 `screenOptions` 中定义全局主题，在特定导航器或屏幕中覆盖特定样式 | Define global theme in the root navigator's `screenOptions`, override specific styles in particular navigators or screens.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useCallback, useMemo, useLayoutEffect } from 'react';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Platform } from 'react-native';

  const Stack = createNativeStackNavigator();

  // 最佳实践1：提取共享主题配置 | Best Practice 1: Extract shared theme configuration
  const commonHeaderOptions = {
    headerStyle: {
      backgroundColor: '#6200ee',
      elevation: 4,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.25,
      shadowRadius: 3.84,
    },
    headerTintColor: '#fff',
    headerTitleStyle: {
      fontWeight: 'bold',
      fontSize: Platform.OS === 'ios' ? 17 : 20,
    },
    headerTitleAlign: 'center',
    headerBackTitleVisible: false,  // iOS: 隐藏返回按钮文字 | Hide back button text
  };

  // 最佳实践2：使用 useCallback 优化按钮点击处理 | Best Practice 2: Optimize button clicks with useCallback
  function OptimizedScreen({ navigation }) {
    const [count, setCount] = React.useState(0);
    const [items, setItems] = React.useState([]);

    // 使用 useCallback 避免每次渲染都创建新函数 | Use useCallback to avoid creating new function on every render
    const handleSave = useCallback(() => {
      console.log('Save clicked', count);
      // 保存逻辑... | Save logic...
    }, [count]);  // 仅在 count 变化时重新创建 | Only recreate when count changes

    const handleShare = useCallback(() => {
      console.log('Share clicked');
      // 分享逻辑... | Share logic...
    }, []);  // 无依赖，函数永远不变 | No dependencies, function never changes

    // 使用 useMemo 优化头部按钮组件 | Use useMemo to optimize header button component
    const headerRightComponent = useMemo(() => (
      <View style={{ flexDirection: 'row' }}>
        <TouchableOpacity onPress={handleShare} style={{ marginRight: 15 }}>
          <Ionicons name="share-outline" size={24} color="#fff" />
        </TouchableOpacity>
        <TouchableOpacity onPress={handleSave}>
          <Ionicons name="save-outline" size={24} color="#fff" />
        </TouchableOpacity>
      </View>
    ), [handleSave, handleShare]);  // 仅在处理函数变化时重新创建 | Only recreate when handlers change

    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => headerRightComponent,
      });
    }, [navigation, headerRightComponent]);

    return (
      <View>
        <Text>Count: {count}</Text>
        <Button title="Increment" onPress={() => setCount(c => c + 1)} />
      </View>
    );
  }

  // 最佳实践3：组织化的导航结构 | Best Practice 3: Organized navigation structure
  function App() {
    return (
      <Stack.Navigator
        // 全局默认选项 | Global default options
        screenOptions={commonHeaderOptions}
      >
        {/* 主要屏幕组 | Main screen group */}
        <Stack.Group>
          <Stack.Screen
            name="Home"
            component={HomeScreen}
            options={{ title: '首页 | Home' }}
          />
          <Stack.Screen
            name="Profile"
            component={ProfileScreen}
            options={{ title: '个人资料 | Profile' }}
          />
        </Stack.Group>

        {/* 模态屏幕组 - 不同的样式 | Modal screen group - different style */}
        <Stack.Group
          screenOptions={{
            presentation: 'modal',
            headerStyle: {
              backgroundColor: '#03dac6',  // 覆盖默认背景色 | Override default background
            },
          }}
        >
          <Stack.Screen
            name="Settings"
            component={SettingsScreen}
            options={{ title: '设置 | Settings' }}
          />
          <Stack.Screen
            name="EditProfile"
            component={EditProfileScreen}
            options={{ title: '编辑资料 | Edit Profile' }}
          />
        </Stack.Group>

        {/* 全屏屏幕组 - 无头部 | Fullscreen group - no header */}
        <Stack.Group screenOptions={{ headerShown: false }}>
          <Stack.Screen name="Splash" component={SplashScreen} />
          <Stack.Screen name="Onboarding" component={OnboardingScreen} />
        </Stack.Group>
      </Stack.Navigator>
    );
  }

  // 最佳实践4：自定义头部组件复用 | Best Practice 4: Reusable custom header components
  function CustomHeaderButton({ icon, onPress, color = '#fff' }) {
    return (
      <TouchableOpacity
        onPress={onPress}
        style={{
          padding: 8,
          marginHorizontal: 4,
        }}
      >
        <Ionicons name={icon} size={24} color={color} />
      </TouchableOpacity>
    );
  }

  function ReusableHeaderScreen({ navigation }) {
    const handleEdit = useCallback(() => {
      console.log('Edit');
    }, []);

    const handleDelete = useCallback(() => {
      console.log('Delete');
    }, []);

    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <View style={{ flexDirection: 'row' }}>
            <CustomHeaderButton icon="create-outline" onPress={handleEdit} />
            <CustomHeaderButton icon="trash-outline" onPress={handleDelete} />
          </View>
        ),
      });
    }, [navigation, handleEdit, handleDelete]);

    return <View><Text>可复用头部按钮 | Reusable Header Buttons</Text></View>;
  }

  // 最佳实践5：避免的反模式 | Best Practice 5: Anti-patterns to avoid
  function BadPracticeScreen({ navigation }) {
    const [data, setData] = React.useState([]);

    // ❌ 错误：在 useEffect 中设置头部选项会导致闪烁 | Wrong: Setting header in useEffect causes flicker
    React.useEffect(() => {
      navigation.setOptions({
        title: `项目数: ${data.length}`,
      });
    }, [navigation, data]);

    // ✅ 正确：使用 useLayoutEffect | Correct: Use useLayoutEffect
    React.useLayoutEffect(() => {
      navigation.setOptions({
        title: `项目数: ${data.length}`,
      });
    }, [navigation, data]);

    // ❌ 错误：每次渲染都创建新的头部按钮函数 | Wrong: Creating new header button function on every render
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <Button
            title="保存"
            onPress={() => {
              // 直接在这里定义逻辑会导致每次渲染都创建新函数
              // Defining logic here creates new function on every render
              console.log(data);
            }}
          />
        ),
      });
    }, [navigation, data]);  // data 变化时会导致不必要的更新 | Changes in data cause unnecessary updates

    // ✅ 正确：使用 useCallback | Correct: Use useCallback
    const handleSave = useCallback(() => {
      console.log(data);
    }, [data]);

    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => <Button title="保存" onPress={handleSave} />,
      });
    }, [navigation, handleSave]);

    return <View />;
  }
  ```

  **综合应用检查 | Comprehensive Application Check:**
  - 为什么在头部配置中使用 `Platform.OS` 检查是常见做法？| Why is using `Platform.OS` checks common in header configuration?
    **答案 | Answer:** iOS 和 Android 有不同的设计规范（Human Interface Guidelines vs Material Design）。例如 iOS 头部标题默认居中且较小（17pt），Android 默认居左且较大（20sp）。平台检查可以遵循各平台规范 | iOS and Android have different design guidelines (HIG vs Material Design). For example, iOS header titles default to center and smaller (17pt), Android defaults to left and larger (20sp). Platform checks allow following each platform's conventions.

  - 什么时候应该使用 `Stack.Group` 而不是单独配置每个屏幕？| When should `Stack.Group` be used instead of configuring each screen individually?
    **答案 | Answer:** 当多个屏幕共享相同的配置模式时（如所有模态屏幕、所有无头部屏幕、所有特定主题的屏幕）。`Group` 提供了更好的组织结构和可维护性 | When multiple screens share the same configuration pattern (like all modal screens, all headerless screens, all screens with specific theme). `Group` provides better organization and maintainability.

### 6. 知识巩固与检查 | Knowledge Consolidation and Review (30分钟 | 30 minutes)

- **综合概念检查 | Comprehensive Concept Check:**

  1. 静态选项配置和动态选项配置的核心区别是什么？各自的使用场景是什么？| What's the core difference between static and dynamic options configuration? What are their respective use cases?
     **答案 | Answer:** 静态选项在屏幕定义时确定，适合固定不变的配置（如固定的标题、样式）。动态选项通过 `navigation.setOptions()` 在组件内部更新，适合需要响应状态变化的场景（如基于数据的标题、基于验证的按钮状态）。静态配置性能更好，动态配置更灵活 | Static options are determined at screen definition, suitable for fixed configurations (like fixed titles, styles). Dynamic options are updated via `navigation.setOptions()` inside components, suitable for scenarios needing to respond to state changes (like data-based titles, validation-based button states). Static is more performant, dynamic is more flexible.

  2. `headerStyle`、`headerTintColor` 和 `headerTitleStyle` 之间的关系和优先级是什么？| What's the relationship and priority between `headerStyle`, `headerTintColor`, and `headerTitleStyle`?
     **答案 | Answer:** `headerStyle` 控制头部容器样式（背景、阴影等），`headerTintColor` 统一设置所有文字和图标颜色，`headerTitleStyle` 专门控制标题文字样式。优先级：`headerTitleStyle` 中的具体样式 > `headerTintColor` > 默认值 | `headerStyle` controls header container style (background, shadow, etc.), `headerTintColor` uniformly sets all text and icon colors, `headerTitleStyle` specifically controls title text style. Priority: specific styles in `headerTitleStyle` > `headerTintColor` > default values.

  3. 什么时候应该隐藏头部（`headerShown: false`）？什么时候应该使用透明头部（`headerTransparent: true`）？| When should the header be hidden (`headerShown: false`)? When should transparent header be used (`headerTransparent: true`)?
     **答案 | Answer:** 隐藏头部用于需要完全自定义导航或全屏体验的场景（启动页、登录页、自定义导航栏）。透明头部用于内容需要延伸到头部下方但仍需显示标题和按钮的场景（图片背景、视频头部、沉浸式内容）| Hide header for scenes needing completely custom navigation or fullscreen experience (splash, login, custom nav bar). Transparent header for scenes where content needs to extend under header but still needs to show title and buttons (image backgrounds, video headers, immersive content).

  4. 如何正确地在头部按钮中实现导航和访问组件状态？| How to correctly implement navigation and access component state in header buttons?
     **答案 | Answer:** 在静态 `options` 函数中通过参数获取 `navigation` 对象。在组件内使用 `navigation.setOptions()` 时直接访问组件状态和导航对象。使用 `useCallback` 优化点击处理函数，使用 `useLayoutEffect` 避免闪烁 | In static `options` function, obtain `navigation` object through parameters. When using `navigation.setOptions()` in component, directly access component state and navigation object. Use `useCallback` to optimize click handlers, use `useLayoutEffect` to avoid flicker.

  5. 为什么使用 `useLayoutEffect` 而不是 `useEffect` 来设置头部选项？这对用户体验有什么影响？| Why use `useLayoutEffect` instead of `useEffect` for setting header options? What impact does this have on user experience?
     **答案 | Answer:** `useLayoutEffect` 在浏览器绘制前同步执行，`useEffect` 在绘制后异步执行。使用 `useLayoutEffect` 可以避免头部更新时的视觉闪烁，提供更流畅的用户体验。特别是在头部标题或按钮需要频繁更新时，差异明显 | `useLayoutEffect` executes synchronously before browser paint, `useEffect` executes asynchronously after paint. Using `useLayoutEffect` avoids visual flicker when updating header, providing smoother user experience. The difference is especially noticeable when header title or buttons need frequent updates.

## 实践项目：多功能笔记应用头部系统 | Practical Project: Multi-functional Note App Header System

### 目标 | Objective
创建一个笔记应用，综合应用本日所学的头部定制技术，包括静态和动态选项、样式自定义、头部按钮配置和条件性显示头部。| Create a note-taking app that comprehensively applies today's header customization techniques, including static and dynamic options, style customization, header button configuration, and conditional header display.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 你能区分什么时候使用静态选项配置和动态选项配置吗？| Can you distinguish when to use static vs dynamic options configuration?
   **答案 | Answer:** 固定不变的配置用静态（如应用主题色），需要响应状态的用动态（如基于保存状态的按钮）| Fixed configurations use static (like app theme colors), state-responsive use dynamic (like buttons based on save state).

2. 你知道如何在头部右侧同时显示多个按钮吗？| Do you know how to display multiple buttons on the header right side?
   **答案 | Answer:** 使用 `View` 组件配合 `flexDirection: 'row'` 包裹多个按钮组件 | Use `View` component with `flexDirection: 'row'` to wrap multiple button components.

3. 你了解如何创建自定义头部替代默认导航栏吗？| Do you understand how to create a custom header to replace the default navigation bar?
   **答案 | Answer:** 设置 `headerShown: false` 隐藏默认头部，然后在屏幕内使用 `SafeAreaView` 实现自定义头部 | Set `headerShown: false` to hide default header, then implement custom header in screen using `SafeAreaView`.

### 步骤 | Steps

1. **项目设置与导航结构 | Project Setup & Navigation Structure**
   - 创建 Stack Navigator 作为主导航器
   - 配置全局头部主题（颜色、字体、对齐）
   - 设置三个主要屏幕：笔记列表、笔记详情、笔记编辑

2. **笔记列表屏幕 - 静态头部配置 | Note List Screen - Static Header**
   - 使用静态选项设置固定标题"我的笔记 | My Notes"
   - 添加右侧"新建"按钮（使用图标）
   - 自定义头部样式（背景色、阴影）

3. **笔记详情屏幕 - 动态标题与多按钮 | Note Detail Screen - Dynamic Title & Multiple Buttons**
   - 根据传入的笔记数据动态设置标题
   - 添加右侧多个按钮：编辑、分享、删除
   - 实现按钮的交互逻辑（导航到编辑页、显示分享菜单、删除确认）

4. **笔记编辑屏幕 - 状态响应式头部 | Note Edit Screen - State-responsive Header**
   - 根据保存状态动态更新头部标题（"编辑中" vs "已保存"）
   - 根据表单验证状态启用/禁用保存按钮
   - 自定义返回按钮，未保存时显示确认对话框

5. **全屏预览模式 - 隐藏头部 | Fullscreen Preview - Hidden Header**
   - 创建笔记预览屏幕，初始隐藏头部
   - 点击屏幕切换头部显示/隐藏
   - 实现自定义关闭按钮

### 示例代码 | Example Code

```javascript
/**
 * 多功能笔记应用 | Multi-functional Note App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 静态与动态头部选项配置 | Static and dynamic header options configuration
 * - 头部样式自定义（颜色、字体、阴影）| Header style customization (colors, fonts, shadows)
 * - 头部按钮配置（单个、多个、图标）| Header button configuration (single, multiple, icons)
 * - 条件性显示/隐藏头部 | Conditional show/hide headers
 * - 基于状态的头部更新 | State-based header updates
 */

import React, { useState, useLayoutEffect, useCallback } from 'react';
import {
  View,
  Text,
  FlatList,
  TouchableOpacity,
  TextInput,
  Alert,
  StyleSheet,
  Platform,
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SafeAreaView } from 'react-native-safe-area-context';
import { Ionicons } from '@expo/vector-icons';

const Stack = createNativeStackNavigator();

// 模拟数据 | Mock data
const INITIAL_NOTES = [
  { id: '1', title: 'React Navigation 学习笔记', content: '今天学习了头部定制...', timestamp: Date.now() },
  { id: '2', title: '购物清单', content: '牛奶、面包、鸡蛋...', timestamp: Date.now() - 86400000 },
  { id: '3', title: '项目想法', content: '构建一个笔记应用...', timestamp: Date.now() - 172800000 },
];

// 全局主题配置 | Global theme configuration
const THEME = {
  primary: '#6200ee',
  primaryDark: '#3700b3',
  accent: '#03dac6',
  background: '#ffffff',
  surface: '#f5f5f5',
  error: '#b00020',
  text: '#000000',
  textSecondary: '#666666',
};

// 概念检查点1：共享头部样式配置 | Concept checkpoint 1: Shared header style configuration
const commonHeaderOptions = {
  headerStyle: {
    backgroundColor: THEME.primary,
    elevation: 4,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
  },
  headerTintColor: '#fff',
  headerTitleStyle: {
    fontWeight: 'bold',
    fontSize: Platform.OS === 'ios' ? 17 : 20,
  },
  headerTitleAlign: 'center',
  headerBackTitleVisible: false,
};

// 屏幕1：笔记列表 | Screen 1: Note List
function NoteListScreen({ navigation }) {
  const [notes, setNotes] = useState(INITIAL_NOTES);

  const renderNoteItem = ({ item }) => (
    <TouchableOpacity
      style={styles.noteItem}
      onPress={() => navigation.navigate('NoteDetail', { note: item })}
    >
      <Text style={styles.noteTitle}>{item.title}</Text>
      <Text style={styles.notePreview} numberOfLines={2}>
        {item.content}
      </Text>
      <Text style={styles.noteTimestamp}>
        {new Date(item.timestamp).toLocaleDateString()}
      </Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={notes}
        renderItem={renderNoteItem}
        keyExtractor={(item) => item.id}
        contentContainerStyle={styles.listContainer}
      />
    </View>
  );
}

// 屏幕2：笔记详情 | Screen 2: Note Detail
function NoteDetailScreen({ navigation, route }) {
  const { note } = route.params;

  // 概念检查点2：使用 useCallback 优化头部按钮处理函数 | Concept checkpoint 2: Optimize header button handlers with useCallback
  const handleEdit = useCallback(() => {
    navigation.navigate('NoteEdit', { note });
  }, [navigation, note]);

  const handleShare = useCallback(() => {
    Alert.alert('分享 | Share', `分享笔记: ${note.title}`);
  }, [note]);

  const handleDelete = useCallback(() => {
    Alert.alert(
      '确认删除 | Confirm Delete',
      '确定要删除这条笔记吗？| Are you sure you want to delete this note?',
      [
        { text: '取消 | Cancel', style: 'cancel' },
        {
          text: '删除 | Delete',
          style: 'destructive',
          onPress: () => {
            // 删除逻辑... | Delete logic...
            navigation.goBack();
          },
        },
      ]
    );
  }, [navigation]);

  const handlePreview = useCallback(() => {
    navigation.navigate('NotePreview', { note });
  }, [navigation, note]);

  // 概念检查点3：动态设置标题和多个头部按钮 | Concept checkpoint 3: Dynamically set title and multiple header buttons
  useLayoutEffect(() => {
    navigation.setOptions({
      title: note.title,  // 动态标题 | Dynamic title
      headerRight: () => (
        <View style={styles.headerButtonGroup}>
          <TouchableOpacity onPress={handlePreview} style={styles.headerButton}>
            <Ionicons name="eye-outline" size={24} color="#fff" />
          </TouchableOpacity>
          <TouchableOpacity onPress={handleShare} style={styles.headerButton}>
            <Ionicons name="share-outline" size={24} color="#fff" />
          </TouchableOpacity>
          <TouchableOpacity onPress={handleEdit} style={styles.headerButton}>
            <Ionicons name="create-outline" size={24} color="#fff" />
          </TouchableOpacity>
          <TouchableOpacity onPress={handleDelete} style={styles.headerButton}>
            <Ionicons name="trash-outline" size={24} color="#fff" />
          </TouchableOpacity>
        </View>
      ),
    });
  }, [navigation, note, handleEdit, handleShare, handleDelete, handlePreview]);

  return (
    <View style={styles.container}>
      <View style={styles.detailContainer}>
        <Text style={styles.detailTitle}>{note.title}</Text>
        <Text style={styles.detailTimestamp}>
          {new Date(note.timestamp).toLocaleString()}
        </Text>
        <Text style={styles.detailContent}>{note.content}</Text>
      </View>
    </View>
  );
}

// 屏幕3：笔记编辑 | Screen 3: Note Edit
function NoteEditScreen({ navigation, route }) {
  const { note } = route.params || {};
  const [title, setTitle] = useState(note?.title || '');
  const [content, setContent] = useState(note?.content || '');
  const [isSaved, setIsSaved] = useState(true);

  // 表单验证 | Form validation
  const isValid = title.trim().length > 0 && content.trim().length > 0;

  // 监听内容变化 | Monitor content changes
  React.useEffect(() => {
    setIsSaved(false);
  }, [title, content]);

  // 概念检查点4：保存处理函数 | Concept checkpoint 4: Save handler
  const handleSave = useCallback(() => {
    if (!isValid) {
      Alert.alert('验证错误 | Validation Error', '标题和内容不能为空 | Title and content cannot be empty');
      return;
    }

    // 保存逻辑... | Save logic...
    console.log('Saving note:', { title, content });
    setIsSaved(true);
    Alert.alert('成功 | Success', '笔记已保存 | Note saved');
  }, [title, content, isValid]);

  // 概念检查点5：自定义返回按钮处理未保存更改 | Concept checkpoint 5: Custom back button handling unsaved changes
  const handleBack = useCallback(() => {
    if (!isSaved) {
      Alert.alert(
        '未保存的更改 | Unsaved Changes',
        '你有未保存的更改，确定要离开吗？| You have unsaved changes. Are you sure you want to leave?',
        [
          { text: '取消 | Cancel', style: 'cancel' },
          { text: '放弃 | Discard', style: 'destructive', onPress: () => navigation.goBack() },
          { text: '保存 | Save', onPress: () => { handleSave(); navigation.goBack(); } },
        ]
      );
    } else {
      navigation.goBack();
    }
  }, [isSaved, navigation, handleSave]);

  // 概念检查点6：基于状态动态更新头部 | Concept checkpoint 6: Dynamically update header based on state
  useLayoutEffect(() => {
    navigation.setOptions({
      title: isSaved ? '已保存 | Saved' : '编辑中 | Editing',
      headerStyle: {
        backgroundColor: isSaved ? THEME.primary : THEME.primaryDark,
        ...commonHeaderOptions.headerStyle,
      },
      headerRight: () => (
        <TouchableOpacity
          onPress={handleSave}
          disabled={!isValid || isSaved}
          style={styles.headerButton}
        >
          <Ionicons
            name={isSaved ? "checkmark-circle" : "save-outline"}
            size={24}
            color={isValid && !isSaved ? "#fff" : "#aaa"}
          />
        </TouchableOpacity>
      ),
      headerLeft: () => (
        <TouchableOpacity onPress={handleBack} style={styles.headerButton}>
          <Ionicons name="arrow-back" size={24} color="#fff" />
        </TouchableOpacity>
      ),
    });
  }, [navigation, isSaved, isValid, handleSave, handleBack]);

  return (
    <View style={styles.container}>
      <View style={styles.editContainer}>
        <TextInput
          style={styles.titleInput}
          placeholder="标题 | Title"
          value={title}
          onChangeText={setTitle}
        />
        <TextInput
          style={styles.contentInput}
          placeholder="内容 | Content"
          value={content}
          onChangeText={setContent}
          multiline
          textAlignVertical="top"
        />
        <Text style={styles.validationText}>
          状态 | Status: {isSaved ? '已保存 | Saved' : '未保存 | Unsaved'} |{' '}
          有效 | Valid: {isValid ? '是 | Yes' : '否 | No'}
        </Text>
      </View>
    </View>
  );
}

// 屏幕4：全屏预览 | Screen 4: Fullscreen Preview
function NotePreviewScreen({ navigation, route }) {
  const { note } = route.params;
  const [showHeader, setShowHeader] = useState(true);

  // 概念检查点7：条件性显示/隐藏头部 | Concept checkpoint 7: Conditional show/hide header
  useLayoutEffect(() => {
    navigation.setOptions({
      headerShown: showHeader,
      headerTransparent: true,
      headerStyle: {
        backgroundColor: showHeader ? 'rgba(98, 0, 238, 0.8)' : 'transparent',
      },
    });
  }, [navigation, showHeader]);

  return (
    <TouchableOpacity
      style={styles.previewContainer}
      activeOpacity={1}
      onPress={() => setShowHeader(!showHeader)}
    >
      <SafeAreaView style={styles.previewContent} edges={['bottom']}>
        <Text style={styles.previewTitle}>{note.title}</Text>
        <Text style={styles.previewText}>{note.content}</Text>
        <Text style={styles.previewHint}>
          点击屏幕切换头部 | Tap to toggle header
        </Text>
      </SafeAreaView>

      {!showHeader && (
        <SafeAreaView style={styles.customCloseButton} edges={['top', 'left']}>
          <TouchableOpacity onPress={() => navigation.goBack()}>
            <Ionicons name="close-circle" size={40} color="#fff" />
          </TouchableOpacity>
        </SafeAreaView>
      )}
    </TouchableOpacity>
  );
}

// 主应用导航 | Main App Navigation
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        // 概念检查点8：使用 screenOptions 设置全局默认配置 | Concept checkpoint 8: Use screenOptions for global defaults
        screenOptions={commonHeaderOptions}
      >
        {/* 笔记列表 - 静态头部配置 | Note List - Static header configuration */}
        <Stack.Screen
          name="NoteList"
          component={NoteListScreen}
          options={({ navigation }) => ({
            title: '我的笔记 | My Notes',
            headerRight: () => (
              <TouchableOpacity
                onPress={() => navigation.navigate('NoteEdit')}
                style={styles.headerButton}
              >
                <Ionicons name="add-circle-outline" size={28} color="#fff" />
              </TouchableOpacity>
            ),
          })}
        />

        {/* 笔记详情 - 动态头部配置 | Note Detail - Dynamic header configuration */}
        <Stack.Screen
          name="NoteDetail"
          component={NoteDetailScreen}
          options={{ title: '详情 | Detail' }}  // 初始标题，会被动态更新 | Initial title, will be dynamically updated
        />

        {/* 笔记编辑 - 状态响应式头部 | Note Edit - State-responsive header */}
        <Stack.Screen
          name="NoteEdit"
          component={NoteEditScreen}
          options={{ title: '编辑笔记 | Edit Note' }}  // 初始标题 | Initial title
        />

        {/* 全屏预览 - 条件性头部 | Fullscreen Preview - Conditional header */}
        <Stack.Screen
          name="NotePreview"
          component={NotePreviewScreen}
          options={{
            title: '预览 | Preview',
            animation: 'fade',
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: THEME.background,
  },
  listContainer: {
    padding: 16,
  },
  noteItem: {
    backgroundColor: THEME.surface,
    padding: 16,
    marginBottom: 12,
    borderRadius: 8,
    elevation: 2,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.2,
    shadowRadius: 1.41,
  },
  noteTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: THEME.text,
    marginBottom: 8,
  },
  notePreview: {
    fontSize: 14,
    color: THEME.textSecondary,
    marginBottom: 8,
  },
  noteTimestamp: {
    fontSize: 12,
    color: THEME.textSecondary,
  },
  headerButtonGroup: {
    flexDirection: 'row',
    marginRight: 8,
  },
  headerButton: {
    padding: 8,
    marginHorizontal: 4,
  },
  detailContainer: {
    padding: 20,
  },
  detailTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    color: THEME.text,
    marginBottom: 8,
  },
  detailTimestamp: {
    fontSize: 12,
    color: THEME.textSecondary,
    marginBottom: 16,
  },
  detailContent: {
    fontSize: 16,
    color: THEME.text,
    lineHeight: 24,
  },
  editContainer: {
    flex: 1,
    padding: 16,
  },
  titleInput: {
    fontSize: 20,
    fontWeight: 'bold',
    borderBottomWidth: 1,
    borderBottomColor: THEME.textSecondary,
    paddingVertical: 12,
    marginBottom: 16,
  },
  contentInput: {
    flex: 1,
    fontSize: 16,
    lineHeight: 24,
    padding: 12,
    backgroundColor: THEME.surface,
    borderRadius: 8,
  },
  validationText: {
    marginTop: 16,
    fontSize: 14,
    color: THEME.textSecondary,
  },
  previewContainer: {
    flex: 1,
    backgroundColor: THEME.primary,
  },
  previewContent: {
    flex: 1,
    padding: 20,
    justifyContent: 'center',
  },
  previewTitle: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#fff',
    marginBottom: 20,
    textAlign: 'center',
  },
  previewText: {
    fontSize: 18,
    color: '#fff',
    lineHeight: 28,
    textAlign: 'center',
  },
  previewHint: {
    marginTop: 40,
    fontSize: 14,
    color: 'rgba(255, 255, 255, 0.7)',
    textAlign: 'center',
    fontStyle: 'italic',
  },
  customCloseButton: {
    position: 'absolute',
    top: 0,
    right: 0,
    padding: 16,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确应用了静态和动态选项配置？| Does the project correctly apply static and dynamic options configuration?
   - ✅ 笔记列表使用静态选项
   - ✅ 笔记详情和编辑使用动态选项响应状态

2. 头部样式自定义是否符合最佳实践？| Does header style customization follow best practices?
   - ✅ 全局主题配置在 `commonHeaderOptions`
   - ✅ 使用平台检查适配 iOS 和 Android

3. 代码是否体现了概念间的正确关系？| Does the code reflect correct relationships between concepts?
   - ✅ `useLayoutEffect` 用于头部更新避免闪烁
   - ✅ `useCallback` 优化按钮处理函数
   - ✅ 正确处理未保存状态的导航确认

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **静态vs动态选项理解强化 | Static vs Dynamic Options Understanding Reinforcement**
   - **练习描述 | Exercise Description:** 创建一个应用，其中某些屏幕使用静态选项，某些使用动态选项。记录并比较两种方式在性能和代码复杂度上的差异
   - **概念检查 | Concept Check:** 你能解释为什么静态选项性能更好吗？什么情况下性能差异明显？
   - **学习目标 | Learning Objective:** 深入理解静态和动态配置的性能影响和使用场景

2. **头部样式系统设计 | Header Style System Design**
   - **练习描述 | Exercise Description:** 设计一个主题系统，支持亮色/暗色主题切换，所有头部样式根据主题自动更新
   - **概念检查 | Concept Check:** 如何在不重复代码的情况下为多个屏幕应用主题？
   - **学习目标 | Learning Objective:** 掌握可维护的头部样式架构设计

3. **复杂头部按钮交互 | Complex Header Button Interactions**
   - **练习描述 | Exercise Description:** 实现一个购物车应用，头部显示购物车图标和商品数量徽章，点击跳转到购物车页面
   - **概念检查 | Concept Check:** 如何在头部按钮上显示动态数量？如何优化避免不必要的重新渲染？
   - **学习目标 | Learning Objective:** 提高头部按钮的高级定制能力

4. **自定义导航栏实现 | Custom Navigation Bar Implementation**
   - **练习描述 | Exercise Description:** 隐藏默认头部，实现一个完全自定义的导航栏，包括渐变背景、搜索框、多个操作按钮
   - **概念检查 | Concept Check:** 隐藏头部后需要注意哪些布局问题？如何处理安全区域？
   - **学习目标 | Learning Objective:** 掌握完全自定义导航体验的实现

5. **头部动画效果 | Header Animation Effects**
   - **练习描述 | Exercise Description:** 实现滚动时头部收缩/展开的效果（类似 iOS Safari）
   - **概念检查 | Concept Check:** 如何结合 `Animated` API 和头部配置？`headerTransparent` 在这个场景中如何使用？
   - **学习目标 | Learning Objective:** 学习头部与动画的结合应用

6. **跨平台头部适配 | Cross-platform Header Adaptation**
   - **练习描述 | Exercise Description:** 创建一个应用，头部在 iOS 和 Android 上遵循各自平台规范（HIG vs Material Design）
   - **概念检查 | Concept Check:** iOS 和 Android 头部设计有哪些主要区别？如何用代码实现？
   - **学习目标 | Learning Objective:** 掌握平台特定的头部设计规范

7. **头部状态持久化 | Header State Persistence**
   - **练习描述 | Exercise Description:** 实现一个阅读应用，用户可以在头部选择字体大小，该设置在应用重启后仍然保留
   - **概念检查 | Concept Check:** 如何结合 AsyncStorage 和头部配置？何时加载和保存设置？
   - **学习目标 | Learning Objective:** 学习头部状态与数据持久化的集成

## 学习资源 | Learning Resources
- [React Navigation - Header Configuration](https://reactnavigation.org/docs/headers)
- [React Navigation - Header Buttons](https://reactnavigation.org/docs/header-buttons)
- [React Navigation - Options Resolution](https://reactnavigation.org/docs/screen-options-resolution)
- [React Native - Platform Specific Code](https://reactnative.dev/docs/platform-specific-code)
- [React - useLayoutEffect Hook](https://react.dev/reference/react/useLayoutEffect)
- [React - useCallback Hook](https://react.dev/reference/react/useCallback)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解静态选项配置和动态选项配置的区别和使用场景 | Understand difference between static and dynamic options and their use cases
- [ ] 掌握 `options` 属性（对象和函数形式）的使用 | Master using `options` prop (object and function forms)
- [ ] 能够使用 `navigation.setOptions()` 动态更新头部 | Can dynamically update header using `navigation.setOptions()`
- [ ] 熟练自定义头部样式（背景色、文字颜色、字体、阴影）| Proficient in customizing header styles (background, text color, fonts, shadows)
- [ ] 掌握 `headerLeft` 和 `headerRight` 配置头部按钮 | Master configuring header buttons with `headerLeft` and `headerRight`
- [ ] 能够实现头部多个按钮和图标按钮 | Can implement multiple buttons and icon buttons in header
- [ ] 理解何时使用 `headerShown: false` 隐藏头部 | Understand when to use `headerShown: false` to hide header
- [ ] 能够创建自定义头部替代默认导航栏 | Can create custom headers to replace default navigation bar
- [ ] 掌握使用 `useLayoutEffect` 避免头部更新闪烁 | Master using `useLayoutEffect` to avoid header update flicker
- [ ] 能够使用 `useCallback` 和 `useMemo` 优化头部性能 | Can optimize header performance with `useCallback` and `useMemo`
- [ ] 理解并应用头部配置最佳实践 | Understand and apply header configuration best practices
- [ ] 完成实践项目：多功能笔记应用 | Complete practical project: Multi-functional Note App
- [ ] 所有 CCQs 能够正确回答 | All CCQs answered correctly
- [ ] 代码示例理解并能够运行 | Code examples understood and can execute
- [ ] 至少完成3个扩展练习 | At least 3 extension exercises completed

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
1. 清晰解释静态选项和动态选项的区别及使用时机
2. 独立实现包含多个自定义按钮的头部
3. 根据组件状态动态更新头部标题和样式
4. 创建自定义头部替代默认导航栏
5. 向他人解释 `useLayoutEffect` vs `useEffect` 在头部配置中的重要性

Before marking as complete, ensure you can:
1. Clearly explain the difference between static and dynamic options and when to use each
2. Independently implement a header with multiple custom buttons
3. Dynamically update header title and styles based on component state
4. Create custom headers to replace default navigation bar
5. Explain to others the importance of `useLayoutEffect` vs `useEffect` in header configuration
