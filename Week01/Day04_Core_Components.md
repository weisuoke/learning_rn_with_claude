# React Native入门 - 第4天：核心组件 | React Native Introduction - Day 4: Core Components

## 学习目标 | Learning Objectives
- 理解React Native核心组件与HTML元素的映射关系 | Understand the mapping between React Native Core Components and HTML elements
- 掌握View、Text、Image等基础组件的使用 | Master the usage of basic components like View, Text, and Image
- 了解React Native中文本必须被包裹的规则 | Learn the rule that all text must be wrapped in React Native
- 能够使用核心组件构建基本的UI界面 | Be able to build basic UI interfaces using Core Components
- 掌握从URL和本地资源加载图片的方法 | Master methods for loading images from URLs and local assets
- 理解React Native组件与Web开发的关键区别 | Understand key differences between React Native components and web development

## 详细内容 | Detailed Content

### 1. 核心组件基础概念 | Core Components Fundamentals (1小时 | 1 hour)

- **View组件 - 容器的基石 | View Component - The Foundation of Containers**

  **概念定义 | Concept Definition:**
  View是React Native中最基本的UI容器组件，相当于Web开发中的`<div>`元素。它是构建所有用户界面的基础，用于布局和容纳其他组件。| View is the most fundamental UI container component in React Native, equivalent to the `<div>` element in web development. It is the foundation for building all user interfaces, used for layout and containing other components.

  **核心特征 | Key Characteristics:**
  - View组件不会直接显示任何内容，它是一个容器 | View components don't directly display content; they are containers
  - 可以无限嵌套View组件来创建复杂布局 | You can infinitely nest View components to create complex layouts
  - View支持样式、触摸事件和布局属性 | View supports styles, touch events, and layout properties
  - View默认为Flexbox布局，与Web的block布局不同 | View defaults to Flexbox layout, different from web's block layout

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. View组件可以包含其他View组件吗？| Can a View component contain other View components?
     **答案 | Answer:** 是 | Yes - View组件可以无限嵌套，这是构建复杂布局的基础 | View components can be infinitely nested, which is the foundation for building complex layouts
  2. View组件相当于Web开发中的哪个HTML元素？| Which HTML element is View equivalent to in web development?
     **答案 | Answer:** `<div>` - View是React Native中的主要容器组件 | `<div>` - View is the primary container component in React Native
  3. View组件可以直接显示文本内容吗？| Can a View component directly display text content?
     **答案 | Answer:** 否 | No - 文本内容必须包裹在Text组件中 | Text content must be wrapped in a Text component
  4. View的默认布局模式是什么？| What is the default layout mode for View?
     **答案 | Answer:** Flexbox - 与Web的block布局不同，React Native的View默认使用Flexbox | Flexbox - Unlike web's block layout, React Native's View uses Flexbox by default

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, StyleSheet } from 'react-native';

  // 基本View组件示例 | Basic View component example
  const BasicViewExample = () => {
    return (
      // 最外层容器View | Outer container View
      <View style={styles.container}>
        {/* 嵌套的子View - 就像嵌套的div | Nested child View - like nested divs */}
        <View style={styles.box}>
          {/* 可以继续嵌套更多层级 | Can continue nesting more levels */}
          <View style={styles.innerBox} />
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1, // 占满可用空间 | Fill available space
      backgroundColor: '#f0f0f0',
    },
    box: {
      width: 200,
      height: 200,
      backgroundColor: 'blue',
      margin: 20,
    },
    innerBox: {
      width: 100,
      height: 100,
      backgroundColor: 'red',
      margin: 10,
    },
  });

  export default BasicViewExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码会渲染几个View组件？| How many View components will this code render?
    **答案 | Answer:** 3个 - container、box和innerBox | 3 - container, box, and innerBox
  - 如果移除所有style属性，View还会渲染吗？| Will the View still render if all style attributes are removed?
    **答案 | Answer:** 会，但不可见（无背景色和尺寸） | Yes, but invisible (no background color and size)

  **常见误区检查 | Common Misconception Checks:**
  - View可以像HTML div一样直接包含文本吗？| Can View directly contain text like HTML div?
    **答案 | Answer:** 不能 - 必须使用Text组件包裹文本 | No - text must be wrapped in a Text component
  - View的布局行为和Web的div完全一样吗？| Does View's layout behavior work exactly like web's div?
    **答案 | Answer:** 不一样 - View默认使用Flexbox，而div默认是block布局 | No - View defaults to Flexbox, while div defaults to block layout

- **Text组件 - 文本显示的唯一方式 | Text Component - The Only Way to Display Text**

  **概念定义 | Concept Definition:**
  Text是React Native中唯一用于显示文本的组件，相当于Web中的`<span>`、`<p>`等文本元素。在React Native中，所有文本内容都必须包裹在Text组件中，这是与Web开发的重要区别。| Text is the only component used to display text in React Native, equivalent to text elements like `<span>` and `<p>` in web. In React Native, all text content must be wrapped in a Text component, which is an important difference from web development.

  **核心特征 | Key Characteristics:**
  - 所有文本内容必须在Text组件内 - 这是强制规则 | All text content must be inside Text components - this is a mandatory rule
  - Text组件可以嵌套，继承父Text的样式 | Text components can be nested and inherit parent Text styles
  - Text支持触摸事件、样式和文本特定属性 | Text supports touch events, styles, and text-specific properties
  - Text不能包含View组件，但可以包含其他Text | Text cannot contain View components but can contain other Text components

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中可以在View中直接写文本吗？| Can you write text directly in a View in React Native?
     **答案 | Answer:** 否 | No - 会导致错误，所有文本必须在Text组件中 | This will cause an error; all text must be in Text components
  2. Text组件可以嵌套使用吗？| Can Text components be nested?
     **答案 | Answer:** 可以 | Yes - 子Text会继承父Text的样式属性 | Child Text will inherit parent Text's style properties
  3. Text组件可以包含View组件吗？| Can a Text component contain a View component?
     **答案 | Answer:** 不可以 | No - Text只能包含文本或其他Text组件 | Text can only contain text or other Text components
  4. Text组件相当于Web中的哪些元素？| Which web elements is Text equivalent to?
     **答案 | Answer:** `<span>`, `<p>`, `<h1>`等所有文本元素 | `<span>`, `<p>`, `<h1>` and all text elements

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  // Text组件使用示例 | Text component usage example
  const TextExample = () => {
    return (
      <View style={styles.container}>
        {/* 基本文本 | Basic text */}
        <Text style={styles.basicText}>这是基本文本 | This is basic text</Text>

        {/* 嵌套Text - 样式继承 | Nested Text - style inheritance */}
        <Text style={styles.parentText}>
          父文本样式 | Parent text style
          <Text style={styles.childText}> 子文本继承并覆盖 | Child text inherits and overrides</Text>
        </Text>

        {/* ❌ 错误示例 - 不能这样直接写文本 | ❌ Wrong example - cannot write text directly */}
        {/* <View>这会报错 | This will cause error</View> */}

        {/* ✅ 正确示例 | ✅ Correct example */}
        <View>
          <Text>文本必须在Text中 | Text must be in Text component</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    basicText: {
      fontSize: 16,
      color: 'black',
      marginBottom: 10,
    },
    parentText: {
      fontSize: 18,
      color: 'blue',
      fontWeight: 'bold',
    },
    childText: {
      fontSize: 14, // 覆盖父组件的fontSize | Overrides parent's fontSize
      color: 'red', // 覆盖父组件的color | Overrides parent's color
      // fontWeight继承自父组件 | fontWeight inherited from parent
    },
  });

  export default TextExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果在View中直接写"Hello"会发生什么？| What happens if you write "Hello" directly in a View?
    **答案 | Answer:** 应用会崩溃并显示错误 | The app will crash and show an error
  - childText的fontWeight值是什么？| What is the fontWeight value of childText?
    **答案 | Answer:** 'bold' - 从parentText继承 | 'bold' - inherited from parentText

  **常见误区检查 | Common Misconception Checks:**
  - Text和Web中的span完全一样吗？| Is Text exactly the same as span in web?
    **答案 | Answer:** 不完全一样 - Text有更严格的嵌套规则，不能包含块级元素 | Not exactly - Text has stricter nesting rules and cannot contain block-level elements
  - 空的Text组件会占用空间吗？| Will an empty Text component take up space?
    **答案 | Answer:** 不会 - 除非设置了固定尺寸或padding | No - unless fixed dimensions or padding are set

