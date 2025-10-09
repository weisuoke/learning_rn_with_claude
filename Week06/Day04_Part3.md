# React Native入门 - 第6周第4天：分类与搜索功能（第3部分）| React Native Introduction - Week 6 Day 4: Categories & Search Functionality (Part 3)

## 实践项目：新闻搜索与分类浏览器 | Practical Project: News Search & Category Browser

### 目标 | Objective
构建一个完整的新闻搜索与分类浏览功能，整合水平滚动分类选择器、防抖搜索、多状态管理、性能优化和导航整合，形成真实应用级别的用户体验。| Build a complete news search and category browsing feature that integrates horizontal scrolling category selector, debounced search, multi-state management, performance optimization, and navigation integration to create a real-world app-level user experience.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 水平ScrollView和分类选择器的区别是什么？| What's the difference between horizontal ScrollView and category selector?
   **答案 | Answer:** ScrollView是容器组件，分类选择器是基于ScrollView实现的UI模式，包含可点击的分类按钮和选中状态管理 | ScrollView is a container component; category selector is a UI pattern built on ScrollView, including clickable category buttons and selection state management

2. 防抖（Debouncing）如何减少API调用次数？| How does debouncing reduce API call frequency?
   **答案 | Answer:** 延迟函数执行直到用户停止输入一段时间，避免每次按键都触发API调用 | Delays function execution until user stops typing for a period, avoiding API calls on every keystroke

3. 为什么需要在组件卸载时取消网络请求？| Why cancel network requests when component unmounts?
   **答案 | Answer:** 防止在已卸载组件上更新状态导致内存泄漏和警告 | Prevents state updates on unmounted components that cause memory leaks and warnings

### 步骤 | Steps

#### 步骤1：创建项目目录结构 | Step 1: Create Project Directory Structure
```
/src
  /screens
    CategorySearchScreen.js  # 主搜索屏幕 | Main search screen
  /components
    CategorySelector.js      # 分类选择器组件 | Category selector component
    SearchBar.js            # 搜索栏组件 | Search bar component
    ArticleList.js          # 文章列表组件 | Article list component
    EmptyState.js           # 空状态组件 | Empty state component
  /hooks
    useSearch.js            # 搜索逻辑Hook | Search logic hook
    useDebounce.js          # 防抖Hook | Debounce hook
  /api
    newsAPI.js              # API调用封装 | API call wrapper
  /constants
    categories.js           # 分类常量 | Category constants
```

#### 步骤2：实现防抖Hook | Step 2: Implement Debounce Hook
创建 `src/hooks/useDebounce.js`:

```javascript
import { useState, useEffect } from 'react';

/**
 * 防抖Hook
 * @param {any} value - 需要防抖的值
 * @param {number} delay - 延迟时间（毫秒）
 * @returns {any} - 防抖后的值
 */
export const useDebounce = (value, delay) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    // 设置延迟定时器 | Set delay timer
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // 清理函数：清除定时器 | Cleanup: clear timer
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
};
```

#### 步骤3：创建API服务层 | Step 3: Create API Service Layer
创建 `src/api/newsAPI.js`:

```javascript
const API_KEY = 'YOUR_NEWS_API_KEY'; // 替换为你的API密钥 | Replace with your API key
const BASE_URL = 'https://newsapi.org/v2';

/**
 * 获取分类新闻
 * @param {string} category - 分类名称
 * @param {AbortSignal} signal - 取消信号
 * @returns {Promise<Array>} - 文章数组
 */
export const fetchArticlesByCategory = async (category, signal) => {
  const url = `${BASE_URL}/top-headlines?category=${category}&country=us&apiKey=${API_KEY}`;

  const response = await fetch(url, { signal });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }

  const data = await response.json();
  return data.articles || [];
};

/**
 * 搜索文章
 * @param {string} query - 搜索查询
 * @param {AbortSignal} signal - 取消信号
 * @returns {Promise<Array>} - 文章数组
 */
export const searchArticles = async (query, signal) => {
  const url = `${BASE_URL}/everything?q=${encodeURIComponent(query)}&sortBy=publishedAt&apiKey=${API_KEY}`;

  const response = await fetch(url, { signal });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }

  const data = await response.json();
  return data.articles || [];
};
```

