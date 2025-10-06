# React Native入门 - 第3周第3天：定位与尺寸 | React Native Introduction - Week 3 Day 3: Positioning and Dimensions

## 学习目标 | Learning Objectives
- 掌握React Native中的绝对定位和相对定位 | Master absolute and relative positioning in React Native
- 理解position属性与web开发的区别 | Understand differences of position property from web development
- 学会使用Dimensions API获取设备屏幕尺寸 | Learn to use Dimensions API to get device screen dimensions
- 能够创建基于百分比的响应式布局 | Be able to create percentage-based responsive layouts
- 掌握叠加元素和浮动按钮的实现技巧 | Master techniques for implementing overlay elements and floating action buttons
- 理解React Native中没有fixed定位的替代方案 | Understand alternatives to fixed positioning which doesn't exist in React Native

## 详细内容 | Detailed Content

### 1. Position属性基础 | Position Property Fundamentals (1小时 | 1 hour)

- **相对定位 vs 绝对定位 | Relative vs Absolute Positioning**

  **概念定义 | Concept Definition:**
  在React Native中，position属性只有两个值：'relative'（默认）和'absolute'。relative定位的元素仍在正常文档流中，而absolute定位的元素会脱离文档流并相对于其最近的非static父元素定位。 | In React Native, the position property has only two values: 'relative' (default) and 'absolute'. Relatively positioned elements remain in the normal document flow, while absolutely positioned elements are removed from the document flow and positioned relative to their nearest non-static parent element.

  **核心特征 | Key Characteristics:**
  - React Native没有'static'、'fixed'或'sticky'定位 | React Native doesn't have 'static', 'fixed', or 'sticky' positioning
  - 默认值是'relative'，但通常不需要显式设置 | Default value is 'relative', but usually doesn't need to be explicitly set
  - absolute定位的元素使用top、right、bottom、left来定位 | Absolutely positioned elements use top, right, bottom, left for positioning
  - absolute定位相对于父容器，不是相对于viewport | Absolute positioning is relative to parent container, not viewport
  - 没有z-index的概念，后渲染的元素在上层 | No z-index concept, later rendered elements appear on top

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native支持CSS中的position: fixed吗？| Does React Native support position: fixed from CSS?
     **答案 | Answer:** 否 | No - React Native只支持relative和absolute两种定位方式 | React Native only supports relative and absolute positioning
  2. 绝对定位的元素会占用文档流中的空间吗？| Do absolutely positioned elements take up space in the document flow?
     **答案 | Answer:** 否 | No - 绝对定位元素脱离文档流，不占用空间 | Absolutely positioned elements are removed from document flow and don't take up space
  3. 在React Native中，绝对定位是相对于什么元素定位的？| In React Native, what is absolute positioning relative to?
     **答案 | Answer:** 相对于最近的父容器 | Relative to the nearest parent container - 通常是直接父View | typically the direct parent View
  4. 后渲染的元素会出现在先渲染元素的上方还是下方？| Do later rendered elements appear above or below earlier rendered elements?
     **答案 | Answer:** 上方 | Above - React Native使用渲染顺序而非z-index来控制层叠顺序 | React Native uses render order instead of z-index to control stacking order

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  const PositionExample = () => {
    return (
      <View style={styles.container}>
        {/* 相对定位（默认）| Relative positioning (default) */}
        <View style={styles.relativeBox}>
          <Text>相对定位 | Relative</Text>
        </View>

        {/* 绝对定位 | Absolute positioning */}
        <View style={styles.absoluteBox}>
          <Text>绝对定位 | Absolute</Text>
        </View>

        {/* 这个盒子会显示在绝对定位盒子下方，因为绝对定位不占用空间 */}
        {/* This box appears below the relative box because absolute doesn't take space */}
        <View style={styles.relativeBox}>
          <Text>另一个相对定位 | Another Relative</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f0f0f0',
    },
    relativeBox: {
      backgroundColor: '#4A90E2',
      padding: 15,
      marginBottom: 10,
      borderRadius: 8,
    },
    absoluteBox: {
      position: 'absolute', // 脱离文档流 | Removed from document flow
      top: 100, // 距离父容器顶部100像素 | 100 pixels from parent top
      right: 20, // 距离父容器右侧20像素 | 20 pixels from parent right
      backgroundColor: '#E94B3C',
      padding: 15,
      borderRadius: 8,
    },
  });

  export default PositionExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果移除absoluteBox的position: 'absolute'，它会出现在哪里？| If you remove position: 'absolute' from absoluteBox, where will it appear?
    **答案 | Answer:** 它会出现在第一个relativeBox和第二个relativeBox之间，成为正常文档流的一部分 | It will appear between the first and second relativeBox, becoming part of the normal document flow
  - 如果将top改为bottom: 100，元素会移动到哪里？| If you change top to bottom: 100, where will the element move?
    **答案 | Answer:** 元素会定位在距离父容器底部100像素的位置 | The element will be positioned 100 pixels from the bottom of the parent container

  **常见误区检查 | Common Misconception Checks:**
  - Web开发者常认为absolute定位相对于viewport，在React Native中是这样吗？| Web developers often think absolute positioning is relative to viewport, is this true in React Native?
    **答案 | Answer:** 否，在React Native中absolute定位相对于父容器，不是viewport | No, in React Native absolute positioning is relative to the parent container, not the viewport
  - 可以使用z-index来控制元素层叠顺序吗？| Can you use z-index to control element stacking order?
    **答案 | Answer:** 不推荐，React Native主要依靠渲染顺序，虽然zIndex属性存在但不如web中可靠 | Not recommended, React Native mainly relies on render order, although zIndex property exists but is less reliable than in web

### 2. Top, Right, Bottom, Left定位详解 | Top, Right, Bottom, Left Positioning Details (1小时 | 1 hour)

