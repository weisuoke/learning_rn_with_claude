# React Native入门 - 第18天：文章列表与详情界面构建（第3部分）| React Native Introduction - Day 18: Building Article List & Detail Screens (Part 3)

## 实践项目：完整的新闻列表与详情流程 | Practical Project: Complete News List and Detail Flow

### 目标 | Objective
构建一个完整的新闻浏览流程，包括首页文章列表和文章详情页，综合应用ArticleCard组件、类型安全的导航、图片加载状态管理、外部链接打开和响应式排版系统。项目将展示如何将独立的组件和工具函数组合成流畅的用户体验。| Build a complete news browsing flow including home article list and article detail page, comprehensively applying ArticleCard component, type-safe navigation, image loading state management, external link opening, and responsive typography system. The project demonstrates how to combine independent components and utility functions into a smooth user experience.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 表现型组件(ArticleCard)应该包含数据获取逻辑吗？| Should presentational components (ArticleCard) contain data fetching logic?
   **答案 | Answer:** 否，数据应通过props传入，组件只负责UI呈现 | No, data should be passed via props, components are only responsible for UI presentation

2. 导航参数的TypeScript类型定义能防止什么问题？| What problems can TypeScript type definitions for navigation parameters prevent?
   **答案 | Answer:** 防止参数名拼写错误、类型不匹配等运行时错误，提供编译时检查和智能提示 | Prevents parameter name typos and type mismatches at runtime, provides compile-time checking and intellisense

3. Linking.openURL打开的链接会在应用内显示吗？| Will links opened by Linking.openURL display within the app?
   **答案 | Answer:** 不会，会在系统浏览器或外部应用中打开 | No, they open in system browser or external apps

### 步骤 | Steps

#### 1. 创建类型定义文件 | Create Type Definition Files
```typescript
// src/types/article.ts
export interface Article {
  source: {
    id: string | null;
    name: string;
  };
  author: string | null;
  title: string;
  description: string;
  url: string;
  urlToImage: string | null;
  publishedAt: string;
  content: string | null;
}

// src/types/navigation.ts
import { Article } from './article';

export type HomeStackParamList = {
  Home: undefined;
  ArticleDetail: {
    article: Article;
  };
};
```

#### 2. 实现导航结构 | Implement Navigation Structure
```typescript
// src/navigation/HomeStack.tsx
import React from 'react';
import { createStackNavigator } from '@react-navigation/stack';
import { HomeStackParamList } from '../types/navigation';
import { HomeScreen } from '../screens/HomeScreen';
import { ArticleDetailScreen } from '../screens/ArticleDetailScreen';

const Stack = createStackNavigator<HomeStackParamList>();

export const HomeStack: React.FC = () => {
  return (
    <Stack.Navigator
      screenOptions={{
        headerStyle: {
          backgroundColor: '#1DA1F2',
        },
        headerTintColor: '#FFFFFF',
        headerTitleStyle: {
          fontWeight: '600',
        },
      }}
    >
      <Stack.Screen
        name="Home"
        component={HomeScreen}
        options={{
          title: '新闻头条 | Top News',
        }}
      />
      <Stack.Screen
        name="ArticleDetail"
        component={ArticleDetailScreen}
        options={{
          title: '文章详情 | Article Details',
        }}
      />
    </Stack.Navigator>
  );
};
```