### 2. Image组件 - 图片显示 | Image Component - Displaying Images (1小时 | 1 hour)

- **Image组件的两种加载方式 | Two Loading Methods for Image Component**

  **概念定义 | Concept Definition:**
  Image组件用于显示图片，相当于Web中的`<img>`标签。React Native的Image组件支持两种主要的图片加载方式：网络图片（通过URL）和本地资源图片（通过require）。| Image component is used to display images, equivalent to the `<img>` tag in web. React Native's Image component supports two main image loading methods: network images (via URL) and local asset images (via require).

  **核心特征 | Key Characteristics:**
  - 网络图片需要使用source={{uri: 'url'}}格式并指定尺寸 | Network images require source={{uri: 'url'}} format and specified dimensions
  - 本地图片使用source={require('./path')}，尺寸可自动推断 | Local images use source={require('./path')}, dimensions can be auto-inferred
  - Image组件必须指定宽高，否则不显示（网络图片） | Image component must specify width/height, otherwise won't display (network images)
  - 支持多种图片格式：PNG、JPG、GIF等 | Supports multiple image formats: PNG, JPG, GIF, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 加载网络图片时必须指定宽高吗？| Must you specify width and height when loading network images?
     **答案 | Answer:** 是 | Yes - 网络图片必须显式指定尺寸才能显示 | Network images must have explicit dimensions to display
  2. 本地图片和网络图片的source写法一样吗？| Is the source syntax the same for local and network images?
     **答案 | Answer:** 不一样 | No - 本地用require()，网络用{uri: 'url'} | Local uses require(), network uses {uri: 'url'}
  3. Image组件相当于Web的什么标签？| What web tag is the Image component equivalent to?
     **答案 | Answer:** `<img>` - 但使用方式有所不同 | `<img>` - but with different usage patterns
  4. 可以不给本地图片设置宽高吗？| Can you omit width/height for local images?
     **答案 | Answer:** 可以 | Yes - 本地图片可以自动推断尺寸，但最好明确指定 | Local images can auto-infer dimensions, but explicit specification is better

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Image, StyleSheet } from 'react-native';

  // Image组件使用示例 | Image component usage example
  const ImageExample = () => {
    return (
      <View style={styles.container}>
        {/* 方式1: 网络图片 - 必须指定宽高 | Method 1: Network image - must specify dimensions */}
        <Image
          source={{
            uri: 'https://reactnative.dev/img/tiny_logo.png'
          }}
          style={styles.networkImage} // 必须有width和height | Must have width and height
        />

        {/* 方式2: 本地资源图片 - 使用require | Method 2: Local asset image - use require */}
        <Image
          source={require('./assets/local-image.png')} // 注意路径 | Note the path
          style={styles.localImage}
        />

        {/* 带有默认图和错误处理 | With default image and error handling */}
        <Image
          source={{ uri: 'https://example.com/image.jpg' }}
          style={styles.networkImage}
          defaultSource={require('./assets/placeholder.png')} // 加载中显示 | Show while loading
          onError={(error) => console.log('图片加载失败 | Image load failed', error)}
          onLoad={() => console.log('图片加载成功 | Image loaded successfully')}
        />

        {/* resizeMode属性控制图片适配 | resizeMode property controls image fitting */}
        <Image
          source={require('./assets/wide-image.png')}
          style={styles.resizableImage}
          resizeMode="contain" // cover | contain | stretch | repeat | center
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      alignItems: 'center',
    },
    networkImage: {
      width: 100, // 网络图片必需 | Required for network images
      height: 100, // 网络图片必需 | Required for network images
      marginBottom: 20,
    },
    localImage: {
      width: 150,
      height: 150,
      marginBottom: 20,
    },
    resizableImage: {
      width: 200,
      height: 100,
      borderWidth: 1,
      borderColor: '#ccc',
    },
  });

  export default ImageExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果networkImage没有指定width和height会怎样？| What happens if networkImage doesn't specify width and height?
    **答案 | Answer:** 图片不会显示 | The image won't display
  - require()可以用于加载网络图片吗？| Can require() be used to load network images?
    **答案 | Answer:** 不可以 - require()仅用于本地资源 | No - require() is only for local assets

  **常见误区检查 | Common Misconception Checks:**
  - Image的source可以直接写URL字符串吗？| Can Image's source be a direct URL string?
    **答案 | Answer:** 不可以 - 必须是{uri: 'url'}对象格式 | No - must be in {uri: 'url'} object format
  - 所有Image都需要指定宽高吗？| Do all Images need specified width and height?
    **答案 | Answer:** 网络图片必须，本地图片可选但推荐 | Network images must, local images optional but recommended

### 3. 核心组件对比与选择 | Core Components Comparison and Selection (1小时 | 1 hour)

