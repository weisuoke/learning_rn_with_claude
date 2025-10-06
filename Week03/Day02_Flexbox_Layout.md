# React Native入门 - 第2天：Flexbox布局系统 | React Native Introduction - Day 2: Flexbox Layout System

## 学习目标 | Learning Objectives
- 理解React Native中Flexbox与Web Flexbox的关键区别 | Understand key differences between React Native Flexbox and Web Flexbox
- 掌握flexDirection、justifyContent和alignItems的使用 | Master the usage of flexDirection, justifyContent, and alignItems
- 学会使用flex属性创建比例布局 | Learn to create proportional layouts using flex properties
- 能够构建常见的移动端布局模式(header、footer等) | Build common mobile layout patterns (header, footer, etc.)
- 理解React Native中所有View都是flex容器的特性 | Understand that all Views in React Native are flex containers by default
- 掌握在移动端创建响应式布局的基础技巧 | Master fundamental techniques for creating responsive layouts on mobile

## 详细内容 | Detailed Content

### 1. React Native Flexbox基础概念 | React Native Flexbox Fundamentals (1小时 | 1 hour)

- **Flexbox是默认布局系统 | Flexbox is the Default Layout System**

  **概念定义 | Concept Definition:**
  在React Native中，所有的View组件默认就是flex容器，不需要像Web那样显式设置`display: flex`。这是React Native布局的核心特性，意味着每个View都可以使用Flexbox属性来控制其子元素的布局。| In React Native, all View components are flex containers by default, without needing to explicitly set `display: flex` like in web development. This is a core feature of React Native layout, meaning every View can use Flexbox properties to control the layout of its children.

  **核心特征 | Key Characteristics:**
  - React Native中不存在CSS，所有样式通过JavaScript对象定义 | No CSS exists in React Native; all styles are defined through JavaScript objects
  - 所有View组件自动成为flex容器，无需声明 | All View components automatically become flex containers without declaration
  - 默认的主轴方向是垂直的(column)，这与Web的默认水平方向(row)相反 | The default main axis direction is vertical (column), opposite to web's default horizontal (row)
  - 布局计算基于设备独立像素，自动适配不同屏幕密度 | Layout calculation is based on device-independent pixels, automatically adapting to different screen densities

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中，需要设置`display: flex`来启用Flexbox吗？| In React Native, do you need to set `display: flex` to enable Flexbox?
     **答案 | Answer:** 否 | No - 所有View组件默认就是flex容器 | All View components are flex containers by default
  2. React Native的默认flexDirection是什么？| What is the default flexDirection in React Native?
     **答案 | Answer:** column (垂直方向) | column (vertical direction) - 与Web的默认值row不同 | Different from web's default value of row
  3. React Native中可以使用CSS样式表吗？| Can you use CSS stylesheets in React Native?
     **答案 | Answer:** 否 | No - React Native使用JavaScript对象来定义样式 | React Native uses JavaScript objects to define styles
  4. 什么时候View组件表现为flex容器？| When does a View component behave as a flex container?
     **答案 | Answer:** 始终如此 | Always - 这是React Native的默认行为 | This is React Native's default behavior

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  // 基础Flexbox示例 | Basic Flexbox Example
  const BasicFlexboxExample = () => {
    return (
      <View style={styles.container}>
        {/* 不需要display: flex，View默认就是flex容器 */}
        {/* No need for display: flex, View is a flex container by default */}
        <View style={styles.box1}>
          <Text>盒子 1 | Box 1</Text>
        </View>
        <View style={styles.box2}>
          <Text>盒子 2 | Box 2</Text>
        </View>
        <View style={styles.box3}>
          <Text>盒子 3 | Box 3</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      // 默认flexDirection是'column'，子元素垂直排列
      // Default flexDirection is 'column', children are arranged vertically
      backgroundColor: '#f0f0f0',
      padding: 20,
    },
    box1: {
      height: 100,
      backgroundColor: '#ff6b6b',
      justifyContent: 'center',
      alignItems: 'center',
      marginBottom: 10,
    },
    box2: {
      height: 100,
      backgroundColor: '#4ecdc4',
      justifyContent: 'center',
      alignItems: 'center',
      marginBottom: 10,
    },
    box3: {
      height: 100,
      backgroundColor: '#45b7d1',
      justifyContent: 'center',
      alignItems: 'center',
    },
  });

  export default BasicFlexboxExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中的三个盒子会如何排列？| How will the three boxes be arranged in this code?
    **答案 | Answer:** 垂直排列(从上到下) | Vertically arranged (top to bottom) - 因为默认flexDirection是column | Because default flexDirection is column
  - 如果移除container的padding: 20会发生什么？| What happens if we remove padding: 20 from container?
    **答案 | Answer:** 盒子会紧贴屏幕边缘，没有外边距 | Boxes will be flush against screen edges with no margin

  **常见误区检查 | Common Misconception Checks:**
  - React Native的Flexbox和Web的Flexbox完全相同吗？| Is React Native Flexbox exactly the same as Web Flexbox?
    **答案 | Answer:** 否 | No - 主要区别包括默认flexDirection不同(column vs row)、不需要display:flex声明、某些CSS属性不可用 | Key differences include different default flexDirection (column vs row), no need for display:flex declaration, and some CSS properties are unavailable
  - 设置View的height后，它还会响应flex属性吗？| After setting a View's height, will it still respond to flex properties?
    **答案 | Answer:** 固定height会覆盖flex的行为 | Fixed height will override flex behavior - flex通常用于动态尺寸，与固定尺寸互斥 | flex is typically used for dynamic sizing and is mutually exclusive with fixed dimensions

### 2. FlexDirection - 控制主轴方向 | FlexDirection - Controlling Main Axis Direction (1小时 | 1 hour)

- **主轴方向的选择 | Choosing Main Axis Direction**

  **概念定义 | Concept Definition:**
  flexDirection属性决定了Flexbox容器的主轴方向，即子元素的排列方向。React Native支持四个值：'column'(默认，垂直从上到下)、'column-reverse'(垂直从下到上)、'row'(水平从左到右)、'row-reverse'(水平从右到左)。理解主轴和交叉轴的概念是掌握Flexbox的关键。| The flexDirection property determines the main axis direction of a Flexbox container, i.e., the arrangement direction of child elements. React Native supports four values: 'column' (default, vertical top to bottom), 'column-reverse' (vertical bottom to top), 'row' (horizontal left to right), 'row-reverse' (horizontal right to left). Understanding the concept of main axis and cross axis is key to mastering Flexbox.

  **核心特征 | Key Characteristics:**
  - flexDirection为'row'时，主轴是水平的，子元素横向排列 | When flexDirection is 'row', the main axis is horizontal, children are arranged horizontally
  - flexDirection为'column'时，主轴是垂直的，子元素纵向排列 | When flexDirection is 'column', the main axis is vertical, children are arranged vertically
  - 主轴方向影响justifyContent的工作方向 | Main axis direction affects the working direction of justifyContent
  - 交叉轴始终垂直于主轴 | Cross axis is always perpendicular to main axis
  - reverse变体会反转子元素的排列顺序 | Reverse variants reverse the order of child elements

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 当flexDirection是'row'时，justifyContent控制的是哪个方向？| When flexDirection is 'row', which direction does justifyContent control?
     **答案 | Answer:** 水平方向(主轴) | Horizontal direction (main axis) - justifyContent总是沿着主轴工作 | justifyContent always works along the main axis
  2. flexDirection: 'column-reverse'会如何影响子元素排列？| How does flexDirection: 'column-reverse' affect child element arrangement?
     **答案 | Answer:** 子元素从下到上垂直排列，顺序相反 | Children are arranged vertically from bottom to top, in reverse order
  3. 主轴为'row'时，交叉轴是什么方向？| When the main axis is 'row', what direction is the cross axis?
     **答案 | Answer:** 垂直方向 | Vertical direction - 交叉轴永远垂直于主轴 | Cross axis is always perpendicular to main axis
  4. 改变flexDirection会影响alignItems的行为吗？| Does changing flexDirection affect alignItems behavior?
     **答案 | Answer:** 是 | Yes - alignItems沿交叉轴工作，交叉轴方向随主轴改变而改变 | alignItems works along cross axis, which changes as main axis changes

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, Text, TouchableOpacity, StyleSheet } from 'react-native';

  // FlexDirection交互式演示 | FlexDirection Interactive Demo
  const FlexDirectionDemo = () => {
    const [direction, setDirection] = useState('column');

    const directions = ['column', 'column-reverse', 'row', 'row-reverse'];

    return (
      <View style={styles.container}>
        <Text style={styles.title}>
          当前方向 | Current Direction: {direction}
        </Text>

        {/* 方向选择按钮 | Direction Selection Buttons */}
        <View style={styles.buttonContainer}>
          {directions.map((dir) => (
            <TouchableOpacity
              key={dir}
              style={[
                styles.button,
                direction === dir && styles.activeButton
              ]}
              onPress={() => setDirection(dir)}
            >
              <Text style={styles.buttonText}>{dir}</Text>
            </TouchableOpacity>
          ))}
        </View>

        {/* 动态flexDirection容器 | Dynamic flexDirection Container */}
        <View style={[styles.flexContainer, { flexDirection: direction }]}>
          <View style={[styles.box, { backgroundColor: '#ff6b6b' }]}>
            <Text style={styles.boxText}>1</Text>
          </View>
          <View style={[styles.box, { backgroundColor: '#4ecdc4' }]}>
            <Text style={styles.boxText}>2</Text>
          </View>
          <View style={[styles.box, { backgroundColor: '#45b7d1' }]}>
            <Text style={styles.boxText}>3</Text>
          </View>
          <View style={[styles.box, { backgroundColor: '#f9ca24' }]}>
            <Text style={styles.boxText}>4</Text>
          </View>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 15,
      textAlign: 'center',
    },
    buttonContainer: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      justifyContent: 'space-around',
      marginBottom: 20,
    },
    button: {
      padding: 10,
      backgroundColor: '#e0e0e0',
      borderRadius: 5,
      marginBottom: 10,
    },
    activeButton: {
      backgroundColor: '#6c5ce7',
    },
    buttonText: {
      fontSize: 12,
      color: '#333',
    },
    flexContainer: {
      flex: 1,
      backgroundColor: '#f0f0f0',
      padding: 10,
      borderRadius: 10,
      // flexDirection会动态改变 | flexDirection changes dynamically
    },
    box: {
      width: 70,
      height: 70,
      justifyContent: 'center',
      alignItems: 'center',
      margin: 5,
      borderRadius: 8,
    },
    boxText: {
      color: '#fff',
      fontSize: 24,
      fontWeight: 'bold',
    },
  });

  export default FlexDirectionDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当选择'row-reverse'时，数字1234的显示顺序是什么？| When selecting 'row-reverse', what is the display order of numbers 1234?
    **答案 | Answer:** 4321(从左到右) | 4321 (left to right) - reverse会反转子元素顺序 | reverse reverses child element order
  - 如果将flexContainer的flexDirection改为'row'，并且子盒子没有设置width，会发生什么？| If we change flexContainer's flexDirection to 'row' and child boxes have no width set, what happens?
    **答案 | Answer:** 如果没有width，盒子会收缩到最小内容宽度 | Without width, boxes will shrink to minimum content width

  **常见误区检查 | Common Misconception Checks:**
  - flexDirection只影响子元素的排列方向，不影响其他flex属性吗？| Does flexDirection only affect child element arrangement direction, not other flex properties?
    **答案 | Answer:** 否 | No - flexDirection定义主轴，从而影响justifyContent(主轴)和alignItems(交叉轴)的工作方向 | flexDirection defines main axis, thus affecting working directions of justifyContent (main axis) and alignItems (cross axis)
  - 'row-reverse'和先设置'row'再用transform旋转效果一样吗？| Is 'row-reverse' the same as setting 'row' then rotating with transform?
    **答案 | Answer:** 否 | No - row-reverse改变flex布局顺序，transform只是视觉旋转，不影响布局流 | row-reverse changes flex layout order, transform is only visual rotation without affecting layout flow

