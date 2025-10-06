# React Native入门 - 第2天：文本组件与排版 | React Native Introduction - Day 2: Text Components and Typography

## 学习目标 | Learning Objectives
- 掌握React Native的Text组件及其核心属性 | Master the React Native Text component and its core properties
- 理解移动端文本渲染的关键约束与最佳实践 | Understand key constraints and best practices for mobile text rendering
- 学会使用嵌套Text组件创建富文本样式 | Learn to use nested Text components to create rich text styles
- 掌握常用排版属性：字重、字号、颜色、对齐 | Master common typography properties: fontWeight, fontSize, color, textAlign
- 理解系统字体与自定义字体的区别 | Understand the difference between system fonts and custom fonts
- 能够创建可复用的文本样式组件 | Be able to create reusable text style components

## 详细内容 | Detailed Content

### 1. Text组件基础 | Text Component Fundamentals (1小时 | 1 hour)

- **核心概念：Text组件的必要性 | Core Concept: The Necessity of Text Component**

  **概念定义 | Concept Definition:**
  在React Native中，所有文本内容必须包裹在`<Text>`组件中。这与Web开发的重要区别是：你不能直接将文本放在`<View>`或其他容器组件中。Text组件是React Native中唯一能渲染文本的组件。

  In React Native, all text content must be wrapped in a `<Text>` component. This is a crucial difference from web development: you cannot place text directly inside a `<View>` or other container components. The Text component is the only component in React Native that can render text.

  **核心特征 | Key Characteristics:**
  - Text组件是文本渲染的唯一容器 - 违反此规则会导致运行时错误 | Text component is the only container for text rendering - violating this rule causes runtime errors
  - Text组件可以嵌套，子Text会继承父Text的样式 | Text components can be nested, and child Text components inherit parent styles
  - Text组件支持触摸事件（onPress），使文本可交互 | Text components support touch events (onPress), making text interactive
  - Text内容会根据容器宽度自动换行 | Text content automatically wraps based on container width

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以直接在View组件中放置文本字符串吗？| Can you place text strings directly inside a View component?
     **答案 | Answer:** 否 | No - 所有文本必须包裹在Text组件中，否则会抛出错误 | All text must be wrapped in a Text component, otherwise an error will be thrown
  2. Text组件可以嵌套其他Text组件吗？| Can Text components nest other Text components?
     **答案 | Answer:** 是 | Yes - 嵌套的Text会继承父Text的样式属性 | Nested Text components inherit style properties from parent Text
  3. Text组件只能用于显示静态文本吗？| Are Text components only for displaying static text?
     **答案 | Answer:** 否 | No - Text组件支持onPress等交互事件，可以实现可点击的文本 | Text components support interactive events like onPress, enabling clickable text
  4. 如果Text组件的内容超过容器宽度会发生什么？| What happens if Text component content exceeds container width?
     **答案 | Answer:** 自动换行 | Automatic wrapping - 除非设置numberOfLines属性限制行数 | Unless numberOfLines prop is set to limit lines

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  // ✅ 正确用法 | Correct Usage
  function CorrectExample() {
    return (
      <View style={styles.container}>
        <Text>这是正确的文本渲染方式 | This is the correct way to render text</Text>
      </View>
    );
  }

  // ❌ 错误用法 - 这会导致运行时错误！| Wrong Usage - This will cause a runtime error!
  function WrongExample() {
    return (
      <View style={styles.container}>
        直接放置文本会报错 | Directly placing text causes an error
      </View>
    );
    // 错误信息 | Error: Text strings must be rendered within a <Text> component
  }

  // ✅ 嵌套Text示例 | Nested Text Example
  function NestedTextExample() {
    return (
      <Text style={styles.parentText}>
        这是父文本 | This is parent text
        <Text style={styles.childText}>
          这是继承了父样式的子文本 | This is child text inheriting parent styles
        </Text>
      </Text>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    parentText: {
      fontSize: 16,
      color: 'blue',
    },
    childText: {
      fontWeight: 'bold', // 继承fontSize和color，添加fontWeight | Inherits fontSize and color, adds fontWeight
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果运行WrongExample会看到什么错误？| What error will you see if running WrongExample?
    **答案 | Answer:** "Text strings must be rendered within a <Text> component" - React Native运行时错误 | React Native runtime error
  - NestedTextExample中的childText会是什么颜色？| What color will childText be in NestedTextExample?
    **答案 | Answer:** 蓝色(blue) - 从parentText继承了color属性 | Blue - inherits the color property from parentText

  **常见误区检查 | Common Misconception Checks:**
  - 误区：Text组件就像HTML的span或div，可以随意使用 | Misconception: Text components are like HTML span or div, can be used freely
    **答案 | Answer:** 错误 | Wrong - Text组件有严格的使用规则，必须包裹所有文本内容 | Text components have strict usage rules, must wrap all text content
  - 误区：嵌套Text会创建独立的样式作用域 | Misconception: Nested Text creates isolated style scopes
    **答案 | Answer:** 错误 | Wrong - 子Text会继承父Text的样式，除非显式覆盖 | Child Text inherits parent Text styles unless explicitly overridden

### 2. 文本样式属性 | Text Styling Properties (1小时 | 1 hour)

- **核心概念：Typography属性系统 | Core Concept: Typography Property System**

  **概念定义 | Concept Definition:**
  React Native的Text组件提供了丰富的排版属性来控制文本的视觉呈现。这些属性包括字体大小(fontSize)、字重(fontWeight)、颜色(color)、文本对齐(textAlign)等。与Web CSS不同，React Native使用JavaScript对象来定义样式，属性名采用驼峰命名法。

  React Native's Text component provides rich typography properties to control text visual presentation. These properties include font size (fontSize), font weight (fontWeight), color (color), text alignment (textAlign), etc. Unlike Web CSS, React Native uses JavaScript objects to define styles with camelCase property names.

  **核心特征 | Key Characteristics:**
  - 样式属性使用驼峰命名法（fontSize而非font-size）| Style properties use camelCase (fontSize not font-size)
  - 数值类型的属性不需要单位（fontSize: 16而非'16px'）| Numeric properties don't need units (fontSize: 16 not '16px')
  - 颜色值支持多种格式：命名颜色、十六进制、rgb/rgba | Color values support multiple formats: named colors, hex, rgb/rgba
  - fontWeight可以是字符串('bold')或数值('100'-'900') | fontWeight can be string ('bold') or numeric ('100'-'900')

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中设置fontSize时需要添加'px'单位吗？| Do you need to add 'px' unit when setting fontSize in React Native?
     **答案 | Answer:** 否 | No - 直接使用数值，如fontSize: 16 | Use numbers directly, like fontSize: 16
  2. fontWeight: 'bold'和fontWeight: '700'效果相同吗？| Are fontWeight: 'bold' and fontWeight: '700' the same?
     **答案 | Answer:** 是 | Yes - 'bold'相当于'700'字重 | 'bold' is equivalent to '700' weight
  3. textAlign属性可以应用在View组件上吗？| Can the textAlign property be applied to View components?
     **答案 | Answer:** 否 | No - textAlign只对Text组件有效 | textAlign only works on Text components
  4. color: 'rgb(255, 0, 0)'和color: '#FF0000'显示的颜色相同吗？| Do color: 'rgb(255, 0, 0)' and color: '#FF0000' display the same color?
     **答案 | Answer:** 是 | Yes - 两者都表示红色，只是格式不同 | Both represent red color, just different formats

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  function TypographyShowcase() {
    return (
      <View style={styles.container}>
        {/* 标题样式 | Heading Styles */}
        <Text style={styles.heading1}>
          一级标题 H1 | Heading 1
        </Text>
        <Text style={styles.heading2}>
          二级标题 H2 | Heading 2
        </Text>

        {/* 不同字重演示 | Font Weight Demo */}
        <Text style={styles.light}>
          细体文本 (fontWeight: '300') | Light Text
        </Text>
        <Text style={styles.normal}>
          正常文本 (fontWeight: '400') | Normal Text
        </Text>
        <Text style={styles.bold}>
          粗体文本 (fontWeight: 'bold') | Bold Text
        </Text>

        {/* 文本对齐演示 | Text Alignment Demo */}
        <Text style={styles.alignLeft}>
          左对齐文本 | Left Aligned Text
        </Text>
        <Text style={styles.alignCenter}>
          居中对齐文本 | Center Aligned Text
        </Text>
        <Text style={styles.alignRight}>
          右对齐文本 | Right Aligned Text
        </Text>

        {/* 颜色演示 | Color Demo */}
        <Text style={styles.colorNamed}>
          命名颜色: 'tomato' | Named color: 'tomato'
        </Text>
        <Text style={styles.colorHex}>
          十六进制颜色: '#3498db' | Hex color: '#3498db'
        </Text>
        <Text style={styles.colorRgb}>
          RGB颜色: 'rgb(46, 204, 113)' | RGB color: 'rgb(46, 204, 113)'
        </Text>
        <Text style={styles.colorRgba}>
          RGBA颜色（50%透明）| RGBA color (50% opacity)
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    heading1: {
      fontSize: 32,
      fontWeight: 'bold',
      color: '#2c3e50',
      marginBottom: 10,
    },
    heading2: {
      fontSize: 24,
      fontWeight: '600',
      color: '#34495e',
      marginBottom: 20,
    },
    light: {
      fontSize: 16,
      fontWeight: '300',
      marginVertical: 5,
    },
    normal: {
      fontSize: 16,
      fontWeight: '400',
      marginVertical: 5,
    },
    bold: {
      fontSize: 16,
      fontWeight: 'bold', // 等同于 '700' | Equivalent to '700'
      marginVertical: 5,
    },
    alignLeft: {
      fontSize: 16,
      textAlign: 'left',
      backgroundColor: '#ecf0f1',
      padding: 10,
      marginVertical: 5,
    },
    alignCenter: {
      fontSize: 16,
      textAlign: 'center',
      backgroundColor: '#ecf0f1',
      padding: 10,
      marginVertical: 5,
    },
    alignRight: {
      fontSize: 16,
      textAlign: 'right',
      backgroundColor: '#ecf0f1',
      padding: 10,
      marginVertical: 5,
    },
    colorNamed: {
      fontSize: 16,
      color: 'tomato',
      marginVertical: 5,
    },
    colorHex: {
      fontSize: 16,
      color: '#3498db',
      marginVertical: 5,
    },
    colorRgb: {
      fontSize: 16,
      color: 'rgb(46, 204, 113)',
      marginVertical: 5,
    },
    colorRgba: {
      fontSize: 16,
      color: 'rgba(155, 89, 182, 0.5)',
      marginVertical: 5,
    },
  });

  export default TypographyShowcase;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - heading2的fontWeight数值是多少？| What is the numeric value of heading2's fontWeight?
    **答案 | Answer:** 600 - 介于normal(400)和bold(700)之间的半粗体 | Semi-bold between normal(400) and bold(700)
  - 如果将textAlign: 'center'应用到容器View会有效果吗？| Would applying textAlign: 'center' to a container View work?
    **答案 | Answer:** 否 | No - textAlign只对Text组件有效，View需要使用alignItems | textAlign only works on Text, View needs alignItems

  **常见误区检查 | Common Misconception Checks:**
  - 误区：fontSize: '16px'是正确的写法 | Misconception: fontSize: '16px' is the correct syntax
    **答案 | Answer:** 错误 | Wrong - 应该使用数值：fontSize: 16，不需要单位 | Should use number: fontSize: 16, no units needed
  - 误区：所有CSS属性都可以直接用在React Native中 | Misconception: All CSS properties work directly in React Native
    **答案 | Answer:** 错误 | Wrong - React Native只支持部分CSS属性的子集，且命名方式不同 | React Native only supports a subset of CSS properties with different naming

### 3. 嵌套文本与富文本样式 | Nested Text and Rich Text Styling (1小时 | 1 hour)

- **核心概念：Text嵌套与样式继承 | Core Concept: Text Nesting and Style Inheritance**

  **概念定义 | Concept Definition:**
  React Native允许Text组件嵌套，这是创建富文本样式的主要方法。嵌套的Text组件会继承父Text的样式属性，同时可以添加或覆盖特定样式。这种机制使得在同一段文本中实现多种样式组合成为可能，类似于HTML中的内联样式但更加结构化。

  React Native allows Text component nesting, which is the primary method for creating rich text styles. Nested Text components inherit parent Text style properties while being able to add or override specific styles. This mechanism makes it possible to achieve multiple style combinations within the same text passage, similar to inline styles in HTML but more structured.

  **核心特征 | Key Characteristics:**
  - 子Text继承父Text的所有样式属性 | Child Text inherits all style properties from parent Text
  - 子Text可以选择性覆盖继承的样式 | Child Text can selectively override inherited styles
  - 嵌套Text可以有自己的onPress事件处理器 | Nested Text can have its own onPress event handlers
  - 样式继承仅在Text组件之间有效，不跨越View边界 | Style inheritance only works between Text components, not across View boundaries

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果父Text的fontSize是16，子Text没有设置fontSize，子Text的字号是多少？| If parent Text fontSize is 16 and child Text doesn't set fontSize, what is the child's font size?
     **答案 | Answer:** 16 - 子Text继承父Text的fontSize | 16 - child Text inherits parent Text's fontSize
  2. 子Text可以设置与父Text不同的onPress处理器吗？| Can child Text have a different onPress handler than parent Text?
     **答案 | Answer:** 可以 | Yes - 每个Text组件可以有独立的事件处理器 | Each Text component can have independent event handlers
  3. View组件中的Text会继承View的样式吗？| Will Text inside a View inherit the View's styles?
     **答案 | Answer:** 否 | No - 样式继承只发生在Text组件之间 | Style inheritance only occurs between Text components
  4. 如果父Text和子Text都设置了color，显示哪个颜色？| If both parent and child Text set color, which color is displayed?
     **答案 | Answer:** 子Text的color | Child Text's color - 子组件样式覆盖父组件样式 | Child component styles override parent styles

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import { View, Text, StyleSheet, Alert } from 'react-native';

  function RichTextExample() {
    const [pressedPart, setPressedPart] = useState('');

    return (
      <View style={styles.container}>
        {/* 基础嵌套示例 | Basic Nesting Example */}
        <Text style={styles.paragraph}>
          这是一段普通文本，
          <Text style={styles.bold}>这部分是粗体，</Text>
          <Text style={styles.italic}>这部分是斜体，</Text>
          <Text style={styles.boldItalic}>这部分又粗又斜。</Text>
        </Text>

        {/* 英文版 | English Version */}
        <Text style={styles.paragraph}>
          This is normal text,
          <Text style={styles.bold}>this part is bold, </Text>
          <Text style={styles.italic}>this part is italic, </Text>
          <Text style={styles.boldItalic}>this part is both.</Text>
        </Text>

        {/* 样式继承与覆盖演示 | Style Inheritance and Override Demo */}
        <Text style={styles.parentStyle}>
          父文本：蓝色、16px | Parent: blue, 16px
          <Text style={styles.childStyle1}>
            {' '}子文本1：继承蓝色，覆盖为粗体 | Child 1: inherits blue, overrides to bold
          </Text>
          <Text style={styles.childStyle2}>
            {' '}子文本2：覆盖为红色 | Child 2: overrides to red
          </Text>
        </Text>

        {/* 可点击的富文本 | Clickable Rich Text */}
        <Text style={styles.paragraph}>
          阅读并同意
          <Text
            style={styles.link}
            onPress={() => Alert.alert('用户协议', '打开用户协议页面 | Open User Agreement')}
          >
            《用户协议》
          </Text>
          和
          <Text
            style={styles.link}
            onPress={() => Alert.alert('隐私政策', '打开隐私政策页面 | Open Privacy Policy')}
          >
            《隐私政策》
          </Text>
        </Text>

        {/* 复杂样式组合 | Complex Style Combinations */}
        <Text style={styles.complexParent}>
          特价商品：
          <Text style={styles.originalPrice}>¥299</Text>
          {' '}
          <Text style={styles.currentPrice}>¥199</Text>
          {' '}
          <Text style={styles.discount}>限时8折！</Text>
        </Text>

        {/* 多级嵌套示例 | Multi-level Nesting Example */}
        <Text style={styles.level1}>
          第一级（大号黑色）| Level 1 (large black)
          <Text style={styles.level2}>
            {' '}第二级（继承大号，变蓝色）| Level 2 (inherits large, turns blue)
            <Text style={styles.level3}>
              {' '}第三级（继承大号蓝色，加粗）| Level 3 (inherits large blue, adds bold)
            </Text>
          </Text>
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      backgroundColor: '#fff',
    },
    paragraph: {
      fontSize: 16,
      lineHeight: 24,
      color: '#333',
      marginBottom: 15,
    },
    bold: {
      fontWeight: 'bold',
    },
    italic: {
      fontStyle: 'italic',
    },
    boldItalic: {
      fontWeight: 'bold',
      fontStyle: 'italic',
    },
    parentStyle: {
      fontSize: 16,
      color: 'blue',
      marginBottom: 15,
    },
    childStyle1: {
      fontWeight: 'bold', // 继承color: 'blue' | Inherits color: 'blue'
    },
    childStyle2: {
      color: 'red', // 覆盖父级的blue | Overrides parent's blue
    },
    link: {
      color: '#1890ff',
      textDecorationLine: 'underline',
      fontWeight: '500',
    },
    complexParent: {
      fontSize: 18,
      marginBottom: 15,
    },
    originalPrice: {
      fontSize: 16,
      color: '#999',
      textDecorationLine: 'line-through',
    },
    currentPrice: {
      fontSize: 24,
      color: '#ff4d4f',
      fontWeight: 'bold',
    },
    discount: {
      fontSize: 14,
      color: '#fff',
      backgroundColor: '#ff4d4f',
      paddingHorizontal: 8,
      paddingVertical: 2,
      borderRadius: 4,
    },
    level1: {
      fontSize: 20,
      color: '#000',
    },
    level2: {
      color: 'blue', // 继承fontSize: 20 | Inherits fontSize: 20
    },
    level3: {
      fontWeight: 'bold', // 继承fontSize: 20和color: 'blue' | Inherits fontSize: 20 and color: 'blue'
    },
  });

  export default RichTextExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - level3的Text最终会显示什么颜色和字号？| What color and font size will level3 Text ultimately display?
    **答案 | Answer:** 蓝色(blue)、20号字 - 从level2继承了color，从level1继承了fontSize | Blue, size 20 - inherits color from level2, fontSize from level1
  - 点击"用户协议"和"隐私政策"链接会发生什么？| What happens when clicking "User Agreement" and "Privacy Policy" links?
    **答案 | Answer:** 分别触发不同的Alert弹窗 - 每个Text有独立的onPress处理器 | Triggers different Alert dialogs - each Text has independent onPress handlers

  **常见误区检查 | Common Misconception Checks:**
  - 误区：嵌套Text会创建新的样式作用域，不会继承父样式 | Misconception: Nested Text creates new style scope, won't inherit parent styles
    **答案 | Answer:** 错误 | Wrong - 子Text会继承父Text的所有样式，除非显式覆盖 | Child Text inherits all parent Text styles unless explicitly overridden
  - 误区：只有直接子Text才会继承样式 | Misconception: Only direct child Text inherits styles
    **答案 | Answer:** 错误 | Wrong - 多级嵌套的Text会逐级继承所有祖先Text的样式 | Multi-level nested Text inherits styles from all ancestor Text components

### 4. 文本截断与行数限制 | Text Truncation and Line Limiting (40分钟 | 40 minutes)

- **核心概念：numberOfLines属性与文本溢出 | Core Concept: numberOfLines Property and Text Overflow**

  **概念定义 | Concept Definition:**
  numberOfLines属性用于限制Text组件显示的最大行数。当文本内容超过指定行数时，会被截断并显示省略号(...)。这在移动应用中非常常见，用于卡片预览、列表项摘要等场景。结合ellipsizeMode属性，可以控制省略号的显示位置（头部、中间、尾部）。

  The numberOfLines property limits the maximum number of lines displayed by a Text component. When text content exceeds the specified lines, it gets truncated with ellipsis (...). This is very common in mobile apps for card previews, list item summaries, etc. Combined with ellipsizeMode, you can control where the ellipsis appears (head, middle, tail).

  **核心特征 | Key Characteristics:**
  - numberOfLines设置最大显示行数，默认无限制 | numberOfLines sets max display lines, default is unlimited
  - 文本超出时自动添加省略号(...) | Automatically adds ellipsis (...) when text overflows
  - ellipsizeMode控制省略号位置：'head' | 'middle' | 'tail' | 'clip' | ellipsizeMode controls ellipsis position: 'head' | 'middle' | 'tail' | 'clip'
  - 需要设置numberOfLines才能使ellipsizeMode生效 | numberOfLines must be set for ellipsizeMode to work

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果只设置ellipsizeMode而不设置numberOfLines会有省略号吗？| Will there be an ellipsis if you only set ellipsizeMode without numberOfLines?
     **答案 | Answer:** 否 | No - ellipsizeMode需要numberOfLines配合使用 | ellipsizeMode requires numberOfLines to work
  2. numberOfLines={0}会显示多少行？| How many lines will numberOfLines={0} display?
     **答案 | Answer:** 不显示任何行 | No lines displayed - 文本完全隐藏 | Text completely hidden
  3. ellipsizeMode='middle'的省略号出现在哪里？| Where does the ellipsis appear with ellipsizeMode='middle'?
     **答案 | Answer:** 文本中间 | Middle of text - 保留开头和结尾，省略中间部分 | Preserves start and end, omits middle part
  4. 可以动态改变numberOfLines的值来展开/收起文本吗？| Can you dynamically change numberOfLines value to expand/collapse text?
     **答案 | Answer:** 可以 | Yes - 这是实现"查看更多"功能的常用方法 | This is a common method for implementing "see more" functionality

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import { View, Text, StyleSheet, Pressable } from 'react-native';

  function TextTruncationExample() {
    const [expanded, setExpanded] = useState(false);

    const longText = `这是一段很长的文本内容，用于演示numberOfLines属性的效果。在实际应用中，我们经常需要在列表或卡片中显示文章摘要、商品描述或用户评论。通过限制行数，可以保持界面整洁，同时提供"查看更多"的交互来展示完整内容。This is a very long text content used to demonstrate the effect of the numberOfLines property. In real applications, we often need to display article summaries, product descriptions, or user comments in lists or cards. By limiting the number of lines, we can keep the interface clean while providing a "see more" interaction to show the full content.`;

    return (
      <View style={styles.container}>
        {/* 单行截断示例 | Single Line Truncation */}
        <Text style={styles.label}>单行截断（默认tail）| Single Line (default tail):</Text>
        <Text numberOfLines={1} style={styles.text}>
          {longText}
        </Text>

        {/* 多行截断 | Multi-line Truncation */}
        <Text style={styles.label}>三行截断 | Three Lines:</Text>
        <Text numberOfLines={3} style={styles.text}>
          {longText}
        </Text>

        {/* 不同省略位置演示 | Different Ellipsis Positions */}
        <Text style={styles.label}>头部省略 ellipsizeMode='head':</Text>
        <Text numberOfLines={1} ellipsizeMode="head" style={styles.text}>
          {longText}
        </Text>

        <Text style={styles.label}>中间省略 ellipsizeMode='middle':</Text>
        <Text numberOfLines={1} ellipsizeMode="middle" style={styles.text}>
          {longText}
        </Text>

        <Text style={styles.label}>尾部省略 ellipsizeMode='tail' (默认 | default):</Text>
        <Text numberOfLines={1} ellipsizeMode="tail" style={styles.text}>
          {longText}
        </Text>

        <Text style={styles.label}>裁剪不显示省略号 ellipsizeMode='clip':</Text>
        <Text numberOfLines={1} ellipsizeMode="clip" style={styles.text}>
          {longText}
        </Text>

        {/* 可展开/收起的文本 | Expandable/Collapsible Text */}
        <View style={styles.expandableContainer}>
          <Text style={styles.label}>可展开文本 | Expandable Text:</Text>
          <Text
            numberOfLines={expanded ? undefined : 3}
            style={styles.text}
          >
            {longText}
          </Text>
          <Pressable onPress={() => setExpanded(!expanded)}>
            <Text style={styles.toggleButton}>
              {expanded ? '收起 | Collapse' : '查看更多 | See More'}
            </Text>
          </Pressable>
        </View>

        {/* 实际应用场景：商品卡片 | Real-world Scenario: Product Card */}
        <View style={styles.productCard}>
          <Text style={styles.productTitle} numberOfLines={2}>
            高端商务笔记本电脑 超轻薄设计 长续航 | Premium Business Laptop Ultra-thin Design Long Battery Life
          </Text>
          <Text style={styles.productPrice}>¥8,999</Text>
          <Text style={styles.productDesc} numberOfLines={3}>
            这款笔记本采用全金属机身，重量仅1.2kg，配备最新处理器和16GB内存，续航时间长达12小时，非常适合商务人士移动办公使用。
            This laptop features an all-metal body, weighs only 1.2kg, equipped with the latest processor and 16GB RAM, battery life up to 12 hours, perfect for business professionals on the go.
          </Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    label: {
      fontSize: 14,
      fontWeight: 'bold',
      color: '#666',
      marginTop: 15,
      marginBottom: 5,
    },
    text: {
      fontSize: 16,
      lineHeight: 24,
      color: '#333',
      backgroundColor: '#fff',
      padding: 10,
      borderRadius: 4,
    },
    expandableContainer: {
      marginTop: 20,
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
    },
    toggleButton: {
      fontSize: 14,
      color: '#1890ff',
      marginTop: 10,
      fontWeight: '500',
    },
    productCard: {
      marginTop: 20,
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 8,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    productTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#333',
      marginBottom: 10,
    },
    productPrice: {
      fontSize: 24,
      fontWeight: 'bold',
      color: '#ff4d4f',
      marginBottom: 10,
    },
    productDesc: {
      fontSize: 14,
      lineHeight: 20,
      color: '#666',
    },
  });

  export default TextTruncationExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 可展开文本中，expanded为false时显示多少行？| In expandable text, how many lines are shown when expanded is false?
    **答案 | Answer:** 3行 - numberOfLines={expanded ? undefined : 3} | 3 lines - numberOfLines={expanded ? undefined : 3}
  - ellipsizeMode='clip'和'tail'的区别是什么？| What's the difference between ellipsizeMode='clip' and 'tail'?
    **答案 | Answer:** 'clip'直接裁剪不显示省略号，'tail'在末尾显示... | 'clip' cuts directly without ellipsis, 'tail' shows ... at the end

  **常见误区检查 | Common Misconception Checks:**
  - 误区：numberOfLines会改变Text容器的高度 | Misconception: numberOfLines changes the Text container height
    **答案 | Answer:** 正确 | Correct - 限制行数确实会影响Text的渲染高度 | Limiting lines does affect Text rendering height
  - 误区：省略号会占用一个字符的位置 | Misconception: Ellipsis takes up one character's space
    **答案 | Answer:** 正确 | Correct - 省略号(...)会占用显示空间，因此截断点会提前 | Ellipsis (...) takes display space, so truncation point comes earlier

### 5. 系统字体与自定义字体 | System Fonts and Custom Fonts (30分钟 | 30 minutes)

- **核心概念：字体加载机制 | Core Concept: Font Loading Mechanism**

  **概念定义 | Concept Definition:**
  React Native默认使用系统字体（iOS的San Francisco、Android的Roboto）。当需要使用自定义字体时，需要通过Expo的expo-font库或React Native CLI的资源链接机制来加载字体文件。自定义字体需要在应用启动前加载完成，通常使用异步加载和loading状态管理。

  React Native uses system fonts by default (San Francisco on iOS, Roboto on Android). When custom fonts are needed, they must be loaded through Expo's expo-font library or React Native CLI's resource linking mechanism. Custom fonts need to be loaded before app launch, typically using async loading and loading state management.

  **核心特征 | Key Characteristics:**
  - 系统字体无需加载，开箱即用 | System fonts work out-of-box, no loading required
  - 自定义字体需要异步加载，加载完成前应显示loading状态 | Custom fonts require async loading, should show loading state before completion
  - 字体文件通常放在assets/fonts目录 | Font files are typically placed in assets/fonts directory
  - fontFamily属性值必须与加载的字体名称完全匹配 | fontFamily property value must exactly match loaded font name

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在Expo项目中使用系统字体需要安装expo-font吗？| Do you need to install expo-font to use system fonts in Expo projects?
     **答案 | Answer:** 否 | No - 系统字体默认可用，无需安装额外包 | System fonts are available by default, no extra packages needed
  2. 自定义字体加载是同步还是异步操作？| Is custom font loading synchronous or asynchronous?
     **答案 | Answer:** 异步 | Asynchronous - 需要等待字体文件下载和解析 | Requires waiting for font file download and parsing
  3. 如果在字体加载完成前使用自定义fontFamily会怎样？| What happens if you use custom fontFamily before font loading completes?
     **答案 | Answer:** 会回退到系统默认字体 | Falls back to system default font - 可能导致样式闪烁 | May cause style flashing

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState, useEffect } from 'react';
  import { View, Text, StyleSheet, ActivityIndicator } from 'react-native';
  // 注意：如果使用Expo，需要先安装：expo install expo-font
  // Note: If using Expo, install first: expo install expo-font
  import * as Font from 'expo-font';

  function CustomFontsExample() {
    const [fontsLoaded, setFontsLoaded] = useState(false);

    useEffect(() => {
      async function loadFonts() {
        try {
          // 加载自定义字体 | Load custom fonts
          await Font.loadAsync({
            // 字体名称: 字体文件路径 | Font name: font file path
            'Roboto-Regular': require('./assets/fonts/Roboto-Regular.ttf'),
            'Roboto-Bold': require('./assets/fonts/Roboto-Bold.ttf'),
            'OpenSans-Regular': require('./assets/fonts/OpenSans-Regular.ttf'),
          });
          setFontsLoaded(true);
        } catch (error) {
          console.error('字体加载失败 | Font loading failed:', error);
        }
      }

      loadFonts();
    }, []);

    // 字体加载完成前显示loading | Show loading before fonts are loaded
    if (!fontsLoaded) {
      return (
        <View style={styles.loadingContainer}>
          <ActivityIndicator size="large" color="#1890ff" />
          <Text style={styles.loadingText}>加载字体中... | Loading fonts...</Text>
        </View>
      );
    }

    return (
      <View style={styles.container}>
        {/* 系统默认字体 | System Default Font */}
        <Text style={styles.systemFont}>
          系统默认字体（iOS: San Francisco, Android: Roboto）
        </Text>
        <Text style={styles.systemFont}>
          System Default Font (iOS: San Francisco, Android: Roboto)
        </Text>

        {/* 自定义字体演示 | Custom Font Demo */}
        <Text style={styles.robotoRegular}>
          Roboto Regular 字体演示 | Roboto Regular Font Demo
        </Text>
        <Text style={styles.robotoBold}>
          Roboto Bold 字体演示 | Roboto Bold Font Demo
        </Text>
        <Text style={styles.openSans}>
          Open Sans 字体演示 | Open Sans Font Demo
        </Text>

        {/* 字体回退示例 | Font Fallback Example */}
        <Text style={styles.withFallback}>
          这段文本使用了fontFamily并设置了回退字体
        </Text>
        <Text style={styles.withFallback}>
          This text uses fontFamily with fallback fonts
        </Text>

        {/* 跨平台字体差异说明 | Cross-platform Font Differences */}
        <View style={styles.infoBox}>
          <Text style={styles.infoTitle}>跨平台注意事项 | Cross-platform Notes:</Text>
          <Text style={styles.infoText}>
            • iOS和Android的默认字体不同{'\n'}
            • iOS and Android have different default fonts{'\n\n'}
            • 自定义字体可保证跨平台一致性{'\n'}
            • Custom fonts ensure cross-platform consistency{'\n\n'}
            • 字体文件会增加应用体积{'\n'}
            • Font files increase app size
          </Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    loadingContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#fff',
    },
    loadingText: {
      marginTop: 10,
      fontSize: 16,
      color: '#666',
    },
    container: {
      padding: 20,
      backgroundColor: '#fff',
    },
    systemFont: {
      fontSize: 16,
      marginBottom: 10,
      color: '#333',
    },
    robotoRegular: {
      fontFamily: 'Roboto-Regular',
      fontSize: 18,
      marginTop: 20,
      marginBottom: 10,
      color: '#333',
    },
    robotoBold: {
      fontFamily: 'Roboto-Bold',
      fontSize: 18,
      marginBottom: 10,
      color: '#333',
    },
    openSans: {
      fontFamily: 'OpenSans-Regular',
      fontSize: 18,
      marginBottom: 10,
      color: '#333',
    },
    withFallback: {
      // 如果OpenSans-Regular加载失败，会回退到系统字体
      // If OpenSans-Regular fails to load, falls back to system font
      fontFamily: 'OpenSans-Regular',
      fontSize: 16,
      marginTop: 20,
      marginBottom: 5,
      color: '#666',
    },
    infoBox: {
      marginTop: 20,
      padding: 15,
      backgroundColor: '#e6f7ff',
      borderLeftWidth: 4,
      borderLeftColor: '#1890ff',
      borderRadius: 4,
    },
    infoTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#0050b3',
      marginBottom: 10,
    },
    infoText: {
      fontSize: 14,
      lineHeight: 22,
      color: '#595959',
    },
  });

  export default CustomFontsExample;

  // ============================================
  // 配置文件示例：expo项目需要在app.json中注册字体
  // Config file example: Expo projects need to register fonts in app.json
  // ============================================
  /*
  {
    "expo": {
      "name": "YourAppName",
      "slug": "your-app-slug",
      "version": "1.0.0",
      ...
      // 如果使用Expo的资源加载机制（不使用expo-font代码加载）
      // If using Expo's asset loading mechanism (not expo-font code loading)
      "assetBundlePatterns": [
        "assets/fonts/*"
      ]
    }
  }
  */
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果不添加fontsLoaded检查就直接使用自定义字体会怎样？| What happens if you use custom fonts without fontsLoaded check?
    **答案 | Answer:** 会短暂显示系统字体，然后切换到自定义字体，造成视觉闪烁 | Briefly shows system font then switches to custom font, causing visual flashing
  - 自定义字体文件应该放在项目的哪个目录？| In which directory should custom font files be placed?
    **答案 | Answer:** 通常放在assets/fonts/目录下 | Usually in assets/fonts/ directory

  **常见误区检查 | Common Misconception Checks:**
  - 误区：fontFamily可以像CSS一样设置字体栈（多个回退字体）| Misconception: fontFamily can set font stack like CSS (multiple fallback fonts)
    **答案 | Answer:** 部分正确 | Partially correct - React Native的fontFamily只接受单个字体名，但未加载时会自动回退到系统字体 | React Native fontFamily only accepts single font name, but auto-falls back to system font when not loaded