- **React Native vs Web元素映射 | React Native vs Web Elements Mapping**

  **概念定义 | Concept Definition:**
  React Native不使用HTML元素，而是提供了一套对应的核心组件。理解这种映射关系是从Web开发过渡到React Native的关键。每个React Native组件都有其特定的用途和限制。| React Native doesn't use HTML elements but provides a set of corresponding core components. Understanding this mapping is key to transitioning from web development to React Native. Each React Native component has its specific purpose and constraints.

  **核心映射关系 | Core Mapping Relationships:**
  - `<div>` → `<View>` - 容器和布局 | Container and layout
  - `<span>`, `<p>`, `<h1>`-`<h6>` → `<Text>` - 所有文本 | All text
  - `<img>` → `<Image>` - 图片显示 | Image display
  - `<input>` → `<TextInput>` - 文本输入 | Text input
  - `<button>` → `<Button>` 或 `<TouchableOpacity>` | Button or TouchableOpacity
  - `<ul>`, `<ol>` → `<FlatList>` 或 `<SectionList>` | FlatList or SectionList

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以在React Native中使用`<div>`吗？| Can you use `<div>` in React Native?
     **答案 | Answer:** 不可以 | No - 必须使用`<View>` | Must use `<View>`
  2. Text组件可以替代所有Web文本标签吗？| Can Text component replace all web text tags?
     **答案 | Answer:** 是 | Yes - Text是显示所有文本的唯一方式 | Text is the only way to display all text
  3. Web的`<h1>`在React Native中如何实现？| How to implement web's `<h1>` in React Native?
     **答案 | Answer:** 使用Text组件并设置样式（fontSize, fontWeight等）| Use Text component with styles (fontSize, fontWeight, etc.)
  4. 为什么React Native不直接使用HTML元素？| Why doesn't React Native use HTML elements directly?
     **答案 | Answer:** 因为它渲染的是原生组件，不是Web DOM | Because it renders native components, not web DOM

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, Image, StyleSheet } from 'react-native';

  // Web vs React Native对比示例 | Web vs React Native comparison example

  // ❌ Web开发方式 (不能在React Native中使用) | Web approach (cannot use in React Native)
  /*
  const WebExample = () => {
    return (
      <div className="container">
        <h1>标题 | Title</h1>
        <p>段落文本 | Paragraph text</p>
        <img src="image.jpg" alt="图片" />
      </div>
    );
  };
  */

  // ✅ React Native方式 | React Native approach
  const NativeExample = () => {
    return (
      <View style={styles.container}> {/* 替代div | Replace div */}
        <Text style={styles.title}>标题 | Title</Text> {/* 替代h1 | Replace h1 */}
        <Text style={styles.paragraph}>段落文本 | Paragraph text</Text> {/* 替代p | Replace p */}
        <Image
          source={require('./image.jpg')}
          style={styles.image}
        /> {/* 替代img | Replace img */}
      </View>
    );
  };

  // 更复杂的映射示例 | More complex mapping example
  const ComplexMapping = () => {
    return (
      <View style={styles.container}>
        {/* Web: <div class="card"> */}
        <View style={styles.card}>
          {/* Web: <h2>Card Title</h2> */}
          <Text style={styles.cardTitle}>Card Title</Text>

          {/* Web: <div class="content"> */}
          <View style={styles.content}>
            {/* Web: <p>Description text</p> */}
            <Text style={styles.description}>Description text</Text>

            {/* Web: <div class="image-container"> */}
            <View style={styles.imageContainer}>
              {/* Web: <img src="..." /> */}
              <Image
                source={{uri: 'https://example.com/image.png'}}
                style={styles.cardImage}
              />
            </View>
          </View>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    title: {
      fontSize: 24, // 类似h1的大小 | Similar to h1 size
      fontWeight: 'bold',
      marginBottom: 10,
    },
    paragraph: {
      fontSize: 16, // 类似p的大小 | Similar to p size
      lineHeight: 24,
      marginBottom: 20,
    },
    image: {
      width: 300,
      height: 200,
    },
    card: {
      backgroundColor: 'white',
      borderRadius: 8,
      padding: 16,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3, // Android阴影 | Android shadow
    },
    cardTitle: {
      fontSize: 20,
      fontWeight: '600',
      marginBottom: 12,
    },
    content: {
      marginTop: 8,
    },
    description: {
      fontSize: 14,
      color: '#666',
      marginBottom: 12,
    },
    imageContainer: {
      alignItems: 'center',
    },
    cardImage: {
      width: 250,
      height: 150,
      borderRadius: 4,
    },
  });

  export default NativeExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中有多少个容器组件？| How many container components are in this code?
    **答案 | Answer:** 4个View组件 | 4 View components
  - 如何实现类似Web的`<h1>`到`<h6>`的效果？| How to achieve web's `<h1>` to `<h6>` effects?
    **答案 | Answer:** 使用不同fontSize的Text组件 | Use Text components with different fontSize values

  **常见误区检查 | Common Misconception Checks:**
  - React Native组件的className属性能用吗？| Can React Native components use className attribute?
    **答案 | Answer:** 不能 - 使用style属性替代 | No - use style attribute instead
  - View和div的行为完全一样吗？| Do View and div behave exactly the same?
    **答案 | Answer:** 不完全一样 - View默认flexbox，div默认block | Not exactly - View defaults to flexbox, div defaults to block

### 4. 组件嵌套规则与限制 | Component Nesting Rules and Limitations (1小时 | 1 hour)

