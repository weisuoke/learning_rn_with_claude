# React Native入门 - 第27天：深度链接与通用链接 | React Native Introduction - Day 27: Deep Linking & Universal Links

## 学习目标 | Learning Objectives
- 理解深度链接(Deep Linking)的概念和在移动应用中的重要性 | Understand the concept of deep linking and its importance in mobile applications
- 掌握在React Navigation中配置深度链接的方法 | Master how to configure deep linking in React Navigation
- 学会定义URL路径并将其映射到应用屏幕 | Learn to define URL paths and map them to app screens
- 能够解析URL参数并传递给目标屏幕组件 | Be able to parse URL parameters and pass them to target screen components
- 了解iOS通用链接和Android应用链接的基础概念 | Understand basic concepts of iOS Universal Links and Android App Links
- 掌握在开发环境中测试深度链接的技巧 | Master techniques for testing deep links in development environment

## 详细内容 | Detailed Content

### 1. 深度链接基础概念 | Deep Linking Fundamentals (1小时 | 1 hour)

- **什么是深度链接 | What is Deep Linking**

  **概念定义 | Concept Definition:**
  深度链接(Deep Linking)是一种允许用户通过URL直接导航到移动应用内特定屏幕或内容的技术，而不是仅仅打开应用的首页。它类似于Web开发中的路由系统，但需要处理移动平台的特殊性。

  Deep Linking is a technology that allows users to navigate directly to specific screens or content within a mobile app via URLs, rather than just opening the app's homepage. It's similar to the routing system in web development but needs to handle mobile platform specifics.

  **核心特征 | Key Characteristics:**
  - 打破应用孤岛：允许从外部(浏览器、邮件、其他应用)直接跳转到应用内部内容 | Breaks app silos: allows jumping from external sources (browser, email, other apps) directly to app content
  - URL映射机制：将URL结构映射到应用的导航结构 | URL mapping mechanism: maps URL structure to app navigation structure
  - 参数传递能力：通过URL参数向目标屏幕传递数据 | Parameter passing capability: passes data to target screens via URL parameters
  - 提升用户体验：减少用户到达目标内容的点击次数 | Enhances user experience: reduces clicks needed to reach target content
  - 营销和分享利器：支持通过链接分享特定内容，追踪营销效果 | Marketing and sharing tool: supports sharing specific content via links and tracking marketing effectiveness

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 深度链接能直接打开应用内的特定页面吗？| Can deep links directly open specific pages within an app?
     **答案 | Answer:** 是 | Yes - 这是深度链接的核心功能，区别于仅打开应用首页的普通链接 | This is the core function of deep linking, different from regular links that only open the app homepage
  2. 深度链接只能在应用内部使用吗？| Can deep links only be used within the app?
     **答案 | Answer:** 否 | No - 深度链接的价值在于可以从外部(浏览器、邮件、社交媒体等)触发 | The value of deep links is that they can be triggered from external sources (browsers, emails, social media, etc.)
  3. 深度链接和Web开发中的路由系统有相似之处吗？| Are deep links similar to routing systems in web development?
     **答案 | Answer:** 是 | Yes - 两者都是将URL路径映射到特定内容，但深度链接需要额外处理移动平台的应用启动和状态管理 | Both map URL paths to specific content, but deep linking requires additional handling of app launch and state management on mobile platforms
  4. 用户点击深度链接时，应用必须已经在运行吗？| Must the app be running when a user clicks a deep link?
     **答案 | Answer:** 否 | No - 深度链接可以启动未运行的应用，并导航到指定屏幕 | Deep links can launch apps that aren't running and navigate to specified screens

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 深度链接示例场景 | Deep linking example scenario
  // 用户在浏览器中点击: myapp://product/12345
  // 应用打开并直接显示产品ID为12345的详情页

  // User clicks in browser: myapp://product/12345
  // App opens and directly shows product detail page with ID 12345

  // 这需要以下组件配合 | This requires coordination of:
  // 1. URL Scheme配置 (myapp://)
  // 2. 路径映射配置 (product/:id)
  // 3. 参数解析和传递 (id: 12345)
  // 4. 导航到目标屏幕 (ProductDetail screen)
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果用户点击链接`myapp://settings/notifications`，应用应该打开哪个屏幕？| If a user clicks `myapp://settings/notifications`, which screen should the app open?
    **答案 | Answer:** 设置中的通知页面，而不是设置首页 | The notifications page within settings, not the settings homepage
  - 深度链接中的参数(如`/product/123`)如何传递给React组件？| How are parameters in deep links (like `/product/123`) passed to React components?
    **答案 | Answer:** 通过React Navigation的路由参数系统(route.params) | Through React Navigation's route params system (route.params)

  **常见误区检查 | Common Misconception Checks:**
  - 深度链接只是为了方便开发者调试，对用户体验没有实际帮助 | Deep links are just for developer debugging and don't actually help user experience
    **答案 | Answer:** 错误 - 深度链接极大提升用户体验，允许用户直接访问感兴趣的内容，是现代移动应用的标准功能 | Incorrect - Deep links greatly enhance user experience by allowing direct access to content of interest and are a standard feature of modern mobile apps
  - 深度链接和Web URL完全相同，可以直接复用Web路由配置 | Deep links are exactly the same as web URLs and can directly reuse web routing configuration
    **答案 | Answer:** 部分正确 - 虽然概念相似，但深度链接需要考虑应用冷启动、状态恢复、平台差异等移动特有问题 | Partially correct - While concepts are similar, deep linking needs to consider app cold start, state restoration, platform differences, and other mobile-specific issues

