# React Native入门 - 第7周第2天：FlatList基础与性能优化（第二部分）| React Native Introduction - Week 7 Day 2: FlatList Basics & Performance Optimization (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. 高级性能优化：getItemLayout和removeClippedSubviews | Advanced Performance Optimization: getItemLayout & removeClippedSubviews (1小时 | 1 hour)

- **getItemLayout跳过动态测量 | getItemLayout Skipping Dynamic Measurement**

  **概念定义 | Concept Definition:**
  getItemLayout是一个优化函数，它让你预先告诉FlatList每个列表项的准确尺寸（高度/宽度和偏移量），从而跳过动态测量过程。这对于固定高度的列表项特别有效，可以显著提升初始渲染速度和滚动性能，尤其是在实现scrollToIndex等功能时。

  getItemLayout is an optimization function that lets you pre-tell FlatList the exact dimensions (height/width and offset) of each list item, thereby skipping the dynamic measurement process. This is particularly effective for fixed-height list items and can significantly improve initial render speed and scrolling performance, especially when implementing features like scrollToIndex.

  **核心特征 | Key Characteristics:**
  - 避免布局计算：FlatList无需等待组件渲染后测量尺寸 | Avoids layout calculation: FlatList doesn't need to wait for component render to measure dimensions
  - 提升滚动性能：可以精确计算滚动位置和可见性 | Improves scrolling performance: can accurately calculate scroll position and visibility
  - 必要条件：列表项必须有固定且一致的尺寸 | Prerequisite: list items must have fixed and consistent dimensions
  - 函数签名：(data, index) => {length, offset, index} | Function signature: (data, index) => {length, offset, index}

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. getItemLayout适用于高度不一致的列表项吗？| Is getItemLayout suitable for list items with inconsistent heights?
     **答案 | Answer:** 否 | No - 它要求每个item有固定高度，否则计算会出错 | It requires each item to have fixed height, otherwise calculations will be wrong
  2. getItemLayout返回的offset是什么？| What is the offset returned by getItemLayout?
     **答案 | Answer:** 该item距离列表顶部的距离 | The distance of that item from the top of the list
  3. 使用getItemLayout后，FlatList还需要测量组件高度吗？| After using getItemLayout, does FlatList still need to measure component height?
     **答案 | Answer:** 否 | No - FlatList会直接使用提供的尺寸信息 | FlatList will directly use the provided dimension information
  4. 什么情况下必须使用getItemLayout？| When is getItemLayout required?
     **答案 | Answer:** 使用scrollToIndex方法时强烈建议使用 | Strongly recommended when using scrollToIndex method

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // getItemLayout优化固定高度列表 | getItemLayout optimizing fixed-height list
  import React, { useRef } from 'react';
  import { FlatList, View, Text, StyleSheet, Button } from 'react-native';

  const ITEM_HEIGHT = 80; // 固定高度 | Fixed height
  const SEPARATOR_HEIGHT = 1;
  const TOTAL_ITEM_HEIGHT = ITEM_HEIGHT + SEPARATOR_HEIGHT;

  const GetItemLayoutDemo = () => {
    const flatListRef = useRef(null);

    const data = Array.from({ length: 1000 }, (_, i) => ({
      id: `${i}`,
      title: `Fixed Height Item ${i}`,
      subtitle: `This item has exactly ${ITEM_HEIGHT}px height`,
    }));

    // ✅ getItemLayout实现 | getItemLayout implementation
    const getItemLayout = (data, index) => ({
      length: TOTAL_ITEM_HEIGHT, // item高度 | item height
      offset: TOTAL_ITEM_HEIGHT * index, // item偏移量 | item offset
      index, // item索引 | item index
    });

    // 快速跳转到任意位置 | Fast jump to any position
    const scrollToIndex = (index) => {
      flatListRef.current?.scrollToIndex({
        index,
        animated: true,
      });
    };

    const renderItem = ({ item }) => (
      <View style={styles.item}>
        <Text style={styles.title}>{item.title}</Text>
        <Text style={styles.subtitle}>{item.subtitle}</Text>
      </View>
    );

    const renderSeparator = () => <View style={styles.separator} />;

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Button title="Jump to #100" onPress={() => scrollToIndex(100)} />
          <Button title="Jump to #500" onPress={() => scrollToIndex(500)} />
          <Button title="Jump to #999" onPress={() => scrollToIndex(999)} />
        </View>

        <Text style={styles.info}>
          Each item has fixed height: {ITEM_HEIGHT}px
          {'\n'}getItemLayout enables instant scroll to any index
        </Text>

        <FlatList
          ref={flatListRef}
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          getItemLayout={getItemLayout} // ✅ 提供精确尺寸 | Provide exact dimensions
          ItemSeparatorComponent={renderSeparator}

          // getItemLayout的好处：这些功能会更快更准确 | Benefits of getItemLayout: these features work faster and more accurately
          // scrollToIndex, initialScrollIndex等 | scrollToIndex, initialScrollIndex, etc.
        />
      </View>
    );
  };

  // 对比：没有getItemLayout的情况 | Comparison: without getItemLayout
  const WithoutGetItemLayout = () => {
    const flatListRef = useRef(null);
    const data = Array.from({ length: 1000 }, (_, i) => ({ id: `${i}`, title: `Item ${i}` }));

    const scrollToIndex = (index) => {
      // ⚠️ 警告：没有getItemLayout，scrollToIndex可能不准确 | Warning: without getItemLayout, scrollToIndex may be inaccurate
      flatListRef.current?.scrollToIndex({
        index,
        animated: true,
      });
    };

    return (
      <View>
        <Button title="Scroll to 500" onPress={() => scrollToIndex(500)} />
        <FlatList
          ref={flatListRef}
          data={data}
          renderItem={({ item }) => <Text>{item.title}</Text>}
          keyExtractor={(item) => item.id}
          // ❌ 缺少getItemLayout，scrollToIndex可能滚动到错误位置 | Missing getItemLayout, scrollToIndex may scroll to wrong position
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
    },
    controls: {
      flexDirection: 'row',
      justifyContent: 'space-around',
      padding: 12,
      backgroundColor: '#f5f5f5',
    },
    info: {
      padding: 12,
      backgroundColor: '#e3f2fd',
      fontSize: 12,
      textAlign: 'center',
    },
    item: {
      height: ITEM_HEIGHT, // 确保固定高度 | Ensure fixed height
      justifyContent: 'center',
      paddingHorizontal: 16,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
    },
    subtitle: {
      fontSize: 12,
      color: '#666',
      marginTop: 4,
    },
    separator: {
      height: SEPARATOR_HEIGHT,
      backgroundColor: '#e0e0e0',
    },
  });

  export default GetItemLayoutDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 点击"Jump to #500"按钮会立即准确跳转吗？| Will clicking "Jump to #500" button immediately and accurately jump?
    **答案 | Answer:** 是的，因为getItemLayout提供了精确的offset计算 | Yes, because getItemLayout provides accurate offset calculation
  - 如果item高度实际是100px但getItemLayout返回80px会怎样？| What happens if actual item height is 100px but getItemLayout returns 80px?
    **答案 | Answer:** 布局会错乱，滚动位置计算不准确 | Layout will be messed up, scroll position calculation inaccurate

  **常见误区检查 | Common Misconception Checks:**
  - getItemLayout可以用于不同高度的列表项吗？| Can getItemLayout be used for list items with different heights?
    **答案 | Answer:** 技术上可以为每个index返回不同高度，但通常用于固定高度场景 | Technically yes you can return different heights for each index, but usually used for fixed-height scenarios
  - getItemLayout会让FlatList更快渲染吗？| Does getItemLayout make FlatList render faster?
    **答案 | Answer:** 它跳过测量阶段，提升性能，但不改变实际组件渲染速度 | It skips measurement phase improving performance, but doesn't change actual component rendering speed