- **React Native的嵌套限制 | React Native Nesting Restrictions**

  **概念定义 | Concept Definition:**
  React Native对组件嵌套有严格的规则，这些规则确保应用能够正确编译为原生代码。最重要的规则是：文本内容必须在Text组件内，Text组件不能包含View组件。| React Native has strict rules for component nesting, which ensure the app can be correctly compiled to native code. The most important rule is: text content must be inside Text components, and Text components cannot contain View components.

  **核心嵌套规则 | Core Nesting Rules:**
  - View可以包含View、Text、Image等任何组件 | View can contain View, Text, Image, and any other components
  - Text只能包含Text和文本字符串，不能包含View | Text can only contain Text and text strings, cannot contain View
  - 所有文本必须在Text内，不能直接在View中 | All text must be inside Text, cannot be directly in View
  - Image不能包含子组件（除非使用ImageBackground）| Image cannot contain child components (unless using ImageBackground)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. View组件中可以直接写"Hello"吗？| Can you write "Hello" directly in a View component?
     **答案 | Answer:** 不可以 | No - 会导致错误，必须包裹在Text中 | Will cause error, must be wrapped in Text
  2. Text组件可以包含View组件吗？| Can a Text component contain a View component?
     **答案 | Answer:** 不可以 | No - Text只能包含文本或其他Text | Text can only contain text or other Text
  3. 为什么React Native有这些嵌套限制？| Why does React Native have these nesting restrictions?
     **答案 | Answer:** 为了能正确映射到原生组件 | To correctly map to native components
  4. Image组件可以包含子组件吗？| Can an Image component contain child components?
     **答案 | Answer:** 普通Image不可以，需使用ImageBackground | Regular Image cannot, need to use ImageBackground

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, Image, ImageBackground, StyleSheet } from 'react-native';

  // 嵌套规则示例 | Nesting rules examples
  const NestingRulesExample = () => {
    return (
      <View style={styles.container}>
        {/* ✅ 正确: View包含View | Correct: View contains View */}
        <View style={styles.outerBox}>
          <View style={styles.innerBox} />
        </View>

        {/* ✅ 正确: View包含Text | Correct: View contains Text */}
        <View style={styles.textContainer}>
          <Text>这是正确的 | This is correct</Text>
        </View>

        {/* ❌ 错误: View直接包含文本 | Wrong: View directly contains text */}
        {/* <View>这会报错 | This will error</View> */}

        {/* ✅ 正确: Text包含Text | Correct: Text contains Text */}
        <Text style={styles.parentText}>
          外层文本 | Outer text
          <Text style={styles.childText}> 内层文本 | Inner text</Text>
        </Text>

        {/* ❌ 错误: Text包含View | Wrong: Text contains View */}
        {/*
        <Text>
          文本
          <View><Text>这会报错 | This will error</Text></View>
        </Text>
        */}

        {/* ✅ 正确: View包含Image | Correct: View contains Image */}
        <View style={styles.imageWrapper}>
          <Image
            source={require('./assets/icon.png')}
            style={styles.icon}
          />
        </View>

        {/* ❌ 错误: Image包含子组件 | Wrong: Image contains children */}
        {/*
        <Image source={require('./image.png')} style={styles.image}>
          <Text>不能这样 | Cannot do this</Text>
        </Image>
        */}

        {/* ✅ 正确: 使用ImageBackground包含子组件 | Correct: Use ImageBackground to contain children */}
        <ImageBackground
          source={require('./assets/background.png')}
          style={styles.backgroundImage}
        >
          <Text style={styles.overlayText}>
            在图片上显示文本 | Display text on image
          </Text>
        </ImageBackground>

        {/* ✅ 复杂但正确的嵌套 | Complex but correct nesting */}
        <View style={styles.card}>
          <View style={styles.header}>
            <Image source={require('./avatar.png')} style={styles.avatar} />
            <View style={styles.headerText}>
              <Text style={styles.name}>用户名 | Username</Text>
              <Text style={styles.subtitle}>副标题 | Subtitle</Text>
            </View>
          </View>
          <View style={styles.body}>
            <Text style={styles.content}>
              卡片内容文本 | Card content text
            </Text>
          </View>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    outerBox: {
      width: 200,
      height: 200,
      backgroundColor: 'lightblue',
      padding: 20,
    },
    innerBox: {
      width: 100,
      height: 100,
      backgroundColor: 'darkblue',
    },
    textContainer: {
      marginVertical: 10,
    },
    parentText: {
      fontSize: 16,
      color: 'black',
    },
    childText: {
      fontSize: 14,
      color: 'blue',
    },
    imageWrapper: {
      alignItems: 'center',
      marginVertical: 10,
    },
    icon: {
      width: 50,
      height: 50,
    },
    backgroundImage: {
      width: 300,
      height: 200,
      justifyContent: 'center',
      alignItems: 'center',
      marginVertical: 10,
    },
    overlayText: {
      color: 'white',
      fontSize: 18,
      fontWeight: 'bold',
      backgroundColor: 'rgba(0,0,0,0.5)',
      padding: 10,
    },
    card: {
      backgroundColor: 'white',
      borderRadius: 8,
      padding: 16,
      marginTop: 20,
    },
    header: {
      flexDirection: 'row',
      alignItems: 'center',
      marginBottom: 12,
    },
    avatar: {
      width: 40,
      height: 40,
      borderRadius: 20,
      marginRight: 12,
    },
    headerText: {
      flex: 1,
    },
    name: {
      fontSize: 16,
      fontWeight: 'bold',
    },
    subtitle: {
      fontSize: 12,
      color: '#666',
    },
    body: {
      marginTop: 8,
    },
    content: {
      fontSize: 14,
      lineHeight: 20,
    },
  });

  export default NestingRulesExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么注释掉的代码会报错？| Why will the commented code cause errors?
    **答案 | Answer:** 违反了嵌套规则（View直接包含文本，Text包含View）| Violates nesting rules (View directly contains text, Text contains View)
  - ImageBackground和Image的主要区别是什么？| What's the main difference between ImageBackground and Image?
    **答案 | Answer:** ImageBackground可以包含子组件 | ImageBackground can contain child components

  **常见误区检查 | Common Misconception Checks:**
  - 可以通过样式让Text包含块级布局吗？| Can you make Text contain block-level layout through styles?
    **答案 | Answer:** 不能 - Text的嵌套限制是结构性的，不是样式问题 | No - Text's nesting restrictions are structural, not a styling issue
  - Web中可行的嵌套在React Native中都可行吗？| Does all nesting that works in web also work in React Native?
    **答案 | Answer:** 不是 - React Native有更严格的嵌套规则 | No - React Native has stricter nesting rules

### 5. 样式和属性差异 | Styling and Property Differences (45分钟 | 45 minutes)

- **React Native样式系统 | React Native Styling System**

  **概念定义 | Concept Definition:**
  React Native使用JavaScript对象定义样式，而不是CSS。虽然许多样式属性名称相似，但有重要的差异，如使用驼峰命名、不支持某些CSS属性、以及默认使用Flexbox布局。| React Native uses JavaScript objects to define styles instead of CSS. While many style property names are similar, there are important differences, such as using camelCase naming, not supporting certain CSS properties, and defaulting to Flexbox layout.

  **关键差异 | Key Differences:**
  - CSS属性名使用驼峰命名：background-color → backgroundColor | CSS properties use camelCase: background-color → backgroundColor
  - 数值默认单位是dp（设备独立像素），不需要写'px' | Numeric values default to dp (device-independent pixels), no 'px' needed
  - 不支持简写属性（部分）：margin: '10px 20px' ❌ | Doesn't support shorthand properties (partially): margin: '10px 20px' ❌
  - 默认flexbox布局，不是block | Defaults to flexbox layout, not block

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. React Native的样式属性名使用什么命名方式？| What naming convention does React Native use for style properties?
     **答案 | Answer:** 驼峰命名 - backgroundColor而不是background-color | camelCase - backgroundColor not background-color
  2. 需要为数值添加'px'单位吗？| Do you need to add 'px' unit to numeric values?
     **答案 | Answer:** 不需要 - 直接写数字，默认是dp | No - write numbers directly, default is dp
  3. View的默认display值是什么？| What is the default display value for View?
     **答案 | Answer:** flex - 所有View默认是flexbox容器 | flex - all Views are flexbox containers by default

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  // 样式差异示例 | Styling differences example
  const StylingExample = () => {
    return (
      <View style={styles.container}>
        {/* ✅ 正确: 驼峰命名 | Correct: camelCase */}
        <View style={styles.correctBox}>
          <Text style={styles.text}>正确样式 | Correct styling</Text>
        </View>

        {/* ✅ 数值不需要单位 | Numeric values don't need units */}
        <View style={{
          width: 100,        // ✅ 不是 '100px'
          height: 100,       // ✅ 不是 '100px'
          marginTop: 20,     // ✅ 不是 '20px'
          backgroundColor: 'blue',
        }}>
          <Text style={{color: 'white'}}>无单位数值 | Unitless values</Text>
        </View>

        {/* ❌ Web简写在React Native中的处理 | Web shorthand handling in React Native */}
        <View style={{
          // ❌ 不支持: margin: '10 20'
          // ✅ 需要分开写 | Need to write separately:
          marginVertical: 10,
          marginHorizontal: 20,
          // 或者 | Or:
          // marginTop: 10,
          // marginBottom: 10,
          // marginLeft: 20,
          // marginRight: 20,
        }}>
          <Text>边距示例 | Margin example</Text>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
    },
    correctBox: {
      backgroundColor: '#f0f0f0',  // ✅ 驼峰命名 | camelCase
      borderRadius: 8,              // ✅ 不是 border-radius
      paddingVertical: 10,          // ✅ React Native特有简写
      paddingHorizontal: 15,
    },
    text: {
      fontSize: 16,                 // ✅ 数值，不需要'px'
      fontWeight: 'bold',           // ✅ 字符串值
      color: '#333',                // ✅ 颜色可以用hex、rgb等
    },
  });

  export default StylingExample;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - paddingVertical: 10 等价于什么？| What is paddingVertical: 10 equivalent to?
    **答案 | Answer:** paddingTop: 10 + paddingBottom: 10
  - 可以使用'100px'作为width的值吗？| Can you use '100px' as the width value?
    **答案 | Answer:** 可以但不推荐 - 直接用100即可 | Possible but not recommended - just use 100

  **常见误区检查 | Common Misconception Checks:**
  - React Native支持所有CSS属性吗？| Does React Native support all CSS properties?
    **答案 | Answer:** 不支持 - 只支持子集，如无float、position有限制 | No - only supports a subset, e.g., no float, limited position