- **深度链接的三种类型 | Three Types of Deep Links**

  **概念定义 | Concept Definition:**
  移动应用中有三种主要的深度链接类型，每种有不同的技术实现和使用场景：URL Scheme、Universal Links (iOS) 和 App Links (Android)。

  There are three main types of deep links in mobile apps, each with different technical implementations and use cases: URL Scheme, Universal Links (iOS), and App Links (Android).

  **核心特征 | Key Characteristics:**
  - **URL Scheme**：自定义协议(如`myapp://`)，最基础的深度链接方式 | Custom protocol (like `myapp://`), the most basic deep linking method
    - 优点：实现简单，跨平台一致 | Pros: Simple implementation, cross-platform consistency
    - 缺点：需要用户确认打开应用，可能被其他应用劫持协议 | Cons: Requires user confirmation to open app, protocol can be hijacked by other apps
  - **Universal Links (iOS)**：使用HTTPS域名，无需确认直接打开应用 | Uses HTTPS domains, opens app directly without confirmation
    - 优点：用户体验好，安全性高，如果应用未安装则打开网页 | Pros: Good UX, high security, opens webpage if app not installed
    - 缺点：需要服务器配置和Apple验证 | Cons: Requires server configuration and Apple verification
  - **App Links (Android)**：Android版本的Universal Links | Android version of Universal Links
    - 优点：类似Universal Links的优势 | Pros: Similar advantages to Universal Links
    - 缺点：需要服务器配置和域名验证 | Cons: Requires server configuration and domain verification

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. URL Scheme链接(如`myapp://`)和HTTPS链接(如`https://example.com`)在用户体验上有区别吗？| Is there a UX difference between URL Scheme links (like `myapp://`) and HTTPS links (like `https://example.com`)?
     **答案 | Answer:** 是 | Yes - URL Scheme需要用户确认是否打开应用，而Universal/App Links可以直接打开，体验更流畅 | URL Scheme requires user confirmation to open the app, while Universal/App Links can open directly for a smoother experience
  2. 如果用户设备上没有安装应用，Universal Links会发生什么？| What happens with Universal Links if the app isn't installed on the user's device?
     **答案 | Answer:** 会在浏览器中打开对应的网页 | Opens the corresponding webpage in the browser - 这是Universal Links的优势，提供降级方案 | This is an advantage of Universal Links, providing a fallback
  3. 在开发阶段，哪种深度链接类型最容易测试？| Which type of deep link is easiest to test during development?
     **答案 | Answer:** URL Scheme | URL Scheme - 不需要服务器配置和域名验证，可以立即测试 | Doesn't require server configuration or domain verification, can be tested immediately
  4. 多个应用可以注册相同的URL Scheme吗？| Can multiple apps register the same URL Scheme?
     **答案 | Answer:** 可以 | Yes - 这是URL Scheme的安全隐患，系统无法保证打开哪个应用 | This is a security concern with URL Schemes, the system can't guarantee which app opens

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 三种深度链接类型的URL示例 | URL examples for three types of deep links

  // 1. URL Scheme (开发阶段最常用) | URL Scheme (most common in development)
  const urlSchemeLink = "myapp://product/123";
  // 特点：自定义协议，需要用户确认 | Characteristic: custom protocol, requires user confirmation

  // 2. Universal Link (iOS生产环境) | Universal Link (iOS production)
  const universalLink = "https://myapp.com/product/123";
  // 特点：HTTPS链接，无缝打开应用或网页 | Characteristic: HTTPS link, seamlessly opens app or web

  // 3. App Link (Android生产环境) | App Link (Android production)
  const appLink = "https://myapp.com/product/123";
  // 特点：与Universal Link类似，但针对Android | Characteristic: similar to Universal Link but for Android

  // 注意：Universal Links和App Links使用相同的HTTPS URL
  // 但需要在各自平台进行不同的配置
  // Note: Universal Links and App Links use the same HTTPS URL
  // but require different configurations on each platform
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 本日学习中我们主要实践哪种类型的深度链接？| Which type of deep link will we primarily practice today?
    **答案 | Answer:** URL Scheme - 因为它不需要服务器配置，适合学习和开发阶段 | Because it doesn't require server configuration, suitable for learning and development
  - 如果要在生产环境提供最佳用户体验，应该使用哪种深度链接？| For the best user experience in production, which type of deep link should be used?
    **答案 | Answer:** Universal Links (iOS) 和 App Links (Android) | Universal Links (iOS) and App Links (Android)

  **常见误区检查 | Common Misconception Checks:**
  - Universal Links需要应用先安装才能工作 | Universal Links require the app to be installed first to work
    **答案 | Answer:** 错误 - Universal Links的优势之一是应用未安装时会打开网页，提供下载选项 | Incorrect - One advantage of Universal Links is they open a webpage when the app isn't installed, providing download options
  - URL Scheme是过时技术，不应该再使用 | URL Scheme is outdated technology and shouldn't be used anymore
    **答案 | Answer:** 错误 - URL Scheme在开发、测试和某些特定场景(如应用间通信)仍然有价值 | Incorrect - URL Scheme still has value in development, testing, and certain scenarios like inter-app communication

