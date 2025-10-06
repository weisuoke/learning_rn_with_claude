# React Native入门 - 第4天：ScrollView和用户交互 | React Native Introduction - Day 4: ScrollView and User Interaction

## 学习目标 | Learning Objectives
- 理解ScrollView组件及其在移动开发中的应用 | Understand the ScrollView component and its applications in mobile development
- 掌握垂直和水平滚动视图的实现方法 | Master implementation of vertical and horizontal scroll views
- 学习移动端用户交互组件：Button和Pressable | Learn mobile user interaction components: Button and Pressable
- 了解触摸反馈和交互设计的最佳实践 | Understand touch feedback and interaction design best practices
- 区分现代交互组件与遗留组件（TouchableOpacity等） | Differentiate between modern interaction components and legacy components (TouchableOpacity, etc.)
- 构建具有良好用户体验的可滚动移动界面 | Build scrollable mobile interfaces with excellent user experience

## 详细内容 | Detailed Content

### 1. ScrollView基础概念 | ScrollView Fundamentals (1小时 | 1 hour)

- **ScrollView核心概念 | ScrollView Core Concept**

  **概念定义 | Concept Definition:**
  ScrollView是React Native中的一个容器组件，它允许用户通过触摸滑动来查看超出屏幕显示范围的内容。与Web开发中的overflow: scroll不同，ScrollView是专为触摸交互优化的移动原生组件。| ScrollView is a container component in React Native that allows users to view content exceeding the screen's visible area through touch gestures. Unlike Web's overflow: scroll, ScrollView is a native mobile component optimized for touch interaction.

  **核心特征 | Key Characteristics:**
  - ScrollView会立即渲染所有子组件，无论是否可见 | ScrollView renders all children immediately, whether visible or not
  - 适用于内容数量有限的场景（通常少于几十个项目） | Suitable for scenarios with limited content (typically fewer than dozens of items)
  - 支持垂直和水平两个滚动方向 | Supports both vertical and horizontal scroll directions
  - 提供丰富的滚动事件和控制方法 | Provides rich scroll events and control methods
  - 在iOS和Android上有原生的滚动性能和感觉 | Has native scroll performance and feel on both iOS and Android

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. ScrollView会等待用户滚动到某个位置才渲染该位置的内容吗？| Does ScrollView wait until the user scrolls to a position before rendering content at that position?
     **答案 | Answer:** 否 | No - ScrollView立即渲染所有子组件，这是它与FlatList的关键区别 | ScrollView renders all children immediately, which is a key difference from FlatList
  2. 一个ScrollView可以同时在垂直和水平方向滚动吗？| Can a ScrollView scroll in both vertical and horizontal directions simultaneously?
     **答案 | Answer:** 否 | No - ScrollView一次只能在一个方向滚动，由horizontal属性决定 | ScrollView can only scroll in one direction at a time, determined by the horizontal prop
  3. 当内容少于屏幕高度时，ScrollView还能滚动吗？| Can ScrollView still scroll when content is less than screen height?
     **答案 | Answer:** 默认不能，但可以通过contentContainerStyle设置minHeight使其可滚动 | By default no, but you can set minHeight in contentContainerStyle to make it scrollable
  4. ScrollView适合显示1000个列表项吗？| Is ScrollView suitable for displaying 1000 list items?
     **答案 | Answer:** 不适合 | No - 应该使用FlatList或SectionList，因为ScrollView会立即渲染所有项导致性能问题 | Should use FlatList or SectionList instead, as ScrollView renders all items immediately causing performance issues

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { ScrollView, View, Text, StyleSheet } from 'react-native';

  // 基础垂直滚动示例 | Basic vertical scroll example
  const BasicScrollViewExample = () => {
    return (
      <ScrollView style={styles.container}>
        {/* 创建多个内容块来演示滚动 | Create multiple content blocks to demonstrate scrolling */}
        {[1, 2, 3, 4, 5, 6, 7, 8].map((item) => (
          <View key={item} style={styles.contentBlock}>
            <Text style={styles.text}>内容块 {item} | Content Block {item}</Text>
          </View>
        ))}
      </ScrollView>
    );
  };

  // 使用contentContainerStyle控制内容样式 | Using contentContainerStyle to control content styling
  const ScrollViewWithPadding = () => {
    return (
      <ScrollView
        style={styles.scrollView}
        contentContainerStyle={styles.contentContainer} // 注意：样式应用于内容容器而非ScrollView本身 | Note: styles apply to content container, not ScrollView itself
      >
        <Text>这里的padding是在contentContainer上 | This padding is on the contentContainer</Text>
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f0f0f0',
    },
    scrollView: {
      backgroundColor: '#e0e0e0',
    },
    contentContainer: {
      padding: 20, // 内容周围的填充 | Padding around content
      alignItems: 'center', // 内容居中对齐 | Center align content
    },
    contentBlock: {
      width: '100%',
      height: 150,
      backgroundColor: '#4a90e2',
      marginBottom: 15,
      borderRadius: 10,
      justifyContent: 'center',
      alignItems: 'center',
    },
    text: {
      color: 'white',
      fontSize: 18,
      fontWeight: 'bold',
    },
  });

  export default BasicScrollViewExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 上面的代码中，style和contentContainerStyle有什么区别？| What's the difference between style and contentContainerStyle in the code above?
    **答案 | Answer:** style应用于ScrollView容器本身（控制外部尺寸和背景），contentContainerStyle应用于包裹所有子元素的内容容器（控制内容的padding、对齐等） | style applies to the ScrollView container itself (controls outer dimensions and background), contentContainerStyle applies to the content wrapper containing all children (controls content padding, alignment, etc.)
  - 如果想让ScrollView占据剩余屏幕空间，应该使用什么样式？| What style should be used to make ScrollView occupy remaining screen space?
    **答案 | Answer:** style={{ flex: 1 }}，这会让ScrollView扩展填充父容器的可用空间 | style={{ flex: 1 }}, which makes ScrollView expand to fill available space in parent container

  **常见误区检查 | Common Misconception Checks:**
  - 我可以在ScrollView的style属性中设置padding来控制内容边距吗？| Can I set padding in ScrollView's style prop to control content margins?
    **答案 | Answer:** 技术上可以，但不推荐。最佳实践是在contentContainerStyle中设置padding，这样padding会随内容一起滚动 | Technically yes, but not recommended. Best practice is to set padding in contentContainerStyle so padding scrolls with content
  - ScrollView和Web中的div设置overflow: scroll是一样的吗？| Is ScrollView the same as a div with overflow: scroll in Web?
    **答案 | Answer:** 概念相似但实现完全不同。ScrollView是原生组件，具有原生触摸手势、惯性滚动和平台特定的滚动行为，而不仅仅是CSS属性 | Conceptually similar but implementation is completely different. ScrollView is a native component with native touch gestures, momentum scrolling, and platform-specific scroll behavior, not just a CSS property

- **水平ScrollView | Horizontal ScrollView**

  **概念定义 | Concept Definition:**
  通过设置horizontal={true}属性，ScrollView可以改变滚动方向为水平。水平滚动常用于图片画廊、标签选择器和横向内容列表。| By setting the horizontal={true} prop, ScrollView changes its scroll direction to horizontal. Horizontal scrolling is commonly used for image galleries, tab selectors, and horizontal content lists.

  **核心特征 | Key Characteristics:**
  - 需要显式设置horizontal={true}来启用水平滚动 | Requires explicitly setting horizontal={true} to enable horizontal scrolling
  - 子元素会自动水平排列，无需额外的flexDirection设置 | Child elements automatically arrange horizontally without needing flexDirection settings
  - 通常需要设置showsHorizontalScrollIndicator={false}来隐藏滚动条以获得更好的视觉效果 | Often requires showsHorizontalScrollIndicator={false} to hide scrollbar for better visual appearance
  - 子元素需要有明确的宽度，否则可能无法正确显示 | Child elements need explicit width, otherwise they may not display correctly

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 设置horizontal={true}后，子元素的排列方式会自动改变吗？| After setting horizontal={true}, do child elements automatically change their arrangement?
     **答案 | Answer:** 是 | Yes - 子元素会自动水平排列，就像设置了flexDirection: 'row' | Child elements automatically arrange horizontally, as if flexDirection: 'row' was set
  2. 水平ScrollView中的子元素是否需要设置宽度？| Do child elements in horizontal ScrollView need a width set?
     **答案 | Answer:** 是 | Yes - 没有明确宽度的子元素可能会收缩到最小尺寸或显示异常 | Without explicit width, child elements may shrink to minimum size or display incorrectly
  3. 可以通过CSS的flexDirection: 'row'来创建水平滚动吗？| Can you create horizontal scrolling with CSS flexDirection: 'row'?
     **答案 | Answer:** 不能 | No - 必须使用horizontal={true}属性，仅使用flexDirection不会启用滚动功能 | Must use horizontal={true} prop, using only flexDirection won't enable scrolling functionality

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { ScrollView, View, Image, Text, StyleSheet, Dimensions } from 'react-native';

  const { width } = Dimensions.get('window'); // 获取屏幕宽度 | Get screen width

  // 水平图片画廊示例 | Horizontal image gallery example
  const HorizontalGallery = () => {
    const images = [
      'https://picsum.photos/300/200?random=1',
      'https://picsum.photos/300/200?random=2',
      'https://picsum.photos/300/200?random=3',
      'https://picsum.photos/300/200?random=4',
      'https://picsum.photos/300/200?random=5',
    ];

    return (
      <View style={styles.galleryContainer}>
        <Text style={styles.title}>图片画廊 | Image Gallery</Text>
        <ScrollView
          horizontal={true} // 启用水平滚动 | Enable horizontal scrolling
          showsHorizontalScrollIndicator={false} // 隐藏滚动条 | Hide scrollbar
          pagingEnabled={false} // 设为true可启用分页效果 | Set to true to enable paging effect
          contentContainerStyle={styles.galleryContent}
        >
          {images.map((uri, index) => (
            <View key={index} style={styles.imageContainer}>
              <Image
                source={{ uri }}
                style={styles.image}
                resizeMode="cover"
              />
              <Text style={styles.imageLabel}>照片 {index + 1} | Photo {index + 1}</Text>
            </View>
          ))}
        </ScrollView>
      </View>
    );
  };

  // 水平标签选择器示例 | Horizontal tag selector example
  const HorizontalTags = () => {
    const tags = ['全部 | All', '热门 | Popular', '最新 | Latest', '推荐 | Recommended',
                  '技术 | Tech', '设计 | Design', '商业 | Business', '生活 | Lifestyle'];

    return (
      <ScrollView
        horizontal
        showsHorizontalScrollIndicator={false}
        contentContainerStyle={styles.tagsContainer}
      >
        {tags.map((tag, index) => (
          <View key={index} style={styles.tag}>
            <Text style={styles.tagText}>{tag}</Text>
          </View>
        ))}
      </ScrollView>
    );
  };

  const styles = StyleSheet.create({
    galleryContainer: {
      marginVertical: 20,
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginLeft: 15,
      marginBottom: 10,
    },
    galleryContent: {
      paddingHorizontal: 15, // 两端留白 | Padding on both ends
    },
    imageContainer: {
      marginRight: 15, // 图片间距 | Spacing between images
      alignItems: 'center',
    },
    image: {
      width: 280, // 明确的宽度 | Explicit width
      height: 180,
      borderRadius: 12,
    },
    imageLabel: {
      marginTop: 8,
      fontSize: 14,
      color: '#666',
    },
    tagsContainer: {
      paddingHorizontal: 15,
      paddingVertical: 10,
    },
    tag: {
      backgroundColor: '#e0e0e0',
      paddingHorizontal: 16,
      paddingVertical: 8,
      borderRadius: 20,
      marginRight: 10, // 标签间距 | Spacing between tags
    },
    tagText: {
      fontSize: 14,
      color: '#333',
    },
  });

  export default HorizontalGallery;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - pagingEnabled属性有什么作用？| What does the pagingEnabled prop do?
    **答案 | Answer:** 启用分页滚动，每次滚动会停在一个"页面"（通常是屏幕宽度）的位置，类似轮播图效果 | Enables paged scrolling where each scroll stops at a "page" position (usually screen width), similar to carousel effect
  - 为什么水平ScrollView的子元素需要marginRight而不是marginBottom？| Why do horizontal ScrollView children need marginRight instead of marginBottom?
    **答案 | Answer:** 因为子元素是水平排列的，marginRight创建元素之间的间距，而marginBottom不会产生视觉效果 | Because children are arranged horizontally, marginRight creates spacing between elements, while marginBottom has no visual effect

  **常见误区检查 | Common Misconception Checks:**
  - 我可以嵌套两个ScrollView实现同时水平和垂直滚动吗？| Can I nest two ScrollViews to achieve both horizontal and vertical scrolling?
    **答案 | Answer:** 技术上可以，但会导致手势冲突和糟糕的用户体验。如果需要2D滚动，考虑使用其他方案如react-native-gesture-handler | Technically yes, but causes gesture conflicts and poor UX. For 2D scrolling, consider alternatives like react-native-gesture-handler

