# React Native入门 - 第7周第2天：FlatList基础与性能优化 | React Native Introduction - Week 7 Day 2: FlatList Basics & Performance Optimization

## 学习目标 | Learning Objectives
- 掌握FlatList的核心props及其作用机制 | Master FlatList core props and their mechanisms
- 理解FlatList的虚拟化渲染原理和性能优势 | Understand FlatList virtualization rendering principles and performance advantages
- 学会使用性能优化配置处理大数据集 | Learn to use performance optimization configurations for large datasets
- 能够实现React.memo和useCallback优化列表组件 | Be able to implement React.memo and useCallback to optimize list components
- 掌握getItemLayout和removeClippedSubviews等高级优化技术 | Master advanced optimization techniques like getItemLayout and removeClippedSubviews
- 能够使用React DevTools Profiler分析和监控性能 | Be able to use React DevTools Profiler to analyze and monitor performance

## 详细内容 | Detailed Content

### 1. FlatList核心概念与虚拟化原理 | FlatList Core Concepts & Virtualization Principles (1小时 | 1 hour)

- **FlatList虚拟化渲染 | FlatList Virtualization Rendering**

  **概念定义 | Concept Definition:**
  FlatList是React Native中专为高性能列表渲染设计的组件，通过虚拟化（Virtualization）技术只渲染当前屏幕可见区域及其附近的列表项，而不是一次性渲染所有数据。当用户滚动时，FlatList会动态挂载进入视口的组件，卸载离开视口的组件，从而保持恒定的内存占用和流畅的性能。

  FlatList is a component in React Native specifically designed for high-performance list rendering. It uses virtualization technology to render only the list items in the currently visible screen area and its vicinity, rather than rendering all data at once. As users scroll, FlatList dynamically mounts components entering the viewport and unmounts those leaving it, maintaining constant memory usage and smooth performance.

  **核心特征 | Key Characteristics:**
  - 懒加载渲染：只渲染可见区域的组件，大幅减少初始渲染时间 | Lazy rendering: only renders components in visible areas, significantly reducing initial render time
  - 自动回收机制：离开视口的组件会被卸载，释放内存资源 | Automatic recycling: components leaving the viewport are unmounted, freeing memory resources
  - 高性能滚动：即使数据集包含数千项，也能保持60fps的流畅滚动 | High-performance scrolling: maintains 60fps smooth scrolling even with thousands of data items
  - 声明式API：通过data和renderItem props以React方式定义列表 | Declarative API: defines lists in React way through data and renderItem props

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. FlatList会一次性渲染所有数据项吗？| Does FlatList render all data items at once?
     **答案 | Answer:** 否 | No - FlatList只渲染可见区域及其附近的项，这是虚拟化的核心优势 | FlatList only renders items in and near the visible area, which is the core advantage of virtualization
  2. 当列表项离开屏幕时，FlatList会如何处理？| What does FlatList do when list items leave the screen?
     **答案 | Answer:** 卸载组件 | Unmounts components - 这样可以释放内存并保持性能稳定 | This frees memory and maintains stable performance
  3. 如果你有10000条数据，FlatList会在内存中保持多少个组件？| If you have 10,000 data items, how many components does FlatList keep in memory?
     **答案 | Answer:** 只保持可见区域+缓冲区的数量（通常几十个）| Only keeps the number in visible area + buffer zone (typically a few dozen) - 不是全部10000个 | Not all 10,000
  4. FlatList的虚拟化技术解决了ScrollView的什么问题？| What problem of ScrollView does FlatList's virtualization solve?
     **答案 | Answer:** 解决了大数据集的性能和内存问题 | Solves performance and memory issues with large datasets - ScrollView会渲染所有子组件导致性能下降 | ScrollView renders all children causing performance degradation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // FlatList基础使用 - 虚拟化渲染演示 | FlatList basic usage - virtualization demo
  import React from 'react';
  import { FlatList, View, Text, StyleSheet } from 'react-native';

  // 生成大数据集 | Generate large dataset
  const generateData = (count) => {
    return Array.from({ length: count }, (_, index) => ({
      id: `item-${index}`,
      title: `Item ${index + 1}`,
      description: `This is item number ${index + 1}`
    }));
  };

  const VirtualizationDemo = () => {
    // 创建10000条数据 | Create 10,000 items
    const data = generateData(10000);

    // renderItem会被FlatList按需调用 | renderItem will be called on-demand by FlatList
    const renderItem = ({ item, index }) => {
      console.log(`Rendering item: ${index}`); // 观察控制台：只渲染可见项 | Watch console: only visible items render

      return (
        <View style={styles.itemContainer}>
          <Text style={styles.title}>{item.title}</Text>
          <Text style={styles.description}>{item.description}</Text>
        </View>
      );
    };

    return (
      <FlatList
        data={data}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        // 以下日志可帮助理解虚拟化 | The following logs help understand virtualization
        onViewableItemsChanged={({ viewableItems }) => {
          console.log(`Currently visible items: ${viewableItems.length}`);
        }}
        viewabilityConfig={{
          itemVisiblePercentThreshold: 50
        }}
      />
    );
  };

  const styles = StyleSheet.create({
    itemContainer: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    description: {
      fontSize: 14,
      color: '#666',
    },
  });

  export default VirtualizationDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码会立即渲染10000个组件吗？| Will this code immediately render 10,000 components?
    **答案 | Answer:** 否，只会渲染初始可见的项（通常10-20个取决于屏幕大小）| No, it will only render initially visible items (typically 10-20 depending on screen size)
  - 如果滚动到列表中间，控制台会显示什么？| What will the console show if you scroll to the middle of the list?
    **答案 | Answer:** 会看到新项被渲染的日志，之前的项被卸载 | You'll see logs of new items being rendered and previous items being unmounted

  **常见误区检查 | Common Misconception Checks:**
  - FlatList会预渲染所有数据以提高性能吗？| Does FlatList pre-render all data to improve performance?
    **答案 | Answer:** 否，预渲染所有数据会导致性能问题。FlatList采用按需渲染策略 | No, pre-rendering all data causes performance issues. FlatList uses on-demand rendering strategy
  - 虚拟化意味着数据只存储可见项吗？| Does virtualization mean data only stores visible items?
    **答案 | Answer:** 否，完整的data数组在内存中，但只有可见项被渲染成组件 | No, the complete data array is in memory, but only visible items are rendered as components

