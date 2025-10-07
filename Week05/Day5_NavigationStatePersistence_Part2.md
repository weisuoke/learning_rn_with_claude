# React Native入门 - 第5天：导航状态持久化与状态管理集成 (Part 2) | React Native Introduction - Day 5: Navigation State Persistence & State Management Integration (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. useFocusEffect钩子深入 | Deep Dive into useFocusEffect Hook (45分钟 | 45 minutes)

- **useFocusEffect概念 | useFocusEffect Concept**

  **概念定义 | Concept Definition:**
  useFocusEffect是React Navigation提供的自定义hook，用于在屏幕获得焦点时执行副作用操作（如数据刷新、订阅、分析追踪）。与useEffect不同,它只在屏幕聚焦时运行，并在失焦或卸载时清理，避免在后台屏幕执行不必要的操作。| useFocusEffect is a custom hook provided by React Navigation for executing side effects when a screen gains focus (such as data refreshing, subscriptions, analytics tracking). Unlike useEffect, it only runs when the screen is focused and cleans up when unfocused or unmounted, avoiding unnecessary operations on background screens.

  **核心特征 | Key Characteristics:**
  - 焦点感知：只在屏幕进入视图时执行 | Focus-aware: Only executes when screen enters view
  - 自动清理：屏幕失焦时运行清理函数 | Automatic cleanup: Runs cleanup function when screen loses focus
  - 避免内存泄漏：防止在后台屏幕保持订阅或定时器 | Prevents memory leaks: Avoids keeping subscriptions or timers on background screens
  - 导航集成：与导航生命周期完美同步 | Navigation integration: Perfectly synced with navigation lifecycle

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useFocusEffect会在组件首次挂载时运行吗？| Does useFocusEffect run on initial component mount?
     **答案 | Answer:** 是 | Yes - 首次挂载时屏幕获得焦点，会运行effect | Screen gains focus on first mount, effect runs
  2. 当用户从屏幕A导航到屏幕B，屏幕A的useFocusEffect会发生什么？| What happens to screen A's useFocusEffect when user navigates from A to B?
     **答案 | Answer:** 运行清理函数 | Runs cleanup function - 因为屏幕A失去焦点 | Because screen A loses focus
  3. useFocusEffect与useEffect有什么本质区别？| What's the fundamental difference between useFocusEffect and useEffect?
     **答案 | Answer:** useEffect基于组件挂载/卸载，useFocusEffect基于屏幕焦点状态 | useEffect based on component mount/unmount, useFocusEffect based on screen focus state
  4. 是否应该在useFocusEffect中进行数据获取？| Should data fetching be done in useFocusEffect?
     **答案 | Answer:** 视情况而定 | It depends - 如果需要每次进入屏幕都刷新数据则适合，否则用useEffect | Suitable if need to refresh data every time entering screen, otherwise use useEffect

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // useFocusEffect基础用法 | Basic useFocusEffect usage
  import { useFocusEffect } from '@react-navigation/native';
  import { useCallback, useState } from 'react';

  // 示例1：屏幕聚焦时刷新数据 | Example 1: Refresh data when screen focuses
  function ProfileScreen() {
    const [profile, setProfile] = useState(null);
    const [loading, setLoading] = useState(false);

    useFocusEffect(
      useCallback(() => {
        // 屏幕聚焦时执行 | Execute when screen focuses
        console.log('Profile screen focused');

        let isActive = true; // 防止竞态条件 | Prevent race condition

        const fetchProfile = async () => {
          setLoading(true);
          try {
            const data = await api.getProfile();
            if (isActive) { // 只在仍然活跃时更新状态 | Only update state if still active
              setProfile(data);
            }
          } catch (error) {
            console.error(error);
          } finally {
            if (isActive) {
              setLoading(false);
            }
          }
        };

        fetchProfile();

        // 清理函数：屏幕失焦时执行 | Cleanup function: Execute when screen loses focus
        return () => {
          isActive = false; // 标记为不活跃 | Mark as inactive
          console.log('Profile screen unfocused');
        };
      }, []) // 依赖数组通常为空 | Dependency array usually empty
    );

    if (loading) return <LoadingSpinner />;
    return <ProfileView profile={profile} />;
  }

  // 示例2：聚焦时启动/停止定时器 | Example 2: Start/stop timer on focus
  function ChatScreen() {
    const [messages, setMessages] = useState([]);

    useFocusEffect(
      useCallback(() => {
        // 启动轮询 | Start polling
        const interval = setInterval(() => {
          fetchNewMessages().then(newMessages => {
            setMessages(prev => [...prev, ...newMessages]);
          });
        }, 5000); // 每5秒检查新消息 | Check for new messages every 5 seconds

        // 清理：停止轮询 | Cleanup: Stop polling
        return () => {
          clearInterval(interval);
          console.log('Stopped polling for messages');
        };
      }, [])
    );

    return <MessageList messages={messages} />;
  }

  // 示例3：结合useCallback避免不必要的重新订阅 | Example 3: Combine with useCallback to avoid unnecessary re-subscriptions
  function NotificationsScreen({ userId }: Props) {
    const [notifications, setNotifications] = useState([]);

    useFocusEffect(
      useCallback(() => {
        // 订阅推送通知 | Subscribe to push notifications
        const unsubscribe = subscribeToPushNotifications(userId, (notification) => {
          setNotifications(prev => [notification, ...prev]);
        });

        // 清理：取消订阅 | Cleanup: Unsubscribe
        return () => {
          unsubscribe();
          console.log('Unsubscribed from push notifications');
        };
      }, [userId]) // userId变化时重新订阅 | Re-subscribe when userId changes
    );

    return <NotificationList notifications={notifications} />;
  }

  // 示例4：分析追踪（屏幕浏览统计）| Example 4: Analytics tracking (screen view statistics)
  function ArticleDetailScreen({ route }: Props) {
    const { articleId } = route.params;

    useFocusEffect(
      useCallback(() => {
        // 记录屏幕浏览 | Log screen view
        analytics.logScreenView({
          screen_name: 'ArticleDetail',
          screen_class: 'ArticleDetailScreen',
          article_id: articleId,
        });

        const startTime = Date.now();

        return () => {
          // 记录停留时间 | Log time spent
          const timeSpent = Date.now() - startTime;
          analytics.logEvent('article_view_duration', {
            article_id: articleId,
            duration_ms: timeSpent,
          });
        };
      }, [articleId])
    );

    return <ArticleContent articleId={articleId} />;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用useCallback包装useFocusEffect的回调？| Why wrap useFocusEffect callback with useCallback?
    **答案 | Answer:** 避免每次渲染都创建新函数，防止不必要的effect重新执行 | Avoid creating new function on every render, prevent unnecessary effect re-execution
  - isActive变量在示例1中的作用是什么？| What is the purpose of the isActive variable in Example 1?
    **答案 | Answer:** 防止竞态条件，当屏幕快速失焦时避免更新已卸载组件的状态 | Prevent race conditions, avoid updating state of unmounted component when screen quickly loses focus
  - 为什么示例2的清理函数要清除interval？| Why does Example 2's cleanup function clear the interval?
    **答案 | Answer:** 屏幕失焦后停止轮询，节省资源并避免内存泄漏 | Stop polling after screen loses focus, save resources and avoid memory leaks

  **常见误区检查 | Common Misconception Checks:**
  - useFocusEffect可以替代所有useEffect吗？| Can useFocusEffect replace all useEffect?
    **答案 | Answer:** 否 | No - 只用于需要响应屏幕焦点变化的场景，其他副作用仍用useEffect | Only for scenarios needing response to screen focus changes, other side effects still use useEffect
  - 是否需要在useFocusEffect中手动检查屏幕是否聚焦？| Is it necessary to manually check if screen is focused in useFocusEffect?
    **答案 | Answer:** 否 | No - useFocusEffect已经处理了焦点检测，无需手动判断 | useFocusEffect already handles focus detection, no manual judgment needed

- **最佳实践与性能优化 | Best Practices and Performance Optimization**

  **概念定义 | Concept Definition:**
  正确使用useFocusEffect需要遵循一系列最佳实践，包括依赖管理、避免过度刷新、合理使用清理函数、以及与其他hooks的配合。这些实践可以确保应用性能、避免内存泄漏、提供流畅的用户体验。| Proper use of useFocusEffect requires following a series of best practices, including dependency management, avoiding excessive refreshes, proper use of cleanup functions, and coordination with other hooks. These practices ensure app performance, avoid memory leaks, and provide smooth user experience.

  **核心特征 | Key Characteristics:**
  - 依赖最小化：只在依赖数组中包含必要的值 | Minimize dependencies: Only include necessary values in dependency array
  - 防抖优化：避免频繁的焦点切换触发过多请求 | Debounce optimization: Avoid frequent focus switches triggering too many requests
  - 条件执行：基于应用状态决定是否执行effect | Conditional execution: Decide whether to execute effect based on app state
  - 错误处理：妥善处理异步操作中的错误 | Error handling: Properly handle errors in asynchronous operations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 每次屏幕聚焦都应该重新获取数据吗？| Should data be refetched every time screen focuses?
     **答案 | Answer:** 不一定 | Not necessarily - 可以使用缓存策略，根据数据时效性决定 | Can use caching strategy, decide based on data freshness
  2. 是否可以在useFocusEffect中调用多个异步操作？| Can multiple asynchronous operations be called in useFocusEffect?
     **答案 | Answer:** 可以 | Yes - 但要注意竞态条件和取消机制 | But be careful with race conditions and cancellation mechanisms
  3. 深层嵌套的屏幕切换会触发所有父屏幕的useFocusEffect吗？| Does deep nested screen switching trigger useFocusEffect on all parent screens?
     **答案 | Answer:** 否 | No - 只有失去焦点的屏幕会运行清理，获得焦点的屏幕运行effect | Only screens losing focus run cleanup, screens gaining focus run effect

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // useFocusEffect最佳实践 | useFocusEffect best practices

  // 实践1：使用缓存避免过度刷新 | Practice 1: Use caching to avoid excessive refreshes
  import { useFocusEffect } from '@react-navigation/native';
  import { useCallback, useRef } from 'react';

  function ProductListScreen() {
    const [products, setProducts] = useState([]);
    const lastFetchTime = useRef(0); // 记录上次获取时间 | Record last fetch time
    const CACHE_DURATION = 5 * 60 * 1000; // 5分钟缓存 | 5-minute cache

    useFocusEffect(
      useCallback(() => {
        const now = Date.now();
        const timeSinceLastFetch = now - lastFetchTime.current;

        // 只有超过缓存时间才重新获取 | Only refetch if cache expired
        if (timeSinceLastFetch > CACHE_DURATION) {
          fetchProducts().then(data => {
            setProducts(data);
            lastFetchTime.current = now; // 更新获取时间 | Update fetch time
          });
        } else {
          console.log('Using cached data');
        }
      }, [])
    );

    return <ProductGrid products={products} />;
  }

  // 实践2：条件性执行effect | Practice 2: Conditional effect execution
  import { useSelector } from 'react-redux';

  function DashboardScreen() {
    const isOnline = useSelector(state => state.network.isOnline);
    const [data, setData] = useState(null);

    useFocusEffect(
      useCallback(() => {
        // 只在在线时获取数据 | Only fetch data when online
        if (!isOnline) {
          console.log('Offline, skipping data fetch');
          return; // 提前返回，不执行数据获取 | Early return, don't fetch data
        }

        let cancelled = false;

        const loadData = async () => {
          try {
            const result = await api.getDashboardData();
            if (!cancelled) {
              setData(result);
            }
          } catch (error) {
            if (!cancelled) {
              // 显示错误提示 | Show error message
              showError(error.message);
            }
          }
        };

        loadData();

        return () => {
          cancelled = true; // 标记为已取消 | Mark as cancelled
        };
      }, [isOnline]) // 在线状态变化时重新执行 | Re-execute when online status changes
    );

    return <DashboardView data={data} />;
  }

  // 实践3：结合useIsFocused hook进行条件渲染 | Practice 3: Combine with useIsFocused hook for conditional rendering
  import { useIsFocused } from '@react-navigation/native';

  function VideoPlayerScreen({ route }: Props) {
    const { videoUrl } = route.params;
    const isFocused = useIsFocused(); // 获取当前焦点状态 | Get current focus state

    useFocusEffect(
      useCallback(() => {
        console.log('Video screen focused');

        return () => {
          console.log('Video screen unfocused');
        };
      }, [])
    );

    // 只在屏幕聚焦时播放视频 | Only play video when screen is focused
    return (
      <View>
        <VideoPlayer
          url={videoUrl}
          paused={!isFocused} // 失焦时暂停 | Pause when unfocused
        />
      </View>
    );
  }

  // 实践4：复杂的清理逻辑 | Practice 4: Complex cleanup logic
  function LiveChatScreen() {
    const [messages, setMessages] = useState([]);
    const wsRef = useRef<WebSocket | null>(null);
    const reconnectTimeoutRef = useRef<NodeJS.Timeout>();

    useFocusEffect(
      useCallback(() => {
        // 建立WebSocket连接 | Establish WebSocket connection
        const connectWebSocket = () => {
          wsRef.current = new WebSocket('wss://chat.example.com');

          wsRef.current.onmessage = (event) => {
            const message = JSON.parse(event.data);
            setMessages(prev => [...prev, message]);
          };

          wsRef.current.onerror = () => {
            // 3秒后重连 | Reconnect after 3 seconds
            reconnectTimeoutRef.current = setTimeout(connectWebSocket, 3000);
          };
        };

        connectWebSocket();

        // 复杂清理：关闭WebSocket和取消重连 | Complex cleanup: Close WebSocket and cancel reconnection
        return () => {
          if (wsRef.current) {
            wsRef.current.close();
            wsRef.current = null;
          }
          if (reconnectTimeoutRef.current) {
            clearTimeout(reconnectTimeoutRef.current);
          }
          console.log('Cleaned up WebSocket connection');
        };
      }, [])
    );

    return <ChatMessages messages={messages} />;
  }

  // 实践5：与React Query集成 | Practice 5: Integration with React Query
  import { useQuery, useQueryClient } from '@tanstack/react-query';

  function OrdersScreen() {
    const queryClient = useQueryClient();

    // 使用React Query管理数据 | Use React Query to manage data
    const { data: orders } = useQuery({
      queryKey: ['orders'],
      queryFn: fetchOrders,
      staleTime: 5 * 60 * 1000, // 5分钟内认为数据新鲜 | Consider data fresh within 5 minutes
    });

    useFocusEffect(
      useCallback(() => {
        // 屏幕聚焦时使缓存失效（下次访问会重新获取）| Invalidate cache on screen focus (will refetch on next access)
        queryClient.invalidateQueries({ queryKey: ['orders'] });
      }, [queryClient])
    );

    return <OrderList orders={orders} />;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - lastFetchTime使用useRef而不是useState的原因是什么？| Why use useRef instead of useState for lastFetchTime?
    **答案 | Answer:** useRef不会触发重新渲染，且可以在effect间保持值 | useRef doesn't trigger re-render and can preserve value between effects
  - 为什么VideoPlayerScreen中使用useIsFocused而不只用useFocusEffect？| Why use useIsFocused in VideoPlayerScreen instead of just useFocusEffect?
    **答案 | Answer:** useIsFocused可以在渲染时读取焦点状态，用于条件渲染；useFocusEffect用于副作用 | useIsFocused can read focus state during render for conditional rendering; useFocusEffect for side effects
  - React Query的invalidateQueries在useFocusEffect中如何工作？| How does React Query's invalidateQueries work in useFocusEffect?
    **答案 | Answer:** 标记查询为过期，下次访问时React Query会自动重新获取数据 | Marks query as stale, React Query automatically refetches data on next access

### 5. 通知驱动的导航 | Notification-Driven Navigation (30分钟 | 30 minutes)

- **通知处理与深链接 | Notification Handling and Deep Linking**

  **概念定义 | Concept Definition:**
  通知驱动的导航是指应用根据推送通知或本地通知的内容，自动导航到特定屏幕并传递相关参数。这涉及解析通知payload、判断应用状态（前台/后台/关闭）、以及使用navigationRef在非组件上下文中执行导航操作。| Notification-driven navigation refers to the app automatically navigating to specific screens and passing relevant parameters based on the content of push notifications or local notifications. This involves parsing notification payloads, determining app state (foreground/background/closed), and using navigationRef to perform navigation operations in non-component contexts.

  **核心特征 | Key Characteristics:**
  - 状态感知：根据应用状态（前台/后台）采取不同导航策略 | State-aware: Different navigation strategies based on app state (foreground/background)
  - 延迟导航：应用启动时延迟执行导航直到导航器就绪 | Delayed navigation: Delay navigation execution until navigator is ready on app startup
  - 参数传递：从通知payload提取参数传递给目标屏幕 | Parameter passing: Extract parameters from notification payload to pass to target screen
  - 错误处理：妥善处理无效通知或导航失败 | Error handling: Properly handle invalid notifications or navigation failures

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应用在关闭状态收到通知并被打开时，导航操作应该何时执行？| When should navigation operations execute when app receives notification and is opened from closed state?
     **答案 | Answer:** 等待NavigationContainer挂载并就绪后 | After NavigationContainer is mounted and ready
  2. 前台收到通知时应该立即导航吗？| Should app navigate immediately when receiving notification in foreground?
     **答案 | Answer:** 不一定 | Not necessarily - 可以显示应用内通知让用户选择，或静默处理 | Can show in-app notification for user choice, or handle silently
  3. 通知payload通常包含哪些导航信息？| What navigation information does notification payload usually contain?
     **答案 | Answer:** 目标屏幕名称、路由参数、深链接URL等 | Target screen name, route parameters, deep link URL, etc.
  4. 是否需要验证通知payload的有效性？| Is it necessary to validate notification payload validity?
     **答案 | Answer:** 是 | Yes - 避免无效数据导致应用崩溃或错误导航 | Avoid invalid data causing app crashes or wrong navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 通知驱动导航的完整实现 | Complete implementation of notification-driven navigation

  // 1. 创建通知服务（src/services/notificationService.ts）| Create notification service
  import notifee, { EventType } from '@notifee/react-native';
  import messaging from '@react-native-firebase/messaging';
  import * as RootNavigation from '../navigation/navigationRef';

  interface NotificationData {
    type: 'message' | 'order' | 'post' | 'general';
    screen: string;
    params?: Record<string, any>;
    deepLink?: string;
  }

  // 解析通知数据 | Parse notification data
  function parseNotificationData(remoteMessage: any): NotificationData | null {
    try {
      const { data } = remoteMessage;

      // 验证必需字段 | Validate required fields
      if (!data || !data.type) {
        console.warn('Invalid notification data');
        return null;
      }

      return {
        type: data.type,
        screen: data.screen,
        params: data.params ? JSON.parse(data.params) : undefined,
        deepLink: data.deepLink,
      };
    } catch (error) {
      console.error('Failed to parse notification data:', error);
      return null;
    }
  }

  // 执行导航（支持深链接和直接屏幕导航）| Execute navigation (supports deep links and direct screen navigation)
  function navigateFromNotification(data: NotificationData) {
    // 优先使用深链接 | Prefer deep link
    if (data.deepLink) {
      // 假设配置了Linking配置 | Assume Linking config is set up
      Linking.openURL(data.deepLink).catch(error => {
        console.error('Failed to open deep link:', error);
      });
      return;
    }

    // 使用屏幕名称和参数导航 | Navigate using screen name and parameters
    if (data.screen) {
      RootNavigation.navigate(data.screen, data.params);
    }
  }

  // 初始化通知处理 | Initialize notification handling
  export function initializeNotifications() {
    // 处理前台消息 | Handle foreground messages
    messaging().onMessage(async (remoteMessage) => {
      console.log('Foreground notification received:', remoteMessage);

      const data = parseNotificationData(remoteMessage);
      if (!data) return;

      // 显示应用内通知 | Show in-app notification
      await notifee.displayNotification({
        title: remoteMessage.notification?.title,
        body: remoteMessage.notification?.body,
        data: data as any, // 保存数据供点击时使用 | Save data for tap handling
        android: {
          channelId: 'default',
          pressAction: { id: 'default' }, // 启用点击事件 | Enable tap event
        },
      });
    });

    // 处理后台/退出状态的消息点击 | Handle background/quit state message taps
    messaging().onNotificationOpenedApp((remoteMessage) => {
      console.log('Background notification opened:', remoteMessage);

      const data = parseNotificationData(remoteMessage);
      if (data) {
        // 稍微延迟确保导航器就绪 | Slight delay to ensure navigator is ready
        setTimeout(() => navigateFromNotification(data), 500);
      }
    });

    // 检查启动时的通知（应用完全关闭时收到通知）| Check notification on launch (app was quit)
    messaging()
      .getInitialNotification()
      .then((remoteMessage) => {
        if (remoteMessage) {
          console.log('App opened from quit state by notification:', remoteMessage);

          const data = parseNotificationData(remoteMessage);
          if (data) {
            // 保存到状态，等待导航器就绪后执行 | Save to state, execute after navigator is ready
            RootNavigation.setPendingNotificationNavigation(data);
          }
        }
      });

    // 处理本地通知点击（使用notifee）| Handle local notification taps (using notifee)
    return notifee.onForegroundEvent(({ type, detail }) => {
      if (type === EventType.PRESS) {
        const data = detail.notification?.data as NotificationData;
        if (data) {
          navigateFromNotification(data);
        }
      }
    });
  }

  // 2. 在navigationRef中添加待处理导航逻辑 | Add pending navigation logic in navigationRef
  // src/navigation/navigationRef.ts
  import { createNavigationContainerRef } from '@react-navigation/native';

  export const navigationRef = createNavigationContainerRef();
  let pendingNavigation: NotificationData | null = null;

  export function setPendingNotificationNavigation(data: NotificationData) {
    pendingNavigation = data;
  }

  export function executePendingNavigation() {
    if (pendingNavigation && navigationRef.isReady()) {
      navigateFromNotification(pendingNavigation);
      pendingNavigation = null; // 清除 | Clear
    }
  }

  // 3. 在App组件中初始化 | Initialize in App component
  // src/App.tsx
  import { useEffect } from 'react';
  import { initializeNotifications } from './services/notificationService';
  import { navigationRef, executePendingNavigation } from './navigation/navigationRef';

  function App() {
    useEffect(() => {
      // 初始化通知监听 | Initialize notification listeners
      const unsubscribe = initializeNotifications();

      return unsubscribe; // 清理 | Cleanup
    }, []);

    return (
      <NavigationContainer
        ref={navigationRef}
        onReady={() => {
          // 导航器就绪后执行待处理的导航 | Execute pending navigation after navigator is ready
          executePendingNavigation();
        }}
      >
        <RootNavigator />
      </NavigationContainer>
    );
  }

  // 4. 示例：发送本地通知 | Example: Send local notification
  async function sendLocalNotification(type: string, screen: string, params: any) {
    await notifee.displayNotification({
      title: 'New Activity',
      body: 'You have a new message',
      data: {
        type,
        screen,
        params: JSON.stringify(params),
      } as any,
      android: {
        channelId: 'default',
        pressAction: { id: 'default' },
      },
    });
  }

  // 使用示例 | Usage example
  sendLocalNotification('message', 'Chat', { chatId: '123', userId: '456' });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么在后台消息打开时使用setTimeout延迟导航？| Why use setTimeout to delay navigation when opening background messages?
    **答案 | Answer:** 确保NavigationContainer完全初始化和就绪，避免过早的导航调用失败 | Ensure NavigationContainer is fully initialized and ready, avoid premature navigation call failures
  - getInitialNotification和onNotificationOpenedApp有什么区别？| What's the difference between getInitialNotification and onNotificationOpenedApp?
    **答案 | Answer:** getInitialNotification用于应用完全关闭时的启动，onNotificationOpenedApp用于后台状态 | getInitialNotification for app launch from quit state, onNotificationOpenedApp for background state
  - 为什么需要parseNotificationData函数？| Why need the parseNotificationData function?
    **答案 | Answer:** 验证和标准化通知数据格式，防止无效或恶意数据导致问题 | Validate and normalize notification data format, prevent issues from invalid or malicious data

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在消息处理器中直接使用useNavigation hook？| Can useNavigation hook be used directly in message handlers?
    **答案 | Answer:** 否 | No - hooks只能在React组件中使用，必须使用navigationRef | Hooks can only be used in React components, must use navigationRef
  - 前台收到通知时必须立即导航吗？| Must navigate immediately when receiving notification in foreground?
    **答案 | Answer:** 否 | No - 最佳实践是显示应用内提示让用户决定，避免打断当前操作 | Best practice is showing in-app prompt for user decision, avoid interrupting current operation

### 6. 综合实践与高级模式 | Comprehensive Practice and Advanced Patterns (30分钟 | 30 minutes)

- **多层导航状态协调 | Multi-Layer Navigation State Coordination**

  **概念定义 | Concept Definition:**
  多层导航状态协调是指在复杂应用中管理多个导航器（如嵌套的Tab和Stack导航器）的状态、认证流程、以及全局状态之间的协调。这需要设计清晰的状态架构、定义状态优先级、并确保各层状态变化能够正确传播和响应。| Multi-layer navigation state coordination refers to managing the state of multiple navigators (such as nested Tab and Stack navigators), authentication flows, and coordination between global state in complex applications. This requires designing clear state architecture, defining state priorities, and ensuring state changes at various layers can correctly propagate and respond.

  **核心特征 | Key Characteristics:**
  - 层次化状态：区分导航状态、认证状态、业务状态 | Hierarchical state: Distinguish navigation state, auth state, business state
  - 状态驱动导航：高层状态变化驱动整个导航结构变化 | State-driven navigation: High-level state changes drive entire navigation structure changes
  - 优先级管理：确定认证、权限检查在导航前执行 | Priority management: Ensure auth, permission checks execute before navigation
  - 状态同步：保持持久化状态与内存状态一致 | State synchronization: Keep persistent state and in-memory state consistent

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 认证状态变化应该影响导航结构还是只影响屏幕内容？| Should auth state changes affect navigation structure or only screen content?
     **答案 | Answer:** 通常影响导航结构 | Usually affect navigation structure - 未登录显示AuthStack，已登录显示AppStack | Show AuthStack when not logged in, show AppStack when logged in
  2. 多个状态同时变化时如何确定导航行为？| How to determine navigation behavior when multiple states change simultaneously?
     **答案 | Answer:** 设定优先级规则，如认证状态 > 权限状态 > 业务状态 | Set priority rules, such as auth state > permission state > business state
  3. 嵌套导航器的状态持久化应该分别管理还是统一管理？| Should state persistence of nested navigators be managed separately or uniformly?
     **答案 | Answer:** 统一管理 | Uniformly - 通过根NavigationContainer的onStateChange捕获整个导航树状态 | Capture entire navigation tree state through root NavigationContainer's onStateChange
  4. 深链接导航是否需要检查用户权限？| Does deep link navigation need to check user permissions?
     **答案 | Answer:** 是 | Yes - 应该在导航前验证用户有权限访问目标屏幕 | Should verify user has permission to access target screen before navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 多层导航状态协调的完整实现 | Complete implementation of multi-layer navigation state coordination

  // 1. 定义应用状态类型（src/types/state.ts）| Define application state types
  export type AppState = {
    auth: {
      isAuthenticated: boolean;
      user: User | null;
      loading: boolean; // 认证检查中 | Auth check in progress
    };
    onboarding: {
      completed: boolean;
    };
    permissions: {
      cameraGranted: boolean;
      locationGranted: boolean;
    };
  };

  // 2. 创建全局状态store（使用Zustand）| Create global state store (using Zustand)
  // src/store/appStore.ts
  import { create } from 'zustand';
  import { persist, createJSONStorage } from 'zustand/middleware';
  import AsyncStorage from '@react-native-async-storage/async-storage';

  export const useAppStore = create<AppState>()(
    persist(
      (set) => ({
        auth: {
          isAuthenticated: false,
          user: null,
          loading: true, // 初始加载状态 | Initial loading state
        },
        onboarding: {
          completed: false,
        },
        permissions: {
          cameraGranted: false,
          locationGranted: false,
        },

        // Actions
        setAuthState: (auth: Partial<AppState['auth']>) =>
          set((state) => ({ auth: { ...state.auth, ...auth } })),

        completeOnboarding: () =>
          set((state) => ({ onboarding: { ...state.onboarding, completed: true } })),

        updatePermissions: (permissions: Partial<AppState['permissions']>) =>
          set((state) => ({ permissions: { ...state.permissions, ...permissions } })),
      }),
      {
        name: 'app-storage',
        storage: createJSONStorage(() => AsyncStorage),
        partialize: (state) => ({
          // 只持久化部分状态 | Only persist partial state
          onboarding: state.onboarding,
          permissions: state.permissions,
          // 不持久化认证状态（出于安全考虑）| Don't persist auth state (security)
        }),
      }
    )
  );

  // 3. 智能根导航器（协调多层状态）| Smart root navigator (coordinate multi-layer state)
  // src/navigation/RootNavigator.tsx
  import { useAppStore } from '../store/appStore';
  import { useEffect } from 'react';

  function RootNavigator() {
    const { auth, onboarding, setAuthState } = useAppStore();

    // 应用启动时检查认证状态 | Check auth state on app launch
    useEffect(() => {
      const checkAuthStatus = async () => {
        try {
          const token = await SecureStore.getItemAsync('auth_token');
          if (token) {
            // 验证token有效性 | Validate token
            const user = await api.validateToken(token);
            setAuthState({ isAuthenticated: true, user, loading: false });
          } else {
            setAuthState({ loading: false });
          }
        } catch (error) {
          console.error('Auth check failed:', error);
          setAuthState({ loading: false });
        }
      };

      checkAuthStatus();
    }, []);

    // 加载中显示启动屏幕 | Show splash screen while loading
    if (auth.loading) {
      return <SplashScreen />;
    }

    // 状态驱动的导航结构 | State-driven navigation structure
    // 优先级: 认证 > 引导 > 主应用 | Priority: Auth > Onboarding > Main app
    if (!auth.isAuthenticated) {
      return <AuthNavigator />; // 未登录：认证流程 | Not logged in: Auth flow
    }

    if (!onboarding.completed) {
      return <OnboardingNavigator />; // 首次使用：引导流程 | First use: Onboarding flow
    }

    return <MainNavigator />; // 正常使用：主应用 | Normal use: Main app
  }

  // 4. 主导航器（嵌套Tab和Stack）| Main navigator (nested Tab and Stack)
  // src/navigation/MainNavigator.tsx
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createStackNavigator } from '@react-navigation/stack';

  const Tab = createBottomTabNavigator();
  const Stack = createStackNavigator();

  // 首页堆栈 | Home stack
  function HomeStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="HomeScreen" component={HomeScreen} />
        <Stack.Screen name="ArticleDetail" component={ArticleDetailScreen} />
        <Stack.Screen
          name="CreatePost"
          component={CreatePostScreen}
          options={{ presentation: 'modal' }} // modal展示 | Modal presentation
        />
      </Stack.Navigator>
    );
  }

  // 个人资料堆栈 | Profile stack
  function ProfileStack() {
    const permissions = useAppStore(state => state.permissions);

    return (
      <Stack.Navigator>
        <Stack.Screen name="ProfileScreen" component={ProfileScreen} />
        <Stack.Screen name="Settings" component={SettingsScreen} />
        {/* 条件路由：只有相机权限时才显示 | Conditional route: Only show with camera permission */}
        {permissions.cameraGranted && (
          <Stack.Screen name="UpdateAvatar" component={UpdateAvatarScreen} />
        )}
      </Stack.Navigator>
    );
  }

  // 主Tab导航 | Main tab navigation
  function MainNavigator() {
    return (
      <Tab.Navigator>
        <Tab.Screen name="Home" component={HomeStack} />
        <Tab.Screen name="Search" component={SearchScreen} />
        <Tab.Screen name="Profile" component={ProfileStack} />
      </Tab.Navigator>
    );
  }

  // 5. 权限保护的导航 | Permission-protected navigation
  // src/navigation/ProtectedRoute.tsx
  import { useAppStore } from '../store/appStore';

  interface ProtectedRouteProps {
    component: React.ComponentType<any>;
    requiredPermission?: 'camera' | 'location';
    fallbackScreen?: string;
  }

  function ProtectedRoute({
    component: Component,
    requiredPermission,
    fallbackScreen = 'PermissionDenied',
    ...rest
  }: ProtectedRouteProps) {
    const permissions = useAppStore(state => state.permissions);
    const navigation = useNavigation();

    // 检查权限 | Check permission
    const hasPermission = requiredPermission
      ? permissions[`${requiredPermission}Granted`]
      : true;

    useEffect(() => {
      if (!hasPermission) {
        // 权限不足时导航到回退屏幕 | Navigate to fallback screen when permission insufficient
        navigation.navigate(fallbackScreen);
      }
    }, [hasPermission]);

    if (!hasPermission) {
      return null; // 或显示权限请求屏幕 | Or show permission request screen
    }

    return <Component {...rest} />;
  }

  // 使用示例 | Usage example
  <Stack.Screen name="CameraCapture">
    {(props) => (
      <ProtectedRoute
        {...props}
        component={CameraCaptureScreen}
        requiredPermission="camera"
      />
    )}
  </Stack.Screen>

  // 6. 深链接与权限结合 | Deep linking combined with permissions
  // src/navigation/linking.ts
  import { useAppStore } from '../store/appStore';

  export const linking = {
    prefixes: ['myapp://', 'https://myapp.com'],
    config: {
      screens: {
        Auth: {
          screens: {
            Login: 'login',
            Signup: 'signup',
          },
        },
        Main: {
          screens: {
            Home: {
              screens: {
                HomeScreen: 'home',
                ArticleDetail: 'article/:id',
              },
            },
            Profile: {
              screens: {
                ProfileScreen: 'profile',
                Settings: 'settings',
              },
            },
          },
        },
      },
    },
    // 自定义getStateFromPath以添加权限检查 | Custom getStateFromPath to add permission check
    getStateFromPath: (path: string, options: any) => {
      const state = getStateFromPath(path, options);

      // 获取当前认证和权限状态 | Get current auth and permission state
      const { auth, permissions } = useAppStore.getState();

      // 检查是否需要认证 | Check if authentication is required
      if (!auth.isAuthenticated && !path.startsWith('/login') && !path.startsWith('/signup')) {
        // 重定向到登录 | Redirect to login
        return {
          routes: [{ name: 'Auth', state: { routes: [{ name: 'Login' }] } }],
        };
      }

      // 可以添加更多权限检查逻辑 | Can add more permission check logic
      return state;
    },
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么认证状态不包含在持久化中？| Why is auth state not included in persistence?
    **答案 | Answer:** 安全考虑，认证token应该存储在安全存储中，每次启动重新验证 | Security consideration, auth tokens should be stored in secure storage, re-validate on each launch
  - ProtectedRoute如何防止未授权访问？| How does ProtectedRoute prevent unauthorized access?
    **答案 | Answer:** 通过检查权限状态，在权限不足时导航到回退屏幕或显示权限请求 | By checking permission state, navigate to fallback screen or show permission request when insufficient
  - 自定义getStateFromPath的作用是什么？| What is the purpose of custom getStateFromPath?
    **答案 | Answer:** 在解析深链接时添加认证和权限检查，防止通过深链接绕过权限控制 | Add auth and permission checks when parsing deep links, prevent bypassing permission control via deep links

## 实践项目：用户活动追踪应用 | Practical Project: User Activity Tracking App

### 目标 | Objective
构建一个完整的用户活动追踪应用，综合应用导航状态持久化、全局状态管理集成、useFocusEffect、通知驱动导航等所有核心概念。应用需要支持认证流程、多层导航、权限管理、以及通知响应。| Build a complete user activity tracking app that comprehensively applies all core concepts: navigation state persistence, global state management integration, useFocusEffect, notification-driven navigation. The app needs to support authentication flows, multi-layer navigation, permission management, and notification response.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 导航状态持久化如何在应用重启后恢复用户位置？| How does navigation state persistence restore user position after app restart?
   **答案 | Answer:** 通过onStateChange保存状态到AsyncStorage，应用启动时通过initialState恢复 | Save state to AsyncStorage via onStateChange, restore via initialState on app launch
2. navigationRef与navigation prop在何时分别使用？| When to use navigationRef vs navigation prop respectively?
   **答案 | Answer:** 屏幕组件内用navigation prop，组件外（Redux action、service）用navigationRef | Use navigation prop within screen components, use navigationRef outside components (Redux actions, services)
3. useFocusEffect的清理函数何时执行？| When does useFocusEffect's cleanup function execute?
   **答案 | Answer:** 屏幕失去焦点或组件卸载时 | When screen loses focus or component unmounts

### 步骤 | Steps

1. **初始化项目与状态管理 | Initialize project and state management**
   - 创建新的React Native项目
   - 安装必要依赖：@react-navigation、zustand、@react-native-async-storage/async-storage
   - 设置全局状态store（认证、用户信息、权限）

2. **构建认证流程与导航架构 | Build auth flow and navigation architecture**
   - 创建AuthStack（Login、Signup屏幕）
   - 创建MainStack（带Tab导航的主应用）
   - 在RootNavigator中实现基于认证状态的条件渲染
   - 配置导航状态持久化

3. **实现活动追踪功能 | Implement activity tracking functionality**
   - 活动列表屏幕（使用useFocusEffect刷新数据）
   - 活动详情屏幕（接收路由参数）
   - 创建活动modal屏幕
   - 使用useFocusEffect记录屏幕浏览时长

4. **集成通知系统 | Integrate notification system**
   - 配置推送通知监听
   - 实现通知点击导航到活动详情
   - 支持应用在前台/后台/关闭状态的通知处理
   - 在navigationRef中添加待处理通知队列

5. **添加深链接支持 | Add deep linking support**
   - 配置linking对象
   - 支持 `myapp://activity/:id` 格式
   - 测试深链接在不同应用状态下的行为

### 示例代码 | Example Code

```typescript
/**
 * 用户活动追踪应用 | User Activity Tracking App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 导航状态持久化 | Navigation state persistence
 * - navigationRef外部导航 | navigationRef external navigation
 * - Zustand全局状态管理 | Zustand global state management
 * - useFocusEffect数据刷新 | useFocusEffect data refresh
 * - 通知驱动导航 | Notification-driven navigation
 * - 多层导航协调 | Multi-layer navigation coordination
 */

// 1. 全局状态定义（src/store/appStore.ts）
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

interface Activity {
  id: string;
  title: string;
  description: string;
  timestamp: number;
}

interface AppStore {
  // 认证状态
  isAuthenticated: boolean;
  user: { id: string; name: string } | null;

  // 活动数据
  activities: Activity[];

  // 待处理导航
  pendingNavigation: { screen: string; params?: any } | null;

  // Actions
  login: (user: { id: string; name: string }) => void;
  logout: () => void;
  addActivity: (activity: Activity) => void;
  setPendingNavigation: (nav: { screen: string; params?: any } | null) => void;
}

export const useAppStore = create<AppStore>()(
  persist(
    (set) => ({
      isAuthenticated: false,
      user: null,
      activities: [],
      pendingNavigation: null,

      login: (user) => set({ isAuthenticated: true, user }),

      logout: () => set({ isAuthenticated: false, user: null }),

      addActivity: (activity) =>
        set((state) => ({ activities: [activity, ...state.activities] })),

      setPendingNavigation: (nav) => set({ pendingNavigation: nav }),
    }),
    {
      name: 'activity-tracker-storage',
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);

// 2. navigationRef设置（src/navigation/navigationRef.ts）
import { createNavigationContainerRef } from '@react-navigation/native';
import { useAppStore } from '../store/appStore';

export const navigationRef = createNavigationContainerRef();

export function navigate(name: string, params?: object) {
  if (navigationRef.isReady()) {
    navigationRef.navigate(name as never, params as never);
  } else {
    // 导航器未就绪，保存到待处理队列
    useAppStore.getState().setPendingNavigation({ screen: name, params });
  }
}

export function executePendingNavigation() {
  const { pendingNavigation, setPendingNavigation } = useAppStore.getState();

  if (pendingNavigation && navigationRef.isReady()) {
    navigationRef.navigate(
      pendingNavigation.screen as never,
      pendingNavigation.params as never
    );
    setPendingNavigation(null);
  }
}

// 3. 根导航器（src/navigation/RootNavigator.tsx）
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { useEffect, useState, useCallback } from 'react';
import { useAppStore } from '../store/appStore';
import { navigationRef, executePendingNavigation } from './navigationRef';

const PERSISTENCE_KEY = 'NAVIGATION_STATE_V1';
const Stack = createStackNavigator();

function RootNavigator() {
  const isAuthenticated = useAppStore(state => state.isAuthenticated);
  const [isReady, setIsReady] = useState(false);
  const [initialState, setInitialState] = useState();

  // 恢复导航状态
  useEffect(() => {
    const restoreState = async () => {
      try {
        const savedStateString = await AsyncStorage.getItem(PERSISTENCE_KEY);
        if (savedStateString) {
          setInitialState(JSON.parse(savedStateString));
        }
      } catch (error) {
        console.error('Failed to restore navigation state:', error);
      } finally {
        setIsReady(true);
      }
    };

    restoreState();
  }, []);

  // 保存导航状态（带防抖）
  const onStateChange = useCallback((state: any) => {
    AsyncStorage.setItem(PERSISTENCE_KEY, JSON.stringify(state));
  }, []);

  if (!isReady) {
    return null; // 或显示启动屏幕
  }

  return (
    <NavigationContainer
      ref={navigationRef}
      initialState={initialState}
      onStateChange={onStateChange}
      onReady={executePendingNavigation} // 导航就绪后执行待处理导航
    >
      {isAuthenticated ? <MainNavigator /> : <AuthNavigator />}
    </NavigationContainer>
  );
}

// 4. 认证导航器
function AuthNavigator() {
  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="Signup" component={SignupScreen} />
    </Stack.Navigator>
  );
}

// 5. 主导航器（带Tab）
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

function MainNavigator() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Activities" component={ActivitiesScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}

// 6. 活动列表屏幕（使用useFocusEffect）
import { useFocusEffect } from '@react-navigation/native';
import { useState, useCallback, useRef } from 'react';

function ActivitiesScreen({ navigation }: any) {
  const activities = useAppStore(state => state.activities);
  const [refreshing, setRefreshing] = useState(false);
  const focusTimeRef = useRef<number>(0);

  // 屏幕聚焦时刷新数据
  useFocusEffect(
    useCallback(() => {
      console.log('Activities screen focused');
      focusTimeRef.current = Date.now();

      // 模拟数据刷新
      setRefreshing(true);
      setTimeout(() => setRefreshing(false), 1000);

      return () => {
        // 记录停留时长
        const duration = Date.now() - focusTimeRef.current;
        console.log(`User spent ${duration}ms on Activities screen`);
      };
    }, [])
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={activities}
        refreshing={refreshing}
        onRefresh={() => {
          setRefreshing(true);
          setTimeout(() => setRefreshing(false), 1000);
        }}
        renderItem={({ item }) => (
          <TouchableOpacity
            onPress={() => navigation.navigate('ActivityDetail', { activityId: item.id })}
          >
            <View style={styles.activityItem}>
              <Text style={styles.activityTitle}>{item.title}</Text>
              <Text>{item.description}</Text>
            </View>
          </TouchableOpacity>
        )}
        keyExtractor={(item) => item.id}
      />

      <FAB
        icon="plus"
        onPress={() => navigation.navigate('CreateActivity')}
        style={styles.fab}
      />
    </View>
  );
}

// 7. 通知服务集成（src/services/notificationService.ts）
import messaging from '@react-native-firebase/messaging';
import * as RootNavigation from '../navigation/navigationRef';

export function initializeNotifications() {
  // 前台消息
  messaging().onMessage(async (remoteMessage) => {
    console.log('Foreground notification:', remoteMessage);
    // 显示应用内通知
  });

  // 后台消息打开应用
  messaging().onNotificationOpenedApp((remoteMessage) => {
    console.log('Background notification opened:', remoteMessage);

    if (remoteMessage.data?.activityId) {
      RootNavigation.navigate('ActivityDetail', {
        activityId: remoteMessage.data.activityId,
      });
    }
  });

  // 应用完全关闭时的启动通知
  messaging()
    .getInitialNotification()
    .then((remoteMessage) => {
      if (remoteMessage?.data?.activityId) {
        RootNavigation.navigate('ActivityDetail', {
          activityId: remoteMessage.data.activityId,
        });
      }
    });
}

// 8. App入口（src/App.tsx）
import { useEffect } from 'react';
import { initializeNotifications } from './services/notificationService';

export default function App() {
  useEffect(() => {
    initializeNotifications();
  }, []);

  return <RootNavigator />;
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  activityItem: {
    padding: 16,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  activityTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 8,
  },
  fab: {
    position: 'absolute',
    right: 16,
    bottom: 16,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确实现了导航状态持久化？重启应用后能否恢复到之前的屏幕？| Does the project correctly implement navigation state persistence? Can it restore to previous screen after restart?
2. navigationRef是否在通知处理和非组件上下文中正确使用？| Is navigationRef correctly used in notification handling and non-component contexts?
3. useFocusEffect是否用于数据刷新和屏幕追踪？是否正确清理了副作用？| Is useFocusEffect used for data refresh and screen tracking? Are side effects properly cleaned up?
4. 认证流程是否通过状态变化自动切换导航器？| Does auth flow automatically switch navigators through state changes?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **导航状态持久化优化 | Navigation State Persistence Optimization**
   - **练习描述 | Exercise Description:** 实现选择性持久化策略，某些敏感屏幕（如支付页）不应被持久化
   - **概念检查 | Concept Check:** 如何在onStateChange中过滤特定路由？
   - **学习目标 | Learning Objective:** 理解导航状态结构和安全性考虑

2. **navigationRef类型安全增强 | navigationRef Type Safety Enhancement**
   - **练习描述 | Exercise Description:** 为navigationRef添加完整的TypeScript类型定义，包括所有屏幕和参数
   - **概念检查 | Concept Check:** TypeScript泛型如何应用于导航系统？
   - **学习目标 | Learning Objective:** 掌握类型安全的导航模式

3. **useFocusEffect与React Query集成 | useFocusEffect with React Query Integration**
   - **练习描述 | Exercise Description:** 使用React Query管理数据获取，结合useFocusEffect实现智能缓存失效
   - **概念检查 | Concept Check:** 何时应该失效查询缓存？
   - **学习目标 | Learning Objective:** 学习现代数据获取模式与导航的集成

4. **通知优先级队列 | Notification Priority Queue**
   - **练习描述 | Exercise Description:** 实现通知导航队列系统，支持多个待处理通知按优先级执行
   - **概念检查 | Concept Check:** 如何设计通知导航的优先级规则？
   - **学习目标 | Learning Objective:** 掌握复杂异步导航逻辑管理

5. **多认证流程支持 | Multi-Authentication Flow Support**
   - **练习描述 | Exercise Description:** 支持多种认证方式（邮箱、社交登录、生物识别），每种有不同的导航流程
   - **概念检查 | Concept Check:** 如何设计可扩展的认证导航架构？
   - **学习目标 | Learning Objective:** 理解复杂业务逻辑的导航设计

6. **导航性能监控 | Navigation Performance Monitoring**
   - **练习描述 | Exercise Description:** 使用useFocusEffect和分析工具监控每个屏幕的加载时间、停留时间
   - **概念检查 | Concept Check:** 如何测量屏幕渲染性能？
   - **学习目标 | Learning Objective:** 学习性能监控与优化技术

7. **离线优先的导航状态同步 | Offline-First Navigation State Sync**
   - **练习描述 | Exercise Description:** 实现离线时的导航状态本地存储，在线时同步到服务器
   - **概念检查 | Concept Check:** 离线导航状态如何与云端同步？
   - **学习目标 | Learning Objective:** 掌握离线优先应用架构

## 学习资源 | Learning Resources
- [React Navigation State Persistence文档](https://reactnavigation.org/docs/state-persistence)
- [Zustand官方文档](https://docs.pmnd.rs/zustand/getting-started/introduction)
- [React Navigation TypeScript指南](https://reactnavigation.org/docs/typescript)
- [React Native Firebase Messaging](https://rnfirebase.io/messaging/usage)
- [useFocusEffect API参考](https://reactnavigation.org/docs/use-focus-effect)
- [AsyncStorage最佳实践](https://react-native-async-storage.github.io/async-storage/docs/usage)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解导航状态持久化的原理和实现方式 | Understand principles and implementation of navigation state persistence
- [ ] 能够使用AsyncStorage正确保存和恢复导航状态 | Can correctly save and restore navigation state using AsyncStorage
- [ ] 掌握navigationRef在组件外部的使用 | Master using navigationRef outside components
- [ ] 理解基于状态的导航模式（声明式导航）| Understand state-based navigation pattern (declarative navigation)
- [ ] 能够集成Zustand或Redux与导航系统 | Can integrate Zustand or Redux with navigation system
- [ ] 熟练使用useFocusEffect进行数据刷新和清理 | Proficient in using useFocusEffect for data refresh and cleanup
- [ ] 实现通知驱动的导航功能 | Implement notification-driven navigation functionality
- [ ] 能够协调多层导航状态（认证、权限、业务状态）| Can coordinate multi-layer navigation state (auth, permissions, business state)
- [ ] 理解导航系统的性能优化策略 | Understand performance optimization strategies for navigation system
- [ ] 完成实践项目并通过所有检查点 | Complete practical project and pass all checkpoints

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够独立实现一个包含认证流程、状态持久化、通知导航的完整应用。理解导航与全局状态管理的集成是构建生产级React Native应用的关键技能。
Before marking as complete, ensure you can correctly answer all CCQs from today and independently implement a complete app with auth flow, state persistence, and notification navigation. Understanding the integration of navigation and global state management is a key skill for building production-grade React Native applications.