- **四向定位属性 | Four-Directional Positioning Properties**

  **概念定义 | Concept Definition:**
  top、right、bottom、left属性用于精确控制绝对定位元素的位置。这些属性接受数字值（像素）或百分比字符串，定义元素边缘与父容器相应边缘之间的距离。 | The top, right, bottom, left properties are used to precisely control the position of absolutely positioned elements. These properties accept number values (pixels) or percentage strings, defining the distance between the element's edge and the corresponding edge of the parent container.

  **核心特征 | Key Characteristics:**
  - 只对position: 'absolute'的元素生效 | Only effective for elements with position: 'absolute'
  - 可以同时使用多个方向属性（如top和left）| Can use multiple directional properties simultaneously (e.g., top and left)
  - 可以使用负值来移出父容器边界 | Can use negative values to move outside parent container boundaries
  - 百分比值相对于父容器的对应维度 | Percentage values are relative to the corresponding dimension of parent container
  - 同时设置top和bottom可以拉伸元素高度 | Setting both top and bottom simultaneously can stretch element height

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. top: 0 和 bottom: 0 同时设置会发生什么？| What happens when you set both top: 0 and bottom: 0?
     **答案 | Answer:** 元素会从父容器顶部拉伸到底部，填充整个高度 | The element will stretch from top to bottom of parent, filling entire height
  2. 可以对relative定位的元素使用top和left吗？| Can you use top and left on relatively positioned elements?
     **答案 | Answer:** 可以，但这会相对于其原始位置偏移，而不是相对于父容器 | Yes, but this will offset relative to its original position, not relative to parent container
  3. left: '50%'的百分比是相对于什么计算的？| What is the percentage of left: '50%' calculated relative to?
     **答案 | Answer:** 相对于父容器的宽度 | Relative to the parent container's width
  4. 使用负值top: -20会让元素移动到哪里？| Where will the element move with a negative value top: -20?
     **答案 | Answer:** 向上移出父容器20像素 | Move 20 pixels upward outside the parent container

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet, Dimensions } from 'react-native';

  const PositioningDetails = () => {
    return (
      <View style={styles.container}>
        {/* 左上角定位 | Top-left positioning */}
        <View style={styles.topLeft}>
          <Text style={styles.text}>左上 | Top Left</Text>
        </View>

        {/* 右上角定位 | Top-right positioning */}
        <View style={styles.topRight}>
          <Text style={styles.text}>右上 | Top Right</Text>
        </View>

        {/* 居中定位技巧 | Center positioning technique */}
        <View style={styles.centered}>
          <Text style={styles.text}>居中 | Centered</Text>
        </View>

        {/* 拉伸填充 | Stretch to fill */}
        <View style={styles.stretched}>
          <Text style={styles.text}>拉伸填充 | Stretched</Text>
        </View>

        {/* 底部固定 | Bottom fixed */}
        <View style={styles.bottomFixed}>
          <Text style={styles.text}>底部固定 | Bottom Fixed</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#ecf0f1',
    },
    topLeft: {
      position: 'absolute',
      top: 20,
      left: 20,
      backgroundColor: '#3498db',
      padding: 10,
      borderRadius: 5,
    },
    topRight: {
      position: 'absolute',
      top: 20,
      right: 20,
      backgroundColor: '#e74c3c',
      padding: 10,
      borderRadius: 5,
    },
    centered: {
      position: 'absolute',
      // 居中技巧：使用50%减去元素尺寸的一半 | Centering technique: use 50% minus half element size
      top: '50%',
      left: '50%',
      // 注意：这只是近似居中，准确居中需要知道元素尺寸 | Note: approximate center only, precise centering needs element dimensions
      transform: [{ translateX: -50 }, { translateY: -25 }], // 需要配合transform | Need to work with transform
      backgroundColor: '#2ecc71',
      padding: 10,
      borderRadius: 5,
    },
    stretched: {
      position: 'absolute',
      top: 100,
      left: 20,
      right: 20,
      bottom: 100,
      backgroundColor: 'rgba(155, 89, 182, 0.3)',
      justifyContent: 'center',
      alignItems: 'center',
      borderWidth: 2,
      borderColor: '#9b59b6',
      borderStyle: 'dashed',
    },
    bottomFixed: {
      position: 'absolute',
      bottom: 20,
      left: 20,
      right: 20,
      backgroundColor: '#f39c12',
      padding: 15,
      borderRadius: 5,
      alignItems: 'center',
    },
    text: {
      color: 'white',
      fontWeight: 'bold',
    },
  });

  export default PositioningDetails;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果只设置left: 20而不设置top，元素会在哪里？| If you only set left: 20 without setting top, where will the element be?
    **答案 | Answer:** 元素会保持在默认的垂直位置（通常是顶部），距离左边20像素 | The element will stay at default vertical position (usually top), 20 pixels from left
  - stretched样式中同时设置top、left、right、bottom的作用是什么？| What's the purpose of setting top, left, right, bottom simultaneously in stretched style?
    **答案 | Answer:** 创建一个自适应的矩形区域，四边都有固定的边距，元素会拉伸填充剩余空间 | Creates an adaptive rectangular area with fixed margins on all sides, element stretches to fill remaining space

  **常见误区检查 | Common Misconception Checks:**
  - 百分比定位是相对于屏幕尺寸吗？| Is percentage positioning relative to screen size?
    **答案 | Answer:** 否，是相对于父容器尺寸，不是屏幕尺寸 | No, it's relative to parent container size, not screen size
  - 设置left: 0, right: 0可以让元素宽度100%吗？| Can setting left: 0, right: 0 make element width 100%?
    **答案 | Answer:** 是的，元素会拉伸到父容器的完整宽度 | Yes, the element will stretch to the full width of the parent container

### 3. Dimensions API - 获取屏幕尺寸 | Dimensions API - Getting Screen Dimensions (1小时 | 1 hour)

- **Dimensions.get()方法 | Dimensions.get() Method**

  **概念定义 | Concept Definition:**
  Dimensions API是React Native提供的用于获取设备屏幕和窗口尺寸的工具。Dimensions.get('window')返回应用窗口的尺寸，而Dimensions.get('screen')返回整个设备屏幕的尺寸。这对于创建响应式布局至关重要。 | Dimensions API is a React Native tool for getting device screen and window dimensions. Dimensions.get('window') returns app window dimensions, while Dimensions.get('screen') returns entire device screen dimensions. This is crucial for creating responsive layouts.

  **核心特征 | Key Characteristics:**
  - 'window'和'screen'在Android上通常相同，iOS上可能不同 | 'window' and 'screen' are usually the same on Android, may differ on iOS
  - 返回对象包含width、height、scale、fontScale属性 | Returns object with width, height, scale, fontScale properties
  - 这是一次性的静态读取，不会自动更新 | This is a one-time static read, doesn't auto-update
  - 屏幕旋转时需要监听变化事件 | Need to listen to change events for screen rotation
  - 可以用于计算基于屏幕尺寸的百分比布局 | Can be used to calculate percentage-based layouts relative to screen size

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Dimensions.get('window')和Dimensions.get('screen')的区别是什么？| What's the difference between Dimensions.get('window') and Dimensions.get('screen')?
     **答案 | Answer:** 'window'是应用可见区域，'screen'是整个设备屏幕；在iOS上，'screen'包含状态栏，'window'不包含 | 'window' is app visible area, 'screen' is entire device screen; on iOS, 'screen' includes status bar, 'window' doesn't
  2. 当设备旋转时，Dimensions.get()的值会自动更新吗？| Does Dimensions.get() value automatically update when device rotates?
     **答案 | Answer:** 否，需要监听Dimensions的change事件或使用useWindowDimensions hook | No, need to listen to Dimensions change event or use useWindowDimensions hook
  3. scale属性代表什么？| What does the scale property represent?
     **答案 | Answer:** 设备的像素密度比率，用于将逻辑像素转换为物理像素 | Device pixel density ratio, used to convert logical pixels to physical pixels
  4. 如何计算屏幕宽度的50%？| How to calculate 50% of screen width?
     **答案 | Answer:** Dimensions.get('window').width * 0.5 或 Dimensions.get('window').width / 2 | Dimensions.get('window').width * 0.5 or Dimensions.get('window').width / 2

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState, useEffect } from 'react';
  import { View, Text, StyleSheet, Dimensions } from 'react-native';

  const DimensionsExample = () => {
    // 获取初始屏幕尺寸 | Get initial screen dimensions
    const [dimensions, setDimensions] = useState({
      window: Dimensions.get('window'),
      screen: Dimensions.get('screen'),
    });

    useEffect(() => {
      // 监听尺寸变化（如屏幕旋转）| Listen for dimension changes (like rotation)
      const subscription = Dimensions.addEventListener('change', ({ window, screen }) => {
        setDimensions({ window, screen });
      });

      // 清理监听器 | Cleanup listener
      return () => subscription?.remove();
    }, []);

    // 计算百分比宽度 | Calculate percentage widths
    const windowWidth = dimensions.window.width;
    const windowHeight = dimensions.window.height;

    return (
      <View style={styles.container}>
        <Text style={styles.title}>设备尺寸信息 | Device Dimensions</Text>

        {/* 显示窗口尺寸 | Display window dimensions */}
        <View style={styles.infoBox}>
          <Text style={styles.label}>Window 尺寸 | Window Dimensions:</Text>
          <Text>宽度 Width: {dimensions.window.width.toFixed(0)}px</Text>
          <Text>高度 Height: {dimensions.window.height.toFixed(0)}px</Text>
          <Text>缩放 Scale: {dimensions.window.scale}</Text>
          <Text>字体缩放 Font Scale: {dimensions.window.fontScale}</Text>
        </View>

        {/* 显示屏幕尺寸 | Display screen dimensions */}
        <View style={styles.infoBox}>
          <Text style={styles.label}>Screen 尺寸 | Screen Dimensions:</Text>
          <Text>宽度 Width: {dimensions.screen.width.toFixed(0)}px</Text>
          <Text>高度 Height: {dimensions.screen.height.toFixed(0)}px</Text>
        </View>

        {/* 演示百分比宽度 | Demonstrate percentage widths */}
        <Text style={styles.label}>百分比宽度示例 | Percentage Width Examples:</Text>

        <View style={[styles.percentBox, { width: windowWidth * 0.5 }]}>
          <Text style={styles.percentText}>50% 宽度 | 50% Width</Text>
        </View>

        <View style={[styles.percentBox, { width: windowWidth * 0.75, backgroundColor: '#e74c3c' }]}>
          <Text style={styles.percentText}>75% 宽度 | 75% Width</Text>
        </View>

        <View style={[styles.percentBox, { width: windowWidth * 0.9, backgroundColor: '#2ecc71' }]}>
          <Text style={styles.percentText}>90% 宽度 | 90% Width</Text>
        </View>

        {/* 方形示例（基于最小维度）| Square example (based on minimum dimension) */}
        <View
          style={[
            styles.square,
            {
              width: Math.min(windowWidth, windowHeight) * 0.4,
              height: Math.min(windowWidth, windowHeight) * 0.4,
            }
          ]}
        >
          <Text style={styles.percentText}>响应式方形 | Responsive Square</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#ecf0f1',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    infoBox: {
      backgroundColor: 'white',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    label: {
      fontWeight: 'bold',
      marginTop: 10,
      marginBottom: 10,
      fontSize: 16,
    },
    percentBox: {
      backgroundColor: '#3498db',
      padding: 15,
      borderRadius: 8,
      marginBottom: 10,
      alignItems: 'center',
    },
    percentText: {
      color: 'white',
      fontWeight: 'bold',
    },
    square: {
      backgroundColor: '#9b59b6',
      borderRadius: 8,
      alignItems: 'center',
      justifyContent: 'center',
      alignSelf: 'center',
      marginTop: 10,
    },
  });

  export default DimensionsExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 当你旋转设备时，dimensions状态会自动更新吗？| Will the dimensions state automatically update when you rotate the device?
    **答案 | Answer:** 是的，因为我们添加了change事件监听器，它会在尺寸变化时触发并更新状态 | Yes, because we added a change event listener that triggers and updates state when dimensions change
  - Math.min(windowWidth, windowHeight)的作用是什么？| What's the purpose of Math.min(windowWidth, windowHeight)?
    **答案 | Answer:** 获取宽度和高度中的最小值，确保方形元素在横屏或竖屏时都能完整显示 | Gets the minimum of width and height, ensuring square element displays completely in both portrait and landscape

  **常见误区检查 | Common Misconception Checks:**
  - 在组件中直接使用Dimensions.get()作为样式值，旋转时会更新吗？| If you directly use Dimensions.get() as a style value in a component, will it update on rotation?
    **答案 | Answer:** 不会，需要将其存储在state中并监听变化事件才能响应旋转 | No, you need to store it in state and listen to change events to respond to rotation
  - scale: 2表示什么？| What does scale: 2 mean?
    **答案 | Answer:** 表示Retina或高DPI屏幕，1个逻辑像素对应2个物理像素 | Indicates Retina or high-DPI screen, 1 logical pixel corresponds to 2 physical pixels