- **removeClippedSubviews原生优化 | removeClippedSubviews Native Optimization**

  **概念定义 | Concept Definition:**
  removeClippedSubviews是一个Android特有的优化属性，当设置为true时，FlatList会在原生层直接移除（detach）屏幕外的视图，而不仅仅是在JavaScript层卸载组件。这可以进一步减少内存占用和提升性能，特别是在处理复杂列表项时。

  removeClippedSubviews is an Android-specific optimization property that, when set to true, causes FlatList to directly remove (detach) off-screen views at the native layer, not just unmount components at the JavaScript layer. This can further reduce memory usage and improve performance, especially when handling complex list items.

  **核心特征 | Key Characteristics:**
  - 主要针对Android平台：iOS效果有限或可能导致问题 | Primarily for Android platform: limited effect on iOS or may cause issues
  - 原生层优化：比JavaScript层的虚拟化更深层次 | Native layer optimization: deeper than JavaScript layer virtualization
  - 可能的副作用：某些复杂布局可能出现闪烁或渲染问题 | Possible side effects: some complex layouts may experience flickering or rendering issues
  - 适用场景：列表项复杂、性能瓶颈在原生渲染层时 | Use cases: when list items are complex and performance bottleneck is at native rendering layer

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. removeClippedSubviews在iOS上有用吗？| Is removeClippedSubviews useful on iOS?
     **答案 | Answer:** 用处有限，主要是Android优化 | Limited usefulness, primarily Android optimization
  2. removeClippedSubviews会影响JavaScript层的组件吗？| Does removeClippedSubviews affect components at JavaScript layer?
     **答案 | Answer:** 否，它在原生层操作，JavaScript组件已经被虚拟化卸载 | No, it operates at native layer, JavaScript components already unmounted by virtualization
  3. 所有FlatList都应该使用removeClippedSubviews吗？| Should all FlatLists use removeClippedSubviews?
     **答案 | Answer:** 否，先测试是否真的提升性能，可能引入bug | No, test first if it really improves performance, may introduce bugs
  4. removeClippedSubviews和虚拟化是同一个概念吗？| Are removeClippedSubviews and virtualization the same concept?
     **答案 | Answer:** 否，虚拟化在JavaScript层，removeClippedSubviews在原生层 | No, virtualization is at JavaScript layer, removeClippedSubviews at native layer

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // removeClippedSubviews优化演示 | removeClippedSubviews optimization demo
  import React, { useState } from 'react';
  import { FlatList, View, Text, Image, StyleSheet, Switch, Platform } from 'react-native';

  const RemoveClippedSubviewsDemo = () => {
    const [removeClipped, setRemoveClipped] = useState(false);

    // 复杂的列表项数据 | Complex list item data
    const data = Array.from({ length: 500 }, (_, i) => ({
      id: `${i}`,
      title: `Complex Item ${i}`,
      description: 'This item contains images and complex layout',
      imageUrl: `https://picsum.photos/100/100?random=${i}`,
    }));

    const renderItem = ({ item }) => (
      <View style={styles.complexItem}>
        <Image
          source={{ uri: item.imageUrl }}
          style={styles.image}
        />
        <View style={styles.content}>
          <Text style={styles.title}>{item.title}</Text>
          <Text style={styles.description}>{item.description}</Text>
          <View style={styles.metadata}>
            <Text style={styles.meta}>Metadata 1</Text>
            <Text style={styles.meta}>Metadata 2</Text>
            <Text style={styles.meta}>Metadata 3</Text>
          </View>
        </View>
      </View>
    );

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Text style={styles.label}>
            removeClippedSubviews: {removeClipped ? 'ON' : 'OFF'}
          </Text>
          <Switch
            value={removeClipped}
            onValueChange={setRemoveClipped}
          />
          {Platform.OS === 'ios' && (
            <Text style={styles.warning}>
              ⚠️ Limited effect on iOS
            </Text>
          )}
        </View>

        <Text style={styles.info}>
          {Platform.OS === 'android'
            ? 'On Android: Enable to detach off-screen views at native layer'
            : 'On iOS: This optimization has limited effect'}
        </Text>

        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          removeClippedSubviews={removeClipped} // ✅ 动态切换观察效果 | Dynamically toggle to observe effect

          // 其他性能优化配合使用 | Use with other performance optimizations
          windowSize={10}
          maxToRenderPerBatch={5}
          initialNumToRender={10}
        />
      </View>
    );
  };

  // 何时使用removeClippedSubviews的指南 | Guide for when to use removeClippedSubviews
  const OptimizationGuide = () => {
    return (
      <View style={styles.guide}>
        <Text style={styles.guideTitle}>使用removeClippedSubviews的场景：</Text>
        <Text style={styles.guideItem}>✅ Android平台的应用</Text>
        <Text style={styles.guideItem}>✅ 列表项包含复杂原生组件（Image, Video等）</Text>
        <Text style={styles.guideItem}>✅ 已经应用其他优化但仍有性能问题</Text>
        <Text style={styles.guideItem}>✅ 经过测试确认能提升性能</Text>

        <Text style={styles.guideTitle}>不推荐使用的场景：</Text>
        <Text style={styles.guideItem}>❌ iOS应用（效果有限）</Text>
        <Text style={styles.guideItem}>❌ 列表项有复杂动画或过渡效果</Text>
        <Text style={styles.guideItem}>❌ 出现渲染闪烁或布局问题</Text>
        <Text style={styles.guideItem}>❌ 简单列表不需要此优化</Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
    },
    controls: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'space-between',
      padding: 16,
      backgroundColor: '#f5f5f5',
    },
    label: {
      fontSize: 16,
      fontWeight: 'bold',
    },
    warning: {
      fontSize: 12,
      color: '#ff9800',
      marginLeft: 8,
    },
    info: {
      padding: 12,
      backgroundColor: '#e3f2fd',
      fontSize: 12,
      textAlign: 'center',
    },
    complexItem: {
      flexDirection: 'row',
      padding: 16,
      backgroundColor: '#fff',
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
    },
    image: {
      width: 100,
      height: 100,
      borderRadius: 8,
      marginRight: 12,
    },
    content: {
      flex: 1,
      justifyContent: 'center',
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    description: {
      fontSize: 14,
      color: '#666',
      marginBottom: 8,
    },
    metadata: {
      flexDirection: 'row',
    },
    meta: {
      fontSize: 12,
      color: '#999',
      marginRight: 8,
    },
    guide: {
      padding: 16,
    },
    guideTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginTop: 12,
      marginBottom: 8,
    },
    guideItem: {
      fontSize: 14,
      marginBottom: 4,
      paddingLeft: 8,
    },
  });

  export default RemoveClippedSubviewsDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在Android设备上启用removeClippedSubviews后滚动会更流畅吗？| Will scrolling be smoother on Android device after enabling removeClippedSubviews?
    **答案 | Answer:** 可能会，特别是列表项包含图片等复杂组件时 | Possibly, especially when list items contain complex components like images
  - iOS上启用这个属性有风险吗？| Is there risk enabling this property on iOS?
    **答案 | Answer:** 可能导致渲染问题，建议只在Android使用 | May cause rendering issues, recommended to use only on Android

  **常见误区检查 | Common Misconception Checks:**
  - removeClippedSubviews会减少渲染的组件数量吗？| Does removeClippedSubviews reduce the number of rendered components?
    **答案 | Answer:** 否，组件数量由虚拟化控制，它只是在原生层detach视图 | No, component count is controlled by virtualization, it only detaches views at native layer
  - 所有性能问题都能通过removeClippedSubviews解决吗？| Can all performance issues be solved by removeClippedSubviews?
    **答案 | Answer:** 否，它只是众多优化手段之一，需要综合使用多种优化 | No, it's just one of many optimization techniques, need comprehensive use of multiple optimizations