### 2. ScrollView高级特性 | ScrollView Advanced Features (1小时 | 1 hour)

- **ScrollView事件和方法 | ScrollView Events and Methods**

  **概念定义 | Concept Definition:**
  ScrollView提供了丰富的事件回调（如onScroll、onMomentumScrollEnd）和控制方法（如scrollTo、scrollToEnd），允许开发者精确控制和响应滚动行为。| ScrollView provides rich event callbacks (like onScroll, onMomentumScrollEnd) and control methods (like scrollTo, scrollToEnd), allowing developers to precisely control and respond to scroll behavior.

  **核心特征 | Key Characteristics:**
  - onScroll事件在滚动时持续触发，可获取当前滚动位置 | onScroll event fires continuously during scrolling, providing current scroll position
  - 需要使用useRef创建引用来调用ScrollView的方法 | Need to use useRef to create a reference for calling ScrollView methods
  - scrollTo方法可以编程式滚动到指定位置 | scrollTo method allows programmatic scrolling to specific positions
  - onMomentumScrollEnd在滚动惯性结束时触发 | onMomentumScrollEnd fires when scroll momentum ends

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. onScroll事件每次滚动时都会触发吗？| Does onScroll event fire every time during scrolling?
     **答案 | Answer:** 是，它会持续触发 | Yes, it fires continuously - 默认每帧触发一次，可以通过scrollEventThrottle控制频率 | Fires once per frame by default, can control frequency with scrollEventThrottle
  2. 要调用scrollTo方法，需要先做什么准备？| What preparation is needed before calling scrollTo method?
     **答案 | Answer:** 需要使用useRef创建ScrollView的引用 | Need to create a ScrollView reference using useRef - 然后通过ref.current.scrollTo()调用 | Then call via ref.current.scrollTo()
  3. scrollTo和scrollToEnd的区别是什么？| What's the difference between scrollTo and scrollToEnd?
     **答案 | Answer:** scrollTo需要指定具体坐标位置(x, y)，scrollToEnd直接滚动到内容末尾 | scrollTo requires specific coordinates (x, y), scrollToEnd directly scrolls to content end
  4. onScroll事件每次都重新渲染组件会影响性能吗？| Will re-rendering on every onScroll event affect performance?
     **答案 | Answer:** 是 | Yes - 应该使用scrollEventThrottle限制触发频率，或使用Animated.event优化性能 | Should use scrollEventThrottle to limit frequency, or use Animated.event for performance optimization

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useRef, useState } from 'react';
  import { ScrollView, View, Text, Button, StyleSheet } from 'react-native';

  // ScrollView引用和方法示例 | ScrollView ref and methods example
  const ScrollViewMethodsExample = () => {
    const scrollViewRef = useRef(null); // 创建ScrollView引用 | Create ScrollView reference
    const [scrollPosition, setScrollPosition] = useState(0); // 追踪滚动位置 | Track scroll position

    // 滚动到顶部方法 | Scroll to top method
    const scrollToTop = () => {
      scrollViewRef.current?.scrollTo({
        x: 0,
        y: 0,
        animated: true, // 启用动画 | Enable animation
      });
    };

    // 滚动到底部方法 | Scroll to bottom method
    const scrollToBottom = () => {
      scrollViewRef.current?.scrollToEnd({
        animated: true,
      });
    };

    // 滚动到中间位置方法 | Scroll to middle method
    const scrollToMiddle = () => {
      scrollViewRef.current?.scrollTo({
        x: 0,
        y: 400, // 具体的y坐标 | Specific y coordinate
        animated: true,
      });
    };

    // 处理滚动事件 | Handle scroll event
    const handleScroll = (event) => {
      const yOffset = event.nativeEvent.contentOffset.y; // 获取垂直滚动偏移 | Get vertical scroll offset
      setScrollPosition(Math.round(yOffset)); // 更新滚动位置状态 | Update scroll position state
    };

    return (
      <View style={styles.container}>
        {/* 控制按钮 | Control buttons */}
        <View style={styles.controls}>
          <Text style={styles.positionText}>
            当前位置 | Current Position: {scrollPosition}px
          </Text>
          <View style={styles.buttonRow}>
            <Button title="顶部 | Top" onPress={scrollToTop} />
            <Button title="中间 | Middle" onPress={scrollToMiddle} />
            <Button title="底部 | Bottom" onPress={scrollToBottom} />
          </View>
        </View>

        {/* ScrollView内容 | ScrollView content */}
        <ScrollView
          ref={scrollViewRef} // 关联引用 | Associate reference
          style={styles.scrollView}
          onScroll={handleScroll} // 滚动事件处理 | Scroll event handler
          scrollEventThrottle={16} // 每16ms触发一次(约60fps) | Fire every 16ms (~60fps)
        >
          {[...Array(20)].map((_, index) => (
            <View key={index} style={styles.item}>
              <Text style={styles.itemText}>
                项目 {index + 1} | Item {index + 1}
              </Text>
            </View>
          ))}
        </ScrollView>
      </View>
    );
  };

  // 滚动进度指示器示例 | Scroll progress indicator example
  const ScrollProgressIndicator = () => {
    const [scrollProgress, setScrollProgress] = useState(0); // 滚动进度百分比 | Scroll progress percentage

    const handleScroll = (event) => {
      const { contentOffset, contentSize, layoutMeasurement } = event.nativeEvent;
      // 计算滚动进度：当前位置 / (总内容高度 - 可见区域高度) | Calculate scroll progress: current position / (total content height - visible area height)
      const scrollHeight = contentSize.height - layoutMeasurement.height;
      const progress = (contentOffset.y / scrollHeight) * 100;
      setScrollProgress(Math.max(0, Math.min(100, progress))); // 限制在0-100之间 | Limit between 0-100
    };

    return (
      <View style={styles.container}>
        {/* 进度条 | Progress bar */}
        <View style={styles.progressContainer}>
          <View style={[styles.progressBar, { width: `${scrollProgress}%` }]} />
          <Text style={styles.progressText}>{Math.round(scrollProgress)}%</Text>
        </View>

        <ScrollView
          onScroll={handleScroll}
          scrollEventThrottle={16}
        >
          {[...Array(30)].map((_, index) => (
            <View key={index} style={styles.contentBlock}>
              <Text>内容块 {index + 1} | Content Block {index + 1}</Text>
            </View>
          ))}
        </ScrollView>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    controls: {
      padding: 15,
      backgroundColor: '#f8f8f8',
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    positionText: {
      fontSize: 16,
      marginBottom: 10,
      textAlign: 'center',
    },
    buttonRow: {
      flexDirection: 'row',
      justifyContent: 'space-around',
    },
    scrollView: {
      flex: 1,
    },
    item: {
      height: 80,
      justifyContent: 'center',
      alignItems: 'center',
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    itemText: {
      fontSize: 18,
    },
    progressContainer: {
      height: 30,
      backgroundColor: '#f0f0f0',
      justifyContent: 'center',
      position: 'relative',
    },
    progressBar: {
      height: '100%',
      backgroundColor: '#4a90e2',
      position: 'absolute',
    },
    progressText: {
      textAlign: 'center',
      fontWeight: 'bold',
      zIndex: 1,
    },
    contentBlock: {
      padding: 20,
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
  });

  export default ScrollViewMethodsExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - scrollEventThrottle={16}中的16代表什么？| What does the 16 in scrollEventThrottle={16} represent?
    **答案 | Answer:** 16毫秒，即每16ms触发一次onScroll事件，约等于60帧每秒 | 16 milliseconds, meaning onScroll fires every 16ms, approximately 60 frames per second
  - 为什么使用scrollViewRef.current?.scrollTo()而不是scrollViewRef.current.scrollTo()？| Why use scrollViewRef.current?.scrollTo() instead of scrollViewRef.current.scrollTo()?
    **答案 | Answer:** 使用可选链操作符(?)避免ref为null时崩溃，这是更安全的写法 | Using optional chaining (?) prevents crashes when ref is null, it's a safer approach

  **常见误区检查 | Common Misconception Checks:**
  - 我可以在onScroll中直接调用setState更新状态吗？| Can I directly call setState in onScroll to update state?
    **答案 | Answer:** 可以但要谨慎 | Yes but with caution - onScroll触发频繁，每次都更新状态可能导致性能问题。对于复杂状态更新，考虑使用debounce或Animated.event | onScroll fires frequently, updating state every time may cause performance issues. For complex state updates, consider using debounce or Animated.event

### 3. Button组件 | Button Component (30分钟 | 30 minutes)

- **Button基础用法 | Button Basic Usage**

  **概念定义 | Concept Definition:**
  Button是React Native提供的基础按钮组件，用于响应用户的点击操作。它是一个跨平台组件，但在iOS和Android上有不同的默认外观。| Button is a basic button component provided by React Native for responding to user taps. It's a cross-platform component but has different default appearances on iOS and Android.

  **核心特征 | Key Characteristics:**
  - Button使用onPress属性而不是Web的onClick | Button uses onPress prop instead of Web's onClick
  - Button的样式定制能力非常有限（只能改变颜色） | Button has very limited styling capabilities (can only change color)
  - 在iOS上显示为文本按钮，在Android上显示为带背景的按钮 | Displays as text button on iOS, as button with background on Android
  - Button不是自闭合标签，通过title属性设置文本 | Button is not self-closing, sets text via title prop
  - 按钮文本会自动转换为大写（Android平台） | Button text automatically converts to uppercase (Android platform)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Button组件的点击事件处理器叫什么？| What's the name of Button component's tap event handler?
     **答案 | Answer:** onPress - 不是onClick，这是移动端触摸事件的标准命名 | onPress - not onClick, this is the standard naming for mobile touch events
  2. Button组件可以自由设置padding、borderRadius等样式吗？| Can Button component freely set padding, borderRadius and other styles?
     **答案 | Answer:** 不能 | No - Button只支持color属性改变颜色，要完全自定义样式需要使用Pressable或Touchable组件 | Button only supports color prop to change color, need to use Pressable or Touchable components for full style customization
  3. 在iOS和Android上，Button的默认外观相同吗？| Do Button components have the same default appearance on iOS and Android?
     **答案 | Answer:** 不相同 | No - iOS显示蓝色文本按钮，Android显示带背景色的凸起按钮 | iOS shows blue text button, Android shows raised button with background color
  4. Button组件的文本内容如何设置？| How do you set Button component's text content?
     **答案 | Answer:** 使用title属性 | Using title prop - 而不是像Text组件那样作为children | Not as children like Text component

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import { View, Button, Text, StyleSheet, Alert } from 'react-native';

  // Button基础示例 | Button basic example
  const BasicButtonExample = () => {
    const [count, setCount] = useState(0); // 计数状态 | Count state

    // 按钮点击处理函数 | Button press handler
    const handlePress = () => {
      setCount(count + 1);
      console.log('按钮被点击 | Button pressed:', count + 1);
    };

    // 显示警告对话框 | Show alert dialog
    const showAlert = () => {
      Alert.alert(
        '警告标题 | Alert Title',
        '这是一个警告消息 | This is an alert message',
        [
          { text: '取消 | Cancel', style: 'cancel' },
          { text: '确定 | OK', onPress: () => console.log('OK pressed') },
        ]
      );
    };

    return (
      <View style={styles.container}>
        <Text style={styles.counterText}>
          点击次数 | Click Count: {count}
        </Text>

        {/* 基础按钮 | Basic button */}
        <Button
          title="点击我 | Click Me"
          onPress={handlePress} // 点击处理器 | Press handler
        />

        <View style={styles.spacer} />

        {/* 自定义颜色按钮 | Custom color button */}
        <Button
          title="蓝色按钮 | Blue Button"
          color="#4a90e2" // iOS上改变文本颜色，Android上改变背景色 | Changes text color on iOS, background on Android
          onPress={() => console.log('蓝色按钮点击 | Blue button pressed')}
        />

        <View style={styles.spacer} />

        {/* 禁用状态按钮 | Disabled button */}
        <Button
          title="禁用按钮 | Disabled Button"
          disabled={true} // 禁用按钮 | Disable button
          onPress={() => console.log('这不会被触发 | This won't fire')}
        />

        <View style={styles.spacer} />

        {/* 触发警告对话框 | Trigger alert dialog */}
        <Button
          title="显示警告 | Show Alert"
          color="#e74c3c"
          onPress={showAlert}
        />

        {/* 注意：以下是错误示例 | Note: Following are incorrect examples */}
        {/* ❌ 错误：Button不支持style属性 | Wrong: Button doesn't support style prop */}
        {/* <Button title="错误示例" style={{ borderRadius: 10 }} /> */}

        {/* ❌ 错误：Button不能包含子元素 | Wrong: Button can't contain children */}
        {/* <Button onPress={handlePress}><Text>错误</Text></Button> */}
      </View>
    );
  };

  // Button的限制和替代方案 | Button limitations and alternatives
  const ButtonLimitationsExample = () => {
    return (
      <View style={styles.container}>
        <Text style={styles.sectionTitle}>
          Button组件的限制 | Button Component Limitations
        </Text>

        <View style={styles.limitationItem}>
          <Text style={styles.limitationText}>
            ❌ 不能自定义边框圆角 | Cannot customize border radius
          </Text>
          <Text style={styles.limitationText}>
            ❌ 不能添加图标 | Cannot add icons
          </Text>
          <Text style={styles.limitationText}>
            ❌ 不能控制内边距 | Cannot control padding
          </Text>
          <Text style={styles.limitationText}>
            ❌ 不能设置渐变背景 | Cannot set gradient background
          </Text>
          <Text style={styles.limitationText}>
            ✅ 只能改变颜色(color属性) | Can only change color (color prop)
          </Text>
        </View>

        <Text style={styles.note}>
          💡 提示：需要自定义样式时，使用Pressable组件 | Tip: Use Pressable component when custom styling is needed
        </Text>

        {/* 平台差异示例 | Platform differences example */}
        <View style={styles.platformComparison}>
          <Text style={styles.platformTitle}>平台差异 | Platform Differences:</Text>
          <Text>• iOS: 蓝色文本按钮 | Blue text button</Text>
          <Text>• Android: 带背景的凸起按钮 | Raised button with background</Text>
          <Text>• Android: 文本自动大写 | Text auto-uppercase</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      justifyContent: 'center',
      padding: 20,
      backgroundColor: '#fff',
    },
    counterText: {
      fontSize: 24,
      textAlign: 'center',
      marginBottom: 20,
      fontWeight: 'bold',
    },
    spacer: {
      height: 15, // 按钮之间的间距 | Spacing between buttons
    },
    sectionTitle: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 15,
    },
    limitationItem: {
      backgroundColor: '#f8f8f8',
      padding: 15,
      borderRadius: 8,
      marginBottom: 15,
    },
    limitationText: {
      fontSize: 14,
      marginBottom: 8,
    },
    note: {
      fontSize: 14,
      color: '#666',
      fontStyle: 'italic',
      marginTop: 10,
    },
    platformComparison: {
      marginTop: 20,
      padding: 15,
      backgroundColor: '#e8f4f8',
      borderRadius: 8,
    },
    platformTitle: {
      fontWeight: 'bold',
      marginBottom: 8,
    },
  });

  export default BasicButtonExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么Button组件不推荐用于生产环境的自定义界面？| Why is Button component not recommended for production custom UIs?
    **答案 | Answer:** 因为Button样式定制能力极其有限，无法满足现代应用的设计需求，且在不同平台上外观不一致 | Because Button has extremely limited styling capabilities, cannot meet modern app design requirements, and has inconsistent appearance across platforms
  - Web开发中的onClick在React Native中对应什么？| What's the React Native equivalent of onClick from Web development?
    **答案 | Answer:** onPress - 这是React Native中所有触摸事件的标准命名 | onPress - this is the standard naming for all touch events in React Native

  **常见误区检查 | Common Misconception Checks:**
  - 我可以通过传入style属性来自定义Button的外观吗？| Can I customize Button's appearance by passing a style prop?
    **答案 | Answer:** 不能 | No - Button组件不接受style属性，传入会被忽略。需要自定义样式时必须使用Pressable组件 | Button component doesn't accept style prop, it will be ignored. Must use Pressable component when custom styling is needed

### 4. Pressable组件 | Pressable Component (1小时 | 1 hour)

- **Pressable核心概念 | Pressable Core Concept**

  **概念定义 | Concept Definition:**
  Pressable是React Native推荐的现代触摸响应组件，它提供了完全的样式控制能力和丰富的交互状态（按下、悬停、聚焦等），用于创建自定义按钮和可交互元素。| Pressable is React Native's recommended modern touch-responsive component that provides full styling control and rich interaction states (pressed, hovered, focused, etc.) for creating custom buttons and interactive elements.

  **核心特征 | Key Characteristics:**
  - Pressable是一个容器组件，可以包含任何子元素 | Pressable is a container component that can contain any child elements
  - 支持基于交互状态的动态样式（pressed状态） | Supports dynamic styling based on interaction states (pressed state)
  - 提供onPressIn、onPressOut等细粒度的触摸事件 | Provides fine-grained touch events like onPressIn, onPressOut
  - style属性可以是函数，根据pressed状态返回不同样式 | style prop can be a function that returns different styles based on pressed state
  - 完全跨平台兼容，外观由开发者完全控制 | Fully cross-platform compatible with developer-controlled appearance

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Pressable和Button的主要区别是什么？| What's the main difference between Pressable and Button?
     **答案 | Answer:** Pressable支持完全自定义样式和子元素，Button样式定制能力非常有限 | Pressable supports full custom styling and children, Button has very limited styling capabilities
  2. Pressable的style属性可以是函数吗？| Can Pressable's style prop be a function?
     **答案 | Answer:** 是 | Yes - style可以是函数，接收pressed状态参数，返回对应的样式对象 | style can be a function that receives pressed state parameter and returns corresponding style object
  3. Pressable必须包含文本内容吗？| Must Pressable contain text content?
     **答案 | Answer:** 不必须 | No - Pressable可以包含任何React元素：文本、图标、图片或其他组件 | Pressable can contain any React elements: text, icons, images, or other components
  4. onPress和onPressIn有什么区别？| What's the difference between onPress and onPressIn?
     **答案 | Answer:** onPressIn在手指按下时立即触发，onPress在手指抬起时触发（完整的点击操作） | onPressIn fires immediately when finger touches down, onPress fires when finger lifts (complete tap operation)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import { View, Text, Pressable, StyleSheet } from 'react-native';

  // Pressable基础示例 | Pressable basic example
  const BasicPressableExample = () => {
    const [count, setCount] = useState(0);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>Pressable基础用法 | Pressable Basic Usage</Text>

        {/* 使用style函数实现按下效果 | Using style function for press effect */}
        <Pressable
          style={({ pressed }) => [
            styles.button,
            pressed && styles.buttonPressed, // 按下时应用额外样式 | Apply additional style when pressed
          ]}
          onPress={() => setCount(count + 1)} // 点击事件 | Press event
        >
          <Text style={styles.buttonText}>
            点击次数: {count} | Press Count: {count}
          </Text>
        </Pressable>

        {/* 使用children函数实现内容变化 | Using children function for content change */}
        <Pressable
          style={styles.button}
          onPress={() => console.log('按下 | Pressed')}
        >
          {({ pressed }) => (
            <Text style={styles.buttonText}>
              {pressed ? '按下中... | Pressing...' : '点击我 | Click Me'}
            </Text>
          )}
        </Pressable>
      </View>
    );
  };

  // 自定义按钮组件示例 | Custom button component example
  const CustomButton = ({ title, onPress, variant = 'primary' }) => {
    // 根据variant选择样式 | Select styles based on variant
    const getButtonStyles = (pressed) => {
      const baseStyle = styles.customButton;
      let variantStyle = styles.primaryButton;
      let pressedStyle = styles.primaryButtonPressed;

      if (variant === 'secondary') {
        variantStyle = styles.secondaryButton;
        pressedStyle = styles.secondaryButtonPressed;
      } else if (variant === 'danger') {
        variantStyle = styles.dangerButton;
        pressedStyle = styles.dangerButtonPressed;
      }

      return [baseStyle, variantStyle, pressed && pressedStyle];
    };

    const getTextStyles = () => {
      if (variant === 'secondary') {
        return [styles.customButtonText, styles.secondaryButtonText];
      }
      return styles.customButtonText;
    };

    return (
      <Pressable
        style={({ pressed }) => getButtonStyles(pressed)}
        onPress={onPress}
        android_ripple={{ color: 'rgba(255, 255, 255, 0.3)' }} // Android波纹效果 | Android ripple effect
      >
        <Text style={getTextStyles()}>{title}</Text>
      </Pressable>
    );
  };

  // 带图标的按钮示例 | Button with icon example
  const IconButton = ({ icon, text, onPress }) => {
    return (
      <Pressable
        style={({ pressed }) => [
          styles.iconButton,
          pressed && styles.iconButtonPressed,
        ]}
        onPress={onPress}
      >
        <View style={styles.iconContainer}>
          <Text style={styles.icon}>{icon}</Text>
          <Text style={styles.iconButtonText}>{text}</Text>
        </View>
      </Pressable>
    );
  };

  // 综合示例 | Comprehensive example
  const PressableShowcase = () => {
    const [pressState, setPressState] = useState('等待中... | Waiting...');

    const handlePressIn = () => setPressState('手指按下 | Finger Down');
    const handlePressOut = () => setPressState('手指抬起 | Finger Up');
    const handlePress = () => setPressState('完成点击 | Tap Complete');
    const handleLongPress = () => setPressState('长按触发 | Long Press Triggered');

    return (
      <View style={styles.showcaseContainer}>
        <Text style={styles.stateText}>状态 | State: {pressState}</Text>

        {/* 演示所有触摸事件 | Demonstrate all touch events */}
        <Pressable
          style={({ pressed }) => [
            styles.eventButton,
            pressed && styles.eventButtonPressed,
          ]}
          onPressIn={handlePressIn} // 按下时 | On press down
          onPressOut={handlePressOut} // 抬起时 | On press up
          onPress={handlePress} // 点击完成 | On tap complete
          onLongPress={handleLongPress} // 长按 | On long press
          delayLongPress={500} // 长按延迟时间(ms) | Long press delay (ms)
        >
          <Text style={styles.eventButtonText}>
            按我测试所有事件 | Press Me to Test All Events
          </Text>
        </Pressable>

        {/* 自定义按钮变体示例 | Custom button variants example */}
        <View style={styles.buttonGroup}>
          <CustomButton
            title="主要按钮 | Primary"
            variant="primary"
            onPress={() => console.log('Primary pressed')}
          />
          <CustomButton
            title="次要按钮 | Secondary"
            variant="secondary"
            onPress={() => console.log('Secondary pressed')}
          />
          <CustomButton
            title="危险按钮 | Danger"
            variant="danger"
            onPress={() => console.log('Danger pressed')}
          />
        </View>

        {/* 图标按钮示例 | Icon button examples */}
        <View style={styles.iconButtonGroup}>
          <IconButton icon="❤️" text="喜欢 | Like" onPress={() => {}} />
          <IconButton icon="💬" text="评论 | Comment" onPress={() => {}} />
          <IconButton icon="↗️" text="分享 | Share" onPress={() => {}} />
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    button: {
      backgroundColor: '#4a90e2',
      padding: 15,
      borderRadius: 8,
      marginVertical: 10,
      minWidth: 200,
      alignItems: 'center',
    },
    buttonPressed: {
      backgroundColor: '#357abd', // 按下时变深 | Darker when pressed
      transform: [{ scale: 0.98 }], // 轻微缩小效果 | Slight scale down effect
    },
    buttonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    customButton: {
      padding: 15,
      borderRadius: 10,
      marginVertical: 8,
      minWidth: 250,
      alignItems: 'center',
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3, // Android阴影 | Android shadow
    },
    primaryButton: {
      backgroundColor: '#4a90e2',
    },
    primaryButtonPressed: {
      backgroundColor: '#357abd',
      elevation: 1, // 按下时减小阴影 | Reduce shadow when pressed
    },
    secondaryButton: {
      backgroundColor: 'transparent',
      borderWidth: 2,
      borderColor: '#4a90e2',
    },
    secondaryButtonPressed: {
      backgroundColor: '#f0f0f0',
    },
    dangerButton: {
      backgroundColor: '#e74c3c',
    },
    dangerButtonPressed: {
      backgroundColor: '#c0392b',
    },
    customButtonText: {
      fontSize: 16,
      fontWeight: '600',
      color: 'white',
    },
    secondaryButtonText: {
      color: '#4a90e2',
    },
    iconButton: {
      backgroundColor: '#f8f8f8',
      padding: 12,
      borderRadius: 8,
      marginHorizontal: 5,
      minWidth: 100,
      alignItems: 'center',
    },
    iconButtonPressed: {
      backgroundColor: '#e0e0e0',
    },
    iconContainer: {
      alignItems: 'center',
    },
    icon: {
      fontSize: 24,
      marginBottom: 5,
    },
    iconButtonText: {
      fontSize: 12,
      color: '#333',
    },
    showcaseContainer: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    stateText: {
      fontSize: 18,
      textAlign: 'center',
      marginBottom: 20,
      padding: 15,
      backgroundColor: '#f0f0f0',
      borderRadius: 8,
      fontWeight: '600',
    },
    eventButton: {
      backgroundColor: '#9b59b6',
      padding: 20,
      borderRadius: 10,
      marginBottom: 30,
    },
    eventButtonPressed: {
      backgroundColor: '#8e44ad',
    },
    eventButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
      textAlign: 'center',
    },
    buttonGroup: {
      marginBottom: 30,
    },
    iconButtonGroup: {
      flexDirection: 'row',
      justifyContent: 'space-around',
    },
  });

  export default PressableShowcase;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - style={({ pressed }) => [...]}中的pressed参数是什么类型？| What type is the pressed parameter in style={({ pressed }) => [...]}?
    **答案 | Answer:** 布尔值(boolean) | Boolean - true表示当前处于按下状态，false表示未按下 | true means currently in pressed state, false means not pressed
  - android_ripple属性有什么作用？| What does the android_ripple prop do?
    **答案 | Answer:** 为Android平台添加Material Design的波纹效果，iOS上无效果 | Adds Material Design ripple effect on Android platform, no effect on iOS
  - children可以是函数吗？| Can children be a function?
    **答案 | Answer:** 是 | Yes - children函数会接收pressed状态，可以根据状态返回不同的内容 | children function receives pressed state and can return different content based on state

  **常见误区检查 | Common Misconception Checks:**
  - Pressable的按下效果会自动添加吗？| Does Pressable automatically add press effects?
    **答案 | Answer:** 不会 | No - 开发者需要通过style函数或children函数手动实现按下效果，Pressable只提供状态 | Developers need to manually implement press effects through style or children functions, Pressable only provides the state
  - 我应该总是使用Pressable而不是Button吗？| Should I always use Pressable instead of Button?
    **答案 | Answer:** 几乎是 | Almost yes - Pressable提供更好的控制和一致性，Button仅在快速原型开发或不需要自定义样式时使用 | Pressable provides better control and consistency, Button is only used for rapid prototyping or when custom styling is not needed

### 5. 遗留触摸组件 | Legacy Touch Components (30分钟 | 30 minutes)

- **TouchableOpacity和TouchableHighlight | TouchableOpacity and TouchableHighlight**

  **概念定义 | Concept Definition:**
  TouchableOpacity和TouchableHighlight是React Native早期版本的触摸响应组件。虽然现在推荐使用Pressable，但在现有代码库中仍然常见，了解它们对于维护旧项目很重要。| TouchableOpacity and TouchableHighlight are touch-responsive components from early React Native versions. While Pressable is now recommended, they're still common in existing codebases, making them important to understand for maintaining legacy projects.

  **核心特征 | Key Characteristics:**
  - TouchableOpacity按下时会降低透明度（opacity） | TouchableOpacity reduces opacity when pressed
  - TouchableHighlight按下时会改变底层颜色 | TouchableHighlight changes underlay color when pressed
  - 两者都不如Pressable灵活和强大 | Both are less flexible and powerful than Pressable
  - 主要用于维护旧代码，新项目应使用Pressable | Mainly used for maintaining old code, new projects should use Pressable
  - TouchableHighlight要求只有一个子元素 | TouchableHighlight requires exactly one child element

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 新的React Native项目应该使用TouchableOpacity吗？| Should new React Native projects use TouchableOpacity?
     **答案 | Answer:** 不应该 | No - 应该使用Pressable，它是更现代、更强大的替代品 | Should use Pressable, which is a more modern and powerful alternative
  2. TouchableOpacity的默认按下效果是什么？| What's the default press effect of TouchableOpacity?
     **答案 | Answer:** 降低透明度到0.2 | Reduces opacity to 0.2 - 可以通过activeOpacity属性自定义 | Can be customized via activeOpacity prop
  3. TouchableHighlight和Pressable的主要区别是什么？| What's the main difference between TouchableHighlight and Pressable?
     **答案 | Answer:** TouchableHighlight只能改变底层颜色，Pressable可以完全自定义按下状态的样式 | TouchableHighlight can only change underlay color, Pressable allows full customization of pressed state styles
  4. 为什么这些组件被称为"遗留"组件？| Why are these components called "legacy" components?
     **答案 | Answer:** 因为Pressable在React Native 0.63引入后成为官方推荐，这些组件不再是最佳实践 | Because Pressable became the official recommendation after introduction in React Native 0.63, these components are no longer best practice

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import {
    View,
    Text,
    TouchableOpacity,
    TouchableHighlight,
    Pressable,
    StyleSheet
  } from 'react-native';

  // 对比三种触摸组件 | Comparing three touch components
  const TouchableComparison = () => {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>触摸组件对比 | Touch Components Comparison</Text>

        {/* TouchableOpacity示例 | TouchableOpacity example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>TouchableOpacity (遗留 | Legacy)</Text>
          <TouchableOpacity
            style={styles.legacyButton}
            onPress={() => console.log('TouchableOpacity pressed')}
            activeOpacity={0.7} // 自定义按下时的透明度 | Custom pressed opacity
          >
            <Text style={styles.legacyButtonText}>
              按下时透明度降低 | Opacity Decreases on Press
            </Text>
          </TouchableOpacity>
        </View>

        {/* TouchableHighlight示例 | TouchableHighlight example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>TouchableHighlight (遗留 | Legacy)</Text>
          <TouchableHighlight
            style={styles.legacyButton}
            onPress={() => console.log('TouchableHighlight pressed')}
            underlayColor="#2980b9" // 按下时的底层颜色 | Underlay color when pressed
          >
            <Text style={styles.legacyButtonText}>
              按下时显示底层颜色 | Shows Underlay Color on Press
            </Text>
          </TouchableHighlight>
        </View>

        {/* Pressable示例(推荐) | Pressable example (recommended) */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>Pressable (推荐 | Recommended)</Text>
          <Pressable
            style={({ pressed }) => [
              styles.modernButton,
              pressed && styles.modernButtonPressed,
            ]}
            onPress={() => console.log('Pressable pressed')}
          >
            {({ pressed }) => (
              <Text style={styles.modernButtonText}>
                {pressed ? '按下中 | Pressing' : '完全自定义效果 | Fully Custom Effect'}
              </Text>
            )}
          </Pressable>
        </View>

        {/* 对比说明 | Comparison notes */}
        <View style={styles.comparisonBox}>
          <Text style={styles.comparisonTitle}>✅ 为什么选择Pressable? | Why Choose Pressable?</Text>
          <Text style={styles.comparisonItem}>• 更灵活的样式控制 | More flexible style control</Text>
          <Text style={styles.comparisonItem}>• 支持函数式style和children | Supports functional style and children</Text>
          <Text style={styles.comparisonItem}>• 更细粒度的触摸事件 | More granular touch events</Text>
          <Text style={styles.comparisonItem}>• 跨平台一致性更好 | Better cross-platform consistency</Text>
          <Text style={styles.comparisonItem}>• 官方推荐的现代方案 | Official recommended modern solution</Text>
        </View>

        <View style={styles.migrationBox}>
          <Text style={styles.migrationTitle}>
            🔄 迁移建议 | Migration Recommendations
          </Text>
          <Text style={styles.migrationText}>
            遇到旧代码时：了解其功能{'\n'}
            When encountering old code: Understand its functionality{'\n\n'}
            重构项目时：迁移到Pressable{'\n'}
            When refactoring projects: Migrate to Pressable{'\n\n'}
            新项目：始终使用Pressable{'\n'}
            New projects: Always use Pressable
          </Text>
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
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    section: {
      marginBottom: 20,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 10,
      color: '#666',
    },
    legacyButton: {
      backgroundColor: '#3498db',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
    },
    legacyButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    modernButton: {
      backgroundColor: '#27ae60',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
    },
    modernButtonPressed: {
      backgroundColor: '#229954',
      transform: [{ scale: 0.98 }],
    },
    modernButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    comparisonBox: {
      backgroundColor: '#e8f5e9',
      padding: 15,
      borderRadius: 8,
      marginTop: 10,
      marginBottom: 15,
    },
    comparisonTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#2e7d32',
    },
    comparisonItem: {
      fontSize: 14,
      marginBottom: 5,
      color: '#333',
    },
    migrationBox: {
      backgroundColor: '#fff3e0',
      padding: 15,
      borderRadius: 8,
      borderLeftWidth: 4,
      borderLeftColor: '#ff9800',
    },
    migrationTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#e65100',
    },
    migrationText: {
      fontSize: 14,
      lineHeight: 22,
      color: '#333',
    },
  });

  export default TouchableComparison;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果在维护旧项目时遇到TouchableOpacity，应该立即重写为Pressable吗？| If you encounter TouchableOpacity in legacy project maintenance, should you immediately rewrite it as Pressable?
    **答案 | Answer:** 不一定 | Not necessarily - 如果功能正常且不需要修改，可以保持不变。重构时或添加新功能时再迁移到Pressable | If working properly and no modifications needed, can keep as is. Migrate to Pressable during refactoring or when adding new features
  - TouchableHighlight的underlayColor在哪里显示？| Where does TouchableHighlight's underlayColor show?
    **答案 | Answer:** 在按下时显示在内容下方作为背景色 | Shows beneath content as background color when pressed

  **常见误区检查 | Common Misconception Checks:**
  - TouchableOpacity已经被废弃了吗？| Has TouchableOpacity been deprecated?
    **答案 | Answer:** 还没有正式废弃 | Not officially deprecated yet - 但React Native团队推荐新项目使用Pressable，TouchableOpacity可能在未来版本废弃 | But React Native team recommends Pressable for new projects, TouchableOpacity may be deprecated in future versions