### 6. 知识巩固与最佳实践 | Knowledge Consolidation and Best Practices (20分钟 | 20 minutes)

- **Text组件最佳实践总结 | Text Component Best Practices Summary**

  **关键原则 | Key Principles:**
  - 所有文本必须用Text包裹 - 这是React Native的强制规则 | All text must be wrapped in Text - this is React Native's mandatory rule
  - 使用StyleSheet创建可复用的文本样式而非内联样式 | Use StyleSheet to create reusable text styles instead of inline styles
  - 为长文本设置numberOfLines避免布局溢出 | Set numberOfLines for long text to avoid layout overflow
  - 自定义字体需要妥善处理加载状态 | Custom fonts need proper loading state handling
  - 利用嵌套Text实现富文本而非多个Text组件 | Use nested Text for rich text instead of multiple Text components

  **综合概念检查 | Comprehensive Concept Check:**
  1. 为什么React Native强制要求文本必须用Text包裹？| Why does React Native mandate text must be wrapped in Text?
     **答案 | Answer:** 移动平台的文本渲染机制与Web不同，需要专门的组件处理字体、布局和触摸事件 | Mobile platform text rendering differs from web, requires specialized component for fonts, layout, and touch events
  2. 什么时候应该使用嵌套Text而不是并列多个Text组件？| When should you use nested Text instead of multiple parallel Text components?
     **答案 | Answer:** 当需要在同一段落中应用不同样式，且希望这些文本作为整体流动布局时 | When different styles are needed in the same paragraph and you want text to flow as a whole
  3. numberOfLines和lineHeight有什么区别？| What's the difference between numberOfLines and lineHeight?
     **答案 | Answer:** numberOfLines限制显示行数，lineHeight设置行高（行间距）| numberOfLines limits displayed lines, lineHeight sets line height (line spacing)
  4. 如何实现"查看更多"展开文本的功能？| How to implement "see more" expandable text functionality?
     **答案 | Answer:** 使用state控制numberOfLines的值，在undefined（无限制）和特定数字之间切换 | Use state to control numberOfLines value, toggle between undefined (unlimited) and specific number
  5. 自定义字体的fontFamily值应该写什么？| What should the fontFamily value be for custom fonts?
     **答案 | Answer:** 必须与Font.loadAsync中定义的字体名称完全匹配 | Must exactly match the font name defined in Font.loadAsync