#### 步骤4：定义分类常量 | Step 4: Define Category Constants
创建 `src/constants/categories.js`:

```javascript
export const CATEGORIES = [
  { id: 'general', label: 'General', icon: '📰' },
  { id: 'business', label: 'Business', icon: '💼' },
  { id: 'technology', label: 'Technology', icon: '💻' },
  { id: 'entertainment', label: 'Entertainment', icon: '🎬' },
  { id: 'sports', label: 'Sports', icon: '⚽' },
  { id: 'science', label: 'Science', icon: '🔬' },
  { id: 'health', label: 'Health', icon: '🏥' },
];
```

#### 步骤5：创建搜索逻辑Hook | Step 5: Create Search Logic Hook
创建 `src/hooks/useSearch.js`:

```javascript
import { useState, useEffect, useRef } from 'react';
import { searchArticles } from '../api/newsAPI';

export const useSearch = (debouncedQuery) => {
  const [results, setResults] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const abortControllerRef = useRef(null);
  const cacheRef = useRef(new Map());
  const isMountedRef = useRef(true);

  useEffect(() => {
    isMountedRef.current = true;

    return () => {
      isMountedRef.current = false;
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, []);

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
        const articles = await searchArticles(
          debouncedQuery,
          abortControllerRef.current.signal
        );

        if (isMountedRef.current) {
          setResults(articles);
          // 缓存结果 | Cache results
          cacheRef.current.set(debouncedQuery, articles);

          // 限制缓存大小 | Limit cache size
          if (cacheRef.current.size > 10) {
            const firstKey = cacheRef.current.keys().next().value;
            cacheRef.current.delete(firstKey);
          }
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
  }, [debouncedQuery]);

  const retry = () => {
    if (debouncedQuery.trim()) {
      cacheRef.current.delete(debouncedQuery);
      // 触发重新搜索 | Trigger re-search
      setIsLoading(true);
    }
  };

  return { results, isLoading, error, retry };
};
```

#### 步骤6：创建分类选择器组件 | Step 6: Create Category Selector Component
创建 `src/components/CategorySelector.js`:

```javascript
import React from 'react';
import { ScrollView, Pressable, Text, StyleSheet } from 'react-native';
import { CATEGORIES } from '../constants/categories';

const CategorySelector = ({ selectedCategory, onSelectCategory }) => {
  return (
    <ScrollView
      horizontal
      showsHorizontalScrollIndicator={false}
      style={styles.container}
      contentContainerStyle={styles.contentContainer}
    >
      {CATEGORIES.map((category) => {
        const isSelected = selectedCategory === category.id;
        return (
          <Pressable
            key={category.id}
            onPress={() => onSelectCategory(category.id)}
            style={[
              styles.categoryChip,
              isSelected && styles.categoryChipSelected,
            ]}
          >
            <Text style={styles.categoryIcon}>{category.icon}</Text>
            <Text
              style={[
                styles.categoryLabel,
                isSelected && styles.categoryLabelSelected,
              ]}
            >
              {category.label}
            </Text>
          </Pressable>
        );
      })}
    </ScrollView>
  );
};

const styles = StyleSheet.create({
  container: {
    flexGrow: 0,
    backgroundColor: '#FFFFFF',
    borderBottomWidth: 1,
    borderBottomColor: '#E0E0E0',
  },
  contentContainer: {
    paddingHorizontal: 16,
    paddingVertical: 12,
  },
  categoryChip: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingHorizontal: 16,
    paddingVertical: 8,
    marginRight: 10,
    backgroundColor: '#F5F5F5',
    borderRadius: 20,
    borderWidth: 2,
    borderColor: 'transparent',
  },
  categoryChipSelected: {
    backgroundColor: '#E8F4FF',
    borderColor: '#007AFF',
  },
  categoryIcon: {
    fontSize: 16,
    marginRight: 6,
  },
  categoryLabel: {
    fontSize: 14,
    fontWeight: '500',
    color: '#333333',
  },
  categoryLabelSelected: {
    color: '#007AFF',
    fontWeight: '600',
  },
});

export default CategorySelector;
```

