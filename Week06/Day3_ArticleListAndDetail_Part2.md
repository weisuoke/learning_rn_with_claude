# React Native入门 - 第18天：文章列表与详情界面构建（第2部分）| React Native Introduction - Day 18: Building Article List & Detail Screens (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. ArticleDetail屏幕实现 | ArticleDetail Screen Implementation (1.5小时 | 1.5 hours)

- **详情屏幕布局架构 | Detail Screen Layout Architecture**

  **概念定义 | Concept Definition:**
  ArticleDetailScreen是一个容器组件（Container Component），负责展示文章的完整信息，包括大图、标题、内容、作者信息和操作按钮。它使用ScrollView作为根容器以支持长内容滚动，并通过自定义header配置实现分享和收藏功能。布局遵循Material Design和iOS Human Interface Guidelines的内容层次原则。| ArticleDetailScreen is a container component responsible for displaying complete article information, including large images, titles, content, author info, and action buttons. It uses ScrollView as the root container to support long content scrolling and implements share/bookmark functionality through custom header configuration. The layout follows content hierarchy principles from Material Design and iOS Human Interface Guidelines.

  **核心特征 | Key Characteristics:**
  - **ScrollView容器**：支持内容超出屏幕时的垂直滚动 | **ScrollView container**: Supports vertical scrolling when content exceeds screen
  - **自定义Header**：使用navigation.setOptions配置右侧操作按钮 | **Custom header**: Uses navigation.setOptions to configure right-side action buttons
  - **富内容展示**：清晰的排版层次、合理的间距和字体大小 | **Rich content display**: Clear typographic hierarchy, appropriate spacing and font sizes
  - **外部链接打开**：使用Linking API打开浏览器查看完整文章 | **External link opening**: Uses Linking API to open browser for full article

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么DetailScreen要使用ScrollView而不是View？| Why should DetailScreen use ScrollView instead of View?
     **答案 | Answer:** 文章内容可能超出屏幕高度，ScrollView允许用户滚动查看完整内容 | Article content may exceed screen height, ScrollView allows users to scroll to view full content
  2. navigation.setOptions应该在哪里调用？| Where should navigation.setOptions be called?
     **答案 | Answer:** 在useEffect或useLayoutEffect中调用，确保在组件渲染后设置 | Call in useEffect or useLayoutEffect to ensure it's set after component renders
  3. Linking.openURL会在应用内打开链接吗？| Will Linking.openURL open the link within the app?
     **答案 | Answer:** 不会，它会调用系统默认浏览器或应用外部打开链接 | No, it calls the system default browser or opens the link externally
  4. 详情屏幕的header配置会影响其他屏幕吗？| Will the detail screen's header configuration affect other screens?
     **答案 | Answer:** 不会，每个屏幕的header配置是独立的 | No, each screen's header configuration is independent

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/screens/ArticleDetailScreen.tsx
  import React, { useLayoutEffect } from 'react';
  import {
    View,
    Text,
    ScrollView,
    StyleSheet,
    Pressable,
    Share,
    Linking,
    Alert,
  } from 'react-native';
  import { useRoute, useNavigation } from '@react-navigation/native';
  import { ArticleDetailRouteProp } from '../types/navigation';
  import { ArticleImage } from '../components/ArticleImage';
  import { Ionicons } from '@expo/vector-icons';

  export const ArticleDetailScreen: React.FC = () => {
    const route = useRoute<ArticleDetailRouteProp>();
    const navigation = useNavigation();
    const { article } = route.params;

    // 配置header右侧按钮 | Configure header right buttons
    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <View style={styles.headerButtons}>
            {/* 分享按钮 | Share button */}
            <Pressable
              style={styles.headerButton}
              onPress={handleShare}
              android_ripple={{ color: 'rgba(0,0,0,0.1)', borderless: true }}
            >
              <Ionicons name="share-outline" size={24} color="#1DA1F2" />
            </Pressable>

            {/* 收藏按钮 | Bookmark button */}
            <Pressable
              style={styles.headerButton}
              onPress={handleBookmark}
              android_ripple={{ color: 'rgba(0,0,0,0.1)', borderless: true }}
            >
              <Ionicons
                name="bookmark-outline" // 未收藏状态 | Not bookmarked state
                // name="bookmark" // 已收藏状态 | Bookmarked state
                size={24}
                color="#1DA1F2"
              />
            </Pressable>
          </View>
        ),
      });
    }, [navigation]); // 依赖项确保navigation变化时重新设置 | Dependencies ensure reset when navigation changes

    // 分享文章 | Share article
    const handleShare = async () => {
      try {
        await Share.share({
          message: `${article.title}\n\n${article.url}`, // 分享标题和URL | Share title and URL
          url: article.url, // iOS会特殊处理URL | iOS handles URL specially
          title: article.title, // Android对话框标题 | Android dialog title
        });
      } catch (error) {
        Alert.alert('分享失败 | Share Failed', '无法分享此文章 | Unable to share this article');
      }
    };

    // 收藏文章（后续实现具体逻辑）| Bookmark article (implement specific logic later)
    const handleBookmark = () => {
      Alert.alert('收藏成功 | Bookmarked', '文章已添加到收藏 | Article added to bookmarks');
    };

    // 打开完整文章链接 | Open full article link
    const handleOpenFullArticle = async () => {
      try {
        // 检查URL是否可以打开 | Check if URL can be opened
        const supported = await Linking.canOpenURL(article.url);
        if (supported) {
          await Linking.openURL(article.url);
        } else {
          Alert.alert('无法打开链接 | Cannot Open Link', '此链接不受支持 | This link is not supported');
        }
      } catch (error) {
        Alert.alert('打开失败 | Open Failed', '无法打开文章链接 | Unable to open article link');
      }
    };

    // 格式化发布时间 | Format publish time
    const formatPublishDate = (dateString: string) => {
      const date = new Date(dateString);
      const now = new Date();
      const diffInHours = Math.floor((now.getTime() - date.getTime()) / (1000 * 60 * 60));

      if (diffInHours < 1) return '刚刚 | Just now';
      if (diffInHours < 24) return `${diffInHours}小时前 | ${diffInHours} hours ago`;
      if (diffInHours < 48) return '昨天 | Yesterday';

      return date.toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
      });
    };

    return (
      <ScrollView
        style={styles.container}
        showsVerticalScrollIndicator={false} // 隐藏滚动条 | Hide scrollbar
      >
        {/* 文章封面图 | Article cover image */}
        <ArticleImage uri={article.urlToImage} style={styles.coverImage} />

        <View style={styles.contentContainer}>
          {/* 文章标题 | Article title */}
          <Text style={styles.title}>{article.title}</Text>

          {/* 文章元信息 | Article meta information */}
          <View style={styles.metaContainer}>
            <View style={styles.sourceContainer}>
              <Ionicons name="newspaper-outline" size={16} color="#657786" />
              <Text style={styles.sourceName}>{article.source.name}</Text>
            </View>

            <View style={styles.dateContainer}>
              <Ionicons name="time-outline" size={16} color="#657786" />
              <Text style={styles.publishDate}>
                {formatPublishDate(article.publishedAt)}
              </Text>
            </View>
          </View>

          {/* 作者信息 | Author information */}
          {article.author && (
            <View style={styles.authorContainer}>
              <Ionicons name="person-outline" size={16} color="#657786" />
              <Text style={styles.authorName}>{article.author}</Text>
            </View>
          )}

          {/* 文章描述/摘要 | Article description/summary */}
          <Text style={styles.description}>{article.description}</Text>

          {/* 文章内容 | Article content */}
          {article.content && (
            <Text style={styles.content}>
              {/* NewsAPI的content通常被截断，添加提示 | NewsAPI content is usually truncated, add hint */}
              {article.content.replace(/\[\+\d+ chars\]$/, '...')}
            </Text>
          )}

          {/* 阅读完整文章按钮 | Read full article button */}
          <Pressable
            style={({ pressed }) => [
              styles.readMoreButton,
              pressed && styles.readMoreButtonPressed,
            ]}
            onPress={handleOpenFullArticle}
          >
            <Text style={styles.readMoreText}>阅读完整文章 | Read Full Article</Text>
            <Ionicons name="arrow-forward" size={20} color="#FFFFFF" />
          </Pressable>

          {/* 底部安全区域 | Bottom safe area */}
          <View style={styles.bottomSpacer} />
        </View>
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#FFFFFF',
    },
    headerButtons: {
      flexDirection: 'row',
      marginRight: 8, // 右侧边距 | Right margin
    },
    headerButton: {
      padding: 8,
      marginLeft: 8, // 按钮间距 | Button spacing
    },
    coverImage: {
      width: '100%',
      height: 250, // 更大的封面图 | Larger cover image
    },
    contentContainer: {
      padding: 16,
    },
    title: {
      fontSize: 24,
      fontWeight: '700',
      color: '#14171A',
      lineHeight: 32,
      marginBottom: 16,
    },
    metaContainer: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      marginBottom: 12,
      paddingBottom: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#E1E8ED',
    },
    sourceContainer: {
      flexDirection: 'row',
      alignItems: 'center',
    },
    sourceName: {
      fontSize: 14,
      fontWeight: '600',
      color: '#1DA1F2',
      marginLeft: 6,
    },
    dateContainer: {
      flexDirection: 'row',
      alignItems: 'center',
    },
    publishDate: {
      fontSize: 14,
      color: '#657786',
      marginLeft: 6,
    },
    authorContainer: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: 16,
    },
    authorName: {
      fontSize: 14,
      color: '#14171A',
      marginLeft: 6,
    },
    description: {
      fontSize: 16,
      color: '#14171A',
      lineHeight: 24,
      marginBottom: 16,
      fontWeight: '500', // 稍微加粗以突出摘要 | Slightly bold to emphasize summary
    },
    content: {
      fontSize: 16,
      color: '#14171A',
      lineHeight: 26, // 更大的行高提升可读性 | Larger line height for better readability
      marginBottom: 24,
    },
    readMoreButton: {
      backgroundColor: '#1DA1F2',
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'center',
      padding: 16,
      borderRadius: 8,
      marginTop: 8,
    },
    readMoreButtonPressed: {
      backgroundColor: '#1A8CD8', // 按下时的深色 | Darker color when pressed
    },
    readMoreText: {
      fontSize: 16,
      fontWeight: '600',
      color: '#FFFFFF',
      marginRight: 8,
    },
    bottomSpacer: {
      height: 32, // 底部留白，避免内容贴底 | Bottom spacing to avoid content touching bottom
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Share.share()方法在iOS和Android上的行为有什么不同？| How does Share.share() behave differently on iOS and Android?
    **答案 | Answer:** iOS会显示系统分享菜单（可分享到多个应用），Android显示标准分享对话框，两者UI不同但功能相似 | iOS shows system share sheet (can share to multiple apps), Android shows standard share dialog, different UIs but similar functionality
  - Linking.canOpenURL的作用是什么？| What is the purpose of Linking.canOpenURL?
    **答案 | Answer:** 检查设备是否能够打开指定URL，避免打开失败导致的错误 | Checks if device can open the specified URL, avoiding errors from failed opening attempts
  - useLayoutEffect和useEffect在这里使用有什么区别？| What's the difference between using useLayoutEffect and useEffect here?
    **答案 | Answer:** useLayoutEffect在DOM更新后、浏览器绘制前同步执行，确保header在首次渲染时就正确显示，避免闪烁 | useLayoutEffect executes synchronously after DOM updates but before browser paint, ensuring header displays correctly on first render, avoiding flicker

  **常见误区检查 | Common Misconception Checks:**
  - Linking.openURL会在应用内的WebView中打开链接吗？| Will Linking.openURL open links in an in-app WebView?
    **答案 | Answer:** 不会，它会使用系统浏览器或外部应用打开。如需应用内WebView，需使用react-native-webview | No, it uses system browser or external app. For in-app WebView, need to use react-native-webview
  - 是否可以在ScrollView内嵌套另一个ScrollView？| Can you nest another ScrollView inside a ScrollView?
    **答案 | Answer:** 技术上可以，但会导致滚动冲突和性能问题，应避免或使用nestedScrollEnabled属性 | Technically yes, but causes scroll conflicts and performance issues; should avoid or use nestedScrollEnabled property

- **自定义Header按钮交互 | Custom Header Button Interactions**

  **概念定义 | Concept Definition:**
  React Navigation允许通过screenOptions和navigation.setOptions动态配置屏幕header。自定义header按钮可以提供分享、收藏、搜索等快捷操作。按钮交互应包含视觉反馈（如按压效果）和功能反馈（如成功提示）以提升用户体验。| React Navigation allows dynamic screen header configuration through screenOptions and navigation.setOptions. Custom header buttons can provide quick actions like share, bookmark, and search. Button interactions should include visual feedback (like press effects) and functional feedback (like success notifications) to enhance user experience.

  **核心特征 | Key Characteristics:**
  - **动态配置**：使用useLayoutEffect在组件挂载时设置header | **Dynamic configuration**: Use useLayoutEffect to set header on component mount
  - **图标按钮**：使用@expo/vector-icons提供一致的图标视觉 | **Icon buttons**: Use @expo/vector-icons for consistent icon visuals
  - **状态反馈**：通过图标变化（如outline vs filled）显示状态 | **State feedback**: Show state through icon changes (e.g., outline vs filled)
  - **交互反馈**：使用android_ripple和Pressable的pressed状态 | **Interaction feedback**: Use android_ripple and Pressable's pressed state

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. navigation.setOptions在每次重新渲染时都会调用吗？| Does navigation.setOptions get called on every re-render?
     **答案 | Answer:** 如果在useLayoutEffect中且依赖项未变化，不会每次都调用 | If in useLayoutEffect with unchanged dependencies, it won't be called every time
  2. headerRight返回的组件可以访问屏幕的state吗？| Can the component returned by headerRight access the screen's state?
     **答案 | Answer:** 可以，因为它是在组件内部定义的闭包函数 | Yes, because it's a closure function defined inside the component
  3. Ionicons的name属性如何影响图标显示？| How does Ionicons' name property affect icon display?
     **答案 | Answer:** name指定要显示的图标，"-outline"后缀表示轮廓版本，无后缀为填充版本 | name specifies which icon to display, "-outline" suffix indicates outline version, no suffix for filled version
  4. android_ripple效果在iOS上会显示吗？| Will android_ripple effect show on iOS?
     **答案 | Answer:** 不会，android_ripple仅在Android上生效，iOS需使用Pressable的style函数处理 | No, android_ripple only works on Android; iOS needs Pressable's style function

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/components/HeaderBookmarkButton.tsx
  // 可复用的收藏按钮组件 | Reusable bookmark button component
  import React from 'react';
  import { Pressable, StyleSheet, Platform } from 'react-native';
  import { Ionicons } from '@expo/vector-icons';

  interface HeaderBookmarkButtonProps {
    isBookmarked: boolean; // 是否已收藏 | Whether bookmarked
    onPress: () => void; // 点击回调 | Click callback
    color?: string; // 图标颜色 | Icon color
  }

  export const HeaderBookmarkButton: React.FC<HeaderBookmarkButtonProps> = ({
    isBookmarked,
    onPress,
    color = '#1DA1F2',
  }) => {
    return (
      <Pressable
        style={({ pressed }) => [
          styles.button,
          pressed && Platform.OS === 'ios' && styles.buttonPressed, // iOS按压效果 | iOS press effect
        ]}
        onPress={onPress}
        android_ripple={{
          color: 'rgba(0,0,0,0.1)',
          borderless: true, // 圆形波纹 | Circular ripple
          radius: 20, // 波纹半径 | Ripple radius
        }}
        hitSlop={{ top: 10, bottom: 10, left: 10, right: 10 }} // 扩大点击区域 | Expand touch area
      >
        <Ionicons
          name={isBookmarked ? 'bookmark' : 'bookmark-outline'} // 动态图标 | Dynamic icon
          size={24}
          color={color}
        />
      </Pressable>
    );
  };

  const styles = StyleSheet.create({
    button: {
      padding: 8,
      marginLeft: 8,
    },
    buttonPressed: {
      opacity: 0.6, // iOS按压透明度 | iOS press opacity
    },
  });

  // 在ArticleDetailScreen中使用 | Usage in ArticleDetailScreen
  import React, { useState, useLayoutEffect } from 'react';
  import { useNavigation } from '@react-navigation/native';
  import { HeaderBookmarkButton } from '../components/HeaderBookmarkButton';

  export const ArticleDetailScreen: React.FC = () => {
    const navigation = useNavigation();
    const [isBookmarked, setIsBookmarked] = useState(false); // 收藏状态 | Bookmark state

    useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <HeaderBookmarkButton
            isBookmarked={isBookmarked}
            onPress={() => setIsBookmarked(!isBookmarked)} // 切换收藏状态 | Toggle bookmark state
          />
        ),
      });
    }, [navigation, isBookmarked]); // isBookmarked变化时更新header | Update header when isBookmarked changes

    // ... rest of the component
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - hitSlop属性的作用是什么？| What is the purpose of the hitSlop property?
    **答案 | Answer:** 扩大组件的可点击区域，使小图标更容易被点击，提升用户体验 | Expands the touchable area of the component, making small icons easier to tap, improving user experience
  - 为什么useLayoutEffect的依赖项要包含isBookmarked？| Why does useLayoutEffect's dependency array need to include isBookmarked?
    **答案 | Answer:** 确保isBookmarked变化时重新设置header，使图标状态与实际状态同步 | Ensures header is reset when isBookmarked changes, keeping icon state synchronized with actual state

  **常见误区检查 | Common Misconception Checks:**
  - headerRight可以返回多个组件吗？| Can headerRight return multiple components?
    **答案 | Answer:** 可以，但需要包裹在一个父容器（如View）中 | Yes, but they need to be wrapped in a parent container (like View)
  - 是否可以在header按钮中使用异步操作？| Can asynchronous operations be used in header buttons?
    **答案 | Answer:** 可以，但要注意处理加载状态和错误情况 | Yes, but need to handle loading states and error cases