### 3. JustifyContent和AlignItems - 主轴与交叉轴对齐 | JustifyContent and AlignItems - Main and Cross Axis Alignment (1小时 | 1 hour)

- **双轴对齐控制 | Dual-Axis Alignment Control**

  **概念定义 | Concept Definition:**
  justifyContent控制子元素在主轴上的分布和对齐方式，alignItems控制子元素在交叉轴上的对齐方式。这两个属性配合flexDirection使用，可以实现各种复杂的布局模式。常用值包括：flex-start、flex-end、center、space-between、space-around、space-evenly(justifyContent)；stretch、flex-start、flex-end、center、baseline(alignItems)。| justifyContent controls the distribution and alignment of child elements along the main axis, while alignItems controls the alignment of child elements along the cross axis. These two properties work with flexDirection to achieve various complex layout patterns. Common values include: flex-start, flex-end, center, space-between, space-around, space-evenly (justifyContent); stretch, flex-start, flex-end, center, baseline (alignItems).

  **核心特征 | Key Characteristics:**
  - justifyContent的space-between会在子元素之间均匀分配空间，首尾无间距 | justifyContent's space-between evenly distributes space between children, with no spacing at start/end
  - justifyContent的space-around会在每个子元素周围分配相等空间 | justifyContent's space-around distributes equal space around each child
  - alignItems的stretch(默认值)会拉伸子元素填满交叉轴 | alignItems' stretch (default value) stretches children to fill cross axis
  - alignItems只影响单行布局，多行需要使用alignContent | alignItems only affects single-line layout, multi-line requires alignContent
  - 子元素可以使用alignSelf覆盖父容器的alignItems设置 | Children can use alignSelf to override parent's alignItems setting

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. justifyContent: 'space-between'会在第一个和最后一个子元素外侧留空间吗？| Does justifyContent: 'space-between' leave space outside the first and last child elements?
     **答案 | Answer:** 否 | No - space-between只在子元素之间分配空间，首尾紧贴容器边缘 | space-between only distributes space between children, start/end flush with container edges
  2. 当flexDirection是'row'时，alignItems: 'center'会如何对齐子元素？| When flexDirection is 'row', how does alignItems: 'center' align children?
     **答案 | Answer:** 垂直居中对齐 | Vertically center aligned - alignItems沿交叉轴(垂直)工作 | alignItems works along cross axis (vertical)
  3. alignItems的默认值是什么？| What is the default value of alignItems?
     **答案 | Answer:** stretch - 子元素会拉伸填满交叉轴(如果没有设置固定尺寸) | stretch - children stretch to fill cross axis (if no fixed dimension is set)
  4. space-around和space-evenly的区别是什么？| What's the difference between space-around and space-evenly?
     **答案 | Answer:** space-around每个元素周围空间相等(边缘空间是中间的一半)，space-evenly所有空间完全相等 | space-around has equal space around each element (edge space is half of middle), space-evenly has completely equal spacing
  5. justifyContent可以用于交叉轴吗？| Can justifyContent be used for the cross axis?
     **答案 | Answer:** 否 | No - justifyContent只作用于主轴，交叉轴使用alignItems或alignContent | justifyContent only works on main axis, cross axis uses alignItems or alignContent

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, ScrollView, StyleSheet } from 'react-native';

  // 对齐属性综合演示 | Comprehensive Alignment Demo
  const AlignmentDemo = () => {
    // 不同的justifyContent值 | Different justifyContent values
    const justifyValues = [
      'flex-start',
      'flex-end',
      'center',
      'space-between',
      'space-around',
      'space-evenly'
    ];

    // 不同的alignItems值 | Different alignItems values
    const alignValues = ['flex-start', 'flex-end', 'center', 'stretch'];

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.sectionTitle}>
          JustifyContent 示例 | JustifyContent Examples
        </Text>

        {justifyValues.map((value) => (
          <View key={value} style={styles.exampleContainer}>
            <Text style={styles.label}>{value}</Text>
            <View style={[styles.row, { justifyContent: value }]}>
              <View style={styles.smallBox} />
              <View style={styles.smallBox} />
              <View style={styles.smallBox} />
            </View>
          </View>
        ))}

        <Text style={styles.sectionTitle}>
          AlignItems 示例 | AlignItems Examples
        </Text>

        {alignValues.map((value) => (
          <View key={value} style={styles.exampleContainer}>
            <Text style={styles.label}>{value}</Text>
            <View style={[styles.column, { alignItems: value }]}>
              <View style={[styles.smallBox, { height: 30 }]} />
              <View style={[styles.smallBox, { height: 50 }]} />
              <View style={[styles.smallBox, { height: 40 }]} />
            </View>
          </View>
        ))}

        <Text style={styles.sectionTitle}>
          组合使用 | Combined Usage
        </Text>

        <View style={styles.exampleContainer}>
          <Text style={styles.label}>
            居中布局 | Centered Layout{'\n'}
            (justifyContent: center + alignItems: center)
          </Text>
          <View style={styles.centeredContainer}>
            <View style={styles.centeredBox}>
              <Text style={styles.centeredText}>完全居中 | Centered</Text>
            </View>
          </View>
        </View>
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
      padding: 15,
    },
    sectionTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginTop: 20,
      marginBottom: 15,
      color: '#2c3e50',
    },
    exampleContainer: {
      marginBottom: 20,
    },
    label: {
      fontSize: 14,
      color: '#7f8c8d',
      marginBottom: 8,
      fontWeight: '600',
    },
    row: {
      flexDirection: 'row',
      height: 80,
      backgroundColor: '#ecf0f1',
      borderRadius: 8,
      padding: 10,
    },
    column: {
      flexDirection: 'row', // 用row展示alignItems的垂直对齐效果
      height: 100,
      backgroundColor: '#ecf0f1',
      borderRadius: 8,
      padding: 10,
    },
    smallBox: {
      width: 50,
      height: 50,
      backgroundColor: '#3498db',
      borderRadius: 5,
    },
    centeredContainer: {
      height: 150,
      backgroundColor: '#ecf0f1',
      borderRadius: 8,
      justifyContent: 'center',
      alignItems: 'center',
    },
    centeredBox: {
      width: 120,
      height: 80,
      backgroundColor: '#e74c3c',
      borderRadius: 10,
      justifyContent: 'center',
      alignItems: 'center',
    },
    centeredText: {
      color: '#fff',
      fontWeight: 'bold',
    },
  });

  export default AlignmentDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在space-evenly布局中，如果有3个子元素，会产生几个相等的空间？| In a space-evenly layout with 3 child elements, how many equal spaces are created?
    **答案 | Answer:** 4个相等空间 | 4 equal spaces - 元素前、元素间(2个)、元素后各一个 | One before elements, two between elements, one after
  - 当alignItems设置为'stretch'但子元素设置了固定height，子元素会被拉伸吗？| When alignItems is set to 'stretch' but child has fixed height, will child be stretched?
    **答案 | Answer:** 否 | No - 固定尺寸会覆盖stretch行为 | Fixed dimension overrides stretch behavior

  **常见误区检查 | Common Misconception Checks:**
  - justifyContent和alignItems可以互换使用吗？| Can justifyContent and alignItems be used interchangeably?
    **答案 | Answer:** 否 | No - justifyContent控制主轴，alignItems控制交叉轴，它们作用于不同的轴向 | justifyContent controls main axis, alignItems controls cross axis, they work on different axes
  - 设置alignItems: 'center'后，所有子元素都会居中吗？| After setting alignItems: 'center', will all children be centered?
    **答案 | Answer:** 只在交叉轴上居中 | Only centered on cross axis - 主轴位置由justifyContent控制 | Main axis position is controlled by justifyContent

