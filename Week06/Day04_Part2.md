# React Native入门 - 第6周第4天：分类与搜索功能（第2部分）| React Native Introduction - Week 6 Day 4: Categories & Search Functionality (Part 2)

### 3. 搜索结果展示与空状态处理 | Search Results Display & Empty State Handling (1小时 | 1 hour)

- **条件渲染与多状态管理 | Conditional Rendering & Multi-State Management**

  **概念定义 | Concept Definition:**
  在搜索功能中，UI需要根据不同的应用状态呈现不同的视图：初始状态、加载中、有结果、无结果、错误状态。这需要通过条件渲染和清晰的状态管理来实现良好的用户体验。| In search functionality, the UI needs to present different views based on different application states: initial, loading, with results, no results, and error. This requires conditional rendering and clear state management to achieve good user experience.

  **核心特征 | Key Characteristics:**
  - **状态互斥性**：同一时间只能处于一种主要状态（加载、错误或显示内容）| **Mutually exclusive states**: Can only be in one primary state at a time (loading, error, or displaying content)
  - **优先级顺序**：错误状态 > 加载状态 > 空状态 > 正常状态 | **Priority order**: Error state > Loading state > Empty state > Normal state
  - **视觉反馈**：每种状态都应有清晰的视觉表现 | **Visual feedback**: Each state should have clear visual representation
  - **用户引导**：空状态和错误状态应提供行动建议 | **User guidance**: Empty and error states should provide actionable suggestions

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以同时显示加载指示器和搜索结果吗？| Can you show loading indicator and search results simultaneously?
     **答案 | Answer:** 一般不建议 | Generally not recommended - But acceptable for "load more" scenarios; for initial search, show loading exclusively
  2. 空搜索结果（0条）和初始状态（未搜索）应该显示相同的UI吗？| Should empty search results (0 items) and initial state (no search) show the same UI?
     **答案 | Answer:** 否 | No - Empty results should show "No results found for 'query'" with suggestions, initial state can show search tips or popular searches
  3. 错误状态下是否应该保留之前成功的搜索结果？| Should you keep previous successful search results when error occurs?
     **答案 | Answer:** 取决于UX设计 | Depends on UX design - Clearing results makes error more obvious; keeping them allows users to still reference old data
  4. 用户清空搜索框时应该显示什么状态？| What state should be shown when user clears the search box?
     **答案 | Answer:** 回到初始状态 | Return to initial state - Clear results and show default content or search suggestions

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import {
    View,
    TextInput,
    Text,
    ScrollView,
    ActivityIndicator,
    Pressable,
    StyleSheet,
  } from 'react-native';

  // 搜索状态枚举 | Search state enum
  const SEARCH_STATE = {
    INITIAL: 'initial',        // 初始状态 | Initial state
    LOADING: 'loading',        // 加载中 | Loading
    SUCCESS: 'success',        // 成功有结果 | Success with results
    EMPTY: 'empty',           // 成功但无结果 | Success but empty
    ERROR: 'error',           // 错误 | Error
  };

  const SearchWithStates = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchState, setSearchState] = useState(SEARCH_STATE.INITIAL);
    const [searchResults, setSearchResults] = useState([]);
    const [errorMessage, setErrorMessage] = useState('');

    // 使用防抖Hook（简化版）| Use debounce hook (simplified)
    const [debouncedQuery, setDebouncedQuery] = useState('');

    useEffect(() => {
      const timer = setTimeout(() => {
        setDebouncedQuery(searchQuery);
      }, 500);
      return () => clearTimeout(timer);
    }, [searchQuery]);

    // 当防抖查询变化时执行搜索 | Execute search when debounced query changes
    useEffect(() => {
      if (debouncedQuery.trim()) {
        performSearch(debouncedQuery);
      } else {
        // 查询为空，回到初始状态 | Query empty, return to initial state
        setSearchState(SEARCH_STATE.INITIAL);
        setSearchResults([]);
      }
    }, [debouncedQuery]);

    const performSearch = async (query) => {
      setSearchState(SEARCH_STATE.LOADING);  // 设置加载状态 | Set loading state
      setErrorMessage('');

      try {
        // 模拟API调用 | Simulate API call
        const response = await fetch(
          `https://newsapi.org/v2/everything?q=${encodeURIComponent(query)}&apiKey=YOUR_API_KEY`
        );

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        const results = data.articles || [];

        if (results.length === 0) {
          setSearchState(SEARCH_STATE.EMPTY);  // 无结果状态 | Empty state
        } else {
          setSearchState(SEARCH_STATE.SUCCESS);  // 成功状态 | Success state
          setSearchResults(results);
        }
      } catch (error) {
        console.error('Search error:', error);
        setSearchState(SEARCH_STATE.ERROR);  // 错误状态 | Error state
        setErrorMessage(error.message || 'Failed to search articles');
      }
    };

    const handleRetry = () => {
      if (debouncedQuery.trim()) {
        performSearch(debouncedQuery);
      }
    };

    const handleClear = () => {
      setSearchQuery('');  // 清空输入 | Clear input
      setSearchState(SEARCH_STATE.INITIAL);  // 重置状态 | Reset state
      setSearchResults([]);
      setErrorMessage('');
    };

    return (
      <View style={styles.container}>
        {/* 搜索输入框 | Search input */}
        <View style={styles.searchBar}>
          <TextInput
            style={styles.searchInput}
            value={searchQuery}
            onChangeText={setSearchQuery}
            placeholder="Search articles..."
            placeholderTextColor="#999999"
            autoCapitalize="none"
            autoCorrect={false}
          />
          {searchQuery.length > 0 && (
            <Pressable onPress={handleClear} style={styles.clearButton}>
              <Text style={styles.clearButtonText}>✕</Text>
            </Pressable>
          )}
        </View>

        {/* 状态指示 | State indicator (for debugging/demo) */}
        <Text style={styles.debugText}>
          State: {searchState} | Query: "{debouncedQuery}"
        </Text>

        {/* 内容区域 - 根据状态条件渲染 | Content area - conditional rendering based on state */}
        <View style={styles.contentArea}>
          {/* 初始状态 | Initial state */}
          {searchState === SEARCH_STATE.INITIAL && (
            <View style={styles.centeredContainer}>
              <Text style={styles.initialTitle}>🔍 Search for News</Text>
              <Text style={styles.initialSubtitle}>
                Enter keywords to find articles
              </Text>
              <View style={styles.suggestionBox}>
                <Text style={styles.suggestionTitle}>Popular searches:</Text>
                {['Technology', 'Climate', 'Business', 'Sports'].map((term) => (
                  <Pressable
                    key={term}
                    onPress={() => setSearchQuery(term)}
                    style={styles.suggestionChip}
                  >
                    <Text style={styles.suggestionText}>{term}</Text>
                  </Pressable>
                ))}
              </View>
            </View>
          )}

          {/* 加载状态 | Loading state */}
          {searchState === SEARCH_STATE.LOADING && (
            <View style={styles.centeredContainer}>
              <ActivityIndicator size="large" color="#007AFF" />
              <Text style={styles.loadingText}>Searching for "{debouncedQuery}"...</Text>
            </View>
          )}

          {/* 错误状态 | Error state */}
          {searchState === SEARCH_STATE.ERROR && (
            <View style={styles.centeredContainer}>
              <Text style={styles.errorIcon}>⚠️</Text>
              <Text style={styles.errorTitle}>Search Failed</Text>
              <Text style={styles.errorMessage}>{errorMessage}</Text>
              <Pressable style={styles.retryButton} onPress={handleRetry}>
                <Text style={styles.retryButtonText}>Retry Search</Text>
              </Pressable>
            </View>
          )}

          {/* 空结果状态 | Empty results state */}
          {searchState === SEARCH_STATE.EMPTY && (
            <View style={styles.centeredContainer}>
              <Text style={styles.emptyIcon}>📭</Text>
              <Text style={styles.emptyTitle}>No Results Found</Text>
              <Text style={styles.emptyMessage}>
                No articles found for "{debouncedQuery}"
              </Text>
              <View style={styles.emptyTips}>
                <Text style={styles.emptyTipsTitle}>Try:</Text>
                <Text style={styles.emptyTip}>• Using different keywords</Text>
                <Text style={styles.emptyTip}>• Checking your spelling</Text>
                <Text style={styles.emptyTip}>• Using more general terms</Text>
              </View>
            </View>
          )}

          {/* 成功状态 - 显示结果 | Success state - show results */}
          {searchState === SEARCH_STATE.SUCCESS && (
            <ScrollView style={styles.resultsList}>
              <Text style={styles.resultsHeader}>
                Found {searchResults.length} articles for "{debouncedQuery}"
              </Text>
              {searchResults.map((article, index) => (
                <View key={index} style={styles.articleCard}>
                  <Text style={styles.articleTitle} numberOfLines={2}>
                    {article.title}
                  </Text>
                  <Text style={styles.articleDescription} numberOfLines={3}>
                    {article.description}
                  </Text>
                  <Text style={styles.articleSource}>{article.source.name}</Text>
                </View>
              ))}
            </ScrollView>
          )}
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#FFFFFF',
    },
    searchBar: {
      flexDirection: 'row',
      alignItems: 'center',
      margin: 16,
      paddingHorizontal: 12,
      height: 44,
      backgroundColor: '#F5F5F5',
      borderRadius: 8,
      borderWidth: 1,
      borderColor: '#E0E0E0',
    },
    searchInput: {
      flex: 1,
      fontSize: 16,
      color: '#333333',
    },
    clearButton: {
      padding: 4,
    },
    clearButtonText: {
      fontSize: 18,
      color: '#999999',
    },
    debugText: {
      paddingHorizontal: 16,
      fontSize: 12,
      color: '#999999',
      marginBottom: 8,
    },
    contentArea: {
      flex: 1,
    },
    centeredContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    // 初始状态样式 | Initial state styles
    initialTitle: {
      fontSize: 24,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    initialSubtitle: {
      fontSize: 16,
      color: '#666666',
      marginBottom: 24,
    },
    suggestionBox: {
      width: '100%',
      alignItems: 'center',
    },
    suggestionTitle: {
      fontSize: 14,
      color: '#666666',
      marginBottom: 12,
    },
    suggestionChip: {
      paddingHorizontal: 16,
      paddingVertical: 8,
      marginVertical: 4,
      backgroundColor: '#E8F4FF',
      borderRadius: 16,
    },
    suggestionText: {
      fontSize: 14,
      color: '#007AFF',
      fontWeight: '500',
    },
    // 加载状态样式 | Loading state styles
    loadingText: {
      marginTop: 16,
      fontSize: 16,
      color: '#666666',
    },
    // 错误状态样式 | Error state styles
    errorIcon: {
      fontSize: 48,
      marginBottom: 16,
    },
    errorTitle: {
      fontSize: 20,
      fontWeight: '600',
      color: '#D32F2F',
      marginBottom: 8,
    },
    errorMessage: {
      fontSize: 14,
      color: '#666666',
      textAlign: 'center',
      marginBottom: 24,
    },
    retryButton: {
      backgroundColor: '#007AFF',
      paddingHorizontal: 24,
      paddingVertical: 12,
      borderRadius: 8,
    },
    retryButtonText: {
      color: '#FFFFFF',
      fontSize: 16,
      fontWeight: '600',
    },
    // 空结果状态样式 | Empty state styles
    emptyIcon: {
      fontSize: 48,
      marginBottom: 16,
    },
    emptyTitle: {
      fontSize: 20,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    emptyMessage: {
      fontSize: 14,
      color: '#666666',
      textAlign: 'center',
      marginBottom: 24,
    },
    emptyTips: {
      width: '100%',
      paddingHorizontal: 20,
    },
    emptyTipsTitle: {
      fontSize: 14,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    emptyTip: {
      fontSize: 14,
      color: '#666666',
      marginVertical: 4,
    },
    // 结果列表样式 | Results list styles
    resultsList: {
      flex: 1,
    },
    resultsHeader: {
      padding: 16,
      fontSize: 14,
      fontWeight: '600',
      color: '#666666',
      backgroundColor: '#F9F9F9',
      borderBottomWidth: 1,
      borderBottomColor: '#E0E0E0',
    },
    articleCard: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#F0F0F0',
    },
    articleTitle: {
      fontSize: 16,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    articleDescription: {
      fontSize: 14,
      color: '#666666',
      lineHeight: 20,
      marginBottom: 8,
    },
    articleSource: {
      fontSize: 12,
      color: '#999999',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用枚举（SEARCH_STATE）而不是多个布尔状态变量？| Why use enum (SEARCH_STATE) instead of multiple boolean state variables?
    **答案 | Answer:** 确保状态互斥性，避免多个布尔值同时为true的矛盾状态 | Ensures mutual exclusivity, prevents contradictory states where multiple booleans are true simultaneously
  - 初始状态和空结果状态的主要区别是什么？| What's the main difference between initial state and empty results state?
    **答案 | Answer:** 初始状态是未搜索，空结果是搜索后无结果；空结果应显示查询词和改进建议 | Initial is no search, empty is search with no results; empty should show query term and improvement suggestions
  - 为什么在`performSearch`开始时调用`setErrorMessage('')`？| Why call `setErrorMessage('')` at the start of `performSearch`?
    **答案 | Answer:** 清除上次搜索的错误信息，避免新搜索显示旧错误 | Clear error from previous search to prevent showing stale error messages

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要在组件中存储搜索历史记录？| Do you need to store search history in the component?
    **答案 | Answer:** 不是必需的 | Not necessary - Search history should be stored in AsyncStorage or Context for persistence across sessions, not in component state
  - 空结果时是否应该显示"加载中"的加载过往？| Should you show "loading" spinner when results are empty?
    **答案 | Answer:** 否 | No - Empty state means search completed successfully but returned no results. Loading state is only for pending requests

### 4. 状态清理与导航整合 | State Cleanup & Navigation Integration (1小时 | 1 hour)

- **组件生命周期与状态清理 | Component Lifecycle & State Cleanup**

  **概念定义 | Concept Definition:**
  在React Native应用中，当用户离开搜索页面导航到其他屏幕时，需要适当清理搜索状态和取消未完成的网络请求，以避免内存泄漏和不必要的状态更新。| In React Native apps, when users navigate away from the search screen to other screens, it's necessary to properly clean up search state and cancel pending network requests to prevent memory leaks and unnecessary state updates.

  **核心特征 | Key Characteristics:**
  - **导航监听**：使用`useFocusEffect`或`useEffect`监听屏幕焦点变化 | **Navigation listening**: Use `useFocusEffect` or `useEffect` to listen for screen focus changes
  - **请求取消**：使用AbortController取消飞行中的fetch请求 | **Request cancellation**: Use AbortController to cancel in-flight fetch requests
  - **状态重置**：决定是否在导航离开时保留或清除搜索状态 | **State reset**: Decide whether to preserve or clear search state on navigation away
  - **定时器清理**：清除防抖定时器和其他异步操作 | **Timer cleanup**: Clear debounce timers and other async operations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 组件卸载时未清理的fetch请求会发生什么？| What happens to uncleaned fetch requests when component unmounts?
     **答案 | Answer:** 可能导致内存泄漏和"无法在未挂载组件上更新状态"警告 | May cause memory leaks and "Can't perform state update on unmounted component" warnings
  2. `useFocusEffect`和`useEffect`在导航场景中有什么区别？| What's the difference between `useFocusEffect` and `useEffect` in navigation scenarios?
     **答案 | Answer:** `useFocusEffect`在屏幕获得/失去焦点时触发，`useEffect`只在组件挂载/卸载时触发 | `useFocusEffect` triggers when screen gains/loses focus, `useEffect` only on component mount/unmount
  3. 应该在用户每次离开搜索屏幕时都清空搜索历史吗？| Should you clear search history every time user leaves search screen?
     **答案 | Answer:** 取决于产品需求 | Depends on product requirements - Usually preserve results for better UX when returning, but clear input field
  4. AbortController可以取消已经完成的请求吗？| Can AbortController cancel already completed requests?
     **答案 | Answer:** 否 | No - It only cancels pending requests. Completed requests can't be aborted

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect, useRef, useCallback } from 'react';
  import { View, TextInput, Text, ScrollView, StyleSheet } from 'react-native';
  import { useFocusEffect } from '@react-navigation/native';

  // 使用AbortController的搜索组件 | Search component with AbortController
  const SearchWithCleanup = ({ navigation }) => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [isLoading, setIsLoading] = useState(false);

    // 使用useRef存储AbortController | Use useRef to store AbortController
    const abortControllerRef = useRef(null);

    const performSearch = async (query) => {
      // 取消之前的请求 | Cancel previous request
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }

      // 创建新的AbortController | Create new AbortController
      abortControllerRef.current = new AbortController();

      setIsLoading(true);

      try {
        const response = await fetch(
          `https://newsapi.org/v2/everything?q=${encodeURIComponent(query)}`,
          {
            signal: abortControllerRef.current.signal,  // 传递signal | Pass signal
          }
        );

        const data = await response.json();
        setSearchResults(data.articles || []);
      } catch (error) {
        if (error.name === 'AbortError') {
          console.log('Request was cancelled');  // 请求被取消 | Request cancelled
        } else {
          console.error('Search error:', error);
        }
      } finally {
        setIsLoading(false);
      }
    };

    // 清理函数 | Cleanup function
    useEffect(() => {
      return () => {
        // 组件卸载时取消请求 | Cancel request on component unmount
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }
      };
    }, []);

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.input}
          value={searchQuery}
          onChangeText={(text) => {
            setSearchQuery(text);
            if (text.trim()) {
              performSearch(text);
            }
          }}
          placeholder="Search..."
        />
        {isLoading && <Text>Loading...</Text>}
        <ScrollView>
          {searchResults.map((item, index) => (
            <Text key={index}>{item.title}</Text>
          ))}
        </ScrollView>
      </View>
    );
  };

  // 使用useFocusEffect的搜索组件 | Search component with useFocusEffect
  const SearchWithFocusManagement = ({ navigation }) => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [preserveResults, setPreserveResults] = useState(true);

    // 防抖定时器引用 | Debounce timer reference
    const debounceTimerRef = useRef(null);
    const abortControllerRef = useRef(null);

    const performSearch = async (query) => {
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }

      abortControllerRef.current = new AbortController();

      try {
        const response = await fetch(
          `https://newsapi.org/v2/everything?q=${encodeURIComponent(query)}`,
          { signal: abortControllerRef.current.signal }
        );
        const data = await response.json();
        setSearchResults(data.articles || []);
      } catch (error) {
        if (error.name !== 'AbortError') {
          console.error('Search error:', error);
        }
      }
    };

    const handleSearchChange = (text) => {
      setSearchQuery(text);

      // 清除旧的防抖定时器 | Clear old debounce timer
      if (debounceTimerRef.current) {
        clearTimeout(debounceTimerRef.current);
      }

      // 设置新的防抖定时器 | Set new debounce timer
      debounceTimerRef.current = setTimeout(() => {
        if (text.trim()) {
          performSearch(text);
        } else {
          setSearchResults([]);
        }
      }, 500);
    };

    // 屏幕失去焦点时的清理 | Cleanup when screen loses focus
    useFocusEffect(
      useCallback(() => {
        console.log('Search screen focused');

        // 返回清理函数 | Return cleanup function
        return () => {
          console.log('Search screen unfocused');

          // 取消进行中的请求 | Cancel pending requests
          if (abortControllerRef.current) {
            abortControllerRef.current.abort();
          }

          // 清除防抖定时器 | Clear debounce timer
          if (debounceTimerRef.current) {
            clearTimeout(debounceTimerRef.current);
          }

          // 根据配置决定是否清除结果 | Clear results based on configuration
          if (!preserveResults) {
            setSearchQuery('');
            setSearchResults([]);
          }
        };
      }, [preserveResults])  // preserveResults变化时重新创建effect | Recreate effect when preserveResults changes
    );

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.input}
          value={searchQuery}
          onChangeText={handleSearchChange}
          placeholder="Search articles..."
        />
        <ScrollView>
          {searchResults.map((item, index) => (
            <View key={index} style={styles.resultItem}>
              <Text style={styles.resultTitle}>{item.title}</Text>
            </View>
          ))}
        </ScrollView>
      </View>
    );
  };

  // 完整的状态清理示例 | Complete state cleanup example
  const SearchScreenWithFullCleanup = ({ navigation }) => {
    const [searchQuery, setSearchQuery] = useState('');
    const [debouncedQuery, setDebouncedQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [isLoading, setIsLoading] = useState(false);

    const abortControllerRef = useRef(null);
    const debounceTimerRef = useRef(null);
    const isMountedRef = useRef(true);  // 追踪组件挂载状态 | Track component mounted state

    // 防抖逻辑 | Debounce logic
    useEffect(() => {
      if (debounceTimerRef.current) {
        clearTimeout(debounceTimerRef.current);
      }

      debounceTimerRef.current = setTimeout(() => {
        setDebouncedQuery(searchQuery);
      }, 500);

      return () => {
        if (debounceTimerRef.current) {
          clearTimeout(debounceTimerRef.current);
        }
      };
    }, [searchQuery]);

    // 执行搜索 | Perform search
    useEffect(() => {
      if (!debouncedQuery.trim()) {
        setSearchResults([]);
        return;
      }

      const search = async () => {
        // 取消上次请求 | Cancel previous request
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }

        abortControllerRef.current = new AbortController();
        setIsLoading(true);

        try {
          const response = await fetch(
            `https://newsapi.org/v2/everything?q=${encodeURIComponent(debouncedQuery)}`,
            { signal: abortControllerRef.current.signal }
          );

          const data = await response.json();

          // 只在组件仍挂载时更新状态 | Only update state if component still mounted
          if (isMountedRef.current) {
            setSearchResults(data.articles || []);
            setIsLoading(false);
          }
        } catch (error) {
          if (error.name === 'AbortError') {
            console.log('Search cancelled');
          } else if (isMountedRef.current) {
            console.error('Search error:', error);
            setIsLoading(false);
          }
        }
      };

      search();
    }, [debouncedQuery]);

    // 组件卸载清理 | Component unmount cleanup
    useEffect(() => {
      isMountedRef.current = true;

      return () => {
        isMountedRef.current = false;  // 标记组件已卸载 | Mark component unmounted

        // 取消请求 | Cancel requests
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }

        // 清除定时器 | Clear timers
        if (debounceTimerRef.current) {
          clearTimeout(debounceTimerRef.current);
        }
      };
    }, []);

    // 导航焦点管理 | Navigation focus management
    useFocusEffect(
      useCallback(() => {
        // 屏幕获得焦点 | Screen gains focus
        return () => {
          // 屏幕失去焦点时清空输入 | Clear input when screen loses focus
          setSearchQuery('');
        };
      }, [])
    );

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.input}
          value={searchQuery}
          onChangeText={setSearchQuery}
          placeholder="Search articles..."
          autoFocus
        />
        {isLoading && <Text style={styles.loadingText}>Searching...</Text>}
        <ScrollView style={styles.resultsList}>
          {searchResults.map((article, index) => (
            <View key={index} style={styles.resultItem}>
              <Text style={styles.resultTitle}>{article.title}</Text>
            </View>
          ))}
        </ScrollView>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#FFFFFF',
    },
    input: {
      height: 44,
      margin: 16,
      paddingHorizontal: 12,
      borderWidth: 1,
      borderColor: '#CCCCCC',
      borderRadius: 8,
      fontSize: 16,
    },
    loadingText: {
      textAlign: 'center',
      color: '#666666',
      fontSize: 14,
    },
    resultsList: {
      flex: 1,
    },
    resultItem: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#F0F0F0',
    },
    resultTitle: {
      fontSize: 16,
      color: '#333333',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用`isMountedRef`而不是直接使用状态变量？| Why use `isMountedRef` instead of a state variable?
    **答案 | Answer:** useRef不会触发重新渲染，且在组件卸载后仍可访问，适合清理逻辑 | useRef doesn't trigger re-renders and remains accessible after unmount, suitable for cleanup logic
  - `useFocusEffect`的清理函数何时执行？| When does the cleanup function of `useFocusEffect` execute?
    **答案 | Answer:** 屏幕失去焦点时或effect重新运行前 | When screen loses focus or before effect re-runs
  - 如果不使用AbortController会有什么问题？| What problems occur if you don't use AbortController?
    **答案 | Answer:** 快速切换搜索时旧请求仍继续，可能晚于新请求返回导致显示错误结果 | Old requests continue when rapidly changing searches, may return later than new requests causing wrong results display

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要在每个useEffect中都返回清理函数？| Do you need to return cleanup function in every useEffect?
    **答案 | Answer:** 不是必需的 | Not required - Only needed when effect creates subscriptions, timers, or other resources that need cleanup
  - `useFocusEffect`可以完全替代`useEffect`吗？| Can `useFocusEffect` completely replace `useEffect`?
    **答案 | Answer:** 否 | No - `useFocusEffect` is for navigation-specific logic. Use `useEffect` for data that should persist across focus changes

### 5. 搜索性能优化与最佳实践 | Search Performance Optimization & Best Practices (45分钟 | 45 minutes)

- **性能优化策略 | Performance Optimization Strategies**

  **概念定义 | Concept Definition:**
  搜索功能的性能优化涉及减少不必要的渲染、优化网络请求、实现智能缓存机制，以及使用虚拟化列表处理大量结果，从而提供流畅的用户体验。| Search performance optimization involves reducing unnecessary renders, optimizing network requests, implementing intelligent caching mechanisms, and using virtualized lists for large result sets to provide a smooth user experience.

  **核心特征 | Key Characteristics:**
  - **请求去重**：防止相同查询重复请求 | **Request deduplication**: Prevent duplicate requests for same query
  - **结果缓存**：本地缓存搜索结果减少网络请求 | **Result caching**: Cache search results locally to reduce network requests
  - **虚拟化列表**：大量结果使用FlatList而非ScrollView | **Virtualized lists**: Use FlatList instead of ScrollView for large result sets
  - **组件优化**：使用React.memo和useMemo避免不必要渲染 | **Component optimization**: Use React.memo and useMemo to avoid unnecessary renders

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 缓存搜索结果应该使用哪种数据结构？| What data structure should be used to cache search results?
     **答案 | Answer:** Map或Object，以查询词作为key | Map or Object with query as key - Provides O(1) lookup time
  2. 何时应该使用FlatList而不是ScrollView显示搜索结果？| When should you use FlatList instead of ScrollView for search results?
     **答案 | Answer:** 结果超过20-30项时 | When results exceed 20-30 items - FlatList virtualizes offscreen items
  3. React.memo如何帮助优化搜索结果列表？| How does React.memo help optimize search result lists?
     **答案 | Answer:** 防止未改变的列表项重新渲染 | Prevents re-rendering of unchanged list items when parent updates
  4. 应该在什么时候清空搜索结果缓存？| When should you clear search result cache?
     **答案 | Answer:** 应用重启、内存不足或用户手动刷新时 | On app restart, low memory, or user manual refresh

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect, useRef, useMemo, useCallback } from 'react';
  import { View, TextInput, FlatList, Text, StyleSheet } from 'react-native';

  // 结果项组件（使用React.memo优化）| Result item component (optimized with React.memo)
  const SearchResultItem = React.memo(({ article, onPress }) => {
    console.log('Rendering article:', article.title);  // 调试：查看渲染次数 | Debug: check render count

    return (
      <View style={styles.resultItem}>
        <Text style={styles.resultTitle} numberOfLines={2}>
          {article.title}
        </Text>
        <Text style={styles.resultDescription} numberOfLines={2}>
          {article.description}
        </Text>
        <Text style={styles.resultSource}>{article.source.name}</Text>
      </View>
    );
  });

  // 带缓存的优化搜索组件 | Optimized search component with caching
  const OptimizedSearch = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [debouncedQuery, setDebouncedQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [isLoading, setIsLoading] = useState(false);

    // 搜索缓存 | Search cache
    const searchCacheRef = useRef(new Map());
    const abortControllerRef = useRef(null);

    // 防抖逻辑 | Debounce logic
    useEffect(() => {
      const timer = setTimeout(() => {
        setDebouncedQuery(searchQuery);
      }, 500);

      return () => clearTimeout(timer);
    }, [searchQuery]);

    // 执行搜索（带缓存）| Perform search (with caching)
    useEffect(() => {
      if (!debouncedQuery.trim()) {
        setSearchResults([]);
        return;
      }

      // 检查缓存 | Check cache
      if (searchCacheRef.current.has(debouncedQuery)) {
        console.log('Using cached results for:', debouncedQuery);
        setSearchResults(searchCacheRef.current.get(debouncedQuery));
        return;
      }

      const search = async () => {
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }

        abortControllerRef.current = new AbortController();
        setIsLoading(true);

        try {
          const response = await fetch(
            `https://newsapi.org/v2/everything?q=${encodeURIComponent(debouncedQuery)}`,
            { signal: abortControllerRef.current.signal }
          );

          const data = await response.json();
          const results = data.articles || [];

          // 存入缓存 | Store in cache
          searchCacheRef.current.set(debouncedQuery, results);

          // 限制缓存大小（保留最近10个）| Limit cache size (keep last 10)
          if (searchCacheRef.current.size > 10) {
            const firstKey = searchCacheRef.current.keys().next().value;
            searchCacheRef.current.delete(firstKey);
          }

          setSearchResults(results);
        } catch (error) {
          if (error.name !== 'AbortError') {
            console.error('Search error:', error);
          }
        } finally {
          setIsLoading(false);
        }
      };

      search();
    }, [debouncedQuery]);

    // 使用useCallback优化回调函数 | Optimize callback with useCallback
    const renderItem = useCallback(({ item }) => (
      <SearchResultItem article={item} onPress={() => console.log('Pressed:', item.title)} />
    ), []);

    // 使用useMemo优化keyExtractor | Optimize keyExtractor with useMemo
    const keyExtractor = useCallback((item, index) => item.url || index.toString(), []);

    // 渲染空状态组件 | Render empty state component
    const ListEmptyComponent = useMemo(() => (
      <View style={styles.emptyContainer}>
        <Text style={styles.emptyText}>
          {isLoading ? 'Searching...' : 'No results found'}
        </Text>
      </View>
    ), [isLoading]);

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.searchInput}
          value={searchQuery}
          onChangeText={setSearchQuery}
          placeholder="Search articles..."
          placeholderTextColor="#999999"
        />

        <Text style={styles.debugInfo}>
          Cache size: {searchCacheRef.current.size} | Loading: {isLoading.toString()}
        </Text>

        <FlatList
          data={searchResults}
          renderItem={renderItem}  // 使用useCallback包装的函数 | Use useCallback-wrapped function
          keyExtractor={keyExtractor}
          ListEmptyComponent={ListEmptyComponent}
          // 性能优化属性 | Performance optimization props
          removeClippedSubviews={true}  // 移除屏幕外视图 | Remove offscreen views
          maxToRenderPerBatch={10}      // 每批渲染数量 | Number to render per batch
          updateCellsBatchingPeriod={50}  // 批处理周期 | Batching period
          initialNumToRender={10}       // 初始渲染数量 | Initial render count
          windowSize={5}                // 视窗大小倍数 | Window size multiplier
        />
      </View>
    );
  };

  // 请求去重示例 | Request deduplication example
  const SearchWithDeduplication = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);

    // 追踪进行中的请求 | Track in-flight requests
    const pendingRequestsRef = useRef(new Set());
    const cacheRef = useRef(new Map());

    const performSearch = async (query) => {
      // 检查是否已有相同请求 | Check if same request already pending
      if (pendingRequestsRef.current.has(query)) {
        console.log('Request already in flight for:', query);
        return;
      }

      // 检查缓存 | Check cache
      if (cacheRef.current.has(query)) {
        console.log('Using cache for:', query);
        setSearchResults(cacheRef.current.get(query));
        return;
      }

      // 标记请求开始 | Mark request start
      pendingRequestsRef.current.add(query);

      try {
        const response = await fetch(
          `https://newsapi.org/v2/everything?q=${encodeURIComponent(query)}`
        );
        const data = await response.json();
        const results = data.articles || [];

        // 缓存结果 | Cache results
        cacheRef.current.set(query, results);
        setSearchResults(results);
      } catch (error) {
        console.error('Search error:', error);
      } finally {
        // 移除请求标记 | Remove request marker
        pendingRequestsRef.current.delete(query);
      }
    };

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.searchInput}
          value={searchQuery}
          onChangeText={(text) => {
            setSearchQuery(text);
            if (text.trim()) {
              performSearch(text);
            }
          }}
          placeholder="Search with deduplication..."
        />
        <Text style={styles.debugInfo}>
          Pending requests: {pendingRequestsRef.current.size}
        </Text>
        <FlatList
          data={searchResults}
          renderItem={({ item }) => (
            <View style={styles.resultItem}>
              <Text>{item.title}</Text>
            </View>
          )}
          keyExtractor={(item, index) => index.toString()}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#FFFFFF',
    },
    searchInput: {
      height: 44,
      margin: 16,
      paddingHorizontal: 12,
      borderWidth: 1,
      borderColor: '#CCCCCC',
      borderRadius: 8,
      fontSize: 16,
    },
    debugInfo: {
      paddingHorizontal: 16,
      fontSize: 12,
      color: '#999999',
      marginBottom: 8,
    },
    resultItem: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#F0F0F0',
    },
    resultTitle: {
      fontSize: 16,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 4,
    },
    resultDescription: {
      fontSize: 14,
      color: '#666666',
      marginBottom: 4,
    },
    resultSource: {
      fontSize: 12,
      color: '#999999',
    },
    emptyContainer: {
      padding: 40,
      alignItems: 'center',
    },
    emptyText: {
      fontSize: 16,
      color: '#999999',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用Map而不是普通Object作为缓存？| Why use Map instead of plain Object for cache?
    **答案 | Answer:** Map保持插入顺序，支持任意类型key，有size属性，更适合缓存场景 | Map preserves insertion order, supports any key type, has size property, better suited for caching
  - `React.memo`如何判断组件是否需要重新渲染？| How does React.memo determine if component needs re-render?
    **答案 | Answer:** 浅比较props，所有props相同则跳过渲染 | Shallow compares props, skips render if all props are the same
  - FlatList的`windowSize`属性是什么意思？| What does FlatList's `windowSize` prop mean?
    **答案 | Answer:** 渲染窗口大小的倍数（相对于可见区域），默认21 | Multiplier of render window size (relative to visible area), defaults to 21

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该缓存所有搜索结果永久保存？| Should you cache all search results permanently?
    **答案 | Answer:** 否 | No - Implement cache size limits and TTL (time-to-live) to prevent memory issues
  - React.memo会深度比较props吗？| Does React.memo deep compare props?
    **答案 | Answer:** 否 | No - Only shallow comparison by default. Provide custom comparison function for deep comparison if needed

### 6. 完整搜索功能整合 | Complete Search Feature Integration (30分钟 | 30 minutes)

- **将所有概念整合到生产级搜索组件 | Integrating All Concepts into Production-Grade Search Component**

  **概念定义 | Concept Definition:**
  生产级搜索组件需要整合防抖、状态管理、错误处理、性能优化、导航整合等所有学到的技术，提供完整、健壮且用户友好的搜索体验。| A production-grade search component integrates all learned techniques including debouncing, state management, error handling, performance optimization, and navigation integration to provide a complete, robust, and user-friendly search experience.

  **核心特征 | Key Characteristics:**
  - **完整状态机**：覆盖所有可能的状态转换 | **Complete state machine**: Covers all possible state transitions
  - **可复用性**：通过props配置实现组件复用 | **Reusability**: Component reuse through prop configuration
  - **可测试性**：关注点分离，便于单元测试 | **Testability**: Separation of concerns for easy unit testing
  - **可访问性**：支持屏幕阅读器和键盘导航 | **Accessibility**: Supports screen readers and keyboard navigation

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 生产级组件和demo组件的主要区别是什么？| What's the main difference between production and demo components?
     **答案 | Answer:** 生产级组件有完整错误处理、性能优化、可配置性、可测试性和文档 | Production components have complete error handling, performance optimization, configurability, testability, and documentation
  2. 为什么要将搜索逻辑抽离到自定义Hook中？| Why extract search logic into custom Hook?
     **答案 | Answer:** 提高复用性和可测试性，分离业务逻辑和UI | Improves reusability and testability, separates business logic from UI
  3. 如何测试带有异步操作的搜索组件？| How to test search components with async operations?
     **答案 | Answer:** 使用mock函数模拟API调用，测试状态变化和UI响应 | Use mock functions to simulate API calls, test state changes and UI responses
  4. 生产环境中应该如何处理API密钥？| How should API keys be handled in production?
     **答案 | Answer:** 使用环境变量，通过后端代理API请求，不暴露在客户端 | Use environment variables, proxy API requests through backend, don't expose in client

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect, useRef, useCallback } from 'react';
  import {
    View,
    TextInput,
    FlatList,
    Text,
    Pressable,
    ActivityIndicator,
    StyleSheet,
  } from 'react-native';
  import { useFocusEffect } from '@react-navigation/native';

  // 自定义Hook：搜索功能逻辑 | Custom Hook: Search functionality logic
  const useSearch = ({ apiEndpoint, debounceDelay = 500, cacheSize = 10 }) => {
    const [query, setQuery] = useState('');
    const [debouncedQuery, setDebouncedQuery] = useState('');
    const [results, setResults] = useState([]);
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState(null);

    const cacheRef = useRef(new Map());
    const abortControllerRef = useRef(null);
    const isMountedRef = useRef(true);

    // 防抖逻辑 | Debounce logic
    useEffect(() => {
      const timer = setTimeout(() => {
        setDebouncedQuery(query);
      }, debounceDelay);

      return () => clearTimeout(timer);
    }, [query, debounceDelay]);

    // 搜索逻辑 | Search logic
    useEffect(() => {
      if (!debouncedQuery.trim()) {
        setResults([]);
        setError(null);
        return;
      }

      // 检查缓存 | Check cache
      if (cacheRef.current.has(debouncedQuery)) {
        setResults(cacheRef.current.get(debouncedQuery));
        return;
      }

      const performSearch = async () => {
        // 取消之前的请求 | Cancel previous request
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }

        abortControllerRef.current = new AbortController();
        setIsLoading(true);
        setError(null);

        try {
          const response = await fetch(
            `${apiEndpoint}?q=${encodeURIComponent(debouncedQuery)}`,
            { signal: abortControllerRef.current.signal }
          );

          if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
          }

          const data = await response.json();
          const articles = data.articles || [];

          if (isMountedRef.current) {
            // 更新缓存 | Update cache
            cacheRef.current.set(debouncedQuery, articles);

            // 限制缓存大小 | Limit cache size
            if (cacheRef.current.size > cacheSize) {
              const firstKey = cacheRef.current.keys().next().value;
              cacheRef.current.delete(firstKey);
            }

            setResults(articles);
          }
        } catch (err) {
          if (err.name === 'AbortError') {
            console.log('Search cancelled');
          } else if (isMountedRef.current) {
            setError(err.message);
          }
        } finally {
          if (isMountedRef.current) {
            setIsLoading(false);
          }
        }
      };

      performSearch();
    }, [debouncedQuery, apiEndpoint, cacheSize]);

    // 清理函数 | Cleanup function
    useEffect(() => {
      isMountedRef.current = true;

      return () => {
        isMountedRef.current = false;
        if (abortControllerRef.current) {
          abortControllerRef.current.abort();
        }
      };
    }, []);

    const clearSearch = useCallback(() => {
      setQuery('');
      setDebouncedQuery('');
      setResults([]);
      setError(null);
    }, []);

    const retry = useCallback(() => {
      if (debouncedQuery.trim()) {
        // 清除缓存中的这个查询 | Remove this query from cache
        cacheRef.current.delete(debouncedQuery);
        // 重新触发搜索 | Re-trigger search
        setDebouncedQuery('');
        setTimeout(() => setDebouncedQuery(query), 0);
      }
    }, [debouncedQuery, query]);

    return {
      query,
      setQuery,
      debouncedQuery,
      results,
      isLoading,
      error,
      clearSearch,
      retry,
    };
  };

  // 生产级搜索组件 | Production-grade search component
  const ProductionSearchScreen = ({ navigation }) => {
    const {
      query,
      setQuery,
      debouncedQuery,
      results,
      isLoading,
      error,
      clearSearch,
      retry,
    } = useSearch({
      apiEndpoint: 'https://newsapi.org/v2/everything',
      debounceDelay: 500,
      cacheSize: 10,
    });

    // 导航焦点管理 | Navigation focus management
    useFocusEffect(
      useCallback(() => {
        return () => {
          // 屏幕失焦时清空搜索 | Clear search when screen loses focus
          clearSearch();
        };
      }, [clearSearch])
    );

    // 渲染搜索结果项 | Render search result item
    const renderResultItem = useCallback(({ item }) => (
      <Pressable
        style={styles.resultItem}
        onPress={() => navigation.navigate('ArticleDetail', { article: item })}
      >
        <Text style={styles.resultTitle} numberOfLines={2}>
          {item.title}
        </Text>
        <Text style={styles.resultDescription} numberOfLines={2}>
          {item.description}
        </Text>
        <View style={styles.resultMeta}>
          <Text style={styles.resultSource}>{item.source.name}</Text>
          <Text style={styles.resultDate}>
            {new Date(item.publishedAt).toLocaleDateString()}
          </Text>
        </View>
      </Pressable>
    ), [navigation]);

    // Key提取器 | Key extractor
    const keyExtractor = useCallback((item, index) => item.url || index.toString(), []);

    // 渲染空状态 | Render empty state
    const renderEmptyState = () => {
      if (isLoading) {
        return (
          <View style={styles.centeredContainer}>
            <ActivityIndicator size="large" color="#007AFF" />
            <Text style={styles.loadingText}>Searching for "{debouncedQuery}"...</Text>
          </View>
        );
      }

      if (error) {
        return (
          <View style={styles.centeredContainer}>
            <Text style={styles.errorIcon}>⚠️</Text>
            <Text style={styles.errorTitle}>Search Failed</Text>
            <Text style={styles.errorMessage}>{error}</Text>
            <Pressable style={styles.retryButton} onPress={retry}>
              <Text style={styles.retryButtonText}>Retry</Text>
            </Pressable>
          </View>
        );
      }

      if (debouncedQuery.trim() && results.length === 0) {
        return (
          <View style={styles.centeredContainer}>
            <Text style={styles.emptyIcon}>📭</Text>
            <Text style={styles.emptyTitle}>No Results Found</Text>
            <Text style={styles.emptyMessage}>
              No articles found for "{debouncedQuery}"
            </Text>
          </View>
        );
      }

      return (
        <View style={styles.centeredContainer}>
          <Text style={styles.initialIcon}>🔍</Text>
          <Text style={styles.initialTitle}>Search Articles</Text>
          <Text style={styles.initialMessage}>Enter keywords to find news articles</Text>
        </View>
      );
    };

    return (
      <View style={styles.container}>
        {/* 搜索栏 | Search bar */}
        <View style={styles.searchBar}>
          <TextInput
            style={styles.searchInput}
            value={query}
            onChangeText={setQuery}
            placeholder="Search articles..."
            placeholderTextColor="#999999"
            autoCapitalize="none"
            autoCorrect={false}
            returnKeyType="search"
          />
          {query.length > 0 && (
            <Pressable onPress={clearSearch} style={styles.clearButton}>
              <Text style={styles.clearButtonText}>✕</Text>
            </Pressable>
          )}
        </View>

        {/* 结果列表 | Results list */}
        <FlatList
          data={results}
          renderItem={renderResultItem}
          keyExtractor={keyExtractor}
          ListEmptyComponent={renderEmptyState}
          contentContainerStyle={
            results.length === 0 ? styles.emptyListContent : styles.listContent
          }
          // 性能优化 | Performance optimization
          removeClippedSubviews={true}
          maxToRenderPerBatch={10}
          updateCellsBatchingPeriod={50}
          initialNumToRender={10}
          windowSize={5}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#FFFFFF',
    },
    searchBar: {
      flexDirection: 'row',
      alignItems: 'center',
      margin: 16,
      paddingHorizontal: 12,
      height: 44,
      backgroundColor: '#F5F5F5',
      borderRadius: 8,
      borderWidth: 1,
      borderColor: '#E0E0E0',
    },
    searchInput: {
      flex: 1,
      fontSize: 16,
      color: '#333333',
    },
    clearButton: {
      padding: 4,
    },
    clearButtonText: {
      fontSize: 18,
      color: '#999999',
    },
    listContent: {
      paddingBottom: 16,
    },
    emptyListContent: {
      flexGrow: 1,
    },
    centeredContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    resultItem: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#F0F0F0',
    },
    resultTitle: {
      fontSize: 16,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    resultDescription: {
      fontSize: 14,
      color: '#666666',
      lineHeight: 20,
      marginBottom: 8,
    },
    resultMeta: {
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    resultSource: {
      fontSize: 12,
      color: '#007AFF',
      fontWeight: '500',
    },
    resultDate: {
      fontSize: 12,
      color: '#999999',
    },
    loadingText: {
      marginTop: 12,
      fontSize: 14,
      color: '#666666',
    },
    errorIcon: {
      fontSize: 48,
      marginBottom: 16,
    },
    errorTitle: {
      fontSize: 20,
      fontWeight: '600',
      color: '#D32F2F',
      marginBottom: 8,
    },
    errorMessage: {
      fontSize: 14,
      color: '#666666',
      textAlign: 'center',
      marginBottom: 24,
    },
    retryButton: {
      backgroundColor: '#007AFF',
      paddingHorizontal: 24,
      paddingVertical: 12,
      borderRadius: 8,
    },
    retryButtonText: {
      color: '#FFFFFF',
      fontSize: 16,
      fontWeight: '600',
    },
    emptyIcon: {
      fontSize: 48,
      marginBottom: 16,
    },
    emptyTitle: {
      fontSize: 20,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    emptyMessage: {
      fontSize: 14,
      color: '#666666',
      textAlign: 'center',
    },
    initialIcon: {
      fontSize: 48,
      marginBottom: 16,
    },
    initialTitle: {
      fontSize: 20,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    initialMessage: {
      fontSize: 14,
      color: '#666666',
      textAlign: 'center',
    },
  });

  export default ProductionSearchScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么将搜索逻辑封装在自定义Hook中？| Why encapsulate search logic in a custom Hook?
    **答案 | Answer:** 分离关注点，提高复用性和可测试性，可在其他组件中复用 | Separates concerns, improves reusability and testability, can be reused in other components
  - `useSearch` Hook返回的`retry`函数如何工作？| How does the `retry` function returned by `useSearch` Hook work?
    **答案 | Answer:** 清除缓存并重置防抖查询触发重新搜索 | Clears cache and resets debounced query to trigger new search
  - 为什么使用`FlatList`的`contentContainerStyle`而不是`style`？| Why use `contentContainerStyle` instead of `style` on FlatList?
    **答案 | Answer:** `contentContainerStyle`控制内容容器，支持flexGrow实现空状态居中 | `contentContainerStyle` controls content container, supports flexGrow for centering empty states

  **常见误区检查 | Common Misconception Checks:**
  - 自定义Hook可以在条件语句中调用吗？| Can custom Hooks be called inside conditional statements?
    **答案 | Answer:** 否 | No - Hooks must be called at the top level of components, following Rules of Hooks
  - 生产级组件是否意味着代码量越多越好？| Does production-grade mean more code is better?
    **答案 | Answer:** 否 | No - Production-grade means robust, maintainable, and well-tested, not necessarily verbose. Balance complexity with readability
