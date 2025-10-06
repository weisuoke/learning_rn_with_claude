# React Native入门 - 第5天：阴影、边框与高级样式 | React Native Introduction - Day 5: Shadows, Borders, and Advanced Styling

## 学习目标 | Learning Objectives
- 掌握iOS和Android平台不同的阴影实现方式 | Master different shadow implementation approaches for iOS and Android platforms
- 理解边框属性及其在移动端的应用 | Understand border properties and their applications on mobile
- 学习创建圆角和圆形UI元素的技巧 | Learn techniques for creating rounded corners and circular UI elements
- 掌握跨平台一致性样式的最佳实践 | Master best practices for cross-platform consistent styling
- 学会使用渐变背景和高级视觉效果 | Learn to use gradient backgrounds and advanced visual effects
- 能够构建现代化的卡片组件设计 | Be able to build modern card component designs

## 详细内容 | Detailed Content

### 1. iOS vs Android 阴影系统 | iOS vs Android Shadow Systems (1小时 | 1 hour)

- **iOS阴影属性 | iOS Shadow Properties**

  **概念定义 | Concept Definition:**
  iOS使用一组独立的样式属性来创建阴影效果，包括shadowColor（阴影颜色）、shadowOffset（阴影偏移）、shadowOpacity（阴影不透明度）和shadowRadius（阴影模糊半径）。这些属性共同工作来创建逼真的阴影效果。 | iOS uses a set of independent style properties to create shadow effects, including shadowColor (shadow color), shadowOffset (shadow displacement), shadowOpacity (shadow opacity), and shadowRadius (shadow blur radius). These properties work together to create realistic shadow effects.

  **核心特征 | Key Characteristics:**
  - shadowColor：定义阴影的颜色，支持任何有效的颜色值 | shadowColor: defines the shadow color, supports any valid color value
  - shadowOffset：{ width: number, height: number } 控制阴影的位置偏移 | shadowOffset: { width: number, height: number } controls shadow position displacement
  - shadowOpacity：0到1之间的值，控制阴影透明度 | shadowOpacity: value between 0 and 1, controls shadow transparency
  - shadowRadius：控制阴影的模糊程度，值越大越模糊 | shadowRadius: controls shadow blur level, larger values create more blur
  - 这些属性仅在iOS上有效 | These properties only work on iOS

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. iOS阴影需要设置多个属性才能生效吗？| Do iOS shadows require multiple properties to take effect?
     **答案 | Answer:** 是 | Yes - 通常需要设置shadowColor、shadowOffset、shadowOpacity和shadowRadius四个属性 | Typically requires setting all four properties: shadowColor, shadowOffset, shadowOpacity, and shadowRadius
  2. shadowOpacity的有效值范围是什么？| What is the valid range for shadowOpacity?
     **答案 | Answer:** 0到1之间 | Between 0 and 1 - 0表示完全透明，1表示完全不透明 | 0 means fully transparent, 1 means fully opaque
  3. shadowOffset的width为正数时，阴影会向哪个方向偏移？| When shadowOffset width is positive, which direction does the shadow shift?
     **答案 | Answer:** 向右 | To the right - 正值向右，负值向左 | Positive values shift right, negative values shift left
  4. iOS阴影属性在Android设备上会生效吗？| Do iOS shadow properties work on Android devices?
     **答案 | Answer:** 否 | No - iOS阴影属性仅在iOS平台有效 | iOS shadow properties only work on iOS platform

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // iOS阴影完整示例 | Complete iOS shadow example
  import { StyleSheet, View, Text, Platform } from 'react-native';

  const IOSShadowCard = () => {
    return (
      <View style={styles.card}>
        <Text>iOS阴影卡片 | iOS Shadow Card</Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    card: {
      backgroundColor: 'white',
      padding: 20,
      margin: 16,
      borderRadius: 8,
      // iOS阴影属性 | iOS shadow properties
      shadowColor: '#000',              // 黑色阴影 | Black shadow
      shadowOffset: {                   // 阴影偏移 | Shadow offset
        width: 0,                       // 水平不偏移 | No horizontal offset
        height: 2,                      // 向下偏移2像素 | 2 pixels down
      },
      shadowOpacity: 0.25,              // 25%不透明度 | 25% opacity
      shadowRadius: 3.84,               // 模糊半径 | Blur radius
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果只设置shadowColor而不设置shadowOpacity，阴影会显示吗？| Will the shadow display if only shadowColor is set without shadowOpacity?
    **答案 | Answer:** 不会 | No - shadowOpacity默认为0，必须设置大于0的值才能看到阴影 | shadowOpacity defaults to 0, must be set to a value greater than 0 to see the shadow
  - 如何创建一个向上的阴影效果？| How to create an upward shadow effect?
    **答案 | Answer:** 设置shadowOffset的height为负值 | Set shadowOffset height to a negative value

  **常见误区检查 | Common Misconception Checks:**
  - iOS阴影会自动在Android上显示吗？| Do iOS shadows automatically display on Android?
    **答案 | Answer:** 否 | No - 必须使用Platform.OS或elevation来为Android单独处理 | Must use Platform.OS or elevation to handle Android separately
  - shadowRadius值越大阴影越深吗？| Does a larger shadowRadius value make the shadow darker?
    **答案 | Answer:** 否 | No - shadowRadius控制模糊程度，不影响深度；深度由shadowOpacity控制 | shadowRadius controls blur level, not darkness; darkness is controlled by shadowOpacity

- **Android Elevation属性 | Android Elevation Property**

  **概念定义 | Concept Definition:**
  Android使用单一的elevation属性来创建Material Design风格的阴影效果。elevation值决定了元素在Z轴上的高度，系统会自动根据这个值生成相应的阴影。 | Android uses a single elevation property to create Material Design style shadow effects. The elevation value determines the element's height on the Z-axis, and the system automatically generates corresponding shadows based on this value.

  **核心特征 | Key Characteristics:**
  - elevation：数字值，表示元素的提升高度 | elevation: numeric value representing element's elevation height
  - 值越大，阴影越明显，元素看起来越"浮"在页面上 | Larger values create more prominent shadows, making elements appear more "floating" above the page
  - 这是Android专属属性，在iOS上无效 | This is an Android-only property, doesn't work on iOS
  - 符合Material Design设计规范 | Follows Material Design specifications
  - 不能像iOS那样精细控制阴影的方向和颜色 | Cannot fine-tune shadow direction and color like iOS

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Android的elevation属性可以控制阴影颜色吗？| Can Android's elevation property control shadow color?
     **答案 | Answer:** 否 | No - elevation只能控制阴影的强度，颜色由系统决定 | elevation only controls shadow intensity, color is determined by the system
  2. elevation值为0时会有阴影吗？| Will there be a shadow when elevation is 0?
     **答案 | Answer:** 否 | No - elevation为0表示元素在基础层面，没有阴影 | elevation of 0 means element is at base level with no shadow
  3. elevation属性在iOS设备上会生效吗？| Does the elevation property work on iOS devices?
     **答案 | Answer:** 否 | No - elevation是Android专属属性 | elevation is Android-only property
  4. 更大的elevation值会让阴影更深还是更模糊？| Does a larger elevation value make shadows darker or more blurred?
     **答案 | Answer:** 两者都有 | Both - 更大的elevation会让阴影更深且扩散范围更大 | Larger elevation makes shadows both darker and more spread out

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Android Elevation示例 | Android Elevation example
  import { StyleSheet, View, Text, Platform } from 'react-native';

  const AndroidElevationCard = () => {
    return (
      <View style={styles.card}>
        <Text>Android提升卡片 | Android Elevation Card</Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    card: {
      backgroundColor: 'white',
      padding: 20,
      margin: 16,
      borderRadius: 8,
      // Android专属阴影 | Android-only shadow
      elevation: 4,                     // Material Design标准提升值 | Material Design standard elevation value
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - elevation值通常在什么范围内使用？| What range are elevation values typically used in?
    **答案 | Answer:** 0-24 | 0-24 - Material Design推荐卡片使用2-8，浮动按钮使用6-12 | Material Design recommends 2-8 for cards, 6-12 for floating buttons
  - 如何在不同平台使用不同的阴影？| How to use different shadows on different platforms?
    **答案 | Answer:** 使用Platform.OS条件判断或Platform.select() | Use Platform.OS conditional check or Platform.select()

  **常见误区检查 | Common Misconception Checks:**
  - elevation可以设置负值吗？| Can elevation be set to negative values?
    **答案 | Answer:** 否 | No - elevation必须是非负数，负值会被忽略 | elevation must be non-negative, negative values are ignored
  - 同时设置elevation和iOS阴影属性会冲突吗？| Will setting both elevation and iOS shadow properties cause conflicts?
    **答案 | Answer:** 否 | No - 它们在各自平台独立工作，不会冲突 | They work independently on their respective platforms without conflicts

### 2. 跨平台阴影解决方案 | Cross-Platform Shadow Solutions (1小时 | 1 hour)

- **Platform.select()方法 | Platform.select() Method**

  **概念定义 | Concept Definition:**
  Platform.select()是React Native提供的一个工具方法，允许我们根据运行平台返回不同的值。在样式处理中，它可以帮助我们为iOS和Android提供各自平台特定的阴影样式，实现跨平台一致的视觉效果。 | Platform.select() is a utility method provided by React Native that allows us to return different values based on the running platform. In style handling, it helps us provide platform-specific shadow styles for iOS and Android to achieve cross-platform consistent visual effects.

  **核心特征 | Key Characteristics:**
  - 接受一个对象，包含ios和android键 | Accepts an object with ios and android keys
  - 根据Platform.OS自动返回对应平台的值 | Automatically returns the corresponding platform value based on Platform.OS
  - 可以在StyleSheet中直接使用 | Can be used directly in StyleSheet
  - 支持default键作为备选值 | Supports default key as fallback value
  - 使代码更简洁，避免多个条件判断 | Makes code more concise, avoiding multiple conditional checks

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Platform.select()可以在样式对象中使用吗？| Can Platform.select() be used in style objects?
     **答案 | Answer:** 是 | Yes - 可以直接在StyleSheet.create()中使用 | Can be used directly in StyleSheet.create()
  2. Platform.select()必须包含ios和android两个键吗？| Must Platform.select() include both ios and android keys?
     **答案 | Answer:** 否 | No - 可以只包含一个平台的键，或使用default键 | Can include only one platform key, or use default key
  3. Platform.select()在运行时会重新计算吗？| Does Platform.select() recalculate at runtime?
     **答案 | Answer:** 否 | No - 在样式定义时就已确定，不会动态变化 | Determined at style definition time, doesn't change dynamically
  4. Platform.select()可以返回对象吗？| Can Platform.select() return objects?
     **答案 | Answer:** 是 | Yes - 可以返回任何类型的值，包括对象 | Can return any type of value, including objects

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 使用Platform.select()创建跨平台阴影 | Create cross-platform shadows using Platform.select()
  import { StyleSheet, View, Text, Platform } from 'react-native';

  const CrossPlatformCard = () => {
    return (
      <View style={styles.card}>
        <Text>跨平台阴影卡片 | Cross-Platform Shadow Card</Text>
      </View>
    );
  };

  const styles = StyleSheet.create({
    card: {
      backgroundColor: 'white',
      padding: 20,
      margin: 16,
      borderRadius: 8,
      // 使用Platform.select()为不同平台设置阴影 | Use Platform.select() for platform-specific shadows
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.25,
          shadowRadius: 3.84,
        },
        android: {
          elevation: 4,
        },
      }),
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 展开运算符(...)在这里的作用是什么？| What is the purpose of the spread operator (...) here?
    **答案 | Answer:** 将Platform.select()返回的样式对象合并到card样式中 | Merges the style object returned by Platform.select() into the card style
  - 如果在web平台运行，会使用哪个阴影样式？| If running on web platform, which shadow style will be used?
    **答案 | Answer:** 不会有任何阴影，因为没有定义web或default键 | No shadow will be applied since no web or default key is defined

  **常见误区检查 | Common Misconception Checks:**
  - Platform.select()会增加运行时性能开销吗？| Does Platform.select() add runtime performance overhead?
    **答案 | Answer:** 否 | No - 它在编译/加载时求值，没有运行时开销 | It evaluates at compile/load time with no runtime overhead
  - 可以在Platform.select()中使用动态值吗？| Can dynamic values be used in Platform.select()?
    **答案 | Answer:** 否 | No - Platform.select()在样式定义时求值，不支持动态值 | Platform.select() evaluates at style definition time, doesn't support dynamic values

- **创建可复用的阴影样式 | Creating Reusable Shadow Styles**

  **概念定义 | Concept Definition:**
  通过将跨平台阴影逻辑封装成可复用的样式工具函数或常量对象，我们可以在整个应用中保持一致的阴影效果，提高代码可维护性，并确保设计系统的统一性。 | By encapsulating cross-platform shadow logic into reusable style utility functions or constant objects, we can maintain consistent shadow effects throughout the application, improve code maintainability, and ensure design system consistency.

  **核心特征 | Key Characteristics:**
  - 定义多个层级的阴影强度（如small, medium, large）| Define multiple levels of shadow intensity (e.g., small, medium, large)
  - 每个层级包含对应的iOS和Android样式 | Each level contains corresponding iOS and Android styles
  - 可以创建为单独的样式模块导出 | Can be created as a separate style module for export
  - 符合Material Design或iOS Human Interface Guidelines | Follows Material Design or iOS Human Interface Guidelines
  - 便于全局样式主题管理 | Facilitates global style theme management

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 创建阴影样式工具的主要好处是什么？| What are the main benefits of creating shadow style utilities?
     **答案 | Answer:** 代码复用、一致性、易维护 | Code reuse, consistency, easy maintenance - 避免在每个组件中重复定义阴影 | Avoids repeating shadow definitions in every component
  2. 应该定义多少个阴影层级？| How many shadow levels should be defined?
     **答案 | Answer:** 3-5个 | 3-5 levels - 通常包括无阴影、轻微、中等、较深、很深 | Typically includes none, subtle, medium, elevated, high
  3. 阴影工具应该放在什么位置？| Where should shadow utilities be placed?
     **答案 | Answer:** 专门的样式工具文件 | Dedicated style utility file - 如styles/shadows.js或theme/shadows.js | Such as styles/shadows.js or theme/shadows.js
  4. 为什么不同层级的elevation值不是线性增长的？| Why don't different elevation levels increase linearly?
     **答案 | Answer:** 符合视觉感知 | Follows visual perception - 人眼对阴影的感知不是线性的，需要指数级增长才能看出明显差异 | Human eye perception of shadows is not linear, requires exponential growth to see noticeable differences

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // shadows.js - 可复用的阴影样式工具 | Reusable shadow style utilities
  import { Platform } from 'react-native';

  // 创建阴影生成函数 | Create shadow generator function
  const createShadow = (elevation) => {
    if (Platform.OS === 'ios') {
      // iOS阴影映射表 | iOS shadow mapping table
      const shadowMap = {
        1: { height: 1, radius: 1, opacity: 0.18 },
        2: { height: 1, radius: 2, opacity: 0.20 },
        3: { height: 2, radius: 3, opacity: 0.22 },
        4: { height: 2, radius: 4, opacity: 0.25 },
        6: { height: 3, radius: 5, opacity: 0.27 },
        8: { height: 4, radius: 6, opacity: 0.30 },
      };

      const shadow = shadowMap[elevation] || shadowMap[1];

      return {
        shadowColor: '#000',
        shadowOffset: {
          width: 0,
          height: shadow.height,
        },
        shadowOpacity: shadow.opacity,
        shadowRadius: shadow.radius,
      };
    }

    // Android直接返回elevation | Android returns elevation directly
    return {
      elevation,
    };
  };

  // 预定义的阴影层级 | Predefined shadow levels
  export const shadows = {
    none: {},                           // 无阴影 | No shadow
    small: createShadow(2),            // 轻微阴影 - 适用于按钮 | Subtle shadow - for buttons
    medium: createShadow(4),           // 中等阴影 - 适用于卡片 | Medium shadow - for cards
    large: createShadow(6),            // 较深阴影 - 适用于对话框 | Elevated shadow - for dialogs
    extraLarge: createShadow(8),       // 很深阴影 - 适用于模态框 | High shadow - for modals
  };

  // 使用示例 | Usage example
  import { StyleSheet } from 'react-native';
  import { shadows } from './shadows';

  const styles = StyleSheet.create({
    card: {
      backgroundColor: 'white',
      padding: 16,
      borderRadius: 8,
      ...shadows.medium,              // 应用中等阴影 | Apply medium shadow
    },
    button: {
      backgroundColor: '#007AFF',
      padding: 12,
      borderRadius: 6,
      ...shadows.small,               // 应用轻微阴影 | Apply small shadow
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - createShadow函数中为什么要检查Platform.OS？| Why check Platform.OS in createShadow function?
    **答案 | Answer:** 因为iOS和Android使用不同的阴影属性 | Because iOS and Android use different shadow properties
  - 如何在现有样式中添加阴影？| How to add shadow to existing styles?
    **答案 | Answer:** 使用展开运算符：...shadows.medium | Use spread operator: ...shadows.medium

  **常见误区检查 | Common Misconception Checks:**
  - 可以在运行时动态切换阴影层级吗？| Can shadow levels be dynamically switched at runtime?
    **答案 | Answer:** 是 | Yes - 可以通过条件样式或状态管理动态应用不同的阴影层级 | Can dynamically apply different shadow levels through conditional styles or state management
  - 所有组件都需要阴影吗？| Do all components need shadows?
    **答案 | Answer:** 否 | No - 只在需要视觉层次和深度感的地方使用阴影 | Only use shadows where visual hierarchy and depth perception are needed

### 3. 边框样式基础 | Border Styling Basics (45分钟 | 45 minutes)

- **边框属性系统 | Border Property System**

  **概念定义 | Concept Definition:**
  React Native的边框系统包括边框宽度(borderWidth)、边框颜色(borderColor)和边框样式(borderStyle)三个核心属性。与CSS类似，但支持的样式更有限。此外，还可以单独控制每条边的样式。 | React Native's border system includes three core properties: border width (borderWidth), border color (borderColor), and border style (borderStyle). Similar to CSS but with more limited style support. Additionally, individual border sides can be controlled separately.

  **核心特征 | Key Characteristics:**
  - borderWidth：数字值，设置边框宽度（像素） | borderWidth: numeric value, sets border width (pixels)
  - borderColor：字符串，设置边框颜色 | borderColor: string, sets border color
  - borderStyle：'solid' | 'dotted' | 'dashed'，设置边框样式 | borderStyle: 'solid' | 'dotted' | 'dashed', sets border style
  - 可以单独设置：borderTopWidth, borderRightWidth, borderBottomWidth, borderLeftWidth | Can set individually: borderTopWidth, borderRightWidth, borderBottomWidth, borderLeftWidth
  - 可以单独设置颜色：borderTopColor, borderRightColor等 | Can set colors individually: borderTopColor, borderRightColor, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. borderStyle支持哪些值？| What values does borderStyle support?
     **答案 | Answer:** 'solid', 'dotted', 'dashed' | 'solid', 'dotted', 'dashed' - 不支持CSS的其他边框样式如double、groove等 | Doesn't support other CSS border styles like double, groove, etc.
  2. 可以只设置顶部边框吗？| Can you set only the top border?
     **答案 | Answer:** 是 | Yes - 使用borderTopWidth和borderTopColor | Use borderTopWidth and borderTopColor
  3. 如果只设置borderWidth不设置borderColor会怎样？| What happens if only borderWidth is set without borderColor?
     **答案 | Answer:** 会显示黑色边框 | Will display black border - borderColor默认为黑色 | borderColor defaults to black
  4. borderWidth可以设置负值吗？| Can borderWidth be set to negative values?
     **答案 | Answer:** 否 | No - borderWidth必须是非负数 | borderWidth must be non-negative

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 边框样式示例 | Border style examples
  import { StyleSheet, View, Text } from 'react-native';

  const BorderExamples = () => {
    return (
      <>
        {/* 实线边框 | Solid border */}
        <View style={styles.solidBorder}>
          <Text>实线边框 | Solid Border</Text>
        </View>

        {/* 虚线边框 | Dashed border */}
        <View style={styles.dashedBorder}>
          <Text>虚线边框 | Dashed Border</Text>
        </View>

        {/* 点线边框 | Dotted border */}
        <View style={styles.dottedBorder}>
          <Text>点线边框 | Dotted Border</Text>
        </View>

        {/* 单边边框 | Single side border */}
        <View style={styles.bottomBorder}>
          <Text>仅底部边框 | Bottom Border Only</Text>
        </View>

        {/* 多色边框 | Multi-color border */}
        <View style={styles.multiColorBorder}>
          <Text>多色边框 | Multi-Color Border</Text>
        </View>
      </>
    );
  };

  const styles = StyleSheet.create({
    solidBorder: {
      borderWidth: 2,                   // 2像素宽边框 | 2 pixel wide border
      borderColor: '#007AFF',          // 蓝色边框 | Blue border
      borderStyle: 'solid',            // 实线 | Solid line
      padding: 16,
      margin: 8,
    },
    dashedBorder: {
      borderWidth: 2,
      borderColor: '#FF3B30',          // 红色 | Red
      borderStyle: 'dashed',           // 虚线 | Dashed line
      padding: 16,
      margin: 8,
    },
    dottedBorder: {
      borderWidth: 2,
      borderColor: '#34C759',          // 绿色 | Green
      borderStyle: 'dotted',           // 点线 | Dotted line
      padding: 16,
      margin: 8,
    },
    bottomBorder: {
      borderBottomWidth: 2,            // 只设置底部边框宽度 | Only set bottom border width
      borderBottomColor: '#AF52DE',    // 紫色底部边框 | Purple bottom border
      padding: 16,
      margin: 8,
    },
    multiColorBorder: {
      borderTopWidth: 3,
      borderTopColor: '#FF3B30',       // 红色顶部 | Red top
      borderRightWidth: 3,
      borderRightColor: '#FF9500',     // 橙色右侧 | Orange right
      borderBottomWidth: 3,
      borderBottomColor: '#FFCC00',    // 黄色底部 | Yellow bottom
      borderLeftWidth: 3,
      borderLeftColor: '#34C759',      // 绿色左侧 | Green left
      padding: 16,
      margin: 8,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何创建只有左侧和右侧有边框的容器？| How to create a container with only left and right borders?
    **答案 | Answer:** 设置borderLeftWidth和borderRightWidth，不设置top和bottom | Set borderLeftWidth and borderRightWidth, don't set top and bottom
  - borderStyle在不同平台上渲染效果一致吗？| Does borderStyle render consistently across platforms?
    **答案 | Answer:** 基本一致，但dotted和dashed在不同平台可能有细微差异 | Generally consistent, but dotted and dashed may have subtle differences on different platforms

  **常见误区检查 | Common Misconception Checks:**
  - 可以使用border简写属性吗（如border: '1px solid red'）？| Can you use border shorthand property (like border: '1px solid red')?
    **答案 | Answer:** 否 | No - React Native不支持CSS简写属性，必须分别设置 | React Native doesn't support CSS shorthand, must set separately
  - 边框会影响元素的尺寸吗？| Do borders affect element dimensions?
    **答案 | Answer:** 是 | Yes - 边框会增加元素的总尺寸（类似CSS的content-box） | Borders increase total element dimensions (similar to CSS content-box)

### 4. 圆角与圆形元素 | Rounded Corners and Circular Elements (45分钟 | 45 minutes)

- **borderRadius 圆角系统 | borderRadius Rounded Corner System**

  **概念定义 | Concept Definition:**
  borderRadius用于创建圆角效果，可以统一设置所有角的圆角半径，也可以单独控制每个角。当borderRadius值等于元素宽高的一半时，可以创建完美的圆形。 | borderRadius is used to create rounded corner effects. It can uniformly set the radius for all corners or control each corner individually. When borderRadius equals half of the element's width/height, it creates a perfect circle.

  **核心特征 | Key Characteristics:**
  - borderRadius：统一设置四个角的圆角半径 | borderRadius: uniformly sets radius for all four corners
  - borderTopLeftRadius, borderTopRightRadius, borderBottomLeftRadius, borderBottomRightRadius：单独控制每个角 | borderTopLeftRadius, borderTopRightRadius, borderBottomLeftRadius, borderBottomRightRadius: control each corner individually
  - 值为数字，表示像素单位 | Value is numeric, represents pixels
  - 可以创建从轻微圆角到完全圆形的各种效果 | Can create effects from subtle rounding to perfect circles
  - 结合固定宽高可以创建圆形头像、按钮等 | Combined with fixed width/height to create circular avatars, buttons, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如何创建一个完美的圆形？| How to create a perfect circle?
     **答案 | Answer:** 设置相等的width和height，borderRadius为宽高的一半 | Set equal width and height, borderRadius to half of width/height
  2. borderRadius可以超过元素宽高吗？| Can borderRadius exceed element width/height?
     **答案 | Answer:** 可以，但效果会被限制在最大可能的圆角 | Yes, but effect will be capped at maximum possible rounding
  3. 可以只圆化顶部两个角吗？| Can you round only the top two corners?
     **答案 | Answer:** 是 | Yes - 设置borderTopLeftRadius和borderTopRightRadius | Set borderTopLeftRadius and borderTopRightRadius
  4. borderRadius会影响子元素的显示吗？| Does borderRadius affect child element display?
     **答案 | Answer:** 是 | Yes - 配合overflow: 'hidden'可以裁剪子元素 | Combined with overflow: 'hidden' can clip child elements

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 圆角和圆形示例 | Rounded corners and circular examples
  import { StyleSheet, View, Text, Image } from 'react-native';

  const RoundedExamples = () => {
    return (
      <>
        {/* 轻微圆角卡片 | Subtle rounded card */}
        <View style={styles.subtleCard}>
          <Text>轻微圆角 (4px) | Subtle Rounded (4px)</Text>
        </View>

        {/* 中等圆角卡片 | Medium rounded card */}
        <View style={styles.mediumCard}>
          <Text>中等圆角 (12px) | Medium Rounded (12px)</Text>
        </View>

        {/* 高度圆角按钮 | Highly rounded button */}
        <View style={styles.pillButton}>
          <Text style={styles.buttonText}>胶囊按钮 | Pill Button</Text>
        </View>

        {/* 圆形头像 | Circular avatar */}
        <View style={styles.avatar}>
          <Text style={styles.avatarText}>头像 | Avatar</Text>
        </View>

        {/* 单角圆化 | Single corner rounded */}
        <View style={styles.notificationBadge}>
          <Text style={styles.badgeText}>9+</Text>
        </View>

        {/* 顶部圆角卡片 | Top rounded card */}
        <View style={styles.topRoundedCard}>
          <Text>仅顶部圆角 | Top Rounded Only</Text>
        </View>
      </>
    );
  };

  const styles = StyleSheet.create({
    subtleCard: {
      backgroundColor: '#F2F2F7',
      padding: 16,
      margin: 8,
      borderRadius: 4,                 // 轻微圆角 | Subtle rounding
    },
    mediumCard: {
      backgroundColor: '#E5E5EA',
      padding: 16,
      margin: 8,
      borderRadius: 12,                // 中等圆角 | Medium rounding
    },
    pillButton: {
      backgroundColor: '#007AFF',
      paddingVertical: 12,
      paddingHorizontal: 24,
      margin: 8,
      borderRadius: 25,                // 高圆角创建胶囊形状 | High rounding creates pill shape
    },
    buttonText: {
      color: 'white',
      fontWeight: '600',
      textAlign: 'center',
    },
    avatar: {
      width: 80,                       // 相等的宽高 | Equal width and height
      height: 80,
      borderRadius: 40,                // 宽高的一半创建圆形 | Half of width/height creates circle
      backgroundColor: '#FF3B30',
      justifyContent: 'center',
      alignItems: 'center',
      margin: 8,
    },
    avatarText: {
      color: 'white',
      fontWeight: 'bold',
    },
    notificationBadge: {
      width: 24,
      height: 24,
      borderRadius: 12,                // 圆形徽章 | Circular badge
      backgroundColor: '#FF3B30',
      justifyContent: 'center',
      alignItems: 'center',
      position: 'absolute',            // 通常用于绝对定位 | Typically used with absolute positioning
      top: 8,
      right: 8,
    },
    badgeText: {
      color: 'white',
      fontSize: 10,
      fontWeight: 'bold',
    },
    topRoundedCard: {
      backgroundColor: 'white',
      padding: 16,
      margin: 8,
      borderTopLeftRadius: 16,         // 只圆化顶部左角 | Only round top left corner
      borderTopRightRadius: 16,        // 只圆化顶部右角 | Only round top right corner
      borderWidth: 1,
      borderColor: '#E5E5EA',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何创建一个椭圆形？| How to create an oval shape?
    **答案 | Answer:** 设置不同的width和height，borderRadius为较小值的一半 | Set different width and height, borderRadius to half of the smaller value
  - 如何让图片在圆角容器中不溢出？| How to prevent images from overflowing in rounded containers?
    **答案 | Answer:** 在容器上设置overflow: 'hidden' | Set overflow: 'hidden' on the container

  **常见误区检查 | Common Misconception Checks:**
  - borderRadius可以使用百分比吗？| Can borderRadius use percentages?
    **答案 | Answer:** 否 | No - React Native的borderRadius只接受像素值 | React Native borderRadius only accepts pixel values
  - 圆角会自动裁剪背景图片吗？| Does rounded corner automatically clip background images?
    **答案 | Answer:** 是 | Yes - 但可能需要overflow: 'hidden'来确保所有内容被裁剪 | Yes - but may need overflow: 'hidden' to ensure all content is clipped

### 5. 渐变背景与高级视觉效果 | Gradient Backgrounds and Advanced Visual Effects (45分钟 | 45 minutes)

- **Expo LinearGradient组件 | Expo LinearGradient Component**

  **概念定义 | Concept Definition:**
  React Native核心库不支持CSS渐变，需要使用expo-linear-gradient或react-native-linear-gradient等第三方库来实现渐变背景效果。LinearGradient组件允许创建线性渐变，支持多个颜色点和渐变方向控制。 | React Native core doesn't support CSS gradients. Third-party libraries like expo-linear-gradient or react-native-linear-gradient are needed to implement gradient backgrounds. LinearGradient component allows creating linear gradients with support for multiple color stops and gradient direction control.

  **核心特征 | Key Characteristics:**
  - colors：数组，定义渐变颜色序列 | colors: array, defines gradient color sequence
  - start和end：{ x: number, y: number }，控制渐变方向 | start and end: { x: number, y: number }, controls gradient direction
  - locations：可选数组，指定每个颜色的位置（0-1） | locations: optional array, specifies position of each color (0-1)
  - 可以作为容器使用，包含其他组件 | Can be used as container, wrapping other components
  - 支持从上到下、从左到右、对角线等各种方向 | Supports top-to-bottom, left-to-right, diagonal and various directions

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native核心库支持CSS渐变吗？| Does React Native core support CSS gradients?
     **答案 | Answer:** 否 | No - 必须使用第三方库如expo-linear-gradient | Must use third-party libraries like expo-linear-gradient
  2. LinearGradient的start和end值范围是什么？| What is the value range for LinearGradient start and end?
     **答案 | Answer:** 0到1 | 0 to 1 - {x: 0, y: 0}是左上角，{x: 1, y: 1}是右下角 | {x: 0, y: 0} is top-left, {x: 1, y: 1} is bottom-right
  3. colors数组必须包含几个颜色？| How many colors must the colors array contain?
     **答案 | Answer:** 至少2个 | At least 2 - 可以有更多颜色创建多色渐变 | Can have more colors to create multi-color gradients
  4. 如何创建从上到下的垂直渐变？| How to create a top-to-bottom vertical gradient?
     **答案 | Answer:** start={{x: 0, y: 0}}, end={{x: 0, y: 1}} | start={{x: 0, y: 0}}, end={{x: 0, y: 1}}

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 安装命令 | Installation command
  // npx expo install expo-linear-gradient

  // 渐变背景示例 | Gradient background examples
  import { StyleSheet, View, Text } from 'react-native';
  import { LinearGradient } from 'expo-linear-gradient';

  const GradientExamples = () => {
    return (
      <>
        {/* 垂直渐变 | Vertical gradient */}
        <LinearGradient
          colors={['#4c669f', '#3b5998', '#192f6a']}
          style={styles.verticalGradient}
          start={{ x: 0, y: 0 }}       // 顶部开始 | Start from top
          end={{ x: 0, y: 1 }}         // 底部结束 | End at bottom
        >
          <Text style={styles.gradientText}>垂直渐变 | Vertical Gradient</Text>
        </LinearGradient>

        {/* 水平渐变 | Horizontal gradient */}
        <LinearGradient
          colors={['#FF6B6B', '#FFE66D', '#4ECDC4']}
          style={styles.horizontalGradient}
          start={{ x: 0, y: 0 }}       // 左侧开始 | Start from left
          end={{ x: 1, y: 0 }}         // 右侧结束 | End at right
        >
          <Text style={styles.gradientText}>水平渐变 | Horizontal Gradient</Text>
        </LinearGradient>

        {/* 对角线渐变 | Diagonal gradient */}
        <LinearGradient
          colors={['#FA709A', '#FEE140']}
          style={styles.diagonalGradient}
          start={{ x: 0, y: 0 }}       // 左上角 | Top-left
          end={{ x: 1, y: 1 }}         // 右下角 | Bottom-right
        >
          <Text style={styles.gradientText}>对角线渐变 | Diagonal Gradient</Text>
        </LinearGradient>

        {/* 多色渐变带位置控制 | Multi-color gradient with location control */}
        <LinearGradient
          colors={['#667eea', '#764ba2', '#f093fb', '#f5576c']}
          locations={[0, 0.3, 0.7, 1]}  // 精确控制每个颜色的位置 | Precisely control each color position
          style={styles.multiColorGradient}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 1 }}
        >
          <Text style={styles.gradientText}>多色渐变 | Multi-Color Gradient</Text>
        </LinearGradient>

        {/* 渐变按钮 | Gradient button */}
        <LinearGradient
          colors={['#56CCF2', '#2F80ED']}
          style={styles.gradientButton}
          start={{ x: 0, y: 0 }}
          end={{ x: 1, y: 0 }}
        >
          <Text style={styles.buttonText}>渐变按钮 | Gradient Button</Text>
        </LinearGradient>
      </>
    );
  };

  const styles = StyleSheet.create({
    verticalGradient: {
      padding: 20,
      margin: 8,
      borderRadius: 8,
    },
    horizontalGradient: {
      padding: 20,
      margin: 8,
      borderRadius: 8,
    },
    diagonalGradient: {
      padding: 20,
      margin: 8,
      borderRadius: 8,
    },
    multiColorGradient: {
      padding: 20,
      margin: 8,
      borderRadius: 8,
    },
    gradientButton: {
      paddingVertical: 12,
      paddingHorizontal: 24,
      margin: 8,
      borderRadius: 25,
      alignItems: 'center',
    },
    gradientText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
      textAlign: 'center',
    },
    buttonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: 'bold',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何创建从左下到右上的对角线渐变？| How to create a diagonal gradient from bottom-left to top-right?
    **答案 | Answer:** start={{ x: 0, y: 1 }}, end={{ x: 1, y: 0 }} | start={{ x: 0, y: 1 }}, end={{ x: 1, y: 0 }}
  - locations数组的值必须按升序排列吗？| Must the locations array values be in ascending order?
    **答案 | Answer:** 是 | Yes - 值必须从0到1递增，否则渐变效果会异常 | Values must increase from 0 to 1, otherwise gradient will be abnormal

  **常见误区检查 | Common Misconception Checks:**
  - LinearGradient可以设置borderRadius吗？| Can LinearGradient have borderRadius?
    **答案 | Answer:** 是 | Yes - 在style中设置borderRadius，配合overflow: 'hidden' | Set borderRadius in style, combined with overflow: 'hidden'
  - 可以在LinearGradient上应用阴影吗？| Can shadows be applied to LinearGradient?
    **答案 | Answer:** 是 | Yes - 可以像普通View一样应用阴影属性 | Can apply shadow properties like a normal View

- **组合效果：渐变 + 阴影 + 圆角 | Combined Effects: Gradient + Shadow + Rounded**

  **概念定义 | Concept Definition:**
  通过组合渐变背景、阴影效果和圆角边框，可以创建现代化的、具有深度感的UI组件。这种组合是构建高质量移动应用界面的关键技巧，需要理解各个样式属性如何协同工作。 | By combining gradient backgrounds, shadow effects, and rounded borders, modern UI components with depth perception can be created. This combination is a key technique for building high-quality mobile app interfaces, requiring understanding of how various style properties work together.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 渐变和阴影可以同时应用吗？| Can gradients and shadows be applied simultaneously?
     **答案 | Answer:** 是 | Yes - LinearGradient组件可以有阴影样式 | LinearGradient component can have shadow styles
  2. 如何确保圆角正确裁剪渐变边缘？| How to ensure rounded corners correctly clip gradient edges?
     **答案 | Answer:** 设置overflow: 'hidden' | Set overflow: 'hidden'
  3. 渐变卡片的阴影应该设置在哪里？| Where should shadows be set for gradient cards?
     **答案 | Answer:** LinearGradient组件的style属性中 | In the style prop of LinearGradient component

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 组合效果示例 | Combined effects example
  import { StyleSheet, View, Text, Platform } from 'react-native';
  import { LinearGradient } from 'expo-linear-gradient';

  const ModernCard = () => {
    return (
      <LinearGradient
        colors={['#667eea', '#764ba2']}
        start={{ x: 0, y: 0 }}
        end={{ x: 1, y: 1 }}
        style={styles.modernCard}          // 组合所有效果 | Combine all effects
      >
        <Text style={styles.cardTitle}>现代卡片设计 | Modern Card Design</Text>
        <Text style={styles.cardDescription}>
          渐变背景 + 阴影 + 圆角 | Gradient + Shadow + Rounded
        </Text>
      </LinearGradient>
    );
  };

  const styles = StyleSheet.create({
    modernCard: {
      margin: 16,
      padding: 20,
      borderRadius: 16,                // 圆角 | Rounded corners
      overflow: 'hidden',              // 确保内容被圆角裁剪 | Ensure content is clipped by rounded corners
      // 跨平台阴影 | Cross-platform shadow
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: {
            width: 0,
            height: 4,
          },
          shadowOpacity: 0.3,
          shadowRadius: 6,
        },
        android: {
          elevation: 8,
        },
      }),
    },
    cardTitle: {
      color: 'white',
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 8,
    },
    cardDescription: {
      color: 'rgba(255, 255, 255, 0.9)',
      fontSize: 14,
    },
  });
  ```

### 6. 实践整合：构建现代卡片组件 | Practical Integration: Building Modern Card Component (45分钟 | 45 minutes)

- **综合卡片设计系统 | Comprehensive Card Design System**

  **概念定义 | Concept Definition:**
  现代卡片组件是移动应用中最常用的UI元素之一，它综合运用了阴影、边框、圆角、间距等多种样式技巧。一个好的卡片设计系统应该包含多个变体（简单卡片、渐变卡片、图片卡片等），并确保在iOS和Android上都有良好的视觉效果。 | Modern card components are one of the most commonly used UI elements in mobile apps, comprehensively utilizing various styling techniques including shadows, borders, rounded corners, and spacing. A good card design system should include multiple variants (simple cards, gradient cards, image cards, etc.) and ensure good visual effects on both iOS and Android.

  **核心特征 | Key Characteristics:**
  - 响应式间距和内边距 | Responsive spacing and padding
  - 跨平台一致的阴影效果 | Cross-platform consistent shadow effects
  - 合理的视觉层次和对比度 | Proper visual hierarchy and contrast
  - 可复用的样式模式 | Reusable style patterns
  - 支持不同的内容类型（文本、图片、按钮等）| Support for different content types (text, images, buttons, etc.)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 卡片组件通常需要哪些基本样式？| What basic styles do card components typically need?
     **答案 | Answer:** 背景色、内边距、圆角、阴影、外边距 | Background color, padding, rounded corners, shadow, margin
  2. 如何让卡片在iOS和Android上看起来一致？| How to make cards look consistent on iOS and Android?
     **答案 | Answer:** 使用Platform.select()或自定义阴影工具 | Use Platform.select() or custom shadow utilities
  3. 卡片内的图片应该如何处理圆角？| How should images inside cards handle rounded corners?
     **答案 | Answer:** 在图片容器上设置borderRadius和overflow: 'hidden' | Set borderRadius and overflow: 'hidden' on image container
  4. 什么是卡片设计的"提升层级"概念？| What is the "elevation level" concept in card design?
     **答案 | Answer:** 通过阴影深度表示UI元素的重要性和层次关系 | Using shadow depth to indicate UI element importance and hierarchical relationships

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 完整的卡片组件系统 | Complete card component system
  import React from 'react';
  import { StyleSheet, View, Text, Image, TouchableOpacity, Platform } from 'react-native';
  import { LinearGradient } from 'expo-linear-gradient';

  // 1. 基础卡片组件 | Basic Card Component
  const BasicCard = ({ title, description }) => {
    return (
      <View style={[styles.baseCard, styles.shadow]}>
        <Text style={styles.cardTitle}>{title}</Text>
        <Text style={styles.cardDescription}>{description}</Text>
      </View>
    );
  };

  // 2. 图片卡片组件 | Image Card Component
  const ImageCard = ({ image, title, description }) => {
    return (
      <View style={[styles.baseCard, styles.shadow]}>
        <View style={styles.imageContainer}>
          <Image source={image} style={styles.cardImage} resizeMode="cover" />
        </View>
        <View style={styles.cardContent}>
          <Text style={styles.cardTitle}>{title}</Text>
          <Text style={styles.cardDescription}>{description}</Text>
        </View>
      </View>
    );
  };

  // 3. 渐变卡片组件 | Gradient Card Component
  const GradientCard = ({ title, description, colors }) => {
    return (
      <LinearGradient
        colors={colors || ['#667eea', '#764ba2']}
        start={{ x: 0, y: 0 }}
        end={{ x: 1, y: 1 }}
        style={[styles.baseCard, styles.shadow]}
      >
        <Text style={[styles.cardTitle, styles.whiteText]}>{title}</Text>
        <Text style={[styles.cardDescription, styles.whiteText]}>{description}</Text>
      </LinearGradient>
    );
  };

  // 4. 可点击的行动卡片 | Actionable Card Component
  const ActionCard = ({ title, description, buttonText, onPress }) => {
    return (
      <View style={[styles.baseCard, styles.shadow]}>
        <Text style={styles.cardTitle}>{title}</Text>
        <Text style={styles.cardDescription}>{description}</Text>
        <TouchableOpacity style={styles.cardButton} onPress={onPress}>
          <Text style={styles.buttonText}>{buttonText}</Text>
        </TouchableOpacity>
      </View>
    );
  };

  // 5. 统计卡片组件 | Stats Card Component
  const StatsCard = ({ value, label, icon, trend }) => {
    return (
      <View style={[styles.statsCard, styles.shadowSmall]}>
        <View style={styles.statsHeader}>
          <Text style={styles.statsValue}>{value}</Text>
          <Text style={[styles.trend, trend > 0 ? styles.positive : styles.negative]}>
            {trend > 0 ? '↑' : '↓'} {Math.abs(trend)}%
          </Text>
        </View>
        <Text style={styles.statsLabel}>{label}</Text>
      </View>
    );
  };

  // 使用示例 | Usage Example
  const CardShowcase = () => {
    return (
      <View style={styles.container}>
        <BasicCard
          title="基础卡片 | Basic Card"
          description="简单优雅的卡片设计，带有阴影和圆角 | Simple elegant card with shadow and rounded corners"
        />

        <ImageCard
          image={require('./assets/sample.jpg')}
          title="图片卡片 | Image Card"
          description="包含图片的卡片布局 | Card layout with image"
        />

        <GradientCard
          title="渐变卡片 | Gradient Card"
          description="使用渐变背景的现代卡片 | Modern card with gradient background"
          colors={['#FA709A', '#FEE140']}
        />

        <ActionCard
          title="行动卡片 | Action Card"
          description="包含可点击按钮的交互卡片 | Interactive card with clickable button"
          buttonText="了解更多 | Learn More"
          onPress={() => console.log('Card action pressed')}
        />

        <View style={styles.statsRow}>
          <StatsCard value="2.4K" label="用户 | Users" trend={12} />
          <StatsCard value="86%" label="增长 | Growth" trend={-3} />
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#F5F5F5',
      padding: 16,
    },
    // 基础卡片样式 | Base card styles
    baseCard: {
      backgroundColor: 'white',
      borderRadius: 12,
      padding: 16,
      marginBottom: 16,
      overflow: 'hidden',               // 确保圆角生效 | Ensure rounded corners work
    },
    // 阴影样式 - 中等强度 | Shadow styles - medium intensity
    shadow: {
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.25,
          shadowRadius: 3.84,
        },
        android: {
          elevation: 4,
        },
      }),
    },
    // 阴影样式 - 轻微强度 | Shadow styles - subtle intensity
    shadowSmall: {
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 1 },
          shadowOpacity: 0.18,
          shadowRadius: 1.5,
        },
        android: {
          elevation: 2,
        },
      }),
    },
    // 卡片内容样式 | Card content styles
    cardTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      color: '#1A1A1A',
      marginBottom: 8,
    },
    cardDescription: {
      fontSize: 14,
      color: '#666666',
      lineHeight: 20,
    },
    whiteText: {
      color: 'white',
    },
    // 图片卡片样式 | Image card styles
    imageContainer: {
      width: '100%',
      height: 180,
      marginBottom: 12,
      borderRadius: 8,
      overflow: 'hidden',               // 裁剪图片圆角 | Clip image rounded corners
    },
    cardImage: {
      width: '100%',
      height: '100%',
    },
    cardContent: {
      padding: 4,
    },
    // 按钮样式 | Button styles
    cardButton: {
      backgroundColor: '#007AFF',
      borderRadius: 8,
      paddingVertical: 12,
      paddingHorizontal: 24,
      marginTop: 12,
      alignItems: 'center',
    },
    buttonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    // 统计卡片样式 | Stats card styles
    statsCard: {
      backgroundColor: 'white',
      borderRadius: 12,
      padding: 16,
      flex: 1,
      marginHorizontal: 4,
    },
    statsRow: {
      flexDirection: 'row',
      marginBottom: 16,
    },
    statsHeader: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      marginBottom: 8,
    },
    statsValue: {
      fontSize: 28,
      fontWeight: 'bold',
      color: '#1A1A1A',
    },
    statsLabel: {
      fontSize: 14,
      color: '#666666',
    },
    trend: {
      fontSize: 14,
      fontWeight: '600',
    },
    positive: {
      color: '#34C759',
    },
    negative: {
      color: '#FF3B30',
    },
  });

  export default CardShowcase;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么图片容器需要overflow: 'hidden'？| Why does the image container need overflow: 'hidden'?
    **答案 | Answer:** 确保图片被borderRadius正确裁剪 | Ensures image is correctly clipped by borderRadius
  - 如何创建卡片的悬浮效果（按压时）？| How to create a hover effect for cards (on press)?
    **答案 | Answer:** 使用TouchableOpacity包装，或在按压状态改变阴影强度 | Wrap with TouchableOpacity, or change shadow intensity on press state
  - 统计卡片中的flex: 1有什么作用？| What does flex: 1 do in stats cards?
    **答案 | Answer:** 让多个统计卡片平均分配父容器宽度 | Makes multiple stats cards evenly distribute parent container width

  **常见误区检查 | Common Misconception Checks:**
  - 所有卡片都需要相同的阴影强度吗？| Do all cards need the same shadow intensity?
    **答案 | Answer:** 否 | No - 根据重要性和UI层级使用不同强度的阴影 | Use different shadow intensities based on importance and UI hierarchy
  - LinearGradient可以直接包含Image吗？| Can LinearGradient directly contain an Image?
    **答案 | Answer:** 是 | Yes - LinearGradient可以像View一样包含任何子组件 | LinearGradient can contain any child components like a View

## 实践项目：精美卡片库展示应用 | Practical Project: Beautiful Card Gallery App

### 目标 | Objective
创建一个展示各种卡片设计的移动应用，综合运用本日所学的阴影、边框、圆角和渐变技巧。应用需要包含至少5种不同类型的卡片组件，在iOS和Android上都能完美显示，并展示现代移动UI设计的最佳实践。 | Create a mobile app showcasing various card designs, comprehensively applying today's learned techniques of shadows, borders, rounded corners, and gradients. The app needs to include at least 5 different types of card components, display perfectly on both iOS and Android, and demonstrate best practices in modern mobile UI design.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. iOS阴影和Android elevation的区别是什么？如何实现跨平台一致的阴影效果？| What's the difference between iOS shadows and Android elevation? How to achieve cross-platform consistent shadow effects?
   **答案 | Answer:** iOS使用shadowColor、shadowOffset、shadowOpacity、shadowRadius四个属性；Android使用单一的elevation属性。使用Platform.select()或自定义阴影工具函数来实现跨平台一致性。 | iOS uses four properties: shadowColor, shadowOffset, shadowOpacity, shadowRadius; Android uses single elevation property. Use Platform.select() or custom shadow utility functions to achieve cross-platform consistency.

2. 如何创建一个完美的圆形元素？| How to create a perfect circular element?
   **答案 | Answer:** 设置相等的width和height，borderRadius设置为宽高值的一半。 | Set equal width and height, set borderRadius to half of the width/height value.

3. React Native支持CSS渐变吗？如果不支持，如何实现渐变背景？| Does React Native support CSS gradients? If not, how to implement gradient backgrounds?
   **答案 | Answer:** 不支持。需要使用第三方库如expo-linear-gradient，通过LinearGradient组件实现。 | No. Need to use third-party libraries like expo-linear-gradient, implement through LinearGradient component.

### 步骤 | Steps

1. **环境准备 | Environment Setup**
   - 创建新的Expo项目或在现有项目中新建组件 | Create new Expo project or create new component in existing project
   - 安装expo-linear-gradient：`npx expo install expo-linear-gradient` | Install expo-linear-gradient: `npx expo install expo-linear-gradient`
   - 创建项目文件结构：components/, styles/, assets/ | Create project file structure: components/, styles/, assets/

2. **创建可复用的阴影工具 | Create Reusable Shadow Utilities**
   - 在styles/shadows.js中实现阴影生成函数 | Implement shadow generator function in styles/shadows.js
   - 定义small、medium、large、extraLarge四个阴影层级 | Define four shadow levels: small, medium, large, extraLarge
   - 确保iOS和Android平台的视觉一致性 | Ensure visual consistency across iOS and Android platforms

3. **实现5种卡片组件 | Implement 5 Card Component Types**
   - 基础信息卡片（Basic Info Card）：白色背景、阴影、圆角 | Basic Info Card: white background, shadow, rounded corners
   - 图片卡片（Image Card）：包含图片、标题、描述 | Image Card: contains image, title, description
   - 渐变卡片（Gradient Card）：使用LinearGradient | Gradient Card: uses LinearGradient
   - 统计卡片（Stats Card）：显示数据和趋势 | Stats Card: displays data and trends
   - 操作卡片（Action Card）：包含按钮的交互卡片 | Action Card: interactive card with button

4. **构建主应用界面 | Build Main App Interface**
   - 使用ScrollView创建可滚动的卡片画廊 | Use ScrollView to create scrollable card gallery
   - 添加标题和分类标签 | Add titles and category labels
   - 实现卡片点击交互（可选）| Implement card tap interaction (optional)

5. **添加高级视觉效果 | Add Advanced Visual Effects**
   - 为特定卡片添加多色边框 | Add multi-color borders to specific cards
   - 创建组合效果：渐变+阴影+圆角 | Create combined effects: gradient + shadow + rounded corners
   - 使用不同的borderRadius值创建视觉变化 | Use different borderRadius values to create visual variety

### 示例代码 | Example Code

```javascript
/**
 * 精美卡片库展示应用 | Beautiful Card Gallery App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 跨平台阴影系统（iOS shadowXXX vs Android elevation）| Cross-platform shadow system (iOS shadowXXX vs Android elevation)
 * - 边框样式和圆角设计 | Border styles and rounded corner design
 * - 渐变背景实现 | Gradient background implementation
 * - 现代卡片组件设计模式 | Modern card component design patterns
 */

// App.js - 主应用文件 | Main app file
import React from 'react';
import { StyleSheet, View, Text, ScrollView, SafeAreaView, StatusBar } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';

// 导入可复用的阴影工具 | Import reusable shadow utilities
import { shadows } from './styles/shadows';

// 导入卡片组件 | Import card components
import BasicCard from './components/BasicCard';
import ImageCard from './components/ImageCard';
import GradientCard from './components/GradientCard';
import StatsCard from './components/StatsCard';
import ActionCard from './components/ActionCard';

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <StatusBar barStyle="dark-content" />

      {/* 应用标题 | App header */}
      <View style={styles.header}>
        <Text style={styles.title}>精美卡片库 | Card Gallery</Text>
        <Text style={styles.subtitle}>探索现代移动UI设计 | Explore Modern Mobile UI</Text>
      </View>

      <ScrollView style={styles.scrollView} showsVerticalScrollIndicator={false}>
        {/* 基础卡片部分 | Basic cards section */}
        <Text style={styles.sectionTitle}>基础卡片 | Basic Cards</Text>
        <BasicCard
          title="简洁设计 | Clean Design"
          description="使用标准阴影和圆角创建优雅的基础卡片 | Create elegant basic cards with standard shadow and rounded corners"
        />

        {/* 图片卡片部分 | Image cards section */}
        <Text style={styles.sectionTitle}>图片卡片 | Image Cards</Text>
        <ImageCard
          image={require('./assets/sample-1.jpg')}
          title="风景摄影 | Landscape Photography"
          description="精美图片与信息的完美结合 | Perfect combination of beautiful images and information"
        />

        {/* 渐变卡片部分 | Gradient cards section */}
        <Text style={styles.sectionTitle}>渐变卡片 | Gradient Cards</Text>
        <GradientCard
          title="现代风格 | Modern Style"
          description="使用线性渐变创建视觉冲击力 | Create visual impact with linear gradients"
          colors={['#667eea', '#764ba2']}
        />
        <GradientCard
          title="活力配色 | Vibrant Colors"
          description="多色渐变展示创意可能 | Multi-color gradients show creative possibilities"
          colors={['#FA709A', '#FEE140', '#30CFD0']}
        />

        {/* 统计卡片部分 | Stats cards section */}
        <Text style={styles.sectionTitle}>数据卡片 | Stats Cards</Text>
        <View style={styles.statsRow}>
          <StatsCard value="12.5K" label="下载量 | Downloads" trend={23} />
          <StatsCard value="4.8" label="评分 | Rating" trend={5} />
        </View>
        <View style={styles.statsRow}>
          <StatsCard value="89%" label="活跃度 | Active" trend={-2} />
          <StatsCard value="2.3K" label="评论 | Reviews" trend={15} />
        </View>

        {/* 操作卡片部分 | Action cards section */}
        <Text style={styles.sectionTitle}>交互卡片 | Interactive Cards</Text>
        <ActionCard
          title="开始学习 | Start Learning"
          description="点击按钮开始你的React Native之旅 | Tap button to start your React Native journey"
          buttonText="立即开始 | Get Started"
          onPress={() => alert('开始学习！| Start Learning!')}
        />

        {/* 组合效果演示 | Combined effects demo */}
        <Text style={styles.sectionTitle}>高级效果 | Advanced Effects</Text>
        <View style={styles.premiumCard}>
          <LinearGradient
            colors={['#1e3c72', '#2a5298']}
            start={{ x: 0, y: 0 }}
            end={{ x: 1, y: 1 }}
            style={styles.premiumGradient}
          >
            <Text style={styles.premiumTitle}>专业版卡片 | Premium Card</Text>
            <Text style={styles.premiumDescription}>
              渐变背景 + 深度阴影 + 多色边框 | Gradient + Deep Shadow + Multi-color Border
            </Text>
          </LinearGradient>
        </View>

        {/* 底部间距 | Bottom spacing */}
        <View style={styles.bottomSpacer} />
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F5F7FA',
  },
  header: {
    padding: 20,
    paddingTop: 10,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#1A1A1A',
    marginBottom: 4,
  },
  subtitle: {
    fontSize: 14,
    color: '#666666',
  },
  scrollView: {
    flex: 1,
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: '600',
    color: '#1A1A1A',
    marginTop: 24,
    marginBottom: 12,
    marginLeft: 16,
  },
  statsRow: {
    flexDirection: 'row',
    paddingHorizontal: 16,
    marginBottom: 12,
  },
  // 专业版卡片样式 - 组合多种效果 | Premium card styles - combining multiple effects
  premiumCard: {
    marginHorizontal: 16,
    marginBottom: 16,
    borderRadius: 16,
    // 深度阴影 | Deep shadow
    ...shadows.extraLarge,
    // 多色边框效果 | Multi-color border effect
    borderWidth: 3,
    borderTopColor: '#667eea',
    borderRightColor: '#764ba2',
    borderBottomColor: '#f093fb',
    borderLeftColor: '#4facfe',
  },
  premiumGradient: {
    padding: 24,
    borderRadius: 13,              // 略小于外层borderRadius以显示边框 | Slightly smaller than outer borderRadius to show border
    overflow: 'hidden',
  },
  premiumTitle: {
    fontSize: 22,
    fontWeight: 'bold',
    color: 'white',
    marginBottom: 8,
  },
  premiumDescription: {
    fontSize: 14,
    color: 'rgba(255, 255, 255, 0.9)',
  },
  bottomSpacer: {
    height: 40,
  },
});

// styles/shadows.js - 阴影工具文件 | Shadow utilities file
import { Platform } from 'react-native';

const createShadow = (elevation) => {
  if (Platform.OS === 'ios') {
    const shadowMap = {
      1: { height: 1, radius: 1, opacity: 0.18 },
      2: { height: 1, radius: 2, opacity: 0.20 },
      3: { height: 2, radius: 3, opacity: 0.22 },
      4: { height: 2, radius: 4, opacity: 0.25 },
      6: { height: 3, radius: 5, opacity: 0.27 },
      8: { height: 4, radius: 6, opacity: 0.30 },
      12: { height: 6, radius: 8, opacity: 0.35 },
    };

    const shadow = shadowMap[elevation] || shadowMap[1];

    return {
      shadowColor: '#000',
      shadowOffset: { width: 0, height: shadow.height },
      shadowOpacity: shadow.opacity,
      shadowRadius: shadow.radius,
    };
  }

  return { elevation };
};

export const shadows = {
  none: {},
  small: createShadow(2),
  medium: createShadow(4),
  large: createShadow(6),
  extraLarge: createShadow(8),
  maximum: createShadow(12),
};

// components/BasicCard.js - 基础卡片组件 | Basic card component
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';
import { shadows } from '../styles/shadows';

const BasicCard = ({ title, description }) => {
  return (
    <View style={[styles.card, shadows.medium]}>
      <Text style={styles.title}>{title}</Text>
      <Text style={styles.description}>{description}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    padding: 16,
    marginHorizontal: 16,
    marginBottom: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#1A1A1A',
    marginBottom: 8,
  },
  description: {
    fontSize: 14,
    color: '#666666',
    lineHeight: 20,
  },
});

export default BasicCard;

// components/ImageCard.js - 图片卡片组件 | Image card component
import React from 'react';
import { StyleSheet, View, Text, Image } from 'react-native';
import { shadows } from '../styles/shadows';

const ImageCard = ({ image, title, description }) => {
  return (
    <View style={[styles.card, shadows.medium]}>
      <View style={styles.imageContainer}>
        <Image source={image} style={styles.image} resizeMode="cover" />
      </View>
      <View style={styles.content}>
        <Text style={styles.title}>{title}</Text>
        <Text style={styles.description}>{description}</Text>
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    marginHorizontal: 16,
    marginBottom: 16,
    overflow: 'hidden',
  },
  imageContainer: {
    width: '100%',
    height: 200,
    overflow: 'hidden',
  },
  image: {
    width: '100%',
    height: '100%',
  },
  content: {
    padding: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#1A1A1A',
    marginBottom: 8,
  },
  description: {
    fontSize: 14,
    color: '#666666',
    lineHeight: 20,
  },
});

export default ImageCard;

// components/GradientCard.js - 渐变卡片组件 | Gradient card component
import React from 'react';
import { StyleSheet, Text } from 'react-native';
import { LinearGradient } from 'expo-linear-gradient';
import { shadows } from '../styles/shadows';

const GradientCard = ({ title, description, colors }) => {
  return (
    <LinearGradient
      colors={colors || ['#667eea', '#764ba2']}
      start={{ x: 0, y: 0 }}
      end={{ x: 1, y: 1 }}
      style={[styles.card, shadows.medium]}
    >
      <Text style={styles.title}>{title}</Text>
      <Text style={styles.description}>{description}</Text>
    </LinearGradient>
  );
};

const styles = StyleSheet.create({
  card: {
    borderRadius: 12,
    padding: 20,
    marginHorizontal: 16,
    marginBottom: 16,
    overflow: 'hidden',
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: 'white',
    marginBottom: 8,
  },
  description: {
    fontSize: 14,
    color: 'rgba(255, 255, 255, 0.9)',
    lineHeight: 20,
  },
});

export default GradientCard;

// components/StatsCard.js - 统计卡片组件 | Stats card component
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';
import { shadows } from '../styles/shadows';

const StatsCard = ({ value, label, trend }) => {
  const isPositive = trend > 0;

  return (
    <View style={[styles.card, shadows.small]}>
      <View style={styles.header}>
        <Text style={styles.value}>{value}</Text>
        <Text style={[styles.trend, isPositive ? styles.positive : styles.negative]}>
          {isPositive ? '↑' : '↓'} {Math.abs(trend)}%
        </Text>
      </View>
      <Text style={styles.label}>{label}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    padding: 16,
    flex: 1,
    marginHorizontal: 4,
  },
  header: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 8,
  },
  value: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#1A1A1A',
  },
  label: {
    fontSize: 14,
    color: '#666666',
  },
  trend: {
    fontSize: 14,
    fontWeight: '600',
  },
  positive: {
    color: '#34C759',
  },
  negative: {
    color: '#FF3B30',
  },
});

export default StatsCard;

// components/ActionCard.js - 操作卡片组件 | Action card component
import React from 'react';
import { StyleSheet, View, Text, TouchableOpacity } from 'react-native';
import { shadows } from '../styles/shadows';

const ActionCard = ({ title, description, buttonText, onPress }) => {
  return (
    <View style={[styles.card, shadows.medium]}>
      <Text style={styles.title}>{title}</Text>
      <Text style={styles.description}>{description}</Text>
      <TouchableOpacity style={styles.button} onPress={onPress}>
        <Text style={styles.buttonText}>{buttonText}</Text>
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    padding: 16,
    marginHorizontal: 16,
    marginBottom: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#1A1A1A',
    marginBottom: 8,
  },
  description: {
    fontSize: 14,
    color: '#666666',
    lineHeight: 20,
    marginBottom: 16,
  },
  button: {
    backgroundColor: '#007AFF',
    borderRadius: 8,
    paddingVertical: 12,
    alignItems: 'center',
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '600',
  },
});

export default ActionCard;
```

### 项目完成检查 | Project Completion Check

1. 项目是否在iOS和Android上都能正确显示阴影？| Does the project correctly display shadows on both iOS and Android?
2. 所有卡片的圆角是否生效，图片是否正确裁剪？| Do all cards have working rounded corners, are images correctly clipped?
3. 渐变卡片是否能正确显示渐变背景？| Do gradient cards correctly display gradient backgrounds?
4. 代码是否使用了可复用的阴影工具而不是重复定义？| Does the code use reusable shadow utilities instead of repetitive definitions?
5. 各种卡片类型是否展示了不同的设计模式和使用场景？| Do different card types showcase different design patterns and use cases?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **阴影深度理解练习 | Shadow Depth Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建一个包含5个卡片的页面，每个卡片使用不同的阴影深度（从最浅到最深），观察并记录视觉差异。
   - **概念检查 | Concept Check:** 阴影深度如何影响UI元素的视觉层级？ | How does shadow depth affect visual hierarchy of UI elements?
   - **学习目标 | Learning Objective:** 深入理解阴影在建立视觉层次中的作用。 | Deeply understand the role of shadows in establishing visual hierarchy.

2. **跨平台样式统一练习 | Cross-Platform Style Consistency Exercise**
   - **练习描述 | Exercise Description:** 创建一个卡片组件，使用条件样式确保在iOS和Android上看起来尽可能一致，然后在两个平台上截图对比。
   - **概念检查 | Concept Check:** Platform.select()和条件判断哪个更适合处理样式差异？ | Which is better for handling style differences: Platform.select() or conditional statements?
   - **学习目标 | Learning Objective:** 掌握跨平台样式一致性的实现技巧。 | Master techniques for achieving cross-platform style consistency.

3. **圆角设计模式练习 | Rounded Corner Design Pattern Exercise**
   - **练习描述 | Exercise Description:** 设计一个界面，使用不同的borderRadius值（4px, 8px, 12px, 16px, 20px, 50%）创建各种UI元素，形成统一的设计语言。
   - **概念检查 | Concept Check:** 不同的圆角半径适合哪些类型的UI元素？ | What types of UI elements are suitable for different corner radius values?
   - **学习目标 | Learning Objective:** 建立圆角设计的系统性认知。 | Develop systematic understanding of rounded corner design.

4. **渐变配色练习 | Gradient Color Scheme Exercise**
   - **练习描述 | Exercise Description:** 研究Material Design和iOS设计指南中的渐变使用，创建3种不同情绪（平静、活力、专业）的渐变配色方案。
   - **概念检查 | Concept Check:** 如何通过渐变方向和颜色选择传达不同的视觉情绪？ | How to convey different visual moods through gradient direction and color selection?
   - **学习目标 | Learning Objective:** 提高渐变配色的设计能力。 | Improve gradient color scheme design skills.

5. **边框创意应用练习 | Creative Border Application Exercise**
   - **练习描述 | Exercise Description:** 使用边框属性创建至少3种创意效果：分隔线、装饰边框、强调边框等。
   - **概念检查 | Concept Check:** 如何通过单边边框和多色边框创建视觉亮点？ | How to create visual highlights through single-side borders and multi-color borders?
   - **学习目标 | Learning Objective:** 发展边框属性的创新应用能力。 | Develop innovative application skills for border properties.

6. **性能优化练习 | Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 创建一个包含50个卡片的长列表，测试使用StyleSheet.create()和内联样式的性能差异。
   - **概念检查 | Concept Check:** 为什么StyleSheet.create()比内联样式性能更好？ | Why is StyleSheet.create() better performing than inline styles?
   - **学习目标 | Learning Objective:** 理解样式性能优化的重要性。 | Understand the importance of style performance optimization.

7. **设计系统构建练习 | Design System Building Exercise**
   - **练习描述 | Exercise Description:** 基于今天的卡片组件，扩展创建一个完整的设计系统，包括按钮、输入框、标签等组件，保持统一的视觉风格。
   - **概念检查 | Concept Check:** 如何确保设计系统中所有组件的视觉一致性？ | How to ensure visual consistency across all components in a design system?
   - **学习目标 | Learning Objective:** 提高设计系统思维和组件库构建能力。 | Improve design system thinking and component library building skills.

## 学习资源 | Learning Resources

- [React Native StyleSheet API 官方文档 | React Native StyleSheet API Official Docs](https://reactnative.dev/docs/stylesheet)
- [React Native Platform 模块文档 | React Native Platform Module Docs](https://reactnative.dev/docs/platform-specific-code)
- [Expo LinearGradient 文档 | Expo LinearGradient Docs](https://docs.expo.dev/versions/latest/sdk/linear-gradient/)
- [Material Design - Elevation & Shadows](https://m3.material.io/styles/elevation/overview)
- [iOS Human Interface Guidelines - Visual Design](https://developer.apple.com/design/human-interface-guidelines/foundations/layout)
- [React Native Shadow Generator](https://ethercreative.github.io/react-native-shadow-generator/) - 在线阴影生成工具 | Online shadow generator tool
- [Gradient Hunt](https://gradienthunt.com/) - 渐变配色灵感 | Gradient color inspiration
- [跨平台样式最佳实践 | Cross-Platform Styling Best Practices](https://thoughtbot.com/blog/structure-for-styling-in-react-native)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解iOS阴影属性（shadowColor, shadowOffset, shadowOpacity, shadowRadius）| Understand iOS shadow properties (shadowColor, shadowOffset, shadowOpacity, shadowRadius)
- [ ] 理解Android elevation属性及其与iOS阴影的差异 | Understand Android elevation property and its differences from iOS shadows
- [ ] 能够使用Platform.select()创建跨平台一致的阴影效果 | Can use Platform.select() to create cross-platform consistent shadow effects
- [ ] 掌握边框属性系统（borderWidth, borderColor, borderStyle）| Master border property system (borderWidth, borderColor, borderStyle)
- [ ] 能够创建圆角和圆形UI元素 | Can create rounded corner and circular UI elements
- [ ] 理解并能使用LinearGradient创建渐变背景 | Understand and can use LinearGradient to create gradient backgrounds
- [ ] 创建了可复用的阴影工具函数或常量 | Created reusable shadow utility functions or constants
- [ ] 完成精美卡片库展示应用项目 | Completed Beautiful Card Gallery App project
- [ ] 所有卡片在iOS和Android上视觉效果一致 | All cards have consistent visual effects on iOS and Android
- [ ] 至少完成3个扩展练习 | Completed at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释iOS和Android阴影系统的差异、边框和圆角的应用技巧、以及如何创建跨平台一致的现代UI组件。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others the differences between iOS and Android shadow systems, border and rounded corner application techniques, and how to create cross-platform consistent modern UI components.