### 6. 实践：从Web到Native的转换 | Practice: Converting from Web to Native (45分钟 | 45 minutes)

- **组件转换实战技巧 | Component Conversion Practical Tips**

  **转换步骤 | Conversion Steps:**
  1. 将所有HTML标签替换为对应的React Native组件 | Replace all HTML tags with corresponding React Native components
  2. 将className改为style，CSS改为StyleSheet对象 | Change className to style, CSS to StyleSheet objects
  3. 确保所有文本都包裹在Text组件中 | Ensure all text is wrapped in Text components
  4. 调整样式属性为驼峰命名和正确的值格式 | Adjust style properties to camelCase and correct value format
  5. 处理不支持的CSS属性，寻找替代方案 | Handle unsupported CSS properties, find alternatives

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 转换Web组件的第一步是什么？| What's the first step in converting web components?
     **答案 | Answer:** 替换HTML标签为React Native组件 | Replace HTML tags with React Native components
  2. 转换时最容易忘记什么？| What's most easily forgotten during conversion?
     **答案 | Answer:** 将文本包裹在Text组件中 | Wrapping text in Text components
  3. 如何处理Web中的float布局？| How to handle float layout from web?
     **答案 | Answer:** 使用flexbox替代 | Use flexbox instead

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // ========== Web版本 (React) ==========
  /*
  import './styles.css';

  const WebCard = () => {
    return (
      <div className="card">
        <div className="header">
          <img src="avatar.png" alt="Avatar" className="avatar" />
          <div className="header-text">
            <h3>John Doe</h3>
            <p className="subtitle">Software Engineer</p>
          </div>
        </div>
        <div className="body">
          <p>This is a card component that displays user information.</p>
          <button className="action-button">View Profile</button>
        </div>
      </div>
    );
  };

  // styles.css
  .card {
    background-color: white;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }

  .header {
    display: flex;
    align-items: center;
    margin-bottom: 12px;
  }

  .avatar {
    width: 50px;
    height: 50px;
    border-radius: 25px;
    margin-right: 12px;
  }

  .header-text {
    flex: 1;
  }

  h3 {
    font-size: 18px;
    font-weight: bold;
    margin: 0 0 4px 0;
  }

  .subtitle {
    font-size: 14px;
    color: #666;
    margin: 0;
  }

  .body {
    margin-top: 8px;
  }

  .body p {
    font-size: 14px;
    line-height: 20px;
    margin-bottom: 12px;
  }

  .action-button {
    background-color: #007AFF;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 6px;
    font-size: 14px;
  }
  */

  // ========== React Native版本 (转换后) ==========
  import React from 'react';
  import { View, Text, Image, TouchableOpacity, StyleSheet } from 'react-native';

  const NativeCard = () => {
    return (
      // div → View
      <View style={styles.card}>
        <View style={styles.header}>
          {/* img → Image, 注意source格式 */}
          <Image
            source={require('./avatar.png')}
            style={styles.avatar}
          />
          <View style={styles.headerText}>
            {/* h3 → Text (带样式) */}
            <Text style={styles.title}>John Doe</Text>
            {/* p → Text */}
            <Text style={styles.subtitle}>Software Engineer</Text>
          </View>
        </View>

        <View style={styles.body}>
          {/* p → Text */}
          <Text style={styles.bodyText}>
            This is a card component that displays user information.
          </Text>
          {/* button → TouchableOpacity + Text */}
          <TouchableOpacity style={styles.actionButton}>
            <Text style={styles.buttonText}>View Profile</Text>
          </TouchableOpacity>
        </View>
      </View>
    );
  };

  const styles = StyleSheet.create({
    // .card → card
    card: {
      backgroundColor: 'white',      // background-color → backgroundColor
      borderRadius: 8,                // 数值不需要'px'
      padding: 16,
      // box-shadow → shadow相关属性
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,                   // Android阴影
    },

    // .header → header
    header: {
      flexDirection: 'row',           // display: flex + flex-direction
      alignItems: 'center',           // align-items → alignItems
      marginBottom: 12,               // margin-bottom → marginBottom
    },

    // .avatar → avatar
    avatar: {
      width: 50,
      height: 50,
      borderRadius: 25,
      marginRight: 12,                // margin-right → marginRight
    },

    // .header-text → headerText
    headerText: {
      flex: 1,
    },

    // h3 → title (Text样式)
    title: {
      fontSize: 18,                   // font-size → fontSize
      fontWeight: 'bold',             // font-weight → fontWeight
      marginBottom: 4,
    },

    // .subtitle → subtitle
    subtitle: {
      fontSize: 14,
      color: '#666',
    },

    // .body → body
    body: {
      marginTop: 8,
    },

    // .body p → bodyText
    bodyText: {
      fontSize: 14,
      lineHeight: 20,                 // line-height → lineHeight
      marginBottom: 12,
    },

    // .action-button → actionButton
    actionButton: {
      backgroundColor: '#007AFF',
      paddingVertical: 10,            // 替代 padding: 10px 20px
      paddingHorizontal: 20,
      borderRadius: 6,
      alignItems: 'center',           // 居中按钮文本
    },

    // 按钮内文本样式
    buttonText: {
      color: 'white',
      fontSize: 14,
      fontWeight: '600',
    },
  });

  export default NativeCard;

  /*
   * 转换要点总结 | Conversion Key Points Summary:
   *
   * 1. 标签替换 | Tag Replacement:
   *    - div → View
   *    - h3, p → Text
   *    - img → Image
   *    - button → TouchableOpacity
   *
   * 2. 样式转换 | Style Conversion:
   *    - className → style prop
   *    - CSS文件 → StyleSheet.create()
   *    - 连字符 → 驼峰: margin-bottom → marginBottom
   *    - 去掉单位: 16px → 16
   *
   * 3. 特殊处理 | Special Handling:
   *    - 文本必须在Text中 | Text must be in Text component
   *    - 按钮需要包裹Text | Button needs to wrap Text
   *    - box-shadow → shadowColor/shadowOffset等
   *    - display: flex → flexDirection (默认column)
   *
   * 4. 不支持的属性 | Unsupported Properties:
   *    - float → 使用flexbox
   *    - position: fixed → 使用position: absolute
   *    - z-index → 通过组件顺序控制
   */
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 转换中最重要的区别是什么？| What's the most important difference in conversion?
    **答案 | Answer:** 文本必须在Text组件中，不能直接在View中 | Text must be in Text component, cannot be directly in View
  - Web的button如何转换？| How to convert web's button?
    **答案 | Answer:** 使用TouchableOpacity包裹Text组件 | Use TouchableOpacity wrapping Text component

  **常见误区检查 | Common Misconception Checks:**
  - 可以直接复制Web的JSX到React Native吗？| Can you directly copy web JSX to React Native?
    **答案 | Answer:** 不可以 - 需要替换所有HTML标签和调整样式 | No - need to replace all HTML tags and adjust styles

