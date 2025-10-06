# React Native入门 - 第3天：图片与媒体处理 | React Native Introduction - Day 3: Images and Media

## 学习目标 | Learning Objectives
- 掌握React Native中Image组件的核心用法 | Master core usage of the Image component in React Native
- 理解本地图片与远程图片的加载机制差异 | Understand the differences between local and remote image loading mechanisms
- 熟练应用resizeMode控制图片显示方式 | Proficiently apply resizeMode to control image display
- 实现圆形头像等常见图片样式 | Implement common image styles like circular avatars
- 处理远程图片的加载状态与错误处理 | Handle loading states and error handling for remote images
- 理解移动端图片性能优化的关键概念 | Understand key concepts of mobile image performance optimization

## 详细内容 | Detailed Content

### 1. Image组件基础 | Image Component Fundamentals (1小时 | 1 hour)

- **Image组件的核心概念 | Core Image Component Concept**

  **概念定义 | Concept Definition:**
  Image是React Native中用于显示图片的核心组件。与Web的`<img>`标签不同,Image组件在移动端需要明确的尺寸设置,并提供了特殊的source属性来处理本地和远程图片资源。

  Image is the core component for displaying images in React Native. Unlike the web's `<img>` tag, the Image component on mobile requires explicit size settings and provides a special source prop to handle both local and remote image resources.

  **核心特征 | Key Characteristics:**
  - Image组件必须设置width和height样式,否则不会显示 | Image components must have width and height styles set, or they won't display
  - source属性的格式根据图片来源(本地/远程)而不同 | The source prop format differs based on image origin (local/remote)
  - 提供resizeMode属性控制图片如何适配容器尺寸 | Provides resizeMode prop to control how images fit container dimensions
  - 支持加载状态事件监听(onLoadStart, onLoad, onError) | Supports loading state event listeners (onLoadStart, onLoad, onError)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中,Image组件如果不设置宽高会显示吗? | In React Native, will an Image component display if width and height are not set?
     **答案 | Answer:** 否 | No - Image组件必须有明确的尺寸才能渲染 | Image components must have explicit dimensions to render
  2. Image组件的source属性可以直接传入字符串路径吗? | Can the Image component's source prop directly accept a string path?
     **答案 | Answer:** 否 | No - 必须使用对象格式,如{uri: 'path'}或require() | Must use object format like {uri: 'path'} or require()
  3. Web的`<img>`标签和React Native的Image组件在默认行为上最大的区别是什么? | What's the biggest difference in default behavior between web's `<img>` tag and React Native's Image component?
     **答案 | Answer:** Web的img会根据图片原始尺寸自动调整,而RN的Image必须手动设置尺寸 | Web img auto-adjusts based on original image size, while RN Image requires manual size setting
  4. Image组件可以不设置source属性吗? | Can the Image component work without a source prop?
     **答案 | Answer:** 否 | No - source是必需的属性,否则无内容可显示 | source is a required prop, otherwise there's no content to display

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Image, StyleSheet } from 'react-native';

  // ❌ 错误示例 - 缺少尺寸,不会显示 | Wrong example - missing dimensions, won't display
  const WrongImage = () => (
    <Image source={{ uri: 'https://example.com/photo.jpg' }} />
  );

  // ✅ 正确示例 - 明确设置尺寸 | Correct example - explicit dimensions
  const CorrectImage = () => (
    <Image
      source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
      style={{ width: 100, height: 100 }} // 必须设置 | Must set
    />
  );

  // 基础Image组件使用 | Basic Image component usage
  const BasicImageExample = () => (
    <View style={styles.container}>
      {/* 远程图片 - 使用uri | Remote image - using uri */}
      <Image
        source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
        style={styles.remoteImage}
      />

      {/* 本地图片 - 使用require | Local image - using require */}
      <Image
        source={require('./assets/local-image.png')}
        style={styles.localImage}
      />
    </View>
  );

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      alignItems: 'center',
      padding: 20,
    },
    remoteImage: {
      width: 150,
      height: 150,
      marginBottom: 20,
    },
    localImage: {
      width: 200,
      height: 150,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果Image组件只设置width不设置height会发生什么? | What happens if Image component only has width but not height?
    **答案 | Answer:** 图片不会显示,因为缺少完整的尺寸信息 | Image won't display due to missing complete dimension info
  - 如何判断一个Image是加载远程图片还是本地图片? | How to tell if an Image is loading a remote or local image?
    **答案 | Answer:** 查看source属性 - {uri: ...}是远程,require()是本地 | Check source prop - {uri: ...} is remote, require() is local

  **常见误区检查 | Common Misconception Checks:**
  - Web开发者常认为Image会自动根据图片尺寸调整,这在React Native中正确吗? | Web developers often think Image auto-adjusts based on image size, is this correct in React Native?
    **答案 | Answer:** 不正确 | Incorrect - RN需要明确设置样式尺寸,不会自动适配 | RN requires explicit style dimensions, won't auto-fit
  - 可以用字符串路径直接给source赋值吗? | Can you assign a string path directly to source?
    **答案 | Answer:** 不可以 | No - 必须使用对象{uri: path}或require()函数 | Must use object {uri: path} or require() function

- **本地图片 vs 远程图片 | Local Images vs Remote Images**

  **概念定义 | Concept Definition:**
  React Native区分两种图片加载方式:本地图片(打包在应用中)使用require()函数,远程图片(从网络加载)使用source={{uri: 'URL'}}格式。这两种方式在性能、缓存和打包体积上有显著差异。

  React Native distinguishes two image loading methods: local images (bundled in the app) use the require() function, while remote images (loaded from network) use the source={{uri: 'URL'}} format. These methods have significant differences in performance, caching, and bundle size.

  **核心特征 | Key Characteristics:**
  - 本地图片在编译时就确定路径,支持不同分辨率(@2x, @3x) | Local images have paths determined at compile time, support different resolutions (@2x, @3x)
  - 远程图片需要网络请求,支持自动缓存机制 | Remote images require network requests, support automatic caching mechanisms
  - 本地图片增加应用体积,远程图片依赖网络连接 | Local images increase app size, remote images depend on network connection
  - require()路径必须是静态字符串,不能是变量 | require() path must be a static string, cannot be a variable

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 本地图片的路径可以用变量动态拼接吗? | Can local image paths be dynamically constructed with variables?
     **答案 | Answer:** 否 | No - require()必须使用静态字符串路径 | require() must use static string paths
  2. 远程图片会被React Native自动缓存吗? | Are remote images automatically cached by React Native?
     **答案 | Answer:** 是(仅iOS),Android需要额外配置 | Yes (iOS only), Android needs additional configuration
  3. 哪种图片加载方式会增加应用安装包大小? | Which image loading method increases app bundle size?
     **答案 | Answer:** 本地图片 | Local images - 因为它们被打包进应用 | because they're bundled into the app
  4. 如何为本地图片提供多种分辨率版本? | How to provide multiple resolution versions for local images?
     **答案 | Answer:** 使用@2x, @3x命名约定(如image@2x.png) | Use @2x, @3x naming convention (like image@2x.png)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Image, StyleSheet } from 'react-native';

  const ImageTypesExample = () => {
    // ❌ 错误 - require不能使用变量 | Wrong - require can't use variables
    const imageName = 'logo';
    // const wrongLocal = require(`./assets/${imageName}.png`); // 编译错误 | Compile error

    // ✅ 正确 - 静态路径 | Correct - static path
    const correctLocal = require('./assets/logo.png');

    return (
      <View style={styles.container}>
        {/* 本地图片 - 支持多分辨率 | Local image - supports multi-resolution */}
        {/* 如果有logo@2x.png, logo@3x.png会自动选择 | Automatically selects logo@2x.png, logo@3x.png if available */}
        <Image
          source={require('./assets/logo.png')}
          style={styles.image}
        />

        {/* 远程图片 - 需要网络 | Remote image - requires network */}
        <Image
          source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
          style={styles.image}
        />

        {/* 远程图片 - 指定headers(如认证token) | Remote image - with headers (like auth token) */}
        <Image
          source={{
            uri: 'https://api.example.com/user/avatar',
            headers: {
              Authorization: 'Bearer token123'
            }
          }}
          style={styles.image}
        />

        {/* Base64图片 - 数据URI格式 | Base64 image - data URI format */}
        <Image
          source={{ uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUg...' }}
          style={styles.image}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    image: {
      width: 100,
      height: 100,
      marginBottom: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果要显示用户上传的头像,应该用本地还是远程图片? | Should you use local or remote images for user-uploaded avatars?
    **答案 | Answer:** 远程图片 | Remote images - 用户内容无法预先打包 | User content cannot be pre-bundled
  - 应用logo应该用哪种方式加载? | Which method should be used for app logo?
    **答案 | Answer:** 本地图片 | Local images - 保证可用性和加载速度 | Ensures availability and loading speed

  **常见误区检查 | Common Misconception Checks:**
  - 所有图片都可以用远程加载以减少应用体积吗? | Can all images be loaded remotely to reduce app size?
    **答案 | Answer:** 不建议 | Not recommended - 关键UI元素应本地化以保证用户体验 | Critical UI elements should be localized to ensure user experience

### 2. resizeMode属性详解 | resizeMode Property Deep Dive (1小时 | 1 hour)

- **resizeMode的概念与作用 | resizeMode Concept and Purpose**

  **概念定义 | Concept Definition:**
  resizeMode决定了当图片尺寸与Image组件容器尺寸不匹配时,图片如何调整大小和位置。这类似于CSS的object-fit属性,但有移动端特定的值和行为。

  resizeMode determines how an image adjusts its size and position when its dimensions don't match the Image component container. This is similar to CSS's object-fit property, but with mobile-specific values and behaviors.

  **核心特征 | Key Characteristics:**
  - 有5个可选值:cover(默认), contain, stretch, center, repeat | Has 5 options: cover (default), contain, stretch, center, repeat
  - cover: 保持宽高比填充容器,可能裁剪 | cover: maintains aspect ratio filling container, may crop
  - contain: 完整显示图片,可能留白 | contain: shows full image, may leave empty space
  - stretch: 拉伸图片填满容器,不保持宽高比 | stretch: stretches image to fill container, ignoring aspect ratio
  - center: 图片居中显示原始尺寸,不缩放 | center: displays image at original size centered, no scaling

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. resizeMode的默认值是什么? | What's the default value of resizeMode?
     **答案 | Answer:** cover - 填充容器并保持宽高比 | cover - fills container maintaining aspect ratio
  2. 哪个resizeMode会破坏图片的宽高比? | Which resizeMode breaks the image aspect ratio?
     **答案 | Answer:** stretch - 强制图片适配容器尺寸 | stretch - forces image to fit container dimensions
  3. 如果想确保图片完整显示不被裁剪,应该用哪个模式? | Which mode ensures the entire image is visible without cropping?
     **答案 | Answer:** contain - 完整显示图片,四周可能留白 | contain - shows full image, may have empty space around
  4. center模式会缩放图片吗? | Does center mode scale the image?
     **答案 | Answer:** 否 | No - 以原始尺寸居中显示 | Displays at original size centered
  5. repeat模式在iOS和Android上行为一致吗? | Does repeat mode behave consistently on iOS and Android?
     **答案 | Answer:** 否 | No - repeat仅在iOS上支持 | repeat is only supported on iOS

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Image, Text, StyleSheet } from 'react-native';

  const ResizeModeDemo = () => {
    const imageUri = 'https://reactnative.dev/img/tiny_logo.png';

    return (
      <View style={styles.container}>
        {/* cover - 默认值,填充容器 | cover - default, fills container */}
        <View style={styles.imageContainer}>
          <Text style={styles.label}>cover (默认 | default)</Text>
          <Image
            source={{ uri: imageUri }}
            style={styles.image}
            resizeMode="cover" // 保持宽高比,可能裁剪 | Maintains aspect ratio, may crop
          />
        </View>

        {/* contain - 完整显示 | contain - full display */}
        <View style={styles.imageContainer}>
          <Text style={styles.label}>contain</Text>
          <Image
            source={{ uri: imageUri }}
            style={styles.image}
            resizeMode="contain" // 完整显示图片,可能留白 | Shows full image, may leave space
          />
        </View>

        {/* stretch - 拉伸填充 | stretch - stretch to fill */}
        <View style={styles.imageContainer}>
          <Text style={styles.label}>stretch</Text>
          <Image
            source={{ uri: imageUri }}
            style={styles.image}
            resizeMode="stretch" // 不保持宽高比 | Doesn't maintain aspect ratio
          />
        </View>

        {/* center - 居中原始尺寸 | center - centered original size */}
        <View style={styles.imageContainer}>
          <Text style={styles.label}>center</Text>
          <Image
            source={{ uri: imageUri }}
            style={styles.image}
            resizeMode="center" // 不缩放,居中显示 | No scaling, centered
          />
        </View>

        {/* repeat - 平铺(仅iOS) | repeat - tiled (iOS only) */}
        <View style={styles.imageContainer}>
          <Text style={styles.label}>repeat (仅iOS | iOS only)</Text>
          <Image
            source={{ uri: imageUri }}
            style={styles.image}
            resizeMode="repeat" // 平铺图片 | Tiles the image
          />
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 10,
    },
    imageContainer: {
      marginBottom: 20,
      backgroundColor: '#f0f0f0', // 背景色帮助看清留白 | Background to visualize empty space
    },
    image: {
      width: 200,
      height: 150,
      backgroundColor: '#e0e0e0',
    },
    label: {
      fontSize: 14,
      fontWeight: 'bold',
      marginBottom: 5,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果一个宽的横向图片放在正方形容器中,cover模式会裁剪哪部分? | If a wide horizontal image is in a square container, which part will cover mode crop?
    **答案 | Answer:** 上下两端 | Top and bottom edges - 图片会缩放到容器宽度,超出部分裁剪 | Image scales to container width, excess is cropped
  - 用户头像应该用哪个resizeMode? | Which resizeMode should be used for user avatars?
    **答案 | Answer:** cover - 确保填充整个头像框且保持人脸比例 | cover - ensures filling entire avatar frame while maintaining face proportions

  **常见误区检查 | Common Misconception Checks:**
  - contain模式会拉伸图片填满容器吗? | Does contain mode stretch the image to fill the container?
    **答案 | Answer:** 否 | No - 保持宽高比完整显示,不会拉伸变形 | Maintains aspect ratio showing full image, no distortion
  - center和contain有什么区别? | What's the difference between center and contain?
    **答案 | Answer:** contain会缩放图片适配容器,center不缩放保持原始尺寸 | contain scales to fit container, center keeps original size without scaling

### 3. 样式化Image组件 | Styling Image Components (45分钟 | 45 minutes)

- **圆形头像与边框样式 | Circular Avatars and Border Styles**

  **概念定义 | Concept Definition:**
  通过borderRadius和其他样式属性,可以将Image组件转换为圆形头像、圆角卡片等各种形状。这是移动应用中最常见的图片样式需求之一。

  By using borderRadius and other style properties, Image components can be transformed into circular avatars, rounded cards, and various shapes. This is one of the most common image styling requirements in mobile apps.

  **核心特征 | Key Characteristics:**
  - borderRadius设置为宽高一半可创建完美圆形 | borderRadius set to half of width/height creates perfect circle
  - 圆形Image必须确保width和height相等 | Circular Image must ensure width and height are equal
  - 可组合borderWidth和borderColor创建边框效果 | Can combine borderWidth and borderColor for border effects
  - 支持overflow: 'hidden'裁剪超出圆角的内容 | Supports overflow: 'hidden' to clip content beyond rounded corners

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 创建圆形头像,borderRadius应该设置为多少? | To create a circular avatar, what should borderRadius be set to?
     **答案 | Answer:** 宽度或高度的一半 | Half of width or height (例如width: 100则borderRadius: 50 | e.g., if width: 100 then borderRadius: 50)
  2. 如果Image宽100高150,设置borderRadius: 50会是什么形状? | If Image is width 100 height 150, with borderRadius: 50, what shape results?
     **答案 | Answer:** 椭圆形 | Oval - 因为宽高不相等 | because width and height are unequal
  3. borderRadius可以超过宽高的一半吗? | Can borderRadius exceed half of width/height?
     **答案 | Answer:** 可以,但效果等同于宽高的一半(完美圆形) | Yes, but effect is same as half (perfect circle)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { View, Image, StyleSheet } from 'react-native';

  const AvatarStylesExample = () => {
    const avatarUri = 'https://randomuser.me/api/portraits/women/44.jpg';

    return (
      <View style={styles.container}>
        {/* 圆形头像 - 基础版 | Circular avatar - basic */}
        <Image
          source={{ uri: avatarUri }}
          style={styles.circularAvatar}
        />

        {/* 圆形头像 - 带边框 | Circular avatar - with border */}
        <Image
          source={{ uri: avatarUri }}
          style={styles.borderedAvatar}
        />

        {/* 圆角矩形 | Rounded rectangle */}
        <Image
          source={{ uri: avatarUri }}
          style={styles.roundedRectangle}
        />

        {/* 带阴影的头像 (iOS效果更好) | Avatar with shadow (better on iOS) */}
        <Image
          source={{ uri: avatarUri }}
          style={styles.shadowedAvatar}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      flexDirection: 'row',
      flexWrap: 'wrap',
      justifyContent: 'space-around',
      padding: 20,
    },
    circularAvatar: {
      width: 100,
      height: 100,
      borderRadius: 50, // 宽高的一半 | Half of width/height
    },
    borderedAvatar: {
      width: 100,
      height: 100,
      borderRadius: 50,
      borderWidth: 3, // 边框宽度 | Border width
      borderColor: '#4A90E2', // 边框颜色 | Border color
    },
    roundedRectangle: {
      width: 120,
      height: 80,
      borderRadius: 15, // 圆角矩形 | Rounded corners
    },
    shadowedAvatar: {
      width: 100,
      height: 100,
      borderRadius: 50,
      // iOS阴影 | iOS shadow
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.3,
      shadowRadius: 4,
      // Android阴影 | Android shadow
      elevation: 5,
    },
  });

  // 可复用的Avatar组件 | Reusable Avatar component
  const Avatar = ({ uri, size = 60, borderColor = '#fff', borderWidth = 2 }) => (
    <Image
      source={{ uri }}
      style={{
        width: size,
        height: size,
        borderRadius: size / 2, // 动态计算 | Dynamically calculated
        borderWidth,
        borderColor,
      }}
    />
  );

  // 使用示例 | Usage example
  const AvatarUsage = () => (
    <View style={{ flexDirection: 'row', gap: 10 }}>
      <Avatar uri="https://randomuser.me/api/portraits/men/32.jpg" size={50} />
      <Avatar uri="https://randomuser.me/api/portraits/women/65.jpg" size={80} borderColor="#E91E63" />
    </View>
  );
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果要创建直径80的圆形头像,width、height和borderRadius应该是多少? | For an 80-diameter circular avatar, what should width, height, and borderRadius be?
    **答案 | Answer:** width: 80, height: 80, borderRadius: 40
  - 为什么阴影效果在Android上用elevation而不是shadow属性? | Why use elevation instead of shadow properties for shadow effects on Android?
    **答案 | Answer:** Android不支持shadow系列属性,必须用elevation | Android doesn't support shadow properties, must use elevation

  **常见误区检查 | Common Misconception Checks:**
  - 可以只设置borderRadius而不设置相同的width和height来创建圆形吗? | Can you create a circle by only setting borderRadius without equal width and height?
    **答案 | Answer:** 否 | No - 宽高不等会产生椭圆 | Unequal width/height produces an oval

### 4. 远程图片加载状态处理 | Remote Image Loading State Handling (1小时 | 1 hour)

- **图片加载生命周期 | Image Loading Lifecycle**

  **概念定义 | Concept Definition:**
  远程图片加载是异步过程,包含加载开始、加载中、加载完成和加载失败四个阶段。React Native的Image组件提供了onLoadStart、onLoad、onError等事件来追踪这些状态。

  Remote image loading is an asynchronous process with four stages: load start, loading, load complete, and load error. React Native's Image component provides events like onLoadStart, onLoad, onError to track these states.

  **核心特征 | Key Characteristics:**
  - onLoadStart: 图片开始加载时触发 | onLoadStart: triggered when image loading begins
  - onLoad: 图片成功加载完成时触发 | onLoad: triggered when image loads successfully
  - onError: 图片加载失败时触发 | onError: triggered when image loading fails
  - 可以用ActivityIndicator显示加载状态 | Can use ActivityIndicator to show loading state
  - 应该提供fallback图片处理加载失败 | Should provide fallback image for loading failures

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 本地图片(require)需要处理加载状态吗? | Do local images (require) need loading state handling?
     **答案 | Answer:** 否 | No - 本地图片同步加载,立即可用 | Local images load synchronously, immediately available
  2. onLoad事件会在哪个阶段触发? | At which stage does the onLoad event trigger?
     **答案 | Answer:** 图片成功下载并解码完成后 | After image is successfully downloaded and decoded
  3. 如果网络断开,远程图片会触发什么事件? | What event triggers if network is disconnected for remote images?
     **答案 | Answer:** onError - 网络错误导致加载失败 | onError - network error causes loading failure
  4. 用户体验角度,为什么需要显示加载状态? | From UX perspective, why show loading states?
     **答案 | Answer:** 避免空白闪烁,告知用户内容正在加载 | Avoids blank flashing, informs user content is loading

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { useState } from 'react';
  import { View, Image, ActivityIndicator, Text, StyleSheet } from 'react-native';

  // 带加载状态的Image组件 | Image component with loading state
  const LoadingImage = ({ uri, style }) => {
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(false);

    return (
      <View style={[style, styles.imageContainer]}>
        <Image
          source={{ uri }}
          style={StyleSheet.absoluteFill} // 填充整个父容器 | Fills entire parent container
          onLoadStart={() => {
            setLoading(true);
            setError(false);
          }}
          onLoad={() => {
            setLoading(false);
          }}
          onError={(e) => {
            setLoading(false);
            setError(true);
            console.log('图片加载失败 | Image load failed:', e.nativeEvent.error);
          }}
        />

        {/* 加载中显示spinner | Show spinner while loading */}
        {loading && (
          <View style={styles.loadingOverlay}>
            <ActivityIndicator size="large" color="#0000ff" />
          </View>
        )}

        {/* 加载失败显示错误提示 | Show error message on failure */}
        {error && (
          <View style={styles.errorOverlay}>
            <Text style={styles.errorText}>加载失败 | Load Failed</Text>
          </View>
        )}
      </View>
    );
  };

  // 带fallback的智能Image | Smart Image with fallback
  const SmartImage = ({ uri, fallbackUri, style }) => {
    const [imageUri, setImageUri] = useState(uri);
    const [isLoading, setIsLoading] = useState(true);

    return (
      <View style={style}>
        <Image
          source={{ uri: imageUri }}
          style={StyleSheet.absoluteFill}
          onLoadStart={() => setIsLoading(true)}
          onLoadEnd={() => setIsLoading(false)}
          onError={() => {
            // 加载失败时使用fallback | Use fallback on failure
            if (imageUri !== fallbackUri) {
              setImageUri(fallbackUri);
            }
          }}
        />
        {isLoading && (
          <ActivityIndicator
            style={styles.loader}
            size="small"
            color="#999"
          />
        )}
      </View>
    );
  };

  // 使用示例 | Usage example
  const ImageLoadingExample = () => (
    <View style={styles.container}>
      <LoadingImage
        uri="https://reactnative.dev/img/tiny_logo.png"
        style={styles.image}
      />

      <SmartImage
        uri="https://invalid-url.com/image.jpg" // 会失败 | Will fail
        fallbackUri="https://via.placeholder.com/150" // 降级到此 | Fallback to this
        style={styles.image}
      />
    </View>
  );

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    imageContainer: {
      position: 'relative',
      justifyContent: 'center',
      alignItems: 'center',
    },
    image: {
      width: 200,
      height: 200,
      marginBottom: 20,
    },
    loadingOverlay: {
      ...StyleSheet.absoluteFillObject, // 覆盖整个容器 | Covers entire container
      backgroundColor: 'rgba(255, 255, 255, 0.8)',
      justifyContent: 'center',
      alignItems: 'center',
    },
    errorOverlay: {
      ...StyleSheet.absoluteFillObject,
      backgroundColor: '#f0f0f0',
      justifyContent: 'center',
      alignItems: 'center',
    },
    errorText: {
      color: '#ff0000',
      fontSize: 14,
    },
    loader: {
      position: 'absolute',
      alignSelf: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么需要onLoadStart而不是只用onLoad? | Why need onLoadStart instead of just onLoad?
    **答案 | Answer:** onLoadStart可以立即显示loading UI,提升用户体验 | onLoadStart can immediately show loading UI, improving UX
  - StyleSheet.absoluteFill的作用是什么? | What's the purpose of StyleSheet.absoluteFill?
    **答案 | Answer:** 等同于{position: 'absolute', left: 0, right: 0, top: 0, bottom: 0},填充父容器 | Equivalent to {position: 'absolute', left: 0, right: 0, top: 0, bottom: 0}, fills parent

  **常见误区检查 | Common Misconception Checks:**
  - 是否所有Image组件都需要loading状态? | Do all Image components need loading states?
    **答案 | Answer:** 否 | No - 仅远程图片需要,本地图片同步加载 | Only remote images need it, local images load synchronously
  - onLoad触发后图片一定显示了吗? | Does onLoad trigger guarantee the image is displayed?
    **答案 | Answer:** 是 | Yes - 图片已解码并渲染完成 | Image is decoded and rendered

### 5. 图片性能优化 | Image Performance Optimization (45分钟 | 45 minutes)

- **图片缓存与内存管理 | Image Caching and Memory Management**

  **概念定义 | Concept Definition:**
  移动端图片加载需要特别注意性能和内存消耗。React Native在iOS上自动缓存远程图片,但Android需要手动配置。大图片会占用大量内存,应该进行优化处理。

  Mobile image loading requires special attention to performance and memory consumption. React Native automatically caches remote images on iOS, but Android requires manual configuration. Large images consume significant memory and should be optimized.

  **核心特征 | Key Characteristics:**
  - iOS自动缓存远程图片,Android需要额外配置 | iOS auto-caches remote images, Android needs extra config
  - 应避免同时加载过多大尺寸图片 | Should avoid loading too many large images simultaneously
  - 使用适当的图片尺寸,避免加载远超显示尺寸的图片 | Use appropriate image sizes, avoid loading images much larger than display size
  - 考虑使用第三方库(如react-native-fast-image)提升性能 | Consider third-party libraries (like react-native-fast-image) for better performance

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在FlatList中显示100张图片,应该注意什么? | What should you watch for when displaying 100 images in FlatList?
     **答案 | Answer:** 内存管理 | Memory management - 使用virtualizedList只渲染可见项 | Use virtualizedList to only render visible items
  2. 如果显示区域是100x100,加载1000x1000的图片有什么问题? | What's the problem with loading a 1000x1000 image for a 100x100 display area?
     **答案 | Answer:** 浪费内存和带宽 | Wastes memory and bandwidth - 应使用适当尺寸的图片 | Should use appropriately sized images
  3. Android上远程图片不缓存的后果? | What's the consequence of no caching for remote images on Android?
     **答案 | Answer:** 每次都重新下载,消耗流量和时间 | Re-downloads every time, consuming data and time

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { Image, StyleSheet, Platform } from 'react-native';

  // ❌ 性能问题示例 - 加载过大图片 | Performance issue - loading oversized image
  const InefficientImage = () => (
    <Image
      source={{ uri: 'https://example.com/huge-4k-image.jpg' }} // 4K图片 | 4K image
      style={{ width: 100, height: 100 }} // 但只显示100x100 | But only displays 100x100
    />
  );

  // ✅ 优化版 - 请求适当尺寸 | Optimized - request appropriate size
  const EfficientImage = () => (
    <Image
      source={{ uri: 'https://example.com/thumbnail-200x200.jpg' }} // 缩略图 | Thumbnail
      style={{ width: 100, height: 100 }}
    />
  );

  // 图片预加载技术 | Image prefetching technique
  const prefetchImages = async (imageUrls) => {
    try {
      // 预加载图片到缓存 | Prefetch images to cache
      const prefetchTasks = imageUrls.map(url =>
        Image.prefetch(url)
      );
      await Promise.all(prefetchTasks);
      console.log('图片预加载完成 | Images prefetched');
    } catch (error) {
      console.log('预加载失败 | Prefetch failed:', error);
    }
  };

  // 使用示例 | Usage example
  // prefetchImages([
  //   'https://example.com/image1.jpg',
  //   'https://example.com/image2.jpg',
  // ]);

  // 查询缓存中的图片 | Query cached images
  const checkImageCache = async (url) => {
    const isCached = await Image.queryCache([url]);
    console.log(`图片缓存状态 | Cache status:`, isCached);
    // 返回 {url: 'disk'} 或 {url: 'memory'} | Returns {url: 'disk'} or {url: 'memory'}
  };

  // 内存优化的大图列表 | Memory-optimized large image list
  import { FlatList } from 'react-native';

  const OptimizedImageList = ({ imageUrls }) => (
    <FlatList
      data={imageUrls}
      renderItem={({ item }) => (
        <Image
          source={{ uri: item }}
          style={styles.listImage}
        />
      )}
      keyExtractor={(item, index) => index.toString()}
      // 性能优化配置 | Performance optimization config
      removeClippedSubviews={true} // 移除屏幕外的视图 | Remove off-screen views
      maxToRenderPerBatch={10} // 每批最多渲染10个 | Max 10 items per batch
      windowSize={5} // 渲染窗口大小 | Render window size
    />
  );

  const styles = StyleSheet.create({
    listImage: {
      width: '100%',
      height: 200,
      marginBottom: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - Image.prefetch()的作用是什么? | What does Image.prefetch() do?
    **答案 | Answer:** 提前下载图片到缓存,加速后续显示 | Pre-downloads images to cache, speeding up subsequent display
  - FlatList的removeClippedSubviews如何优化性能? | How does FlatList's removeClippedSubviews optimize performance?
    **答案 | Answer:** 移除屏幕外的原生视图,减少内存占用 | Removes off-screen native views, reducing memory usage

  **常见误区检查 | Common Misconception Checks:**
  - 所有图片都会自动缓存吗? | Are all images automatically cached?
    **答案 | Answer:** 否 | No - 仅远程图片且iOS默认缓存,Android需配置 | Only remote images and iOS caches by default, Android needs configuration

### 6. 实战技巧与最佳实践 | Practical Tips and Best Practices (30分钟 | 30 minutes)

- **常见图片使用场景 | Common Image Usage Scenarios**

  **实用技巧总结 | Practical Tips Summary:**
  - 使用本地图片作为placeholder提升体验 | Use local images as placeholders for better UX
  - 为不同屏幕密度提供@2x, @3x版本 | Provide @2x, @3x versions for different screen densities
  - 圆形头像使用cover模式确保填充 | Use cover mode for circular avatars to ensure filling
  - 大图列表使用FlatList而非ScrollView | Use FlatList instead of ScrollView for large image lists
  - 考虑使用WebP格式减少文件大小 | Consider using WebP format to reduce file size

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么推荐用本地图片作为placeholder? | Why use local images as placeholders?
     **答案 | Answer:** 立即显示,无需等待网络加载 | Displays immediately, no network wait
  2. @2x和@3x图片如何选择使用? | How are @2x and @3x images selected?
     **答案 | Answer:** React Native根据设备像素比(PixelRatio)自动选择 | React Native auto-selects based on device pixel ratio
  3. ScrollView和FlatList在图片列表场景的主要区别? | Main difference between ScrollView and FlatList for image lists?
     **答案 | Answer:** FlatList虚拟化渲染,ScrollView一次性渲染全部 | FlatList virtualizes rendering, ScrollView renders all at once

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import { Image, View, StyleSheet } from 'react-native';

  // 最佳实践: 带placeholder的远程图片 | Best practice: Remote image with placeholder
  const ImageWithPlaceholder = ({ uri }) => {
    const [imageLoaded, setImageLoaded] = useState(false);

    return (
      <View style={styles.container}>
        {/* Placeholder - 本地图片立即显示 | Placeholder - local image shows immediately */}
        {!imageLoaded && (
          <Image
            source={require('./assets/placeholder.png')}
            style={StyleSheet.absoluteFill}
          />
        )}

        {/* 实际图片 | Actual image */}
        <Image
          source={{ uri }}
          style={StyleSheet.absoluteFill}
          onLoad={() => setImageLoaded(true)}
        />
      </View>
    );
  };

  // 最佳实践: 多密度图片支持 | Best practice: Multi-density image support
  // 文件结构 | File structure:
  // - assets/logo.png (1x)
  // - assets/logo@2x.png (2x)
  // - assets/logo@3x.png (3x)
  const MultiDensityImage = () => (
    <Image
      source={require('./assets/logo.png')} // 自动选择合适版本 | Auto-selects appropriate version
      style={{ width: 100, height: 100 }}
    />
  );

  // 最佳实践: 优雅的错误处理 | Best practice: Graceful error handling
  const RobustImage = ({ uri, fallback = require('./assets/default.png') }) => {
    const [source, setSource] = useState({ uri });

    return (
      <Image
        source={source}
        style={styles.image}
        onError={() => {
          setSource(fallback); // 失败时显示默认图片 | Show default on failure
        }}
      />
    );
  };

  const styles = StyleSheet.create({
    container: {
      width: 200,
      height: 200,
    },
    image: {
      width: 150,
      height: 150,
    },
  });
  ```

## 实践项目：个人资料卡片应用 | Practical Project: Profile Card App

### 目标 | Objective
构建一个展示用户个人资料的卡片应用,综合运用Image组件的各种特性:圆形头像、远程图片加载、加载状态处理、图片样式化等核心概念。

Build a profile card app displaying user information, comprehensively applying Image component features: circular avatars, remote image loading, loading state handling, image styling and other core concepts.

### 概念应用检查 | Concept Application Check
在开始项目前,请确认对以下概念的理解 | Before starting the project, please confirm understanding of:

1. Image组件必须设置什么属性才能显示? | What properties must Image components have to display?
   **答案 | Answer:** source(图片源)和style中的width、height | source (image source) and width, height in style
2. 如何创建圆形头像? | How to create circular avatars?
   **答案 | Answer:** 设置相等的width和height,borderRadius为宽高的一半 | Set equal width and height, borderRadius to half of dimensions
3. 远程图片需要处理哪些状态? | What states need handling for remote images?
   **答案 | Answer:** 加载中、加载成功、加载失败 | Loading, load success, load failure

### 步骤 | Steps
1. 创建基础布局:SafeAreaView + ScrollView容器 | Create basic layout: SafeAreaView + ScrollView container
2. 实现CircularAvatar组件:圆形头像、边框、加载状态 | Implement CircularAvatar component: circular avatar, border, loading state
3. 添加背景封面图片(cover模式) | Add background cover image (cover mode)
4. 显示用户信息:姓名、职位、简介(Text组件) | Display user info: name, title, bio (Text components)
5. 添加技能标签的横向图片列表(ScrollView horizontal) | Add horizontal skill tag image list (ScrollView horizontal)
6. 实现加载失败时的fallback机制 | Implement fallback mechanism for load failures

### 示例代码 | Example Code
```jsx
/**
 * 个人资料卡片应用 | Profile Card App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Image组件基础使用(本地/远程图片) | Image component basics (local/remote images)
 * - resizeMode属性控制图片显示 | resizeMode property for image display control
 * - 圆形头像样式实现 | Circular avatar styling
 * - 远程图片加载状态管理 | Remote image loading state management
 * - 图片性能优化实践 | Image performance optimization practices
 */

import { useState, useEffect } from 'react';
import {
  View,
  Text,
  Image,
  ScrollView,
  SafeAreaView,
  ActivityIndicator,
  StyleSheet,
  Dimensions,
} from 'react-native';

const { width: SCREEN_WIDTH } = Dimensions.get('window');

// 可复用的圆形头像组件 | Reusable circular avatar component
const CircularAvatar = ({ uri, size = 120, borderColor = '#fff', borderWidth = 4 }) => {
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(false);

  return (
    <View
      style={[
        styles.avatarContainer,
        {
          width: size,
          height: size,
          borderRadius: size / 2,
          borderWidth,
          borderColor,
        },
      ]}
    >
      <Image
        source={{ uri }}
        style={{
          width: size,
          height: size,
          borderRadius: size / 2,
        }}
        resizeMode="cover" // 确保填充整个圆形区域 | Ensures filling entire circular area
        onLoadStart={() => setLoading(true)}
        onLoad={() => setLoading(false)}
        onError={() => {
          setLoading(false);
          setError(true);
        }}
      />

      {/* 加载中指示器 | Loading indicator */}
      {loading && (
        <View style={styles.avatarLoading}>
          <ActivityIndicator size="large" color="#fff" />
        </View>
      )}

      {/* 加载失败显示默认头像 | Show default avatar on failure */}
      {error && (
        <View style={styles.avatarError}>
          <Text style={styles.avatarErrorText}>?</Text>
        </View>
      )}
    </View>
  );
};

// 封面图片组件 | Cover image component
const CoverImage = ({ uri }) => {
  const [loading, setLoading] = useState(true);

  return (
    <View style={styles.coverContainer}>
      <Image
        source={{ uri }}
        style={styles.coverImage}
        resizeMode="cover" // cover模式填充容器 | cover mode fills container
        onLoadStart={() => setLoading(true)}
        onLoadEnd={() => setLoading(false)}
      />
      {loading && (
        <View style={styles.coverLoading}>
          <ActivityIndicator size="large" color="#fff" />
        </View>
      )}
    </View>
  );
};

// 技能图标组件 | Skill icon component
const SkillIcon = ({ icon, name }) => (
  <View style={styles.skillItem}>
    <Image
      source={{ uri: icon }}
      style={styles.skillIcon}
      resizeMode="contain" // contain模式完整显示图标 | contain mode shows full icon
    />
    <Text style={styles.skillName}>{name}</Text>
  </View>
);

// 主应用组件 | Main app component
const ProfileCardApp = () => {
  // 模拟用户数据 | Mock user data
  const userData = {
    name: '张小明 | Zhang Xiaoming',
    title: 'React Native开发者 | React Native Developer',
    bio: '热爱移动开发,专注于打造优质用户体验 | Passionate about mobile development, focused on creating excellent user experiences',
    avatar: 'https://randomuser.me/api/portraits/men/75.jpg',
    cover: 'https://images.unsplash.com/photo-1579546929518-9e396f3cc809?w=800',
    skills: [
      { icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg', name: 'React' },
      { icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/javascript/javascript-original.svg', name: 'JavaScript' },
      { icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/typescript/typescript-original.svg', name: 'TypeScript' },
      { icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg', name: 'Node.js' },
    ],
  };

  // 预加载图片优化性能 | Prefetch images for performance
  useEffect(() => {
    const imagesToPrefetch = [
      userData.avatar,
      userData.cover,
      ...userData.skills.map(skill => skill.icon),
    ];

    imagesToPrefetch.forEach(url => {
      Image.prefetch(url);
    });
  }, []);

  return (
    <SafeAreaView style={styles.container}>
      <ScrollView showsVerticalScrollIndicator={false}>
        {/* 封面图片 | Cover image */}
        <CoverImage uri={userData.cover} />

        {/* 头像位置在封面图上 | Avatar positioned on cover */}
        <View style={styles.avatarWrapper}>
          <CircularAvatar
            uri={userData.avatar}
            size={120}
            borderColor="#fff"
            borderWidth={5}
          />
        </View>

        {/* 用户信息卡片 | User info card */}
        <View style={styles.infoCard}>
          <Text style={styles.name}>{userData.name}</Text>
          <Text style={styles.title}>{userData.title}</Text>
          <Text style={styles.bio}>{userData.bio}</Text>

          {/* 技能标签 - 横向滚动 | Skills - horizontal scroll */}
          <Text style={styles.sectionTitle}>技能 | Skills</Text>
          <ScrollView
            horizontal
            showsHorizontalScrollIndicator={false}
            contentContainerStyle={styles.skillsContainer}
          >
            {userData.skills.map((skill, index) => (
              <SkillIcon key={index} icon={skill.icon} name={skill.name} />
            ))}
          </ScrollView>
        </View>

        {/* 图片展示区 - 演示不同resizeMode | Image showcase - demonstrating different resizeModes */}
        <View style={styles.showcaseSection}>
          <Text style={styles.sectionTitle}>resizeMode演示 | resizeMode Demo</Text>

          <View style={styles.showcaseRow}>
            <View style={styles.showcaseItem}>
              <Text style={styles.showcaseLabel}>cover</Text>
              <Image
                source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
                style={styles.showcaseImage}
                resizeMode="cover"
              />
            </View>

            <View style={styles.showcaseItem}>
              <Text style={styles.showcaseLabel}>contain</Text>
              <Image
                source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
                style={styles.showcaseImage}
                resizeMode="contain"
              />
            </View>

            <View style={styles.showcaseItem}>
              <Text style={styles.showcaseLabel}>stretch</Text>
              <Image
                source={{ uri: 'https://reactnative.dev/img/tiny_logo.png' }}
                style={styles.showcaseImage}
                resizeMode="stretch"
              />
            </View>
          </View>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  // 封面样式 | Cover styles
  coverContainer: {
    width: SCREEN_WIDTH,
    height: 200,
    backgroundColor: '#e0e0e0',
  },
  coverImage: {
    width: '100%',
    height: '100%',
  },
  coverLoading: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'rgba(0,0,0,0.3)',
  },
  // 头像样式 | Avatar styles
  avatarWrapper: {
    alignItems: 'center',
    marginTop: -60, // 负margin让头像覆盖在封面上 | Negative margin overlays avatar on cover
  },
  avatarContainer: {
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#fff',
  },
  avatarLoading: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'rgba(0,0,0,0.5)',
    borderRadius: 60,
  },
  avatarError: {
    ...StyleSheet.absoluteFillObject,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#ccc',
    borderRadius: 60,
  },
  avatarErrorText: {
    fontSize: 48,
    color: '#fff',
  },
  // 信息卡片样式 | Info card styles
  infoCard: {
    backgroundColor: '#fff',
    marginHorizontal: 20,
    marginTop: 20,
    padding: 20,
    borderRadius: 15,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 8,
    elevation: 3,
  },
  name: {
    fontSize: 24,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 5,
  },
  title: {
    fontSize: 16,
    color: '#666',
    textAlign: 'center',
    marginBottom: 15,
  },
  bio: {
    fontSize: 14,
    color: '#333',
    textAlign: 'center',
    lineHeight: 20,
    marginBottom: 20,
  },
  sectionTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 15,
  },
  // 技能样式 | Skills styles
  skillsContainer: {
    flexDirection: 'row',
    gap: 15,
  },
  skillItem: {
    alignItems: 'center',
    width: 70,
  },
  skillIcon: {
    width: 50,
    height: 50,
    marginBottom: 5,
  },
  skillName: {
    fontSize: 12,
    color: '#333',
    textAlign: 'center',
  },
  // 展示区样式 | Showcase styles
  showcaseSection: {
    backgroundColor: '#fff',
    margin: 20,
    padding: 20,
    borderRadius: 15,
  },
  showcaseRow: {
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  showcaseItem: {
    flex: 1,
    alignItems: 'center',
    marginHorizontal: 5,
  },
  showcaseLabel: {
    fontSize: 12,
    fontWeight: 'bold',
    marginBottom: 5,
  },
  showcaseImage: {
    width: 80,
    height: 80,
    backgroundColor: '#f0f0f0',
  },
});

export default ProfileCardApp;
```

### 项目完成检查 | Project Completion Check
1. 圆形头像是否正确实现?(相等的width/height,borderRadius为一半) | Is circular avatar correctly implemented? (equal width/height, borderRadius half)
2. 远程图片是否有加载状态指示?(ActivityIndicator) | Do remote images show loading state? (ActivityIndicator)
3. 封面图片是否使用cover模式填充容器? | Does cover image use cover mode to fill container?
4. 技能图标是否使用contain模式完整显示? | Do skill icons use contain mode for full display?
5. 是否处理了图片加载失败的情况? | Is image load failure handled?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Image组件理解强化 | Image Component Understanding Reinforcement**
   - **练习描述 | Exercise Description:** 创建一个对比页面,展示同一张图片在所有5种resizeMode下的显示效果
   - **概念检查 | Concept Check:** 你能解释每种resizeMode的具体行为吗? | Can you explain the specific behavior of each resizeMode?
   - **学习目标 | Learning Objective:** 深刻理解resizeMode对图片显示的影响

2. **加载状态应用练习 | Loading State Application Exercise**
   - **练习描述 | Exercise Description:** 构建一个图片画廊,显示加载进度(可用onProgress事件)
   - **概念检查 | Concept Check:** onProgress和onLoad的触发时机有什么区别? | What's the difference in trigger timing between onProgress and onLoad?
   - **学习目标 | Learning Objective:** 掌握完整的图片加载生命周期管理

3. **性能优化练习 | Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 创建一个包含50+图片的FlatList,对比是否使用prefetch的性能差异
   - **概念检查 | Concept Check:** 为什么FlatList比ScrollView更适合大量图片? | Why is FlatList better than ScrollView for many images?
   - **学习目标 | Learning Objective:** 理解虚拟化列表和图片预加载的性能优势

4. **样式化综合练习 | Comprehensive Styling Exercise**
   - **练习描述 | Exercise Description:** 实现Instagram风格的正方形图片网格(3列,圆角,间距)
   - **概念检查 | Concept Check:** 如何确保图片在不同屏幕尺寸下保持正方形? | How to ensure images remain square across different screen sizes?
   - **学习目标 | Learning Objective:** 结合Flexbox和Image样式创建复杂布局

5. **错误处理创新练习 | Error Handling Innovation Exercise**
   - **练习描述 | Exercise Description:** 创建一个智能Image组件,支持多个fallback URL链(URL1失败尝试URL2...)
   - **概念检查 | Concept Check:** 如何避免无限重试导致的性能问题? | How to avoid performance issues from infinite retries?
   - **学习目标 | Learning Objective:** 设计健壮的错误处理机制

6. **本地图片管理练习 | Local Image Management Exercise**
   - **练习描述 | Exercise Description:** 创建一个图片资源管理系统,用对象映射管理所有本地require图片
   - **概念检查 | Concept Check:** 为什么不能在require()中使用变量? | Why can't variables be used in require()?
   - **学习目标 | Learning Objective:** 理解静态资源打包机制

7. **项目扩展练习 | Project Extension Exercise**
   - **练习描述 | Exercise Description:** 为个人资料卡片添加相册功能,支持横向滚动查看多张照片
   - **概念检查 | Concept Check:** 横向ScrollView和纵向ScrollView如何嵌套使用? | How to nest horizontal and vertical ScrollViews?
   - **学习目标 | Learning Objective:** 提高复杂布局和多Image组件协同能力

## 学习资源 | Learning Resources
- [React Native Image官方文档 | Official Documentation](https://reactnative.dev/docs/image)
- [Image性能优化指南 | Performance Guide](https://reactnative.dev/docs/images)
- [react-native-fast-image库 | Library](https://github.com/DylanVann/react-native-fast-image)
- [Expo Image组件(增强版) | Expo Image (Enhanced)](https://docs.expo.dev/versions/latest/sdk/image/)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Image组件必须设置width和height | Understand Image components must have width and height
- [ ] 掌握本地图片(require)和远程图片({uri})的区别 | Master difference between local (require) and remote ({uri}) images
- [ ] 能正确应用5种resizeMode实现不同效果 | Can correctly apply 5 resizeModes for different effects
- [ ] 会创建圆形头像组件 | Can create circular avatar components
- [ ] 能处理远程图片的加载状态(loading/error) | Can handle remote image loading states (loading/error)
- [ ] 理解图片性能优化的关键点(缓存、尺寸、预加载) | Understand key image performance optimization points (caching, sizing, prefetching)
- [ ] 完成个人资料卡片实践项目 | Completed profile card practical project
- [ ] 能解释iOS和Android图片缓存的差异 | Can explain iOS and Android image caching differences
- [ ] 掌握Image.prefetch()的使用 | Master Image.prefetch() usage
- [ ] 至少完成3个扩展练习 | Completed at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前,请确保能够正确回答本日所有CCQs,并能够向他人清晰解释Image组件的核心概念、resizeMode的作用、本地与远程图片的区别、以及如何处理图片加载状态。

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others the Image component's core concepts, resizeMode's purpose, differences between local and remote images, and how to handle image loading states.