#### 步骤7：创建搜索栏组件 | Step 7: Create Search Bar Component
创建 `src/components/SearchBar.js`:

```javascript
import React from 'react';
import { View, TextInput, Pressable, Text, StyleSheet } from 'react-native';

const SearchBar = ({ value, onChangeText, onClear }) => {
  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        value={value}
        onChangeText={onChangeText}
        placeholder="Search articles..."
        placeholderTextColor="#999999"
        autoCapitalize="none"
        autoCorrect={false}
        returnKeyType="search"
      />
      {value.length > 0 && (
        <Pressable onPress={onClear} style={styles.clearButton}>
          <Text style={styles.clearButtonText}>✕</Text>
        </Pressable>
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
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
  input: {
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
});

export default SearchBar;
```

#### 步骤8：创建空状态组件 | Step 8: Create Empty State Component
创建 `src/components/EmptyState.js`:

```javascript
import React from 'react';
import { View, Text, Pressable, ActivityIndicator, StyleSheet } from 'react-native';

const EmptyState = ({ type, query, onRetry }) => {
  if (type === 'loading') {
    return (
      <View style={styles.container}>
        <ActivityIndicator size="large" color="#007AFF" />
        <Text style={styles.message}>Loading articles...</Text>
      </View>
    );
  }

  if (type === 'error') {
    return (
      <View style={styles.container}>
        <Text style={styles.icon}>⚠️</Text>
        <Text style={styles.title}>Something Went Wrong</Text>
        <Text style={styles.message}>Failed to load articles</Text>
        <Pressable style={styles.button} onPress={onRetry}>
          <Text style={styles.buttonText}>Retry</Text>
        </Pressable>
      </View>
    );
  }

  if (type === 'empty') {
    return (
      <View style={styles.container}>
        <Text style={styles.icon}>📭</Text>
        <Text style={styles.title}>No Results</Text>
        <Text style={styles.message}>
          No articles found {query ? `for "${query}"` : ''}
        </Text>
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <Text style={styles.icon}>🔍</Text>
      <Text style={styles.title}>Search News</Text>
      <Text style={styles.message}>Enter keywords or browse categories</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  icon: {
    fontSize: 48,
    marginBottom: 16,
  },
  title: {
    fontSize: 20,
    fontWeight: '600',
    color: '#333333',
    marginBottom: 8,
  },
  message: {
    fontSize: 14,
    color: '#666666',
    textAlign: 'center',
    marginBottom: 24,
  },
  button: {
    backgroundColor: '#007AFF',
    paddingHorizontal: 24,
    paddingVertical: 12,
    borderRadius: 8,
  },
  buttonText: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: '600',
  },
});

export default EmptyState;
```

#### 步骤9：创建文章列表组件 | Step 9: Create Article List Component
创建 `src/components/ArticleList.js`:

```javascript
import React, { useCallback } from 'react';
import { FlatList, View, Text, Pressable, StyleSheet } from 'react-native';

const ArticleItem = React.memo(({ article, onPress }) => (
  <Pressable style={styles.articleItem} onPress={() => onPress(article)}>
    <Text style={styles.articleTitle} numberOfLines={2}>
      {article.title}
    </Text>
    <Text style={styles.articleDescription} numberOfLines={2}>
      {article.description}
    </Text>
    <View style={styles.articleMeta}>
      <Text style={styles.articleSource}>{article.source.name}</Text>
      <Text style={styles.articleDate}>
        {new Date(article.publishedAt).toLocaleDateString()}
      </Text>
    </View>
  </Pressable>
));

const ArticleList = ({ articles, onArticlePress }) => {
  const renderItem = useCallback(
    ({ item }) => <ArticleItem article={item} onPress={onArticlePress} />,
    [onArticlePress]
  );

  const keyExtractor = useCallback(
    (item, index) => item.url || index.toString(),
    []
  );

  return (
    <FlatList
      data={articles}
      renderItem={renderItem}
      keyExtractor={keyExtractor}
      contentContainerStyle={styles.listContent}
      // 性能优化 | Performance optimization
      removeClippedSubviews={true}
      maxToRenderPerBatch={10}
      updateCellsBatchingPeriod={50}
      initialNumToRender={10}
      windowSize={5}
    />
  );
};

const styles = StyleSheet.create({
  listContent: {
    paddingBottom: 16,
  },
  articleItem: {
    padding: 16,
    borderBottomWidth: 1,
    borderBottomColor: '#F0F0F0',
    backgroundColor: '#FFFFFF',
  },
  articleTitle: {
    fontSize: 16,
    fontWeight: '600',
    color: '#333333',
    marginBottom: 8,
    lineHeight: 22,
  },
  articleDescription: {
    fontSize: 14,
    color: '#666666',
    lineHeight: 20,
    marginBottom: 8,
  },
  articleMeta: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  articleSource: {
    fontSize: 12,
    color: '#007AFF',
    fontWeight: '500',
  },
  articleDate: {
    fontSize: 12,
    color: '#999999',
  },
});

export default ArticleList;
```

