# React Native入门 - 第8天：Views和Containers | React Native Introduction - Day 8: Views and Containers

## 学习目标 | Learning Objectives
- 理解View组件作为React Native中基础布局容器的核心作用 | Understand View component as the fundamental layout container in React Native
- 掌握View、ScrollView和SafeAreaView的使用场景和区别 | Master the use cases and differences between View, ScrollView, and SafeAreaView
- 学会使用style属性创建边框、阴影和背景色 | Learn to create borders, shadows, and background colors using style props
- 理解移动端UI原语与Web HTML元素的思维转换 | Understand the mental shift from Web HTML elements to mobile UI primitives
- 掌握嵌套View布局来可视化组件树结构 | Master nested View layouts to visualize component tree structure
- 了解overflow属性在React Native中的特殊行为 | Learn the special behavior of overflow property in React Native

## 详细内容 | Detailed Content

### 1. View组件基础 | View Component Fundamentals (1.5小时 | 1.5 hours)

- **View组件的核心概念 | Core Concept of View Component**

  **概念定义 | Concept Definition:**
  View是React Native中最基础的UI构建块，类似于Web中的div，但它专为移动端UI设计。View是一个支持Flexbox布局、样式、触摸事件处理和无障碍功能的容器组件。它不会渲染任何可见内容，除非你通过style属性添加样式。

  View is the most fundamental UI building block in React Native, similar to div in Web, but specifically designed for mobile UI. View is a container component that supports Flexbox layout, styling, touch event handling, and accessibility features. It renders nothing visible unless you add styles through the style prop.

  **核心特征 | Key Characteristics:**
  - View必须用于包裹所有其他组件（除了Text可以嵌套Text） | View must be used to wrap all other components (except Text can nest Text)
  - 默认使用Flexbox布局，且flexDirection默认为'column'（与Web的'row'不同） | Uses Flexbox layout by default, with flexDirection defaulting to 'column' (different from Web's 'row')
  - View不能直接包含文本，文本必须用Text组件包裹 | View cannot directly contain text; text must be wrapped in Text component
  - 支持触摸事件但本身不提供视觉反馈（需要Pressable等组件） | Supports touch events but doesn't provide visual feedback itself (requires Pressable, etc.)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. View组件可以直接包含文本字符串吗？| Can View component directly contain text strings?
     **答案 | Answer:** 否 | No - 所有文本必须包裹在Text组件中，否则会报错 | All text must be wrapped in Text component, otherwise it will throw an error
  2. React Native中View的默认flexDirection是什么？| What is the default flexDirection of View in React Native?
     **答案 | Answer:** 'column' - 与Web的'row'不同，这是移动端UI的常见布局方式 | 'column' - Different from Web's 'row', this is the common layout pattern for mobile UI
  3. View组件和Web的div元素最大的区别是什么？| What's the biggest difference between View component and Web's div element?
     **答案 | Answer:** View专为移动触摸界面设计，默认Flexbox布局，不支持直接文本内容 | View is designed for mobile touch interfaces, has Flexbox layout by default, and doesn't support direct text content
  4. 一个空的View组件（没有style）会在屏幕上显示什么？| What will an empty View component (without style) display on screen?
     **答案 | Answer:** 什么都不显示 | Nothing - View本身没有默认样式或可见内容 | View itself has no default style or visible content

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Text, StyleSheet } from 'react-native';

  // 基础View示例 | Basic View Example
  export default function App() {
    return (
      <View style={styles.container}>
        {/* View必须包含其他组件 | View must contain other components */}
        <View style={styles.box}>
          <Text>这是一个View容器 | This is a View container</Text>
        </View>

        {/* 错误示例（会报错）| Wrong Example (will error) */}
        {/* <View>直接写文本会报错 | Direct text will error</View> */}

        {/* 正确做法 | Correct way */}
        <View style={styles.box}>
          <Text>文本必须在Text组件中 | Text must be in Text component</Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1, // 占据整个屏幕 | Take up entire screen
      // 注意：默认flexDirection是'column' | Note: default flexDirection is 'column'
    },
    box: {
      padding: 20,
      margin: 10,
      backgroundColor: '#e0e0e0',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果去掉上面代码中的Text组件，直接在View中写文字会发生什么？| What happens if you remove the Text component and write text directly in View?
    **答案 | Answer:** 应用会崩溃并显示错误："Text strings must be rendered within a <Text> component" | App will crash with error: "Text strings must be rendered within a <Text> component"
  - styles.container中的flex: 1是什么意思？| What does flex: 1 in styles.container mean?
    **答案 | Answer:** 让容器占据所有可用空间（整个屏幕高度），这是移动端全屏布局的常用做法 | Makes the container take up all available space (entire screen height), common pattern for full-screen mobile layouts

  **常见误区检查 | Common Misconception Checks:**
  - Web开发者常认为View就是div的直接替代品，这对吗？| Web developers often think View is a direct replacement for div, is this correct?
    **答案 | Answer:** 不完全对 | Not entirely - View有移动端特定的约束（如不能直接包含文本）和默认行为（flexDirection: 'column'），需要转变思维方式 | View has mobile-specific constraints (like not containing direct text) and default behaviors (flexDirection: 'column'), requiring a mental shift

- **嵌套View与组件树可视化 | Nested Views and Component Tree Visualization**

  **概念定义 | Concept Definition:**
  嵌套View是通过在一个View组件内部放置另一个View组件来创建层次结构。通过给不同层级的View设置不同的背景色、边距和内边距，可以清晰地可视化组件树的结构，这是理解React Native布局的关键技巧。

  Nested Views are created by placing one View component inside another to create hierarchy. By setting different background colors, margins, and padding for Views at different levels, you can clearly visualize the component tree structure, which is a key technique for understanding React Native layouts.

  **核心特征 | Key Characteristics:**
  - 每一层View都可以有自己的样式和布局属性 | Each View layer can have its own style and layout properties
  - 子View继承父View的Flexbox上下文 | Child Views inherit the Flexbox context from parent View
  - margin创建View之间的外部空间，padding创建View内部空间 | margin creates external space between Views, padding creates internal space within View
  - 背景色不会影响子组件，只影响当前View的可视区域 | Background color doesn't affect children, only affects current View's visible area

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 当一个View嵌套在另一个View中时，子View的位置由什么决定？| When a View is nested in another View, what determines the child View's position?
     **答案 | Answer:** 由父View的Flexbox布局属性（如flexDirection, justifyContent, alignItems）和子View自身的margin决定 | Determined by parent View's Flexbox layout properties (like flexDirection, justifyContent, alignItems) and child View's own margin
  2. padding和margin的区别是什么？| What's the difference between padding and margin?
     **答案 | Answer:** padding是内边距（View边界到内容的距离），margin是外边距（View边界到其他组件的距离） | padding is internal spacing (distance from View border to content), margin is external spacing (distance from View border to other components)
  3. 如果父View设置了backgroundColor，子View没有设置，子View会显示什么颜色？| If parent View has backgroundColor set and child View doesn't, what color will child View display?
     **答案 | Answer:** 透明（可以看到父View的背景色） | Transparent (you can see through to parent View's background color)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Text, StyleSheet } from 'react-native';

  // 嵌套View可视化组件树 | Nested Views to visualize component tree
  export default function NestedViewsExample() {
    return (
      <View style={styles.level1}>
        <Text>Level 1 - 最外层 | Outermost</Text>

        <View style={styles.level2}>
          <Text>Level 2 - 第二层 | Second level</Text>

          <View style={styles.level3}>
            <Text>Level 3 - 第三层 | Third level</Text>

            <View style={styles.level4}>
              <Text>Level 4 - 最内层 | Innermost</Text>
            </View>
          </View>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    level1: {
      backgroundColor: '#ffebee', // 浅红色 | Light red
      padding: 20, // 内边距让子View不贴边 | Padding keeps children from edges
      margin: 10,
    },
    level2: {
      backgroundColor: '#e3f2fd', // 浅蓝色 | Light blue
      padding: 15,
      margin: 10, // 距离父View的margin | Margin from parent View
    },
    level3: {
      backgroundColor: '#f3e5f5', // 浅紫色 | Light purple
      padding: 10,
      margin: 10,
    },
    level4: {
      backgroundColor: '#e8f5e9', // 浅绿色 | Light green
      padding: 10,
      margin: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果将level2的margin改为0会发生什么视觉变化？| What visual change occurs if you change level2's margin to 0?
    **答案 | Answer:** level2的蓝色区域会紧贴level1的红色边界，不再有间隔 | level2's blue area will touch level1's red border with no gap
  - padding和margin哪个会增加组件本身的可点击/触摸区域？| Which one increases the component's own clickable/touchable area - padding or margin?
    **答案 | Answer:** padding - padding是组件内部空间，包含在触摸区域内；margin是外部空间，不包含在触摸区域内 | padding - padding is internal space, included in touch area; margin is external space, not included in touch area

### 2. SafeAreaView - 处理设备特殊区域 | SafeAreaView - Handling Device-Specific Areas (1小时 | 1 hour)

- **SafeAreaView的核心概念 | Core Concept of SafeAreaView**

  **概念定义 | Concept Definition:**
  SafeAreaView是一个专门用于处理设备安全区域的容器组件。它会自动添加padding以避免内容被刘海屏（notch）、状态栏、Home Indicator等设备特殊区域遮挡。SafeAreaView确保你的内容始终显示在可见和可交互的屏幕区域内。

  SafeAreaView is a container component specifically designed to handle device safe areas. It automatically adds padding to prevent content from being obscured by device-specific areas like notches, status bars, and Home Indicators. SafeAreaView ensures your content is always displayed in the visible and interactive screen area.

  **核心特征 | Key Characteristics:**
  - 仅在iOS 11+设备上有实际效果，Android设备上行为类似普通View | Only has actual effect on iOS 11+ devices, behaves like regular View on Android
  - 自动检测设备的安全区域并添加相应的内边距 | Automatically detects device safe areas and adds corresponding padding
  - 通常用作应用的根容器或屏幕的顶层容器 | Usually used as app's root container or screen's top-level container
  - 来自react-native核心库，但推荐使用react-native-safe-area-context库获得更好的跨平台支持 | From react-native core library, but react-native-safe-area-context is recommended for better cross-platform support

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. SafeAreaView主要解决什么问题？| What problem does SafeAreaView primarily solve?
     **答案 | Answer:** 防止内容被iPhone刘海屏、Home Indicator、Android状态栏等设备特殊区域遮挡 | Prevents content from being obscured by iPhone notches, Home Indicators, Android status bars, and other device-specific areas
  2. SafeAreaView在Android设备上有用吗？| Is SafeAreaView useful on Android devices?
     **答案 | Answer:** 原生SafeAreaView在Android上效果有限，主要针对iOS；推荐使用react-native-safe-area-context获得更好的Android支持 | Native SafeAreaView has limited effect on Android, mainly for iOS; react-native-safe-area-context is recommended for better Android support
  3. 应该把SafeAreaView放在组件树的什么位置？| Where should SafeAreaView be placed in the component tree?
     **答案 | Answer:** 通常作为最外层容器（根容器），确保所有内容都在安全区域内 | Usually as the outermost container (root container) to ensure all content is within safe area
  4. SafeAreaView和普通View的区别是什么？| What's the difference between SafeAreaView and regular View?
     **答案 | Answer:** SafeAreaView会自动添加padding以适应设备安全区域，而View不会 | SafeAreaView automatically adds padding to adapt to device safe areas, while View doesn't

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { SafeAreaView, View, Text, StyleSheet } from 'react-native';

  // SafeAreaView vs View 对比 | SafeAreaView vs View Comparison
  export default function SafeAreaExample() {
    return (
      // 使用SafeAreaView作为根容器 | Use SafeAreaView as root container
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.header}>
          <Text style={styles.headerText}>
            这个内容不会被刘海屏遮挡 | This content won't be obscured by notch
          </Text>
        </View>

        <View style={styles.content}>
          <Text>主要内容区域 | Main content area</Text>
        </View>

        <View style={styles.footer}>
          <Text>
            这个内容不会被Home Indicator遮挡 | This won't be obscured by Home Indicator
          </Text>
        </View>
      </SafeAreaView>
    );
  }

  // 错误示例：不使用SafeAreaView | Wrong Example: Without SafeAreaView
  function UnsafeExample() {
    return (
      <View style={styles.safeArea}> {/* 普通View无法处理安全区域 | Regular View can't handle safe areas */}
        <View style={styles.header}>
          <Text style={styles.headerText}>
            在iPhone X上这可能被刘海遮挡 | This might be obscured by notch on iPhone X
          </Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    safeArea: {
      flex: 1,
      backgroundColor: '#ffffff',
      // SafeAreaView会自动添加顶部和底部padding | SafeAreaView automatically adds top and bottom padding
    },
    header: {
      backgroundColor: '#6200ee',
      padding: 15,
    },
    headerText: {
      color: '#ffffff',
      fontSize: 18,
      fontWeight: 'bold',
    },
    content: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    footer: {
      backgroundColor: '#03dac6',
      padding: 15,
      alignItems: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在iPhone X模拟器上运行这两个示例，视觉上有什么区别？| What's the visual difference when running these two examples on iPhone X simulator?
    **答案 | Answer:** SafeAreaView版本的header不会被顶部刘海遮挡，footer不会被底部Home Indicator遮挡；View版本可能会被遮挡 | SafeAreaView version's header won't be obscured by top notch, footer won't be obscured by bottom Home Indicator; View version might be obscured
  - 如果在SafeAreaView内部再嵌套一个SafeAreaView会怎样？| What happens if you nest a SafeAreaView inside another SafeAreaView?
    **答案 | Answer:** 不推荐这样做，可能导致双重padding；SafeAreaView应该只在最外层使用一次 | Not recommended, may cause double padding; SafeAreaView should only be used once at outermost level

  **常见误区检查 | Common Misconception Checks:**
  - SafeAreaView会让我的应用在所有设备上都完美显示吗？| Will SafeAreaView make my app display perfectly on all devices?
    **答案 | Answer:** 否 | No - SafeAreaView只处理设备安全区域，不解决不同屏幕尺寸的响应式布局问题，你仍需要设计响应式UI | SafeAreaView only handles device safe areas, doesn't solve responsive layout for different screen sizes, you still need to design responsive UI

### 3. ScrollView - 可滚动容器 | ScrollView - Scrollable Container (1小时 | 1 hour)

- **ScrollView vs View的关键区别 | Key Differences between ScrollView and View**

  **概念定义 | Concept Definition:**
  ScrollView是一个可以包含多个组件和视图的可滚动容器。当内容超出屏幕尺寸时，ScrollView允许用户滚动查看全部内容。它是View的特殊版本，添加了滚动功能，但性能特征完全不同——ScrollView会一次性渲染所有子组件。

  ScrollView is a scrollable container that can hold multiple components and views. When content exceeds screen size, ScrollView allows users to scroll to view all content. It's a special version of View with scrolling functionality added, but with completely different performance characteristics—ScrollView renders all children at once.

  **核心特征 | Key Characteristics:**
  - 默认垂直滚动，可设置horizontal={true}实现水平滚动 | Vertically scrollable by default, can set horizontal={true} for horizontal scrolling
  - 会一次性渲染所有子组件（即使不在屏幕可见区域），不适合长列表 | Renders all children at once (even if not visible), not suitable for long lists
  - 必须有固定的高度，通过flex或具体数值设置 | Must have a fixed height, set through flex or specific value
  - 不能在ScrollView内嵌套同方向的ScrollView（会导致滚动冲突） | Cannot nest ScrollView in the same direction (causes scroll conflicts)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 什么时候应该使用ScrollView而不是View？| When should you use ScrollView instead of View?
     **答案 | Answer:** 当内容高度/宽度超过屏幕尺寸，需要用户滚动查看时 | When content height/width exceeds screen size and users need to scroll to view
  2. ScrollView适合显示100条数据的列表吗？| Is ScrollView suitable for displaying a list of 100 items?
     **答案 | Answer:** 不适合 | No - ScrollView会一次渲染所有100条，性能差；应该用FlatList（下周学习） | ScrollView renders all 100 at once, poor performance; should use FlatList (next week's topic)
  3. ScrollView必须设置高度吗？| Must ScrollView have a height set?
     **答案 | Answer:** 是的 | Yes - 没有高度限制，ScrollView不知道什么时候该滚动；通常用flex: 1或具体高度 | Without height limit, ScrollView doesn't know when to scroll; usually use flex: 1 or specific height
  4. 可以在ScrollView内部再放一个同方向的ScrollView吗？| Can you put a ScrollView in the same direction inside another ScrollView?
     **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - 会导致滚动手势冲突和糟糕的用户体验 | Will cause scroll gesture conflicts and poor user experience

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { SafeAreaView, ScrollView, View, Text, StyleSheet } from 'react-native';

  // ScrollView基础示例 | ScrollView Basic Example
  export default function ScrollViewExample() {
    return (
      <SafeAreaView style={styles.container}>
        {/* 垂直滚动（默认）| Vertical scrolling (default) */}
        <ScrollView style={styles.scrollView}>
          <Text style={styles.title}>垂直滚动内容 | Vertically Scrollable Content</Text>

          {/* 生成足够多的内容以触发滚动 | Generate enough content to trigger scrolling */}
          {[...Array(20)].map((_, index) => (
            <View key={index} style={styles.item}>
              <Text>项目 {index + 1} | Item {index + 1}</Text>
            </View>
          ))}
        </ScrollView>
      </SafeAreaView>
    );
  }

  // 水平滚动示例 | Horizontal Scrolling Example
  function HorizontalScrollExample() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.title}>水平滚动图片库 | Horizontal Scrolling Gallery</Text>

        {/* horizontal={true} 启用水平滚动 | horizontal={true} enables horizontal scrolling */}
        <ScrollView
          horizontal={true}
          showsHorizontalScrollIndicator={false} // 隐藏滚动条 | Hide scrollbar
          style={styles.horizontalScroll}
        >
          {[...Array(10)].map((_, index) => (
            <View key={index} style={styles.card}>
              <Text>卡片 {index + 1} | Card {index + 1}</Text>
            </View>
          ))}
        </ScrollView>
      </SafeAreaView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1, // SafeAreaView需要flex: 1撑满屏幕 | SafeAreaView needs flex: 1 to fill screen
      backgroundColor: '#f5f5f5',
    },
    scrollView: {
      flex: 1, // ScrollView需要高度限制 | ScrollView needs height constraint
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      padding: 15,
      backgroundColor: '#ffffff',
    },
    item: {
      padding: 20,
      marginVertical: 8,
      marginHorizontal: 16,
      backgroundColor: '#ffffff',
      borderRadius: 8,
    },
    horizontalScroll: {
      flexGrow: 0, // 限制ScrollView高度 | Limit ScrollView height
    },
    card: {
      width: 150,
      height: 200,
      margin: 10,
      backgroundColor: '#e0e0e0',
      borderRadius: 10,
      justifyContent: 'center',
      alignItems: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果去掉container的flex: 1会发生什么？| What happens if you remove flex: 1 from container?
    **答案 | Answer:** SafeAreaView不会撑满屏幕，ScrollView也失去高度约束，可能无法正常滚动 | SafeAreaView won't fill screen, ScrollView loses height constraint, may not scroll properly
  - showsHorizontalScrollIndicator={false}的作用是什么？| What does showsHorizontalScrollIndicator={false} do?
    **答案 | Answer:** 隐藏水平滚动条，让UI更简洁美观 | Hides horizontal scrollbar for cleaner UI appearance

  **常见误区检查 | Common Misconception Checks:**
  - ScrollView可以自动计算内容高度并滚动吗？| Can ScrollView automatically calculate content height and scroll?
    **答案 | Answer:** 部分正确 | Partially - ScrollView可以测量子组件总高度，但自身必须有高度限制（通过flex或具体值），否则会无限扩展不滚动 | ScrollView can measure total child height, but itself must have height constraint (via flex or specific value), otherwise it expands infinitely without scrolling

### 4. 样式属性 - 边框、阴影和背景 | Style Props - Borders, Shadows, and Backgrounds (1.5小时 | 1.5 hours)

- **边框样式 | Border Styling**

  **概念定义 | Concept Definition:**
  React Native的边框样式通过borderWidth、borderColor、borderRadius等属性控制。与Web CSS类似但有重要区别：所有边框相关属性都是独立的样式属性，没有简写形式（如CSS的border: 1px solid black）。边框绘制在组件的边界上，不占用padding或margin空间。

  React Native border styling is controlled through borderWidth, borderColor, borderRadius and other properties. Similar to Web CSS but with important differences: all border-related properties are independent style props, no shorthand form (like CSS's border: 1px solid black). Borders are drawn on component boundaries, don't take up padding or margin space.

  **核心特征 | Key Characteristics:**
  - 必须分别设置borderWidth和borderColor，没有简写语法 | Must set borderWidth and borderColor separately, no shorthand syntax
  - 可以分别设置四个边的样式（borderTopWidth, borderRightWidth等） | Can set styles for four sides separately (borderTopWidth, borderRightWidth, etc.)
  - borderRadius可以创建圆角，数值越大圆角越明显 | borderRadius creates rounded corners, larger value means more rounded
  - borderStyle仅支持'solid'、'dotted'、'dashed'（iOS可能不支持虚线） | borderStyle only supports 'solid', 'dotted', 'dashed' (iOS may not support dashed)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以像CSS那样写border: '1px solid black'吗？| Can you write border: '1px solid black' like in CSS?
     **答案 | Answer:** 不可以 | No - React Native不支持简写，必须分别设置borderWidth: 1, borderColor: 'black', borderStyle: 'solid' | React Native doesn't support shorthand, must set borderWidth: 1, borderColor: 'black', borderStyle: 'solid' separately
  2. borderRadius: 50是什么效果？| What effect does borderRadius: 50 have?
     **答案 | Answer:** 创建圆角，如果组件是正方形（width=height=100），会变成圆形 | Creates rounded corners, if component is square (width=height=100), becomes circular
  3. 边框会占用组件的width和height吗？| Does border take up component's width and height?
     **答案 | Answer:** 是的 | Yes - 边框绘制在组件边界上，包含在总尺寸内（类似CSS的border-box） | Border is drawn on component boundary, included in total size (similar to CSS border-box)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Text, StyleSheet } from 'react-native';

  // 边框样式示例 | Border Styling Examples
  export default function BorderExample() {
    return (
      <View style={styles.container}>
        {/* 基础边框 | Basic Border */}
        <View style={styles.basicBorder}>
          <Text>基础边框 | Basic Border</Text>
        </View>

        {/* 圆角边框 | Rounded Border */}
        <View style={styles.roundedBorder}>
          <Text>圆角边框 | Rounded Border</Text>
        </View>

        {/* 圆形（正方形+大圆角）| Circle (Square + Large Border Radius) */}
        <View style={styles.circle}>
          <Text style={styles.circleText}>圆形 | Circle</Text>
        </View>

        {/* 不同边的边框 | Different Borders */}
        <View style={styles.differentBorders}>
          <Text>只有底部边框 | Only Bottom Border</Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      justifyContent: 'space-around',
      backgroundColor: '#f5f5f5',
    },
    basicBorder: {
      padding: 15,
      borderWidth: 2, // 边框宽度 | Border width
      borderColor: '#6200ee', // 边框颜色 | Border color
      borderStyle: 'solid', // 边框样式（可选：solid/dotted/dashed）| Border style (options: solid/dotted/dashed)
      backgroundColor: '#ffffff',
    },
    roundedBorder: {
      padding: 15,
      borderWidth: 2,
      borderColor: '#03dac6',
      borderRadius: 10, // 圆角半径 | Border radius
      backgroundColor: '#ffffff',
    },
    circle: {
      width: 100,
      height: 100, // 宽高相等 | Equal width and height
      borderRadius: 50, // 半径为宽度的一半 | Radius is half of width
      borderWidth: 3,
      borderColor: '#ff6f00',
      justifyContent: 'center',
      alignItems: 'center',
      backgroundColor: '#fff3e0',
    },
    circleText: {
      textAlign: 'center',
    },
    differentBorders: {
      padding: 15,
      borderBottomWidth: 3, // 只设置底部边框 | Only bottom border
      borderBottomColor: '#d32f2f',
      backgroundColor: '#ffffff',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果将circle的borderRadius改为100会怎样？| What happens if you change circle's borderRadius to 100?
    **答案 | Answer:** 仍然是圆形，因为borderRadius超过宽度/高度的一半会被限制为最大圆形 | Still circular, because borderRadius exceeding half of width/height is capped at maximum circle
  - 如何创建一个只有左边有边框的View？| How to create a View with only left border?
    **答案 | Answer:** 使用borderLeftWidth和borderLeftColor，不设置其他边 | Use borderLeftWidth and borderLeftColor, don't set other sides

- **阴影效果 | Shadow Effects**

  **概念定义 | Concept Definition:**
  React Native的阴影效果在iOS和Android上实现方式完全不同。iOS使用shadowColor、shadowOffset、shadowOpacity、shadowRadius四个属性；Android使用elevation一个属性。这是React Native中平台差异最明显的样式特性之一，需要分别处理以获得一致的视觉效果。

  React Native shadow effects are implemented completely differently on iOS and Android. iOS uses four properties: shadowColor, shadowOffset, shadowOpacity, shadowRadius; Android uses one property: elevation. This is one of the most platform-specific styling features in React Native, requiring separate handling for consistent visual effects.

  **核心特征 | Key Characteristics:**
  - iOS需要4个shadow属性配合使用才能显示阴影 | iOS requires 4 shadow properties to work together to display shadow
  - Android只需要elevation属性（数值越大阴影越深）| Android only needs elevation property (larger value means deeper shadow)
  - 阴影不占用布局空间，是视觉效果层 | Shadows don't take up layout space, are visual effect layer
  - 阴影会影响性能，大量使用时需要注意优化 | Shadows affect performance, need optimization when used extensively

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. iOS和Android显示阴影的方式相同吗？| Do iOS and Android display shadows the same way?
     **答案 | Answer:** 不相同 | No - iOS用shadow*属性，Android用elevation；需要同时设置才能跨平台一致 | iOS uses shadow* properties, Android uses elevation; both must be set for cross-platform consistency
  2. 只设置shadowColor能看到阴影吗（iOS）？| Can you see shadow by only setting shadowColor (iOS)?
     **答案 | Answer:** 不能 | No - iOS必须同时设置shadowOpacity（不透明度）否则阴影完全透明不可见 | iOS must also set shadowOpacity (opacity) otherwise shadow is completely transparent
  3. elevation: 0在Android上是什么效果？| What effect does elevation: 0 have on Android?
     **答案 | Answer:** 无阴影 | No shadow - elevation为0表示组件在Z轴上没有高度，无阴影效果 | elevation of 0 means component has no height on Z-axis, no shadow effect
  4. 阴影会影响组件的布局位置吗？| Does shadow affect component's layout position?
     **答案 | Answer:** 不会 | No - 阴影是绘制在组件上方的视觉层，不占用Flexbox布局空间 | Shadow is a visual layer drawn above component, doesn't take up Flexbox layout space

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Text, StyleSheet, Platform } from 'react-native';

  // 阴影效果示例 | Shadow Effects Examples
  export default function ShadowExample() {
    return (
      <View style={styles.container}>
        {/* iOS阴影 | iOS Shadow */}
        <View style={styles.iosShadow}>
          <Text>iOS阴影效果 | iOS Shadow</Text>
          <Text style={styles.note}>
            (Android上看不到) | (Not visible on Android)
          </Text>
        </View>

        {/* Android阴影 | Android Shadow */}
        <View style={styles.androidShadow}>
          <Text>Android阴影效果 | Android Shadow</Text>
          <Text style={styles.note}>
            (iOS上看不到) | (Not visible on iOS)
          </Text>
        </View>

        {/* 跨平台阴影（推荐）| Cross-platform Shadow (Recommended) */}
        <View style={styles.crossPlatformShadow}>
          <Text>跨平台阴影 | Cross-platform Shadow</Text>
          <Text style={styles.note}>
            当前平台: {Platform.OS} | Current platform: {Platform.OS}
          </Text>
        </View>

        {/* 卡片效果（阴影+圆角+边距）| Card Effect (Shadow + Rounded + Spacing) */}
        <View style={styles.card}>
          <Text style={styles.cardTitle}>卡片组件 | Card Component</Text>
          <Text>组合使用边框、圆角和阴影创建卡片效果 | Combine border, rounded corners and shadow for card effect</Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      justifyContent: 'space-around',
      backgroundColor: '#f5f5f5',
    },
    iosShadow: {
      padding: 20,
      backgroundColor: '#ffffff',
      // iOS阴影属性（4个必须配合使用）| iOS shadow properties (4 must work together)
      shadowColor: '#000000', // 阴影颜色 | Shadow color
      shadowOffset: { width: 0, height: 2 }, // 阴影偏移 | Shadow offset
      shadowOpacity: 0.25, // 阴影不透明度 (0-1) | Shadow opacity (0-1)
      shadowRadius: 3.84, // 阴影模糊半径 | Shadow blur radius
    },
    androidShadow: {
      padding: 20,
      backgroundColor: '#ffffff',
      // Android阴影属性（只需一个）| Android shadow property (only one needed)
      elevation: 5, // 阴影深度，值越大阴影越深 | Shadow depth, larger value means deeper shadow
    },
    crossPlatformShadow: {
      padding: 20,
      backgroundColor: '#ffffff',
      // 同时设置iOS和Android阴影 | Set both iOS and Android shadows
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
    card: {
      padding: 20,
      margin: 10,
      backgroundColor: '#ffffff',
      borderRadius: 12, // 圆角 | Rounded corners
      // 跨平台阴影 | Cross-platform shadow
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.3,
      shadowRadius: 4.65,
      elevation: 8,
    },
    cardTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 8,
    },
    note: {
      fontSize: 12,
      color: '#666',
      marginTop: 5,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Platform.select()的作用是什么？| What does Platform.select() do?
    **答案 | Answer:** 根据当前运行的平台（iOS或Android）返回不同的样式对象，实现平台特定样式 | Returns different style objects based on current platform (iOS or Android), enabling platform-specific styling
  - 如果想要更深的阴影效果，应该调整哪些属性？| Which properties should be adjusted for deeper shadow effect?
    **答案 | Answer:** iOS：增加shadowOpacity和shadowRadius；Android：增加elevation值 | iOS: increase shadowOpacity and shadowRadius; Android: increase elevation value

  **常见误区检查 | Common Misconception Checks:**
  - 设置shadowColor就能在Android上显示阴影吗？| Can you display shadow on Android by setting shadowColor?
    **答案 | Answer:** 不能 | No - Android完全忽略shadow*属性，必须使用elevation | Android completely ignores shadow* properties, must use elevation

### 5. overflow属性的特殊行为 | Special Behavior of overflow Property (45分钟 | 45 minutes)

- **overflow在React Native中的限制 | Limitations of overflow in React Native**

  **概念定义 | Concept Definition:**
  overflow属性控制当子组件超出父容器边界时的显示方式。在Web中overflow非常强大（visible/hidden/scroll/auto），但在React Native中功能受限且行为不一致。Android默认支持overflow: 'hidden'，但iOS在某些情况下不支持，特别是结合borderRadius使用时。

  The overflow property controls how child components are displayed when they exceed parent container boundaries. In Web, overflow is very powerful (visible/hidden/scroll/auto), but in React Native it's limited and inconsistent. Android supports overflow: 'hidden' by default, but iOS doesn't support it in certain cases, especially when combined with borderRadius.

  **核心特征 | Key Characteristics:**
  - React Native只支持'visible'和'hidden'两个值（无scroll/auto）| React Native only supports 'visible' and 'hidden' (no scroll/auto)
  - Android上overflow: 'hidden'配合borderRadius可以正常工作 | On Android, overflow: 'hidden' works properly with borderRadius
  - iOS上overflow: 'hidden'可能不生效，特别是在带圆角的容器中 | On iOS, overflow: 'hidden' may not work, especially in containers with rounded corners
  - Web开发者需要特别注意这个平台差异 | Web developers need to pay special attention to this platform difference

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native支持overflow: 'scroll'吗？| Does React Native support overflow: 'scroll'?
     **答案 | Answer:** 不支持 | No - 只支持'visible'和'hidden'；滚动需要使用ScrollView组件 | Only supports 'visible' and 'hidden'; scrolling requires ScrollView component
  2. overflow: 'hidden'在iOS和Android上行为一致吗？| Does overflow: 'hidden' behave consistently on iOS and Android?
     **答案 | Answer:** 不一致 | No - Android支持良好，iOS在某些情况下（特别是borderRadius）可能不生效 | Android supports well, iOS may not work in some cases (especially with borderRadius)
  3. 如何在React Native中实现"裁剪超出父容器的内容"这个Web常见需求？| How to achieve "clip content exceeding parent container" in React Native, a common Web requirement?
     **答案 | Answer:** 使用overflow: 'hidden'，但需要测试iOS兼容性；如果不工作，考虑调整布局或使用额外的View包裹 | Use overflow: 'hidden', but test iOS compatibility; if doesn't work, consider adjusting layout or using additional View wrapper
  4. overflow: 'visible'是默认值吗？| Is overflow: 'visible' the default value?
     **答案 | Answer:** 是的 | Yes - 默认情况下子组件可以超出父容器边界显示 | By default, child components can display beyond parent container boundaries

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Text, Image, StyleSheet, Platform } from 'react-native';

  // overflow属性示例 | overflow Property Examples
  export default function OverflowExample() {
    return (
      <View style={styles.container}>
        <Text style={styles.sectionTitle}>
          overflow: 'visible' (默认 | Default)
        </Text>
        <View style={styles.visibleContainer}>
          <View style={styles.overflowingBox}>
            <Text>这个盒子超出了父容器 | This box exceeds parent</Text>
          </View>
        </View>

        <Text style={styles.sectionTitle}>
          overflow: 'hidden'
        </Text>
        <View style={styles.hiddenContainer}>
          <View style={styles.overflowingBox}>
            <Text>被裁剪的内容 | Clipped content</Text>
          </View>
        </View>

        <Text style={styles.sectionTitle}>
          overflow + borderRadius (平台差异 | Platform Difference)
        </Text>
        <View style={styles.roundedHiddenContainer}>
          <Image
            source={{ uri: 'https://picsum.photos/200' }}
            style={styles.overflowingImage}
          />
        </View>
        <Text style={styles.note}>
          iOS可能无法裁剪圆角外的内容 | iOS may not clip content outside rounded corners
        </Text>

        <Text style={styles.platform}>
          当前平台: {Platform.OS} | Current Platform: {Platform.OS}
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginTop: 20,
      marginBottom: 10,
    },
    visibleContainer: {
      width: 150,
      height: 100,
      backgroundColor: '#e3f2fd',
      borderWidth: 2,
      borderColor: '#2196f3',
      overflow: 'visible', // 默认值 | Default value
    },
    hiddenContainer: {
      width: 150,
      height: 100,
      backgroundColor: '#f3e5f5',
      borderWidth: 2,
      borderColor: '#9c27b0',
      overflow: 'hidden', // 裁剪超出内容 | Clip overflowing content
    },
    roundedHiddenContainer: {
      width: 150,
      height: 150,
      backgroundColor: '#fff3e0',
      borderRadius: 75, // 圆形容器 | Circular container
      overflow: 'hidden', // 尝试裁剪圆角外的图片 | Try to clip image outside rounded corners
      borderWidth: 3,
      borderColor: '#ff9800',
    },
    overflowingBox: {
      width: 200, // 比父容器宽 | Wider than parent
      height: 80,
      backgroundColor: '#ffcdd2',
      padding: 10,
      borderWidth: 1,
      borderColor: '#f44336',
    },
    overflowingImage: {
      width: 200, // 比圆形容器大 | Larger than circular container
      height: 200,
    },
    note: {
      fontSize: 12,
      color: '#666',
      marginTop: 5,
      fontStyle: 'italic',
    },
    platform: {
      marginTop: 20,
      fontSize: 14,
      fontWeight: 'bold',
      color: '#1976d2',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在iOS模拟器上运行，roundedHiddenContainer中的图片会被圆角裁剪吗？| On iOS simulator, will the image in roundedHiddenContainer be clipped by rounded corners?
    **答案 | Answer:** 可能不会 | Probably not - iOS在overflow: 'hidden' + borderRadius组合时可能不生效，图片会超出圆角 | iOS may not work with overflow: 'hidden' + borderRadius combination, image may exceed rounded corners
  - 如何解决iOS上圆角图片不被裁剪的问题？| How to solve the issue of rounded images not being clipped on iOS?
    **答案 | Answer:** 直接给Image组件设置borderRadius，而不是依赖父容器的overflow: 'hidden' | Set borderRadius directly on Image component instead of relying on parent's overflow: 'hidden'

  **常见误区检查 | Common Misconception Checks:**
  - overflow在React Native中和Web中的行为完全一样吗？| Does overflow behave exactly the same in React Native as in Web?
    **答案 | Answer:** 不一样 | No - React Native只支持visible/hidden，且iOS兼容性有问题；Web支持visible/hidden/scroll/auto且跨浏览器一致 | React Native only supports visible/hidden with iOS compatibility issues; Web supports visible/hidden/scroll/auto with consistent cross-browser behavior

### 6. 知识巩固与实践 | Knowledge Consolidation and Practice (30分钟 | 30 minutes)

- **View组件家族总结 | View Component Family Summary**

  **综合概念检查 | Comprehensive Concept Check:**
  1. View、SafeAreaView、ScrollView三者的核心区别是什么？| What are the core differences between View, SafeAreaView, and ScrollView?
     **答案 | Answer:** View是基础容器；SafeAreaView处理设备安全区域；ScrollView支持滚动但会渲染所有子组件 | View is basic container; SafeAreaView handles device safe areas; ScrollView supports scrolling but renders all children
  2. 什么时候必须使用SafeAreaView？| When must SafeAreaView be used?
     **答案 | Answer:** 当应用有全屏内容且需要避免被刘海屏、状态栏、Home Indicator遮挡时 | When app has full-screen content and needs to avoid being obscured by notches, status bars, Home Indicators
  3. 如何在React Native中创建跨平台一致的卡片阴影效果？| How to create cross-platform consistent card shadow effect in React Native?
     **答案 | Answer:** 同时设置iOS的shadow*属性和Android的elevation，通常使用Platform.select() | Set both iOS shadow* properties and Android elevation, usually using Platform.select()
  4. React Native中哪些样式特性与Web有明显差异？| Which styling features in React Native differ significantly from Web?
     **答案 | Answer:** flexDirection默认值、overflow行为、阴影实现方式、边框简写不支持 | flexDirection default value, overflow behavior, shadow implementation, border shorthand not supported
  5. 为什么不推荐用ScrollView渲染长列表？下周应该学什么？| Why is ScrollView not recommended for long lists? What should be learned next week?
     **答案 | Answer:** ScrollView一次渲染所有子组件，性能差；下周学习FlatList（虚拟化列表）和样式布局 | ScrollView renders all children at once, poor performance; next week learn FlatList (virtualized list) and styling layouts

## 实践项目：个人名片应用 | Practical Project: Personal Card App

### 目标 | Objective
创建一个个人名片应用，综合运用View、SafeAreaView、ScrollView以及边框、阴影、圆角等样式属性。项目将展示如何使用嵌套View构建复杂布局，并处理设备安全区域和样式的跨平台兼容性。

Create a personal card application that comprehensively applies View, SafeAreaView, ScrollView, and style properties like borders, shadows, and rounded corners. The project will demonstrate how to build complex layouts with nested Views and handle device safe areas and cross-platform style compatibility.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. View组件可以直接包含文本吗？如果不能，应该用什么？| Can View component directly contain text? If not, what should be used?
   **答案 | Answer:** 不能，所有文本必须包裹在Text组件中 | No, all text must be wrapped in Text component
2. 如何让一个View占据整个屏幕高度？| How to make a View take up the entire screen height?
   **答案 | Answer:** 设置flex: 1，且确保父容器也有flex: 1 | Set flex: 1, and ensure parent container also has flex: 1
3. 跨平台阴影需要设置哪些属性？| What properties are needed for cross-platform shadows?
   **答案 | Answer:** iOS需要shadowColor/shadowOffset/shadowOpacity/shadowRadius；Android需要elevation | iOS needs shadowColor/shadowOffset/shadowOpacity/shadowRadius; Android needs elevation

### 步骤 | Steps
1. 创建SafeAreaView根容器处理设备安全区域 | Create SafeAreaView root container to handle device safe areas
2. 使用ScrollView包裹主要内容以支持滚动 | Use ScrollView to wrap main content for scrolling support
3. 构建个人信息卡片（头像+姓名+职位）使用嵌套View和圆角 | Build personal info card (avatar + name + title) using nested Views and rounded corners
4. 添加技能标签列表，每个标签是带圆角边框的View | Add skills tag list, each tag is a View with rounded borders
5. 实现跨平台阴影效果让卡片有层次感 | Implement cross-platform shadow effects for card depth

### 示例代码 | Example Code
```jsx
import React from 'react';
import {
  SafeAreaView,
  ScrollView,
  View,
  Text,
  Image,
  StyleSheet,
  Platform,
} from 'react-native';

/**
 * 个人名片应用 | Personal Card App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - View组件嵌套和布局 | View component nesting and layout
 * - SafeAreaView处理设备安全区域 | SafeAreaView handling device safe areas
 * - ScrollView实现可滚动内容 | ScrollView for scrollable content
 * - 跨平台阴影效果 | Cross-platform shadow effects
 * - 边框和圆角样式 | Border and rounded corner styling
 */

export default function PersonalCardApp() {
  // 个人信息数据 | Personal information data
  const profile = {
    name: '张伟 | Zhang Wei',
    title: 'React Native 开发者 | React Native Developer',
    avatar: 'https://i.pravatar.cc/150?img=12',
    bio: '热爱移动开发，专注于React Native跨平台应用开发。从Web开发转向移动端，致力于打造流畅的用户体验。| Passionate about mobile development, focusing on React Native cross-platform app development. Transitioned from Web to mobile, dedicated to creating smooth user experiences.',
    skills: [
      'React Native',
      'JavaScript',
      'TypeScript',
      'iOS & Android',
      'UI/UX Design',
      'RESTful API',
    ],
    contact: {
      email: 'zhangwei@example.com',
      github: 'github.com/zhangwei',
      location: '北京，中国 | Beijing, China',
    },
  };

  return (
    // 概念检查点1: SafeAreaView处理设备安全区域 | Concept checkpoint 1: SafeAreaView handles device safe areas
    <SafeAreaView style={styles.container}>
      {/* 概念检查点2: ScrollView支持内容滚动 | Concept checkpoint 2: ScrollView supports content scrolling */}
      <ScrollView
        style={styles.scrollView}
        showsVerticalScrollIndicator={false}
      >
        {/* 概念检查点3: 卡片组件使用跨平台阴影 | Concept checkpoint 3: Card component uses cross-platform shadow */}
        <View style={styles.card}>
          {/* 头像部分 - 圆形图片 | Avatar section - circular image */}
          <View style={styles.avatarContainer}>
            <Image
              source={{ uri: profile.avatar }}
              style={styles.avatar}
            />
          </View>

          {/* 基本信息 | Basic information */}
          <Text style={styles.name}>{profile.name}</Text>
          <Text style={styles.title}>{profile.title}</Text>

          {/* 分割线 | Divider */}
          <View style={styles.divider} />

          {/* 个人简介 | Bio */}
          <View style={styles.section}>
            <Text style={styles.sectionTitle}>关于我 | About Me</Text>
            <Text style={styles.bio}>{profile.bio}</Text>
          </View>

          {/* 技能标签 - 概念检查点4: 嵌套View布局 | Skills tags - Concept checkpoint 4: nested View layout */}
          <View style={styles.section}>
            <Text style={styles.sectionTitle}>技能 | Skills</Text>
            <View style={styles.skillsContainer}>
              {profile.skills.map((skill, index) => (
                // 概念检查点5: 每个标签是独立的View带边框圆角 | Concept checkpoint 5: each tag is a separate View with border and rounded corners
                <View key={index} style={styles.skillTag}>
                  <Text style={styles.skillText}>{skill}</Text>
                </View>
              ))}
            </View>
          </View>

          {/* 联系方式 | Contact information */}
          <View style={styles.section}>
            <Text style={styles.sectionTitle}>联系方式 | Contact</Text>
            <View style={styles.contactContainer}>
              <ContactItem
                icon="📧"
                label="Email | 邮箱"
                value={profile.contact.email}
              />
              <ContactItem
                icon="💻"
                label="GitHub"
                value={profile.contact.github}
              />
              <ContactItem
                icon="📍"
                label="Location | 位置"
                value={profile.contact.location}
              />
            </View>
          </View>
        </View>

        {/* 额外信息卡片 - 演示多个卡片布局 | Additional info card - demonstrate multiple card layout */}
        <View style={styles.card}>
          <Text style={styles.sectionTitle}>项目经验 | Project Experience</Text>
          <Text style={styles.bio}>
            开发了多个React Native应用，包括电商、社交、工具类应用。
            擅长性能优化和用户体验改进。
            {'\n\n'}
            Developed multiple React Native apps including e-commerce, social, and utility apps.
            Expert in performance optimization and UX improvement.
          </Text>
        </View>

        {/* 底部间距 | Bottom spacing */}
        <View style={styles.bottomSpace} />
      </ScrollView>
    </SafeAreaView>
  );
}

// 联系方式项组件 - 展示View组件化思维 | Contact item component - demonstrate View componentization
function ContactItem({ icon, label, value }) {
  return (
    <View style={styles.contactItem}>
      <Text style={styles.contactIcon}>{icon}</Text>
      <View style={styles.contactInfo}>
        <Text style={styles.contactLabel}>{label}</Text>
        <Text style={styles.contactValue}>{value}</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  // 根容器 - flex: 1占满屏幕 | Root container - flex: 1 fills screen
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  // ScrollView样式 | ScrollView styles
  scrollView: {
    flex: 1,
  },
  // 卡片样式 - 跨平台阴影是关键 | Card styles - cross-platform shadow is key
  card: {
    backgroundColor: '#ffffff',
    marginHorizontal: 16,
    marginTop: 16,
    padding: 20,
    borderRadius: 16,
    // iOS阴影 | iOS shadow
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 8,
    // Android阴影 | Android shadow
    elevation: 4,
  },
  // 头像容器 - 居中对齐 | Avatar container - center alignment
  avatarContainer: {
    alignItems: 'center',
    marginBottom: 16,
  },
  // 圆形头像 - borderRadius为宽度一半 | Circular avatar - borderRadius is half of width
  avatar: {
    width: 120,
    height: 120,
    borderRadius: 60,
    borderWidth: 4,
    borderColor: '#6200ee',
  },
  // 姓名样式 | Name styles
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    textAlign: 'center',
    color: '#212121',
    marginBottom: 8,
  },
  // 职位样式 | Title styles
  title: {
    fontSize: 16,
    color: '#757575',
    textAlign: 'center',
    marginBottom: 16,
  },
  // 分割线 - 单独的View作为视觉元素 | Divider - separate View as visual element
  divider: {
    height: 1,
    backgroundColor: '#e0e0e0',
    marginVertical: 16,
  },
  // 章节容器 | Section container
  section: {
    marginTop: 16,
  },
  // 章节标题 | Section title
  sectionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#212121',
    marginBottom: 12,
  },
  // 个人简介文本 | Bio text
  bio: {
    fontSize: 14,
    lineHeight: 22,
    color: '#616161',
  },
  // 技能容器 - flexWrap实现标签换行 | Skills container - flexWrap for tag wrapping
  skillsContainer: {
    flexDirection: 'row',
    flexWrap: 'wrap',
    marginTop: 8,
  },
  // 技能标签 - 边框+圆角+间距 | Skill tag - border + rounded corners + spacing
  skillTag: {
    paddingHorizontal: 16,
    paddingVertical: 8,
    borderRadius: 20,
    borderWidth: 1.5,
    borderColor: '#6200ee',
    marginRight: 10,
    marginBottom: 10,
  },
  // 技能文本 | Skill text
  skillText: {
    fontSize: 14,
    color: '#6200ee',
    fontWeight: '500',
  },
  // 联系方式容器 | Contact container
  contactContainer: {
    marginTop: 8,
  },
  // 联系方式项 - flexDirection: row横向布局 | Contact item - flexDirection: row for horizontal layout
  contactItem: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingVertical: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#f0f0f0',
  },
  // 联系图标 | Contact icon
  contactIcon: {
    fontSize: 24,
    marginRight: 12,
  },
  // 联系信息容器 | Contact info container
  contactInfo: {
    flex: 1,
  },
  // 联系标签 | Contact label
  contactLabel: {
    fontSize: 12,
    color: '#9e9e9e',
    marginBottom: 2,
  },
  // 联系值 | Contact value
  contactValue: {
    fontSize: 14,
    color: '#212121',
  },
  // 底部间距 - 确保内容不被底部遮挡 | Bottom spacing - ensure content not obscured at bottom
  bottomSpace: {
    height: 20,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确使用SafeAreaView避免内容被设备特殊区域遮挡？| Does the project correctly use SafeAreaView to avoid content being obscured by device-specific areas?
2. ScrollView是否正常工作，内容超出屏幕时可以滚动？| Does ScrollView work properly, allowing scrolling when content exceeds screen?
3. 卡片阴影在iOS和Android上是否都能正常显示？| Do card shadows display correctly on both iOS and Android?
4. 头像是否正确显示为圆形（使用borderRadius）？| Does avatar display correctly as circular (using borderRadius)?
5. 技能标签的布局是否使用flexWrap实现自动换行？| Does skills tag layout use flexWrap for automatic wrapping?
6. 代码中是否体现了View组件化思维（ContactItem组件）？| Does the code reflect View componentization thinking (ContactItem component)?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **View嵌套理解强化 | View Nesting Understanding Reinforcement**
   - **练习描述 | Exercise Description:** 创建一个四层嵌套的View，每层设置不同的padding、margin和backgroundColor，观察并记录各层之间的空间关系
   - **概念检查 | Concept Check:** padding和margin如何影响嵌套View的布局？哪个影响View内部空间，哪个影响View之间的距离？
   - **学习目标 | Learning Objective:** 深刻理解padding（内边距）和margin（外边距）的区别及其在嵌套布局中的作用

2. **SafeAreaView应用练习 | SafeAreaView Application Exercise**
   - **练习描述 | Exercise Description:** 在iPhone X和Android模拟器上分别测试有/无SafeAreaView的布局差异，截图对比
   - **概念检查 | Concept Check:** SafeAreaView在哪些设备上有实际效果？它如何自动调整padding？
   - **学习目标 | Learning Objective:** 理解SafeAreaView的跨平台行为和设备适配原理

3. **跨平台阴影练习 | Cross-platform Shadow Exercise**
   - **练习描述 | Exercise Description:** 创建三个卡片：一个只用iOS阴影，一个只用Android阴影，一个使用Platform.select()实现跨平台阴影，对比效果
   - **概念检查 | Concept Check:** iOS需要哪4个shadow属性？Android需要什么属性？如何用Platform.select()？
   - **学习目标 | Learning Objective:** 掌握React Native中阴影的平台差异和跨平台解决方案

4. **overflow行为探索 | overflow Behavior Exploration**
   - **练习描述 | Exercise Description:** 创建一个带borderRadius的容器，内部放置超出边界的Image，分别测试overflow: 'visible'和'hidden'在iOS/Android上的效果
   - **概念检查 | Concept Check:** overflow在React Native中支持哪些值？在iOS上与borderRadius结合时有什么问题？
   - **学习目标 | Learning Objective:** 理解overflow属性的限制和平台兼容性问题

5. **ScrollView性能认知 | ScrollView Performance Awareness**
   - **练习描述 | Exercise Description:** 创建一个ScrollView包含100个View组件，观察初始渲染时间；思考为什么不适合长列表
   - **概念检查 | Concept Check:** ScrollView如何渲染子组件？为什么不适合长列表？应该用什么替代？
   - **学习目标 | Learning Objective:** 认识ScrollView的性能限制，为学习FlatList做准备

6. **样式组合实践 | Style Combination Practice**
   - **练习描述 | Exercise Description:** 不看代码，尝试重新创建个人名片项目中的卡片样式（圆角+阴影+边距）
   - **概念检查 | Concept Check:** 如何组合borderRadius、shadow*/elevation、padding、margin创建卡片效果？
   - **学习目标 | Learning Objective:** 熟练运用多种样式属性创建复杂视觉效果

7. **组件化思维练习 | Componentization Thinking Exercise**
   - **练习描述 | Exercise Description:** 将个人名片项目中的技能标签部分提取为独立的SkillTag组件，接受props: { skill: string, color: string }
   - **概念检查 | Concept Check:** 如何将重复的View结构提取为可复用组件？如何通过props控制样式？
   - **学习目标 | Learning Objective:** 培养React Native组件化思维，提高代码复用性

## 学习资源 | Learning Resources
- [View Component - React Native官方文档](https://reactnative.dev/docs/view)
- [SafeAreaView - React Native官方文档](https://reactnative.dev/docs/safeareaview)
- [ScrollView - React Native官方文档](https://reactnative.dev/docs/scrollview)
- [React Native样式指南 - Style Props](https://reactnative.dev/docs/style)
- [Platform Specific Code - 平台特定代码](https://reactnative.dev/docs/platform-specific-code)
- [React Native布局与样式 - 深入理解](https://reactnative.dev/docs/flexbox)
- [react-native-safe-area-context - 更好的SafeArea解决方案](https://github.com/th3rdwave/react-native-safe-area-context)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解View是React Native最基础的容器组件，不能直接包含文本
- [ ] 掌握View、SafeAreaView、ScrollView的使用场景和区别
- [ ] 能够使用嵌套View创建复杂布局并通过背景色可视化组件树
- [ ] 理解SafeAreaView如何处理iPhone刘海屏和Android状态栏
- [ ] 能够实现跨平台一致的阴影效果（iOS shadow*属性 + Android elevation）
- [ ] 掌握边框样式（borderWidth, borderColor, borderRadius）的使用
- [ ] 理解overflow属性在React Native中的限制和平台差异
- [ ] 能够使用Platform.select()编写平台特定代码
- [ ] 完成个人名片项目，综合运用所有View相关概念
- [ ] 认识ScrollView的性能限制，为下周学习FlatList做准备

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释：
1. 为什么View不能直接包含文本？
2. SafeAreaView和View的核心区别是什么？
3. 如何在React Native中实现跨平台一致的卡片阴影？
4. overflow: 'hidden'在iOS和Android上的行为差异是什么？
5. ScrollView什么时候用，什么时候不用？

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others:
1. Why can't View directly contain text?
2. What's the core difference between SafeAreaView and View?
3. How to implement cross-platform consistent card shadow in React Native?
4. What's the behavior difference of overflow: 'hidden' on iOS vs Android?
5. When to use ScrollView and when not to use it?
