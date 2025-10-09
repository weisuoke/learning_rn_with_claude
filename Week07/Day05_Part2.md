# React Native入门 - 第7周第5天：高级FlatList模式(第2部分) | React Native Introduction - Week 7 Day 5: Advanced FlatList Patterns (Part 2)

## 详细内容(续) | Detailed Content (Continued)

### 4. onViewableItemsChanged - 追踪可见项目 | onViewableItemsChanged - Tracking Visible Items (1小时 | 1 hour)

- **onViewableItemsChanged回调概念 | onViewableItemsChanged Callback Concept**

  **概念定义 | Concept Definition:**
  onViewableItemsChanged是FlatList的一个回调函数,在列表中可见的项目发生变化时被调用。它接收一个对象参数,包含viewableItems(当前可见的项目数组)和changed(可见性状态改变的项目数组)。这个功能对于实现视频自动播放、图片懒加载、分析追踪等场景非常关键。 | onViewableItemsChanged is a FlatList callback function invoked when the visible items in the list change. It receives an object parameter containing viewableItems (array of currently visible items) and changed (array of items whose visibility status changed). This feature is crucial for implementing scenarios like video auto-play, image lazy loading, and analytics tracking.

  **核心特征 | Key Characteristics:**
  - **实时追踪** | **Real-time Tracking**: 随着用户滚动实时更新可见项目列表 | Updates the visible items list in real-time as users scroll
  - **可配置阈值** | **Configurable Threshold**: 通过viewabilityConfig定义何时算作"可见" | Defines what counts as "visible" through viewabilityConfig
  - **性能优化** | **Performance Optimized**: 使用节流机制避免过于频繁的回调 | Uses throttling mechanism to avoid excessively frequent callbacks
  - **双向信息** | **Bidirectional Information**: 提供进入视图和离开视图的项目信息 | Provides information about items entering and leaving the view

  **概念定义 | Concept Definition:**
  viewabilityConfig是配置对象,用于定义"可见性"的具体标准。它有多个配置选项,最常用的是itemVisiblePercentThreshold(项目可见百分比阈值)和viewAreaCoveragePercentThreshold(视图区域覆盖百分比阈值)。 | viewabilityConfig is a configuration object used to define specific criteria for "visibility". It has multiple configuration options, with the most commonly used being itemVisiblePercentThreshold (item visible percentage threshold) and viewAreaCoveragePercentThreshold (viewport area coverage percentage threshold).

  **核心特征 | Key Characteristics:**
  - **itemVisiblePercentThreshold** | **itemVisiblePercentThreshold**: 项目可见部分占其总高度/宽度的百分比(0-100) | Percentage of item's visible portion relative to its total height/width (0-100)
  - **viewAreaCoveragePercentThreshold** | **viewAreaCoveragePercentThreshold**: 项目占据视口区域的百分比(0-100) | Percentage of viewport area occupied by the item (0-100)
  - **minimumViewTime** | **minimumViewTime**: 项目必须保持可见的最小时间(毫秒) | Minimum time (milliseconds) an item must remain visible
  - **waitForInteraction** | **waitForInteraction**: 是否等待用户交互后才开始追踪 | Whether to wait for user interaction before starting tracking

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. onViewableItemsChanged会在每次滚动像素变化时触发吗？| Does onViewableItemsChanged trigger on every scroll pixel change?
     **答案 | Answer:** 否 | No - 只在可见项目列表实际发生变化时触发,有内置的优化机制 | Only triggers when the visible items list actually changes, with built-in optimization mechanisms
  2. itemVisiblePercentThreshold: 50是什么含义？| What does itemVisiblePercentThreshold: 50 mean?
     **答案 | Answer:** 项目至少50%的部分在视口内才算"可见" | An item is considered "visible" only when at least 50% of it is within the viewport
  3. viewableItems和changed数组有什么区别？| What's the difference between viewableItems and changed arrays?
     **答案 | Answer:** viewableItems包含当前所有可见项目,changed只包含可见性状态刚刚改变的项目 | viewableItems contains all currently visible items; changed contains only items whose visibility status just changed
  4. 是否需要在viewabilityConfig中同时设置itemVisiblePercentThreshold和viewAreaCoveragePercentThreshold？| Do we need to set both itemVisiblePercentThreshold and viewAreaCoveragePercentThreshold in viewabilityConfig?
     **答案 | Answer:** 否 | No - 通常只设置其中一个,它们是互斥的可见性判断标准 | Usually only set one; they are mutually exclusive visibility criteria

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 视频Feed自动播放示例 | Video Feed Auto-play Example
  import React, { useState, useRef, useCallback } from 'react';
  import { FlatList, View, Text, StyleSheet, Dimensions } from 'react-native';
  import Video from 'react-native-video';  // 需要安装react-native-video | Requires react-native-video installation

  const { height: SCREEN_HEIGHT } = Dimensions.get('window');
  const VIDEO_HEIGHT = SCREEN_HEIGHT * 0.7;

  // 视频数据 | Video data
  const VIDEOS_DATA = [
    { id: '1', uri: 'https://example.com/video1.mp4', title: 'Amazing Sunset Timelapse', creator: '@nature_lover' },
    { id: '2', uri: 'https://example.com/video2.mp4', title: 'Cooking Tutorial: Pasta', creator: '@chef_mike' },
    { id: '3', uri: 'https://example.com/video3.mp4', title: 'Urban Dance Performance', creator: '@dance_crew' },
    { id: '4', uri: 'https://example.com/video4.mp4', title: 'Tech Review: Latest Phone', creator: '@tech_guru' },
    { id: '5', uri: 'https://example.com/video5.mp4', title: 'Travel Vlog: Tokyo', creator: '@wanderlust' },
  ];

  // 单个视频项组件 | Individual video item component
  const VideoItem = ({ item, isVisible }) => {
    const [paused, setPaused] = useState(!isVisible);  // 根据可见性控制播放 | Control playback based on visibility

    // 当可见性改变时,更新播放状态 | Update playback state when visibility changes
    React.useEffect(() => {
      setPaused(!isVisible);
    }, [isVisible]);

    return (
      <View style={styles.videoContainer}>
        {/* 视频播放器 | Video player */}
        <Video
          source={{ uri: item.uri }}
          style={styles.video}
          paused={paused}  // 控制播放/暂停 | Control play/pause
          repeat={true}  // 循环播放 | Loop playback
          resizeMode="cover"
          volume={1.0}
          muted={false}
        />

        {/* 视频信息叠加层 | Video info overlay */}
        <View style={styles.infoOverlay}>
          <Text style={styles.title}>{item.title}</Text>
          <Text style={styles.creator}>{item.creator}</Text>
          {isVisible && <View style={styles.playingIndicator} />}
        </View>

        {/* 播放状态指示 | Playback status indicator */}
        {!isVisible && (
          <View style={styles.pausedOverlay}>
            <Text style={styles.pausedText}>⏸</Text>
          </View>
        )}
      </View>
    );
  };

  // 主组件 | Main component
  const VideoFeed = () => {
    // 追踪当前可见的视频ID | Track currently visible video IDs
    const [visibleVideoIds, setVisibleVideoIds] = useState([]);

    // viewabilityConfig配置 - 使用useRef确保引用稳定 | viewabilityConfig - use useRef for stable reference
    const viewabilityConfig = useRef({
      itemVisiblePercentThreshold: 50,  // 视频至少50%可见时算作可见 | Video is considered visible when at least 50% is visible
      minimumViewTime: 300,  // 必须保持可见至少300ms | Must remain visible for at least 300ms
    }).current;

    // 可见项目变化处理函数 | Viewable items change handler
    const onViewableItemsChanged = useRef(({ viewableItems, changed }) => {
      console.log('Viewable Items Changed');
      console.log('Currently visible:', viewableItems.length);
      console.log('Changed items:', changed.length);

      // 更新可见视频ID列表 | Update visible video IDs list
      const visibleIds = viewableItems.map((item) => item.item.id);
      setVisibleVideoIds(visibleIds);

      // 记录变化的项目(用于分析) | Log changed items (for analytics)
      changed.forEach((item) => {
        if (item.isViewable) {
          console.log(`Video ${item.item.id} entered view`);
          // 可以在这里发送分析事件 | Can send analytics event here
        } else {
          console.log(`Video ${item.item.id} left view`);
        }
      });
    }).current;

    return (
      <View style={styles.container}>
        <FlatList
          data={VIDEOS_DATA}
          renderItem={({ item }) => (
            <VideoItem
              item={item}
              isVisible={visibleVideoIds.includes(item.id)}  // 传递可见性状态 | Pass visibility status
            />
          )}
          keyExtractor={(item) => item.id}
          pagingEnabled={true}  // 启用分页滚动 | Enable paging scroll
          showsVerticalScrollIndicator={false}
          // 关键属性：可见性变化回调 | Key property: Visibility change callback
          onViewableItemsChanged={onViewableItemsChanged}
          viewabilityConfig={viewabilityConfig}
          // 性能优化 | Performance optimization
          removeClippedSubviews={true}
          maxToRenderPerBatch={2}
          windowSize={3}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#000',
    },
    videoContainer: {
      height: VIDEO_HEIGHT,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#1a1a1a',
    },
    video: {
      width: '100%',
      height: '100%',
    },
    infoOverlay: {
      position: 'absolute',
      bottom: 20,
      left: 20,
      right: 20,
    },
    title: {
      color: '#fff',
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 6,
    },
    creator: {
      color: '#fff',
      fontSize: 14,
      opacity: 0.8,
    },
    playingIndicator: {
      marginTop: 8,
      width: 40,
      height: 4,
      backgroundColor: '#E1306C',
      borderRadius: 2,
    },
    pausedOverlay: {
      ...StyleSheet.absoluteFillObject,
      backgroundColor: 'rgba(0, 0, 0, 0.5)',
      justifyContent: 'center',
      alignItems: 'center',
    },
    pausedText: {
      fontSize: 48,
      color: '#fff',
    },
  });

  export default VideoFeed;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么viewabilityConfig和onViewableItemsChanged使用useRef包裹？| Why are viewabilityConfig and onViewableItemsChanged wrapped with useRef?
    **答案 | Answer:** 确保引用稳定性,避免每次渲染创建新的引用导致FlatList重新绑定回调 | Ensures reference stability, avoiding FlatList rebinding callbacks due to new references created on each render
  - minimumViewTime: 300的作用是什么？| What is the purpose of minimumViewTime: 300?
    **答案 | Answer:** 防止快速滚动时触发过多回调,项目必须可见至少300ms才算真正"可见" | Prevents excessive callbacks during fast scrolling; item must be visible for at least 300ms to be considered truly "visible"
  - changed数组在什么场景下特别有用？| In what scenarios is the changed array particularly useful?
    **答案 | Answer:** 分析追踪、资源管理(如停止不可见视频)、动画触发等需要知道"什么改变了"的场景 | Analytics tracking, resource management (like stopping invisible videos), animation triggers, and other scenarios needing to know "what changed"

  **常见误区检查 | Common Misconception Checks:**
  - 误区：onViewableItemsChanged会自动暂停不可见的视频 | Misconception: onViewableItemsChanged automatically pauses invisible videos
    **答案 | Answer:** 错误 - 它只提供可见性信息,需要手动在回调中处理播放/暂停逻辑 | Wrong - It only provides visibility information; you must manually handle play/pause logic in the callback
  - 误区：itemVisiblePercentThreshold越小性能越好 | Misconception: Smaller itemVisiblePercentThreshold means better performance
    **答案 | Answer:** 错误 - 阈值大小不直接影响性能,应根据业务需求选择合适的值 | Wrong - Threshold size doesn't directly affect performance; choose appropriate value based on business needs