#### 步骤10：创建主屏幕组件 | Step 10: Create Main Screen Component
创建 `src/screens/CategorySearchScreen.js`:

```javascript
import React, { useState, useEffect, useRef, useCallback } from 'react';
import { View, StyleSheet } from 'react-native';
import { useFocusEffect } from '@react-navigation/native';
import CategorySelector from '../components/CategorySelector';
import SearchBar from '../components/SearchBar';
import ArticleList from '../components/ArticleList';
import EmptyState from '../components/EmptyState';
import { useDebounce } from '../hooks/useDebounce';
import { useSearch } from '../hooks/useSearch';
import { fetchArticlesByCategory } from '../api/newsAPI';

const CategorySearchScreen = ({ navigation }) => {
  const [selectedCategory, setSelectedCategory] = useState('general');
  const [searchQuery, setSearchQuery] = useState('');
  const [categoryArticles, setCategoryArticles] = useState([]);
  const [categoryLoading, setCategoryLoading] = useState(false);
  const [categoryError, setCategoryError] = useState(null);

  const abortControllerRef = useRef(null);
  const isMountedRef = useRef(true);

  // 搜索防抖 | Search debounce
  const debouncedSearchQuery = useDebounce(searchQuery, 500);

  // 使用搜索Hook | Use search hook
  const {
    results: searchResults,
    isLoading: searchLoading,
    error: searchError,
    retry: retrySearch,
  } = useSearch(debouncedSearchQuery);

  // 获取分类文章 | Fetch category articles
  useEffect(() => {
    isMountedRef.current = true;

    return () => {
      isMountedRef.current = false;
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }
    };
  }, []);

  useEffect(() => {
    const fetchCategoryArticles = async () => {
      if (abortControllerRef.current) {
        abortControllerRef.current.abort();
      }

      abortControllerRef.current = new AbortController();
      setCategoryLoading(true);
      setCategoryError(null);

      try {
        const articles = await fetchArticlesByCategory(
          selectedCategory,
          abortControllerRef.current.signal
        );

        if (isMountedRef.current) {
          setCategoryArticles(articles);
        }
      } catch (error) {
        if (error.name === 'AbortError') {
          console.log('Category fetch cancelled');
        } else if (isMountedRef.current) {
          setCategoryError(error.message);
        }
      } finally {
        if (isMountedRef.current) {
          setCategoryLoading(false);
        }
      }
    };

    // 只在没有搜索查询时获取分类文章 | Only fetch category articles when no search query
    if (!searchQuery.trim()) {
      fetchCategoryArticles();
    }
  }, [selectedCategory, searchQuery]);

  // 导航焦点管理 | Navigation focus management
  useFocusEffect(
    useCallback(() => {
      return () => {
        // 离开屏幕时清空搜索 | Clear search when leaving screen
        setSearchQuery('');
      };
    }, [])
  );

  // 处理文章点击 | Handle article press
  const handleArticlePress = useCallback(
    (article) => {
      navigation.navigate('ArticleDetail', { article });
    },
    [navigation]
  );

  // 处理清空搜索 | Handle clear search
  const handleClearSearch = () => {
    setSearchQuery('');
  };

  // 重试分类加载 | Retry category loading
  const retryCategoryLoad = () => {
    setSelectedCategory(selectedCategory); // 触发重新加载 | Trigger reload
  };

  // 确定当前显示内容 | Determine current display content
  const isSearching = searchQuery.trim().length > 0;
  const currentArticles = isSearching ? searchResults : categoryArticles;
  const currentLoading = isSearching ? searchLoading : categoryLoading;
  const currentError = isSearching ? searchError : categoryError;
  const currentRetry = isSearching ? retrySearch : retryCategoryLoad;

  return (
    <View style={styles.container}>
      {/* 搜索栏 | Search bar */}
      <SearchBar
        value={searchQuery}
        onChangeText={setSearchQuery}
        onClear={handleClearSearch}
      />

      {/* 分类选择器 | Category selector */}
      <CategorySelector
        selectedCategory={selectedCategory}
        onSelectCategory={setSelectedCategory}
      />

      {/* 内容区域 | Content area */}
      {currentLoading && (
        <EmptyState type="loading" />
      )}

      {!currentLoading && currentError && (
        <EmptyState type="error" onRetry={currentRetry} />
      )}

      {!currentLoading && !currentError && currentArticles.length === 0 && (
        <EmptyState
          type={isSearching ? 'empty' : 'initial'}
          query={debouncedSearchQuery}
        />
      )}

      {!currentLoading && !currentError && currentArticles.length > 0 && (
        <ArticleList
          articles={currentArticles}
          onArticlePress={handleArticlePress}
        />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FFFFFF',
  },
});

export default CategorySearchScreen;
```