## 实践项目：个人简历卡片 | Practical Project: Personal Resume Card

### 目标 | Objective
构建一个美观的个人简历卡片组件，综合应用Text组件的各种样式、嵌套、截断等特性，模拟真实移动应用中的用户信息展示场景。

Build an elegant personal resume card component that comprehensively applies various Text component features including styling, nesting, truncation, simulating real mobile app user information display scenarios.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 所有文本内容必须包裹在Text组件中吗？| Must all text content be wrapped in Text components?
   **答案 | Answer:** 是的，这是React Native的强制要求，否则会报错 | Yes, this is React Native's mandatory requirement, otherwise errors occur
2. 如何在同一行文本中显示不同颜色和字重的文字？| How to display text with different colors and weights in the same line?
   **答案 | Answer:** 使用嵌套Text组件，子Text继承父Text样式但可以覆盖特定属性 | Use nested Text components, child Text inherits parent styles but can override specific properties
3. 如何限制个人简介只显示3行，超出部分显示省略号？| How to limit personal bio to 3 lines with ellipsis for overflow?
   **答案 | Answer:** 使用numberOfLines={3}属性 | Use numberOfLines={3} property

### 步骤 | Steps
1. 创建基础卡片布局，使用View作为容器，SafeAreaView确保安全区域显示 | Create basic card layout, use View as container, SafeAreaView ensures safe area display
2. 实现头部区域：姓名（大号粗体）+ 职位（中号常规字重）| Implement header section: name (large bold) + position (medium regular weight)
3. 添加联系方式区域：使用嵌套Text显示图标和信息（邮箱、电话、地址）| Add contact info section: use nested Text to display icons and info (email, phone, address)
4. 实现技能标签：使用Text组件创建带背景色的标签效果 | Implement skill tags: use Text components to create tags with background colors
5. 添加个人简介：使用numberOfLines={3}限制行数，配合"展开/收起"按钮 | Add personal bio: use numberOfLines={3} to limit lines, with "expand/collapse" button
6. 完善样式：添加阴影、圆角、间距等视觉细节 | Refine styles: add shadows, border radius, spacing and other visual details