## 实践项目：产品卡片展示应用 | Practical Project: Product Card Display App

### 目标 | Objective
构建一个产品卡片展示应用，综合运用View、Text、Image等核心组件，展示从Web思维转换到React Native的完整过程。项目将包含产品图片、标题、描述、价格等信息，并实现响应式布局。| Build a product card display app that comprehensively applies core components like View, Text, and Image, demonstrating the complete process of transitioning from web thinking to React Native. The project will include product images, titles, descriptions, prices, and implement responsive layout.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. View、Text、Image三个核心组件的使用场景分别是什么？| What are the use cases for View, Text, and Image core components?
   **答案 | Answer:** View用于布局容器，Text用于显示所有文本，Image用于显示图片 | View for layout containers, Text for displaying all text, Image for displaying images

2. 为什么所有文本必须在Text组件中？| Why must all text be in Text components?
   **答案 | Answer:** React Native的架构要求，确保能正确映射到原生文本组件 | React Native architecture requirement to ensure correct mapping to native text components

3. 网络图片和本地图片的加载方式有何不同？| What's the difference between loading network and local images?
   **答案 | Answer:** 网络图片用source={{uri: 'url'}}且必须指定尺寸，本地图片用source={require('./path')} | Network images use source={{uri: 'url'}} and must specify dimensions, local images use source={require('./path')}

### 步骤 | Steps
1. **创建项目结构** - 使用View组件搭建基本布局框架 | Create project structure - Build basic layout framework using View components
2. **添加产品图片** - 使用Image组件从网络和本地加载图片 | Add product images - Load images from network and local using Image component
3. **设计文本层级** - 使用Text组件展示标题、描述、价格等信息 | Design text hierarchy - Display title, description, price using Text components
4. **样式美化** - 应用StyleSheet创建美观的卡片样式 | Style beautification - Apply StyleSheet to create beautiful card styles
5. **组件化重构** - 将ProductCard提取为可复用组件 | Component refactoring - Extract ProductCard as reusable component

