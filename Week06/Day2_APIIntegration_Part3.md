# React Native入门 - 第2天：API集成与数据获取（第3部分） | React Native Introduction - Day 2: API Integration & Data Fetching (Part 3)

## 实践项目：新闻阅读器首页 | Practical Project: News Reader Home Screen

### 目标 | Objective
构建一个完整的新闻阅读器首页，集成真实的NewsAPI数据，实现加载状态管理、错误处理和下拉刷新功能。这个项目综合应用了今天学习的所有核心概念：API服务层、自定义Hook、useEffect数据获取和RefreshControl交互。 | Build a complete news reader home screen, integrate real NewsAPI data, implement loading state management, error handling, and pull-to-refresh functionality. This project comprehensively applies all core concepts learned today: API service layer, custom hooks, useEffect data fetching, and RefreshControl interaction.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. API服务层为什么要与UI组件分离？| Why should the API service layer be separated from UI components?
   **答案 | Answer:** 实现关注点分离，提高代码可复用性、可测试性和可维护性 | Achieve separation of concerns, improve code reusability, testability, and maintainability

2. 自定义Hook中的状态在不同组件间是共享的吗？| Is state in custom hooks shared between different components?
   **答案 | Answer:** 否，每个组件调用Hook时都有独立的状态实例 | No, each component calling the hook has an independent state instance

3. 为什么需要同时维护loading和refreshing两个状态？| Why maintain both loading and refreshing states?
   **答案 | Answer:** 区分初始加载和下拉刷新，可以显示不同的UI反馈 | Distinguish initial load from pull-to-refresh, can show different UI feedback

### 步骤 | Steps

#### 步骤1：设置项目结构 | Step 1: Set Up Project Structure
```bash
# 在项目根目录创建以下文件夹和文件 | Create following folders and files in project root
mkdir -p src/services src/hooks src/components src/screens src/utils src/constants
touch src/services/api.js
touch src/hooks/useArticles.js
touch src/utils/errorHandler.js
touch src/components/ArticleCard.js
touch src/components/ArticleList.js
touch src/screens/HomeScreen.js
touch src/constants/colors.js
```

#### 步骤2：配置环境变量 | Step 2: Configure Environment Variables
```bash
# 安装依赖 | Install dependencies
npm install react-native-dotenv

# 创建.env文件 | Create .env file
echo "NEWS_API_KEY=your_api_key_from_newsapi.org" > .env
echo "NEWS_API_BASE_URL=https://newsapi.org/v2" >> .env

# 添加到.gitignore | Add to .gitignore
echo ".env" >> .gitignore
```

#### 步骤3：实现API服务层 | Step 3: Implement API Service Layer
验证是否理解：API服务层应该返回什么？ | Verify understanding: What should API service layer return?
**答案 | Answer:** 返回处理后的数据（如articles数组）或抛出标准化的错误 | Return processed data (like articles array) or throw standardized errors

#### 步骤4：创建自定义Hook | Step 4: Create Custom Hook
验证是否理解：useArticles Hook应该暴露哪些值？ | Verify understanding: What values should useArticles hook expose?
**答案 | Answer:** articles, loading, error, refreshing, refresh(), retry() | articles, loading, error, refreshing, refresh(), retry()

#### 步骤5：构建UI组件 | Step 5: Build UI Components
验证是否理解：ArticleCard应该使用React.memo优化吗？ | Verify understanding: Should ArticleCard use React.memo optimization?
**答案 | Answer:** 是，因为文章卡片会在列表中多次渲染，避免不必要的重新渲染 | Yes, because article cards render multiple times in list, avoid unnecessary re-renders

### 示例代码 | Example Code