- **高级可见性追踪应用 | Advanced Visibility Tracking Applications**

  **概念定义 | Concept Definition:**
  高级可见性追踪涉及结合onViewableItemsChanged和其他状态管理技术,实现复杂的用户体验功能,如智能预加载、播放列表管理、曝光度分析、自适应内容加载等。这些技术在社交媒体、视频平台和电商应用中广泛使用。 | Advanced visibility tracking involves combining onViewableItemsChanged with other state management techniques to implement complex user experience features like intelligent preloading, playlist management, exposure analytics, and adaptive content loading. These techniques are widely used in social media, video platforms, and e-commerce applications.

  **核心特征 | Key Characteristics:**
  - **智能预加载** | **Smart Preloading**: 基于可见性预测和预加载即将进入视图的内容 | Predict and preload content about to enter view based on visibility
  - **资源优化** | **Resource Optimization**: 及时释放不可见项目的资源(如视频、大图) | Timely release resources of invisible items (like videos, large images)
  - **分析集成** | **Analytics Integration**: 追踪用户真正看到了哪些内容及停留时间 | Track what content users actually saw and dwell time
  - **个性化体验** | **Personalized Experience**: 根据可见性行为调整内容推荐 | Adjust content recommendations based on visibility behavior

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么要在项目即将进入视图时预加载内容？| Why preload content when items are about to enter the view?
     **答案 | Answer:** 提供无缝的用户体验,避免用户滚动到内容时看到加载状态 | Provides seamless user experience, avoiding users seeing loading states when scrolling to content
  2. 如何确定一个项目"即将进入视图"？| How to determine an item is "about to enter the view"?
     **答案 | Answer:** 通过viewabilityConfig的较低阈值或监听滚动位置预测 | Through lower threshold in viewabilityConfig or predicting by monitoring scroll position
  3. 停留时间(dwell time)追踪对产品有什么价值？| What value does dwell time tracking provide for products?
     **答案 | Answer:** 了解用户对内容的兴趣程度,优化推荐算法,改进内容策略 | Understand user interest level in content, optimize recommendation algorithms, improve content strategy

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 智能预加载的图片画廊 | Smart Preloading Image Gallery
  import React, { useState, useRef, useCallback } from 'react';
  import { FlatList, View, Image, Text, StyleSheet, Dimensions, ActivityIndicator } from 'react-native';

  const { width: SCREEN_WIDTH } = Dimensions.get('window');
  const IMAGE_SIZE = (SCREEN_WIDTH - 32) / 2;

  // 生成大量图片数据 | Generate large amount of image data
  const IMAGES_DATA = Array.from({ length: 100 }, (_, index) => ({
    id: `img-${index + 1}`,
    uri: `https://picsum.photos/600/600?random=${index + 1}`,
    title: `Photo ${index + 1}`,
    loaded: false,  // 追踪是否已加载 | Track if loaded
  }));

  // 智能图片组件 - 只在可见或即将可见时加载 | Smart Image component - only loads when visible or about to be visible
  const SmartImage = ({ uri, title, shouldLoad, onLoadComplete }) => {
    const [isLoaded, setIsLoaded] = useState(false);
    const [isLoading, setIsLoading] = useState(false);

    React.useEffect(() => {
      if (shouldLoad && !isLoaded && !isLoading) {
        setIsLoading(true);
      }
    }, [shouldLoad, isLoaded, isLoading]);

    return (
      <View style={styles.imageContainer}>
        {shouldLoad ? (
          <>
            <Image
              source={{ uri }}
              style={styles.image}
              onLoadStart={() => setIsLoading(true)}
              onLoadEnd={() => {
                setIsLoading(false);
                setIsLoaded(true);
                onLoadComplete();
              }}
            />
            {isLoading && (
              <View style={styles.loadingOverlay}>
                <ActivityIndicator size="small" color="#E1306C" />
              </View>
            )}
          </>
        ) : (
          <View style={[styles.image, styles.placeholder]}>
            <Text style={styles.placeholderText}>📷</Text>
          </View>
        )}
        <Text style={styles.title} numberOfLines={1}>{title}</Text>
      </View>
    );
  };

  // 主组件 | Main component
  const SmartImageGallery = () => {
    // 追踪哪些图片应该加载 | Track which images should load
    const [imagesToLoad, setImagesToLoad] = useState(new Set());
    // 追踪已完成加载的图片 | Track loaded images
    const [loadedImages, setLoadedImages] = useState(new Set());
    // 追踪曝光时间 | Track exposure time
    const exposureTimers = useRef({});

    // 配置：更宽松的阈值用于预加载 | Config: More lenient threshold for preloading
    const viewabilityConfig = useRef({
      itemVisiblePercentThreshold: 10,  // 10%可见即开始预加载 | Start preloading when 10% visible
    }).current;

    // 可见性变化处理 | Visibility change handler
    const onViewableItemsChanged = useRef(({ viewableItems, changed }) => {
      // 收集应该加载的图片ID | Collect image IDs that should load
      const idsToLoad = new Set(imagesToLoad);

      viewableItems.forEach((item) => {
        idsToLoad.add(item.item.id);

        // 开始追踪曝光时间 | Start tracking exposure time
        if (!exposureTimers.current[item.item.id]) {
          exposureTimers.current[item.item.id] = {
            startTime: Date.now(),
            itemId: item.item.id,
          };
        }
      });

      // 处理离开视图的项目 | Handle items leaving view
      changed.forEach((item) => {
        if (!item.isViewable && exposureTimers.current[item.item.id]) {
          const duration = Date.now() - exposureTimers.current[item.item.id].startTime;
          console.log(`Image ${item.item.id} was viewed for ${duration}ms`);

          // 可以在这里发送分析数据 | Can send analytics data here
          // Analytics.track('image_exposure', { imageId: item.item.id, duration });

          delete exposureTimers.current[item.item.id];
        }
      });

      setImagesToLoad(idsToLoad);
    }).current;

    // 图片加载完成回调 | Image load complete callback
    const handleImageLoadComplete = useCallback((imageId) => {
      setLoadedImages((prev) => new Set([...prev, imageId]));
    }, []);

    return (
      <View style={styles.container}>
        {/* 加载统计信息 | Loading statistics */}
        <View style={styles.statsBar}>
          <Text style={styles.statsText}>
            Loaded: {loadedImages.size} / {IMAGES_DATA.length}
          </Text>
          <Text style={styles.statsText}>
            Queued: {imagesToLoad.size}
          </Text>
        </View>

        <FlatList
          data={IMAGES_DATA}
          renderItem={({ item }) => (
            <SmartImage
              uri={item.uri}
              title={item.title}
              shouldLoad={imagesToLoad.has(item.id)}  // 基于可见性控制加载 | Control loading based on visibility
              onLoadComplete={() => handleImageLoadComplete(item.id)}
            />
          )}
          keyExtractor={(item) => item.id}
          numColumns={2}
          onViewableItemsChanged={onViewableItemsChanged}
          viewabilityConfig={viewabilityConfig}
          contentContainerStyle={styles.listContent}
          showsVerticalScrollIndicator={false}
          // 性能优化 | Performance optimization
          removeClippedSubviews={true}
          maxToRenderPerBatch={10}
          windowSize={5}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fafafa',
    },
    statsBar: {
      flexDirection: 'row',
      justifyContent: 'space-around',
      backgroundColor: '#fff',
      padding: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
    },
    statsText: {
      fontSize: 14,
      fontWeight: '600',
      color: '#262626',
    },
    listContent: {
      padding: 8,
    },
    imageContainer: {
      width: IMAGE_SIZE,
      margin: 8,
    },
    image: {
      width: '100%',
      height: IMAGE_SIZE,
      borderRadius: 8,
      backgroundColor: '#e0e0e0',
    },
    placeholder: {
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#f0f0f0',
    },
    placeholderText: {
      fontSize: 32,
      opacity: 0.3,
    },
    loadingOverlay: {
      ...StyleSheet.absoluteFillObject,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: 'rgba(255, 255, 255, 0.8)',
      borderRadius: 8,
    },
    title: {
      marginTop: 6,
      fontSize: 12,
      color: '#262626',
      textAlign: 'center',
    },
  });

  export default SmartImageGallery;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么itemVisiblePercentThreshold设置为10而不是50？| Why is itemVisiblePercentThreshold set to 10 instead of 50?
    **答案 | Answer:** 较低阈值允许提前预加载,当用户滚动到图片时已经加载完成,提供更流畅的体验 | Lower threshold allows early preloading; images are loaded when users scroll to them, providing smoother experience
  - exposureTimers的作用是什么？| What is the purpose of exposureTimers?
    **答案 | Answer:** 追踪每个图片在视图中的停留时间,用于用户行为分析和内容质量评估 | Track dwell time of each image in view, used for user behavior analysis and content quality assessment
  - Set数据结构在这里为什么比Array更合适？| Why is the Set data structure more suitable than Array here?
    **答案 | Answer:** Set自动去重且has/add/delete操作时间复杂度为O(1),比Array的includes/push更高效 | Set automatically deduplicates and has/add/delete operations have O(1) time complexity, more efficient than Array's includes/push