### 4. Flex属性 - 创建比例布局 | Flex Property - Creating Proportional Layouts (1小时 | 1 hour)

- **动态空间分配 | Dynamic Space Distribution**

  **概念定义 | Concept Definition:**
  flex属性定义了flex容器中子元素如何分配可用空间。在React Native中，flex的行为与Web略有不同：flex: 1表示该元素会扩展并填充可用空间。当多个子元素都设置flex值时，它们会按比例分配空间。flex只接受数字值，常用flex: 1使元素占满剩余空间。| The flex property defines how child elements in a flex container distribute available space. In React Native, flex behaves slightly differently from web: flex: 1 means the element will expand and fill available space. When multiple children have flex values, they distribute space proportionally. flex only accepts numeric values, commonly using flex: 1 to make elements fill remaining space.

  **核心特征 | Key Characteristics:**
  - flex: 1会使元素扩展填充所有可用空间 | flex: 1 makes element expand to fill all available space
  - 多个flex元素按其flex值的比例分配空间(如flex:1和flex:2按1:2分配) | Multiple flex elements distribute space proportionally to their flex values (e.g., flex:1 and flex:2 distribute 1:2)
  - flex: 0使元素根据其内容或固定尺寸确定大小 | flex: 0 makes element size based on its content or fixed dimensions
  - flex值为负数时会被视为flex: 0 | Negative flex values are treated as flex: 0
  - flex与固定尺寸(width/height)不能同时有效工作 | flex and fixed dimensions (width/height) cannot work together effectively

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果容器中有两个子元素，一个flex: 1，一个flex: 2，它们如何分配空间？| If a container has two children, one with flex: 1 and one with flex: 2, how do they distribute space?
     **答案 | Answer:** 1:2的比例 | 1:2 ratio - flex:1的元素占1/3空间，flex:2的元素占2/3空间 | flex:1 element takes 1/3 space, flex:2 element takes 2/3 space
  2. flex: 1和width: '100%'有什么区别？| What's the difference between flex: 1 and width: '100%'?
     **答案 | Answer:** flex:1会填充可用空间并响应容器变化，width:'100%'是固定百分比可能导致溢出 | flex:1 fills available space and responds to container changes, width:'100%' is fixed percentage that may cause overflow
  3. 给元素同时设置flex: 1和height: 100会发生什么？| What happens if you set both flex: 1 and height: 100 on an element?
     **答案 | Answer:** height会被忽略，flex: 1控制元素高度 | height is ignored, flex: 1 controls element height - flex优先级更高 | flex has higher priority
  4. flex: 0的元素会占用空间吗？| Does an element with flex: 0 take up space?
     **答案 | Answer:** 是，根据其内容或设置的固定尺寸 | Yes, based on its content or set fixed dimensions - 但不会扩展填充额外空间 | But won't expand to fill extra space

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet, ScrollView } from 'react-native';

  // Flex比例布局演示 | Flex Proportional Layout Demo
  const FlexProportionalDemo = () => {
    return (
      <ScrollView style={styles.container}>
        <Text style={styles.title}>
          Flex比例布局示例 | Flex Proportional Layout Examples
        </Text>

        {/* 示例1: 等分布局 | Example 1: Equal Distribution */}
        <View style={styles.section}>
          <Text style={styles.subtitle}>
            等分布局 (flex: 1 for all) | Equal Distribution
          </Text>
          <View style={styles.flexContainer}>
            <View style={[styles.box, styles.box1, { flex: 1 }]}>
              <Text style={styles.boxText}>flex: 1</Text>
            </View>
            <View style={[styles.box, styles.box2, { flex: 1 }]}>
              <Text style={styles.boxText}>flex: 1</Text>
            </View>
            <View style={[styles.box, styles.box3, { flex: 1 }]}>
              <Text style={styles.boxText}>flex: 1</Text>
            </View>
          </View>
        </View>

        {/* 示例2: 不同比例 | Example 2: Different Ratios */}
        <View style={styles.section}>
          <Text style={styles.subtitle}>
            1:2:3 比例布局 | 1:2:3 Ratio Layout
          </Text>
          <View style={styles.flexContainer}>
            <View style={[styles.box, styles.box1, { flex: 1 }]}>
              <Text style={styles.boxText}>flex: 1{'\n'}(1/6)</Text>
            </View>
            <View style={[styles.box, styles.box2, { flex: 2 }]}>
              <Text style={styles.boxText}>flex: 2{'\n'}(2/6)</Text>
            </View>
            <View style={[styles.box, styles.box3, { flex: 3 }]}>
              <Text style={styles.boxText}>flex: 3{'\n'}(3/6)</Text>
            </View>
          </View>
        </View>

        {/* 示例3: 固定+弹性混合 | Example 3: Fixed + Flexible Mix */}
        <View style={styles.section}>
          <Text style={styles.subtitle}>
            固定宽度 + flex: 1 | Fixed Width + flex: 1
          </Text>
          <View style={[styles.flexContainer, { flexDirection: 'row' }]}>
            <View style={[styles.box, styles.box1, { width: 80 }]}>
              <Text style={styles.boxText}>80px{'\n'}固定</Text>
            </View>
            <View style={[styles.box, styles.box2, { flex: 1 }]}>
              <Text style={styles.boxText}>flex: 1{'\n'}填充剩余</Text>
            </View>
            <View style={[styles.box, styles.box3, { width: 60 }]}>
              <Text style={styles.boxText}>60px{'\n'}固定</Text>
            </View>
          </View>
        </View>

        {/* 示例4: 实际应用 - 聊天界面 | Example 4: Real Application - Chat UI */}
        <View style={styles.section}>
          <Text style={styles.subtitle}>
            实际应用：聊天输入栏 | Real App: Chat Input Bar
          </Text>
          <View style={styles.chatContainer}>
            <View style={styles.avatarPlaceholder}>
              <Text style={styles.avatarText}>头像</Text>
            </View>
            <View style={styles.inputContainer}>
              <Text style={styles.inputText}>输入消息... | Type message...</Text>
            </View>
            <View style={styles.sendButton}>
              <Text style={styles.sendText}>发送 | Send</Text>
            </View>
          </View>
        </View>

        {/* 示例5: 垂直比例布局 | Example 5: Vertical Proportional Layout */}
        <View style={styles.section}>
          <Text style={styles.subtitle}>
            垂直比例 (Header:Content:Footer = 1:4:1) | Vertical Ratio
          </Text>
          <View style={styles.verticalContainer}>
            <View style={[styles.vBox, styles.box1, { flex: 1 }]}>
              <Text style={styles.boxText}>Header (flex: 1)</Text>
            </View>
            <View style={[styles.vBox, styles.box2, { flex: 4 }]}>
              <Text style={styles.boxText}>Content (flex: 4)</Text>
            </View>
            <View style={[styles.vBox, styles.box3, { flex: 1 }]}>
              <Text style={styles.boxText}>Footer (flex: 1)</Text>
            </View>
          </View>
        </View>
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
      padding: 15,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
      color: '#2c3e50',
    },
    section: {
      marginBottom: 30,
    },
    subtitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 10,
      color: '#34495e',
    },
    flexContainer: {
      flexDirection: 'row',
      height: 100,
      backgroundColor: '#ecf0f1',
      borderRadius: 8,
      overflow: 'hidden',
    },
    box: {
      justifyContent: 'center',
      alignItems: 'center',
      padding: 5,
    },
    box1: {
      backgroundColor: '#3498db',
    },
    box2: {
      backgroundColor: '#2ecc71',
    },
    box3: {
      backgroundColor: '#e74c3c',
    },
    boxText: {
      color: '#fff',
      fontWeight: 'bold',
      textAlign: 'center',
      fontSize: 12,
    },
    // 聊天界面样式 | Chat UI Styles
    chatContainer: {
      flexDirection: 'row',
      height: 60,
      backgroundColor: '#f8f9fa',
      borderRadius: 30,
      padding: 5,
      alignItems: 'center',
    },
    avatarPlaceholder: {
      width: 50,
      height: 50,
      borderRadius: 25,
      backgroundColor: '#95a5a6',
      justifyContent: 'center',
      alignItems: 'center',
    },
    avatarText: {
      color: '#fff',
      fontSize: 10,
    },
    inputContainer: {
      flex: 1,
      marginHorizontal: 10,
      height: 40,
      backgroundColor: '#fff',
      borderRadius: 20,
      justifyContent: 'center',
      paddingHorizontal: 15,
    },
    inputText: {
      color: '#95a5a6',
    },
    sendButton: {
      width: 70,
      height: 50,
      backgroundColor: '#3498db',
      borderRadius: 25,
      justifyContent: 'center',
      alignItems: 'center',
    },
    sendText: {
      color: '#fff',
      fontWeight: 'bold',
    },
    // 垂直布局样式 | Vertical Layout Styles
    verticalContainer: {
      height: 300,
      backgroundColor: '#ecf0f1',
      borderRadius: 8,
      overflow: 'hidden',
    },
    vBox: {
      justifyContent: 'center',
      alignItems: 'center',
    },
  });

  export default FlexProportionalDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在1:2:3比例布局示例中，如果容器宽度是300px，flex: 2的盒子宽度是多少？| In the 1:2:3 ratio layout example, if container width is 300px, what is the width of the flex: 2 box?
    **答案 | Answer:** 100px - 总比例是6，flex:2占2/6，即300 * 2/6 = 100px | Total ratio is 6, flex:2 takes 2/6, which is 300 * 2/6 = 100px
  - 在聊天输入栏示例中，为什么输入框使用flex: 1？| In the chat input bar example, why does the input box use flex: 1?
    **答案 | Answer:** 让输入框填充头像和发送按钮之间的所有剩余空间，实现响应式布局 | To make input box fill all remaining space between avatar and send button, achieving responsive layout

  **常见误区检查 | Common Misconception Checks:**
  - flex: 2是flex: 1的两倍大小吗？| Is flex: 2 twice the size of flex: 1?
    **答案 | Answer:** 只有在它们是唯一的flex元素时才是 | Only if they are the only flex elements - 实际大小取决于所有flex元素的比例之和 | Actual size depends on the sum of all flex element ratios
  - 可以对flex元素设置margin吗？| Can you set margin on flex elements?
    **答案 | Answer:** 可以 | Yes - margin会减少可用空间，flex计算会考虑margin后的剩余空间 | margin reduces available space, flex calculation considers remaining space after margin