```javascript
// ============================================
// /src/constants/colors.js
// 颜色常量定义 | Color constants definition
// ============================================
export const COLORS = {
  primary: '#007AFF',
  secondary: '#5856D6',
  error: '#FF3B30',
  success: '#34C759',
  background: '#F2F2F7',
  cardBackground: '#FFFFFF',
  text: '#000000',
  textSecondary: '#666666',
  textTertiary: '#999999',
  border: '#E5E5EA',
  placeholder: '#C7C7CC',
};

// ============================================
// /src/utils/errorHandler.js
// 错误处理工具 | Error handling utility
// ============================================
export class APIError extends Error {
  constructor(message, status, data) {
    super(message);
    this.name = 'APIError';
    this.status = status;
    this.data = data;
  }
}

export const getErrorMessage = (error) => {
  if (error.status === 0) {
    return {
      title: 'No Internet Connection',
      message: 'Please check your network settings and try again.',
      retryable: true,
    };
  }

  if (error.status === 401) {
    return {
      title: 'Unauthorized',
      message: 'Invalid API key. Please check your configuration.',
      retryable: false,
    };
  }

  if (error.status === 429) {
    return {
      title: 'Rate Limited',
      message: 'Too many requests. Please try again in a few minutes.',
      retryable: true,
    };
  }

  if (error.status >= 500) {
    return {
      title: 'Server Error',
      message: 'Something went wrong on our end. Please try again later.',
      retryable: true,
    };
  }

  return {
    title: 'Error',
    message: error.message || 'An unexpected error occurred.',
    retryable: true,
  };
};

// ============================================
// /src/services/api.js
// API服务层 | API service layer
// ============================================
import { NEWS_API_KEY, NEWS_API_BASE_URL } from '@env';
import { APIError } from '../utils/errorHandler';

const apiRequest = async (endpoint) => {
  const url = `${NEWS_API_BASE_URL}${endpoint}`;

  try {
    const response = await fetch(url, {
      headers: {
        'Content-Type': 'application/json',
      },
    });

    const data = await response.json();

    if (!response.ok) {
      throw new APIError(
        data.message || 'Request failed',
        response.status,
        data
      );
    }

    if (data.status === 'error') {
      throw new APIError(data.message, response.status, data);
    }

    return data;
  } catch (error) {
    if (error instanceof TypeError) {
      throw new APIError(
        'Network error. Please check your connection.',
        0,
        null
      );
    }
    throw error;
  }
};

export const newsAPI = {
  getTopHeadlines: async (country = 'us', page = 1) => {
    const endpoint = `/top-headlines?country=${country}&page=${page}&pageSize=20&apiKey=${NEWS_API_KEY}`;
    const data = await apiRequest(endpoint);
    return data.articles || [];
  },

  getArticlesByCategory: async (category, page = 1) => {
    const endpoint = `/top-headlines?category=${category}&page=${page}&pageSize=20&apiKey=${NEWS_API_KEY}`;
    const data = await apiRequest(endpoint);
    return data.articles || [];
  },

  searchArticles: async (query, page = 1) => {
    if (!query) return [];
    const encodedQuery = encodeURIComponent(query);
    const endpoint = `/everything?q=${encodedQuery}&page=${page}&pageSize=20&sortBy=publishedAt&apiKey=${NEWS_API_KEY}`;
    const data = await apiRequest(endpoint);
    return data.articles || [];
  },
};

// ============================================
// /src/hooks/useArticles.js
// 自定义数据获取Hook | Custom data fetching hook
// ============================================
import { useState, useEffect, useCallback } from 'react';
import { getErrorMessage } from '../utils/errorHandler';

export const useArticles = (fetchFunction, dependencies = []) => {
  const [state, setState] = useState({
    articles: [],
    loading: true,
    error: null,
    refreshing: false,
  });

  const fetchArticles = useCallback(async (isRefreshing = false) => {
    setState(prev => ({
      ...prev,
      loading: !isRefreshing,
      refreshing: isRefreshing,
      error: null,
    }));

    try {
      const data = await fetchFunction();
      setState({
        articles: data,
        loading: false,
        error: null,
        refreshing: false,
      });
    } catch (err) {
      const errorInfo = getErrorMessage(err);
      setState(prev => ({
        ...prev,
        loading: false,
        error: errorInfo,
        refreshing: false,
      }));
      console.error('Fetch articles error:', err);
    }
  }, [fetchFunction]);

  useEffect(() => {
    fetchArticles();
  }, [...dependencies, fetchArticles]);

  const refresh = useCallback(() => {
    fetchArticles(true);
  }, [fetchArticles]);

  const retry = useCallback(() => {
    fetchArticles(false);
  }, [fetchArticles]);

  return {
    ...state,
    refresh,
    retry,
  };
};

// 预定义Hook变体 | Predefined hook variants
export const useTopHeadlines = (country = 'us') => {
  return useArticles(
    () => newsAPI.getTopHeadlines(country),
    [country]
  );
};

// ============================================
// /src/components/ArticleCard.js
// 文章卡片组件 | Article card component
// ============================================
import React, { memo } from 'react';
import { View, Text, Image, TouchableOpacity, StyleSheet } from 'react-native';
import { COLORS } from '../constants/colors';

const ArticleCard = memo(({ article, onPress }) => {
  const imageSource = article.urlToImage
    ? { uri: article.urlToImage }
    : null;

  const formatDate = (dateString) => {
    const date = new Date(dateString);
    return date.toLocaleDateString('en-US', {
      month: 'short',
      day: 'numeric',
      year: 'numeric'
    });
  };

  return (
    <TouchableOpacity
      style={styles.card}
      onPress={onPress}
      activeOpacity={0.8}
    >
      {imageSource && (
        <Image
          source={imageSource}
          style={styles.image}
          resizeMode="cover"
        />
      )}
      <View style={styles.content}>
        <Text style={styles.title} numberOfLines={2}>
          {article.title}
        </Text>
        {article.description && (
          <Text style={styles.description} numberOfLines={3}>
            {article.description}
          </Text>
        )}
        <View style={styles.footer}>
          <Text style={styles.source} numberOfLines={1}>
            {article.source?.name || 'Unknown Source'}
          </Text>
          <Text style={styles.date}>
            {formatDate(article.publishedAt)}
          </Text>
        </View>
      </View>
    </TouchableOpacity>
  );
}, (prevProps, nextProps) => {
  return prevProps.article.url === nextProps.article.url;
});

const styles = StyleSheet.create({
  card: {
    backgroundColor: COLORS.cardBackground,
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
    backgroundColor: COLORS.placeholder,
  },
  content: {
    padding: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: COLORS.text,
    marginBottom: 8,
    lineHeight: 24,
  },
  description: {
    fontSize: 14,
    color: COLORS.textSecondary,
    lineHeight: 20,
    marginBottom: 12,
  },
  footer: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  source: {
    flex: 1,
    fontSize: 12,
    fontWeight: '600',
    color: COLORS.primary,
    marginRight: 8,
  },
  date: {
    fontSize: 12,
    color: COLORS.textTertiary,
  },
});

export default ArticleCard;

// ============================================
// /src/screens/HomeScreen.js
// 主屏幕组件 | Home screen component
// ============================================
import React from 'react';
import {
  View,
  Text,
  StyleSheet,
  ActivityIndicator,
  FlatList,
  RefreshControl,
  TouchableOpacity,
  SafeAreaView,
} from 'react-native';
import { useTopHeadlines } from '../hooks/useArticles';
import ArticleCard from '../components/ArticleCard';
import { COLORS } from '../constants/colors';

const HomeScreen = ({ navigation }) => {
  const { articles, loading, error, refreshing, refresh, retry } = useTopHeadlines('us');

  const handleArticlePress = (article) => {
    // 导航到文章详情页（将在Day 3实现）
    // Navigate to article detail (to be implemented in Day 3)
    console.log('Article pressed:', article.title);
    // navigation.navigate('ArticleDetail', { article });
  };

  // 初始加载状态 | Initial loading state
  if (loading) {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.centerContainer}>
          <ActivityIndicator size="large" color={COLORS.primary} />
          <Text style={styles.loadingText}>Loading latest news...</Text>
        </View>
      </SafeAreaView>
    );
  }

  // 错误状态（无缓存数据） | Error state (no cached data)
  if (error && articles.length === 0) {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.centerContainer}>
          <Text style={styles.errorIcon}>⚠️</Text>
          <Text style={styles.errorTitle}>{error.title}</Text>
          <Text style={styles.errorMessage}>{error.message}</Text>
          {error.retryable && (
            <TouchableOpacity style={styles.retryButton} onPress={retry}>
              <Text style={styles.retryButtonText}>Retry</Text>
            </TouchableOpacity>
          )}
        </View>
      </SafeAreaView>
    );
  }

  // 成功状态（显示文章列表） | Success state (show article list)
  return (
    <SafeAreaView style={styles.safeArea}>
      <View style={styles.header}>
        <Text style={styles.headerTitle}>Top Headlines</Text>
        <Text style={styles.headerSubtitle}>
          {articles.length} articles
        </Text>
      </View>

      <FlatList
        data={articles}
        keyExtractor={(item, index) => item.url || `article-${index}`}
        renderItem={({ item }) => (
          <ArticleCard
            article={item}
            onPress={() => handleArticlePress(item)}
          />
        )}
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={refresh}
            tintColor={COLORS.primary}
            title="Pull to refresh"
            titleColor={COLORS.textSecondary}
            colors={[COLORS.primary, COLORS.secondary]}
            progressBackgroundColor={COLORS.cardBackground}
          />
        }
        ListEmptyComponent={
          <View style={styles.emptyContainer}>
            <Text style={styles.emptyText}>No articles available</Text>
            <Text style={styles.emptySubtext}>Pull down to refresh</Text>
          </View>
        }
        contentContainerStyle={
          articles.length === 0 ? styles.emptyList : styles.list
        }
        showsVerticalScrollIndicator={false}
      />

      {/* 显示错误提示条（有缓存数据时） | Show error banner (when cached data exists) */}
      {error && articles.length > 0 && (
        <View style={styles.errorBanner}>
          <Text style={styles.errorBannerText}>{error.message}</Text>
          {error.retryable && (
            <TouchableOpacity onPress={retry}>
              <Text style={styles.errorBannerRetry}>Retry</Text>
            </TouchableOpacity>
          )}
        </View>
      )}
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: COLORS.background,
  },
  header: {
    backgroundColor: COLORS.cardBackground,
    paddingHorizontal: 16,
    paddingVertical: 16,
    borderBottomWidth: 1,
    borderBottomColor: COLORS.border,
  },
  headerTitle: {
    fontSize: 28,
    fontWeight: 'bold',
    color: COLORS.text,
    marginBottom: 4,
  },
  headerSubtitle: {
    fontSize: 14,
    color: COLORS.textSecondary,
  },
  list: {
    paddingVertical: 8,
  },
  centerContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  loadingText: {
    marginTop: 12,
    fontSize: 16,
    color: COLORS.textSecondary,
  },
  errorIcon: {
    fontSize: 48,
    marginBottom: 16,
  },
  errorTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: COLORS.error,
    marginBottom: 8,
    textAlign: 'center',
  },
  errorMessage: {
    fontSize: 14,
    color: COLORS.textSecondary,
    textAlign: 'center',
    marginBottom: 20,
    paddingHorizontal: 40,
  },
  retryButton: {
    backgroundColor: COLORS.primary,
    paddingHorizontal: 32,
    paddingVertical: 12,
    borderRadius: 8,
  },
  retryButtonText: {
    color: COLORS.cardBackground,
    fontSize: 16,
    fontWeight: '600',
  },
  emptyContainer: {
    alignItems: 'center',
    paddingVertical: 60,
  },
  emptyText: {
    fontSize: 18,
    fontWeight: '600',
    color: COLORS.text,
    marginBottom: 8,
  },
  emptySubtext: {
    fontSize: 14,
    color: COLORS.textSecondary,
  },
  emptyList: {
    flexGrow: 1,
    justifyContent: 'center',
  },
  errorBanner: {
    position: 'absolute',
    bottom: 0,
    left: 0,
    right: 0,
    backgroundColor: COLORS.error,
    paddingHorizontal: 16,
    paddingVertical: 12,
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  errorBannerText: {
    flex: 1,
    color: COLORS.cardBackground,
    fontSize: 14,
  },
  errorBannerRetry: {
    color: COLORS.cardBackground,
    fontSize: 14,
    fontWeight: '600',
    marginLeft: 12,
  },
});

export default HomeScreen;
```