#### 3. 实现HomeScreen（使用完整功能）| Implement HomeScreen (with full features)
```typescript
// src/screens/HomeScreen.tsx
import React, { useState, useEffect } from 'react';
import {
  View,
  FlatList,
  StyleSheet,
  ActivityIndicator,
  Text,
  RefreshControl,
} from 'react-native';
import { useNavigation } from '@react-navigation/native';
import { StackNavigationProp } from '@react-navigation/stack';
import { HomeStackParamList } from '../types/navigation';
import { Article } from '../types/article';
import { ArticleCard } from '../components/ArticleCard';
import { getTopHeadlines } from '../services/api';

type HomeScreenNavigationProp = StackNavigationProp<HomeStackParamList, 'Home'>;

export const HomeScreen: React.FC = () => {
  const navigation = useNavigation<HomeScreenNavigationProp>();

  // 状态管理 | State management
  const [articles, setArticles] = useState<Article[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [refreshing, setRefreshing] = useState<boolean>(false);
  const [error, setError] = useState<string | null>(null);

  // 获取文章数据 | Fetch articles data
  const fetchArticles = async (isRefresh: boolean = false) => {
    try {
      if (isRefresh) {
        setRefreshing(true);
      } else {
        setLoading(true);
      }
      setError(null);

      const data = await getTopHeadlines();
      setArticles(data);
    } catch (err) {
      setError('加载失败，请稍后重试 | Load failed, please try again later');
      console.error('Error fetching articles:', err);
    } finally {
      setLoading(false);
      setRefreshing(false);
    }
  };

  // 初次加载 | Initial load
  useEffect(() => {
    fetchArticles();
  }, []);

  // 下拉刷新 | Pull to refresh
  const handleRefresh = () => {
    fetchArticles(true);
  };

  // 文章点击处理 | Article press handler
  const handleArticlePress = (article: Article) => {
    navigation.navigate('ArticleDetail', { article });
  };

  // 渲染列表项 | Render list item
  const renderArticleItem = ({ item }: { item: Article }) => (
    <ArticleCard article={item} onPress={() => handleArticlePress(item)} />
  );

  // 渲染列表分隔符 | Render item separator
  const renderSeparator = () => <View style={styles.separator} />;

  // 渲染列表头部 | Render list header
  const renderHeader = () => (
    <View style={styles.header}>
      <Text style={styles.headerTitle}>今日头条 | Today's Headlines</Text>
      <Text style={styles.headerSubtitle}>
        {articles.length} 篇文章 | {articles.length} articles
      </Text>
    </View>
  );

  // 渲染空状态 | Render empty state
  const renderEmpty = () => (
    <View style={styles.emptyContainer}>
      <Text style={styles.emptyText}>📰</Text>
      <Text style={styles.emptyTitle}>暂无新闻 | No News</Text>
      <Text style={styles.emptySubtitle}>
        下拉刷新获取最新内容 | Pull down to refresh for latest content
      </Text>
    </View>
  );

  // 加载状态 | Loading state
  if (loading && !refreshing) {
    return (
      <View style={styles.centerContainer}>
        <ActivityIndicator size="large" color="#1DA1F2" />
        <Text style={styles.loadingText}>加载中... | Loading...</Text>
      </View>
    );
  }

  // 错误状态 | Error state
  if (error && articles.length === 0) {
    return (
      <View style={styles.centerContainer}>
        <Text style={styles.errorText}>⚠️</Text>
        <Text style={styles.errorTitle}>{error}</Text>
      </View>
    );
  }

  // 文章列表 | Article list
  return (
    <FlatList
      data={articles}
      renderItem={renderArticleItem}
      keyExtractor={(item) => item.url}
      ListHeaderComponent={renderHeader}
      ListEmptyComponent={renderEmpty}
      ItemSeparatorComponent={renderSeparator}
      contentContainerStyle={styles.listContent}
      refreshControl={
        <RefreshControl
          refreshing={refreshing}
          onRefresh={handleRefresh}
          colors={['#1DA1F2']} // Android
          tintColor="#1DA1F2" // iOS
        />
      }
    />
  );
};

const styles = StyleSheet.create({
  centerContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5F8FA',
    padding: 20,
  },
  loadingText: {
    marginTop: 16,
    fontSize: 16,
    color: '#657786',
  },
  errorText: {
    fontSize: 64,
    marginBottom: 16,
  },
  errorTitle: {
    fontSize: 18,
    color: '#14171A',
    textAlign: 'center',
  },
  listContent: {
    backgroundColor: '#F5F8FA',
    paddingBottom: 16,
  },
  header: {
    padding: 16,
    backgroundColor: '#FFFFFF',
    borderBottomWidth: 1,
    borderBottomColor: '#E1E8ED',
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: '700',
    color: '#14171A',
    marginBottom: 4,
  },
  headerSubtitle: {
    fontSize: 14,
    color: '#657786',
  },
  separator: {
    height: 1,
    backgroundColor: '#E1E8ED',
  },
  emptyContainer: {
    alignItems: 'center',
    paddingVertical: 60,
  },
  emptyText: {
    fontSize: 64,
    marginBottom: 16,
  },
  emptyTitle: {
    fontSize: 18,
    fontWeight: '600',
    color: '#14171A',
    marginBottom: 8,
  },
  emptySubtitle: {
    fontSize: 14,
    color: '#657786',
    textAlign: 'center',
  },
});
```

