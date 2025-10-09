# React Native入门 - 第21天：Pull-to-Refresh与无限滚动（第二部分） | React Native Introduction - Day 21: Pull-to-Refresh & Infinite Scroll (Part 2)

## 详细内容（续） | Detailed Content (Continued)

### 4. ListFooterComponent与ListEmptyComponent | ListFooterComponent & ListEmptyComponent (45分钟 | 45 minutes)

- **ListFooterComponent用于加载指示器 | ListFooterComponent for Loading Indicators**

  **概念定义 | Concept Definition:**
  `ListFooterComponent`是FlatList提供的一个prop，用于在列表底部渲染自定义内容。它最常用于显示"加载更多"状态的指示器、"没有更多数据"的提示、或者其他列表底部的静态内容。该组件会随着列表一起滚动，并且会在所有数据项之后渲染。| `ListFooterComponent` is a FlatList prop used to render custom content at the bottom of the list. It's most commonly used to display "loading more" state indicators, "no more data" messages, or other static content at the list bottom. This component scrolls with the list and renders after all data items.

  **核心特征 | Key Characteristics:**
  - **动态内容**：可以根据加载状态显示不同的内容 | **Dynamic content**: Can display different content based on loading state
  - **条件渲染**：通常使用函数组件进行条件判断 | **Conditional rendering**: Typically uses function component for conditional logic
  - **滚动集成**：作为列表的一部分，会随列表滚动 | **Scroll integration**: Part of the list, scrolls with list content
  - **性能友好**：只渲染一次，不会像list items那样重复渲染 | **Performance-friendly**: Renders only once, unlike repeated list items

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `ListFooterComponent`接受组件还是函数？| Does `ListFooterComponent` accept a component or a function?
     **答案 | Answer:** 两者都可以 | Both - 可以是React组件或返回React元素的函数 | Can be a React component or a function returning React element
  2. 如果列表为空，`ListFooterComponent`会显示吗？| Will `ListFooterComponent` show if the list is empty?
     **答案 | Answer:** 不会 | No - 空列表时只显示`ListEmptyComponent` | Only `ListEmptyComponent` shows when list is empty
  3. 可以在`ListFooterComponent`中访问加载状态吗？| Can you access loading state in `ListFooterComponent`?
     **答案 | Answer:** 可以 | Yes - 通过闭包或使用函数组件可以访问外部状态 | Through closure or using function component to access external state
  4. `ListFooterComponent`是否会在每次滚动时重新渲染？| Does `ListFooterComponent` re-render on every scroll?
     **答案 | Answer:** 不会，除非其依赖的props或state发生变化 | No, unless its dependent props or state changes

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // ListFooterComponent最佳实践 | ListFooterComponent Best Practices
  import React, { useState } from 'react';
  import {
    FlatList,
    View,
    Text,
    ActivityIndicator,
    StyleSheet,
    TouchableOpacity,
  } from 'react-native';

  interface Product {
    id: string;
    name: string;
    price: number;
  }

  const ListFooterExample = () => {
    const [products, setProducts] = useState<Product[]>(
      Array.from({ length: 20 }, (_, i) => ({
        id: `product-${i}`,
        name: `Product ${i + 1}`,
        price: Math.random() * 100,
      }))
    );
    const [isLoadingMore, setIsLoadingMore] = useState(false);
    const [hasMore, setHasMore] = useState(true);
    const [error, setError] = useState<string | null>(null);

    const loadMore = async () => {
      if (isLoadingMore || !hasMore) return;

      setIsLoadingMore(true);
      setError(null);

      try {
        // 模拟API调用 | Simulate API call
        await new Promise(resolve => setTimeout(resolve, 2000));

        // 模拟随机错误 | Simulate random error
        if (Math.random() < 0.2) {
          throw new Error('网络请求失败 | Network request failed');
        }

        const newProducts: Product[] = Array.from({ length: 10 }, (_, i) => ({
          id: `product-${products.length + i}`,
          name: `Product ${products.length + i + 1}`,
          price: Math.random() * 100,
        }));

        setProducts(prev => [...prev, ...newProducts]);

        // 模拟到达末尾 | Simulate reaching end
        if (products.length >= 50) {
          setHasMore(false);
        }
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setIsLoadingMore(false);
      }
    };

    // Footer渲染逻辑 | Footer rendering logic
    const renderFooter = () => {
      // 情况1：正在加载更多 | Case 1: Loading more
      if (isLoadingMore) {
        return (
          <View style={styles.footer}>
            <ActivityIndicator size="small" color="#007AFF" />
            <Text style={styles.footerText}>加载中... | Loading...</Text>
          </View>
        );
      }

      // 情况2：加载错误 | Case 2: Loading error
      if (error) {
        return (
          <View style={styles.footer}>
            <Text style={styles.errorText}>{error}</Text>
            <TouchableOpacity
              style={styles.retryButton}
              onPress={loadMore}
            >
              <Text style={styles.retryButtonText}>
                重试 | Retry
              </Text>
            </TouchableOpacity>
          </View>
        );
      }

      // 情况3：没有更多数据 | Case 3: No more data
      if (!hasMore) {
        return (
          <View style={styles.footer}>
            <Text style={styles.endText}>
              已经到底了 | You've reached the end
            </Text>
          </View>
        );
      }

      // 情况4：可以加载更多但未触发 | Case 4: Can load more but not triggered
      return (
        <TouchableOpacity
          style={styles.loadMoreButton}
          onPress={loadMore}
        >
          <Text style={styles.loadMoreText}>
            点击加载更多 | Tap to load more
          </Text>
        </TouchableOpacity>
      );
    };

    return (
      <FlatList
        data={products}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.productItem}>
            <Text style={styles.productName}>{item.name}</Text>
            <Text style={styles.productPrice}>
              ${item.price.toFixed(2)}
            </Text>
          </View>
        )}
        onEndReached={loadMore}
        onEndReachedThreshold={0.5}
        ListFooterComponent={renderFooter} // 使用函数组件 | Use function component
      />
    );
  };

  const styles = StyleSheet.create({
    productItem: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    productName: {
      fontSize: 16,
    },
    productPrice: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#007AFF',
    },
    footer: {
      paddingVertical: 20,
      alignItems: 'center',
    },
    footerText: {
      marginTop: 8,
      fontSize: 14,
      color: '#666',
    },
    errorText: {
      fontSize: 14,
      color: '#FF3B30',
      marginBottom: 10,
    },
    retryButton: {
      paddingHorizontal: 20,
      paddingVertical: 8,
      backgroundColor: '#007AFF',
      borderRadius: 4,
    },
    retryButtonText: {
      color: 'white',
      fontSize: 14,
    },
    endText: {
      fontSize: 14,
      color: '#999',
    },
    loadMoreButton: {
      padding: 16,
      alignItems: 'center',
      backgroundColor: '#f5f5f5',
    },
    loadMoreText: {
      fontSize: 14,
      color: '#007AFF',
    },
  });

  export default ListFooterExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要将Footer渲染逻辑提取为单独的函数？| Why extract footer rendering logic into a separate function?
    **答案 | Answer:** 提高代码可读性，使条件逻辑更清晰，便于测试和维护 | Improves code readability, makes conditional logic clearer, easier to test and maintain
  - 在Footer中添加"重试"按钮有什么好处？| What's the benefit of adding a "retry" button in the footer?
    **答案 | Answer:** 当加载失败时，用户可以手动重试，无需滚动触发 | When loading fails, users can manually retry without scrolling to trigger

  **常见误区检查 | Common Misconception Checks:**
  - `ListFooterComponent`是否总是可见的？| Is `ListFooterComponent` always visible?
    **答案 | Answer:** 否，当列表为空时不显示，需要列表有数据才会渲染 | No, doesn't show when list is empty, requires list to have data to render
  - 可以在Footer中直接修改列表数据吗？| Can you directly modify list data in the footer?
    **答案 | Answer:** 可以通过回调修改，但不建议，应该通过明确的加载函数 | Can modify through callbacks, but not recommended, should use explicit loading functions