### 示例代码 | Example Code
```jsx
import React, { useState } from 'react';
import {
  SafeAreaView,
  View,
  Text,
  StyleSheet,
  Pressable,
  ScrollView
} from 'react-native';

/**
 * 个人简历卡片组件 | Personal Resume Card Component
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Text组件的必要性和基本使用 | Text component necessity and basic usage
 * - 文本样式属性（fontSize, fontWeight, color, textAlign）| Text style properties (fontSize, fontWeight, color, textAlign)
 * - 嵌套Text实现富文本效果 | Nested Text for rich text effects
 * - numberOfLines实现文本截断 | numberOfLines for text truncation
 * - 交互式文本展开/收起 | Interactive text expand/collapse
 */
function ResumeCard() {
  const [bioExpanded, setBioExpanded] = useState(false);

  // 个人简介长文本 | Personal bio long text
  const bioText = `资深前端工程师，专注于React Native移动应用开发，拥有5年+跨平台开发经验。熟练掌握JavaScript/TypeScript、React生态系统，擅长构建高性能、用户体验优秀的移动应用。曾主导多个千万级用户量的App项目，对移动端性能优化、组件化架构设计有深入理解。热爱技术分享，活跃于开源社区。

Senior Frontend Engineer specializing in React Native mobile app development with 5+ years of cross-platform development experience. Proficient in JavaScript/TypeScript and React ecosystem, expert in building high-performance, user-friendly mobile applications. Led multiple apps with 10M+ users, deep understanding of mobile performance optimization and component architecture design. Passionate about tech sharing and active in open-source community.`;

  return (
    <SafeAreaView style={styles.safeArea}>
      <ScrollView style={styles.scrollView}>
        <View style={styles.card}>
          {/* 头部：姓名和职位 | Header: Name and Position */}
          <View style={styles.header}>
            <Text style={styles.name}>张伟 | Zhang Wei</Text>
            <Text style={styles.position}>
              高级React Native工程师 | Senior React Native Engineer
            </Text>
          </View>

          {/* 联系方式 | Contact Information */}
          <View style={styles.contactSection}>
            <Text style={styles.sectionTitle}>联系方式 | Contact</Text>

            {/* 邮箱 - 使用嵌套Text实现图标+文本效果 | Email - nested Text for icon+text */}
            <Text style={styles.contactItem}>
              <Text style={styles.contactIcon}>✉️ </Text>
              <Text style={styles.contactText}>zhangwei@example.com</Text>
            </Text>

            {/* 电话 | Phone */}
            <Text style={styles.contactItem}>
              <Text style={styles.contactIcon}>📱 </Text>
              <Text style={styles.contactText}>+86 138-0000-0000</Text>
            </Text>

            {/* 地址 | Address */}
            <Text style={styles.contactItem}>
              <Text style={styles.contactIcon}>📍 </Text>
              <Text style={styles.contactText}>北京市朝阳区 | Beijing, Chaoyang District</Text>
            </Text>

            {/* GitHub - 可点击链接效果 | GitHub - clickable link effect */}
            <Text style={styles.contactItem}>
              <Text style={styles.contactIcon}>🔗 </Text>
              <Text
                style={styles.contactLink}
                onPress={() => console.log('打开GitHub链接 | Open GitHub link')}
              >
                github.com/zhangwei
              </Text>
            </Text>
          </View>

          {/* 核心技能标签 | Core Skills Tags */}
          <View style={styles.skillsSection}>
            <Text style={styles.sectionTitle}>核心技能 | Core Skills</Text>
            <View style={styles.tagsContainer}>
              {/* 使用Text组件创建标签效果 | Use Text components to create tag effects */}
              <Text style={[styles.tag, styles.tagPrimary]}>React Native</Text>
              <Text style={[styles.tag, styles.tagPrimary]}>TypeScript</Text>
              <Text style={[styles.tag, styles.tagSecondary]}>Redux</Text>
              <Text style={[styles.tag, styles.tagSecondary]}>React Hooks</Text>
              <Text style={[styles.tag, styles.tagSecondary]}>Expo</Text>
              <Text style={[styles.tag, styles.tagTertiary]}>性能优化 | Performance</Text>
              <Text style={[styles.tag, styles.tagTertiary]}>组件化 | Components</Text>
            </View>
          </View>

          {/* 个人简介 - 使用numberOfLines和可展开功能 | Bio - with numberOfLines and expandable */}
          <View style={styles.bioSection}>
            <Text style={styles.sectionTitle}>个人简介 | About Me</Text>
            <Text
              style={styles.bioText}
              numberOfLines={bioExpanded ? undefined : 3}
            >
              {bioText}
            </Text>
            <Pressable
              onPress={() => setBioExpanded(!bioExpanded)}
              style={styles.expandButton}
            >
              <Text style={styles.expandButtonText}>
                {bioExpanded
                  ? '收起 ▲ | Collapse ▲'
                  : '查看更多 ▼ | See More ▼'}
              </Text>
            </Pressable>
          </View>

          {/* 工作经历摘要 | Work Experience Summary */}
          <View style={styles.experienceSection}>
            <Text style={styles.sectionTitle}>工作经历 | Experience</Text>

            <View style={styles.experienceItem}>
              <Text style={styles.companyName}>
                科技有限公司 | Tech Co., Ltd
              </Text>
              <Text style={styles.experienceRole}>
                高级前端工程师 | Senior Frontend Engineer
                <Text style={styles.experienceDate}> (2020 - 至今 | Present)</Text>
              </Text>
              <Text style={styles.experienceDesc} numberOfLines={2}>
                负责公司核心移动应用的架构设计与开发，主导React Native技术栈迁移，性能提升40%。
                Responsible for core mobile app architecture and development, led React Native tech stack migration, 40% performance improvement.
              </Text>
            </View>

            <View style={styles.experienceItem}>
              <Text style={styles.companyName}>
                互联网公司 | Internet Co.
              </Text>
              <Text style={styles.experienceRole}>
                前端工程师 | Frontend Engineer
                <Text style={styles.experienceDate}> (2018 - 2020)</Text>
              </Text>
              <Text style={styles.experienceDesc} numberOfLines={2}>
                参与多个跨平台移动项目开发，积累丰富的React Native实战经验。
                Participated in multiple cross-platform mobile projects, accumulated rich React Native practical experience.
              </Text>
            </View>
          </View>

          {/* 底部签名 - 演示多样式嵌套Text | Footer Signature - multi-style nested Text */}
          <View style={styles.footer}>
            <Text style={styles.footerText}>
              <Text style={styles.footerLabel}>更新时间 | Updated: </Text>
              <Text style={styles.footerValue}>2025年1月 | Jan 2025</Text>
              {' • '}
              <Text style={styles.footerLabel}>版本 | Version: </Text>
              <Text style={styles.footerValue}>2.0</Text>
            </Text>
          </View>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  scrollView: {
    flex: 1,
  },
  card: {
    margin: 20,
    backgroundColor: '#ffffff',
    borderRadius: 16,
    padding: 20,
    // iOS阴影 | iOS shadow
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.1,
    shadowRadius: 8,
    // Android阴影 | Android shadow
    elevation: 5,
  },

  // 头部样式 | Header Styles
  header: {
    borderBottomWidth: 2,
    borderBottomColor: '#1890ff',
    paddingBottom: 15,
    marginBottom: 20,
  },
  name: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#1890ff',
    marginBottom: 8,
  },
  position: {
    fontSize: 18,
    fontWeight: '500',
    color: '#595959',
  },

  // 区块标题 | Section Title
  sectionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#262626',
    marginBottom: 12,
    marginTop: 20,
  },

  // 联系方式样式 | Contact Styles
  contactSection: {
    marginBottom: 10,
  },
  contactItem: {
    fontSize: 15,
    lineHeight: 28,
    color: '#595959',
  },
  contactIcon: {
    fontSize: 16,
  },
  contactText: {
    fontSize: 15,
    color: '#595959',
  },
  contactLink: {
    fontSize: 15,
    color: '#1890ff',
    textDecorationLine: 'underline',
  },

  // 技能标签样式 | Skills Tag Styles
  skillsSection: {
    marginBottom: 10,
  },
  tagsContainer: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    gap: 8,
  },
  tag: {
    fontSize: 14,
    paddingHorizontal: 12,
    paddingVertical: 6,
    borderRadius: 16,
    overflow: 'hidden',
    fontWeight: '500',
  },
  tagPrimary: {
    backgroundColor: '#1890ff',
    color: '#ffffff',
  },
  tagSecondary: {
    backgroundColor: '#e6f7ff',
    color: '#0050b3',
  },
  tagTertiary: {
    backgroundColor: '#f0f0f0',
    color: '#595959',
  },

  // 个人简介样式 | Bio Styles
  bioSection: {
    marginBottom: 10,
  },
  bioText: {
    fontSize: 15,
    lineHeight: 24,
    color: '#595959',
    textAlign: 'justify',
  },
  expandButton: {
    marginTop: 10,
    alignSelf: 'flex-start',
  },
  expandButtonText: {
    fontSize: 14,
    color: '#1890ff',
    fontWeight: '500',
  },

  // 工作经历样式 | Experience Styles
  experienceSection: {
    marginBottom: 10,
  },
  experienceItem: {
    marginBottom: 15,
    paddingLeft: 15,
    borderLeftWidth: 3,
    borderLeftColor: '#1890ff',
  },
  companyName: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#262626',
    marginBottom: 4,
  },
  experienceRole: {
    fontSize: 15,
    color: '#595959',
    marginBottom: 6,
  },
  experienceDate: {
    fontSize: 14,
    color: '#8c8c8c',
    fontWeight: 'normal',
  },
  experienceDesc: {
    fontSize: 14,
    lineHeight: 20,
    color: '#8c8c8c',
  },

  // 底部样式 | Footer Styles
  footer: {
    marginTop: 20,
    paddingTop: 15,
    borderTopWidth: 1,
    borderTopColor: '#f0f0f0',
  },
  footerText: {
    fontSize: 13,
    textAlign: 'center',
    color: '#8c8c8c',
  },
  footerLabel: {
    color: '#bfbfbf',
  },
  footerValue: {
    color: '#595959',
    fontWeight: '500',
  },
});

export default ResumeCard;
```