### 5. 复杂布局模式组合 | Complex Layout Pattern Combinations (1小时 | 1 hour)

- **嵌套FlatList模式 | Nested FlatList Pattern**

  **概念定义 | Concept Definition:**
  嵌套FlatList是指在一个FlatList的renderItem中渲染另一个FlatList,常见于分类列表、多层级内容展示等场景。虽然功能强大,但需要特别注意性能优化,避免滚动冲突和渲染问题。 | Nested FlatList refers to rendering another FlatList within a FlatList's renderItem, commonly seen in categorized lists and multi-level content displays. While powerful, it requires special attention to performance optimization, avoiding scroll conflicts and rendering issues.

  **核心特征 | Key Characteristics:**
  - **双向滚动** | **Bidirectional Scrolling**: 外层垂直滚动,内层横向滚动(最常见的组合) | Outer vertical scrolling, inner horizontal scrolling (most common combination)
  - **性能挑战** | **Performance Challenges**: 嵌套结构会放大性能问题,需要严格优化 | Nested structure amplifies performance issues, requires strict optimization
  - **滚动冲突** | **Scroll Conflicts**: 需要正确设置嵌套滚动属性避免手势冲突 | Need to correctly set nested scrolling properties to avoid gesture conflicts
  - **内存管理** | **Memory Management**: 多个列表同时存在,需要更激进的虚拟化策略 | Multiple lists exist simultaneously, requiring more aggressive virtualization strategies

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以无限层级嵌套FlatList吗？| Can FlatList be nested infinitely?
     **答案 | Answer:** 理论上可以,但实践中不应该超过2层,否则性能会急剧下降 | Theoretically yes, but in practice shouldn't exceed 2 levels, otherwise performance degrades sharply
  2. 嵌套FlatList时哪个应该是horizontal? | When nesting FlatList, which one should be horizontal?
     **答案 | Answer:** 通常内层是horizontal,外层是vertical,避免同向滚动冲突 | Usually inner is horizontal, outer is vertical, avoiding same-direction scroll conflicts
  3. 嵌套FlatList中的keyExtractor必须全局唯一吗？| Must keyExtractor in nested FlatList be globally unique?
     **答案 | Answer:** 每个FlatList内部唯一即可,但建议使用全局唯一key以避免潜在问题 | Unique within each FlatList is sufficient, but globally unique keys are recommended to avoid potential issues
  4. 如何优化嵌套FlatList的性能？| How to optimize nested FlatList performance?
     **答案 | Answer:** 使用React.memo包裹内层FlatList,设置合理的windowSize,使用getItemLayout | Wrap inner FlatList with React.memo, set reasonable windowSize, use getItemLayout

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 分类内容浏览器(如Netflix首页) | Categorized Content Browser (like Netflix homepage)
  import React, { memo } from 'react';
  import { FlatList, View, Image, Text, StyleSheet, Dimensions } from 'react-native';

  const { width: SCREEN_WIDTH } = Dimensions.get('window');
  const CARD_WIDTH = SCREEN_WIDTH * 0.4;
  const CARD_HEIGHT = CARD_WIDTH * 1.2;

  // 生成示例数据 | Generate example data
  const CATEGORIES_DATA = [
    {
      id: 'trending',
      title: 'Trending Now',
      items: Array.from({ length: 10 }, (_, i) => ({
        id: `trending-${i}`,
        title: `Trending ${i + 1}`,
        image: `https://picsum.photos/300/400?random=${i}`,
      })),
    },
    {
      id: 'action',
      title: 'Action Movies',
      items: Array.from({ length: 10 }, (_, i) => ({
        id: `action-${i}`,
        title: `Action ${i + 1}`,
        image: `https://picsum.photos/300/400?random=${i + 10}`,
      })),
    },
    {
      id: 'comedy',
      title: 'Comedy Shows',
      items: Array.from({ length: 10 }, (_, i) => ({
        id: `comedy-${i}`,
        title: `Comedy ${i + 1}`,
        image: `https://picsum.photos/300/400?random=${i + 20}`,
      })),
    },
    {
      id: 'documentary',
      title: 'Documentaries',
      items: Array.from({ length: 10 }, (_, i) => ({
        id: `doc-${i}`,
        title: `Doc ${i + 1}`,
        image: `https://picsum.photos/300/400?random=${i + 30}`,
      })),
    },
  ];

  // 内容卡片组件 | Content card component
  const ContentCard = memo(({ title, image }) => (
    <View style={styles.card}>
      <Image source={{ uri: image }} style={styles.cardImage} />
      <Text style={styles.cardTitle} numberOfLines={2}>{title}</Text>
    </View>
  ));

  // 横向滚动行组件(内层FlatList) | Horizontal scrolling row component (inner FlatList)
  const CategoryRow = memo(({ title, items }) => {
    return (
      <View style={styles.categoryContainer}>
        {/* 分类标题 | Category title */}
        <Text style={styles.categoryTitle}>{title}</Text>

        {/* 内层横向FlatList | Inner horizontal FlatList */}
        <FlatList
          data={items}
          renderItem={({ item }) => (
            <ContentCard title={item.title} image={item.image} />
          )}
          keyExtractor={(item) => item.id}
          horizontal={true}  // 内层横向滚动 | Inner horizontal scrolling
          showsHorizontalScrollIndicator={false}
          contentContainerStyle={styles.rowContent}
          // 性能优化：固定尺寸的getItemLayout | Performance optimization: getItemLayout for fixed size
          getItemLayout={(data, index) => ({
            length: CARD_WIDTH + 12,
            offset: (CARD_WIDTH + 12) * index,
            index,
          })}
          // 限制渲染窗口大小 | Limit render window size
          initialNumToRender={3}
          maxToRenderPerBatch={3}
          windowSize={5}
          // 移除屏幕外视图 | Remove off-screen views
          removeClippedSubviews={true}
        />
      </View>
    );
  });

  // 主组件(外层FlatList) | Main component (outer FlatList)
  const CategoryBrowser = () => {
    return (
      <View style={styles.container}>
        {/* 外层垂直FlatList | Outer vertical FlatList */}
        <FlatList
          data={CATEGORIES_DATA}
          renderItem={({ item }) => (
            <CategoryRow title={item.title} items={item.items} />
          )}
          keyExtractor={(item) => item.id}
          showsVerticalScrollIndicator={false}
          // 外层列表的性能优化 | Outer list performance optimization
          removeClippedSubviews={true}
          maxToRenderPerBatch={2}
          windowSize={3}
          // 重要：避免嵌套滚动冲突 | Important: Avoid nested scroll conflicts
          nestedScrollEnabled={true}  // Android需要 | Required for Android
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#141414',  // Netflix风格的深色背景 | Netflix-style dark background
    },
    categoryContainer: {
      marginBottom: 24,
    },
    categoryTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      color: '#fff',
      marginLeft: 16,
      marginBottom: 12,
    },
    rowContent: {
      paddingHorizontal: 10,
    },
    card: {
      width: CARD_WIDTH,
      marginHorizontal: 6,
    },
    cardImage: {
      width: '100%',
      height: CARD_HEIGHT,
      borderRadius: 8,
      backgroundColor: '#2a2a2a',
    },
    cardTitle: {
      marginTop: 8,
      fontSize: 14,
      color: '#fff',
      fontWeight: '500',
    },
  });

  export default CategoryBrowser;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么CategoryRow和ContentCard都使用React.memo包裹？| Why are both CategoryRow and ContentCard wrapped with React.memo?
    **答案 | Answer:** 防止不必要的重新渲染,当滚动外层列表时,内层已渲染的行不会重新渲染 | Prevents unnecessary re-renders; when scrolling outer list, already-rendered inner rows won't re-render
  - nestedScrollEnabled={true}的作用是什么？| What is the purpose of nestedScrollEnabled={true}?
    **答案 | Answer:** Android平台需要此属性以正确处理嵌套滚动,iOS不需要但设置也无害 | Android platform needs this property to correctly handle nested scrolling; iOS doesn't need it but setting it is harmless
  - 为什么内层FlatList的initialNumToRender设为3？| Why is the inner FlatList's initialNumToRender set to 3?
    **答案 | Answer:** 横向列表通常只显示2-3个item,初始渲染3个足够,减少初始渲染负担 | Horizontal lists usually only show 2-3 items; initially rendering 3 is sufficient, reducing initial render burden

  **常见误区检查 | Common Misconception Checks:**
  - 误区：嵌套的两个FlatList都可以是垂直滚动 | Misconception: Both nested FlatLists can be vertically scrolling
    **答案 | Answer:** 技术上可行但会导致严重的滚动冲突,用户体验极差,应避免 | Technically possible but causes severe scroll conflicts and extremely poor user experience; should be avoided
  - 误区：嵌套FlatList会自动优化性能 | Misconception: Nested FlatList automatically optimizes performance
    **答案 | Answer:** 错误 - 必须手动应用React.memo、getItemLayout等优化,否则性能会很差 | Wrong - Must manually apply optimizations like React.memo, getItemLayout, otherwise performance will be poor