- **ListEmptyComponent空状态设计 | ListEmptyComponent Empty State Design**

  **概念定义 | Concept Definition:**
  `ListEmptyComponent`在列表数据为空（`data.length === 0`）时显示。优秀的空状态设计不仅仅是显示"无数据"文字，而应该解释为什么为空、用户可以做什么、以及如何改善情况。这对于首次使用、搜索无结果、网络错误等场景都很重要。| `ListEmptyComponent` is displayed when the list data is empty (`data.length === 0`). Excellent empty state design goes beyond just showing "no data" text - it should explain why it's empty, what users can do, and how to improve the situation. This is important for scenarios like first-time use, no search results, network errors, etc.

  **核心特征 | Key Characteristics:**
  - **仅空列表显示**：只在`data.length === 0`时渲染 | **Empty-list-only display**: Only renders when `data.length === 0`
  - **用户指导**：提供清晰的下一步操作指引 | **User guidance**: Provides clear next-step action guidance
  - **情感设计**：使用插图、图标和友好的文案 | **Emotional design**: Uses illustrations, icons, and friendly copy
  - **可操作性**：通常包含CTA按钮（如"重新加载"、"创建第一项"） | **Actionability**: Usually includes CTA buttons (like "Reload", "Create first item")

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果列表有数据，`ListEmptyComponent`会显示吗？| Will `ListEmptyComponent` show if the list has data?
     **答案 | Answer:** 不会 | No - 只在data数组长度为0时显示 | Only shows when data array length is 0
  2. 可以在`ListEmptyComponent`中显示加载指示器吗？| Can you show a loading indicator in `ListEmptyComponent`?
     **答案 | Answer:** 可以 | Yes - 初次加载时可以在空状态组件中显示加载器 | Can show loader in empty state component during initial load
  3. `ListEmptyComponent`和`ListFooterComponent`会同时显示吗？| Will `ListEmptyComponent` and `ListFooterComponent` show simultaneously?
     **答案 | Answer:** 不会 | No - ListEmptyComponent只在列表为空时显示，此时Footer不显示 | ListEmptyComponent only shows when list is empty, Footer doesn't show then
  4. 空状态设计应该包含哪些元素？| What elements should empty state design include?
     **答案 | Answer:** 解释性文字、视觉元素（图标/插图）、行动按钮 | Explanatory text, visual elements (icons/illustrations), action buttons

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 优秀的空状态设计示例 | Excellent Empty State Design Example
  import React, { useState, useEffect } from 'react';
  import {
    FlatList,
    View,
    Text,
    StyleSheet,
    TouchableOpacity,
    ActivityIndicator,
  } from 'react-native';

  interface Task {
    id: string;
    title: string;
    completed: boolean;
  }

  type EmptyStateType = 'loading' | 'error' | 'no_data' | 'no_results';

  const EmptyStateExample = () => {
    const [tasks, setTasks] = useState<Task[]>([]);
    const [emptyStateType, setEmptyStateType] = useState<EmptyStateType>('loading');

    useEffect(() => {
      loadTasks();
    }, []);

    const loadTasks = async () => {
      setEmptyStateType('loading');

      try {
        // 模拟API调用 | Simulate API call
        await new Promise(resolve => setTimeout(resolve, 2000));

        // 模拟不同的结果 | Simulate different outcomes
        const random = Math.random();
        if (random < 0.3) {
          throw new Error('加载失败 | Load failed');
        } else if (random < 0.6) {
          setTasks([]); // 空数据 | Empty data
          setEmptyStateType('no_data');
        } else {
          // 有数据 | Has data
          setTasks([
            { id: '1', title: 'Task 1', completed: false },
            { id: '2', title: 'Task 2', completed: true },
          ]);
        }
      } catch (error) {
        setEmptyStateType('error');
      }
    };

    // 空状态渲染组件 | Empty state render component
    const renderEmptyComponent = () => {
      switch (emptyStateType) {
        // 加载中状态 | Loading state
        case 'loading':
          return (
            <View style={styles.emptyContainer}>
              <ActivityIndicator size="large" color="#007AFF" />
              <Text style={styles.emptyTitle}>
                加载任务中... | Loading tasks...
              </Text>
            </View>
          );

        // 错误状态 | Error state
        case 'error':
          return (
            <View style={styles.emptyContainer}>
              <Text style={styles.errorIcon}>⚠️</Text>
              <Text style={styles.emptyTitle}>
                加载失败 | Failed to load
              </Text>
              <Text style={styles.emptyMessage}>
                无法连接到服务器，请检查网络连接
                {'\n'}
                Unable to connect to server, please check network connection
              </Text>
              <TouchableOpacity
                style={styles.actionButton}
                onPress={loadTasks}
              >
                <Text style={styles.actionButtonText}>
                  重试 | Retry
                </Text>
              </TouchableOpacity>
            </View>
          );

        // 无数据状态 | No data state
        case 'no_data':
          return (
            <View style={styles.emptyContainer}>
              <Text style={styles.emptyIcon}>📋</Text>
              <Text style={styles.emptyTitle}>
                还没有任务 | No tasks yet
              </Text>
              <Text style={styles.emptyMessage}>
                创建你的第一个任务来开始工作
                {'\n'}
                Create your first task to get started
              </Text>
              <TouchableOpacity
                style={styles.actionButton}
                onPress={() => {
                  // 创建任务逻辑 | Create task logic
                  setTasks([
                    {
                      id: Date.now().toString(),
                      title: 'New Task',
                      completed: false,
                    },
                  ]);
                }}
              >
                <Text style={styles.actionButtonText}>
                  创建任务 | Create Task
                </Text>
              </TouchableOpacity>
            </View>
          );

        // 无搜索结果状态 | No search results state
        case 'no_results':
          return (
            <View style={styles.emptyContainer}>
              <Text style={styles.emptyIcon}>🔍</Text>
              <Text style={styles.emptyTitle}>
                未找到结果 | No results found
              </Text>
              <Text style={styles.emptyMessage}>
                尝试调整搜索条件或创建新任务
                {'\n'}
                Try adjusting search criteria or create a new task
              </Text>
            </View>
          );

        default:
          return null;
      }
    };

    return (
      <View style={styles.container}>
        <FlatList
          data={tasks}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => (
            <View style={styles.taskItem}>
              <Text style={styles.taskTitle}>{item.title}</Text>
              <Text>{item.completed ? '✅' : '⭕️'}</Text>
            </View>
          )}
          // 空状态组件 | Empty state component
          ListEmptyComponent={renderEmptyComponent}
          contentContainerStyle={
            tasks.length === 0 ? styles.emptyListContent : undefined
          }
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: 'white',
    },
    emptyListContent: {
      flexGrow: 1, // 使空状态组件可以垂直居中 | Allows empty state to center vertically
    },
    emptyContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 40,
    },
    emptyIcon: {
      fontSize: 64,
      marginBottom: 16,
    },
    errorIcon: {
      fontSize: 64,
      marginBottom: 16,
    },
    emptyTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      color: '#333',
      marginBottom: 8,
      textAlign: 'center',
    },
    emptyMessage: {
      fontSize: 14,
      color: '#666',
      textAlign: 'center',
      lineHeight: 20,
      marginBottom: 24,
    },
    actionButton: {
      paddingHorizontal: 32,
      paddingVertical: 12,
      backgroundColor: '#007AFF',
      borderRadius: 8,
    },
    actionButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    taskItem: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    taskTitle: {
      fontSize: 16,
    },
  });

  export default EmptyStateExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用`contentContainerStyle`的`flexGrow: 1`？| Why use `flexGrow: 1` in `contentContainerStyle`?
    **答案 | Answer:** 使空状态组件能够填充整个可用空间并垂直居中 | Allows empty state component to fill available space and center vertically
  - 不同的空状态场景需要不同的设计吗？| Do different empty state scenarios need different designs?
    **答案 | Answer:** 是的，加载、错误、无数据等场景应有不同的视觉和交互设计 | Yes, loading, error, no data scenarios should have different visual and interaction designs

  **常见误区检查 | Common Misconception Checks:**
  - 空状态只需要显示"暂无数据"文字即可吗？| Is it enough for empty state to just show "No data" text?
    **答案 | Answer:** 不够，应该提供清晰的解释、视觉元素和行动指引 | Not enough, should provide clear explanation, visual elements, and action guidance
  - `ListEmptyComponent`和初次加载状态是一样的吗？| Is `ListEmptyComponent` the same as initial loading state?
    **答案 | Answer:** 不一样，初次加载应显示加载器，加载完成后的空状态应显示指导 | Different, initial loading should show loader, empty state after loading should show guidance