#### 4. 实现API服务层（模拟数据）| Implement API Service Layer (Mock Data)
```typescript
// src/services/api.ts
import { Article } from '../types/article';

// 模拟API延迟 | Simulate API delay
const delay = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms));

// 模拟文章数据 | Mock article data
const MOCK_ARTICLES: Article[] = [
  {
    source: { id: 'techcrunch', name: 'TechCrunch' },
    author: 'Sarah Perez',
    title: 'React Native 0.75 brings major performance improvements',
    description:
      'The latest version of React Native includes significant performance enhancements, better developer experience, and new architecture improvements.',
    url: 'https://example.com/article-1',
    urlToImage: 'https://picsum.photos/400/300?random=1',
    publishedAt: new Date(Date.now() - 2 * 60 * 60 * 1000).toISOString(), // 2小时前
    content:
      'React Native continues to evolve with significant updates that improve app performance and developer productivity. The new version brings...',
  },
  {
    source: { id: 'bbc-news', name: 'BBC News' },
    author: 'Jane Smith',
    title: 'Mobile app development trends in 2024',
    description:
      'Industry experts share insights on the future of mobile development, including AI integration and cross-platform frameworks.',
    url: 'https://example.com/article-2',
    urlToImage: 'https://picsum.photos/400/300?random=2',
    publishedAt: new Date(Date.now() - 5 * 60 * 60 * 1000).toISOString(), // 5小时前
    content:
      'The mobile development landscape is rapidly changing. Developers are increasingly turning to cross-platform solutions...',
  },
  {
    source: { id: 'the-verge', name: 'The Verge' },
    author: 'Tom Warren',
    title: 'Understanding TypeScript in React Native projects',
    description:
      'A comprehensive guide to integrating TypeScript into your React Native workflow for better code quality and maintainability.',
    url: 'https://example.com/article-3',
    urlToImage: null, // 测试无图片情况
    publishedAt: new Date(Date.now() - 24 * 60 * 60 * 1000).toISOString(), // 1天前
    content:
      'TypeScript has become the standard for modern React Native development. Here is why and how to adopt it effectively...',
  },
  {
    source: { id: 'wired', name: 'Wired' },
    author: null, // 测试无作者情况
    title: 'Best practices for mobile UI design',
    description:
      'Learn the fundamental principles of designing intuitive and beautiful mobile interfaces that users love.',
    url: 'https://example.com/article-4',
    urlToImage: 'https://picsum.photos/400/300?random=4',
    publishedAt: new Date(Date.now() - 48 * 60 * 60 * 1000).toISOString(), // 2天前
    content:
      'Mobile UI design requires careful consideration of screen size, touch targets, and user context. These best practices will help...',
  },
];

/**
 * 获取头条新闻 | Get top headlines
 */
export const getTopHeadlines = async (): Promise<Article[]> => {
  await delay(1500); // 模拟网络延迟 | Simulate network delay

  // 模拟10%的错误率 | Simulate 10% error rate
  if (Math.random() < 0.1) {
    throw new Error('Network error');
  }

  return MOCK_ARTICLES;
};

/**
 * 真实API集成示例（需要API密钥）| Real API integration example (requires API key)
 */
// import axios from 'axios';
//
// const API_KEY = 'your_newsapi_key';
// const BASE_URL = 'https://newsapi.org/v2';
//
// export const getTopHeadlines = async (): Promise<Article[]> => {
//   const response = await axios.get(`${BASE_URL}/top-headlines`, {
//     params: {
//       country: 'us',
//       apiKey: API_KEY,
//     },
//   });
//   return response.data.articles;
// };
```