### 5. React DevTools Profiler性能监控 | React DevTools Profiler Performance Monitoring (45分钟 | 45 minutes)

- **使用Profiler分析FlatList性能 | Using Profiler to Analyze FlatList Performance**

  **概念定义 | Concept Definition:**
  React DevTools Profiler是React官方提供的性能分析工具，可以记录组件渲染时间、识别性能瓶颈、查看哪些组件重新渲染以及渲染的原因。在优化FlatList时，Profiler能帮助你验证优化是否有效，找出不必要的重渲染。

  React DevTools Profiler is an official performance analysis tool provided by React that can record component render times, identify performance bottlenecks, view which components re-render and why. When optimizing FlatList, Profiler helps you verify if optimizations are effective and find unnecessary re-renders.

  **核心特征 | Key Characteristics:**
  - 火焰图（Flame Graph）：可视化组件渲染时间和嵌套关系 | Flame Graph: visualizes component render time and nesting relationships
  - 排序图（Ranked Graph）：按渲染时间排序组件，快速找出慢组件 | Ranked Graph: sorts components by render time, quickly finds slow components
  - 提交（Commits）时间线：查看每次state更新导致的渲染 | Commits timeline: view renders caused by each state update
  - Why did this render：解释组件为什么重新渲染 | Why did this render: explains why component re-rendered

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Profiler会影响应用的实际性能吗？| Does Profiler affect actual app performance?
     **答案 | Answer:** 会有轻微影响，但足以识别性能问题 | Has slight impact, but enough to identify performance issues
  2. 火焰图中颜色越黄/红代表什么？| What does yellower/redder color in flame graph represent?
     **答案 | Answer:** 渲染时间越长 | Longer render time
  3. 如何判断FlatList优化是否成功？| How to judge if FlatList optimization is successful?
     **答案 | Answer:** 对比优化前后的Profiler数据，查看渲染时间和重渲染次数 | Compare Profiler data before and after optimization, check render time and re-render count
  4. Profiler只能在开发环境使用吗？| Can Profiler only be used in development environment?
     **答案 | Answer:** 主要用于开发，生产环境性能分析需要其他工具 | Mainly for development, production performance analysis needs other tools

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 使用Profiler监控FlatList性能 | Using Profiler to monitor FlatList performance
  import React, { useState, useCallback, memo, Profiler } from 'react';
  import { FlatList, View, Text, StyleSheet, Button, TouchableOpacity } from 'react-native';

  // 性能监控回调 | Performance monitoring callback
  const onRenderCallback = (
    id, // 被监控组件的id | id of monitored component
    phase, // "mount" 或 "update" | "mount" or "update"
    actualDuration, // 本次渲染耗时 | time spent on this render
    baseDuration, // 不使用memoization的预估时间 | estimated time without memoization
    startTime, // 开始渲染的时间 | when render started
    commitTime, // 提交的时间 | when committed
    interactions // 本次更新相关的interactions | interactions related to this update
  ) => {
    console.log(`[Profiler] ${id} - ${phase}`);
    console.log(`  Actual Duration: ${actualDuration.toFixed(2)}ms`);
    console.log(`  Base Duration: ${baseDuration.toFixed(2)}ms`);
    console.log(`  Improvement: ${((baseDuration - actualDuration) / baseDuration * 100).toFixed(2)}%`);
  };

  // 未优化的列表项 | Unoptimized list item
  const UnoptimizedItem = ({ item, onPress }) => {
    console.log(`[Unoptimized] Rendering: ${item.id}`);
    return (
      <TouchableOpacity style={styles.item} onPress={() => onPress(item.id)}>
        <Text>{item.title}</Text>
      </TouchableOpacity>
    );
  };

  // 优化的列表项 | Optimized list item
  const OptimizedItem = memo(({ item, onPress }) => {
    console.log(`[Optimized] Rendering: ${item.id}`);
    return (
      <TouchableOpacity style={styles.item} onPress={() => onPress(item.id)}>
        <Text>{item.title}</Text>
      </TouchableOpacity>
    );
  });

  const ProfilerDemo = () => {
    const [data, setData] = useState(
      Array.from({ length: 50 }, (_, i) => ({
        id: `${i}`,
        title: `Item ${i}`,
        count: 0,
      }))
    );

    const [useOptimized, setUseOptimized] = useState(false);

    const handlePress = useCallback((id) => {
      setData(prevData =>
        prevData.map(item =>
          item.id === id ? { ...item, count: item.count + 1 } : item
        )
      );
    }, []);

    const renderOptimizedItem = useCallback(({ item }) => (
      <OptimizedItem item={item} onPress={handlePress} />
    ), [handlePress]);

    const renderUnoptimizedItem = ({ item }) => (
      <UnoptimizedItem item={item} onPress={handlePress} />
    );

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Button
            title={useOptimized ? "Using Optimized ✅" : "Using Unoptimized ❌"}
            onPress={() => setUseOptimized(!useOptimized)}
          />
        </View>

        <Text style={styles.instructions}>
          1. Open React DevTools Profiler tab
          {'\n'}2. Click "Record" button
          {'\n'}3. Tap any list item
          {'\n'}4. Stop recording and analyze flame graph
          {'\n'}5. Compare optimized vs unoptimized versions
        </Text>

        {/* 包裹Profiler组件监控性能 | Wrap with Profiler component to monitor performance */}
        <Profiler id="FlatList" onRender={onRenderCallback}>
          <FlatList
            data={data}
            renderItem={useOptimized ? renderOptimizedItem : renderUnoptimizedItem}
            keyExtractor={(item) => item.id}
            ListHeaderComponent={() => (
              <Text style={styles.header}>
                {useOptimized
                  ? "✅ Optimized: Only tapped item re-renders"
                  : "❌ Unoptimized: All items may re-render"}
              </Text>
            )}
          />
        </Profiler>
      </View>
    );
  };

  // Profiler最佳实践指南 | Profiler best practices guide
  const ProfilerGuide = () => {
    return (
      <View style={styles.guide}>
        <Text style={styles.guideTitle}>使用React DevTools Profiler的步骤：</Text>

        <Text style={styles.step}>1. 安装React DevTools</Text>
        <Text style={styles.stepDetail}>
          浏览器扩展或standalone应用（npx react-devtools）
        </Text>

        <Text style={styles.step}>2. 打开Profiler标签</Text>
        <Text style={styles.stepDetail}>
          确保在Development模式下运行应用
        </Text>

        <Text style={styles.step}>3. 开始记录</Text>
        <Text style={styles.stepDetail}>
          点击录制按钮，执行要分析的操作（如滚动列表）
        </Text>

        <Text style={styles.step}>4. 停止并分析</Text>
        <Text style={styles.stepDetail}>
          查看火焰图识别慢组件，检查Ranked视图找出最耗时的渲染
        </Text>

        <Text style={styles.step}>5. 应用优化</Text>
        <Text style={styles.stepDetail}>
          根据分析结果添加React.memo、useCallback等优化
        </Text>

        <Text style={styles.step}>6. 再次测试</Text>
        <Text style={styles.stepDetail}>
          对比优化前后的Profiler数据，验证改进效果
        </Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
    },
    controls: {
      padding: 16,
      backgroundColor: '#f5f5f5',
    },
    instructions: {
      padding: 16,
      backgroundColor: '#fff3e0',
      fontSize: 13,
      lineHeight: 20,
    },
    header: {
      padding: 16,
      backgroundColor: '#e3f2fd',
      fontSize: 14,
      fontWeight: 'bold',
      textAlign: 'center',
    },
    item: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
      backgroundColor: '#fff',
    },
    guide: {
      padding: 16,
    },
    guideTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 16,
    },
    step: {
      fontSize: 16,
      fontWeight: 'bold',
      marginTop: 12,
      marginBottom: 4,
    },
    stepDetail: {
      fontSize: 14,
      color: '#666',
      marginBottom: 8,
      paddingLeft: 16,
    },
  });

  export default ProfilerDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在Profiler中如何区分优化版本和未优化版本的性能差异？| How to distinguish performance difference between optimized and unoptimized versions in Profiler?
    **答案 | Answer:** 查看actualDuration和渲染的组件数量，优化版本应该更少更快 | Check actualDuration and number of rendered components, optimized version should be fewer and faster
  - onRenderCallback中的baseDuration代表什么？| What does baseDuration in onRenderCallback represent?
    **答案 | Answer:** 不使用memoization等优化时的预估渲染时间 | Estimated render time without optimizations like memoization

  **常见误区检查 | Common Misconception Checks:**
  - Profiler可以在生产环境使用吗？| Can Profiler be used in production environment?
    **答案 | Answer:** Profiler组件可以，但React DevTools主要用于开发环境 | Profiler component yes, but React DevTools mainly for development
  - actualDuration为0ms说明组件没有渲染吗？| Does actualDuration of 0ms mean component didn't render?
    **答案 | Answer:** 可能是渲染极快或被优化跳过，需要结合phase查看 | May be extremely fast render or optimized skip, need to check with phase