### 项目完成检查 | Project Completion Check
1. 分类选择器是否正确实现水平滚动和选中状态？| Does the category selector correctly implement horizontal scrolling and selected state?
2. 搜索输入是否使用了防抖技术减少API调用？| Does search input use debouncing to reduce API calls?
3. 是否正确处理了加载、错误和空状态？| Are loading, error, and empty states handled correctly?
4. 组件卸载时是否取消了未完成的网络请求？| Are incomplete network requests cancelled on component unmount?
5. 是否使用了FlatList进行列表性能优化？| Is FlatList used for list performance optimization?
6. 搜索结果是否正确缓存以避免重复请求？| Are search results properly cached to avoid duplicate requests?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **防抖时间优化练习 | Debounce Timing Optimization Exercise**
   - **练习描述 | Exercise Description:** 实现一个可配置的防抖延迟设置，允许用户在设置中调整搜索防抖时间（200ms、500ms、800ms）| Implement configurable debounce delay settings, allowing users to adjust search debounce time in settings (200ms, 500ms, 800ms)
   - **概念检查 | Concept Check:** 不同的防抖延迟对用户体验和API调用次数有什么影响？| How do different debounce delays affect user experience and API call frequency?
   - **学习目标 | Learning Objective:** 理解防抖延迟时间的权衡和实际应用 | Understand trade-offs of debounce delay timing and practical applications

2. **请求取消机制深化 | Request Cancellation Mechanism Deepening**
   - **练习描述 | Exercise Description:** 在搜索组件中添加一个"请求计数器"，显示已取消和已完成的请求数量 | Add a "request counter" to search component showing cancelled and completed request counts
   - **概念检查 | Concept Check:** 为什么快速切换搜索查询时会看到请求被取消？| Why are requests cancelled when rapidly switching search queries?
   - **学习目标 | Learning Objective:** 深入理解AbortController的工作原理 | Deeply understand how AbortController works

3. **缓存策略优化练习 | Cache Strategy Optimization Exercise**
   - **练习描述 | Exercise Description:** 实现带有过期时间（TTL）的搜索缓存，5分钟后自动失效 | Implement search cache with expiration time (TTL), automatically invalidating after 5 minutes
   - **概念检查 | Concept Check:** 为什么需要缓存过期机制？无限缓存会有什么问题？| Why is cache expiration needed? What problems arise from unlimited caching?
   - **学习目标 | Learning Objective:** 掌握高级缓存策略和内存管理 | Master advanced caching strategies and memory management