### 6. 最佳实践总结 | Best Practices Summary (30分钟 | 30 minutes)

- **高级FlatList关键原则 | Advanced FlatList Key Principles**

  **关键原则 | Key Principles:**
  - **选择合适的布局模式** | **Choose Appropriate Layout Pattern**: 根据内容类型选择horizontal、numColumns或嵌套组合 | Choose horizontal, numColumns, or nested combinations based on content type
  - **严格的性能优化** | **Strict Performance Optimization**: 复杂布局更需要React.memo、getItemLayout、windowSize等优化 | Complex layouts require optimizations like React.memo, getItemLayout, windowSize even more
  - **精确的间距计算** | **Precise Spacing Calculation**: 使用数学公式计算item尺寸和间距,确保像素完美 | Use mathematical formulas to calculate item sizes and spacing, ensuring pixel-perfect layouts
  - **智能资源管理** | **Smart Resource Management**: 利用onViewableItemsChanged管理重资源(视频、大图) | Use onViewableItemsChanged to manage heavy resources (videos, large images)

  **实践验证问题 | Practice Verification Questions:**
  1. 什么时候应该使用横向FlatList而不是垂直FlatList？| When should you use horizontal FlatList instead of vertical FlatList?
     **答案 | Answer:** 内容需要横向浏览(如故事、轮播)或作为垂直列表中的一行时 | When content needs horizontal browsing (like stories, carousels) or as a row within a vertical list
  2. numColumns和嵌套FlatList的主要区别是什么？| What's the main difference between numColumns and nested FlatList?
     **答案 | Answer:** numColumns用于静态网格,嵌套用于每行内容不同且可独立滚动的场景 | numColumns for static grids; nested for scenarios where each row has different content and can scroll independently
  3. snapToInterval最适合哪种场景？| What scenario is snapToInterval most suitable for?
     **答案 | Answer:** 需要精确对齐的轮播、卡片翻页、全屏视频feed等场景 | Scenarios requiring precise alignment like carousels, card pagination, full-screen video feeds

