# React Native入门 - 第4天：响应式设计与平台特定样式 | React Native Introduction - Day 4: Responsive Design and Platform-Specific Styles

## 学习目标 | Learning Objectives
- 掌握Platform模块检测iOS和Android平台的方法 | Master the Platform module for detecting iOS and Android platforms
- 理解并使用useWindowDimensions Hook创建响应式布局 | Understand and use the useWindowDimensions Hook to create responsive layouts
- 学会创建平台特定的组件和样式文件 | Learn to create platform-specific components and style files
- 实现适配不同屏幕尺寸和方向的移动布局 | Implement mobile layouts that adapt to different screen sizes and orientations
- 掌握跨平台一致性与平台特定优化的平衡 | Master the balance between cross-platform consistency and platform-specific optimization
- 构建能够在手机和平板上优雅切换布局的组件 | Build components that gracefully switch layouts between phones and tablets

## 详细内容 | Detailed Content

### 1. Platform模块基础 | Platform Module Basics (1小时 | 1 hour)

- **Platform模块核心概念 | Platform Module Core Concept**

  **概念定义 | Concept Definition:**
  Platform模块是React Native提供的用于检测应用运行平台的核心API。它允许开发者编写平台特定的代码,实现在iOS和Android上呈现不同的UI或行为。这是跨平台开发中实现平台优化的关键工具。| The Platform module is a core API provided by React Native for detecting the platform on which the app is running. It allows developers to write platform-specific code, implementing different UI or behavior on iOS and Android. This is a key tool for platform optimization in cross-platform development.

  **核心特征 | Key Characteristics:**
  - Platform.OS返回字符串'ios'或'android',用于条件判断 | Platform.OS returns a string 'ios' or 'android' for conditional logic
  - Platform.select()方法允许基于平台选择不同的值或组件 | Platform.select() method allows selecting different values or components based on platform
  - Platform.Version提供平台的具体版本号(iOS为字符串,Android为数字) | Platform.Version provides the specific platform version (string for iOS, number for Android)
  - 可以创建.ios.js和.android.js文件实现组件级别的平台分离 | Can create .ios.js and .android.js files for component-level platform separation

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Platform.OS能返回'web'平台吗? | Can Platform.OS return 'web' platform?
     **答案 | Answer:** 否 | No - 在标准React Native环境中只返回'ios'或'android',如需web支持需要React Native Web | In standard React Native environment it only returns 'ios' or 'android', web support requires React Native Web
  2. Platform.select()和简单的if/else判断有什么区别? | What's the difference between Platform.select() and simple if/else statements?
     **答案 | Answer:** Platform.select()返回基于平台的对象值,代码更简洁且可用于样式对象;if/else用于逻辑分支 | Platform.select() returns object values based on platform with cleaner syntax for style objects; if/else is for logic branching
  3. 如果同时存在Button.js和Button.ios.js文件,iOS平台会加载哪个? | If both Button.js and Button.ios.js exist, which one will iOS load?
     **答案 | Answer:** Button.ios.js | Button.ios.js - React Native会优先加载平台特定文件 | React Native prioritizes platform-specific files
  4. Platform.Version在iOS和Android上的数据类型相同吗? | Is Platform.Version the same data type on iOS and Android?
     **答案 | Answer:** 否 | No - iOS返回字符串(如'14.0'),Android返回数字(如30) | iOS returns string (like '14.0'), Android returns number (like 30)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { Platform, StyleSheet } from 'react-native';

  // 方法1: 使用Platform.OS进行条件判断 | Method 1: Conditional logic with Platform.OS
  const platformText = Platform.OS === 'ios' ? 'iOS设备' : 'Android设备';
  // iOS: platformText = 'iOS设备' | iOS: platformText = 'iOS Device'
  // Android: platformText = 'Android设备' | Android: platformText = 'Android Device'

  // 方法2: 使用Platform.select()选择值 | Method 2: Value selection with Platform.select()
  const padding = Platform.select({
    ios: 20,        // iOS使用20像素内边距 | iOS uses 20px padding
    android: 16,    // Android使用16像素内边距 | Android uses 16px padding
  });

  // 方法3: 在样式中使用Platform.select() | Method 3: Platform.select() in styles
  const styles = StyleSheet.create({
    container: {
      marginTop: Platform.select({
        ios: 0,      // iOS: 状态栏由SafeAreaView处理 | iOS: Status bar handled by SafeAreaView
        android: 25, // Android: 需要手动添加状态栏高度 | Android: Manual status bar height needed
      }),
    },
    // 整个样式对象也可以平台特定 | Entire style object can be platform-specific
    button: Platform.select({
      ios: {
        backgroundColor: '#007AFF', // iOS蓝色 | iOS blue
        borderRadius: 8,
      },
      android: {
        backgroundColor: '#2196F3', // Material Design蓝色 | Material Design blue
        borderRadius: 4,
        elevation: 2,               // Android特有阴影 | Android-specific shadow
      },
    }),
  });

  // 方法4: 检查平台版本 | Method 4: Check platform version
  if (Platform.OS === 'android' && Platform.Version >= 30) {
    console.log('Android 11或更高版本'); // Android 11 or higher
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在iOS上运行时,padding变量的值是多少? | What is the value of padding variable when running on iOS?
    **答案 | Answer:** 20 - Platform.select()返回ios属性的值 | Platform.select() returns the ios property value
  - 如果在Android 9(API 28)上运行版本检查代码会发生什么? | What happens if the version check code runs on Android 9 (API 28)?
    **答案 | Answer:** 不会执行console.log,因为28 < 30 | console.log won't execute because 28 < 30

  **常见误区检查 | Common Misconception Checks:**
  - Platform.select()需要同时提供ios和android两个键吗? | Does Platform.select() require both ios and android keys?
    **答案 | Answer:** 否 - 可以只提供一个平台的值,另一个会是undefined,可以设置default键作为后备值 | No - Can provide only one platform value, the other will be undefined, can set default key as fallback
  - Platform.OS的值会在应用运行时改变吗? | Can Platform.OS value change during app runtime?
    **答案 | Answer:** 否 - 它在应用启动时确定且不会改变 | No - It's determined at app launch and never changes

- **平台特定文件结构 | Platform-Specific File Structure**

  **概念定义 | Concept Definition:**
  React Native允许通过文件扩展名自动加载平台特定的实现。当你创建Button.ios.js和Button.android.js时,import Button会根据运行平台自动选择正确的文件。这种机制实现了组件级别的平台分离,无需在代码中写条件判断。| React Native allows automatic loading of platform-specific implementations through file extensions. When you create Button.ios.js and Button.android.js, importing Button will automatically select the correct file based on the running platform. This mechanism achieves component-level platform separation without conditional code.

  **核心特征 | Key Characteristics:**
  - 文件命名遵循ComponentName.{platform}.js模式 | File naming follows ComponentName.{platform}.js pattern
  - Metro打包器在构建时会自动选择对应平台的文件 | Metro bundler automatically selects platform-specific files during build
  - 可以同时有通用文件(Component.js)和平台文件作为覆盖 | Can have both common file (Component.js) and platform files as overrides
  - 适用于组件文件(.js/.tsx)也适用于资源文件和配置 | Works for component files (.js/.tsx) as well as assets and configs

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果只有Button.android.js存在,iOS会报错吗? | If only Button.android.js exists, will iOS throw an error?
     **答案 | Answer:** 是 | Yes - iOS找不到对应实现会报错,除非有Button.js作为后备 | iOS will error as it can't find implementation, unless Button.js exists as fallback
  2. 平台特定文件的import语句需要包含平台后缀吗? | Do import statements for platform-specific files need the platform suffix?
     **答案 | Answer:** 否 | No - import { Button } from './Button' 即可,Metro会自动解析 | import { Button } from './Button' is enough, Metro auto-resolves
  3. 可以为.png图片创建平台特定版本吗? | Can you create platform-specific versions for .png images?
     **答案 | Answer:** 是 | Yes - 如logo.ios.png和logo.android.png,遵循相同的命名规则 | Yes - like logo.ios.png and logo.android.png, following same naming convention

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 文件结构: | File structure:
  // components/
  //   ├── Button.js          (通用后备实现 | Common fallback)
  //   ├── Button.ios.js      (iOS特定实现 | iOS-specific)
  //   └── Button.android.js  (Android特定实现 | Android-specific)

  // Button.ios.js - iOS实现 | Button.ios.js - iOS implementation
  import React from 'react';
  import { TouchableOpacity, Text, StyleSheet } from 'react-native';

  export default function Button({ title, onPress }) {
    return (
      <TouchableOpacity style={styles.iosButton} onPress={onPress}>
        <Text style={styles.iosText}>{title}</Text>
      </TouchableOpacity>
    );
  }

  const styles = StyleSheet.create({
    iosButton: {
      backgroundColor: '#007AFF',  // iOS标准蓝色 | iOS standard blue
      paddingVertical: 12,
      paddingHorizontal: 24,
      borderRadius: 8,             // 圆角较大 | Larger border radius
    },
    iosText: {
      color: 'white',
      fontSize: 17,                // iOS文字稍大 | iOS text slightly larger
      fontWeight: '600',           // SF Pro字体权重 | SF Pro font weight
    },
  });

  // Button.android.js - Android实现 | Button.android.js - Android implementation
  import React from 'react';
  import { TouchableOpacity, Text, StyleSheet } from 'react-native';

  export default function Button({ title, onPress }) {
    return (
      <TouchableOpacity style={styles.androidButton} onPress={onPress}>
        <Text style={styles.androidText}>{title.toUpperCase()}</Text>
      </TouchableOpacity>
    );
  }

  const styles = StyleSheet.create({
    androidButton: {
      backgroundColor: '#2196F3',  // Material蓝色 | Material blue
      paddingVertical: 12,
      paddingHorizontal: 24,
      borderRadius: 4,             // 圆角较小 | Smaller border radius
      elevation: 3,                // Material阴影 | Material shadow
    },
    androidText: {
      color: 'white',
      fontSize: 14,                // Android文字稍小 | Android text slightly smaller
      fontWeight: '500',           // Roboto字体权重 | Roboto font weight
      letterSpacing: 1.25,         // Material大写字母间距 | Material uppercase letter spacing
    },
  });

  // 在任何组件中使用 | Usage in any component
  // App.js
  import Button from './components/Button'; // 自动加载对应平台实现 | Auto-loads platform implementation

  function App() {
    return (
      <Button
        title="Click Me"
        onPress={() => console.log('Pressed')}
      />
    );
  }
  // iOS设备: 蓝色按钮,17号字体 | iOS device: Blue button, 17pt font
  // Android设备: 蓝色按钮,大写文字,带阴影 | Android device: Blue button, uppercase text, with elevation
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Android上的按钮文字会如何显示? | How will button text display on Android?
    **答案 | Answer:** 全大写(CLICK ME),因为调用了title.toUpperCase() | All uppercase (CLICK ME) because title.toUpperCase() is called
  - 如果删除Button.ios.js会发生什么? | What happens if Button.ios.js is deleted?
    **答案 | Answer:** iOS会降级使用Button.js(如果存在),否则报错 | iOS will fall back to Button.js (if exists), otherwise error

  **常见误区检查 | Common Misconception Checks:**
  - 平台特定文件必须导出相同的props接口吗? | Must platform-specific files export the same props interface?
    **答案 | Answer:** 应该保持一致以保证跨平台API统一,但技术上可以不同 | Should maintain consistency for unified cross-platform API, but technically can differ

### 2. 响应式设计基础 - useWindowDimensions Hook | Responsive Design Basics - useWindowDimensions Hook (1小时 | 1 hour)

- **useWindowDimensions核心概念 | useWindowDimensions Core Concept**

  **概念定义 | Concept Definition:**
  useWindowDimensions是React Native提供的Hook,用于获取当前窗口的宽度、高度、缩放比例和字体缩放比例。与旧的Dimensions API不同,这个Hook会在屏幕旋转或窗口大小改变时自动触发组件重新渲染,是实现响应式布局的核心工具。| useWindowDimensions is a Hook provided by React Native for obtaining the current window's width, height, scale, and fontScale. Unlike the old Dimensions API, this Hook automatically triggers component re-renders when the screen rotates or window size changes, making it the core tool for implementing responsive layouts.

  **核心特征 | Key Characteristics:**
  - 返回对象包含width, height, scale, fontScale四个属性 | Returns object with width, height, scale, fontScale properties
  - 屏幕旋转时会自动更新并触发重新渲染 | Automatically updates and triggers re-render on screen rotation
  - scale表示像素密度比率(1x, 2x, 3x屏幕) | scale represents pixel density ratio (1x, 2x, 3x screens)
  - fontScale表示用户设置的字体缩放倍数(无障碍功能) | fontScale represents user's font scaling multiplier (accessibility feature)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useWindowDimensions和Dimensions.get('window')的主要区别是什么? | What's the main difference between useWindowDimensions and Dimensions.get('window')?
     **答案 | Answer:** useWindowDimensions是响应式的会自动更新;Dimensions.get()是静态的需要手动监听 | useWindowDimensions is reactive and auto-updates; Dimensions.get() is static and requires manual listeners
  2. 当设备从竖屏旋转到横屏时,width和height会交换吗? | When device rotates from portrait to landscape, do width and height swap?
     **答案 | Answer:** 是 | Yes - width变为旧height,height变为旧width,Hook会触发重新渲染 | width becomes old height, height becomes old width, Hook triggers re-render
  3. scale值为2.0表示什么? | What does a scale value of 2.0 mean?
     **答案 | Answer:** 2x Retina屏幕,物理像素是逻辑像素的2倍 | 2x Retina screen, physical pixels are 2x logical pixels
  4. fontScale可以小于1.0吗? | Can fontScale be less than 1.0?
     **答案 | Answer:** 理论上可以,但实际系统通常最小1.0,用户可以设置大于1.0的值 | Theoretically yes, but systems typically minimum 1.0, users can set values greater than 1.0

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet, useWindowDimensions } from 'react-native';

  function ResponsiveComponent() {
    // Hook返回实时窗口尺寸 | Hook returns real-time window dimensions
    const { width, height, scale, fontScale } = useWindowDimensions();

    // 根据宽度判断设备类型 | Determine device type based on width
    const isTablet = width >= 768;  // iPad Mini及以上 | iPad Mini and above
    const isPhone = width < 768;

    // 计算响应式列数 | Calculate responsive column count
    const columnCount = isTablet ? 3 : 2;
    const itemWidth = (width - 40) / columnCount; // 减去padding | Subtract padding

    return (
      <View style={styles.container}>
        <Text style={[styles.text, { fontSize: 16 * fontScale }]}>
          窗口宽度: {Math.round(width)}px | Window Width: {Math.round(width)}px
        </Text>
        <Text style={[styles.text, { fontSize: 16 * fontScale }]}>
          窗口高度: {Math.round(height)}px | Window Height: {Math.round(height)}px
        </Text>
        <Text style={[styles.text, { fontSize: 16 * fontScale }]}>
          设备类型: {isTablet ? '平板' : '手机'} | Device: {isTablet ? 'Tablet' : 'Phone'}
        </Text>
        <Text style={[styles.text, { fontSize: 16 * fontScale }]}>
          像素密度: {scale}x | Pixel Density: {scale}x
        </Text>
        <Text style={[styles.text, { fontSize: 16 * fontScale }]}>
          字体缩放: {fontScale.toFixed(2)} | Font Scale: {fontScale.toFixed(2)}
        </Text>

        {/* 响应式网格示例 | Responsive grid example */}
        <View style={styles.grid}>
          {[1, 2, 3, 4, 5, 6].map(num => (
            <View
              key={num}
              style={[
                styles.gridItem,
                {
                  width: itemWidth - 10,  // 10px间距 | 10px spacing
                  height: itemWidth - 10,
                }
              ]}
            >
              <Text>{num}</Text>
            </View>
          ))}
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    text: {
      marginBottom: 8,
      color: '#333',
    },
    grid: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      marginTop: 20,
      gap: 10, // 网格间距 | Grid spacing
    },
    gridItem: {
      backgroundColor: '#007AFF',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
    },
  });

  export default ResponsiveComponent;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在iPhone 14 Pro(393px宽)上,itemWidth大约是多少? | On iPhone 14 Pro (393px width), what is itemWidth approximately?
    **答案 | Answer:** (393 - 40) / 2 = 176.5px,因为是手机所以columnCount为2 | (393 - 40) / 2 = 176.5px, columnCount is 2 because it's a phone
  - 如果用户设置系统字体为"超大",fontScale可能是多少? | If user sets system font to "Extra Large", what might fontScale be?
    **答案 | Answer:** 约1.5-2.0,文字会相应放大以提高可读性 | Around 1.5-2.0, text will scale up accordingly for better readability

  **常见误区检查 | Common Misconception Checks:**
  - useWindowDimensions的width等于设备物理像素宽度吗? | Does useWindowDimensions width equal device physical pixel width?
    **答案 | Answer:** 否 - 它返回逻辑像素(DP),物理像素 = 逻辑像素 × scale | No - It returns logical pixels (DP), physical pixels = logical pixels × scale
  - 在组件外部使用useWindowDimensions可以吗? | Can useWindowDimensions be used outside components?
    **答案 | Answer:** 否 - 它是Hook,必须在函数组件内部调用 | No - It's a Hook, must be called inside function components