- **FlatList必需的核心Props | FlatList Essential Core Props**

  **概念定义 | Concept Definition:**
  FlatList有三个核心必需props：data（数据数组）、renderItem（渲染函数）和keyExtractor（键提取器）。理解这三个props的正确使用对于构建高性能列表至关重要。

  FlatList has three essential core props: data (data array), renderItem (render function), and keyExtractor (key extractor). Understanding the correct usage of these three props is crucial for building high-performance lists.

  **核心特征 | Key Characteristics:**
  - data：必须是数组类型，FlatList会监听其引用变化来触发重新渲染 | data: must be an array type, FlatList monitors its reference changes to trigger re-renders
  - renderItem：接收{item, index}参数，返回React元素 | renderItem: receives {item, index} parameters, returns React element
  - keyExtractor：为每个item生成唯一key，用于React的diff算法优化 | keyExtractor: generates unique key for each item, used for React's diff algorithm optimization
  - key的稳定性直接影响性能：key变化会导致组件卸载重建 | Key stability directly affects performance: key changes cause component unmount and rebuild

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果不提供keyExtractor，FlatList能正常工作吗？| Can FlatList work normally without keyExtractor?
     **答案 | Answer:** 可以但不推荐 | Yes but not recommended - 默认使用index作为key，在数据变化时可能导致性能问题 | Defaults to using index as key, may cause performance issues when data changes
  2. keyExtractor返回的key必须是什么类型？| What type must the key returned by keyExtractor be?
     **答案 | Answer:** 字符串 | String - 必须是唯一的字符串标识符 | Must be a unique string identifier
  3. data数组的引用变化会发生什么？| What happens when the data array reference changes?
     **答案 | Answer:** FlatList会重新渲染 | FlatList will re-render - 这就是为什么要避免在render中创建新数组 | This is why you should avoid creating new arrays in render
  4. renderItem函数应该定义在组件内部还是外部？| Should renderItem function be defined inside or outside the component?
     **答案 | Answer:** 最好在外部或使用useCallback包裹 | Best outside or wrapped with useCallback - 避免每次渲染创建新函数 | Avoid creating new function on every render

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // FlatList核心Props正确使用 | FlatList core props correct usage
  import React, { useState, useCallback } from 'react';
  import { FlatList, View, Text, StyleSheet, Button } from 'react-native';

  const CorePropsDemo = () => {
    const [data, setData] = useState([
      { id: '1', name: 'Apple', category: 'Fruit' },
      { id: '2', name: 'Carrot', category: 'Vegetable' },
      { id: '3', name: 'Banana', category: 'Fruit' },
    ]);

    // ✅ 正确：使用useCallback避免函数重新创建 | Correct: use useCallback to avoid function recreation
    const renderItem = useCallback(({ item }) => (
      <View style={styles.item}>
        <Text style={styles.name}>{item.name}</Text>
        <Text style={styles.category}>{item.category}</Text>
      </View>
    ), []); // 空依赖数组，函数只创建一次 | Empty dependency array, function created only once

    // ✅ 正确：稳定的key提取器 | Correct: stable key extractor
    const keyExtractor = useCallback((item) => item.id, []);

    // 添加新项测试 | Add new item test
    const addItem = () => {
      setData(prevData => [
        ...prevData,
        {
          id: `${Date.now()}`, // 确保唯一ID | Ensure unique ID
          name: `Item ${prevData.length + 1}`,
          category: 'New'
        }
      ]);
    };

    return (
      <View style={styles.container}>
        <Button title="Add Item" onPress={addItem} />
        <FlatList
          data={data} // ✅ 数组引用变化时FlatList会更新 | FlatList updates when array reference changes
          renderItem={renderItem} // ✅ 稳定的函数引用 | Stable function reference
          keyExtractor={keyExtractor} // ✅ 基于唯一id的key | Key based on unique id
        />
      </View>
    );
  };

  // ❌ 错误示例：性能问题 | Wrong example: performance issues
  const BadPropsDemo = () => {
    const [items, setItems] = useState([/* ... */]);

    return (
      <FlatList
        // ❌ 每次渲染创建新数组引用 | Creates new array reference on every render
        data={items.filter(item => item.active)}

        // ❌ 每次渲染创建新函数 | Creates new function on every render
        renderItem={({ item }) => (
          <View><Text>{item.name}</Text></View>
        )}

        // ❌ 使用index作为key不稳定 | Using index as key is unstable
        keyExtractor={(item, index) => index.toString()}
      />
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
    },
    item: {
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    name: {
      fontSize: 18,
      fontWeight: 'bold',
    },
    category: {
      fontSize: 14,
      color: '#666',
      marginTop: 4,
    },
  });

  export default CorePropsDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - CorePropsDemo中的renderItem函数会在每次渲染时重新创建吗？| Will the renderItem function in CorePropsDemo be recreated on every render?
    **答案 | Answer:** 否，useCallback确保函数引用稳定 | No, useCallback ensures stable function reference
  - BadPropsDemo中有哪些性能问题？| What performance issues exist in BadPropsDemo?
    **答案 | Answer:** (1) filter创建新数组触发不必要的重渲染 (2) renderItem每次都是新函数 (3) index作为key在数据顺序变化时会有问题 | (1) filter creates new array triggering unnecessary re-renders (2) renderItem is new function each time (3) index as key causes issues when data order changes

  **常见误区检查 | Common Misconception Checks:**
  - keyExtractor可以返回数字类型的key吗？| Can keyExtractor return numeric type keys?
    **答案 | Answer:** 技术上可以但会自动转换为字符串，最好直接返回字符串 | Technically yes but it auto-converts to string, better to return string directly
  - 每次setState都会导致FlatList完全重新渲染吗？| Does every setState cause FlatList to completely re-render?
    **答案 | Answer:** 否，FlatList会进行diff比较，只更新变化的项 | No, FlatList performs diff comparison and only updates changed items