#### 5. 集成所有组件 | Integrate All Components
将前面创建的ArticleCard、ArticleImage、ArticleDetailScreen、Linking工具函数和Typography系统整合到项目中。| Integrate previously created ArticleCard, ArticleImage, ArticleDetailScreen, Linking utilities, and Typography system into the project.

### 项目完成检查 | Project Completion Check

1. ✅ ArticleCard组件是否正确显示文章信息（标题、描述、来源、日期）？| Does ArticleCard component correctly display article info (title, description, source, date)?

2. ✅ 图片加载是否有loading状态和错误处理？| Does image loading have loading state and error handling?

3. ✅ 点击文章卡片是否能正确导航到详情页并传递article数据？| Does clicking article card correctly navigate to detail page with article data?

4. ✅ ArticleDetailScreen是否显示完整文章内容和自定义header按钮？| Does ArticleDetailScreen display full article content and custom header buttons?

5. ✅ "阅读完整文章"按钮是否能在浏览器中打开外部链接？| Does "Read Full Article" button open external link in browser?

6. ✅ 分享按钮是否调用系统分享功能？| Does share button invoke system share functionality?

7. ✅ 下拉刷新功能是否正常工作？| Does pull-to-refresh functionality work properly?

8. ✅ TypeScript类型定义是否提供了智能提示和编译时错误检查？| Do TypeScript type definitions provide intellisense and compile-time error checking?

9. ✅ 排版系统是否建立了清晰的视觉层次？| Does typography system establish clear visual hierarchy?

10. ✅ 平台特定的阴影样式是否在iOS和Android上都正确显示？| Do platform-specific shadow styles display correctly on both iOS and Android?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **组件抽象练习 | Component Abstraction Exercise**
   - **练习描述 | Exercise Description:** 将ArticleDetailScreen中的元信息部分（来源、日期、作者）提取为独立的ArticleMeta组件，使其可以在列表和详情页复用。| Extract the meta information section (source, date, author) from ArticleDetailScreen into an independent ArticleMeta component for reuse in both list and detail pages.
   - **概念检查 | Concept Check:** 表现型组件应该接收什么类型的props？| What types of props should presentational components receive?
   - **学习目标 | Learning Objective:** 加深对组件抽象和复用的理解 | Deepen understanding of component abstraction and reusability

2. **类型安全练习 | Type Safety Exercise**
   - **练习描述 | Exercise Description:** 为API服务层添加完整的TypeScript类型定义，包括API响应类型、错误类型和loading状态类型。创建自定义hook `useArticles`封装数据获取逻辑。| Add complete TypeScript type definitions for API service layer, including API response types, error types, and loading state types. Create custom hook `useArticles` to encapsulate data fetching logic.
   - **概念检查 | Concept Check:** 自定义hook如何提供类型安全？| How do custom hooks provide type safety?
   - **学习目标 | Learning Objective:** 提高TypeScript在实际项目中的应用能力 | Improve TypeScript application skills in real projects

3. **错误处理练习 | Error Handling Exercise**
   - **练习描述 | Exercise Description:** 实现完善的错误边界（Error Boundary）组件，捕获ArticleCard和ArticleDetailScreen中的渲染错误，显示友好的错误信息而不是白屏。| Implement comprehensive Error Boundary component to catch rendering errors in ArticleCard and ArticleDetailScreen, displaying user-friendly error messages instead of blank screens.
   - **概念检查 | Concept Check:** Error Boundary可以捕获哪些类型的错误？| What types of errors can Error Boundary catch?
   - **学习目标 | Learning Objective:** 掌握React错误边界的概念和应用 | Master React Error Boundary concepts and applications