### 6. 性能优化最佳实践总结 | Performance Optimization Best Practices Summary (30分钟 | 30 minutes)

- **FlatList性能优化清单 | FlatList Performance Optimization Checklist**

  **关键原则 | Key Principles:**
  - 始终使用keyExtractor提供稳定唯一的key | Always use keyExtractor to provide stable unique keys
  - 用React.memo包裹列表项组件避免不必要的重渲染 | Wrap list item components with React.memo to avoid unnecessary re-renders
  - 用useCallback稳定传递给子组件的函数引用 | Use useCallback to stabilize function references passed to child components
  - 固定高度列表使用getItemLayout优化 | Use getItemLayout for fixed-height lists
  - 根据列表复杂度调整windowSize、initialNumToRender等参数 | Adjust windowSize, initialNumToRender based on list complexity
  - Android平台可尝试removeClippedSubviews | Try removeClippedSubviews on Android platform
  - 使用React DevTools Profiler验证优化效果 | Use React DevTools Profiler to verify optimization effects

  **实践验证问题 | Practice Verification Questions:**
  1. 什么时候应该优先使用getItemLayout？| When should you prioritize using getItemLayout?
     **答案 | Answer:** 列表项有固定高度且需要scrollToIndex功能时 | When list items have fixed height and need scrollToIndex functionality
  2. React.memo和useCallback必须一起使用吗？| Must React.memo and useCallback be used together?
     **答案 | Answer:** 不是必须，但配合使用才能发挥最大效果 | Not required, but work together for maximum effect
  3. 性能优化的优先级是什么？| What's the priority of performance optimizations?
     **答案 | Answer:** 先组件级优化（memo/useCallback），再配置优化（windowSize等），最后原生优化（removeClippedSubviews）| First component-level (memo/useCallback), then configuration (windowSize etc.), finally native (removeClippedSubviews)