### 6. 用户交互最佳实践 | User Interaction Best Practices (30分钟 | 30 minutes)

- **触摸反馈和可访问性 | Touch Feedback and Accessibility**

  **概念定义 | Concept Definition:**
  良好的移动用户体验需要即时的视觉反馈和可访问性支持。这包括触摸反馈、适当的触摸目标尺寸、以及辅助功能支持（如屏幕阅读器）。| Good mobile user experience requires immediate visual feedback and accessibility support. This includes touch feedback, appropriate touch target sizes, and assistive technology support (like screen readers).

  **核心特征 | Key Characteristics:**
  - 触摸目标应至少44x44点（iOS）或48x48dp（Android） | Touch targets should be at least 44x44 points (iOS) or 48x48dp (Android)
  - 所有交互元素都应该有明显的按下状态 | All interactive elements should have clear pressed states
  - 使用accessibilityLabel提供屏幕阅读器支持 | Use accessibilityLabel to provide screen reader support
  - 避免过度的动画影响性能和用户体验 | Avoid excessive animations that impact performance and UX
  - 为不同平台提供平台特定的交互模式 | Provide platform-specific interaction patterns for different platforms

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 移动端触摸目标的最小推荐尺寸是多少？| What's the minimum recommended size for mobile touch targets?
     **答案 | Answer:** 44x44点(iOS)或48x48dp(Android) | 44x44 points (iOS) or 48x48dp (Android) - 更小的目标难以准确点击 | Smaller targets are difficult to tap accurately
  2. accessibilityLabel属性有什么作用？| What does the accessibilityLabel prop do?
     **答案 | Answer:** 为屏幕阅读器提供元素的描述性文本，帮助视障用户理解元素功能 | Provides descriptive text for screen readers to help visually impaired users understand element functionality
  3. 为什么所有可点击元素都应该有视觉反馈？| Why should all tappable elements have visual feedback?
     **答案 | Answer:** 让用户确认他们的触摸被识别，提供即时反馈改善用户体验和信心 | Lets users confirm their touch was recognized, immediate feedback improves UX and confidence
  4. 平台特定的交互模式是什么意思？| What does platform-specific interaction patterns mean?
     **答案 | Answer:** iOS和Android有不同的设计规范，如Android使用波纹效果，iOS使用淡入淡出 | iOS and Android have different design guidelines, like Android uses ripple effects, iOS uses fade effects

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React, { useState } from 'react';
  import {
    View,
    Text,
    Pressable,
    Platform,
    StyleSheet
  } from 'react-native';

  // 可访问的按钮组件 | Accessible button component
  const AccessibleButton = ({ title, onPress, icon, disabled = false }) => {
    return (
      <Pressable
        style={({ pressed }) => [
          styles.accessibleButton,
          pressed && !disabled && styles.accessibleButtonPressed,
          disabled && styles.accessibleButtonDisabled,
        ]}
        onPress={disabled ? undefined : onPress}
        disabled={disabled}
        // 可访问性属性 | Accessibility props
        accessible={true} // 标记为可访问元素 | Mark as accessible element
        accessibilityLabel={title} // 屏幕阅读器会读出这个文本 | Screen reader will read this text
        accessibilityRole="button" // 告诉辅助技术这是一个按钮 | Tell assistive tech this is a button
        accessibilityState={{ disabled }} // 传递禁用状态 | Pass disabled state
        accessibilityHint="双击激活 | Double tap to activate" // 提供使用提示 | Provide usage hint
        // Android波纹效果 | Android ripple effect
        android_ripple={{ color: 'rgba(255, 255, 255, 0.3)' }}
      >
        <View style={styles.buttonContent}>
          {icon && <Text style={styles.buttonIcon}>{icon}</Text>}
          <Text style={[
            styles.buttonText,
            disabled && styles.buttonTextDisabled,
          ]}>
            {title}
          </Text>
        </View>
      </Pressable>
    );
  };

  // 最小触摸目标演示 | Minimum touch target demonstration
  const TouchTargetDemo = () => {
    const [selectedSize, setSelectedSize] = useState(null);

    return (
      <View style={styles.demoContainer}>
        <Text style={styles.demoTitle}>
          触摸目标尺寸对比 | Touch Target Size Comparison
        </Text>

        {/* 过小的触摸目标(不推荐) | Too small touch target (not recommended) */}
        <View style={styles.targetRow}>
          <Text style={styles.targetLabel}>30x30 ❌</Text>
          <Pressable
            style={({ pressed }) => [
              styles.touchTarget,
              { width: 30, height: 30 },
              pressed && styles.touchTargetPressed,
            ]}
            onPress={() => setSelectedSize('30x30')}
          >
            <Text style={styles.targetText}>小</Text>
          </Pressable>
          <Text style={styles.targetNote}>太小，难以点击 | Too small, hard to tap</Text>
        </View>

        {/* 推荐的触摸目标 | Recommended touch target */}
        <View style={styles.targetRow}>
          <Text style={styles.targetLabel}>44x44 ✅</Text>
          <Pressable
            style={({ pressed }) => [
              styles.touchTarget,
              { width: 44, height: 44 },
              pressed && styles.touchTargetPressed,
            ]}
            onPress={() => setSelectedSize('44x44')}
            accessibilityLabel="推荐尺寸按钮 | Recommended size button"
          >
            <Text style={styles.targetText}>中</Text>
          </Pressable>
          <Text style={styles.targetNote}>
            推荐尺寸 | Recommended size
          </Text>
        </View>

        {/* 大型触摸目标 | Large touch target */}
        <View style={styles.targetRow}>
          <Text style={styles.targetLabel}>60x60 ✅</Text>
          <Pressable
            style={({ pressed }) => [
              styles.touchTarget,
              { width: 60, height: 60 },
              pressed && styles.touchTargetPressed,
            ]}
            onPress={() => setSelectedSize('60x60')}
          >
            <Text style={styles.targetText}>大</Text>
          </Pressable>
          <Text style={styles.targetNote}>更容易点击 | Easier to tap</Text>
        </View>

        {selectedSize && (
          <Text style={styles.selectedText}>
            已选择 | Selected: {selectedSize}
          </Text>
        )}
      </View>
    );
  };

  // 平台特定交互示例 | Platform-specific interaction example
  const PlatformSpecificButton = ({ title, onPress }) => {
    // iOS风格：淡入淡出效果 | iOS style: fade effect
    const iOSStyle = ({ pressed }) => [
      styles.platformButton,
      { opacity: pressed ? 0.6 : 1 },
    ];

    // Android风格：使用波纹效果 | Android style: use ripple effect
    const androidProps = {
      android_ripple: {
        color: 'rgba(0, 0, 0, 0.2)',
        borderless: false,
      },
    };

    return (
      <Pressable
        style={Platform.OS === 'ios' ? iOSStyle : styles.platformButton}
        onPress={onPress}
        {...(Platform.OS === 'android' && androidProps)}
      >
        <Text style={styles.platformButtonText}>{title}</Text>
        <Text style={styles.platformNote}>
          {Platform.OS === 'ios' ? '(iOS淡入淡出 | iOS fade)' : '(Android波纹 | Android ripple)'}
        </Text>
      </Pressable>
    );
  };

  // 综合最佳实践示例 | Comprehensive best practices example
  const BestPracticesShowcase = () => {
    const [likeCount, setLikeCount] = useState(0);
    const [isLiked, setIsLiked] = useState(false);

    const handleLike = () => {
      setIsLiked(!isLiked);
      setLikeCount(isLiked ? likeCount - 1 : likeCount + 1);
    };

    return (
      <View style={styles.showcaseContainer}>
        <Text style={styles.showcaseTitle}>
          交互最佳实践 | Interaction Best Practices
        </Text>

        {/* 可访问的按钮 | Accessible buttons */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>可访问性支持 | Accessibility Support</Text>
          <AccessibleButton
            title="主要操作 | Primary Action"
            icon="✓"
            onPress={() => console.log('Primary action')}
          />
          <AccessibleButton
            title="次要操作 | Secondary Action"
            icon="→"
            onPress={() => console.log('Secondary action')}
          />
          <AccessibleButton
            title="禁用按钮 | Disabled Button"
            icon="⊘"
            disabled={true}
            onPress={() => console.log('This will not fire')}
          />
        </View>

        {/* 触摸目标演示 | Touch target demo */}
        <TouchTargetDemo />

        {/* 平台特定交互 | Platform-specific interaction */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>平台特定交互 | Platform-Specific Interaction</Text>
          <PlatformSpecificButton
            title="点击查看平台效果 | Tap to See Platform Effect"
            onPress={() => console.log(`${Platform.OS} interaction`)}
          />
        </View>

        {/* 视觉反馈示例 | Visual feedback example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>即时视觉反馈 | Immediate Visual Feedback</Text>
          <Pressable
            style={({ pressed }) => [
              styles.likeButton,
              isLiked && styles.likeButtonActive,
              pressed && styles.likeButtonPressed,
            ]}
            onPress={handleLike}
            accessibilityLabel={isLiked ? '取消喜欢 | Unlike' : '喜欢 | Like'}
            accessibilityState={{ selected: isLiked }}
          >
            <Text style={styles.likeIcon}>{isLiked ? '❤️' : '🤍'}</Text>
            <Text style={[
              styles.likeText,
              isLiked && styles.likeTextActive,
            ]}>
              {likeCount} {isLiked ? '已喜欢 | Liked' : '喜欢 | Like'}
            </Text>
          </Pressable>
        </View>

        {/* 最佳实践清单 | Best practices checklist */}
        <View style={styles.checklistBox}>
          <Text style={styles.checklistTitle}>✓ 交互设计检查清单 | Interaction Design Checklist</Text>
          <Text style={styles.checklistItem}>✓ 触摸目标≥44x44pt | Touch target ≥44x44pt</Text>
          <Text style={styles.checklistItem}>✓ 明显的按下状态 | Clear pressed state</Text>
          <Text style={styles.checklistItem}>✓ accessibilityLabel支持 | accessibilityLabel support</Text>
          <Text style={styles.checklistItem}>✓ 合理的禁用状态 | Proper disabled state</Text>
          <Text style={styles.checklistItem}>✓ 平台特定的交互模式 | Platform-specific patterns</Text>
          <Text style={styles.checklistItem}>✓ 避免过度动画 | Avoid excessive animation</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    accessibleButton: {
      backgroundColor: '#4a90e2',
      paddingVertical: 12,
      paddingHorizontal: 20,
      borderRadius: 8,
      marginVertical: 8,
      minHeight: 44, // 确保最小触摸目标 | Ensure minimum touch target
      justifyContent: 'center',
      alignItems: 'center',
    },
    accessibleButtonPressed: {
      backgroundColor: '#357abd',
      transform: [{ scale: 0.98 }],
    },
    accessibleButtonDisabled: {
      backgroundColor: '#bdc3c7',
    },
    buttonContent: {
      flexDirection: 'row',
      alignItems: 'center',
    },
    buttonIcon: {
      fontSize: 18,
      marginRight: 8,
      color: 'white',
    },
    buttonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    buttonTextDisabled: {
      color: '#ecf0f1',
    },
    demoContainer: {
      padding: 15,
      backgroundColor: '#f8f8f8',
      borderRadius: 8,
      marginVertical: 15,
    },
    demoTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 15,
      textAlign: 'center',
    },
    targetRow: {
      flexDirection: 'row',
      alignItems: 'center',
      marginVertical: 10,
    },
    targetLabel: {
      width: 80,
      fontSize: 14,
      fontWeight: '600',
    },
    touchTarget: {
      backgroundColor: '#3498db',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 4,
      marginHorizontal: 15,
    },
    touchTargetPressed: {
      backgroundColor: '#2980b9',
    },
    targetText: {
      color: 'white',
      fontWeight: 'bold',
    },
    targetNote: {
      flex: 1,
      fontSize: 12,
      color: '#666',
    },
    selectedText: {
      marginTop: 15,
      fontSize: 14,
      textAlign: 'center',
      color: '#27ae60',
      fontWeight: 'bold',
    },
    platformButton: {
      backgroundColor: '#9b59b6',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
    },
    platformButtonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
    platformNote: {
      color: 'white',
      fontSize: 12,
      marginTop: 5,
      opacity: 0.8,
    },
    showcaseContainer: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    showcaseTitle: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    section: {
      marginBottom: 25,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 12,
      color: '#333',
    },
    likeButton: {
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'center',
      backgroundColor: '#f0f0f0',
      padding: 15,
      borderRadius: 25,
      borderWidth: 2,
      borderColor: '#ddd',
      minHeight: 50,
    },
    likeButtonActive: {
      backgroundColor: '#ffe6e6',
      borderColor: '#e74c3c',
    },
    likeButtonPressed: {
      transform: [{ scale: 0.95 }],
    },
    likeIcon: {
      fontSize: 24,
      marginRight: 10,
    },
    likeText: {
      fontSize: 16,
      fontWeight: '600',
      color: '#666',
    },
    likeTextActive: {
      color: '#e74c3c',
    },
    checklistBox: {
      backgroundColor: '#e8f5e9',
      padding: 15,
      borderRadius: 8,
      marginTop: 10,
    },
    checklistTitle: {
      fontSize: 16,
      fontWeight: 'bold',
      marginBottom: 10,
      color: '#2e7d32',
    },
    checklistItem: {
      fontSize: 14,
      marginBottom: 6,
      color: '#333',
      paddingLeft: 10,
    },
  });

  export default BestPracticesShowcase;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - accessibilityRole有哪些常见值？| What are common values for accessibilityRole?
    **答案 | Answer:** button, link, text, image, header, search等 | button, link, text, image, header, search, etc. - 帮助辅助技术理解元素类型 | Helps assistive tech understand element type
  - 为什么Android使用波纹效果而iOS使用淡入淡出？| Why does Android use ripple effect while iOS uses fade?
    **答案 | Answer:** 这是各平台的设计规范，遵循平台规范能提供用户熟悉的体验 | These are platform design guidelines, following them provides familiar UX for users

  **常见误区检查 | Common Misconception Checks:**
  - 添加accessibilityLabel会改变按钮的视觉外观吗？| Does adding accessibilityLabel change the button's visual appearance?
    **答案 | Answer:** 不会 | No - accessibility属性只影响辅助技术（如屏幕阅读器），不改变视觉外观 | Accessibility props only affect assistive tech (like screen readers), don't change visual appearance
  - 我可以为了美观使用小于44x44的触摸目标吗？| Can I use touch targets smaller than 44x44 for aesthetics?
    **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - 会严重影响可用性，特别是对手指较大或运动障碍的用户 | Severely impacts usability, especially for users with larger fingers or motor disabilities

## 实践项目：社交媒体Feed | Practical Project: Social Media Feed

### 目标 | Objective
构建一个社交媒体Feed应用，综合应用ScrollView、Pressable和用户交互的所有概念。项目包括垂直滚动的帖子列表、水平滚动的故事栏、以及各种交互元素（点赞、评论、分享按钮）。| Build a social media feed application that comprehensively applies all concepts of ScrollView, Pressable, and user interaction. The project includes a vertical scrolling post list, horizontal scrolling story bar, and various interactive elements (like, comment, share buttons).

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. ScrollView会立即渲染所有子元素，这对性能有什么影响？| ScrollView renders all children immediately, what's the performance impact?
   **答案 | Answer:** 如果内容过多会导致初始加载缓慢和内存占用增加，这就是为什么此项目限制帖子数量 | If there's too much content it causes slow initial loading and increased memory usage, which is why this project limits post count
2. Pressable的style属性接收函数时，函数参数是什么？| When Pressable's style prop receives a function, what's the function parameter?
   **答案 | Answer:** 一个包含pressed等状态的对象，用于根据交互状态返回不同样式 | An object containing states like pressed, used to return different styles based on interaction state
3. 为什么需要为交互元素添加accessibilityLabel？| Why do we need to add accessibilityLabel to interactive elements?
   **答案 | Answer:** 为屏幕阅读器提供描述，使视障用户也能使用应用 | Provides descriptions for screen readers, making the app usable for visually impaired users

### 步骤 | Steps
1. 设置项目结构：创建组件文件夹和样式定义 | Set up project structure: Create component folders and style definitions
2. 实现水平滚动的Stories组件：使用horizontal ScrollView | Implement horizontal scrolling Stories component: Use horizontal ScrollView
3. 创建Post组件：包含图片、文本和交互按钮 | Create Post component: Include image, text, and interactive buttons
4. 实现垂直滚动的Feed：使用ScrollView包裹多个Post | Implement vertical scrolling Feed: Use ScrollView to wrap multiple Posts
5. 添加交互功能：点赞、评论计数和视觉反馈 | Add interactive features: Like, comment count, and visual feedback
6. 优化可访问性：添加accessibility属性 | Optimize accessibility: Add accessibility props
7. 测试滚动性能和交互响应 | Test scroll performance and interaction responsiveness

### 示例代码 | Example Code
```jsx
/**
 * 社交媒体Feed应用 | Social Media Feed App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - ScrollView（垂直和水平） | ScrollView (vertical and horizontal)
 * - Pressable组件和交互状态 | Pressable component and interaction states
 * - 用户交互最佳实践 | User interaction best practices
 * - 可访问性支持 | Accessibility support
 */

import React, { useState } from 'react';
import {
  View,
  Text,
  Image,
  ScrollView,
  Pressable,
  StyleSheet,
  Dimensions,
  SafeAreaView,
} from 'react-native';

const { width } = Dimensions.get('window');

// Story组件（水平滚动故事栏中的单个故事） | Story component (single story in horizontal scroll)
const Story = ({ username, avatar, isViewed }) => {
  return (
    <Pressable
      style={({ pressed }) => [
        styles.story,
        pressed && styles.storyPressed,
      ]}
      onPress={() => console.log(`查看${username}的故事 | View ${username}'s story`)}
      accessibilityLabel={`${username}的故事 | ${username}'s story`}
      accessibilityRole="button"
    >
      <View style={[
        styles.storyAvatarContainer,
        isViewed && styles.storyAvatarViewed,
      ]}>
        <Image
          source={{ uri: avatar }}
          style={styles.storyAvatar}
        />
      </View>
      <Text style={styles.storyUsername} numberOfLines={1}>
        {username}
      </Text>
    </Pressable>
  );
};