### 项目完成检查 | Project Completion Check
1. 所有文本内容是否都正确包裹在Text组件中？| Is all text content correctly wrapped in Text components?
2. 嵌套Text是否正确实现了样式继承（如联系方式的图标+文本）？| Does nested Text correctly implement style inheritance (like icon+text in contact info)?
3. 个人简介的numberOfLines功能是否正常工作？| Does the numberOfLines feature in personal bio work correctly?
4. 技能标签是否使用Text组件实现了不同的背景色和文字色？| Do skill tags use Text components with different background and text colors?
5. 代码是否遵循了最佳实践（使用StyleSheet而非内联样式）？| Does the code follow best practices (using StyleSheet instead of inline styles)?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Text组件约束理解练习 | Text Component Constraint Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建一个故意违反Text规则的示例，观察并记录错误信息，然后修复它
   - **概念检查 | Concept Check:** 为什么React Native要求所有文本必须用Text包裹？
   - **学习目标 | Learning Objective:** 深入理解移动端文本渲染的底层机制

2. **样式继承实验练习 | Style Inheritance Experiment Exercise**
   - **练习描述 | Exercise Description:** 创建4层嵌套的Text组件，每层添加不同样式，观察最内层Text的最终样式
   - **概念检查 | Concept Check:** 嵌套Text的样式继承规则是什么？
   - **学习目标 | Learning Objective:** 掌握Text组件的样式继承和覆盖机制