## 实践项目：高性能商品列表 | Practical Project: High-Performance Product List

### 目标 | Objective
构建一个包含1000+商品的电商列表应用，应用本日所学的所有性能优化技术，实现流畅的60fps滚动体验。

Build an e-commerce list application with 1000+ products, applying all performance optimization techniques learned today to achieve smooth 60fps scrolling experience.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. FlatList的虚拟化机制如何减少内存占用？| How does FlatList's virtualization mechanism reduce memory usage?
   **答案 | Answer:** 只保持可见区域的组件在内存中，离屏组件被卸载 | Only keeps components in visible area in memory, off-screen components are unmounted

2. 为什么renderItem需要用useCallback包裹？| Why does renderItem need to be wrapped with useCallback?
   **答案 | Answer:** 避免每次渲染创建新函数引用，配合React.memo减少子组件重渲染 | Avoid creating new function reference on each render, work with React.memo to reduce child component re-renders

3. getItemLayout的三个返回值分别代表什么？| What do the three return values of getItemLayout represent?
   **答案 | Answer:** length（item高度），offset（距顶部距离），index（索引）| length (item height), offset (distance from top), index (index)

### 步骤 | Steps

1. **创建项目结构和数据 | Create Project Structure and Data**
   - 生成1000条模拟商品数据（id、名称、价格、图片、评分）
   - 定义ProductItem组件结构