### 4. 创建叠加层和徽章 | Creating Overlays and Badges (1小时 | 1 hour)

- **叠加元素的实现模式 | Overlay Element Implementation Patterns**

  **概念定义 | Concept Definition:**
  叠加层是指使用绝对定位放置在其他元素上方的UI组件，常见于通知徽章、图片标签、模态框遮罩等场景。这种模式利用了绝对定位脱离文档流的特性，使元素可以"浮动"在其他内容之上。 | Overlays are UI components placed above other elements using absolute positioning, commonly seen in notification badges, image tags, modal overlays, etc. This pattern leverages the document-flow-breaking nature of absolute positioning, allowing elements to "float" above other content.

  **核心特征 | Key Characteristics:**
  - 使用绝对定位实现层叠效果 | Use absolute positioning to achieve stacking effect
  - 父容器通常需要设置相对定位（虽然默认就是）| Parent container usually needs relative positioning (though it's default)
  - 可以使用负值让徽章"溢出"父容器 | Can use negative values to make badge "overflow" parent container
  - 通常配合borderRadius创建圆形徽章 | Usually combined with borderRadius to create circular badges
  - 需要考虑触摸事件的传递问题 | Need to consider touch event propagation

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么徽章通常使用绝对定位而不是相对定位？| Why do badges typically use absolute positioning instead of relative?
     **答案 | Answer:** 因为绝对定位脱离文档流，不会影响其他元素布局，可以精确定位在指定位置 | Because absolute positioning removes from document flow, doesn't affect other elements' layout, and can be precisely positioned
  2. 如何让徽章出现在容器的右上角？| How to make a badge appear at the top-right corner of a container?
     **答案 | Answer:** 使用position: 'absolute', top: 0, right: 0（或使用负值调整）| Use position: 'absolute', top: 0, right: 0 (or use negative values to adjust)
  3. 父容器需要特殊的position设置吗？| Does the parent container need special position settings?
     **答案 | Answer:** 不需要，React Native中View默认就是relative定位 | No, View in React Native is relative positioning by default
  4. 如何创建半透明的遮罩层？| How to create a semi-transparent overlay mask?
     **答案 | Answer:** 使用绝对定位占满整个父容器，配合rgba或透明度backgroundColor | Use absolute positioning to fill parent, combined with rgba or transparent backgroundColor

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import { View, Text, StyleSheet, Image, TouchableOpacity } from 'react-native';

  const OverlayBadgeExample = () => {
    const [notificationCount, setNotificationCount] = useState(3);
    const [showOverlay, setShowOverlay] = useState(false);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>叠加层与徽章示例 | Overlay & Badge Examples</Text>

        {/* 带徽章的图标按钮 | Icon button with badge */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>通知徽章 | Notification Badge</Text>
          <View style={styles.iconWrapper}>
            <View style={styles.iconPlaceholder}>
              <Text style={styles.iconText}>🔔</Text>
            </View>
            {/* 徽章绝对定位在右上角 | Badge absolutely positioned at top-right */}
            {notificationCount > 0 && (
              <View style={styles.badge}>
                <Text style={styles.badgeText}>
                  {notificationCount > 9 ? '9+' : notificationCount}
                </Text>
              </View>
            )}
          </View>
        </View>

        {/* 图片上的标签 | Tag on image */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>图片标签 | Image Tag</Text>
          <View style={styles.imageContainer}>
            <View style={styles.imagePlaceholder}>
              <Text>📷 图片 | Image</Text>
            </View>
            {/* 左上角标签 | Top-left tag */}
            <View style={styles.tagTopLeft}>
              <Text style={styles.tagText}>新品 | NEW</Text>
            </View>
            {/* 右下角价格标签 | Bottom-right price tag */}
            <View style={styles.tagBottomRight}>
              <Text style={styles.tagText}>¥99</Text>
            </View>
          </View>
        </View>

        {/* 模态遮罩层示例 | Modal overlay example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>模态遮罩 | Modal Overlay</Text>
          <TouchableOpacity
            style={styles.button}
            onPress={() => setShowOverlay(true)}
          >
            <Text style={styles.buttonText}>显示遮罩 | Show Overlay</Text>
          </TouchableOpacity>

          {showOverlay && (
            <View style={styles.overlay}>
              <View style={styles.modalContent}>
                <Text style={styles.modalTitle}>模态对话框 | Modal Dialog</Text>
                <Text style={styles.modalText}>
                  这是一个使用绝对定位和半透明背景创建的遮罩层
                  {'\n\n'}
                  This is an overlay created with absolute positioning and semi-transparent background
                </Text>
                <TouchableOpacity
                  style={styles.closeButton}
                  onPress={() => setShowOverlay(false)}
                >
                  <Text style={styles.buttonText}>关闭 | Close</Text>
                </TouchableOpacity>
              </View>
            </View>
          )}
        </View>

        {/* 溢出徽章示例 | Overflow badge example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>溢出徽章 | Overflow Badge</Text>
          <View style={styles.avatarWrapper}>
            <View style={styles.avatar}>
              <Text style={styles.avatarText}>👤</Text>
            </View>
            {/* 徽章部分溢出父容器 | Badge partially overflows parent */}
            <View style={styles.statusBadge} />
          </View>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#ecf0f1',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    section: {
      marginBottom: 30,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 10,
    },
    // 通知徽章样式 | Notification badge styles
    iconWrapper: {
      width: 60,
      height: 60,
      position: 'relative', // 明确设置为相对定位（虽然默认就是）| Explicitly set to relative (though default)
    },
    iconPlaceholder: {
      width: 60,
      height: 60,
      backgroundColor: '#3498db',
      borderRadius: 30,
      justifyContent: 'center',
      alignItems: 'center',
    },
    iconText: {
      fontSize: 30,
    },
    badge: {
      position: 'absolute',
      top: -5, // 负值让徽章部分溢出 | Negative value for partial overflow
      right: -5,
      backgroundColor: '#e74c3c',
      borderRadius: 10,
      minWidth: 20,
      height: 20,
      justifyContent: 'center',
      alignItems: 'center',
      paddingHorizontal: 5,
      borderWidth: 2,
      borderColor: 'white', // 白色边框增强视觉效果 | White border for visual enhancement
    },
    badgeText: {
      color: 'white',
      fontSize: 12,
      fontWeight: 'bold',
    },
    // 图片标签样式 | Image tag styles
    imageContainer: {
      width: '100%',
      height: 200,
      position: 'relative',
    },
    imagePlaceholder: {
      width: '100%',
      height: '100%',
      backgroundColor: '#bdc3c7',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
    },
    tagTopLeft: {
      position: 'absolute',
      top: 10,
      left: 10,
      backgroundColor: '#e74c3c',
      paddingHorizontal: 10,
      paddingVertical: 5,
      borderRadius: 4,
    },
    tagBottomRight: {
      position: 'absolute',
      bottom: 10,
      right: 10,
      backgroundColor: '#f39c12',
      paddingHorizontal: 12,
      paddingVertical: 6,
      borderRadius: 4,
    },
    tagText: {
      color: 'white',
      fontWeight: 'bold',
      fontSize: 12,
    },
    // 模态遮罩样式 | Modal overlay styles
    button: {
      backgroundColor: '#3498db',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
    },
    buttonText: {
      color: 'white',
      fontWeight: 'bold',
    },
    overlay: {
      position: 'absolute',
      top: 0,
      left: 0,
      right: 0,
      bottom: 0,
      backgroundColor: 'rgba(0, 0, 0, 0.5)', // 半透明黑色 | Semi-transparent black
      justifyContent: 'center',
      alignItems: 'center',
    },
    modalContent: {
      backgroundColor: 'white',
      padding: 20,
      borderRadius: 12,
      width: '80%',
      maxWidth: 400,
    },
    modalTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 15,
      textAlign: 'center',
    },
    modalText: {
      fontSize: 14,
      marginBottom: 20,
      textAlign: 'center',
      lineHeight: 20,
    },
    closeButton: {
      backgroundColor: '#e74c3c',
      padding: 12,
      borderRadius: 6,
      alignItems: 'center',
    },
    // 溢出徽章样式 | Overflow badge styles
    avatarWrapper: {
      width: 80,
      height: 80,
      position: 'relative',
    },
    avatar: {
      width: 80,
      height: 80,
      backgroundColor: '#9b59b6',
      borderRadius: 40,
      justifyContent: 'center',
      alignItems: 'center',
    },
    avatarText: {
      fontSize: 40,
    },
    statusBadge: {
      position: 'absolute',
      bottom: 5,
      right: 5,
      width: 20,
      height: 20,
      backgroundColor: '#2ecc71', // 绿色表示在线 | Green for online status
      borderRadius: 10,
      borderWidth: 3,
      borderColor: 'white',
    },
  });

  export default OverlayBadgeExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么徽章要使用负值的top和right？| Why use negative values for top and right in the badge?
    **答案 | Answer:** 让徽章部分溢出父容器，创建"悬浮"在图标边缘的视觉效果 | To make badge partially overflow parent container, creating a "floating" visual effect on the edge of the icon
  - overlay样式中为什么同时设置top: 0, left: 0, right: 0, bottom: 0？| Why set top: 0, left: 0, right: 0, bottom: 0 simultaneously in overlay style?
    **答案 | Answer:** 让遮罩层完全覆盖整个父容器（通常是整个屏幕）| To make overlay completely cover entire parent container (usually entire screen)

  **常见误区检查 | Common Misconception Checks:**
  - 徽章的borderColor: 'white'有什么作用？| What's the purpose of borderColor: 'white' on the badge?
    **答案 | Answer:** 创建白色边框，使徽章与背景分离，增强可读性和视觉层次 | Creates white border to separate badge from background, enhancing readability and visual hierarchy
  - 模态遮罩的backgroundColor为什么使用rgba？| Why use rgba for modal overlay backgroundColor?
    **答案 | Answer:** rgba的第四个参数是透明度，可以创建半透明效果，让下层内容若隐若现 | The fourth parameter of rgba is opacity, creating semi-transparent effect that allows underlying content to show through