- **响应式布局策略 | Responsive Layout Strategies**

  **概念定义 | Concept Definition:**
  响应式布局策略是指根据设备屏幕尺寸、方向和像素密度动态调整UI布局的方法。在移动开发中,需要考虑从小屏手机(320px)到大屏平板(1024px+)的适配,以及横竖屏切换时的布局重构。| Responsive layout strategies refer to methods of dynamically adjusting UI layout based on device screen size, orientation, and pixel density. In mobile development, it's necessary to consider adaptation from small phones (320px) to large tablets (1024px+), as well as layout reconstruction during orientation changes.

  **核心特征 | Key Characteristics:**
  - 基于断点(breakpoints)定义不同屏幕尺寸的布局规则 | Define layout rules for different screen sizes based on breakpoints
  - 使用百分比、flex比例而非固定像素实现弹性布局 | Use percentages and flex ratios instead of fixed pixels for flexible layouts
  - 横竖屏方向改变时可能需要完全不同的组件结构 | May require completely different component structures for orientation changes
  - 考虑安全区域(Safe Area)避免刘海、圆角屏幕遮挡 | Consider Safe Area to avoid notch and rounded screen obstruction

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 什么是断点(breakpoint)? | What is a breakpoint?
     **答案 | Answer:** 预定义的屏幕宽度阈值,用于触发布局变化,如768px区分手机和平板 | Predefined screen width thresholds to trigger layout changes, like 768px to distinguish phones and tablets
  2. 横屏模式下应该如何调整列表布局? | How should list layout be adjusted in landscape mode?
     **答案 | Answer:** 可以从垂直单列改为水平多列网格,充分利用宽屏空间 | Can change from vertical single column to horizontal multi-column grid to utilize wide screen
  3. 为什么要使用flex而非固定宽度? | Why use flex instead of fixed width?
     **答案 | Answer:** flex可自动适应不同屏幕,固定宽度在小屏可能溢出或浪费空间 | flex auto-adapts to different screens, fixed width may overflow or waste space on small screens

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet, useWindowDimensions } from 'react-native';

  // 定义响应式断点 | Define responsive breakpoints
  const BREAKPOINTS = {
    phone: 0,
    tablet: 768,
    desktop: 1024,
  };

  function AdaptiveLayout() {
    const { width, height } = useWindowDimensions();

    // 判断设备类型 | Determine device type
    const deviceType = width >= BREAKPOINTS.desktop ? 'desktop'
                     : width >= BREAKPOINTS.tablet ? 'tablet'
                     : 'phone';

    // 判断屏幕方向 | Determine screen orientation
    const isPortrait = height > width;
    const isLandscape = width > height;

    // 响应式间距 | Responsive spacing
    const padding = deviceType === 'phone' ? 16 : 24;

    // 响应式列数 | Responsive column count
    const columns = deviceType === 'phone' ? 1
                  : deviceType === 'tablet' && isPortrait ? 2
                  : deviceType === 'tablet' && isLandscape ? 3
                  : 4;

    // 手机横屏时使用水平布局 | Use horizontal layout for phone landscape
    const flexDirection = deviceType === 'phone' && isLandscape
                        ? 'row'
                        : 'column';

    return (
      <View style={[styles.container, { padding, flexDirection }]}>
        <View style={styles.header}>
          <Text style={styles.title}>
            {deviceType.toUpperCase()} - {isPortrait ? '竖屏' : '横屏'} |
            {deviceType.toUpperCase()} - {isPortrait ? 'Portrait' : 'Landscape'}
          </Text>
          <Text style={styles.subtitle}>
            {width} × {height} | Columns: {columns}
          </Text>
        </View>

        {/* 响应式内容区 | Responsive content area */}
        <View style={[
          styles.content,
          {
            flexDirection: columns > 1 ? 'row' : 'column',
            flexWrap: 'wrap',
          }
        ]}>
          {[1, 2, 3, 4, 5, 6].map(item => (
            <View
              key={item}
              style={[
                styles.card,
                {
                  width: columns > 1
                       ? `${100 / columns - 2}%`  // 百分比宽度,减去margin | Percentage width minus margin
                       : '100%',
                  aspectRatio: deviceType === 'phone' ? 16/9 : 1, // 手机用宽屏比例 | Wide ratio for phones
                }
              ]}
            >
              <Text style={styles.cardText}>Card {item}</Text>
            </View>
          ))}
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f0f0f0',
    },
    header: {
      marginBottom: 20,
      paddingBottom: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#ddd',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      color: '#333',
    },
    subtitle: {
      fontSize: 14,
      color: '#666',
      marginTop: 4,
    },
    content: {
      gap: 16, // 子元素间距 | Gap between children
    },
    card: {
      backgroundColor: '#007AFF',
      borderRadius: 12,
      justifyContent: 'center',
      alignItems: 'center',
      marginBottom: 16,
    },
    cardText: {
      color: 'white',
      fontSize: 18,
      fontWeight: '600',
    },
  });

  export default AdaptiveLayout;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在iPad Pro 12.9"(1024px宽)横屏时,会显示几列? | On iPad Pro 12.9" (1024px width) landscape, how many columns will display?
    **答案 | Answer:** 4列,因为width >= 1024所以deviceType为'desktop',columns设为4 | 4 columns, because width >= 1024 so deviceType is 'desktop', columns set to 4
  - iPhone SE(375px宽)横屏时,主容器的flexDirection是什么? | On iPhone SE (375px width) landscape, what is the main container's flexDirection?
    **答案 | Answer:** 'row',因为是phone且横屏(width > height) | 'row', because it's phone and landscape (width > height)

  **常见误区检查 | Common Misconception Checks:**
  - 百分比宽度'50%'在所有屏幕上都是屏幕宽度的一半吗? | Is percentage width '50%' always half of screen width?
    **答案 | Answer:** 否 - 它是父容器宽度的50%,如果父容器有padding则实际显示宽度更小 | No - It's 50% of parent container width, if parent has padding actual display width is smaller