### 5. 错误处理与边界情况 | Error Handling & Edge Cases (30分钟 | 30 minutes)

- **网络错误处理策略 | Network Error Handling Strategy**

  **概念定义 | Concept Definition:**
  在实际应用中，网络请求可能失败（超时、断网、服务器错误等）。良好的错误处理不仅仅是捕获异常，还要提供用户友好的错误信息、重试机制、以及优雅的降级体验。对于列表加载，需要区分初次加载失败、刷新失败、加载更多失败这三种场景。| In real applications, network requests may fail (timeout, no connection, server errors, etc.). Good error handling goes beyond just catching exceptions - it provides user-friendly error messages, retry mechanisms, and graceful degradation experiences. For list loading, you need to distinguish between three scenarios: initial load failure, refresh failure, and load more failure.

  **核心特征 | Key Characteristics:**
  - **错误类型区分**：网络错误、超时、服务器错误需要不同的处理 | **Error type distinction**: Network errors, timeouts, server errors need different handling
  - **用户友好提示**：将技术错误转化为用户可理解的语言 | **User-friendly messages**: Transform technical errors into user-understandable language
  - **重试机制**：提供手动重试按钮或自动重试逻辑 | **Retry mechanism**: Provide manual retry button or automatic retry logic
  - **状态保留**：错误时保留已加载的数据，不清空列表 | **State preservation**: Keep loaded data on error, don't clear the list

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 加载更多失败时，应该清空已加载的数据吗？| Should you clear loaded data when load more fails?
     **答案 | Answer:** 不应该 | No - 应该保留已有数据，只显示错误提示 | Should keep existing data, only show error message
  2. 初次加载失败和加载更多失败的UI有何不同？| How does UI differ between initial load failure and load more failure?
     **答案 | Answer:** 初次失败显示全屏错误，加载更多失败显示底部错误提示 | Initial failure shows full-screen error, load more failure shows bottom error message
  3. 是否应该向用户显示原始错误消息（如"500 Internal Server Error"）？| Should you show raw error messages to users (like "500 Internal Server Error")?
     **答案 | Answer:** 不应该 | No - 应该转换为用户友好的语言（如"服务器繁忙，请稍后重试"） | Should convert to user-friendly language (like "Server busy, please retry later")

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 完整的错误处理示例 | Complete Error Handling Example
  import React, { useState, useEffect } from 'react';
  import {
    FlatList,
    View,
    Text,
    TouchableOpacity,
    ActivityIndicator,
    StyleSheet,
    Alert,
  } from 'react-native';

  interface NewsItem {
    id: string;
    title: string;
    summary: string;
  }

  type ErrorType = 'network' | 'timeout' | 'server' | null;

  const ErrorHandlingExample = () => {
    const [news, setNews] = useState<NewsItem[]>([]);
    const [isInitialLoading, setIsInitialLoading] = useState(true);
    const [isRefreshing, setIsRefreshing] = useState(false);
    const [isLoadingMore, setIsLoadingMore] = useState(false);

    const [initialError, setInitialError] = useState<ErrorType>(null);
    const [loadMoreError, setLoadMoreError] = useState<ErrorType>(null);

    const [page, setPage] = useState(1);
    const [hasMore, setHasMore] = useState(true);

    // 模拟API调用，可能失败 | Simulate API call that may fail
    const fetchNews = async (pageNum: number): Promise<NewsItem[]> => {
      await new Promise(resolve => setTimeout(resolve, 1500));

      // 模拟随机错误 | Simulate random errors
      const random = Math.random();
      if (random < 0.2) {
        throw new Error('network'); // 网络错误 | Network error
      } else if (random < 0.3) {
        throw new Error('timeout'); // 超时 | Timeout
      } else if (random < 0.4) {
        throw new Error('server'); // 服务器错误 | Server error
      }

      // 成功返回数据 | Successfully return data
      return Array.from({ length: 10 }, (_, i) => ({
        id: `news-${pageNum}-${i}`,
        title: `新闻标题 ${(pageNum - 1) * 10 + i + 1} | News Title ${(pageNum - 1) * 10 + i + 1}`,
        summary: `这是新闻摘要... | This is news summary...`,
      }));
    };

    // 将错误类型转换为用户友好的消息 | Convert error type to user-friendly message
    const getErrorMessage = (errorType: ErrorType): string => {
      switch (errorType) {
        case 'network':
          return '网络连接失败，请检查网络设置 | Network connection failed, please check network settings';
        case 'timeout':
          return '请求超时，请稍后重试 | Request timeout, please retry later';
        case 'server':
          return '服务器繁忙，请稍后重试 | Server busy, please retry later';
        default:
          return '未知错误 | Unknown error';
      }
    };

    // 初次加载 | Initial load
    useEffect(() => {
      loadInitialData();
    }, []);

    const loadInitialData = async () => {
      setIsInitialLoading(true);
      setInitialError(null);

      try {
        const data = await fetchNews(1);
        setNews(data);
        setPage(1);
        setHasMore(true);
      } catch (error) {
        const errorType = error instanceof Error ? error.message as ErrorType : 'network';
        setInitialError(errorType);
      } finally {
        setIsInitialLoading(false);
      }
    };

    // 下拉刷新 | Pull to refresh
    const onRefresh = async () => {
      setIsRefreshing(true);

      try {
        const data = await fetchNews(1);
        setNews(data);
        setPage(1);
        setHasMore(true);
        setInitialError(null); // 清除之前的错误 | Clear previous errors
      } catch (error) {
        // 刷新失败时使用Alert提示，不影响已有数据 | Use Alert for refresh failure, don't affect existing data
        Alert.alert(
          '刷新失败 | Refresh Failed',
          getErrorMessage(error instanceof Error ? error.message as ErrorType : 'network'),
          [{ text: '确定 | OK' }]
        );
      } finally {
        setIsRefreshing(false);
      }
    };

    // 加载更多 | Load more
    const loadMore = async () => {
      if (isLoadingMore || !hasMore) return;

      setIsLoadingMore(true);
      setLoadMoreError(null);

      try {
        const nextPage = page + 1;
        const newData = await fetchNews(nextPage);

        setNews(prev => [...prev, ...newData]);
        setPage(nextPage);

        if (nextPage >= 5) {
          setHasMore(false); // 模拟到达末尾 | Simulate reaching end
        }
      } catch (error) {
        const errorType = error instanceof Error ? error.message as ErrorType : 'network';
        setLoadMoreError(errorType);
        // 不抛出错误，保留已加载的数据 | Don't throw error, keep loaded data
      } finally {
        setIsLoadingMore(false);
      }
    };

    // 初次加载错误状态 | Initial loading error state
    if (isInitialLoading) {
      return (
        <View style={styles.centerContainer}>
          <ActivityIndicator size="large" color="#007AFF" />
        </View>
      );
    }

    if (initialError) {
      return (
        <View style={styles.centerContainer}>
          <Text style={styles.errorIcon}>⚠️</Text>
          <Text style={styles.errorTitle}>加载失败 | Load Failed</Text>
          <Text style={styles.errorMessage}>
            {getErrorMessage(initialError)}
          </Text>
          <TouchableOpacity
            style={styles.retryButton}
            onPress={loadInitialData}
          >
            <Text style={styles.retryButtonText}>重试 | Retry</Text>
          </TouchableOpacity>
        </View>
      );
    }

    return (
      <FlatList
        data={news}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.newsItem}>
            <Text style={styles.newsTitle}>{item.title}</Text>
            <Text style={styles.newsSummary}>{item.summary}</Text>
          </View>
        )}
        refreshing={isRefreshing}
        onRefresh={onRefresh}
        onEndReached={loadMore}
        onEndReachedThreshold={0.5}
        ListFooterComponent={() => {
          // 显示加载更多错误 | Show load more error
          if (loadMoreError) {
            return (
              <View style={styles.footerError}>
                <Text style={styles.footerErrorText}>
                  {getErrorMessage(loadMoreError)}
                </Text>
                <TouchableOpacity
                  style={styles.footerRetryButton}
                  onPress={loadMore}
                >
                  <Text style={styles.footerRetryText}>重试 | Retry</Text>
                </TouchableOpacity>
              </View>
            );
          }

          if (isLoadingMore) {
            return (
              <View style={styles.footer}>
                <ActivityIndicator size="small" color="#007AFF" />
              </View>
            );
          }

          if (!hasMore) {
            return (
              <Text style={styles.endText}>
                没有更多内容 | No more content
              </Text>
            );
          }

          return null;
        }}
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
    errorIcon: {
      fontSize: 64,
      marginBottom: 16,
    },
    errorTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 8,
    },
    errorMessage: {
      fontSize: 14,
      color: '#666',
      textAlign: 'center',
      marginBottom: 24,
    },
    retryButton: {
      paddingHorizontal: 32,
      paddingVertical: 12,
      backgroundColor: '#007AFF',
      borderRadius: 8,
    },
    retryButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    newsItem: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    newsTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    newsSummary: {
      fontSize: 14,
      color: '#666',
    },
    footer: {
      paddingVertical: 20,
      alignItems: 'center',
    },
    footerError: {
      padding: 20,
      alignItems: 'center',
      backgroundColor: '#FFF3F3',
    },
    footerErrorText: {
      fontSize: 14,
      color: '#FF3B30',
      marginBottom: 12,
      textAlign: 'center',
    },
    footerRetryButton: {
      paddingHorizontal: 20,
      paddingVertical: 8,
      backgroundColor: '#007AFF',
      borderRadius: 4,
    },
    footerRetryText: {
      color: 'white',
      fontSize: 14,
    },
    endText: {
      textAlign: 'center',
      padding: 20,
      color: '#999',
    },
  });

  export default ErrorHandlingExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么刷新失败使用Alert而加载更多失败使用底部错误提示？| Why use Alert for refresh failure but bottom error message for load more failure?
    **答案 | Answer:** 刷新是用户主动操作，Alert提供即时反馈；加载更多是被动触发，底部提示不打断用户 | Refresh is user-initiated, Alert provides immediate feedback; load more is passive, bottom message doesn't interrupt user