### 5. 浮动操作按钮（FAB）实现 | Floating Action Button (FAB) Implementation (1小时 | 1 hour)

- **FAB设计模式 | FAB Design Pattern**

  **概念定义 | Concept Definition:**
  浮动操作按钮（FAB）是Material Design中的经典模式，是一个固定在屏幕右下角的圆形按钮，用于展示应用中最重要的操作。在React Native中，由于没有fixed定位，需要使用绝对定位配合适当的bottom和right值来模拟固定效果。 | Floating Action Button (FAB) is a classic Material Design pattern, a circular button fixed at the bottom-right of the screen to showcase the app's most important action. In React Native, due to lack of fixed positioning, we need to use absolute positioning with appropriate bottom and right values to simulate fixed effect.

  **核心特征 | Key Characteristics:**
  - 使用绝对定位模拟固定在屏幕底部 | Use absolute positioning to simulate fixing at screen bottom
  - 通常是圆形，使用相等的width、height和borderRadius | Usually circular, using equal width, height and borderRadius
  - 需要考虑安全区域（SafeAreaView）避免被系统UI遮挡 | Need to consider safe area (SafeAreaView) to avoid system UI occlusion
  - 应该有明显的阴影或elevation提升视觉层次 | Should have prominent shadow or elevation for visual hierarchy
  - 在ScrollView中也能保持固定位置 | Maintains fixed position even in ScrollView

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native中FAB真的是"fixed"定位吗？| Is FAB truly "fixed" positioning in React Native?
     **答案 | Answer:** 不是，是使用绝对定位相对于父容器模拟固定效果 | No, it uses absolute positioning relative to parent container to simulate fixed effect
  2. 为什么FAB需要设置在所有内容的外层？| Why does FAB need to be set outside all content?
     **答案 | Answer:** 因为绝对定位相对于父容器，需要将父容器设置为整个屏幕才能固定在屏幕位置 | Because absolute positioning is relative to parent, parent must be entire screen to fix at screen position
  3. 如何让FAB在ScrollView滚动时保持固定？| How to keep FAB fixed when ScrollView scrolls?
     **答案 | Answer:** FAB不能作为ScrollView的子元素，应该与ScrollView并列，共同位于父View中 | FAB cannot be child of ScrollView, should be sibling to ScrollView, both in parent View
  4. borderRadius应该设置为多少才能让方形变成圆形？| What should borderRadius be set to for transforming square to circle?
     **答案 | Answer:** 宽度或高度的一半，例如width: 60, borderRadius: 30 | Half of width or height, e.g., width: 60, borderRadius: 30

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import {
    View,
    Text,
    StyleSheet,
    TouchableOpacity,
    ScrollView,
    SafeAreaView,
    Alert
  } from 'react-native';

  const FABExample = () => {
    const [items, setItems] = useState([
      '项目 1 | Item 1', '项目 2 | Item 2', '项目 3 | Item 3',
      '项目 4 | Item 4', '项目 5 | Item 5', '项目 6 | Item 6',
      '项目 7 | Item 7', '项目 8 | Item 8', '项目 9 | Item 9',
      '项目 10 | Item 10',
    ]);

    const handleFABPress = () => {
      Alert.alert(
        '添加新项目 | Add New Item',
        '这是FAB的操作 | This is FAB action',
        [{ text: '确定 | OK' }]
      );
    };

    return (
      <SafeAreaView style={styles.container}>
        {/* 主要内容区域 | Main content area */}
        <ScrollView style={styles.scrollView} contentContainerStyle={styles.scrollContent}>
          <Text style={styles.title}>可滚动内容 | Scrollable Content</Text>
          <Text style={styles.subtitle}>
            滚动时FAB保持固定位置{'\n'}
            FAB stays fixed while scrolling
          </Text>

          {items.map((item, index) => (
            <View key={index} style={styles.listItem}>
              <Text style={styles.listItemText}>{item}</Text>
            </View>
          ))}

          {/* 底部占位空间，避免内容被FAB遮挡 | Bottom spacing to avoid FAB covering content */}
          <View style={{ height: 80 }} />
        </ScrollView>

        {/* FAB - 作为SafeAreaView的直接子元素，而非ScrollView的子元素 */}
        {/* FAB - Direct child of SafeAreaView, not ScrollView */}
        <TouchableOpacity
          style={styles.fab}
          onPress={handleFABPress}
          activeOpacity={0.8} // 按下时的透明度 | Opacity when pressed
        >
          <Text style={styles.fabIcon}>+</Text>
        </TouchableOpacity>

        {/* 扩展FAB示例 - 带标签 | Extended FAB example - with label */}
        <TouchableOpacity
          style={styles.extendedFAB}
          onPress={handleFABPress}
          activeOpacity={0.8}
        >
          <Text style={styles.fabIcon}>+</Text>
          <Text style={styles.fabLabel}>新建 | Create</Text>
        </TouchableOpacity>
      </SafeAreaView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#ecf0f1',
    },
    scrollView: {
      flex: 1,
    },
    scrollContent: {
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
      textAlign: 'center',
    },
    subtitle: {
      fontSize: 14,
      color: '#7f8c8d',
      marginBottom: 20,
      textAlign: 'center',
    },
    listItem: {
      backgroundColor: 'white',
      padding: 15,
      borderRadius: 8,
      marginBottom: 10,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 1 },
      shadowOpacity: 0.1,
      shadowRadius: 2,
      elevation: 2,
    },
    listItemText: {
      fontSize: 16,
    },
    // 标准圆形FAB | Standard circular FAB
    fab: {
      position: 'absolute',
      bottom: 20, // 距离底部20像素 | 20 pixels from bottom
      right: 20, // 距离右侧20像素 | 20 pixels from right
      width: 60,
      height: 60,
      borderRadius: 30, // 宽高的一半，形成圆形 | Half of width/height for circle
      backgroundColor: '#e74c3c',
      justifyContent: 'center',
      alignItems: 'center',
      // iOS阴影 | iOS shadow
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.3,
      shadowRadius: 6,
      // Android阴影 | Android shadow
      elevation: 8,
    },
    fabIcon: {
      fontSize: 30,
      color: 'white',
      fontWeight: '300',
    },
    // 扩展FAB（带标签）| Extended FAB (with label)
    extendedFAB: {
      position: 'absolute',
      bottom: 90, // 在标准FAB上方 | Above standard FAB
      right: 20,
      flexDirection: 'row',
      alignItems: 'center',
      backgroundColor: '#3498db',
      paddingVertical: 12,
      paddingHorizontal: 20,
      borderRadius: 25,
      // iOS阴影 | iOS shadow
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 4 },
      shadowOpacity: 0.3,
      shadowRadius: 6,
      // Android阴影 | Android shadow
      elevation: 8,
    },
    fabLabel: {
      color: 'white',
      fontSize: 16,
      fontWeight: 'bold',
      marginLeft: 8,
    },
  });

  export default FABExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么FAB要放在SafeAreaView的直接子元素，而不是ScrollView内部？| Why place FAB as direct child of SafeAreaView instead of inside ScrollView?
    **答案 | Answer:** 如果放在ScrollView内部，FAB会随内容滚动；作为SafeAreaView的子元素，使用绝对定位可以固定在屏幕位置 | If placed inside ScrollView, FAB would scroll with content; as child of SafeAreaView, absolute positioning fixes it to screen position
  - 底部的<View style={{ height: 80 }} />的作用是什么？| What's the purpose of the bottom <View style={{ height: 80 }} />?
    **答案 | Answer:** 在可滚动内容底部添加占位空间，防止最后的列表项被FAB遮挡 | Adds spacing at bottom of scrollable content to prevent last list item from being covered by FAB

  **常见误区检查 | Common Misconception Checks:**
  - FAB的elevation和shadowOpacity能在所有平台生效吗？| Do FAB's elevation and shadowOpacity work on all platforms?
    **答案 | Answer:** 否，elevation只在Android生效，shadowOpacity等shadow属性只在iOS生效，需要同时设置才能跨平台 | No, elevation only works on Android, shadowOpacity and other shadow properties only work on iOS, need both for cross-platform
  - 能用position: 'fixed'实现FAB吗？| Can you use position: 'fixed' for FAB?
    **答案 | Answer:** 不能，React Native不支持fixed定位，只能用绝对定位配合合适的父容器结构模拟 | No, React Native doesn't support fixed positioning, can only simulate with absolute positioning and proper parent container structure