2. **实现基础FlatList | Implement Basic FlatList**
   - 使用FlatList渲染商品列表
   - 提供稳定的keyExtractor

3. **应用组件级优化 | Apply Component-Level Optimization**
   - 用React.memo包裹ProductItem
   - 用useCallback包裹所有回调函数

4. **配置性能参数 | Configure Performance Parameters**
   - 设置合适的windowSize（如10）
   - 配置initialNumToRender（如15）
   - 配置maxToRenderPerBatch（如10）

5. **添加getItemLayout | Add getItemLayout**
   - 确保ProductItem有固定高度
   - 实现getItemLayout函数

6. **性能测试和调优 | Performance Testing and Tuning**
   - 使用React DevTools Profiler分析性能
   - 根据分析结果调整参数

### 示例代码 | Example Code

```javascript
/**
 * 高性能商品列表项目 | High-Performance Product List Project
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - FlatList虚拟化渲染 | FlatList virtualization rendering
 * - React.memo组件优化 | React.memo component optimization
 * - useCallback函数稳定化 | useCallback function stabilization
 * - getItemLayout尺寸优化 | getItemLayout dimension optimization
 * - 性能参数配置 | Performance parameter configuration
 */

import React, { useState, useCallback, memo, useMemo } from 'react';
import {
  FlatList,
  View,
  Text,
  Image,
  StyleSheet,
  TouchableOpacity,
  StatusBar,
} from 'react-native';

// 生成模拟商品数据 | Generate mock product data
const generateProducts = (count) => {
  return Array.from({ length: count }, (_, i) => ({
    id: `product-${i}`,
    name: `Product ${i + 1}`,
    price: (Math.random() * 100 + 10).toFixed(2),
    rating: (Math.random() * 2 + 3).toFixed(1), // 3.0-5.0
    imageUrl: `https://picsum.photos/80/80?random=${i}`,
    inStock: Math.random() > 0.2, // 80% 有货 | 80% in stock
  }));
};