4. **性能优化练习 | Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 使用React.memo优化ArticleCard组件，使用useCallback优化事件处理函数，使用useMemo缓存格式化后的日期字符串，测量优化前后的渲染次数。| Use React.memo to optimize ArticleCard component, useCallback for event handlers, useMemo to cache formatted date strings, and measure render counts before and after optimization.
   - **概念检查 | Concept Check:** React.memo在什么情况下会阻止重新渲染？| Under what conditions does React.memo prevent re-rendering?
   - **学习目标 | Learning Objective:** 理解React性能优化的核心技术 | Understand core techniques for React performance optimization

5. **无障碍访问练习 | Accessibility Exercise**
   - **练习描述 | Exercise Description:** 为ArticleCard和按钮添加accessibility标签（accessibilityLabel, accessibilityHint, accessibilityRole），确保屏幕阅读器用户能够理解界面内容。| Add accessibility labels (accessibilityLabel, accessibilityHint, accessibilityRole) to ArticleCard and buttons to ensure screen reader users can understand interface content.
   - **概念检查 | Concept Check:** accessibilityRole有哪些常用值？| What are common values for accessibilityRole?
   - **学习目标 | Learning Objective:** 培养无障碍设计意识 | Develop awareness of accessibility design

6. **状态管理练习 | State Management Exercise**
   - **练习描述 | Exercise Description:** 创建BookmarkContext使用Context API管理文章收藏状态，在ArticleDetailScreen中实现添加/移除收藏功能，在HomeScreen中显示收藏状态指示器。| Create BookmarkContext using Context API to manage article bookmark state, implement add/remove bookmark functionality in ArticleDetailScreen, display bookmark status indicator in HomeScreen.
   - **概念检查 | Concept Check:** 何时应该使用Context而不是props drilling？| When should Context be used instead of props drilling?
   - **学习目标 | Learning Objective:** 掌握Context API的实际应用场景 | Master practical use cases for Context API