### 2. FlatList性能优化配置参数 | FlatList Performance Optimization Configuration Parameters (1.5小时 | 1.5 hours)

- **windowSize优化窗口大小 | windowSize Optimizing Window Size**

  **概念定义 | Concept Definition:**
  windowSize定义了FlatList在当前视口之外应该渲染多少"屏幕"的内容。默认值为21，表示向上和向下各10个屏幕高度，加上当前可见的1个屏幕。调整这个值可以在内存使用和滚动流畅度之间找到平衡。

  windowSize defines how many "screens" worth of content FlatList should render outside the current viewport. The default value is 21, representing 10 screen heights up and down plus the 1 currently visible screen. Adjusting this value helps balance between memory usage and scrolling smoothness.

  **核心特征 | Key Characteristics:**
  - 较小的windowSize（如5）：减少内存占用，但快速滚动时可能出现空白闪烁 | Smaller windowSize (like 5): reduces memory footprint but may cause blank flashes during fast scrolling
  - 较大的windowSize（如21默认值）：更流畅的滚动体验，但占用更多内存 | Larger windowSize (like default 21): smoother scrolling experience but uses more memory
  - 建议根据列表项的复杂度调整：简单项用小值，复杂项用大值 | Recommended to adjust based on list item complexity: small value for simple items, large value for complex items
  - 影响initialNumToRender和maxToRenderPerBatch的实际效果 | Affects the actual effect of initialNumToRender and maxToRenderPerBatch

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. windowSize=5表示总共渲染多少个屏幕的内容？| windowSize=5 means rendering how many screens of content in total?
     **答案 | Answer:** 5个屏幕 | 5 screens - 当前屏幕上下各2个加当前1个 | 2 screens up and down from current plus current 1
  2. 增大windowSize会提高还是降低内存使用？| Does increasing windowSize increase or decrease memory usage?
     **答案 | Answer:** 提高 | Increases - 因为会保持更多组件在内存中 | Because it keeps more components in memory
  3. 什么情况下应该减小windowSize？| When should you decrease windowSize?
     **答案 | Answer:** 列表项非常复杂或设备内存有限时 | When list items are very complex or device memory is limited
  4. windowSize只影响初始渲染吗？| Does windowSize only affect initial render?
     **答案 | Answer:** 否 | No - 它持续影响滚动时的渲染窗口大小 | It continuously affects the render window size during scrolling

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // windowSize对比演示 | windowSize comparison demo
  import React, { useState } from 'react';
  import { FlatList, View, Text, StyleSheet, Button } from 'react-native';

  const WindowSizeDemo = () => {
    const [windowSize, setWindowSize] = useState(21); // 默认值 | Default value

    // 生成1000条复杂数据 | Generate 1000 complex items
    const data = Array.from({ length: 1000 }, (_, i) => ({
      id: `item-${i}`,
      title: `Complex Item ${i}`,
      subtitle: `This is a complex item with more content ${i}`,
      tags: ['tag1', 'tag2', 'tag3'],
    }));

    const renderItem = ({ item, index }) => (
      <View style={styles.complexItem}>
        <Text style={styles.title}>{item.title}</Text>
        <Text style={styles.subtitle}>{item.subtitle}</Text>
        <View style={styles.tagsContainer}>
          {item.tags.map((tag, i) => (
            <Text key={i} style={styles.tag}>{tag}</Text>
          ))}
        </View>
        {/* 模拟复杂组件 | Simulate complex component */}
        <Text style={styles.meta}>Index: {index}</Text>
      </View>
    );

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Button
            title="windowSize: 5 (省内存)"
            onPress={() => setWindowSize(5)}
          />
          <Button
            title="windowSize: 21 (默认)"
            onPress={() => setWindowSize(21)}
          />
        </View>

        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          windowSize={windowSize}

          // 添加日志观察渲染行为 | Add logs to observe rendering behavior
          onViewableItemsChanged={({ viewableItems }) => {
            console.log(`windowSize=${windowSize}, Visible items: ${viewableItems.length}`);
          }}

          // 显示当前windowSize | Display current windowSize
          ListHeaderComponent={() => (
            <Text style={styles.header}>Current windowSize: {windowSize}</Text>
          )}
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
      padding: 10,
      backgroundColor: '#f5f5f5',
    },
    header: {
      padding: 16,
      fontSize: 18,
      fontWeight: 'bold',
      backgroundColor: '#e3f2fd',
      textAlign: 'center',
    },
    complexItem: {
      padding: 16,
      backgroundColor: '#fff',
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
      minHeight: 120, // 复杂项有固定高度 | Complex item has fixed height
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    subtitle: {
      fontSize: 14,
      color: '#666',
      marginBottom: 8,
    },
    tagsContainer: {
      flexDirection: 'row',
      marginBottom: 8,
    },
    tag: {
      backgroundColor: '#e0e0e0',
      padding: 4,
      marginRight: 4,
      borderRadius: 4,
      fontSize: 12,
    },
    meta: {
      fontSize: 12,
      color: '#999',
    },
  });

  export default WindowSizeDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 快速滚动时，windowSize=5和windowSize=21哪个更流畅？| When scrolling fast, which is smoother: windowSize=5 or windowSize=21?
    **答案 | Answer:** windowSize=21更流畅，因为预渲染了更多内容 | windowSize=21 is smoother because it pre-renders more content
  - 如果每个列表项包含大图片，应该用大还是小的windowSize？| If each list item contains large images, should you use large or small windowSize?
    **答案 | Answer:** 较小的windowSize以避免内存溢出 | Smaller windowSize to avoid memory overflow

  **常见误区检查 | Common Misconception Checks:**
  - windowSize越大性能越好吗？| Is performance better with larger windowSize?
    **答案 | Answer:** 否，过大会占用过多内存，可能导致应用崩溃 | No, too large uses too much memory and may cause app crashes
  - windowSize=1可以吗？| Can windowSize=1?
    **答案 | Answer:** 技术上可以但会导致明显的滚动卡顿和空白 | Technically yes but causes obvious scrolling lag and blanks