### 3. 平台特定样式实现 | Platform-Specific Styling Implementation (1小时 | 1 hour)

- **阴影的跨平台实现 | Cross-Platform Shadow Implementation**

  **概念定义 | Concept Definition:**
  iOS和Android使用完全不同的阴影系统:iOS使用shadow系列属性(shadowColor, shadowOffset, shadowOpacity, shadowRadius),而Android使用elevation属性。要实现跨平台一致的阴影效果,需要同时设置两套属性,或使用Platform.select()。| iOS and Android use completely different shadow systems: iOS uses shadow properties (shadowColor, shadowOffset, shadowOpacity, shadowRadius), while Android uses elevation property. To achieve consistent cross-platform shadow effects, you need to set both sets of properties or use Platform.select().

  **核心特征 | Key Characteristics:**
  - iOS阴影是基于图层的,支持自定义颜色、偏移、透明度和模糊半径 | iOS shadows are layer-based, supporting custom color, offset, opacity and blur radius
  - Android elevation基于Material Design,数值越大阴影越明显 | Android elevation based on Material Design, higher values create more prominent shadows
  - iOS阴影对性能有一定影响,建议使用shadowPath优化 | iOS shadows have performance impact, recommend using shadowPath for optimization
  - 两个系统的阴影视觉效果有细微差异,完全一致很难实现 | Visual effects of shadows on both systems have subtle differences, perfect consistency is difficult

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Android上设置shadowColor会生效吗? | Will shadowColor work on Android?
     **答案 | Answer:** 否 | No - Android只认elevation,shadowColor会被忽略 | Android only recognizes elevation, shadowColor is ignored
  2. elevation的值可以是负数吗? | Can elevation value be negative?
     **答案 | Answer:** 否 | No - elevation必须是非负数,负数会被视为0 | elevation must be non-negative, negative values treated as 0
  3. 为什么iOS阴影需要同时设置shadowOpacity? | Why do iOS shadows require shadowOpacity to be set?
     **答案 | Answer:** 默认shadowOpacity为0(完全透明),必须设置>0的值才能看到阴影 | Default shadowOpacity is 0 (fully transparent), must set >0 value to see shadow
  4. 哪个平台的阴影性能开销更大? | Which platform has higher shadow performance overhead?
     **答案 | Answer:** iOS - 因为是实时计算的图层效果;Android的elevation由GPU加速更高效 | iOS - because it's real-time calculated layer effect; Android elevation is GPU-accelerated more efficient

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet, Platform } from 'react-native';

  function ShadowCard({ children }) {
    return (
      <View style={styles.card}>
        {children}
      </View>
    );
  }

  const styles = StyleSheet.create({
    card: {
      backgroundColor: 'white',
      padding: 20,
      margin: 16,
      borderRadius: 12,

      // 方法1: 同时设置两个平台的阴影属性 | Method 1: Set shadow properties for both platforms
      // iOS阴影 | iOS shadow
      shadowColor: '#000',           // 阴影颜色 | Shadow color
      shadowOffset: {                // 阴影偏移 | Shadow offset
        width: 0,
        height: 2,
      },
      shadowOpacity: 0.25,           // 阴影透明度(0-1) | Shadow opacity (0-1)
      shadowRadius: 3.84,            // 阴影模糊半径 | Shadow blur radius

      // Android阴影 | Android shadow
      elevation: 5,                  // 海拔高度,控制阴影大小 | Elevation controls shadow size
    },
  });

  // 方法2: 使用Platform.select()精确控制 | Method 2: Precise control with Platform.select()
  const advancedStyles = StyleSheet.create({
    cardWithPlatformShadow: {
      backgroundColor: 'white',
      padding: 20,
      margin: 16,
      borderRadius: 12,

      // 使用Platform.select为不同平台定义阴影 | Use Platform.select to define shadows per platform
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 4 },
          shadowOpacity: 0.3,
          shadowRadius: 4.65,
        },
        android: {
          elevation: 8,
        },
      }),
    },

    // 方法3: 轻、中、重三种阴影级别 | Method 3: Light, medium, heavy shadow levels
    shadowLight: Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 1 },
        shadowOpacity: 0.18,
        shadowRadius: 1.0,
      },
      android: {
        elevation: 2,
      },
    }),

    shadowMedium: Platform.select({
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

    shadowHeavy: Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 6 },
        shadowOpacity: 0.37,
        shadowRadius: 7.49,
      },
      android: {
        elevation: 12,
      },
    }),
  });

  // 优化的阴影组件(iOS性能优化) | Optimized shadow component (iOS performance)
  function OptimizedShadowCard({ children, width = 200, height = 100 }) {
    const shadowPath = Platform.OS === 'ios'
      ? `M 0 0 L ${width} 0 L ${width} ${height} L 0 ${height} Z`
      : undefined;

    return (
      <View style={[
        styles.card,
        { width, height },
        Platform.OS === 'ios' && { shadowPath }, // iOS性能优化 | iOS performance optimization
      ]}>
        {children}
      </View>
    );
  }

  export default function ShadowDemo() {
    return (
      <View style={styles.container}>
        <ShadowCard>
          <Text>基础阴影卡片 | Basic Shadow Card</Text>
        </ShadowCard>

        <View style={[styles.card, advancedStyles.shadowLight]}>
          <Text>轻阴影 | Light Shadow</Text>
        </View>

        <View style={[styles.card, advancedStyles.shadowMedium]}>
          <Text>中等阴影 | Medium Shadow</Text>
        </View>

        <View style={[styles.card, advancedStyles.shadowHeavy]}>
          <Text>重阴影 | Heavy Shadow</Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
      paddingVertical: 20,
    },
    // ... (card styles from previous example)
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Android上的elevation: 5大致对应iOS的哪组阴影参数? | What iOS shadow parameters roughly correspond to Android elevation: 5?
    **答案 | Answer:** shadowOpacity: 0.25, shadowRadius: 3.84,这是shadowMedium的配置 | shadowOpacity: 0.25, shadowRadius: 3.84, this is shadowMedium configuration
  - 为什么shadowPath可以提升iOS性能? | Why can shadowPath improve iOS performance?
    **答案 | Answer:** 预定义阴影路径避免iOS实时计算阴影形状,减少渲染开销 | Predefined shadow path avoids iOS real-time shadow shape calculation, reducing render overhead

  **常见误区检查 | Common Misconception Checks:**
  - 设置了elevation后还需要设置shadowColor吗? | Do you still need to set shadowColor after setting elevation?
    **答案 | Answer:** 否 - elevation是Android独有,shadowColor是iOS独有,但为了代码可读性可以都写 | No - elevation is Android-only, shadowColor is iOS-only, but both can be written for code readability
  - iOS阴影会被View的borderRadius裁剪吗? | Will iOS shadow be clipped by View's borderRadius?
    **答案 | Answer:** 否 - 阴影基于View形状但不受borderRadius裁剪,会显示在圆角外 | No - shadow based on View shape but not clipped by borderRadius, will show outside rounded corners

- **字体和文本的平台差异 | Font and Text Platform Differences**

  **概念定义 | Concept Definition:**
  iOS和Android使用不同的系统默认字体族:iOS使用San Francisco(SF),Android使用Roboto。两个平台的字重(fontWeight)渲染、行高计算、字母间距也有差异。要实现一致的文本外观,需要考虑平台特定的字体属性或使用自定义字体。| iOS and Android use different system default font families: iOS uses San Francisco (SF), Android uses Roboto. Both platforms have differences in font weight rendering, line height calculation, and letter spacing. To achieve consistent text appearance, platform-specific font properties or custom fonts need to be considered.

  **核心特征 | Key Characteristics:**
  - iOS的SF字体更紧凑,Android的Roboto更开阔 | iOS SF font is more compact, Android Roboto is more spacious
  - Android的fontWeight:'500'显示比iOS更粗 | Android fontWeight:'500' displays bolder than iOS
  - iOS的默认lineHeight更紧密,Android有更多垂直空间 | iOS default lineHeight is tighter, Android has more vertical space
  - Material Design建议Android大写文字增加letterSpacing | Material Design recommends increased letterSpacing for Android uppercase text

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 相同的fontSize在两个平台上显示大小一样吗? | Does the same fontSize display the same size on both platforms?
     **答案 | Answer:** 大致相同,但由于字体族不同,视觉感受可能有差异 | Roughly same, but visual perception may differ due to different font families
  2. fontWeight:'bold'在Android上是多少数值? | What numeric value is fontWeight:'bold' on Android?
     **答案 | Answer:** 700 - 但Android的700比iOS的700显示更粗 | 700 - but Android's 700 displays bolder than iOS's 700
  3. 为什么Material Design按钮文字通常大写? | Why is Material Design button text typically uppercase?
     **答案 | Answer:** 这是Material设计规范,强调操作性和可读性 | It's Material design specification, emphasizing actionability and readability

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import { View, Text, StyleSheet, Platform } from 'react-native';

  function CrossPlatformText() {
    return (
      <View style={styles.container}>
        {/* 基础文本 - 使用系统默认字体 | Basic text - System default font */}
        <Text style={styles.defaultText}>
          默认字体文本 | Default Font Text
        </Text>

        {/* 平台特定字重 | Platform-specific font weight */}
        <Text style={styles.boldText}>
          加粗文本 (fontWeight: 'bold') | Bold Text
        </Text>

        {/* 平台优化的标题文本 | Platform-optimized heading text */}
        <Text style={styles.heading}>
          标题文本 | Heading Text
        </Text>

        {/* 按钮文本 - 遵循平台规范 | Button text - Following platform conventions */}
        <Text style={styles.buttonText}>
          {Platform.OS === 'android' ? 'BUTTON TEXT' : 'Button Text'}
        </Text>

        {/* 使用Platform.select优化的段落文本 | Paragraph text optimized with Platform.select */}
        <Text style={styles.paragraph}>
          这是一段较长的文本,展示不同平台的行高和间距差异。
          iOS使用San Francisco字体,而Android使用Roboto字体。
          | This is a longer text paragraph showing line height and spacing differences
          across platforms. iOS uses San Francisco font while Android uses Roboto.
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },

    defaultText: {
      fontSize: 16,
      marginBottom: 16,
      // 系统会自动使用平台默认字体 | System automatically uses platform default font
      // iOS: SF Pro Text | Android: Roboto
    },

    boldText: {
      fontSize: 16,
      fontWeight: 'bold', // iOS: 700, Android: 700 (but looks bolder)
      marginBottom: 16,
    },

    heading: {
      fontSize: 24,
      marginBottom: 16,
      // 使用Platform.select调整字重以达到视觉一致 | Use Platform.select to adjust weight for visual consistency
      ...Platform.select({
        ios: {
          fontWeight: '700',
        },
        android: {
          fontWeight: '600', // Android用600达到与iOS 700相似的效果 | Android 600 achieves similar effect to iOS 700
        },
      }),
    },

    buttonText: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 16,
      // Android按钮文本通常大写且有字母间距 | Android button text typically uppercase with letter spacing
      ...Platform.select({
        ios: {
          fontSize: 17, // iOS按钮文字稍大 | iOS button text slightly larger
        },
        android: {
          fontSize: 14,
          letterSpacing: 1.25, // Material Design规范 | Material Design spec
        },
      }),
    },

    paragraph: {
      fontSize: 16,
      marginBottom: 16,
      // 平台特定的行高和字母间距 | Platform-specific line height and letter spacing
      ...Platform.select({
        ios: {
          lineHeight: 22,      // iOS紧凑的行高 | iOS compact line height
          letterSpacing: -0.3, // iOS轻微的字母紧缩 | iOS slight letter tightening
        },
        android: {
          lineHeight: 24,      // Android更宽松的行高 | Android more relaxed line height
          letterSpacing: 0,    // Android默认间距 | Android default spacing
        },
      }),
    },
  });

  // 创建跨平台一致的文本组件 | Create cross-platform consistent text component
  function UniversalText({ children, variant = 'body', style, ...props }) {
    const textStyles = {
      heading: {
        fontSize: 24,
        ...Platform.select({
          ios: { fontWeight: '700' },
          android: { fontWeight: '600' },
        }),
      },
      subheading: {
        fontSize: 18,
        ...Platform.select({
          ios: { fontWeight: '600' },
          android: { fontWeight: '500' },
        }),
      },
      body: {
        fontSize: 16,
        ...Platform.select({
          ios: { lineHeight: 22 },
          android: { lineHeight: 24 },
        }),
      },
      caption: {
        fontSize: 12,
        color: '#666',
        ...Platform.select({
          ios: { lineHeight: 16 },
          android: { lineHeight: 18 },
        }),
      },
    };

    return (
      <Text style={[textStyles[variant], style]} {...props}>
        {children}
      </Text>
    );
  }

  export { CrossPlatformText, UniversalText };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Android上的buttonText为什么设置letterSpacing: 1.25? | Why is letterSpacing: 1.25 set for buttonText on Android?
    **答案 | Answer:** 遵循Material Design规范,大写文字需要增加字母间距以提高可读性 | Following Material Design spec, uppercase text needs increased letter spacing for readability
  - iOS的paragraph文本lineHeight为什么是22而不是24? | Why is iOS paragraph text lineHeight 22 instead of 24?
    **答案 | Answer:** SF字体自然更紧凑,较小的行高能保持与Android视觉上的一致性 | SF font is naturally more compact, smaller line height maintains visual consistency with Android

  **常见误区检查 | Common Misconception Checks:**
  - 在React Native中可以使用fontFamily: 'Arial'吗? | Can you use fontFamily: 'Arial' in React Native?
    **答案 | Answer:** 只有在该字体已安装在设备上时才可以,否则会降级到系统默认字体 | Only if that font is installed on the device, otherwise falls back to system default
  - letterSpacing的单位是什么? | What is the unit for letterSpacing?
    **答案 | Answer:** 逻辑像素(DP),与fontSize相同的单位 | Logical pixels (DP), same unit as fontSize