### 5. 构建常见移动端布局模式 | Building Common Mobile Layout Patterns (1小时 | 1 hour)

- **Header和Footer布局实践 | Header and Footer Layout Practice**

  **概念定义 | Concept Definition:**
  移动应用中最常见的布局模式包括固定Header(顶部导航栏)、可滚动Content(主内容区)和固定Footer(底部标签栏)。实现这种布局的关键是使用flex属性让中间内容区自动填充剩余空间，同时Header和Footer保持固定高度。这种模式是几乎所有移动应用的基础结构。| The most common layout pattern in mobile apps includes a fixed Header (top navigation bar), scrollable Content (main content area), and fixed Footer (bottom tab bar). The key to implementing this layout is using the flex property to make the middle content area automatically fill remaining space while Header and Footer maintain fixed heights. This pattern is the fundamental structure of almost all mobile applications.

  **核心特征 | Key Characteristics:**
  - Header通常固定高度(如60px)，包含导航元素和标题 | Header typically has fixed height (e.g., 60px), containing navigation elements and title
  - Content区域使用flex: 1填充所有剩余空间，通常可滚动 | Content area uses flex: 1 to fill all remaining space, usually scrollable
  - Footer固定高度，包含标签栏或操作按钮 | Footer has fixed height, containing tab bar or action buttons
  - 使用SafeAreaView确保内容不被刘海屏或底部手势条遮挡 | Use SafeAreaView to ensure content isn't obscured by notches or bottom gesture bars
  - Header和Footer通常使用flexDirection: 'row'来水平排列子元素 | Header and Footer typically use flexDirection: 'row' to arrange children horizontally

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么Content区域要使用flex: 1而不是固定高度？| Why should the Content area use flex: 1 instead of fixed height?
     **答案 | Answer:** 因为不同设备屏幕高度不同，flex:1可以自动适应剩余空间 | Because different devices have different screen heights, flex:1 automatically adapts to remaining space
  2. Header中如何实现"左侧图标、中间标题、右侧按钮"的布局？| How to achieve "left icon, center title, right button" layout in Header?
     **答案 | Answer:** 使用flexDirection:'row'，左右元素固定宽度，中间标题flex:1并设置textAlign:'center' | Use flexDirection:'row', fixed width for left/right elements, center title with flex:1 and textAlign:'center'
  3. Footer中的标签按钮如何平均分布？| How to evenly distribute tab buttons in Footer?
     **答案 | Answer:** 使用flexDirection:'row'和justifyContent:'space-around'或每个按钮设置flex:1 | Use flexDirection:'row' with justifyContent:'space-around' or set flex:1 on each button
  4. SafeAreaView解决什么问题？| What problem does SafeAreaView solve?
     **答案 | Answer:** 防止内容被设备的安全区域(如刘海、状态栏、底部手势条)遮挡 | Prevents content from being obscured by device safe areas (notch, status bar, bottom gesture bar)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import {
    View,
    Text,
    StyleSheet,
    SafeAreaView,
    ScrollView,
    TouchableOpacity,
  } from 'react-native';

  // 经典移动端布局 | Classic Mobile Layout
  const MobileLayoutPattern = () => {
    return (
      <SafeAreaView style={styles.container}>
        {/* Header - 固定顶部 | Header - Fixed Top */}
        <View style={styles.header}>
          <TouchableOpacity style={styles.headerButton}>
            <Text style={styles.headerButtonText}>☰</Text>
          </TouchableOpacity>

          <View style={styles.headerTitle}>
            <Text style={styles.headerTitleText}>
              我的应用 | My App
            </Text>
          </View>

          <TouchableOpacity style={styles.headerButton}>
            <Text style={styles.headerButtonText}>👤</Text>
          </TouchableOpacity>
        </View>

        {/* Content - 可滚动内容区 | Content - Scrollable Content Area */}
        <ScrollView style={styles.content}>
          <View style={styles.contentInner}>
            <Text style={styles.contentTitle}>
              主内容区 | Main Content Area
            </Text>

            {/* 示例内容卡片 | Example Content Cards */}
            {[1, 2, 3, 4, 5, 6].map((item) => (
              <View key={item} style={styles.card}>
                <Text style={styles.cardTitle}>
                  卡片 {item} | Card {item}
                </Text>
                <Text style={styles.cardText}>
                  这是一个内容卡片示例。flex: 1 让内容区自动填充Header和Footer之间的所有空间。
                  {'\n\n'}
                  This is a content card example. flex: 1 makes the content area automatically fill all space between Header and Footer.
                </Text>
              </View>
            ))}
          </View>
        </ScrollView>

        {/* Footer - 固定底部标签栏 | Footer - Fixed Bottom Tab Bar */}
        <View style={styles.footer}>
          <TouchableOpacity style={styles.tab}>
            <Text style={styles.tabIcon}>🏠</Text>
            <Text style={styles.tabText}>首页 | Home</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.tab}>
            <Text style={styles.tabIcon}>🔍</Text>
            <Text style={styles.tabText}>搜索 | Search</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.tab}>
            <Text style={styles.tabIcon}>➕</Text>
            <Text style={styles.tabText}>发布 | Post</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.tab}>
            <Text style={styles.tabIcon}>💬</Text>
            <Text style={styles.tabText}>消息 | Messages</Text>
          </TouchableOpacity>

          <TouchableOpacity style={styles.tab}>
            <Text style={styles.tabIcon}>👤</Text>
            <Text style={styles.tabText}>我的 | Me</Text>
          </TouchableOpacity>
        </View>
      </SafeAreaView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    // Header 样式 | Header Styles
    header: {
      height: 60,
      flexDirection: 'row',
      alignItems: 'center',
      backgroundColor: '#3498db',
      paddingHorizontal: 15,
      borderBottomWidth: 1,
      borderBottomColor: '#2980b9',
    },
    headerButton: {
      width: 40,
      height: 40,
      justifyContent: 'center',
      alignItems: 'center',
    },
    headerButtonText: {
      fontSize: 24,
      color: '#fff',
    },
    headerTitle: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    headerTitleText: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#fff',
    },
    // Content 样式 | Content Styles
    content: {
      flex: 1, // 关键：填充剩余空间 | Key: Fill remaining space
      backgroundColor: '#ecf0f1',
    },
    contentInner: {
      padding: 15,
    },
    contentTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 15,
      color: '#2c3e50',
    },
    card: {
      backgroundColor: '#fff',
      borderRadius: 10,
      padding: 15,
      marginBottom: 15,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    cardTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 8,
      color: '#34495e',
    },
    cardText: {
      fontSize: 14,
      color: '#7f8c8d',
      lineHeight: 20,
    },
    // Footer 样式 | Footer Styles
    footer: {
      height: 70,
      flexDirection: 'row',
      backgroundColor: '#fff',
      borderTopWidth: 1,
      borderTopColor: '#dfe6e9',
    },
    tab: {
      flex: 1, // 每个标签平均分配空间 | Each tab evenly distributes space
      justifyContent: 'center',
      alignItems: 'center',
    },
    tabIcon: {
      fontSize: 24,
      marginBottom: 4,
    },
    tabText: {
      fontSize: 10,
      color: '#636e72',
    },
  });

  export default MobileLayoutPattern;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果移除content的flex: 1会发生什么？| What happens if we remove flex: 1 from content?
    **答案 | Answer:** 内容区会收缩到最小高度(可能看不见)，因为没有扩展指令 | Content area will shrink to minimum height (possibly invisible) because there's no expansion directive
  - Footer中的每个tab为什么使用flex: 1？| Why does each tab in Footer use flex: 1?
    **答案 | Answer:** 让5个标签平均分配Footer的宽度，每个占1/5 | To make 5 tabs evenly distribute Footer width, each taking 1/5

  **常见误区检查 | Common Misconception Checks:**
  - Header设置固定高度后还需要设置flex吗？| After setting fixed height for Header, do you still need to set flex?
    **答案 | Answer:** 不需要 | No - 固定高度已经确定了Header的尺寸，flex用于动态尺寸 | Fixed height already determines Header size, flex is for dynamic sizing
  - SafeAreaView会影响Flexbox布局吗？| Does SafeAreaView affect Flexbox layout?
    **答案 | Answer:** 不影响 | No - SafeAreaView只是添加安全区域padding，内部flex布局正常工作 | SafeAreaView only adds safe area padding, internal flex layout works normally