3. **排版系统构建练习 | Typography System Building Exercise**
   - **练习描述 | Exercise Description:** 创建一套完整的排版系统（Heading1-6, Body, Caption, Label等），使用StyleSheet集中管理
   - **概念检查 | Concept Check:** 如何创建可复用的文本样式？
   - **学习目标 | Learning Objective:** 建立系统化的样式管理思维

4. **文本截断场景练习 | Text Truncation Scenario Exercise**
   - **练习描述 | Exercise Description:** 实现一个新闻列表，每条新闻标题最多2行，摘要最多3行，带省略号
   - **概念检查 | Concept Check:** numberOfLines和ellipsizeMode如何配合使用？
   - **学习目标 | Learning Objective:** 掌握移动端常见的文本截断模式

5. **交互式文本练习 | Interactive Text Exercise**
   - **练习描述 | Exercise Description:** 创建一个"服务条款"页面，关键词可点击跳转到详细说明，使用嵌套Text实现
   - **概念检查 | Concept Check:** 嵌套Text的onPress事件如何独立工作？
   - **学习目标 | Learning Objective:** 学会创建复杂的交互式文本组件

6. **跨平台字体适配练习 | Cross-platform Font Adaptation Exercise**
   - **练习描述 | Exercise Description:** 研究iOS和Android的默认字体差异，使用Platform.select实现平台特定字体
   - **概念检查 | Concept Check:** 如何处理跨平台字体显示差异？
   - **学习目标 | Learning Objective:** 理解跨平台开发中的字体一致性问题