// Stories横向滚动栏 | Horizontal scrolling stories bar
const StoriesBar = ({ stories }) => {
  return (
    <View style={styles.storiesContainer}>
      <ScrollView
        horizontal
        showsHorizontalScrollIndicator={false}
        contentContainerStyle={styles.storiesContent}
      >
        {stories.map((story) => (
          <Story
            key={story.id}
            username={story.username}
            avatar={story.avatar}
            isViewed={story.isViewed}
          />
        ))}
      </ScrollView>
    </View>
  );
};

// 交互按钮组件 | Interaction button component
const ActionButton = ({ icon, count, isActive, onPress, label }) => {
  return (
    <Pressable
      style={({ pressed }) => [
        styles.actionButton,
        pressed && styles.actionButtonPressed,
      ]}
      onPress={onPress}
      accessibilityLabel={label}
      accessibilityRole="button"
      accessibilityState={{ selected: isActive }}
    >
      <Text style={[
        styles.actionIcon,
        isActive && styles.actionIconActive,
      ]}>
        {icon}
      </Text>
      {count > 0 && (
        <Text style={[
          styles.actionCount,
          isActive && styles.actionCountActive,
        ]}>
          {count}
        </Text>
      )}
    </Pressable>
  );
};

// Post组件（单个帖子） | Post component (single post)
const Post = ({ post }) => {
  const [isLiked, setIsLiked] = useState(post.isLiked);
  const [likeCount, setLikeCount] = useState(post.likes);
  const [isSaved, setIsSaved] = useState(false);

  // 处理点赞 | Handle like
  const handleLike = () => {
    setIsLiked(!isLiked);
    setLikeCount(isLiked ? likeCount - 1 : likeCount + 1);
  };

  // 处理保存 | Handle save
  const handleSave = () => {
    setIsSaved(!isSaved);
  };

  return (
    <View style={styles.post}>
      {/* 帖子头部 | Post header */}
      <View style={styles.postHeader}>
        <Image
          source={{ uri: post.userAvatar }}
          style={styles.postAvatar}
        />
        <View style={styles.postHeaderText}>
          <Text style={styles.postUsername}>{post.username}</Text>
          <Text style={styles.postLocation}>{post.location}</Text>
        </View>
        <Pressable
          style={({ pressed }) => [
            styles.moreButton,
            pressed && styles.moreButtonPressed,
          ]}
          onPress={() => console.log('显示更多选项 | Show more options')}
          accessibilityLabel="更多选项 | More options"
        >
          <Text style={styles.moreIcon}>⋯</Text>
        </Pressable>
      </View>

      {/* 帖子图片 | Post image */}
      <Image
        source={{ uri: post.image }}
        style={styles.postImage}
        resizeMode="cover"
      />

      {/* 交互按钮栏 | Interaction buttons bar */}
      <View style={styles.actionsBar}>
        <View style={styles.leftActions}>
          <ActionButton
            icon={isLiked ? '❤️' : '🤍'}
            count={likeCount}
            isActive={isLiked}
            onPress={handleLike}
            label={isLiked ? '取消喜欢 | Unlike' : '喜欢 | Like'}
          />
          <ActionButton
            icon="💬"
            count={post.comments}
            isActive={false}
            onPress={() => console.log('打开评论 | Open comments')}
            label="评论 | Comments"
          />
          <ActionButton
            icon="↗️"
            count={0}
            isActive={false}
            onPress={() => console.log('分享 | Share')}
            label="分享 | Share"
          />
        </View>
        <ActionButton
          icon={isSaved ? '🔖' : '⊙'}
          count={0}
          isActive={isSaved}
          onPress={handleSave}
          label={isSaved ? '取消保存 | Unsave' : '保存 | Save'}
        />
      </View>

      {/* 帖子内容 | Post content */}
      <View style={styles.postContent}>
        {likeCount > 0 && (
          <Text style={styles.likesText}>
            {likeCount} 个赞 | {likeCount} likes
          </Text>
        )}
        <Text style={styles.caption}>
          <Text style={styles.captionUsername}>{post.username}</Text>
          {' '}{post.caption}
        </Text>
        {post.comments > 0 && (
          <Text style={styles.viewComments}>
            查看全部 {post.comments} 条评论 | View all {post.comments} comments
          </Text>
        )}
        <Text style={styles.postTime}>{post.timeAgo}</Text>
      </View>
    </View>
  );
};

