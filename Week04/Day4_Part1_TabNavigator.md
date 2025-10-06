# React Native入门 - 第28天：底部标签导航 (第一部分) | React Native Introduction - Day 28: Bottom Tab Navigator (Part 1)

## 学习目标 | Learning Objectives
- 理解底部标签导航的概念及其在移动应用中的作用 | Understand the concept of bottom tab navigation and its role in mobile applications
- 掌握如何安装和配置React Navigation的底部标签导航器 | Master how to install and configure React Navigation's Bottom Tab Navigator
- 学会创建多个标签页并添加图标 | Learn to create multiple tabs and add icons
- 理解标签导航器的自定义选项和样式配置 | Understand customization options and style configuration for tab navigators
- 掌握嵌套导航器的核心模式：在标签内嵌入堆栈导航 | Master the core pattern of nested navigators: embedding stack navigation within tabs
- 能够区分何时使用标签导航与堆栈导航 | Be able to distinguish when to use tab navigation versus stack navigation

## 详细内容 | Detailed Content

### 1. 底部标签导航概念与设置 | Bottom Tab Navigation Concepts & Setup (1小时 | 1 hour)

- **底部标签导航器的概念 | Bottom Tab Navigator Concept**

  **概念定义 | Concept Definition:**
  底部标签导航器（Bottom Tab Navigator）是React Navigation提供的一种导航模式，它在屏幕底部显示一组标签，允许用户快速在应用的不同主要部分之间切换。每个标签代表应用的一个独立功能区域，点击标签即可切换到对应的屏幕。这是移动应用中最常见的导航模式之一。

  The Bottom Tab Navigator is a navigation pattern provided by React Navigation that displays a set of tabs at the bottom of the screen, allowing users to quickly switch between different major sections of an app. Each tab represents an independent functional area of the application, and tapping a tab switches to the corresponding screen. This is one of the most common navigation patterns in mobile apps.

  **核心特征 | Key Characteristics:**
  - 持久性：标签栏在所有标签页中保持可见，提供一致的导航体验 | Persistence: The tab bar remains visible across all tab screens, providing consistent navigation experience
  - 平行结构：各标签页之间是平行关系，不是层级关系 | Parallel structure: Tab screens have a parallel relationship, not a hierarchical one
  - 状态保持：切换标签时，每个标签页的状态会被保留 | State preservation: When switching tabs, the state of each tab screen is preserved
  - 视觉提示：当前选中的标签通常会有不同的颜色或样式 | Visual feedback: The currently selected tab typically has a different color or style

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 底部标签导航器中的标签栏会在切换标签时消失吗？| Does the tab bar disappear when switching between tabs in a Bottom Tab Navigator?
     **答案 | Answer:** 否 | No - 标签栏保持持久可见，这是标签导航的核心特征 | The tab bar remains persistently visible, which is a core characteristic of tab navigation

  2. 当你从一个标签切换到另一个标签，然后再切换回来时，之前的标签页状态会保留吗？| When you switch from one tab to another and then switch back, is the previous tab's state preserved?
     **答案 | Answer:** 是 | Yes - React Navigation默认保持每个标签的状态，除非你明确卸载组件 | React Navigation preserves each tab's state by default, unless you explicitly unmount components

  3. 标签导航器适合用于展示应用的主要功能区域还是详细信息流？| Is the Tab Navigator suitable for displaying main functional areas of the app or detailed information flows?
     **答案 | Answer:** 主要功能区域 | Main functional areas - 标签导航用于应用的顶层结构（如首页、搜索、个人资料），而详细信息流应使用堆栈导航 | Tab navigation is for top-level app structure (like Home, Search, Profile), while detailed information flows should use stack navigation

  4. 在底部标签导航器中，各个标签页之间是层级关系还是平行关系？| In a Bottom Tab Navigator, are the tab screens in a hierarchical or parallel relationship?
     **答案 | Answer:** 平行关系 | Parallel relationship - 标签页之间处于同一层级，用户可以直接在它们之间切换，不存在"后退"到上一个标签的概念 | Tab screens are on the same level; users can switch directly between them without a concept of "going back" to a previous tab

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 安装底部标签导航器 | Installing Bottom Tab Navigator
  // 在终端运行 | Run in terminal:
  // npm install @react-navigation/bottom-tabs

  // 基础底部标签导航器设置 | Basic Bottom Tab Navigator setup
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { Text, View } from 'react-native';

  // 创建标签导航器实例 | Create tab navigator instance
  const Tab = createBottomTabNavigator();

  // 首页屏幕组件 | Home screen component
  function HomeScreen() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Home Screen</Text>
      </View>
    );
  }

  // 搜索屏幕组件 | Search screen component
  function SearchScreen() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Search Screen</Text>
      </View>
    );
  }

  // 个人资料屏幕组件 | Profile screen component
  function ProfileScreen() {
    return (
      <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
        <Text>Profile Screen</Text>
      </View>
    );
  }

  // App主组件 | Main App component
  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator>
          {/* 定义三个标签页 | Define three tabs */}
          <Tab.Screen name="Home" component={HomeScreen} />
          <Tab.Screen name="Search" component={SearchScreen} />
          <Tab.Screen name="Profile" component={ProfileScreen} />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码会创建几个标签？| How many tabs will this code create?
    **答案 | Answer:** 3个标签：Home、Search和Profile | 3 tabs: Home, Search, and Profile

  - 如果你点击"Search"标签，然后点击"Profile"标签，再点击回"Search"标签，SearchScreen组件会重新渲染吗（状态会重置吗）？| If you tap the "Search" tab, then the "Profile" tab, then back to "Search", will the SearchScreen component re-render (will its state reset)?
    **答案 | Answer:** SearchScreen会重新渲染，但其状态会保留（除非使用了unmountOnBlur选项） | SearchScreen will re-render, but its state will be preserved (unless using the unmountOnBlur option)

  **常见误区检查 | Common Misconception Checks:**
  - 底部标签导航器是否会自动处理屏幕间的层级关系和后退导航？| Does the Bottom Tab Navigator automatically handle hierarchical relationships and back navigation between screens?
    **答案 | Answer:** 否 | No - 标签导航器不处理层级关系。如果需要层级导航（如从列表到详情页），应在每个标签内嵌套堆栈导航器 | Tab Navigator doesn't handle hierarchical relationships. If you need hierarchical navigation (like from a list to a detail screen), you should nest a Stack Navigator inside each tab

  - 标签导航器和堆栈导航器可以互换使用吗？| Are Tab Navigators and Stack Navigators interchangeable?
    **答案 | Answer:** 否 | No - 它们服务于不同的导航需求。标签用于平行的顶层功能区域，堆栈用于层级性的内容流（如查看详情、编辑等） | They serve different navigation needs. Tabs are for parallel top-level functional areas, while stacks are for hierarchical content flows (like viewing details, editing, etc.)

