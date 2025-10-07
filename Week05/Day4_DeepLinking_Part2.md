# React Native入门 - 第27天：深度链接与通用链接 (第二部分) | React Native Introduction - Day 27: Deep Linking & Universal Links (Part 2)

## 详细内容(续) | Detailed Content (Continued)

### 4. 测试深度链接 | Testing Deep Links (1小时 | 1 hour)

- **开发环境测试工具与方法 | Development Testing Tools and Methods**

  **概念定义 | Concept Definition:**
  在开发阶段，需要使用特定工具和命令来测试深度链接功能，而无需构建生产环境的Universal Links或App Links配置。主要工具包括`npx uri-scheme`、`adb`(Android)和`xcrun simctl`(iOS)。

  During development, specific tools and commands are needed to test deep linking functionality without building production Universal Links or App Links configuration. Main tools include `npx uri-scheme`, `adb` (Android), and `xcrun simctl` (iOS).

  **核心特征 | Key Characteristics:**
  - uri-scheme CLI工具：React Native提供的官方深度链接测试工具 | uri-scheme CLI tool: Official deep linking testing tool provided by React Native
  - 平台特定命令：iOS和Android有各自的命令行工具 | Platform-specific commands: iOS and Android have their own CLI tools
  - 应用状态测试：可以测试应用未运行、后台运行、前台运行三种状态 | App state testing: can test three states - app not running, background, foreground
  - 实时调试：结合日志查看深度链接的解析和导航过程 | Real-time debugging: view deep link parsing and navigation process with logs
  - 快速迭代：无需重新构建应用即可测试不同URL | Fast iteration: test different URLs without rebuilding the app

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在开发阶段测试深度链接，需要配置HTTPS域名和服务器吗？| To test deep links in development, do you need to configure HTTPS domain and server?
     **答案 | Answer:** 否 | No - 使用URL Scheme(如myapp://)即可，无需服务器配置 | Use URL Scheme (like myapp://) without server configuration
  2. `npx uri-scheme open`命令可以在真实设备和模拟器/模拟器上使用吗？| Can `npx uri-scheme open` command be used on both real devices and simulators/emulators?
     **答案 | Answer:** 是 | Yes - 支持iOS模拟器、Android模拟器和真实设备 | Supports iOS simulator, Android emulator, and real devices
  3. 如果应用当前正在运行，打开深度链接会重启应用吗？| If the app is currently running, will opening a deep link restart the app?
     **答案 | Answer:** 否 | No - 应用会保持运行状态，React Navigation会处理导航 | App stays running, React Navigation handles navigation
  4. 测试深度链接时，可以查看React Navigation的导航日志吗？| When testing deep links, can you view React Navigation's navigation logs?
     **答案 | Answer:** 可以 | Yes - 在NavigationContainer中设置`onStateChange`可以记录导航状态变化 | Set `onStateChange` in NavigationContainer to log navigation state changes

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 1. 使用uri-scheme命令测试深度链接 | Testing deep links with uri-scheme command

  // 基础命令格式 | Basic command format
  // npx uri-scheme open [URL] --[platform]

  // iOS示例 | iOS examples
  // 打开产品详情页 | Open product detail page
  npx uri-scheme open "myapp://product/123" --ios

  // 带查询参数的URL | URL with query parameters
  npx uri-scheme open "myapp://products?category=books&sort=price" --ios

  // Android示例 | Android examples
  npx uri-scheme open "myapp://user/alice/posts" --android

  // 2. 查看应用注册的URL Scheme | View app's registered URL Schemes
  npx uri-scheme list

  // 输出示例 | Output example:
  // iOS: myapp
  // Android: myapp

  // 3. 原生命令测试 (不使用uri-scheme工具) | Native command testing (without uri-scheme tool)

  // iOS模拟器 (使用xcrun) | iOS Simulator (using xcrun)
  xcrun simctl openurl booted "myapp://article/456"

  // Android模拟器/设备 (使用adb) | Android Emulator/Device (using adb)
  adb shell am start -W -a android.intent.action.VIEW -d "myapp://settings"

  // 4. 添加导航日志用于调试 | Add navigation logs for debugging
  import { NavigationContainer } from '@react-navigation/native';

  function App() {
    return (
      <NavigationContainer
        linking={linking}
        onStateChange={(state) => {
          // 记录导航状态变化，用于调试深度链接 | Log navigation state changes for debugging deep links
          console.log('Navigation state changed:', JSON.stringify(state, null, 2));
        }}
        onReady={() => {
          console.log('Navigation is ready');
        }}
      >
        {/* 导航器 | Navigators */}
      </NavigationContainer>
    );
  }

  // 5. 监听深度链接事件 (用于调试) | Listen to deep link events (for debugging)
  import { useEffect } from 'react';
  import { Linking } from 'react-native';

  function useDeepLinkLogger() {
    useEffect(() => {
      // 监听应用打开时的初始URL | Listen for initial URL when app opens
      Linking.getInitialURL().then((url) => {
        if (url) {
          console.log('App opened with URL:', url);
        }
      });

      // 监听应用运行时的深度链接 | Listen for deep links while app is running
      const subscription = Linking.addEventListener('url', ({ url }) => {
        console.log('Deep link received:', url);
      });

      return () => subscription.remove();
    }, []);
  }

  // 6. 测试checklist示例 | Testing checklist example
  /*
  测试场景清单 | Test Scenario Checklist:

  ✅ 应用未运行时打开深度链接
     Test opening deep link when app is not running
     命令: npx uri-scheme open "myapp://product/123" --ios

  ✅ 应用在后台运行时打开深度链接
     Test opening deep link when app is in background

  ✅ 应用在前台运行时打开深度链接
     Test opening deep link when app is in foreground

  ✅ 测试带路径参数的URL
     Test URLs with path parameters
     命令: npx uri-scheme open "myapp://user/alice/post/456" --android

  ✅ 测试带查询参数的URL
     Test URLs with query parameters
     命令: npx uri-scheme open "myapp://search?q=react&filter=recent" --ios

  ✅ 测试无效/不存在的路径
     Test invalid/non-existent paths
     命令: npx uri-scheme open "myapp://nonexistent/path" --ios
     预期：导航到默认屏幕或显示404

  ✅ 测试嵌套导航器路径
     Test nested navigator paths
     命令: npx uri-scheme open "myapp://home/detail/789" --android
  */
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 要测试URL `myapp://profile/edit`在iOS模拟器上，应该使用什么命令？| What command should be used to test URL `myapp://profile/edit` on iOS simulator?
    **答案 | Answer:** `npx uri-scheme open "myapp://profile/edit" --ios`
  - 如果想查看当前应用注册了哪些URL Scheme，应该运行什么命令？| What command should be run to see which URL Schemes the current app has registered?
    **答案 | Answer:** `npx uri-scheme list`

  **常见误区检查 | Common Misconception Checks:**
  - 必须在真实设备上测试深度链接，模拟器不支持 | Deep links must be tested on real devices, simulators don't support them
    **答案 | Answer:** 错误 - iOS模拟器和Android模拟器都完全支持URL Scheme深度链接测试 | Incorrect - Both iOS simulator and Android emulator fully support URL Scheme deep link testing
  - 使用uri-scheme命令时，URL不需要引号包裹 | When using uri-scheme command, URLs don't need to be wrapped in quotes
    **答案 | Answer:** 错误 - 包含特殊字符(如&、?)的URL必须用引号包裹，否则会被shell解析 | Incorrect - URLs containing special characters (like &, ?) must be wrapped in quotes, otherwise they'll be parsed by the shell

- **处理应用启动时的初始URL | Handling Initial URL on App Launch**

  **概念定义 | Concept Definition:**
  当应用通过深度链接启动时(应用未运行状态)，需要特殊处理来获取和解析这个初始URL。React Navigation的`linking`配置会自动处理这个场景，但理解底层机制对于调试和自定义行为很重要。

  When an app is launched via a deep link (app not running state), special handling is needed to retrieve and parse this initial URL. React Navigation's `linking` configuration automatically handles this scenario, but understanding the underlying mechanism is important for debugging and custom behavior.

  **核心特征 | Key Characteristics:**
  - 初始URL获取：使用`Linking.getInitialURL()`获取应用启动时的URL | Initial URL retrieval: use `Linking.getInitialURL()` to get URL when app launches
  - 异步处理：getInitialURL返回Promise，因为可能需要从系统查询 | Asynchronous handling: getInitialURL returns Promise as it may need to query the system
  - React Navigation自动处理：配置linking后无需手动处理初始URL | React Navigation auto-handles: no need to manually handle initial URL after configuring linking
  - 应用状态区分：区分"冷启动"(通过深度链接启动)和"热启动"(应用已运行) | App state distinction: distinguish "cold start" (launched via deep link) and "hot start" (app already running)
  - 导航时机：必须等待NavigationContainer准备就绪后才能导航 | Navigation timing: must wait for NavigationContainer to be ready before navigating

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果应用通过深度链接启动，`Linking.getInitialURL()`会返回什么？| If app is launched via deep link, what does `Linking.getInitialURL()` return?
     **答案 | Answer:** 返回Promise，resolve为启动应用的URL字符串 | Returns Promise that resolves to the URL string used to launch the app
  2. 配置了linking后，还需要手动调用`Linking.getInitialURL()`吗？| After configuring linking, do you still need to manually call `Linking.getInitialURL()`?
     **答案 | Answer:** 通常不需要 | Usually not needed - React Navigation自动处理初始URL | React Navigation automatically handles initial URL
  3. 应用正常启动(非深度链接启动)时，`Linking.getInitialURL()`返回什么？| When app launches normally (not via deep link), what does `Linking.getInitialURL()` return?
     **答案 | Answer:** 返回Promise，resolve为null | Returns Promise that resolves to null
  4. 可以在App组件的顶层直接调用navigation.navigate处理初始URL吗？| Can you call navigation.navigate directly at the top level of App component to handle initial URL?
     **答案 | Answer:** 否 | No - 必须等待NavigationContainer准备就绪(onReady) | Must wait for NavigationContainer to be ready (onReady)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 处理初始URL的示例 | Examples of handling initial URL

  // 1. React Navigation自动处理 (推荐方式) | React Navigation auto-handling (recommended)
  import { NavigationContainer } from '@react-navigation/native';

  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Home: 'home',
        Product: 'product/:id',
      },
    },
  };

  function App() {
    return (
      <NavigationContainer
        linking={linking}
        fallback={<Text>Loading...</Text>}  // 解析初始URL时显示 | Shown while parsing initial URL
      >
        <Stack.Navigator>
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Product" component={ProductScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }
  // React Navigation会自动:
  // 1. 调用Linking.getInitialURL()
  // 2. 解析URL并匹配到对应屏幕
  // 3. 导航到目标屏幕并传递参数
  // React Navigation automatically:
  // 1. Calls Linking.getInitialURL()
  // 2. Parses URL and matches to corresponding screen
  // 3. Navigates to target screen and passes parameters

  // 2. 手动处理初始URL (用于自定义逻辑) | Manually handle initial URL (for custom logic)
  import { useEffect, useState } from 'react';
  import { Linking } from 'react-native';

  function App() {
    const [initialUrl, setInitialUrl] = useState(null);
    const [isReady, setIsReady] = useState(false);

    useEffect(() => {
      // 获取初始URL | Get initial URL
      Linking.getInitialURL().then((url) => {
        if (url) {
          console.log('App launched with URL:', url);
          setInitialUrl(url);

          // 自定义逻辑：例如记录分析数据 | Custom logic: e.g. log analytics
          analytics.logEvent('app_opened_via_deep_link', { url });
        }
      });
    }, []);

    return (
      <NavigationContainer
        linking={linking}
        onReady={() => {
          setIsReady(true);
          if (initialUrl) {
            console.log('Navigation ready, initial URL processed:', initialUrl);
          }
        }}
      >
        {/* 导航器 | Navigators */}
      </NavigationContainer>
    );
  }

  // 3. 监听应用运行时的深度链接 | Listen for deep links while app is running
  function App() {
    useEffect(() => {
      // 处理初始URL (应用冷启动) | Handle initial URL (cold start)
      Linking.getInitialURL().then((url) => {
        if (url) {
          handleDeepLink(url, 'cold_start');
        }
      });

      // 监听运行时的深度链接 (应用热启动) | Listen for runtime deep links (hot start)
      const subscription = Linking.addEventListener('url', ({ url }) => {
        handleDeepLink(url, 'hot_start');
      });

      return () => subscription.remove();
    }, []);

    const handleDeepLink = (url, launchType) => {
      console.log(`Deep link received (${launchType}):`, url);

      // 自定义处理逻辑 | Custom handling logic
      // 例如：权限检查、用户认证状态检查等
      // E.g.: permission checks, user auth state checks, etc.
    };

    return (
      <NavigationContainer linking={linking}>
        {/* 导航器 | Navigators */}
      </NavigationContainer>
    );
  }

  // 4. 处理无效URL的情况 | Handle invalid URL cases
  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        Home: 'home',
        Product: 'product/:id',
        NotFound: '*',  // 通配符，匹配所有未定义的路径 | Wildcard, matches all undefined paths
      },
    },
  };

  function NotFoundScreen({ route }) {
    const attemptedUrl = route.params?.screen || 'unknown';

    return (
      <View>
        <Text>Page not found</Text>
        <Text>You tried to open: {attemptedUrl}</Text>
        <Button title="Go Home" onPress={() => navigation.navigate('Home')} />
      </View>
    );
  }

  // 5. 在初始URL处理完成前显示加载状态 | Show loading state before initial URL is processed
  function App() {
    const [isNavigationReady, setIsNavigationReady] = useState(false);

    return (
      <>
        {!isNavigationReady && (
          <View style={styles.splash}>
            <ActivityIndicator size="large" />
            <Text>Loading...</Text>
          </View>
        )}
        <NavigationContainer
          linking={linking}
          onReady={() => setIsNavigationReady(true)}
        >
          <Stack.Navigator>
            {/* 屏幕定义 | Screen definitions */}
          </Stack.Navigator>
        </NavigationContainer>
      </>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 用户通过深度链接`myapp://product/999`启动应用，React Navigation会在什么时候导航到Product屏幕？| User launches app via deep link `myapp://product/999`, when will React Navigation navigate to Product screen?
    **答案 | Answer:** NavigationContainer准备就绪后(onReady触发后) | After NavigationContainer is ready (after onReady triggers)
  - 如果不配置linking，应用能通过深度链接启动吗？| Without configuring linking, can the app be launched via deep link?
    **答案 | Answer:** 应用会启动，但不会自动导航到对应屏幕，停留在默认初始屏幕 | App will launch but won't auto-navigate to corresponding screen, stays on default initial screen

  **常见误区检查 | Common Misconception Checks:**
  - Linking.getInitialURL()是同步函数，可以直接获取返回值 | Linking.getInitialURL() is a synchronous function, can directly get return value
    **答案 | Answer:** 错误 - 是异步函数，返回Promise，需要使用.then()或await | Incorrect - It's asynchronous, returns Promise, need to use .then() or await
  - 配置linking后，Linking.addEventListener('url')就不需要了 | After configuring linking, Linking.addEventListener('url') is no longer needed
    **答案 | Answer:** 部分正确 - 对于基本导航不需要，但如果要添加自定义逻辑(如分析、权限检查)仍然有用 | Partially correct - Not needed for basic navigation, but still useful for custom logic (like analytics, permission checks)

### 5. 深度链接最佳实践与常见问题 | Deep Linking Best Practices & Common Issues (30分钟 | 30 minutes)

- **深度链接设计原则 | Deep Linking Design Principles**

  **概念定义 | Concept Definition:**
  良好的深度链接设计应该遵循Web URL的最佳实践，保持简洁、语义化、可预测，同时考虑移动应用的特殊性，如用户认证状态、权限要求等。

  Good deep link design should follow web URL best practices, maintaining simplicity, semantic meaning, and predictability, while considering mobile app specifics like user authentication state and permission requirements.

  **核心特征 | Key Characteristics:**
  - 语义化URL：路径应该清晰表达资源层级(如`/user/123/posts`而非`/page?id=123&type=posts`) | Semantic URLs: paths should clearly express resource hierarchy (like `/user/123/posts` not `/page?id=123&type=posts`)
  - 短而易记：URL应尽可能短，便于分享和记忆 | Short and memorable: URLs should be as short as possible for easy sharing and remembering
  - 认证处理：需要登录的内容应优雅处理，如跳转到登录页后返回目标页 | Auth handling: content requiring login should be handled gracefully, like redirecting to login then back to target
  - 降级策略：无效或过期的深度链接应有合理的降级方案 | Fallback strategy: invalid or expired deep links should have reasonable fallback
  - 参数验证：验证URL参数的有效性，防止错误或恶意输入 | Parameter validation: validate URL parameter validity to prevent errors or malicious input

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 哪个URL设计更好：`myapp://post/123`还是`myapp://content?type=post&id=123`？| Which URL design is better: `myapp://post/123` or `myapp://content?type=post&id=123`?
     **答案 | Answer:** 第一个 | First one - 更简洁、语义化，符合RESTful设计原则 | More concise, semantic, follows RESTful design principles
  2. 如果深度链接指向的内容需要用户登录，应该怎么处理？| If deep link points to content requiring user login, how should it be handled?
     **答案 | Answer:** 跳转到登录页，登录成功后再导航到目标内容 | Redirect to login page, navigate to target content after successful login
  3. 深度链接的URL参数需要在组件中验证吗？| Do deep link URL parameters need validation in components?
     **答案 | Answer:** 是 | Yes - 应验证参数类型、范围、权限等，防止错误和安全问题 | Should validate parameter type, range, permissions, etc. to prevent errors and security issues
  4. 商业应用中，深度链接URL应该永久有效吗？| In commercial apps, should deep link URLs be permanently valid?
     **答案 | Answer:** 取决于内容类型 - 核心内容(如产品)应保持稳定，临时内容(如活动)可以过期 | Depends on content type - Core content (like products) should remain stable, temporary content (like promotions) can expire

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 深度链接最佳实践示例 | Deep linking best practices examples

  // 1. 语义化URL设计 ✅ 推荐 | Semantic URL design ✅ Recommended
  const goodLinking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        UserProfile: 'user/:userId',              // 清晰的资源路径 | Clear resource path
        UserPosts: 'user/:userId/posts',          // 层级关系明确 | Clear hierarchy
        PostDetail: 'post/:postId',               // 简洁直观 | Concise and intuitive
        CategoryProducts: 'category/:slug/products', // 使用slug而非数字ID | Use slug instead of numeric ID
      },
    },
  };

  // ❌ 避免的设计 | Design to avoid
  const badLinking = {
    config: {
      screens: {
        GenericPage: 'page',  // URL: myapp://page?type=user&id=123&action=posts
        // 问题：不语义化，难以理解和维护
        // Problem: Not semantic, hard to understand and maintain
      },
    },
  };

  // 2. 认证状态处理 | Authentication state handling
  function App() {
    const { user } = useAuth();

    const linking = {
      prefixes: ['myapp://'],
      config: {
        screens: user ? {
          // 已登录用户的路由 | Authenticated user routes
          Home: 'home',
          Profile: 'profile',
          Settings: 'settings',
        } : {
          // 未登录用户的路由 | Unauthenticated user routes
          Login: 'login',
          Signup: 'signup',
          Home: 'home',  // 公开内容 | Public content
        },
      },
    };

    return <NavigationContainer linking={linking}>{/* ... */}</NavigationContainer>;
  }

  // 3. 需要认证的深度链接处理 | Handling deep links that require auth
  function ProtectedScreen({ route, navigation }) {
    const { user } = useAuth();
    const { postId } = route.params;

    useEffect(() => {
      if (!user) {
        // 保存目标URL，登录后跳转 | Save target URL, redirect after login
        navigation.navigate('Login', {
          returnTo: 'PostDetail',
          returnParams: { postId },
        });
      }
    }, [user]);

    if (!user) {
      return <ActivityIndicator />;
    }

    return <PostDetailContent postId={postId} />;
  }

  function LoginScreen({ route, navigation }) {
    const handleLoginSuccess = async () => {
      // 登录成功后返回目标页面 | Return to target page after successful login
      const { returnTo, returnParams } = route.params || {};

      if (returnTo) {
        navigation.navigate(returnTo, returnParams);
      } else {
        navigation.navigate('Home');
      }
    };

    return <LoginForm onSuccess={handleLoginSuccess} />;
  }

  // 4. URL参数验证 | URL parameter validation
  function ProductDetailScreen({ route, navigation }) {
    const { productId } = route.params;

    // 验证参数 | Validate parameters
    useEffect(() => {
      const id = parseInt(productId, 10);

      if (isNaN(id) || id <= 0) {
        // 无效ID，导航到错误页面 | Invalid ID, navigate to error page
        Alert.alert('Invalid Product', 'The product link is invalid.');
        navigation.navigate('Home');
        return;
      }

      // 检查产品是否存在 | Check if product exists
      fetchProduct(id).catch(() => {
        Alert.alert('Not Found', 'This product no longer exists.');
        navigation.navigate('Home');
      });
    }, [productId]);

    return <ProductContent productId={productId} />;
  }

  // 5. 过期链接的降级处理 | Fallback handling for expired links
  function CampaignScreen({ route, navigation }) {
    const { campaignId } = route.params;
    const [campaign, setCampaign] = useState(null);
    const [error, setError] = useState(null);

    useEffect(() => {
      fetchCampaign(campaignId)
        .then(setCampaign)
        .catch((err) => {
          if (err.status === 404 || err.message === 'expired') {
            // 活动已过期，显示友好提示 | Campaign expired, show friendly message
            setError('This campaign has ended.');
          }
        });
    }, [campaignId]);

    if (error) {
      return (
        <View style={styles.container}>
          <Text style={styles.errorText}>{error}</Text>
          <Button
            title="Browse Active Campaigns"
            onPress={() => navigation.navigate('Campaigns')}
          />
        </View>
      );
    }

    return campaign ? <CampaignContent campaign={campaign} /> : <ActivityIndicator />;
  }

  // 6. 深度链接分析追踪 | Deep link analytics tracking
  function App() {
    const navigationRef = useRef();

    const linking = {
      prefixes: ['myapp://'],
      config: { /* ... */ },
    };

    useEffect(() => {
      // 追踪初始URL | Track initial URL
      Linking.getInitialURL().then((url) => {
        if (url) {
          analytics.logEvent('deep_link_opened', {
            url,
            source: 'cold_start',
            timestamp: Date.now(),
          });
        }
      });

      // 追踪运行时深度链接 | Track runtime deep links
      const subscription = Linking.addEventListener('url', ({ url }) => {
        analytics.logEvent('deep_link_opened', {
          url,
          source: 'hot_start',
          timestamp: Date.now(),
        });
      });

      return () => subscription.remove();
    }, []);

    return (
      <NavigationContainer
        ref={navigationRef}
        linking={linking}
        onStateChange={(state) => {
          // 追踪深度链接导航成功 | Track successful deep link navigation
          const currentRoute = navigationRef.current?.getCurrentRoute();
          if (currentRoute) {
            analytics.logScreenView({
              screen_name: currentRoute.name,
              screen_params: currentRoute.params,
            });
          }
        }}
      >
        {/* 导航器 | Navigators */}
      </NavigationContainer>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 设计博客应用的文章详情URL，哪个更好：`/article/123`还是`/article/how-to-use-deep-links`？| Design article detail URL for blog app, which is better: `/article/123` or `/article/how-to-use-deep-links`?
    **答案 | Answer:** 第二个(使用slug) - 更具语义，SEO友好，用户可读性更高 | Second (using slug) - More semantic, SEO-friendly, better user readability
  - 深度链接打开需要付费会员的内容，但用户是免费用户，应该如何处理？| Deep link opens premium content but user is free tier, how to handle?
    **答案 | Answer:** 显示内容预览和升级提示，而不是直接拒绝访问 | Show content preview and upgrade prompt instead of direct access denial

  **常见误区检查 | Common Misconception Checks:**
  - 深度链接URL可以包含敏感信息(如token、密码)，因为只有应用能打开 | Deep link URLs can contain sensitive info (like tokens, passwords) since only the app can open them
    **答案 | Answer:** 错误 - URL可能被记录、分享或缓存，不应包含敏感信息 | Incorrect - URLs can be logged, shared, or cached, should not contain sensitive info
  - 所有URL参数都是安全的，不需要验证 | All URL parameters are safe and don't need validation
    **答案 | Answer:** 错误 - 必须验证参数，防止注入攻击、应用崩溃等问题 | Incorrect - Must validate parameters to prevent injection attacks, app crashes, etc.

### 6. Universal Links与App Links入门 | Introduction to Universal Links & App Links (30分钟 | 30 minutes)

- **Universal Links (iOS) 和 App Links (Android) 概述 | Universal Links (iOS) & App Links (Android) Overview**

  **概念定义 | Concept Definition:**
  Universal Links(iOS)和App Links(Android)是基于HTTPS的深度链接技术，允许使用标准Web URL直接打开应用，无需用户确认，且在应用未安装时会自动降级到网页。这是生产环境推荐的深度链接方案。

  Universal Links (iOS) and App Links (Android) are HTTPS-based deep linking technologies that allow standard web URLs to directly open apps without user confirmation, and automatically fallback to web pages when app is not installed. This is the recommended deep linking solution for production environments.

  **核心特征 | Key Characteristics:**
  - HTTPS域名：使用真实的Web URL(如`https://myapp.com/product/123`) | HTTPS domain: uses real web URLs (like `https://myapp.com/product/123`)
  - 无缝体验：无需用户确认，直接打开应用 | Seamless experience: opens app directly without user confirmation
  - 自动降级：应用未安装时在浏览器中打开对应网页 | Automatic fallback: opens corresponding webpage in browser when app not installed
  - 域名验证：需要在服务器上托管配置文件证明域名所有权 | Domain verification: requires hosting configuration file on server to prove domain ownership
  - 安全性高：防止其他应用劫持URL | High security: prevents other apps from hijacking URLs
  - 配置复杂：需要服务器端和客户端配合配置 | Complex setup: requires both server-side and client-side configuration

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Universal Links使用什么协议的URL？| What protocol do Universal Links use?
     **答案 | Answer:** HTTPS - 必须是有效的HTTPS域名，不能是自定义协议 | HTTPS - Must be valid HTTPS domain, not custom protocol
  2. 如果用户设备上没有安装应用，点击Universal Link会怎样？| What happens if user clicks Universal Link on device without the app installed?
     **答案 | Answer:** 在浏览器中打开对应的网页 | Opens corresponding webpage in browser
  3. Universal Links和URL Scheme深度链接的主要区别是什么？| What's the main difference between Universal Links and URL Scheme deep links?
     **答案 | Answer:** Universal Links使用HTTPS URL，无需确认，更安全；URL Scheme使用自定义协议，需要确认 | Universal Links use HTTPS URLs, no confirmation needed, more secure; URL Scheme uses custom protocol, requires confirmation
  4. 要实现Universal Links，只需要配置React Native应用吗？| To implement Universal Links, do you only need to configure the React Native app?
     **答案 | Answer:** 否 | No - 还需要在Web服务器上配置验证文件(iOS: apple-app-site-association, Android: assetlinks.json) | Also need to configure verification file on web server (iOS: apple-app-site-association, Android: assetlinks.json)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Universal Links / App Links 配置概览 | Universal Links / App Links configuration overview

  // 1. React Navigation配置 (同时支持URL Scheme和Universal/App Links)
  //    React Navigation configuration (supports both URL Scheme and Universal/App Links)
  const linking = {
    prefixes: [
      'myapp://',                    // URL Scheme (开发测试) | URL Scheme (dev testing)
      'https://myapp.com',           // Universal Link (iOS生产) | Universal Link (iOS production)
      'https://myapp.com',           // App Link (Android生产) | App Link (Android production)
      'https://*.myapp.com',         // 支持子域名 | Support subdomains
    ],
    config: {
      screens: {
        Home: 'home',
        Product: 'product/:id',
      },
    },
  };

  // 2. iOS Universal Links服务器配置文件示例
  //    iOS Universal Links server configuration file example
  // 文件位置: https://myapp.com/.well-known/apple-app-site-association
  // File location: https://myapp.com/.well-known/apple-app-site-association
  /*
  {
    "applinks": {
      "apps": [],
      "details": [
        {
          "appID": "TEAM_ID.com.mycompany.myapp",
          "paths": [
            "/product/*",
            "/user/*",
            "/article/*"
          ]
        }
      ]
    }
  }
  */

  // 3. Android App Links服务器配置文件示例
  //    Android App Links server configuration file example
  // 文件位置: https://myapp.com/.well-known/assetlinks.json
  // File location: https://myapp.com/.well-known/assetlinks.json
  /*
  [
    {
      "relation": ["delegate_permission/common.handle_all_urls"],
      "target": {
        "namespace": "android_app",
        "package_name": "com.mycompany.myapp",
        "sha256_cert_fingerprints": [
          "SHA256_FINGERPRINT_OF_YOUR_APP"
        ]
      }
    }
  ]
  */

  // 4. iOS原生配置 (ios/MyApp/Info.plist 或 Xcode配置)
  //    iOS native configuration (ios/MyApp/Info.plist or Xcode config)
  /*
  <key>com.apple.developer.associated-domains</key>
  <array>
    <string>applinks:myapp.com</string>
    <string>applinks:*.myapp.com</string>
  </array>
  */

  // 5. Android原生配置 (android/app/src/main/AndroidManifest.xml)
  //    Android native configuration (android/app/src/main/AndroidManifest.xml)
  /*
  <activity android:name=".MainActivity">
    <intent-filter android:autoVerify="true">
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data
        android:scheme="https"
        android:host="myapp.com"
        android:pathPrefix="/product" />
      <data
        android:scheme="https"
        android:host="myapp.com"
        android:pathPrefix="/user" />
    </intent-filter>
  </activity>
  */

  // 6. 测试Universal Links (iOS)
  //    Testing Universal Links (iOS)
  // 注意：Universal Links在模拟器上测试时需要注意:
  // Note: Testing Universal Links on simulator requires attention:
  // - 不能直接在Safari地址栏输入URL (会打开网页而非应用)
  //   Can't directly enter URL in Safari address bar (will open webpage not app)
  // - 需要从备忘录、邮件等其他应用点击链接
  //   Need to click link from other apps like Notes, Mail
  // - 或使用xcrun命令: xcrun simctl openurl booted https://myapp.com/product/123

  // 7. 验证配置是否正确 | Verify configuration is correct
  // iOS: 使用Apple's App Site Association验证器
  // iOS: Use Apple's App Site Association validator
  // https://branch.io/resources/aasa-validator/

  // Android: 使用Google's Digital Asset Links验证器
  // Android: Use Google's Digital Asset Links validator
  // https://developers.google.com/digital-asset-links/tools/generator

  // 8. React Native中的使用方式与URL Scheme完全相同
  //    Usage in React Native is exactly the same as URL Scheme
  function App() {
    return (
      <NavigationContainer linking={linking}>
        <Stack.Navigator>
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Product" component={ProductScreen} />
        </Stack.Navigator>
      </NavigationContainer>
    );
  }
  // 用户点击 https://myapp.com/product/123
  // -> 应用打开 (如果已安装)
  // -> 或在浏览器打开网页 (如果未安装)
  // User clicks https://myapp.com/product/123
  // -> App opens (if installed)
  // -> Or opens webpage in browser (if not installed)
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 开发阶段实现Universal Links需要有真实的服务器域名吗？| Do you need a real server domain to implement Universal Links during development?
    **答案 | Answer:** 是 - 这是Universal Links的限制，开发阶段通常使用URL Scheme代替 | Yes - This is a limitation of Universal Links, usually use URL Scheme during development instead
  - Universal Links配置文件`apple-app-site-association`需要有文件扩展名吗？| Does the Universal Links configuration file `apple-app-site-association` need a file extension?
    **答案 | Answer:** 否 - 必须没有扩展名，且Content-Type为application/json | No - Must have no extension, and Content-Type must be application/json

  **常见误区检查 | Common Misconception Checks:**
  - Universal Links配置好后，所有HTTPS链接都会打开应用 | After configuring Universal Links, all HTTPS links will open the app
    **答案 | Answer:** 错误 - 只有在配置文件(apple-app-site-association)中指定的paths会打开应用 | Incorrect - Only paths specified in configuration file (apple-app-site-association) will open the app
  - Universal Links在iOS模拟器上无法测试 | Universal Links cannot be tested on iOS simulator
    **答案 | Answer:** 错误 - 可以测试，但需要从其他应用(如备忘录)点击链接，或使用xcrun命令 | Incorrect - Can be tested, but need to click link from other apps (like Notes) or use xcrun command

## 实践项目：新闻阅读器深度链接系统 | Practical Project: News Reader Deep Linking System

### 目标 | Objective
构建一个完整的新闻阅读器应用深度链接系统，支持从外部链接直接打开特定文章、分类、作者页面，并处理用户认证、参数验证等实际场景。

Build a complete news reader app deep linking system that supports opening specific articles, categories, and author pages from external links, while handling authentication, parameter validation, and other real scenarios.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 深度链接的三种类型(URL Scheme, Universal Links, App Links)有什么区别？| What's the difference between the three types of deep links (URL Scheme, Universal Links, App Links)?
   **答案 | Answer:** URL Scheme使用自定义协议(如myapp://)，需要用户确认，易于开发测试；Universal/App Links使用HTTPS URL，无需确认，应用未安装时降级到网页，适合生产环境 | URL Scheme uses custom protocol (like myapp://), requires user confirmation, easy for dev testing; Universal/App Links use HTTPS URLs, no confirmation needed, fallback to webpage if app not installed, suitable for production
2. 路径参数和查询参数在route.params中如何区分？| How are path parameters and query parameters distinguished in route.params?
   **答案 | Answer:** 不区分 - 两者都合并在同一个route.params对象中，都是字符串类型 | Not distinguished - Both merged in same route.params object, both are string type
3. 如何测试应用在未运行状态下被深度链接启动的情况？| How to test app being launched via deep link when not running?
   **答案 | Answer:** 完全关闭应用，使用npx uri-scheme open或原生命令打开深度链接 | Completely close app, use npx uri-scheme open or native command to open deep link

### 步骤 | Steps

#### 1. 项目初始化与导航结构 | Project Initialization & Navigation Structure
创建包含Tab Navigator和Stack Navigator的嵌套导航结构
Create nested navigation structure with Tab Navigator and Stack Navigator

#### 2. 配置深度链接 | Configure Deep Linking
在NavigationContainer中配置linking，支持以下URL模式：
Configure linking in NavigationContainer, support following URL patterns:
- `myapp://` - 首页 | Homepage
- `myapp://article/:id` - 文章详情 | Article detail
- `myapp://category/:slug` - 分类文章列表 | Category article list
- `myapp://author/:authorId` - 作者页面 | Author page
- `myapp://search?q=keyword&category=tech` - 搜索 | Search

#### 3. 实现URL参数验证 | Implement URL Parameter Validation
验证文章ID、作者ID的有效性，处理无效参数
Validate article ID and author ID validity, handle invalid parameters

#### 4. 处理认证状态 | Handle Authentication State
收藏的文章需要登录，未登录时跳转到登录页
Bookmarked articles require login, redirect to login page if not authenticated

#### 5. 添加测试和日志 | Add Testing and Logging
实现深度链接日志记录和测试脚本
Implement deep link logging and testing scripts

### 示例代码 | Example Code

```javascript
"""
新闻阅读器深度链接系统 | News Reader Deep Linking System
演示生产级深度链接实现的完整示例 | Complete example demonstrating production-level deep linking implementation

本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
- 深度链接配置与嵌套导航器映射 | Deep link configuration and nested navigator mapping
- 路径参数和查询参数解析 | Path and query parameter parsing
- URL参数验证与错误处理 | URL parameter validation and error handling
- 认证状态处理和受保护路由 | Authentication state handling and protected routes
- 深度链接测试和调试 | Deep link testing and debugging
"""

// ===== 1. 深度链接配置 | Deep Linking Configuration =====
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createStackNavigator } from '@react-navigation/stack';
import { Linking } from 'react-native';
import { useEffect, useRef } from 'react';

const Tab = createBottomTabNavigator();
const Stack = createStackNavigator();

// 定义linking配置 | Define linking configuration
const linking = {
  prefixes: [
    'newsreader://',           // URL Scheme for development
    'https://newsreader.app',  // Universal/App Links for production
  ],
  config: {
    screens: {
      MainTabs: {
        screens: {
          HomeTab: {
            screens: {
              Feed: 'home',
              ArticleDetail: 'article/:articleId',
            },
          },
          CategoriesTab: {
            screens: {
              Categories: 'categories',
              CategoryFeed: 'category/:slug',
            },
          },
          SearchTab: {
            screens: {
              Search: 'search',  // 支持查询参数: ?q=keyword&category=tech
            },
          },
          ProfileTab: {
            screens: {
              Profile: 'profile',
              Bookmarks: 'bookmarks',
              AuthorProfile: 'author/:authorId',
            },
          },
        },
      },
      Login: 'login',
      NotFound: '*',
    },
  },
};

// ===== 2. 主应用组件 | Main App Component =====
function App() {
  const navigationRef = useRef();

  useEffect(() => {
    // 监听并记录深度链接 | Listen and log deep links
    Linking.getInitialURL().then((url) => {
      if (url) {
        console.log('[Deep Link] App opened with URL:', url);
      }
    });

    const subscription = Linking.addEventListener('url', ({ url }) => {
      console.log('[Deep Link] Received while running:', url);
    });

    return () => subscription.remove();
  }, []);

  return (
    <NavigationContainer
      ref={navigationRef}
      linking={linking}
      fallback={<LoadingScreen />}
      onReady={() => {
        console.log('[Navigation] Ready to handle deep links');
      }}
      onStateChange={(state) => {
        const currentRoute = navigationRef.current?.getCurrentRoute();
        if (currentRoute) {
          console.log('[Navigation] Current route:', currentRoute.name);
          console.log('[Navigation] Params:', currentRoute.params);
        }
      }}
    >
      <RootNavigator />
    </NavigationContainer>
  );
}

// ===== 3. 导航器结构 | Navigator Structure =====
function RootNavigator() {
  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      <Stack.Screen name="MainTabs" component={MainTabs} />
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="NotFound" component={NotFoundScreen} />
    </Stack.Navigator>
  );
}

function MainTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="HomeTab" component={HomeStack} options={{ title: 'Home' }} />
      <Tab.Screen name="CategoriesTab" component={CategoriesStack} options={{ title: 'Categories' }} />
      <Tab.Screen name="SearchTab" component={SearchStack} options={{ title: 'Search' }} />
      <Tab.Screen name="ProfileTab" component={ProfileStack} options={{ title: 'Profile' }} />
    </Tab.Navigator>
  );
}

function HomeStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Feed" component={FeedScreen} />
      <Stack.Screen name="ArticleDetail" component={ArticleDetailScreen} />
    </Stack.Navigator>
  );
}

function CategoriesStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Categories" component={CategoriesScreen} />
      <Stack.Screen name="CategoryFeed" component={CategoryFeedScreen} />
    </Stack.Navigator>
  );
}

function SearchStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Search" component={SearchScreen} />
    </Stack.Navigator>
  );
}

function ProfileStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Profile" component={ProfileScreen} />
      <Stack.Screen name="Bookmarks" component={BookmarksScreen} />
      <Stack.Screen name="AuthorProfile" component={AuthorProfileScreen} />
    </Stack.Navigator>
  );
}

// ===== 4. 屏幕组件 (带参数验证) | Screen Components (with parameter validation) =====

// 文章详情页 - 演示路径参数验证 | Article Detail - demonstrates path parameter validation
function ArticleDetailScreen({ route, navigation }) {
  const { articleId } = route.params;
  const [article, setArticle] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // 验证参数格式 | Validate parameter format
    const id = parseInt(articleId, 10);
    if (isNaN(id) || id <= 0) {
      setError('Invalid article ID');
      setLoading(false);
      return;
    }

    // 获取文章数据 | Fetch article data
    fetchArticle(id)
      .then((data) => {
        setArticle(data);
        setLoading(false);
      })
      .catch((err) => {
        if (err.status === 404) {
          setError('Article not found');
        } else {
          setError('Failed to load article');
        }
        setLoading(false);
      });
  }, [articleId]);

  if (loading) return <ActivityIndicator />;

  if (error) {
    return (
      <View style={styles.errorContainer}>
        <Text style={styles.errorText}>{error}</Text>
        <Button title="Go to Home" onPress={() => navigation.navigate('Feed')} />
      </View>
    );
  }

  return (
    <ScrollView>
      <Text style={styles.title}>{article.title}</Text>
      <Text style={styles.author}>By {article.author}</Text>
      <Text style={styles.content}>{article.content}</Text>
      <Button
        title="View Author Profile"
        onPress={() => navigation.navigate('AuthorProfile', { authorId: article.authorId })}
      />
    </ScrollView>
  );
}

// 分类页 - 演示slug参数 | Category Feed - demonstrates slug parameter
function CategoryFeedScreen({ route, navigation }) {
  const { slug } = route.params;
  const [articles, setArticles] = useState([]);

  useEffect(() => {
    // slug验证(只允许小写字母、数字、连字符) | Validate slug (only lowercase, numbers, hyphens)
    if (!/^[a-z0-9-]+$/.test(slug)) {
      navigation.navigate('NotFound');
      return;
    }

    fetchCategoryArticles(slug).then(setArticles);
  }, [slug]);

  return (
    <FlatList
      data={articles}
      renderItem={({ item }) => (
        <ArticleCard
          article={item}
          onPress={() => navigation.navigate('ArticleDetail', { articleId: item.id })}
        />
      )}
    />
  );
}

// 搜索页 - 演示查询参数解析 | Search - demonstrates query parameter parsing
function SearchScreen({ route, navigation }) {
  const { q, category, sort } = route.params || {};
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (q) {
      const searchParams = {
        query: q,
        category: category || 'all',
        sortBy: sort || 'relevance',
      };

      console.log('[Search] Params from deep link:', searchParams);
      performSearch(searchParams).then(setResults);
    }
  }, [q, category, sort]);

  return (
    <View>
      <SearchBar initialValue={q} onSearch={(text) => {/* ... */}} />
      <Text>Category: {category || 'all'}</Text>
      <Text>Sort: {sort || 'relevance'}</Text>
      <FlatList
        data={results}
        renderItem={({ item }) => <ArticleCard article={item} />}
      />
    </View>
  );
}

// 收藏页 - 演示需要认证的路由 | Bookmarks - demonstrates protected route
function BookmarksScreen({ navigation }) {
  const { user, isLoading } = useAuth();

  useEffect(() => {
    if (!isLoading && !user) {
      // 未登录，跳转到登录页 | Not authenticated, redirect to login
      navigation.navigate('Login', {
        returnTo: 'Bookmarks',
      });
    }
  }, [user, isLoading]);

  if (isLoading) return <ActivityIndicator />;
  if (!user) return null;

  return (
    <View>
      <Text>My Bookmarks</Text>
      {/* 收藏列表 | Bookmarks list */}
    </View>
  );
}

// 作者页 - 演示参数验证和链接生成 | Author Profile - demonstrates param validation and link generation
function AuthorProfileScreen({ route, navigation }) {
  const { authorId } = route.params;
  const [author, setAuthor] = useState(null);

  useEffect(() => {
    const id = parseInt(authorId, 10);
    if (isNaN(id)) {
      navigation.navigate('NotFound');
      return;
    }

    fetchAuthor(id).then(setAuthor);
  }, [authorId]);

  const shareAuthorProfile = () => {
    const url = `newsreader://author/${authorId}`;
    Share.share({
      message: `Check out ${author.name}'s profile: ${url}`,
    });
  };

  return author ? (
    <View>
      <Text style={styles.authorName}>{author.name}</Text>
      <Text>Articles: {author.articleCount}</Text>
      <Button title="Share Profile" onPress={shareAuthorProfile} />
    </View>
  ) : <ActivityIndicator />;
}

// 404页面 | 404 Page
function NotFoundScreen({ route, navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Page Not Found</Text>
      <Text>The link you followed may be broken or expired.</Text>
      <Button title="Go to Home" onPress={() => navigation.navigate('MainTabs')} />
    </View>
  );
}

// ===== 5. 测试脚本 | Testing Scripts =====
/*
深度链接测试命令 | Deep Link Testing Commands

# 首页 | Homepage
npx uri-scheme open "newsreader://home" --ios

# 文章详情 | Article detail
npx uri-scheme open "newsreader://article/123" --ios
npx uri-scheme open "newsreader://article/456" --android

# 分类页 | Category page
npx uri-scheme open "newsreader://category/technology" --ios

# 搜索(带查询参数) | Search (with query params)
npx uri-scheme open "newsreader://search?q=react&category=tech&sort=recent" --ios

# 作者页 | Author page
npx uri-scheme open "newsreader://author/42" --android

# 无效URL测试 | Invalid URL testing
npx uri-scheme open "newsreader://article/invalid" --ios
npx uri-scheme open "newsreader://nonexistent/path" --android

# 应用状态测试 | App state testing
# 1. 完全关闭应用 | Completely close app
# 2. 运行以下命令 | Run following command
npx uri-scheme open "newsreader://article/999" --ios
# 3. 验证应用启动并直接打开文章页 | Verify app launches and directly opens article page
*/
```

### 项目完成检查 | Project Completion Check

1. 深度链接配置是否覆盖了所有主要屏幕路径？| Does deep link configuration cover all main screen paths?
2. URL参数验证是否完整(类型检查、范围检查、格式验证)？| Is URL parameter validation complete (type check, range check, format validation)?
3. 受保护的路由(如收藏页)是否正确处理未登录状态？| Do protected routes (like bookmarks) correctly handle unauthenticated state?
4. 无效URL是否有友好的降级处理(404页面)？| Do invalid URLs have friendly fallback handling (404 page)?
5. 是否添加了日志记录以便调试深度链接流程？| Are logs added for debugging deep link flow?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **深度链接与导航状态理解练习 | Deep Linking & Navigation State Understanding Exercise**
   - **练习描述 | Exercise Description:** 绘制应用的导航树，标注每个屏幕的深度链接路径，理解URL结构与导航层级的映射关系
   - **概念检查 | Concept Check:** 能否解释为什么`myapp://article/123`可以直接导航到深层嵌套的ArticleDetail屏幕？
   - **学习目标 | Learning Objective:** 深入理解React Navigation如何将扁平的URL映射到嵌套的导航结构

2. **参数解析与类型转换练习 | Parameter Parsing & Type Conversion Exercise**
   - **练习描述 | Exercise Description:** 实现一个通用的URL参数验证hook `useValidatedParams`，自动处理类型转换和验证
   - **概念检查 | Concept Check:** 为什么所有URL参数都是字符串类型？如何安全地转换为其他类型？
   - **学习目标 | Learning Objective:** 掌握URL参数的类型系统和安全验证模式

3. **认证流程与深度链接集成练习 | Auth Flow & Deep Linking Integration Exercise**
   - **练习描述 | Exercise Description:** 实现"保存目标URL → 登录 → 返回目标页"的完整流程
   - **概念检查 | Concept Check:** 如何在登录后恢复用户原本想访问的深度链接目标？
   - **学习目标 | Learning Objective:** 理解深度链接在复杂用户流程中的状态管理

4. **错误处理与降级策略练习 | Error Handling & Fallback Strategy Exercise**
   - **练习描述 | Exercise Description:** 为深度链接系统设计完整的错误处理策略(无效ID、过期内容、网络错误等)
   - **概念检查 | Concept Check:** 哪些类型的深度链接错误应该导航到404页，哪些应该显示重试？
   - **学习目标 | Learning Objective:** 培养生产级应用的错误处理思维

5. **深度链接分析与追踪练习 | Deep Link Analytics & Tracking Exercise**
   - **练习描述 | Exercise Description:** 实现深度链接的分析追踪系统，记录用户来源、转化率、最受欢迎的链接等
   - **概念检查 | Concept Check:** 如何区分深度链接的来源(邮件、社交媒体、推送通知)？
   - **学习目标 | Learning Objective:** 理解深度链接在产品分析中的价值

6. **Universal Links原理探索练习 | Universal Links Principle Exploration Exercise**
   - **练习描述 | Exercise Description:** 研究Universal Links的域名验证机制，尝试配置一个测试环境
   - **概念检查 | Concept Check:** 为什么Universal Links比URL Scheme更安全？
   - **学习目标 | Learning Objective:** 深入理解生产环境深度链接的技术原理

7. **动态链接生成与分享练习 | Dynamic Link Generation & Sharing Exercise**
   - **练习描述 | Exercise Description:** 实现一个"分享文章"功能，生成带追踪参数的深度链接
   - **概念检查 | Concept Check:** 如何通过查询参数追踪分享来源和用户行为？
   - **学习目标 | Learning Objective:** 掌握深度链接的实际应用场景

## 学习资源 | Learning Resources

- [React Navigation Deep Linking 官方文档 | React Navigation Deep Linking Official Docs](https://reactnavigation.org/docs/deep-linking)
- [React Navigation Configuring Links | Configuring Links Guide](https://reactnavigation.org/docs/configuring-links)
- [Apple Universal Links 文档 | Apple Universal Links Documentation](https://developer.apple.com/ios/universal-links/)
- [Android App Links 文档 | Android App Links Documentation](https://developer.android.com/training/app-links)
- [React Native Linking API | Linking API Reference](https://reactnative.dev/docs/linking)
- [Branch.io Deep Linking 最佳实践 | Branch.io Deep Linking Best Practices](https://help.branch.io/developers-hub/docs/react-native)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解深度链接的三种类型及其区别(URL Scheme, Universal Links, App Links)
- [ ] 能够配置React Navigation的linking对象实现基础深度链接
- [ ] 掌握路径参数(:param)和查询参数(?key=value)的定义和解析
- [ ] 会为嵌套导航器配置深度链接路径
- [ ] 能够使用uri-scheme或原生命令测试深度链接
- [ ] 理解如何处理应用启动时的初始URL
- [ ] 实现了URL参数的验证和错误处理
- [ ] 掌握受保护路由与深度链接的集成(认证检查)
- [ ] 了解Universal Links和App Links的基础配置要求
- [ ] 完成实践项目：新闻阅读器深度链接系统

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释：
1. 深度链接如何将URL映射到嵌套导航结构
2. 路径参数和查询参数的使用场景区别
3. 为什么需要验证URL参数以及如何验证
4. Universal Links相比URL Scheme的优势

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others:
1. How deep links map URLs to nested navigation structure
2. Usage scenario differences between path parameters and query parameters
3. Why URL parameter validation is needed and how to validate
4. Advantages of Universal Links compared to URL Scheme