// 主应用组件 | Main app component
const SocialMediaFeed = () => {
  // 模拟数据 | Mock data
  const stories = [
    { id: 1, username: 'Your Story', avatar: 'https://i.pravatar.cc/150?img=1', isViewed: false },
    { id: 2, username: 'alice_dev', avatar: 'https://i.pravatar.cc/150?img=2', isViewed: false },
    { id: 3, username: 'bob_design', avatar: 'https://i.pravatar.cc/150?img=3', isViewed: true },
    { id: 4, username: 'carol_code', avatar: 'https://i.pravatar.cc/150?img=4', isViewed: false },
    { id: 5, username: 'david_tech', avatar: 'https://i.pravatar.cc/150?img=5', isViewed: true },
    { id: 6, username: 'emma_js', avatar: 'https://i.pravatar.cc/150?img=6', isViewed: false },
  ];

  const posts = [
    {
      id: 1,
      username: 'alice_dev',
      userAvatar: 'https://i.pravatar.cc/150?img=2',
      location: 'San Francisco, CA',
      image: 'https://picsum.photos/400/500?random=1',
      caption: '美好的一天开始编码！🚀 | Beautiful day to start coding! 🚀',
      likes: 234,
      comments: 12,
      timeAgo: '2小时前 | 2 hours ago',
      isLiked: false,
    },
    {
      id: 2,
      username: 'bob_design',
      userAvatar: 'https://i.pravatar.cc/150?img=3',
      location: 'New York, NY',
      image: 'https://picsum.photos/400/500?random=2',
      caption: '新的设计灵感 ✨ | New design inspiration ✨',
      likes: 567,
      comments: 28,
      timeAgo: '4小时前 | 4 hours ago',
      isLiked: true,
    },
    {
      id: 3,
      username: 'carol_code',
      userAvatar: 'https://i.pravatar.cc/150?img=4',
      location: 'Seattle, WA',
      image: 'https://picsum.photos/400/500?random=3',
      caption: '周末黑客马拉松成果 💻 | Weekend hackathon results 💻',
      likes: 89,
      comments: 5,
      timeAgo: '6小时前 | 6 hours ago',
      isLiked: false,
    },
  ];

  // 追踪滚动位置（可选的附加功能） | Track scroll position (optional additional feature)
  const [scrollPosition, setScrollPosition] = useState(0);

  const handleScroll = (event) => {
    const yOffset = event.nativeEvent.contentOffset.y;
    setScrollPosition(yOffset);
  };

  return (
    <SafeAreaView style={styles.container}>
      {/* 顶部导航栏 | Top navigation bar */}
      <View style={styles.header}>
        <Text style={styles.headerTitle}>📱 SocialFeed</Text>
        <View style={styles.headerActions}>
          <Pressable
            style={({ pressed }) => [
              styles.headerButton,
              pressed && styles.headerButtonPressed,
            ]}
            onPress={() => console.log('打开消息 | Open messages')}
            accessibilityLabel="消息 | Messages"
          >
            <Text style={styles.headerIcon}>✉️</Text>
          </Pressable>
        </View>
      </View>

      {/* 主滚动视图 | Main scroll view */}
      <ScrollView
        style={styles.feed}
        showsVerticalScrollIndicator={false}
        onScroll={handleScroll}
        scrollEventThrottle={16}
      >
        {/* 故事栏 | Stories bar */}
        <StoriesBar stories={stories} />

        {/* 帖子列表 | Posts list */}
        {posts.map((post) => (
          <Post key={post.id} post={post} />
        ))}

        {/* 加载更多提示 | Load more hint */}
        <View style={styles.loadMoreContainer}>
          <Text style={styles.loadMoreText}>
            🎉 你已查看所有帖子 | You've seen all posts 🎉
          </Text>
        </View>
      </ScrollView>

      {/* 底部导航栏 | Bottom navigation bar */}
      <View style={styles.bottomNav}>
        {['🏠', '🔍', '➕', '❤️', '👤'].map((icon, index) => (
          <Pressable
            key={index}
            style={({ pressed }) => [
              styles.navButton,
              pressed && styles.navButtonPressed,
            ]}
            onPress={() => console.log(`导航 ${index} | Nav ${index}`)}
            accessibilityLabel={`导航选项 ${index + 1} | Navigation option ${index + 1}`}
          >
            <Text style={styles.navIcon}>{icon}</Text>
          </Pressable>
        ))}
      </View>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },

  // 头部样式 | Header styles
  header: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    paddingHorizontal: 15,
    paddingVertical: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#dbdbdb',
  },
  headerTitle: {
    fontSize: 24,
    fontWeight: 'bold',
  },
  headerActions: {
    flexDirection: 'row',
  },
  headerButton: {
    padding: 8,
    borderRadius: 20,
  },
  headerButtonPressed: {
    backgroundColor: '#f0f0f0',
  },
  headerIcon: {
    fontSize: 24,
  },

  // Feed样式 | Feed styles
  feed: {
    flex: 1,
  },

  // Stories样式 | Stories styles
  storiesContainer: {
    borderBottomWidth: 1,
    borderBottomColor: '#dbdbdb',
    backgroundColor: '#fafafa',
  },
  storiesContent: {
    paddingHorizontal: 10,
    paddingVertical: 15,
  },
  story: {
    alignItems: 'center',
    marginHorizontal: 8,
    width: 70,
  },
  storyPressed: {
    opacity: 0.7,
  },
  storyAvatarContainer: {
    width: 66,
    height: 66,
    borderRadius: 33,
    padding: 3,
    borderWidth: 2,
    borderColor: '#e1306c',
    marginBottom: 5,
  },
  storyAvatarViewed: {
    borderColor: '#dbdbdb',
  },
  storyAvatar: {
    width: 60,
    height: 60,
    borderRadius: 30,
    borderWidth: 2,
    borderColor: '#fff',
  },
  storyUsername: {
    fontSize: 12,
    color: '#262626',
  },

  // Post样式 | Post styles
  post: {
    marginBottom: 15,
  },
  postHeader: {
    flexDirection: 'row',
    alignItems: 'center',
    paddingHorizontal: 15,
    paddingVertical: 10,
  },
  postAvatar: {
    width: 32,
    height: 32,
    borderRadius: 16,
  },
  postHeaderText: {
    flex: 1,
    marginLeft: 10,
  },
  postUsername: {
    fontWeight: '600',
    fontSize: 14,
    color: '#262626',
  },
  postLocation: {
    fontSize: 12,
    color: '#8e8e8e',
  },
  moreButton: {
    padding: 8,
    borderRadius: 15,
  },
  moreButtonPressed: {
    backgroundColor: '#f0f0f0',
  },
  moreIcon: {
    fontSize: 20,
    fontWeight: 'bold',
  },
  postImage: {
    width: width,
    height: width * 1.25, // 4:5比例 | 4:5 ratio
    backgroundColor: '#f0f0f0',
  },

  // 交互按钮样式 | Action buttons styles
  actionsBar: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    paddingHorizontal: 15,
    paddingVertical: 8,
  },
  leftActions: {
    flexDirection: 'row',
  },
  actionButton: {
    flexDirection: 'row',
    alignItems: 'center',
    marginRight: 15,
    padding: 5,
    borderRadius: 20,
    minWidth: 44,
    minHeight: 44,
    justifyContent: 'center',
  },
  actionButtonPressed: {
    backgroundColor: '#f0f0f0',
  },
  actionIcon: {
    fontSize: 24,
  },
  actionIconActive: {
    transform: [{ scale: 1.1 }],
  },
  actionCount: {
    marginLeft: 5,
    fontSize: 14,
    fontWeight: '600',
    color: '#262626',
  },
  actionCountActive: {
    color: '#e1306c',
  },

  // 帖子内容样式 | Post content styles
  postContent: {
    paddingHorizontal: 15,
  },
  likesText: {
    fontWeight: '600',
    fontSize: 14,
    marginBottom: 5,
    color: '#262626',
  },
  caption: {
    fontSize: 14,
    lineHeight: 18,
    color: '#262626',
  },
  captionUsername: {
    fontWeight: '600',
  },
  viewComments: {
    color: '#8e8e8e',
    marginTop: 5,
    fontSize: 14,
  },
  postTime: {
    color: '#8e8e8e',
    fontSize: 12,
    marginTop: 5,
  },

  // 加载更多样式 | Load more styles
  loadMoreContainer: {
    padding: 30,
    alignItems: 'center',
  },
  loadMoreText: {
    fontSize: 14,
    color: '#8e8e8e',
  },

  // 底部导航样式 | Bottom navigation styles
  bottomNav: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    paddingVertical: 10,
    borderTopWidth: 1,
    borderTopColor: '#dbdbdb',
    backgroundColor: '#fff',
  },
  navButton: {
    padding: 10,
    borderRadius: 20,
    minWidth: 44,
    minHeight: 44,
    justifyContent: 'center',
    alignItems: 'center',
  },
  navButtonPressed: {
    backgroundColor: '#f0f0f0',
  },
  navIcon: {
    fontSize: 24,
  },
});

