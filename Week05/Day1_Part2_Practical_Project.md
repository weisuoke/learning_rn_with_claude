# React Native入门 - 第5周第1天：模态屏幕实践项目（下） | React Native - Week 5 Day 1: Modal Screens Practical Project (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. 透明模态覆盖层实现 | Transparent Modal Overlay Implementation (1小时 | 1 hour)

- **透明模态的设计原理 | Transparent Modal Design Principles**

  **概念定义 | Concept Definition:**
  透明模态（`transparentModal`）允许开发者创建部分遮罩效果的覆盖层界面，如底部抽屉、操作菜单、提示框等。与传统模态不同，透明模态的背景可以完全自定义，通常使用半透明黑色来突出前景内容，同时保持底层界面的可见性，增强用户的上下文感知。

  Transparent modals (`transparentModal`) allow developers to create partially masked overlay interfaces like bottom sheets, action menus, and alert dialogs. Unlike traditional modals, transparent modals have fully customizable backgrounds, typically using semi-transparent black to highlight foreground content while maintaining visibility of the underlying interface for enhanced contextual awareness.

  **核心特征 | Key Characteristics:**
  - **背景控制灵活性 | Flexible Background Control**: 可以使用任何颜色和透明度值（如`rgba(0,0,0,0.5)`）
  - **点击背景关闭 | Tap-to-Dismiss Background**: 常见模式是点击半透明区域关闭模态
  - **内容定位自由 | Free Content Positioning**: 可以将内容定位在屏幕任意位置（底部、中心、顶部）
  - **轻量级交互 | Lightweight Interactions**: 适合快速选择、确认操作等不中断流程的交互

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. `transparentModal`的"透明"是指什么？| What does "transparent" mean in `transparentModal`?
     **答案 | Answer:** 模态背景可以是透明或半透明的，显示下层内容 | Modal background can be transparent or semi-transparent, showing underlying content
  2. 为什么点击背景关闭是透明模态的常见模式？| Why is tap-to-dismiss on background a common pattern for transparent modals?
     **答案 | Answer:** 提供直观的退出方式，用户自然期望点击遮罩外区域关闭 | Provides intuitive exit method, users naturally expect tapping outside to close
  3. 透明模态适合展示复杂的多步骤流程吗？| Are transparent modals suitable for complex multi-step flows?
     **答案 | Answer:** 不适合 | Not suitable - 应使用全屏模态，透明模态适合单一简短任务
  4. 如何防止透明模态的背景点击事件传递到下层屏幕？| How to prevent background tap events in transparent modals from reaching underlying screens?
     **答案 | Answer:** 使用`<TouchableOpacity activeOpacity={1}>`包裹背景，阻止事件冒泡 | Wrap background with `<TouchableOpacity activeOpacity={1}>` to prevent event bubbling

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 透明模态的完整实现 - 底部抽屉示例 | Complete Transparent Modal - Bottom Sheet Example
  import React from 'react';
  import {
    View,
    Text,
    TouchableOpacity,
    StyleSheet,
    Animated,
  } from 'react-native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function TransparentModalNavigator() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="Main" component={MainScreen} />

        {/* 透明模态配置 | Transparent Modal Configuration */}
        <Stack.Screen
          name="BottomSheet"
          component={BottomSheetModal}
          options={{
            presentation: 'transparentModal', // 关键：透明模态 | Key: transparent modal
            headerShown: false, // 不显示header | No header
            animation: 'fade', // 淡入淡出动画 | Fade animation
            contentStyle: { backgroundColor: 'transparent' }, // 透明容器背景 | Transparent container background
          }}
        />
      </Stack.Navigator>
    );
  }

  // 底部抽屉模态组件 | Bottom Sheet Modal Component
  function BottomSheetModal({ navigation }) {
    const slideAnim = React.useRef(new Animated.Value(300)).current; // 初始位置在屏幕下方 | Initial position below screen

    React.useEffect(() => {
      // 进入动画：从下往上滑 | Entry animation: slide up
      Animated.spring(slideAnim, {
        toValue: 0,
        useNativeDriver: true,
        tension: 65,
        friction: 11,
      }).start();
    }, []);

    const closeModal = () => {
      // 退出动画：从上往下滑 | Exit animation: slide down
      Animated.timing(slideAnim, {
        toValue: 300,
        duration: 250,
        useNativeDriver: true,
      }).start(() => {
        navigation.goBack();
      });
    };

    return (
      <View style={styles.overlay}>
        {/* 半透明背景遮罩 - 点击关闭 | Semi-transparent backdrop - Tap to dismiss */}
        <TouchableOpacity
          style={styles.backdrop}
          activeOpacity={1} // 重要：防止透明度变化 | Important: prevent opacity change
          onPress={closeModal}
        />

        {/* 实际内容区域 - 带动画 | Actual content area - with animation */}
        <Animated.View
          style={[
            styles.bottomSheet,
            { transform: [{ translateY: slideAnim }] }, // 绑定动画值 | Bind animation value
          ]}
        >
          {/* 拖动指示器 | Drag indicator */}
          <View style={styles.dragIndicator} />

          <Text style={styles.sheetTitle}>选择操作 | Select Action</Text>

          {/* 操作选项 | Action Options */}
          <TouchableOpacity
            style={styles.option}
            onPress={() => {
              console.log('编辑 | Edit');
              closeModal();
            }}
          >
            <Text style={styles.optionText}>✏️ 编辑 | Edit</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={styles.option}
            onPress={() => {
              console.log('分享 | Share');
              closeModal();
            }}
          >
            <Text style={styles.optionText}>📤 分享 | Share</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.option, styles.destructive]}
            onPress={() => {
              console.log('删除 | Delete');
              closeModal();
            }}
          >
            <Text style={[styles.optionText, styles.destructiveText]}>
              🗑️ 删除 | Delete
            </Text>
          </TouchableOpacity>

          {/* 取消按钮 | Cancel Button */}
          <TouchableOpacity
            style={styles.cancelButton}
            onPress={closeModal}
          >
            <Text style={styles.cancelText}>取消 | Cancel</Text>
          </TouchableOpacity>
        </Animated.View>
      </View>
    );
  }

  // 主屏幕 - 触发透明模态 | Main Screen - Trigger Transparent Modal
  function MainScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.mainTitle}>透明模态示例 | Transparent Modal Example</Text>
        <TouchableOpacity
          style={styles.button}
          onPress={() => navigation.navigate('BottomSheet')}
        >
          <Text style={styles.buttonText}>显示底部操作菜单 | Show Bottom Action Sheet</Text>
        </TouchableOpacity>
      </View>
    );
  }

  const styles = StyleSheet.create({
    // 覆盖层样式 | Overlay Styles
    overlay: {
      flex: 1,
      justifyContent: 'flex-end', // 内容对齐到底部 | Align content to bottom
    },
    backdrop: {
      ...StyleSheet.absoluteFillObject, // 填满整个屏幕 | Fill entire screen
      backgroundColor: 'rgba(0, 0, 0, 0.5)', // 半透明黑色 | Semi-transparent black
    },

    // 底部抽屉样式 | Bottom Sheet Styles
    bottomSheet: {
      backgroundColor: 'white',
      borderTopLeftRadius: 20,
      borderTopRightRadius: 20,
      paddingHorizontal: 20,
      paddingBottom: 34, // 考虑iOS安全区域 | Account for iOS safe area
      paddingTop: 20,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: -3 },
      shadowOpacity: 0.1,
      shadowRadius: 5,
      elevation: 5,
    },
    dragIndicator: {
      width: 40,
      height: 4,
      backgroundColor: '#D1D5DB',
      borderRadius: 2,
      alignSelf: 'center',
      marginBottom: 20,
    },
    sheetTitle: {
      fontSize: 18,
      fontWeight: '600',
      marginBottom: 15,
      textAlign: 'center',
    },

    // 选项样式 | Option Styles
    option: {
      paddingVertical: 15,
      borderBottomWidth: 1,
      borderBottomColor: '#E5E7EB',
    },
    optionText: {
      fontSize: 16,
      color: '#111827',
    },
    destructive: {
      borderBottomWidth: 0,
    },
    destructiveText: {
      color: '#EF4444',
    },

    // 取消按钮 | Cancel Button
    cancelButton: {
      marginTop: 15,
      paddingVertical: 15,
      backgroundColor: '#F3F4F6',
      borderRadius: 10,
      alignItems: 'center',
    },
    cancelText: {
      fontSize: 16,
      fontWeight: '600',
      color: '#6B7280',
    },

    // 主屏幕样式 | Main Screen Styles
    container: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    mainTitle: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 30,
    },
    button: {
      backgroundColor: '#3B82F6',
      paddingHorizontal: 30,
      paddingVertical: 15,
      borderRadius: 10,
    },
    buttonText: {
      color: 'white',
      fontSize: 16,
      fontWeight: '600',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么`backdrop`使用`StyleSheet.absoluteFillObject`？| Why does `backdrop` use `StyleSheet.absoluteFillObject`?
    **答案 | Answer:** 让背景遮罩覆盖整个屏幕，包括安全区域 | Makes backdrop cover entire screen including safe areas
  - 进入和退出动画为什么使用不同的动画类型（spring vs timing）？| Why use different animation types for entry (spring) and exit (timing)?
    **答案 | Answer:** Spring动画有弹性效果更自然，timing动画退出更快速流畅 | Spring has bouncy natural feel, timing is quicker for exit
  - `dragIndicator`的作用是什么？| What's the purpose of `dragIndicator`?
    **答案 | Answer:** 视觉提示用户可以拖动关闭（iOS常见设计模式）| Visual hint that users can drag to dismiss (common iOS pattern)

  **常见误区检查 | Common Misconception Checks:**
  - "使用`transparentModal`后不需要设置`contentStyle: { backgroundColor: 'transparent' }`" | "No need to set `contentStyle: { backgroundColor: 'transparent' }` with `transparentModal`"
    **答案 | Answer:** 错误 | False - 虽然叫透明模态，但容器默认可能有背景色，需要显式设置透明
  - "透明模态的动画必须手动实现" | "Animations in transparent modals must be manually implemented"
    **答案 | Answer:** 部分正确 | Partially true - 可以使用内置`animation`选项，但自定义效果（如滑入）需要手动动画

### 5. Push vs Modal：选择正确的导航方式 | Push vs Modal: Choosing the Right Navigation (45分钟 | 45 minutes)

- **决策框架 | Decision Framework**

  **概念定义 | Concept Definition:**
  在React Native导航中，选择使用`navigation.push()`（推送）还是模态展示是一个重要的UX决策。Push适合线性的、有层级关系的导航流程，而Modal适合临时的、可中断的任务。正确的选择能显著提升用户体验和应用的导航清晰度。

  In React Native navigation, choosing between `navigation.push()` (push) and modal presentation is an important UX decision. Push is suitable for linear, hierarchical navigation flows, while modals fit temporary, interruptible tasks. The right choice significantly improves user experience and navigation clarity.

  **核心特征对比 | Key Characteristics Comparison:**
  - **Push导航 | Push Navigation**:
    - 用于主导航流程，屏幕间有明确的父子层级关系
    - 保留完整的导航历史，用户可以逐级返回
    - 视觉上从右侧滑入（或平台默认方向），表示"深入"内容
    - 示例：列表 → 详情 → 评论 → 用户资料

  - **Modal展示 | Modal Presentation**:
    - 用于临时任务，与主流程相对独立
    - 需要用户完成或取消任务，不是导航历史的一部分
    - 从底部滑入（iOS）或覆盖，表示"临时层"
    - 示例：创建内容、筛选选项、快速预览、确认对话框

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 用户查看文章详情后点击作者头像进入作者主页，应该用push还是modal？| When user taps author avatar from article detail to view author profile, use push or modal?
     **答案 | Answer:** Push - 这是主导航流程的一部分，有明确的层级关系 | Push - part of main navigation flow with clear hierarchy
  2. 用户在文章列表页点击"写新文章"按钮，应该用push还是modal？| When user taps "Write New Article" button from article list, use push or modal?
     **答案 | Answer:** Modal - 创建内容是临时任务，完成后返回列表 | Modal - content creation is temporary task, returns to list after completion
  3. 电商应用中，从商品列表 → 商品详情 → 评论列表，应该全部用push吗？| In e-commerce app, from product list → product detail → reviews list, all use push?
     **答案 | Answer:** 是的 | Yes - 这是一个连续的浏览流程，用户期望能逐级返回
  4. 什么情况下可以在modal内部使用push导航？| When can you use push navigation inside a modal?
     **答案 | Answer:** 当模态包含多步骤流程时（如多页表单、引导流程）| When modal contains multi-step flow (like multi-page form, onboarding)

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // Push vs Modal 决策示例 | Push vs Modal Decision Examples
  import React from 'react';
  import { View, Text, Button, FlatList, TouchableOpacity } from 'react-native';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Stack = createNativeStackNavigator();

  function NavigationPatternsDemo() {
    return (
      <Stack.Navigator>
        {/* 主屏幕组 - 使用Push导航 | Main Screens Group - Use Push Navigation */}
        <Stack.Group>
          <Stack.Screen
            name="ArticleList"
            component={ArticleListScreen}
            options={{ title: '文章列表 | Article List' }}
          />
          {/* Push: 主流程的下一级 | Push: Next level in main flow */}
          <Stack.Screen
            name="ArticleDetail"
            component={ArticleDetailScreen}
            options={{ title: '文章详情 | Article Detail' }}
          />
          {/* Push: 继续深入层级 | Push: Continue deeper hierarchy */}
          <Stack.Screen
            name="AuthorProfile"
            component={AuthorProfileScreen}
            options={{ title: '作者资料 | Author Profile' }}
          />
        </Stack.Group>

        {/* 模态屏幕组 - 临时任务 | Modal Screens Group - Temporary Tasks */}
        <Stack.Group screenOptions={{ presentation: 'modal' }}>
          {/* Modal: 创建内容 | Modal: Content creation */}
          <Stack.Screen
            name="CreateArticle"
            component={CreateArticleScreen}
            options={{
              title: '写文章 | Write Article',
              headerLeft: () => <Button title="取消 | Cancel" />,
              headerRight: () => <Button title="发布 | Publish" />,
            }}
          />
          {/* Modal: 筛选和设置 | Modal: Filter and settings */}
          <Stack.Screen
            name="FilterOptions"
            component={FilterOptionsScreen}
            options={{ title: '筛选 | Filter' }}
          />
        </Stack.Group>

        {/* 透明模态组 - 轻量级交互 | Transparent Modal Group - Lightweight Interactions */}
        <Stack.Group screenOptions={{ presentation: 'transparentModal', headerShown: false }}>
          <Stack.Screen
            name="ShareSheet"
            component={ShareSheetScreen}
          />
          <Stack.Screen
            name="ConfirmDialog"
            component={ConfirmDialogScreen}
          />
        </Stack.Group>
      </Stack.Navigator>
    );
  }

  // 文章列表 - 演示Push和Modal的触发 | Article List - Demonstrate Push and Modal Triggers
  function ArticleListScreen({ navigation }) {
    const articles = [
      { id: '1', title: 'React Native 导航最佳实践 | RN Navigation Best Practices' },
      { id: '2', title: '模态屏幕设计指南 | Modal Screen Design Guide' },
    ];

    return (
      <View style={{ flex: 1 }}>
        {/* Push触发：查看详情 | Push Trigger: View Detail */}
        <FlatList
          data={articles}
          renderItem={({ item }) => (
            <TouchableOpacity
              style={{ padding: 15, borderBottomWidth: 1, borderColor: '#ddd' }}
              onPress={() =>
                navigation.push('ArticleDetail', { articleId: item.id }) // Push - 主流程导航 | Push - main flow navigation
              }
            >
              <Text style={{ fontSize: 16 }}>{item.title}</Text>
            </TouchableOpacity>
          )}
          keyExtractor={(item) => item.id}
        />

        {/* Modal触发：创建新文章 | Modal Trigger: Create New Article */}
        <Button
          title="+ 写新文章 | + Write New Article"
          onPress={() =>
            navigation.navigate('CreateArticle') // Modal - 临时任务 | Modal - temporary task
          }
        />
      </View>
    );
  }

  // 文章详情 - 演示从详情页继续Push或打开Modal | Article Detail - Continue Push or Open Modal
  function ArticleDetailScreen({ navigation, route }) {
    return (
      <View style={{ flex: 1, padding: 20 }}>
        <Text style={{ fontSize: 18, marginBottom: 20 }}>
          文章ID: {route.params.articleId}
        </Text>

        {/* Push: 查看作者资料（主流程的延续）| Push: View author profile (main flow continuation) */}
        <Button
          title="查看作者 | View Author"
          onPress={() => navigation.push('AuthorProfile', { authorId: 'author123' })}
        />

        {/* Modal: 分享文章（临时操作）| Modal: Share article (temporary action) */}
        <Button
          title="分享 | Share"
          onPress={() => navigation.navigate('ShareSheet')}
        />

        {/* TransparentModal: 确认删除（快速交互）| TransparentModal: Confirm delete (quick interaction) */}
        <Button
          title="删除 | Delete"
          color="red"
          onPress={() => navigation.navigate('ConfirmDialog', { action: 'delete' })}
        />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么"写新文章"使用Modal而不是Push？| Why use Modal instead of Push for "Write New Article"?
    **答案 | Answer:** 创建内容是独立任务，完成后回到列表，不是浏览流程的一部分 | Content creation is independent task, returns to list, not part of browsing flow
  - 如果用户从文章详情 → 作者资料 → 作者的其他文章，这个流程合理吗？| Is it reasonable for user to go: article detail → author profile → author's other articles?
    **答案 | Answer:** 合理，全部使用Push，形成清晰的导航历史 | Reasonable, all use Push, forming clear navigation history

  **常见误区检查 | Common Misconception Checks:**
  - "所有创建/编辑操作都应该使用Modal" | "All create/edit operations should use modals"
    **答案 | Answer:** 不完全正确 | Not entirely true - 如果是主流程的一部分（如多步注册），应使用Push；只有独立任务用Modal
  - "Modal只能从主屏幕触发" | "Modals can only be triggered from main screens"
    **答案 | Answer:** 错误 | False - Modal可以从任何屏幕触发，包括其他Modal内部（嵌套Modal需谨慎使用）

### 6. 知识巩固与实践检查 | Knowledge Consolidation and Practice Review (30分钟 | 30 minutes)

- **综合概念检查 | Comprehensive Concept Check**

  1. **模态屏幕的三个核心特征是什么？| What are the three core characteristics of modal screens?**
     **答案 | Answer:** 临时性、视觉覆盖层级、明确的关闭方式 | Temporary nature, visual overlay hierarchy, explicit dismissal methods

  2. **`presentation: 'modal'`和`presentation: 'transparentModal'`的使用场景有何不同？| How do use cases differ between `presentation: 'modal'` and `presentation: 'transparentModal'`?**
     **答案 | Answer:** modal用于全屏/半屏任务（创建内容），transparentModal用于轻量级交互（底部菜单、对话框）| modal for fullscreen/half-screen tasks (content creation), transparentModal for lightweight interactions (bottom sheets, dialogs)

  3. **在什么情况下应该禁用模态的手势关闭（`gestureEnabled: false`）？| When should you disable gesture dismissal for modals (`gestureEnabled: false`)?**
     **答案 | Answer:** 有未保存更改、支付流程、强制引导等需要用户明确决策的场景 | Scenarios with unsaved changes, payment flows, forced onboarding requiring explicit user decision

  4. **如何在透明模态中实现点击背景关闭的功能？| How to implement tap-to-dismiss on background in transparent modals?**
     **答案 | Answer:** 使用`TouchableOpacity`包裹背景，设置`activeOpacity={1}`和`onPress={navigation.goBack}` | Wrap background with `TouchableOpacity`, set `activeOpacity={1}` and `onPress={navigation.goBack}`

  5. **全屏模态内部可以包含导航堆栈吗？为什么？| Can fullscreen modals contain navigation stacks? Why?**
     **答案 | Answer:** 可以 | Yes - 用于多步骤流程（如引导、表单），每步有自己的屏幕但共享模态的关闭逻辑 | For multi-step flows (onboarding, forms) where each step has own screen but shares modal dismissal logic

## 实践项目：多场景模态应用 | Practical Project: Multi-Scenario Modal Application

### 目标 | Objective
构建一个综合演示应用，展示不同类型模态屏幕的实际应用场景，包括：
1. iOS风格的从底部滑出创建内容模态
2. 全屏模态包含多步骤流程
3. 透明模态实现底部操作菜单和确认对话框
4. 正确区分Push和Modal导航

Build a comprehensive demo app showcasing practical use cases for different modal screen types, including:
1. iOS-style bottom slide-up content creation modal
2. Fullscreen modal with multi-step flow
3. Transparent modals for bottom action sheets and confirmation dialogs
4. Correct differentiation between Push and Modal navigation

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解：

1. **模态屏幕和普通屏幕的主要区别是什么？| What's the main difference between modal and regular screens?**
   **答案 | Answer:** 模态是临时任务层，需要完成/取消后关闭；普通屏幕是主导航流程的一部分 | Modals are temporary task layers requiring completion/cancellation; regular screens are part of main navigation flow

2. **何时使用`presentation: 'transparentModal'`？| When to use `presentation: 'transparentModal'`?**
   **答案 | Answer:** 需要半透明背景、轻量级交互（如底部菜单、对话框），且希望显示底层内容时 | When needing semi-transparent background, lightweight interactions (bottom sheets, dialogs), and showing underlying content

3. **如何为模态添加适当的关闭机制？| How to add appropriate dismissal mechanisms to modals?**
   **答案 | Answer:** iOS：左上角Cancel/Done按钮 + 手势下滑；Android：返回键 + 右上角X按钮；透明模态：点击背景 | iOS: top-left Cancel/Done + swipe gesture; Android: back button + top-right X; Transparent: tap background

### 步骤 | Steps

1. **项目初始化与导航结构 | Project Initialization and Navigation Structure**
   - 创建React Native项目并安装`@react-navigation/native`和`@react-navigation/native-stack`
   - 设置根导航器，包含主屏幕组、模态组和透明模态组
   - 使用`Stack.Group`组织不同类型的屏幕

2. **实现iOS风格创建内容模态 | Implement iOS-Style Content Creation Modal**
   - 配置`presentation: 'modal'`
   - 添加header左右两侧的"取消"和"发布"按钮
   - 实现简单的表单输入（标题、内容）
   - 验证手势下滑关闭功能（iOS）

3. **构建全屏多步骤模态流程 | Build Fullscreen Multi-Step Modal Flow**
   - 创建引导流程模态（3个步骤）
   - 在模态内部嵌套`Stack.Navigator`
   - 第一步显示"跳过"按钮，最后一步显示"完成"按钮
   - 使用`navigation.getParent()?.goBack()`关闭整个模态流程

4. **实现透明模态交互 | Implement Transparent Modal Interactions**
   - **底部操作菜单**：
     - 配置`transparentModal`展示样式
     - 实现半透明背景遮罩（`rgba(0,0,0,0.5)`）
     - 添加从底部滑入的动画（使用`Animated`）
     - 实现点击背景关闭功能
   - **确认对话框**：
     - 内容居中显示
     - 包含"确认"和"取消"两个操作
     - 点击背景或取消按钮关闭

5. **演示Push vs Modal的正确使用 | Demonstrate Correct Push vs Modal Usage**
   - 主列表 → 详情页（Push）
   - 详情页 → 评论列表（Push，继续深入）
   - 任意页面 → 创建内容（Modal，临时任务）
   - 任意页面 → 操作菜单（TransparentModal，快速交互）

### 示例代码 | Example Code

```jsx
/**
 * 模态屏幕综合示例项目 | Comprehensive Modal Screens Example Project
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - iOS风格模态（card, modal, transparentModal）| iOS-style modals
 * - 全屏多步骤模态流程 | Fullscreen multi-step modal flows
 * - 透明模态交互（底部菜单、对话框）| Transparent modal interactions
 * - Push vs Modal的正确选择 | Correct Push vs Modal choices
 */

import React, { useState, useRef, useEffect } from 'react';
import {
  View,
  Text,
  Button,
  FlatList,
  TouchableOpacity,
  TextInput,
  StyleSheet,
  Animated,
  Platform,
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { SafeAreaView } from 'react-native-safe-area-context';

const Stack = createNativeStackNavigator();
const ModalStack = createNativeStackNavigator();

// ==================== 主导航器 | Main Navigator ====================
export default function App() {
  return (
    <NavigationContainer>
      <RootNavigator />
    </NavigationContainer>
  );
}

function RootNavigator() {
  return (
    <Stack.Navigator>
      {/* ===== 主屏幕组 - Push导航 | Main Screens - Push Navigation ===== */}
      <Stack.Group>
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: '首页 | Home' }}
        />
        <Stack.Screen
          name="ItemDetail"
          component={ItemDetailScreen}
          options={{ title: '详情 | Detail' }}
        />
        <Stack.Screen
          name="Comments"
          component={CommentsScreen}
          options={{ title: '评论 | Comments' }}
        />
      </Stack.Group>

      {/* ===== 模态屏幕组 - Modal展示 | Modal Screens - Modal Presentation ===== */}
      <Stack.Group screenOptions={{ presentation: 'modal' }}>
        <Stack.Screen
          name="CreateItem"
          component={CreateItemScreen}
          options={{
            title: '创建项目 | Create Item',
            headerLeft: ({ tintColor }) => (
              <Button title="取消 | Cancel" color={tintColor} onPress={() => navigation.goBack()} />
            ),
          }}
        />
        <Stack.Screen
          name="OnboardingFlow"
          component={OnboardingFlowNavigator}
          options={{
            headerShown: false,
            presentation: 'fullScreenModal',
          }}
        />
      </Stack.Group>

      {/* ===== 透明模态组 | Transparent Modal Group ===== */}
      <Stack.Group
        screenOptions={{
          presentation: 'transparentModal',
          headerShown: false,
          animation: 'fade',
        }}
      >
        <Stack.Screen name="ActionSheet" component={ActionSheetModal} />
        <Stack.Screen name="ConfirmDialog" component={ConfirmDialogModal} />
      </Stack.Group>
    </Stack.Navigator>
  );
}

// ==================== 引导流程导航器 | Onboarding Flow Navigator ====================
function OnboardingFlowNavigator() {
  return (
    <ModalStack.Navigator>
      <ModalStack.Screen
        name="Step1"
        component={OnboardingStep1}
        options={({ navigation }) => ({
          title: '步骤 1/3 | Step 1/3',
          headerLeft: () => (
            <Button
              title="跳过 | Skip"
              onPress={() => navigation.getParent()?.goBack()}
            />
          ),
        })}
      />
      <ModalStack.Screen
        name="Step2"
        component={OnboardingStep2}
        options={{ title: '步骤 2/3 | Step 2/3' }}
      />
      <ModalStack.Screen
        name="Step3"
        component={OnboardingStep3}
        options={({ navigation }) => ({
          title: '步骤 3/3 | Step 3/3',
          headerLeft: () => null,
          headerRight: () => (
            <Button
              title="完成 | Done"
              onPress={() => navigation.getParent()?.goBack()}
            />
          ),
        })}
      />
    </ModalStack.Navigator>
  );
}

// ==================== 主屏幕 | Home Screen ====================
function HomeScreen({ navigation }) {
  const items = [
    { id: '1', title: '模态屏幕设计指南 | Modal Design Guide' },
    { id: '2', title: 'React Navigation 最佳实践 | React Navigation Best Practices' },
    { id: '3', title: 'iOS 人机界面指南 | iOS HIG' },
  ];

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={items}
        renderItem={({ item }) => (
          <TouchableOpacity
            style={styles.listItem}
            onPress={() => navigation.push('ItemDetail', { item })} // Push - 主流程 | Push - main flow
          >
            <Text style={styles.itemTitle}>{item.title}</Text>
            <Text style={styles.arrow}>›</Text>
          </TouchableOpacity>
        )}
        keyExtractor={(item) => item.id}
      />

      <View style={styles.actionButtons}>
        {/* Modal - 创建内容 | Modal - Create content */}
        <TouchableOpacity
          style={styles.primaryButton}
          onPress={() => navigation.navigate('CreateItem')}
        >
          <Text style={styles.buttonText}>+ 创建新项目 | + Create New Item</Text>
        </TouchableOpacity>

        {/* FullScreen Modal - 引导流程 | FullScreen Modal - Onboarding */}
        <TouchableOpacity
          style={styles.secondaryButton}
          onPress={() => navigation.navigate('OnboardingFlow')}
        >
          <Text style={styles.secondaryButtonText}>查看引导 | View Onboarding</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
}

// ==================== 详情屏幕 | Detail Screen ====================
function ItemDetailScreen({ navigation, route }) {
  const { item } = route.params;

  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.detailContent}>
        <Text style={styles.detailTitle}>{item.title}</Text>
        <Text style={styles.detailBody}>
          这是详情页的内容。您可以向下滚动查看更多信息，或使用下方的按钮进行操作。
          {'\n\n'}
          This is the detail page content. You can scroll down for more information or use the buttons below.
        </Text>

        {/* Push - 继续深入 | Push - Continue deeper */}
        <Button
          title="查看评论 (Push) | View Comments (Push)"
          onPress={() => navigation.push('Comments', { itemId: item.id })}
        />

        {/* TransparentModal - 快速操作 | TransparentModal - Quick action */}
        <Button
          title="更多操作... | More Actions..."
          onPress={() => navigation.navigate('ActionSheet', { item })}
        />
      </View>
    </SafeAreaView>
  );
}

// ==================== 评论屏幕 | Comments Screen ====================
function CommentsScreen({ route }) {
  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.sectionTitle}>评论列表 | Comments List</Text>
      <Text style={styles.hint}>
        这是通过Push导航到达的评论页，用户可以使用返回按钮逐级返回。
        {'\n\n'}
        This is the comments page reached via Push navigation. Users can navigate back step by step.
      </Text>
    </SafeAreaView>
  );
}

// ==================== 创建项目模态 | Create Item Modal ====================
function CreateItemScreen({ navigation }) {
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');

  const handlePublish = () => {
    console.log('发布 | Publishing:', { title, content });
    navigation.goBack();
  };

  return (
    <SafeAreaView style={styles.modalContainer}>
      <View style={styles.formContainer}>
        <Text style={styles.label}>标题 | Title</Text>
        <TextInput
          style={styles.input}
          placeholder="输入标题... | Enter title..."
          value={title}
          onChangeText={setTitle}
        />

        <Text style={styles.label}>内容 | Content</Text>
        <TextInput
          style={[styles.input, styles.textArea]}
          placeholder="输入内容... | Enter content..."
          multiline
          value={content}
          onChangeText={setContent}
        />

        <Button title="发布 | Publish" onPress={handlePublish} />
      </View>
    </SafeAreaView>
  );
}

// ==================== 引导流程屏幕 | Onboarding Flow Screens ====================
function OnboardingStep1({ navigation }) {
  return (
    <SafeAreaView style={styles.onboardingContainer}>
      <Text style={styles.onboardingTitle}>欢迎使用 | Welcome</Text>
      <Text style={styles.onboardingText}>这是第一步引导内容 | This is step 1 content</Text>
      <Button title="下一步 | Next" onPress={() => navigation.navigate('Step2')} />
    </SafeAreaView>
  );
}

function OnboardingStep2({ navigation }) {
  return (
    <SafeAreaView style={styles.onboardingContainer}>
      <Text style={styles.onboardingTitle}>功能介绍 | Features</Text>
      <Text style={styles.onboardingText}>这是第二步引导内容 | This is step 2 content</Text>
      <Button title="下一步 | Next" onPress={() => navigation.navigate('Step3')} />
    </SafeAreaView>
  );
}

function OnboardingStep3() {
  return (
    <SafeAreaView style={styles.onboardingContainer}>
      <Text style={styles.onboardingTitle}>开始使用 | Get Started</Text>
      <Text style={styles.onboardingText}>
        这是最后一步，点击右上角"完成"开始使用 | Final step, tap "Done" to start
      </Text>
    </SafeAreaView>
  );
}

// ==================== 底部操作菜单（透明模态）| Action Sheet (Transparent Modal) ====================
function ActionSheetModal({ navigation, route }) {
  const slideAnim = useRef(new Animated.Value(300)).current;
  const { item } = route.params || {};

  useEffect(() => {
    Animated.spring(slideAnim, {
      toValue: 0,
      tension: 65,
      friction: 11,
      useNativeDriver: true,
    }).start();
  }, []);

  const closeSheet = () => {
    Animated.timing(slideAnim, {
      toValue: 300,
      duration: 250,
      useNativeDriver: true,
    }).start(() => navigation.goBack());
  };

  const handleAction = (action) => {
    console.log(`操作 | Action: ${action} on item:`, item);
    if (action === 'delete') {
      navigation.navigate('ConfirmDialog', {
        title: '确认删除 | Confirm Delete',
        message: `确定要删除"${item?.title}"吗？| Are you sure to delete "${item?.title}"?`,
        onConfirm: () => {
          console.log('已删除 | Deleted');
          navigation.goBack();
        },
      });
    } else {
      closeSheet();
    }
  };

  return (
    <View style={styles.overlay}>
      <TouchableOpacity style={styles.backdrop} activeOpacity={1} onPress={closeSheet} />
      <Animated.View style={[styles.bottomSheet, { transform: [{ translateY: slideAnim }] }]}>
        <View style={styles.dragIndicator} />
        <Text style={styles.sheetTitle}>选择操作 | Select Action</Text>

        <TouchableOpacity style={styles.sheetOption} onPress={() => handleAction('edit')}>
          <Text style={styles.sheetOptionText}>✏️ 编辑 | Edit</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.sheetOption} onPress={() => handleAction('share')}>
          <Text style={styles.sheetOptionText}>📤 分享 | Share</Text>
        </TouchableOpacity>

        <TouchableOpacity
          style={[styles.sheetOption, styles.destructiveOption]}
          onPress={() => handleAction('delete')}
        >
          <Text style={[styles.sheetOptionText, styles.destructiveText]}>🗑️ 删除 | Delete</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.cancelButton} onPress={closeSheet}>
          <Text style={styles.cancelText}>取消 | Cancel</Text>
        </TouchableOpacity>
      </Animated.View>
    </View>
  );
}

// ==================== 确认对话框（透明模态）| Confirm Dialog (Transparent Modal) ====================
function ConfirmDialogModal({ navigation, route }) {
  const { title, message, onConfirm } = route.params || {};
  const scaleAnim = useRef(new Animated.Value(0.9)).current;
  const opacityAnim = useRef(new Animated.Value(0)).current;

  useEffect(() => {
    Animated.parallel([
      Animated.spring(scaleAnim, {
        toValue: 1,
        tension: 100,
        useNativeDriver: true,
      }),
      Animated.timing(opacityAnim, {
        toValue: 1,
        duration: 200,
        useNativeDriver: true,
      }),
    ]).start();
  }, []);

  const handleConfirm = () => {
    onConfirm?.();
    navigation.goBack();
  };

  return (
    <View style={styles.dialogOverlay}>
      <TouchableOpacity
        style={styles.dialogBackdrop}
        activeOpacity={1}
        onPress={() => navigation.goBack()}
      />
      <Animated.View
        style={[
          styles.dialogBox,
          {
            transform: [{ scale: scaleAnim }],
            opacity: opacityAnim,
          },
        ]}
      >
        <Text style={styles.dialogTitle}>{title || '确认 | Confirm'}</Text>
        <Text style={styles.dialogMessage}>{message || '确定要执行此操作吗？| Confirm this action?'}</Text>

        <View style={styles.dialogButtons}>
          <TouchableOpacity
            style={[styles.dialogButton, styles.cancelDialogButton]}
            onPress={() => navigation.goBack()}
          >
            <Text style={styles.cancelDialogText}>取消 | Cancel</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.dialogButton, styles.confirmDialogButton]}
            onPress={handleConfirm}
          >
            <Text style={styles.confirmDialogText}>确认 | Confirm</Text>
          </TouchableOpacity>
        </View>
      </Animated.View>
    </View>
  );
}

// ==================== 样式 | Styles ====================
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  modalContainer: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },

  // 列表样式 | List Styles
  listItem: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    padding: 16,
    borderBottomWidth: 1,
    borderBottomColor: '#E5E7EB',
  },
  itemTitle: {
    fontSize: 16,
    color: '#111827',
  },
  arrow: {
    fontSize: 20,
    color: '#9CA3AF',
  },

  // 按钮样式 | Button Styles
  actionButtons: {
    padding: 20,
    gap: 10,
  },
  primaryButton: {
    backgroundColor: '#3B82F6',
    padding: 15,
    borderRadius: 10,
    alignItems: 'center',
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: '600',
  },
  secondaryButton: {
    backgroundColor: '#E5E7EB',
    padding: 15,
    borderRadius: 10,
    alignItems: 'center',
  },
  secondaryButtonText: {
    color: '#374151',
    fontSize: 16,
    fontWeight: '600',
  },

  // 详情页样式 | Detail Styles
  detailContent: {
    padding: 20,
    gap: 15,
  },
  detailTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#111827',
  },
  detailBody: {
    fontSize: 16,
    lineHeight: 24,
    color: '#4B5563',
  },

  // 表单样式 | Form Styles
  formContainer: {
    padding: 20,
    gap: 15,
  },
  label: {
    fontSize: 16,
    fontWeight: '600',
    color: '#374151',
  },
  input: {
    backgroundColor: 'white',
    borderRadius: 8,
    padding: 12,
    fontSize: 16,
    borderWidth: 1,
    borderColor: '#D1D5DB',
  },
  textArea: {
    minHeight: 120,
    textAlignVertical: 'top',
  },

  // 引导流程样式 | Onboarding Styles
  onboardingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  onboardingTitle: {
    fontSize: 28,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  onboardingText: {
    fontSize: 16,
    textAlign: 'center',
    marginBottom: 30,
    color: '#6B7280',
  },

  // 底部菜单样式 | Bottom Sheet Styles
  overlay: {
    flex: 1,
    justifyContent: 'flex-end',
  },
  backdrop: {
    ...StyleSheet.absoluteFillObject,
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
  },
  bottomSheet: {
    backgroundColor: 'white',
    borderTopLeftRadius: 20,
    borderTopRightRadius: 20,
    paddingHorizontal: 20,
    paddingBottom: Platform.OS === 'ios' ? 34 : 20,
    paddingTop: 20,
  },
  dragIndicator: {
    width: 40,
    height: 4,
    backgroundColor: '#D1D5DB',
    borderRadius: 2,
    alignSelf: 'center',
    marginBottom: 20,
  },
  sheetTitle: {
    fontSize: 18,
    fontWeight: '600',
    marginBottom: 15,
    textAlign: 'center',
  },
  sheetOption: {
    paddingVertical: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#E5E7EB',
  },
  sheetOptionText: {
    fontSize: 16,
    color: '#111827',
  },
  destructiveOption: {
    borderBottomWidth: 0,
  },
  destructiveText: {
    color: '#EF4444',
  },
  cancelButton: {
    marginTop: 15,
    paddingVertical: 15,
    backgroundColor: '#F3F4F6',
    borderRadius: 10,
    alignItems: 'center',
  },
  cancelText: {
    fontSize: 16,
    fontWeight: '600',
    color: '#6B7280',
  },

  // 对话框样式 | Dialog Styles
  dialogOverlay: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  dialogBackdrop: {
    ...StyleSheet.absoluteFillObject,
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
  },
  dialogBox: {
    backgroundColor: 'white',
    borderRadius: 15,
    padding: 20,
    width: '100%',
    maxWidth: 340,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 10,
    elevation: 5,
  },
  dialogTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 10,
    textAlign: 'center',
  },
  dialogMessage: {
    fontSize: 16,
    color: '#6B7280',
    textAlign: 'center',
    marginBottom: 20,
  },
  dialogButtons: {
    flexDirection: 'row',
    gap: 10,
  },
  dialogButton: {
    flex: 1,
    paddingVertical: 12,
    borderRadius: 8,
    alignItems: 'center',
  },
  cancelDialogButton: {
    backgroundColor: '#E5E7EB',
  },
  cancelDialogText: {
    color: '#374151',
    fontWeight: '600',
  },
  confirmDialogButton: {
    backgroundColor: '#EF4444',
  },
  confirmDialogText: {
    color: 'white',
    fontWeight: '600',
  },

  // 通用样式 | Common Styles
  sectionTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    padding: 20,
  },
  hint: {
    fontSize: 14,
    color: '#6B7280',
    padding: 20,
    lineHeight: 20,
  },
});
```

### 项目完成检查 | Project Completion Check
1. ✅ iOS风格模态是否正确实现从底部滑入效果？| Is iOS-style modal correctly implemented with bottom slide-in?
2. ✅ 用户能否在iOS上通过向下手势关闭模态？| Can users dismiss modals with swipe down on iOS?
3. ✅ 全屏引导流程是否包含多个屏幕且可以正确关闭整个流程？| Does fullscreen onboarding contain multiple screens with correct flow dismissal?
4. ✅ 透明模态的背景是否半透明且可点击关闭？| Is transparent modal background semi-transparent and tap-to-dismiss?
5. ✅ Push和Modal的使用是否符合最佳实践（列表→详情用Push，创建内容用Modal）？| Does Push vs Modal usage follow best practices?
6. ✅ 底部操作菜单是否实现了滑入动画？| Does bottom action sheet implement slide-in animation?
7. ✅ 确认对话框是否居中显示且有缩放动画？| Is confirm dialog centered with scale animation?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **模态屏幕设计模式研究 | Modal Screen Design Pattern Research**
   - **练习描述 | Exercise Description:** 研究并总结iOS HIG和Material Design中关于模态屏幕的设计指南，对比两者的差异
   - **概念检查 | Concept Check:**
     - iOS和Android的模态展示方式有何不同？| How do iOS and Android modal presentations differ?
     - 什么时候应该遵循平台特定的设计模式？| When should you follow platform-specific design patterns?
   - **学习目标 | Learning Objective:** 理解跨平台设计的权衡，能够做出合理的UX决策

2. **手势交互实现练习 | Gesture Interaction Implementation Exercise**
   - **练习描述 | Exercise Description:** 使用`react-native-gesture-handler`为透明模态添加向下拖动关闭功能（类似iOS原生效果）
   - **概念检查 | Concept Check:**
     - 手势关闭和按钮关闭在用户体验上有何不同？| How do gesture dismissal and button dismissal differ in UX?
     - 何时应该禁用手势关闭？| When should gesture dismissal be disabled?
   - **学习目标 | Learning Objective:** 掌握高级手势处理，提升模态交互的流畅度

3. **模态状态管理练习 | Modal State Management Exercise**
   - **练习描述 | Exercise Description:** 使用Context或状态管理库（Zustand）管理全局模态状态，实现从任意位置触发模态
   - **概念检查 | Concept Check:**
     - 为什么有时需要在导航之外管理模态状态？| Why sometimes need to manage modal state outside navigation?
     - 如何避免模态状态和导航状态冲突？| How to avoid conflicts between modal and navigation state?
   - **学习目标 | Learning Objective:** 理解导航与状态管理的集成，处理复杂的应用状态

4. **无障碍访问优化练习 | Accessibility Optimization Exercise**
   - **练习描述 | Exercise Description:** 为模态屏幕添加无障碍支持（屏幕阅读器公告、焦点管理、键盘导航）
   - **概念检查 | Concept Check:**
     - 模态打开时应该如何管理焦点？| How should focus be managed when modal opens?
     - 屏幕阅读器用户如何知道模态已打开？| How do screen reader users know modal is open?
   - **学习目标 | Learning Objective:** 理解移动端无障碍设计，确保所有用户都能使用模态

5. **性能优化练习 | Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 使用`React.memo`和`useMemo`优化模态性能，特别是包含复杂内容的透明模态
   - **概念检查 | Concept Check:**
     - 模态的哪些部分容易导致性能问题？| Which parts of modals can cause performance issues?
     - 如何避免模态打开时的卡顿？| How to avoid stuttering when modal opens?
   - **学习目标 | Learning Objective:** 掌握React Native性能优化技巧，确保流畅的模态体验

6. **跨平台一致性练习 | Cross-Platform Consistency Exercise**
   - **练习描述 | Exercise Description:** 创建一个自定义`useModal` Hook，统一iOS和Android的模态行为和样式
   - **概念检查 | Concept Check:**
     - 如何在保持平台特性的同时实现一致的UX？| How to achieve consistent UX while maintaining platform characteristics?
     - 什么情况下应该牺牲平台特性以获得一致性？| When should platform characteristics be sacrificed for consistency?
   - **学习目标 | Learning Objective:** 理解跨平台开发的挑战，创建可复用的模态解决方案

7. **真实场景应用练习 | Real-World Scenario Application Exercise**
   - **练习描述 | Exercise Description:** 重构一个现有应用的导航结构，将不合理的Push导航改为Modal（或反之）
   - **概念检查 | Concept Check:**
     - 如何识别导航模式的使用不当？| How to identify improper use of navigation patterns?
     - 重构导航对用户体验有何影响？| How does navigation refactoring impact UX?
   - **学习目标 | Learning Objective:** 能够审查和改进现有代码，将理论应用到实际项目

## 学习资源 | Learning Resources
- [React Navigation - Modal Screens 官方文档 | Official Docs](https://reactnavigation.org/docs/modal)
- [iOS Human Interface Guidelines - Modality](https://developer.apple.com/design/human-interface-guidelines/modality)
- [Material Design - Dialogs](https://m3.material.io/components/dialogs/overview)
- [React Native Gesture Handler - 手势交互 | Gesture Interactions](https://docs.swmansion.com/react-native-gesture-handler/)
- [Animated API - 高级动画 | Advanced Animations](https://reactnative.dev/docs/animated)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解模态屏幕的三个核心特征（临时性、视觉层级、显式关闭）| Understand three core modal characteristics
- [ ] 能够区分`card`、`modal`和`transparentModal`的使用场景 | Can differentiate use cases for presentation styles
- [ ] 成功实现iOS风格的从底部滑出模态 | Successfully implement iOS-style bottom sheet modal
- [ ] 掌握全屏模态中嵌套导航的实现方法 | Master nested navigation in fullscreen modals
- [ ] 能够实现透明模态的点击背景关闭功能 | Can implement tap-to-dismiss for transparent modals
- [ ] 理解Push vs Modal的决策标准并能正确应用 | Understand and correctly apply Push vs Modal decision criteria
- [ ] 为模态添加适当的关闭按钮（iOS: 左上角，Android: 右上角）| Add appropriate close buttons to modals
- [ ] 掌握使用`Stack.Group`组织不同类型屏幕的方法 | Master organizing screen types with Stack.Group
- [ ] 能够使用`Animated` API为模态添加自定义动画 | Can add custom animations to modals with Animated API
- [ ] 完成综合实践项目，包含所有模态类型 | Complete comprehensive project with all modal types

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够：
1. 向他人清晰解释模态屏幕与普通屏幕的本质区别
2. 根据业务需求快速判断应使用哪种展示样式
3. 独立实现iOS和Android平台的模态最佳实践
4. 理解透明模态的实现原理并能举一反三

Before marking as complete, ensure you can:
1. Clearly explain the essential differences between modal and regular screens to others
2. Quickly determine which presentation style to use based on requirements
3. Independently implement modal best practices for both iOS and Android
4. Understand transparent modal principles and apply them creatively
