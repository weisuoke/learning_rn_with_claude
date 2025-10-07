# React Native入门 - 第2天：API集成与数据获取（第2部分） | React Native Introduction - Day 2: API Integration & Data Fetching (Part 2)

## 详细内容（续） | Detailed Content (Continued)

### 3. 自定义Hook：useArticles | Custom Hook: useArticles (1.5小时 | 1.5 hours)

- **自定义Hook的概念与设计原则 | Custom Hook Concept & Design Principles**

  **概念定义 | Concept Definition:**
  自定义Hook是一个以"use"开头的JavaScript函数，它可以调用其他Hook。自定义Hook允许我们将组件逻辑提取到可复用的函数中，特别适合封装数据获取、订阅等副作用逻辑。 | A custom hook is a JavaScript function whose name starts with "use" and that can call other hooks. Custom hooks allow us to extract component logic into reusable functions, particularly suitable for encapsulating side effects logic like data fetching and subscriptions.

  **核心特征 | Key Characteristics:**
  - **可复用性**：同一个Hook可以在多个组件中使用 | **Reusability**: Same hook can be used in multiple components
  - **状态隔离**：每次调用Hook都创建独立的状态 | **State Isolation**: Each hook call creates independent state
  - **逻辑封装**：将复杂的状态管理逻辑从组件中分离 | **Logic Encapsulation**: Separates complex state management logic from components
  - **符合React规则**：必须在组件顶层调用，不能在条件语句中 | **Follows React Rules**: Must be called at component top level, not in conditionals

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 自定义Hook的名字必须以什么开头？| What must custom hook names start with?
     **答案 | Answer:** "use" - 这是React识别Hook的约定 | "use" - this is the convention for React to recognize hooks
  2. 两个组件使用同一个自定义Hook会共享状态吗？| Will two components using the same custom hook share state?
     **答案 | Answer:** 否 | No - 每个组件都有自己独立的状态实例 | Each component has its own independent state instance
  3. 可以在if语句中调用自定义Hook吗？| Can you call a custom hook inside an if statement?
     **答案 | Answer:** 否 | No - 违反Hook规则，必须在组件顶层调用 | Violates hook rules, must be called at component top level
  4. 自定义Hook可以调用其他Hook（如useState、useEffect）吗？| Can custom hooks call other hooks (like useState, useEffect)?
     **答案 | Answer:** 是 | Yes - 这正是自定义Hook的核心功能 | This is exactly the core functionality of custom hooks

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/hooks/useArticles.js
  // 自定义Hook用于管理文章数据获取 | Custom hook for managing article data fetching

  import { useState, useEffect, useCallback } from 'react';
  import { newsAPI } from '../services/api';
  import { getErrorMessage } from '../utils/errorHandler';

  /**
   * useArticles - 通用文章数据获取Hook
   * @param {Function} fetchFunction - API获取函数
   * @param {Array} dependencies - 依赖数组，变化时重新获取
   * @returns {Object} { articles, loading, error, refresh, retry }
   */
  export const useArticles = (fetchFunction, dependencies = []) => {
    // 状态管理 | State management
    const [articles, setArticles] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    const [refreshing, setRefreshing] = useState(false);

    // 核心数据获取函数 | Core data fetching function
    const fetchArticles = useCallback(async (isRefreshing = false) => {
      try {
        // 设置加载状态 | Set loading state
        if (isRefreshing) {
          setRefreshing(true);
        } else {
          setLoading(true);
        }
        setError(null);

        // 调用API | Call API
        const data = await fetchFunction();

        // 更新文章数据 | Update articles data
        setArticles(data);
      } catch (err) {
        // 处理错误 | Handle error
        const errorInfo = getErrorMessage(err);
        setError(errorInfo);
        console.error('Fetch articles error:', err);
      } finally {
        // 清除加载状态 | Clear loading state
        setLoading(false);
        setRefreshing(false);
      }
    }, [fetchFunction]);

    // 组件挂载时获取数据 | Fetch data on component mount
    useEffect(() => {
      fetchArticles();
    }, [...dependencies, fetchArticles]);

    // 刷新函数（用于下拉刷新） | Refresh function (for pull-to-refresh)
    const refresh = useCallback(() => {
      fetchArticles(true);
    }, [fetchArticles]);

    // 重试函数（用于错误后重试） | Retry function (for retrying after error)
    const retry = useCallback(() => {
      fetchArticles(false);
    }, [fetchArticles]);

    return {
      articles,     // 文章数组 | Articles array
      loading,      // 初始加载状态 | Initial loading state
      error,        // 错误信息对象 | Error info object
      refreshing,   // 下拉刷新状态 | Pull-to-refresh state
      refresh,      // 刷新函数 | Refresh function
      retry,        // 重试函数 | Retry function
    };
  };

  // 预定义的Hook变体 | Predefined hook variants
  export const useTopHeadlines = (country = 'us') => {
    return useArticles(
      () => newsAPI.getTopHeadlines(country),
      [country]
    );
  };

  export const useCategoryArticles = (category) => {
    return useArticles(
      () => newsAPI.getArticlesByCategory(category),
      [category]
    );
  };

  export const useSearchArticles = (query) => {
    return useArticles(
      () => query ? newsAPI.searchArticles(query) : Promise.resolve([]),
      [query]
    );
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用useCallback包装fetchArticles？| Why use useCallback to wrap fetchArticles?
    **答案 | Answer:** 避免每次渲染都创建新函数，防止useEffect无限循环 | Avoid creating new function on every render, preventing useEffect infinite loop
  - loading和refreshing状态有什么区别？| What's the difference between loading and refreshing states?
    **答案 | Answer:** loading用于初始加载，refreshing用于下拉刷新，可以显示不同的UI | loading for initial load, refreshing for pull-to-refresh, can show different UI
  - dependencies参数的作用是什么？| What is the purpose of the dependencies parameter?
    **答案 | Answer:** 当依赖项变化时重新获取数据，例如切换类别或搜索词 | Re-fetch data when dependencies change, e.g., switching category or search term

  **常见误区检查 | Common Misconception Checks:**
  - 误区：自定义Hook只能用于数据获取 | Misconception: Custom hooks can only be used for data fetching
    **答案 | Answer:** 错误 | Wrong - 可以用于任何逻辑复用，如表单处理、动画、定时器等 | Can be used for any logic reuse, like form handling, animations, timers, etc.
  - 误区：useCallback是必需的 | Misconception: useCallback is required
    **答案 | Answer:** 不完全正确 | Not entirely correct - 在这个场景中是必需的以避免无限循环，但不是所有情况都需要 | Required in this scenario to avoid infinite loops, but not needed in all cases

- **状态管理的最佳实践 | State Management Best Practices**

  **概念定义 | Concept Definition:**
  在数据获取场景中，良好的状态管理需要处理三种主要状态：加载中（loading）、成功（success）、错误（error）。这种模式被称为"异步状态机"。 | In data fetching scenarios, good state management needs to handle three main states: loading, success, and error. This pattern is called an "asynchronous state machine."

  **状态转换图 | State Transition Diagram:**
  ```
  idle (初始) → loading → success (数据) or error (错误信息)
                    ↑           ↓              ↓
                    └───────── refresh ────────┘
  ```

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以同时处于loading和error状态吗？| Can you be in both loading and error states simultaneously?
     **答案 | Answer:** 否 | No - 这些是互斥的状态 | These are mutually exclusive states
  2. 刷新数据时应该清除旧数据吗？| Should old data be cleared when refreshing?
     **答案 | Answer:** 取决于UX设计 | Depends on UX design - 通常保留旧数据并显示refreshing指示器更好 | Usually better to keep old data and show refreshing indicator
  3. 错误状态应该保留之前成功获取的数据吗？| Should error state retain previously fetched data?
     **答案 | Answer:** 是 | Yes - 让用户仍能看到缓存数据，提供更好的体验 | Allows users to still see cached data, providing better experience

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/hooks/useArticles.js (改进版)
  // 增强的状态管理 | Enhanced state management

  export const useArticles = (fetchFunction, dependencies = []) => {
    // 使用状态对象而不是多个独立状态 | Use state object instead of multiple independent states
    const [state, setState] = useState({
      articles: [],
      loading: true,
      error: null,
      refreshing: false,
      lastFetchTime: null, // 添加获取时间戳 | Add fetch timestamp
    });

    const fetchArticles = useCallback(async (isRefreshing = false) => {
      // 更新状态：开始获取 | Update state: start fetching
      setState(prev => ({
        ...prev,
        loading: !isRefreshing,
        refreshing: isRefreshing,
        error: null,
      }));

      try {
        const data = await fetchFunction();

        // 更新状态：成功 | Update state: success
        setState({
          articles: data,
          loading: false,
          error: null,
          refreshing: false,
          lastFetchTime: Date.now(),
        });
      } catch (err) {
        const errorInfo = getErrorMessage(err);

        // 更新状态：失败（保留旧数据） | Update state: failure (keep old data)
        setState(prev => ({
          ...prev,
          loading: false,
          error: errorInfo,
          refreshing: false,
        }));
      }
    }, [fetchFunction]);

    // ... 其余代码相同

    return {
      ...state,
      refresh: useCallback(() => fetchArticles(true), [fetchArticles]),
      retry: useCallback(() => fetchArticles(false), [fetchArticles]),
    };
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用单个state对象而不是多个useState？| Why use a single state object instead of multiple useState?
    **答案 | Answer:** 确保状态更新的原子性，避免中间状态导致UI闪烁 | Ensures atomicity of state updates, avoids UI flickering from intermediate states
  - lastFetchTime的作用是什么？| What is the purpose of lastFetchTime?
    **答案 | Answer:** 可用于实现缓存策略，避免频繁重复请求 | Can be used for cache strategy, avoiding frequent duplicate requests

### 4. useEffect与数据获取 | useEffect & Data Fetching (1小时 | 1 hour)

- **useEffect的生命周期与依赖数组 | useEffect Lifecycle & Dependency Array**

  **概念定义 | Concept Definition:**
  useEffect是React中处理副作用的Hook。它在组件渲染后执行，可以用于数据获取、订阅、DOM操作等。依赖数组控制effect何时重新运行。 | useEffect is the hook for handling side effects in React. It runs after component render and can be used for data fetching, subscriptions, DOM manipulation, etc. The dependency array controls when the effect reruns.

  **核心特征 | Key Characteristics:**
  - **在渲染后执行**：不会阻塞UI渲染 | **Runs After Render**: Doesn't block UI rendering
  - **依赖追踪**：只在依赖项变化时重新运行 | **Dependency Tracking**: Reruns only when dependencies change
  - **清理函数**：返回函数用于清理副作用 | **Cleanup Function**: Returned function used for cleanup
  - **多次调用**：可以在一个组件中使用多个useEffect | **Multiple Calls**: Can use multiple useEffects in one component

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useEffect在组件渲染之前还是之后运行？| Does useEffect run before or after component render?
     **答案 | Answer:** 之后 | After - 这样可以确保DOM已经更新 | This ensures DOM is already updated
  2. 空依赖数组[]意味着什么？| What does an empty dependency array [] mean?
     **答案 | Answer:** effect只在组件挂载时运行一次 | Effect runs only once on component mount
  3. 没有依赖数组会怎样？| What happens without a dependency array?
     **答案 | Answer:** effect在每次渲染后都运行 | Effect runs after every render
  4. 清理函数什么时候执行？| When does the cleanup function execute?
     **答案 | Answer:** 在组件卸载时和下次effect运行之前 | On component unmount and before next effect run

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/screens/HomeScreen.js
  // 在组件中使用useArticles Hook | Using useArticles hook in component

  import React from 'react';
  import { View, Text, StyleSheet, ActivityIndicator, FlatList, RefreshControl } from 'react-native';
  import { useTopHeadlines } from '../hooks/useArticles';

  const HomeScreen = () => {
    // 使用自定义Hook获取数据 | Use custom hook to fetch data
    const { articles, loading, error, refreshing, refresh, retry } = useTopHeadlines('us');

    // 渲染加载状态 | Render loading state
    if (loading) {
      return (
        <View style={styles.centerContainer}>
          <ActivityIndicator size="large" color="#007AFF" />
          <Text style={styles.loadingText}>Loading news...</Text>
        </View>
      );
    }

    // 渲染错误状态 | Render error state
    if (error && !articles.length) {
      return (
        <View style={styles.centerContainer}>
          <Text style={styles.errorTitle}>{error.title}</Text>
          <Text style={styles.errorMessage}>{error.message}</Text>
          {error.retryable && (
            <TouchableOpacity style={styles.retryButton} onPress={retry}>
              <Text style={styles.retryButtonText}>Retry</Text>
            </TouchableOpacity>
          )}
        </View>
      );
    }

    // 渲染文章列表 | Render article list
    return (
      <FlatList
        data={articles}
        keyExtractor={(item, index) => item.url || index.toString()}
        renderItem={({ item }) => <ArticleCard article={item} />}
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={refresh}
            tintColor="#007AFF"
          />
        }
        ListEmptyComponent={
          <View style={styles.centerContainer}>
            <Text style={styles.emptyText}>No articles found</Text>
          </View>
        }
      />
    );
  };

  const styles = StyleSheet.create({
    centerContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    loadingText: {
      marginTop: 12,
      fontSize: 16,
      color: '#666',
    },
    errorTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#FF3B30',
      marginBottom: 8,
    },
    errorMessage: {
      fontSize: 14,
      color: '#666',
      textAlign: 'center',
      marginBottom: 20,
    },
    retryButton: {
      backgroundColor: '#007AFF',
      paddingHorizontal: 24,
      paddingVertical: 12,
      borderRadius: 8,
    },
    retryButtonText: {
      color: '#FFF',
      fontSize: 16,
      fontWeight: '600',
    },
    emptyText: {
      fontSize: 16,
      color: '#999',
    },
  });

  export default HomeScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么HomeScreen组件中没有显式的useEffect？| Why is there no explicit useEffect in HomeScreen component?
    **答案 | Answer:** 已经封装在useTopHeadlines自定义Hook中 | Already encapsulated in the useTopHeadlines custom hook
  - 如果error存在但articles不为空会显示什么？| What will be displayed if error exists but articles is not empty?
    **答案 | Answer:** 显示文章列表（带缓存数据），用户仍能浏览旧内容 | Shows article list (with cached data), users can still browse old content
  - RefreshControl何时触发？| When does RefreshControl trigger?
    **答案 | Answer:** 用户下拉列表时触发onRefresh | Triggered when user pulls down the list

  **常见误区检查 | Common Misconception Checks:**
  - 误区：useEffect可以返回async函数 | Misconception: useEffect can return async function
    **答案 | Answer:** 错误 | Wrong - 必须返回清理函数或undefined，需在effect内部使用async函数 | Must return cleanup function or undefined, need to use async function inside effect
  - 误区：所有依赖项都必须在数组中列出 | Misconception: All dependencies must be listed in array
    **答案 | Answer:** 正确 | Correct - 遗漏依赖会导致stale closure问题 | Omitting dependencies causes stale closure issues