### 5. Linking API深入应用 | Advanced Linking API Application (45分钟 | 45 minutes)

- **外部URL打开与深度链接 | External URL Opening and Deep Linking**

  **概念定义 | Concept Definition:**
  Linking API是React Native提供的跨平台模块，用于处理应用与外部世界的交互，包括打开网页、拨打电话、发送邮件和处理深度链接。在新闻应用中，主要用于打开文章的完整网页版本。正确使用Linking需要错误处理、URL验证和用户权限检查。| The Linking API is a cross-platform module provided by React Native for handling app interactions with the external world, including opening webpages, making calls, sending emails, and handling deep links. In news apps, it's primarily used to open full web versions of articles. Proper Linking usage requires error handling, URL validation, and user permission checks.

  **核心特征 | Key Characteristics:**
  - **canOpenURL验证**：检查URL格式和设备能力 | **canOpenURL validation**: Checks URL format and device capabilities
  - **异步操作**：openURL返回Promise，需要async/await处理 | **Asynchronous operation**: openURL returns Promise, requires async/await handling
  - **错误捕获**：使用try-catch处理打开失败情况 | **Error catching**: Use try-catch to handle opening failures
  - **协议支持**：支持http、https、mailto、tel等多种URL scheme | **Protocol support**: Supports various URL schemes like http, https, mailto, tel

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Linking.openURL是同步还是异步方法？| Is Linking.openURL synchronous or asynchronous?
     **答案 | Answer:** 异步方法，返回Promise | Asynchronous method, returns Promise
  2. 如果URL格式错误，canOpenURL会返回什么？| What does canOpenURL return if URL format is wrong?
     **答案 | Answer:** 返回false或抛出错误，取决于错误类型 | Returns false or throws error, depending on error type
  3. Linking.openURL在Android和iOS上的行为完全一致吗？| Does Linking.openURL behave identically on Android and iOS?
     **答案 | Answer:** 基本一致，但某些URL scheme可能需要平台特定配置 | Mostly consistent, but some URL schemes may require platform-specific configuration
  4. 打开外部链接时，用户会离开应用吗？| Will users leave the app when opening external links?
     **答案 | Answer:** 是的，会切换到系统浏览器或其他应用 | Yes, will switch to system browser or other apps

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/utils/linking.ts
  // 封装Linking API的工具函数 | Utility functions wrapping Linking API
  import { Linking, Alert, Platform } from 'react-native';

  /**
   * 安全地打开URL | Safely open URL
   * @param url - 要打开的URL | URL to open
   * @param errorTitle - 错误提示标题 | Error alert title
   * @returns Promise<boolean> - 是否成功打开 | Whether successfully opened
   */
  export const openURL = async (
    url: string,
    errorTitle: string = '打开失败 | Open Failed'
  ): Promise<boolean> => {
    try {
      // 验证URL格式 | Validate URL format
      if (!url || typeof url !== 'string') {
        throw new Error('Invalid URL');
      }

      // 检查是否可以打开 | Check if can be opened
      const supported = await Linking.canOpenURL(url);

      if (!supported) {
        Alert.alert(
          errorTitle,
          `无法打开此链接: ${url}\n\nUnable to open this link: ${url}`
        );
        return false;
      }

      // 打开URL | Open URL
      await Linking.openURL(url);
      return true;
    } catch (error) {
      console.error('Error opening URL:', error);
      Alert.alert(
        errorTitle,
        '打开链接时出错，请稍后重试\n\nError opening link, please try again later'
      );
      return false;
    }
  };

  /**
   * 打开电话拨号 | Open phone dialer
   */
  export const openPhoneDialer = async (phoneNumber: string): Promise<boolean> => {
    const url = `tel:${phoneNumber}`;
    return openURL(url, '无法拨打电话 | Cannot Make Call');
  };

  /**
   * 打开邮件客户端 | Open email client
   */
  export const openEmail = async (
    email: string,
    subject?: string,
    body?: string
  ): Promise<boolean> => {
    let url = `mailto:${email}`;

    const params: string[] = [];
    if (subject) params.push(`subject=${encodeURIComponent(subject)}`);
    if (body) params.push(`body=${encodeURIComponent(body)}`);

    if (params.length > 0) {
      url += `?${params.join('&')}`;
    }

    return openURL(url, '无法打开邮件 | Cannot Open Email');
  };

  /**
   * 在地图应用中打开位置 | Open location in maps app
   */
  export const openMaps = async (
    latitude: number,
    longitude: number,
    label?: string
  ): Promise<boolean> => {
    const scheme = Platform.select({
      ios: 'maps', // iOS使用Apple Maps | iOS uses Apple Maps
      android: 'geo', // Android使用Google Maps | Android uses Google Maps
    });

    let url: string;
    if (Platform.OS === 'ios') {
      url = `${scheme}:0,0?q=${latitude},${longitude}`;
      if (label) url += `(${encodeURIComponent(label)})`;
    } else {
      url = `${scheme}:${latitude},${longitude}?q=${latitude},${longitude}`;
      if (label) url += `(${encodeURIComponent(label)})`;
    }

    return openURL(url, '无法打开地图 | Cannot Open Maps');
  };

  /**
   * 获取初始URL（用于深度链接）| Get initial URL (for deep linking)
   */
  export const getInitialURL = async (): Promise<string | null> => {
    try {
      const url = await Linking.getInitialURL();
      return url;
    } catch (error) {
      console.error('Error getting initial URL:', error);
      return null;
    }
  };

  /**
   * 监听URL变化（用于处理应用运行时的深度链接）| Listen for URL changes (for handling deep links while app is running)
   */
  export const addURLListener = (
    callback: (event: { url: string }) => void
  ): (() => void) => {
    const subscription = Linking.addEventListener('url', callback);
    return () => subscription.remove(); // 返回取消订阅函数 | Return unsubscribe function
  };

  // 使用示例 | Usage example
  import React from 'react';
  import { View, Pressable, Text } from 'react-native';
  import { openURL, openEmail } from '../utils/linking';

  export const ContactInfo: React.FC = () => {
    const handleContactPress = async () => {
      await openEmail(
        'support@newsapp.com',
        'App Feedback', // 邮件主题 | Email subject
        'I would like to provide feedback...' // 邮件正文 | Email body
      );
    };

    return (
      <Pressable onPress={handleContactPress}>
        <Text>联系我们 | Contact Us</Text>
      </Pressable>
    );
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - encodeURIComponent的作用是什么？| What is the purpose of encodeURIComponent?
    **答案 | Answer:** 将字符串编码为URL安全格式，处理特殊字符如空格、中文等 | Encodes strings to URL-safe format, handling special characters like spaces, Chinese characters, etc.
  - Linking.addEventListener返回什么？| What does Linking.addEventListener return?
    **答案 | Answer:** 返回一个订阅对象，包含remove方法用于取消监听 | Returns a subscription object with a remove method for canceling the listener
  - 为什么打开地图的URL在iOS和Android上不同？| Why are the URLs for opening maps different on iOS and Android?
    **答案 | Answer:** iOS使用Apple Maps的URL scheme (maps:)，Android使用Google Maps的URL scheme (geo:) | iOS uses Apple Maps URL scheme (maps:), Android uses Google Maps URL scheme (geo:)

  **常见误区检查 | Common Misconception Checks:**
  - Linking.canOpenURL可以检测设备上是否安装了特定应用吗？| Can Linking.canOpenURL detect if a specific app is installed on the device?
    **答案 | Answer:** 部分可以，但iOS 9+需要在Info.plist中声明要查询的URL scheme | Partially yes, but iOS 9+ requires declaring queried URL schemes in Info.plist
  - 打开外部链接后，应用会被关闭吗？| Will the app be closed after opening an external link?
    **答案 | Answer:** 不会，应用会进入后台，用户可以通过任务切换器返回 | No, the app goes to background, users can return via task switcher

### 6. 响应式排版与视觉层次 | Responsive Typography and Visual Hierarchy (30分钟 | 30 minutes)

- **排版系统设计 | Typography System Design**

  **概念定义 | Concept Definition:**
  排版系统(Typography System)定义了应用中所有文本的样式规范，包括字体大小、行高、字重、颜色和间距。良好的排版系统能够建立清晰的视觉层次，引导用户注意力，提升内容可读性。在React Native中，通过创建可复用的文本样式常量和组件来实现一致的排版。| A typography system defines style specifications for all text in an application, including font sizes, line heights, font weights, colors, and spacing. A good typography system establishes clear visual hierarchy, guides user attention, and improves content readability. In React Native, this is implemented through reusable text style constants and components.

  **核心特征 | Key Characteristics:**
  - **字体缩放比例**：使用1.25倍或1.333倍的比例创建大小层次 | **Font scale ratio**: Use 1.25x or 1.333x ratio to create size hierarchy
  - **行高计算**：通常为字体大小的1.4-1.6倍 | **Line height calculation**: Typically 1.4-1.6 times font size
  - **语义化命名**：如heading1, body, caption等 | **Semantic naming**: Like heading1, body, caption
  - **可访问性**：支持系统字体缩放(allowFontScaling) | **Accessibility**: Supports system font scaling (allowFontScaling)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 行高(lineHeight)对文本可读性有什么影响？| How does line height affect text readability?
     **答案 | Answer:** 适当的行高增加行间空白，减少视觉拥挤，提升阅读舒适度 | Appropriate line height increases inter-line white space, reduces visual crowding, enhances reading comfort
  2. fontWeight的值范围是什么？| What is the value range for fontWeight?
     **答案 | Answer:** '100'到'900'（字符串）或'normal'、'bold'等关键字 | '100' to '900' (strings) or keywords like 'normal', 'bold'
  3. allowFontScaling默认值是什么？| What is the default value of allowFontScaling?
     **答案 | Answer:** true，允许根据系统设置缩放字体 | true, allows font scaling based on system settings
  4. 为什么标题和正文应该使用不同的行高？| Why should headings and body text use different line heights?
     **答案 | Answer:** 标题通常较大且单行/少行，可用较小行高；正文需要更大行高以提升长文本可读性 | Headings are usually large and single/few lines, can use smaller line height; body text needs larger line height for long-form readability

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // src/constants/typography.ts
  // 排版系统常量定义 | Typography system constant definitions
  import { TextStyle } from 'react-native';

  // 基础字体大小 | Base font size
  const BASE_FONT_SIZE = 16;

  // 字体缩放比例 | Font scale ratio
  const SCALE_RATIO = 1.25;

  // 颜色定义 | Color definitions
  export const COLORS = {
    text: {
      primary: '#14171A', // 主要文本 | Primary text
      secondary: '#657786', // 次要文本 | Secondary text
      tertiary: '#AAB8C2', // 第三级文本 | Tertiary text
      link: '#1DA1F2', // 链接文本 | Link text
      inverse: '#FFFFFF', // 反色文本(深色背景) | Inverse text (dark background)
    },
  };

  // 字体权重 | Font weights
  export const FONT_WEIGHTS = {
    regular: '400' as TextStyle['fontWeight'],
    medium: '500' as TextStyle['fontWeight'],
    semibold: '600' as TextStyle['fontWeight'],
    bold: '700' as TextStyle['fontWeight'],
  };

  // 排版样式定义 | Typography style definitions
  export const TYPOGRAPHY = {
    // 一级标题 | Heading 1
    h1: {
      fontSize: BASE_FONT_SIZE * SCALE_RATIO * SCALE_RATIO * SCALE_RATIO, // ~31.25
      lineHeight: BASE_FONT_SIZE * SCALE_RATIO * SCALE_RATIO * SCALE_RATIO * 1.3,
      fontWeight: FONT_WEIGHTS.bold,
      color: COLORS.text.primary,
    },

    // 二级标题 | Heading 2
    h2: {
      fontSize: BASE_FONT_SIZE * SCALE_RATIO * SCALE_RATIO, // ~25
      lineHeight: BASE_FONT_SIZE * SCALE_RATIO * SCALE_RATIO * 1.3,
      fontWeight: FONT_WEIGHTS.bold,
      color: COLORS.text.primary,
    },

    // 三级标题 | Heading 3
    h3: {
      fontSize: BASE_FONT_SIZE * SCALE_RATIO, // 20
      lineHeight: BASE_FONT_SIZE * SCALE_RATIO * 1.4,
      fontWeight: FONT_WEIGHTS.semibold,
      color: COLORS.text.primary,
    },

    // 正文 | Body text
    body: {
      fontSize: BASE_FONT_SIZE, // 16
      lineHeight: BASE_FONT_SIZE * 1.6, // 25.6
      fontWeight: FONT_WEIGHTS.regular,
      color: COLORS.text.primary,
    },

    // 强调正文 | Emphasized body
    bodyBold: {
      fontSize: BASE_FONT_SIZE,
      lineHeight: BASE_FONT_SIZE * 1.6,
      fontWeight: FONT_WEIGHTS.semibold,
      color: COLORS.text.primary,
    },

    // 小号文本 | Small text
    small: {
      fontSize: BASE_FONT_SIZE / SCALE_RATIO, // ~12.8
      lineHeight: (BASE_FONT_SIZE / SCALE_RATIO) * 1.5,
      fontWeight: FONT_WEIGHTS.regular,
      color: COLORS.text.secondary,
    },

    // 说明文字 | Caption text
    caption: {
      fontSize: BASE_FONT_SIZE / (SCALE_RATIO * SCALE_RATIO), // ~10.24
      lineHeight: (BASE_FONT_SIZE / (SCALE_RATIO * SCALE_RATIO)) * 1.4,
      fontWeight: FONT_WEIGHTS.regular,
      color: COLORS.text.tertiary,
    },

    // 按钮文字 | Button text
    button: {
      fontSize: BASE_FONT_SIZE,
      lineHeight: BASE_FONT_SIZE * 1.2,
      fontWeight: FONT_WEIGHTS.semibold,
      color: COLORS.text.inverse,
      textAlign: 'center' as TextStyle['textAlign'],
    },

    // 链接 | Link
    link: {
      fontSize: BASE_FONT_SIZE,
      lineHeight: BASE_FONT_SIZE * 1.6,
      fontWeight: FONT_WEIGHTS.medium,
      color: COLORS.text.link,
      textDecorationLine: 'underline' as TextStyle['textDecorationLine'],
    },
  };

  // 可复用的Text组件 | Reusable Text components
  // src/components/Typography.tsx
  import React from 'react';
  import { Text as RNText, TextProps as RNTextProps, StyleSheet } from 'react-native';
  import { TYPOGRAPHY } from '../constants/typography';

  interface TypographyProps extends RNTextProps {
    variant?: keyof typeof TYPOGRAPHY; // 排版变体 | Typography variant
  }

  export const Typography: React.FC<TypographyProps> = ({
    variant = 'body',
    style,
    children,
    ...props
  }) => {
    return (
      <RNText
        style={[TYPOGRAPHY[variant], style]} // 合并预定义样式和自定义样式 | Merge predefined and custom styles
        allowFontScaling={true} // 支持系统字体缩放 | Support system font scaling
        {...props}
      >
        {children}
      </RNText>
    );
  };

  // 使用示例 | Usage example
  import React from 'react';
  import { View, StyleSheet } from 'react-native';
  import { Typography } from '../components/Typography';

  export const ArticleContent: React.FC = () => {
    return (
      <View style={styles.container}>
        {/* 使用预定义的排版样式 | Use predefined typography styles */}
        <Typography variant="h1">文章标题 | Article Title</Typography>
        <Typography variant="small" style={styles.meta}>
          2小时前 | 2 hours ago
        </Typography>
        <Typography variant="body">
          这是文章的正文内容，使用标准的body样式确保最佳可读性。
          This is the article body content, using standard body style for optimal readability.
        </Typography>
        <Typography variant="link">阅读更多 | Read More</Typography>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      padding: 16,
    },
    meta: {
      marginBottom: 12,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - SCALE_RATIO设为1.25的意义是什么？| What is the significance of setting SCALE_RATIO to 1.25?
    **答案 | Answer:** 创建和谐的视觉比例，每级标题大小是上一级的1.25倍，形成清晰的层次 | Creates harmonious visual proportions, each heading level is 1.25 times the previous, forming clear hierarchy
  - allowFontScaling设为false会有什么影响？| What impact does setting allowFontScaling to false have?
    **答案 | Answer:** 文本不会随系统字体设置缩放，可能影响视力不佳用户的可访问性 | Text won't scale with system font settings, potentially affecting accessibility for users with poor vision

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该在每个Text组件上重复定义fontSize和color？| Should fontSize and color be repeatedly defined on each Text component?
    **答案 | Answer:** 不应该，应使用排版系统集中管理，确保一致性和易维护性 | No, should use typography system for centralized management, ensuring consistency and maintainability
  - lineHeight可以设为百分比吗？| Can lineHeight be set as a percentage?
    **答案 | Answer:** 不可以，React Native的lineHeight只接受数字(像素值) | No, React Native's lineHeight only accepts numbers (pixel values)