### 6. 响应式技巧与最佳实践 | Responsive Techniques and Best Practices (30分钟 | 30 minutes)

- **移动端响应式设计原则 | Mobile Responsive Design Principles**

  **概念定义 | Concept Definition:**
  React Native的响应式设计不同于Web，主要依赖Flexbox的弹性特性而非媒体查询。核心原则是使用相对单位(flex、百分比)而非绝对像素，利用Dimensions API获取屏幕尺寸，使用aspectRatio保持元素比例，以及合理组合flexWrap处理内容溢出。这些技巧能让应用在不同尺寸设备上都有良好表现。| Responsive design in React Native differs from web, primarily relying on Flexbox's flexibility rather than media queries. Core principles include using relative units (flex, percentages) instead of absolute pixels, utilizing the Dimensions API to get screen size, using aspectRatio to maintain element proportions, and properly combining flexWrap to handle content overflow. These techniques ensure apps perform well on different device sizes.

  **核心特征 | Key Characteristics:**
  - 优先使用flex和百分比，避免硬编码像素值 | Prioritize flex and percentages, avoid hardcoded pixel values
  - 使用Dimensions.get('window')获取设备屏幕尺寸 | Use Dimensions.get('window') to get device screen dimensions
  - aspectRatio属性可以保持元素宽高比，适合图片和卡片 | aspectRatio property maintains element aspect ratio, suitable for images and cards
  - flexWrap: 'wrap'允许子元素换行，防止溢出 | flexWrap: 'wrap' allows children to wrap, preventing overflow
  - 使用Platform API检测平台，应用特定样式 | Use Platform API to detect platform and apply specific styles

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么移动端响应式设计更依赖Flexbox而非媒体查询？| Why does mobile responsive design rely more on Flexbox than media queries?
     **答案 | Answer:** React Native没有CSS媒体查询，Flexbox的弹性特性天然适合不同屏幕尺寸 | React Native has no CSS media queries, Flexbox's flexible nature is naturally suited for different screen sizes
  2. aspectRatio: 1意味着什么？| What does aspectRatio: 1 mean?
     **答案 | Answer:** 元素的宽高比为1:1(正方形) | Element's width-to-height ratio is 1:1 (square)
  3. flexWrap: 'wrap'解决什么问题？| What problem does flexWrap: 'wrap' solve?
     **答案 | Answer:** 当子元素总宽度超过容器宽度时，允许换行显示，防止溢出 | When total child width exceeds container width, allows wrapping to prevent overflow
  4. Dimensions API获取的尺寸单位是什么？| What unit does Dimensions API return?
     **答案 | Answer:** 设备独立像素(dp/pt) | Device-independent pixels (dp/pt) - 不是物理像素 | Not physical pixels

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import {
    View,
    Text,
    StyleSheet,
    Dimensions,
    ScrollView,
    Platform,
  } from 'react-native';

  // 获取屏幕尺寸 | Get Screen Dimensions
  const { width: SCREEN_WIDTH, height: SCREEN_HEIGHT } = Dimensions.get('window');

  // 响应式设计最佳实践 | Responsive Design Best Practices
  const ResponsiveBestPractices = () => {
    return (
      <ScrollView style={styles.container}>
        {/* 1. 使用百分比宽度 | 1. Using Percentage Width */}
        <View style={styles.section}>
          <Text style={styles.title}>
            百分比宽度布局 | Percentage Width Layout
          </Text>
          <View style={styles.percentageContainer}>
            <View style={[styles.percentBox, { width: '30%' }]}>
              <Text style={styles.boxText}>30%</Text>
            </View>
            <View style={[styles.percentBox, { width: '70%' }]}>
              <Text style={styles.boxText}>70%</Text>
            </View>
          </View>
        </View>

        {/* 2. AspectRatio保持比例 | 2. AspectRatio Maintains Proportion */}
        <View style={styles.section}>
          <Text style={styles.title}>
            宽高比布局 | Aspect Ratio Layout
          </Text>
          <View style={styles.aspectContainer}>
            <View style={styles.aspectBox}>
              <Text style={styles.boxText}>
                aspectRatio: 16/9{'\n'}(宽屏比例)
              </Text>
            </View>
          </View>
        </View>

        {/* 3. FlexWrap响应式网格 | 3. FlexWrap Responsive Grid */}
        <View style={styles.section}>
          <Text style={styles.title}>
            自适应网格 (flexWrap) | Adaptive Grid (flexWrap)
          </Text>
          <View style={styles.gridContainer}>
            {[1, 2, 3, 4, 5, 6, 7, 8].map((item) => (
              <View key={item} style={styles.gridItem}>
                <Text style={styles.gridText}>{item}</Text>
              </View>
            ))}
          </View>
        </View>

        {/* 4. 基于屏幕尺寸的动态布局 | 4. Dynamic Layout Based on Screen Size */}
        <View style={styles.section}>
          <Text style={styles.title}>
            屏幕尺寸信息 | Screen Size Info
          </Text>
          <View style={styles.infoContainer}>
            <Text style={styles.infoText}>
              屏幕宽度 | Width: {SCREEN_WIDTH.toFixed(0)}dp
            </Text>
            <Text style={styles.infoText}>
              屏幕高度 | Height: {SCREEN_HEIGHT.toFixed(0)}dp
            </Text>
            <Text style={styles.infoText}>
              平台 | Platform: {Platform.OS}
            </Text>
            <Text style={styles.infoText}>
              设备类型 | Device: {SCREEN_WIDTH > 600 ? 'Tablet' : 'Phone'}
            </Text>
          </View>
        </View>

        {/* 5. 平台特定样式 | 5. Platform-Specific Styles */}
        <View style={styles.section}>
          <Text style={styles.title}>
            平台特定样式 | Platform-Specific Styles
          </Text>
          <View style={styles.platformBox}>
            <Text style={styles.platformText}>
              {Platform.OS === 'ios'
                ? 'iOS 样式 (圆角、阴影) | iOS Style (Rounded, Shadow)'
                : 'Android 样式 (Elevation) | Android Style (Elevation)'}
            </Text>
          </View>
        </View>
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
      padding: 15,
    },
    section: {
      marginBottom: 25,
    },
    title: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#2c3e50',
    },
    // 百分比布局 | Percentage Layout
    percentageContainer: {
      flexDirection: 'row',
      height: 80,
    },
    percentBox: {
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#3498db',
      marginHorizontal: 2,
      borderRadius: 8,
    },
    boxText: {
      color: '#fff',
      fontWeight: 'bold',
      textAlign: 'center',
    },
    // AspectRatio 布局 | AspectRatio Layout
    aspectContainer: {
      width: '100%',
    },
    aspectBox: {
      width: '100%',
      aspectRatio: 16 / 9, // 16:9 宽高比 | 16:9 aspect ratio
      backgroundColor: '#e74c3c',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
    },
    // FlexWrap 网格 | FlexWrap Grid
    gridContainer: {
      flexDirection: 'row',
      flexWrap: 'wrap', // 关键：允许换行 | Key: Allow wrapping
      justifyContent: 'space-between',
    },
    gridItem: {
      width: (SCREEN_WIDTH - 50) / 4, // 动态计算宽度 | Dynamically calculate width
      aspectRatio: 1,
      backgroundColor: '#2ecc71',
      justifyContent: 'center',
      alignItems: 'center',
      marginBottom: 10,
      borderRadius: 8,
    },
    gridText: {
      color: '#fff',
      fontSize: 20,
      fontWeight: 'bold',
    },
    // 信息容器 | Info Container
    infoContainer: {
      backgroundColor: '#f8f9fa',
      padding: 15,
      borderRadius: 8,
      borderLeftWidth: 4,
      borderLeftColor: '#6c5ce7',
    },
    infoText: {
      fontSize: 14,
      color: '#2d3436',
      marginBottom: 5,
    },
    // 平台特定样式 | Platform-Specific Styles
    platformBox: {
      padding: 20,
      backgroundColor: '#fff',
      borderRadius: Platform.OS === 'ios' ? 12 : 4,
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.25,
          shadowRadius: 3.84,
        },
        android: {
          elevation: 5,
        },
      }),
    },
    platformText: {
      fontSize: 14,
      color: '#2c3e50',
      textAlign: 'center',
    },
  });

  export default ResponsiveBestPractices;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么gridItem的宽度使用(SCREEN_WIDTH - 50) / 4而不是固定值？| Why does gridItem width use (SCREEN_WIDTH - 50) / 4 instead of a fixed value?
    **答案 | Answer:** 确保在不同屏幕宽度下每行显示4个网格，减50是为了扣除padding和间距 | Ensures 4 grid items per row on different screen widths, minus 50 accounts for padding and spacing
  - Platform.select()的作用是什么？| What does Platform.select() do?
    **答案 | Answer:** 根据平台(iOS/Android)返回不同的样式对象，实现平台特定样式 | Returns different style objects based on platform (iOS/Android), achieving platform-specific styles

  **常见误区检查 | Common Misconception Checks:**
  - 使用Dimensions.get()后，旋转设备会自动更新尺寸吗？| After using Dimensions.get(), will rotating the device automatically update dimensions?
    **答案 | Answer:** 否 | No - 需要监听Dimensions的change事件或使用useWindowDimensions hook来响应变化 | Need to listen to Dimensions change event or use useWindowDimensions hook to respond to changes
  - aspectRatio和固定width/height可以一起用吗？| Can aspectRatio be used together with fixed width/height?
    **答案 | Answer:** 只需要设置一个维度和aspectRatio | Only need to set one dimension and aspectRatio - 设置width+aspectRatio会自动计算height，反之亦然 | Setting width+aspectRatio auto-calculates height, and vice versa