## 实践项目：媒体发现应用 | Practical Project: Media Discovery App

### 目标 | Objective
构建一个综合性的媒体发现应用,整合本节课学习的所有高级FlatList技术：横向滚动、网格布局、轮播、可见性追踪和嵌套列表。这个项目将模拟真实的内容平台(如Netflix、Instagram、YouTube的组合),展示如何在生产环境中应用这些模式。 | Build a comprehensive media discovery app that integrates all advanced FlatList techniques learned in this lesson: horizontal scrolling, grid layouts, carousels, visibility tracking, and nested lists. This project will simulate a real content platform (combining Netflix, Instagram, YouTube) and demonstrate how to apply these patterns in production environments.

### 概念应用检查 | Concept Application Check
在开始项目前,请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. horizontal={true}和numColumns有什么本质区别？| What is the fundamental difference between horizontal={true} and numColumns?
   **答案 | Answer:** horizontal改变滚动方向为横向,numColumns保持垂直滚动但将项目排列成多列网格 | horizontal changes scroll direction to horizontal; numColumns maintains vertical scrolling but arranges items in multi-column grid

2. snapToInterval的值应该如何精确计算？| How should snapToInterval value be precisely calculated?
   **答案 | Answer:** 单个item的完整宽度(包括内容宽度和两侧margin) | Complete width of a single item (including content width and margins on both sides)

3. onViewableItemsChanged在什么时候触发？| When does onViewableItemsChanged trigger?
   **答案 | Answer:** 当可见项目列表发生变化时,根据viewabilityConfig的阈值条件 | When the visible items list changes, based on viewabilityConfig threshold conditions