// ✅ 优化：使用React.memo的商品项组件 | Optimized: Product item component with React.memo
const ProductItem = memo(({ product, onPress, onFavorite }) => {
  console.log(`Rendering product: ${product.id}`); // 监控渲染 | Monitor renders

  return (
    <TouchableOpacity
      style={styles.productItem}
      onPress={() => onPress(product.id)}
      activeOpacity={0.7}
    >
      <Image
        source={{ uri: product.imageUrl }}
        style={styles.productImage}
      />

      <View style={styles.productInfo}>
        <Text style={styles.productName} numberOfLines={2}>
          {product.name}
        </Text>

        <View style={styles.ratingContainer}>
          <Text style={styles.rating}>⭐ {product.rating}</Text>
          <Text style={[styles.stock, !product.inStock && styles.outOfStock]}>
            {product.inStock ? 'In Stock' : 'Out of Stock'}
          </Text>
        </View>

        <Text style={styles.price}>${product.price}</Text>
      </View>

      <TouchableOpacity
        style={styles.favoriteButton}
        onPress={() => onFavorite(product.id)}
      >
        <Text style={styles.favoriteIcon}>♡</Text>
      </TouchableOpacity>
    </TouchableOpacity>
  );
});

// 主应用组件 | Main app component
const HighPerformanceProductList = () => {
  // 使用useMemo避免重新生成数据 | Use useMemo to avoid regenerating data
  const products = useMemo(() => generateProducts(1000), []);

  // ✅ 稳定的回调函数 | Stable callback functions
  const handleProductPress = useCallback((productId) => {
    console.log(`Product pressed: ${productId}`);
    // 实际应用：导航到商品详情 | Real app: navigate to product detail
  }, []);

  const handleFavorite = useCallback((productId) => {
    console.log(`Favorited: ${productId}`);
    // 实际应用：添加到收藏夹 | Real app: add to favorites
  }, []);

  // ✅ 使用useCallback的renderItem | renderItem with useCallback
  const renderItem = useCallback(({ item }) => (
    <ProductItem
      product={item}
      onPress={handleProductPress}
      onFavorite={handleFavorite}
    />
  ), [handleProductPress, handleFavorite]);

  // ✅ getItemLayout优化：固定高度120px | getItemLayout optimization: fixed height 120px
  const ITEM_HEIGHT = 120;
  const getItemLayout = useCallback((data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  }), []);

  // 列表头部 | List header
  const renderHeader = () => (
    <View style={styles.header}>
      <Text style={styles.headerTitle}>Premium Products</Text>
      <Text style={styles.headerSubtitle}>1000+ items with optimized performance</Text>
    </View>
  );

  return (
    <View style={styles.container}>
      <StatusBar barStyle="dark-content" />

      <FlatList
        data={products}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}

        // ✅ 性能优化配置 | Performance optimization configuration
        getItemLayout={getItemLayout}
        windowSize={10} // 减少内存占用 | Reduce memory footprint
        initialNumToRender={15} // 首屏渲染15个 | Initial render 15 items
        maxToRenderPerBatch={10} // 每批最多10个 | Max 10 per batch
        removeClippedSubviews={true} // Android优化 | Android optimization

        // UI组件 | UI components
        ListHeaderComponent={renderHeader}

        // 性能优化：减少更新频率 | Performance: reduce update frequency
        updateCellsBatchingPeriod={50}
      />
    </View>
  );
};

// 固定高度确保getItemLayout准确 | Fixed height ensures getItemLayout accuracy
const ITEM_HEIGHT = 120;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  header: {
    padding: 20,
    backgroundColor: '#fff',
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 4,
  },
  headerSubtitle: {
    fontSize: 14,
    color: '#666',
  },
  productItem: {
    height: ITEM_HEIGHT, // ✅ 固定高度 | Fixed height
    flexDirection: 'row',
    backgroundColor: '#fff',
    marginVertical: 4,
    marginHorizontal: 12,
    borderRadius: 8,
    padding: 12,
    alignItems: 'center',
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.1,
    shadowRadius: 2,
    elevation: 2,
  },
  productImage: {
    width: 80,
    height: 80,
    borderRadius: 8,
    backgroundColor: '#e0e0e0',
  },
  productInfo: {
    flex: 1,
    marginLeft: 12,
    justifyContent: 'center',
  },
  productName: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 4,
    color: '#333',
  },
  ratingContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    marginBottom: 4,
  },
  rating: {
    fontSize: 14,
    color: '#ff9800',
    marginRight: 12,
  },
  stock: {
    fontSize: 12,
    color: '#4caf50',
    fontWeight: '500',
  },
  outOfStock: {
    color: '#f44336',
  },
  price: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#2196F3',
  },
  favoriteButton: {
    padding: 8,
  },
  favoriteIcon: {
    fontSize: 24,
    color: '#ff4081',
  },
});

