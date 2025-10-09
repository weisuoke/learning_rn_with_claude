# React Native入门 - 第7周第4天：SectionList分组数据渲染 (Part 2) | React Native Introduction - Week 7 Day 4: SectionList for Grouped Data (Part 2)

## 详细内容(续) | Detailed Content (Continued)

### 4. FlatList转SectionList实践 | FlatList to SectionList Conversion Practice (1.5小时 | 1.5 hours)

- **数据转换策略 | Data Conversion Strategies**

  **概念定义 | Concept Definition:**
  将平面数组(FlatList使用的data)转换为分组数据(SectionList使用的sections)是SectionList应用中的关键步骤。转换策略包括按属性分组、按时间分组、按字母分组等多种方式，需要根据业务需求选择合适的算法。| Converting flat arrays (data used by FlatList) to grouped data (sections used by SectionList) is a key step in SectionList applications. Conversion strategies include grouping by property, by time, by alphabet, etc., requiring selection of appropriate algorithms based on business needs.

  **核心特征 | Key Characteristics:**
  - 数据转换通常在数据获取后、渲染前完成 | Data conversion typically happens after data fetching, before rendering
  - 需要定义分组依据(grouping key或计算函数) | Need to define grouping criteria (grouping key or calculation function)
  - 考虑排序逻辑(section排序和item排序) | Consider sorting logic (section sort and item sort)
  - 处理边缘情况(空section、特殊字符、数字等) | Handle edge cases (empty sections, special characters, numbers, etc.)
  - 性能影响：大数据集转换应该memoize或在后台线程处理 | Performance impact: large dataset conversions should be memoized or processed in background thread

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 数据转换应该在哪个生命周期阶段执行？| At which lifecycle stage should data conversion be executed?
     **答案 | Answer:** 在数据获取后、传递给SectionList前，通常在useState/useEffect或数据处理函数中。不应在render函数中执行以避免性能问题。| After data fetching, before passing to SectionList, typically in useState/useEffect or data processing functions. Should not execute in render function to avoid performance issues.

  2. 转换后的sections数组需要排序吗？| Does the converted sections array need to be sorted?
     **答案 | Answer:** 视业务需求而定 | Depends on business needs - 通常需要排序以确保section按预期顺序显示(如字母顺序、时间倒序等) | Usually needs sorting to ensure sections display in expected order (like alphabetical, reverse chronological, etc.)

  3. 如何处理包含空data数组的section？| How to handle sections with empty data arrays?
     **答案 | Answer:** 可以过滤掉空sections，或保留它们但在renderSectionHeader中处理显示逻辑 | Can filter out empty sections, or keep them but handle display logic in renderSectionHeader

  4. 大数据集(10000+项)转换时应该注意什么？| What should be noted when converting large datasets (10000+ items)?
     **答案 | Answer:** 使用useMemo缓存转换结果，避免每次渲染都重新计算；考虑使用Web Worker或后台线程处理；监控转换耗时 | Use useMemo to cache conversion results, avoid recalculating on every render; consider using Web Worker or background thread; monitor conversion time

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 完整的数据转换示例集合 | Complete data conversion examples collection
  import React, { useMemo } from 'react';
  import { SectionList, Text, View, StyleSheet } from 'react-native';

  // 示例1: 联系人按首字母分组 | Example 1: Contacts grouped by first letter
  interface Contact {
    id: string;
    name: string;
    phone: string;
  }

  const groupContactsByLetter = (contacts: Contact[]) => {
    // 创建分组对象 | Create grouping object
    const grouped: { [key: string]: Contact[] } = {};

    contacts.forEach(contact => {
      // 获取首字母并转大写 | Get first letter and convert to uppercase
      const firstLetter = contact.name.charAt(0).toUpperCase();

      // 处理非字母字符(数字、符号等) | Handle non-alphabetic characters (numbers, symbols, etc.)
      const key = /[A-Z]/.test(firstLetter) ? firstLetter : '#';

      if (!grouped[key]) {
        grouped[key] = [];
      }
      grouped[key].push(contact);
    });

    // 转换为sections格式并排序 | Convert to sections format and sort
    return Object.keys(grouped)
      .sort((a, b) => {
        // '#'排在最后 | Put '#' at the end
        if (a === '#') return 1;
        if (b === '#') return -1;
        return a.localeCompare(b);
      })
      .map(letter => ({
        title: letter,
        data: grouped[letter].sort((a, b) => a.name.localeCompare(b.name)), // 名字排序 | Sort by name
      }));
  };

  // 使用示例 | Usage example
  const ContactListExample = () => {
    const flatContacts: Contact[] = [
      { id: '1', name: 'Alice Johnson', phone: '555-0101' },
      { id: '2', name: 'Bob Wilson', phone: '555-0102' },
      { id: '3', name: 'Charlie Brown', phone: '555-0103' },
      { id: '4', name: '123 Pizza', phone: '555-0104' }, // 特殊情况 | Special case
    ];

    // 使用useMemo缓存转换结果 | Use useMemo to cache conversion result
    const sections = useMemo(
      () => groupContactsByLetter(flatContacts),
      [flatContacts] // 依赖项：仅当contacts变化时重新计算 | Dependency: recalculate only when contacts change
    );

    return (
      <SectionList
        sections={sections}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.contactItem}>
            <Text style={styles.contactName}>{item.name}</Text>
            <Text style={styles.contactPhone}>{item.phone}</Text>
          </View>
        )}
        renderSectionHeader={({ section }) => (
          <View style={styles.sectionHeader}>
            <Text style={styles.sectionHeaderText}>{section.title}</Text>
          </View>
        )}
        stickySectionHeadersEnabled={true}
      />
    );
  };

  // 示例2: 消息按日期分组 | Example 2: Messages grouped by date
  interface Message {
    id: string;
    text: string;
    timestamp: number; // Unix timestamp
    sender: string;
  }

  const groupMessagesByDate = (messages: Message[]) => {
    const grouped: { [key: string]: Message[] } = {};

    messages.forEach(message => {
      // 转换时间戳为日期字符串 | Convert timestamp to date string
      const date = new Date(message.timestamp);
      const dateKey = date.toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
      }); // e.g., "January 15, 2025"

      if (!grouped[dateKey]) {
        grouped[dateKey] = [];
      }
      grouped[dateKey].push(message);
    });

    // 按日期倒序排列(最新的在前) | Sort by date in reverse (newest first)
    return Object.keys(grouped)
      .sort((a, b) => {
        const dateA = new Date(a).getTime();
        const dateB = new Date(b).getTime();
        return dateB - dateA; // 倒序 | Reverse order
      })
      .map(dateKey => ({
        title: dateKey,
        data: grouped[dateKey].sort((a, b) => b.timestamp - a.timestamp), // 时间倒序 | Time reverse order
      }));
  };

  // 示例3: 任务按状态分组 | Example 3: Tasks grouped by status
  interface Task {
    id: string;
    title: string;
    status: 'todo' | 'inProgress' | 'done';
    priority: 'high' | 'medium' | 'low';
  }

  const groupTasksByStatus = (tasks: Task[]) => {
    // 定义section顺序 | Define section order
    const statusOrder = ['inProgress', 'todo', 'done'];
    const statusLabels = {
      inProgress: '🔄 In Progress',
      todo: '📋 To Do',
      done: '✅ Done',
    };

    const grouped: { [key: string]: Task[] } = {
      inProgress: [],
      todo: [],
      done: [],
    };

    // 分组并按优先级排序 | Group and sort by priority
    tasks.forEach(task => {
      grouped[task.status].push(task);
    });

    const priorityOrder = { high: 0, medium: 1, low: 2 };

    // 构建sections | Build sections
    return statusOrder.map(status => ({
      title: statusLabels[status as keyof typeof statusLabels],
      data: grouped[status].sort(
        (a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]
      ),
      status, // 保留原始状态用于样式定制 | Keep original status for style customization
    }));
  };

  // 示例4: 通用分组函数 | Example 4: Generic grouping function
  function groupBy<T>(
    items: T[],
    keySelector: (item: T) => string,
    sortSections?: (a: string, b: string) => number,
    sortItems?: (a: T, b: T) => number
  ) {
    const grouped: { [key: string]: T[] } = {};

    items.forEach(item => {
      const key = keySelector(item);
      if (!grouped[key]) {
        grouped[key] = [];
      }
      grouped[key].push(item);
    });

    let keys = Object.keys(grouped);
    if (sortSections) {
      keys = keys.sort(sortSections);
    }

    return keys.map(key => ({
      title: key,
      data: sortItems ? grouped[key].sort(sortItems) : grouped[key],
    }));
  }

  // 通用函数使用示例 | Generic function usage example
  const GenericGroupingExample = () => {
    interface Product {
      id: string;
      name: string;
      category: string;
      price: number;
    }

    const products: Product[] = [
      { id: '1', name: 'iPhone', category: 'Electronics', price: 999 },
      { id: '2', name: 'Shirt', category: 'Clothing', price: 29 },
      { id: '3', name: 'iPad', category: 'Electronics', price: 599 },
    ];

    // 按类别分组，类别字母排序，商品按价格排序 | Group by category, sort categories alphabetically, sort products by price
    const sections = useMemo(
      () =>
        groupBy(
          products,
          (product) => product.category, // key selector
          (a, b) => a.localeCompare(b), // section sort
          (a, b) => a.price - b.price // item sort
        ),
      [products]
    );

    return (
      <SectionList
        sections={sections}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.productItem}>
            <Text style={styles.productName}>{item.name}</Text>
            <Text style={styles.productPrice}>${item.price}</Text>
          </View>
        )}
        renderSectionHeader={({ section }) => (
          <View style={styles.categoryHeader}>
            <Text style={styles.categoryHeaderText}>{section.title}</Text>
          </View>
        )}
      />
    );
  };

  const styles = StyleSheet.create({
    contactItem: {
      padding: 16,
      backgroundColor: 'white',
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    contactName: {
      fontSize: 16,
      fontWeight: '500',
      color: '#333',
    },
    contactPhone: {
      fontSize: 14,
      color: '#666',
      marginTop: 4,
    },
    sectionHeader: {
      backgroundColor: '#f5f5f5',
      padding: 8,
      paddingLeft: 16,
    },
    sectionHeaderText: {
      fontSize: 14,
      fontWeight: '600',
      color: '#007AFF',
    },
    productItem: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      padding: 16,
      backgroundColor: 'white',
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    productName: {
      fontSize: 15,
      color: '#333',
    },
    productPrice: {
      fontSize: 15,
      fontWeight: '600',
      color: '#007AFF',
    },
    categoryHeader: {
      backgroundColor: '#007AFF',
      padding: 12,
    },
    categoryHeaderText: {
      fontSize: 16,
      fontWeight: '700',
      color: 'white',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - groupContactsByLetter函数如何处理名字以数字开头的联系人？| How does groupContactsByLetter handle contacts whose names start with numbers?
    **答案 | Answer:** 将它们归类到'#'分组中，并且'#'分组会排在所有字母分组的最后 | Categorizes them into '#' group, and '#' group will be sorted at the end after all letter groups

  - 为什么要使用useMemo包装转换函数？| Why use useMemo to wrap the conversion function?
    **答案 | Answer:** 避免每次组件渲染时都重新执行转换逻辑，只有当依赖项(contacts数据)改变时才重新计算，提升性能 | Avoids re-executing conversion logic on every component render, only recalculates when dependencies (contacts data) change, improving performance

  - groupBy通用函数的sortItems参数是可选的，如果不提供会怎样？| The sortItems parameter of groupBy generic function is optional, what happens if not provided?
    **答案 | Answer:** section内的items将保持原始顺序，不会进行排序 | Items within sections will maintain original order and won't be sorted

  **常见误区检查 | Common Misconception Checks:**
  - 可以直接修改原始数组来创建sections吗？| Can you directly modify the original array to create sections?
    **答案 | Answer:** 不应该 | Shouldn't - 应该创建新的数据结构，遵循React不可变性原则。直接修改可能导致状态管理问题和难以追踪的bugs。| Should create new data structures, following React's immutability principle. Direct modification may cause state management issues and hard-to-track bugs.

  - 转换函数应该放在组件内部还是外部？| Should conversion functions be placed inside or outside components?
    **答案 | Answer:** 取决于是否需要访问组件状态/props | Depends on whether component state/props access is needed - 纯数据转换函数应该放在组件外部或单独文件中，便于复用和测试。需要访问状态的应该在组件内部并用useCallback/useMemo优化。| Pure data conversion functions should be placed outside components or in separate files for reusability and testing. Those needing state access should be inside components and optimized with useCallback/useMemo.

- **性能优化技巧 | Performance Optimization Techniques**

  **概念定义 | Concept Definition:**
  SectionList虽然继承了FlatList的优化特性,但分组结构带来了额外的渲染开销。需要通过合理的配置、组件优化和数据处理来确保大数据集下的流畅性能。| While SectionList inherits FlatList's optimization features, the grouped structure introduces additional rendering overhead. Need proper configuration, component optimization, and data processing to ensure smooth performance with large datasets.

  **核心特征 | Key Characteristics:**
  - 使用React.memo优化section header和item组件 | Use React.memo to optimize section header and item components
  - 配置getItemLayout跳过测量步骤(如果高度固定) | Configure getItemLayout to skip measurement (if height is fixed)
  - 合理设置windowSize、maxToRenderPerBatch等参数 | Properly set windowSize, maxToRenderPerBatch parameters
  - 避免在renderItem中创建内联函数和对象 | Avoid creating inline functions and objects in renderItem
  - 监控和分析性能瓶颈 | Monitor and analyze performance bottlenecks

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么section header也需要用React.memo优化？| Why do section headers also need React.memo optimization?
     **答案 | Answer:** Section headers在列表滚动时可能会频繁重渲染(特别是sticky headers)，memo可以防止不必要的重渲染 | Section headers may re-render frequently during scrolling (especially sticky headers), memo prevents unnecessary re-renders

  2. getItemLayout对SectionList有什么特殊要求？| What are the special requirements for getItemLayout with SectionList?
     **答案 | Answer:** 需要同时考虑section headers和items的高度，计算更复杂。如果headers和items高度都固定，可以显著提升性能。| Need to consider both section headers and items heights, calculation is more complex. If both headers and items have fixed heights, can significantly improve performance.

  3. 在renderItem中使用内联函数有什么问题？| What's the problem with using inline functions in renderItem?
     **答案 | Answer:** 每次渲染都会创建新函数，导致子组件props变化而重渲染，即使数据没变。应该提取为组件外部函数或使用useCallback。| Creates new function on every render, causing child component props to change and re-render even if data hasn't changed. Should extract as external function or use useCallback.

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // 性能优化完整示例 | Complete performance optimization example
  import React, { useMemo, useCallback, memo } from 'react';
  import { SectionList, Text, View, StyleSheet, Platform } from 'react-native';

  // 1. 优化的Item组件 | 1. Optimized Item component
  interface ItemData {
    id: string;
    name: string;
    details: string;
  }

  interface ItemProps {
    item: ItemData;
    onPress: (id: string) => void;
  }

  // 使用React.memo防止不必要的重渲染 | Use React.memo to prevent unnecessary re-renders
  const OptimizedItem = memo<ItemProps>(({ item, onPress }) => {
    console.log('Rendering item:', item.id); // 用于调试：监控渲染次数 | For debugging: monitor render count

    return (
      <View style={styles.optimizedItem}>
        <Text style={styles.itemName}>{item.name}</Text>
        <Text style={styles.itemDetails}>{item.details}</Text>
      </View>
    );
  });

  // 2. 优化的SectionHeader组件 | 2. Optimized SectionHeader component
  interface SectionHeaderProps {
    title: string;
    itemCount: number;
  }

  const OptimizedSectionHeader = memo<SectionHeaderProps>(({ title, itemCount }) => {
    console.log('Rendering header:', title);

    return (
      <View style={styles.optimizedHeader}>
        <Text style={styles.headerTitle}>{title}</Text>
        <Text style={styles.headerCount}>{itemCount} items</Text>
      </View>
    );
  });

  // 3. 完整的优化列表组件 | 3. Complete optimized list component
  const PerformantSectionList = () => {
    // 生成大量测试数据 | Generate large test data
    const sections = useMemo(() => {
      return Array.from({ length: 30 }, (_, sectionIndex) => ({
        title: `Section ${sectionIndex}`,
        data: Array.from({ length: 50 }, (_, itemIndex) => ({
          id: `${sectionIndex}-${itemIndex}`,
          name: `Item ${sectionIndex}-${itemIndex}`,
          details: `Details for item ${itemIndex}`,
        })),
      }));
    }, []);

    // 使用useCallback缓存回调函数 | Use useCallback to cache callback function
    const handleItemPress = useCallback((id: string) => {
      console.log('Item pressed:', id);
    }, []);

    // 提取renderItem避免内联函数 | Extract renderItem to avoid inline functions
    const renderItem = useCallback(
      ({ item }: { item: ItemData }) => (
        <OptimizedItem item={item} onPress={handleItemPress} />
      ),
      [handleItemPress]
    );

    // 提取renderSectionHeader | Extract renderSectionHeader
    const renderSectionHeader = useCallback(
      ({ section }: { section: { title: string; data: ItemData[] } }) => (
        <OptimizedSectionHeader title={section.title} itemCount={section.data.length} />
      ),
      []
    );

    // 配置getItemLayout(假设item高度固定为60，header高度40) | Configure getItemLayout (assuming item height 60, header height 40)
    const ITEM_HEIGHT = 60;
    const HEADER_HEIGHT = 40;

    const getItemLayout = useCallback(
      (data: any, index: number) => {
        // 计算该index对应的offset和length | Calculate offset and length for this index
        // 这个计算比较复杂，需要考虑前面所有sections的headers和items | This calculation is complex, need to consider all previous sections' headers and items
        let offset = 0;
        let length = ITEM_HEIGHT;

        // 简化版本：如果每个section的item数量相同 | Simplified version: if each section has same item count
        // 实际应用中可能需要更复杂的计算 | In actual applications may need more complex calculation
        return { length, offset: offset + index * ITEM_HEIGHT, index };
      },
      []
    );

    // keyExtractor优化 | keyExtractor optimization
    const keyExtractor = useCallback((item: ItemData) => item.id, []);

    return (
      <SectionList
        sections={sections}
        renderItem={renderItem}
        renderSectionHeader={renderSectionHeader}
        keyExtractor={keyExtractor}
        // 性能优化配置 | Performance optimization configuration
        removeClippedSubviews={Platform.OS === 'android'} // Android优化 | Android optimization
        maxToRenderPerBatch={10} // 每批渲染10个 | Render 10 items per batch
        updateCellsBatchingPeriod={50} // 批次间隔50ms | Batch interval 50ms
        initialNumToRender={20} // 初始渲染20个 | Initial render 20 items
        windowSize={10} // 渲染窗口大小 | Render window size
        // getItemLayout={getItemLayout} // 如果高度固定，启用此项 | If height is fixed, enable this
        stickySectionHeadersEnabled={true}
        // 性能监控 | Performance monitoring
        onViewableItemsChanged={useCallback(({ viewableItems }) => {
          console.log('Viewable items:', viewableItems.length);
        }, [])}
      />
    );
  };

  const styles = StyleSheet.create({
    optimizedItem: {
      height: 60,
      padding: 16,
      backgroundColor: 'white',
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
      justifyContent: 'center',
    },
    itemName: {
      fontSize: 15,
      fontWeight: '500',
      color: '#333',
    },
    itemDetails: {
      fontSize: 13,
      color: '#666',
      marginTop: 4,
    },
    optimizedHeader: {
      height: 40,
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      backgroundColor: '#f8f8f8',
      paddingHorizontal: 16,
      borderBottomWidth: 2,
      borderBottomColor: '#007AFF',
    },
    headerTitle: {
      fontSize: 16,
      fontWeight: '600',
      color: '#333',
    },
    headerCount: {
      fontSize: 12,
      color: '#666',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么renderItem和renderSectionHeader都用useCallback包装？| Why are both renderItem and renderSectionHeader wrapped with useCallback?
    **答案 | Answer:** 防止每次父组件渲染时创建新函数引用，导致SectionList认为props改变而重渲染所有items | Prevents creating new function references on every parent render, which would cause SectionList to think props changed and re-render all items

  - maxToRenderPerBatch设置为10的意义是什么？| What's the significance of setting maxToRenderPerBatch to 10?
    **答案 | Answer:** 控制每次批量渲染的item数量，较小的值可以让渲染更平滑(减少单次渲染负担)，但可能增加总渲染时间 | Controls the number of items rendered per batch, smaller values make rendering smoother (reduce single render burden), but may increase total render time

  **常见误区检查 | Common Misconception Checks:**
  - 是否所有SectionList都需要配置getItemLayout？| Do all SectionLists need getItemLayout configuration?
    **答案 | Answer:** 不是 | No - 只有当item和header高度完全固定且列表很长(1000+项)时才值得配置。动态高度的列表无法使用getItemLayout。| Only worth configuring when item and header heights are completely fixed and list is very long (1000+ items). Lists with dynamic heights cannot use getItemLayout.

  - React.memo是否会自动优化所有性能问题？| Does React.memo automatically optimize all performance issues?
    **答案 | Answer:** 不会 | No - React.memo只防止props未变时的重渲染。如果props每次都变(如内联对象/函数)，memo无效。还需要配合useCallback、useMemo等使用。| React.memo only prevents re-renders when props haven't changed. If props change every time (like inline objects/functions), memo is ineffective. Needs to be used together with useCallback, useMemo, etc.

### 5. 实践项目：联系人列表应用 | Practical Project: Contacts List App (1.5小时 | 1.5 hours)

**项目目标 | Project Objective:**
构建一个功能完整的联系人列表应用,展示SectionList的实际应用场景。应用将包含按字母分组的联系人、搜索过滤功能、右侧字母索引、性能优化等特性。| Build a fully functional contacts list application demonstrating real-world SectionList usage. The app will include contacts grouped alphabetically, search/filter functionality, right-side alphabet index, performance optimizations, and more.

**概念应用检查 | Concept Application Check**

在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. sections数据结构必须包含哪些属性？| What properties must the sections data structure contain?
   **答案 | Answer:** 必须包含data数组；通常还包含title等自定义属性用于header显示 | Must contain data array; typically also includes title and other custom properties for header display

2. 如何将平面联系人数组转换为按字母分组的sections？| How to convert a flat contacts array to alphabetically grouped sections?
   **答案 | Answer:** 遍历数组按首字母分组到对象中,然后转换为sections数组格式,并对sections和每个section的data排序 | Iterate through array grouping by first letter into object, then convert to sections array format, and sort both sections and each section's data

3. sticky headers在iOS和Android上的默认行为是否相同？| Is sticky headers' default behavior the same on iOS and Android?
   **答案 | Answer:** 不同 - iOS默认启用(true),Android默认禁用(false) | Different - iOS enabled by default (true), Android disabled by default (false)

**项目步骤 | Project Steps**

1. **创建项目结构和类型定义** | **Create project structure and type definitions**
2. **实现联系人数据生成和分组逻辑** | **Implement contact data generation and grouping logic**
3. **构建基础SectionList渲染** | **Build basic SectionList rendering**
4. **添加字母索引导航** | **Add alphabet index navigation**
5. **实现搜索过滤功能** | **Implement search/filter functionality**
6. **性能优化和样式美化** | **Performance optimization and style polish**

**完整示例代码 | Complete Example Code:**

```typescript
// ContactsListApp.tsx - 生产级联系人列表应用 | Production-quality contacts list app
import React, { useState, useMemo, useCallback, memo, useRef } from 'react';
import {
  View,
  Text,
  SectionList,
  StyleSheet,
  TextInput,
  TouchableOpacity,
  Platform,
  StatusBar,
  SafeAreaView,
} from 'react-native';

// 类型定义 | Type definitions
interface Contact {
  id: string;
  name: string;
  phone: string;
  email?: string;
  avatar?: string;
}

interface ContactSection {
  title: string;
  data: Contact[];
}

// 生成模拟联系人数据 | Generate mock contact data
const generateContacts = (count: number): Contact[] => {
  const firstNames = ['Alice', 'Bob', 'Charlie', 'David', 'Emma', 'Frank', 'Grace', 'Henry', 'Ivy', 'Jack'];
  const lastNames = ['Smith', 'Johnson', 'Williams', 'Brown', 'Jones', 'Garcia', 'Miller', 'Davis', 'Rodriguez', 'Martinez'];

  return Array.from({ length: count }, (_, index) => {
    const firstName = firstNames[Math.floor(Math.random() * firstNames.length)];
    const lastName = lastNames[Math.floor(Math.random() * lastNames.length)];
    const name = `${firstName} ${lastName}`;

    return {
      id: `contact-${index}`,
      name,
      phone: `+1 (555) ${String(Math.floor(Math.random() * 900) + 100)}-${String(Math.floor(Math.random() * 9000) + 1000)}`,
      email: `${firstName.toLowerCase()}.${lastName.toLowerCase()}@email.com`,
    };
  }).sort((a, b) => a.name.localeCompare(b.name)); // 按名字排序 | Sort by name
};

// 按首字母分组联系人 | Group contacts by first letter
const groupContactsByLetter = (contacts: Contact[]): ContactSection[] => {
  const grouped: { [key: string]: Contact[] } = {};

  contacts.forEach(contact => {
    const firstLetter = contact.name.charAt(0).toUpperCase();
    const key = /[A-Z]/.test(firstLetter) ? firstLetter : '#';

    if (!grouped[key]) {
      grouped[key] = [];
    }
    grouped[key].push(contact);
  });

  return Object.keys(grouped)
    .sort((a, b) => {
      if (a === '#') return 1;
      if (b === '#') return -1;
      return a.localeCompare(b);
    })
    .map(letter => ({
      title: letter,
      data: grouped[letter],
    }));
};

// 优化的联系人Item组件 | Optimized contact item component
interface ContactItemProps {
  contact: Contact;
  onPress: (contact: Contact) => void;
}

const ContactItem = memo<ContactItemProps>(({ contact, onPress }) => (
  <TouchableOpacity
    style={styles.contactItem}
    onPress={() => onPress(contact)}
    activeOpacity={0.7}
  >
    {/* 头像占位符 | Avatar placeholder */}
    <View style={styles.avatar}>
      <Text style={styles.avatarText}>
        {contact.name.split(' ').map(n => n[0]).join('')}
      </Text>
    </View>

    {/* 联系人信息 | Contact info */}
    <View style={styles.contactInfo}>
      <Text style={styles.contactName}>{contact.name}</Text>
      <Text style={styles.contactPhone}>{contact.phone}</Text>
    </View>

    {/* 右箭头 | Right arrow */}
    <Text style={styles.arrowIcon}>›</Text>
  </TouchableOpacity>
));

// 优化的SectionHeader组件 | Optimized section header component
interface SectionHeaderProps {
  title: string;
}

const SectionHeader = memo<SectionHeaderProps>(({ title }) => (
  <View style={styles.sectionHeader}>
    <Text style={styles.sectionHeaderText}>{title}</Text>
  </View>
));

// 主应用组件 | Main app component
const ContactsListApp = () => {
  // 状态管理 | State management
  const [searchQuery, setSearchQuery] = useState('');
  const sectionListRef = useRef<SectionList<Contact, ContactSection>>(null);

  // 生成所有联系人(实际应用中从API获取) | Generate all contacts (in real app, fetch from API)
  const allContacts = useMemo(() => generateContacts(200), []);

  // 根据搜索过滤联系人 | Filter contacts based on search
  const filteredContacts = useMemo(() => {
    if (!searchQuery.trim()) {
      return allContacts;
    }

    const query = searchQuery.toLowerCase();
    return allContacts.filter(
      contact =>
        contact.name.toLowerCase().includes(query) ||
        contact.phone.includes(query) ||
        contact.email?.toLowerCase().includes(query)
    );
  }, [allContacts, searchQuery]);

  // 转换为sections格式 | Convert to sections format
  const sections = useMemo(
    () => groupContactsByLetter(filteredContacts),
    [filteredContacts]
  );

  // 生成字母索引 | Generate alphabet index
  const alphabet = useMemo(() => {
    const letters = sections.map(section => section.title);
    return letters;
  }, [sections]);

  // 处理联系人点击 | Handle contact press
  const handleContactPress = useCallback((contact: Contact) => {
    console.log('Contact pressed:', contact.name);
    // 实际应用中导航到联系人详情页 | In real app, navigate to contact details
  }, []);

  // 滚动到指定section | Scroll to specific section
  const scrollToSection = useCallback(
    (sectionTitle: string) => {
      const sectionIndex = sections.findIndex(section => section.title === sectionTitle);
      if (sectionIndex !== -1 && sectionListRef.current) {
        sectionListRef.current.scrollToLocation({
          sectionIndex,
          itemIndex: 0,
          animated: true,
          viewPosition: 0,
        });
      }
    },
    [sections]
  );

  // 渲染函数 | Render functions
  const renderItem = useCallback(
    ({ item }: { item: Contact }) => (
      <ContactItem contact={item} onPress={handleContactPress} />
    ),
    [handleContactPress]
  );

  const renderSectionHeader = useCallback(
    ({ section }: { section: ContactSection }) => (
      <SectionHeader title={section.title} />
    ),
    []
  );

  const keyExtractor = useCallback((item: Contact) => item.id, []);

  // 空状态组件 | Empty state component
  const renderEmptyComponent = useCallback(
    () => (
      <View style={styles.emptyState}>
        <Text style={styles.emptyStateText}>
          {searchQuery ? `No contacts found for "${searchQuery}"` : 'No contacts'}
        </Text>
      </View>
    ),
    [searchQuery]
  );

  return (
    <SafeAreaView style={styles.container}>
      {/* 搜索栏 | Search bar */}
      <View style={styles.searchContainer}>
        <TextInput
          style={styles.searchInput}
          placeholder="Search contacts..."
          placeholderTextColor="#999"
          value={searchQuery}
          onChangeText={setSearchQuery}
          autoCapitalize="none"
          autoCorrect={false}
        />
        {searchQuery.length > 0 && (
          <TouchableOpacity
            style={styles.clearButton}
            onPress={() => setSearchQuery('')}
          >
            <Text style={styles.clearButtonText}>✕</Text>
          </TouchableOpacity>
        )}
      </View>

      {/* 联系人总数 | Total contacts count */}
      <View style={styles.countContainer}>
        <Text style={styles.countText}>
          {filteredContacts.length} contact{filteredContacts.length !== 1 ? 's' : ''}
        </Text>
      </View>

      {/* 联系人列表 | Contacts list */}
      <View style={styles.listContainer}>
        <SectionList
          ref={sectionListRef}
          sections={sections}
          keyExtractor={keyExtractor}
          renderItem={renderItem}
          renderSectionHeader={renderSectionHeader}
          stickySectionHeadersEnabled={true}
          ListEmptyComponent={renderEmptyComponent}
          // 性能优化 | Performance optimization
          removeClippedSubviews={Platform.OS === 'android'}
          maxToRenderPerBatch={10}
          updateCellsBatchingPeriod={50}
          initialNumToRender={20}
          windowSize={10}
        />

        {/* 右侧字母索引 | Right-side alphabet index */}
        {!searchQuery && alphabet.length > 0 && (
          <View style={styles.alphabetIndex}>
            {alphabet.map(letter => (
              <TouchableOpacity
                key={letter}
                style={styles.indexItem}
                onPress={() => scrollToSection(letter)}
              >
                <Text style={styles.indexLetter}>{letter}</Text>
              </TouchableOpacity>
            ))}
          </View>
        )}
      </View>
    </SafeAreaView>
  );
};

// 样式定义 | Style definitions
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
    paddingTop: Platform.OS === 'android' ? StatusBar.currentHeight : 0,
  },
  searchContainer: {
    backgroundColor: 'white',
    paddingHorizontal: 16,
    paddingVertical: 8,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
    flexDirection: 'row',
    alignItems: 'center',
  },
  searchInput: {
    flex: 1,
    height: 40,
    backgroundColor: '#f0f0f0',
    borderRadius: 8,
    paddingHorizontal: 12,
    fontSize: 15,
    color: '#333',
  },
  clearButton: {
    marginLeft: 8,
    width: 30,
    height: 30,
    justifyContent: 'center',
    alignItems: 'center',
  },
  clearButtonText: {
    fontSize: 18,
    color: '#666',
  },
  countContainer: {
    backgroundColor: 'white',
    paddingHorizontal: 16,
    paddingVertical: 8,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  countText: {
    fontSize: 13,
    color: '#666',
  },
  listContainer: {
    flex: 1,
  },
  contactItem: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: 'white',
    paddingVertical: 12,
    paddingHorizontal: 16,
    borderBottomWidth: StyleSheet.hairlineWidth,
    borderBottomColor: '#e0e0e0',
  },
  avatar: {
    width: 44,
    height: 44,
    borderRadius: 22,
    backgroundColor: '#007AFF',
    justifyContent: 'center',
    alignItems: 'center',
    marginRight: 12,
  },
  avatarText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '600',
  },
  contactInfo: {
    flex: 1,
  },
  contactName: {
    fontSize: 16,
    fontWeight: '500',
    color: '#333',
    marginBottom: 2,
  },
  contactPhone: {
    fontSize: 14,
    color: '#666',
  },
  arrowIcon: {
    fontSize: 24,
    color: '#ccc',
  },
  sectionHeader: {
    backgroundColor: '#f8f8f8',
    paddingHorizontal: 16,
    paddingVertical: 6,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
    // 跨平台阴影 | Cross-platform shadow
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 1 },
        shadowOpacity: 0.05,
        shadowRadius: 2,
      },
      android: {
        elevation: 2,
      },
    }),
  },
  sectionHeaderText: {
    fontSize: 15,
    fontWeight: '700',
    color: '#007AFF',
  },
  alphabetIndex: {
    position: 'absolute',
    right: 4,
    top: 0,
    bottom: 0,
    justifyContent: 'center',
    paddingVertical: 8,
    backgroundColor: 'transparent',
  },
  indexItem: {
    paddingVertical: 1,
    paddingHorizontal: 6,
  },
  indexLetter: {
    fontSize: 11,
    fontWeight: '600',
    color: '#007AFF',
    textAlign: 'center',
  },
  emptyState: {
    padding: 32,
    alignItems: 'center',
  },
  emptyStateText: {
    fontSize: 15,
    color: '#999',
  },
});

export default ContactsListApp;
```

**项目完成检查 | Project Completion Check:**

1. 联系人是否按字母正确分组显示？| Are contacts correctly grouped and displayed alphabetically?
2. sticky headers在滚动时是否正常工作？| Do sticky headers work properly during scrolling?
3. 搜索功能是否实时过滤联系人？| Does search functionality filter contacts in real-time?
4. 右侧字母索引点击是否能跳转到对应section？| Does clicking alphabet index scroll to corresponding section?
5. 性能优化措施是否已实施(memo、useCallback、useMemo)？| Are performance optimizations implemented (memo, useCallback, useMemo)?
6. 空状态和无搜索结果状态是否正确显示？| Do empty state and no search results state display correctly?
7. 跨平台样式(阴影、StatusBar等)是否正常？| Do cross-platform styles (shadow, StatusBar, etc.) work properly?

### 6. 扩展练习 | Extension Exercises (30分钟 | 30 minutes)

**概念深化练习 | Concept Deepening Exercises:**

1. **多级分组练习 | Multi-level Grouping Exercise**
   - **练习描述 | Exercise Description:** 实现一个两级分组的SectionList,例如按国家分组,每个国家内再按城市分组
   - **概念检查 | Concept Check:** 如何在section数据结构中表示嵌套分组?renderSectionHeader如何区分不同层级?
   - **学习目标 | Learning Objective:** 理解复杂数据结构的组织和渲染

2. **动态section折叠/展开 | Dynamic Section Collapse/Expand**
   - **练习描述 | Exercise Description:** 为每个section添加折叠/展开功能,点击header可以隐藏/显示该section的items
   - **概念检查 | Concept Check:** 如何维护折叠状态?如何动态修改section.data来实现折叠效果?
   - **学习目标 | Learning Objective:** 掌握SectionList的动态数据更新和交互设计

3. **自定义section footer | Custom Section Footer**
   - **练习描述 | Exercise Description:** 使用renderSectionFooter为每个section添加footer,显示总结信息(如"10 items in total")
   - **概念检查 | Concept Check:** renderSectionFooter的参数结构是否与renderSectionHeader相同?
   - **学习目标 | Learning Objective:** 完善section的渲染结构理解

4. **带分隔符的section | Sections with Separators**
   - **练习描述 | Exercise Description:** 使用ItemSeparatorComponent和SectionSeparatorComponent自定义分隔线样式
   - **概念检查 | Concept Check:** SectionList如何区分item间分隔符和section间分隔符?
   - **学习目标 | Learning Objective:** 精细控制列表视觉呈现

5. **性能测试练习 | Performance Testing Exercise**
   - **练习描述 | Exercise Description:** 创建包含5000+联系人的列表,使用React DevTools Profiler监控性能,对比优化前后的渲染时间
   - **概念检查 | Concept Check:** 哪些优化措施对大数据集影响最大?
   - **学习目标 | Learning Objective:** 培养性能分析和优化能力

6. **跨section搜索高亮 | Cross-section Search Highlighting**
   - **练习描述 | Exercise Description:** 实现搜索结果高亮显示,在保持section结构的同时突出显示匹配文本
   - **概念检查 | Concept Check:** 如何在不破坏section结构的情况下实现跨section的搜索?
   - **学习目标 | Learning Objective:** 结合搜索功能深化SectionList理解

7. **自定义滚动行为 | Custom Scroll Behavior**
   - **练习描述 | Exercise Description:** 实现"滚动到顶部"按钮,当用户滚动超过一定距离时显示,点击平滑滚回顶部
   - **概念检查 | Concept Check:** 如何监听SectionList的滚动事件?如何编程式滚动?
   - **学习目标 | Learning Objective:** 掌握列表的滚动控制和用户体验优化

## 学习资源 | Learning Resources
- [SectionList Official Docs](https://reactnative.dev/docs/sectionlist) - React Native官方SectionList文档
- [FlatList Performance Optimization](https://reactnative.dev/docs/optimizing-flatlist-configuration) - FlatList性能优化(适用于SectionList)
- [React.memo API Reference](https://react.dev/reference/react/memo) - React.memo优化组件
- [useMemo and useCallback](https://react.dev/reference/react/hooks) - React Hooks性能优化
- [TypeScript with React Native](https://reactnative.dev/docs/typescript) - TypeScript类型安全

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解SectionList与FlatList的区别和使用场景 | Understand difference between SectionList and FlatList and use cases
- [ ] 掌握sections数据结构的定义和组织 | Master definition and organization of sections data structure
- [ ] 能够实现自定义renderSectionHeader | Can implement custom renderSectionHeader
- [ ] 理解并应用sticky headers效果 | Understand and apply sticky headers effect
- [ ] 能够将平面数据转换为分组数据 | Can convert flat data to grouped data
- [ ] 掌握按字母、日期、属性等分组方法 | Master grouping by alphabet, date, properties, etc.
- [ ] 实现包含搜索和索引的联系人列表应用 | Implement contacts list app with search and index
- [ ] 应用性能优化技巧(memo、useCallback、useMemo) | Apply performance optimization techniques (memo, useCallback, useMemo)
- [ ] 理解getItemLayout的作用和配置方法 | Understand getItemLayout's purpose and configuration
- [ ] 能够调试和分析SectionList性能 | Can debug and analyze SectionList performance

**概念掌握验证 | Concept Mastery Verification:**

在标记完成前,请确保能够正确回答以下问题:
Before marking as complete, ensure you can correctly answer the following questions:

1. 什么时候应该使用SectionList而不是FlatList?
   When should you use SectionList instead of FlatList?

2. sections数据结构的必需属性是什么?
   What is the required property in sections data structure?

3. sticky headers在iOS和Android上的默认行为有何不同?
   How does sticky headers default behavior differ between iOS and Android?

4. 如何优化包含大量items的SectionList的性能?
   How to optimize performance of SectionList with large number of items?

5. 如何实现点击右侧字母索引跳转到对应section?
   How to implement scrolling to corresponding section when clicking alphabet index?

如果能清晰回答这些问题并成功完成实践项目,说明你已经扎实掌握了SectionList的核心概念和实践应用。
If you can clearly answer these questions and successfully complete the practical project, it indicates you have solidly mastered SectionList's core concepts and practical applications.