4. 为什么嵌套FlatList需要特别的性能优化？| Why does nested FlatList require special performance optimization?
   **答案 | Answer:** 多个列表同时渲染会放大性能问题,需要React.memo和更激进的虚拟化 | Multiple lists rendering simultaneously amplifies performance issues; requires React.memo and more aggressive virtualization

### 步骤 | Steps

1. **项目初始化和数据准备** | **Project Initialization and Data Preparation**
   - 创建项目结构和导入必要的依赖
   - 准备多种类型的示例数据(故事、视频、照片、分类)
   - 设置主题色彩和样式常量

2. **实现顶部故事轮播** | **Implement Top Stories Carousel**
   - 使用horizontal FlatList创建Instagram风格的故事行
   - 应用snapToInterval实现平滑对齐
   - 添加基于滚动的缩放和透明度动画

3. **构建分类浏览器** | **Build Category Browser**
   - 使用嵌套FlatList创建Netflix风格的多行内容
   - 外层垂直,内层横向滚动
   - 优化嵌套列表性能

4. **添加照片网格** | **Add Photo Grid**
   - 使用numColumns创建3列照片网格
   - 实现智能图片预加载
   - 集成onViewableItemsChanged追踪曝光

5. **实现视频Feed** | **Implement Video Feed**
   - 创建垂直滚动的视频列表
   - 使用onViewableItemsChanged控制视频自动播放
   - 添加播放状态指示器

### 示例代码 | Example Code

```javascript
/**
 * 媒体发现应用 | Media Discovery App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 横向FlatList (horizontal scrolling) | Horizontal FlatList
 * - 网格布局 (numColumns) | Grid layout (numColumns)
 * - 轮播对齐 (snapToInterval) | Carousel alignment (snapToInterval)
 * - 可见性追踪 (onViewableItemsChanged) | Visibility tracking (onViewableItemsChanged)
 * - 嵌套列表 (nested FlatList) | Nested lists
 */

import React, { useState, useRef, memo } from 'react';
import {
  View,
  Text,
  FlatList,
  Image,
  StyleSheet,
  Dimensions,
  StatusBar,
  Animated,
  ActivityIndicator,
} from 'react-native';

const { width: SCREEN_WIDTH, height: SCREEN_HEIGHT } = Dimensions.get('window');

// ============= 数据生成 | Data Generation =============

// 故事数据 | Stories data
const STORIES_DATA = Array.from({ length: 15 }, (_, i) => ({
  id: `story-${i}`,
  username: `user_${i + 1}`,
  avatar: `https://i.pravatar.cc/150?img=${i + 1}`,
  hasStory: true,
}));

// 分类数据 | Categories data
const CATEGORIES_DATA = [
  {
    id: 'trending',
    title: 'Trending Now',
    items: Array.from({ length: 12 }, (_, i) => ({
      id: `trending-${i}`,
      title: `Trending ${i + 1}`,
      image: `https://picsum.photos/400/240?random=${i}`,
      rating: (8 + Math.random() * 2).toFixed(1),
    })),
  },
  {
    id: 'popular',
    title: 'Popular This Week',
    items: Array.from({ length: 12 }, (_, i) => ({
      id: `popular-${i}`,
      title: `Popular ${i + 1}`,
      image: `https://picsum.photos/400/240?random=${i + 20}`,
      rating: (7 + Math.random() * 3).toFixed(1),
    })),
  },
  {
    id: 'new',
    title: 'New Releases',
    items: Array.from({ length: 12 }, (_, i) => ({
      id: `new-${i}`,
      title: `New ${i + 1}`,
      image: `https://picsum.photos/400/240?random=${i + 40}`,
      rating: (6 + Math.random() * 4).toFixed(1),
    })),
  },
];

// 照片网格数据 | Photo grid data
const PHOTOS_DATA = Array.from({ length: 60 }, (_, i) => ({
  id: `photo-${i}`,
  uri: `https://picsum.photos/600/600?random=${i + 100}`,
  likes: Math.floor(Math.random() * 5000),
}));

// ============= 子组件 | Sub-components =============

// 1. 故事项组件 | Story item component
const StoryItem = memo(({ username, avatar, isFirst }) => (
  <View style={[styles.storyItem, isFirst && styles.firstStory]}>
    <View style={styles.storyAvatarContainer}>
      <Image source={{ uri: avatar }} style={styles.storyAvatar} />
    </View>
    <Text style={styles.storyUsername} numberOfLines={1}>
      {username}
    </Text>
  </View>
));

// 2. 内容卡片组件 | Content card component
const ContentCard = memo(({ title, image, rating }) => (
  <View style={styles.contentCard}>
    <Image source={{ uri: image }} style={styles.contentImage} />
    <View style={styles.ratingBadge}>
      <Text style={styles.ratingText}>⭐ {rating}</Text>
    </View>
    <Text style={styles.contentTitle} numberOfLines={2}>
      {title}
    </Text>
  </View>
));

// 3. 分类行组件 (嵌套FlatList) | Category row component (nested FlatList)
const CategoryRow = memo(({ title, items }) => (
  <View style={styles.categorySection}>
    <Text style={styles.categoryTitle}>{title}</Text>
    <FlatList
      data={items}
      renderItem={({ item }) => (
        <ContentCard title={item.title} image={item.image} rating={item.rating} />
      )}
      keyExtractor={(item) => item.id}
      horizontal={true}
      showsHorizontalScrollIndicator={false}
      contentContainerStyle={styles.categoryContent}
      getItemLayout={(data, index) => ({
        length: 220,
        offset: 220 * index,
        index,
      })}
      initialNumToRender={3}
      maxToRenderPerBatch={3}
      windowSize={5}
      removeClippedSubviews={true}
    />
  </View>
));

// 4. 照片项组件(带智能加载) | Photo item component (with smart loading)
const PhotoItem = memo(({ uri, likes, shouldLoad }) => {
  const [loaded, setLoaded] = useState(false);

  return (
    <View style={styles.photoItem}>
      {shouldLoad ? (
        <>
          <Image
            source={{ uri }}
            style={styles.photoImage}
            onLoad={() => setLoaded(true)}
          />
          {!loaded && (
            <View style={styles.photoLoader}>
              <ActivityIndicator size="small" color="#E1306C" />
            </View>
          )}
        </>
      ) : (
        <View style={[styles.photoImage, styles.photoPlaceholder]} />
      )}
      <View style={styles.photoOverlay}>
        <Text style={styles.photoLikes}>❤️ {likes}</Text>
      </View>
    </View>
  );
});

// ============= 主组件 | Main Component =============