## 实践项目：移动应用Dashboard布局 | Practical Project: Mobile App Dashboard Layout

### 目标 | Objective
构建一个完整的移动应用Dashboard，综合应用今天学习的所有Flexbox概念：固定Header、可滚动内容区(包含统计卡片、图表占位符、活动列表)、固定底部标签栏，以及响应式设计技巧，使布局在不同设备尺寸上都能良好展示。| Build a complete mobile app Dashboard that comprehensively applies all Flexbox concepts learned today: fixed Header, scrollable content area (including stat cards, chart placeholder, activity list), fixed bottom tab bar, and responsive design techniques to ensure the layout displays well on different device sizes.

### 概念应用检查 | Concept Application Check
在开始项目前,请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 为什么Dashboard的内容区使用flex: 1？| Why does the Dashboard content area use flex: 1?
   **答案 | Answer:** 使内容区自动填充Header和Footer之间的所有可用空间，适应不同屏幕高度 | Makes content area automatically fill all available space between Header and Footer, adapting to different screen heights
2. 统计卡片如何实现水平均分三列？| How to achieve three evenly distributed stat cards horizontally?
   **答案 | Answer:** 使用flexDirection:'row'的容器，每个卡片设置flex:1 | Use container with flexDirection:'row', set flex:1 on each card
3. 如何确保Dashboard在横屏时也能正常显示？| How to ensure Dashboard displays correctly in landscape orientation?
   **答案 | Answer:** 使用相对单位(flex、百分比)而非固定像素，使用ScrollView处理内容溢出 | Use relative units (flex, percentages) instead of fixed pixels, use ScrollView to handle content overflow

### 步骤 | Steps
1. **创建基础结构**：使用SafeAreaView包裹整体布局，分别创建Header、Content(ScrollView)、Footer三个主要区域 | Create basic structure: wrap overall layout with SafeAreaView, create three main areas - Header, Content (ScrollView), Footer
2. **实现Header布局**：左侧菜单图标、中间Dashboard标题(flex:1居中)、右侧通知图标，使用flexDirection:'row' | Implement Header layout: left menu icon, center Dashboard title (flex:1 centered), right notification icon, use flexDirection:'row'
3. **构建统计卡片区**：创建3个统计卡片并排显示(flex:1)，每个卡片显示数字和标签，使用阴影增强视觉效果 | Build stat cards section: create 3 stat cards displayed side by side (flex:1), each card shows number and label, use shadows for enhanced visual effect
4. **添加图表占位符**：全宽容器，使用aspectRatio保持16:9比例，显示"图表区域"文本 | Add chart placeholder: full-width container, use aspectRatio to maintain 16:9 ratio, display "Chart Area" text
5. **创建活动列表**：垂直排列的卡片列表，每个卡片包含图标、标题、描述和时间，使用flexDirection:'row'排列卡片内元素 | Create activity list: vertically arranged card list, each card contains icon, title, description and time, use flexDirection:'row' to arrange elements within cards
6. **实现Footer标签栏**：5个图标标签均匀分布(flex:1)，包含图标和文字，响应点击高亮 | Implement Footer tab bar: 5 icon tabs evenly distributed (flex:1), containing icons and text, responsive to click highlighting
7. **应用响应式技巧**：使用Dimensions API调整布局，添加平台特定样式(iOS阴影/Android elevation) | Apply responsive techniques: use Dimensions API to adjust layout, add platform-specific styles (iOS shadow/Android elevation)