### 概念应用练习 | Concept Application Exercise

4. **搜索历史记录功能 | Search History Feature**
   - **练习描述 | Exercise Description:** 使用AsyncStorage实现搜索历史记录，保存最近10次搜索，在搜索框下方显示 | Implement search history using AsyncStorage, save last 10 searches, display below search box
   - **概念检查 | Concept Check:** 搜索历史应该在什么时候保存？在什么时候清除？| When should search history be saved? When should it be cleared?
   - **学习目标 | Learning Objective:** 学习将搜索功能与本地存储结合 | Learn to combine search functionality with local storage

5. **搜索建议/自动完成功能 | Search Suggestions/Autocomplete Feature**
   - **练习描述 | Exercise Description:** 实现搜索建议功能，当用户输入时显示相关搜索建议（可以使用模拟数据）| Implement search suggestions showing related suggestions as user types (can use mock data)
   - **概念检查 | Concept Check:** 搜索建议和搜索结果的防抖策略应该相同吗？| Should debounce strategy for search suggestions be the same as search results?
   - **学习目标 | Learning Objective:** 理解多级搜索交互的设计和实现 | Understand design and implementation of multi-level search interactions

6. **分类过滤组合功能 | Category Filter Combination Feature**
   - **练习描述 | Exercise Description:** 允许同时选择多个分类（使用复选框），显示所有选中分类的文章 | Allow selecting multiple categories (using checkboxes), display articles from all selected categories
   - **概念检查 | Concept Check:** 多选分类时如何处理API调用？串行还是并行？| How to handle API calls with multi-select categories? Serial or parallel?
   - **学习目标 | Learning Objective:** 掌握复杂过滤逻辑和并发API请求管理 | Master complex filtering logic and concurrent API request management

### 概念整合练习 | Concept Integration Exercise

7. **离线搜索支持 | Offline Search Support**
   - **练习描述 | Exercise Description:** 使用AsyncStorage缓存文章数据，在离线时允许搜索已缓存的文章 | Use AsyncStorage to cache article data, allow searching cached articles when offline
   - **概念检查 | Concept Check:** 如何检测设备是否离线？离线搜索的性能优化策略是什么？| How to detect if device is offline? What are performance optimization strategies for offline search?
   - **学习目标 | Learning Objective:** 学习离线优先的移动应用设计模式 | Learn offline-first mobile app design patterns