### 6. 性能优化与最佳实践 | Performance Optimization & Best Practices (30分钟 | 30 minutes)

- **防止重复请求的策略 | Preventing Duplicate Request Strategies**

  **概念定义 | Concept Definition:**
  `onEndReached`在某些情况下可能被快速多次调用（快速滚动、列表回弹等），导致发送重复的网络请求。防止重复请求的策略包括：使用加载状态标志、防抖处理、请求队列管理等。正确实现这些策略可以减少不必要的网络流量和服务器负载。| `onEndReached` may be called multiple times rapidly in certain situations (fast scrolling, list bouncing, etc.), causing duplicate network requests. Strategies to prevent duplicate requests include: using loading state flags, debouncing, request queue management, etc. Correctly implementing these strategies reduces unnecessary network traffic and server load.

  **核心特征 | Key Characteristics:**
  - **状态标志保护**：使用`isLoading`标志防止并发请求 | **State flag protection**: Use `isLoading` flag to prevent concurrent requests
  - **防抖/节流**：限制函数调用频率 | **Debounce/Throttle**: Limit function call frequency
  - **请求取消**：取消未完成的过时请求 | **Request cancellation**: Cancel incomplete stale requests
  - **竞态条件处理**：确保响应按顺序处理 | **Race condition handling**: Ensure responses are processed in order

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么`onEndReached`会被多次调用？| Why is `onEndReached` called multiple times?
     **答案 | Answer:** 滚动事件频繁触发、列表布局变化、回弹效果等都可能导致 | Frequent scroll events, list layout changes, bounce effects can all cause this
  2. 使用`isLoading`标志足够防止重复请求吗？| Is using `isLoading` flag sufficient to prevent duplicate requests?
     **答案 | Answer:** 大多数情况足够，但复杂场景可能需要防抖或请求取消 | Sufficient for most cases, but complex scenarios may need debouncing or request cancellation
  3. 什么是竞态条件（Race Condition）？| What is a race condition?
     **答案 | Answer:** 后发起的请求先返回，导致数据顺序错误 | Later-initiated request returns first, causing data order errors
  4. 应该在哪里检查`isLoading`标志？| Where should you check the `isLoading` flag?
     **答案 | Answer:** 在加载函数开始时立即检查并return | Check immediately at the start of loading function and return

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 防止重复请求的高级实现 | Advanced Duplicate Request Prevention
  import React, { useState, useRef } from 'react';
  import { FlatList, View, Text, ActivityIndicator } from 'react-native';

  interface Message {
    id: string;
    content: string;
  }

  const DuplicateRequestPreventionExample = () => {
    const [messages, setMessages] = useState<Message[]>([]);
    const [isLoadingMore, setIsLoadingMore] = useState(false);

    // 使用ref跟踪请求ID，处理竞态条件 | Use ref to track request ID, handle race conditions
    const requestIdRef = useRef(0);
    const currentPageRef = useRef(1);

    // 使用ref作为防抖标志 | Use ref as debounce flag
    const loadMoreTimerRef = useRef<NodeJS.Timeout | null>(null);

    const fetchMessages = async (page: number, requestId: number): Promise<Message[]> => {
      console.log(`Fetching page ${page}, request ID: ${requestId}`);
      await new Promise(resolve => setTimeout(resolve, 2000));

      return Array.from({ length: 20 }, (_, i) => ({
        id: `msg-${page}-${i}-${requestId}`,
        content: `Message ${(page - 1) * 20 + i + 1}`,
      }));
    };

    const loadMore = () => {
      // 策略1：状态标志保护 | Strategy 1: State flag protection
      if (isLoadingMore) {
        console.log('Already loading, skip');
        return;
      }

      // 策略2：防抖处理 | Strategy 2: Debouncing
      if (loadMoreTimerRef.current) {
        clearTimeout(loadMoreTimerRef.current);
      }

      loadMoreTimerRef.current = setTimeout(() => {
        executeLoadMore();
      }, 300); // 300ms防抖 | 300ms debounce
    };

    const executeLoadMore = async () => {
      setIsLoadingMore(true);

      // 策略3：请求ID管理（处理竞态条件） | Strategy 3: Request ID management (handle race conditions)
      const currentRequestId = ++requestIdRef.current;
      const nextPage = currentPageRef.current + 1;

      console.log(`Starting request ${currentRequestId} for page ${nextPage}`);

      try {
        const newMessages = await fetchMessages(nextPage, currentRequestId);

        // 检查这是否还是最新的请求 | Check if this is still the latest request
        if (currentRequestId === requestIdRef.current) {
          console.log(`Request ${currentRequestId} is latest, updating state`);
          setMessages(prev => [...prev, ...newMessages]);
          currentPageRef.current = nextPage;
        } else {
          console.log(`Request ${currentRequestId} is stale, ignoring response`);
        }
      } catch (error) {
        console.error('Load more failed:', error);
      } finally {
        // 只有最新请求才清除loading状态 | Only clear loading state for latest request
        if (currentRequestId === requestIdRef.current) {
          setIsLoadingMore(false);
        }
      }
    };

    return (
      <View style={{ flex: 1 }}>
        <View style={{ padding: 10, backgroundColor: '#f0f0f0' }}>
          <Text>当前请求ID | Current Request ID: {requestIdRef.current}</Text>
          <Text>加载状态 | Loading: {isLoadingMore ? 'Yes' : 'No'}</Text>
          <Text>当前页码 | Current Page: {currentPageRef.current}</Text>
        </View>
        <FlatList
          data={messages}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => (
            <View style={{ padding: 16, borderBottomWidth: 1, borderBottomColor: '#eee' }}>
              <Text>{item.content}</Text>
              <Text style={{ fontSize: 10, color: '#999' }}>ID: {item.id}</Text>
            </View>
          )}
          onEndReached={loadMore}
          onEndReachedThreshold={0.5}
          ListFooterComponent={
            isLoadingMore ? (
              <View style={{ padding: 20, alignItems: 'center' }}>
                <ActivityIndicator size="small" />
                <Text style={{ marginTop: 8, fontSize: 12, color: '#666' }}>
                  Loading... (Request ID: {requestIdRef.current})
                </Text>
              </View>
            ) : null
          }
        />
      </View>
    );
  };

  export default DuplicateRequestPreventionExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用`useRef`而不是`useState`保存请求ID？| Why use `useRef` instead of `useState` to save request ID?
    **答案 | Answer:** ref的值改变不会触发重新渲染，适合存储辅助数据 | ref value changes don't trigger re-render, suitable for auxiliary data
  - 竞态条件检查在什么时候执行？| When is the race condition check executed?
    **答案 | Answer:** 在异步请求返回后、更新状态前检查请求ID是否是最新的 | After async request returns, before updating state, check if request ID is latest

  **常见误区检查 | Common Misconception Checks:**
  - 防抖和状态标志可以互相替代吗？| Can debouncing and state flag replace each other?
    **答案 | Answer:** 不能，它们解决不同问题：防抖减少调用频率，状态标志防止并发 | No, they solve different problems: debouncing reduces call frequency, state flag prevents concurrency