- **标签导航器的安装与依赖 | Tab Navigator Installation & Dependencies**

  **概念定义 | Concept Definition:**
  在使用底部标签导航器之前，需要先安装React Navigation的核心包和底部标签导航器包。React Navigation采用模块化设计，不同类型的导航器作为独立的包发布，这样可以减小应用的bundle大小，只安装需要的导航器。

  Before using the Bottom Tab Navigator, you need to install React Navigation's core packages and the bottom tabs package. React Navigation uses a modular design where different types of navigators are published as separate packages, which helps reduce your app's bundle size by only installing the navigators you need.

  **核心特征 | Key Characteristics:**
  - 模块化：每种导航器是独立的npm包 | Modular: Each navigator type is a separate npm package
  - 依赖管理：底部标签导航器依赖于核心导航包 | Dependency management: Bottom Tab Navigator depends on core navigation packages
  - 版本兼容性：需确保所有React Navigation包的版本兼容 | Version compatibility: Must ensure all React Navigation packages have compatible versions

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 安装底部标签导航器前，是否需要先安装@react-navigation/native？| Do you need to install @react-navigation/native before installing the Bottom Tab Navigator?
     **答案 | Answer:** 是 | Yes - @react-navigation/native是核心包，所有导航器都依赖它 | @react-navigation/native is the core package that all navigators depend on

  2. 安装@react-navigation/bottom-tabs后，是否就可以直接使用底部标签导航器而不需要其他依赖？| After installing @react-navigation/bottom-tabs, can you directly use the Bottom Tab Navigator without other dependencies?
     **答案 | Answer:** 否 | No - 还需要核心包（@react-navigation/native）以及一些原生依赖（如react-native-screens） | You also need the core package (@react-navigation/native) and some native dependencies (like react-native-screens)

  3. 为什么React Navigation将不同的导航器拆分成独立的包？| Why does React Navigation split different navigators into separate packages?
     **答案 | Answer:** 减小bundle大小 | To reduce bundle size - 应用只需安装实际使用的导航器，避免包含不必要的代码 | Apps only need to install the navigators they actually use, avoiding unnecessary code

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # 第一步：安装核心导航包（如果还没安装）| Step 1: Install core navigation package (if not already installed)
  # 这是React Navigation的基础，必须先安装 | This is the foundation of React Navigation, must be installed first
  npm install @react-navigation/native

  # 第二步：安装原生依赖（使用Expo）| Step 2: Install native dependencies (using Expo)
  # 这些是React Navigation在原生层面需要的库 | These are libraries React Navigation needs at the native level
  expo install react-native-screens react-native-safe-area-context

  # 第三步：安装底部标签导航器包 | Step 3: Install Bottom Tab Navigator package
  # 这是本节课的核心包 | This is the core package for this lesson
  npm install @react-navigation/bottom-tabs

  # 注意：如果不使用Expo，原生依赖的安装需要额外的原生配置步骤
  # Note: If not using Expo, installing native dependencies requires additional native configuration steps
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果只安装了@react-navigation/bottom-tabs而没有安装@react-navigation/native，代码能运行吗？| If you only install @react-navigation/bottom-tabs without @react-navigation/native, will the code run?
    **答案 | Answer:** 否 | No - 会出现模块未找到的错误，因为底部标签导航器依赖核心包 | You'll get a module not found error because the Bottom Tab Navigator depends on the core package

  **常见误区检查 | Common Misconception Checks:**
  - 是否所有React Navigation包都包含在一个npm包中？| Are all React Navigation packages included in a single npm package?
    **答案 | Answer:** 否 | No - React Navigation采用模块化设计，每种导航器（Stack、Tab、Drawer等）都是独立的包，需要单独安装 | React Navigation uses a modular design; each navigator type (Stack, Tab, Drawer, etc.) is a separate package that needs to be installed individually