const MediaDiscoveryApp = () => {
  // 追踪照片网格中应该加载的图片 | Track which photos should load in grid
  const [photosToLoad, setPhotosToLoad] = useState(new Set());

  // 照片可见性配置 | Photo visibility config
  const photoViewabilityConfig = useRef({
    itemVisiblePercentThreshold: 20,
  }).current;

  // 照片可见性变化处理 | Photo visibility change handler
  const onPhotoViewableItemsChanged = useRef(({ viewableItems }) => {
    const idsToLoad = new Set(viewableItems.map((item) => item.item.id));
    setPhotosToLoad(idsToLoad);
  }).current;

  // 渲染照片网格部分 | Render photo grid section
  const renderPhotoGrid = () => (
    <View style={styles.section}>
      <Text style={styles.sectionTitle}>Explore Photos</Text>
      <FlatList
        data={PHOTOS_DATA}
        renderItem={({ item }) => (
          <PhotoItem
            uri={item.uri}
            likes={item.likes}
            shouldLoad={photosToLoad.has(item.id)}
          />
        )}
        keyExtractor={(item) => item.id}
        numColumns={3}
        scrollEnabled={false}  // 嵌入主滚动中 | Embedded in main scroll
        onViewableItemsChanged={onPhotoViewableItemsChanged}
        viewabilityConfig={photoViewabilityConfig}
        columnWrapperStyle={styles.photoRow}
      />
    </View>
  );

  // 主滚动视图内容 | Main scroll view content
  return (
    <View style={styles.container}>
      <StatusBar barStyle="light-content" />

      {/* 头部 | Header */}
      <View style={styles.header}>
        <Text style={styles.headerTitle}>Discover</Text>
      </View>

      <FlatList
        ListHeaderComponent={
          <>
            {/* 1. 故事行(横向滚动) | Stories row (horizontal scroll) */}
            <View style={styles.storiesSection}>
              <FlatList
                data={STORIES_DATA}
                renderItem={({ item, index }) => (
                  <StoryItem
                    username={item.username}
                    avatar={item.avatar}
                    isFirst={index === 0}
                  />
                )}
                keyExtractor={(item) => item.id}
                horizontal={true}
                showsHorizontalScrollIndicator={false}
                contentContainerStyle={styles.storiesContent}
              />
            </View>

            {/* 2. 分类浏览器(嵌套FlatList) | Category browser (nested FlatList) */}
            {CATEGORIES_DATA.map((category) => (
              <CategoryRow
                key={category.id}
                title={category.title}
                items={category.items}
              />
            ))}

            {/* 3. 照片网格(numColumns) | Photo grid (numColumns) */}
            {renderPhotoGrid()}
          </>
        }
        data={[]}  // 空数据,只使用ListHeaderComponent | Empty data, only use ListHeaderComponent
        renderItem={null}
        keyExtractor={() => 'main'}
        showsVerticalScrollIndicator={false}
      />
    </View>
  );
};

// ============= 样式 | Styles =============

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#000',
  },
  header: {
    paddingTop: 50,
    paddingBottom: 10,
    paddingHorizontal: 16,
    backgroundColor: '#000',
  },
  headerTitle: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#fff',
  },

  // 故事样式 | Stories styles
  storiesSection: {
    height: 110,
    borderBottomWidth: 1,
    borderBottomColor: '#262626',
  },
  storiesContent: {
    paddingHorizontal: 12,
    alignItems: 'center',
  },
  storyItem: {
    alignItems: 'center',
    marginRight: 14,
    width: 70,
  },
  firstStory: {
    marginLeft: 4,
  },
  storyAvatarContainer: {
    width: 66,
    height: 66,
    borderRadius: 33,
    borderWidth: 3,
    borderColor: '#E1306C',
    justifyContent: 'center',
    alignItems: 'center',
  },
  storyAvatar: {
    width: 60,
    height: 60,
    borderRadius: 30,
  },
  storyUsername: {
    marginTop: 6,
    fontSize: 12,
    color: '#fff',
    textAlign: 'center',
  },

  // 分类样式 | Category styles
  categorySection: {
    marginBottom: 24,
  },
  categoryTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#fff',
    marginLeft: 16,
    marginBottom: 12,
  },
  categoryContent: {
    paddingHorizontal: 10,
  },
  contentCard: {
    width: 200,
    marginHorizontal: 6,
  },
  contentImage: {
    width: '100%',
    height: 120,
    borderRadius: 8,
    backgroundColor: '#2a2a2a',
  },
  ratingBadge: {
    position: 'absolute',
    top: 6,
    right: 6,
    backgroundColor: 'rgba(0, 0, 0, 0.7)',
    paddingHorizontal: 6,
    paddingVertical: 3,
    borderRadius: 4,
  },
  ratingText: {
    color: '#ffd700',
    fontSize: 11,
    fontWeight: 'bold',
  },
  contentTitle: {
    marginTop: 8,
    fontSize: 14,
    color: '#fff',
    fontWeight: '500',
  },

  // 照片网格样式 | Photo grid styles
  section: {
    marginTop: 16,
  },
  sectionTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#fff',
    marginLeft: 16,
    marginBottom: 12,
  },
  photoRow: {
    paddingHorizontal: 1,
  },
  photoItem: {
    flex: 1,
    aspectRatio: 1,
    margin: 1,
    position: 'relative',
  },
  photoImage: {
    width: '100%',
    height: '100%',
  },
  photoPlaceholder: {
    backgroundColor: '#2a2a2a',
  },
  photoLoader: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'rgba(0, 0, 0, 0.3)',
  },
  photoOverlay: {
    position: 'absolute',
    bottom: 4,
    right: 4,
    backgroundColor: 'rgba(0, 0, 0, 0.6)',
    paddingHorizontal: 6,
    paddingVertical: 2,
    borderRadius: 3,
  },
  photoLikes: {
    color: '#fff',
    fontSize: 10,
    fontWeight: 'bold',
  },
});