### 4. 高级响应式模式 - 方向感知布局 | Advanced Responsive Patterns - Orientation-Aware Layouts (1小时 | 1 hour)

- **屏幕方向检测与布局切换 | Screen Orientation Detection and Layout Switching**

  **概念定义 | Concept Definition:**
  方向感知布局是指根据设备的横屏(landscape)或竖屏(portrait)状态,动态改变组件结构和样式的设计模式。移动设备可以随时旋转,专业的应用需要在方向改变时优雅地重构UI,而不仅仅是缩放元素。| Orientation-aware layouts are design patterns that dynamically change component structure and styles based on the device's landscape or portrait state. Mobile devices can rotate at any time, and professional apps need to gracefully reconstruct UI when orientation changes, not just scale elements.

  **核心特征 | Key Characteristics:**
  - 通过比较width和height判断方向:width > height为横屏 | Determine orientation by comparing width and height: width > height is landscape
  - useWindowDimensions在方向改变时自动触发重新渲染 | useWindowDimensions automatically triggers re-render on orientation change
  - 可能需要完全不同的组件树结构,而非仅样式调整 | May require completely different component tree structure, not just style adjustments
  - 需要考虑方向锁定(orientation lock)的应用场景 | Need to consider orientation lock use cases

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如何判断当前是横屏还是竖屏? | How to determine if current orientation is landscape or portrait?
     **答案 | Answer:** const isLandscape = width > height - 宽度大于高度即为横屏 | const isLandscape = width > height - width greater than height means landscape
  2. 方向改变时useWindowDimensions会自动更新吗? | Does useWindowDimensions auto-update when orientation changes?
     **答案 | Answer:** 是 | Yes - 这是它相比Dimensions.get()的主要优势 | This is its main advantage over Dimensions.get()
  3. 正方形设备(width === height)怎么判断方向? | How to determine orientation on square devices (width === height)?
     **答案 | Answer:** 极少见,可以默认为竖屏或使用Orientation API获取传感器数据 | Very rare, can default to portrait or use Orientation API for sensor data
  4. 所有应用都应该支持横竖屏切换吗? | Should all apps support orientation switching?
     **答案 | Answer:** 否 - 某些应用(如游戏、视频播放器)可能锁定某一方向 | No - Some apps (like games, video players) may lock to one orientation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import {
    View,
    Text,
    Image,
    StyleSheet,
    useWindowDimensions,
    ScrollView
  } from 'react-native';

  function OrientationAwareArticle() {
    const { width, height } = useWindowDimensions();

    // 判断屏幕方向 | Determine screen orientation
    const isLandscape = width > height;
    const isPortrait = height > width;

    // 竖屏:上下结构(图片在上,文字在下) | Portrait: Vertical structure (image top, text bottom)
    // 横屏:左右结构(图片在左,文字在右) | Landscape: Horizontal structure (image left, text right)

    return (
      <ScrollView style={styles.container}>
        <View style={[
          styles.articleContainer,
          { flexDirection: isLandscape ? 'row' : 'column' } // 关键:方向决定flex方向 | Key: orientation determines flex direction
        ]}>
          {/* 图片区域 | Image area */}
          <View style={[
            styles.imageContainer,
            isLandscape ? styles.imageContainerLandscape : styles.imageContainerPortrait
          ]}>
            <Image
              source={{ uri: 'https://picsum.photos/800/600' }}
              style={styles.image}
              resizeMode="cover"
            />
          </View>

          {/* 文字区域 | Text area */}
          <View style={[
            styles.textContainer,
            isLandscape ? styles.textContainerLandscape : styles.textContainerPortrait
          ]}>
            <Text style={styles.title}>
              响应式文章布局 | Responsive Article Layout
            </Text>
            <Text style={styles.paragraph}>
              竖屏时,图片占满宽度显示在顶部,文字在下方。
              横屏时,布局自动切换为左右结构,图片占据左侧40%宽度,文字在右侧60%。
            </Text>
            <Text style={styles.paragraph}>
              In portrait mode, the image takes full width at the top, with text below.
              In landscape mode, layout automatically switches to side-by-side,
              image takes 40% left, text takes 60% right.
            </Text>
          </View>
        </View>
      </ScrollView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    articleContainer: {
      // flex方向由isLandscape动态决定 | flex direction dynamically determined by isLandscape
    },

    // 图片容器 - 竖屏样式 | Image container - Portrait styles
    imageContainer: {
      backgroundColor: '#f0f0f0',
    },
    imageContainerPortrait: {
      width: '100%',      // 竖屏:全宽 | Portrait: Full width
      height: 250,        // 固定高度 | Fixed height
    },
    imageContainerLandscape: {
      width: '40%',       // 横屏:40%宽度 | Landscape: 40% width
      height: '100%',     // 全高 | Full height
    },

    image: {
      width: '100%',
      height: '100%',
    },

    // 文字容器 - 竖屏样式 | Text container - Portrait styles
    textContainer: {
      padding: 20,
    },
    textContainerPortrait: {
      width: '100%',      // 竖屏:全宽 | Portrait: Full width
    },
    textContainerLandscape: {
      width: '60%',       // 横屏:60%宽度 | Landscape: 60% width
    },

    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 16,
    },
    paragraph: {
      fontSize: 16,
      lineHeight: 24,
      marginBottom: 12,
      color: '#333',
    },
  });

  // 更复杂的示例:列表在横竖屏显示不同列数 | More complex example: Grid with different columns
  function ResponsiveGrid({ items }) {
    const { width, height } = useWindowDimensions();
    const isLandscape = width > height;
    const isTablet = width >= 768;

    // 动态计算列数 | Dynamically calculate columns
    const columns = isTablet && isLandscape ? 4  // 平板横屏:4列 | Tablet landscape: 4 columns
                  : isTablet && !isLandscape ? 3 // 平板竖屏:3列 | Tablet portrait: 3 columns
                  : isLandscape ? 3              // 手机横屏:3列 | Phone landscape: 3 columns
                  : 2;                           // 手机竖屏:2列 | Phone portrait: 2 columns

    const itemWidth = (width - (columns + 1) * 10) / columns; // 10px间距 | 10px spacing

    return (
      <ScrollView contentContainerStyle={styles.gridContainer}>
        {items.map((item, index) => (
          <View
            key={index}
            style={[
              styles.gridItem,
              {
                width: itemWidth,
                height: itemWidth, // 正方形 | Square
              }
            ]}
          >
            <Text style={styles.gridItemText}>{item}</Text>
          </View>
        ))}
      </ScrollView>
    );
  }

  const gridStyles = StyleSheet.create({
    gridContainer: {
      flexDirection: 'row',
      flexWrap: 'wrap',
      padding: 10,
      gap: 10, // 网格间距 | Grid spacing
    },
    gridItem: {
      backgroundColor: '#007AFF',
      justifyContent: 'center',
      alignItems: 'center',
      borderRadius: 8,
    },
    gridItemText: {
      color: 'white',
      fontSize: 18,
      fontWeight: 'bold',
    },
  });

  export { OrientationAwareArticle, ResponsiveGrid };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - iPhone 12 Pro Max(428×926px)横屏时,articleContainer的flexDirection是什么? | On iPhone 12 Pro Max (428×926px) landscape, what is articleContainer's flexDirection?
    **答案 | Answer:** 'row',因为横屏时width(926) > height(428) | 'row', because in landscape width(926) > height(428)
  - iPad Pro(1024×1366px)竖屏时,ResponsiveGrid显示几列? | On iPad Pro (1024×1366px) portrait, how many columns does ResponsiveGrid show?
    **答案 | Answer:** 3列,因为isTablet为true且isLandscape为false | 3 columns, because isTablet is true and isLandscape is false

  **常见误区检查 | Common Misconception Checks:**
  - 方向改变时需要手动调用setState更新布局吗? | Do you need to manually call setState to update layout on orientation change?
    **答案 | Answer:** 否 - useWindowDimensions会自动触发重新渲染 | No - useWindowDimensions automatically triggers re-render
  - 横屏时只需要把竖屏的元素旋转90度吗? | In landscape, do you just need to rotate portrait elements 90 degrees?
    **答案 | Answer:** 否 - 通常需要重新设计布局结构,而非简单旋转 | No - Usually need to redesign layout structure, not simply rotate