### 6. 响应式百分比布局实践 | Responsive Percentage Layout Practice (1小时 | 1 hour)

- **基于屏幕尺寸的动态布局 | Dynamic Layout Based on Screen Dimensions**

  **概念定义 | Concept Definition:**
  响应式百分比布局是指使用Dimensions API获取屏幕尺寸，然后通过数学计算将元素尺寸设置为屏幕的百分比。这种方式比CSS百分比更灵活，可以基于屏幕的宽度或高度，甚至是两者的组合来计算布局。 | Responsive percentage layout refers to using Dimensions API to get screen dimensions, then calculating element sizes as percentages of screen through math. This approach is more flexible than CSS percentages, can be based on screen width or height, or even combinations of both.

  **核心特征 | Key Characteristics:**
  - 需要手动计算百分比值（不像web可以直接用%）| Need to manually calculate percentage values (unlike web where % can be used directly)
  - 可以基于宽度、高度或对角线长度计算 | Can calculate based on width, height, or diagonal length
  - 配合useWindowDimensions hook可以响应屏幕旋转 | Combined with useWindowDimensions hook can respond to screen rotation
  - 可以设置最小/最大值限制确保可用性 | Can set min/max limits to ensure usability
  - 适合创建在不同设备上比例一致的UI | Suitable for creating UI with consistent proportions across devices

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中可以直接使用width: '50%'吗？| Can you directly use width: '50%' in React Native?
     **答案 | Answer:** 可以，但这是相对于父容器的百分比，不是屏幕百分比 | Yes, but this is percentage of parent container, not screen percentage
  2. 如何计算屏幕宽度的80%？| How to calculate 80% of screen width?
     **答案 | Answer:** Dimensions.get('window').width * 0.8 或 useWindowDimensions().width * 0.8 | Dimensions.get('window').width * 0.8 or useWindowDimensions().width * 0.8
  3. 为什么有时要使用Math.min()或Math.max()？| Why sometimes use Math.min() or Math.max()?
     **答案 | Answer:** 设置尺寸的上下限，防止在极端屏幕尺寸（如平板）上元素过大或过小 | Set upper/lower limits for dimensions, preventing elements from being too large or small on extreme screen sizes (like tablets)
  4. useWindowDimensions相比Dimensions.get()的优势是什么？| What's the advantage of useWindowDimensions over Dimensions.get()?
     **答案 | Answer:** useWindowDimensions是hook，会在屏幕尺寸变化时自动触发重新渲染 | useWindowDimensions is a hook that automatically triggers re-render when screen dimensions change

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import {
    View,
    Text,
    StyleSheet,
    useWindowDimensions,
    ScrollView
  } from 'react-native';

  const ResponsiveLayout = () => {
    // 使用hook获取实时窗口尺寸 | Use hook to get real-time window dimensions
    const { width, height } = useWindowDimensions();

    // 计算各种响应式尺寸 | Calculate various responsive dimensions
    const cardWidth = Math.min(width * 0.9, 400); // 90%宽度，但不超过400
    const imageHeight = width * 0.5; // 图片高度为屏幕宽度的50%
    const smallCardWidth = (width - 60) / 3; // 三列布局，考虑边距
    const isLandscape = width > height; // 判断是否横屏

    // 动态字体大小（基于屏幕宽度）| Dynamic font size (based on screen width)
    const titleFontSize = Math.max(width * 0.06, 20); // 至少20px
    const bodyFontSize = Math.max(width * 0.04, 14); // 至少14px

    return (
      <ScrollView style={styles.container}>
        <Text style={[styles.title, { fontSize: titleFontSize }]}>
          响应式布局 | Responsive Layout
        </Text>

        {/* 显示当前屏幕信息 | Display current screen info */}
        <View style={styles.infoCard}>
          <Text style={styles.infoText}>
            屏幕宽度 | Width: {width.toFixed(0)}px
          </Text>
          <Text style={styles.infoText}>
            屏幕高度 | Height: {height.toFixed(0)}px
          </Text>
          <Text style={styles.infoText}>
            方向 | Orientation: {isLandscape ? '横屏 | Landscape' : '竖屏 | Portrait'}
          </Text>
        </View>

        {/* 响应式卡片（90%宽度，最大400px）| Responsive card (90% width, max 400px) */}
        <View style={[styles.mainCard, { width: cardWidth }]}>
          <Text style={[styles.cardTitle, { fontSize: bodyFontSize * 1.2 }]}>
            主卡片 | Main Card
          </Text>
          <Text style={[styles.cardText, { fontSize: bodyFontSize }]}>
            宽度: {cardWidth.toFixed(0)}px (90% 或最大 400px)
            {'\n'}
            Width: {cardWidth.toFixed(0)}px (90% or max 400px)
          </Text>
        </View>

        {/* 响应式图片占位符 | Responsive image placeholder */}
        <View
          style={[
            styles.imagePlaceholder,
            {
              width: width * 0.9,
              height: imageHeight,
              alignSelf: 'center',
            }
          ]}
        >
          <Text style={styles.placeholderText}>
            图片区域 | Image Area{'\n'}
            {(width * 0.9).toFixed(0)} x {imageHeight.toFixed(0)}
          </Text>
        </View>

        {/* 三列网格布局 | Three-column grid layout */}
        <Text style={[styles.sectionTitle, { fontSize: bodyFontSize * 1.2 }]}>
          三列网格 | Three-Column Grid
        </Text>
        <View style={styles.gridContainer}>
          {[1, 2, 3].map((num) => (
            <View
              key={num}
              style={[
                styles.gridItem,
                {
                  width: smallCardWidth,
                  height: smallCardWidth, // 正方形
                }
              ]}
            >
              <Text style={styles.gridItemText}>{num}</Text>
            </View>
          ))}
        </View>

        {/* 响应式两列布局（横屏）或单列（竖屏）*/}
        {/* Responsive two-column (landscape) or single-column (portrait) */}
        <Text style={[styles.sectionTitle, { fontSize: bodyFontSize * 1.2 }]}>
          自适应列数 | Adaptive Columns
        </Text>
        <View
          style={[
            styles.adaptiveContainer,
            { flexDirection: isLandscape ? 'row' : 'column' }
          ]}
        >
          <View
            style={[
              styles.adaptiveCard,
              { width: isLandscape ? '48%' : '100%' }
            ]}
          >
            <Text style={styles.adaptiveCardText}>卡片 1 | Card 1</Text>
          </View>
          <View
            style={[
              styles.adaptiveCard,
              { width: isLandscape ? '48%' : '100%' }
            ]}
          >
            <Text style={styles.adaptiveCardText}>卡片 2 | Card 2</Text>
          </View>
        </View>

        {/* 基于屏幕对角线的圆形元素 | Circular element based on screen diagonal */}
        <Text style={[styles.sectionTitle, { fontSize: bodyFontSize * 1.2 }]}>
          对角线比例 | Diagonal Proportion
        </Text>
        <View
          style={[
            styles.circleElement,
            {
              width: Math.sqrt(width * width + height * height) * 0.2,
              height: Math.sqrt(width * width + height * height) * 0.2,
              borderRadius: Math.sqrt(width * width + height * height) * 0.1,
            }
          ]}
        >
          <Text style={styles.circleText}>⭕</Text>
        </View>

        {/* 底部占位 | Bottom spacing */}
        <View style={{ height: 40 }} />
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#ecf0f1',
      padding: 20,
    },
    title: {
      fontWeight: 'bold',
      textAlign: 'center',
      marginBottom: 20,
      color: '#2c3e50',
    },
    infoCard: {
      backgroundColor: 'white',
      padding: 15,
      borderRadius: 8,
      marginBottom: 20,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    infoText: {
      fontSize: 14,
      marginBottom: 5,
      color: '#34495e',
    },
    mainCard: {
      backgroundColor: '#3498db',
      padding: 20,
      borderRadius: 12,
      marginBottom: 20,
      alignSelf: 'center',
    },
    cardTitle: {
      color: 'white',
      fontWeight: 'bold',
      marginBottom: 10,
    },
    cardText: {
      color: 'white',
    },
    imagePlaceholder: {
      backgroundColor: '#95a5a6',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
      marginBottom: 20,
    },
    placeholderText: {
      color: 'white',
      textAlign: 'center',
      fontWeight: 'bold',
    },
    sectionTitle: {
      fontWeight: 'bold',
      marginBottom: 15,
      color: '#2c3e50',
    },
    gridContainer: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      marginBottom: 20,
    },
    gridItem: {
      backgroundColor: '#e74c3c',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
    },
    gridItemText: {
      color: 'white',
      fontSize: 24,
      fontWeight: 'bold',
    },
    adaptiveContainer: {
      justifyContent: 'space-between',
      marginBottom: 20,
    },
    adaptiveCard: {
      backgroundColor: '#2ecc71',
      padding: 20,
      borderRadius: 8,
      marginBottom: 10,
      justifyContent: 'center',
      alignItems: 'center',
    },
    adaptiveCardText: {
      color: 'white',
      fontWeight: 'bold',
    },
    circleElement: {
      backgroundColor: '#9b59b6',
      justifyContent: 'center',
      alignItems: 'center',
      alignSelf: 'center',
      marginBottom: 20,
    },
    circleText: {
      fontSize: 40,
    },
  });

  export default ResponsiveLayout;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用Math.min(width * 0.9, 400)而不是直接用width * 0.9？| Why use Math.min(width * 0.9, 400) instead of just width * 0.9?
    **答案 | Answer:** 在大屏设备（如平板）上限制最大宽度为400，防止卡片过宽影响可读性 | Limits max width to 400 on large devices (like tablets) to prevent cards from being too wide and affecting readability
  - isLandscape的判断逻辑是什么？| What's the logic for isLandscape determination?
    **答案 | Answer:** 比较宽度和高度，如果宽度大于高度则为横屏模式 | Compares width and height, landscape mode if width greater than height

  **常见误区检查 | Common Misconception Checks:**
  - 直接在StyleSheet.create()中使用Dimensions.get()计算会响应旋转吗？| Will using Dimensions.get() calculation directly in StyleSheet.create() respond to rotation?
    **答案 | Answer:** 不会，StyleSheet.create()只执行一次；需要在组件中使用useWindowDimensions并动态计算 | No, StyleSheet.create() only executes once; need to use useWindowDimensions in component and calculate dynamically
  - 字体大小可以完全基于屏幕宽度吗？| Can font size be completely based on screen width?
    **答案 | Answer:** 不建议，应该设置最小值（如Math.max(width * 0.04, 14)）确保在小屏幕上可读 | Not recommended, should set minimum value (like Math.max(width * 0.04, 14)) to ensure readability on small screens