export default HighPerformanceProductList;
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确应用了FlatList虚拟化渲染？| Does the project correctly apply FlatList virtualization rendering?
   - ✅ 查看console日志，确认只有可见项在渲染
2. React.memo的使用是否符合最佳实践？| Does the usage of React.memo follow best practices?
   - ✅ ProductItem被memo包裹，且props稳定
3. 代码是否体现了所有性能优化概念？| Does the code reflect all performance optimization concepts?
   - ✅ 包含useCallback、getItemLayout、windowSize等所有优化

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **虚拟化理解强化练习 | Virtualization Understanding Reinforcement Exercise**
   - **练习描述 | Exercise Description:** 修改项目，添加日志记录当前渲染的items数量，并在不同windowSize下观察变化
   - **概念检查 | Concept Check:** windowSize如何影响内存中的组件数量？
   - **学习目标 | Learning Objective:** 深入理解虚拟化窗口机制

2. **React.memo深度练习 | React.memo Depth Exercise**
   - **练习描述 | Exercise Description:** 为ProductItem添加自定义比较函数，只在price变化时重渲染
   - **概念检查 | Concept Check:** React.memo的第二个参数如何工作？
   - **学习目标 | Learning Objective:** 掌握自定义memo比较逻辑

3. **性能参数调优练习 | Performance Parameter Tuning Exercise**
   - **练习描述 | Exercise Description:** 创建三种配置（省内存、平衡、高流畅度），让用户切换对比
   - **概念检查 | Concept Check:** 各参数之间如何权衡？
   - **学习目标 | Learning Objective:** 培养性能调优思维

4. **Profiler分析练习 | Profiler Analysis Exercise**
   - **练习描述 | Exercise Description:** 使用React DevTools Profiler记录优化前后的数据，生成性能报告
   - **概念检查 | Concept Check:** 如何量化优化效果？
   - **学习目标 | Learning Objective:** 学会科学评估性能

5. **getItemLayout高级练习 | getItemLayout Advanced Exercise**
   - **练习描述 | Exercise Description:** 实现可变高度的section列表，为每个section和item提供正确的layout
   - **概念检查 | Concept Check:** 如何为不同类型的item计算offset？
   - **学习目标 | Learning Objective:** 掌握复杂场景下的layout优化

6. **批量渲染实验 | Batch Rendering Experiment**
   - **练习描述 | Exercise Description:** 创建可视化工具，实时显示每批渲染了多少个items
   - **概念检查 | Concept Check:** maxToRenderPerBatch如何影响滚动体验？
   - **学习目标 | Learning Objective:** 理解批量渲染机制

7. **综合优化挑战 | Comprehensive Optimization Challenge**
   - **练习描述 | Exercise Description:** 优化一个包含图片、视频缩略图的复杂列表，达到60fps
   - **概念检查 | Concept Check:** 如何综合应用所有优化技术？
   - **学习目标 | Learning Objective:** 提升解决真实性能问题的能力

## 学习资源 | Learning Resources
- [FlatList Official Documentation](https://reactnative.dev/docs/flatlist)
- [Optimizing FlatList Configuration](https://reactnative.dev/docs/optimizing-flatlist-configuration)
- [React.memo API Reference](https://react.dev/reference/react/memo)
- [useCallback Hook Guide](https://react.dev/reference/react/useCallback)
- [React DevTools Profiler](https://react.dev/learn/react-developer-tools)
- [Performance Optimization in React Native](https://reactnative.dev/docs/performance)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解FlatList虚拟化渲染原理和优势 | Understand FlatList virtualization rendering principles and advantages
- [ ] 掌握data、renderItem、keyExtractor三个核心props | Master the three core props: data, renderItem, keyExtractor
- [ ] 能够配置windowSize、initialNumToRender等性能参数 | Can configure performance parameters like windowSize, initialNumToRender
- [ ] 熟练使用React.memo优化列表项组件 | Proficient in using React.memo to optimize list item components
- [ ] 掌握useCallback稳定函数引用的技巧 | Master the technique of stabilizing function references with useCallback
- [ ] 能够为固定高度列表实现getItemLayout | Can implement getItemLayout for fixed-height lists
- [ ] 了解removeClippedSubviews的适用场景 | Understand the applicable scenarios for removeClippedSubviews
- [ ] 会使用React DevTools Profiler分析性能 | Can use React DevTools Profiler to analyze performance
- [ ] 完成高性能商品列表实践项目 | Complete high-performance product list practical project
- [ ] 能够识别和解决FlatList性能瓶颈 | Can identify and solve FlatList performance bottlenecks

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释虚拟化、React.memo、useCallback、getItemLayout等核心概念的工作原理和使用场景。

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others the working principles and usage scenarios of core concepts like virtualization, React.memo, useCallback, getItemLayout.
