# React Native入门 - 第18天：文章列表与详情界面构建 | React Native Introduction - Day 18: Building Article List & Detail Screens

## 学习目标 | Learning Objectives
- 掌握可复用卡片组件的设计模式和最佳实践 | Master reusable card component design patterns and best practices
- 理解导航参数传递机制及类型安全实现 | Understand navigation parameter passing mechanisms and type-safe implementation
- 学习图片加载状态管理和错误处理策略 | Learn image loading state management and error handling strategies
- 实现响应式布局和自适应UI组件设计 | Implement responsive layouts and adaptive UI component design
- 掌握React Native中的外部链接打开和平台API集成 | Master external link opening and platform API integration in React Native
- 应用阴影、圆角和排版层次创建专业级UI | Apply shadows, rounded corners, and typography hierarchy to create professional-grade UI

## 详细内容 | Detailed Content

### 1. 可复用卡片组件设计 | Reusable Card Component Design (1.5小时 | 1.5 hours)

- **ArticleCard组件架构 | ArticleCard Component Architecture**

  **概念定义 | Concept Definition:**
  ArticleCard是一个可复用的表现型组件(Presentational Component)，负责展示单个新闻文章的核心信息。它遵循单一职责原则，只关注UI呈现，不包含业务逻辑或数据获取逻辑。这种设计使组件高度可测试、可维护，并能在应用的多个位置复用。| ArticleCard is a reusable presentational component responsible for displaying core information of a single news article. It follows the Single Responsibility Principle, focusing only on UI presentation without business logic or data fetching. This design makes the component highly testable, maintainable, and reusable across multiple locations in the application.

  **核心特征 | Key Characteristics:**
  - **Props驱动渲染**：组件完全由传入的props控制，无内部状态管理 | **Props-driven rendering**: Component is fully controlled by incoming props with no internal state management
  - **条件渲染逻辑**：优雅处理缺失数据（如无图片时显示占位符）| **Conditional rendering logic**: Gracefully handles missing data (e.g., showing placeholder when no image)
  - **可交互性**：使用Pressable组件提供触觉反馈和导航触发 | **Interactivity**: Uses Pressable component to provide haptic feedback and navigation triggers
  - **视觉层次**：通过阴影、间距和排版建立清晰的信息优先级 | **Visual hierarchy**: Establishes clear information priority through shadows, spacing, and typography

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 表现型组件应该包含数据获取逻辑吗？| Should presentational components contain data fetching logic?
     **答案 | Answer:** 否 | No - 表现型组件应该是纯粹的UI组件，数据应该通过props传入 | Presentational components should be pure UI components with data passed via props
  2. 当文章没有图片时，ArticleCard应该如何处理？| How should ArticleCard handle articles without images?
     **答案 | Answer:** 显示占位符或默认图片 | Display a placeholder or default image - 使用条件渲染或可选链操作符处理 | Use conditional rendering or optional chaining operator
  3. Pressable组件相比TouchableOpacity有什么优势？| What advantages does Pressable have over TouchableOpacity?
     **答案 | Answer:** Pressable提供更细粒度的按压状态控制、更好的性能和平台一致性 | Pressable provides more granular press state control, better performance, and platform consistency
  4. 卡片组件的阴影在Android和iOS上实现方式相同吗？| Are shadows in card components implemented the same way on Android and iOS?
     **答案 | Answer:** 否 | No - iOS使用shadowColor/shadowOffset/shadowOpacity/shadowRadius，Android使用elevation | iOS uses shadowColor/shadowOffset/shadowOpacity/shadowRadius, Android uses elevation

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/components/ArticleCard.tsx
  import React from 'react';
  import { View, Text, Image, Pressable, StyleSheet, Platform } from 'react-native';

  // 定义文章数据类型 | Define article data type
  interface Article {
    title: string;
    description: string;
    urlToImage?: string; // 可选属性，文章可能没有图片 | Optional property, article may not have image
    source: {
      name: string;
    };
    publishedAt: string;
  }

  // 组件Props接口 | Component Props interface
  interface ArticleCardProps {
    article: Article;
    onPress: () => void; // 点击回调函数 | Click callback function
  }

  export const ArticleCard: React.FC<ArticleCardProps> = ({ article, onPress }) => {
    // 格式化发布日期 | Format publish date
    const formatDate = (dateString: string) => {
      const date = new Date(dateString);
      return date.toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'short',
        day: 'numeric'
      });
    };

    return (
      <Pressable
        style={({ pressed }) => [
          styles.card,
          pressed && styles.cardPressed // 按下时应用额外样式 | Apply additional style when pressed
        ]}
        onPress={onPress}
        android_ripple={{ color: 'rgba(0, 0, 0, 0.1)' }} // Android水波纹效果 | Android ripple effect
      >
        {/* 条件渲染图片：如果有图片则显示，否则显示占位符 | Conditional image rendering */}
        {article.urlToImage ? (
          <Image
            source={{ uri: article.urlToImage }}
            style={styles.image}
            resizeMode="cover" // 图片填充模式 | Image fill mode
          />
        ) : (
          <View style={styles.imagePlaceholder}>
            <Text style={styles.placeholderText}>无图片 | No Image</Text>
          </View>
        )}

        <View style={styles.content}>
          {/* 文章标题 | Article title */}
          <Text style={styles.title} numberOfLines={2}>
            {article.title}
          </Text>

          {/* 文章描述 | Article description */}
          <Text style={styles.description} numberOfLines={3}>
            {article.description}
          </Text>

          {/* 元信息容器 | Meta information container */}
          <View style={styles.meta}>
            <Text style={styles.source}>{article.source.name}</Text>
            <Text style={styles.date}>{formatDate(article.publishedAt)}</Text>
          </View>
        </View>
      </Pressable>
    );
  };

  const styles = StyleSheet.create({
    card: {
      backgroundColor: '#FFFFFF',
      borderRadius: 12, // 圆角 | Rounded corners
      marginHorizontal: 16,
      marginVertical: 8,
      overflow: 'hidden', // 确保子元素不超出圆角边界 | Ensure children don't exceed rounded borders
      // 平台特定的阴影样式 | Platform-specific shadow styles
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.1,
          shadowRadius: 8,
        },
        android: {
          elevation: 4,
        },
      }),
    },
    cardPressed: {
      opacity: 0.7, // 按下时的透明度变化 | Opacity change when pressed
    },
    image: {
      width: '100%',
      height: 200,
    },
    imagePlaceholder: {
      width: '100%',
      height: 200,
      backgroundColor: '#E1E8ED',
      justifyContent: 'center',
      alignItems: 'center',
    },
    placeholderText: {
      color: '#657786',
      fontSize: 14,
    },
    content: {
      padding: 16,
    },
    title: {
      fontSize: 18,
      fontWeight: '700', // 粗体 | Bold
      color: '#14171A',
      marginBottom: 8,
      lineHeight: 24, // 行高改善可读性 | Line height for better readability
    },
    description: {
      fontSize: 14,
      color: '#657786',
      marginBottom: 12,
      lineHeight: 20,
    },
    meta: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
    },
    source: {
      fontSize: 12,
      fontWeight: '600',
      color: '#1DA1F2', // 品牌色 | Brand color
    },
    date: {
      fontSize: 12,
      color: '#AAB8C2',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果article.urlToImage为undefined，这段代码会显示什么？| What will this code display if article.urlToImage is undefined?
    **答案 | Answer:** 会显示一个灰色占位符，中间有"无图片 | No Image"文字 | Will display a grey placeholder with "无图片 | No Image" text in the center
  - numberOfLines={2}在Text组件中的作用是什么？| What does numberOfLines={2} do in the Text component?
    **答案 | Answer:** 限制文本最多显示2行，超出部分用省略号(...) | Limits text to maximum 2 lines, truncating with ellipsis (...) for overflow
  - 为什么card样式要设置overflow: 'hidden'？| Why does the card style need overflow: 'hidden'?
    **答案 | Answer:** 确保子元素（特别是图片）不会超出卡片的圆角边界 | Ensures child elements (especially images) don't exceed the card's rounded border boundaries

  **常见误区检查 | Common Misconception Checks:**
  - 表现型组件可以直接调用API获取数据吗？| Can presentational components directly call APIs to fetch data?
    **答案 | Answer:** 不能 | No - 应该将数据作为props传入，保持组件的纯粹性和可测试性 | Data should be passed as props to maintain component purity and testability
  - Android的elevation和iOS的shadow可以互换使用吗？| Can Android's elevation and iOS's shadow be used interchangeably?
    **答案 | Answer:** 不能 | No - 需要使用Platform.select()为不同平台提供不同的样式实现 | Need to use Platform.select() to provide different style implementations for different platforms

- **图片加载状态管理 | Image Loading State Management**

  **概念定义 | Concept Definition:**
  图片加载是异步操作，需要处理加载中、加载成功、加载失败三种状态。优秀的用户体验要求在每个状态下提供清晰的视觉反馈。React Native的Image组件提供了onLoadStart、onLoad、onError等事件来跟踪加载状态。| Image loading is an asynchronous operation requiring handling of three states: loading, success, and error. Excellent user experience demands clear visual feedback for each state. React Native's Image component provides events like onLoadStart, onLoad, onError to track loading states.

  **核心特征 | Key Characteristics:**
  - **状态追踪**：使用useState管理loading和error状态 | **State tracking**: Use useState to manage loading and error states
  - **优雅降级**：加载失败时显示占位符而非空白或崩溃 | **Graceful degradation**: Show placeholder on load failure instead of blank space or crash
  - **性能优化**：使用ActivityIndicator显示加载动画，避免用户等待焦虑 | **Performance optimization**: Use ActivityIndicator for loading animation to avoid user wait anxiety
  - **缓存策略**：React Native自动缓存网络图片，减少重复加载 | **Cache strategy**: React Native automatically caches network images to reduce redundant loading

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Image组件的onLoad事件在什么时候触发？| When does the Image component's onLoad event trigger?
     **答案 | Answer:** 图片成功加载完成后触发 | Triggers after image successfully loads
  2. 如果网络图片加载失败，应该如何处理？| How should we handle network image load failures?
     **答案 | Answer:** 使用onError事件捕获错误，显示占位符或默认图片 | Use onError event to catch errors and display placeholder or default image
  3. ActivityIndicator组件的作用是什么？| What is the purpose of the ActivityIndicator component?
     **答案 | Answer:** 显示加载旋转动画，告知用户正在处理操作 | Displays loading spinner animation to inform users that an operation is in progress
  4. 图片加载状态管理需要使用哪些React Hook？| Which React Hooks are needed for image loading state management?
     **答案 | Answer:** 主要使用useState来管理loading和error状态 | Primarily use useState to manage loading and error states

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/components/ArticleImage.tsx
  import React, { useState } from 'react';
  import { View, Image, ActivityIndicator, StyleSheet, Text } from 'react-native';

  interface ArticleImageProps {
    uri?: string; // 可选的图片URL | Optional image URL
    style?: object; // 可选的自定义样式 | Optional custom styles
  }

  export const ArticleImage: React.FC<ArticleImageProps> = ({ uri, style }) => {
    // 状态管理：加载中和错误状态 | State management: loading and error states
    const [loading, setLoading] = useState<boolean>(true);
    const [error, setError] = useState<boolean>(false);

    // 如果没有提供URI，直接显示占位符 | If no URI provided, show placeholder directly
    if (!uri) {
      return (
        <View style={[styles.placeholder, style]}>
          <Text style={styles.placeholderText}>📰</Text>
          <Text style={styles.placeholderSubtext}>无图片 | No Image</Text>
        </View>
      );
    }

    // 加载失败时显示错误占位符 | Show error placeholder on load failure
    if (error) {
      return (
        <View style={[styles.placeholder, style]}>
          <Text style={styles.placeholderText}>⚠️</Text>
          <Text style={styles.placeholderSubtext}>加载失败 | Load Failed</Text>
        </View>
      );
    }

    return (
      <View style={style}>
        {/* 图片组件 | Image component */}
        <Image
          source={{ uri }}
          style={[styles.image, style]}
          resizeMode="cover"
          onLoadStart={() => setLoading(true)} // 开始加载 | Start loading
          onLoad={() => setLoading(false)} // 加载成功 | Load success
          onError={() => {
            setLoading(false);
            setError(true); // 加载失败 | Load failure
          }}
        />

        {/* 加载中显示旋转指示器 | Show spinner while loading */}
        {loading && (
          <View style={styles.loadingContainer}>
            <ActivityIndicator size="large" color="#1DA1F2" />
          </View>
        )}
      </View>
    );
  };

  const styles = StyleSheet.create({
    image: {
      width: '100%',
      height: 200,
    },
    placeholder: {
      width: '100%',
      height: 200,
      backgroundColor: '#E1E8ED',
      justifyContent: 'center',
      alignItems: 'center',
    },
    placeholderText: {
      fontSize: 48,
      marginBottom: 8,
    },
    placeholderSubtext: {
      fontSize: 14,
      color: '#657786',
    },
    loadingContainer: {
      ...StyleSheet.absoluteFillObject, // 绝对定位填满父容器 | Absolute positioning to fill parent
      backgroundColor: 'rgba(0, 0, 0, 0.1)',
      justifyContent: 'center',
      alignItems: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - StyleSheet.absoluteFillObject的作用是什么？| What does StyleSheet.absoluteFillObject do?
    **答案 | Answer:** 创建一个绝对定位的样式对象，使元素填满父容器（position: 'absolute', top: 0, left: 0, right: 0, bottom: 0）| Creates an absolutely positioned style object that fills the parent container (position: 'absolute', top: 0, left: 0, right: 0, bottom: 0)
  - 为什么加载指示器要放在半透明背景上？| Why is the loading indicator placed on a semi-transparent background?
    **答案 | Answer:** 提供更好的视觉对比度，确保指示器在任何图片背景下都清晰可见 | Provides better visual contrast, ensuring the indicator is clearly visible against any image background

  **常见误区检查 | Common Misconception Checks:**
  - 图片加载失败后会自动重试吗？| Will image loading automatically retry after failure?
    **答案 | Answer:** 不会 | No - React Native不会自动重试，需要手动实现重试逻辑 | React Native doesn't auto-retry; retry logic needs manual implementation
  - 所有平台的ActivityIndicator颜色都可以自定义吗？| Can ActivityIndicator color be customized on all platforms?
    **答案 | Answer:** 是 | Yes - 但需要注意某些平台可能有默认样式限制 | But note that some platforms may have default style constraints

### 2. 导航参数类型安全 | Navigation Parameter Type Safety (1小时 | 1 hour)

- **TypeScript导航类型定义 | TypeScript Navigation Type Definitions**

  **概念定义 | Concept Definition:**
  React Navigation支持通过TypeScript类型定义为导航参数提供编译时类型检查。这防止了参数名拼写错误、类型不匹配等常见运行时错误，提高代码质量和开发效率。类型定义通过ParamList接口和导航prop的泛型实现。| React Navigation supports compile-time type checking for navigation parameters through TypeScript type definitions. This prevents common runtime errors like parameter name typos and type mismatches, improving code quality and development efficiency. Type definitions are implemented through ParamList interfaces and navigation prop generics.

  **核心特征 | Key Characteristics:**
  - **编译时检查**：IDE在编写代码时就能发现参数错误 | **Compile-time checking**: IDE can detect parameter errors while writing code
  - **自动补全**：IDE提供导航参数的智能提示 | **Auto-completion**: IDE provides intelligent suggestions for navigation parameters
  - **类型推断**：route.params的类型会根据ParamList自动推断 | **Type inference**: route.params types are automatically inferred from ParamList
  - **重构安全**：修改参数结构时，所有使用位置都会得到类型错误提示 | **Refactoring safety**: When modifying parameter structure, all usage locations receive type error warnings

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. ParamList类型定义应该放在哪里？| Where should ParamList type definitions be placed?
     **答案 | Answer:** 通常放在单独的types.ts文件中，或者navigation配置文件顶部 | Usually in a separate types.ts file or at the top of navigation configuration file
  2. 未定义的参数如何在TypeScript中表示？| How are undefined parameters represented in TypeScript?
     **答案 | Answer:** 使用undefined类型，如ArticleDetail: { article: Article } | undefined表示可以不传参数 | Use undefined type, e.g., ArticleDetail: { article: Article } | undefined means parameters are optional
  3. 为什么需要为导航hook提供泛型参数？| Why do navigation hooks need generic parameters?
     **答案 | Answer:** 提供类型安全，确保navigate和route.params的类型正确 | Provides type safety, ensuring correct types for navigate and route.params
  4. 如果导航参数类型不匹配会发生什么？| What happens if navigation parameter types don't match?
     **答案 | Answer:** TypeScript编译器会报错，防止运行时错误 | TypeScript compiler will error, preventing runtime errors

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/types/navigation.ts
  // 定义导航参数列表类型 | Define navigation parameter list types
  import { Article } from './article';

  export type HomeStackParamList = {
    Home: undefined; // Home屏幕不需要参数 | Home screen needs no parameters
    ArticleDetail: {
      article: Article; // ArticleDetail需要article对象 | ArticleDetail requires article object
    };
  };

  // 为useNavigation和useRoute提供类型 | Provide types for useNavigation and useRoute
  import { StackNavigationProp } from '@react-navigation/stack';
  import { RouteProp } from '@react-navigation/native';

  export type HomeScreenNavigationProp = StackNavigationProp<
    HomeStackParamList,
    'Home'
  >;

  export type ArticleDetailRouteProp = RouteProp<
    HomeStackParamList,
    'ArticleDetail'
  >;

  // src/screens/HomeScreen.tsx
  import React from 'react';
  import { FlatList, StyleSheet } from 'react-native';
  import { useNavigation } from '@react-navigation/native';
  import { HomeScreenNavigationProp } from '../types/navigation';
  import { ArticleCard } from '../components/ArticleCard';
  import { Article } from '../types/article';

  export const HomeScreen: React.FC = () => {
    // 使用类型化的navigation hook | Use typed navigation hook
    const navigation = useNavigation<HomeScreenNavigationProp>();

    // 模拟文章数据 | Mock article data
    const articles: Article[] = [
      // ... article data
    ];

    const handleArticlePress = (article: Article) => {
      // TypeScript会检查参数类型 | TypeScript will check parameter types
      navigation.navigate('ArticleDetail', {
        article // ✅ 类型正确 | Type correct
        // article: "wrong type" // ❌ 会报类型错误 | Would cause type error
      });
    };

    return (
      <FlatList
        data={articles}
        keyExtractor={(item) => item.url}
        renderItem={({ item }) => (
          <ArticleCard
            article={item}
            onPress={() => handleArticlePress(item)}
          />
        )}
      />
    );
  };

  // src/screens/ArticleDetailScreen.tsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';
  import { useRoute } from '@react-navigation/native';
  import { ArticleDetailRouteProp } from '../types/navigation';

  export const ArticleDetailScreen: React.FC = () => {
    // 使用类型化的route hook | Use typed route hook
    const route = useRoute<ArticleDetailRouteProp>();

    // TypeScript知道params.article的类型 | TypeScript knows the type of params.article
    const { article } = route.params;

    return (
      <View style={styles.container}>
        <Text style={styles.title}>{article.title}</Text>
        {/* TypeScript会提供article属性的智能提示 | TypeScript provides intellisense for article properties */}
        <Text>{article.description}</Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 16,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 16,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果Home: undefined改为Home: { id: string }，哪些地方需要修改？| If Home: undefined is changed to Home: { id: string }, what needs to be modified?
    **答案 | Answer:** 所有调用navigation.navigate('Home')的地方都需要传入{ id: string }参数，否则TypeScript会报错 | All calls to navigation.navigate('Home') need to pass { id: string } parameter, otherwise TypeScript will error
  - useNavigation<HomeScreenNavigationProp>()中的泛型参数作用是什么？| What is the purpose of the generic parameter in useNavigation<HomeScreenNavigationProp>()?
    **答案 | Answer:** 指定navigation对象的类型，使得navigate方法有正确的类型提示和检查 | Specifies the type of the navigation object, enabling correct type hints and checking for the navigate method

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以不定义ParamList就使用导航？| Can navigation be used without defining ParamList?
    **答案 | Answer:** 可以，但会失去类型安全和智能提示 | Yes, but loses type safety and intellisense
  - route.params总是存在的吗？| Are route.params always present?
    **答案 | Answer:** 不一定 | Not necessarily - 如果ParamList定义为undefined或不传参数，params可能不存在 | If ParamList is defined as undefined or no parameters are passed, params may not exist

### 3. 平台特定样式和阴影实现 | Platform-Specific Styles and Shadow Implementation (45分钟 | 45 minutes)

- **跨平台阴影策略 | Cross-Platform Shadow Strategy**

  **概念定义 | Concept Definition:**
  iOS和Android在阴影渲染上采用不同的实现机制。iOS使用CoreGraphics的阴影属性（shadowColor、shadowOffset、shadowOpacity、shadowRadius），而Android使用Material Design的elevation概念。React Native通过Platform API和条件样式合并实现跨平台一致的视觉效果。| iOS and Android use different rendering mechanisms for shadows. iOS uses CoreGraphics shadow properties (shadowColor, shadowOffset, shadowOpacity, shadowRadius), while Android uses Material Design's elevation concept. React Native achieves consistent cross-platform visual effects through the Platform API and conditional style merging.

  **核心特征 | Key Characteristics:**
  - **Platform.select()**：根据平台返回不同的样式对象 | **Platform.select()**: Returns different style objects based on platform
  - **样式对象扩展**：使用展开运算符合并平台特定样式 | **Style object spreading**: Uses spread operator to merge platform-specific styles
  - **视觉一致性**：虽然实现不同，但要保持视觉效果相似 | **Visual consistency**: Despite different implementations, visual effects should remain similar
  - **性能考虑**：Android的elevation比复杂的阴影计算性能更好 | **Performance considerations**: Android's elevation performs better than complex shadow calculations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. iOS的shadowRadius和Android的elevation是等价的吗？| Are iOS's shadowRadius and Android's elevation equivalent?
     **答案 | Answer:** 不完全等价，但可以调整以达到相似的视觉效果 | Not exactly equivalent, but can be adjusted to achieve similar visual effects
  2. Platform.OS返回什么值？| What values does Platform.OS return?
     **答案 | Answer:** 'ios', 'android', 'windows', 'macos', 'web'等字符串 | Strings like 'ios', 'android', 'windows', 'macos', 'web'
  3. 为什么Android不支持iOS的shadow属性？| Why doesn't Android support iOS's shadow properties?
     **答案 | Answer:** Android使用Material Design的elevation系统，基于不同的渲染引擎 | Android uses Material Design's elevation system, based on a different rendering engine
  4. Platform.select()和条件判断Platform.OS === 'ios'有什么区别？| What's the difference between Platform.select() and conditional Platform.OS === 'ios'?
     **答案 | Answer:** Platform.select()更简洁，返回值可以直接合并到样式对象中 | Platform.select() is more concise, return values can be directly merged into style objects

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/utils/shadows.ts
  // 创建可复用的阴影样式工具函数 | Create reusable shadow style utility function
  import { Platform, StyleSheet } from 'react-native';

  interface ShadowOptions {
    elevation?: number; // Android elevation值 | Android elevation value
    shadowColor?: string; // iOS阴影颜色 | iOS shadow color
    shadowOffset?: { width: number; height: number }; // iOS阴影偏移 | iOS shadow offset
    shadowOpacity?: number; // iOS阴影透明度 | iOS shadow opacity
    shadowRadius?: number; // iOS阴影模糊半径 | iOS shadow blur radius
  }

  export const createShadow = (options: ShadowOptions = {}) => {
    const {
      elevation = 4,
      shadowColor = '#000',
      shadowOffset = { width: 0, height: 2 },
      shadowOpacity = 0.25,
      shadowRadius = 3.84,
    } = options;

    return Platform.select({
      ios: {
        shadowColor,
        shadowOffset,
        shadowOpacity,
        shadowRadius,
      },
      android: {
        elevation,
      },
      default: {}, // Web或其他平台的默认样式 | Default styles for Web or other platforms
    });
  };

  // 使用示例 | Usage example
  const styles = StyleSheet.create({
    card: {
      backgroundColor: '#FFFFFF',
      borderRadius: 12,
      padding: 16,
      marginVertical: 8,
      // 应用跨平台阴影 | Apply cross-platform shadow
      ...createShadow({
        elevation: 5, // Android
        shadowOpacity: 0.1, // iOS
        shadowRadius: 8, // iOS
      }),
    },
    // 预定义的阴影级别 | Predefined shadow levels
    shadowSmall: {
      ...createShadow({ elevation: 2, shadowOpacity: 0.1, shadowRadius: 2 }),
    },
    shadowMedium: {
      ...createShadow({ elevation: 4, shadowOpacity: 0.15, shadowRadius: 4 }),
    },
    shadowLarge: {
      ...createShadow({ elevation: 8, shadowOpacity: 0.2, shadowRadius: 8 }),
    },
  });

  // 组件中使用 | Usage in component
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';
  import { createShadow } from '../utils/shadows';

  export const ShadowCard: React.FC = () => {
    return (
      <View style={cardStyles.container}>
        <Text>带阴影的卡片 | Card with Shadow</Text>
      </View>
    );
  };

  const cardStyles = StyleSheet.create({
    container: {
      backgroundColor: 'white',
      padding: 20,
      borderRadius: 8,
      // 直接使用工具函数 | Use utility function directly
      ...createShadow({
        elevation: 6,
        shadowColor: '#1DA1F2',
        shadowOpacity: 0.3,
        shadowRadius: 10,
      }),
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用展开运算符...来应用阴影样式？| Why use the spread operator ... to apply shadow styles?
    **答案 | Answer:** 将createShadow()返回的样式对象属性合并到当前样式对象中 | Merges properties from createShadow() return object into current style object
  - 如果在Android上设置shadowOpacity会有效果吗？| Will setting shadowOpacity have any effect on Android?
    **答案 | Answer:** 不会，Android会忽略iOS的shadow属性 | No, Android ignores iOS shadow properties

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以在所有平台上使用相同的阴影代码？| Can the same shadow code be used across all platforms?
    **答案 | Answer:** 不建议 | Not recommended - 应该使用Platform.select()为每个平台优化 | Should use Platform.select() to optimize for each platform
  - elevation值越大阴影越明显吗？| Does a higher elevation value always mean more prominent shadow?
    **答案 | Answer:** 是的 | Yes - 但过高的值（>24）在某些设备上可能导致性能问题 | But excessively high values (>24) may cause performance issues on some devices