### 2. React Navigation深度链接配置 | React Navigation Deep Linking Configuration (1小时 | 1 hour)

- **配置linking对象 | Configuring the linking Object**

  **概念定义 | Concept Definition:**
  React Navigation通过在`NavigationContainer`中配置`linking`属性来启用深度链接功能。这个配置对象定义了URL结构到导航结构的映射关系。

  React Navigation enables deep linking by configuring the `linking` prop in `NavigationContainer`. This configuration object defines the mapping between URL structure and navigation structure.

  **核心特征 | Key Characteristics:**
  - prefixes数组：定义应用接受的URL前缀(如`['myapp://', 'https://myapp.com']`) | prefixes array: defines URL prefixes the app accepts (like `['myapp://', 'https://myapp.com']`)
  - config对象：定义屏幕路径映射，将URL路径映射到导航器中的屏幕 | config object: defines screen path mapping, maps URL paths to screens in navigators
  - 自动解析：React Navigation自动解析URL参数并传递给屏幕组件 | Automatic parsing: React Navigation automatically parses URL params and passes them to screen components
  - 嵌套导航器支持：可以为嵌套的Stack、Tab、Drawer导航器配置路径 | Nested navigator support: can configure paths for nested Stack, Tab, Drawer navigators
  - 初始URL处理：应用启动时自动处理深度链接 | Initial URL handling: automatically handles deep links on app launch

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. linking配置必须在NavigationContainer组件中定义吗？| Must the linking configuration be defined in the NavigationContainer component?
     **答案 | Answer:** 是 | Yes - NavigationContainer是导航树的根，需要在这里配置全局的深度链接行为 | NavigationContainer is the root of the navigation tree, global deep linking behavior needs to be configured here
  2. 如果应用使用了Tab Navigator和Stack Navigator，需要为每个导航器分别配置linking吗？| If the app uses Tab Navigator and Stack Navigator, does each navigator need separate linking configuration?
     **答案 | Answer:** 否 | No - 在NavigationContainer的linking.config中统一配置，使用嵌套结构即可 | Configure centrally in NavigationContainer's linking.config using nested structure
  3. prefixes数组可以包含多个URL前缀吗？| Can the prefixes array contain multiple URL prefixes?
     **答案 | Answer:** 是 | Yes - 可以同时支持URL Scheme和Universal Links/App Links | Can support both URL Scheme and Universal Links/App Links simultaneously
  4. 如果没有配置linking，React Navigation应用能正常导航吗？| Can a React Navigation app navigate normally without configuring linking?
     **答案 | Answer:** 能 | Yes - linking配置是可选的，主要用于支持深度链接，不影响应用内导航 | linking configuration is optional, mainly for deep linking support, doesn't affect in-app navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // React Navigation深度链接基础配置 | Basic React Navigation deep linking configuration
  import { NavigationContainer } from '@react-navigation/native';
  import { createStackNavigator } from '@react-navigation/stack';

  const Stack = createStackNavigator();

  // 定义linking配置对象 | Define linking configuration object
  const linking = {
    // 1. 定义URL前缀 | Define URL prefixes
    prefixes: [
      'myapp://',              // URL Scheme用于开发和测试 | URL Scheme for development and testing
      'https://myapp.com',     // Universal/App Links用于生产环境 | Universal/App Links for production
    ],

    // 2. 定义URL路径到屏幕的映射 | Define URL path to screen mapping
    config: {
      screens: {
        Home: 'home',                    // myapp://home -> Home屏幕
        Profile: 'user/:id',             // myapp://user/123 -> Profile屏幕，参数id=123
        Settings: 'settings',            // myapp://settings -> Settings屏幕
        Article: 'article/:articleId',   // myapp://article/456 -> Article屏幕，参数articleId=456
      },
    },
  };

  function App() {
    return (
      <NavigationContainer linking={linking}> {/* 在这里传入linking配置 | Pass linking config here */}
        <Stack.Navigator>
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Profile" component={ProfileScreen} />
          <Stack.Screen name="Settings" component={SettingsScreen} />
          <Stack.Screen name="Article" component={ArticleScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }

  // 在Profile屏幕中访问URL参数 | Access URL params in Profile screen
  function ProfileScreen({ route }) {
    const { id } = route.params; // 从URL myapp://user/123 中获取 id=123
    // Get id=123 from URL myapp://user/123

    return <Text>User ID: {id}</Text>;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 用户点击URL `myapp://user/789`，Profile屏幕中`route.params.id`的值是什么？| User clicks URL `myapp://user/789`, what's the value of `route.params.id` in Profile screen?
    **答案 | Answer:** '789' (字符串类型) | '789' (string type)
  - 如果想要支持路径`myapp://settings/notifications`，config配置应该如何修改？| To support path `myapp://settings/notifications`, how should the config be modified?
    **答案 | Answer:** 需要使用嵌套配置，Settings下再定义screens | Need to use nested configuration, define screens under Settings

  **常见误区检查 | Common Misconception Checks:**
  - URL参数(如:id)会自动转换为数字类型 | URL params (like :id) are automatically converted to number type
    **答案 | Answer:** 错误 - URL参数始终是字符串，需要在组件中手动转换类型 | Incorrect - URL params are always strings, need manual type conversion in components
  - 定义了linking配置后，应用内的navigate调用需要改用URL字符串 | After configuring linking, in-app navigate calls need to use URL strings
    **答案 | Answer:** 错误 - linking配置不影响应用内导航，仍然使用navigation.navigate('ScreenName') | Incorrect - linking configuration doesn't affect in-app navigation, still use navigation.navigate('ScreenName')

- **嵌套导航器的深度链接配置 | Deep Linking Configuration for Nested Navigators**

  **概念定义 | Concept Definition:**
  实际应用通常使用嵌套导航器(如Tab Navigator包含多个Stack Navigator)。深度链接配置需要反映这种嵌套结构，确保URL能正确映射到深层屏幕。

  Real applications typically use nested navigators (like Tab Navigator containing multiple Stack Navigators). Deep linking configuration needs to reflect this nested structure to ensure URLs correctly map to deeply nested screens.

  **核心特征 | Key Characteristics:**
  - 层级映射：config.screens中使用嵌套对象表示导航器层级 | Hierarchical mapping: use nested objects in config.screens to represent navigator hierarchy
  - 路径组合：最终URL路径是各层路径的组合(如`tab/home/detail/123`) | Path composition: final URL path is combination of paths at each level (like `tab/home/detail/123`)
  - initialRouteName：指定导航器的默认屏幕，影响深度链接的路径解析 | initialRouteName: specifies navigator's default screen, affects deep link path parsing
  - 可选路径段：使用`?`标记可选路径段，支持更灵活的URL结构 | Optional path segments: use `?` to mark optional path segments for more flexible URL structure
  - 路径简化：可以省略中间导航器路径，直接映射到目标屏幕 | Path simplification: can omit intermediate navigator paths, map directly to target screen

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应用有Tab Navigator(Home, Profile)，Home Tab中有Stack(Feed, Detail)，如果要链接到Detail屏幕，URL路径必须包含所有层级吗？| App has Tab Navigator (Home, Profile), Home Tab has Stack (Feed, Detail), must URL path include all levels to link to Detail screen?
     **答案 | Answer:** 不一定 | Not necessarily - 可以配置简化路径，如`detail/:id`直接到Detail，React Navigation会自动处理导航层级 | Can configure simplified path like `detail/:id` directly to Detail, React Navigation automatically handles navigation hierarchy
  2. 如果Tab Navigator的initialRouteName是Home，用户打开`myapp://profile`链接，会切换到Profile tab吗？| If Tab Navigator's initialRouteName is Home, will opening `myapp://profile` link switch to Profile tab?
     **答案 | Answer:** 会 | Yes - 深度链接会覆盖initialRouteName设置 | Deep links override initialRouteName settings
  3. 嵌套导航器配置中，每个screens对象对应什么？| In nested navigator configuration, what does each screens object correspond to?
     **答案 | Answer:** 对应一个导航器及其包含的屏幕或子导航器 | Corresponds to a navigator and its contained screens or child navigators
  4. 如果URL路径是`myapp://home/detail/123`，但config中只定义了`detail/:id`，能正确导航吗？| If URL path is `myapp://home/detail/123` but config only defines `detail/:id`, will navigation work correctly?
     **答案 | Answer:** 取决于配置 | Depends on configuration - 如果detail配置在正确的导航器层级下，可以工作 | If detail is configured under correct navigator level, it can work

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 嵌套导航器的深度链接配置示例 | Deep linking configuration example for nested navigators
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createStackNavigator } from '@react-navigation/stack';

  const Tab = createBottomTabNavigator();
  const Stack = createStackNavigator();

  // Home Tab的Stack Navigator | Home Tab's Stack Navigator
  function HomeStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="Feed" component={FeedScreen} />
        <Stack.Screen name="Detail" component={DetailScreen} />
      </Stack.Navigator>
    );
  }

  // Profile Tab的Stack Navigator | Profile Tab's Stack Navigator
  function ProfileStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="ProfileHome" component={ProfileHomeScreen} />
        <Stack.Screen name="EditProfile" component={EditProfileScreen} />
      </Stack.Navigator>
    );
  }

  // 嵌套导航器配置 | Nested navigator configuration
  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        // Tab Navigator的屏幕配置 | Tab Navigator screen configuration
        HomeTab: {
          // 可选：定义此tab的路径前缀 | Optional: define path prefix for this tab
          path: 'home',
          screens: {
            // Home Stack中的屏幕 | Screens in Home Stack
            Feed: 'feed',                 // myapp://home/feed
            Detail: 'detail/:postId',     // myapp://home/detail/123
          },
        },
        ProfileTab: {
          path: 'profile',
          screens: {
            ProfileHome: '',              // myapp://profile (空字符串表示默认屏幕)
            EditProfile: 'edit',          // myapp://profile/edit
          },
        },
        SettingsTab: 'settings',          // myapp://settings (简单屏幕无嵌套)
      },
    },
  };

  function App() {
    return (
      <NavigationContainer linking={linking}>
        <Tab.Navigator>
          <Tab.Screen name="HomeTab" component={HomeStack} />
          <Tab.Screen name="ProfileTab" component={ProfileStack} />
          <Tab.Screen name="SettingsTab" component={SettingsScreen} />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  // 简化路径配置示例 | Simplified path configuration example
  const linkingSimplified = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        HomeTab: {
          screens: {
            Feed: 'feed',
            Detail: 'post/:postId',  // 简化为 myapp://post/123 而不是 myapp://home/feed/post/123
                                      // Simplified to myapp://post/123 instead of myapp://home/feed/post/123
          },
        },
      },
    },
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 用户点击`myapp://home/detail/456`，会打开哪个Tab，哪个屏幕？| User clicks `myapp://home/detail/456`, which Tab and screen will open?
    **答案 | Answer:** HomeTab，Detail屏幕，参数postId=456 | HomeTab, Detail screen, with param postId=456
  - 如果想要URL`myapp://edit-profile`直接打开EditProfile屏幕，应该如何配置？| To have URL `myapp://edit-profile` directly open EditProfile screen, how to configure?
    **答案 | Answer:** 将EditProfile的path设为'edit-profile'，可以省略ProfileTab层级 | Set EditProfile's path to 'edit-profile', can omit ProfileTab level

  **常见误区检查 | Common Misconception Checks:**
  - 嵌套导航器的深度链接URL必须完整包含所有导航层级路径 | Deep link URLs for nested navigators must include all navigation level paths
    **答案 | Answer:** 错误 - 可以通过配置简化路径，直接映射到深层屏幕 | Incorrect - Can simplify paths through configuration, map directly to deep screens
  - Tab Navigator的initialRouteName会阻止深度链接切换到其他tab | Tab Navigator's initialRouteName prevents deep links from switching to other tabs
    **答案 | Answer:** 错误 - 深度链接优先级高于initialRouteName | Incorrect - Deep links have higher priority than initialRouteName