### 5. RefreshControl与用户交互 | RefreshControl & User Interaction (45分钟 | 45 minutes)

- **下拉刷新的实现原理 | Pull-to-Refresh Implementation**

  **概念定义 | Concept Definition:**
  RefreshControl是React Native提供的组件，用于为ScrollView或FlatList添加下拉刷新功能。它监听用户的下拉手势，触发刷新回调，并在刷新过程中显示加载指示器。 | RefreshControl is a component provided by React Native to add pull-to-refresh functionality to ScrollView or FlatList. It listens for user pull-down gestures, triggers refresh callback, and shows loading indicator during refresh.

  **核心特征 | Key Characteristics:**
  - **平台原生行为**：iOS和Android有不同的默认样式 | **Native Platform Behavior**: iOS and Android have different default styles
  - **状态控制**：通过refreshing prop控制加载指示器 | **State Control**: Controls loading indicator via refreshing prop
  - **回调触发**：onRefresh在用户下拉时被调用 | **Callback Trigger**: onRefresh called when user pulls down
  - **可定制**：可以自定义颜色、标题等 | **Customizable**: Can customize colors, titles, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. RefreshControl可以单独使用吗？| Can RefreshControl be used standalone?
     **答案 | Answer:** 否 | No - 必须作为ScrollView或FlatList的refreshControl prop | Must be used as refreshControl prop of ScrollView or FlatList
  2. refreshing prop应该是什么类型？| What type should the refreshing prop be?
     **答案 | Answer:** boolean - true显示加载指示器，false隐藏 | boolean - true shows loading indicator, false hides
  3. 如果忘记在onRefresh中更新refreshing状态会怎样？| What happens if you forget to update refreshing state in onRefresh?
     **答案 | Answer:** 加载指示器会一直显示，用户体验很差 | Loading indicator will keep showing, poor user experience
  4. iOS和Android的RefreshControl看起来一样吗？| Do iOS and Android RefreshControl look the same?
     **答案 | Answer:** 否 | No - 有平台特定的默认样式和行为 | Have platform-specific default styles and behaviors

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/components/ArticleList.js
  // 可复用的文章列表组件 | Reusable article list component

  import React from 'react';
  import { FlatList, RefreshControl, View, Text, StyleSheet } from 'react-native';
  import ArticleCard from './ArticleCard';

  const ArticleList = ({ articles, refreshing, onRefresh, onArticlePress }) => {
    return (
      <FlatList
        data={articles}
        keyExtractor={(item, index) => item.url || `article-${index}`}
        renderItem={({ item }) => (
          <ArticleCard
            article={item}
            onPress={() => onArticlePress(item)}
          />
        )}
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={onRefresh}
            // iOS特定配置 | iOS-specific config
            tintColor="#007AFF"
            title="Pull to refresh"
            titleColor="#666"
            // Android特定配置 | Android-specific config
            colors={['#007AFF', '#5856D6']}
            progressBackgroundColor="#FFFFFF"
          />
        }
        ListEmptyComponent={
          <View style={styles.emptyContainer}>
            <Text style={styles.emptyText}>No articles available</Text>
            <Text style={styles.emptySubtext}>Pull down to refresh</Text>
          </View>
        }
        contentContainerStyle={articles.length === 0 ? styles.emptyList : null}
      />
    );
  };

  const styles = StyleSheet.create({
    emptyContainer: {
      alignItems: 'center',
      paddingVertical: 60,
    },
    emptyText: {
      fontSize: 18,
      fontWeight: '600',
      color: '#333',
      marginBottom: 8,
    },
    emptySubtext: {
      fontSize: 14,
      color: '#999',
    },
    emptyList: {
      flexGrow: 1,
      justifyContent: 'center',
    },
  });

  export default ArticleList;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - colors和tintColor有什么区别？| What's the difference between colors and tintColor?
    **答案 | Answer:** colors用于Android（可以多个颜色），tintColor用于iOS | colors for Android (can be multiple colors), tintColor for iOS
  - 为什么需要contentContainerStyle的条件判断？| Why need conditional judgment for contentContainerStyle?
    **答案 | Answer:** 当列表为空时，使emptyComponent垂直居中 | When list is empty, center the emptyComponent vertically

  **常见误区检查 | Common Misconception Checks:**
  - 误区：RefreshControl会自动重新获取数据 | Misconception: RefreshControl automatically re-fetches data
    **答案 | Answer:** 错误 | Wrong - 只是触发onRefresh回调，需要手动实现数据获取逻辑 | Only triggers onRefresh callback, need to manually implement data fetching logic

