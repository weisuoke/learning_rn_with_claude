# React Native入门 - 第6周第4天：分类与搜索功能 | React Native Introduction - Week 6 Day 4: Categories & Search Functionality

## 学习目标 | Learning Objectives
- 掌握水平滚动列表的构建和最佳实践 | Master building horizontal scrollable lists and best practices
- 理解并实现防抖（debouncing）技术优化搜索性能 | Understand and implement debouncing to optimize search performance
- 学习如何设计和实现动态过滤UI模式 | Learn how to design and implement dynamic filtering UI patterns
- 掌握TextInput组件的高级用法和事件处理 | Master advanced usage of TextInput component and event handling
- 理解状态管理在复杂交互场景中的应用 | Understand state management in complex interaction scenarios
- 学习如何清理组件状态以避免内存泄漏 | Learn how to clean up component state to prevent memory leaks

## 详细内容 | Detailed Content

### 1. 水平滚动列表与分类选择器 | Horizontal ScrollView & Category Selector (1.5小时 | 1.5 hours)

- **水平滚动视图（Horizontal ScrollView）| Horizontal ScrollView**

  **概念定义 | Concept Definition:**
  水平滚动视图是ScrollView组件的一种变体，通过设置`horizontal={true}`属性使内容横向滚动而非默认的纵向滚动。这是移动应用中实现标签栏、分类选择器、轮播图等UI模式的核心组件。| A Horizontal ScrollView is a variant of the ScrollView component that enables horizontal scrolling by setting the `horizontal={true}` prop, instead of the default vertical scrolling. It's the core component for implementing UI patterns like tab bars, category selectors, and carousels in mobile apps.

  **核心特征 | Key Characteristics:**
  - **滚动方向控制**：通过`horizontal`属性切换滚动方向 | **Scroll direction control**: Switch scroll direction via `horizontal` prop
  - **内容自动布局**：子元素自动横向排列，无需额外flexbox设置 | **Automatic content layout**: Child elements automatically align horizontally without extra flexbox settings
  - **滚动指示器**：`showsHorizontalScrollIndicator`控制滚动条显示 | **Scroll indicator**: `showsHorizontalScrollIndicator` controls scrollbar visibility
  - **性能优化**：对于大量项目应考虑使用FlatList的horizontal模式 | **Performance optimization**: For large lists, consider using FlatList's horizontal mode

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. ScrollView默认的滚动方向是什么？| What is the default scroll direction of ScrollView?
     **答案 | Answer:** 纵向（垂直）| Vertical - ScrollView scrolls vertically by default unless `horizontal={true}` is specified.
  2. `horizontal={true}`和Flexbox的`flexDirection: 'row'`是同一个概念吗？| Are `horizontal={true}` and Flexbox's `flexDirection: 'row'` the same concept?
     **答案 | Answer:** 否 | No - `horizontal={true}` controls scroll direction, while `flexDirection: 'row'` controls layout direction. They serve different purposes but often work together.
  3. 何时应该使用FlatList而不是ScrollView的horizontal模式？| When should you use FlatList instead of horizontal ScrollView?
     **答案 | Answer:** 当列表项超过20-30个时 | When list items exceed 20-30 items - FlatList provides better performance through virtualization for large datasets.
  4. 水平滚动视图中的内容会自动换行吗？| Does content in a horizontal ScrollView automatically wrap to next line?
     **答案 | Answer:** 否 | No - Content continues horizontally in a single line. If wrapping is needed, you must implement it manually or use a different layout approach.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { ScrollView, View, Text, StyleSheet, Pressable } from 'react-native';

  // 基础水平滚动列表 | Basic horizontal scrollable list
  const BasicHorizontalScroll = () => {
    return (
      <ScrollView
        horizontal={true}  // 启用水平滚动 | Enable horizontal scrolling
        showsHorizontalScrollIndicator={false}  // 隐藏滚动条 | Hide scrollbar
        contentContainerStyle={styles.scrollContent}  // 内容容器样式 | Content container style
      >
        {['Technology', 'Business', 'Sports', 'Entertainment', 'Health'].map((category) => (
          <View key={category} style={styles.categoryItem}>
            <Text>{category}</Text>
          </View>
        ))}
      </ScrollView>
    );
  };

  // 可选择的分类按钮列表 | Selectable category button list
  const CategorySelector = () => {
    const [selectedCategory, setSelectedCategory] = React.useState('Technology');

    const categories = [
      'Technology', 'Business', 'Sports',
      'Entertainment', 'Health', 'Science', 'Politics'
    ];

    return (
      <ScrollView
        horizontal
        showsHorizontalScrollIndicator={false}
        contentContainerStyle={styles.scrollContent}
      >
        {categories.map((category) => {
          const isSelected = selectedCategory === category;
          return (
            <Pressable
              key={category}
              onPress={() => setSelectedCategory(category)}  // 选择分类 | Select category
              style={[
                styles.categoryButton,
                isSelected && styles.categoryButtonSelected  // 选中状态样式 | Selected state style
              ]}
            >
              <Text style={[
                styles.categoryText,
                isSelected && styles.categoryTextSelected
              ]}>
                {category}
              </Text>
            </Pressable>
          );
        })}
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    scrollContent: {
      paddingHorizontal: 16,  // 左右内边距 | Horizontal padding
      paddingVertical: 12,    // 上下内边距 | Vertical padding
    },
    categoryItem: {
      marginRight: 12,         // 项目间距 | Item spacing
      padding: 12,
      backgroundColor: '#f0f0f0',
      borderRadius: 8,
    },
    categoryButton: {
      paddingHorizontal: 20,
      paddingVertical: 10,
      marginRight: 10,
      backgroundColor: '#e0e0e0',
      borderRadius: 20,
      borderWidth: 2,
      borderColor: 'transparent',
    },
    categoryButtonSelected: {
      backgroundColor: '#007AFF',  // 选中背景色 | Selected background
      borderColor: '#0051D5',      // 选中边框色 | Selected border
    },
    categoryText: {
      fontSize: 14,
      color: '#333',
      fontWeight: '500',
    },
    categoryTextSelected: {
      color: '#FFFFFF',  // 选中文字颜色 | Selected text color
      fontWeight: '600',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - `CategorySelector`组件中点击按钮会发生什么？| What happens when you press a button in `CategorySelector`?
    **答案 | Answer:** 调用`setSelectedCategory`更新状态，触发重新渲染，应用选中样式 | Calls `setSelectedCategory` to update state, triggers re-render, applies selected styles
  - 如果移除`showsHorizontalScrollIndicator={false}`会有什么效果？| What happens if you remove `showsHorizontalScrollIndicator={false}`?
    **答案 | Answer:** 底部会显示水平滚动条指示器 | A horizontal scrollbar indicator appears at the bottom
  - `contentContainerStyle`和直接在ScrollView上的`style`有什么区别？| What's the difference between `contentContainerStyle` and `style` on ScrollView?
    **答案 | Answer:** `style`控制ScrollView容器本身，`contentContainerStyle`控制内部内容包装器的样式 | `style` controls the ScrollView container itself, `contentContainerStyle` controls the inner content wrapper's style

  **常见误区检查 | Common Misconception Checks:**
  - 在水平ScrollView中需要设置`flexDirection: 'row'`吗？| Do you need to set `flexDirection: 'row'` in horizontal ScrollView?
    **答案 | Answer:** 不需要 | No - `horizontal={true}` automatically handles the layout direction. Adding `flexDirection: 'row'` is redundant.
  - 水平ScrollView的宽度会自动扩展以适应内容吗？| Does horizontal ScrollView automatically expand its width to fit content?
    **答案 | Answer:** 是的，内容区域会扩展 | Yes - The content area expands, but the visible viewport respects the parent container's width constraints.

- **动态数据加载与分类过滤 | Dynamic Data Loading & Category Filtering**

  **概念定义 | Concept Definition:**
  动态数据加载是指根据用户的交互行为（如选择分类）触发API请求，获取相应的数据并更新UI。这需要管理加载状态、错误状态和数据状态的协调。| Dynamic data loading refers to triggering API requests based on user interactions (like selecting a category), fetching corresponding data, and updating the UI. This requires coordinating loading states, error states, and data states.

  **核心特征 | Key Characteristics:**
  - **状态同步**：用户选择应立即反映在UI上，即使数据还在加载 | **State synchronization**: User selection should immediately reflect in UI even while data is loading
  - **加载状态管理**：需要显示加载指示器避免用户困惑 | **Loading state management**: Must show loading indicators to prevent user confusion
  - **错误处理**：网络失败时需要提供重试机制 | **Error handling**: Provide retry mechanism when network fails
  - **避免竞态条件**：快速切换分类时需要取消旧请求 | **Avoid race conditions**: Must cancel old requests when rapidly switching categories

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 用户点击分类按钮后应该立即显示加载状态吗？| Should loading state be shown immediately after user clicks a category button?
     **答案 | Answer:** 是 | Yes - Immediate feedback is crucial for good UX. The loading state should appear before the API call completes.
  2. 如果用户快速点击多个分类按钮会发生什么？| What happens if the user rapidly clicks multiple category buttons?
     **答案 | Answer:** 可能产生竞态条件 | Race conditions may occur - You need to cancel previous requests or ignore outdated responses.
  3. 加载新分类数据时应该保留旧数据还是清空？| When loading new category data, should you keep old data or clear it?
     **答案 | Answer:** 取决于UX设计 | Depends on UX design - Clearing shows a clean slate but may cause jarring experience. Keeping old data with loading overlay is often better.
  4. API错误时是否应该恢复之前选中的分类？| Should you revert to the previously selected category when API fails?
     **答案 | Answer:** 不一定 | Not necessarily - Better to keep the selected category but show error state, allowing user to retry.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, ScrollView, Pressable, Text, ActivityIndicator, StyleSheet } from 'react-native';

  // 分类选择器组件（带数据加载）| Category selector with data loading
  const CategoryScreenExample = () => {
    const [selectedCategory, setSelectedCategory] = useState('technology');
    const [articles, setArticles] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const categories = [
      { id: 'technology', label: 'Technology' },
      { id: 'business', label: 'Business' },
      { id: 'sports', label: 'Sports' },
      { id: 'entertainment', label: 'Entertainment' },
      { id: 'health', label: 'Health' },
    ];

    // 获取分类文章的函数 | Function to fetch category articles
    const fetchArticlesByCategory = async (category) => {
      setLoading(true);  // 设置加载状态 | Set loading state
      setError(null);    // 清除之前的错误 | Clear previous errors

      try {
        // 模拟API调用 | Simulate API call
        const response = await fetch(
          `https://newsapi.org/v2/top-headlines?category=${category}&apiKey=YOUR_API_KEY`
        );

        if (!response.ok) {
          throw new Error('Failed to fetch articles');
        }

        const data = await response.json();
        setArticles(data.articles);  // 更新文章数据 | Update articles data
      } catch (err) {
        setError(err.message);  // 设置错误状态 | Set error state
        console.error('Error fetching articles:', err);
      } finally {
        setLoading(false);  // 结束加载状态 | End loading state
      }
    };

    // 当选中分类改变时获取数据 | Fetch data when selected category changes
    useEffect(() => {
      fetchArticlesByCategory(selectedCategory);
    }, [selectedCategory]);

    // 处理分类选择 | Handle category selection
    const handleCategoryPress = (categoryId) => {
      setSelectedCategory(categoryId);  // 更新选中分类 | Update selected category
    };

    return (
      <View style={styles.container}>
        {/* 分类选择器 | Category selector */}
        <ScrollView
          horizontal
          showsHorizontalScrollIndicator={false}
          style={styles.categoryScroll}
          contentContainerStyle={styles.categoryScrollContent}
        >
          {categories.map((category) => (
            <Pressable
              key={category.id}
              onPress={() => handleCategoryPress(category.id)}
              style={[
                styles.categoryChip,
                selectedCategory === category.id && styles.categoryChipSelected
              ]}
            >
              <Text style={[
                styles.categoryChipText,
                selectedCategory === category.id && styles.categoryChipTextSelected
              ]}>
                {category.label}
              </Text>
            </Pressable>
          ))}
        </ScrollView>

        {/* 内容区域 | Content area */}
        <View style={styles.contentArea}>
          {loading && (
            <View style={styles.loadingContainer}>
              <ActivityIndicator size="large" color="#007AFF" />
              <Text style={styles.loadingText}>Loading {selectedCategory} articles...</Text>
            </View>
          )}

          {error && !loading && (
            <View style={styles.errorContainer}>
              <Text style={styles.errorText}>Error: {error}</Text>
              <Pressable
                style={styles.retryButton}
                onPress={() => fetchArticlesByCategory(selectedCategory)}
              >
                <Text style={styles.retryButtonText}>Retry</Text>
              </Pressable>
            </View>
          )}

          {!loading && !error && articles.length === 0 && (
            <View style={styles.emptyContainer}>
              <Text style={styles.emptyText}>No articles found</Text>
            </View>
          )}

          {!loading && !error && articles.length > 0 && (
            <ScrollView style={styles.articleList}>
              {articles.map((article, index) => (
                <View key={index} style={styles.articleItem}>
                  <Text style={styles.articleTitle}>{article.title}</Text>
                  <Text style={styles.articleDescription} numberOfLines={2}>
                    {article.description}
                  </Text>
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
    categoryScroll: {
      flexGrow: 0,  // 不占用剩余空间 | Don't take remaining space
      borderBottomWidth: 1,
      borderBottomColor: '#E0E0E0',
    },
    categoryScrollContent: {
      paddingHorizontal: 16,
      paddingVertical: 12,
    },
    categoryChip: {
      paddingHorizontal: 16,
      paddingVertical: 8,
      marginRight: 10,
      backgroundColor: '#F5F5F5',
      borderRadius: 16,
      borderWidth: 1,
      borderColor: '#E0E0E0',
    },
    categoryChipSelected: {
      backgroundColor: '#007AFF',
      borderColor: '#007AFF',
    },
    categoryChipText: {
      fontSize: 14,
      color: '#333333',
      fontWeight: '500',
    },
    categoryChipTextSelected: {
      color: '#FFFFFF',
      fontWeight: '600',
    },
    contentArea: {
      flex: 1,  // 占用剩余空间 | Take remaining space
    },
    loadingContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    loadingText: {
      marginTop: 12,
      fontSize: 14,
      color: '#666666',
    },
    errorContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    errorText: {
      fontSize: 16,
      color: '#D32F2F',
      textAlign: 'center',
      marginBottom: 16,
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
    emptyContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    emptyText: {
      fontSize: 16,
      color: '#999999',
    },
    articleList: {
      flex: 1,
    },
    articleItem: {
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
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当用户点击新分类时，`useEffect`会执行几次？| How many times does `useEffect` execute when user clicks a new category?
    **答案 | Answer:** 1次 | Once - When `selectedCategory` changes, useEffect runs once with the new value
  - 为什么`fetchArticlesByCategory`中要先调用`setError(null)`？| Why call `setError(null)` first in `fetchArticlesByCategory`?
    **答案 | Answer:** 清除上次请求的错误状态 | To clear error state from previous request, preventing stale error messages from showing
  - 如果在数据加载过程中用户再次点击同一分类会发生什么？| What happens if user clicks the same category again while data is loading?
    **答案 | Answer:** 会触发新的API请求 | Triggers a new API request - This is expected behavior, effectively acting as a refresh

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要在组件卸载时清理未完成的fetch请求？| Do you need to cleanup unfinished fetch requests when component unmounts?
    **答案 | Answer:** 是的，应该使用AbortController | Yes - You should use AbortController to cancel pending requests to prevent memory leaks and state updates on unmounted components
  - 能否在`fetchArticlesByCategory`外部直接调用`setLoading(false)`？| Can you call `setLoading(false)` outside `fetchArticlesByCategory`?
    **答案 | Answer:** 不推荐 | Not recommended - Loading state should be managed within the async function using try-catch-finally to ensure it's always reset

### 2. TextInput与防抖搜索实现 | TextInput & Debounced Search Implementation (2小时 | 2 hours)

- **TextInput组件深入理解 | Deep Dive into TextInput Component**

  **概念定义 | Concept Definition:**
  TextInput是React Native中用于接收用户文本输入的核心组件，类似于HTML的`<input>`标签。它提供了丰富的事件处理和自定义选项，是构建搜索框、表单等交互的基础。| TextInput is the core component in React Native for receiving user text input, similar to HTML's `<input>` tag. It provides rich event handling and customization options, forming the foundation for building search bars, forms, and other interactions.

  **核心特征 | Key Characteristics:**
  - **受控组件模式**：通过`value`和`onChangeText`实现React状态同步 | **Controlled component pattern**: Sync with React state via `value` and `onChangeText`
  - **多种键盘类型**：`keyboardType`属性可指定数字键盘、邮箱键盘等 | **Multiple keyboard types**: `keyboardType` prop specifies numeric, email keyboards, etc.
  - **事件丰富**：支持`onFocus`、`onBlur`、`onSubmitEditing`等生命周期事件 | **Rich events**: Supports lifecycle events like `onFocus`, `onBlur`, `onSubmitEditing`
  - **平台差异**：iOS和Android的默认行为和样式存在差异 | **Platform differences**: Default behavior and styling differ between iOS and Android

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. TextInput必须是受控组件吗？| Must TextInput always be a controlled component?
     **答案 | Answer:** 否，但推荐 | No, but recommended - You can use it as uncontrolled component, but controlled pattern gives better state management
  2. `onChangeText`和`onChange`事件有什么区别？| What's the difference between `onChangeText` and `onChange` events?
     **答案 | Answer:** `onChangeText`直接返回文本字符串，`onChange`返回事件对象 | `onChangeText` directly returns text string, `onChange` returns event object
  3. 可以在TextInput中同步执行API调用吗？| Can you synchronously make API calls in TextInput's `onChangeText`?
     **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - This would cause performance issues; use debouncing instead
  4. TextInput的`value`属性可以是数字类型吗？| Can TextInput's `value` prop be a number type?
     **答案 | Answer:** 否 | No - `value` must be a string. Numbers must be converted to strings first

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet, Pressable } from 'react-native';

  // 基础搜索输入框 | Basic search input
  const BasicSearchInput = () => {
    const [searchQuery, setSearchQuery] = useState('');

    return (
      <View style={styles.searchContainer}>
        <TextInput
          style={styles.searchInput}
          value={searchQuery}  // 受控值 | Controlled value
          onChangeText={setSearchQuery}  // 更新状态 | Update state
          placeholder="Search articles..."  // 占位符 | Placeholder
          placeholderTextColor="#999999"   // 占位符颜色 | Placeholder color
          autoCapitalize="none"  // 禁用首字母大写 | Disable auto-capitalization
          autoCorrect={false}    // 禁用自动更正 | Disable auto-correct
          returnKeyType="search"  // 键盘返回键类型 | Keyboard return key type
          clearButtonMode="while-editing"  // iOS清除按钮（仅iOS）| Clear button (iOS only)
        />
        <Text style={styles.queryDisplay}>
          Current query: {searchQuery || '(empty)'}
        </Text>
      </View>
    );
  };

  // 带清除按钮的搜索框 | Search input with clear button
  const SearchInputWithClear = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [isFocused, setIsFocused] = useState(false);

    const handleClear = () => {
      setSearchQuery('');  // 清空输入 | Clear input
    };

    return (
      <View style={styles.searchContainer}>
        <View style={[
          styles.searchInputWrapper,
          isFocused && styles.searchInputWrapperFocused  // 聚焦样式 | Focus style
        ]}>
          <TextInput
            style={styles.searchInputField}
            value={searchQuery}
            onChangeText={setSearchQuery}
            onFocus={() => setIsFocused(true)}   // 聚焦事件 | Focus event
            onBlur={() => setIsFocused(false)}   // 失焦事件 | Blur event
            placeholder="Search..."
            placeholderTextColor="#999999"
            autoCapitalize="none"
            autoCorrect={false}
          />
          {searchQuery.length > 0 && (  // 条件渲染清除按钮 | Conditional render clear button
            <Pressable onPress={handleClear} style={styles.clearButton}>
              <Text style={styles.clearButtonText}>✕</Text>
            </Pressable>
          )}
        </View>
      </View>
    );
  };

  // 带搜索按钮的输入框 | Input with search button
  const SearchInputWithButton = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [submittedQuery, setSubmittedQuery] = useState('');

    const handleSearch = () => {
      setSubmittedQuery(searchQuery);  // 提交搜索 | Submit search
      console.log('Searching for:', searchQuery);
    };

    return (
      <View style={styles.searchContainer}>
        <View style={styles.searchRow}>
          <TextInput
            style={styles.searchInputWithButton}
            value={searchQuery}
            onChangeText={setSearchQuery}
            onSubmitEditing={handleSearch}  // 键盘提交事件 | Keyboard submit event
            placeholder="Enter search term..."
            placeholderTextColor="#999999"
            returnKeyType="search"
          />
          <Pressable
            style={[
              styles.searchButton,
              !searchQuery && styles.searchButtonDisabled  // 禁用状态 | Disabled state
            ]}
            onPress={handleSearch}
            disabled={!searchQuery}  // 空查询时禁用 | Disable on empty query
          >
            <Text style={styles.searchButtonText}>Search</Text>
          </Pressable>
        </View>
        {submittedQuery && (
          <Text style={styles.resultText}>
            Showing results for: "{submittedQuery}"
          </Text>
        )}
      </View>
    );
  };

  const styles = StyleSheet.create({
    searchContainer: {
      padding: 16,
    },
    searchInput: {
      height: 44,
      borderWidth: 1,
      borderColor: '#CCCCCC',
      borderRadius: 8,
      paddingHorizontal: 12,
      fontSize: 16,
      backgroundColor: '#FFFFFF',
    },
    queryDisplay: {
      marginTop: 12,
      fontSize: 14,
      color: '#666666',
    },
    searchInputWrapper: {
      flexDirection: 'row',
      alignItems: 'center',
      height: 44,
      borderWidth: 2,
      borderColor: '#E0E0E0',
      borderRadius: 8,
      paddingHorizontal: 12,
      backgroundColor: '#FFFFFF',
    },
    searchInputWrapperFocused: {
      borderColor: '#007AFF',  // 聚焦时边框颜色 | Border color when focused
    },
    searchInputField: {
      flex: 1,  // 占用剩余空间 | Take remaining space
      fontSize: 16,
      color: '#333333',
    },
    clearButton: {
      padding: 4,
      justifyContent: 'center',
      alignItems: 'center',
    },
    clearButtonText: {
      fontSize: 18,
      color: '#999999',
    },
    searchRow: {
      flexDirection: 'row',
      alignItems: 'center',
    },
    searchInputWithButton: {
      flex: 1,
      height: 44,
      borderWidth: 1,
      borderColor: '#CCCCCC',
      borderRadius: 8,
      paddingHorizontal: 12,
      fontSize: 16,
      marginRight: 8,
      backgroundColor: '#FFFFFF',
    },
    searchButton: {
      backgroundColor: '#007AFF',
      paddingHorizontal: 20,
      paddingVertical: 12,
      borderRadius: 8,
      justifyContent: 'center',
      alignItems: 'center',
    },
    searchButtonDisabled: {
      backgroundColor: '#CCCCCC',  // 禁用背景色 | Disabled background
    },
    searchButtonText: {
      color: '#FFFFFF',
      fontSize: 16,
      fontWeight: '600',
    },
    resultText: {
      marginTop: 12,
      fontSize: 14,
      color: '#666666',
      fontStyle: 'italic',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - `SearchInputWithClear`中为什么要使用`searchQuery.length > 0`来控制清除按钮显示？| Why use `searchQuery.length > 0` to control clear button visibility in `SearchInputWithClear`?
    **答案 | Answer:** 只在有输入内容时显示清除按钮，提升用户体验 | Show clear button only when there's input, improving UX by avoiding clutter
  - `onSubmitEditing`事件何时触发？| When does `onSubmitEditing` event trigger?
    **答案 | Answer:** 用户按下键盘的提交/搜索键时 | When user presses the keyboard's submit/search key
  - 为什么搜索按钮需要`disabled`属性？| Why does the search button need the `disabled` prop?
    **答案 | Answer:** 防止空查询提交，提供更好的用户体验和视觉反馈 | Prevents empty query submission, provides better UX and visual feedback

  **常见误区检查 | Common Misconception Checks:**
  - TextInput的`value`可以为`undefined`或`null`吗？| Can TextInput's `value` be `undefined` or `null`?
    **答案 | Answer:** 不推荐 | Not recommended - This makes it uncontrolled. Always use empty string `''` as initial value
  - `clearButtonMode="while-editing"`在Android上有效吗？| Does `clearButtonMode="while-editing"` work on Android?
    **答案 | Answer:** 否 | No - This is iOS-only. For Android, you must implement custom clear button like in the example

- **防抖（Debouncing）技术详解 | Debouncing Technique Explained**

  **概念定义 | Concept Definition:**
  防抖（Debouncing）是一种性能优化技术，用于限制函数执行频率。在搜索场景中，它确保只有在用户停止输入一段时间后才触发搜索请求，而不是每次按键都发送请求。| Debouncing is a performance optimization technique that limits function execution frequency. In search scenarios, it ensures search requests are triggered only after the user stops typing for a period, rather than sending a request on every keystroke.

  **核心特征 | Key Characteristics:**
  - **延迟执行**：函数调用被延迟到最后一次触发后的指定时间 | **Delayed execution**: Function call is delayed until specified time after last trigger
  - **取消机制**：新的调用会取消之前未执行的调用 | **Cancellation mechanism**: New calls cancel previous pending calls
  - **减少API调用**：显著降低网络请求次数 | **Reduced API calls**: Significantly decreases network request count
  - **用户体验优化**：避免不必要的加载状态闪烁 | **UX optimization**: Prevents unnecessary loading state flickers

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 防抖和节流（Throttling）是同一个概念吗？| Are debouncing and throttling the same concept?
     **答案 | Answer:** 否 | No - Debouncing delays until after events stop, throttling limits execution to once per interval regardless of event frequency
  2. 防抖延迟时间设置为多少毫秒合适？| What's a suitable debounce delay time in milliseconds?
     **答案 | Answer:** 通常300-500ms | Typically 300-500ms - Too short (< 200ms) provides little benefit, too long (> 800ms) feels laggy
  3. 用户每次按键都会重新计时吗？| Does the timer reset on every keystroke?
     **答案 | Answer:** 是 | Yes - Each new event clears the previous timer and starts a new one
  4. 防抖会影响用户看到自己输入的字符吗？| Does debouncing affect users seeing their typed characters?
     **答案 | Answer:** 否 | No - UI updates immediately, only the search API call is debounced

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect, useRef } from 'react';
  import { View, TextInput, Text, ActivityIndicator, StyleSheet } from 'react-native';

  // 自定义防抖Hook | Custom debounce hook
  const useDebounce = (value, delay) => {
    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(() => {
      // 设置定时器 | Set timer
      const handler = setTimeout(() => {
        setDebouncedValue(value);  // 延迟更新值 | Delay update value
      }, delay);

      // 清理函数：组件卸载或value/delay变化时清除定时器 | Cleanup: clear timer on unmount or value/delay change
      return () => {
        clearTimeout(handler);
      };
    }, [value, delay]);  // 依赖数组 | Dependency array

    return debouncedValue;
  };

  // 带防抖的搜索组件 | Search component with debounce
  const DebouncedSearchExample = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [isSearching, setIsSearching] = useState(false);

    // 使用防抖Hook，延迟500ms | Use debounce hook with 500ms delay
    const debouncedSearchQuery = useDebounce(searchQuery, 500);

    // 当防抖值变化时执行搜索 | Execute search when debounced value changes
    useEffect(() => {
      if (debouncedSearchQuery) {
        performSearch(debouncedSearchQuery);
      } else {
        setSearchResults([]);  // 清空结果 | Clear results
      }
    }, [debouncedSearchQuery]);

    const performSearch = async (query) => {
      setIsSearching(true);  // 显示加载状态 | Show loading state

      try {
        // 模拟API调用 | Simulate API call
        console.log('Searching for:', query);
        const response = await fetch(
          `https://newsapi.org/v2/everything?q=${encodeURIComponent(query)}&apiKey=YOUR_API_KEY`
        );
        const data = await response.json();
        setSearchResults(data.articles || []);
      } catch (error) {
        console.error('Search error:', error);
        setSearchResults([]);
      } finally {
        setIsSearching(false);  // 隐藏加载状态 | Hide loading state
      }
    };

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.searchInput}
          value={searchQuery}
          onChangeText={setSearchQuery}  // 立即更新UI | Immediately update UI
          placeholder="Search articles (debounced)..."
          placeholderTextColor="#999999"
        />

        <View style={styles.statusBar}>
          <Text style={styles.statusText}>
            Input: "{searchQuery}" | Debounced: "{debouncedSearchQuery}"
          </Text>
          {isSearching && (
            <ActivityIndicator size="small" color="#007AFF" style={styles.spinner} />
          )}
        </View>

        <Text style={styles.resultsCount}>
          Results: {searchResults.length}
        </Text>
      </View>
    );
  };

  // 高级防抖实现（使用useRef避免闭包问题）| Advanced debounce with useRef to avoid closure issues
  const useAdvancedDebounce = (callback, delay) => {
    const callbackRef = useRef(callback);

    // 保持callback引用最新 | Keep callback reference fresh
    useEffect(() => {
      callbackRef.current = callback;
    }, [callback]);

    const debouncedCallback = useRef(
      (() => {
        let timeoutId;
        return (...args) => {
          clearTimeout(timeoutId);  // 清除旧定时器 | Clear old timer
          timeoutId = setTimeout(() => {
            callbackRef.current(...args);  // 执行回调 | Execute callback
          }, delay);
        };
      })()
    ).current;

    return debouncedCallback;
  };

  // 使用高级防抖的搜索组件 | Search component with advanced debounce
  const AdvancedDebouncedSearch = () => {
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [isSearching, setIsSearching] = useState(false);

    const performSearch = async (query) => {
      if (!query.trim()) {
        setSearchResults([]);
        return;
      }

      setIsSearching(true);
      console.log('API call for:', query);

      try {
        // 实际API调用 | Actual API call
        await new Promise(resolve => setTimeout(resolve, 1000));  // 模拟延迟 | Simulate delay
        setSearchResults([
          { id: 1, title: `Result for "${query}" - Item 1` },
          { id: 2, title: `Result for "${query}" - Item 2` },
        ]);
      } finally {
        setIsSearching(false);
      }
    };

    // 创建防抖搜索函数 | Create debounced search function
    const debouncedSearch = useAdvancedDebounce(performSearch, 500);

    const handleSearchChange = (text) => {
      setSearchQuery(text);  // 立即更新输入框 | Update input immediately
      debouncedSearch(text);  // 防抖调用API | Debounced API call
    };

    return (
      <View style={styles.container}>
        <TextInput
          style={styles.searchInput}
          value={searchQuery}
          onChangeText={handleSearchChange}
          placeholder="Advanced debounced search..."
          placeholderTextColor="#999999"
        />

        {isSearching && (
          <View style={styles.loadingRow}>
            <ActivityIndicator size="small" color="#007AFF" />
            <Text style={styles.loadingText}>Searching...</Text>
          </View>
        )}

        <View style={styles.resultsList}>
          {searchResults.map(result => (
            <View key={result.id} style={styles.resultItem}>
              <Text>{result.title}</Text>
            </View>
          ))}
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 16,
      backgroundColor: '#FFFFFF',
    },
    searchInput: {
      height: 44,
      borderWidth: 1,
      borderColor: '#CCCCCC',
      borderRadius: 8,
      paddingHorizontal: 12,
      fontSize: 16,
      marginBottom: 12,
    },
    statusBar: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: 12,
    },
    statusText: {
      flex: 1,
      fontSize: 12,
      color: '#666666',
    },
    spinner: {
      marginLeft: 8,
    },
    resultsCount: {
      fontSize: 14,
      fontWeight: '600',
      color: '#333333',
      marginBottom: 8,
    },
    loadingRow: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: 12,
    },
    loadingText: {
      marginLeft: 8,
      fontSize: 14,
      color: '#666666',
    },
    resultsList: {
      marginTop: 8,
    },
    resultItem: {
      padding: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#F0F0F0',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在`DebouncedSearchExample`中，用户快速输入"React"会触发几次API调用？| In `DebouncedSearchExample`, how many API calls are triggered if user quickly types "React"?
    **答案 | Answer:** 1次 | Once - Only after 500ms of no typing activity
  - `useDebounce` Hook的清理函数（cleanup）何时执行？| When does the cleanup function in `useDebounce` Hook execute?
    **答案 | Answer:** 每次`value`或`delay`变化时，以及组件卸载时 | On every `value` or `delay` change, and on component unmount
  - 为什么`useAdvancedDebounce`使用`useRef`而不是直接使用`callback`？| Why does `useAdvancedDebounce` use `useRef` instead of directly using `callback`?
    **答案 | Answer:** 避免闭包陈旧值问题，确保总是使用最新的callback | Avoids stale closure issues, ensures latest callback is always used

  **常见误区检查 | Common Misconception Checks:**
  - 防抖是否会延迟用户在输入框中看到自己输入的字符？| Does debouncing delay users seeing their typed characters in the input?
    **答案 | Answer:** 否 | No - Only the search API call is debounced. The UI (TextInput value) updates immediately via `setSearchQuery`
  - 可以在TextInput的`onChangeText`中直接使用`setTimeout`实现防抖吗？| Can you implement debouncing by directly using `setTimeout` in TextInput's `onChangeText`?
    **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - You'd need manual cleanup logic. Custom hooks provide better encapsulation and reusability