### 项目完成检查 | Project Completion Check
1. ✅ API服务层是否正确分离，所有网络请求都通过api.js？| Is API service layer properly separated, all network requests go through api.js?
2. ✅ useArticles Hook是否正确管理loading、error、refreshing状态？| Does useArticles hook correctly manage loading, error, refreshing states?
3. ✅ 是否正确处理了网络错误、HTTP错误和API错误？| Are network errors, HTTP errors, and API errors properly handled?
4. ✅ 下拉刷新是否正常工作，refreshing状态是否正确更新？| Does pull-to-refresh work correctly, is refreshing state properly updated?
5. ✅ 错误状态是否显示用户友好的消息和重试按钮？| Does error state show user-friendly messages and retry button?
6. ✅ ArticleCard是否使用React.memo优化性能？| Does ArticleCard use React.memo for performance optimization?
7. ✅ 是否正确使用SafeAreaView适配刘海屏？| Is SafeAreaView correctly used for notch adaptation?
8. ✅ 代码是否遵循最佳实践（错误处理、状态管理、性能优化）？| Does code follow best practices (error handling, state management, performance optimization)?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **超时处理增强 | Timeout Handling Enhancement**
   - **练习描述 | Exercise Description:** 在api.js中实现10秒超时机制，使用AbortController
   - **概念检查 | Concept Check:** AbortController如何终止fetch请求？| How does AbortController abort fetch requests?
     **答案 | Answer:** 通过signal传递给fetch，调用abort()后触发AbortError | Pass signal to fetch, calling abort() triggers AbortError
   - **学习目标 | Learning Objective:** 理解请求超时处理和用户体验优化