## 实践项目：社交媒体Feed | Practical Project: Social Media Feed

### 目标 | Objective
构建一个完整的社交媒体Feed应用，综合应用下拉刷新、无限滚动、多状态管理、错误处理等所有今日学习的概念。该项目将模拟真实世界的社交应用场景，包含帖子列表、用户交互、性能优化等核心功能。| Build a complete social media feed application that comprehensively applies all concepts learned today: pull-to-refresh, infinite scroll, multi-state management, error handling, etc. This project simulates real-world social app scenarios, including post lists, user interactions, performance optimizations, and other core features.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, confirm understanding of the following concepts:

1. `onEndReached`和`onEndReachedThreshold`如何协同工作？| How do `onEndReached` and `onEndReachedThreshold` work together?
   **答案 | Answer:** `onEndReachedThreshold`定义触发距离（如0.5=50%），当滚动到此距离时调用`onEndReached`回调
2. 为什么需要区分初次加载、刷新、加载更多三种状态？| Why distinguish between initial load, refresh, and load more states?
   **答案 | Answer:** 不同状态需要不同的UI反馈，提供更好的用户体验（全屏loader vs 顶部刷新 vs 底部loader）
3. 错误发生时是否应该清空已加载的数据？| Should loaded data be cleared when an error occurs?
   **答案 | Answer:** 不应该，保留已有数据并显示错误提示，允许用户重试

### 步骤 | Steps

1. **项目初始化和类型定义 | Project Initialization and Type Definitions**
   - 定义Post接口（包含id, author, content, image, likes, comments等）
   - 创建模拟数据生成函数
   - 设置状态管理结构

2. **实现核心列表功能 | Implement Core List Features**
   - 配置FlatList基础结构
   - 实现renderItem渲染帖子卡片
   - 添加keyExtractor确保唯一性

3. **实现下拉刷新 | Implement Pull-to-Refresh**
   - 添加RefreshControl配置
   - 实现onRefresh逻辑（获取最新20条帖子）
   - 处理刷新状态和错误