- **安全区域(Safe Area)处理 | Safe Area Handling**

  **概念定义 | Concept Definition:**
  安全区域是指屏幕上不被系统UI(状态栏、导航栏、刘海、圆角、Home指示器)遮挡的区域。现代移动设备有各种形状的屏幕缺口,开发者必须使用SafeAreaView组件或useSafeAreaInsets Hook确保内容显示在可见区域内。| Safe Area refers to the screen area not obscured by system UI (status bar, navigation bar, notch, rounded corners, Home indicator). Modern mobile devices have various screen cutouts, developers must use SafeAreaView component or useSafeAreaInsets Hook to ensure content displays in visible area.

  **核心特征 | Key Characteristics:**
  - iPhone X及以后的设备有刘海(notch)和底部指示器 | iPhone X and later have notch and bottom indicator
  - Android设备可能有打孔屏、水滴屏等异形屏 | Android devices may have punch-hole, waterdrop screens
  - SafeAreaView只在iOS上有效,Android需要额外处理状态栏 | SafeAreaView only works on iOS, Android needs extra status bar handling
  - 横屏时安全区域的insets会改变(左右出现刘海区域) | In landscape, safe area insets change (notch area appears on sides)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. SafeAreaView在Android上有效果吗? | Does SafeAreaView work on Android?
     **答案 | Answer:** 部分有效 - 只处理状态栏,不处理导航栏和异形屏 | Partially - Only handles status bar, not navigation bar or notched screens
  2. 横屏时刘海会出现在哪里? | Where does the notch appear in landscape?
     **答案 | Answer:** 左侧或右侧(取决于旋转方向),不再是顶部 | Left or right side (depending on rotation), no longer at top
  3. useSafeAreaInsets返回什么? | What does useSafeAreaInsets return?
     **答案 | Answer:** 对象{top, right, bottom, left},表示四个方向的安全边距像素值 | Object {top, right, bottom, left}, representing safe margin pixels in four directions
  4. 全屏视频播放时需要考虑安全区域吗? | Do you need to consider safe area for fullscreen video?
     **答案 | Answer:** 不需要 - 全屏内容可以延伸到屏幕边缘,但控制按钮需要避开刘海 | No - Fullscreen content can extend to edges, but control buttons need to avoid notch

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import {
    View,
    Text,
    StyleSheet,
    SafeAreaView,
    Platform,
    StatusBar
  } from 'react-native';
  import { useSafeAreaInsets } from 'react-native-safe-area-context'; // 需要安装 | Requires installation

  // 方法1: 使用SafeAreaView (iOS优先) | Method 1: Using SafeAreaView (iOS-focused)
  function BasicSafeArea() {
    return (
      <SafeAreaView style={styles.safeContainer}>
        <Text>这段文字不会被刘海遮挡 | This text won't be obscured by notch</Text>
      </SafeAreaView>
    );
  }

  const styles = StyleSheet.create({
    safeContainer: {
      flex: 1,
      backgroundColor: '#007AFF',
      // SafeAreaView会自动添加padding避开系统UI | SafeAreaView auto-adds padding to avoid system UI
    },
  });

  // 方法2: 使用useSafeAreaInsets精确控制 | Method 2: Precise control with useSafeAreaInsets
  function AdvancedSafeArea() {
    const insets = useSafeAreaInsets();

    return (
      <View style={[
        styles.container,
        {
          paddingTop: insets.top,        // 顶部安全距离(状态栏+刘海) | Top safe distance (status bar + notch)
          paddingBottom: insets.bottom,  // 底部安全距离(Home指示器) | Bottom safe distance (Home indicator)
          paddingLeft: insets.left,      // 左侧安全距离(横屏刘海) | Left safe distance (landscape notch)
          paddingRight: insets.right,    // 右侧安全距离(横屏刘海) | Right safe distance (landscape notch)
        }
      ]}>
        <Text style={styles.text}>
          顶部距离: {insets.top}px{'\n'}
          底部距离: {insets.bottom}px{'\n'}
          左侧距离: {insets.left}px{'\n'}
          右侧距离: {insets.right}px
        </Text>
      </View>
    );
  }

  // 方法3: 跨平台安全区域方案 | Method 3: Cross-platform safe area solution
  function CrossPlatformSafeArea({ children }) {
    const insets = useSafeAreaInsets();

    return (
      <View style={[
        styles.container,
        {
          // iOS: 使用insets | iOS: Use insets
          // Android: 添加StatusBar高度 | Android: Add StatusBar height
          paddingTop: Platform.select({
            ios: insets.top,
            android: StatusBar.currentHeight || 0,
          }),
          paddingBottom: insets.bottom,
          paddingLeft: insets.left,
          paddingRight: insets.right,
        }
      ]}>
        {children}
      </View>
    );
  }

  // 实际应用:固定底部按钮的安全区域处理 | Real use case: Fixed bottom button with safe area
  function FixedBottomButton() {
    const insets = useSafeAreaInsets();

    return (
      <View style={styles.screenContainer}>
        {/* 主内容区 | Main content area */}
        <View style={styles.content}>
          <Text>页面内容 | Page Content</Text>
        </View>

        {/* 固定底部按钮 | Fixed bottom button */}
        <View style={[
          styles.fixedButton,
          {
            // 关键:底部padding确保不被Home指示器遮挡 | Key: Bottom padding ensures not obscured by Home indicator
            paddingBottom: insets.bottom > 0 ? insets.bottom : 16,
          }
        ]}>
          <Text style={styles.buttonText}>继续 | Continue</Text>
        </View>
      </View>
    );
  }

  const advancedStyles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    text: {
      fontSize: 16,
      lineHeight: 24,
    },
    screenContainer: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    content: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    fixedButton: {
      backgroundColor: '#007AFF',
      paddingVertical: 16,
      paddingHorizontal: 24,
      alignItems: 'center',
      // paddingBottom会由insets动态设置 | paddingBottom will be dynamically set by insets
    },
    buttonText: {
      color: 'white',
      fontSize: 18,
      fontWeight: '600',
    },
  });

  export { BasicSafeArea, AdvancedSafeArea, CrossPlatformSafeArea, FixedBottomButton };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - iPhone 14 Pro Max竖屏时,insets.top大约是多少? | On iPhone 14 Pro Max portrait, what is insets.top approximately?
    **答案 | Answer:** 约59px(状态栏54px + 动态岛区域) | About 59px (status bar 54px + Dynamic Island area)
  - Android设备的insets.bottom通常是多少? | What is insets.bottom typically on Android devices?
    **答案 | Answer:** 0,因为Android没有Home指示器(使用虚拟按键或手势栏) | 0, because Android doesn't have Home indicator (uses virtual buttons or gesture bar)

  **常见误区检查 | Common Misconception Checks:**
  - SafeAreaView可以嵌套使用吗? | Can SafeAreaView be nested?
    **答案 | Answer:** 技术上可以但不推荐,会导致多余的padding累加 | Technically yes but not recommended, causes redundant padding accumulation
  - 所有页面都必须使用SafeAreaView吗? | Must all pages use SafeAreaView?
    **答案 | Answer:** 否 - 只有根容器需要,子组件继承安全区域的布局 | No - Only root container needs it, child components inherit safe area layout

### 5. 创建跨平台UI组件库 | Creating Cross-Platform UI Component Library (30分钟 | 30 minutes)