### 示例代码 | Example Code
```javascript
import React, { useState } from 'react';
import {
  View,
  Text,
  StyleSheet,
  SafeAreaView,
  ScrollView,
  TouchableOpacity,
  Dimensions,
  Platform,
} from 'react-native';

/**
 * 移动应用Dashboard布局完整示例 | Complete Mobile App Dashboard Layout Example
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Flexbox默认布局系统(所有View都是flex容器) | Flexbox default layout system (all Views are flex containers)
 * - flexDirection控制主轴方向 | flexDirection controls main axis direction
 * - justifyContent和alignItems实现对齐 | justifyContent and alignItems achieve alignment
 * - flex属性创建比例布局 | flex property creates proportional layouts
 * - Header/Content/Footer经典移动布局模式 | Header/Content/Footer classic mobile layout pattern
 * - 响应式设计技巧(百分比、aspectRatio、平台特定样式) | Responsive design techniques (percentages, aspectRatio, platform-specific styles)
 */

const { width: SCREEN_WIDTH } = Dimensions.get('window');

const DashboardApp = () => {
  const [activeTab, setActiveTab] = useState(0);

  return (
    <SafeAreaView style={styles.container}>
      {/* ========== Header 区域 ========== */}
      {/* 概念验证：flexDirection:'row'实现水平布局，中间标题flex:1填充剩余空间 */}
      <View style={styles.header}>
        <TouchableOpacity style={styles.headerIcon}>
          <Text style={styles.headerIconText}>☰</Text>
        </TouchableOpacity>

        <View style={styles.headerTitleContainer}>
          <Text style={styles.headerTitle}>Dashboard</Text>
        </View>

        <TouchableOpacity style={styles.headerIcon}>
          <Text style={styles.headerIconText}>🔔</Text>
        </TouchableOpacity>
      </View>

      {/* ========== Content 区域 ========== */}
      {/* 概念验证：flex:1使内容区填充Header和Footer之间的所有空间 */}
      <ScrollView style={styles.content} showsVerticalScrollIndicator={false}>
        {/* 统计卡片区 - 3列均分 */}
        {/* 概念验证：flexDirection:'row' + 每个卡片flex:1实现均分 */}
        <View style={styles.statsContainer}>
          <View style={styles.statCard}>
            <Text style={styles.statNumber}>2,543</Text>
            <Text style={styles.statLabel}>访问量 | Visits</Text>
          </View>
          <View style={styles.statCard}>
            <Text style={styles.statNumber}>1,287</Text>
            <Text style={styles.statLabel}>用户 | Users</Text>
          </View>
          <View style={styles.statCard}>
            <Text style={styles.statNumber}>98.5%</Text>
            <Text style={styles.statLabel}>满意度 | Rating</Text>
          </View>
        </View>

        {/* 图表占位符 */}
        {/* 概念验证：aspectRatio保持16:9宽高比，width:'100%'填充容器宽度 */}
        <View style={styles.chartSection}>
          <Text style={styles.sectionTitle}>
            本周数据分析 | Weekly Analytics
          </Text>
          <View style={styles.chartPlaceholder}>
            <Text style={styles.chartText}>📊</Text>
            <Text style={styles.chartSubtext}>
              图表区域 | Chart Area{'\n'}(16:9 比例)
            </Text>
          </View>
        </View>

        {/* 最近活动列表 */}
        {/* 概念验证：垂直布局(默认column)，每个活动卡片内部使用row布局 */}
        <View style={styles.activitySection}>
          <Text style={styles.sectionTitle}>
            最近活动 | Recent Activities
          </Text>

          {activities.map((activity, index) => (
            <View key={index} style={styles.activityCard}>
              <View style={styles.activityIcon}>
                <Text style={styles.activityIconText}>
                  {activity.icon}
                </Text>
              </View>

              <View style={styles.activityContent}>
                <Text style={styles.activityTitle}>
                  {activity.title}
                </Text>
                <Text style={styles.activityDesc}>
                  {activity.description}
                </Text>
                <Text style={styles.activityTime}>
                  {activity.time}
                </Text>
              </View>
            </View>
          ))}
        </View>

        {/* 底部安全间距 */}
        <View style={{ height: 20 }} />
      </ScrollView>

      {/* ========== Footer 标签栏 ========== */}
      {/* 概念验证：flexDirection:'row' + 每个tab flex:1实现均分，justifyContent:'space-around'也可达到类似效果 */}
      <View style={styles.footer}>
        {tabs.map((tab, index) => (
          <TouchableOpacity
            key={index}
            style={styles.tab}
            onPress={() => setActiveTab(index)}
          >
            <Text style={[
              styles.tabIcon,
              activeTab === index && styles.tabIconActive
            ]}>
              {tab.icon}
            </Text>
            <Text style={[
              styles.tabLabel,
              activeTab === index && styles.tabLabelActive
            ]}>
              {tab.label}
            </Text>
          </TouchableOpacity>
        ))}
      </View>
    </SafeAreaView>
  );
};

// 模拟数据 | Mock Data
const activities = [
  {
    icon: '👤',
    title: '新用户注册 | New User Registered',
    description: 'John Doe 加入了平台 | joined the platform',
    time: '5分钟前 | 5 min ago',
  },
  {
    icon: '📦',
    title: '订单完成 | Order Completed',
    description: '订单 #12345 已发货 | Order shipped',
    time: '1小时前 | 1 hour ago',
  },
  {
    icon: '⭐',
    title: '收到评价 | Review Received',
    description: '产品获得5星好评 | Product got 5-star review',
    time: '3小时前 | 3 hours ago',
  },
  {
    icon: '💬',
    title: '新消息 | New Message',
    description: '客服收到3条新消息 | Support received 3 messages',
    time: '昨天 | Yesterday',
  },
];

const tabs = [
  { icon: '🏠', label: '首页\nHome' },
  { icon: '📊', label: '统计\nStats' },
  { icon: '➕', label: '添加\nAdd' },
  { icon: '💬', label: '消息\nChat' },
  { icon: '👤', label: '我的\nMe' },
];

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },

  // ========== Header 样式 ==========
  header: {
    height: 60,
    flexDirection: 'row', // 水平布局 | Horizontal layout
    alignItems: 'center', // 垂直居中对齐 | Vertically center aligned
    backgroundColor: '#6c5ce7',
    paddingHorizontal: 15,
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.1,
        shadowRadius: 3,
      },
      android: {
        elevation: 4,
      },
    }),
  },
  headerIcon: {
    width: 40,
    height: 40,
    justifyContent: 'center',
    alignItems: 'center',
  },
  headerIconText: {
    fontSize: 24,
    color: '#fff',
  },
  headerTitleContainer: {
    flex: 1, // 填充剩余空间 | Fill remaining space
    justifyContent: 'center',
    alignItems: 'center',
  },
  headerTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#fff',
  },

  // ========== Content 样式 ==========
  content: {
    flex: 1, // 关键：填充Header和Footer之间的空间 | Key: Fill space between Header and Footer
    backgroundColor: '#f8f9fa',
  },

  // 统计卡片区
  statsContainer: {
    flexDirection: 'row', // 水平排列卡片 | Arrange cards horizontally
    paddingHorizontal: 15,
    paddingTop: 20,
    paddingBottom: 10,
  },
  statCard: {
    flex: 1, // 三个卡片均分空间 | Three cards evenly distribute space
    backgroundColor: '#fff',
    marginHorizontal: 5,
    padding: 15,
    borderRadius: 12,
    alignItems: 'center',
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.08,
        shadowRadius: 4,
      },
      android: {
        elevation: 3,
      },
    }),
  },
  statNumber: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2d3436',
    marginBottom: 5,
  },
  statLabel: {
    fontSize: 12,
    color: '#636e72',
    textAlign: 'center',
  },

  // 图表区
  chartSection: {
    paddingHorizontal: 15,
    paddingTop: 20,
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#2d3436',
    marginBottom: 12,
  },
  chartPlaceholder: {
    width: '100%',
    aspectRatio: 16 / 9, // 保持16:9宽高比 | Maintain 16:9 aspect ratio
    backgroundColor: '#fff',
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.08,
        shadowRadius: 4,
      },
      android: {
        elevation: 3,
      },
    }),
  },
  chartText: {
    fontSize: 48,
    marginBottom: 10,
  },
  chartSubtext: {
    fontSize: 14,
    color: '#636e72',
    textAlign: 'center',
  },

  // 活动列表区
  activitySection: {
    paddingHorizontal: 15,
    paddingTop: 20,
  },
  activityCard: {
    flexDirection: 'row', // 图标和内容水平排列 | Icon and content arranged horizontally
    backgroundColor: '#fff',
    padding: 15,
    borderRadius: 12,
    marginBottom: 12,
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 1 },
        shadowOpacity: 0.06,
        shadowRadius: 3,
      },
      android: {
        elevation: 2,
      },
    }),
  },
  activityIcon: {
    width: 50,
    height: 50,
    borderRadius: 25,
    backgroundColor: '#f1f3f5',
    justifyContent: 'center',
    alignItems: 'center',
    marginRight: 15,
  },
  activityIconText: {
    fontSize: 24,
  },
  activityContent: {
    flex: 1, // 内容区填充剩余空间 | Content area fills remaining space
    justifyContent: 'center',
  },
  activityTitle: {
    fontSize: 15,
    fontWeight: '600',
    color: '#2d3436',
    marginBottom: 4,
  },
  activityDesc: {
    fontSize: 13,
    color: '#636e72',
    marginBottom: 4,
  },
  activityTime: {
    fontSize: 12,
    color: '#b2bec3',
  },

  // ========== Footer 标签栏样式 ==========
  footer: {
    height: 70,
    flexDirection: 'row', // 水平排列标签 | Arrange tabs horizontally
    backgroundColor: '#fff',
    borderTopWidth: 1,
    borderTopColor: '#e9ecef',
    paddingBottom: Platform.OS === 'ios' ? 5 : 0, // iOS底部安全区域 | iOS bottom safe area
  },
  tab: {
    flex: 1, // 5个标签均分空间 | 5 tabs evenly distribute space
    justifyContent: 'center',
    alignItems: 'center',
  },
  tabIcon: {
    fontSize: 24,
    marginBottom: 4,
    opacity: 0.5,
  },
  tabIconActive: {
    opacity: 1,
  },
  tabLabel: {
    fontSize: 10,
    color: '#636e72',
    textAlign: 'center',
    lineHeight: 12,
  },
  tabLabelActive: {
    color: '#6c5ce7',
    fontWeight: '600',
  },
});

export default DashboardApp;
```

### 项目完成检查 | Project Completion Check
1. Dashboard是否使用了正确的Header/Content/Footer结构？| Does the Dashboard use correct Header/Content/Footer structure?
   - ✅ Header固定高度60，使用flexDirection:'row'水平布局 | Header fixed height 60, uses flexDirection:'row' for horizontal layout
   - ✅ Content使用flex:1填充剩余空间，ScrollView处理溢出 | Content uses flex:1 to fill remaining space, ScrollView handles overflow
   - ✅ Footer固定高度70，标签均匀分布 | Footer fixed height 70, tabs evenly distributed
2. 统计卡片区是否正确应用了Flexbox比例布局？| Does the stat cards section correctly apply Flexbox proportional layout?
   - ✅ 使用flexDirection:'row'容器，3个卡片各设置flex:1实现均分 | Uses flexDirection:'row' container, 3 cards each set flex:1 for even distribution
3. 图表区域是否使用aspectRatio保持比例？| Does the chart area use aspectRatio to maintain proportion?
   - ✅ 设置aspectRatio: 16/9，width:'100%'，在不同屏幕宽度下保持16:9比例 | Set aspectRatio: 16/9, width:'100%', maintains 16:9 ratio on different screen widths
4. 活动卡片是否正确使用了嵌套Flexbox？| Do activity cards correctly use nested Flexbox?
   - ✅ 卡片使用flexDirection:'row'，图标固定宽度，内容区flex:1填充 | Cards use flexDirection:'row', icon fixed width, content area flex:1 fills
5. Footer标签栏是否实现了均匀分布和高亮效果？| Does the Footer tab bar achieve even distribution and highlight effect?
   - ✅ 5个tab各设置flex:1均分，activeTab状态控制高亮样式 | 5 tabs each set flex:1 for even distribution, activeTab state controls highlight style
6. 是否应用了平台特定样式？| Are platform-specific styles applied?
   - ✅ 使用Platform.select()为iOS添加shadow，为Android添加elevation | Uses Platform.select() to add shadow for iOS, elevation for Android

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Flexbox方向探索练习 | Flexbox Direction Exploration Exercise**
   - **练习描述 | Exercise Description:** 创建一个交互式组件，允许用户动态切换flexDirection的4个值('column', 'column-reverse', 'row', 'row-reverse')，并显示5个编号盒子的排列变化。观察主轴和交叉轴的方向改变。| Create an interactive component that allows users to dynamically switch between 4 flexDirection values ('column', 'column-reverse', 'row', 'row-reverse') and display the arrangement changes of 5 numbered boxes. Observe the direction changes of main and cross axes.
   - **概念检查 | Concept Check:**
     - flexDirection改变时，主轴方向如何变化？| How does the main axis direction change when flexDirection changes?
     - reverse变体如何影响子元素顺序？| How do reverse variants affect child element order?
   - **学习目标 | Learning Objective:** 深入理解主轴、交叉轴概念以及flexDirection对布局的全面影响 | Deeply understand main axis, cross axis concepts and flexDirection's comprehensive impact on layout

