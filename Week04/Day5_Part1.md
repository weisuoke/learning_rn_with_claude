# React Native入门 - 第5天：Drawer导航器与深度链接基础 | React Native Introduction - Day 5: Drawer Navigator & Deep Linking Basics

## 学习目标 | Learning Objectives
- 理解Drawer导航器的工作原理和使用场景 | Understand how Drawer Navigator works and when to use it
- 掌握Drawer导航器的安装和基础配置 | Master installation and basic configuration of Drawer Navigator
- 学会自定义Drawer内容和样式 | Learn to customize Drawer content and styling
- 理解深度链接的概念和基本配置 | Understand deep linking concepts and basic configuration
- 掌握Drawer与其他导航器的组合使用 | Master combining Drawer with other navigators
- 学会程序化控制Drawer的打开和关闭 | Learn to programmatically control Drawer opening and closing

## 详细内容 | Detailed Content

### 1. Drawer导航器核心概念 | Drawer Navigator Core Concepts (1小时 | 1 hour)

- **什么是Drawer导航器 | What is Drawer Navigator**

  **概念定义 | Concept Definition:**
  Drawer导航器是一种从屏幕侧边滑出的侧边栏导航模式，通常用于显示应用的主要导航菜单或设置选项。它提供了一种隐藏式的导航方式，不占用屏幕主要空间，用户可以通过滑动手势或点击菜单按钮来打开。 | Drawer Navigator is a side panel navigation pattern that slides from the screen edge, typically used to display the app's main navigation menu or settings options. It provides a hidden navigation approach that doesn't occupy main screen space, and users can open it through swipe gestures or menu button taps.

  **核心特征 | Key Characteristics:**
  - 侧滑手势：支持从屏幕边缘滑动打开抽屉 | Swipe gesture: supports opening drawer by swiping from screen edge
  - 覆盖式显示：抽屉内容覆盖在主屏幕之上 | Overlay display: drawer content overlays on top of main screen
  - 多种打开方式：手势滑动、点击按钮、程序控制 | Multiple opening methods: gesture swipe, button tap, programmatic control
  - 可自定义内容：完全控制抽屉内的UI和交互 | Customizable content: full control over drawer UI and interactions
  - 适合多级导航：常用于应用的顶层导航结构 | Suitable for multi-level navigation: commonly used for top-level app navigation structure

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Drawer导航器会占用主屏幕的显示空间吗？| Does Drawer Navigator occupy main screen display space?
     **答案 | Answer:** 否 | No - Drawer以覆盖层形式显示，不占用主屏幕空间 | Drawer displays as an overlay and doesn't occupy main screen space
  2. 用户只能通过按钮打开Drawer吗？| Can users only open Drawer through buttons?
     **答案 | Answer:** 否 | No - 可以通过滑动手势、按钮点击或程序控制打开 | Can be opened via swipe gesture, button tap, or programmatic control
  3. Drawer导航器适合用作应用的哪一层级导航？| What navigation level is Drawer Navigator suitable for?
     **答案 | Answer:** 顶层导航 | Top-level navigation - 通常用于应用的主导航结构 | Usually used for the app's main navigation structure
  4. Drawer导航器需要依赖手势库吗？| Does Drawer Navigator require a gesture library?
     **答案 | Answer:** 是 | Yes - 需要安装react-native-gesture-handler来支持手势交互 | Requires react-native-gesture-handler to support gesture interactions

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // Drawer导航器基础结构 | Basic Drawer Navigator structure
  import { createDrawerNavigator } from '@react-navigation/drawer';
  import { NavigationContainer } from '@react-navigation/native';

  const Drawer = createDrawerNavigator();

  // 主屏幕组件 | Home screen component
  function HomeScreen({ navigation }) {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>主屏幕 | Home Screen</Text>
        {/* 程序化打开Drawer | Programmatically open drawer */}
        <Button
          onPress={() => navigation.openDrawer()}
          title="打开菜单 | Open Menu"
        />
      </View>
    );
  }

  // 设置屏幕组件 | Settings screen component
  function SettingsScreen() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>设置 | Settings</Text>
      </View>
    );
  }

  // Drawer导航器配置 | Drawer Navigator configuration
  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator>
          {/* 定义Drawer中的屏幕 | Define screens in Drawer */}
          <Drawer.Screen name="Home" component={HomeScreen} />
          <Drawer.Screen name="Settings" component={SettingsScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中Drawer有几个导航项？| How many navigation items does the Drawer have in this code?
    **答案 | Answer:** 2个 - Home和Settings | 2 items - Home and Settings
  - `navigation.openDrawer()`方法的作用是什么？| What is the purpose of the `navigation.openDrawer()` method?
    **答案 | Answer:** 程序化地打开Drawer菜单 | Programmatically opens the Drawer menu

  **常见误区检查 | Common Misconception Checks:**
  - Drawer导航器可以替代Tab导航器吗？| Can Drawer Navigator replace Tab Navigator?
    **答案 | Answer:** 不完全能 | Not entirely - Drawer适合较多导航项和次要功能，Tab适合3-5个主要功能的快速切换 | Drawer suits more navigation items and secondary features, Tab suits quick switching between 3-5 primary features
  - 必须在每个屏幕添加打开Drawer的按钮吗？| Must we add a button to open Drawer on every screen?
    **答案 | Answer:** 否 | No - 用户可以通过边缘滑动手势打开，但添加按钮能提升可发现性 | Users can open via edge swipe gesture, but adding a button improves discoverability

- **Drawer vs Stack vs Tab导航器对比 | Drawer vs Stack vs Tab Navigator Comparison**

  **概念定义 | Concept Definition:**
  三种导航器各有不同的使用场景：Stack用于线性的屏幕层级关系，Tab用于并列的主要功能切换，Drawer用于较多导航选项和辅助功能的隐藏式访问。 | The three navigators have different use cases: Stack for linear screen hierarchies, Tab for parallel primary feature switching, Drawer for hidden access to more navigation options and auxiliary features.

  **核心特征 | Key Characteristics:**
  - Stack：后进先出，适合详情页导航 | Stack: LIFO (Last In First Out), suitable for detail page navigation
  - Tab：固定底部或顶部，适合主功能切换 | Tab: fixed bottom or top bar, suitable for main feature switching
  - Drawer：侧滑隐藏，适合多选项菜单 | Drawer: side-sliding hidden menu, suitable for multi-option menus
  - 三者可以嵌套组合使用 | All three can be nested and combined

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Stack导航器的导航历史遵循什么数据结构？| What data structure does Stack Navigator's navigation history follow?
     **答案 | Answer:** 栈结构（后进先出）| Stack structure (LIFO - Last In First Out)
  2. 如果应用有8个主要功能区，应该用Tab还是Drawer？| If an app has 8 main functional areas, should we use Tab or Drawer?
     **答案 | Answer:** Drawer | Drawer - Tab导航器通常只适合3-5个选项 | Tab Navigator typically suits only 3-5 options
  3. Drawer导航器可以包含Stack导航器吗？| Can Drawer Navigator contain Stack Navigator?
     **答案 | Answer:** 可以 | Yes - 导航器可以嵌套使用 | Navigators can be nested
  4. 用户在Tab之间切换时，Stack历史会保留吗？| When users switch between Tabs, is Stack history preserved?
     **答案 | Answer:** 是 | Yes - 每个Tab维护自己的导航历史 | Each Tab maintains its own navigation history

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 组合使用Drawer、Tab和Stack | Combining Drawer, Tab and Stack
  import { createDrawerNavigator } from '@react-navigation/drawer';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Drawer = createDrawerNavigator();
  const Tab = createBottomTabNavigator();
  const Stack = createNativeStackNavigator();

  // Home Tab中的Stack导航 | Stack navigation in Home Tab
  function HomeStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="HomeList" component={HomeListScreen} />
        <Stack.Screen name="HomeDetail" component={HomeDetailScreen} />
      </Stack.Navigator>
    );
  }

  // Profile Tab中的Stack导航 | Stack navigation in Profile Tab
  function ProfileStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="ProfileMain" component={ProfileScreen} />
        <Stack.Screen name="EditProfile" component={EditProfileScreen} />
      </Stack.Navigator>
    );
  }

  // Tab导航器包含多个Stack | Tab Navigator containing multiple Stacks
  function TabNavigator() {
    return (
      <Tab.Navigator>
        <Tab.Screen name="Home" component={HomeStack} />
        <Tab.Screen name="Profile" component={ProfileStack} />
      </Tab.Navigator>
    );
  }

  // Drawer导航器作为根导航器 | Drawer Navigator as root navigator
  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator>
          {/* Tab导航器嵌套在Drawer中 | Tab Navigator nested in Drawer */}
          <Drawer.Screen name="Main" component={TabNavigator} />
          <Drawer.Screen name="Settings" component={SettingsScreen} />
          <Drawer.Screen name="About" component={AboutScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在这个导航结构中，从HomeDetailScreen如何打开Drawer？| In this navigation structure, how to open Drawer from HomeDetailScreen?
    **答案 | Answer:** 使用`navigation.openDrawer()` | Use `navigation.openDrawer()` - navigation对象可以访问祖先导航器 | navigation object can access ancestor navigators
  - 这个导航结构有几层嵌套？| How many nesting levels does this navigation structure have?
    **答案 | Answer:** 3层 | 3 levels - Drawer → Tab → Stack

### 2. 安装和配置Drawer导航器 | Installing and Configuring Drawer Navigator (1小时 | 1 hour)

- **安装必需的依赖包 | Installing Required Dependencies**

  **概念定义 | Concept Definition:**
  Drawer导航器需要三个核心依赖：@react-navigation/drawer提供Drawer组件，react-native-gesture-handler处理滑动手势，react-native-reanimated提供流畅的动画效果。 | Drawer Navigator requires three core dependencies: @react-navigation/drawer provides Drawer components, react-native-gesture-handler handles swipe gestures, and react-native-reanimated provides smooth animations.

  **安装步骤 | Installation Steps:**
  - 第一步：安装Drawer导航器包 | Step 1: Install Drawer Navigator package
  - 第二步：安装手势处理库 | Step 2: Install gesture handler library
  - 第三步：安装动画库 | Step 3: Install animation library
  - 第四步：配置原生依赖（重要）| Step 4: Configure native dependencies (important)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Drawer导航器只需安装@react-navigation/drawer就能工作吗？| Does Drawer Navigator work by only installing @react-navigation/drawer?
     **答案 | Answer:** 否 | No - 还需要gesture-handler和reanimated库 | Also requires gesture-handler and reanimated libraries
  2. 安装依赖后需要重启开发服务器吗？| Do you need to restart the dev server after installing dependencies?
     **答案 | Answer:** 是 | Yes - 特别是涉及原生模块的依赖 | Especially for dependencies involving native modules
  3. react-native-gesture-handler的作用是什么？| What is the purpose of react-native-gesture-handler?
     **答案 | Answer:** 处理Drawer的滑动手势交互 | Handles swipe gesture interactions for Drawer
  4. 在Expo项目和纯React Native项目中，安装命令有区别吗？| Is there a difference in installation commands between Expo and pure React Native projects?
     **答案 | Answer:** 有 | Yes - Expo使用`expo install`，纯RN使用`npm install`且可能需要pod install | Expo uses `expo install`, pure RN uses `npm install` and may need pod install

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # Expo项目安装 | Expo project installation
  # 安装Drawer导航器 | Install Drawer Navigator
  npm install @react-navigation/drawer

  # 安装必需的对等依赖 | Install required peer dependencies
  expo install react-native-gesture-handler react-native-reanimated

  # 纯React Native项目安装 | Pure React Native project installation
  npm install @react-navigation/drawer
  npm install react-native-gesture-handler react-native-reanimated

  # iOS需要额外的pod安装 | iOS requires additional pod installation
  cd ios && pod install && cd ..
  ```

  ```jsx
  // 配置react-native-gesture-handler | Configure react-native-gesture-handler
  // 在App.js或index.js的最顶部导入 | Import at the very top of App.js or index.js
  import 'react-native-gesture-handler';
  import { NavigationContainer } from '@react-navigation/native';
  import { createDrawerNavigator } from '@react-navigation/drawer';

  const Drawer = createDrawerNavigator();

  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator>
          {/* Drawer屏幕配置 | Drawer screens configuration */}
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }

  export default App;
  ```

  ```jsx
  // 配置react-native-reanimated (babel.config.js)
  // 在babel配置中添加reanimated插件 | Add reanimated plugin to babel config
  module.exports = {
    presets: ['module:metro-react-native-babel-preset'],
    plugins: [
      // ... 其他插件 | other plugins
      'react-native-reanimated/plugin', // 必须是最后一个插件 | Must be the last plugin
    ],
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么gesture-handler必须在文件最顶部导入？| Why must gesture-handler be imported at the very top of the file?
    **答案 | Answer:** 确保手势处理器在其他组件之前初始化 | Ensures gesture handler is initialized before other components
  - reanimated插件在babel配置中的位置重要吗？| Is the position of reanimated plugin important in babel config?
    **答案 | Answer:** 是 | Yes - 必须是plugins数组的最后一项 | Must be the last item in plugins array

  **常见误区检查 | Common Misconception Checks:**
  - 安装后不重启开发服务器会怎样？| What happens if you don't restart the dev server after installation?
    **答案 | Answer:** 可能导致手势不工作或动画异常 | May cause gestures not working or animation issues
  - 可以用Web版本的Drawer导航器吗？| Can you use the web version of Drawer Navigator?
    **答案 | Answer:** 不建议 | Not recommended - 移动端需要原生手势和动画支持 | Mobile requires native gesture and animation support

- **基础Drawer配置选项 | Basic Drawer Configuration Options**

  **概念定义 | Concept Definition:**
  Drawer导航器提供丰富的配置选项来控制外观和行为，包括打开位置（左/右）、Drawer宽度、背景色、覆盖层颜色等。这些选项通过`screenOptions`或单个Screen的`options`属性设置。 | Drawer Navigator provides rich configuration options to control appearance and behavior, including open position (left/right), drawer width, background color, overlay color, etc. These options are set through `screenOptions` or individual Screen's `options` prop.

  **核心配置项 | Key Configuration Options:**
  - drawerPosition：Drawer打开位置（'left' 或 'right'）| drawerPosition: Drawer open position ('left' or 'right')
  - drawerType：Drawer类型（'front', 'back', 'slide', 'permanent'）| drawerType: Drawer type ('front', 'back', 'slide', 'permanent')
  - drawerStyle：Drawer样式（宽度、背景色等）| drawerStyle: Drawer style (width, background color, etc.)
  - overlayColor：覆盖层颜色和透明度 | overlayColor: overlay color and opacity
  - sceneContainerStyle：主屏幕容器样式 | sceneContainerStyle: main screen container style

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. drawerPosition的默认值是什么？| What is the default value of drawerPosition?
     **答案 | Answer:** 'left' - Drawer默认从左侧打开 | 'left' - Drawer opens from left by default
  2. drawerType为'front'和'back'有什么区别？| What's the difference between drawerType 'front' and 'back'?
     **答案 | Answer:** 'front'是Drawer覆盖在内容上，'back'是内容推开显示Drawer | 'front' means Drawer overlays content, 'back' means content pushes aside to show Drawer
  3. 可以为每个Screen设置不同的Drawer样式吗？| Can you set different Drawer styles for each Screen?
     **答案 | Answer:** 可以但不推荐 | Yes but not recommended - Drawer样式应该全局统一 | Drawer style should be globally consistent
  4. overlayColor的作用是什么？| What is the purpose of overlayColor?
     **答案 | Answer:** 设置Drawer打开时主屏幕上的半透明遮罩颜色 | Sets the semi-transparent mask color on main screen when Drawer opens

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // Drawer配置选项示例 | Drawer configuration options example
  import { createDrawerNavigator } from '@react-navigation/drawer';
  import { View, Text } from 'react-native';

  const Drawer = createDrawerNavigator();

  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator
          // 全局Drawer配置 | Global Drawer configuration
          screenOptions={{
            // Drawer从右侧打开 | Open Drawer from right
            drawerPosition: 'right',

            // Drawer类型：覆盖式 | Drawer type: overlay
            drawerType: 'front',

            // Drawer样式配置 | Drawer style configuration
            drawerStyle: {
              backgroundColor: '#f8f9fa', // 背景色 | Background color
              width: 280, // 宽度 | Width
            },

            // 覆盖层颜色（黑色，50%透明度）| Overlay color (black, 50% opacity)
            overlayColor: 'rgba(0, 0, 0, 0.5)',

            // 主屏幕容器样式 | Main screen container style
            sceneContainerStyle: {
              backgroundColor: '#ffffff',
            },

            // Header配置 | Header configuration
            headerStyle: {
              backgroundColor: '#6200ee',
            },
            headerTintColor: '#fff',
            headerTitleStyle: {
              fontWeight: 'bold',
            },
          }}
        >
          <Drawer.Screen
            name="Home"
            component={HomeScreen}
            options={{
              title: '主页 | Home',
              drawerLabel: '首页 | Home',
            }}
          />
          <Drawer.Screen
            name="Profile"
            component={ProfileScreen}
            options={{
              title: '个人资料 | Profile',
              drawerLabel: '我的资料 | My Profile',
            }}
          />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果想要Drawer占据全屏宽度，drawerStyle的width应该设置为多少？| If you want Drawer to occupy full screen width, what should drawerStyle width be set to?
    **答案 | Answer:** 可以不设置width或设置为屏幕宽度 | Can omit width or set to screen width using Dimensions.get('window').width
  - drawerLabel和title有什么区别？| What's the difference between drawerLabel and title?
    **答案 | Answer:** drawerLabel显示在Drawer菜单中，title显示在Header中 | drawerLabel shows in Drawer menu, title shows in Header

  **常见误区检查 | Common Misconception Checks:**
  - 修改drawerType后为什么没有效果？| Why doesn't changing drawerType take effect?
    **答案 | Answer:** 可能需要完全重启应用（不是热重载）| May need to completely restart the app (not hot reload)
  - overlayColor设置为透明后点击覆盖层会关闭Drawer吗？| After setting overlayColor to transparent, will tapping overlay close Drawer?
    **答案 | Answer:** 会 | Yes - 覆盖层的点击行为不受颜色影响 | Overlay tap behavior is not affected by color

### 3. 自定义Drawer内容 | Customizing Drawer Content (1.5小时 | 1.5 hours)

- **使用自定义Drawer组件 | Using Custom Drawer Component**

  **概念定义 | Concept Definition:**
  默认的Drawer只显示简单的导航列表，通过`drawerContent`属性可以完全自定义Drawer的内容，包括用户头像、个性化菜单、底部按钮等，实现更丰富的用户体验。 | The default Drawer only displays a simple navigation list. Through the `drawerContent` prop, you can fully customize Drawer content, including user avatars, personalized menus, bottom buttons, etc., to achieve a richer user experience.

  **核心特征 | Key Characteristics:**
  - 完全控制：自定义组件可以包含任何React组件 | Full control: custom component can contain any React components
  - 访问导航状态：通过props获取navigation和state | Access navigation state: get navigation and state through props
  - 保留默认行为：可以使用DrawerItemList保留默认项 | Preserve default behavior: can use DrawerItemList to keep default items
  - 灵活布局：可以添加header、footer、分组等 | Flexible layout: can add header, footer, grouping, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 自定义Drawer内容会完全替换默认的导航列表吗？| Does custom Drawer content completely replace the default navigation list?
     **答案 | Answer:** 是 | Yes - 除非在自定义内容中使用DrawerItemList | Unless you use DrawerItemList in custom content
  2. 自定义Drawer组件能访问navigation对象吗？| Can custom Drawer component access navigation object?
     **答案 | Answer:** 是 | Yes - 通过props接收navigation和其他drawer属性 | Receives navigation and other drawer props through props
  3. 可以在自定义Drawer中添加非导航元素吗？| Can you add non-navigation elements in custom Drawer?
     **答案 | Answer:** 可以 | Yes - 如用户信息卡片、广告、设置按钮等 | Such as user info cards, ads, settings buttons, etc.
  4. DrawerItemList组件的作用是什么？| What is the purpose of DrawerItemList component?
     **答案 | Answer:** 渲染默认的导航项列表 | Renders the default navigation item list

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 自定义Drawer内容组件 | Custom Drawer content component
  import {
    DrawerContentScrollView,
    DrawerItemList,
    DrawerItem
  } from '@react-navigation/drawer';
  import { View, Text, Image, StyleSheet } from 'react-native';

  function CustomDrawerContent(props) {
    return (
      <DrawerContentScrollView {...props}>
        {/* 用户信息头部 | User info header */}
        <View style={styles.userInfoSection}>
          <Image
            source={{ uri: 'https://randomuser.me/api/portraits/men/1.jpg' }}
            style={styles.avatar}
          />
          <Text style={styles.userName}>张三 | John Doe</Text>
          <Text style={styles.userEmail}>zhangsan@example.com</Text>
        </View>

        {/* 分隔线 | Divider */}
        <View style={styles.divider} />

        {/* 默认导航项 | Default navigation items */}
        <DrawerItemList {...props} />

        {/* 分隔线 | Divider */}
        <View style={styles.divider} />

        {/* 自定义菜单项 | Custom menu items */}
        <DrawerItem
          label="帮助中心 | Help Center"
          onPress={() => {
            // 导航到帮助页面或打开链接 | Navigate to help page or open link
            console.log('打开帮助中心 | Open Help Center');
          }}
          icon={({ color, size }) => (
            <Icon name="help-circle" color={color} size={size} />
          )}
        />

        <DrawerItem
          label="退出登录 | Logout"
          onPress={() => {
            // 处理登出逻辑 | Handle logout logic
            console.log('用户登出 | User logout');
          }}
          icon={({ color, size }) => (
            <Icon name="logout" color={color} size={size} />
          )}
          labelStyle={{ color: '#e74c3c' }}
        />

        {/* 底部版本信息 | Bottom version info */}
        <View style={styles.footer}>
          <Text style={styles.version}>版本 | Version 1.0.0</Text>
        </View>
      </DrawerContentScrollView>
    );
  }

  const styles = StyleSheet.create({
    userInfoSection: {
      padding: 20,
      alignItems: 'center',
      backgroundColor: '#6200ee',
    },
    avatar: {
      width: 80,
      height: 80,
      borderRadius: 40,
      marginBottom: 10,
    },
    userName: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#fff',
    },
    userEmail: {
      fontSize: 14,
      color: '#e0e0e0',
      marginTop: 5,
    },
    divider: {
      height: 1,
      backgroundColor: '#e0e0e0',
      marginVertical: 10,
    },
    footer: {
      padding: 20,
      alignItems: 'center',
      borderTopWidth: 1,
      borderTopColor: '#e0e0e0',
    },
    version: {
      fontSize: 12,
      color: '#9e9e9e',
    },
  });

  // 在Drawer.Navigator中使用自定义内容 | Use custom content in Drawer.Navigator
  function App() {
    return (
      <NavigationContainer>
        <Drawer.Navigator
          drawerContent={(props) => <CustomDrawerContent {...props} />}
        >
          <Drawer.Screen name="Home" component={HomeScreen} />
          <Drawer.Screen name="Profile" component={ProfileScreen} />
          <Drawer.Screen name="Settings" component={SettingsScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用DrawerContentScrollView而不是普通的ScrollView？| Why use DrawerContentScrollView instead of regular ScrollView?
    **答案 | Answer:** DrawerContentScrollView内置了Drawer所需的特定行为和样式 | DrawerContentScrollView has built-in specific behaviors and styles needed for Drawer
  - 如果不使用DrawerItemList，还能显示Screen定义的导航项吗？| If you don't use DrawerItemList, will Screen-defined navigation items still display?
    **答案 | Answer:** 不会 | No - 需要手动实现导航项或使用DrawerItemList | Need to manually implement navigation items or use DrawerItemList

  **常见误区检查 | Common Misconception Checks:**
  - 自定义Drawer内容后，手势滑动还能工作吗？| After customizing Drawer content, will gesture swipe still work?
    **答案 | Answer:** 能 | Yes - 手势行为由导航器控制，不受内容定制影响 | Gesture behavior is controlled by navigator, unaffected by content customization
  - 必须在自定义组件中使用DrawerContentScrollView吗？| Must you use DrawerContentScrollView in custom component?
    **答案 | Answer:** 不是必须但强烈建议 | Not required but highly recommended - 确保正确的滚动和安全区域处理 | Ensures proper scrolling and safe area handling