4. **实现无限滚动 | Implement Infinite Scroll**
   - 配置onEndReached和onEndReachedThreshold
   - 实现分页加载逻辑
   - 添加hasMore判断防止无效请求

5. **完善加载状态UI | Complete Loading State UI**
   - 实现ListFooterComponent显示加载更多状态
   - 实现ListEmptyComponent显示空状态
   - 添加初次加载的全屏指示器

6. **错误处理和重试机制 | Error Handling and Retry Mechanism**
   - 添加网络错误模拟
   - 实现错误状态显示和重试按钮
   - 区分不同场景的错误处理

7. **性能优化 | Performance Optimization**
   - 使用React.memo优化帖子组件
   - 添加防重复请求保护
   - 实现竞态条件处理

### 示例代码 | Example Code

```typescript
/**
 * 社交媒体Feed应用 | Social Media Feed App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Pull-to-Refresh（下拉刷新）
 * - Infinite Scroll（无限滚动）
 * - Multi-State Management（多状态管理）
 * - Error Handling（错误处理）
 * - Performance Optimization（性能优化）
 */

import React, { useState, useEffect, useRef, useCallback } from 'react';
import {
  FlatList,
  View,
  Text,
  Image,
  StyleSheet,
  TouchableOpacity,
  ActivityIndicator,
  RefreshControl,
  Alert,
} from 'react-native';

// ==================== 类型定义 | Type Definitions ====================

interface Post {
  id: string;
  author: {
    name: string;
    avatar: string;
  };
  content: string;
  image?: string;
  likes: number;
  comments: number;
  timestamp: Date;
}

type LoadingState = 'idle' | 'loading' | 'refreshing' | 'loadingMore';
type ErrorType = 'network' | 'server' | null;

// ==================== 模拟数据生成 | Mock Data Generation ====================

const generateMockPosts = (page: number, count: number = 20): Post[] => {
  return Array.from({ length: count }, (_, i) => {
    const index = (page - 1) * count + i;
    return {
      id: `post-${page}-${i}-${Date.now()}`,
      author: {
        name: `User ${index + 1}`,
        avatar: `https://i.pravatar.cc/150?img=${(index % 70) + 1}`,
      },
      content: `This is post #${index + 1}. Lorem ipsum dolor sit amet, consectetur adipiscing elit. ${
        Math.random() > 0.5
          ? 'Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.'
          : ''
      }`,
      image:
        Math.random() > 0.3
          ? `https://picsum.photos/400/300?random=${index}`
          : undefined,
      likes: Math.floor(Math.random() * 1000),
      comments: Math.floor(Math.random() * 100),
      timestamp: new Date(Date.now() - Math.random() * 86400000 * 7),
    };
  });
};

// 模拟API调用 | Simulate API call
const fetchPosts = async (page: number): Promise<Post[]> => {
  // 模拟网络延迟 | Simulate network delay
  await new Promise(resolve => setTimeout(resolve, 1500));

  // 模拟随机错误（20%概率） | Simulate random errors (20% probability)
  if (Math.random() < 0.2) {
    throw new Error(Math.random() < 0.5 ? 'network' : 'server');
  }

  return generateMockPosts(page);
};

// ==================== 帖子卡片组件 | Post Card Component ====================

interface PostCardProps {
  post: Post;
  onLike: (postId: string) => void;
  onComment: (postId: string) => void;
}

const PostCard = React.memo<PostCardProps>(({ post, onLike, onComment }) => {
  const timeAgo = (date: Date): string => {
    const seconds = Math.floor((Date.now() - date.getTime()) / 1000);
    if (seconds < 60) return '刚刚 | Just now';
    if (seconds < 3600) return `${Math.floor(seconds / 60)}分钟前 | ${Math.floor(seconds / 60)}m ago`;
    if (seconds < 86400) return `${Math.floor(seconds / 3600)}小时前 | ${Math.floor(seconds / 3600)}h ago`;
    return `${Math.floor(seconds / 86400)}天前 | ${Math.floor(seconds / 86400)}d ago`;
  };

  return (
    <View style={styles.postCard}>
      {/* 作者信息 | Author Info */}
      <View style={styles.postHeader}>
        <Image source={{ uri: post.author.avatar }} style={styles.avatar} />
        <View style={styles.authorInfo}>
          <Text style={styles.authorName}>{post.author.name}</Text>
          <Text style={styles.timestamp}>{timeAgo(post.timestamp)}</Text>
        </View>
      </View>

      {/* 帖子内容 | Post Content */}
      <Text style={styles.postContent}>{post.content}</Text>

      {/* 帖子图片 | Post Image */}
      {post.image && (
        <Image
          source={{ uri: post.image }}
          style={styles.postImage}
          resizeMode="cover"
        />
      )}

      {/* 交互按钮 | Interaction Buttons */}
      <View style={styles.actions}>
        <TouchableOpacity
          style={styles.actionButton}
          onPress={() => onLike(post.id)}
        >
          <Text style={styles.actionIcon}>❤️</Text>
          <Text style={styles.actionText}>{post.likes}</Text>
        </TouchableOpacity>

        <TouchableOpacity
          style={styles.actionButton}
          onPress={() => onComment(post.id)}
        >
          <Text style={styles.actionIcon}>💬</Text>
          <Text style={styles.actionText}>{post.comments}</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.actionButton}>
          <Text style={styles.actionIcon}>📤</Text>
          <Text style={styles.actionText}>分享 | Share</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
});

// ==================== 主应用组件 | Main App Component ====================