## 实践项目：图片浏览器与FAB系统 | Practical Project: Image Gallery with FAB System

### 目标 | Objective
创建一个图片浏览器应用，综合应用绝对定位、Dimensions API和响应式布局知识。用户可以浏览图片网格，每张图片带有徽章标签，右下角有FAB用于添加新图片，点击图片显示全屏遮罩预览。 | Create an image gallery app that comprehensively applies absolute positioning, Dimensions API, and responsive layout knowledge. Users can browse an image grid, each image has badge tags, bottom-right FAB for adding new images, clicking images shows fullscreen overlay preview.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 绝对定位的元素会占用文档流空间吗？| Do absolutely positioned elements take up document flow space?
   **答案 | Answer:** 否，绝对定位脱离文档流 | No, absolute positioning removes from document flow
2. 如何让FAB在ScrollView中保持固定位置？| How to keep FAB fixed in ScrollView?
   **答案 | Answer:** FAB不作为ScrollView子元素，而是与ScrollView并列，使用绝对定位 | FAB is not child of ScrollView, but sibling, using absolute positioning
3. useWindowDimensions与Dimensions.get()的主要区别是什么？| What's the main difference between useWindowDimensions and Dimensions.get()?
   **答案 | Answer:** useWindowDimensions是hook，屏幕尺寸变化时会触发重新渲染 | useWindowDimensions is a hook that triggers re-render when screen dimensions change

### 步骤 | Steps
1. **创建响应式图片网格布局** | **Create responsive image grid layout**
   - 使用useWindowDimensions获取屏幕宽度
   - 计算每张图片的宽度（三列布局，考虑间距）
   - 使用FlatList或ScrollView渲染图片列表

2. **添加徽章标签** | **Add badge tags**
   - 在每张图片的左上角添加类别徽章（绝对定位）
   - 在右上角添加收藏状态徽章

3. **实现FAB** | **Implement FAB**
   - 在右下角添加圆形FAB，固定位置
   - 添加合适的阴影和elevation
   - 点击显示Alert或添加新图片