- **initialNumToRender & maxToRenderPerBatch批量渲染优化 | initialNumToRender & maxToRenderPerBatch Batch Rendering Optimization**

  **概念定义 | Concept Definition:**
  initialNumToRender控制首次渲染时应该渲染多少个项目，默认值为10。maxToRenderPerBatch控制每一批次（frame）最多渲染多少个新项目，默认值也是10。这两个参数共同影响首屏加载速度和滚动性能。

  initialNumToRender controls how many items should be rendered on first render, with default value of 10. maxToRenderPerBatch controls maximum number of new items to render per batch (frame), also defaulting to 10. These two parameters together affect initial load speed and scrolling performance.

  **核心特征 | Key Characteristics:**
  - initialNumToRender：较大值加快首屏显示，较小值缩短首次渲染时间 | initialNumToRender: larger value speeds up first screen display, smaller value shortens initial render time
  - maxToRenderPerBatch：控制滚动时每帧渲染的项目数，影响滚动流畅度 | maxToRenderPerBatch: controls items rendered per frame during scrolling, affects scrolling smoothness
  - 需要根据列表项的复杂度和设备性能平衡这两个值 | Need to balance these two values based on list item complexity and device performance
  - 建议：简单项用大值（10-20），复杂项用小值（5-10）| Recommendation: large value (10-20) for simple items, small value (5-10) for complex items

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. initialNumToRender=50会发生什么？| What happens with initialNumToRender=50?
     **答案 | Answer:** 首次渲染50个项目，首屏加载慢但滚动时内容更充足 | Renders 50 items initially, slower first screen load but more content when scrolling
  2. maxToRenderPerBatch影响首次渲染吗？| Does maxToRenderPerBatch affect initial render?
     **答案 | Answer:** 否 | No - 它只影响滚动时的批量渲染 | It only affects batch rendering during scrolling
  3. 如果maxToRenderPerBatch=1会怎样？| What happens if maxToRenderPerBatch=1?
     **答案 | Answer:** 滚动时每帧只渲染1个项目，滚动会很卡顿 | Only renders 1 item per frame during scrolling, scrolling will be very laggy
  4. 这两个参数应该相等吗？| Should these two parameters be equal?
     **答案 | Answer:** 不一定，initialNumToRender通常可以稍大以确保首屏内容充足 | Not necessarily, initialNumToRender can usually be slightly larger to ensure sufficient first screen content

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 批量渲染参数对比 | Batch rendering parameters comparison
  import React, { useState, useCallback, useRef } from 'react';
  import { FlatList, View, Text, StyleSheet, Button } from 'react-native';

  const BatchRenderingDemo = () => {
    const [config, setConfig] = useState({
      initialNumToRender: 10,
      maxToRenderPerBatch: 10,
    });

    const renderCount = useRef(0); // 追踪渲染次数 | Track render count

    const data = Array.from({ length: 500 }, (_, i) => ({
      id: `item-${i}`,
      value: i,
    }));

    const renderItem = useCallback(({ item }) => {
      renderCount.current += 1;

      return (
        <View style={styles.item}>
          <Text>Item {item.value}</Text>
          <Text style={styles.meta}>Total renders so far: {renderCount.current}</Text>
        </View>
      );
    }, []);

    const resetAndApplyConfig = (newConfig) => {
      renderCount.current = 0;
      setConfig(newConfig);
    };

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Button
            title="快速首屏 (initial=20)"
            onPress={() => resetAndApplyConfig({
              initialNumToRender: 20, // 更多首屏内容 | More initial content
              maxToRenderPerBatch: 10,
            })}
          />
          <Button
            title="省资源 (initial=5, batch=5)"
            onPress={() => resetAndApplyConfig({
              initialNumToRender: 5, // 减少首次渲染负担 | Reduce initial render burden
              maxToRenderPerBatch: 5, // 减少滚动时批量 | Reduce scrolling batch
            })}
          />
        </View>

        <Text style={styles.configText}>
          Current config: initialNumToRender={config.initialNumToRender},
          maxToRenderPerBatch={config.maxToRenderPerBatch}
        </Text>

        <FlatList
          data={data}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          initialNumToRender={config.initialNumToRender}
          maxToRenderPerBatch={config.maxToRenderPerBatch}

          // 可选：观察渲染批次 | Optional: observe render batches
          updateCellsBatchingPeriod={50} // 批处理周期50ms | Batch processing period 50ms

          ListHeaderComponent={() => (
            <Text style={styles.header}>
              Scroll and watch render counts - Initial batch renders first {config.initialNumToRender} items
            </Text>
          )}
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
      padding: 10,
      backgroundColor: '#f5f5f5',
    },
    configText: {
      padding: 12,
      backgroundColor: '#fff3e0',
      fontSize: 12,
      textAlign: 'center',
    },
    header: {
      padding: 16,
      backgroundColor: '#e8f5e9',
      fontSize: 14,
      textAlign: 'center',
    },
    item: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
      backgroundColor: '#fff',
    },
    meta: {
      fontSize: 11,
      color: '#999',
      marginTop: 4,
    },
  });

  export default BatchRenderingDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 点击"快速首屏"按钮后，首次会渲染多少个组件？| After clicking "Fast First Screen" button, how many components render initially?
    **答案 | Answer:** 20个（initialNumToRender=20）| 20 (initialNumToRender=20)
  - 为什么"省资源"配置滚动可能不够流畅？| Why might scrolling be less smooth with "Save Resources" config?
    **答案 | Answer:** maxToRenderPerBatch=5导致每批渲染项目少，快速滚动时可能跟不上 | maxToRenderPerBatch=5 results in fewer items per batch, may not keep up during fast scrolling

  **常见误区检查 | Common Misconception Checks:**
  - initialNumToRender越大首屏加载越快吗？| Does larger initialNumToRender mean faster first screen load?
    **答案 | Answer:** 否，渲染更多项目会让首次渲染时间更长，但用户感知的"内容就绪"更快 | No, rendering more items takes longer for initial render, but perceived "content ready" is faster
  - maxToRenderPerBatch应该设置为1以节省性能吗？| Should maxToRenderPerBatch be set to 1 to save performance?
    **答案 | Answer:** 否，这会导致严重的滚动卡顿，得不偿失 | No, this causes severe scrolling lag, counterproductive