### 2. 创建多标签页与基础配置 | Creating Multiple Tabs & Basic Configuration (1小时 | 1 hour)

- **Tab.Navigator和Tab.Screen的使用 | Using Tab.Navigator and Tab.Screen**

  **概念定义 | Concept Definition:**
  `Tab.Navigator`是底部标签导航器的容器组件，它管理所有标签页的状态和切换逻辑。`Tab.Screen`定义每个单独的标签页，类似于堆栈导航器中的`Stack.Screen`。每个`Tab.Screen`需要一个`name`（标签的唯一标识符）和一个`component`（当该标签被选中时要渲染的组件）。

  `Tab.Navigator` is the container component for the Bottom Tab Navigator that manages the state and switching logic of all tabs. `Tab.Screen` defines each individual tab, similar to `Stack.Screen` in Stack Navigator. Each `Tab.Screen` requires a `name` (unique identifier for the tab) and a `component` (the component to render when that tab is selected).

  **核心特征 | Key Characteristics:**
  - 声明式配置：使用JSX声明标签页结构，而不是命令式代码 | Declarative configuration: Uses JSX to declare tab structure instead of imperative code
  - name属性的唯一性：每个Tab.Screen的name必须在同一导航器中唯一 | Uniqueness of name prop: Each Tab.Screen's name must be unique within the same navigator
  - component属性：接受一个React组件，而不是组件实例 | component prop: Accepts a React component, not a component instance
  - 标签顺序：Tab.Screen在代码中的顺序决定了标签栏中标签的显示顺序 | Tab order: The order of Tab.Screen in code determines the display order of tabs in the tab bar

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Tab.Screen的component属性应该传入什么？| What should be passed to the component prop of Tab.Screen?
     **答案 | Answer:** React组件（函数或类） | A React component (function or class) - 传入组件定义本身，不是<HomeScreen />这样的实例 | Pass the component definition itself, not an instance like <HomeScreen />

  2. 两个Tab.Screen可以有相同的name吗？| Can two Tab.Screens have the same name?
     **答案 | Answer:** 否 | No - name是标签的唯一标识符，在同一导航器中必须唯一 | name is the unique identifier for the tab and must be unique within the same navigator

  3. 如果你想改变标签在标签栏中的显示顺序，应该怎么做？| If you want to change the display order of tabs in the tab bar, what should you do?
     **答案 | Answer:** 改变Tab.Screen组件在代码中的顺序 | Change the order of Tab.Screen components in the code - React Navigation按照它们在代码中出现的顺序显示标签 | React Navigation displays tabs in the order they appear in the code

  4. Tab.Navigator可以不包含任何Tab.Screen吗？| Can Tab.Navigator exist without any Tab.Screen?
     **答案 | Answer:** 否 | No - 没有Tab.Screen的Tab.Navigator没有意义，至少需要一个标签页 | A Tab.Navigator without Tab.Screen is meaningless; at least one tab is required

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { Text, View, StyleSheet } from 'react-native';

  // 创建标签导航器 | Create tab navigator
  const Tab = createBottomTabNavigator();

  // 首页屏幕 | Home screen
  function HomeScreen() {
    return (
      <View style={styles.screenContainer}>
        <Text style={styles.screenText}>Home Screen</Text>
        <Text>欢迎来到首页 | Welcome to Home</Text>
      </View>
    );
  }

  // 搜索屏幕 | Search screen
  function SearchScreen() {
    return (
      <View style={styles.screenContainer}>
        <Text style={styles.screenText}>Search Screen</Text>
        <Text>在这里搜索内容 | Search for content here</Text>
      </View>
    );
  }

  // 通知屏幕 | Notifications screen
  function NotificationsScreen() {
    return (
      <View style={styles.screenContainer}>
        <Text style={styles.screenText}>Notifications</Text>
        <Text>查看你的通知 | Check your notifications</Text>
      </View>
    );
  }

  // 个人资料屏幕 | Profile screen
  function ProfileScreen() {
    return (
      <View style={styles.screenContainer}>
        <Text style={styles.screenText}>Profile Screen</Text>
        <Text>管理你的个人资料 | Manage your profile</Text>
      </View>
    );
  }

  // 主应用组件 | Main App component
  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator>
          {/* 按顺序定义四个标签页 | Define four tabs in order */}
          <Tab.Screen
            name="Home"           // 标签的唯一标识符 | Unique identifier for the tab
            component={HomeScreen} // 要渲染的组件 | Component to render
          />
          <Tab.Screen
            name="Search"
            component={SearchScreen}
          />
          <Tab.Screen
            name="Notifications"
            component={NotificationsScreen}
          />
          <Tab.Screen
            name="Profile"
            component={ProfileScreen}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  const styles = StyleSheet.create({
    screenContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    screenText: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码创建了几个标签？| How many tabs does this code create?
    **答案 | Answer:** 4个标签 | 4 tabs - Home、Search、Notifications和Profile | Home, Search, Notifications, and Profile

  - 如果将Profile的Tab.Screen移到代码最前面，Profile标签会出现在标签栏的哪个位置？| If you move the Profile Tab.Screen to the front of the code, where will the Profile tab appear in the tab bar?
    **答案 | Answer:** 最左边/第一个位置 | Leftmost/first position - 标签的显示顺序由Tab.Screen在代码中的顺序决定 | The display order of tabs is determined by the order of Tab.Screen in the code

  **常见误区检查 | Common Misconception Checks:**
  - component属性应该传入<HomeScreen />还是HomeScreen？| Should the component prop receive <HomeScreen /> or HomeScreen?
    **答案 | Answer:** HomeScreen（组件本身，不带尖括号） | HomeScreen (the component itself, without angle brackets) - 传入组件的引用，React Navigation会在适当时机渲染它 | Pass the component reference; React Navigation will render it at the appropriate time

  - 标签的name是否会直接显示在标签栏上？| Does the tab's name directly appear in the tab bar?
    **答案 | Answer:** 是的，默认情况下 | Yes, by default - 但可以通过options的title或tabBarLabel属性自定义显示的文本 | But you can customize the displayed text via the title or tabBarLabel option

- **标签导航器的初始路由配置 | Initial Route Configuration for Tab Navigator**

  **概念定义 | Concept Definition:**
  `initialRouteName`是Tab.Navigator的一个属性，用于指定应用首次加载时应该显示哪个标签页。如果不指定，默认会显示第一个Tab.Screen定义的标签。这在你希望用户首次进入应用时看到特定标签时很有用。

  `initialRouteName` is a prop of Tab.Navigator that specifies which tab should be displayed when the app first loads. If not specified, it defaults to the first Tab.Screen defined. This is useful when you want users to see a specific tab when they first enter the app.

  **核心特征 | Key Characteristics:**
  - 仅影响首次加载：只在应用首次渲染导航器时生效 | Only affects first load: Only takes effect when the navigator is first rendered
  - 必须匹配已存在的Tab.Screen name：值必须是某个Tab.Screen的name | Must match an existing Tab.Screen name: The value must be the name of a Tab.Screen
  - 状态保持后不再生效：一旦用户切换了标签，再次返回应用时会显示上次选中的标签（如果启用了状态持久化） | Doesn't apply after state persistence: Once the user switches tabs, returning to the app will show the last selected tab (if state persistence is enabled)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果不设置initialRouteName，Tab.Navigator会显示哪个标签？| If you don't set initialRouteName, which tab will Tab.Navigator display?
     **答案 | Answer:** 第一个定义的Tab.Screen | The first defined Tab.Screen - 默认行为是显示代码中第一个出现的标签 | Default behavior is to show the first tab that appears in the code

  2. initialRouteName的值可以是任意字符串吗？| Can initialRouteName be any arbitrary string?
     **答案 | Answer:** 否 | No - 必须匹配某个Tab.Screen的name属性，否则会报错 | It must match a Tab.Screen's name prop, otherwise it will throw an error

  3. 如果用户打开应用，切换到Profile标签，然后关闭应用，再次打开时会显示哪个标签？| If a user opens the app, switches to the Profile tab, closes the app, and reopens it, which tab will be displayed?
     **答案 | Answer:** 取决于是否启用了状态持久化 | Depends on whether state persistence is enabled - 如果启用，显示Profile；如果没启用，显示initialRouteName或第一个标签 | If enabled, it shows Profile; if not, it shows initialRouteName or the first tab

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

  const Tab = createBottomTabNavigator();

  // 屏幕组件定义（省略实现细节）| Screen components (implementation details omitted)
  function HomeScreen() { /* ... */ }
  function SearchScreen() { /* ... */ }
  function ProfileScreen() { /* ... */ }

  export default function App() {
    return (
      <NavigationContainer>
        {/* 设置Profile为初始显示的标签 | Set Profile as the initially displayed tab */}
        <Tab.Navigator initialRouteName="Profile">
          <Tab.Screen name="Home" component={HomeScreen} />
          <Tab.Screen name="Search" component={SearchScreen} />
          <Tab.Screen name="Profile" component={ProfileScreen} />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  // 对比示例：不设置initialRouteName | Comparison: without setting initialRouteName
  export function AppWithoutInitial() {
    return (
      <NavigationContainer>
        {/* 默认会显示Home标签（第一个定义的）| Will display Home tab by default (first defined) */}
        <Tab.Navigator>
          <Tab.Screen name="Home" component={HomeScreen} />
          <Tab.Screen name="Search" component={SearchScreen} />
          <Tab.Screen name="Profile" component={ProfileScreen} />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在第一个示例中，用户首次打开应用时会看到哪个屏幕？| In the first example, which screen will the user see when first opening the app?
    **答案 | Answer:** ProfileScreen - 因为initialRouteName设置为"Profile" | Because initialRouteName is set to "Profile"

  - 如果将initialRouteName设置为"Settings"，但没有定义名为"Settings"的Tab.Screen，会发生什么？| If you set initialRouteName to "Settings" but don't define a Tab.Screen named "Settings", what will happen?
    **答案 | Answer:** 应用会报错 | The app will throw an error - React Navigation会警告找不到名为"Settings"的路由 | React Navigation will warn that it can't find a route named "Settings"

  **常见误区检查 | Common Misconception Checks:**
  - initialRouteName是否会影响标签在标签栏中的显示顺序？| Does initialRouteName affect the display order of tabs in the tab bar?
    **答案 | Answer:** 否 | No - initialRouteName只决定首次显示哪个标签，不影响标签栏中标签的排列顺序 | initialRouteName only determines which tab is displayed first, not the arrangement order of tabs in the tab bar

### 3. 添加图标与自定义标签外观 | Adding Icons & Customizing Tab Appearance (1.5小时 | 1.5 hours)

- **使用@expo/vector-icons添加标签图标 | Adding Tab Icons with @expo/vector-icons**

  **概念定义 | Concept Definition:**
  在移动应用中，标签通常会配有图标以提供视觉提示，帮助用户快速识别不同的功能区域。React Navigation允许通过`options`属性的`tabBarIcon`选项为每个标签添加图标。`@expo/vector-icons`是一个包含多种图标库（如Ionicons、FontAwesome、MaterialIcons等）的包，在Expo项目中已预装，非常适合用于标签图标。

  In mobile apps, tabs typically include icons to provide visual cues that help users quickly identify different functional areas. React Navigation allows adding icons to each tab through the `tabBarIcon` option in the `options` prop. `@expo/vector-icons` is a package containing multiple icon libraries (like Ionicons, FontAwesome, MaterialIcons, etc.), pre-installed in Expo projects, making it ideal for tab icons.

  **核心特征 | Key Characteristics:**
  - tabBarIcon是一个函数：接收包含color、size、focused等属性的对象作为参数 | tabBarIcon is a function: Receives an object with properties like color, size, focused as parameters
  - 动态图标：可以根据focused状态显示不同的图标或颜色 | Dynamic icons: Can display different icons or colors based on focused state
  - 多图标库支持：@expo/vector-icons包含Ionicons、MaterialIcons、FontAwesome等多种图标库 | Multiple icon libraries: @expo/vector-icons includes Ionicons, MaterialIcons, FontAwesome, and more
  - 自动颜色管理：color参数会自动传入活动/非活动状态的颜色 | Automatic color management: The color parameter automatically receives active/inactive state colors

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. tabBarIcon属性应该接收一个组件还是一个函数？| Should the tabBarIcon prop receive a component or a function?
     **答案 | Answer:** 函数 | A function - 该函数返回一个图标组件，并接收{focused, color, size}等参数 | The function returns an icon component and receives parameters like {focused, color, size}

  2. tabBarIcon函数的focused参数表示什么？| What does the focused parameter in the tabBarIcon function represent?
     **答案 | Answer:** 该标签是否被选中 | Whether the tab is selected - focused为true表示当前标签被选中，false表示未选中 | focused is true when the tab is currently selected, false when not selected

  3. 为什么tabBarIcon需要是一个函数而不是直接的组件实例？| Why does tabBarIcon need to be a function instead of a direct component instance?
     **答案 | Answer:** 为了接收动态参数 | To receive dynamic parameters - React Navigation需要根据标签状态（选中/未选中）传入不同的color和focused值 | React Navigation needs to pass different color and focused values based on tab state (selected/not selected)

  4. 在Expo项目中，使用@expo/vector-icons前需要单独安装吗？| In an Expo project, do you need to install @expo/vector-icons separately?
     **答案 | Answer:** 否 | No - Expo项目已经预装了@expo/vector-icons | Expo projects come with @expo/vector-icons pre-installed

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { Ionicons } from '@expo/vector-icons'; // 导入Ionicons图标库 | Import Ionicons library
  import { Text, View } from 'react-native';

  const Tab = createBottomTabNavigator();

  // 屏幕组件（简化版）| Screen components (simplified)
  function HomeScreen() {
    return <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Text>Home</Text>
    </View>;
  }

  function SearchScreen() {
    return <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Text>Search</Text>
    </View>;
  }

  function NotificationsScreen() {
    return <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Text>Notifications</Text>
    </View>;
  }

  function ProfileScreen() {
    return <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Text>Profile</Text>
    </View>;
  }

  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            // 全局标签栏样式配置 | Global tab bar style configuration
            tabBarActiveTintColor: 'tomato', // 选中标签的颜色 | Color for selected tab
            tabBarInactiveTintColor: 'gray', // 未选中标签的颜色 | Color for unselected tabs
          }}
        >
          <Tab.Screen
            name="Home"
            component={HomeScreen}
            options={{
              // tabBarIcon接收一个函数 | tabBarIcon receives a function
              tabBarIcon: ({ focused, color, size }) => {
                // 根据focused状态选择不同的图标 | Choose different icons based on focused state
                const iconName = focused ? 'home' : 'home-outline';
                return <Ionicons name={iconName} size={size} color={color} />;
              },
            }}
          />

          <Tab.Screen
            name="Search"
            component={SearchScreen}
            options={{
              tabBarIcon: ({ focused, color, size }) => {
                const iconName = focused ? 'search' : 'search-outline';
                return <Ionicons name={iconName} size={size} color={color} />;
              },
            }}
          />

          <Tab.Screen
            name="Notifications"
            component={NotificationsScreen}
            options={{
              tabBarIcon: ({ focused, color, size }) => {
                // 也可以根据focused改变颜色或其他属性 | Can also change color or other props based on focused
                const iconName = focused ? 'notifications' : 'notifications-outline';
                return <Ionicons name={iconName} size={size} color={color} />;
              },
            }}
          />

          <Tab.Screen
            name="Profile"
            component={ProfileScreen}
            options={{
              tabBarIcon: ({ focused, color, size }) => {
                const iconName = focused ? 'person' : 'person-outline';
                return <Ionicons name={iconName} size={size} color={color} />;
              },
            }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  // 高级示例：使用MaterialIcons和自定义图标尺寸 | Advanced example: Using MaterialIcons with custom icon size
  import { MaterialIcons } from '@expo/vector-icons';

  export function AppWithMaterialIcons() {
    return (
      <NavigationContainer>
        <Tab.Navigator>
          <Tab.Screen
            name="Dashboard"
            component={HomeScreen}
            options={{
              tabBarIcon: ({ color, focused }) => (
                // 可以自定义size，不使用传入的size参数 | Can customize size instead of using the passed size parameter
                <MaterialIcons
                  name="dashboard"
                  size={focused ? 28 : 24} // 选中时图标更大 | Larger icon when selected
                  color={color}
                />
              ),
            }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当用户点击"Home"标签时，tabBarIcon函数的focused参数值是什么？| When a user taps the "Home" tab, what is the value of the focused parameter in the tabBarIcon function?
    **答案 | Answer:** true - 因为Home标签被选中了 | Because the Home tab is selected

  - 如果不使用focused参数，所有标签的图标会有什么问题？| If you don't use the focused parameter, what problem will all tab icons have?
    **答案 | Answer:** 无法区分选中和未选中状态 | Unable to distinguish between selected and unselected states - 所有图标看起来都一样，用户无法知道当前在哪个标签 | All icons look the same, users can't tell which tab they're on

  - color参数的值从哪里来？| Where does the color parameter's value come from?
    **答案 | Answer:** 从Tab.Navigator的screenOptions中的tabBarActiveTintColor和tabBarInactiveTintColor | From tabBarActiveTintColor and tabBarInactiveTintColor in Tab.Navigator's screenOptions - React Navigation根据标签状态自动传入相应的颜色 | React Navigation automatically passes the appropriate color based on tab state

  **常见误区检查 | Common Misconception Checks:**
  - tabBarIcon可以直接写成<Ionicons name="home" />吗？| Can tabBarIcon be written directly as <Ionicons name="home" />?
    **答案 | Answer:** 否 | No - 必须是一个函数，返回图标组件。直接写组件会导致无法接收focused、color等动态参数 | Must be a function that returns the icon component. Writing the component directly prevents receiving dynamic parameters like focused and color

  - 所有标签是否必须使用同一个图标库（如都用Ionicons或都用MaterialIcons）？| Must all tabs use the same icon library (like all Ionicons or all MaterialIcons)?
    **答案 | Answer:** 否 | No - 可以在不同的标签中混合使用不同的图标库，只要它们都从@expo/vector-icons导入 | You can mix different icon libraries across tabs as long as they're all imported from @expo/vector-icons

- **自定义标签栏外观（颜色、样式、标签）| Customizing Tab Bar Appearance (Colors, Styles, Labels)**

  **概念定义 | Concept Definition:**
  React Navigation的底部标签导航器提供了丰富的自定义选项，允许你改变标签栏的外观和行为。这些选项可以在`Tab.Navigator`的`screenOptions`属性中全局设置（应用于所有标签），也可以在单个`Tab.Screen`的`options`属性中单独设置（仅应用于该标签）。常用的自定义选项包括：颜色（activeTintColor、inactiveTintColor）、标签文本（tabBarLabel）、徽章（tabBarBadge）、样式（tabBarStyle）等。

  React Navigation's Bottom Tab Navigator provides rich customization options allowing you to change the appearance and behavior of the tab bar. These options can be set globally in the `screenOptions` prop of `Tab.Navigator` (applying to all tabs), or set individually in a `Tab.Screen`'s `options` prop (applying only to that tab). Common customization options include: colors (activeTintColor, inactiveTintColor), label text (tabBarLabel), badges (tabBarBadge), styles (tabBarStyle), etc.

  **核心特征 | Key Characteristics:**
  - 两级配置：screenOptions用于全局配置，options用于单个标签的配置 | Two-level configuration: screenOptions for global config, options for individual tab config
  - 选项优先级：单个Tab.Screen的options会覆盖Tab.Navigator的screenOptions | Option priority: Individual Tab.Screen's options override Tab.Navigator's screenOptions
  - 动态配置：options可以是函数，根据导航状态动态返回配置对象 | Dynamic configuration: options can be a function that dynamically returns a config object based on navigation state
  - 样式对象：tabBarStyle、tabBarLabelStyle等接收标准的React Native样式对象 | Style objects: tabBarStyle, tabBarLabelStyle, etc. accept standard React Native style objects

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果在screenOptions中设置tabBarActiveTintColor为'blue'，在某个Tab.Screen的options中设置为'red'，该标签选中时的颜色是什么？| If you set tabBarActiveTintColor to 'blue' in screenOptions and 'red' in a Tab.Screen's options, what color will that tab be when selected?
     **答案 | Answer:** 'red' - 单个Tab.Screen的options优先级更高，会覆盖screenOptions | 'red' - Individual Tab.Screen's options have higher priority and override screenOptions

  2. tabBarLabel和title有什么区别？| What's the difference between tabBarLabel and title?
     **答案 | Answer:** tabBarLabel控制标签栏中显示的文本，title控制屏幕顶部标题栏的文本 | tabBarLabel controls the text shown in the tab bar, title controls the text in the screen header

  3. 如果你想隐藏某个标签的标签文本（只显示图标），应该怎么做？| If you want to hide a tab's label text (only show the icon), what should you do?
     **答案 | Answer:** 设置tabBarShowLabel: false | Set tabBarShowLabel: false - 或者设置tabBarLabel: () => null | Or set tabBarLabel: () => null

  4. tabBarBadge用于显示什么？| What is tabBarBadge used to display?
     **答案 | Answer:** 徽章/通知数字 | Badge/notification count - 通常用于显示未读消息数量等，如"5"或"new" | Typically used to show unread message count, like "5" or "new"

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { Ionicons } from '@expo/vector-icons';
  import { Text, View } from 'react-native';

  const Tab = createBottomTabNavigator();

  // 屏幕组件（省略实现）| Screen components (implementation omitted)
  function HomeScreen() { return <View><Text>Home</Text></View>; }
  function SearchScreen() { return <View><Text>Search</Text></View>; }
  function NotificationsScreen() { return <View><Text>Notifications</Text></View>; }
  function ProfileScreen() { return <View><Text>Profile</Text></View>; }

  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            // 全局配置：应用于所有标签 | Global config: applies to all tabs
            tabBarActiveTintColor: '#e91e63',    // 选中标签的颜色 | Color for selected tab
            tabBarInactiveTintColor: '#9e9e9e',  // 未选中标签的颜色 | Color for unselected tabs
            tabBarStyle: {                        // 标签栏容器样式 | Tab bar container style
              backgroundColor: '#ffffff',         // 背景颜色 | Background color
              borderTopWidth: 1,                  // 顶部边框宽度 | Top border width
              borderTopColor: '#e0e0e0',          // 顶部边框颜色 | Top border color
              height: 60,                         // 标签栏高度 | Tab bar height
              paddingBottom: 5,                   // 底部内边距 | Bottom padding
            },
            tabBarLabelStyle: {                   // 标签文本样式 | Tab label text style
              fontSize: 12,                       // 字体大小 | Font size
              fontWeight: '600',                  // 字体粗细 | Font weight
            },
            tabBarIconStyle: {                    // 图标样式 | Icon style
              marginTop: 5,                       // 顶部外边距 | Top margin
            },
          }}
        >
          <Tab.Screen
            name="Home"
            component={HomeScreen}
            options={{
              // 单个标签的配置 | Individual tab configuration
              tabBarLabel: '首页',                // 自定义标签文本（中文）| Custom label text (Chinese)
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="home" size={size} color={color} />
              ),
            }}
          />

          <Tab.Screen
            name="Search"
            component={SearchScreen}
            options={{
              tabBarLabel: '搜索',                // Custom label
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="search" size={size} color={color} />
              ),
            }}
          />

          <Tab.Screen
            name="Notifications"
            component={NotificationsScreen}
            options={{
              tabBarLabel: '通知',                // Custom label
              tabBarBadge: 3,                     // 显示徽章数字3 | Show badge with number 3
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="notifications" size={size} color={color} />
              ),
            }}
          />

          <Tab.Screen
            name="Profile"
            component={ProfileScreen}
            options={{
              tabBarLabel: '我的',                // Custom label
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="person" size={size} color={color} />
              ),
              // 覆盖全局颜色：该标签选中时显示不同的颜色 | Override global color: different color when selected
              tabBarActiveTintColor: '#2196f3',
            }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  // 高级示例：动态options | Advanced example: dynamic options
  export function AppWithDynamicOptions() {
    const [notificationCount, setNotificationCount] = React.useState(5);

    return (
      <NavigationContainer>
        <Tab.Navigator>
          <Tab.Screen
            name="Notifications"
            component={NotificationsScreen}
            options={{
              // options可以是函数，用于动态配置 | options can be a function for dynamic config
              tabBarBadge: notificationCount > 0 ? notificationCount : null,
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="notifications" size={size} color={color} />
              ),
            }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  // 隐藏标签文本的示例 | Example of hiding tab labels
  export function AppWithIconsOnly() {
    return (
      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            tabBarShowLabel: false, // 全局隐藏所有标签文本 | Globally hide all tab labels
          }}
        >
          <Tab.Screen
            name="Home"
            component={HomeScreen}
            options={{
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="home" size={size} color={color} />
              ),
            }}
          />
          {/* 其他标签... | Other tabs... */}
        </Tab.Navigator>
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在第一个示例中，"Notifications"标签被选中时的颜色是什么？| In the first example, what color is the "Notifications" tab when selected?
    **答案 | Answer:** '#e91e63'（粉红色）| '#e91e63' (pink) - 使用全局的tabBarActiveTintColor | Uses the global tabBarActiveTintColor

  - "Profile"标签被选中时的颜色是什么？| What color is the "Profile" tab when selected?
    **答案 | Answer:** '#2196f3'（蓝色）| '#2196f3' (blue) - 因为它在options中覆盖了全局的tabBarActiveTintColor | Because it overrides the global tabBarActiveTintColor in its options

  - 如果将tabBarShowLabel设置为false，标签栏会显示什么？| If you set tabBarShowLabel to false, what will the tab bar display?
    **答案 | Answer:** 只显示图标，不显示文本标签 | Only icons, no text labels - 这种设计常见于图标含义非常明确的应用 | This design is common in apps where icon meanings are very clear

  **常见误区检查 | Common Misconception Checks:**
  - screenOptions和options有什么区别？| What's the difference between screenOptions and options?
    **答案 | Answer:** screenOptions用于Tab.Navigator，设置所有标签的默认配置；options用于Tab.Screen，设置单个标签的特定配置 | screenOptions is for Tab.Navigator and sets default config for all tabs; options is for Tab.Screen and sets specific config for individual tabs

  - tabBarLabel是否必须是字符串？| Does tabBarLabel have to be a string?
    **答案 | Answer:** 否 | No - 可以是字符串、函数（返回React元素）或React元素，函数形式可以接收{focused, color}等参数 | Can be a string, function (returning React element), or React element; function form can receive parameters like {focused, color}