2. **缓存策略实现 | Cache Strategy Implementation**
   - **练习描述 | Exercise Description:** 在useArticles中添加5分钟缓存，避免频繁请求
   - **概念检查 | Concept Check:** 如何判断缓存是否过期？| How to determine if cache is expired?
     **答案 | Answer:** 记录lastFetchTime，当前时间减去lastFetchTime大于缓存时长则过期 | Record lastFetchTime, if current time minus lastFetchTime exceeds cache duration then expired
   - **学习目标 | Learning Objective:** 掌握客户端缓存策略，减少不必要的网络请求

3. **分页加载 | Pagination Loading**
   - **练习描述 | Exercise Description:** 实现"加载更多"功能，滚动到底部时加载下一页
   - **概念检查 | Concept Check:** FlatList如何检测滚动到底部？| How does FlatList detect scroll to bottom?
     **答案 | Answer:** 使用onEndReached和onEndReachedThreshold props | Use onEndReached and onEndReachedThreshold props
   - **学习目标 | Learning Objective:** 理解无限滚动和分页加载的实现原理

4. **离线支持 | Offline Support**
   - **练习描述 | Exercise Description:** 使用AsyncStorage缓存文章，离线时显示缓存数据
   - **概念检查 | Concept Check:** AsyncStorage存储的数据在哪里？| Where is AsyncStorage data stored?
     **答案 | Answer:** 设备本地存储，应用卸载后会删除 | Device local storage, deleted after app uninstallation
   - **学习目标 | Learning Objective:** 实现基本的离线功能，提升应用可用性