4. **创建全屏预览遮罩** | **Create fullscreen preview overlay**
   - 点击图片时显示半透明遮罩
   - 遮罩中央显示大图
   - 提供关闭按钮（绝对定位在右上角）

5. **优化和测试** | **Optimize and test**
   - 测试横竖屏切换的响应性
   - 确保在不同设备尺寸上布局正确
   - 添加加载状态和错误处理

### 示例代码 | Example Code
```jsx
import React, { useState } from 'react';
import {
  View,
  Text,
  StyleSheet,
  ScrollView,
  TouchableOpacity,
  useWindowDimensions,
  SafeAreaView,
  Modal,
  Image,
} from 'react-native';

/**
 * 图片浏览器与FAB系统 | Image Gallery with FAB System
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 绝对定位（徽章、FAB、遮罩）| Absolute positioning (badges, FAB, overlay)
 * - Dimensions API（响应式网格）| Dimensions API (responsive grid)
 * - 百分比布局（图片尺寸计算）| Percentage layout (image size calculation)
 */

// 模拟图片数据 | Mock image data
const MOCK_IMAGES = [
  { id: 1, category: '自然 | Nature', isFavorite: true, color: '#2ecc71' },
  { id: 2, category: '建筑 | Architecture', isFavorite: false, color: '#3498db' },
  { id: 3, category: '美食 | Food', isFavorite: true, color: '#e74c3c' },
  { id: 4, category: '动物 | Animals', isFavorite: false, color: '#f39c12' },
  { id: 5, category: '艺术 | Art', isFavorite: true, color: '#9b59b6' },
  { id: 6, category: '自然 | Nature', isFavorite: false, color: '#1abc9c' },
  { id: 7, category: '建筑 | Architecture', isFavorite: true, color: '#34495e' },
  { id: 8, category: '美食 | Food', isFavorite: false, color: '#e67e22' },
  { id: 9, category: '动物 | Animals', isFavorite: true, color: '#16a085' },
];

const ImageGallery = () => {
  const { width } = useWindowDimensions();
  const [images, setImages] = useState(MOCK_IMAGES);
  const [selectedImage, setSelectedImage] = useState(null);

  // 计算响应式图片尺寸 | Calculate responsive image dimensions
  // 三列布局，每列间距10，左右padding 20 | 3 columns, 10px gap, 20px padding
  const COLUMNS = 3;
  const SPACING = 10;
  const CONTAINER_PADDING = 20;
  const imageSize = (width - CONTAINER_PADDING * 2 - SPACING * (COLUMNS - 1)) / COLUMNS;

  // 切换收藏状态 | Toggle favorite status
  const toggleFavorite = (imageId) => {
    setImages(prevImages =>
      prevImages.map(img =>
        img.id === imageId ? { ...img, isFavorite: !img.isFavorite } : img
      )
    );
  };

  // 渲染单个图片项 | Render single image item
  const renderImageItem = (image) => (
    <TouchableOpacity
      key={image.id}
      style={[styles.imageContainer, { width: imageSize, height: imageSize }]}
      onPress={() => setSelectedImage(image)}
      activeOpacity={0.8}
    >
      {/* 图片占位符 | Image placeholder */}
      <View style={[styles.imagePlaceholder, { backgroundColor: image.color }]}>
        <Text style={styles.imageId}>{image.id}</Text>
      </View>

      {/* 类别徽章 - 左上角绝对定位 | Category badge - top-left absolute positioning */}
      <View style={styles.categoryBadge}>
        <Text style={styles.categoryText}>{image.category.split(' | ')[0]}</Text>
      </View>

      {/* 收藏徽章 - 右上角绝对定位 | Favorite badge - top-right absolute positioning */}
      <TouchableOpacity
        style={styles.favoriteBadge}
        onPress={() => toggleFavorite(image.id)}
      >
        <Text style={styles.favoriteIcon}>{image.isFavorite ? '❤️' : '🤍'}</Text>
      </TouchableOpacity>
    </TouchableOpacity>
  );

  // 渲染图片网格 | Render image grid
  const renderImageGrid = () => {
    const rows = [];
    for (let i = 0; i < images.length; i += COLUMNS) {
      const rowImages = images.slice(i, i + COLUMNS);
      rows.push(
        <View key={i} style={styles.imageRow}>
          {rowImages.map(renderImageItem)}
        </View>
      );
    }
    return rows;
  };

  return (
    <SafeAreaView style={styles.container}>
      {/* 标题栏 | Header */}
      <View style={styles.header}>
        <Text style={styles.headerTitle}>图片浏览器 | Image Gallery</Text>
        <Text style={styles.headerSubtitle}>
          {images.filter(img => img.isFavorite).length} 个收藏 | {images.filter(img => img.isFavorite).length} Favorites
        </Text>
      </View>

      {/* 可滚动图片网格 | Scrollable image grid */}
      <ScrollView
        style={styles.scrollView}
        contentContainerStyle={styles.scrollContent}
      >
        {renderImageGrid()}

        {/* 底部占位，避免被FAB遮挡 | Bottom spacing to avoid FAB occlusion */}
        <View style={{ height: 80 }} />
      </ScrollView>

      {/* 浮动操作按钮 - 添加图片 | Floating Action Button - Add image */}
      <TouchableOpacity
        style={styles.fab}
        onPress={() => {
          const newId = Math.max(...images.map(img => img.id)) + 1;
          const colors = ['#e74c3c', '#3498db', '#2ecc71', '#f39c12', '#9b59b6'];
          const categories = ['自然 | Nature', '建筑 | Architecture', '美食 | Food'];
          setImages([
            ...images,
            {
              id: newId,
              category: categories[Math.floor(Math.random() * categories.length)],
              isFavorite: false,
              color: colors[Math.floor(Math.random() * colors.length)],
            },
          ]);
        }}
        activeOpacity={0.8}
      >
        <Text style={styles.fabIcon}>+</Text>
      </TouchableOpacity>

      {/* 全屏预览模态框 | Fullscreen preview modal */}
      <Modal
        visible={selectedImage !== null}
        transparent={true}
        animationType="fade"
        onRequestClose={() => setSelectedImage(null)}
      >
        <View style={styles.modalOverlay}>
          {/* 半透明背景 - 点击关闭 | Semi-transparent background - tap to close */}
          <TouchableOpacity
            style={styles.modalBackground}
            activeOpacity={1}
            onPress={() => setSelectedImage(null)}
          >
            {/* 图片预览容器 | Image preview container */}
            <View style={styles.modalContent}>
              {selectedImage && (
                <>
                  {/* 大图占位符 | Large image placeholder */}
                  <View
                    style={[
                      styles.largeImage,
                      {
                        backgroundColor: selectedImage.color,
                        width: Math.min(width * 0.9, 400),
                        height: Math.min(width * 0.9, 400),
                      },
                    ]}
                  >
                    <Text style={styles.largeImageId}>{selectedImage.id}</Text>
                  </View>

                  {/* 图片信息 | Image info */}
                  <View style={styles.imageInfo}>
                    <Text style={styles.imageInfoText}>
                      {selectedImage.category}
                    </Text>
                    <Text style={styles.imageInfoText}>
                      {selectedImage.isFavorite ? '已收藏 | Favorited' : '未收藏 | Not Favorited'}
                    </Text>
                  </View>
                </>
              )}
            </View>
          </TouchableOpacity>

          {/* 关闭按钮 - 右上角绝对定位 | Close button - top-right absolute positioning */}
          <TouchableOpacity
            style={styles.closeButton}
            onPress={() => setSelectedImage(null)}
          >
            <Text style={styles.closeButtonText}>✕</Text>
          </TouchableOpacity>
        </View>
      </Modal>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
  },
  header: {
    backgroundColor: 'white',
    padding: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#ddd',
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#2c3e50',
  },
  headerSubtitle: {
    fontSize: 14,
    color: '#7f8c8d',
    marginTop: 5,
  },
  scrollView: {
    flex: 1,
  },
  scrollContent: {
    padding: 20,
  },
  imageRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 10,
  },
  imageContainer: {
    position: 'relative', // 为徽章提供定位上下文 | Provides positioning context for badges
    borderRadius: 8,
    overflow: 'hidden', // 确保圆角生效 | Ensure border radius works
  },
  imagePlaceholder: {
    width: '100%',
    height: '100%',
    justifyContent: 'center',
    alignItems: 'center',
  },
  imageId: {
    fontSize: 40,
    fontWeight: 'bold',
    color: 'white',
  },
  // 类别徽章样式 | Category badge styles
  categoryBadge: {
    position: 'absolute',
    top: 5,
    left: 5,
    backgroundColor: 'rgba(0, 0, 0, 0.6)',
    paddingHorizontal: 8,
    paddingVertical: 4,
    borderRadius: 4,
  },
  categoryText: {
    color: 'white',
    fontSize: 10,
    fontWeight: 'bold',
  },
  // 收藏徽章样式 | Favorite badge styles
  favoriteBadge: {
    position: 'absolute',
    top: 5,
    right: 5,
    backgroundColor: 'rgba(255, 255, 255, 0.9)',
    width: 30,
    height: 30,
    borderRadius: 15,
    justifyContent: 'center',
    alignItems: 'center',
  },
  favoriteIcon: {
    fontSize: 16,
  },
  // FAB样式 | FAB styles
  fab: {
    position: 'absolute',
    bottom: 20,
    right: 20,
    width: 60,
    height: 60,
    borderRadius: 30,
    backgroundColor: '#e74c3c',
    justifyContent: 'center',
    alignItems: 'center',
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 6,
    elevation: 8,
  },
  fabIcon: {
    fontSize: 30,
    color: 'white',
    fontWeight: '300',
  },
  // 模态框样式 | Modal styles
  modalOverlay: {
    flex: 1,
    backgroundColor: 'rgba(0, 0, 0, 0.9)', // 深色半透明背景 | Dark semi-transparent background
  },
  modalBackground: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  modalContent: {
    alignItems: 'center',
  },
  largeImage: {
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 8 },
    shadowOpacity: 0.5,
    shadowRadius: 12,
    elevation: 10,
  },
  largeImageId: {
    fontSize: 80,
    fontWeight: 'bold',
    color: 'white',
  },
  imageInfo: {
    marginTop: 20,
    alignItems: 'center',
  },
  imageInfoText: {
    color: 'white',
    fontSize: 18,
    marginBottom: 5,
  },
  // 关闭按钮样式 | Close button styles
  closeButton: {
    position: 'absolute',
    top: 50,
    right: 20,
    width: 40,
    height: 40,
    borderRadius: 20,
    backgroundColor: 'rgba(255, 255, 255, 0.3)',
    justifyContent: 'center',
    alignItems: 'center',
  },
  closeButtonText: {
    fontSize: 24,
    color: 'white',
    fontWeight: 'bold',
  },
});

export default ImageGallery;
```