2. **对齐属性组合练习 | Alignment Property Combination Exercise**
   - **练习描述 | Exercise Description:** 创建一个9宫格展示器，展示justifyContent(flex-start/center/flex-end)和alignItems(flex-start/center/flex-end)的9种组合效果，每个网格显示一个小圆点的位置。| Create a 9-grid display showing 9 combination effects of justifyContent (flex-start/center/flex-end) and alignItems (flex-start/center/flex-end), each grid showing the position of a small dot.
   - **概念检查 | Concept Check:**
     - justifyContent和alignItems分别控制哪个轴的对齐？| Which axis does justifyContent and alignItems respectively control alignment for?
     - 为什么需要同时设置两个属性才能实现完全居中？| Why do you need to set both properties to achieve complete centering?
   - **学习目标 | Learning Objective:** 掌握双轴对齐控制，理解主轴和交叉轴的独立性 | Master dual-axis alignment control, understand the independence of main and cross axes

3. **Flex比例计算练习 | Flex Ratio Calculation Exercise**
   - **练习描述 | Exercise Description:** 创建一个可视化工具，用户可以为3个盒子分别设置flex值(0-5)，实时显示每个盒子分配到的空间百分比和像素值。加入一个固定宽度的盒子，观察flex计算如何扣除固定空间。| Create a visualization tool where users can set flex values (0-5) for 3 boxes respectively, displaying in real-time the space percentage and pixel value allocated to each box. Add a fixed-width box to observe how flex calculation deducts fixed space.
   - **概念检查 | Concept Check:**
     - 当flex值为1、2、3时，空间比例如何计算？| When flex values are 1, 2, 3, how is the space ratio calculated?
     - 固定宽度元素如何影响flex元素的可用空间？| How do fixed-width elements affect the available space for flex elements?
   - **学习目标 | Learning Objective:** 精确理解flex比例分配机制，掌握固定与弹性布局混合使用 | Precisely understand flex ratio distribution mechanism, master mixing fixed and flexible layouts

4. **嵌套Flexbox布局练习 | Nested Flexbox Layout Exercise**
   - **练习描述 | Exercise Description:** 实现一个复杂的卡片组件：最外层垂直布局(图片、内容、操作栏)，内容区水平布局(图标、文字)，操作栏水平布局(多个按钮均分)。要求使用嵌套的Flexbox实现。| Implement a complex card component: outermost vertical layout (image, content, action bar), content area horizontal layout (icon, text), action bar horizontal layout (multiple evenly distributed buttons). Required to implement using nested Flexbox.
   - **概念检查 | Concept Check:**
     - 嵌套Flexbox中，子容器的flexDirection独立于父容器吗？| In nested Flexbox, is the child container's flexDirection independent of the parent container?
     - 如何确保内层flex元素正确响应外层容器的空间分配？| How to ensure inner flex elements correctly respond to outer container's space distribution?
   - **学习目标 | Learning Objective:** 掌握多层嵌套Flexbox的设计模式，理解独立布局上下文 | Master multi-level nested Flexbox design patterns, understand independent layout contexts

5. **响应式FlexWrap网格练习 | Responsive FlexWrap Grid Exercise**
   - **练习描述 | Exercise Description:** 创建一个商品网格，使用flexWrap:'wrap'实现自动换行。每个商品卡片宽度为(屏幕宽度-40)/3，确保每行显示3个商品。添加功能：切换显示2列/3列/4列。| Create a product grid using flexWrap:'wrap' to implement automatic wrapping. Each product card width is (screen width-40)/3, ensuring 3 products per row. Add feature: switch display between 2/3/4 columns.
   - **概念检查 | Concept Check:**
     - flexWrap如何决定元素何时换行？| How does flexWrap decide when elements should wrap?
     - 如何动态计算卡片宽度以适应不同列数？| How to dynamically calculate card width to accommodate different column numbers?
   - **学习目标 | Learning Objective:** 理解flexWrap的换行机制，掌握动态网格布局实现 | Understand flexWrap's wrapping mechanism, master dynamic grid layout implementation

6. **AspectRatio响应式图片练习 | AspectRatio Responsive Image Exercise**
   - **练习描述 | Exercise Description:** 创建一个图片画廊组件，包含多种宽高比的图片(1:1正方形、16:9横图、9:16竖图、4:3经典)。使用aspectRatio属性确保所有图片在不同屏幕宽度下保持正确比例，不变形。| Create an image gallery component containing images with various aspect ratios (1:1 square, 16:9 landscape, 9:16 portrait, 4:3 classic). Use aspectRatio property to ensure all images maintain correct proportions on different screen widths without distortion.
   - **概念检查 | Concept Check:**
     - aspectRatio与width/height固定值的区别是什么？| What's the difference between aspectRatio and fixed width/height values?
     - 如何使用aspectRatio实现响应式正方形？| How to use aspectRatio to implement responsive square?
   - **学习目标 | Learning Objective:** 掌握aspectRatio在响应式图片布局中的应用，理解比例保持原理 | Master aspectRatio application in responsive image layout, understand proportion maintenance principle

7. **综合布局重构练习 | Comprehensive Layout Refactoring Exercise**
   - **练习描述 | Exercise Description:** 将Dashboard项目扩展为平板和手机两种布局：手机上统计卡片垂直堆叠，平板上水平排列；平板上活动列表显示2列，手机显示1列。使用Dimensions API或useWindowDimensions判断设备类型并应用相应布局。| Extend the Dashboard project to two layouts for tablet and phone: stat cards stack vertically on phone, arrange horizontally on tablet; activity list shows 2 columns on tablet, 1 column on phone. Use Dimensions API or useWindowDimensions to determine device type and apply corresponding layout.
   - **概念检查 | Concept Check:**
     - 如何基于屏幕宽度切换flexDirection？| How to switch flexDirection based on screen width?
     - 响应式布局中，何时使用条件渲染，何时使用动态样式？| In responsive layout, when to use conditional rendering vs dynamic styles?
   - **学习目标 | Learning Objective:** 综合运用Flexbox、响应式技巧、条件逻辑实现跨设备布局 | Comprehensively apply Flexbox, responsive techniques, and conditional logic to implement cross-device layouts

## 学习资源 | Learning Resources
- [React Native Flexbox 官方文档 | React Native Flexbox Official Docs](https://reactnative.dev/docs/flexbox)
- [Yoga布局引擎文档 - RN底层布局系统 | Yoga Layout Engine Docs - RN's Underlying Layout System](https://yogalayout.com/docs)
- [Flexbox Froggy游戏 - 交互式学习Flexbox | Flexbox Froggy Game - Interactive Flexbox Learning](https://flexboxfroggy.com/)
- [React Native Layout实战指南 | React Native Layout Practical Guide](https://blog.reactnativecoach.com/the-full-react-native-layout-cheat-sheet-a4147802405c)
- [StyleSheet API参考 | StyleSheet API Reference](https://reactnative.dev/docs/stylesheet)
- [Dimensions API文档 | Dimensions API Documentation](https://reactnative.dev/docs/dimensions)
- [Platform特定代码指南 | Platform-Specific Code Guide](https://reactnative.dev/docs/platform-specific-code)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解React Native中所有View默认是flex容器，无需display:flex | Understand all Views in React Native are flex containers by default, no need for display:flex
- [ ] 掌握flexDirection的4个值及其对主轴/交叉轴的影响 | Master 4 flexDirection values and their impact on main/cross axes
- [ ] 能够熟练使用justifyContent和alignItems实现各种对齐效果 | Proficiently use justifyContent and alignItems to achieve various alignment effects
- [ ] 理解flex属性的比例分配机制，能创建比例布局 | Understand flex property's ratio distribution mechanism, can create proportional layouts
- [ ] 能够实现Header/Content/Footer经典移动布局模式 | Can implement Header/Content/Footer classic mobile layout pattern
- [ ] 掌握使用aspectRatio保持元素宽高比 | Master using aspectRatio to maintain element aspect ratio
- [ ] 理解flexWrap在响应式网格中的应用 | Understand flexWrap application in responsive grids
- [ ] 能够使用Dimensions API获取屏幕尺寸并动态调整布局 | Can use Dimensions API to get screen dimensions and dynamically adjust layout
- [ ] 掌握Platform API实现iOS和Android平台特定样式 | Master Platform API to implement iOS and Android platform-specific styles
- [ ] 完成Dashboard实践项目，正确应用所有Flexbox概念 | Complete Dashboard practical project, correctly apply all Flexbox concepts

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs(概念检查问题)，并能够向他人清晰解释以下核心概念：
1. React Native Flexbox与Web Flexbox的3个主要区别
2. 主轴和交叉轴的定义及其与flexDirection的关系
3. flex:1的具体含义和使用场景
4. 如何使用嵌套Flexbox实现复杂布局
5. 移动端响应式设计的核心原则

Before marking as complete, ensure you can correctly answer all CCQs (Concept Checking Questions) from today and clearly explain the following core concepts to others:
1. Three main differences between React Native Flexbox and Web Flexbox
2. Definition of main axis and cross axis and their relationship with flexDirection
3. Specific meaning and usage scenarios of flex:1
4. How to use nested Flexbox to implement complex layouts
5. Core principles of mobile responsive design