8. **搜索分析与统计 | Search Analytics & Statistics**
   - **练习描述 | Exercise Description:** 实现搜索统计功能，跟踪最常搜索的关键词、平均搜索时间、搜索成功率 | Implement search analytics tracking most searched keywords, average search time, search success rate
   - **概念检查 | Concept Check:** 在哪个时机收集统计数据最合适？| When is the best time to collect analytics data?
   - **学习目标 | Learning Objective:** 理解移动应用中的用户行为分析 | Understand user behavior analysis in mobile apps

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [React Native ScrollView](https://reactnative.dev/docs/scrollview) - 滚动视图组件文档 | ScrollView component documentation
- [React Native TextInput](https://reactnative.dev/docs/textinput) - 文本输入组件文档 | TextInput component documentation
- [React Native FlatList](https://reactnative.dev/docs/flatlist) - 高性能列表组件 | High-performance list component
- [React Navigation - useFocusEffect](https://reactnavigation.org/docs/use-focus-effect/) - 焦点效果Hook | Focus effect hook

### 概念深入理解 | Deep Concept Understanding
- [Debouncing and Throttling Explained](https://css-tricks.com/debouncing-throttling-explained-examples/) - 防抖和节流详解 | Debouncing and throttling explained
- [AbortController API](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) - 请求取消API | Request cancellation API
- [React Performance Optimization](https://react.dev/learn/render-and-commit) - React性能优化 | React performance optimization

### 实践应用指南 | Practical Application Guides
- [Building a Search Experience](https://www.algolia.com/doc/guides/building-search-ui/getting-started/react/) - 构建搜索体验 | Building search experience
- [React Hooks Best Practices](https://react.dev/learn/reusing-logic-with-custom-hooks) - React Hooks最佳实践 | React Hooks best practices
- [Mobile-First Search Design](https://www.nngroup.com/articles/mobile-search/) - 移动优先搜索设计 | Mobile-first search design

### 相关工具和库 | Related Tools and Libraries
- [use-debounce](https://github.com/xnimorz/use-debounce) - 流行的防抖Hook库 | Popular debounce hook library
- [React Query](https://tanstack.com/query/latest) - 强大的数据获取和缓存库 | Powerful data fetching and caching library
- [Algolia](https://www.algolia.com/) - 专业搜索服务 | Professional search service

---

## ✅ 完成检查清单 | Completion Checklist

- [ ] 理解水平ScrollView和垂直ScrollView的区别 | Understand difference between horizontal and vertical ScrollView
- [ ] 能够实现可选择的分类选择器UI组件 | Can implement selectable category selector UI component
- [ ] 掌握防抖（Debouncing）的概念和实现方法 | Master concept and implementation of debouncing
- [ ] 理解TextInput组件的事件处理和受控组件模式 | Understand TextInput event handling and controlled component pattern
- [ ] 能够管理搜索功能的多种状态（加载、错误、空、成功）| Can manage multiple states of search functionality (loading, error, empty, success)
- [ ] 掌握使用AbortController取消网络请求 | Master using AbortController to cancel network requests
- [ ] 理解组件生命周期中的清理（cleanup）逻辑 | Understand cleanup logic in component lifecycle
- [ ] 能够使用useFocusEffect处理导航相关的状态清理 | Can use useFocusEffect to handle navigation-related state cleanup
- [ ] 掌握搜索结果缓存策略提升性能 | Master search result caching strategies to improve performance
- [ ] 理解FlatList性能优化属性的作用 | Understand FlatList performance optimization props
- [ ] 能够使用React.memo和useCallback优化组件渲染 | Can use React.memo and useCallback to optimize component rendering
- [ ] 能够构建完整的生产级搜索功能 | Can build complete production-grade search functionality
- [ ] 理解自定义Hook在代码复用中的作用 | Understand role of custom hooks in code reuse
- [ ] 掌握请求去重和并发控制技术 | Master request deduplication and concurrency control techniques
- [ ] 完成实践项目：新闻搜索与分类浏览器 | Complete practical project: News Search & Category Browser

**概念掌握验证 | Concept Mastery Verification:**

在标记完成前，请确保能够正确回答以下问题：| Before marking as complete, ensure you can correctly answer:

1. 防抖和节流的主要区别是什么？在搜索场景中为什么选择防抖？| What's the main difference between debouncing and throttling? Why choose debouncing for search scenarios?

2. 如果不使用AbortController取消请求，快速切换搜索查询时会出现什么问题？| What problems occur if you don't use AbortController to cancel requests when rapidly switching search queries?

3. 为什么搜索结果缓存需要大小限制？无限缓存会导致什么问题？| Why does search result cache need size limits? What problems does unlimited caching cause?

4. `useFocusEffect`和`useEffect`在导航场景中的主要区别是什么？| What's the main difference between `useFocusEffect` and `useEffect` in navigation scenarios?

5. React.memo如何帮助优化长列表的性能？它与useMemo有什么区别？| How does React.memo help optimize long list performance? What's the difference from useMemo?

能够清晰解释以上概念后，说明你已经真正掌握了本日的核心内容。| If you can clearly explain the above concepts, it indicates you've truly mastered today's core content.

---

**下一步学习 | Next Learning Steps:**

恭喜完成Day 4的学习！明天（Day 5）我们将学习：| Congratulations on completing Day 4! Tomorrow (Day 5) we'll learn:
- 书签功能与本地存储（Bookmarks & AsyncStorage）
- Context API进行全局状态管理
- 手势交互（滑动删除书签）
- 数据持久化最佳实践

今天学到的搜索和分类功能将与明天的书签功能无缝整合，形成完整的新闻阅读体验！| Today's search and category features will seamlessly integrate with tomorrow's bookmark functionality to create a complete news reading experience!
