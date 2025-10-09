# React Native入门 - 第7周第4天：SectionList分组数据渲染 | React Native Introduction - Week 7 Day 4: SectionList for Grouped Data

## 学习目标 | Learning Objectives
- 理解SectionList与FlatList的区别和使用场景 | Understand the difference between SectionList and FlatList and their use cases
- 掌握sections数据结构的定义和组织方式 | Master the definition and organization of sections data structure
- 学会自定义section headers和实现sticky headers效果 | Learn to customize section headers and implement sticky header effects
- 能够将平面列表数据转换为分组数据 | Be able to convert flat list data into grouped data
- 理解分组列表的性能优化策略 | Understand performance optimization strategies for sectioned lists
- 构建生产级别的联系人列表应用 | Build production-quality contacts list applications

## 详细内容 | Detailed Content

### 1. SectionList核心概念 | SectionList Core Concepts (1小时 | 1 hour)

- **SectionList组件本质 | SectionList Component Essence**

  **概念定义 | Concept Definition:**
  SectionList是React Native提供的用于渲染分组列表的高性能组件。它基于VirtualizedList实现，支持将数据按section(章节/分组)组织，每个section可以有独立的header、footer和数据项。类似iOS Settings应用或Android联系人应用的分组展示效果。| SectionList is a high-performance component provided by React Native for rendering grouped lists. It's built on VirtualizedList, supporting data organization by sections, where each section can have independent headers, footers, and data items. Similar to the grouped display in iOS Settings app or Android Contacts app.

  **核心特征 | Key Characteristics:**
  - 继承FlatList的所有性能优化特性(虚拟化渲染、懒加载等) | Inherits all performance optimization features from FlatList (virtualized rendering, lazy loading, etc.)
  - 使用sections数组而非data数组，每个section包含title和data | Uses sections array instead of data array, each section contains title and data
  - 支持section header的sticky(粘性)效果，滚动时header固定在顶部 | Supports sticky section headers that remain fixed at top during scrolling
  - 可以为每个section单独定义渲染逻辑和样式 | Can define separate rendering logic and styles for each section
  - 自动处理section之间的分隔和布局 | Automatically handles separation and layout between sections

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. SectionList和FlatList的主要区别是什么？| What's the main difference between SectionList and FlatList?
     **答案 | Answer:** SectionList使用sections数组组织数据，每个section包含标题和数据项；FlatList使用单一的data数组。SectionList专门用于分组数据，FlatList用于平面列表。| SectionList uses sections array to organize data, each section contains title and data items; FlatList uses a single data array. SectionList is specifically for grouped data, FlatList for flat lists.

  2. SectionList的sticky header功能是默认开启的吗？| Is SectionList's sticky header feature enabled by default?
     **答案 | Answer:** 在iOS上默认开启(stickySectionHeadersEnabled默认为true)，在Android上默认关闭。可以通过stickySectionHeadersEnabled属性控制。| Enabled by default on iOS (stickySectionHeadersEnabled defaults to true), disabled by default on Android. Can be controlled via stickySectionHeadersEnabled prop.

  3. SectionList是否继承了FlatList的性能优化特性？| Does SectionList inherit FlatList's performance optimization features?
     **答案 | Answer:** 是的 | Yes - SectionList继承了虚拟化渲染、windowSize、removeClippedSubviews等所有FlatList优化特性，同时增加了section管理功能。| SectionList inherits all FlatList optimization features including virtualized rendering, windowSize, removeClippedSubviews, while adding section management capabilities.

  4. 什么场景下应该使用SectionList而不是FlatList？| When should you use SectionList instead of FlatList?
     **答案 | Answer:** 当数据需要按类别/分组展示时，如联系人按字母分组、设置按功能分组、聊天消息按日期分组等。如果数据是平面的、无需分组，使用FlatList更简单。| When data needs to be displayed by categories/groups, such as contacts grouped by alphabet, settings grouped by function, chat messages grouped by date. If data is flat with no grouping needed, FlatList is simpler.

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // SectionList基本结构 | Basic SectionList structure
  import React from 'react';
  import { SectionList, Text, View, StyleSheet } from 'react-native';

  // 定义sections数据结构 | Define sections data structure
  // 每个section必须包含data数组 | Each section must contain data array
  const DATA = [
    {
      title: 'A', // section标题 | section title
      data: ['Alice', 'Andrew', 'Amy'], // 该section的数据项 | data items for this section
    },
    {
      title: 'B',
      data: ['Bob', 'Bella', 'Bruce'],
    },
    {
      title: 'C',
      data: ['Charlie', 'Chloe'],
    },
  ];

  const BasicSectionListExample = () => {
    // renderItem渲染每个数据项 | renderItem renders each data item
    const renderItem = ({ item }) => (
      <View style={styles.item}>
        <Text style={styles.itemText}>{item}</Text>
      </View>
    );

    // renderSectionHeader渲染每个section的header | renderSectionHeader renders each section's header
    const renderSectionHeader = ({ section: { title } }) => (
      <View style={styles.sectionHeader}>
        <Text style={styles.sectionHeaderText}>{title}</Text>
      </View>
    );

    return (
      <SectionList
        sections={DATA} // 使用sections而非data | Use sections instead of data
        keyExtractor={(item, index) => item + index} // 为每个item生成唯一key | Generate unique key for each item
        renderItem={renderItem} // 渲染数据项 | Render data items
        renderSectionHeader={renderSectionHeader} // 渲染section header | Render section headers
        stickySectionHeadersEnabled={true} // 启用sticky headers | Enable sticky headers
      />
    );
  };

  const styles = StyleSheet.create({
    sectionHeader: {
      backgroundColor: '#f4f4f4',
      padding: 8,
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    sectionHeaderText: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#333',
    },
    item: {
      backgroundColor: 'white',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    itemText: {
      fontSize: 14,
      color: '#666',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码会渲染几个section header？| How many section headers will this code render?
    **答案 | Answer:** 3个 - 分别对应'A', 'B', 'C'三个section | 3 - corresponding to sections 'A', 'B', 'C'

  - 如果将stickySectionHeadersEnabled设为false会发生什么？| What happens if stickySectionHeadersEnabled is set to false?
    **答案 | Answer:** Section headers将随内容一起滚动，不再固定在顶部 | Section headers will scroll with content instead of staying fixed at top

  **常见误区检查 | Common Misconception Checks:**
  - SectionList可以使用data属性而非sections吗？| Can SectionList use data prop instead of sections?
    **答案 | Answer:** 不可以 | No - SectionList必须使用sections属性，每个section对象必须包含data数组。使用data属性会报错。| SectionList must use sections prop, each section object must contain a data array. Using data prop will cause an error.

  - sections数组中的每个对象可以省略data属性吗？| Can data property be omitted from each object in sections array?
    **答案 | Answer:** 不可以 | No - data属性是必需的，即使是空数组。省略会导致运行时错误。| The data property is required, even if it's an empty array. Omitting it will cause runtime errors.

- **sections数据结构设计 | sections Data Structure Design**

  **概念定义 | Concept Definition:**
  sections是一个数组，每个元素代表一个section(分组)。每个section对象必须包含data数组，通常还包含title或其他自定义属性。正确的数据结构设计直接影响渲染性能和代码可维护性。| sections is an array where each element represents a section (group). Each section object must contain a data array and typically includes a title or other custom properties. Proper data structure design directly affects rendering performance and code maintainability.

  **核心特征 | Key Characteristics:**
  - 每个section必须有data数组(必需属性) | Each section must have a data array (required property)
  - 可以添加自定义属性如title、key、footer等 | Can add custom properties like title, key, footer, etc.
  - 支持嵌套的复杂数据结构 | Supports nested complex data structures
  - 可以通过TypeScript定义类型确保数据安全 | Can use TypeScript to define types for data safety

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. section对象中哪个属性是必需的？| Which property is required in a section object?
     **答案 | Answer:** data - 必须是数组类型，包含该section的所有数据项 | data - must be an array type containing all data items for that section

  2. 可以为不同的section定义不同的数据项结构吗？| Can different sections have different data item structures?
     **答案 | Answer:** 可以，但不推荐 | Yes, but not recommended - 为了代码一致性，建议所有section的数据项使用统一结构。如果确实需要，可以在renderItem中根据section类型条件渲染。| For code consistency, it's recommended all sections use uniform data item structure. If truly needed, can conditionally render in renderItem based on section type.

  3. sections数组为空时会发生什么？| What happens when sections array is empty?
     **答案 | Answer:** SectionList会渲染空白内容，可以使用ListEmptyComponent显示空状态提示 | SectionList will render blank content; can use ListEmptyComponent to show empty state message

  **代码示例与验证 | Code Examples and Verification:**
  ```typescript
  // TypeScript类型定义示例 | TypeScript type definition example

  // 定义联系人数据项类型 | Define contact item type
  interface Contact {
    id: string;
    name: string;
    phone: string;
    avatar?: string;
  }

  // 定义section类型 | Define section type
  interface ContactSection {
    title: string; // section标题(如字母A-Z) | section title (like letter A-Z)
    data: Contact[]; // 该section的联系人数组 | array of contacts for this section
    index?: number; // 可选：section索引 | optional: section index
  }

  // 创建符合类型的sections数据 | Create sections data matching the type
  const contactSections: ContactSection[] = [
    {
      title: 'A',
      data: [
        { id: '1', name: 'Alice Johnson', phone: '555-0101' },
        { id: '2', name: 'Andrew Smith', phone: '555-0102' },
      ],
    },
    {
      title: 'B',
      data: [
        { id: '3', name: 'Bob Wilson', phone: '555-0201' },
        { id: '4', name: 'Bella Davis', phone: '555-0202' },
      ],
    },
    {
      title: 'C',
      data: [], // 空section也是有效的 | Empty section is also valid
    },
  ];

  // 实用函数：将平面数组转换为sections | Utility function: convert flat array to sections
  const groupContactsByLetter = (contacts: Contact[]): ContactSection[] => {
    // 创建A-Z的空sections | Create empty sections for A-Z
    const sections: ContactSection[] = [];
    const grouped: { [key: string]: Contact[] } = {};

    // 按首字母分组 | Group by first letter
    contacts.forEach(contact => {
      const firstLetter = contact.name.charAt(0).toUpperCase();
      if (!grouped[firstLetter]) {
        grouped[firstLetter] = [];
      }
      grouped[firstLetter].push(contact);
    });

    // 转换为sections格式并排序 | Convert to sections format and sort
    Object.keys(grouped)
      .sort() // 字母排序 | Alphabetical sort
      .forEach(letter => {
        sections.push({
          title: letter,
          data: grouped[letter].sort((a, b) => a.name.localeCompare(b.name)), // 名字排序 | Sort by name
        });
      });

    return sections;
  };

  // 使用示例 | Usage example
  const flatContacts: Contact[] = [
    { id: '1', name: 'Charlie Brown', phone: '555-0301' },
    { id: '2', name: 'Alice Johnson', phone: '555-0101' },
    { id: '3', name: 'Bob Wilson', phone: '555-0201' },
  ];

  const sectioned = groupContactsByLetter(flatContacts);
  // 结果: [{ title: 'A', data: [Alice] }, { title: 'B', data: [Bob] }, { title: 'C', data: [Charlie] }]
  // Result: [{ title: 'A', data: [Alice] }, { title: 'B', data: [Bob] }, { title: 'C', data: [Charlie] }]
  ```

  **实践检查问题 | Practice Checking Questions:**
  - groupContactsByLetter函数如何处理首字母相同的联系人？| How does groupContactsByLetter handle contacts with the same first letter?
    **答案 | Answer:** 它们会被放在同一个section中，并按name字段的字母顺序排序 | They are placed in the same section and sorted alphabetically by name field

  - 如果contacts数组为空，函数会返回什么？| What does the function return if contacts array is empty?
    **答案 | Answer:** 空数组[] - 因为没有联系人可以分组 | Empty array [] - because there are no contacts to group

  **常见误区检查 | Common Misconception Checks:**
  - 可以在section对象中直接存储数组而非使用data属性吗？| Can you store an array directly in section object instead of using data property?
    **答案 | Answer:** 不可以 | No - SectionList要求每个section必须有data属性。如果直接传递数组会导致类型错误。| SectionList requires each section to have a data property. Passing an array directly will cause type errors.

### 2. renderSectionHeader自定义 | renderSectionHeader Customization (1小时 | 1 hour)

- **Section Header渲染机制 | Section Header Rendering Mechanism**

  **概念定义 | Concept Definition:**
  renderSectionHeader是SectionList的核心属性，用于定义每个section header的渲染逻辑。它接收包含section信息的对象作为参数，返回要渲染的React元素。Header的样式和交互直接影响用户体验。| renderSectionHeader is a core prop of SectionList that defines the rendering logic for each section header. It receives an object containing section information as parameter and returns the React element to render. Header style and interaction directly affect user experience.

  **核心特征 | Key Characteristics:**
  - 函数签名: ({ section, ...otherProps }) => React.Element | Function signature: ({ section, ...otherProps }) => React.Element
  - section参数包含当前section的所有数据(title, data, 自定义属性等) | section parameter contains all data of current section (title, data, custom properties, etc.)
  - 每个section header独立渲染，可以有不同样式 | Each section header renders independently and can have different styles
  - 支持sticky效果，滚动时保持在顶部 | Supports sticky effect, stays at top during scrolling
  - 可以添加交互功能(点击折叠/展开等) | Can add interactive features (click to collapse/expand, etc.)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. renderSectionHeader函数接收的section参数包含什么？| What does the section parameter received by renderSectionHeader contain?
     **答案 | Answer:** 包含sections数组中对应section对象的所有属性，如title、data及任何自定义属性 | Contains all properties of the corresponding section object from sections array, like title, data, and any custom properties

  2. 如果不提供renderSectionHeader属性会怎样？| What happens if renderSectionHeader prop is not provided?
     **答案 | Answer:** Section headers将不会被渲染，只显示数据项。但sections结构仍然存在。| Section headers will not be rendered, only data items will show. But sections structure still exists.

  3. 可以在renderSectionHeader中访问当前section的data吗？| Can you access current section's data in renderSectionHeader?
     **答案 | Answer:** 可以 | Yes - 通过section.data可以访问，例如显示该section的项目数量 | Can access via section.data, for example to display the count of items in that section

  4. renderSectionHeader的返回值必须是什么类型？| What type must renderSectionHeader's return value be?
     **答案 | Answer:** React元素(JSX) | React element (JSX) - 可以是任何有效的React组件或原生组件 | Can be any valid React component or native component

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 高级renderSectionHeader示例 | Advanced renderSectionHeader example
  import React from 'react';
  import { SectionList, Text, View, StyleSheet, TouchableOpacity } from 'react-native';

  const AdvancedHeaderExample = () => {
    // 示例1: 显示section项目数量的header | Example 1: Header showing section item count
    const renderSectionHeaderWithCount = ({ section }) => (
      <View style={styles.headerWithCount}>
        <Text style={styles.headerTitle}>{section.title}</Text>
        <Text style={styles.headerCount}>
          {section.data.length} items {/* 显示该section的数据项数量 | Show count of items in section */}
        </Text>
      </View>
    );

    // 示例2: 可折叠的section header | Example 2: Collapsible section header
    const [collapsedSections, setCollapsedSections] = React.useState<string[]>([]);

    const toggleSection = (sectionTitle: string) => {
      setCollapsedSections(prev =>
        prev.includes(sectionTitle)
          ? prev.filter(title => title !== sectionTitle) // 展开 | Expand
          : [...prev, sectionTitle] // 折叠 | Collapse
      );
    };

    const renderCollapsibleHeader = ({ section }) => {
      const isCollapsed = collapsedSections.includes(section.title);

      return (
        <TouchableOpacity
          style={styles.collapsibleHeader}
          onPress={() => toggleSection(section.title)}
          activeOpacity={0.7}
        >
          <Text style={styles.headerTitle}>{section.title}</Text>
          <Text style={styles.collapseIcon}>
            {isCollapsed ? '▼' : '▲'} {/* 折叠指示器 | Collapse indicator */}
          </Text>
        </TouchableOpacity>
      );
    };

    // 示例3: 根据section属性自定义样式 | Example 3: Custom style based on section properties
    const renderStyledHeader = ({ section }) => {
      // 假设section有isPriority属性 | Assume section has isPriority property
      const headerStyle = [
        styles.styledHeader,
        section.isPriority && styles.priorityHeader, // 条件样式 | Conditional style
      ];

      return (
        <View style={headerStyle}>
          <Text style={styles.headerTitle}>
            {section.isPriority && '⭐ '} {/* 优先级标记 | Priority marker */}
            {section.title}
          </Text>
          {section.subtitle && ( // 可选副标题 | Optional subtitle
            <Text style={styles.headerSubtitle}>{section.subtitle}</Text>
          )}
        </View>
      );
    };

    // 测试数据 | Test data
    const sections = [
      {
        title: 'Important',
        data: ['Task 1', 'Task 2'],
        isPriority: true,
        subtitle: 'Urgent items',
      },
      {
        title: 'Regular',
        data: ['Task 3', 'Task 4', 'Task 5'],
        isPriority: false,
      },
    ];

    return (
      <SectionList
        sections={sections}
        keyExtractor={(item, index) => item + index}
        renderItem={({ item }) => (
          <View style={styles.item}>
            <Text>{item}</Text>
          </View>
        )}
        renderSectionHeader={renderStyledHeader} // 使用自定义header | Use custom header
        stickySectionHeadersEnabled={true}
      />
    );
  };

  const styles = StyleSheet.create({
    headerWithCount: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      backgroundColor: '#f8f8f8',
      padding: 12,
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
      backgroundColor: '#e0e0e0',
      paddingHorizontal: 8,
      paddingVertical: 2,
      borderRadius: 10,
    },
    collapsibleHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      backgroundColor: '#f0f0f0',
      padding: 12,
    },
    collapseIcon: {
      fontSize: 14,
      color: '#007AFF',
    },
    styledHeader: {
      backgroundColor: '#f5f5f5',
      padding: 12,
      borderLeftWidth: 4,
      borderLeftColor: '#ccc',
    },
    priorityHeader: {
      backgroundColor: '#fff3e0', // 橙色背景表示优先级 | Orange background for priority
      borderLeftColor: '#ff9800',
    },
    headerSubtitle: {
      fontSize: 12,
      color: '#666',
      marginTop: 4,
    },
    item: {
      padding: 16,
      backgroundColor: 'white',
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - renderCollapsibleHeader中的isCollapsed变量是如何确定的？| How is the isCollapsed variable determined in renderCollapsibleHeader?
    **答案 | Answer:** 通过检查collapsedSections状态数组中是否包含当前section.title来确定 | Determined by checking if collapsedSections state array includes current section.title

  - 如果section对象没有isPriority属性，renderStyledHeader会报错吗？| Will renderStyledHeader throw an error if section object doesn't have isPriority property?
    **答案 | Answer:** 不会 | No - JavaScript中访问不存在的属性返回undefined，条件样式简单不会被应用 | In JavaScript accessing non-existent property returns undefined, conditional style simply won't be applied

  **常见误区检查 | Common Misconception Checks:**
  - renderSectionHeader可以返回null来隐藏header吗？| Can renderSectionHeader return null to hide headers?
    **答案 | Answer:** 可以 | Yes - 返回null会导致该section不渲染header，但section结构仍然存在 | Returning null will prevent that section from rendering a header, but section structure still exists

  - 在renderSectionHeader中修改section对象的属性会影响数据吗？| Will modifying section object properties in renderSectionHeader affect the data?
    **答案 | Answer:** 不会直接影响原始数据 | Won't directly affect original data - 但不应该在render函数中修改对象，这违反React不可变性原则 | But you shouldn't modify objects in render functions, this violates React's immutability principle

### 3. Sticky Headers实现与优化 | Sticky Headers Implementation and Optimization (1小时 | 1 hour)

- **Sticky Headers工作原理 | Sticky Headers Working Principle**

  **概念定义 | Concept Definition:**
  Sticky Headers是指在滚动列表时，当前可见section的header会固定在列表顶部，直到下一个section的header将其推走。这种效果在iOS通讯录、设置应用中很常见，提供了更好的上下文感知。| Sticky Headers refers to the current visible section's header staying fixed at the top of the list during scrolling, until the next section's header pushes it away. This effect is common in iOS Contacts and Settings apps, providing better contextual awareness.

  **核心特征 | Key Characteristics:**
  - 通过stickySectionHeadersEnabled属性控制(iOS默认true，Android默认false) | Controlled by stickySectionHeadersEnabled prop (iOS default true, Android default false)
  - 使用原生动画实现，性能优秀 | Implemented with native animations, excellent performance
  - Header在滚动时保持在列表顶部，不随内容滚动 | Header stays at list top during scrolling, doesn't scroll with content
  - 当下一个header到达顶部时，会推走当前sticky header | When next header reaches top, it pushes away current sticky header
  - 可以自定义sticky header的样式和z-index | Can customize sticky header's style and z-index

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. stickySectionHeadersEnabled在iOS和Android上的默认值相同吗？| Is stickySectionHeadersEnabled's default value the same on iOS and Android?
     **答案 | Answer:** 不同 | No - iOS默认为true(启用)，Android默认为false(禁用) | iOS defaults to true (enabled), Android defaults to false (disabled)

  2. Sticky header会影响列表的滚动性能吗？| Do sticky headers affect list scrolling performance?
     **答案 | Answer:** 基本不会 | Basically no - React Native使用原生实现，sticky效果在原生层处理，对JavaScript线程影响很小 | React Native uses native implementation, sticky effect is handled at native layer with minimal impact on JavaScript thread

  3. 可以让某些section的header sticky而其他不sticky吗？| Can some section headers be sticky while others are not?
     **答案 | Answer:** 不可以 | No - stickySectionHeadersEnabled是全局属性，要么所有headers都sticky，要么都不sticky。如需细粒度控制需要自定义实现。| stickySectionHeadersEnabled is a global prop, either all headers are sticky or none are. Fine-grained control requires custom implementation.

  4. Sticky header的z-index如何控制？| How is sticky header's z-index controlled?
     **答案 | Answer:** 通过在header样式中设置zIndex属性，或使用elevation(Android)和shadowOpacity(iOS)来控制层级 | By setting zIndex property in header styles, or using elevation (Android) and shadowOpacity (iOS) to control layering

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // Sticky Headers实现和样式优化 | Sticky Headers implementation and style optimization
  import React from 'react';
  import {
    SectionList,
    Text,
    View,
    StyleSheet,
    Platform,
    StatusBar
  } from 'react-native';

  const StickyHeadersExample = () => {
    // 生成测试数据 | Generate test data
    const sections = Array.from({ length: 10 }, (_, i) => ({
      title: `Section ${String.fromCharCode(65 + i)}`, // A, B, C...
      data: Array.from({ length: 15 }, (_, j) => `Item ${i}-${j}`),
    }));

    // 优化的sticky header渲染 | Optimized sticky header rendering
    const renderSectionHeader = ({ section }) => (
      <View style={styles.stickyHeader}>
        <Text style={styles.stickyHeaderText}>{section.title}</Text>
        <View style={styles.headerUnderline} />
      </View>
    );

    const renderItem = ({ item, section, index }) => (
      <View style={styles.item}>
        <Text style={styles.itemText}>{item}</Text>
      </View>
    );

    return (
      <View style={styles.container}>
        <SectionList
          sections={sections}
          keyExtractor={(item, index) => item + index}
          renderItem={renderItem}
          renderSectionHeader={renderSectionHeader}
          stickySectionHeadersEnabled={true} // 启用sticky headers | Enable sticky headers
          // 性能优化属性 | Performance optimization props
          removeClippedSubviews={Platform.OS === 'android'} // Android优化 | Android optimization
          maxToRenderPerBatch={10}
          windowSize={10}
          initialNumToRender={20}
          // 滚动优化 | Scroll optimization
          onScrollToIndexFailed={(info) => {
            console.warn('Scroll to index failed:', info);
          }}
        />
      </View>
    );
  };

  // 跨平台sticky header样式 | Cross-platform sticky header styles
  const styles = StyleSheet.create({
    container: {
      flex: 1,
      paddingTop: Platform.OS === 'android' ? StatusBar.currentHeight : 0,
    },
    stickyHeader: {
      backgroundColor: '#f8f9fa',
      borderBottomWidth: 1,
      borderBottomColor: '#dee2e6',
      // 跨平台阴影效果 | Cross-platform shadow effect
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.1,
          shadowRadius: 4,
        },
        android: {
          elevation: 4, // Android使用elevation | Android uses elevation
        },
      }),
    },
    stickyHeaderText: {
      fontSize: 18,
      fontWeight: '700',
      color: '#212529',
      paddingHorizontal: 16,
      paddingVertical: 12,
    },
    headerUnderline: {
      height: 3,
      backgroundColor: '#007AFF',
      width: 40,
      marginLeft: 16,
      borderRadius: 2,
    },
    item: {
      backgroundColor: 'white',
      paddingVertical: 12,
      paddingHorizontal: 16,
      borderBottomWidth: StyleSheet.hairlineWidth,
      borderBottomColor: '#e9ecef',
    },
    itemText: {
      fontSize: 15,
      color: '#495057',
    },
  });

  // 高级示例：带搜索索引的sticky headers | Advanced example: sticky headers with search index
  const StickyHeadersWithIndex = () => {
    const [scrollToSection, setScrollToSection] = React.useState<string | null>(null);
    const sectionListRef = React.useRef<SectionList>(null);

    const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
    const sections = alphabet.map(letter => ({
      title: letter,
      data: Array.from({ length: 5 }, (_, i) => `${letter}${i + 1}`),
    }));

    // 滚动到指定section | Scroll to specific section
    const scrollToSectionByTitle = (title: string) => {
      const index = sections.findIndex(section => section.title === title);
      if (index !== -1 && sectionListRef.current) {
        sectionListRef.current.scrollToLocation({
          sectionIndex: index,
          itemIndex: 0,
          animated: true,
          viewPosition: 0, // 0 = top, 1 = bottom
        });
      }
    };

    return (
      <View style={styles.containerWithIndex}>
        <SectionList
          ref={sectionListRef}
          sections={sections}
          keyExtractor={(item, index) => item + index}
          renderItem={({ item }) => (
            <View style={styles.item}>
              <Text style={styles.itemText}>{item}</Text>
            </View>
          )}
          renderSectionHeader={({ section }) => (
            <View style={styles.stickyHeader}>
              <Text style={styles.stickyHeaderText}>{section.title}</Text>
            </View>
          )}
          stickySectionHeadersEnabled={true}
        />

        {/* 右侧字母索引 | Right-side alphabet index */}
        <View style={styles.alphabetIndex}>
          {alphabet.map(letter => (
            <Text
              key={letter}
              style={styles.indexLetter}
              onPress={() => scrollToSectionByTitle(letter)}
            >
              {letter}
            </Text>
          ))}
        </View>
      </View>
    );
  };

  // 扩展样式 | Extended styles
  const extendedStyles = StyleSheet.create({
    containerWithIndex: {
      flex: 1,
      flexDirection: 'row',
    },
    alphabetIndex: {
      position: 'absolute',
      right: 0,
      top: 0,
      bottom: 0,
      justifyContent: 'center',
      paddingVertical: 8,
      paddingHorizontal: 4,
      backgroundColor: 'rgba(255, 255, 255, 0.9)',
    },
    indexLetter: {
      fontSize: 10,
      fontWeight: '600',
      color: '#007AFF',
      paddingVertical: 2,
      textAlign: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么Android需要removeClippedSubviews而iOS不需要？| Why does Android need removeClippedSubviews while iOS doesn't?
    **答案 | Answer:** Android的视图回收机制与iOS不同，removeClippedSubviews可以帮助Android释放屏幕外的视图内存。iOS的原生优化已经处理得很好。| Android's view recycling mechanism differs from iOS, removeClippedSubviews helps Android release memory for off-screen views. iOS's native optimization already handles this well.

  - scrollToLocation方法的viewPosition参数有什么作用？| What does the viewPosition parameter do in scrollToLocation method?
    **答案 | Answer:** 控制目标项在屏幕中的位置，0表示顶部，1表示底部，0.5表示中间 | Controls where the target item appears on screen: 0 = top, 1 = bottom, 0.5 = middle

  **常见误区检查 | Common Misconception Checks:**
  - Sticky header的样式会自动与普通header不同吗？| Do sticky headers automatically have different styles from regular headers?
    **答案 | Answer:** 不会 | No - Sticky和非sticky状态使用相同的renderSectionHeader渲染，样式完全一致。需要通过额外逻辑来区分状态并应用不同样式。| Sticky and non-sticky states use the same renderSectionHeader rendering with identical styles. Need additional logic to differentiate states and apply different styles.

  - 在Android上设置stickySectionHeadersEnabled={true}无效吗？| Is setting stickySectionHeadersEnabled={true} ineffective on Android?
    **答案 | Answer:** 不是无效 | Not ineffective - 在Android上也完全有效，只是默认值是false。显式设置为true后Android也会有sticky效果。| Works perfectly on Android too, just the default value is false. Explicitly setting to true enables sticky effect on Android.