export default MediaDiscoveryApp;
```

### 项目完成检查 | Project Completion Check

1. 项目是否正确使用了horizontal FlatList创建故事行？| Does the project correctly use horizontal FlatList to create the stories row?
   - ✅ 使用horizontal={true}实现横向滚动 | Uses horizontal={true} for horizontal scrolling
   - ✅ 正确设置item宽度和容器高度 | Correctly sets item width and container height

2. 嵌套FlatList是否经过性能优化？| Is the nested FlatList performance-optimized?
   - ✅ 使用React.memo包裹CategoryRow和ContentCard | Wraps CategoryRow and ContentCard with React.memo
   - ✅ 实现getItemLayout跳过测量 | Implements getItemLayout to skip measurement
   - ✅ 设置合理的windowSize和initialNumToRender | Sets reasonable windowSize and initialNumToRender

3. 照片网格是否使用numColumns并集成可见性追踪？| Does the photo grid use numColumns and integrate visibility tracking?
   - ✅ 使用numColumns={3}创建3列布局 | Uses numColumns={3} to create 3-column layout
   - ✅ 通过onViewableItemsChanged智能预加载图片 | Smart preloads images through onViewableItemsChanged
   - ✅ 显示加载状态指示器 | Shows loading state indicator

4. 整体代码结构是否清晰且易于维护？| Is the overall code structure clear and maintainable?
   - ✅ 组件职责分离,每个组件有单一目的 | Component responsibilities separated, each has single purpose
   - ✅ 使用常量管理尺寸和样式值 | Uses constants to manage sizes and style values
   - ✅ 详细的注释说明关键逻辑 | Detailed comments explaining key logic

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **横向列表性能分析练习 | Horizontal List Performance Analysis Exercise**
   - **练习描述 | Exercise Description:** 创建一个包含1000个item的横向FlatList,使用React DevTools Profiler分析性能,对比使用和不使用getItemLayout的差异
   - **概念检查 | Concept Check:** getItemLayout如何影响横向列表的滚动性能？ | How does getItemLayout affect horizontal list scroll performance?
   - **学习目标 | Learning Objective:** 理解固定尺寸优化对横向滚动性能的影响 | Understand the impact of fixed-size optimization on horizontal scroll performance

2. **网格布局响应式设计练习 | Grid Layout Responsive Design Exercise**
   - **练习描述 | Exercise Description:** 实现一个根据屏幕尺寸自动调整列数的照片网格(手机2列,平板3-4列),监听屏幕方向变化
   - **概念检查 | Concept Check:** numColumns的值可以动态改变吗?如何实现？ | Can numColumns value change dynamically? How to implement?
   - **学习目标 | Learning Objective:** 掌握响应式网格布局的实现方法 | Master implementation methods for responsive grid layouts

3. **轮播指示器练习 | Carousel Indicator Exercise**
   - **练习描述 | Exercise Description:** 为snapToInterval轮播添加底部点指示器,显示当前位置,并实现点击指示器跳转到对应项目
   - **概念检查 | Concept Check:** 如何追踪当前显示的轮播项索引？ | How to track the index of the currently displayed carousel item?
   - **学习目标 | Learning Objective:** 学会结合scrollToIndex和snapToInterval创建完整轮播 | Learn to combine scrollToIndex and snapToInterval to create complete carousel

4. **可见性追踪分析仪表板练习 | Visibility Tracking Analytics Dashboard Exercise**
   - **练习描述 | Exercise Description:** 构建一个实时仪表板,显示用户浏览行为:哪些item被查看了、停留时间、滚动速度等
   - **概念检查 | Concept Check:** 如何计算item的停留时间和可见度百分比？ | How to calculate item dwell time and visibility percentage?
   - **学习目标 | Learning Objective:** 深入理解onViewableItemsChanged在用户行为分析中的应用 | Deeply understand the application of onViewableItemsChanged in user behavior analysis

5. **复杂嵌套布局练习 | Complex Nested Layout Exercise**
   - **练习描述 | Exercise Description:** 创建一个电商首页:顶部横向分类标签、每个分类下有垂直商品列表、每个商品有横向图片轮播
   - **概念检查 | Concept Check:** 3层嵌套FlatList应该如何组织以保证性能？ | How should 3-level nested FlatList be organized to ensure performance?
   - **学习目标 | Learning Objective:** 掌握复杂嵌套场景的架构设计和性能优化 | Master architectural design and performance optimization for complex nested scenarios

6. **无限轮播练习 | Infinite Carousel Exercise**
   - **练习描述 | Exercise Description:** 实现一个无限循环的轮播(滚动到最后一个后自动回到第一个),使用snapToInterval
   - **概念检查 | Concept Check:** 如何在snapToInterval中实现无限循环效果？ | How to implement infinite loop effect with snapToInterval?
   - **学习目标 | Learning Objective:** 学习循环列表的实现技巧和边界处理 | Learn implementation techniques and boundary handling for circular lists

7. **混合布局项目练习 | Mixed Layout Project Exercise**
   - **练习描述 | Exercise Description:** 在同一个FlatList中混合不同类型的item:有些是横向滚动行、有些是单个大卡片、有些是2列网格
   - **概念检查 | Concept Check:** 如何在单个FlatList中实现多种布局类型？ | How to implement multiple layout types in a single FlatList?
   - **学习目标 | Learning Objective:** 掌握基于item type的动态布局渲染 | Master dynamic layout rendering based on item type

## 学习资源 | Learning Resources

- [FlatList Official Documentation - React Native](https://reactnative.dev/docs/flatlist)
- [React Native - Optimizing Flatlist Configuration](https://reactnative.dev/docs/optimizing-flatlist-configuration)
- [React.memo API Reference](https://react.dev/reference/react/memo)
- [Animated API - React Native](https://reactnative.dev/docs/animated)
- [React Native Performance Best Practices](https://reactnative.dev/docs/performance)
- [FlashList - Shopify's High-Performance List](https://shopify.github.io/flash-list/)
- [Instagram Engineering Blog - Improving Scroll Performance](https://engineering.fb.com/)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解horizontal属性并能创建横向滚动列表 | Understand horizontal property and can create horizontal scrolling lists
- [ ] 掌握numColumns实现网格布局 | Master numColumns for grid layouts
- [ ] 能够使用snapToInterval创建对齐良好的轮播 | Can use snapToInterval to create well-aligned carousels
- [ ] 理解onViewableItemsChanged的工作原理和应用场景 | Understand how onViewableItemsChanged works and its application scenarios
- [ ] 能够配置viewabilityConfig控制可见性判断 | Can configure viewabilityConfig to control visibility criteria
- [ ] 掌握嵌套FlatList的实现和性能优化 | Master nested FlatList implementation and performance optimization
- [ ] 完成媒体发现应用实践项目 | Complete the Media Discovery App practical project
- [ ] 所有代码示例都成功运行并理解其工作原理 | All code examples run successfully and their working principles are understood
- [ ] 能够根据实际需求选择合适的高级布局模式 | Can choose appropriate advanced layout patterns based on actual requirements
- [ ] 至少完成3个扩展练习 | At least 3 extension exercises completed

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前,请确保能够回答以下问题: | Before marking as complete, ensure you can answer the following questions:
- 何时使用horizontal vs numColumns vs 嵌套FlatList？ | When to use horizontal vs numColumns vs nested FlatList?
- 如何精确计算snapToInterval的值？ | How to precisely calculate snapToInterval value?
- onViewableItemsChanged如何帮助优化资源管理？ | How does onViewableItemsChanged help optimize resource management?
- 嵌套FlatList的关键性能优化技术有哪些？ | What are the key performance optimization techniques for nested FlatList?

恭喜完成第7周第5天的学习!你现在已经掌握了FlatList的所有高级模式,能够构建复杂的、生产级的列表界面。 | Congratulations on completing Week 7 Day 5! You now master all advanced FlatList patterns and can build complex, production-grade list interfaces.