- **设计系统的平台适配 | Design System Platform Adaptation**

  **概念定义 | Concept Definition:**
  跨平台UI组件库是指创建一套统一的组件API,在不同平台上自动应用符合该平台设计规范的样式和行为。这需要在保持开发体验一致性的同时,尊重iOS Human Interface Guidelines和Android Material Design的差异。| Cross-platform UI component library refers to creating a unified component API that automatically applies styles and behaviors conforming to platform design guidelines. This requires respecting differences between iOS Human Interface Guidelines and Android Material Design while maintaining development experience consistency.

  **核心特征 | Key Characteristics:**
  - 组件对外API保持一致,内部实现根据平台不同 | Component external API remains consistent, internal implementation differs by platform
  - 使用主题系统(theme)定义跨平台的设计令牌(tokens) | Use theme system to define cross-platform design tokens
  - 平台特定的交互反馈:iOS的轻触高亮vs Android的涟漪效果 | Platform-specific interaction feedback: iOS subtle highlight vs Android ripple effect
  - 导出平台无关的样式常量(颜色、间距、字号) | Export platform-agnostic style constants (colors, spacing, font sizes)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 设计令牌(design tokens)是什么? | What are design tokens?
     **答案 | Answer:** 设计系统中的原子级变量,如颜色、间距、字号,可跨平台复用 | Atomic-level variables in design system, like colors, spacing, font sizes, reusable cross-platform
  2. 为什么要封装平台特定组件? | Why encapsulate platform-specific components?
     **答案 | Answer:** 隐藏平台差异,提供统一API,降低使用者的平台知识门槛 | Hide platform differences, provide unified API, lower platform knowledge barrier for users
  3. 主题(theme)应该包含哪些内容? | What should a theme include?
     **答案 | Answer:** 颜色、字体、间距、圆角、阴影等视觉属性的系统化定义 | Systematic definitions of visual properties: colors, fonts, spacing, border radius, shadows

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // theme.js - 跨平台主题定义 | Cross-platform theme definition
  import { Platform } from 'react-native';

  const theme = {
    // 颜色系统 - 平台无关 | Color system - Platform agnostic
    colors: {
      primary: Platform.select({
        ios: '#007AFF',      // iOS蓝 | iOS blue
        android: '#2196F3',  // Material蓝 | Material blue
      }),
      background: '#FFFFFF',
      text: '#000000',
      textSecondary: '#666666',
      border: '#E0E0E0',
      error: '#FF3B30',
    },

    // 间距系统 - 8pt网格 | Spacing system - 8pt grid
    spacing: {
      xs: 4,
      sm: 8,
      md: 16,
      lg: 24,
      xl: 32,
    },

    // 字体系统 | Typography system
    typography: {
      h1: {
        fontSize: 28,
        ...Platform.select({
          ios: { fontWeight: '700' },
          android: { fontWeight: '600' },
        }),
      },
      h2: {
        fontSize: 22,
        fontWeight: '600',
      },
      body: {
        fontSize: 16,
        ...Platform.select({
          ios: { lineHeight: 22 },
          android: { lineHeight: 24 },
        }),
      },
      caption: {
        fontSize: 12,
        color: '#666666',
      },
    },

    // 圆角系统 | Border radius system
    borderRadius: {
      sm: Platform.select({ ios: 8, android: 4 }),
      md: Platform.select({ ios: 12, android: 8 }),
      lg: Platform.select({ ios: 16, android: 12 }),
      round: 9999, // 完全圆形 | Fully rounded
    },

    // 阴影预设 | Shadow presets
    shadows: {
      small: Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 1 },
          shadowOpacity: 0.18,
          shadowRadius: 1.0,
        },
        android: { elevation: 2 },
      }),
      medium: Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.25,
          shadowRadius: 3.84,
        },
        android: { elevation: 5 },
      }),
    },
  };

  export default theme;

  // Button.js - 跨平台按钮组件 | Cross-platform button component
  import React from 'react';
  import {
    TouchableOpacity,
    Text,
    StyleSheet,
    Platform,
    TouchableNativeFeedback, // Android涟漪效果 | Android ripple effect
    View
  } from 'react-native';
  import theme from './theme';

  function Button({
    title,
    onPress,
    variant = 'primary',
    disabled = false,
    fullWidth = false,
  }) {
    const buttonStyles = [
      styles.button,
      variant === 'primary' && styles.buttonPrimary,
      variant === 'secondary' && styles.buttonSecondary,
      fullWidth && styles.buttonFullWidth,
      disabled && styles.buttonDisabled,
    ];

    const textStyles = [
      styles.buttonText,
      variant === 'primary' && styles.buttonTextPrimary,
      variant === 'secondary' && styles.buttonTextSecondary,
    ];

    // Android使用原生涟漪效果 | Android uses native ripple effect
    if (Platform.OS === 'android') {
      return (
        <TouchableNativeFeedback
          onPress={disabled ? undefined : onPress}
          disabled={disabled}
          background={TouchableNativeFeedback.Ripple('#fff', false)}
        >
          <View style={buttonStyles}>
            <Text style={textStyles}>
              {Platform.OS === 'android' ? title.toUpperCase() : title}
            </Text>
          </View>
        </TouchableNativeFeedback>
      );
    }

    // iOS使用TouchableOpacity | iOS uses TouchableOpacity
    return (
      <TouchableOpacity
        style={buttonStyles}
        onPress={onPress}
        disabled={disabled}
        activeOpacity={0.7}
      >
        <Text style={textStyles}>{title}</Text>
      </TouchableOpacity>
    );
  }

  const styles = StyleSheet.create({
    button: {
      paddingVertical: theme.spacing.md,
      paddingHorizontal: theme.spacing.lg,
      borderRadius: theme.borderRadius.md,
      alignItems: 'center',
      justifyContent: 'center',
      ...theme.shadows.small,
    },
    buttonPrimary: {
      backgroundColor: theme.colors.primary,
    },
    buttonSecondary: {
      backgroundColor: 'transparent',
      borderWidth: 1,
      borderColor: theme.colors.primary,
    },
    buttonFullWidth: {
      width: '100%',
    },
    buttonDisabled: {
      opacity: 0.5,
    },
    buttonText: {
      fontSize: 16,
      fontWeight: '600',
      ...Platform.select({
        ios: {
          fontSize: 17,
        },
        android: {
          fontSize: 14,
          letterSpacing: 1.25,
        },
      }),
    },
    buttonTextPrimary: {
      color: '#fff',
    },
    buttonTextSecondary: {
      color: theme.colors.primary,
    },
  });

  export default Button;

  // Card.js - 跨平台卡片组件 | Cross-platform card component
  import React from 'react';
  import { View, StyleSheet } from 'react-native';
  import theme from './theme';

  function Card({ children, variant = 'elevated' }) {
    return (
      <View style={[
        styles.card,
        variant === 'elevated' && theme.shadows.medium,
        variant === 'outlined' && styles.cardOutlined,
      ]}>
        {children}
      </View>
    );
  }

  const styles = StyleSheet.create({
    card: {
      backgroundColor: theme.colors.background,
      borderRadius: theme.borderRadius.md,
      padding: theme.spacing.md,
      marginVertical: theme.spacing.sm,
    },
    cardOutlined: {
      borderWidth: 1,
      borderColor: theme.colors.border,
    },
  });

  export { Button, Card, theme };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Android按钮的title为什么要调用toUpperCase()? | Why is title.toUpperCase() called for Android buttons?
    **答案 | Answer:** 遵循Material Design规范,按钮文字应全大写 | Following Material Design spec, button text should be all caps
  - 为什么iOS使用TouchableOpacity而Android使用TouchableNativeFeedback? | Why does iOS use TouchableOpacity while Android uses TouchableNativeFeedback?
    **答案 | Answer:** iOS规范用透明度反馈,Android规范用涟漪效果,符合各自平台习惯 | iOS spec uses opacity feedback, Android spec uses ripple, matching platform conventions

  **常见误区检查 | Common Misconception Checks:**
  - 主题颜色可以在运行时动态切换吗? | Can theme colors be dynamically switched at runtime?
    **答案 | Answer:** 可以通过Context或状态管理实现深色/浅色主题切换 | Yes, can implement dark/light theme switching via Context or state management

### 6. 实战综合 - 平板与手机自适应仪表板 | Practical Integration - Tablet & Phone Adaptive Dashboard (30分钟 | 30 minutes)