### 项目完成检查 | Project Completion Check
1. 图片网格是否根据屏幕宽度自动调整？| Does the image grid automatically adjust based on screen width?
2. 徽章是否正确使用绝对定位显示在图片上？| Are badges correctly displayed on images using absolute positioning?
3. FAB是否固定在右下角，滚动时保持位置？| Is FAB fixed at bottom-right and maintains position while scrolling?
4. 全屏预览的遮罩是否覆盖整个屏幕？| Does the fullscreen preview overlay cover the entire screen?
5. 旋转设备时布局是否正确响应？| Does the layout respond correctly when rotating the device?
6. 所有绝对定位元素是否有合适的父容器？| Do all absolutely positioned elements have appropriate parent containers?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **定位概念强化** | **Positioning Concept Reinforcement**
   - **练习描述 | Exercise Description:** 创建一个"图层演示器"，展示5个不同颜色的盒子，使用不同的z-index（或渲染顺序）和定位方式，让用户理解层叠上下文
   - **概念检查 | Concept Check:** React Native中如何控制元素的层叠顺序？答案：通过渲染顺序，后渲染的在上层
   - **学习目标 | Learning Objective:** 深入理解React Native中没有z-index时的层叠规则

2. **Dimensions应用练习** | **Dimensions Application Exercise**
   - **练习描述 | Exercise Description:** 创建一个"设备信息面板"，显示屏幕的所有维度信息（width, height, scale, fontScale），以及计算出的物理像素、英寸尺寸等
   - **概念检查 | Concept Check:** scale: 3代表什么？答案：1个逻辑像素对应3个物理像素
   - **学习目标 | Learning Objective:** 理解逻辑像素与物理像素的关系

3. **百分比布局整合** | **Percentage Layout Integration**
   - **练习描述 | Exercise Description:** 创建一个"响应式仪表盘"，包含多个图表组件，每个组件的尺寸都基于屏幕百分比，并在横竖屏切换时重新布局
   - **概念检查 | Concept Check:** 如何让组件在旋转时自动更新布局？答案：使用useWindowDimensions hook
   - **学习目标 | Learning Objective:** 掌握基于Dimensions的完全响应式布局

4. **FAB变体练习** | **FAB Variant Exercise**
   - **练习描述 | Exercise Description:** 创建一个"展开式FAB"，点击主FAB后展开显示3个子操作按钮，使用绝对定位和动画效果
   - **概念检查 | Concept Check:** FAB的子按钮应该如何定位？答案：使用绝对定位，相对于主FAB或父容器计算位置
   - **学习目标 | Learning Objective:** 掌握复杂的绝对定位应用场景

5. **遮罩层高级应用** | **Advanced Overlay Application**
   - **练习描述 | Exercise Description:** 创建一个"引导蒙层"系统，高亮显示特定UI元素，其他区域半透明遮罩，包含箭头指示和说明文字
   - **概念检查 | Concept Check:** 如何让遮罩层只遮挡部分内容？答案：使用多个绝对定位的View，配合透明度和形状
   - **学习目标 | Learning Objective:** 理解复杂遮罩层的组合实现

6. **安全区域处理** | **Safe Area Handling**
   - **练习描述 | Exercise Description:** 在有刘海屏的设备上测试FAB和遮罩层，使用SafeAreaView确保所有元素正确显示，不被系统UI遮挡
   - **概念检查 | Concept Check:** SafeAreaView如何与绝对定位配合？答案：绝对定位的bottom、top值应考虑安全区域
   - **学习目标 | Learning Objective:** 掌握现代移动设备的安全区域处理

7. **性能优化练习** | **Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 创建包含100个图片的网格，使用FlatList替代ScrollView，分析绝对定位元素对性能的影响
   - **概念检查 | Concept Check:** 过多的绝对定位元素会影响性能吗？答案：可能会，特别是配合复杂阴影时，应适度使用
   - **学习目标 | Learning Objective:** 理解定位方式对渲染性能的影响

## 学习资源 | Learning Resources
- [React Native Layout Props](https://reactnative.dev/docs/layout-props) - 官方布局属性文档
- [Dimensions API 官方文档](https://reactnative.dev/docs/dimensions) - Dimensions API详细说明
- [useWindowDimensions Hook](https://reactnative.dev/docs/usewindowdimensions) - 响应式尺寸Hook
- [Material Design - FAB](https://material.io/components/buttons-floating-action-button) - FAB设计指南
- [React Native 定位详解](https://medium.com/@shanerudolfworktive/understanding-positioning-in-react-native-b7d7e91f1451) - 深度理解定位
- [响应式设计最佳实践](https://thoughtbot.com/blog/how-to-make-your-react-native-app-responsive) - 响应式布局技巧

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解绝对定位和相对定位的区别 | Understand difference between absolute and relative positioning
- [ ] 能使用top、right、bottom、left精确定位元素 | Can use top, right, bottom, left to precisely position elements
- [ ] 掌握Dimensions API获取屏幕尺寸 | Master Dimensions API to get screen dimensions
- [ ] 能计算基于屏幕百分比的响应式布局 | Can calculate responsive layouts based on screen percentages
- [ ] 会创建徽章和叠加层效果 | Can create badges and overlay effects
- [ ] 能实现固定位置的FAB | Can implement fixed-position FAB
- [ ] 理解React Native中没有fixed定位的替代方案 | Understand alternatives to fixed positioning in React Native
- [ ] 会处理横竖屏切换的响应式布局 | Can handle responsive layouts for orientation changes
- [ ] 掌握useWindowDimensions hook的使用 | Master useWindowDimensions hook usage
- [ ] 完成图片浏览器实践项目 | Complete image gallery practical project

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释绝对定位、Dimensions API和响应式百分比布局的工作原理。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain how absolute positioning, Dimensions API, and responsive percentage layouts work to others.