### 3. 组件级性能优化：React.memo与useCallback | Component-Level Performance Optimization: React.memo & useCallback (1.5小时 | 1.5 hours)

- **React.memo防止不必要的重渲染 | React.memo Preventing Unnecessary Re-renders**

  **概念定义 | Concept Definition:**
  React.memo是一个高阶组件（HOC），它对组件的props进行浅比较，只有当props真正改变时才重新渲染组件。在FlatList中，使用React.memo包裹列表项组件可以避免当列表数据更新时所有项都重新渲染的问题。

  React.memo is a higher-order component (HOC) that performs shallow comparison of component props and only re-renders when props actually change. In FlatList, wrapping list item components with React.memo prevents all items from re-rendering when list data updates.

  **核心特征 | Key Characteristics:**
  - 执行浅比较：逐个比较props的引用，引用相同则跳过渲染 | Performs shallow comparison: compares props references one by one, skips render if references are the same
  - 适用于纯组件：渲染结果只依赖于props的组件 | Suitable for pure components: components whose render output only depends on props
  - 可自定义比较函数：通过第二个参数提供自定义比较逻辑 | Can customize comparison function: provide custom comparison logic via second parameter
  - 对FlatList性能影响巨大：避免数千个列表项的无效重渲染 | Huge impact on FlatList performance: avoids invalid re-renders of thousands of list items

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React.memo会比较props的值还是引用？| Does React.memo compare props values or references?
     **答案 | Answer:** 引用（浅比较）| References (shallow comparison) - 这就是为什么要确保props稳定 | This is why you need to ensure stable props
  2. 如果列表项组件没有用React.memo，更新一个item会重渲染所有items吗？| If list item component is not wrapped with React.memo, will updating one item re-render all items?
     **答案 | Answer:** 可能会，取决于父组件的更新方式 | Possibly, depends on how parent component updates
  3. React.memo对所有组件都有好处吗？| Is React.memo beneficial for all components?
     **答案 | Answer:** 否 | No - 对于props频繁变化的组件，比较成本可能超过收益 | For components with frequently changing props, comparison cost may exceed benefits
  4. React.memo可以阻止子组件内部state变化导致的重渲染吗？| Can React.memo prevent re-renders caused by internal state changes in child components?
     **答案 | Answer:** 否 | No - 它只检查props，内部state变化会正常触发重渲染 | It only checks props, internal state changes trigger re-renders normally

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // React.memo优化FlatList性能 | React.memo optimizing FlatList performance
  import React, { useState, useCallback, memo } from 'react';
  import { FlatList, View, Text, StyleSheet, Button, TouchableOpacity } from 'react-native';

  // ✅ 优化：使用React.memo的列表项 | Optimized: list item with React.memo
  const MemoizedListItem = memo(({ item, onPress }) => {
    console.log(`Rendering item: ${item.id}`); // 观察重渲染 | Observe re-renders

    return (
      <TouchableOpacity style={styles.item} onPress={() => onPress(item.id)}>
        <Text style={styles.itemTitle}>{item.title}</Text>
        <Text style={styles.itemLikes}>Likes: {item.likes}</Text>
      </TouchableOpacity>
    );
  });

  // ❌ 未优化：没有使用React.memo | Unoptimized: without React.memo
  const UnmemoizedListItem = ({ item, onPress }) => {
    console.log(`Rendering item (unmemoized): ${item.id}`);

    return (
      <TouchableOpacity style={styles.item} onPress={() => onPress(item.id)}>
        <Text style={styles.itemTitle}>{item.title}</Text>
        <Text style={styles.itemLikes}>Likes: {item.likes}</Text>
      </TouchableOpacity>
    );
  };

  const MemoOptimizationDemo = () => {
    const [data, setData] = useState(
      Array.from({ length: 100 }, (_, i) => ({
        id: `${i}`,
        title: `Post ${i}`,
        likes: 0,
      }))
    );

    const [useMemo, setUseMemo] = useState(true);

    // ✅ 稳定的回调函数 | Stable callback function
    const handlePress = useCallback((id) => {
      console.log(`Pressed item: ${id}`);
      setData(prevData =>
        prevData.map(item =>
          item.id === id
            ? { ...item, likes: item.likes + 1 } // 只更新点击的项 | Only update clicked item
            : item
        )
      );
    }, []);

    const renderMemoizedItem = useCallback(({ item }) => (
      <MemoizedListItem item={item} onPress={handlePress} />
    ), [handlePress]);

    const renderUnmemoizedItem = useCallback(({ item }) => (
      <UnmemoizedListItem item={item} onPress={handlePress} />
    ), [handlePress]);

    return (
      <View style={styles.container}>
        <View style={styles.controls}>
          <Button
            title={useMemo ? "Using React.memo ✅" : "Not using React.memo ❌"}
            onPress={() => setUseMemo(!useMemo)}
          />
          <Text style={styles.instruction}>
            Tap any item to increase likes. Watch console logs.
          </Text>
        </View>

        <FlatList
          data={data}
          renderItem={useMemo ? renderMemoizedItem : renderUnmemoizedItem}
          keyExtractor={(item) => item.id}
          ListHeaderComponent={() => (
            <Text style={styles.header}>
              {useMemo
                ? "✅ With React.memo: Only changed item re-renders"
                : "❌ Without React.memo: All items may re-render"}
            </Text>
          )}
        />
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
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    instruction: {
      marginTop: 8,
      fontSize: 12,
      color: '#666',
      textAlign: 'center',
    },
    header: {
      padding: 16,
      backgroundColor: '#e3f2fd',
      fontSize: 14,
      textAlign: 'center',
      fontWeight: 'bold',
    },
    item: {
      padding: 20,
      backgroundColor: '#fff',
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
    },
    itemTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 4,
    },
    itemLikes: {
      fontSize: 14,
      color: '#2196F3',
    },
  });

  export default MemoOptimizationDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 点击一个item增加likes时，使用React.memo会渲染几个组件？| When clicking an item to increase likes, how many components render with React.memo?
    **答案 | Answer:** 只有被点击的那个item（1个）| Only the clicked item (1)
  - 不使用React.memo时，点击会发生什么？| What happens when clicking without React.memo?
    **答案 | Answer:** 可能所有100个items都重新渲染（查看console日志）| Possibly all 100 items re-render (check console logs)

  **常见误区检查 | Common Misconception Checks:**
  - React.memo会自动优化所有组件吗？| Does React.memo automatically optimize all components?
    **答案 | Answer:** 否，只有被memo包裹的组件才会进行props比较 | No, only components wrapped with memo perform props comparison
  - 如果传递内联函数作为prop，React.memo还有效吗？| If passing inline function as prop, is React.memo still effective?
    **答案 | Answer:** 否，内联函数每次都是新引用，会导致React.memo失效 | No, inline function is new reference each time, causes React.memo to be ineffective

