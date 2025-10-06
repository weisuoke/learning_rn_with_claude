# React Native入门 - 第5天：Part 2 - 深度链接与程序化控制 | React Native Introduction - Day 5: Part 2 - Deep Linking & Programmatic Control

### 4. 程序化控制Drawer | Programmatic Drawer Control (1小时 | 1 hour)

- **Drawer导航方法 | Drawer Navigation Methods**

  **概念定义 | Concept Definition:**
  除了手势滑动，可以通过navigation对象提供的方法程序化控制Drawer的打开、关闭和切换状态。这对于响应用户操作（如点击菜单按钮）或特定业务逻辑非常有用。 | Besides gesture swipes, you can programmatically control Drawer opening, closing, and toggling through methods provided by the navigation object. This is very useful for responding to user actions (like clicking menu buttons) or specific business logic.

  **核心方法 | Key Methods:**
  - navigation.openDrawer()：打开Drawer | navigation.openDrawer(): Open Drawer
  - navigation.closeDrawer()：关闭Drawer | navigation.closeDrawer(): Close Drawer
  - navigation.toggleDrawer()：切换Drawer状态 | navigation.toggleDrawer(): Toggle Drawer state
  - navigation.jumpTo('ScreenName')：跳转到指定Drawer屏幕 | navigation.jumpTo('ScreenName'): Jump to specific Drawer screen

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在任何屏幕都能调用openDrawer()吗？| Can you call openDrawer() from any screen?
     **答案 | Answer:** 是 | Yes - 只要该屏幕在Drawer导航器的层级内 | As long as the screen is within Drawer Navigator's hierarchy
  2. toggleDrawer()在Drawer已打开时会做什么？| What does toggleDrawer() do when Drawer is already open?
     **答案 | Answer:** 关闭Drawer | Closes Drawer - toggle方法会切换当前状态 | toggle method switches current state
  3. jumpTo和navigate方法有什么区别？| What's the difference between jumpTo and navigate?
     **答案 | Answer:** jumpTo用于Drawer/Tab间跳转不增加历史，navigate会增加Stack历史 | jumpTo for Drawer/Tab switching without adding history, navigate adds Stack history
  4. 可以在Drawer外的屏幕（如Stack中的子屏幕）打开Drawer吗？| Can you open Drawer from screens outside Drawer (like child screens in Stack)?
     **答案 | Answer:** 可以 | Yes - navigation对象会向上查找父级Drawer导航器 | navigation object searches up for parent Drawer Navigator

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 程序化控制Drawer示例 | Programmatic Drawer control example
  import { View, Text, Button } from 'react-native';
  import { createDrawerNavigator } from '@react-navigation/drawer';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Drawer = createDrawerNavigator();
  const Stack = createNativeStackNavigator();

  // Home屏幕 - 演示Drawer控制方法 | Home screen - demonstrates Drawer control methods
  function HomeScreen({ navigation }) {
    return (
      <View style={{ flex: 1, padding: 20 }}>
        <Text style={{ fontSize: 20, marginBottom: 20 }}>
          Drawer控制示例 | Drawer Control Example
        </Text>

        {/* 打开Drawer | Open Drawer */}
        <Button
          title="打开菜单 | Open Menu"
          onPress={() => navigation.openDrawer()}
        />

        {/* 切换Drawer状态 | Toggle Drawer state */}
        <Button
          title="切换菜单 | Toggle Menu"
          onPress={() => navigation.toggleDrawer()}
        />

        {/* 直接跳转到Profile屏幕 | Jump directly to Profile screen */}
        <Button
          title="跳转到个人资料 | Jump to Profile"
          onPress={() => navigation.jumpTo('Profile')}
        />

        {/* 导航到Stack中的详情页 | Navigate to detail page in Stack */}
        <Button
          title="查看详情 | View Details"
          onPress={() => navigation.navigate('Details')}
        />
      </View>
    );
  }

  // 详情屏幕（Stack中的子屏幕）| Details screen (child screen in Stack)
  function DetailsScreen({ navigation }) {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>详情页 | Details Page</Text>
        {/* 在Stack子屏幕中也能控制Drawer | Can control Drawer in Stack child screen */}
        <Button
          title="打开侧边栏 | Open Drawer"
          onPress={() => navigation.openDrawer()}
        />
        <Button
          title="返回 | Go Back"
          onPress={() => navigation.goBack()}
        />
      </View>
    );
  }

  // Profile屏幕 | Profile screen
  function ProfileScreen({ navigation }) {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>个人资料 | Profile</Text>
        <Button
          title="关闭菜单 | Close Menu"
          onPress={() => navigation.closeDrawer()}
        />
      </View>
    );
  }

  // Home Stack导航器 | Home Stack Navigator
  function HomeStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="HomeMain"
          component={HomeScreen}
          options={({ navigation }) => ({
            title: '主页 | Home',
            headerLeft: () => (
              <Button
                title="☰"
                onPress={() => navigation.openDrawer()}
              />
            ),
          })}
        />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    );
  }

  // App根组件 | App root component
  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator>
          <Drawer.Screen name="Home" component={HomeStack} />
          <Drawer.Screen name="Profile" component={ProfileScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 从DetailsScreen调用jumpTo('Profile')会发生什么？| What happens when calling jumpTo('Profile') from DetailsScreen?
    **答案 | Answer:** 跳转到Profile屏幕且关闭Drawer | Jumps to Profile screen and closes Drawer
  - 在Header中添加打开Drawer的按钮有什么好处？| What's the benefit of adding an open Drawer button in Header?
    **答案 | Answer:** 提高可发现性，不是所有用户都知道滑动手势 | Improves discoverability, not all users know about swipe gestures

  **常见误区检查 | Common Misconception Checks:**
  - toggleDrawer()可以接受参数指定打开或关闭吗？| Can toggleDrawer() accept parameters to specify open or close?
    **答案 | Answer:** 不能 | No - toggleDrawer只切换状态，需明确控制用openDrawer或closeDrawer | toggleDrawer only toggles state, use openDrawer or closeDrawer for explicit control
  - 连续调用多次openDrawer()会发生什么？| What happens when calling openDrawer() multiple times consecutively?
    **答案 | Answer:** 没有副作用 | No side effects - Drawer已经打开时调用openDrawer不会有额外效果 | Calling openDrawer when Drawer is already open has no extra effect

- **Drawer状态监听 | Drawer State Listening**

  **概念定义 | Concept Definition:**
  可以通过navigation的事件监听器来响应Drawer的状态变化，如打开、关闭等事件。这对于需要在Drawer状态改变时执行特定逻辑（如数据刷新、埋点统计）的场景很有用。 | You can use navigation event listeners to respond to Drawer state changes, such as open and close events. This is useful for scenarios that need to execute specific logic when Drawer state changes (like data refresh, analytics tracking).

  **核心事件 | Key Events:**
  - drawerOpen：Drawer打开时触发 | drawerOpen: Triggered when Drawer opens
  - drawerClose：Drawer关闭时触发 | drawerClose: Triggered when Drawer closes
  - state：导航状态变化时触发 | state: Triggered when navigation state changes

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 监听事件需要在组件卸载时清理吗？| Do event listeners need to be cleaned up when component unmounts?
     **答案 | Answer:** 是 | Yes - useEffect返回清理函数或使用addListener返回的unsubscribe | Return cleanup function from useEffect or use unsubscribe returned by addListener
  2. drawerOpen事件会在每次Drawer打开时都触发吗？| Will drawerOpen event trigger every time Drawer opens?
     **答案 | Answer:** 是 | Yes - 无论是手势还是程序控制打开都会触发 | Triggers whether opened by gesture or programmatic control
  3. 可以阻止Drawer打开吗？| Can you prevent Drawer from opening?
     **答案 | Answer:** 不能直接阻止 | Cannot directly prevent - 但可以在打开后立即关闭 | But can close immediately after opening
  4. 事件监听器在哪里注册最合适？| Where is the best place to register event listeners?
     **答案 | Answer:** useEffect中或componentDidMount | In useEffect or componentDidMount

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // Drawer状态监听示例 | Drawer state listening example
  import { useEffect } from 'react';
  import { View, Text } from 'react-native';

  function HomeScreen({ navigation }) {
    useEffect(() => {
      // 监听Drawer打开事件 | Listen to Drawer open event
      const unsubscribeOpen = navigation.addListener('drawerOpen', (e) => {
        console.log('Drawer已打开 | Drawer opened');
        // 执行打开时的逻辑，如埋点统计 | Execute logic when opened, like analytics
      });

      // 监听Drawer关闭事件 | Listen to Drawer close event
      const unsubscribeClose = navigation.addListener('drawerClose', (e) => {
        console.log('Drawer已关闭 | Drawer closed');
        // 执行关闭时的逻辑 | Execute logic when closed
      });

      // 监听导航状态变化 | Listen to navigation state changes
      const unsubscribeState = navigation.addListener('state', (e) => {
        console.log('导航状态变化 | Navigation state changed:', e.data.state);
      });

      // 清理监听器 | Cleanup listeners
      return () => {
        unsubscribeOpen();
        unsubscribeClose();
        unsubscribeState();
      };
    }, [navigation]);

    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>查看控制台日志了解Drawer状态变化 | Check console for Drawer state changes</Text>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么useEffect的依赖数组中要包含navigation？| Why include navigation in useEffect dependency array?
    **答案 | Answer:** 确保navigation对象变化时重新注册监听器 | Ensures listeners are re-registered when navigation object changes
  - 忘记清理监听器会导致什么问题？| What problem occurs if you forget to cleanup listeners?
    **答案 | Answer:** 内存泄漏，组件卸载后监听器仍然存在 | Memory leak, listeners persist after component unmounts

### 5. 深度链接基础 | Deep Linking Basics (1小时 | 1 hour)

- **深度链接概念 | Deep Linking Concepts**

  **概念定义 | Concept Definition:**
  深度链接（Deep Linking）允许用户通过URL直接打开应用的特定页面，而不是总是从首页开始。这对于通知跳转、分享链接、广告投放等场景至关重要。React Navigation通过linking配置实现深度链接功能。 | Deep Linking allows users to open specific pages in the app directly through URLs, rather than always starting from the home page. This is crucial for scenarios like notification jumps, link sharing, and ad campaigns. React Navigation implements deep linking through linking configuration.

  **核心概念 | Key Concepts:**
  - URL Scheme：应用的唯一标识符（如myapp://）| URL Scheme: unique identifier for the app (like myapp://)
  - Path Mapping：URL路径与屏幕的映射关系 | Path Mapping: mapping relationship between URL paths and screens
  - Universal Links：iOS上的通用链接（https://域名）| Universal Links: universal links on iOS (https://domain)
  - App Links：Android上的应用链接 | App Links: app links on Android

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 深度链接和普通的应用内导航有什么区别？| What's the difference between deep linking and regular in-app navigation?
     **答案 | Answer:** 深度链接从外部URL启动，可以在应用未运行时打开特定页面 | Deep linking launches from external URLs, can open specific pages when app isn't running
  2. URL Scheme必须是唯一的吗？| Must URL Scheme be unique?
     **答案 | Answer:** 是 | Yes - 避免与其他应用冲突 | To avoid conflicts with other apps
  3. 深度链接需要应用已安装吗？| Does deep linking require the app to be installed?
     **答案 | Answer:** 是 | Yes - 如果未安装，通常会跳转到应用商店 | If not installed, usually redirects to app store
  4. myapp://home/details/123中，123是什么？| In myapp://home/details/123, what is 123?
     **答案 | Answer:** URL参数，可以作为导航参数传递给Details屏幕 | URL parameter, can be passed as navigation param to Details screen

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 深度链接配置示例 | Deep linking configuration example
  import { NavigationContainer } from '@react-navigation/native';
  import { createDrawerNavigator } from '@react-navigation/drawer';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Drawer = createDrawerNavigator();
  const Stack = createNativeStackNavigator();

  // 深度链接配置 | Deep linking configuration
  const linking = {
    // 应用的URL Scheme | App's URL Scheme
    prefixes: ['myapp://', 'https://myapp.com'],

    // 路径映射配置 | Path mapping configuration
    config: {
      screens: {
        // Drawer导航器的屏幕 | Drawer Navigator screens
        Home: {
          screens: {
            // Stack导航器中的屏幕 | Screens in Stack Navigator
            HomeMain: 'home',
            Details: {
              path: 'details/:id', // 带参数的路径 | Path with parameter
              parse: {
                id: (id) => `${id}`, // 参数解析 | Parameter parsing
              },
            },
          },
        },
        Profile: 'profile',
        Settings: 'settings',
      },
    },
  };

  // Home Stack导航器 | Home Stack Navigator
  function HomeStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="HomeMain" component={HomeScreen} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    );
  }

  // Details屏幕 - 接收深度链接参数 | Details screen - receives deep link params
  function DetailsScreen({ route }) {
    const { id } = route.params || {};
    return (
      <View style={{ flex: 1, padding: 20 }}>
        <Text style={{ fontSize: 18 }}>
          详情页 ID: {id} | Details Page ID: {id}
        </Text>
        <Text style={{ marginTop: 10 }}>
          此页面可以通过以下链接访问 | This page can be accessed via:
        </Text>
        <Text style={{ fontFamily: 'monospace', marginTop: 5 }}>
          myapp://home/details/{id}
        </Text>
        <Text style={{ fontFamily: 'monospace', marginTop: 5 }}>
          https://myapp.com/home/details/{id}
        </Text>
      </View>
    );
  }

  function ProfileScreen() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>个人资料 | Profile</Text>
        <Text>链接: myapp://profile</Text>
      </View>
    );
  }

  function SettingsScreen() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>设置 | Settings</Text>
        <Text>链接: myapp://settings</Text>
      </View>
    );
  }

  // App组件 - 应用深度链接配置 | App component - apply deep linking config
  function App() {
    return (
      <NavigationContainer linking={linking}>
        <Drawer.Navigator>
          <Drawer.Screen name="Home" component={HomeStack} />
          <Drawer.Screen name="Profile" component={ProfileScreen} />
          <Drawer.Screen name="Settings" component={SettingsScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }

  export default App;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 访问myapp://home/details/42会导航到哪个屏幕并传递什么参数？| Accessing myapp://home/details/42 will navigate to which screen and pass what parameter?
    **答案 | Answer:** Details屏幕，参数id为"42" | Details screen with parameter id as "42"
  - prefixes数组中可以有多个URL吗？| Can the prefixes array have multiple URLs?
    **答案 | Answer:** 可以 | Yes - 支持多个scheme和domain | Supports multiple schemes and domains

  **常见误区检查 | Common Misconception Checks:**
  - 深度链接配置后需要重新编译应用吗？| Does deep linking configuration require app recompilation?
    **答案 | Answer:** linking配置本身不需要，但URL Scheme需要在原生配置中设置 | linking config itself doesn't, but URL Scheme needs to be set in native configuration
  - 在开发环境可以直接测试深度链接吗？| Can you test deep links directly in development environment?
    **答案 | Answer:** 可以部分测试 | Partially - 需要使用特定命令或工具（如Expo的expo-linking）| Need to use specific commands or tools (like Expo's expo-linking)

- **测试深度链接 | Testing Deep Links**

  **概念定义 | Concept Definition:**
  在开发过程中，可以通过多种方式测试深度链接功能，包括使用命令行工具（adb/xcrun）、Expo的Linking API、或React Navigation的开发工具。测试能确保URL正确映射到屏幕和参数。 | During development, you can test deep linking through various methods, including command-line tools (adb/xcrun), Expo's Linking API, or React Navigation dev tools. Testing ensures URLs correctly map to screens and parameters.

  **测试方法 | Testing Methods:**
  - iOS模拟器：使用xcrun命令 | iOS Simulator: use xcrun command
  - Android模拟器：使用adb命令 | Android Emulator: use adb command
  - Expo项目：使用Linking.openURL() | Expo projects: use Linking.openURL()
  - 浏览器测试：使用React Navigation devtools | Browser testing: use React Navigation devtools

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在真机上测试深度链接需要应用发布到商店吗？| Does testing deep links on real devices require publishing to app store?
     **答案 | Answer:** 不需要 | No - 可以通过开发版本或TestFlight/内测渠道测试 | Can test through dev builds or TestFlight/internal testing
  2. 深度链接测试失败最常见的原因是什么？| What's the most common reason for deep link testing failures?
     **答案 | Answer:** URL Scheme未在原生配置中正确设置 | URL Scheme not correctly set in native configuration
  3. Expo项目和纯RN项目测试深度链接有区别吗？| Is there a difference in testing deep links between Expo and pure RN projects?
     **答案 | Answer:** 有 | Yes - Expo提供了expo-linking包和更简便的测试方法 | Expo provides expo-linking package and more convenient testing methods
  4. 可以在代码中手动触发深度链接吗？| Can you manually trigger deep links in code?
     **答案 | Answer:** 可以 | Yes - 使用Linking.openURL()模拟深度链接 | Use Linking.openURL() to simulate deep links

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 测试深度链接的工具组件 | Deep link testing utility component
  import { View, Text, Button, Alert } from 'react-native';
  import * as Linking from 'expo-linking'; // Expo项目 | For Expo projects
  // 或者 | Or
  // import { Linking } from 'react-native'; // 纯RN项目 | For pure RN projects

  function DeepLinkTester() {
    // 测试不同的深度链接 | Test different deep links
    const testLinks = [
      'myapp://home',
      'myapp://profile',
      'myapp://settings',
      'myapp://home/details/123',
      'myapp://home/details/456',
    ];

    const handleTestLink = async (url) => {
      try {
        // 检查URL是否可以打开 | Check if URL can be opened
        const supported = await Linking.canOpenURL(url);

        if (supported) {
          // 打开深度链接 | Open deep link
          await Linking.openURL(url);
        } else {
          Alert.alert('错误 | Error', `无法打开链接 | Cannot open URL: ${url}`);
        }
      } catch (error) {
        Alert.alert('错误 | Error', error.message);
      }
    };

    // 获取初始URL（应用通过深度链接打开时）| Get initial URL (when app opens via deep link)
    const getInitialURL = async () => {
      const initialUrl = await Linking.getInitialURL();
      if (initialUrl) {
        Alert.alert('初始URL | Initial URL', initialUrl);
      } else {
        Alert.alert('提示 | Info', '应用不是通过深度链接打开的 | App was not opened via deep link');
      }
    };

    return (
      <View style={{ flex: 1, padding: 20 }}>
        <Text style={{ fontSize: 18, fontWeight: 'bold', marginBottom: 20 }}>
          深度链接测试 | Deep Link Testing
        </Text>

        {/* 显示初始URL | Show initial URL */}
        <Button title="获取初始URL | Get Initial URL" onPress={getInitialURL} />

        <Text style={{ marginTop: 20, marginBottom: 10, fontWeight: 'bold' }}>
          测试链接 | Test Links:
        </Text>

        {/* 测试各个链接 | Test each link */}
        {testLinks.map((url, index) => (
          <Button
            key={index}
            title={url}
            onPress={() => handleTestLink(url)}
            style={{ marginBottom: 10 }}
          />
        ))}

        <Text style={{ marginTop: 20, fontSize: 12, color: '#666' }}>
          注意：在模拟器中测试需要使用命令行工具 | Note: Testing in simulator requires command-line tools
        </Text>
      </View>
    );
  }
  ```

  ```bash
  # iOS模拟器测试命令 | iOS Simulator test commands
  # 测试基本链接 | Test basic link
  xcrun simctl openurl booted myapp://profile

  # 测试带参数的链接 | Test link with parameters
  xcrun simctl openurl booted myapp://home/details/123

  # Android模拟器测试命令 | Android Emulator test commands
  # 测试基本链接 | Test basic link
  adb shell am start -W -a android.intent.action.VIEW -d "myapp://profile"

  # 测试带参数的链接 | Test link with parameters
  adb shell am start -W -a android.intent.action.VIEW -d "myapp://home/details/123"
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Linking.getInitialURL()什么时候返回null？| When does Linking.getInitialURL() return null?
    **答案 | Answer:** 应用不是通过深度链接打开时 | When app is not opened via deep link
  - 为什么要使用canOpenURL()检查？| Why check with canOpenURL()?
    **答案 | Answer:** 确保设备支持该URL scheme，避免崩溃 | Ensures device supports the URL scheme, avoids crashes

  **常见误区检查 | Common Misconception Checks:**
  - 在开发环境点击链接就能自动打开应用吗？| Will clicking a link in development automatically open the app?
    **答案 | Answer:** 不一定 | Not necessarily - 需要正确配置URL scheme和测试环境 | Requires proper URL scheme configuration and test environment
  - getInitialURL只在应用启动时有效吗？| Is getInitialURL only valid when app launches?
    **答案 | Answer:** 是 | Yes - 应用运行中的链接需要监听'url'事件 | Links while app is running need to listen to 'url' event

### 6. 实践项目：新闻阅读器应用 | Practical Project: News Reader App (1.5小时 | 1.5 hours)

**目标 | Objective:**
构建一个综合运用本周所学导航知识的新闻阅读应用，包含Drawer、Tab、Stack三种导航器的嵌套使用，自定义Drawer内容，以及基本的深度链接配置。 | Build a news reading app that comprehensively applies this week's navigation knowledge, including nested use of Drawer, Tab, and Stack navigators, custom Drawer content, and basic deep linking configuration.

**概念应用检查 | Concept Application Check:**
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. Drawer导航器应该嵌套在Tab导航器内还是Tab导航器嵌套在Drawer内？| Should Drawer Navigator be nested inside Tab Navigator or vice versa?
   **答案 | Answer:** Drawer通常在最外层，Tab嵌套在Drawer中 | Drawer is usually at the outermost level, Tab nested in Drawer - 这样Drawer可以在任何Tab中打开 | This way Drawer can be opened from any Tab

2. 如何在Stack深层屏幕中打开Drawer？| How to open Drawer from deep screens in Stack?
   **答案 | Answer:** 使用navigation.openDrawer() | Use navigation.openDrawer() - navigation对象会向上查找Drawer导航器 | navigation object searches upward for Drawer Navigator

3. 深度链接的路径映射应该对应哪一层的屏幕？| Which level of screens should deep link path mapping correspond to?
   **答案 | Answer:** 对应实际的屏幕层级结构 | Corresponds to actual screen hierarchy structure - 包括所有嵌套的导航器 | including all nested navigators

**项目步骤 | Project Steps:**

1. **设置项目结构 | Set up project structure**
   - 创建Drawer、Tab、Stack导航器 | Create Drawer, Tab, Stack navigators
   - 定义屏幕组件（Home、Categories、Profile、ArticleDetail等）| Define screen components (Home, Categories, Profile, ArticleDetail, etc.)
   - 配置导航层级：Drawer → Tab → Stack | Configure navigation hierarchy: Drawer → Tab → Stack

2. **实现Tab导航（主内容区）| Implement Tab navigation (main content area)**
   - 创建3个Tab：首页、分类、个人中心 | Create 3 Tabs: Home, Categories, Profile
   - 每个Tab包含独立的Stack导航器 | Each Tab contains independent Stack Navigator
   - 添加Tab图标和样式 | Add Tab icons and styling

3. **实现Drawer导航（侧边菜单）| Implement Drawer navigation (side menu)**
   - 创建自定义Drawer内容组件 | Create custom Drawer content component
   - 添加用户信息、设置、关于等菜单项 | Add user info, settings, about menu items
   - 配置Drawer样式和位置 | Configure Drawer style and position

4. **实现Stack导航（详情页）| Implement Stack navigation (detail pages)**
   - Home Tab中添加文章列表和详情页 | Add article list and detail pages in Home Tab
   - 传递文章数据作为导航参数 | Pass article data as navigation parameters
   - 自定义Header添加分享按钮 | Customize Header to add share button

5. **配置深度链接 | Configure deep linking**
   - 设置URL scheme和路径映射 | Set up URL scheme and path mapping
   - 测试不同路径的链接 | Test links with different paths
   - 处理文章详情页的参数 | Handle parameters for article detail pages

**示例代码架构 | Example Code Architecture:**

```jsx
// 项目完整代码结构 | Complete project code structure
import React from 'react';
import { View, Text, FlatList, TouchableOpacity, Image, StyleSheet } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createDrawerNavigator, DrawerContentScrollView, DrawerItemList, DrawerItem } from '@react-navigation/drawer';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { Ionicons } from '@expo/vector-icons';

const Drawer = createDrawerNavigator();
const Tab = createBottomTabNavigator();
const Stack = createNativeStackNavigator();

// 模拟文章数据 | Mock article data
const articles = [
  { id: '1', title: '科技新闻：AI发展新趋势 | Tech News: New AI Trends', category: 'tech', image: 'https://via.placeholder.com/150' },
  { id: '2', title: '体育快讯：足球比赛结果 | Sports Update: Football Results', category: 'sports', image: 'https://via.placeholder.com/150' },
  { id: '3', title: '财经观察：股市分析 | Finance Watch: Stock Analysis', category: 'finance', image: 'https://via.placeholder.com/150' },
];

// ========== 屏幕组件 | Screen Components ==========

// 文章列表屏幕 | Article List Screen
function ArticleListScreen({ navigation, category = null }) {
  const filteredArticles = category
    ? articles.filter(a => a.category === category)
    : articles;

  return (
    <FlatList
      data={filteredArticles}
      keyExtractor={item => item.id}
      renderItem={({ item }) => (
        <TouchableOpacity
          style={styles.articleItem}
          onPress={() => navigation.navigate('ArticleDetail', { article: item })}
        >
          <Image source={{ uri: item.image }} style={styles.articleImage} />
          <Text style={styles.articleTitle}>{item.title}</Text>
        </TouchableOpacity>
      )}
    />
  );
}

// 文章详情屏幕 | Article Detail Screen
function ArticleDetailScreen({ route, navigation }) {
  const { article } = route.params;

  return (
    <View style={styles.detailContainer}>
      <Image source={{ uri: article.image }} style={styles.detailImage} />
      <Text style={styles.detailTitle}>{article.title}</Text>
      <Text style={styles.detailContent}>
        这里是文章内容... | Article content here...
      </Text>
    </View>
  );
}

// 分类列表屏幕 | Categories List Screen
function CategoriesScreen({ navigation }) {
  const categories = [
    { id: 'tech', name: '科技 | Tech', icon: 'hardware-chip' },
    { id: 'sports', name: '体育 | Sports', icon: 'football' },
    { id: 'finance', name: '财经 | Finance', icon: 'cash' },
  ];

  return (
    <FlatList
      data={categories}
      keyExtractor={item => item.id}
      renderItem={({ item }) => (
        <TouchableOpacity
          style={styles.categoryItem}
          onPress={() => navigation.navigate('CategoryArticles', { category: item })}
        >
          <Ionicons name={item.icon} size={32} color="#6200ee" />
          <Text style={styles.categoryName}>{item.name}</Text>
        </TouchableOpacity>
      )}
    />
  );
}

// 分类文章屏幕 | Category Articles Screen
function CategoryArticlesScreen({ route, navigation }) {
  const { category } = route.params;
  return <ArticleListScreen navigation={navigation} category={category.id} />;
}

// 个人中心屏幕 | Profile Screen
function ProfileScreen() {
  return (
    <View style={styles.centerContainer}>
      <Text style={styles.title}>个人中心 | Profile</Text>
      <Text>用户设置和信息 | User settings and info</Text>
    </View>
  );
}

// 设置屏幕 | Settings Screen
function SettingsScreen() {
  return (
    <View style={styles.centerContainer}>
      <Text style={styles.title}>设置 | Settings</Text>
      <Text>应用设置选项 | App settings options</Text>
    </View>
  );
}

// 关于屏幕 | About Screen
function AboutScreen() {
  return (
    <View style={styles.centerContainer}>
      <Text style={styles.title}>关于 | About</Text>
      <Text>版本信息：1.0.0 | Version: 1.0.0</Text>
    </View>
  );
}

// ========== Stack导航器 | Stack Navigators ==========

// Home Tab的Stack | Home Tab Stack
function HomeStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen
        name="ArticleList"
        component={ArticleListScreen}
        options={({ navigation }) => ({
          title: '首页 | Home',
          headerLeft: () => (
            <TouchableOpacity onPress={() => navigation.openDrawer()} style={{ marginLeft: 15 }}>
              <Ionicons name="menu" size={24} color="#000" />
            </TouchableOpacity>
          ),
        })}
      />
      <Stack.Screen
        name="ArticleDetail"
        component={ArticleDetailScreen}
        options={({ route }) => ({
          title: route.params.article.title,
          headerRight: () => (
            <TouchableOpacity style={{ marginRight: 15 }}>
              <Ionicons name="share-outline" size={24} color="#000" />
            </TouchableOpacity>
          ),
        })}
      />
    </Stack.Navigator>
  );
}

// Categories Tab的Stack | Categories Tab Stack
function CategoriesStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen
        name="CategoriesList"
        component={CategoriesScreen}
        options={({ navigation }) => ({
          title: '分类 | Categories',
          headerLeft: () => (
            <TouchableOpacity onPress={() => navigation.openDrawer()} style={{ marginLeft: 15 }}>
              <Ionicons name="menu" size={24} color="#000" />
            </TouchableOpacity>
          ),
        })}
      />
      <Stack.Screen
        name="CategoryArticles"
        component={CategoryArticlesScreen}
        options={({ route }) => ({
          title: route.params.category.name,
        })}
      />
      <Stack.Screen
        name="ArticleDetail"
        component={ArticleDetailScreen}
        options={({ route }) => ({
          title: route.params.article.title,
        })}
      />
    </Stack.Navigator>
  );
}

// ========== Tab导航器 | Tab Navigator ==========

function TabNavigator() {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ focused, color, size }) => {
          let iconName;
          if (route.name === 'Home') {
            iconName = focused ? 'home' : 'home-outline';
          } else if (route.name === 'Categories') {
            iconName = focused ? 'grid' : 'grid-outline';
          } else if (route.name === 'Profile') {
            iconName = focused ? 'person' : 'person-outline';
          }
          return <Ionicons name={iconName} size={size} color={color} />;
        },
        tabBarActiveTintColor: '#6200ee',
        tabBarInactiveTintColor: 'gray',
        headerShown: false,
      })}
    >
      <Tab.Screen name="Home" component={HomeStack} options={{ title: '首页 | Home' }} />
      <Tab.Screen name="Categories" component={CategoriesStack} options={{ title: '分类 | Categories' }} />
      <Tab.Screen name="Profile" component={ProfileScreen} options={{ title: '我的 | Mine' }} />
    </Tab.Navigator>
  );
}

// ========== 自定义Drawer内容 | Custom Drawer Content ==========

function CustomDrawerContent(props) {
  return (
    <DrawerContentScrollView {...props}>
      {/* 用户信息区域 | User info section */}
      <View style={styles.drawerHeader}>
        <Image
          source={{ uri: 'https://randomuser.me/api/portraits/men/36.jpg' }}
          style={styles.drawerAvatar}
        />
        <Text style={styles.drawerUserName}>张三 | John Doe</Text>
        <Text style={styles.drawerUserEmail}>john@example.com</Text>
      </View>

      <View style={styles.divider} />

      {/* 默认导航项 | Default navigation items */}
      <DrawerItemList {...props} />

      <View style={styles.divider} />

      {/* 自定义菜单项 | Custom menu items */}
      <DrawerItem
        label="设置 | Settings"
        icon={({ color, size }) => <Ionicons name="settings-outline" color={color} size={size} />}
        onPress={() => props.navigation.navigate('Settings')}
      />
      <DrawerItem
        label="关于 | About"
        icon={({ color, size }) => <Ionicons name="information-circle-outline" color={color} size={size} />}
        onPress={() => props.navigation.navigate('About')}
      />
    </DrawerContentScrollView>
  );
}

// ========== Drawer导航器（根导航器）| Drawer Navigator (Root) ==========

function RootDrawer() {
  return (
    <Drawer.Navigator
      drawerContent={(props) => <CustomDrawerContent {...props} />}
      screenOptions={{
        headerShown: false,
        drawerStyle: {
          backgroundColor: '#f8f9fa',
          width: 280,
        },
      }}
    >
      <Drawer.Screen
        name="Main"
        component={TabNavigator}
        options={{
          drawerLabel: '主页 | Main',
          drawerIcon: ({ color, size }) => <Ionicons name="home" color={color} size={size} />,
        }}
      />
      <Drawer.Screen
        name="Settings"
        component={SettingsScreen}
        options={{
          drawerLabel: '设置 | Settings',
          drawerIcon: ({ color, size }) => <Ionicons name="settings" color={color} size={size} />,
        }}
      />
      <Drawer.Screen
        name="About"
        component={AboutScreen}
        options={{
          drawerLabel: '关于 | About',
          drawerIcon: ({ color, size }) => <Ionicons name="information-circle" color={color} size={size} />,
        }}
      />
    </Drawer.Navigator>
  );
}

// ========== 深度链接配置 | Deep Linking Configuration ==========

const linking = {
  prefixes: ['newsapp://', 'https://newsapp.com'],
  config: {
    screens: {
      Main: {
        screens: {
          Home: {
            screens: {
              ArticleList: 'home',
              ArticleDetail: 'article/:id',
            },
          },
          Categories: {
            screens: {
              CategoriesList: 'categories',
              CategoryArticles: 'category/:categoryId',
              ArticleDetail: 'category/:categoryId/article/:id',
            },
          },
          Profile: 'profile',
        },
      },
      Settings: 'settings',
      About: 'about',
    },
  },
};

// ========== App根组件 | App Root Component ==========

export default function App() {
  return (
    <NavigationContainer linking={linking}>
      <RootDrawer />
    </NavigationContainer>
  );
}

// ========== 样式 | Styles ==========

const styles = StyleSheet.create({
  // 文章列表 | Article list
  articleItem: {
    flexDirection: 'row',
    padding: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
    alignItems: 'center',
  },
  articleImage: {
    width: 60,
    height: 60,
    borderRadius: 8,
    marginRight: 15,
  },
  articleTitle: {
    flex: 1,
    fontSize: 16,
    fontWeight: '500',
  },

  // 文章详情 | Article detail
  detailContainer: {
    flex: 1,
    padding: 20,
  },
  detailImage: {
    width: '100%',
    height: 200,
    borderRadius: 8,
    marginBottom: 15,
  },
  detailTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 15,
  },
  detailContent: {
    fontSize: 16,
    lineHeight: 24,
    color: '#333',
  },

  // 分类列表 | Categories list
  categoryItem: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 20,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  categoryName: {
    fontSize: 18,
    marginLeft: 15,
    fontWeight: '500',
  },

  // 通用居中容器 | Common center container
  centerContainer: {
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

  // Drawer样式 | Drawer styles
  drawerHeader: {
    padding: 20,
    backgroundColor: '#6200ee',
    alignItems: 'center',
  },
  drawerAvatar: {
    width: 80,
    height: 80,
    borderRadius: 40,
    marginBottom: 10,
  },
  drawerUserName: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#fff',
  },
  drawerUserEmail: {
    fontSize: 14,
    color: '#e0e0e0',
    marginTop: 5,
  },
  divider: {
    height: 1,
    backgroundColor: '#e0e0e0',
    marginVertical: 10,
  },
});
```

**项目完成检查 | Project Completion Check:**
1. 项目是否正确使用了Drawer、Tab、Stack三种导航器的嵌套？| Does the project correctly use nested Drawer, Tab, and Stack navigators?
2. 能否从任何Stack深层屏幕打开Drawer？| Can you open Drawer from any deep Stack screen?
3. 自定义Drawer内容是否包含用户信息和额外菜单项？| Does custom Drawer content include user info and extra menu items?
4. 深度链接配置是否能正确映射到各个屏幕？| Does deep linking configuration correctly map to各 screens?
5. 文章详情页的Header是否包含自定义的分享按钮？| Does article detail Header include custom share button?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Drawer导航模式理解练习 | Drawer Navigation Pattern Understanding Exercise**
   - **练习描述 | Exercise Description:** 分析3个流行应用的侧边栏设计，总结不同的Drawer使用模式和最佳实践
   - **概念检查 | Concept Check:** 能否解释为什么某些应用使用Drawer而另一些使用Tab？
   - **学习目标 | Learning Objective:** 深入理解导航模式的选择依据和用户体验考虑

2. **深度链接路径设计练习 | Deep Link Path Design Exercise**
   - **练习描述 | Exercise Description:** 为一个电商应用设计完整的深度链接URL scheme和路径结构
   - **概念检查 | Concept Check:** 如何设计层级清晰且易于分享的深度链接？
   - **学习目标 | Learning Objective:** 掌握深度链接的URL设计原则和SEO友好的路径结构

3. **导航状态管理练习 | Navigation State Management Exercise**
   - **练习描述 | Exercise Description:** 实现Drawer打开/关闭时的动画效果和状态同步
   - **概念检查 | Concept Check:** 导航状态如何与应用状态（如Redux）集成？
   - **学习目标 | Learning Objective:** 理解导航状态管理和事件监听机制

4. **自定义Drawer交互练习 | Custom Drawer Interaction Exercise**
   - **练习描述 | Exercise Description:** 创建一个支持分组、搜索、徽章显示的高级Drawer组件
   - **概念检查 | Concept Check:** 如何在保持原生性能的同时实现复杂Drawer交互？
   - **学习目标 | Learning Objective:** 提升自定义导航组件的设计和实现能力

5. **跨平台导航适配练习 | Cross-Platform Navigation Adaptation Exercise**
   - **练习描述 | Exercise Description:** 实现iOS和Android平台特定的Drawer行为差异
   - **概念检查 | Concept Check:** iOS和Android的导航模式有哪些平台特定的约定？
   - **学习目标 | Learning Objective:** 掌握跨平台导航的适配技巧和平台规范

6. **导航性能优化练习 | Navigation Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 分析和优化包含大量屏幕的Drawer应用的性能
   - **概念检查 | Concept Check:** 哪些因素会影响Drawer导航的性能？
   - **学习目标 | Learning Objective:** 学习导航性能优化技术（懒加载、屏幕预加载等）

7. **深度链接高级应用练习 | Advanced Deep Linking Application Exercise**
   - **练习描述 | Exercise Description:** 实现带有认证保护、参数验证和fallback逻辑的深度链接
   - **概念检查 | Concept Check:** 如何处理深度链接中的安全性和边界情况？
   - **学习目标 | Learning Objective:** 掌握生产级深度链接的完整实现方案

## 学习资源 | Learning Resources
- [React Navigation Drawer Navigator 官方文档 | Official Docs](https://reactnavigation.org/docs/drawer-navigator)
- [React Navigation Deep Linking 官方文档 | Official Docs](https://reactnavigation.org/docs/deep-linking)
- [Drawer Navigator API参考 | API Reference](https://reactnavigation.org/docs/drawer-navigator#api-definition)
- [自定义Drawer内容指南 | Custom Drawer Content Guide](https://reactnavigation.org/docs/drawer-navigator#providing-a-custom-drawercontent)
- [Deep Linking配置详解 | Deep Linking Configuration Details](https://reactnavigation.org/docs/configuring-links)
- [URL Scheme最佳实践 | URL Scheme Best Practices](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Drawer导航器的核心概念和使用场景 | Understand Drawer Navigator core concepts and use cases
- [ ] 成功安装和配置Drawer导航器及其依赖 | Successfully install and configure Drawer Navigator and dependencies
- [ ] 掌握Drawer的基础配置选项（位置、类型、样式）| Master basic Drawer configuration options (position, type, style)
- [ ] 能够创建自定义Drawer内容组件 | Able to create custom Drawer content component
- [ ] 理解Drawer与Tab、Stack导航器的嵌套关系 | Understand nesting relationship between Drawer, Tab, and Stack navigators
- [ ] 掌握程序化控制Drawer的方法（open、close、toggle）| Master programmatic Drawer control methods (open, close, toggle)
- [ ] 能够监听Drawer的状态变化事件 | Able to listen to Drawer state change events
- [ ] 理解深度链接的基本概念和配置方法 | Understand basic concepts and configuration of deep linking
- [ ] 成功配置并测试深度链接功能 | Successfully configure and test deep linking功能
- [ ] 完成新闻阅读器实践项目 | Complete News Reader practical project
- [ ] 能够在Header中添加打开Drawer的按钮 | Able to add button to open Drawer in Header
- [ ] 理解URL scheme和路径映射的关系 | Understand relationship between URL scheme and path mapping

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
- 解释Drawer、Tab、Stack三种导航器的使用场景差异
- 独立实现一个包含自定义内容的Drawer导航器
- 配置并测试多层级嵌套的深度链接
- 在任意屏幕中程序化控制Drawer的打开和关闭

Before marking as complete, ensure you can:
- Explain the usage scenario differences between Drawer, Tab, and Stack navigators
- Independently implement a Drawer Navigator with custom content
- Configure and test multi-level nested deep linking
- Programmatically control Drawer opening and closing from any screen
