# React Native入门 - 第21天：Pull-to-Refresh与无限滚动 | React Native Introduction - Day 21: Pull-to-Refresh & Infinite Scroll

## 学习目标 | Learning Objectives
- 理解并实现FlatList的下拉刷新功能 | Understand and implement pull-to-refresh in FlatList
- 掌握无限滚动的实现原理和最佳实践 | Master infinite scroll implementation principles and best practices
- 学会管理多种加载状态（初次加载、刷新、加载更多） | Learn to manage multiple loading states (initial load, refreshing, loading more)
- 使用ListFooterComponent和ListEmptyComponent优化用户体验 | Use ListFooterComponent and ListEmptyComponent to enhance user experience
- 理解onEndReached和onEndReachedThreshold的工作机制 | Understand how onEndReached and onEndReachedThreshold work
- 构建生产级别的列表分页加载系统 | Build production-grade list pagination loading systems

## 详细内容 | Detailed Content

### 1. Pull-to-Refresh基础概念 | Pull-to-Refresh Fundamentals (1小时 | 1 hour)

- **下拉刷新机制 | Pull-to-Refresh Mechanism**

  **概念定义 | Concept Definition:**
  下拉刷新（Pull-to-Refresh）是移动应用中的标准交互模式，允许用户通过在列表顶部向下拖动来触发内容刷新。在React Native中，FlatList通过`refreshing`和`onRefresh` props提供内置的下拉刷新支持，无需手动实现复杂的手势处理。| Pull-to-Refresh is a standard interaction pattern in mobile apps that allows users to refresh content by dragging down from the top of a list. In React Native, FlatList provides built-in pull-to-refresh support through `refreshing` and `onRefresh` props, eliminating the need for manual gesture handling.

  **核心特征 | Key Characteristics:**
  - **状态驱动**：`refreshing` prop控制刷新指示器的显示和隐藏 | **State-driven**: `refreshing` prop controls the display and hiding of refresh indicator
  - **回调触发**：`onRefresh`函数在用户下拉时被调用 | **Callback-triggered**: `onRefresh` function is called when user pulls down
  - **平台一致性**：在iOS和Android上提供原生外观和感觉 | **Platform consistency**: Provides native look and feel on both iOS and Android
  - **异步友好**：天然支持异步数据获取操作 | **Async-friendly**: Naturally supports asynchronous data fetching operations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `refreshing` prop需要手动设置为true才能显示刷新指示器吗？| Does the `refreshing` prop need to be manually set to true to show the refresh indicator?
     **答案 | Answer:** 是 | Yes - `refreshing`是一个受控属性，必须由开发者管理状态 | `refreshing` is a controlled prop that must be state-managed by the developer
  2. `onRefresh`函数会自动将`refreshing`设置为false吗？| Does the `onRefresh` function automatically set `refreshing` to false?
     **答案 | Answer:** 否 | No - 开发者需要在数据刷新完成后手动设置`refreshing={false}` | Developers must manually set `refreshing={false}` after data refresh completes
  3. 下拉刷新是否可以在ScrollView中使用？| Can pull-to-refresh be used in ScrollView?
     **答案 | Answer:** 可以 | Yes - ScrollView也支持`refreshControl` prop来实现下拉刷新 | ScrollView also supports `refreshControl` prop for pull-to-refresh
  4. 下拉刷新通常用于加载什么类型的数据？| What type of data is pull-to-refresh typically used to load?
     **答案 | Answer:** 最新的数据 | Latest data - 通常用于获取最新内容并替换现有列表数据 | Typically used to fetch latest content and replace existing list data

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 基础下拉刷新实现 | Basic Pull-to-Refresh Implementation
  import React, { useState } from 'react';
  import { FlatList, Text, View, StyleSheet, ActivityIndicator } from 'react-native';

  interface Post {
    id: string;
    title: string;
    timestamp: Date;
  }

  const BasicRefreshExample = () => {
    const [data, setData] = useState<Post[]>([
      { id: '1', title: 'First Post', timestamp: new Date() },
      { id: '2', title: 'Second Post', timestamp: new Date() },
    ]);
    const [refreshing, setRefreshing] = useState(false); // 控制刷新状态 | Control refresh state

    // 下拉刷新处理函数 | Pull-to-refresh handler
    const onRefresh = async () => {
      setRefreshing(true); // 显示刷新指示器 | Show refresh indicator

      // 模拟网络请求 | Simulate network request
      await new Promise(resolve => setTimeout(resolve, 2000));

      // 更新数据为最新内容 | Update data with latest content
      const newData: Post[] = [
        { id: Date.now().toString(), title: 'New Post', timestamp: new Date() },
        ...data,
      ];
      setData(newData);

      setRefreshing(false); // 隐藏刷新指示器 | Hide refresh indicator
    };

    return (
      <FlatList
        data={data}
        keyExtractor={(item) => item.id}
        refreshing={refreshing} // 绑定刷新状态 | Bind refresh state
        onRefresh={onRefresh}   // 绑定刷新回调 | Bind refresh callback
        renderItem={({ item }) => (
          <View style={styles.item}>
            <Text style={styles.title}>{item.title}</Text>
            <Text style={styles.timestamp}>
              {item.timestamp.toLocaleTimeString()}
            </Text>
          </View>
        )}
      />
    );
  };

  const styles = StyleSheet.create({
    item: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
    },
    timestamp: {
      fontSize: 12,
      color: '#666',
      marginTop: 4,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果在`onRefresh`中忘记设置`setRefreshing(false)`会发生什么？| What happens if you forget to set `setRefreshing(false)` in `onRefresh`?
    **答案 | Answer:** 刷新指示器将永远显示，用户体验很差 | The refresh indicator will show forever, resulting in poor user experience
  - 为什么要在`onRefresh`开始时设置`setRefreshing(true)`？| Why set `setRefreshing(true)` at the start of `onRefresh`?
    **答案 | Answer:** 立即显示刷新指示器，提供即时视觉反馈 | To immediately show the refresh indicator and provide instant visual feedback

  **常见误区检查 | Common Misconception Checks:**
  - `refreshing`是自动管理的状态吗？| Is `refreshing` an automatically managed state?
    **答案 | Answer:** 否，这是一个受控属性，开发者必须手动管理 | No, this is a controlled prop that developers must manually manage
  - 下拉刷新会自动清空现有数据吗？| Does pull-to-refresh automatically clear existing data?
    **答案 | Answer:** 不会，开发者需要决定如何更新数据（替换或合并） | No, developers need to decide how to update data (replace or merge)

- **RefreshControl自定义 | RefreshControl Customization**

  **概念定义 | Concept Definition:**
  虽然FlatList提供了简化的`refreshing`和`onRefresh` props，但在需要更多控制时，可以使用`refreshControl` prop传入自定义的RefreshControl组件。这允许自定义颜色、标题和其他平台特定的刷新行为。| While FlatList provides simplified `refreshing` and `onRefresh` props, when more control is needed, you can use the `refreshControl` prop to pass a custom RefreshControl component. This allows customization of colors, titles, and other platform-specific refresh behaviors.

  **核心特征 | Key Characteristics:**
  - **颜色自定义**：可以设置刷新指示器的颜色以匹配应用主题 | **Color customization**: Can set refresh indicator colors to match app theme
  - **标题支持**（仅iOS）：可以显示刷新相关的文本提示 | **Title support** (iOS only): Can display refresh-related text hints
  - **tintColor控制**：控制iOS上刷新指示器的主色调 | **tintColor control**: Controls the main tint color of refresh indicator on iOS
  - **平台差异化**：可以为iOS和Android设置不同的样式 | **Platform differentiation**: Can set different styles for iOS and Android

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. RefreshControl的`title` prop在Android上有效吗？| Does RefreshControl's `title` prop work on Android?
     **答案 | Answer:** 否 | No - `title`仅在iOS上可用，Android会忽略此属性 | `title` is iOS-only, Android ignores this prop
  2. 可以同时使用`refreshing` prop和`refreshControl` prop吗？| Can you use both `refreshing` prop and `refreshControl` prop simultaneously?
     **答案 | Answer:** 使用`refreshControl`时应该省略`refreshing`和`onRefresh` | When using `refreshControl`, omit `refreshing` and `onRefresh` props
  3. RefreshControl的`colors`属性（Android）接受什么类型的值？| What type of value does RefreshControl's `colors` prop (Android) accept?
     **答案 | Answer:** 字符串数组 | String array - 定义刷新指示器的多个颜色 | Defines multiple colors for the refresh indicator

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 自定义RefreshControl样式 | Custom RefreshControl Styling
  import React, { useState } from 'react';
  import {
    FlatList,
    RefreshControl,
    Text,
    View,
    StyleSheet,
    Platform
  } from 'react-native';

  const CustomRefreshExample = () => {
    const [data, setData] = useState<string[]>(['Item 1', 'Item 2', 'Item 3']);
    const [refreshing, setRefreshing] = useState(false);

    const onRefresh = async () => {
      setRefreshing(true);
      await new Promise(resolve => setTimeout(resolve, 2000));
      setData(prev => [`New Item ${Date.now()}`, ...prev]);
      setRefreshing(false);
    };

    return (
      <FlatList
        data={data}
        keyExtractor={(item, index) => `${item}-${index}`}
        // 使用自定义RefreshControl | Use custom RefreshControl
        refreshControl={
          <RefreshControl
            refreshing={refreshing}
            onRefresh={onRefresh}
            // iOS特定属性 | iOS-specific props
            tintColor="#FF6B6B"  // iOS刷新指示器颜色 | iOS refresh indicator color
            title="下拉刷新... | Pull to refresh..."  // 仅iOS显示 | iOS only
            titleColor="#FF6B6B"
            // Android特定属性 | Android-specific props
            colors={['#FF6B6B', '#4ECDC4', '#45B7D1']} // Android动画颜色 | Android animation colors
            progressBackgroundColor="#ffffff"
          />
        }
        renderItem={({ item }) => (
          <View style={styles.item}>
            <Text>{item}</Text>
          </View>
        )}
      />
    );
  };

  const styles = StyleSheet.create({
    item: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么Android使用`colors`数组而不是单个颜色？| Why does Android use a `colors` array instead of a single color?
    **答案 | Answer:** Android的刷新指示器会在这些颜色之间循环动画 | Android's refresh indicator cycles through these colors in animation
  - 在跨平台应用中如何处理iOS和Android的差异？| How to handle iOS and Android differences in cross-platform apps?
    **答案 | Answer:** 使用Platform.select()或条件渲染提供平台特定的配置 | Use Platform.select() or conditional rendering to provide platform-specific configurations

### 2. 无限滚动实现原理 | Infinite Scroll Implementation Principles (1小时 | 1 hour)

- **onEndReached核心机制 | onEndReached Core Mechanism**

  **概念定义 | Concept Definition:**
  无限滚动（Infinite Scroll）是现代移动应用中常见的分页模式，当用户滚动到列表底部时自动加载更多数据。FlatList通过`onEndReached`回调和`onEndReachedThreshold`配置实现这一功能。`onEndReached`在列表滚动到距离底部特定距离时触发，而`onEndReachedThreshold`定义了这个触发距离。| Infinite Scroll is a common pagination pattern in modern mobile apps that automatically loads more data when users scroll to the bottom of a list. FlatList implements this through the `onEndReached` callback and `onEndReachedThreshold` configuration. `onEndReached` triggers when the list scrolls to a specific distance from the bottom, while `onEndReachedThreshold` defines this trigger distance.

  **核心特征 | Key Characteristics:**
  - **自动触发**：基于滚动位置自动调用，无需用户额外操作 | **Auto-trigger**: Automatically called based on scroll position without additional user action
  - **阈值控制**：`onEndReachedThreshold`控制触发时机（0-1之间的数值） | **Threshold control**: `onEndReachedThreshold` controls trigger timing (value between 0-1)
  - **可能重复调用**：在某些情况下可能被多次触发，需要防抖处理 | **Possible duplicate calls**: May be triggered multiple times in certain cases, requiring debounce handling
  - **分页友好**：天然适合实现基于页码或游标的分页 | **Pagination-friendly**: Naturally suited for implementing page-based or cursor-based pagination

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `onEndReachedThreshold={0.5}`表示什么？| What does `onEndReachedThreshold={0.5}` mean?
     **答案 | Answer:** 当滚动到距离列表底部50%的位置时触发 | Triggers when scrolled to 50% distance from the bottom of the list
  2. `onEndReached`会在初次渲染时被调用吗？| Will `onEndReached` be called on initial render?
     **答案 | Answer:** 可能会 | Possibly - 如果初始数据不足以填满屏幕，可能会立即触发 | If initial data doesn't fill the screen, it may trigger immediately
  3. 如何防止`onEndReached`被重复调用？| How to prevent `onEndReached` from being called repeatedly?
     **答案 | Answer:** 使用加载状态标志位，只在非加载状态时执行加载逻辑 | Use a loading state flag and only execute loading logic when not loading
  4. 更大的`onEndReachedThreshold`值意味着更早还是更晚触发？| Does a larger `onEndReachedThreshold` value mean earlier or later triggering?
     **答案 | Answer:** 更早 | Earlier - 更大的值意味着距离底部更远时就触发 | Larger value means triggering when further from the bottom

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 基础无限滚动实现 | Basic Infinite Scroll Implementation
  import React, { useState, useRef } from 'react';
  import { FlatList, Text, View, ActivityIndicator, StyleSheet } from 'react-native';

  interface DataItem {
    id: string;
    content: string;
  }

  const InfiniteScrollExample = () => {
    const [data, setData] = useState<DataItem[]>(
      // 初始化20条数据 | Initialize with 20 items
      Array.from({ length: 20 }, (_, i) => ({
        id: `item-${i}`,
        content: `Item ${i + 1}`,
      }))
    );
    const [isLoadingMore, setIsLoadingMore] = useState(false);
    const currentPage = useRef(1); // 跟踪当前页码 | Track current page number

    // 加载更多数据的函数 | Function to load more data
    const loadMoreData = async () => {
      // 防止重复调用 | Prevent duplicate calls
      if (isLoadingMore) {
        console.log('Already loading, skipping...'); // 调试日志 | Debug log
        return;
      }

      console.log('Loading more data...'); // 调试日志 | Debug log
      setIsLoadingMore(true);

      // 模拟API请求 | Simulate API request
      await new Promise(resolve => setTimeout(resolve, 1500));

      // 生成新数据 | Generate new data
      const nextPage = currentPage.current + 1;
      const newItems: DataItem[] = Array.from({ length: 20 }, (_, i) => ({
        id: `item-${(nextPage - 1) * 20 + i}`,
        content: `Item ${(nextPage - 1) * 20 + i + 1}`,
      }));

      setData(prev => [...prev, ...newItems]); // 追加新数据 | Append new data
      currentPage.current = nextPage; // 更新页码 | Update page number
      setIsLoadingMore(false);
    };

    return (
      <FlatList
        data={data}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.item}>
            <Text>{item.content}</Text>
          </View>
        )}
        // 无限滚动配置 | Infinite scroll configuration
        onEndReached={loadMoreData}        // 到达底部时的回调 | Callback when reaching bottom
        onEndReachedThreshold={0.5}        // 距离底部50%时触发 | Trigger at 50% from bottom
        // 底部加载指示器 | Bottom loading indicator
        ListFooterComponent={
          isLoadingMore ? (
            <View style={styles.footer}>
              <ActivityIndicator size="small" color="#0000ff" />
              <Text style={styles.footerText}>加载中... | Loading...</Text>
            </View>
          ) : null
        }
      />
    );
  };

  const styles = StyleSheet.create({
    item: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    footer: {
      paddingVertical: 20,
      alignItems: 'center',
    },
    footerText: {
      marginTop: 8,
      color: '#666',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么需要`isLoadingMore`状态？| Why do we need the `isLoadingMore` state?
    **答案 | Answer:** 防止在加载过程中重复触发`onEndReached`，避免重复请求 | Prevents duplicate triggers of `onEndReached` during loading, avoiding duplicate requests
  - 如果不使用`useRef`保存页码会有什么问题？| What problem occurs if not using `useRef` to save page number?
    **答案 | Answer:** 使用state会导致闭包问题，可能获取到过时的页码值 | Using state causes closure issues, potentially getting stale page number values

  **常见误区检查 | Common Misconception Checks:**
  - `onEndReachedThreshold={1}`是否意味着滚动到最底部才触发？| Does `onEndReachedThreshold={1}` mean triggering only at the very bottom?
    **答案 | Answer:** 否，值为1表示距离底部100%（即列表高度）时触发，会很早触发 | No, value of 1 means triggering at 100% distance from bottom (i.e., list height), triggering very early
  - 是否应该在`onEndReached`中直接修改data状态？| Should you directly modify data state in `onEndReached`?
    **答案 | Answer:** 应该先设置加载状态，在异步操作完成后再更新数据 | Should first set loading state, then update data after async operation completes

- **分页策略：基于页码 vs 基于游标 | Pagination Strategies: Page-based vs Cursor-based**

  **概念定义 | Concept Definition:**
  实现无限滚动时，主要有两种分页策略。基于页码的分页（Page-based）使用页码和每页条数来请求数据（如page=2, limit=20）；基于游标的分页（Cursor-based）使用上一批数据的最后一项作为起点（如cursor=item_id_40）。基于游标的方式更适合动态变化的数据，而基于页码的方式实现更简单。| When implementing infinite scroll, there are two main pagination strategies. Page-based pagination uses page numbers and items per page to request data (e.g., page=2, limit=20); Cursor-based pagination uses the last item of the previous batch as the starting point (e.g., cursor=item_id_40). Cursor-based approach is better suited for dynamically changing data, while page-based approach is simpler to implement.

  **核心特征 | Key Characteristics:**
  - **页码分页**：简单直观，适合静态或缓慢变化的数据 | **Page-based**: Simple and intuitive, suitable for static or slowly changing data
  - **游标分页**：避免数据重复和遗漏，适合实时更新的数据 | **Cursor-based**: Avoids data duplication and omission, suitable for real-time updated data
  - **偏移问题**：页码分页在数据插入/删除时可能出现重复或遗漏 | **Offset problem**: Page-based pagination may have duplication or omission when data is inserted/deleted
  - **API支持**：选择策略取决于后端API支持的方式 | **API support**: Strategy choice depends on backend API support

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果在加载第2页时，列表顶部新增了5条数据，页码分页会有什么问题？| If 5 new items are added at the top while loading page 2, what problem occurs with page-based pagination?
     **答案 | Answer:** 会重复看到5条数据 | Will see 5 duplicate items - 因为原来第2页的前5条变成了第1页的后5条 | Because the first 5 items of original page 2 became the last 5 items of page 1
  2. 游标分页的游标通常是什么？| What is the cursor in cursor-based pagination typically?
     **答案 | Answer:** 通常是最后一项的唯一标识符（ID）或时间戳 | Typically the unique identifier (ID) or timestamp of the last item
  3. 哪种分页方式更容易实现"跳转到第N页"功能？| Which pagination method makes it easier to implement "jump to page N" functionality?
     **答案 | Answer:** 页码分页 | Page-based - 游标分页需要顺序加载所有前置页面 | Cursor-based requires sequential loading of all previous pages

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 游标分页实现示例 | Cursor-based Pagination Example
  import React, { useState } from 'react';
  import { FlatList, Text, View, ActivityIndicator } from 'react-native';

  interface Post {
    id: string;
    content: string;
    createdAt: number;
  }

  const CursorPaginationExample = () => {
    const [posts, setPosts] = useState<Post[]>([]);
    const [isLoadingMore, setIsLoadingMore] = useState(false);
    const [cursor, setCursor] = useState<string | null>(null); // 游标状态 | Cursor state
    const [hasMore, setHasMore] = useState(true); // 是否还有更多数据 | Whether there's more data

    // 模拟API调用 | Simulate API call
    const fetchPosts = async (afterCursor?: string | null) => {
      // 模拟后端返回数据结构 | Simulate backend response structure
      const response = {
        data: Array.from({ length: 10 }, (_, i) => ({
          id: `post-${Date.now()}-${i}`,
          content: `Post content ${i}`,
          createdAt: Date.now() - i * 1000,
        })),
        nextCursor: `cursor-${Date.now()}`, // 下一页的游标 | Cursor for next page
        hasMore: Math.random() > 0.3, // 随机决定是否还有更多 | Randomly decide if more exists
      };

      return response;
    };

    const loadMore = async () => {
      if (isLoadingMore || !hasMore) return; // 防护检查 | Guard check

      setIsLoadingMore(true);
      const result = await fetchPosts(cursor); // 使用游标请求 | Request using cursor

      setPosts(prev => [...prev, ...result.data]); // 追加数据 | Append data
      setCursor(result.nextCursor); // 更新游标 | Update cursor
      setHasMore(result.hasMore); // 更新是否还有更多 | Update hasMore flag
      setIsLoadingMore(false);
    };

    return (
      <FlatList
        data={posts}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={{ padding: 20, borderBottomWidth: 1 }}>
            <Text>{item.content}</Text>
            <Text style={{ fontSize: 12, color: '#666' }}>
              {new Date(item.createdAt).toLocaleString()}
            </Text>
          </View>
        )}
        onEndReached={loadMore}
        onEndReachedThreshold={0.5}
        ListFooterComponent={
          hasMore && isLoadingMore ? (
            <ActivityIndicator style={{ padding: 20 }} />
          ) : !hasMore ? (
            <Text style={{ textAlign: 'center', padding: 20, color: '#999' }}>
              没有更多数据了 | No more data
            </Text>
          ) : null
        }
      />
    );
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么需要`hasMore`标志？| Why do we need the `hasMore` flag?
    **答案 | Answer:** 防止在没有更多数据时继续发送无效请求 | Prevents sending invalid requests when no more data exists
  - 游标分页如何处理数据实时变化的情况？| How does cursor-based pagination handle real-time data changes?
    **答案 | Answer:** 游标锁定了起始位置，不会受到之前数据插入删除的影响 | Cursor locks the starting position, unaffected by previous data insertions/deletions

### 3. 加载状态管理 | Loading State Management (1小时 | 1 hour)

- **多重加载状态的设计 | Multiple Loading States Design**

  **概念定义 | Concept Definition:**
  在生产级应用中，列表通常需要管理三种独立的加载状态：初次加载（Initial Loading）、下拉刷新（Refreshing）和加载更多（Loading More）。每种状态需要不同的UI反馈和用户体验考虑。正确区分和管理这些状态对于提供流畅的用户体验至关重要。| In production-grade apps, lists typically need to manage three independent loading states: Initial Loading, Refreshing (pull-to-refresh), and Loading More. Each state requires different UI feedback and user experience considerations. Correctly distinguishing and managing these states is crucial for providing a smooth user experience.

  **核心特征 | Key Characteristics:**
  - **初次加载**：显示全屏加载指示器，列表为空时 | **Initial loading**: Shows full-screen loading indicator when list is empty
  - **刷新中**：使用FlatList的内置刷新指示器，不阻塞列表查看 | **Refreshing**: Uses FlatList's built-in refresh indicator, doesn't block list viewing
  - **加载更多**：底部显示小型加载指示器，不影响已加载内容 | **Loading more**: Shows small loading indicator at bottom, doesn't affect loaded content
  - **状态互斥**：这些状态应该是互斥的，不应同时发生 | **State mutual exclusion**: These states should be mutually exclusive, shouldn't occur simultaneously

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以同时处于"刷新中"和"加载更多"状态吗？| Can the list be in both "refreshing" and "loading more" states simultaneously?
     **答案 | Answer:** 不应该 | Should not - 应该使用状态检查防止这种情况 | Should use state checks to prevent this scenario
  2. 初次加载和加载更多有什么区别？| What's the difference between initial loading and loading more?
     **答案 | Answer:** 初次加载时data为空，加载更多时data已有内容 | Initial loading has empty data, loading more has existing data
  3. 下拉刷新应该替换数据还是追加数据？| Should pull-to-refresh replace or append data?
     **答案 | Answer:** 替换 | Replace - 下拉刷新获取最新数据并替换现有列表 | Pull-to-refresh fetches latest data and replaces existing list
  4. 如何知道当前应该显示哪种加载指示器？| How to know which loading indicator to show currently?
     **答案 | Answer:** 基于状态组合：data.length === 0 && isLoading = 初次加载 | Based on state combination: data.length === 0 && isLoading = initial loading

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 完整的多状态加载管理 | Complete Multi-State Loading Management
  import React, { useState, useEffect } from 'react';
  import {
    FlatList,
    View,
    Text,
    ActivityIndicator,
    StyleSheet,
    RefreshControl,
  } from 'react-native';

  interface Article {
    id: string;
    title: string;
    author: string;
  }

  const MultiStateLoadingExample = () => {
    const [articles, setArticles] = useState<Article[]>([]);

    // 三种独立的加载状态 | Three independent loading states
    const [isInitialLoading, setIsInitialLoading] = useState(true);
    const [isRefreshing, setIsRefreshing] = useState(false);
    const [isLoadingMore, setIsLoadingMore] = useState(false);

    const [page, setPage] = useState(1);
    const [hasMore, setHasMore] = useState(true);

    // 模拟API调用 | Simulate API call
    const fetchArticles = async (pageNum: number): Promise<Article[]> => {
      await new Promise(resolve => setTimeout(resolve, 1500));
      return Array.from({ length: 10 }, (_, i) => ({
        id: `article-${pageNum}-${i}`,
        title: `Article ${(pageNum - 1) * 10 + i + 1}`,
        author: `Author ${i + 1}`,
      }));
    };

    // 初次加载 | Initial load
    useEffect(() => {
      loadInitialData();
    }, []);

    const loadInitialData = async () => {
      setIsInitialLoading(true); // 设置初次加载状态 | Set initial loading state
      const data = await fetchArticles(1);
      setArticles(data);
      setPage(1);
      setIsInitialLoading(false); // 清除初次加载状态 | Clear initial loading state
    };

    // 下拉刷新 | Pull to refresh
    const onRefresh = async () => {
      setIsRefreshing(true); // 设置刷新状态 | Set refreshing state
      const data = await fetchArticles(1);
      setArticles(data); // 替换所有数据 | Replace all data
      setPage(1);
      setHasMore(true); // 重置hasMore | Reset hasMore
      setIsRefreshing(false); // 清除刷新状态 | Clear refreshing state
    };

    // 加载更多 | Load more
    const loadMore = async () => {
      // 防止重复加载和状态冲突 | Prevent duplicate loading and state conflicts
      if (isLoadingMore || isRefreshing || !hasMore) {
        return;
      }

      setIsLoadingMore(true); // 设置加载更多状态 | Set loading more state
      const nextPage = page + 1;
      const newData = await fetchArticles(nextPage);

      if (newData.length === 0) {
        setHasMore(false); // 没有更多数据 | No more data
      } else {
        setArticles(prev => [...prev, ...newData]); // 追加数据 | Append data
        setPage(nextPage);
      }

      setIsLoadingMore(false); // 清除加载更多状态 | Clear loading more state
    };

    // 初次加载：显示全屏加载 | Initial loading: show full-screen loader
    if (isInitialLoading) {
      return (
        <View style={styles.centerContainer}>
          <ActivityIndicator size="large" color="#0000ff" />
          <Text style={styles.loadingText}>加载中... | Loading...</Text>
        </View>
      );
    }

    return (
      <FlatList
        data={articles}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.articleItem}>
            <Text style={styles.title}>{item.title}</Text>
            <Text style={styles.author}>作者 | By: {item.author}</Text>
          </View>
        )}
        // 下拉刷新配置 | Pull-to-refresh configuration
        refreshControl={
          <RefreshControl
            refreshing={isRefreshing}
            onRefresh={onRefresh}
            colors={['#0000ff']} // Android
            tintColor="#0000ff"  // iOS
          />
        }
        // 无限滚动配置 | Infinite scroll configuration
        onEndReached={loadMore}
        onEndReachedThreshold={0.3}
        // 底部加载指示器 | Bottom loading indicator
        ListFooterComponent={() => {
          if (!hasMore) {
            return (
              <Text style={styles.endText}>
                没有更多内容了 | No more content
              </Text>
            );
          }

          if (isLoadingMore) {
            return (
              <View style={styles.footerLoading}>
                <ActivityIndicator size="small" color="#0000ff" />
                <Text style={styles.footerText}>
                  加载更多... | Loading more...
                </Text>
              </View>
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
    },
    loadingText: {
      marginTop: 10,
      fontSize: 16,
      color: '#666',
    },
    articleItem: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    author: {
      fontSize: 14,
      color: '#666',
    },
    footerLoading: {
      paddingVertical: 20,
      alignItems: 'center',
    },
    footerText: {
      marginTop: 8,
      fontSize: 14,
      color: '#666',
    },
    endText: {
      textAlign: 'center',
      padding: 20,
      fontSize: 14,
      color: '#999',
    },
  });

  export default MultiStateLoadingExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么在`loadMore`中要检查`isRefreshing`？| Why check `isRefreshing` in `loadMore`?
    **答案 | Answer:** 防止在刷新时同时加载更多，避免状态冲突和数据混乱 | Prevents loading more while refreshing, avoiding state conflicts and data confusion
  - 初次加载和其他状态的UI有什么不同？| How does initial loading UI differ from other states?
    **答案 | Answer:** 初次加载显示全屏加载器，其他状态保留列表可见性 | Initial loading shows full-screen loader, other states maintain list visibility

  **常见误区检查 | Common Misconception Checks:**
  - 可以用一个统一的`isLoading`状态管理所有加载吗？| Can you use a single `isLoading` state to manage all loading?
    **答案 | Answer:** 不推荐，会导致无法区分不同的加载场景和显示适当的UI | Not recommended, leads to inability to distinguish different loading scenarios and show appropriate UI
  - 下拉刷新时需要显示底部的加载指示器吗？| Should bottom loading indicator be shown during pull-to-refresh?
    **答案 | Answer:** 不需要，下拉刷新有自己的顶部指示器 | Not needed, pull-to-refresh has its own top indicator