### 示例代码 | Example Code
```jsx
/**
 * 产品卡片展示应用 | Product Card Display App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - View组件的布局能力 | View component layout capabilities
 * - Text组件的文本显示和样式 | Text component text display and styling
 * - Image组件的图片加载（网络和本地）| Image component image loading (network and local)
 * - StyleSheet样式系统 | StyleSheet styling system
 * - 组件嵌套规则 | Component nesting rules
 */

import React from 'react';
import {
  View,
  Text,
  Image,
  ScrollView,
  StyleSheet,
  Dimensions,
} from 'react-native';

// 获取屏幕宽度用于响应式设计 | Get screen width for responsive design
const { width: SCREEN_WIDTH } = Dimensions.get('window');
const CARD_WIDTH = SCREEN_WIDTH - 40; // 20px margin on each side

// 产品数据 | Product data
const PRODUCTS = [
  {
    id: 1,
    title: '无线蓝牙耳机 | Wireless Bluetooth Earbuds',
    description: '高品质音效，降噪技术，长续航 | High-quality sound, noise cancellation, long battery life',
    price: '¥299',
    imageUrl: 'https://picsum.photos/300/200?random=1',
    localImage: null, // 如果有本地图片可以用 require('./assets/product1.png')
    rating: 4.5,
    stock: 128,
  },
  {
    id: 2,
    title: '智能手表 | Smart Watch',
    description: '健康监测，运动追踪，消息提醒 | Health monitoring, fitness tracking, message notifications',
    price: '¥1,299',
    imageUrl: 'https://picsum.photos/300/200?random=2',
    localImage: null,
    rating: 4.8,
    stock: 56,
  },
  {
    id: 3,
    title: '便携充电宝 | Portable Power Bank',
    description: '20000mAh大容量，快充支持 | 20000mAh capacity, fast charging support',
    price: '¥159',
    imageUrl: 'https://picsum.photos/300/200?random=3',
    localImage: null,
    rating: 4.3,
    stock: 203,
  },
];

// 产品卡片组件 | Product Card Component
const ProductCard = ({ product }) => {
  return (
    // 卡片容器 - 相当于Web的<div class="card"> | Card container - equivalent to web's <div class="card">
    <View style={styles.card}>

      {/* 图片容器 | Image container */}
      <View style={styles.imageContainer}>
        <Image
          // 根据是否有本地图片选择source | Choose source based on local image availability
          source={
            product.localImage
              ? product.localImage
              : { uri: product.imageUrl }
          }
          style={styles.productImage}
          resizeMode="cover" // 图片填充模式 | Image fill mode
        />

        {/* 库存标签 - View嵌套Text | Stock badge - View nesting Text */}
        <View style={styles.stockBadge}>
          <Text style={styles.stockText}>
            库存 | Stock: {product.stock}
          </Text>
        </View>
      </View>

      {/* 产品信息容器 | Product info container */}
      <View style={styles.infoContainer}>

        {/* 标题 - 相当于Web的<h3> | Title - equivalent to web's <h3> */}
        <Text style={styles.title} numberOfLines={2}>
          {product.title}
        </Text>

        {/* 描述 - 相当于Web的<p> | Description - equivalent to web's <p> */}
        <Text style={styles.description} numberOfLines={2}>
          {product.description}
        </Text>

        {/* 价格和评分容器 - flexDirection实现横向布局 | Price and rating container - flexDirection for horizontal layout */}
        <View style={styles.footer}>
          {/* 价格 - Text嵌套Text实现样式变化 | Price - nested Text for style variation */}
          <View style={styles.priceContainer}>
            <Text style={styles.priceLabel}>价格 | Price: </Text>
            <Text style={styles.price}>{product.price}</Text>
          </View>

          {/* 评分显示 | Rating display */}
          <View style={styles.ratingContainer}>
            <Text style={styles.ratingIcon}>⭐</Text>
            <Text style={styles.ratingText}>{product.rating}</Text>
          </View>
        </View>
      </View>
    </View>
  );
};

// 主应用组件 | Main App Component
const ProductCardApp = () => {
  return (
    // ScrollView使内容可滚动 - 相当于Web的滚动容器 | ScrollView makes content scrollable - equivalent to web's scroll container
    <ScrollView style={styles.container}>

      {/* 头部容器 | Header container */}
      <View style={styles.header}>
        {/* 应用标题 - 相当于Web的<h1> | App title - equivalent to web's <h1> */}
        <Text style={styles.appTitle}>
          产品展示 | Product Showcase
        </Text>
        {/* 副标题 - 相当于Web的<p class="subtitle"> | Subtitle - equivalent to web's <p class="subtitle"> */}
        <Text style={styles.appSubtitle}>
          精选优质商品 | Featured Quality Products
        </Text>
      </View>

      {/* 产品列表容器 | Product list container */}
      <View style={styles.productsContainer}>
        {/* 遍历产品数据渲染卡片 | Iterate product data to render cards */}
        {PRODUCTS.map((product) => (
          // 每个卡片的包装器，添加间距 | Wrapper for each card, adding spacing
          <View key={product.id} style={styles.cardWrapper}>
            <ProductCard product={product} />
          </View>
        ))}
      </View>

      {/* 底部说明 | Footer note */}
      <View style={styles.footerNote}>
        <Text style={styles.noteText}>
          💡 注意：所有文本都包裹在Text组件中 |
          💡 Note: All text is wrapped in Text components
        </Text>
        <Text style={styles.noteText}>
          📦 View用于布局，Image用于图片，Text用于文本 |
          📦 View for layout, Image for images, Text for text
        </Text>
      </View>
    </ScrollView>
  );
};

// 样式定义 | Style definitions
const styles = StyleSheet.create({
  // 主容器样式 | Main container style
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },

  // 头部样式 | Header styles
  header: {
    backgroundColor: '#007AFF',
    paddingVertical: 30,
    paddingHorizontal: 20,
    paddingTop: 60, // 为状态栏留出空间 | Space for status bar
  },
  appTitle: {
    fontSize: 28,
    fontWeight: 'bold',
    color: 'white',
    marginBottom: 8,
  },
  appSubtitle: {
    fontSize: 16,
    color: 'rgba(255, 255, 255, 0.9)',
  },

  // 产品容器样式 | Products container styles
  productsContainer: {
    padding: 20,
  },
  cardWrapper: {
    marginBottom: 20, // 卡片间距 | Card spacing
  },

  // 卡片样式 | Card styles
  card: {
    backgroundColor: 'white',
    borderRadius: 12,
    overflow: 'hidden', // 确保子元素不超出圆角 | Ensure children don't overflow rounded corners
    // iOS阴影 | iOS shadow
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 8,
    // Android阴影 | Android shadow
    elevation: 5,
  },

  // 图片容器样式 | Image container styles
  imageContainer: {
    width: '100%',
    height: 200,
    position: 'relative', // 为绝对定位的子元素提供参考 | Provide reference for absolutely positioned children
  },
  productImage: {
    width: '100%',
    height: '100%',
  },
  stockBadge: {
    position: 'absolute',
    top: 10,
    right: 10,
    backgroundColor: 'rgba(0, 122, 255, 0.9)',
    paddingHorizontal: 12,
    paddingVertical: 6,
    borderRadius: 12,
  },
  stockText: {
    color: 'white',
    fontSize: 12,
    fontWeight: '600',
  },

  // 信息容器样式 | Info container styles
  infoContainer: {
    padding: 16,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
    // numberOfLines在组件上设置，这里只是样式 | numberOfLines is set on component, this is just styling
  },
  description: {
    fontSize: 14,
    color: '#666',
    lineHeight: 20,
    marginBottom: 12,
  },

  // 底部容器（价格和评分）| Footer container (price and rating)
  footer: {
    flexDirection: 'row', // 横向排列 | Horizontal arrangement
    justifyContent: 'space-between', // 两端对齐 | Space between
    alignItems: 'center', // 垂直居中 | Vertical center
    marginTop: 8,
  },
  priceContainer: {
    flexDirection: 'row',
    alignItems: 'baseline', // 基线对齐 | Baseline alignment
  },
  priceLabel: {
    fontSize: 14,
    color: '#666',
  },
  price: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#FF6B6B',
  },
  ratingContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#FFF9E6',
    paddingHorizontal: 10,
    paddingVertical: 4,
    borderRadius: 8,
  },
  ratingIcon: {
    fontSize: 16,
    marginRight: 4,
  },
  ratingText: {
    fontSize: 16,
    fontWeight: '600',
    color: '#FFB800',
  },

  // 底部说明样式 | Footer note styles
  footerNote: {
    padding: 20,
    backgroundColor: '#E8F4FF',
    marginHorizontal: 20,
    marginBottom: 40,
    borderRadius: 8,
  },
  noteText: {
    fontSize: 13,
    color: '#0066CC',
    lineHeight: 20,
    marginBottom: 4,
  },
});

export default ProductCardApp;

/**
 * 🎯 关键概念检查点 | Key Concept Checkpoints:
 *
 * 1. ✅ 所有容器使用View组件
 *    All containers use View component
 *
 * 2. ✅ 所有文本都包裹在Text组件中
 *    All text is wrapped in Text components
 *
 * 3. ✅ Image组件正确使用（网络图片指定尺寸）
 *    Image component used correctly (network images with specified dimensions)
 *
 * 4. ✅ 样式使用StyleSheet.create定义
 *    Styles defined using StyleSheet.create
 *
 * 5. ✅ 正确的组件嵌套（View可包含任何组件，Text只包含Text）
 *    Correct component nesting (View can contain any component, Text only contains Text)
 *
 * 6. ✅ flexDirection实现横向布局
 *    flexDirection for horizontal layout
 *
 * 7. ✅ Text嵌套实现样式继承和覆盖
 *    Nested Text for style inheritance and overriding
 */
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确使用了View、Text、Image三个核心组件？| Does the project correctly use View, Text, and Image core components?
   ✅ 是 - 所有布局用View，所有文本用Text，所有图片用Image | Yes - all layouts use View, all text uses Text, all images use Image

2. 是否所有文本都包裹在Text组件中？| Is all text wrapped in Text components?
   ✅ 是 - 没有直接在View中放置文本 | Yes - no text directly placed in View

3. 代码是否体现了Web到Native的转换思维？| Does the code reflect the web-to-native conversion thinking?
   ✅ 是 - 注释中明确标注了与Web元素的对应关系 | Yes - comments clearly indicate correspondence with web elements

4. 样式是否正确使用驼峰命名和无单位数值？| Do styles correctly use camelCase naming and unitless values?
   ✅ 是 - backgroundColor、marginBottom等驼峰命名，数值无'px' | Yes - camelCase like backgroundColor, marginBottom, numeric values without 'px'

5. 网络图片是否指定了宽高？| Are width and height specified for network images?
   ✅ 是 - productImage样式中定义了100%的宽高 | Yes - width and height defined as 100% in productImage style

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **View组件深度理解练习 | View Component Deep Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建一个复杂的嵌套布局，至少包含5层View嵌套，使用flexbox实现卡片网格布局（2列）
   - **概念检查 | Concept Check:** View可以无限嵌套吗？flexDirection的默认值是什么？
   - **学习目标 | Learning Objective:** 深入理解View的容器特性和flexbox布局能力

2. **Text组件嵌套和样式继承练习 | Text Component Nesting and Style Inheritance Exercise**
   - **练习描述 | Exercise Description:** 创建一个富文本段落，使用Text嵌套实现：粗体标题 + 普通文本 + 高亮文本 + 斜体注释，观察样式继承
   - **概念检查 | Concept Check:** 子Text会继承父Text的哪些样式？可以覆盖哪些样式？
   - **学习目标 | Learning Objective:** 掌握Text组件的样式继承机制

3. **Image加载方式对比练习 | Image Loading Methods Comparison Exercise**
   - **练习描述 | Exercise Description:** 创建一个图片展示组件，同时包含：网络图片、本地图片、带占位符的图片、带加载失败处理的图片
   - **概念检查 | Concept Check:** 网络图片和本地图片的source语法有何不同？哪种需要指定尺寸？
   - **学习目标 | Learning Objective:** 完全掌握Image组件的各种加载方式

4. **组件嵌套规则验证练习 | Component Nesting Rules Validation Exercise**
   - **练习描述 | Exercise Description:** 尝试创建以下嵌套组合并观察哪些会报错：View→Text、Text→View、Text→Text、Image→Text，总结规则
   - **概念检查 | Concept Check:** 哪些组件可以包含哪些组件？Text的嵌套限制是什么？
   - **学习目标 | Learning Objective:** 彻底理解React Native的组件嵌套限制

5. **Web到Native转换挑战练习 | Web to Native Conversion Challenge Exercise**
   - **练习描述 | Exercise Description:** 将一个完整的Web博客文章页面（包含h1、h2、p、img、blockquote、ul）转换为React Native版本
   - **概念检查 | Concept Check:** 转换时需要注意哪些关键点？哪些Web元素没有直接对应的组件？
   - **学习目标 | Learning Objective:** 培养Web到Native的转换思维能力

6. **样式系统对比练习 | Styling System Comparison Exercise**
   - **练习描述 | Exercise Description:** 创建相同的卡片组件，一个用Web CSS，一个用React Native StyleSheet，对比所有差异
   - **概念检查 | Concept Check:** CSS属性名如何转换？哪些CSS属性不支持？如何替代？
   - **学习目标 | Learning Objective:** 深入理解React Native样式系统的特点和限制

7. **复杂UI重构练习 | Complex UI Refactoring Exercise**
   - **练习描述 | Exercise Description:** 将实践项目中的ProductCard进一步拆分为：ProductImage、ProductInfo、ProductPrice等子组件，保持功能不变
   - **概念检查 | Concept Check:** 组件拆分的原则是什么？如何在拆分后保持正确的嵌套规则？
   - **学习目标 | Learning Objective:** 提高组件化设计和重构能力

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [React Native Core Components](https://reactnative.dev/docs/components-and-apis) - 核心组件完整文档
- [View Component](https://reactnative.dev/docs/view) - View组件详细API
- [Text Component](https://reactnative.dev/docs/text) - Text组件详细API
- [Image Component](https://reactnative.dev/docs/image) - Image组件详细API
- [StyleSheet API](https://reactnative.dev/docs/stylesheet) - 样式系统文档

### 深入理解资源 | Deep Understanding Resources
- [React Native Express - Core Components](https://www.reactnative.express/core-components) - 可视化核心组件教程
- [Understanding React Native Flexbox](https://reactnative.dev/docs/flexbox) - Flexbox布局详解
- [React Native Styling Cheat Sheet](https://github.com/vhpoet/react-native-styling-cheat-sheet) - 样式速查表

### 实践应用指南 | Practical Application Guides
- [Building Layouts in React Native](https://reactnative.dev/docs/height-and-width) - 布局构建指南
- [Images in React Native](https://reactnative.dev/docs/images) - 图片处理最佳实践

### 常见问题和解答 | FAQ and Answers
- [Why Text Must Be Wrapped](https://stackoverflow.com/questions/tagged/react-native+text) - 为什么文本必须包裹
- [Web vs React Native Components](https://medium.com/@reactnativeacademy/web-vs-react-native-components) - Web与RN组件对比

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解View组件作为容器的核心作用 | Understand View component's core role as container
- [ ] 掌握Text组件是显示文本的唯一方式 | Master that Text component is the only way to display text
- [ ] 能够正确使用Image组件加载网络和本地图片 | Correctly use Image component to load network and local images
- [ ] 理解React Native与Web元素的映射关系 | Understand mapping between React Native and web elements
- [ ] 掌握组件嵌套规则（Text不能包含View）| Master component nesting rules (Text cannot contain View)
- [ ] 能够将Web组件正确转换为React Native组件 | Convert web components to React Native components correctly
- [ ] 理解StyleSheet样式系统的特点 | Understand StyleSheet styling system characteristics
- [ ] 掌握驼峰命名和无单位数值的样式写法 | Master camelCase naming and unitless value style syntax
- [ ] 完成产品卡片展示应用项目 | Complete product card display app project
- [ ] 至少完成3个扩展练习 | Complete at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
1. 无需查看文档就能说出View、Text、Image的基本用法
2. 能够解释为什么所有文本必须在Text组件中
3. 能够快速将Web的HTML/CSS转换为React Native代码
4. 理解并能避免常见的组件嵌套错误

Before marking as complete, ensure you can:
1. Explain basic usage of View, Text, Image without documentation
2. Explain why all text must be in Text components
3. Quickly convert web HTML/CSS to React Native code
4. Understand and avoid common component nesting errors
