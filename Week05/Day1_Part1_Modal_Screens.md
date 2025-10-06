# React Native入门 - 第5周第1天：模态屏幕与展示样式 | React Native Introduction - Week 5 Day 1: Modal Screens & Presentation Styles

## 学习目标 | Learning Objectives
- 理解模态屏幕（Modal）与普通屏幕导航的本质区别 | Understand the essential differences between modal screens and regular navigation
- 掌握React Navigation中三种主要展示样式（card, modal, transparentModal）的使用场景 | Master the use cases for three main presentation styles in React Navigation
- 能够实现iOS风格的从底部滑出模态和全屏模态效果 | Be able to implement iOS-style bottom sheet modals and fullscreen modals
- 学会为模态屏幕添加关闭按钮的最佳实践 | Learn best practices for adding close buttons to modal screens
- 理解透明模态覆盖层（如底部抽屉）的实现原理 | Understand the implementation principles of transparent modal overlays (like bottom sheets)
- 能够根据业务需求选择推送屏幕（push）还是展示模态（present）| Be able to choose between pushing screens vs presenting modals based on business requirements

## 详细内容 | Detailed Content

### 1. 模态屏幕的核心概念 | Core Concepts of Modal Screens (1小时 | 1 hour)

- **什么是模态屏幕？| What is a Modal Screen?**

  **概念定义 | Concept Definition:**
  模态屏幕是一种临时性的、需要用户完成特定任务或做出决策后才能继续的界面展示方式。它会中断当前的操作流程，将用户的注意力完全聚焦在当前任务上。与普通的屏幕导航（push）不同，模态通常从屏幕底部或以覆盖层的形式出现，并且有明确的关闭方式。

  A modal screen is a temporary interface presentation method that interrupts the current workflow, requiring users to complete a specific task or make a decision before continuing. Unlike regular screen navigation (push), modals typically appear from the bottom of the screen or as an overlay, and have explicit dismissal methods.

  **核心特征 | Key Characteristics:**
  - **临时性与任务聚焦 | Temporary and Task-Focused**: 模态屏幕通常用于完成一个独立的、短期的任务（如创建内容、确认操作、填写表单），完成后应该被关闭
  - **视觉层级关系 | Visual Hierarchy**: 模态在视觉上"覆盖"在之前的内容之上，通常会有背景遮罩或阴影效果，强调当前任务的优先级
  - **明确的进入和退出方式 | Clear Entry and Exit Methods**: 模态有独特的动画效果（如从底部滑入），并且通常提供显式的关闭按钮（X、Cancel、Done等）
  - **导航堆栈独立性 | Navigation Stack Independence**: 在React Navigation中，模态可以有自己的导航堆栈，与主导航流程相对独立

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 模态屏幕是用于长期展示内容还是临时任务？| Are modal screens for long-term content display or temporary tasks?
     **答案 | Answer:** 临时任务 | Temporary tasks - 模态的设计目的就是完成特定任务后关闭，不适合长期展示
  2. 用户可以通过点击返回按钮从模态屏幕退出吗？| Can users exit a modal screen by pressing the back button?
     **答案 | Answer:** 取决于平台和配置 | Depends on platform and configuration - iOS模态通常需要显式关闭按钮，Android可能支持返回键，但最佳实践是提供明确的关闭UI
  3. 模态屏幕和通过`navigation.push()`推送的普通屏幕在视觉层级上有何不同？| How do modal screens differ from regular pushed screens in visual hierarchy?
     **答案 | Answer:** 模态覆盖在内容之上，普通推送替换内容 | Modals overlay on top of content, regular pushes replace content - 模态保留了之前的上下文可见性
  4. 什么场景下应该使用模态而不是普通导航？| In what scenarios should you use modals instead of regular navigation?
     **答案 | Answer:** 需要用户完成或取消任务，不应作为主流程的一部分时 | When users need to complete or cancel a task that shouldn't be part of the main navigation flow - 如创建新项目、确认删除、快速预览等

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 模态屏幕的基本实现 | Basic Modal Screen Implementation
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function RootNavigator() {
    return (
      <Stack.Navigator>
        {/* 主屏幕 - 普通导航 | Main Screen - Regular Navigation */}
        <Stack.Screen name="Home" component={HomeScreen} />

        {/* 模态屏幕 - 使用 presentation 选项 | Modal Screen - Using presentation option */}
        <Stack.Screen
          name="CreatePost"
          component={CreatePostScreen}
          options={{
            presentation: 'modal', // 关键配置：以模态方式展示 | Key config: present as modal
            headerShown: true,
            headerTitle: '创建帖子 | Create Post',
          }}
        />
      </Stack.Navigator>
    );
  }

  // 在主屏幕中触发模态 | Triggering modal from main screen
  function HomeScreen({ navigation }) {
    return (
      <View>
        <Button
          title="创建新帖子 | Create New Post"
          onPress={() => navigation.navigate('CreatePost')} // 导航到模态屏幕 | Navigate to modal screen
        />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，`CreatePost`屏幕会如何出现？| How will the `CreatePost` screen appear in this code?
    **答案 | Answer:** 从屏幕底部向上滑出（iOS）或淡入（Android）| Slides up from bottom (iOS) or fades in (Android) - 由`presentation: 'modal'`决定
  - 如果移除`presentation: 'modal'`配置会发生什么？| What happens if we remove the `presentation: 'modal'` config?
    **答案 | Answer:** 屏幕会像普通页面一样从右侧推入 | Screen will push from right like a regular page - 失去模态的视觉特征

  **常见误区检查 | Common Misconception Checks:**
  - "模态屏幕必须使用`Modal`组件实现" | "Modal screens must be implemented with the Modal component"
    **答案 | Answer:** 错误 | False - React Navigation通过`presentation`选项提供模态行为，不需要使用React Native的`Modal`组件（它们是不同的概念）
  - "所有需要用户输入的屏幕都应该是模态" | "All screens requiring user input should be modals"
    **答案 | Answer:** 错误 | False - 只有中断主流程的临时任务才适合模态，如果是主流程的一部分（如多步骤注册），应使用普通导航

- **展示样式的类型 | Types of Presentation Styles**

  **概念定义 | Concept Definition:**
  React Navigation提供了三种主要的屏幕展示样式，每种都有不同的视觉效果和使用场景：`card`（卡片式）、`modal`（模态）和`transparentModal`（透明模态）。这些样式控制屏幕如何出现、覆盖程度以及动画效果。

  React Navigation provides three main screen presentation styles, each with different visual effects and use cases: `card` (card-style), `modal` (modal), and `transparentModal` (transparent modal). These styles control how screens appear, their overlay coverage, and animation effects.

  **核心特征对比 | Key Characteristics Comparison:**
  - **card（默认样式）| card (default style)**: 从右侧滑入，完全替换当前视图，常用于同级导航流程
  - **modal**: 从底部滑入（iOS）或淡入（Android），部分覆盖且可见背景内容，用于临时任务
  - **transparentModal**: 透明背景的模态，允许看到下层内容，用于弹窗、底部抽屉、提示框等轻量级交互

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `card`样式的屏幕会覆盖之前的屏幕还是完全替换？| Does the `card` style overlay or completely replace the previous screen?
     **答案 | Answer:** 完全替换 | Completely replace - 新屏幕占据整个视图区域
  2. `transparentModal`和`modal`的主要视觉区别是什么？| What's the main visual difference between `transparentModal` and `modal`?
     **答案 | Answer:** 背景透明度 | Background transparency - `transparentModal`背景透明可见下层，`modal`有不透明背景
  3. 如果要实现一个底部弹出的选择器（如iOS日期选择器），应该使用哪种样式？| Which style should be used for a bottom sheet picker (like iOS date picker)?
     **答案 | Answer:** transparentModal - 允许看到下层内容同时展示选择器
  4. `modal`样式在iOS和Android上的默认动画有何不同？| How does the default animation for `modal` style differ between iOS and Android?
     **答案 | Answer:** iOS从底部滑入，Android淡入 | iOS slides from bottom, Android fades in - 遵循各平台的设计规范

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 三种展示样式的对比示例 | Comparison of Three Presentation Styles
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function AppNavigator() {
    return (
      <Stack.Navigator>
        {/* 1. Card样式（默认）- 常规导航 | Card style (default) - Regular navigation */}
        <Stack.Screen
          name="Details"
          component={DetailsScreen}
          options={{
            presentation: 'card', // 可省略，是默认值 | Can be omitted, it's the default
            animation: 'slide_from_right', // 从右滑入 | Slide from right
          }}
        />

        {/* 2. Modal样式 - 全屏模态任务 | Modal style - Fullscreen modal task */}
        <Stack.Screen
          name="CreateContent"
          component={CreateContentScreen}
          options={{
            presentation: 'modal', // 从底部滑入的模态 | Bottom sheet modal
            headerShown: true,
            headerLeft: () => (
              <Button title="取消 | Cancel" onPress={() => navigation.goBack()} />
            ),
          }}
        />

        {/* 3. TransparentModal样式 - 透明覆盖层 | TransparentModal style - Transparent overlay */}
        <Stack.Screen
          name="FilterSheet"
          component={FilterSheetScreen}
          options={{
            presentation: 'transparentModal', // 透明背景，可见下层 | Transparent background
            headerShown: false, // 通常不显示header | Usually no header
            animation: 'fade', // 淡入淡出效果 | Fade animation
          }}
        />
      </Stack.Navigator>
    );
  }

  // TransparentModal的典型实现 | Typical TransparentModal Implementation
  function FilterSheetScreen({ navigation }) {
    return (
      <View style={{
        flex: 1,
        backgroundColor: 'rgba(0,0,0,0.5)', // 半透明黑色背景 | Semi-transparent black background
        justifyContent: 'flex-end', // 内容对齐到底部 | Align content to bottom
      }}>
        {/* 点击背景关闭 | Click background to close */}
        <TouchableOpacity
          style={{ flex: 1 }}
          onPress={() => navigation.goBack()}
          activeOpacity={1}
        />

        {/* 实际内容区域 | Actual content area */}
        <View style={{
          backgroundColor: 'white',
          borderTopLeftRadius: 20,
          borderTopRightRadius: 20,
          padding: 20,
          minHeight: 300,
        }}>
          <Text style={{ fontSize: 20, fontWeight: 'bold' }}>
            筛选选项 | Filter Options
          </Text>
          {/* 筛选内容 | Filter content */}
        </View>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - `FilterSheet`屏幕的背景为什么使用`rgba(0,0,0,0.5)`？| Why does `FilterSheet` screen use `rgba(0,0,0,0.5)` for background?
    **答案 | Answer:** 创建半透明遮罩效果，使下层内容可见但变暗 | Creates semi-transparent mask effect, making underlying content visible but dimmed
  - 如果将`CreateContent`的`presentation`改为`card`，用户体验有何变化？| If we change `CreateContent`'s `presentation` to `card`, how does UX change?
    **答案 | Answer:** 失去模态的任务聚焦感，像正常页面导航，可能混淆用户是否在主流程中 | Loses modal task focus, feels like normal page navigation, might confuse if user is in main flow

  **常见误区检查 | Common Misconception Checks:**
  - "使用`transparentModal`就不需要设置背景颜色了" | "Using `transparentModal` means no need to set background color"
    **答案 | Answer:** 错误 | False - `transparentModal`只是允许透明，仍需要手动设置`backgroundColor`（包括透明度）来实现效果

### 2. iOS风格模态实现 | iOS-Style Modal Implementation (1小时 | 1 hour)

- **从底部滑出的模态 | Bottom Sheet Modal**

  **概念定义 | Concept Definition:**
  iOS风格的从底部滑出模态是移动应用中最常见的交互模式之一。屏幕从底部边缘向上滑动出现，通常保留顶部的一部分空间显示之前的内容，创造一种"卡片堆叠"的视觉效果。用户可以通过向下拖动或点击关闭按钮来dismissal模态。

  iOS-style bottom sheet modals are one of the most common interaction patterns in mobile apps. The screen slides up from the bottom edge, typically leaving some space at the top to show previous content, creating a "card stacking" visual effect. Users can dismiss the modal by dragging down or tapping a close button.

  **核心特征 | Key Characteristics:**
  - **卡片视觉效果 | Card Visual Effect**: 模态屏幕像一张卡片覆盖在主内容上，通常有圆角和阴影
  - **可拖动关闭 | Draggable Dismissal**: iOS 13+支持向下拖动手势来关闭模态（通过`gestureEnabled`配置）
  - **高度控制 | Height Control**: 可以是全屏模态，也可以是部分高度的半模态（通过自定义或库实现）
  - **背景可见性 | Background Visibility**: 默认显示一部分底层内容，增强上下文感知

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. iOS模态的"卡片效果"是通过什么配置实现的？| What configuration creates the "card effect" for iOS modals?
     **答案 | Answer:** `presentation: 'modal'` + iOS平台自动应用 | `presentation: 'modal'` + automatically applied on iOS - 包括圆角、阴影和滑动动画
  2. 默认情况下，用户可以在iOS上通过向下滑动关闭模态吗？| By default, can users dismiss modals by swiping down on iOS?
     **答案 | Answer:** 是的 | Yes - `gestureEnabled`默认为true，允许手势关闭
  3. 如果要阻止用户通过手势关闭模态（如未保存的表单），应该怎么做？| How to prevent users from dismissing modals with gestures (e.g., unsaved form)?
     **答案 | Answer:** 设置`gestureEnabled: false` | Set `gestureEnabled: false` - 强制用户使用显式的保存/取消按钮
  4. 从底部滑出的模态适合什么类型的内容？| What type of content is suitable for bottom sheet modals?
     **答案 | Answer:** 创建/编辑内容、选择选项、快速操作面板 | Create/edit content, select options, quick action panels - 需要用户全神贯注的任务

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // iOS风格底部模态的完整实现 | Complete iOS-Style Bottom Modal Implementation
  import React from 'react';
  import { View, Text, Button, StyleSheet, Platform } from 'react-native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { SafeAreaView } from 'react-native-safe-area-context';

  const Stack = createNativeStackNavigator();

  // 主导航器配置 | Main Navigator Configuration
  function ModalNavigator() {
    return (
      <Stack.Navigator>
        {/* 主屏幕 | Main Screen */}
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: '主页 | Home' }}
        />

        {/* iOS风格模态屏幕 | iOS-Style Modal Screen */}
        <Stack.Screen
          name="CreatePost"
          component={CreatePostScreen}
          options={{
            presentation: 'modal', // 关键：模态展示 | Key: modal presentation
            gestureEnabled: true, // 允许向下拖动关闭 | Allow swipe down to dismiss
            headerShown: true,
            headerTitle: '新建帖子 | New Post',
            // iOS特定的模态header样式 | iOS-specific modal header style
            headerLeft: () => (
              <Button
                title="取消 | Cancel"
                onPress={() => navigation.goBack()} // 显式关闭 | Explicit dismiss
              />
            ),
            headerRight: () => (
              <Button
                title="发布 | Post"
                onPress={() => {
                  // 保存逻辑 | Save logic
                  navigation.goBack();
                }}
              />
            ),
          }}
        />
      </Stack.Navigator>
    );
  }

  // 主屏幕 - 触发模态 | Home Screen - Trigger Modal
  function HomeScreen({ navigation }) {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.title}>React Native 模态示例 | Modal Example</Text>

        <Button
          title="创建新帖子 | Create New Post"
          onPress={() => navigation.navigate('CreatePost')}
        />

        <Text style={styles.hint}>
          {Platform.OS === 'ios'
            ? '在iOS上，您可以向下滑动关闭模态 | On iOS, swipe down to dismiss'
            : '点击取消按钮关闭模态 | Tap Cancel to dismiss'}
        </Text>
      </SafeAreaView>
    );
  }

  // 模态屏幕内容 | Modal Screen Content
  function CreatePostScreen({ navigation }) {
    const [content, setContent] = React.useState('');

    return (
      <SafeAreaView style={styles.modalContainer}>
        <View style={styles.modalContent}>
          <Text style={styles.label}>帖子内容 | Post Content</Text>
          <TextInput
            style={styles.input}
            placeholder="写点什么... | Write something..."
            multiline
            value={content}
            onChangeText={setContent}
          />

          {/* iOS模态底部的操作区 | iOS Modal Bottom Action Area */}
          <View style={styles.actionArea}>
            <Button
              title="添加照片 | Add Photo"
              onPress={() => {/* 照片选择逻辑 | Photo picker logic */}}
            />
            <Button
              title="添加位置 | Add Location"
              onPress={() => {/* 位置选择逻辑 | Location picker logic */}}
            />
          </View>
        </View>
      </SafeAreaView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
      alignItems: 'center',
    },
    modalContainer: {
      flex: 1,
      backgroundColor: '#f5f5f5', // iOS模态常用的浅色背景 | Common iOS modal light background
    },
    modalContent: {
      flex: 1,
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    hint: {
      marginTop: 10,
      color: '#666',
      fontSize: 12,
    },
    label: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 8,
    },
    input: {
      backgroundColor: 'white',
      borderRadius: 8,
      padding: 12,
      minHeight: 100,
      textAlignVertical: 'top',
    },
    actionArea: {
      marginTop: 20,
      flexDirection: 'row',
      justifyContent: 'space-around',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么header的左右两侧分别是"取消"和"发布"按钮？| Why are "Cancel" and "Post" buttons on left and right of header?
    **答案 | Answer:** 遵循iOS设计规范 - 左侧为取消/返回，右侧为主要操作 | Follows iOS design guidelines - left for cancel/back, right for primary action
  - 如果用户在输入内容后向下滑动关闭，数据会丢失吗？| If user swipes down to dismiss after typing, will data be lost?
    **答案 | Answer:** 是的，默认会丢失 | Yes, lost by default - 需要添加`beforeRemove`监听器来提示保存（后续课程内容）

  **常见误区检查 | Common Misconception Checks:**
  - "设置`presentation: 'modal'`后在所有平台都会从底部滑入" | "Setting `presentation: 'modal'` makes it slide from bottom on all platforms"
    **答案 | Answer:** 错误 | False - Android默认是淡入效果，需要额外配置`animation`来实现从底部滑入

### 3. 全屏模态与关闭按钮最佳实践 | Fullscreen Modals & Close Button Best Practices (45分钟 | 45 minutes)

- **全屏模态的应用场景 | Fullscreen Modal Use Cases**

  **概念定义 | Concept Definition:**
  全屏模态占据整个屏幕空间，用户无法看到底层内容，适合复杂的多步骤任务、内容创建或需要用户全神贯注的操作。与部分高度模态不同，全屏模态强调任务的独立性和重要性，通常包含自己的导航结构。

  Fullscreen modals occupy the entire screen space, preventing users from seeing underlying content. They're suitable for complex multi-step tasks, content creation, or operations requiring full user attention. Unlike partial-height modals, fullscreen modals emphasize task independence and importance, often containing their own navigation structure.

  **核心特征 | Key Characteristics:**
  - **完整的视图控制 | Complete View Control**: 占据全部屏幕空间，包括安全区域
  - **独立的导航上下文 | Independent Navigation Context**: 可以在模态内部有自己的堆栈导航
  - **明确的关闭机制 | Clear Dismissal Mechanism**: 必须提供显式的关闭方式（按钮或手势）
  - **任务完成导向 | Task Completion Oriented**: 用于用户必须完成或明确取消的任务流程

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 全屏模态和普通全屏页面的主要区别是什么？| What's the main difference between fullscreen modals and regular fullscreen pages?
     **答案 | Answer:** 模态是临时的、可关闭的任务层，不是主导航流程的一部分 | Modals are temporary, dismissible task layers, not part of main navigation flow
  2. 在全屏模态中，关闭按钮应该放在哪里？| Where should the close button be placed in fullscreen modals?
     **答案 | Answer:** iOS通常在左上角（Cancel/Done），Android可在右上角（X）或使用返回键 | iOS typically top-left (Cancel/Done), Android top-right (X) or use back button
  3. 全屏模态内部可以有多个屏幕吗？| Can fullscreen modals contain multiple screens inside?
     **答案 | Answer:** 可以 | Yes - 可以在模态中嵌套Stack Navigator实现多步骤流程

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 全屏模态与嵌套导航 | Fullscreen Modal with Nested Navigation
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const RootStack = createNativeStackNavigator();
  const ModalStack = createNativeStackNavigator();

  // 模态内部的导航器（用于多步骤流程）| Navigator inside modal (for multi-step flow)
  function OnboardingModalNavigator() {
    return (
      <ModalStack.Navigator>
        <ModalStack.Screen
          name="Step1"
          component={OnboardingStep1}
          options={{
            headerTitle: '步骤 1/3 | Step 1/3',
            headerLeft: () => (
              <Button
                title="跳过 | Skip"
                onPress={() => {
                  // 关闭整个模态流程 | Dismiss entire modal flow
                  navigation.getParent()?.goBack();
                }}
              />
            ),
          }}
        />
        <ModalStack.Screen
          name="Step2"
          component={OnboardingStep2}
          options={{ headerTitle: '步骤 2/3 | Step 2/3' }}
        />
        <ModalStack.Screen
          name="Step3"
          component={OnboardingStep3}
          options={{
            headerTitle: '步骤 3/3 | Step 3/3',
            headerLeft: () => null, // 最后一步不显示返回 | No back on last step
            headerRight: () => (
              <Button
                title="完成 | Done"
                onPress={() => {
                  // 完成流程，关闭模态 | Complete flow, dismiss modal
                  navigation.getParent()?.goBack();
                }}
              />
            ),
          }}
        />
      </ModalStack.Navigator>
    );
  }

  // 根导航器 | Root Navigator
  function RootNavigator() {
    return (
      <RootStack.Navigator>
        {/* 主应用屏幕 | Main app screens */}
        <RootStack.Screen name="Main" component={MainScreen} />

        {/* 全屏模态 - 包含多步骤流程 | Fullscreen Modal - Multi-step flow */}
        <RootStack.Screen
          name="OnboardingModal"
          component={OnboardingModalNavigator}
          options={{
            presentation: 'fullScreenModal', // 全屏模态 | Fullscreen modal
            headerShown: false, // 隐藏外层header，使用内部导航的header | Hide outer header, use inner nav header
          }}
        />
      </RootStack.Navigator>
    );
  }
  ```

**继续到Part 2** | **Continued in Part 2**

本文档包含Day 1的前半部分内容，涵盖模态屏幕核心概念、展示样式类型和iOS风格实现。第二部分将包含透明模态、实践项目和扩展练习。

This document contains the first half of Day 1 content, covering modal screen core concepts, presentation style types, and iOS-style implementation. Part 2 will include transparent modals, practical projects, and extension exercises.