const SocialMediaFeedApp = () => {
  // 数据状态 | Data state
  const [posts, setPosts] = useState<Post[]>([]);

  // 加载状态 | Loading states
  const [loadingState, setLoadingState] = useState<LoadingState>('loading');
  const [error, setError] = useState<ErrorType>(null);
  const [hasMore, setHasMore] = useState(true);

  // 分页控制 | Pagination control
  const currentPage = useRef(1);
  const requestId = useRef(0);

  // ========== 初次加载 | Initial Load ==========
  useEffect(() => {
    loadInitialPosts();
  }, []);

  const loadInitialPosts = async () => {
    setLoadingState('loading');
    setError(null);

    try {
      const initialPosts = await fetchPosts(1);
      setPosts(initialPosts);
      currentPage.current = 1;
      setHasMore(true);
      setLoadingState('idle');
    } catch (err) {
      setError(err instanceof Error ? err.message as ErrorType : 'network');
      setLoadingState('idle');
    }
  };

  // ========== 下拉刷新 | Pull to Refresh ==========
  const onRefresh = async () => {
    setLoadingState('refreshing');

    try {
      const latestPosts = await fetchPosts(1);
      setPosts(latestPosts);
      currentPage.current = 1;
      setHasMore(true);
      setError(null);
    } catch (err) {
      Alert.alert(
        '刷新失败 | Refresh Failed',
        '无法获取最新内容，请检查网络连接 | Unable to fetch latest content, please check network',
        [{ text: '确定 | OK' }]
      );
    } finally {
      setLoadingState('idle');
    }
  };

  // ========== 加载更多 | Load More ==========
  const loadMore = async () => {
    // 防护检查 | Guard checks
    if (loadingState !== 'idle' || !hasMore) {
      return;
    }

    setLoadingState('loadingMore');

    const currentRequestId = ++requestId.current;
    const nextPage = currentPage.current + 1;

    try {
      const newPosts = await fetchPosts(nextPage);

      // 竞态条件检查 | Race condition check
      if (currentRequestId === requestId.current) {
        setPosts(prev => [...prev, ...newPosts]);
        currentPage.current = nextPage;

        // 模拟到达末尾（第5页后） | Simulate reaching end (after page 5)
        if (nextPage >= 5) {
          setHasMore(false);
        }
      }
    } catch (err) {
      // 加载更多失败时不使用Alert，使用底部错误提示 | Don't use Alert for load more failure, use footer error
      if (currentRequestId === requestId.current) {
        setError(err instanceof Error ? err.message as ErrorType : 'network');
      }
    } finally {
      if (currentRequestId === requestId.current) {
        setLoadingState('idle');
      }
    }
  };

  // ========== 用户交互处理 | User Interaction Handlers ==========
  const handleLike = useCallback((postId: string) => {
    setPosts(prev =>
      prev.map(post =>
        post.id === postId ? { ...post, likes: post.likes + 1 } : post
      )
    );
  }, []);

  const handleComment = useCallback((postId: string) => {
    Alert.alert('评论 | Comment', `评论帖子 ${postId} | Comment on post ${postId}`);
  }, []);

  // ========== 渲染函数 | Render Functions ==========

  // 初次加载状态 | Initial loading state
  if (loadingState === 'loading') {
    return (
      <View style={styles.centerContainer}>
        <ActivityIndicator size="large" color="#1DA1F2" />
        <Text style={styles.loadingText}>加载中... | Loading...</Text>
      </View>
    );
  }

  // 初次加载错误状态 | Initial loading error state
  if (error && posts.length === 0) {
    return (
      <View style={styles.centerContainer}>
        <Text style={styles.errorIcon}>⚠️</Text>
        <Text style={styles.errorTitle}>加载失败 | Load Failed</Text>
        <Text style={styles.errorMessage}>
          {error === 'network'
            ? '网络连接失败 | Network connection failed'
            : '服务器错误 | Server error'}
        </Text>
        <TouchableOpacity style={styles.retryButton} onPress={loadInitialPosts}>
          <Text style={styles.retryButtonText}>重试 | Retry</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // ========== 主列表渲染 | Main List Render ==========
  return (
    <FlatList
      data={posts}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => (
        <PostCard post={item} onLike={handleLike} onComment={handleComment} />
      )}
      // 下拉刷新配置 | Pull-to-refresh config
      refreshControl={
        <RefreshControl
          refreshing={loadingState === 'refreshing'}
          onRefresh={onRefresh}
          tintColor="#1DA1F2"
          colors={['#1DA1F2']}
        />
      }
      // 无限滚动配置 | Infinite scroll config
      onEndReached={loadMore}
      onEndReachedThreshold={0.5}
      // 底部组件 | Footer component
      ListFooterComponent={() => {
        // 加载更多错误 | Load more error
        if (error && posts.length > 0) {
          return (
            <View style={styles.footerError}>
              <Text style={styles.footerErrorText}>
                {error === 'network' ? '网络错误 | Network error' : '服务器错误 | Server error'}
              </Text>
              <TouchableOpacity style={styles.footerRetryButton} onPress={loadMore}>
                <Text style={styles.footerRetryText}>重试 | Retry</Text>
              </TouchableOpacity>
            </View>
          );
        }

        // 加载中 | Loading
        if (loadingState === 'loadingMore') {
          return (
            <View style={styles.footer}>
              <ActivityIndicator size="small" color="#1DA1F2" />
              <Text style={styles.footerText}>加载更多... | Loading more...</Text>
            </View>
          );
        }

        // 没有更多 | No more
        if (!hasMore) {
          return (
            <View style={styles.footer}>
              <Text style={styles.endText}>已经到底了 🎉 | You've reached the end 🎉</Text>
            </View>
          );
        }

        return null;
      }}
      // 空状态 | Empty state
      ListEmptyComponent={() => (
        <View style={styles.emptyContainer}>
          <Text style={styles.emptyIcon}>📭</Text>
          <Text style={styles.emptyText}>暂无帖子 | No posts yet</Text>
        </View>
      )}
      // 性能优化 | Performance optimization
      removeClippedSubviews={true}
      maxToRenderPerBatch={10}
      windowSize={21}
    />
  );
};

// ==================== 样式定义 | Style Definitions ====================

const styles = StyleSheet.create({
  // 居中容器 | Center container
  centerContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F7F9FA',
    padding: 20,
  },
  loadingText: {
    marginTop: 12,
    fontSize: 16,
    color: '#657786',
  },
  // 错误状态 | Error state
  errorIcon: {
    fontSize: 64,
    marginBottom: 16,
  },
  errorTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#14171A',
    marginBottom: 8,
  },
  errorMessage: {
    fontSize: 14,
    color: '#657786',
    textAlign: 'center',
    marginBottom: 24,
  },
  retryButton: {
    paddingHorizontal: 32,
    paddingVertical: 12,
    backgroundColor: '#1DA1F2',
    borderRadius: 24,
  },
  retryButtonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
  // 帖子卡片 | Post card
  postCard: {
    backgroundColor: 'white',
    borderBottomWidth: 1,
    borderBottomColor: '#E1E8ED',
    padding: 16,
  },
  postHeader: {
    flexDirection: 'row',
    marginBottom: 12,
  },
  avatar: {
    width: 48,
    height: 48,
    borderRadius: 24,
    marginRight: 12,
  },
  authorInfo: {
    justifyContent: 'center',
  },
  authorName: {
    fontSize: 15,
    fontWeight: 'bold',
    color: '#14171A',
  },
  timestamp: {
    fontSize: 13,
    color: '#657786',
    marginTop: 2,
  },
  postContent: {
    fontSize: 15,
    color: '#14171A',
    lineHeight: 20,
    marginBottom: 12,
  },
  postImage: {
    width: '100%',
    height: 200,
    borderRadius: 12,
    marginBottom: 12,
  },
  actions: {
    flexDirection: 'row',
    paddingTop: 8,
  },
  actionButton: {
    flexDirection: 'row',
    alignItems: 'center',
    marginRight: 24,
  },
  actionIcon: {
    fontSize: 18,
    marginRight: 6,
  },
  actionText: {
    fontSize: 13,
    color: '#657786',
  },
  // 底部组件 | Footer component
  footer: {
    paddingVertical: 20,
    alignItems: 'center',
  },
  footerText: {
    marginTop: 8,
    fontSize: 14,
    color: '#657786',
  },
  endText: {
    fontSize: 14,
    color: '#AAB8C2',
  },
  footerError: {
    padding: 20,
    alignItems: 'center',
    backgroundColor: '#FFEBEE',
  },
  footerErrorText: {
    fontSize: 14,
    color: '#E0245E',
    marginBottom: 12,
  },
  footerRetryButton: {
    paddingHorizontal: 20,
    paddingVertical: 8,
    backgroundColor: '#1DA1F2',
    borderRadius: 16,
  },
  footerRetryText: {
    color: 'white',
    fontSize: 14,
    fontWeight: '600',
  },
  // 空状态 | Empty state
  emptyContainer: {
    paddingVertical: 60,
    alignItems: 'center',
  },
  emptyIcon: {
    fontSize: 64,
    marginBottom: 16,
  },
  emptyText: {
    fontSize: 16,
    color: '#657786',
  },
});