5. **请求重试机制 | Request Retry Mechanism**
   - **练习描述 | Exercise Description:** 实现失败请求自动重试3次，使用指数退避策略
   - **概念检查 | Concept Check:** 什么是指数退避？| What is exponential backoff?
     **答案 | Answer:** 每次重试等待时间呈指数增长（如1s, 2s, 4s），避免服务器过载 | Wait time increases exponentially for each retry (e.g., 1s, 2s, 4s), avoiding server overload
   - **学习目标 | Learning Objective:** 掌握健壮的错误恢复策略

6. **API响应拦截器 | API Response Interceptor**
   - **练习描述 | Exercise Description:** 实现全局响应拦截器，统一处理认证失效
   - **概念检查 | Concept Check:** 拦截器应该在哪一层实现？| At which layer should interceptors be implemented?
     **答案 | Answer:** API服务层，在apiRequest函数中 | API service layer, in apiRequest function
   - **学习目标 | Learning Objective:** 理解中间件模式和横切关注点处理

7. **性能监控 | Performance Monitoring**
   - **练习描述 | Exercise Description:** 记录每个API请求的耗时，在开发环境打印性能日志
   - **概念检查 | Concept Check:** 如何测量异步操作的耗时？| How to measure async operation duration?
     **答案 | Answer:** 在开始时记录Date.now()，完成后计算差值 | Record Date.now() at start, calculate difference on completion
   - **学习目标 | Learning Objective:** 建立性能意识，识别慢速API调用

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [React Native Networking](https://reactnative.dev/docs/network) - React Native网络请求官方指南
- [React Hooks](https://react.dev/reference/react) - React Hooks完整参考
- [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) - MDN Fetch API文档
- [NewsAPI Documentation](https://newsapi.org/docs) - NewsAPI官方文档

### 进阶阅读 | Advanced Reading
- [Error Handling in React](https://react.dev/reference/react/Component#catching-rendering-errors-with-an-error-boundary) - React错误边界
- [Custom Hooks Best Practices](https://react.dev/learn/reusing-logic-with-custom-hooks) - 自定义Hook最佳实践
- [React Performance Optimization](https://react.dev/learn/render-and-commit) - React性能优化指南

### 工具和库 | Tools & Libraries
- [Axios](https://axios-http.com/) - 更强大的HTTP客户端（可选替代fetch）
- [React Query](https://tanstack.com/query) - 强大的数据同步库（高级）
- [SWR](https://swr.vercel.app/) - React数据获取库（高级）

---

## ✅ 完成检查清单 | Completion Checklist

### 概念理解 | Concept Understanding
- [ ] 理解API服务层的作用和设计原则 | Understand the role and design principles of API service layer
- [ ] 掌握fetch API的使用和错误处理 | Master fetch API usage and error handling
- [ ] 能够区分网络错误、HTTP错误和应用错误 | Can distinguish network errors, HTTP errors, and application errors
- [ ] 理解自定义Hook的工作原理和状态隔离 | Understand how custom hooks work and state isolation
- [ ] 掌握useEffect的依赖数组和清理函数 | Master useEffect dependency array and cleanup function
- [ ] 理解异步状态机（loading/success/error） | Understand asynchronous state machine (loading/success/error)

### 实践技能 | Practical Skills
- [ ] 能够创建可复用的API服务层 | Can create reusable API service layer
- [ ] 能够实现自定义数据获取Hook | Can implement custom data fetching hooks
- [ ] 能够正确处理和显示各种错误状态 | Can correctly handle and display various error states
- [ ] 能够实现下拉刷新功能 | Can implement pull-to-refresh functionality
- [ ] 能够使用React.memo优化组件性能 | Can use React.memo to optimize component performance
- [ ] 能够构建完整的数据驱动界面 | Can build complete data-driven interfaces

### 代码质量 | Code Quality
- [ ] 代码遵循关注点分离原则 | Code follows separation of concerns principle
- [ ] 错误处理全面且用户友好 | Error handling is comprehensive and user-friendly
- [ ] 组件复用性高，易于维护 | Components are highly reusable and easy to maintain
- [ ] 状态管理清晰，无不必要的重新渲染 | State management is clear, no unnecessary re-renders

### 项目完成度 | Project Completeness
- [ ] 实践项目正常运行，无崩溃 | Practical project runs normally without crashes
- [ ] 所有功能正常工作（加载、刷新、错误重试） | All features work correctly (loading, refresh, error retry)
- [ ] UI响应流畅，用户体验良好 | UI is responsive, good user experience
- [ ] 代码有适当的注释和文档 | Code has appropriate comments and documentation

### 扩展练习 | Extension Exercises
- [ ] 至少完成3个扩展练习 | Completed at least 3 extension exercises
- [ ] 能够独立实现新功能（如分页、缓存） | Can independently implement new features (like pagination, caching)
- [ ] 理解每个练习背后的概念和最佳实践 | Understand concepts and best practices behind each exercise

---

**概念掌握验证 | Concept Mastery Verification:**

在标记完成前，请确保能够正确回答以下问题：| Before marking as complete, ensure you can correctly answer the following questions:

1. **为什么要创建API服务层而不是直接在组件中调用fetch？** | **Why create an API service layer instead of calling fetch directly in components?**
   - 关注点分离，提高可测试性和可维护性 | Separation of concerns, improve testability and maintainability

2. **自定义Hook useArticles返回的状态中，loading和refreshing有什么区别？** | **In the state returned by custom hook useArticles, what's the difference between loading and refreshing?**
   - loading用于初始加载，refreshing用于下拉刷新，可显示不同UI | loading for initial load, refreshing for pull-to-refresh, can show different UI

3. **为什么要使用useCallback包装fetchArticles函数？** | **Why wrap fetchArticles function with useCallback?**
   - 避免每次渲染创建新函数引用，防止useEffect无限循环 | Avoid creating new function reference on each render, prevent useEffect infinite loop

4. **如何区分网络连接错误和HTTP 404错误？** | **How to distinguish network connection error from HTTP 404 error?**
   - 网络错误抛出TypeError，HTTP错误需检查response.ok和status | Network error throws TypeError, HTTP error needs checking response.ok and status

5. **React.memo的自定义比较函数何时应该返回true？** | **When should React.memo's custom comparison function return true?**
   - 当props相同时返回true，阻止重新渲染 | Return true when props are the same, prevent re-render

如果你能清晰回答以上所有问题，并完成了实践项目和至少3个扩展练习，恭喜你已经掌握了Day 2的核心内容！ | If you can clearly answer all the above questions and have completed the practical project and at least 3 extension exercises, congratulations on mastering Day 2's core content!

**下一步 | Next Steps:**
明天（Day 3），我们将学习如何构建文章详情页和文章列表，深入了解React Native的导航传参、图片加载优化和外部链接处理。| Tomorrow (Day 3), we'll learn how to build article detail and article list screens, diving deep into React Native navigation params, image loading optimization, and external link handling.