- **综合应用检查 | Comprehensive Application Check**

  **概念定义 | Concept Definition:**
  自适应仪表板是一个综合性实践项目,需要整合本日学习的所有概念:Platform检测、useWindowDimensions响应式布局、平台特定样式、方向感知、安全区域处理。项目要求在手机和平板、横竖屏之间无缝切换布局。| Adaptive dashboard is a comprehensive practice project integrating all concepts learned today: Platform detection, useWindowDimensions responsive layout, platform-specific styles, orientation awareness, safe area handling. The project requires seamless layout switching between phone and tablet, portrait and landscape.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 仪表板在手机竖屏和平板横屏应该有什么布局差异? | What layout differences should the dashboard have between phone portrait and tablet landscape?
     **答案 | Answer:** 手机竖屏用垂直单列,平板横屏可用多列网格或侧边栏+主内容布局 | Phone portrait uses vertical single column, tablet landscape can use multi-column grid or sidebar + main content layout
  2. 如何确保仪表板在刘海屏上正确显示? | How to ensure dashboard displays correctly on notched screens?
     **答案 | Answer:** 使用SafeAreaView或useSafeAreaInsets处理顶部和底部安全区域 | Use SafeAreaView or useSafeAreaInsets to handle top and bottom safe areas
  3. 图表组件的尺寸应该如何响应屏幕变化? | How should chart component size respond to screen changes?
     **答案 | Answer:** 使用useWindowDimensions获取实时尺寸,按百分比或计算值设置图表宽高 | Use useWindowDimensions to get real-time dimensions, set chart width/height by percentage or calculated values

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React from 'react';
  import {
    View,
    Text,
    StyleSheet,
    ScrollView,
    useWindowDimensions,
    Platform,
  } from 'react-native';
  import { useSafeAreaInsets } from 'react-native-safe-area-context';

  // 仪表板主组件 | Dashboard main component
  function AdaptiveDashboard() {
    const { width, height } = useWindowDimensions();
    const insets = useSafeAreaInsets();

    // 设备和方向判断 | Device and orientation detection
    const isTablet = width >= 768;
    const isLandscape = width > height;

    // 响应式布局配置 | Responsive layout config
    const layout = {
      columns: isTablet && isLandscape ? 3 : isTablet ? 2 : 1,
      padding: isTablet ? 24 : 16,
      cardWidth: isTablet && isLandscape
                 ? (width - 72) / 3  // 3列布局 | 3-column layout
                 : isTablet
                 ? (width - 56) / 2  // 2列布局 | 2-column layout
                 : width - 32,       // 单列布局 | Single column
    };

    return (
      <View style={[
        styles.container,
        {
          paddingTop: insets.top,
          paddingBottom: insets.bottom,
        }
      ]}>
        {/* 头部 | Header */}
        <View style={[styles.header, { paddingHorizontal: layout.padding }]}>
          <Text style={styles.headerTitle}>仪表板 | Dashboard</Text>
          <Text style={styles.headerSubtitle}>
            {isTablet ? '平板' : '手机'} · {isLandscape ? '横屏' : '竖屏'}
            {' | '}
            {isTablet ? 'Tablet' : 'Phone'} · {isLandscape ? 'Landscape' : 'Portrait'}
          </Text>
        </View>

        <ScrollView
          contentContainerStyle={[
            styles.content,
            { padding: layout.padding }
          ]}
        >
          {/* 统计卡片行 | Stats cards row */}
          <View style={[
            styles.statsContainer,
            { flexDirection: layout.columns > 1 ? 'row' : 'column' }
          ]}>
            {[
              { label: '总用户 | Total Users', value: '1,234' },
              { label: '活跃用户 | Active', value: '856' },
              { label: '收入 | Revenue', value: '$12.5K' },
            ].map((stat, index) => (
              <StatCard
                key={index}
                label={stat.label}
                value={stat.value}
                width={layout.columns > 1 ? layout.cardWidth : '100%'}
              />
            ))}
          </View>

          {/* 图表区域 | Chart area */}
          <ChartCard
            title="用户增长趋势 | User Growth Trend"
            width={width - layout.padding * 2}
            height={isTablet ? 300 : 200}
          />

          {/* 活动列表 | Activity list */}
          <View style={styles.activitySection}>
            <Text style={styles.sectionTitle}>
              最近活动 | Recent Activity
            </Text>
            {[1, 2, 3, 4].map(item => (
              <ActivityItem key={item} index={item} />
            ))}
          </View>
        </ScrollView>
      </View>
    );
  }

  // 统计卡片组件 | Stat card component
  function StatCard({ label, value, width }) {
    return (
      <View style={[
        styles.statCard,
        { width },
        Platform.select({
          ios: {
            shadowColor: '#000',
            shadowOffset: { width: 0, height: 2 },
            shadowOpacity: 0.1,
            shadowRadius: 4,
          },
          android: {
            elevation: 3,
          },
        }),
      ]}>
        <Text style={styles.statValue}>{value}</Text>
        <Text style={styles.statLabel}>{label}</Text>
      </View>
    );
  }

  // 图表卡片组件 | Chart card component
  function ChartCard({ title, width, height }) {
    return (
      <View style={[
        styles.chartCard,
        { width },
        Platform.select({
          ios: {
            shadowColor: '#000',
            shadowOffset: { width: 0, height: 2 },
            shadowOpacity: 0.1,
            shadowRadius: 4,
          },
          android: {
            elevation: 3,
          },
        }),
      ]}>
        <Text style={styles.chartTitle}>{title}</Text>
        <View style={[styles.chartPlaceholder, { height }]}>
          <Text style={styles.chartPlaceholderText}>
            📊 图表占位符 | Chart Placeholder
          </Text>
        </View>
      </View>
    );
  }

  // 活动项组件 | Activity item component
  function ActivityItem({ index }) {
    return (
      <View style={styles.activityItem}>
        <View style={styles.activityDot} />
        <View style={styles.activityContent}>
          <Text style={styles.activityTitle}>
            用户操作 #{index} | User Action #{index}
          </Text>
          <Text style={styles.activityTime}>
            {index}分钟前 | {index} minutes ago
          </Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    header: {
      paddingVertical: 20,
      backgroundColor: '#fff',
      borderBottomWidth: 1,
      borderBottomColor: '#e0e0e0',
    },
    headerTitle: {
      fontSize: 28,
      fontWeight: 'bold',
      color: '#333',
    },
    headerSubtitle: {
      fontSize: 14,
      color: '#666',
      marginTop: 4,
    },
    content: {
      paddingBottom: 20,
    },
    statsContainer: {
      gap: 16,
      marginBottom: 20,
    },
    statCard: {
      backgroundColor: '#fff',
      padding: 20,
      borderRadius: Platform.select({ ios: 12, android: 8 }),
      marginBottom: 16,
    },
    statValue: {
      fontSize: 32,
      fontWeight: 'bold',
      color: Platform.select({ ios: '#007AFF', android: '#2196F3' }),
      marginBottom: 8,
    },
    statLabel: {
      fontSize: 14,
      color: '#666',
    },
    chartCard: {
      backgroundColor: '#fff',
      padding: 20,
      borderRadius: Platform.select({ ios: 12, android: 8 }),
      marginBottom: 20,
    },
    chartTitle: {
      fontSize: 18,
      fontWeight: '600',
      marginBottom: 16,
    },
    chartPlaceholder: {
      backgroundColor: '#f0f0f0',
      borderRadius: 8,
      justifyContent: 'center',
      alignItems: 'center',
    },
    chartPlaceholderText: {
      fontSize: 16,
      color: '#999',
    },
    activitySection: {
      backgroundColor: '#fff',
      padding: 20,
      borderRadius: Platform.select({ ios: 12, android: 8 }),
    },
    sectionTitle: {
      fontSize: 18,
      fontWeight: '600',
      marginBottom: 16,
    },
    activityItem: {
      flexDirection: 'row',
      alignItems: 'center',
      paddingVertical: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#f0f0f0',
    },
    activityDot: {
      width: 8,
      height: 8,
      borderRadius: 4,
      backgroundColor: Platform.select({ ios: '#007AFF', android: '#2196F3' }),
      marginRight: 12,
    },
    activityContent: {
      flex: 1,
    },
    activityTitle: {
      fontSize: 16,
      color: '#333',
      marginBottom: 4,
    },
    activityTime: {
      fontSize: 12,
      color: '#999',
    },
  });

  export default AdaptiveDashboard;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - iPad Pro(1024×1366px)横屏时,统计卡片的宽度是多少? | On iPad Pro (1024×1366px) landscape, what is the width of stat cards?
    **答案 | Answer:** (1024 - 72) / 3 ≈ 317px,因为3列布局,减去padding(24×3) | (1024 - 72) / 3 ≈ 317px, because 3-column layout, minus padding (24×3)
  - 为什么头部需要设置paddingTop为insets.top? | Why does header need paddingTop set to insets.top?
    **答案 | Answer:** 避免内容被状态栏或刘海遮挡,确保在安全区域内显示 | Avoid content being obscured by status bar or notch, ensure display in safe area

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要为每个方向编写单独的组件? | Do you need to write separate components for each orientation?
    **答案 | Answer:** 否 - 使用响应式逻辑和条件渲染,一个组件处理所有情况 | No - Use responsive logic and conditional rendering, one component handles all cases

## 实践项目：跨平台响应式个人资料页 | Practical Project: Cross-Platform Responsive Profile Page

### 目标 | Objective
构建一个个人资料页面,展示Platform模块、useWindowDimensions、平台特定样式和安全区域处理的综合应用。页面需要在手机/平板、横竖屏、iOS/Android上都能完美适配。| Build a profile page demonstrating comprehensive application of Platform module, useWindowDimensions, platform-specific styles, and safe area handling. The page must perfectly adapt across phone/tablet, portrait/landscape, iOS/Android.

### 概念应用检查 | Concept Application Check
在开始项目前,请确认对以下概念的理解 | Before starting the project, confirm understanding of the following concepts:

1. Platform.select()和简单的if判断有什么区别? | What's the difference between Platform.select() and simple if statements?
   **答案 | Answer:** Platform.select()返回对象值,更适合样式配置;if判断用于逻辑分支 | Platform.select() returns object values, better for style configs; if for logic branching

2. useWindowDimensions在屏幕旋转时会自动更新吗? | Does useWindowDimensions auto-update on screen rotation?
   **答案 | Answer:** 是,这是它的核心优势,会触发组件重新渲染 | Yes, this is its core advantage, triggers component re-render

3. iOS和Android的阴影属性可以共存吗? | Can iOS and Android shadow properties coexist?
   **答案 | Answer:** 可以,各平台只识别自己的属性,互不干扰 | Yes, each platform only recognizes its own properties, no interference

### 步骤 | Steps
1. **创建项目结构** | **Create project structure**
   - 设置SafeAreaProvider根组件 | Set up SafeAreaProvider root component
   - 创建主题配置文件(theme.js) | Create theme config file (theme.js)
   - 定义响应式断点常量 | Define responsive breakpoint constants

2. **实现头部区域** | **Implement header area**
   - 使用Platform.select设置平台特定头部高度 | Use Platform.select to set platform-specific header height
   - 添加头像(圆形,带平台特定阴影) | Add avatar (circular with platform-specific shadow)
   - 用户名使用平台优化的字体样式 | Username uses platform-optimized font styles

3. **构建响应式统计区域** | **Build responsive stats section**
   - 使用useWindowDimensions判断设备类型 | Use useWindowDimensions to determine device type
   - 手机竖屏:垂直排列,平板横屏:水平3列 | Phone portrait: vertical, tablet landscape: horizontal 3 columns
   - 统计卡片应用跨平台阴影 | Apply cross-platform shadows to stat cards

4. **实现设置列表** | **Implement settings list**
   - 每个设置项根据方向调整布局 | Each setting item adjusts layout based on orientation
   - 使用平台特定的图标和交互反馈 | Use platform-specific icons and interaction feedback
   - iOS用chevron右箭头,Android用Material箭头 | iOS uses chevron right, Android uses Material arrow

5. **底部安全区域处理** | **Handle bottom safe area**
   - 使用useSafeAreaInsets获取底部边距 | Use useSafeAreaInsets to get bottom margin
   - 退出登录按钮保持在安全区域内 | Logout button stays within safe area

