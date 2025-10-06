# React Native入门 - 第15天：StyleSheet API与样式系统 | React Native Introduction - Day 15: StyleSheet API and Styling System

## 学习目标 | Learning Objectives
- 理解React Native中StyleSheet API的作用和性能优势 | Understand the role and performance benefits of StyleSheet API in React Native
- 掌握StyleSheet.create()与内联样式对象的区别 | Master the differences between StyleSheet.create() and inline style objects
- 了解React Native样式属性与CSS的命名差异 | Learn the naming differences between React Native style properties and CSS
- 识别哪些CSS属性在React Native中不可用 | Identify which CSS properties are NOT available in React Native
- 学会使用数组语法进行样式组合 | Learn to use array syntax for style composition
- 掌握从Web开发到移动端样式开发的思维转换 | Master the mental shift from web to mobile styling development

## 详细内容 | Detailed Content

### 1. StyleSheet API基础 | StyleSheet API Fundamentals (1小时 | 1 hour)

- **StyleSheet.create() 方法 | StyleSheet.create() Method**

  **概念定义 | Concept Definition:**
  StyleSheet.create() 是React Native提供的一个API方法，用于创建优化的样式表对象。它接受一个样式定义对象并返回一个包含样式引用的对象，这些引用在运行时会被转换为优化的原生样式。| StyleSheet.create() is an API method provided by React Native for creating optimized stylesheet objects. It accepts a style definition object and returns an object containing style references that are converted to optimized native styles at runtime.

  **核心特征 | Key Characteristics:**
  - **性能优化**：将样式对象转换为不可变的引用ID，避免重复创建 | **Performance Optimization**: Converts style objects to immutable reference IDs, avoiding repeated creation
  - **验证机制**：在开发模式下验证样式属性的有效性 | **Validation Mechanism**: Validates style property validity in development mode
  - **原生桥接**：样式通过桥接层传递给原生UI组件，使用ID比传递整个对象更高效 | **Native Bridge**: Styles pass through the bridge to native UI components; using IDs is more efficient than passing entire objects
  - **不可变性**：创建后的样式对象不能被修改，确保UI更新的可预测性 | **Immutability**: Created style objects cannot be modified, ensuring predictable UI updates

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. StyleSheet.create() 创建的样式对象可以在运行时被修改吗？| Can style objects created by StyleSheet.create() be modified at runtime?
     **答案 | Answer:** 否 | No - StyleSheet.create()创建的是不可变引用，这确保了性能和可预测性 | StyleSheet.create() creates immutable references, ensuring performance and predictability

  2. 在React Native中，所有的样式都必须使用StyleSheet.create()吗？| In React Native, must all styles use StyleSheet.create()?
     **答案 | Answer:** 否 | No - 可以使用普通JavaScript对象作为内联样式，但StyleSheet.create()提供了性能优势 | Plain JavaScript objects can be used as inline styles, but StyleSheet.create() provides performance benefits

  3. StyleSheet.create()的主要性能优势是什么？| What is the main performance advantage of StyleSheet.create()?
     **答案 | Answer:** 将样式对象转换为ID引用，通过桥接层传递时更高效，避免重复序列化 | Converts style objects to ID references, more efficient when passing through the bridge, avoids repeated serialization

  4. StyleSheet.create()在开发模式下会做什么额外的工作？| What extra work does StyleSheet.create() do in development mode?
     **答案 | Answer:** 验证样式属性的有效性，如果使用了不支持的属性会发出警告 | Validates style property validity and warns if unsupported properties are used

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { StyleSheet, View, Text } from 'react-native';

  // 使用 StyleSheet.create() 创建样式 | Creating styles with StyleSheet.create()
  // 这会在模块加载时创建优化的样式引用 | This creates optimized style references when the module loads
  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      color: '#333333',
    },
  });

  // 使用创建的样式 | Using the created styles
  function MyComponent() {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>Hello React Native</Text>
      </View>
    );
  }

  // 对比：普通对象作为内联样式 | Comparison: Plain object as inline style
  // 每次渲染都会创建新对象（性能较差）| Creates a new object on each render (worse performance)
  function MyComponentInline() {
    return (
      <View style={{
        flex: 1,
        backgroundColor: '#f5f5f5',
        padding: 20,
      }}>
        <Text style={{
          fontSize: 24,
          fontWeight: 'bold',
          color: '#333333',
        }}>Hello React Native</Text>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这两种方式在小型应用中的性能差异明显吗？| Is the performance difference noticeable in small apps between these two approaches?
    **答案 | Answer:** 不明显，但在大型应用或频繁重渲染的组件中会产生累积影响 | Not noticeable, but accumulates in large apps or frequently re-rendered components

  - styles.container 的值是什么类型？| What type is the value of styles.container?
    **答案 | Answer:** 一个数字ID（在开发模式下可能是对象，但在生产环境中被优化为ID）| A numeric ID (may be an object in dev mode, but optimized to ID in production)

  **常见误区检查 | Common Misconception Checks:**
  - StyleSheet.create()会自动合并样式吗？| Does StyleSheet.create() automatically merge styles?
    **答案 | Answer:** 不会，它只是创建样式引用。样式合并需要使用数组语法或展开运算符 | No, it only creates style references. Style merging requires array syntax or spread operator

  - 使用内联样式就一定性能差吗？| Do inline styles always perform poorly?
    **答案 | Answer:** 在小型应用中差异可忽略，但最佳实践是使用StyleSheet.create()以获得验证和优化 | Difference is negligible in small apps, but best practice is using StyleSheet.create() for validation and optimization

### 2. React Native样式属性命名规则 | React Native Style Property Naming Conventions (1小时 | 1 hour)

- **驼峰命名法 vs 短横线命名法 | camelCase vs kebab-case**

  **概念定义 | Concept Definition:**
  React Native使用JavaScript对象来定义样式，因此所有样式属性名必须遵循JavaScript的命名规范，使用驼峰命名法（camelCase），而不是CSS中的短横线命名法（kebab-case）。| React Native uses JavaScript objects to define styles, so all style property names must follow JavaScript naming conventions using camelCase, not the kebab-case used in CSS.

  **核心特征 | Key Characteristics:**
  - **JavaScript对象属性**：样式是JavaScript对象的属性，不能包含连字符 | **JavaScript Object Properties**: Styles are JavaScript object properties and cannot contain hyphens
  - **一致的转换规则**：CSS属性名的连字符后的字母变为大写 | **Consistent Conversion Rule**: Letters after hyphens in CSS property names become uppercase
  - **值的类型差异**：某些属性的值在React Native中是数字而不是字符串 | **Value Type Differences**: Some property values are numbers instead of strings in React Native
  - **无单位数字**：大多数尺寸值是无单位的数字，代表密度无关像素（DP）| **Unitless Numbers**: Most dimension values are unitless numbers representing density-independent pixels (DP)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. CSS的 "background-color" 在React Native中应该写成什么？| What should CSS "background-color" be written as in React Native?
     **答案 | Answer:** backgroundColor - 移除连字符，后续单词首字母大写 | backgroundColor - remove hyphen, capitalize first letter of subsequent words

  2. 在React Native中，padding: 10 的值需要加引号吗？| In React Native, does the value in padding: 10 need quotes?
     **答案 | Answer:** 不需要 | No - 数字值直接写，不需要引号或单位（如px）| Numeric values are written directly without quotes or units (like px)

  3. 所有CSS属性都可以通过简单的驼峰转换在React Native中使用吗？| Can all CSS properties be used in React Native through simple camelCase conversion?
     **答案 | Answer:** 不可以 | No - 许多CSS属性在React Native中不支持（如float、position: fixed等）| Many CSS properties are not supported in React Native (like float, position: fixed, etc.)

  4. React Native中的颜色值可以使用哪些格式？| What color formats can be used in React Native?
     **答案 | Answer:** 十六进制（#RGB, #RGBA, #RRGGBB, #RRGGBBAA）、rgb()、rgba()、hsl()、hsla()、以及颜色名称 | Hexadecimal (#RGB, #RGBA, #RRGGBB, #RRGGBBAA), rgb(), rgba(), hsl(), hsla(), and color names

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { StyleSheet, View, Text } from 'react-native';

  // CSS到React Native样式属性的转换示例 | CSS to React Native style property conversion examples
  const styles = StyleSheet.create({
    // CSS: background-color → React Native: backgroundColor
    container: {
      backgroundColor: '#ffffff',  // 不是 'background-color'

      // CSS: padding-top → React Native: paddingTop
      paddingTop: 20,  // 数字值，不需要 'px' 单位
      paddingBottom: 20,
      paddingLeft: 15,
      paddingRight: 15,

      // CSS: margin-bottom → React Native: marginBottom
      marginBottom: 10,

      // CSS: border-radius → React Native: borderRadius
      borderRadius: 8,

      // CSS: box-shadow → React Native: shadow* 属性
      // 注意：阴影在iOS和Android上实现方式不同
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.25,
      shadowRadius: 3.84,
      elevation: 5,  // Android专用
    },

    text: {
      // CSS: font-size → React Native: fontSize
      fontSize: 16,  // 数字，不是 '16px'

      // CSS: font-weight → React Native: fontWeight
      fontWeight: 'bold',  // 字符串值：'normal', 'bold', '100'-'900'

      // CSS: text-align → React Native: textAlign
      textAlign: 'center',

      // CSS: line-height → React Native: lineHeight
      lineHeight: 24,

      // CSS: color → React Native: color
      color: '#333333',
    },
  });

  // 常见转换对照表 | Common conversion reference
  const conversionExamples = StyleSheet.create({
    // flex-direction → flexDirection
    row: { flexDirection: 'row' },

    // justify-content → justifyContent
    centered: { justifyContent: 'center' },

    // align-items → alignItems
    alignCenter: { alignItems: 'center' },

    // align-self → alignSelf
    selfEnd: { alignSelf: 'flex-end' },

    // border-width → borderWidth
    bordered: { borderWidth: 1, borderColor: '#ddd' },

    // max-width → maxWidth
    constrained: { maxWidth: 400 },
  });

  function StyleNamingExample() {
    return (
      <View style={styles.container}>
        <Text style={styles.text}>
          React Native使用驼峰命名法 | React Native uses camelCase
        </Text>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - fontSize: 16 中的16代表什么单位？| What unit does 16 represent in fontSize: 16?
    **答案 | Answer:** 密度无关像素（DP/DIP），会根据设备屏幕密度自动缩放 | Density-independent pixels (DP/DIP), automatically scaled based on device screen density

  - 为什么要使用 shadowColor 和 elevation 两个属性？| Why use both shadowColor and elevation properties?
    **答案 | Answer:** shadowColor等是iOS阴影属性，elevation是Android阴影属性，需要同时使用以支持两个平台 | shadowColor etc. are iOS shadow properties, elevation is Android shadow property; both needed to support both platforms

  **常见误区检查 | Common Misconception Checks:**
  - 可以在React Native中使用 'padding-top': 20 吗？| Can you use 'padding-top': 20 in React Native?
    **答案 | Answer:** 不可以 | No - 必须使用驼峰命名 paddingTop，JavaScript对象属性名不能包含连字符（除非用引号，但不推荐）| Must use camelCase paddingTop; JavaScript object property names cannot contain hyphens (unless quoted, not recommended)

  - 所有尺寸值都可以省略单位吗？| Can all dimension values omit units?
    **答案 | Answer:** 是的 | Yes - React Native中所有数字尺寸值都是无单位的，默认为DP | All numeric dimension values in React Native are unitless, defaulting to DP

### 3. React Native不支持的CSS属性 | CSS Properties NOT Available in React Native (1小时 | 1 hour)

- **移动端样式限制 | Mobile Styling Limitations**

  **概念定义 | Concept Definition:**
  React Native并不是CSS的完整实现，而是CSS的一个子集，专门为移动端UI优化。许多Web开发中常用的CSS属性在React Native中不可用，因为它们不适合移动端渲染引擎或没有对应的原生实现。| React Native is not a complete implementation of CSS, but a subset specifically optimized for mobile UI. Many commonly used CSS properties in web development are not available in React Native because they don't suit mobile rendering engines or lack native implementations.

  **核心特征 | Key Characteristics:**
  - **无Grid布局**：不支持 display: grid，只能使用Flexbox | **No Grid Layout**: display: grid is not supported; only Flexbox is available
  - **无Float浮动**：float 属性不存在，布局完全依赖Flexbox | **No Float**: float property doesn't exist; layout relies entirely on Flexbox
  - **有限的Position**：只支持 relative 和 absolute，不支持 fixed 和 sticky | **Limited Position**: Only supports relative and absolute, not fixed or sticky
  - **无伪类和伪元素**：不支持 :hover, :before, :after 等CSS选择器 | **No Pseudo-classes/elements**: :hover, :before, :after and other CSS selectors not supported
  - **无CSS动画语法**：不支持 @keyframes 和 transition 属性，需要使用Animated API | **No CSS Animation Syntax**: @keyframes and transition not supported; must use Animated API

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native支持 display: grid 吗？| Does React Native support display: grid?
     **答案 | Answer:** 否 | No - 只支持Flexbox布局，所有布局都需要用Flexbox实现 | Only Flexbox layout is supported; all layouts must be implemented with Flexbox

  2. 在React Native中可以使用 position: fixed 创建固定头部吗？| Can you use position: fixed to create a fixed header in React Native?
     **答案 | Answer:** 否 | No - 不支持fixed定位，需要使用absolute定位或特定的组件（如FlatList的ListHeaderComponent）| fixed positioning is not supported; use absolute positioning or specific components (like FlatList's ListHeaderComponent)

  3. float: left 在React Native中有替代方案吗？| Is there an alternative to float: left in React Native?
     **答案 | Answer:** 使用 flexDirection: 'row' 和相关Flexbox属性来实现类似效果 | Use flexDirection: 'row' and related Flexbox properties to achieve similar effects

  4. 如何在React Native中实现悬停效果？| How to implement hover effects in React Native?
     **答案 | Answer:** 使用Pressable或TouchableOpacity组件的状态回调，结合状态管理来改变样式 | Use Pressable or TouchableOpacity component's state callbacks combined with state management to change styles

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { StyleSheet, View, Text, Pressable } from 'react-native';
  import { useState } from 'react';

  // ❌ 以下CSS属性在React Native中不可用 | NOT available in React Native
  const unsupportedStyles = {
    // display: 'grid',              // ❌ 不支持Grid
    // float: 'left',                // ❌ 不支持Float
    // position: 'fixed',            // ❌ 不支持Fixed定位
    // position: 'sticky',           // ❌ 不支持Sticky定位
    // cursor: 'pointer',            // ❌ 移动端无鼠标指针
    // overflow: 'visible',          // ⚠️  在某些组件上有限制
  };

  // ✅ React Native替代方案 | React Native alternatives
  const styles = StyleSheet.create({
    // Grid布局替代：使用嵌套的Flexbox | Grid alternative: nested Flexbox
    gridAlternative: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      justifyContent: 'space-between',
    },
    gridItem: {
      width: '48%',  // 模拟2列grid | Simulate 2-column grid
      marginBottom: 10,
    },

    // Float替代：使用Flexbox的row方向 | Float alternative: Flexbox row direction
    floatAlternativeContainer: {
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    floatAlternativeLeft: {
      width: '30%',
    },
    floatAlternativeRight: {
      width: '65%',
    },

    // Fixed定位替代：使用absolute + top/bottom | Fixed alternative: absolute + top/bottom
    fixedAlternative: {
      position: 'absolute',
      top: 0,
      left: 0,
      right: 0,
      zIndex: 100,
      backgroundColor: 'white',
      height: 60,
    },

    // 悬停效果替代：使用状态控制样式 | Hover alternative: state-controlled styling
    pressableBase: {
      padding: 15,
      backgroundColor: '#007AFF',
      borderRadius: 8,
    },
    pressablePressed: {
      backgroundColor: '#0051D5',  // 按压时的深色 | Darker when pressed
      transform: [{ scale: 0.98 }],
    },
  });

  // 模拟悬停效果的实现 | Hover effect simulation implementation
  function HoverAlternativeExample() {
    const [isPressed, setIsPressed] = useState(false);

    return (
      <Pressable
        style={[
          styles.pressableBase,
          isPressed && styles.pressablePressed,  // 条件样式 | Conditional styling
        ]}
        onPressIn={() => setIsPressed(true)}
        onPressOut={() => setIsPressed(false)}
      >
        <Text style={{ color: 'white' }}>Press Me</Text>
      </Pressable>
    );
  }

  // Grid布局替代示例 | Grid layout alternative example
  function GridAlternativeExample() {
    return (
      <View style={styles.gridAlternative}>
        <View style={styles.gridItem}>
          <Text>Item 1</Text>
        </View>
        <View style={styles.gridItem}>
          <Text>Item 2</Text>
        </View>
        <View style={styles.gridItem}>
          <Text>Item 3</Text>
        </View>
        <View style={styles.gridItem}>
          <Text>Item 4</Text>
        </View>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何实现一个固定在屏幕底部的按钮？| How to implement a button fixed at the bottom of the screen?
    **答案 | Answer:** 使用 position: 'absolute', bottom: 0, left: 0, right: 0 | Use position: 'absolute', bottom: 0, left: 0, right: 0

  - 没有Grid布局，如何创建响应式的卡片网格？| Without Grid layout, how to create a responsive card grid?
    **答案 | Answer:** 使用 flexDirection: 'row', flexWrap: 'wrap' 并设置每个项目的百分比宽度 | Use flexDirection: 'row', flexWrap: 'wrap' and set percentage width for each item

  **常见误区检查 | Common Misconception Checks:**
  - 是否可以通过第三方库获得完整的CSS支持？| Can you get full CSS support through third-party libraries?
    **答案 | Answer:** 不能 | No - React Native的渲染引擎决定了这些限制，第三方库只能提供替代方案，不能直接支持原生不支持的属性 | React Native's rendering engine determines these limitations; third-party libraries can only provide alternatives, not directly support natively unsupported properties

  - 学习React Native样式时可以直接套用CSS知识吗？| Can you directly apply CSS knowledge when learning React Native styling?
    **答案 | Answer:** 部分可以 | Partially - Flexbox概念相似但有差异，需要重新学习React Native的特定实现和限制 | Flexbox concepts are similar but different; need to relearn React Native's specific implementations and limitations

### 4. 样式组合与数组语法 | Style Composition and Array Syntax (1小时 | 1 hour)

- **使用数组合并样式 | Merging Styles with Arrays**

  **概念定义 | Concept Definition:**
  React Native支持使用数组语法来组合多个样式对象，数组中后面的样式会覆盖前面的同名属性，类似于CSS的层叠规则。这是实现条件样式、主题切换和样式复用的核心机制。| React Native supports using array syntax to combine multiple style objects, where later styles in the array override earlier same-named properties, similar to CSS cascade rules. This is the core mechanism for implementing conditional styles, theme switching, and style reuse.

  **核心特征 | Key Characteristics:**
  - **数组顺序决定优先级**：后面的样式覆盖前面的 | **Array Order Determines Priority**: Later styles override earlier ones
  - **支持嵌套数组**：数组可以包含数组，会被扁平化处理 | **Supports Nested Arrays**: Arrays can contain arrays and will be flattened
  - **可以包含条件值**：数组中可以包含 false、null、undefined，会被忽略 | **Can Include Conditional Values**: Arrays can include false, null, undefined which will be ignored
  - **性能优化**：React Native会智能合并样式，只传递最终值给原生层 | **Performance Optimization**: React Native intelligently merges styles, only passing final values to native layer

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在 style={[styles.base, styles.highlighted]} 中，如果两个样式都定义了backgroundColor，最终会使用哪个？| In style={[styles.base, styles.highlighted]}, if both styles define backgroundColor, which one will be used?
     **答案 | Answer:** styles.highlighted中的值 | The value from styles.highlighted - 后面的样式覆盖前面的 | Later styles override earlier ones

  2. style={[styles.button, isPressed && styles.pressed]} 中，当isPressed为false时会发生什么？| In style={[styles.button, isPressed && styles.pressed]}, what happens when isPressed is false?
     **答案 | Answer:** false会被忽略，只应用styles.button | false is ignored, only styles.button is applied

  3. 样式数组中可以包含多少个样式对象？| How many style objects can be included in a style array?
     **答案 | Answer:** 没有限制 | No limit - 但应该保持合理数量以提高可读性 | But should keep reasonable number for readability

  4. 样式数组会创建新对象吗？| Do style arrays create new objects?
     **答案 | Answer:** React Native会在内部优化合并过程，不会在每次渲染时创建新对象 | React Native optimizes the merge process internally, doesn't create new objects on each render

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { StyleSheet, View, Text, Pressable } from 'react-native';
  import { useState } from 'react';

  const styles = StyleSheet.create({
    // 基础样式 | Base styles
    baseButton: {
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
      justifyContent: 'center',
      backgroundColor: '#f0f0f0',
    },

    // 主题样式 | Theme styles
    primaryButton: {
      backgroundColor: '#007AFF',
    },

    dangerButton: {
      backgroundColor: '#FF3B30',
    },

    // 状态样式 | State styles
    pressedButton: {
      opacity: 0.7,
      transform: [{ scale: 0.98 }],
    },

    disabledButton: {
      backgroundColor: '#cccccc',
      opacity: 0.5,
    },

    // 尺寸变体 | Size variants
    largeButton: {
      padding: 20,
      minWidth: 200,
    },

    smallButton: {
      padding: 10,
      minWidth: 100,
    },
  });

  // 样式组合示例 | Style composition examples
  function StyleCompositionExample() {
    const [isPrimary, setIsPrimary] = useState(true);
    const [isPressed, setIsPressed] = useState(false);
    const [isDisabled, setIsDisabled] = useState(false);

    return (
      <View style={{ padding: 20, gap: 15 }}>
        {/* 示例1：基础组合 | Example 1: Basic composition */}
        <Pressable
          style={[
            styles.baseButton,           // 基础样式 | Base style
            styles.primaryButton,        // 主题样式 | Theme style
          ]}
        >
          <Text style={{ color: 'white' }}>Primary Button</Text>
        </Pressable>

        {/* 示例2：条件样式 | Example 2: Conditional styles */}
        <Pressable
          style={[
            styles.baseButton,
            isPrimary ? styles.primaryButton : styles.dangerButton,  // 三元运算符 | Ternary operator
            isPressed && styles.pressedButton,  // 逻辑与运算符 | Logical AND operator
          ]}
          onPressIn={() => setIsPressed(true)}
          onPressOut={() => setIsPressed(false)}
        >
          <Text style={{ color: 'white' }}>
            {isPrimary ? 'Primary' : 'Danger'}
          </Text>
        </Pressable>

        {/* 示例3：多重条件 | Example 3: Multiple conditions */}
        <Pressable
          disabled={isDisabled}
          style={[
            styles.baseButton,
            styles.primaryButton,
            styles.largeButton,          // 尺寸变体 | Size variant
            isPressed && styles.pressedButton,
            isDisabled && styles.disabledButton,  // 禁用状态会覆盖主题颜色 | Disabled state overrides theme color
          ]}
        >
          <Text style={{ color: isDisabled ? '#666' : 'white' }}>
            {isDisabled ? 'Disabled' : 'Large Primary'}
          </Text>
        </Pressable>

        {/* 示例4：内联样式覆盖 | Example 4: Inline style override */}
        <View style={[
          styles.baseButton,
          styles.primaryButton,
          { backgroundColor: '#34C759' },  // 内联样式优先级最高 | Inline style has highest priority
        ]}>
          <Text style={{ color: 'white' }}>Custom Color</Text>
        </View>

        {/* 示例5：嵌套数组（会被扁平化）| Example 5: Nested arrays (will be flattened) */}
        <View style={[
          styles.baseButton,
          [styles.primaryButton, styles.largeButton],  // 嵌套数组 | Nested array
          isPressed && [styles.pressedButton, { borderWidth: 2, borderColor: '#0051D5' }],
        ]}>
          <Text style={{ color: 'white' }}>Nested Styles</Text>
        </View>
      </View>
    );
  }

  // 实用的样式组合模式 | Practical style composition pattern
  function DynamicCard({ type = 'default', elevated = false, children }) {
    const cardStyles = StyleSheet.create({
      base: {
        padding: 15,
        borderRadius: 12,
        backgroundColor: 'white',
      },
      info: { borderLeftWidth: 4, borderLeftColor: '#007AFF' },
      warning: { borderLeftWidth: 4, borderLeftColor: '#FF9500' },
      error: { borderLeftWidth: 4, borderLeftColor: '#FF3B30' },
      elevated: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.25,
        shadowRadius: 3.84,
        elevation: 5,
      },
    });

    return (
      <View style={[
        cardStyles.base,
        type === 'info' && cardStyles.info,
        type === 'warning' && cardStyles.warning,
        type === 'error' && cardStyles.error,
        elevated && cardStyles.elevated,
      ]}>
        {children}
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在 [styles.a, styles.b, { color: 'red' }] 中，哪个样式的优先级最高？| In [styles.a, styles.b, { color: 'red' }], which style has the highest priority?
    **答案 | Answer:** 内联对象 { color: 'red' } - 它在数组最后 | The inline object { color: 'red' } - it's last in the array

  - isActive && styles.active 中，isActive为null时会怎样？| In isActive && styles.active, what happens when isActive is null?
    **答案 | Answer:** 返回null，会被React Native忽略，不应用任何样式 | Returns null, ignored by React Native, no styles applied

  **常见误区检查 | Common Misconception Checks:**
  - 样式数组会降低性能吗？| Do style arrays reduce performance?
    **答案 | Answer:** 不会 | No - React Native内部会优化样式合并，性能影响可忽略不计 | React Native optimizes style merging internally, performance impact is negligible

  - 可以在样式数组中使用展开运算符吗？| Can you use spread operator in style arrays?
    **答案 | Answer:** 可以 | Yes - style={[...baseStyles, customStyle]} 是有效的，但数组语法更直观 | style={[...baseStyles, customStyle]} is valid, but array syntax is more intuitive

### 5. 性能对比：StyleSheet vs 内联样式 | Performance Comparison: StyleSheet vs Inline Styles (30分钟 | 30 minutes)

- **性能影响分析 | Performance Impact Analysis**

  **概念定义 | Concept Definition:**
  虽然在小型应用中StyleSheet.create()和内联样式的性能差异可以忽略不计，但了解它们的底层工作原理对于构建大型应用至关重要。StyleSheet提供的优化主要体现在样式的序列化和传递过程中。| While the performance difference between StyleSheet.create() and inline styles is negligible in small apps, understanding their underlying mechanisms is crucial for building large-scale applications. StyleSheet's optimizations are mainly reflected in the style serialization and transfer process.

  **核心特征 | Key Characteristics:**
  - **引用vs对象**：StyleSheet创建不可变引用，内联样式每次渲染创建新对象 | **Reference vs Object**: StyleSheet creates immutable references, inline styles create new objects on each render
  - **桥接优化**：引用ID通过桥接传递比完整对象更快 | **Bridge Optimization**: Reference IDs pass through bridge faster than complete objects
  - **开发时验证**：StyleSheet在开发模式提供属性验证 | **Development Validation**: StyleSheet provides property validation in development mode
  - **累积效应**：在复杂UI和频繁更新中差异会累积 | **Cumulative Effect**: Differences accumulate in complex UI and frequent updates

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在单个组件中，StyleSheet和内联样式的性能差异明显吗？| In a single component, is the performance difference between StyleSheet and inline styles noticeable?
     **答案 | Answer:** 不明显 | Not noticeable - 现代设备的性能足以处理 | Modern device performance is sufficient to handle it

  2. 什么场景下StyleSheet的性能优势会更明显？| In what scenarios does StyleSheet's performance advantage become more apparent?
     **答案 | Answer:** 长列表渲染、频繁重渲染的组件、复杂的样式对象 | Long list rendering, frequently re-rendered components, complex style objects

  3. StyleSheet.create()是否会阻止组件重渲染？| Does StyleSheet.create() prevent component re-rendering?
     **答案 | Answer:** 不会 | No - 它只是优化样式传递，不影响React的重渲染机制 | It only optimizes style passing, doesn't affect React's re-rendering mechanism

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { StyleSheet, View, Text, FlatList } from 'react-native';

  // 性能测试组件 | Performance test component

  // 方式1：使用StyleSheet（推荐）| Method 1: Using StyleSheet (Recommended)
  const styles = StyleSheet.create({
    item: {
      padding: 15,
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
      backgroundColor: 'white',
    },
    itemText: {
      fontSize: 16,
      color: '#333',
    },
  });

  function OptimizedListItem({ title, index }) {
    // styles.item 是一个不可变的数字ID引用
    // 每次渲染传递的是同一个引用，不需要序列化对象
    return (
      <View style={styles.item}>
        <Text style={styles.itemText}>{index}. {title}</Text>
      </View>
    );
  }

  // 方式2：使用内联样式（不推荐用于列表）| Method 2: Inline styles (Not recommended for lists)
  function UnoptimizedListItem({ title, index }) {
    // 每次渲染都创建新的样式对象
    // 在FlatList中渲染1000个项目时，会创建1000个新对象
    return (
      <View style={{
        padding: 15,
        borderBottomWidth: 1,
        borderBottomColor: '#e0e0e0',
        backgroundColor: 'white',
      }}>
        <Text style={{
          fontSize: 16,
          color: '#333',
        }}>
          {index}. {title}
        </Text>
      </View>
    );
  }

  // 方式3：混合使用（动态样式的最佳实践）| Method 3: Hybrid approach (Best practice for dynamic styles)
  function HybridListItem({ title, index, isHighlighted }) {
    return (
      <View style={[
        styles.item,
        isHighlighted && { backgroundColor: '#e3f2fd' },  // 只有条件样式用内联
      ]}>
        <Text style={[
          styles.itemText,
          isHighlighted && { fontWeight: 'bold', color: '#1976d2' },
        ]}>
          {index}. {title}
        </Text>
      </View>
    );
  }

  // 性能对比示例 | Performance comparison example
  function PerformanceComparisonDemo() {
    const data = Array.from({ length: 1000 }, (_, i) => ({
      id: i,
      title: `Item ${i}`,
    }));

    return (
      <View style={{ flex: 1 }}>
        {/* 使用优化的组件 | Using optimized component */}
        <FlatList
          data={data}
          keyExtractor={item => item.id.toString()}
          renderItem={({ item, index }) => (
            <OptimizedListItem title={item.title} index={index} />
          )}
          // 在1000项列表中，StyleSheet方式会明显更流畅
          // In a 1000-item list, StyleSheet approach will be noticeably smoother
        />
      </View>
    );
  }

  // 实际测量性能差异的方法 | Method to actually measure performance difference
  function PerformanceMeasurement() {
    // 注意：在小型应用中，这个差异可能检测不到
    // Note: In small apps, this difference might not be detectable

    const measureStyleSheetPerformance = () => {
      const start = performance.now();
      // 模拟大量样式创建
      const styleSheet = StyleSheet.create({
        ...Array.from({ length: 100 }, (_, i) => ({
          [`style${i}`]: {
            padding: i,
            margin: i * 2,
            backgroundColor: `rgb(${i}, ${i}, ${i})`,
          },
        })).reduce((acc, curr) => ({ ...acc, ...curr }), {}),
      });
      const end = performance.now();
      console.log(`StyleSheet creation: ${end - start}ms`);
    };

    const measureInlineStylePerformance = () => {
      const start = performance.now();
      // 模拟大量内联样式创建
      const inlineStyles = Array.from({ length: 100 }, (_, i) => ({
        padding: i,
        margin: i * 2,
        backgroundColor: `rgb(${i}, ${i}, ${i})`,
      }));
      const end = performance.now();
      console.log(`Inline styles creation: ${end - start}ms`);
    };

    return (
      <View style={{ padding: 20 }}>
        <Text>查看控制台输出性能对比结果</Text>
        <Text>Check console for performance comparison results</Text>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在什么情况下内联样式是可以接受的？| When are inline styles acceptable?
    **答案 | Answer:** 动态计算的值、一次性使用的样式、原型开发阶段 | Dynamically calculated values, one-time use styles, prototyping phase

  - 为什么长列表渲染中StyleSheet的性能优势更明显？| Why is StyleSheet's performance advantage more apparent in long list rendering?
    **答案 | Answer:** 减少了对象创建和垃圾回收，降低了通过桥接传递数据的开销 | Reduces object creation and garbage collection, lowers overhead of passing data through bridge

  **常见误区检查 | Common Misconception Checks:**
  - 使用StyleSheet会让应用自动变快吗？| Does using StyleSheet automatically make the app faster?
    **答案 | Answer:** 不会 | No - 它只是优化了样式传递，其他性能问题（如复杂计算、大量DOM）需要其他方式优化 | It only optimizes style passing; other performance issues (complex calculations, heavy DOM) need other optimization approaches

  - 所有内联样式都应该改为StyleSheet吗？| Should all inline styles be converted to StyleSheet?
    **答案 | Answer:** 不必要 | Not necessary - 对于动态样式（如基于props/state的计算值），内联样式或混合使用更合适 | For dynamic styles (like calculated values based on props/state), inline styles or hybrid approach is more appropriate

### 6. 最佳实践与样式组织 | Best Practices and Style Organization (30分钟 | 30 minutes)

- **样式代码组织策略 | Style Code Organization Strategies**

  **概念定义 | Concept Definition:**
  良好的样式组织不仅提高代码可维护性，还能避免样式冲突和重复。在React Native中，样式组织策略包括：组件内样式、共享样式、主题系统、以及样式命名约定。| Good style organization not only improves code maintainability but also prevents style conflicts and duplication. In React Native, style organization strategies include: component-scoped styles, shared styles, theme systems, and style naming conventions.

  **核心特征 | Key Characteristics:**
  - **就近原则**：组件特定样式与组件代码放在一起 | **Proximity Principle**: Component-specific styles stay with component code
  - **复用分离**：共享样式提取到独立文件 | **Reuse Separation**: Shared styles extracted to separate files
  - **主题化**：颜色、字体、间距等设计令牌集中管理 | **Theming**: Design tokens like colors, fonts, spacing centrally managed
  - **命名规范**：清晰的命名约定提高可读性 | **Naming Conventions**: Clear naming conventions improve readability

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 只在一个组件中使用的样式应该放在哪里？| Where should styles used only in one component be placed?
     **答案 | Answer:** 在组件文件内部，使用StyleSheet.create() | Inside the component file, using StyleSheet.create()

  2. 多个组件共享的按钮样式应该如何组织？| How should button styles shared by multiple components be organized?
     **答案 | Answer:** 提取到独立的样式文件或组件库中 | Extract to separate style file or component library

  3. 主题颜色应该硬编码在每个样式中吗？| Should theme colors be hardcoded in each style?
     **答案 | Answer:** 不应该 | No - 应该集中在主题文件中，通过常量引用 | Should be centralized in theme file, referenced through constants

  4. 样式文件应该和组件文件放在同一目录吗？| Should style files be placed in the same directory as component files?
     **答案 | Answer:** 对于组件特定样式是，对于共享样式应该有独立的styles目录 | Yes for component-specific styles, shared styles should have a separate styles directory

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // ============ 文件结构示例 | File Structure Example ============
  // src/
  // ├── components/
  // │   ├── Button/
  // │   │   ├── Button.js          // 组件逻辑
  // │   │   └── Button.styles.js   // 组件样式（可选，可以直接在组件文件中）
  // │   └── Card/
  // │       └── Card.js
  // ├── styles/
  // │   ├── theme.js               // 主题常量
  // │   ├── commonStyles.js        // 共享样式
  // │   └── typography.js          // 排版样式
  // └── App.js

  // ============ theme.js - 主题文件 | Theme File ============
  export const theme = {
    colors: {
      primary: '#007AFF',
      secondary: '#5856D6',
      success: '#34C759',
      danger: '#FF3B30',
      warning: '#FF9500',

      background: '#FFFFFF',
      surface: '#F2F2F7',
      border: '#E5E5EA',

      text: {
        primary: '#000000',
        secondary: '#3C3C43',
        tertiary: '#8E8E93',
        inverse: '#FFFFFF',
      },
    },

    spacing: {
      xs: 4,
      sm: 8,
      md: 16,
      lg: 24,
      xl: 32,
    },

    typography: {
      sizes: {
        xs: 12,
        sm: 14,
        md: 16,
        lg: 20,
        xl: 24,
        xxl: 32,
      },
      weights: {
        regular: '400',
        medium: '500',
        semibold: '600',
        bold: '700',
      },
    },

    borderRadius: {
      sm: 4,
      md: 8,
      lg: 12,
      xl: 16,
      round: 9999,
    },
  };

  // ============ commonStyles.js - 共享样式 | Shared Styles ============
  import { StyleSheet } from 'react-native';
  import { theme } from './theme';

  export const commonStyles = StyleSheet.create({
    // 布局助手 | Layout helpers
    flexCenter: {
      justifyContent: 'center',
      alignItems: 'center',
    },

    flexRow: {
      flexDirection: 'row',
      alignItems: 'center',
    },

    flexBetween: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
    },

    // 间距助手 | Spacing helpers
    padding_md: {
      padding: theme.spacing.md,
    },

    margin_md: {
      margin: theme.spacing.md,
    },

    // 阴影助手 | Shadow helpers
    shadow_sm: {
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 1 },
      shadowOpacity: 0.18,
      shadowRadius: 1.0,
      elevation: 1,
    },

    shadow_md: {
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.23,
      shadowRadius: 2.62,
      elevation: 4,
    },
  });

  // ============ Button.js - 组件内样式 | Component-scoped Styles ============
  import { StyleSheet, Pressable, Text } from 'react-native';
  import { theme } from '../styles/theme';

  export function Button({
    title,
    variant = 'primary',
    size = 'md',
    onPress
  }) {
    return (
      <Pressable
        style={[
          styles.base,
          styles[variant],          // 动态选择变体样式 | Dynamic variant selection
          styles[`size_${size}`],   // 动态选择尺寸样式 | Dynamic size selection
        ]}
        onPress={onPress}
      >
        <Text style={[
          styles.text,
          styles[`text_${variant}`],
        ]}>
          {title}
        </Text>
      </Pressable>
    );
  }

  // 组件专属样式，使用主题常量 | Component-specific styles using theme constants
  const styles = StyleSheet.create({
    base: {
      borderRadius: theme.borderRadius.md,
      justifyContent: 'center',
      alignItems: 'center',
    },

    // 变体样式 | Variant styles
    primary: {
      backgroundColor: theme.colors.primary,
    },

    secondary: {
      backgroundColor: theme.colors.secondary,
    },

    outline: {
      backgroundColor: 'transparent',
      borderWidth: 1,
      borderColor: theme.colors.primary,
    },

    // 尺寸样式 | Size styles
    size_sm: {
      paddingVertical: theme.spacing.sm,
      paddingHorizontal: theme.spacing.md,
    },

    size_md: {
      paddingVertical: theme.spacing.md,
      paddingHorizontal: theme.spacing.lg,
    },

    size_lg: {
      paddingVertical: theme.spacing.lg,
      paddingHorizontal: theme.spacing.xl,
    },

    // 文本样式 | Text styles
    text: {
      fontSize: theme.typography.sizes.md,
      fontWeight: theme.typography.weights.semibold,
    },

    text_primary: {
      color: theme.colors.text.inverse,
    },

    text_secondary: {
      color: theme.colors.text.inverse,
    },

    text_outline: {
      color: theme.colors.primary,
    },
  });

  // ============ 最佳实践总结 | Best Practices Summary ============

  // ✅ 推荐做法 | Recommended Practices
  const goodPractices = {
    // 1. 使用主题常量
    useTheme: StyleSheet.create({
      container: {
        backgroundColor: theme.colors.background,  // ✅ 使用主题
        padding: theme.spacing.md,
      },
    }),

    // 2. 语义化命名
    semanticNaming: StyleSheet.create({
      primaryButton: { /* ... */ },     // ✅ 表达意图
      dangerText: { /* ... */ },
    }),

    // 3. 组合而非重复
    composition: [
      commonStyles.flexCenter,           // ✅ 复用共享样式
      commonStyles.padding_md,
      { backgroundColor: 'white' },      // 添加特定样式
    ],
  };

  // ❌ 不推荐做法 | Not Recommended Practices
  const badPractices = {
    // 1. 硬编码颜色值
    hardcodedColors: StyleSheet.create({
      container: {
        backgroundColor: '#007AFF',      // ❌ 应使用 theme.colors.primary
      },
    }),

    // 2. 无意义命名
    poorNaming: StyleSheet.create({
      style1: { /* ... */ },             // ❌ 命名不清晰
      bigRed: { /* ... */ },             // ❌ 描述外观而非用途
    }),

    // 3. 重复样式
    duplication: StyleSheet.create({
      button1: {
        justifyContent: 'center',
        alignItems: 'center',            // ❌ 重复的居中样式
      },
      button2: {
        justifyContent: 'center',
        alignItems: 'center',            // ❌ 应提取为共享样式
      },
    }),
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 什么时候应该将样式提取到独立文件？| When should styles be extracted to a separate file?
    **答案 | Answer:** 当样式超过50-100行、被多个组件共享、或影响代码可读性时 | When styles exceed 50-100 lines, are shared by multiple components, or affect code readability

  - 如何命名表示状态的样式？| How to name styles that represent states?
    **答案 | Answer:** 使用形容词或状态名，如 pressed, active, disabled, highlighted | Use adjectives or state names like pressed, active, disabled, highlighted

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该为每个颜色创建样式类？| Should you create a style class for every color?
    **答案 | Answer:** 不应该 | No - 颜色应该在主题中定义，样式应该基于语义（如primary、danger）而非具体颜色值 | Colors should be defined in theme, styles should be based on semantics (like primary, danger) not specific color values

  - 所有样式都应该在StyleSheet.create()中吗？| Should all styles be in StyleSheet.create()?
    **答案 | Answer:** 大部分应该，但动态计算的样式（基于props的宽度、位置等）可以用内联方式 | Most should, but dynamically calculated styles (width, position based on props) can be inline

## 实践项目：样式展示应用 | Practical Project: Style Showcase App

### 目标 | Objective
构建一个展示React Native样式系统各种特性的演示应用，包括StyleSheet API、样式组合、主题系统和性能对比。这个项目将综合应用本日所学的所有概念。| Build a demo app showcasing various features of React Native's styling system, including StyleSheet API, style composition, theme system, and performance comparison. This project comprehensively applies all concepts learned today.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. StyleSheet.create() 和内联样式的主要区别是什么？| What's the main difference between StyleSheet.create() and inline styles?
   **答案 | Answer:** StyleSheet.create()创建不可变的样式引用，提供性能优化和开发时验证；内联样式每次渲染创建新对象 | StyleSheet.create() creates immutable style references, providing performance optimization and development-time validation; inline styles create new objects on each render

2. 如何使用数组语法实现条件样式？| How to implement conditional styles using array syntax?
   **答案 | Answer:** style={[styles.base, condition && styles.conditional]} - 条件为true时应用额外样式 | style={[styles.base, condition && styles.conditional]} - additional style applied when condition is true

3. 为什么要使用主题系统？| Why use a theme system?
   **答案 | Answer:** 集中管理设计令牌（颜色、间距等），确保一致性，便于维护和切换主题 | Centrally manage design tokens (colors, spacing, etc.), ensure consistency, facilitate maintenance and theme switching

### 步骤 | Steps
1. **创建项目结构**：设置主题文件、共享样式、组件目录 | **Create Project Structure**: Set up theme file, shared styles, component directories
2. **实现主题系统**：定义颜色、间距、排版常量 | **Implement Theme System**: Define colors, spacing, typography constants
3. **构建样式演示组件**：展示不同样式技术 | **Build Style Demo Components**: Showcase different styling techniques
4. **实现性能对比**：创建列表对比StyleSheet和内联样式 | **Implement Performance Comparison**: Create list comparing StyleSheet and inline styles
5. **添加交互和状态**：实现动态样式切换 | **Add Interaction and State**: Implement dynamic style switching

### 示例代码 | Example Code
```javascript
"""
样式展示应用 | Style Showcase App
展示React Native样式系统的核心概念和最佳实践 | Demonstrating core concepts and best practices of React Native styling system

本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
- StyleSheet API的使用 | Using StyleSheet API
- 样式组合与条件样式 | Style composition and conditional styles
- 主题系统的实现 | Theme system implementation
- 性能优化最佳实践 | Performance optimization best practices
"""

import React, { useState } from 'react';
import {
  StyleSheet,
  View,
  Text,
  Pressable,
  ScrollView,
  SafeAreaView,
  FlatList,
  Switch,
} from 'react-native';

// ============ 主题定义 | Theme Definition ============
const theme = {
  colors: {
    primary: '#007AFF',
    secondary: '#5856D6',
    success: '#34C759',
    danger: '#FF3B30',
    background: '#F2F2F7',
    surface: '#FFFFFF',
    border: '#E5E5EA',
    text: {
      primary: '#000000',
      secondary: '#8E8E93',
      inverse: '#FFFFFF',
    },
  },
  spacing: {
    xs: 4,
    sm: 8,
    md: 16,
    lg: 24,
    xl: 32,
  },
  typography: {
    sizes: {
      sm: 14,
      md: 16,
      lg: 20,
      xl: 28,
    },
    weights: {
      regular: '400',
      semibold: '600',
      bold: '700',
    },
  },
  borderRadius: {
    sm: 8,
    md: 12,
    lg: 16,
  },
};

// ============ 共享样式 | Shared Styles ============
const commonStyles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.colors.background,
  },
  section: {
    backgroundColor: theme.colors.surface,
    padding: theme.spacing.md,
    marginBottom: theme.spacing.md,
    borderRadius: theme.borderRadius.md,
  },
  sectionTitle: {
    fontSize: theme.typography.sizes.lg,
    fontWeight: theme.typography.weights.bold,
    color: theme.colors.text.primary,
    marginBottom: theme.spacing.md,
  },
  flexRow: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  shadow: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3,
  },
});

// ============ 可复用按钮组件 | Reusable Button Component ============
function CustomButton({
  title,
  variant = 'primary',
  size = 'md',
  onPress,
  disabled = false,
}) {
  const [isPressed, setIsPressed] = useState(false);

  const buttonStyles = StyleSheet.create({
    base: {
      borderRadius: theme.borderRadius.sm,
      justifyContent: 'center',
      alignItems: 'center',
    },
    primary: {
      backgroundColor: theme.colors.primary,
    },
    secondary: {
      backgroundColor: theme.colors.secondary,
    },
    danger: {
      backgroundColor: theme.colors.danger,
    },
    outline: {
      backgroundColor: 'transparent',
      borderWidth: 2,
      borderColor: theme.colors.primary,
    },
    size_sm: {
      paddingVertical: theme.spacing.sm,
      paddingHorizontal: theme.spacing.md,
    },
    size_md: {
      paddingVertical: theme.spacing.md,
      paddingHorizontal: theme.spacing.lg,
    },
    size_lg: {
      paddingVertical: theme.spacing.lg,
      paddingHorizontal: theme.spacing.xl,
    },
    pressed: {
      opacity: 0.7,
      transform: [{ scale: 0.98 }],
    },
    disabled: {
      backgroundColor: theme.colors.border,
      opacity: 0.5,
    },
    text: {
      fontSize: size === 'sm' ? theme.typography.sizes.sm :
               size === 'lg' ? theme.typography.sizes.lg :
               theme.typography.sizes.md,
      fontWeight: theme.typography.weights.semibold,
    },
    text_primary: { color: theme.colors.text.inverse },
    text_secondary: { color: theme.colors.text.inverse },
    text_danger: { color: theme.colors.text.inverse },
    text_outline: { color: theme.colors.primary },
    text_disabled: { color: theme.colors.text.secondary },
  });

  return (
    <Pressable
      style={[
        buttonStyles.base,
        buttonStyles[variant],
        buttonStyles[`size_${size}`],
        isPressed && buttonStyles.pressed,
        disabled && buttonStyles.disabled,
      ]}
      onPress={onPress}
      onPressIn={() => setIsPressed(true)}
      onPressOut={() => setIsPressed(false)}
      disabled={disabled}
    >
      <Text
        style={[
          buttonStyles.text,
          disabled ? buttonStyles.text_disabled : buttonStyles[`text_${variant}`],
        ]}
      >
        {title}
      </Text>
    </Pressable>
  );
}

// ============ 样式组合演示 | Style Composition Demo ============
function StyleCompositionDemo() {
  const [isActive, setIsActive] = useState(false);

  const demoStyles = StyleSheet.create({
    card: {
      padding: theme.spacing.lg,
      borderRadius: theme.borderRadius.md,
      backgroundColor: theme.colors.surface,
    },
    activeCard: {
      backgroundColor: theme.colors.primary,
      borderWidth: 2,
      borderColor: theme.colors.secondary,
    },
    cardText: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.text.primary,
    },
    activeCardText: {
      color: theme.colors.text.inverse,
      fontWeight: theme.typography.weights.bold,
    },
  });

  return (
    <View style={commonStyles.section}>
      <Text style={commonStyles.sectionTitle}>
        样式组合演示 | Style Composition Demo
      </Text>

      <Pressable
        style={[
          demoStyles.card,
          commonStyles.shadow,
          isActive && demoStyles.activeCard,  // 条件样式 | Conditional style
        ]}
        onPress={() => setIsActive(!isActive)}
      >
        <Text style={[
          demoStyles.cardText,
          isActive && demoStyles.activeCardText,
        ]}>
          {isActive ? '激活状态 | Active' : '点击激活 | Tap to Activate'}
        </Text>
      </Pressable>

      <Text style={{
        marginTop: theme.spacing.md,
        fontSize: theme.typography.sizes.sm,
        color: theme.colors.text.secondary
      }}>
        使用数组语法组合样式 | Using array syntax to compose styles
      </Text>
    </View>
  );
}

// ============ 性能对比演示 | Performance Comparison Demo ============
function PerformanceComparisonDemo() {
  const [useStyleSheet, setUseStyleSheet] = useState(true);

  // 使用StyleSheet创建的样式 | Styles created with StyleSheet
  const optimizedStyles = StyleSheet.create({
    item: {
      padding: theme.spacing.md,
      borderBottomWidth: 1,
      borderBottomColor: theme.colors.border,
      backgroundColor: theme.colors.surface,
    },
    itemText: {
      fontSize: theme.typography.sizes.md,
      color: theme.colors.text.primary,
    },
  });

  // 模拟数据 | Mock data
  const data = Array.from({ length: 50 }, (_, i) => ({
    id: i.toString(),
    title: `Item ${i + 1}`,
  }));

  // 使用StyleSheet的优化渲染 | Optimized rendering with StyleSheet
  const renderOptimizedItem = ({ item }) => (
    <View style={optimizedStyles.item}>
      <Text style={optimizedStyles.itemText}>{item.title}</Text>
    </View>
  );

  // 使用内联样式的未优化渲染 | Unoptimized rendering with inline styles
  const renderInlineItem = ({ item }) => (
    <View style={{
      padding: theme.spacing.md,
      borderBottomWidth: 1,
      borderBottomColor: theme.colors.border,
      backgroundColor: theme.colors.surface,
    }}>
      <Text style={{
        fontSize: theme.typography.sizes.md,
        color: theme.colors.text.primary,
      }}>
        {item.title}
      </Text>
    </View>
  );

  return (
    <View style={commonStyles.section}>
      <Text style={commonStyles.sectionTitle}>
        性能对比 | Performance Comparison
      </Text>

      <View style={[commonStyles.flexRow, { marginBottom: theme.spacing.md }]}>
        <Text style={{ flex: 1, fontSize: theme.typography.sizes.md }}>
          使用 StyleSheet | Use StyleSheet
        </Text>
        <Switch
          value={useStyleSheet}
          onValueChange={setUseStyleSheet}
          trackColor={{ false: theme.colors.border, true: theme.colors.primary }}
        />
      </View>

      <View style={{ height: 300, borderRadius: theme.borderRadius.sm, overflow: 'hidden' }}>
        <FlatList
          data={data}
          keyExtractor={item => item.id}
          renderItem={useStyleSheet ? renderOptimizedItem : renderInlineItem}
        />
      </View>

      <Text style={{
        marginTop: theme.spacing.md,
        fontSize: theme.typography.sizes.sm,
        color: theme.colors.text.secondary,
      }}>
        {useStyleSheet
          ? '当前使用StyleSheet（推荐）| Currently using StyleSheet (Recommended)'
          : '当前使用内联样式 | Currently using inline styles'}
      </Text>
    </View>
  );
}

// ============ 按钮变体展示 | Button Variants Showcase ============
function ButtonVariantsDemo() {
  return (
    <View style={commonStyles.section}>
      <Text style={commonStyles.sectionTitle}>
        按钮变体 | Button Variants
      </Text>

      <View style={{ gap: theme.spacing.md }}>
        <CustomButton title="Primary Button" variant="primary" />
        <CustomButton title="Secondary Button" variant="secondary" />
        <CustomButton title="Danger Button" variant="danger" />
        <CustomButton title="Outline Button" variant="outline" />
        <CustomButton title="Disabled Button" variant="primary" disabled />

        <View style={commonStyles.flexRow}>
          <CustomButton title="Small" variant="primary" size="sm" />
          <View style={{ width: theme.spacing.sm }} />
          <CustomButton title="Medium" variant="primary" size="md" />
          <View style={{ width: theme.spacing.sm }} />
          <CustomButton title="Large" variant="primary" size="lg" />
        </View>
      </View>
    </View>
  );
}

// ============ 主应用组件 | Main App Component ============
export default function App() {
  return (
    <SafeAreaView style={commonStyles.container}>
      <ScrollView
        contentContainerStyle={{ padding: theme.spacing.md }}
        showsVerticalScrollIndicator={false}
      >
        <Text style={{
          fontSize: theme.typography.sizes.xl,
          fontWeight: theme.typography.weights.bold,
          color: theme.colors.text.primary,
          marginBottom: theme.spacing.lg,
        }}>
          React Native 样式系统 | Styling System
        </Text>

        <StyleCompositionDemo />
        <ButtonVariantsDemo />
        <PerformanceComparisonDemo />

        <View style={[commonStyles.section, { marginBottom: theme.spacing.xl }]}>
          <Text style={commonStyles.sectionTitle}>
            关键概念总结 | Key Concepts Summary
          </Text>

          <Text style={{
            fontSize: theme.typography.sizes.md,
            lineHeight: 24,
            color: theme.colors.text.primary
          }}>
            • StyleSheet.create() 提供性能优化{'\n'}
            • 使用数组语法组合样式{'\n'}
            • 主题系统确保设计一致性{'\n'}
            • 条件样式实现动态UI{'\n'}
            • 命名规范采用驼峰命名法{'\n'}
            • 许多CSS属性不可用（Grid、Float等）
          </Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确使用了StyleSheet.create()？| Does the project correctly use StyleSheet.create()?
2. 是否实现了主题系统并在组件中使用？| Is the theme system implemented and used in components?
3. 样式组合是否使用了数组语法？| Does style composition use array syntax?
4. 是否展示了条件样式的应用？| Does it demonstrate conditional style application?
5. 性能对比是否清晰展示了两种方式的差异？| Does the performance comparison clearly show the difference between the two approaches?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **StyleSheet深度理解练习 | StyleSheet Deep Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建一个实验，对比在100次渲染循环中StyleSheet和内联样式的实际性能差异，使用performance.now()测量时间
   - **概念检查 | Concept Check:** StyleSheet.create()创建的是什么类型的值？它如何影响性能？
   - **学习目标 | Learning Objective:** 通过实际测量理解性能优化的量化影响

2. **样式命名规范练习 | Style Naming Convention Exercise**
   - **练习描述 | Exercise Description:** 将以下CSS属性名转换为React Native格式：background-color, font-size, padding-top, margin-bottom, border-radius, justify-content, align-items, flex-direction
   - **概念检查 | Concept Check:** 为什么React Native使用驼峰命名而不是短横线命名？
   - **学习目标 | Learning Objective:** 熟练掌握CSS到React Native的命名转换规则

3. **样式组合模式练习 | Style Composition Pattern Exercise**
   - **练习描述 | Exercise Description:** 创建一个Card组件，支持5种不同的变体（default, info, warning, error, success）和3种尺寸（sm, md, lg），使用数组语法组合样式
   - **概念检查 | Concept Check:** 在style数组中，如果多个样式定义了相同属性，哪个会生效？
   - **学习目标 | Learning Objective:** 掌握复杂的条件样式组合技巧

4. **主题系统实现练习 | Theme System Implementation Exercise**
   - **练习描述 | Exercise Description:** 实现一个完整的主题系统，支持亮色和暗色模式切换，包括颜色、间距、排版的完整定义
   - **概念检查 | Concept Check:** 主题系统如何提高代码的可维护性？
   - **学习目标 | Learning Objective:** 理解设计系统和主题化的重要性

5. **CSS限制应对练习 | CSS Limitations Workaround Exercise**
   - **练习描述 | Exercise Description:** 不使用Grid布局，仅用Flexbox实现一个响应式的图片网格（2列、3列、4列自适应）
   - **概念检查 | Concept Check:** React Native不支持哪些常用的CSS布局属性？如何用Flexbox替代？
   - **学习目标 | Learning Objective:** 学会用Flexbox解决没有Grid布局的限制

6. **动态样式性能优化练习 | Dynamic Style Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 创建一个列表组件，项目颜色基于索引动态变化，对比三种实现方式的性能：完全内联样式、StyleSheet+内联混合、useMemo优化
   - **概念检查 | Concept Check:** 什么情况下内联样式是可接受的？如何优化动态样式的性能？
   - **学习目标 | Learning Objective:** 理解动态样式的性能权衡和优化策略

7. **样式复用架构练习 | Style Reuse Architecture Exercise**
   - **练习描述 | Exercise Description:** 为一个应用设计完整的样式文件结构，包括：theme.js、commonStyles.js、typography.js，并创建至少5个组件使用这些共享样式
   - **概念检查 | Concept Check:** 如何组织样式代码以最大化复用性和可维护性？
   - **学习目标 | Learning Objective:** 建立可扩展的样式架构思维

## 学习资源 | Learning Resources
- [React Native StyleSheet API 官方文档](https://reactnative.dev/docs/stylesheet) | [React Native StyleSheet API Official Documentation](https://reactnative.dev/docs/stylesheet)
- [React Native样式属性完整列表](https://reactnative.dev/docs/view-style-props) | [Complete List of React Native Style Properties](https://reactnative.dev/docs/view-style-props)
- [从Web到Mobile的样式迁移指南](https://reactnative.dev/docs/style) | [Style Migration Guide from Web to Mobile](https://reactnative.dev/docs/style)
- [React Native性能优化最佳实践](https://reactnative.dev/docs/performance) | [React Native Performance Optimization Best Practices](https://reactnative.dev/docs/performance)
- [设计系统和主题化案例研究](https://medium.com/styled-components/building-a-design-system-for-react-native-7c3a3a1c8b9e) | [Design Systems and Theming Case Studies](https://medium.com/styled-components/building-a-design-system-for-react-native-7c3a3a1c8b9e)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解StyleSheet.create()的作用和性能优势 | Understand the role and performance benefits of StyleSheet.create()
- [ ] 掌握React Native样式属性的驼峰命名规则 | Master camelCase naming rules for React Native style properties
- [ ] 了解React Native中不可用的CSS属性及其替代方案 | Know unavailable CSS properties in React Native and their alternatives
- [ ] 熟练使用数组语法进行样式组合 | Proficiently use array syntax for style composition
- [ ] 能够实现条件样式和动态样式 | Able to implement conditional and dynamic styles
- [ ] 理解StyleSheet和内联样式的性能差异 | Understand performance differences between StyleSheet and inline styles
- [ ] 掌握主题系统的设计和实现 | Master theme system design and implementation
- [ ] 能够组织大型应用的样式代码结构 | Able to organize style code structure for large apps
- [ ] 完成样式展示应用项目 | Complete the Style Showcase App project
- [ ] 完成至少3个扩展练习 | Complete at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释每个核心概念。特别要能解释：为什么React Native使用JavaScript对象而非CSS、StyleSheet的性能优化原理、以及如何用Flexbox替代不支持的CSS布局。

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain each core concept to others. Particularly, be able to explain: why React Native uses JavaScript objects instead of CSS, the performance optimization principles of StyleSheet, and how to use Flexbox to replace unsupported CSS layouts.