export default SocialMediaFeed;
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确使用了水平和垂直ScrollView？| Does the project correctly use both horizontal and vertical ScrollView?
2. Pressable组件是否实现了明显的按下状态反馈？| Do Pressable components implement clear pressed state feedback?
3. 所有交互元素是否都有足够大的触摸目标（≥44x44）？| Do all interactive elements have sufficiently large touch targets (≥44x44)?
4. 是否为关键交互元素添加了accessibilityLabel？| Are accessibilityLabels added to key interactive elements?
5. 点赞、评论等交互功能是否正常工作？| Do interactions like like and comment work properly?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **ScrollView性能理解练习 | ScrollView Performance Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建两个版本的列表：一个使用ScrollView渲染100个项目，另一个使用FlatList。对比性能差异。
   - **概念检查 | Concept Check:** ScrollView和FlatList在渲染策略上有什么根本区别？| What's the fundamental difference in rendering strategy between ScrollView and FlatList?
   - **学习目标 | Learning Objective:** 深入理解ScrollView的渲染机制和性能限制

2. **Pressable状态管理练习 | Pressable State Management Exercise**
   - **练习描述 | Exercise Description:** 创建一个自定义按钮组件，支持loading、disabled、success三种状态，每种状态有不同的视觉样式。
   - **概念检查 | Concept Check:** style函数除了pressed参数，还能访问哪些状态？| Besides pressed parameter, what other states can the style function access?
   - **学习目标 | Learning Objective:** 掌握复杂交互状态的样式管理