export default SocialMediaFeedApp;
```

### 项目完成检查 | Project Completion Check

1. 项目是否正确实现了下拉刷新功能？| Does the project correctly implement pull-to-refresh?
   - ✅ 下拉时显示刷新指示器
   - ✅ 刷新完成后替换为最新数据
   - ✅ 刷新失败时使用Alert提示

2. 无限滚动是否按预期工作？| Does infinite scroll work as expected?
   - ✅ 滚动到底部自动加载更多
   - ✅ 使用防重复请求保护
   - ✅ 到达末尾时显示"已到底"提示

3. 是否正确管理了所有加载状态？| Are all loading states correctly managed?
   - ✅ 初次加载：全屏loading
   - ✅ 刷新中：顶部RefreshControl
   - ✅ 加载更多：底部小型loader

4. 错误处理是否完善？| Is error handling comprehensive?
   - ✅ 初次加载失败：全屏错误+重试按钮
   - ✅ 刷新失败：Alert提示
   - ✅ 加载更多失败：底部错误提示+重试按钮

5. 性能优化是否到位？| Are performance optimizations in place?
   - ✅ PostCard组件使用React.memo
   - ✅ 回调函数使用useCallback
   - ✅ 使用竞态条件保护

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **下拉刷新行为分析练习 | Pull-to-Refresh Behavior Analysis Exercise**
   - **练习描述 | Exercise Description:** 创建一个实验性应用，对比FlatList内置的`refreshing` prop和使用`RefreshControl` prop的区别，记录它们在不同平台上的行为差异
   - **概念检查 | Concept Check:** `refreshing` prop和`refreshControl` prop可以同时使用吗？
   - **学习目标 | Learning Objective:** 深入理解React Native跨平台刷新机制的实现细节

2. **onEndReached触发时机研究 | onEndReached Trigger Timing Study**
   - **练习描述 | Exercise Description:** 创建测试应用，使用不同的`onEndReachedThreshold`值(0.1, 0.5, 1, 2)，记录每个值的触发位置和用户体验差异
   - **概念检查 | Concept Check:** threshold值大于1是否有意义？会在什么位置触发？
   - **学习目标 | Learning Objective:** 理解阈值参数对无限滚动触发时机的精确影响

3. **加载状态机设计练习 | Loading State Machine Design Exercise**
   - **练习描述 | Exercise Description:** 使用状态机库（如XState）重构多状态管理，定义所有可能的状态转换和边界条件
   - **概念检查 | Concept Check:** 从"refreshing"状态可以直接转换到"loadingMore"状态吗？
   - **学习目标 | Learning Objective:** 建立严格的状态管理思维模式

4. **竞态条件复现与解决 | Race Condition Reproduction and Resolution**
   - **练习描述 | Exercise Description:** 故意创建竞态条件场景（快速滚动触发多个请求，后发起的先返回），然后实现三种不同的解决方案并对比
   - **概念检查 | Concept Check:** 什么情况下会出现竞态条件？如何检测？
   - **学习目标 | Learning Objective:** 掌握识别和解决异步竞态问题的能力

### 应用扩展练习 | Application Extension Exercises

5. **实现双向无限滚动 | Implement Bi-directional Infinite Scroll**
   - **练习描述 | Exercise Description:** 扩展社交Feed项目，支持向上滚动加载更早的内容（使用`onEndReached`的反向版本）
   - **概念检查 | Concept Check:** 如何在不丢失滚动位置的情况下在列表顶部插入数据？
   - **学习目标 | Learning Objective:** 理解列表双向分页的复杂性和ScrollView的维护策略

6. **添加乐观更新 | Add Optimistic Updates**
   - **练习描述 | Exercise Description:** 在点赞功能中实现乐观更新（立即更新UI，后台发送请求，失败时回滚）
   - **概念检查 | Concept Check:** 乐观更新失败时如何优雅地恢复状态？
   - **学习目标 | Learning Objective:** 学习提升用户体验的高级交互模式

7. **实现智能预加载 | Implement Smart Preloading**
   - **练习描述 | Exercise Description:** 基于用户滚动速度和方向，动态调整`onEndReachedThreshold`值，实现智能预加载
   - **概念检查 | Concept Check:** 如何测量用户的滚动速度和方向？
   - **学习目标 | Learning Objective:** 掌握性能优化的高级技巧和用户行为分析

## 学习资源 | Learning Resources

- [React Native FlatList官方文档 | React Native FlatList Official Docs](https://reactnative.dev/docs/flatlist)
- [RefreshControl组件文档 | RefreshControl Component Docs](https://reactnative.dev/docs/refreshcontrol)
- [FlatList性能优化指南 | FlatList Performance Optimization Guide](https://reactnative.dev/docs/optimizing-flatlist-configuration)
- [分页和无限滚动最佳实践 | Pagination and Infinite Scroll Best Practices](https://www.smashingmagazine.com/2022/03/pagination-infinite-scroll-load-more-buttons/)
- [React Hooks性能优化 | React Hooks Performance Optimization](https://react.dev/reference/react/useCallback)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解下拉刷新的工作原理和实现方式 | Understand how pull-to-refresh works and implementation methods
- [ ] 掌握`onEndReached`和`onEndReachedThreshold`的配合使用 | Master the coordinated use of `onEndReached` and `onEndReachedThreshold`
- [ ] 能够区分和管理初次加载、刷新、加载更多三种状态 | Can distinguish and manage initial load, refresh, and load more states
- [ ] 正确使用`ListFooterComponent`显示加载状态 | Correctly use `ListFooterComponent` to display loading states
- [ ] 设计优秀的`ListEmptyComponent`空状态体验 | Design excellent `ListEmptyComponent` empty state experience
- [ ] 实现健壮的错误处理和重试机制 | Implement robust error handling and retry mechanisms
- [ ] 掌握防止重复请求的多种策略 | Master multiple strategies to prevent duplicate requests
- [ ] 理解并处理竞态条件问题 | Understand and handle race condition issues
- [ ] 完成社交媒体Feed实践项目 | Complete the social media feed practical project
- [ ] 能够根据应用场景选择合适的分页策略 | Can choose appropriate pagination strategies based on application scenarios

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
1. 向他人清晰解释pull-to-refresh和infinite scroll的区别和应用场景
2. 独立实现一个包含完整加载状态管理的列表组件
3. 识别并修复常见的无限滚动bug（重复请求、竞态条件等）
4. 设计符合用户体验最佳实践的加载和错误状态

Before marking as complete, ensure you can:
1. Clearly explain to others the differences and use cases between pull-to-refresh and infinite scroll
2. Independently implement a list component with complete loading state management
3. Identify and fix common infinite scroll bugs (duplicate requests, race conditions, etc.)
4. Design loading and error states that follow user experience best practices