- **useCallback稳定函数引用 | useCallback Stabilizing Function References**

  **概念定义 | Concept Definition:**
  useCallback是React Hook，用于记忆化回调函数，返回稳定的函数引用。在FlatList中，它常与React.memo配合使用：用useCallback包裹传递给列表项的回调函数，确保props引用不变，从而让React.memo的浅比较生效。

  useCallback is a React Hook used to memoize callback functions and return stable function references. In FlatList, it's often used with React.memo: wrap callback functions passed to list items with useCallback to ensure props references don't change, making React.memo's shallow comparison effective.

  **核心特征 | Key Characteristics:**
  - 返回记忆化的函数：除非依赖项变化，否则返回相同的函数引用 | Returns memoized function: returns same function reference unless dependencies change
  - 依赖数组控制更新：只有依赖项变化时才创建新函数 | Dependency array controls updates: only creates new function when dependencies change
  - 必须与React.memo配合：单独使用useCallback无法防止子组件重渲染 | Must work with React.memo: using useCallback alone cannot prevent child component re-renders
  - 适用场景：传递给子组件的回调、传递给FlatList的renderItem等 | Use cases: callbacks passed to child components, renderItem passed to FlatList, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useCallback会阻止组件重渲染吗？| Does useCallback prevent component re-renders?
     **答案 | Answer:** 否 | No - 它只保持函数引用稳定，需要配合React.memo使用 | It only keeps function reference stable, needs to work with React.memo
  2. useCallback的依赖数组为空[]意味着什么？| What does an empty dependency array [] in useCallback mean?
     **答案 | Answer:** 函数只在组件挂载时创建一次，整个生命周期保持相同引用 | Function is created only once on mount, keeps same reference throughout lifecycle
  3. 如果依赖数组包含state，state变化时会发生什么？| If dependency array includes state, what happens when state changes?
     **答案 | Answer:** useCallback会返回新的函数引用 | useCallback returns new function reference
  4. 每个回调函数都应该用useCallback包裹吗？| Should every callback function be wrapped with useCallback?
     **答案 | Answer:** 否 | No - 只在需要稳定引用时使用（如传递给优化的子组件）| Only use when stable reference is needed (like passing to optimized child components)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // useCallback在FlatList中的正确使用 | Correct usage of useCallback in FlatList
  import React, { useState, useCallback, memo } from 'react';
  import { FlatList, View, Text, StyleSheet, TouchableOpacity, Button } from 'react-native';

  // 优化的列表项组件 | Optimized list item component
  const OptimizedItem = memo(({ item, onToggle, onDelete }) => {
    console.log(`Rendering item: ${item.id}`);

    return (
      <View style={styles.item}>
        <TouchableOpacity onPress={() => onToggle(item.id)} style={styles.itemContent}>
          <Text style={[styles.itemText, item.completed && styles.completed]}>
            {item.title}
          </Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => onDelete(item.id)} style={styles.deleteButton}>
          <Text style={styles.deleteText}>Delete</Text>
        </TouchableOpacity>
      </View>
    );
  });

  const UseCallbackDemo = () => {
    const [todos, setTodos] = useState([
      { id: '1', title: 'Learn FlatList', completed: false },
      { id: '2', title: 'Master React.memo', completed: false },
      { id: '3', title: 'Understand useCallback', completed: false },
    ]);

    // ✅ 正确：使用useCallback稳定函数引用 | Correct: use useCallback to stabilize function reference
    const handleToggle = useCallback((id) => {
      setTodos(prevTodos =>
        prevTodos.map(todo =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
      );
    }, []); // 空依赖：函数永远不变 | Empty dependency: function never changes

    const handleDelete = useCallback((id) => {
      setTodos(prevTodos => prevTodos.filter(todo => todo.id !== id));
    }, []);

    // ✅ renderItem也使用useCallback | renderItem also uses useCallback
    const renderItem = useCallback(({ item }) => (
      <OptimizedItem
        item={item}
        onToggle={handleToggle}
        onDelete={handleDelete}
      />
    ), [handleToggle, handleDelete]); // 依赖稳定的回调 | Depends on stable callbacks

    const addTodo = () => {
      const newTodo = {
        id: Date.now().toString(),
        title: `New Todo ${todos.length + 1}`,
        completed: false,
      };
      setTodos(prevTodos => [...prevTodos, newTodo]);
    };

    // ❌ 错误示例：不使用useCallback | Wrong example: without useCallback
    const BadExample = () => {
      return (
        <FlatList
          data={todos}
          renderItem={({ item }) => (
            <OptimizedItem
              item={item}
              // ❌ 每次渲染都是新函数，React.memo失效 | New function on each render, React.memo ineffective
              onToggle={(id) => setTodos(/*...*/)}
              onDelete={(id) => setTodos(/*...*/)}
            />
          )}
          keyExtractor={(item) => item.id}
        />
      );
    };

    return (
      <View style={styles.container}>
        <View style={styles.header}>
          <Text style={styles.title}>Todo List with useCallback</Text>
          <Button title="Add Todo" onPress={addTodo} />
          <Text style={styles.hint}>
            Toggle or delete items. Check console for re-renders.
          </Text>
        </View>

        <FlatList
          data={todos}
          renderItem={renderItem}
          keyExtractor={(item) => item.id}
          ListEmptyComponent={() => (
            <Text style={styles.empty}>No todos yet. Add one!</Text>
          )}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    header: {
      padding: 16,
      backgroundColor: '#fff',
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 12,
    },
    hint: {
      fontSize: 12,
      color: '#666',
      marginTop: 8,
    },
    item: {
      flexDirection: 'row',
      backgroundColor: '#fff',
      padding: 16,
      marginVertical: 4,
      marginHorizontal: 16,
      borderRadius: 8,
      alignItems: 'center',
    },
    itemContent: {
      flex: 1,
    },
    itemText: {
      fontSize: 16,
    },
    completed: {
      textDecorationLine: 'line-through',
      color: '#999',
    },
    deleteButton: {
      padding: 8,
      backgroundColor: '#ff5252',
      borderRadius: 4,
    },
    deleteText: {
      color: '#fff',
      fontSize: 12,
    },
    empty: {
      textAlign: 'center',
      marginTop: 32,
      fontSize: 16,
      color: '#999',
    },
  });

  export default UseCallbackDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 点击toggle一个todo时，有多少个OptimizedItem会重新渲染？| When toggling a todo, how many OptimizedItem components re-render?
    **答案 | Answer:** 只有1个（被toggle的那个），因为其他items的props没变 | Only 1 (the toggled one), because other items' props haven't changed
  - 如果handleToggle没有用useCallback包裹会怎样？| What if handleToggle is not wrapped with useCallback?
    **答案 | Answer:** 每次渲染都是新函数引用，所有items都会重渲染 | New function reference on each render, all items will re-render

  **常见误区检查 | Common Misconception Checks:**
  - useCallback可以提高函数执行速度吗？| Can useCallback improve function execution speed?
    **答案 | Answer:** 否，它只保持引用稳定，不改变函数执行性能 | No, it only keeps reference stable, doesn't change function execution performance
  - 依赖数组应该包含函数内使用的所有变量吗？| Should dependency array include all variables used in the function?
    **答案 | Answer:** 应该，但如果使用函数式更新（prevState => ...）可以避免某些依赖 | Yes, but using functional updates (prevState => ...) can avoid some dependencies