### 6. 性能优化与最佳实践 | Performance Optimization & Best Practices (30分钟 | 30 minutes)

- **避免不必要的重新渲染 | Avoiding Unnecessary Re-renders**

  **概念定义 | Concept Definition:**
  React组件在状态或props变化时会重新渲染。过多的重新渲染会影响性能。使用React.memo、useMemo和useCallback可以优化渲染性能。 | React components re-render when state or props change. Excessive re-renders affect performance. Using React.memo, useMemo, and useCallback can optimize rendering performance.

  **优化技术 | Optimization Techniques:**
  - **React.memo**：防止props未变化时的重新渲染 | Prevents re-render when props haven't changed
  - **useMemo**：缓存计算结果 | Caches computation results
  - **useCallback**：缓存函数引用 | Caches function references
  - **key prop**：帮助React识别列表项变化 | Helps React identify list item changes

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React.memo适用于函数组件还是类组件？| Does React.memo apply to functional or class components?
     **答案 | Answer:** 函数组件 | Functional components - 类组件使用PureComponent | Class components use PureComponent
  2. useMemo和useCallback有什么区别？| What's the difference between useMemo and useCallback?
     **答案 | Answer:** useMemo缓存值，useCallback缓存函数 | useMemo caches values, useCallback caches functions
  3. 所有组件都应该用React.memo包裹吗？| Should all components be wrapped with React.memo?
     **答案 | Answer:** 否 | No - 只在确实有性能问题时使用，过度使用反而增加内存开销 | Only use when there are actual performance issues, overuse increases memory overhead

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/components/ArticleCard.js
  // 优化的文章卡片组件 | Optimized article card component

  import React, { memo } from 'react';
  import { View, Text, Image, TouchableOpacity, StyleSheet } from 'react-native';

  const ArticleCard = memo(({ article, onPress }) => {
    // 处理缺失的图片 | Handle missing images
    const imageSource = article.urlToImage
      ? { uri: article.urlToImage }
      : require('../assets/placeholder.png');

    // 格式化日期 | Format date
    const formatDate = (dateString) => {
      const date = new Date(dateString);
      return date.toLocaleDateString('en-US', {
        month: 'short',
        day: 'numeric',
        year: 'numeric'
      });
    };

    return (
      <TouchableOpacity style={styles.card} onPress={onPress} activeOpacity={0.8}>
        <Image
          source={imageSource}
          style={styles.image}
          resizeMode="cover"
        />
        <View style={styles.content}>
          <Text style={styles.title} numberOfLines={2}>
            {article.title}
          </Text>
          <Text style={styles.description} numberOfLines={3}>
            {article.description || 'No description available'}
          </Text>
          <View style={styles.footer}>
            <Text style={styles.source}>{article.source?.name || 'Unknown'}</Text>
            <Text style={styles.date}>{formatDate(article.publishedAt)}</Text>
          </View>
        </View>
      </TouchableOpacity>
    );
  }, (prevProps, nextProps) => {
    // 自定义比较函数：只比较article的url
    // Custom comparison: only compare article url
    return prevProps.article.url === nextProps.article.url;
  });

  const styles = StyleSheet.create({
    card: {
      backgroundColor: '#FFF',
      marginHorizontal: 16,
      marginVertical: 8,
      borderRadius: 12,
      overflow: 'hidden',
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    image: {
      width: '100%',
      height: 200,
      backgroundColor: '#E5E5E5',
    },
    content: {
      padding: 16,
    },
    title: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#000',
      marginBottom: 8,
      lineHeight: 24,
    },
    description: {
      fontSize: 14,
      color: '#666',
      lineHeight: 20,
      marginBottom: 12,
    },
    footer: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
    },
    source: {
      fontSize: 12,
      fontWeight: '600',
      color: '#007AFF',
    },
    date: {
      fontSize: 12,
      color: '#999',
    },
  });

  export default ArticleCard;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要提供自定义比较函数给React.memo？| Why provide a custom comparison function to React.memo?
    **答案 | Answer:** 默认比较会浅比较所有props，自定义比较可以只检查关键属性（url），提高效率 | Default comparison shallow compares all props, custom comparison can check only key properties (url) for better efficiency
  - formatDate函数应该移到组件外部吗？| Should formatDate function be moved outside the component?
    **答案 | Answer:** 是 | Yes - 避免每次渲染都重新创建函数 | Avoid recreating function on every render

  **常见误区检查 | Common Misconception Checks:**
  - 误区：使用React.memo总是能提高性能 | Misconception: Using React.memo always improves performance
    **答案 | Answer:** 错误 | Wrong - 如果props频繁变化，比较开销可能大于重新渲染 | If props change frequently, comparison overhead may exceed re-render cost