3. **ScrollView事件练习 | ScrollView Events Exercise**
   - **练习描述 | Exercise Description:** 实现一个"回到顶部"按钮，只在用户滚动超过200px后显示，并带有淡入动画。
   - **概念检查 | Concept Check:** onScroll事件中的nativeEvent包含哪些有用的滚动信息？| What useful scroll information does nativeEvent contain in onScroll event?
   - **学习目标 | Learning Objective:** 掌握ScrollView事件和滚动位置追踪

4. **可访问性深化练习 | Accessibility Deepening Exercise**
   - **练习描述 | Exercise Description:** 为社交媒体Feed项目的所有交互元素添加完整的可访问性支持（label、role、state、hint）。
   - **概念检查 | Concept Check:** accessibilityState可以传递哪些状态信息？| What state information can accessibilityState pass?
   - **学习目标 | Learning Objective:** 全面理解React Native的可访问性API

5. **平台特定交互练习 | Platform-Specific Interaction Exercise**
   - **练习描述 | Exercise Description:** 创建一个按钮组件，在iOS上使用淡入淡出效果，在Android上使用Material波纹效果，并添加触觉反馈。
   - **概念检查 | Concept Check:** 如何使用Platform API检测当前平台？| How to use Platform API to detect current platform?
   - **学习目标 | Learning Objective:** 掌握平台特定的交互设计实现