### 3. URL参数解析与query字符串 | URL Parameter Parsing & Query Strings (1小时 | 1 hour)

- **路径参数(Path Parameters) | Path Parameters**

  **概念定义 | Concept Definition:**
  路径参数是嵌入在URL路径中的动态值，使用冒号语法定义(如`:id`)。React Navigation自动提取这些参数并通过`route.params`传递给屏幕组件。

  Path parameters are dynamic values embedded in the URL path, defined using colon syntax (like `:id`). React Navigation automatically extracts these parameters and passes them to screen components via `route.params`.

  **核心特征 | Key Characteristics:**
  - 冒号语法：在linking config中使用`:paramName`定义动态路径段 | Colon syntax: use `:paramName` in linking config to define dynamic path segments
  - 自动解析：无需手动解析URL，React Navigation自动提取参数值 | Automatic parsing: no need to manually parse URL, React Navigation automatically extracts param values
  - 字符串类型：所有路径参数都是字符串，需要手动类型转换 | String type: all path parameters are strings, require manual type conversion
  - 必需参数：默认情况下路径参数是必需的，缺失会导致匹配失败 | Required parameters: by default path parameters are required, missing them causes match failure
  - 多参数支持：一个路径可以包含多个参数(如`/user/:userId/post/:postId`) | Multiple parameter support: one path can contain multiple parameters (like `/user/:userId/post/:postId`)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 路径配置为`article/:id`，URL是`myapp://article/999`，`route.params.id`的值是数字999吗？| Path configured as `article/:id`, URL is `myapp://article/999`, is `route.params.id` the number 999?
     **答案 | Answer:** 否 | No - 是字符串'999'，需要使用parseInt()或Number()转换 | It's the string '999', need to use parseInt() or Number() to convert
  2. 如果路径需要两个参数(用户ID和文章ID)，可以配置为`user/:userId/article/:articleId`吗？| If path needs two parameters (user ID and article ID), can it be configured as `user/:userId/article/:articleId`?
     **答案 | Answer:** 可以 | Yes - 支持多个路径参数，都会被解析到route.params对象中 | Supports multiple path parameters, all parsed into route.params object
  3. 如果用户访问`myapp://article/`(缺少id参数)，会发生什么？| What happens if user visits `myapp://article/` (missing id parameter)?
     **答案 | Answer:** 路径不匹配，通常会导航到默认屏幕或显示404 | Path doesn't match, usually navigates to default screen or shows 404
  4. route.params中的参数可以在组件中直接修改吗？| Can parameters in route.params be modified directly in the component?
     **答案 | Answer:** 不推荐 | Not recommended - route.params是导航参数，应该通过navigation.setParams()修改 | route.params are navigation parameters, should be modified via navigation.setParams()

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 路径参数使用示例 | Path parameter usage examples

  // 1. 单个路径参数配置 | Single path parameter configuration
  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Article: 'article/:articleId',  // 定义动态参数 | Define dynamic parameter
      },
    },
  };

  // 2. 在组件中访问路径参数 | Access path parameters in component
  function ArticleScreen({ route, navigation }) {
    // 从route.params获取参数 | Get parameter from route.params
    const { articleId } = route.params;

    // 注意：articleId是字符串类型 | Note: articleId is string type
    const numericId = parseInt(articleId, 10); // 转换为数字 | Convert to number

    return (
      <View>
        <Text>Article ID (string): {articleId}</Text>
        <Text>Article ID (number): {numericId}</Text>
      </View>
    );
  }

  // 3. 多个路径参数示例 | Multiple path parameters example
  const linkingMultiParams = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        UserPost: 'user/:userId/post/:postId',  // 两个参数 | Two parameters
      },
    },
  };

  function UserPostScreen({ route }) {
    const { userId, postId } = route.params;
    // URL: myapp://user/alice/post/123
    // userId = 'alice', postId = '123'

    return (
      <View>
        <Text>User: {userId}</Text>
        <Text>Post: {postId}</Text>
      </View>
    );
  }

  // 4. 可选路径参数 (使用?) | Optional path parameters (using ?)
  const linkingOptional = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Product: 'product/:productId/:variant?',  // variant是可选参数 | variant is optional
      },
    },
  };

  function ProductScreen({ route }) {
    const { productId, variant } = route.params;
    // myapp://product/123 -> productId='123', variant=undefined
    // myapp://product/123/red -> productId='123', variant='red'

    return (
      <View>
        <Text>Product: {productId}</Text>
        <Text>Variant: {variant || 'default'}</Text>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - URL是`myapp://user/bob/post/456`，配置是`user/:userId/post/:postId`，`route.params`对象的内容是什么？| URL is `myapp://user/bob/post/456`, config is `user/:userId/post/:postId`, what's the content of `route.params` object?
    **答案 | Answer:** `{ userId: 'bob', postId: '456' }` (都是字符串) | `{ userId: 'bob', postId: '456' }` (both strings)
  - 如果需要传递布尔值(如isPublic=true)，路径参数是最佳选择吗？| If need to pass boolean value (like isPublic=true), are path parameters the best choice?
    **答案 | Answer:** 否 - 查询字符串(query string)更适合可选的布尔或多值参数 | No - Query strings are better suited for optional boolean or multi-value parameters

  **常见误区检查 | Common Misconception Checks:**
  - 路径参数`:id`会自动识别参数类型(数字、布尔值等) | Path parameter `:id` automatically identifies parameter type (number, boolean, etc.)
    **答案 | Answer:** 错误 - 所有路径参数都是字符串，必须手动类型转换 | Incorrect - All path parameters are strings, must manually convert types
  - 定义可选参数`:param?`后，访问`route.params.param`时不需要检查undefined | After defining optional parameter `:param?`, no need to check for undefined when accessing `route.params.param`
    **答案 | Answer:** 错误 - 可选参数可能是undefined，使用前应检查或提供默认值 | Incorrect - Optional parameters may be undefined, should check or provide default value before use

- **查询字符串参数(Query String Parameters) | Query String Parameters**

  **概念定义 | Concept Definition:**
  查询字符串参数是附加在URL末尾的键值对，使用`?`和`&`分隔(如`?sort=date&filter=active`)。React Navigation自动解析查询参数并添加到`route.params`对象中。

  Query string parameters are key-value pairs appended to the end of URLs, separated by `?` and `&` (like `?sort=date&filter=active`). React Navigation automatically parses query parameters and adds them to the `route.params` object.

  **核心特征 | Key Characteristics:**
  - 可选性：查询参数通常是可选的，缺失不影响路径匹配 | Optional: query parameters are typically optional, their absence doesn't affect path matching
  - 多值支持：适合传递多个可选配置参数(如筛选、排序选项) | Multiple value support: suitable for passing multiple optional configuration parameters (like filter, sort options)
  - 自动解析：React Navigation自动将查询字符串解析为对象 | Automatic parsing: React Navigation automatically parses query string into object
  - 与路径参数合并：查询参数和路径参数合并在同一个`route.params`对象中 | Merged with path parameters: query parameters and path parameters merged in same `route.params` object
  - 字符串类型：与路径参数一样，查询参数值也是字符串 | String type: like path parameters, query parameter values are also strings

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 查询参数需要在linking config中预先定义吗？| Do query parameters need to be predefined in linking config?
     **答案 | Answer:** 否 | No - 查询参数会自动解析，不需要在config中声明 | Query parameters are automatically parsed, no need to declare in config
  2. URL是`myapp://products?category=electronics&sort=price`，有几个查询参数？| URL is `myapp://products?category=electronics&sort=price`, how many query parameters?
     **答案 | Answer:** 2个 - category和sort | 2 - category and sort
  3. 路径参数和查询参数可以同时使用吗？| Can path parameters and query parameters be used together?
     **答案 | Answer:** 可以 | Yes - 两者会合并到同一个route.params对象中 | Both are merged into the same route.params object
  4. 如果查询参数值包含特殊字符或空格，需要特殊处理吗？| If query parameter value contains special characters or spaces, does it need special handling?
     **答案 | Answer:** 是 - 应该使用URL编码(encodeURIComponent)，React Navigation会自动解码 | Yes - Should use URL encoding (encodeURIComponent), React Navigation automatically decodes

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 查询字符串参数使用示例 | Query string parameter usage examples

  // 1. 基本查询参数示例 | Basic query parameter example
  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Products: 'products',  // 不需要定义查询参数 | No need to define query parameters
      },
    },
  };

  function ProductsScreen({ route }) {
    // URL: myapp://products?category=books&sort=price&order=asc
    const { category, sort, order } = route.params || {};
    // category = 'books', sort = 'price', order = 'asc' (都是字符串)
    // category = 'books', sort = 'price', order = 'asc' (all strings)

    return (
      <View>
        <Text>Category: {category || 'all'}</Text>
        <Text>Sort by: {sort || 'default'}</Text>
        <Text>Order: {order || 'desc'}</Text>
      </View>
    );
  }

  // 2. 路径参数与查询参数结合 | Combining path and query parameters
  const linkingCombined = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        UserPosts: 'user/:userId/posts',  // 路径参数 | Path parameter
      },
    },
  };

  function UserPostsScreen({ route }) {
    // URL: myapp://user/123/posts?status=published&limit=10
    const { userId, status, limit } = route.params;
    // userId = '123' (路径参数), status = 'published', limit = '10' (查询参数)
    // userId = '123' (path param), status = 'published', limit = '10' (query params)

    const limitNumber = parseInt(limit, 10) || 20; // 转换并提供默认值 | Convert and provide default

    return (
      <View>
        <Text>User ID: {userId}</Text>
        <Text>Status filter: {status}</Text>
        <Text>Limit: {limitNumber}</Text>
      </View>
    );
  }

  // 3. 查询参数的布尔值处理 | Boolean value handling for query parameters
  function SearchScreen({ route }) {
    // URL: myapp://search?q=react&exact=true&caseSensitive=false
    const { q, exact, caseSensitive } = route.params || {};

    // 将字符串转换为布尔值 | Convert strings to booleans
    const isExact = exact === 'true';
    const isCaseSensitive = caseSensitive === 'true';

    return (
      <View>
        <Text>Search query: {q}</Text>
        <Text>Exact match: {isExact ? 'Yes' : 'No'}</Text>
        <Text>Case sensitive: {isCaseSensitive ? 'Yes' : 'No'}</Text>
      </View>
    );
  }

  // 4. 通过代码生成带查询参数的深度链接 | Generate deep link with query params programmatically
  import { Linking } from 'react-native';

  function shareProduct(productId, referralCode) {
    const params = new URLSearchParams({
      ref: referralCode,
      utm_source: 'share',
      utm_campaign: 'product_sharing'
    });

    const url = `myapp://product/${productId}?${params.toString()}`;
    // 结果: myapp://product/123?ref=ABC&utm_source=share&utm_campaign=product_sharing
    // Result: myapp://product/123?ref=ABC&utm_source=share&utm_campaign=product_sharing

    Linking.openURL(url);
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - URL是`myapp://articles/42?highlight=true`，`route.params`包含哪些键？| URL is `myapp://articles/42?highlight=true`, what keys does `route.params` contain?
    **答案 | Answer:** `{ articleId: '42', highlight: 'true' }` (假设路径配置为`articles/:articleId`) | `{ articleId: '42', highlight: 'true' }` (assuming path config is `articles/:articleId`)
  - 查询参数`?count=5`中的5是数字类型吗？| In query parameter `?count=5`, is 5 a number type?
    **答案 | Answer:** 否，是字符串'5'，需要用parseInt转换 | No, it's the string '5', needs conversion with parseInt

  **常见误区检查 | Common Misconception Checks:**
  - 查询参数必须在linking.config中预先声明才能使用 | Query parameters must be predeclared in linking.config to be used
    **答案 | Answer:** 错误 - 查询参数自动解析，无需配置 | Incorrect - Query parameters are automatically parsed, no configuration needed
  - 查询参数`?active=true`在route.params.active中是布尔值true | Query parameter `?active=true` is boolean true in route.params.active
    **答案 | Answer:** 错误 - 是字符串'true'，需要手动转换为布尔值 | Incorrect - It's the string 'true', needs manual conversion to boolean