7. **动画增强练习 | Animation Enhancement Exercise**
   - **练习描述 | Exercise Description:** 使用React Native Animated API为ArticleCard的按压添加缩放动画，为ArticleDetailScreen的出现添加淡入动画，提升交互体验。| Use React Native Animated API to add scale animation for ArticleCard press, fade-in animation for ArticleDetailScreen appearance, enhancing interaction experience.
   - **概念检查 | Concept Check:** Animated.timing和Animated.spring有什么区别？| What's the difference between Animated.timing and Animated.spring?
   - **学习目标 | Learning Objective:** 学习React Native动画基础 | Learn React Native animation fundamentals

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [React Navigation - Passing Parameters](https://reactnavigation.org/docs/params/)
- [React Native - Image Component](https://reactnative.dev/docs/image)
- [React Native - Linking API](https://reactnative.dev/docs/linking)
- [React Native - Platform Specific Code](https://reactnative.dev/docs/platform-specific-code)
- [TypeScript for React Native](https://reactnative.dev/docs/typescript)

### 设计资源 | Design Resources
- [Material Design - Typography](https://m3.material.io/styles/typography/overview)
- [iOS Human Interface Guidelines - Typography](https://developer.apple.com/design/human-interface-guidelines/typography)
- [Designing for Mobile - Best Practices](https://www.nngroup.com/articles/mobile-ux/)

### API服务 | API Services
- [NewsAPI.org Documentation](https://newsapi.org/docs/endpoints/top-headlines)
- [New York Times Developer API](https://developer.nytimes.com/)
- [The Guardian Open Platform](https://open-platform.theguardian.com/)

### 工具与库 | Tools and Libraries
- [React Native Vector Icons](https://github.com/oblador/react-native-vector-icons)
- [React Native Paper (Material Design Components)](https://callstack.github.io/react-native-paper/)
- [React Native Reanimated (Advanced Animations)](https://docs.swmansion.com/react-native-reanimated/)

---

## ✅ 完成检查清单 | Completion Checklist

- [ ] **ArticleCard组件理解**：能够创建可复用的卡片组件，正确处理缺失数据（如无图片）| **ArticleCard component understanding**: Can create reusable card components, correctly handle missing data (e.g., no image)

- [ ] **导航参数类型安全**：能够定义ParamList类型，使用泛型为navigation和route提供类型 | **Navigation parameter type safety**: Can define ParamList types, use generics to provide types for navigation and route

- [ ] **图片加载状态管理**：理解如何使用useState追踪loading/error状态，使用Image事件处理加载生命周期 | **Image loading state management**: Understand how to use useState to track loading/error states, use Image events to handle loading lifecycle

- [ ] **平台特定样式**：能够使用Platform.select()为iOS和Android提供不同的阴影实现 | **Platform-specific styles**: Can use Platform.select() to provide different shadow implementations for iOS and Android

- [ ] **Linking API应用**：掌握canOpenURL验证、openURL打开外部链接的完整流程和错误处理 | **Linking API application**: Master complete flow of canOpenURL validation, openURL for opening external links, and error handling

- [ ] **排版系统设计**：能够创建可复用的排版常量，建立清晰的字体大小和行高层次 | **Typography system design**: Can create reusable typography constants, establish clear font size and line height hierarchy

- [ ] **自定义Header配置**：理解useLayoutEffect的使用时机，能够动态配置navigation header | **Custom header configuration**: Understand when to use useLayoutEffect, can dynamically configure navigation header

- [ ] **ScrollView使用**：知道何时使用ScrollView而非View，理解showsVerticalScrollIndicator等属性 | **ScrollView usage**: Know when to use ScrollView instead of View, understand properties like showsVerticalScrollIndicator

- [ ] **FlatList优化**：能够实现pull-to-refresh、empty state、header/footer和item separator | **FlatList optimization**: Can implement pull-to-refresh, empty state, header/footer, and item separator

- [ ] **完整项目集成**：能够将独立组件、工具函数、类型定义组合成完整的功能流程 | **Complete project integration**: Can combine independent components, utility functions, and type definitions into complete functional flow

### 概念掌握验证 | Concept Mastery Verification

在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释以下核心概念：| Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain the following core concepts to others:

1. **表现型组件与容器组件的区别** | **Difference between presentational and container components**
2. **TypeScript泛型在导航类型安全中的作用** | **Role of TypeScript generics in navigation type safety**
3. **React Native中跨平台阴影的实现策略** | **Cross-platform shadow implementation strategy in React Native**
4. **Linking API的使用场景和最佳实践** | **Use cases and best practices for Linking API**
5. **如何设计一致且可维护的排版系统** | **How to design consistent and maintainable typography systems**

### 实践能力验证 | Practical Skills Verification

完成以下任务以验证实践能力：| Complete the following tasks to verify practical skills:

1. ✅ 能够独立创建带图片、文本、元信息的ArticleCard组件
2. ✅ 能够配置类型安全的导航并正确传递复杂对象参数
3. ✅ 能够实现完整的图片加载状态管理（loading/success/error）
4. ✅ 能够使用Linking.openURL打开外部链接并处理错误
5. ✅ 能够创建可复用的排版样式常量和Typography组件
6. ✅ 能够使用Platform.select()实现跨平台一致的视觉效果
7. ✅ 能够配置自定义header按钮并提供交互反馈

---

**恭喜完成Day 18的学习！| Congratulations on completing Day 18!**

今天你构建了一个完整的新闻浏览流程，掌握了可复用组件设计、类型安全导航、图片加载管理、外部链接打开和排版系统等核心技能。这些技能是构建专业级React Native应用的基础。明天我们将继续Day 4的学习，实现分类筛选和搜索功能，进一步提升应用的交互体验。| Today you built a complete news browsing flow, mastering core skills like reusable component design, type-safe navigation, image loading management, external link opening, and typography systems. These skills are fundamental for building professional-grade React Native applications. Tomorrow we'll continue with Day 4, implementing category filtering and search functionality to further enhance app interaction experience.

**记住：优秀的应用不仅功能完整，更要注重细节体验！| Remember: Excellent apps are not just feature-complete, but also focus on detailed experience!**