### 示例代码 | Example Code
```javascript
import React from 'react';
import {
  View,
  Text,
  Image,
  StyleSheet,
  ScrollView,
  TouchableOpacity,
  Platform,
  useWindowDimensions,
} from 'react-native';
import { SafeAreaProvider, useSafeAreaInsets } from 'react-native-safe-area-context';

// 主题配置 | Theme config
const theme = {
  colors: {
    primary: Platform.select({ ios: '#007AFF', android: '#2196F3' }),
    background: '#F5F5F5',
    card: '#FFFFFF',
    text: '#000000',
    textSecondary: '#666666',
  },
  spacing: { sm: 8, md: 16, lg: 24, xl: 32 },
  borderRadius: Platform.select({ ios: 12, android: 8 }),
};

// 个人资料页面组件 | Profile page component
function ProfileScreen() {
  const { width, height } = useWindowDimensions();
  const insets = useSafeAreaInsets();

  // 响应式逻辑 | Responsive logic
  const isTablet = width >= 768;
  const isLandscape = width > height;
  const statsLayout = isTablet && isLandscape ? 'row' : 'column';

  return (
    <View style={[styles.container, { paddingTop: insets.top }]}>
      {/* 头部区域 | Header section */}
      <View style={styles.header}>
        <Image
          source={{ uri: 'https://i.pravatar.cc/300' }}
          style={[
            styles.avatar,
            Platform.select({
              ios: {
                shadowColor: '#000',
                shadowOffset: { width: 0, height: 4 },
                shadowOpacity: 0.3,
                shadowRadius: 6,
              },
              android: { elevation: 8 },
            }),
          ]}
        />
        <Text style={styles.username}>张三 | John Doe</Text>
        <Text style={styles.email}>john.doe@example.com</Text>
      </View>

      <ScrollView
        style={styles.content}
        contentContainerStyle={{ paddingBottom: insets.bottom + 20 }}
      >
        {/* 统计区域 | Stats section */}
        <View style={[styles.statsContainer, { flexDirection: statsLayout }]}>
          <StatCard title="帖子 | Posts" value="42" />
          <StatCard title="关注者 | Followers" value="1.2K" />
          <StatCard title="关注中 | Following" value="328" />
        </View>

        {/* 设置列表 | Settings list */}
        <View style={styles.settingsContainer}>
          <SettingItem
            title="编辑资料 | Edit Profile"
            icon="✏️"
            isLandscape={isLandscape}
          />
          <SettingItem
            title="隐私设置 | Privacy"
            icon="🔒"
            isLandscape={isLandscape}
          />
          <SettingItem
            title="通知 | Notifications"
            icon="🔔"
            isLandscape={isLandscape}
          />
          <SettingItem
            title="帮助中心 | Help Center"
            icon="❓"
            isLandscape={isLandscape}
          />
        </View>

        {/* 退出按钮 | Logout button */}
        <TouchableOpacity
          style={[
            styles.logoutButton,
            Platform.select({
              ios: {
                shadowColor: '#000',
                shadowOffset: { width: 0, height: 2 },
                shadowOpacity: 0.1,
                shadowRadius: 4,
              },
              android: { elevation: 2 },
            }),
          ]}
        >
          <Text style={styles.logoutText}>
            {Platform.OS === 'android' ? '退出登录 | LOGOUT' : '退出登录 | Logout'}
          </Text>
        </TouchableOpacity>
      </ScrollView>
    </View>
  );
}

// 统计卡片组件 | Stat card component
function StatCard({ title, value }) {
  return (
    <View style={[
      styles.statCard,
      Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.1,
          shadowRadius: 4,
        },
        android: { elevation: 3 },
      }),
    ]}>
      <Text style={styles.statValue}>{value}</Text>
      <Text style={styles.statTitle}>{title}</Text>
    </View>
  );
}

// 设置项组件 | Setting item component
function SettingItem({ title, icon, isLandscape }) {
  const arrow = Platform.OS === 'ios' ? '›' : '→';

  return (
    <TouchableOpacity
      style={[
        styles.settingItem,
        isLandscape && styles.settingItemLandscape,
      ]}
    >
      <View style={styles.settingLeft}>
        <Text style={styles.settingIcon}>{icon}</Text>
        <Text style={styles.settingTitle}>{title}</Text>
      </View>
      <Text style={styles.settingArrow}>{arrow}</Text>
    </TouchableOpacity>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: theme.colors.background,
  },
  header: {
    alignItems: 'center',
    paddingVertical: theme.spacing.xl,
    backgroundColor: theme.colors.card,
    borderBottomLeftRadius: theme.borderRadius,
    borderBottomRightRadius: theme.borderRadius,
  },
  avatar: {
    width: 100,
    height: 100,
    borderRadius: 50,
    marginBottom: theme.spacing.md,
    backgroundColor: '#E0E0E0',
  },
  username: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 4,
    ...Platform.select({
      ios: { fontWeight: '700' },
      android: { fontWeight: '600' },
    }),
  },
  email: {
    fontSize: 14,
    color: theme.colors.textSecondary,
  },
  content: {
    flex: 1,
    padding: theme.spacing.md,
  },
  statsContainer: {
    gap: theme.spacing.md,
    marginBottom: theme.spacing.lg,
  },
  statCard: {
    flex: 1,
    backgroundColor: theme.colors.card,
    padding: theme.spacing.lg,
    borderRadius: theme.borderRadius,
    alignItems: 'center',
  },
  statValue: {
    fontSize: 28,
    fontWeight: 'bold',
    color: theme.colors.primary,
    marginBottom: 4,
  },
  statTitle: {
    fontSize: 14,
    color: theme.colors.textSecondary,
  },
  settingsContainer: {
    backgroundColor: theme.colors.card,
    borderRadius: theme.borderRadius,
    marginBottom: theme.spacing.lg,
    overflow: 'hidden',
  },
  settingItem: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    padding: theme.spacing.md,
    borderBottomWidth: 1,
    borderBottomColor: '#F0F0F0',
  },
  settingItemLandscape: {
    paddingVertical: theme.spacing.lg, // 横屏时增加垂直内边距 | Increase vertical padding in landscape
  },
  settingLeft: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  settingIcon: {
    fontSize: 24,
    marginRight: theme.spacing.md,
  },
  settingTitle: {
    fontSize: 16,
    color: theme.colors.text,
  },
  settingArrow: {
    fontSize: 24,
    color: theme.colors.textSecondary,
  },
  logoutButton: {
    backgroundColor: '#FF3B30',
    padding: theme.spacing.md,
    borderRadius: theme.borderRadius,
    alignItems: 'center',
  },
  logoutText: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: '600',
    ...Platform.select({
      android: {
        letterSpacing: 1.25,
      },
    }),
  },
});

// 应用根组件 | App root component
export default function App() {
  return (
    <SafeAreaProvider>
      <ProfileScreen />
    </SafeAreaProvider>
  );
}
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确使用了Platform.select()处理平台差异? | Does the project correctly use Platform.select() to handle platform differences?
2. 响应式布局是否在不同设备和方向上都正确切换? | Does responsive layout correctly switch across different devices and orientations?
3. 阴影效果在iOS和Android上是否都正确显示? | Are shadow effects correctly displayed on both iOS and Android?
4. 安全区域是否正确处理,内容不被刘海或圆角遮挡? | Is safe area correctly handled, content not obscured by notch or rounded corners?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Platform模块深度探索 | Platform Module Deep Dive**
   - **练习描述 | Exercise Description:** 创建一个组件展示Platform模块的所有属性(OS, Version, isPad, isTV等),并使用Platform.select()实现至少5种不同的平台特定样式
   - **概念检查 | Concept Check:** Platform.Version在iOS和Android上返回的数据类型是否相同? | Does Platform.Version return the same data type on iOS and Android?
   - **学习目标 | Learning Objective:** 深入理解Platform API的完整功能和使用场景

2. **useWindowDimensions实时监控练习 | useWindowDimensions Real-time Monitoring**
   - **练习描述 | Exercise Description:** 构建一个尺寸监控仪表板,实时显示width、height、scale、fontScale,并在数值变化时高亮显示
   - **概念检查 | Concept Check:** 旋转屏幕时useWindowDimensions会自动触发重新渲染吗? | Does useWindowDimensions auto-trigger re-render on rotation?
   - **学习目标 | Learning Objective:** 掌握响应式Hook的工作原理和性能特性

3. **跨平台阴影一致性练习 | Cross-Platform Shadow Consistency**
   - **练习描述 | Exercise Description:** 创建3个阴影级别(light/medium/heavy)的卡片,调整参数使iOS和Android视觉效果尽可能接近
   - **概念检查 | Concept Check:** iOS的shadowOpacity和Android的elevation哪个对性能影响更大? | Which has bigger performance impact: iOS shadowOpacity or Android elevation?
   - **学习目标 | Learning Objective:** 理解跨平台阴影实现的差异和优化技巧

4. **方向感知UI重构练习 | Orientation-Aware UI Reconstruction**
   - **练习描述 | Exercise Description:** 实现一个新闻阅读器,竖屏时标题在图片上方,横屏时标题在图片右侧,完全改变组件树结构
   - **概念检查 | Concept Check:** 方向改变时是否需要手动调用setState? | Do you need to manually call setState on orientation change?
   - **学习目标 | Learning Objective:** 学会基于方向动态重构组件结构而非仅调整样式

5. **安全区域边缘案例练习 | Safe Area Edge Cases**
   - **练习描述 | Exercise Description:** 创建一个全屏视频播放器,视频延伸到屏幕边缘,但控制按钮避开刘海和圆角区域
   - **概念检查 | Concept Check:** SafeAreaView和useSafeAreaInsets有什么区别? | What's the difference between SafeAreaView and useSafeAreaInsets?
   - **学习目标 | Learning Objective:** 掌握安全区域的高级应用场景

6. **主题系统构建练习 | Theme System Construction**
   - **练习描述 | Exercise Description:** 创建一个完整的主题系统,包含颜色、间距、字体、阴影的设计令牌,并实现深色/浅色模式切换
   - **概念检查 | Concept Check:** 设计令牌(design tokens)应该包含哪些类别? | What categories should design tokens include?
   - **学习目标 | Learning Objective:** 学会构建可维护的跨平台设计系统

7. **综合响应式应用练习 | Comprehensive Responsive App**
   - **练习描述 | Exercise Description:** 扩展实践项目的个人资料页,添加照片网格(手机2列/平板4列)和设置项的横屏优化布局
   - **概念检查 | Concept Check:** 如何计算网格项宽度以适应不同列数? | How to calculate grid item width to fit different column counts?
   - **学习目标 | Learning Objective:** 将所有响应式技术整合到实际应用中

## 学习资源 | Learning Resources
- [Platform API官方文档 | Platform API Official Docs](https://reactnative.dev/docs/platform)
- [useWindowDimensions Hook文档 | useWindowDimensions Hook Docs](https://reactnative.dev/docs/usewindowdimensions)
- [iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/ios)
- [Material Design Guidelines](https://material.io/design)
- [react-native-safe-area-context库 | react-native-safe-area-context Library](https://github.com/th3rdwave/react-native-safe-area-context)
- [响应式设计最佳实践 | Responsive Design Best Practices](https://thoughtbot.com/blog/structure-for-styling-in-react-native)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Platform.OS和Platform.select()的使用场景 | Understand use cases for Platform.OS and Platform.select()
- [ ] 能够创建.ios.js和.android.js平台特定文件 | Can create .ios.js and .android.js platform-specific files
- [ ] 掌握useWindowDimensions Hook实现响应式布局 | Master useWindowDimensions Hook for responsive layouts
- [ ] 理解iOS阴影属性与Android elevation的差异 | Understand differences between iOS shadow properties and Android elevation
- [ ] 能够实现跨平台一致的文本样式 | Can implement cross-platform consistent text styles
- [ ] 掌握基于屏幕方向改变组件结构的技巧 | Master techniques for changing component structure based on orientation
- [ ] 理解并正确使用SafeAreaView和useSafeAreaInsets | Understand and correctly use SafeAreaView and useSafeAreaInsets
- [ ] 能够创建响应手机和平板的自适应布局 | Can create adaptive layouts responsive to phones and tablets
- [ ] 构建了跨平台UI组件库的基础 | Built foundation for cross-platform UI component library
- [ ] 完成了响应式个人资料页实践项目 | Completed responsive profile page practice project

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前,请确保能够正确回答本日所有CCQs,并能够向他人清晰解释Platform检测、响应式布局、平台特定样式和安全区域处理的核心概念。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others the core concepts of Platform detection, responsive layouts, platform-specific styles, and safe area handling.