7. **自定义字体加载练习 | Custom Font Loading Exercise**
   - **练习描述 | Exercise Description:** 下载并集成Google Fonts中的2-3款字体到项目，实现字体切换功能
   - **概念检查 | Concept Check:** 自定义字体的加载流程是什么？
   - **学习目标 | Learning Objective:** 掌握完整的自定义字体集成流程

## 学习资源 | Learning Resources
- [React Native Text官方文档 | Official Text Documentation](https://reactnative.dev/docs/text)
- [React Native样式和布局 | Styling and Layout](https://reactnative.dev/docs/style)
- [Expo Font文档 | Expo Font Docs](https://docs.expo.dev/versions/latest/sdk/font/)
- [Google Fonts](https://fonts.google.com/) - 免费字体资源 | Free font resources
- [Typography.js](http://kyleamathews.github.io/typography.js/) - 排版设计灵感 | Typography design inspiration

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Text组件是React Native中渲染文本的唯一方式 | Understand Text is the only way to render text in React Native
- [ ] 掌握fontSize、fontWeight、color、textAlign等核心样式属性 | Master core style properties: fontSize, fontWeight, color, textAlign
- [ ] 能够使用嵌套Text实现富文本效果 | Can use nested Text to achieve rich text effects
- [ ] 理解Text样式继承和覆盖规则 | Understand Text style inheritance and override rules
- [ ] 熟练使用numberOfLines和ellipsizeMode控制文本截断 | Proficient in using numberOfLines and ellipsizeMode for text truncation
- [ ] 能够实现可展开/收起的长文本组件 | Can implement expandable/collapsible long text components
- [ ] 理解系统字体和自定义字体的区别 | Understand difference between system fonts and custom fonts
- [ ] 掌握自定义字体的异步加载方法 | Master async loading method for custom fonts
- [ ] 能够创建可复用的排版样式系统 | Can create reusable typography style system
- [ ] 完成个人简历卡片实践项目 | Complete personal resume card practical project

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释每个核心概念。特别要能解释：为什么React Native强制要求Text包裹文本？嵌套Text的样式继承机制是什么？如何正确使用numberOfLines？

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain each core concept to others. Specifically, be able to explain: Why does React Native mandate Text wrapping for text? What is the style inheritance mechanism of nested Text? How to correctly use numberOfLines?