6. **嵌套滚动练习 | Nested Scrolling Exercise**
   - **练习描述 | Exercise Description:** 在垂直ScrollView中嵌套水平ScrollView（如Stories栏），确保滚动手势不冲突。
   - **概念检查 | Concept Check:** 为什么嵌套的ScrollView可能导致手势冲突？| Why can nested ScrollViews cause gesture conflicts?
   - **学习目标 | Learning Objective:** 理解手势处理和滚动方向优先级

7. **项目扩展练习 | Project Extension Exercise**
   - **练习描述 | Exercise Description:** 为社交媒体Feed添加下拉刷新功能和上拉加载更多功能。
   - **概念检查 | Concept Check:** RefreshControl组件如何与ScrollView配合使用？| How does RefreshControl component work with ScrollView?
   - **学习目标 | Learning Objective:** 掌握高级滚动交互模式

## 学习资源 | Learning Resources
- [ScrollView官方文档 | ScrollView Official Documentation](https://reactnative.dev/docs/scrollview)
- [Pressable官方文档 | Pressable Official Documentation](https://reactnative.dev/docs/pressable)
- [Button官方文档 | Button Official Documentation](https://reactnative.dev/docs/button)
- [可访问性指南 | Accessibility Guide](https://reactnative.dev/docs/accessibility)
- [触摸处理 | Handling Touches](https://reactnative.dev/docs/handling-touches)
- [iOS人机界面指南 | iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Material Design交互规范 | Material Design Interaction Guidelines](https://material.io/design/interaction)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解ScrollView的立即渲染特性和适用场景 | Understand ScrollView's immediate rendering and use cases
- [ ] 能熟练实现垂直和水平滚动 | Can proficiently implement vertical and horizontal scrolling
- [ ] 掌握ScrollView的事件和方法（onScroll、scrollTo等） | Master ScrollView events and methods (onScroll, scrollTo, etc.)
- [ ] 理解Button组件的限制和使用场景 | Understand Button component limitations and use cases
- [ ] 能使用Pressable创建完全自定义的交互组件 | Can use Pressable to create fully custom interactive components
- [ ] 掌握style函数和children函数的用法 | Master usage of style function and children function
- [ ] 了解TouchableOpacity等遗留组件及其替代方案 | Know legacy components like TouchableOpacity and their alternatives
- [ ] 理解移动端交互设计最佳实践（触摸目标、反馈、可访问性） | Understand mobile interaction design best practices (touch targets, feedback, accessibility)
- [ ] 能为交互元素添加完整的可访问性支持 | Can add comprehensive accessibility support to interactive elements
- [ ] 成功完成社交媒体Feed实践项目 | Successfully completed social media feed practical project

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释ScrollView、Pressable和移动交互设计的核心概念。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain core concepts of ScrollView, Pressable, and mobile interaction design to others.
