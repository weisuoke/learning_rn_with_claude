# React Native入门 - 第35天：Bookmarks与本地存储 (Part 2) | React Native Introduction - Day 35: Bookmarks & Local Storage (Part 2)

## 详细内容（续） | Detailed Content (Continued)

### 4. 手势处理与交互优化 | Gesture Handling & Interaction Optimization (1.5小时 | 1.5 hours)

- **react-native-gesture-handler集成 | react-native-gesture-handler Integration**

  **概念定义 | Concept Definition:**
  react-native-gesture-handler是React Native的手势处理库，提供原生级别的手势识别性能。它替代了React Native内置的手势系统，支持多点触控、拖拽、滑动等复杂交互。在Expo项目中已预装，常用于实现滑动删除、拖拽排序、下拉刷新等交互。相比内置的PanResponder，gesture-handler性能更好、API更简洁、与原生动画系统集成更紧密。

  react-native-gesture-handler is a gesture handling library for React Native providing native-level gesture recognition performance. It replaces React Native's built-in gesture system, supporting multi-touch, drag, swipe, and other complex interactions. Pre-installed in Expo projects, commonly used for swipe-to-delete, drag-to-reorder, pull-to-refresh interactions. Compared to built-in PanResponder, gesture-handler offers better performance, cleaner API, and tighter integration with native animation system.

  **核心特征 | Key Characteristics:**
  - 原生性能：手势处理在UI线程执行，响应更快 | Native performance: Gesture processing runs on UI thread, faster response
  - 声明式API：使用组件而非命令式代码处理手势 | Declarative API: Uses components instead of imperative code for gesture handling
  - 手势组合：支持同时识别多个手势和手势优先级 | Gesture composition: Supports simultaneous gesture recognition and priority
  - 跨平台一致性：iOS和Android行为统一 | Cross-platform consistency: Unified behavior on iOS and Android

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. gesture-handler和React Native内置的TouchableOpacity有什么区别？| What's the difference between gesture-handler and React Native's built-in TouchableOpacity?
     **答案 | Answer:** gesture-handler在原生线程处理，性能更好 | gesture-handler processes on native thread, better performance - TouchableOpacity在JavaScript线程处理手势，可能有延迟。gesture-handler的手势识别更准确，特别是在复杂交互（如滑动删除）中表现更好。
     TouchableOpacity processes gestures on JavaScript thread, may have delays. gesture-handler's gesture recognition is more accurate, especially in complex interactions like swipe-to-delete.

  2. Swipeable组件的friction参数作用是什么？| What does the friction parameter in Swipeable component do?
     **答案 | Answer:** 控制滑动阻力 | Controls swipe resistance - friction越大，滑动时需要的手指移动距离越远。friction=2表示手指移动2像素，内容才移动1像素，让滑动操作更有控制感，防止误触。
     Higher friction requires longer finger movement to swipe. friction=2 means content moves 1 pixel for every 2 pixels of finger movement, making swipe more controllable, preventing accidental triggers.

  3. Swipeable必须配合FlatList使用吗？| Must Swipeable be used with FlatList?
     **答案 | Answer:** 不必须 | Not required - Swipeable可以包裹任何内容，但最常用于列表项。在FlatList的renderItem中使用Swipeable可以让每个列表项都可滑动删除。
     Swipeable can wrap any content, but is most commonly used for list items. Using Swipeable in FlatList's renderItem makes each item swipeable for deletion.

  4. 滑动删除的rightThreshold参数有什么作用？| What does the rightThreshold parameter do in swipe-to-delete?
     **答案 | Answer:** 设置触发删除的滑动距离 | Sets swipe distance threshold to trigger deletion - 当滑动距离超过这个值时，释放手指会自动完成滑动。40表示40像素，低于此值释放会回弹，高于此值会展开删除按钮。
     When swipe distance exceeds this value, releasing finger automatically completes the swipe. 40 means 40 pixels - below this bounces back, above this expands delete button.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // react-native-gesture-handler高级使用 | Advanced usage of react-native-gesture-handler
  import React, { useRef } from 'react';
  import {
    View,
    Text,
    StyleSheet,
    Pressable,
    Animated,
  } from 'react-native';
  import {
    GestureHandlerRootView,
    Swipeable,
    RectButton,
    PanGestureHandler,
  } from 'react-native-gesture-handler';
  import { Ionicons } from '@expo/vector-icons';

  // 1. 基础Swipeable实现 | Basic Swipeable implementation
  const BasicSwipeableItem = ({ article, onDelete }) => {
    const swipeableRef = useRef(null);

    // 渲染右侧删除按钮 | Render right delete button
    const renderRightActions = (progress, dragX) => {
      // progress: Animated.Value，从0到1表示滑动进度 | Animated.Value from 0 to 1 representing swipe progress
      // dragX: Animated.Value，手指的X轴位移 | Animated.Value representing finger's X-axis displacement

      // 根据滑动进度计算透明度 | Calculate opacity based on swipe progress
      const opacity = progress.interpolate({
        inputRange: [0, 1],
        outputRange: [0.5, 1],
      });

      // 根据拖动距离计算缩放 | Calculate scale based on drag distance
      const scale = dragX.interpolate({
        inputRange: [-100, -50, 0],
        outputRange: [1, 0.9, 0.7],
        extrapolate: 'clamp',
      });

      return (
        <Animated.View
          style={[
            styles.rightAction,
            { opacity, transform: [{ scale }] },
          ]}
        >
          <RectButton
            style={styles.deleteButton}
            onPress={() => {
              swipeableRef.current?.close(); // 关闭滑动 | Close swipeable
              onDelete(article);
            }}
          >
            <Ionicons name="trash-outline" size={24} color="#fff" />
            <Text style={styles.deleteButtonText}>删除 | Delete</Text>
          </RectButton>
        </Animated.View>
      );
    };

    return (
      <Swipeable
        ref={swipeableRef}
        renderRightActions={renderRightActions}
        rightThreshold={40}
        friction={2}
        overshootRight={false} // 防止过度滑动 | Prevent overshooting
        onSwipeableOpen={() => console.log('Opened')}
        onSwipeableClose={() => console.log('Closed')}
      >
        <View style={styles.itemContainer}>
          <Text style={styles.itemTitle}>{article.title}</Text>
        </View>
      </Swipeable>
    );
  };

  // 2. 高级Swipeable：左右两侧都有操作 | Advanced Swipeable: Actions on both sides
  const AdvancedSwipeableItem = ({ article, onDelete, onShare, onBookmark }) => {
    // 渲染左侧操作按钮 | Render left action buttons
    const renderLeftActions = (progress, dragX) => {
      const translateX = dragX.interpolate({
        inputRange: [0, 100],
        outputRange: [-100, 0],
        extrapolate: 'clamp',
      });

      return (
        <Animated.View
          style={[
            styles.leftActions,
            { transform: [{ translateX }] },
          ]}
        >
          <RectButton
            style={[styles.actionButton, { backgroundColor: '#4CAF50' }]}
            onPress={() => onBookmark(article)}
          >
            <Ionicons name="bookmark-outline" size={24} color="#fff" />
            <Text style={styles.actionText}>书签 | Bookmark</Text>
          </RectButton>

          <RectButton
            style={[styles.actionButton, { backgroundColor: '#2196F3' }]}
            onPress={() => onShare(article)}
          >
            <Ionicons name="share-outline" size={24} color="#fff" />
            <Text style={styles.actionText}>分享 | Share</Text>
          </RectButton>
        </Animated.View>
      );
    };

    // 渲染右侧删除按钮 | Render right delete button
    const renderRightActions = (progress, dragX) => {
      const translateX = dragX.interpolate({
        inputRange: [-100, 0],
        outputRange: [0, 100],
        extrapolate: 'clamp',
      });

      return (
        <Animated.View
          style={[
            styles.rightActions,
            { transform: [{ translateX }] },
          ]}
        >
          <RectButton
            style={[styles.actionButton, { backgroundColor: '#FF3B30' }]}
            onPress={() => onDelete(article)}
          >
            <Ionicons name="trash-outline" size={24} color="#fff" />
            <Text style={styles.actionText}>删除 | Delete</Text>
          </RectButton>
        </Animated.View>
      );
    };

    return (
      <Swipeable
        renderLeftActions={renderLeftActions}
        renderRightActions={renderRightActions}
        leftThreshold={30}
        rightThreshold={40}
      >
        <View style={styles.itemContainer}>
          <Text style={styles.itemTitle}>{article.title}</Text>
        </View>
      </Swipeable>
    );
  };

  // 3. 自定义手势处理：拖拽排序 | Custom gesture handling: Drag to reorder
  const DraggableItem = ({ item, onDragEnd }) => {
    const translateY = useRef(new Animated.Value(0)).current;
    const isDragging = useRef(false);

    const onGestureEvent = Animated.event(
      [{ nativeEvent: { translationY: translateY } }],
      { useNativeDriver: true }
    );

    const onHandlerStateChange = (event) => {
      if (event.nativeEvent.state === State.END) {
        isDragging.current = false;

        // 根据移动距离判断是否改变位置 | Determine if position changed based on movement distance
        const finalY = event.nativeEvent.translationY;

        if (Math.abs(finalY) > 50) {
          onDragEnd(item, finalY > 0 ? 'down' : 'up');
        }

        // 重置位置 | Reset position
        Animated.spring(translateY, {
          toValue: 0,
          useNativeDriver: true,
        }).start();
      } else if (event.nativeEvent.state === State.BEGAN) {
        isDragging.current = true;
      }
    };

    return (
      <PanGestureHandler
        onGestureEvent={onGestureEvent}
        onHandlerStateChange={onHandlerStateChange}
      >
        <Animated.View
          style={[
            styles.draggableItem,
            {
              transform: [{ translateY }],
              opacity: isDragging.current ? 0.7 : 1,
            },
          ]}
        >
          <Ionicons name="menu-outline" size={24} color="#999" />
          <Text style={styles.itemTitle}>{item.title}</Text>
        </Animated.View>
      </PanGestureHandler>
    );
  };

  // 4. 下拉刷新手势 | Pull-to-refresh gesture
  const PullToRefreshList = ({ data, onRefresh }) => {
    const translateY = useRef(new Animated.Value(0)).current;
    const [isRefreshing, setIsRefreshing] = React.useState(false);

    const onGestureEvent = Animated.event(
      [{ nativeEvent: { translationY: translateY } }],
      { useNativeDriver: true }
    );

    const onHandlerStateChange = async (event) => {
      if (event.nativeEvent.state === State.END) {
        const pullDistance = event.nativeEvent.translationY;

        if (pullDistance > 100 && !isRefreshing) {
          setIsRefreshing(true);

          // 执行刷新 | Perform refresh
          await onRefresh();

          setIsRefreshing(false);
        }

        // 重置位置 | Reset position
        Animated.spring(translateY, {
          toValue: 0,
          useNativeDriver: true,
        }).start();
      }
    };

    return (
      <PanGestureHandler
        onGestureEvent={onGestureEvent}
        onHandlerStateChange={onHandlerStateChange}
        enabled={!isRefreshing}
      >
        <Animated.View style={{ transform: [{ translateY }] }}>
          {isRefreshing && (
            <View style={styles.refreshIndicator}>
              <Text>刷新中... | Refreshing...</Text>
            </View>
          )}
          {data.map((item) => (
            <View key={item.id} style={styles.itemContainer}>
              <Text>{item.title}</Text>
            </View>
          ))}
        </Animated.View>
      </PanGestureHandler>
    );
  };

  // 5. 手势冲突处理 | Gesture conflict handling
  const GestureConflictExample = () => {
    // 场景：ScrollView中嵌套水平滑动的Swipeable
    // Scenario: Swipeable with horizontal swipe nested in ScrollView

    return (
      <GestureHandlerRootView style={styles.container}>
        <ScrollView>
          {articles.map((article) => (
            <Swipeable
              key={article.id}
              renderRightActions={renderRightActions}
              // 配置手势优先级 | Configure gesture priority
              shouldCancelWhenOutside={true}
              // 当滑动超出边界时取消手势 | Cancel gesture when swipe exceeds boundary
            >
              <View style={styles.itemContainer}>
                <Text>{article.title}</Text>
              </View>
            </Swipeable>
          ))}
        </ScrollView>
      </GestureHandlerRootView>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
    },
    itemContainer: {
      backgroundColor: '#fff',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    itemTitle: {
      fontSize: 16,
      color: '#333',
    },
    rightAction: {
      justifyContent: 'center',
    },
    deleteButton: {
      backgroundColor: '#FF3B30',
      justifyContent: 'center',
      alignItems: 'center',
      width: 80,
      height: '100%',
    },
    deleteButtonText: {
      color: '#fff',
      fontSize: 12,
      marginTop: 4,
    },
    leftActions: {
      flexDirection: 'row',
    },
    rightActions: {
      flexDirection: 'row',
    },
    actionButton: {
      justifyContent: 'center',
      alignItems: 'center',
      width: 80,
    },
    actionText: {
      color: '#fff',
      fontSize: 12,
      marginTop: 4,
    },
    draggableItem: {
      flexDirection: 'row',
      alignItems: 'center',
      backgroundColor: '#fff',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    refreshIndicator: {
      padding: 16,
      alignItems: 'center',
      backgroundColor: '#f0f0f0',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么renderRightActions函数会接收progress和dragX参数？| Why does renderRightActions receive progress and dragX parameters?
    **答案 | Answer:** 用于创建动画效果 | To create animation effects - progress和dragX都是Animated.Value，可以通过interpolate映射到其他动画属性（如透明度、缩放、位移），让删除按钮的显示更流畅自然。
    Both progress and dragX are Animated.Values that can be mapped to other animation properties (opacity, scale, translation) via interpolate, making delete button appearance smoother and more natural.

  - RectButton和Pressable有什么区别？| What's the difference between RectButton and Pressable?
    **答案 | Answer:** RectButton是gesture-handler提供的原生按钮 | RectButton is a native button from gesture-handler - 它在原生层处理触摸，性能更好，特别是在滑动操作中。Pressable是React Native内置的，在JS线程处理。
    It handles touches on native layer, better performance, especially in swipe operations. Pressable is React Native built-in, processes on JS thread.

  **常见误区检查 | Common Misconception Checks:**
  - 是否需要在App根组件包裹GestureHandlerRootView？| Do you need to wrap GestureHandlerRootView in App root component?
    **答案 | Answer:** 在Expo中不需要 | Not needed in Expo - Expo已经在根部自动包裹了GestureHandlerRootView。但在bare React Native项目中需要手动包裹，否则手势无法工作。
    Expo automatically wraps GestureHandlerRootView at root. But in bare React Native projects, manual wrapping is required, otherwise gestures won't work.

  - Swipeable组件内的内容可以包含按钮吗？| Can Swipeable component's content contain buttons?
    **答案 | Answer:** 可以 | Yes - 但需要注意手势冲突。按钮的点击手势可能与滑动手势冲突。建议使用onPress而非onLongPress，并调整friction参数让滑动更明确，减少误触。
    But be aware of gesture conflicts. Button tap gesture may conflict with swipe gesture. Recommend using onPress instead of onLongPress, and adjust friction parameter to make swipe more deliberate, reducing accidental triggers.

- **交互反馈与动画 | Interaction Feedback & Animation**

  **概念定义 | Concept Definition:**
  交互反馈是用户操作后应用给予的视觉或触觉响应，确认操作已被识别和执行。在书签功能中，包括按钮按下的视觉变化、书签添加的动画效果、删除确认的对话框等。良好的交互反馈让应用感觉响应迅速、值得信赖。React Native提供了Animated API用于流畅的动画，配合手势库可以创造原生级别的交互体验。

  Interaction feedback is the visual or haptic response an application provides after user operations, confirming that actions have been recognized and executed. In bookmark functionality, this includes visual changes on button press, animation effects when adding bookmarks, deletion confirmation dialogs. Good interaction feedback makes the app feel responsive and trustworthy. React Native provides Animated API for smooth animations, combined with gesture library to create native-level interaction experiences.

  **核心特征 | Key Characteristics:**
  - 即时响应：用户操作后立即有视觉反馈，不等待网络或存储 | Immediate response: Visual feedback right after user action, doesn't wait for network or storage
  - 微妙动画：使用缩放、淡入淡出等微妙效果增强质感 | Subtle animations: Use scale, fade effects to enhance tactile quality
  - 触觉反馈：在关键操作时提供震动反馈（可选） | Haptic feedback: Provide vibration feedback for key operations (optional)
  - 状态可视化：通过颜色、图标变化清晰表达状态 | State visualization: Clearly express state through color, icon changes

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 动画应该在什么时机播放？| When should animations play?
     **答案 | Answer:** 在状态变化时 | On state changes - 例如书签状态从false变为true时播放动画，而不是在按钮按下时。这样即使状态通过其他方式改变（如从其他屏幕返回），动画也会正确播放。
     For example, play animation when bookmark state changes from false to true, not on button press. This way, even if state changes through other means (returning from another screen), animation plays correctly.

  2. useNativeDriver: true参数有什么作用？| What does the useNativeDriver: true parameter do?
     **答案 | Answer:** 在原生线程运行动画 | Runs animation on native thread - 动画不受JavaScript线程阻塞影响，更流畅。但只支持transform和opacity属性，不支持布局属性（如width、height）。
     Animation not affected by JavaScript thread blocking, smoother. But only supports transform and opacity properties, not layout properties (width, height).

  3. 触觉反馈(Haptics)应该在所有操作中使用吗？| Should haptic feedback be used for all operations?
     **答案 | Answer:** 不应该 | No - 过度使用会让用户疲劳。只在重要操作（如删除、提交）或错误提示时使用。轻触按钮不需要触觉反馈，滑动删除完成时可以使用。
     Overuse causes user fatigue. Only use for important operations (delete, submit) or error notifications. Light button taps don't need haptic feedback, swipe-to-delete completion can use it.

  4. 动画时长应该设置为多少？| What should animation duration be set to?
     **答案 | Answer:** 通常150-300ms | Typically 150-300ms - 太短(< 100ms)用户感知不到，太长(> 500ms)感觉迟缓。书签按钮缩放动画150ms合适，屏幕转场300ms合适。遵循平台规范：iOS偏好更快的动画。
     Too short (< 100ms) imperceptible, too long (> 500ms) feels sluggish. Bookmark button scale animation 150ms appropriate, screen transitions 300ms appropriate. Follow platform conventions: iOS prefers faster animations.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 交互反馈与动画完整实现 | Complete interaction feedback and animation implementation
  import React, { useRef, useEffect } from 'react';
  import {
    View,
    Text,
    Pressable,
    Animated,
    StyleSheet,
    Platform,
  } from 'react-native';
  import * as Haptics from 'expo-haptics';
  import { Ionicons } from '@expo/vector-icons';

  // 1. 书签按钮动画组件 | Bookmark button animation component
  const AnimatedBookmarkButton = ({ bookmarked, onPress, disabled }) => {
    // 动画值 | Animation values
    const scaleAnim = useRef(new Animated.Value(1)).current;
    const colorAnim = useRef(new Animated.Value(bookmarked ? 1 : 0)).current;
    const rotateAnim = useRef(new Animated.Value(0)).current;

    // 当书签状态改变时播放动画 | Play animation when bookmark state changes
    useEffect(() => {
      // 缩放动画：弹跳效果 | Scale animation: bounce effect
      Animated.sequence([
        Animated.spring(scaleAnim, {
          toValue: 1.3,
          friction: 3,
          tension: 40,
          useNativeDriver: true,
        }),
        Animated.spring(scaleAnim, {
          toValue: 1,
          friction: 3,
          tension: 40,
          useNativeDriver: true,
        }),
      ]).start();

      // 颜色过渡动画 | Color transition animation
      Animated.timing(colorAnim, {
        toValue: bookmarked ? 1 : 0,
        duration: 200,
        useNativeDriver: false, // 颜色插值不支持useNativeDriver | Color interpolation doesn't support useNativeDriver
      }).start();

      // 旋转动画（添加书签时） | Rotation animation (when adding bookmark)
      if (bookmarked) {
        Animated.sequence([
          Animated.timing(rotateAnim, {
            toValue: 1,
            duration: 200,
            useNativeDriver: true,
          }),
          Animated.timing(rotateAnim, {
            toValue: 0,
            duration: 200,
            useNativeDriver: true,
          }),
        ]).start();
      }

      // 触觉反馈 | Haptic feedback
      if (bookmarked) {
        Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
      } else {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      }
    }, [bookmarked]);

    // 计算插值颜色 | Calculate interpolated color
    const iconColor = colorAnim.interpolate({
      inputRange: [0, 1],
      outputRange: ['#999', '#FF6347'],
    });

    // 计算旋转角度 | Calculate rotation angle
    const rotation = rotateAnim.interpolate({
      inputRange: [0, 1],
      outputRange: ['0deg', '360deg'],
    });

    const handlePress = () => {
      // 按下时的触觉反馈 | Haptic feedback on press
      Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);

      // 按下动画 | Press animation
      Animated.sequence([
        Animated.timing(scaleAnim, {
          toValue: 0.9,
          duration: 100,
          useNativeDriver: true,
        }),
        Animated.timing(scaleAnim, {
          toValue: 1,
          duration: 100,
          useNativeDriver: true,
        }),
      ]).start();

      onPress();
    };

    return (
      <Pressable
        onPress={handlePress}
        disabled={disabled}
        style={({ pressed }) => [
          styles.bookmarkButton,
          pressed && styles.pressed,
        ]}
      >
        <Animated.View
          style={{
            transform: [
              { scale: scaleAnim },
              { rotate: rotation },
            ],
          }}
        >
          <Animated.View>
            <Ionicons
              name={bookmarked ? 'bookmark' : 'bookmark-outline'}
              size={28}
              color={iconColor}
            />
          </Animated.View>
        </Animated.View>
      </Pressable>
    );
  };

  // 2. Toast通知组件 | Toast notification component
  const Toast = ({ visible, message, type = 'success' }) => {
    const translateY = useRef(new Animated.Value(-100)).current;
    const opacity = useRef(new Animated.Value(0)).current;

    useEffect(() => {
      if (visible) {
        // 显示动画 | Show animation
        Animated.parallel([
          Animated.timing(translateY, {
            toValue: 0,
            duration: 300,
            useNativeDriver: true,
          }),
          Animated.timing(opacity, {
            toValue: 1,
            duration: 300,
            useNativeDriver: true,
          }),
        ]).start();

        // 3秒后自动隐藏 | Auto-hide after 3 seconds
        setTimeout(() => {
          Animated.parallel([
            Animated.timing(translateY, {
              toValue: -100,
              duration: 300,
              useNativeDriver: true,
            }),
            Animated.timing(opacity, {
              toValue: 0,
              duration: 300,
              useNativeDriver: true,
            }),
          ]).start();
        }, 3000);
      }
    }, [visible]);

    const backgroundColor = type === 'success' ? '#4CAF50' : '#FF3B30';

    return (
      <Animated.View
        style={[
          styles.toast,
          {
            backgroundColor,
            transform: [{ translateY }],
            opacity,
          },
        ]}
      >
        <Ionicons
          name={type === 'success' ? 'checkmark-circle' : 'alert-circle'}
          size={24}
          color="#fff"
        />
        <Text style={styles.toastText}>{message}</Text>
      </Animated.View>
    );
  };

  // 3. 加载状态动画 | Loading state animation
  const LoadingIndicator = ({ visible }) => {
    const rotateAnim = useRef(new Animated.Value(0)).current;

    useEffect(() => {
      if (visible) {
        // 循环旋转动画 | Continuous rotation animation
        Animated.loop(
          Animated.timing(rotateAnim, {
            toValue: 1,
            duration: 1000,
            useNativeDriver: true,
          })
        ).start();
      }
    }, [visible]);

    const rotation = rotateAnim.interpolate({
      inputRange: [0, 1],
      outputRange: ['0deg', '360deg'],
    });

    if (!visible) return null;

    return (
      <View style={styles.loadingContainer}>
        <Animated.View style={{ transform: [{ rotate: rotation }] }}>
          <Ionicons name="refresh" size={32} color="#FF6347" />
        </Animated.View>
        <Text style={styles.loadingText}>加载中... | Loading...</Text>
      </View>
    );
  };

  // 4. 滑动删除动画增强 | Enhanced swipe-to-delete animation
  const AnimatedSwipeableItem = ({ article, onDelete }) => {
    const renderRightActions = (progress, dragX) => {
      // 删除按钮背景色渐变 | Delete button background gradient
      const backgroundColor = dragX.interpolate({
        inputRange: [-100, -50, 0],
        outputRange: ['#D32F2F', '#FF3B30', '#FF6347'],
        extrapolate: 'clamp',
      });

      // 删除图标缩放 | Delete icon scale
      const iconScale = dragX.interpolate({
        inputRange: [-100, -50, 0],
        outputRange: [1.2, 1, 0.8],
        extrapolate: 'clamp',
      });

      // 删除文本透明度 | Delete text opacity
      const textOpacity = dragX.interpolate({
        inputRange: [-80, -40, 0],
        outputRange: [1, 0.7, 0],
        extrapolate: 'clamp',
      });

      return (
        <Animated.View
          style={[
            styles.deleteAction,
            { backgroundColor },
          ]}
        >
          <Pressable
            style={styles.deleteButtonContent}
            onPress={() => {
              // 删除前的触觉反馈 | Haptic feedback before deletion
              Haptics.notificationAsync(
                Haptics.NotificationFeedbackType.Warning
              );
              onDelete(article);
            }}
          >
            <Animated.View style={{ transform: [{ scale: iconScale }] }}>
              <Ionicons name="trash-outline" size={24} color="#fff" />
            </Animated.View>
            <Animated.Text style={[styles.deleteText, { opacity: textOpacity }]}>
              删除 | Delete
            </Animated.Text>
          </Pressable>
        </Animated.View>
      );
    };

    return (
      <Swipeable
        renderRightActions={renderRightActions}
        rightThreshold={40}
        friction={2}
        onSwipeableWillOpen={() => {
          // 滑动即将打开时的轻微触觉反馈 | Light haptic feedback when swipe is about to open
          Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        }}
      >
        <View style={styles.itemContainer}>
          <Text style={styles.itemTitle}>{article.title}</Text>
        </View>
      </Swipeable>
    );
  };

  // 5. 空状态动画 | Empty state animation
  const AnimatedEmptyState = () => {
    const fadeAnim = useRef(new Animated.Value(0)).current;
    const scaleAnim = useRef(new Animated.Value(0.8)).current;

    useEffect(() => {
      Animated.parallel([
        Animated.timing(fadeAnim, {
          toValue: 1,
          duration: 500,
          useNativeDriver: true,
        }),
        Animated.spring(scaleAnim, {
          toValue: 1,
          friction: 5,
          tension: 40,
          useNativeDriver: true,
        }),
      ]).start();
    }, []);

    return (
      <Animated.View
        style={[
          styles.emptyContainer,
          {
            opacity: fadeAnim,
            transform: [{ scale: scaleAnim }],
          },
        ]}
      >
        <Ionicons name="bookmark-outline" size={80} color="#ccc" />
        <Text style={styles.emptyTitle}>还没有书签 | No Bookmarks Yet</Text>
        <Text style={styles.emptyDescription}>
          保存您喜欢的文章，稍后阅读
          {'\n'}
          Bookmark your favorite articles to read later
        </Text>
      </Animated.View>
    );
  };

  // 6. 平台特定的交互反馈 | Platform-specific interaction feedback
  const PlatformSpecificFeedback = ({ onPress }) => {
    const handlePress = () => {
      if (Platform.OS === 'ios') {
        // iOS使用更轻的触觉反馈 | iOS uses lighter haptic feedback
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
      } else {
        // Android使用标准触觉反馈 | Android uses standard haptic feedback
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
      }
      onPress();
    };

    return (
      <Pressable
        onPress={handlePress}
        style={({ pressed }) => [
          styles.button,
          // iOS使用缩放效果，Android使用背景色变化 | iOS uses scale effect, Android uses background color change
          Platform.OS === 'ios'
            ? { transform: [{ scale: pressed ? 0.95 : 1 }] }
            : { backgroundColor: pressed ? '#e0e0e0' : '#fff' },
        ]}
      >
        <Text>按钮 | Button</Text>
      </Pressable>
    );
  };

  const styles = StyleSheet.create({
    bookmarkButton: {
      padding: 8,
    },
    pressed: {
      opacity: 0.7,
    },
    toast: {
      position: 'absolute',
      top: 50,
      left: 20,
      right: 20,
      flexDirection: 'row',
      alignItems: 'center',
      padding: 16,
      borderRadius: 8,
      zIndex: 1000,
      ...Platform.select({
        ios: {
          shadowColor: '#000',
          shadowOffset: { width: 0, height: 2 },
          shadowOpacity: 0.25,
          shadowRadius: 4,
        },
        android: {
          elevation: 5,
        },
      }),
    },
    toastText: {
      color: '#fff',
      fontSize: 16,
      marginLeft: 12,
      fontWeight: '500',
    },
    loadingContainer: {
      flexDirection: 'row',
      alignItems: 'center',
      padding: 16,
    },
    loadingText: {
      marginLeft: 12,
      fontSize: 16,
      color: '#666',
    },
    deleteAction: {
      justifyContent: 'center',
      alignItems: 'center',
      width: 80,
    },
    deleteButtonContent: {
      alignItems: 'center',
    },
    deleteText: {
      color: '#fff',
      fontSize: 12,
      marginTop: 4,
    },
    itemContainer: {
      backgroundColor: '#fff',
      padding: 16,
    },
    itemTitle: {
      fontSize: 16,
    },
    emptyContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      paddingHorizontal: 40,
    },
    emptyTitle: {
      fontSize: 20,
      fontWeight: '600',
      marginTop: 16,
      color: '#333',
    },
    emptyDescription: {
      fontSize: 14,
      color: '#999',
      textAlign: 'center',
      marginTop: 8,
      lineHeight: 20,
    },
    button: {
      padding: 16,
      borderRadius: 8,
      alignItems: 'center',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么颜色插值不能使用useNativeDriver: true？| Why can't color interpolation use useNativeDriver: true?
    **答案 | Answer:** 颜色属性不在原生驱动支持的列表中 | Color properties aren't in native driver's supported list - useNativeDriver只支持transform和opacity属性。颜色、布局属性（width、height、padding）等必须在JS线程处理。
    useNativeDriver only supports transform and opacity properties. Color, layout properties (width, height, padding) must be processed on JS thread.

  - Haptics.ImpactFeedbackStyle有哪些级别？| What levels does Haptics.ImpactFeedbackStyle have?
    **答案 | Answer:** Light、Medium、Heavy三个级别 | Three levels: Light, Medium, Heavy - Light用于轻触、选择；Medium用于按钮按下；Heavy用于重要操作、错误。应根据操作重要性选择合适级别。
    Light for light taps, selections; Medium for button presses; Heavy for important operations, errors. Choose appropriate level based on operation importance.

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该为每个按钮添加触觉反馈？| Should haptic feedback be added to every button?
    **答案 | Answer:** 不应该 | No - 过度使用会降低特殊感，并可能惹恼用户。只在重要操作、状态变化或需要确认的操作时使用。普通的列表滚动、标签切换不需要。
    Overuse reduces special feeling and may annoy users. Only use for important operations, state changes, or operations needing confirmation. Regular list scrolling, tab switching don't need it.

  - 动画是否会阻塞主线程影响性能？| Do animations block the main thread affecting performance?
    **答案 | Answer:** 使用useNativeDriver时不会 | Not when using useNativeDriver - 动画在原生线程运行，不受JS线程阻塞影响。但颜色、布局动画仍在JS线程，大量使用时可能影响性能，应注意优化。
    Animations run on native thread, unaffected by JS thread blocking. But color, layout animations still on JS thread, may affect performance with heavy use, should optimize.

### 5. 数据同步与一致性 | Data Synchronization & Consistency (45分钟 | 45 minutes)

- **跨屏幕状态同步 | Cross-Screen State Synchronization**

  **概念定义 | Concept Definition:**
  在多屏幕应用中，用户可能在不同屏幕操作相同的数据（如在详情页添加书签，在书签页查看）。状态同步确保所有屏幕显示的数据一致，避免过时或冲突的信息。使用Context API实现的状态管理天然支持跨屏幕同步：当Context值变化时，所有消费该Context的组件自动重新渲染并显示最新数据。

  In multi-screen applications, users may operate on the same data from different screens (e.g., adding bookmarks in detail page, viewing in bookmarks page). State synchronization ensures all screens display consistent data, avoiding stale or conflicting information. State management implemented with Context API naturally supports cross-screen synchronization: when Context value changes, all components consuming that Context automatically re-render with latest data.

  **核心特征 | Key Characteristics:**
  - 单一数据源(Single Source of Truth)：所有屏幕从同一个Context读取数据 | Single source of truth: All screens read data from the same Context
  - 自动更新：Context变化时，相关组件自动重渲染 | Automatic updates: Related components auto-re-render when Context changes
  - 双向同步：任何屏幕的修改都会反映到所有其他屏幕 | Bidirectional sync: Changes from any screen reflected to all other screens
  - 持久化同步：内存状态与AsyncStorage保持同步 | Persistence sync: Memory state stays synced with AsyncStorage

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 用户在详情页添加书签后，书签列表页会自动更新吗？| After user adds bookmark in detail page, does bookmark list page auto-update?
     **答案 | Answer:** 是的 | Yes - 因为两个页面都从BookmarksContext读取数据。addBookmark更新Context state，触发所有消费者重渲染。返回书签列表页时，会看到最新添加的书签。
     Because both pages read from BookmarksContext. addBookmark updates Context state, triggering all consumers to re-render. When returning to bookmark list, will see newly added bookmark.

  2. 如果在两个屏幕同时修改同一个书签会发生什么？| What happens if the same bookmark is modified on two screens simultaneously?
     **答案 | Answer:** 后执行的操作会覆盖前一个 | Later operation overwrites the previous one - Context使用单一state，不处理冲突解决。但在书签功能中，这种情况极少发生（用户一次只能在一个屏幕操作）。
     Context uses single state, doesn't handle conflict resolution. But in bookmark functionality, this rarely happens (user can only operate on one screen at a time).

  3. AsyncStorage的保存失败会影响内存中的状态吗？| If AsyncStorage save fails, does it affect in-memory state?
     **答案 | Answer:** 不会立即影响 | Doesn't immediately affect - 采用乐观更新策略：先更新内存状态，异步保存到AsyncStorage。即使保存失败，当前会话中状态仍然正确，但应用重启后会丢失。应该记录错误并可能重试。
     Uses optimistic update strategy: update in-memory state first, save to AsyncStorage asynchronously. Even if save fails, state correct in current session, but lost after app restart. Should log error and possibly retry.

  4. 使用多个Context会有同步问题吗？| Are there sync issues with multiple Contexts?
     **答案 | Answer:** 如果数据相互独立则没有 | No if data is independent - BookmarksContext和ThemeContext管理不同的数据域，互不影响。但如果一个数据依赖另一个，需要在useEffect中监听两个Context并手动同步。
     BookmarksContext and ThemeContext manage different data domains, don't affect each other. But if one data depends on another, need to watch both Contexts in useEffect and manually sync.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 跨屏幕状态同步实现 | Cross-screen state synchronization implementation
  import React, { createContext, useContext, useReducer, useEffect, useCallback } from 'react';
  import AsyncStorage from '@react-native-async-storage/async-storage';

  // 1. 创建同步的BookmarksContext | Create synchronized BookmarksContext
  const BookmarksContext = createContext(null);
  const STORAGE_KEY = '@news_app_bookmarks';

  // 定义action类型 | Define action types
  const ACTIONS = {
    LOAD_SUCCESS: 'LOAD_SUCCESS',
    LOAD_ERROR: 'LOAD_ERROR',
    ADD_BOOKMARK: 'ADD_BOOKMARK',
    REMOVE_BOOKMARK: 'REMOVE_BOOKMARK',
    CLEAR_ALL: 'CLEAR_ALL',
    SYNC_START: 'SYNC_START',
    SYNC_SUCCESS: 'SYNC_SUCCESS',
    SYNC_ERROR: 'SYNC_ERROR',
  };

  // Reducer处理状态更新 | Reducer handles state updates
  const bookmarksReducer = (state, action) => {
    switch (action.type) {
      case ACTIONS.LOAD_SUCCESS:
        return {
          ...state,
          bookmarks: action.payload,
          isLoading: false,
          error: null,
          lastSynced: new Date().toISOString(),
        };

      case ACTIONS.LOAD_ERROR:
        return {
          ...state,
          isLoading: false,
          error: action.payload,
        };

      case ACTIONS.ADD_BOOKMARK: {
        const exists = state.bookmarks.some(
          item => item.url === action.payload.url
        );
        if (exists) return state;

        return {
          ...state,
          bookmarks: [
            ...state.bookmarks,
            {
              ...action.payload,
              bookmarkedAt: new Date().toISOString(),
            },
          ],
          isSyncing: true, // 标记正在同步 | Mark as syncing
        };
      }

      case ACTIONS.REMOVE_BOOKMARK:
        return {
          ...state,
          bookmarks: state.bookmarks.filter(
            item => item.url !== action.payload
          ),
          isSyncing: true,
        };

      case ACTIONS.CLEAR_ALL:
        return {
          ...state,
          bookmarks: [],
          isSyncing: true,
        };

      case ACTIONS.SYNC_SUCCESS:
        return {
          ...state,
          isSyncing: false,
          lastSynced: new Date().toISOString(),
          syncError: null,
        };

      case ACTIONS.SYNC_ERROR:
        return {
          ...state,
          isSyncing: false,
          syncError: action.payload,
        };

      default:
        return state;
    }
  };

  // Provider组件 | Provider component
  export const BookmarksProvider = ({ children }) => {
    const [state, dispatch] = useReducer(bookmarksReducer, {
      bookmarks: [],
      isLoading: true,
      error: null,
      isSyncing: false,
      syncError: null,
      lastSynced: null,
    });

    // 应用启动时加载书签 | Load bookmarks on app start
    useEffect(() => {
      loadBookmarks();
    }, []);

    // 书签变化时自动保存到AsyncStorage | Auto-save to AsyncStorage when bookmarks change
    useEffect(() => {
      if (!state.isLoading && state.isSyncing) {
        syncToStorage();
      }
    }, [state.bookmarks, state.isLoading, state.isSyncing]);

    // 从AsyncStorage加载 | Load from AsyncStorage
    const loadBookmarks = async () => {
      try {
        const stored = await AsyncStorage.getItem(STORAGE_KEY);
        const data = stored ? JSON.parse(stored) : { bookmarks: [], version: '1.0' };

        dispatch({
          type: ACTIONS.LOAD_SUCCESS,
          payload: data.bookmarks,
        });

        console.log(`Loaded ${data.bookmarks.length} bookmarks from storage`);
      } catch (error) {
        console.error('Failed to load bookmarks:', error);
        dispatch({
          type: ACTIONS.LOAD_ERROR,
          payload: error.message,
        });
      }
    };

    // 同步到AsyncStorage | Sync to AsyncStorage
    const syncToStorage = async () => {
      try {
        const dataToStore = {
          bookmarks: state.bookmarks,
          version: '1.0',
          lastUpdated: new Date().toISOString(),
        };

        await AsyncStorage.setItem(STORAGE_KEY, JSON.stringify(dataToStore));

        dispatch({ type: ACTIONS.SYNC_SUCCESS });

        console.log(`Synced ${state.bookmarks.length} bookmarks to storage`);
      } catch (error) {
        console.error('Failed to sync bookmarks:', error);
        dispatch({
          type: ACTIONS.SYNC_ERROR,
          payload: error.message,
        });

        // 同步失败时可以实现重试逻辑 | Can implement retry logic on sync failure
        // setTimeout(() => syncToStorage(), 5000);
      }
    };

    // 添加书签（所有屏幕共享） | Add bookmark (shared across all screens)
    const addBookmark = useCallback((article) => {
      dispatch({
        type: ACTIONS.ADD_BOOKMARK,
        payload: article,
      });
    }, []);

    // 删除书签（所有屏幕共享） | Remove bookmark (shared across all screens)
    const removeBookmark = useCallback((articleUrl) => {
      dispatch({
        type: ACTIONS.REMOVE_BOOKMARK,
        payload: articleUrl,
      });
    }, []);

    // 清空所有书签 | Clear all bookmarks
    const clearAllBookmarks = useCallback(() => {
      dispatch({ type: ACTIONS.CLEAR_ALL });
    }, []);

    // 检查是否已书签 | Check if bookmarked
    const isBookmarked = useCallback((articleUrl) => {
      return state.bookmarks.some(item => item.url === articleUrl);
    }, [state.bookmarks]);

    // 切换书签状态 | Toggle bookmark status
    const toggleBookmark = useCallback((article) => {
      if (isBookmarked(article.url)) {
        removeBookmark(article.url);
      } else {
        addBookmark(article);
      }
    }, [isBookmarked, addBookmark, removeBookmark]);

    // 手动重新加载（用于错误恢复） | Manual reload (for error recovery)
    const reload = useCallback(() => {
      dispatch({ type: ACTIONS.SYNC_START });
      loadBookmarks();
    }, []);

    // Context值（使用useMemo优化） | Context value (optimized with useMemo)
    const value = React.useMemo(() => ({
      bookmarks: state.bookmarks,
      isLoading: state.isLoading,
      error: state.error,
      isSyncing: state.isSyncing,
      syncError: state.syncError,
      lastSynced: state.lastSynced,
      addBookmark,
      removeBookmark,
      clearAllBookmarks,
      isBookmarked,
      toggleBookmark,
      reload,
    }), [
      state.bookmarks,
      state.isLoading,
      state.error,
      state.isSyncing,
      state.syncError,
      state.lastSynced,
      addBookmark,
      removeBookmark,
      clearAllBookmarks,
      isBookmarked,
      toggleBookmark,
      reload,
    ]);

    return (
      <BookmarksContext.Provider value={value}>
        {children}
      </BookmarksContext.Provider>
    );
  };

  // 自定义Hook | Custom Hook
  export const useBookmarks = () => {
    const context = useContext(BookmarksContext);
    if (!context) {
      throw new Error('useBookmarks must be used within BookmarksProvider');
    }
    return context;
  };

  // 2. 在ArticleDetail中使用 | Usage in ArticleDetail
  const ArticleDetailScreen = ({ route }) => {
    const { article } = route.params;
    const { isBookmarked, toggleBookmark, isSyncing } = useBookmarks();

    const bookmarked = isBookmarked(article.url);

    return (
      <View>
        <Text>{article.title}</Text>
        <Pressable onPress={() => toggleBookmark(article)} disabled={isSyncing}>
          <Ionicons
            name={bookmarked ? 'bookmark' : 'bookmark-outline'}
            size={28}
            color={bookmarked ? '#FF6347' : '#999'}
          />
          {isSyncing && <ActivityIndicator size="small" />}
        </Pressable>
      </View>
    );
  };

  // 3. 在BookmarksList中使用 | Usage in BookmarksList
  const BookmarksScreen = () => {
    const { bookmarks, removeBookmark, isLoading, reload, syncError } = useBookmarks();

    if (isLoading) {
      return <LoadingScreen />;
    }

    if (syncError) {
      return (
        <ErrorScreen
          message={syncError}
          onRetry={reload}
        />
      );
    }

    return (
      <FlatList
        data={bookmarks}
        renderItem={({ item }) => (
          <BookmarkItem
            article={item}
            onDelete={() => removeBookmark(item.url)}
          />
        )}
        keyExtractor={(item) => item.url}
        ListEmptyComponent={<EmptyState />}
      />
    );
  };

  // 4. 同步状态监控组件 | Sync status monitoring component
  const SyncStatusIndicator = () => {
    const { isSyncing, lastSynced, syncError } = useBookmarks();

    if (syncError) {
      return (
        <View style={styles.syncStatus}>
          <Ionicons name="alert-circle" size={16} color="#FF3B30" />
          <Text style={styles.errorText}>同步失败 | Sync Failed</Text>
        </View>
      );
    }

    if (isSyncing) {
      return (
        <View style={styles.syncStatus}>
          <ActivityIndicator size="small" color="#666" />
          <Text style={styles.syncText}>同步中... | Syncing...</Text>
        </View>
      );
    }

    if (lastSynced) {
      const timeSince = getTimeSince(lastSynced);
      return (
        <View style={styles.syncStatus}>
          <Ionicons name="checkmark-circle" size={16} color="#4CAF50" />
          <Text style={styles.syncText}>
            {timeSince}前同步 | Synced {timeSince} ago
          </Text>
        </View>
      );
    }

    return null;
  };

  // 辅助函数：计算时间差 | Helper function: Calculate time difference
  const getTimeSince = (timestamp) => {
    const seconds = Math.floor((new Date() - new Date(timestamp)) / 1000);
    if (seconds < 60) return `${seconds}秒`;
    const minutes = Math.floor(seconds / 60);
    if (minutes < 60) return `${minutes}分钟`;
    const hours = Math.floor(minutes / 60);
    return `${hours}小时`;
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用useCallback包裹addBookmark等函数？| Why wrap addBookmark and other functions with useCallback?
    **答案 | Answer:** 防止Context消费者不必要的重渲染 | Prevents unnecessary re-renders of Context consumers - 如果函数在每次渲染时都创建新引用，Context value对象会改变，导致所有消费者重渲染。useCallback确保函数引用稳定。
    If functions create new references on every render, Context value object changes, causing all consumers to re-render. useCallback ensures stable function references.

  - isSyncing状态有什么作用？| What's the purpose of isSyncing state?
    **答案 | Answer:** 提供同步进度反馈 | Provides sync progress feedback - 用户操作后可以看到正在保存的指示器，了解数据是否已持久化。也可以在同步期间禁用某些操作，防止数据冲突。
    Users can see saving indicator after operations, know if data is persisted. Can also disable certain operations during sync to prevent data conflicts.

  **常见误区检查 | Common Misconception Checks:**
  - Context更新会导致整个应用重渲染吗？| Does Context update cause entire app to re-render?
    **答案 | Answer:** 不会 | No - 只有使用useContext(BookmarksContext)的组件会重渲染。未使用该Context的组件不受影响。这就是为什么应该合理拆分Context，避免把所有状态放在一个Context中。
    Only components using useContext(BookmarksContext) will re-render. Components not using that Context are unaffected. This is why you should reasonably split Contexts, avoid putting all state in one Context.

  - AsyncStorage保存失败时是否应该阻止用户继续操作？| Should user be prevented from continuing if AsyncStorage save fails?
    **答案 | Answer:** 不应该 | No - 采用乐观UI策略，允许用户继续使用。在后台记录错误、可能重试保存，或在适当时机提示用户。完全阻塞会严重影响用户体验。
    Use optimistic UI strategy, allow user to continue. Log error in background, possibly retry save, or notify user at appropriate time. Complete blocking severely impacts user experience.

### 6. 性能优化与最佳实践 | Performance Optimization & Best Practices (30分钟 | 30 minutes)

- **Context性能优化 | Context Performance Optimization**

  **概念定义 | Concept Definition:**
  虽然Context API提供了便捷的状态管理，但不当使用会导致性能问题。主要问题是Context值改变时，所有消费者组件都会重渲染，即使它们只使用Context中的一小部分数据。性能优化包括：拆分Context、使用useMemo缓存Context值、使用React.memo避免子组件不必要的渲染、以及合理设计Context的数据结构。

  While Context API provides convenient state management, improper use can cause performance issues. The main problem is all consumer components re-render when Context value changes, even if they only use a small portion of Context data. Performance optimization includes: splitting Contexts, using useMemo to cache Context values, using React.memo to avoid unnecessary child component renders, and properly designing Context data structure.

  **核心特征 | Key Characteristics:**
  - Context拆分：将不同更新频率的数据放在不同Context中 | Context splitting: Place data with different update frequencies in separate Contexts
  - 选择器模式：只订阅需要的部分数据 | Selector pattern: Subscribe only to needed portions of data
  - 记忆化：使用useMemo、useCallback防止创建新引用 | Memoization: Use useMemo, useCallback to prevent creating new references
  - 组件优化：使用React.memo包裹子组件 | Component optimization: Wrap child components with React.memo

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果BookmarksContext包含100个书签，用户添加第101个会重渲染所有消费者吗？| If BookmarksContext contains 100 bookmarks, adding 101st re-renders all consumers?
     **答案 | Answer:** 是的 | Yes - 因为Context的bookmarks数组引用改变了。这就是为什么需要优化：使用React.memo让不相关的组件避免重渲染，或将bookmarks数据和操作函数拆分到不同Context。
     Because Context's bookmarks array reference changed. This is why optimization is needed: use React.memo to prevent unrelated components from re-rendering, or split bookmarks data and operation functions into separate Contexts.

  2. 为什么要用useMemo包裹Context的value对象？| Why wrap Context's value object with useMemo?
     **答案 | Answer:** 防止Provider重渲染时创建新对象 | Prevents creating new object when Provider re-renders - 即使bookmarks数据没变，Provider重渲染会创建新的value对象引用，导致所有消费者重渲染。useMemo只在依赖项变化时创建新对象。
     Even if bookmarks data hasn't changed, Provider re-render creates new value object reference, causing all consumers to re-render. useMemo creates new object only when dependencies change.

  3. React.memo和useMemo有什么区别？| What's the difference between React.memo and useMemo?
     **答案 | Answer:** React.memo用于组件，useMemo用于值 | React.memo for components, useMemo for values - React.memo是高阶组件，包裹组件防止props没变时重渲染。useMemo是Hook，缓存计算结果防止重复计算。两者都是性能优化手段。
     React.memo is a higher-order component, wraps components to prevent re-render when props unchanged. useMemo is a Hook, caches computed results to prevent redundant calculations. Both are performance optimization techniques.

  4. Context拆分的标准是什么？| What's the criterion for splitting Contexts?
     **答案 | Answer:** 根据更新频率和使用范围 | Based on update frequency and usage scope - 频繁更新的数据（如输入框值）应该独立Context；不同功能域的数据（书签、主题、用户信息）应该分开；全局使用和局部使用的数据也应该分开。
     Frequently updated data (like input values) should have separate Context; data from different functional domains (bookmarks, theme, user info) should be separated; globally used and locally used data should also be separated.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Context性能优化最佳实践 | Context performance optimization best practices
  import React, { createContext, useContext, useMemo, useCallback, memo } from 'react';

  // 1. Context拆分策略 | Context splitting strategy

  // 方案A：单一Context（可能导致性能问题） | Approach A: Single Context (may cause performance issues)
  const SingleBookmarksContext = createContext(null);

  const SingleBookmarksProvider = ({ children }) => {
    const [bookmarks, setBookmarks] = useState([]);
    const [isLoading, setIsLoading] = useState(false);
    const [filter, setFilter] = useState(''); // 假设有过滤功能 | Assuming there's filter functionality

    // 问题：filter变化会导致所有使用bookmarks的组件重渲染
    // Problem: filter change causes all components using bookmarks to re-render
    const value = useMemo(() => ({
      bookmarks,
      isLoading,
      filter,
      setFilter,
      addBookmark,
      removeBookmark,
    }), [bookmarks, isLoading, filter]);

    return (
      <SingleBookmarksContext.Provider value={value}>
        {children}
      </SingleBookmarksContext.Provider>
    );
  };

  // 方案B：拆分Context（推荐） | Approach B: Split Contexts (recommended)
  const BookmarksDataContext = createContext(null);
  const BookmarksActionsContext = createContext(null);

  const OptimizedBookmarksProvider = ({ children }) => {
    const [bookmarks, setBookmarks] = useState([]);
    const [isLoading, setIsLoading] = useState(false);

    // 数据Context：包含经常读取但不常变化的数据
    // Data Context: Contains frequently read but infrequently changed data
    const dataValue = useMemo(() => ({
      bookmarks,
      isLoading,
    }), [bookmarks, isLoading]);

    // 操作Context：包含函数（函数引用稳定） | Actions Context: Contains functions (stable function references)
    const actionsValue = useMemo(() => ({
      addBookmark: (article) => {
        setBookmarks(prev => [...prev, article]);
      },
      removeBookmark: (url) => {
        setBookmarks(prev => prev.filter(item => item.url !== url));
      },
    }), []); // 空依赖数组，函数永远不变 | Empty dependency array, functions never change

    return (
      <BookmarksDataContext.Provider value={dataValue}>
        <BookmarksActionsContext.Provider value={actionsValue}>
          {children}
        </BookmarksActionsContext.Provider>
      </BookmarksDataContext.Provider>
    );
  };

  // 自定义Hooks | Custom Hooks
  const useBookmarksData = () => {
    const context = useContext(BookmarksDataContext);
    if (!context) throw new Error('useBookmarksData must be used within provider');
    return context;
  };

  const useBookmarksActions = () => {
    const context = useContext(BookmarksActionsContext);
    if (!context) throw new Error('useBookmarksActions must be used within provider');
    return context;
  };

  // 2. 选择器模式（只订阅需要的数据） | Selector pattern (subscribe only to needed data)
  const useBookmarkById = (articleUrl) => {
    const { bookmarks } = useBookmarksData();

    // 使用useMemo避免每次都查找 | Use useMemo to avoid searching every time
    return useMemo(() => {
      return bookmarks.find(item => item.url === articleUrl);
    }, [bookmarks, articleUrl]);
  };

  const useBookmarksCount = () => {
    const { bookmarks } = useBookmarksData();

    // 只订阅数量，不订阅具体内容 | Subscribe only to count, not specific content
    return useMemo(() => bookmarks.length, [bookmarks]);
  };

  // 3. 组件级优化 | Component-level optimization

  // 未优化的列表项 | Unoptimized list item
  const BookmarkItemUnoptimized = ({ article, onDelete }) => {
    console.log('Rendering:', article.title); // 会看到大量日志 | Will see lots of logs

    return (
      <View style={styles.item}>
        <Text>{article.title}</Text>
        <Pressable onPress={onDelete}>
          <Ionicons name="trash-outline" size={24} />
        </Pressable>
      </View>
    );
  };

  // 优化的列表项（使用React.memo） | Optimized list item (using React.memo)
  const BookmarkItemOptimized = memo(({ article, onDelete }) => {
    console.log('Rendering:', article.title); // 只在props变化时打印 | Only logs when props change

    return (
      <View style={styles.item}>
        <Text>{article.title}</Text>
        <Pressable onPress={onDelete}>
          <Ionicons name="trash-outline" size={24} />
        </Pressable>
      </View>
    );
  }, (prevProps, nextProps) => {
    // 自定义比较函数：只有url变化才重渲染 | Custom comparison: re-render only when url changes
    return prevProps.article.url === nextProps.article.url;
  });

  // 4. 列表渲染优化 | List rendering optimization
  const BookmarksListOptimized = () => {
    const { bookmarks } = useBookmarksData();
    const { removeBookmark } = useBookmarksActions();

    // 使用useCallback确保onDelete引用稳定 | Use useCallback to ensure stable onDelete reference
    const handleDelete = useCallback((articleUrl) => {
      removeBookmark(articleUrl);
    }, [removeBookmark]);

    // 使用keyExtractor和getItemLayout优化FlatList | Optimize FlatList with keyExtractor and getItemLayout
    const keyExtractor = useCallback((item) => item.url, []);

    const getItemLayout = useCallback((data, index) => ({
      length: 100, // 假设每项高度100 | Assuming each item height 100
      offset: 100 * index,
      index,
    }), []);

    const renderItem = useCallback(({ item }) => (
      <BookmarkItemOptimized
        article={item}
        onDelete={() => handleDelete(item.url)}
      />
    ), [handleDelete]);

    return (
      <FlatList
        data={bookmarks}
        renderItem={renderItem}
        keyExtractor={keyExtractor}
        getItemLayout={getItemLayout}
        // 性能优化配置 | Performance optimization config
        removeClippedSubviews={true} // Android性能优化 | Android performance optimization
        maxToRenderPerBatch={10} // 每批渲染10项 | Render 10 items per batch
        updateCellsBatchingPeriod={50} // 50ms批处理 | 50ms batching period
        windowSize={10} // 渲染窗口大小 | Render window size
      />
    );
  };

  // 5. 计算密集型操作优化 | Compute-intensive operation optimization
  const BookmarksStatistics = () => {
    const { bookmarks } = useBookmarksData();

    // 使用useMemo缓存计算结果 | Use useMemo to cache computed results
    const statistics = useMemo(() => {
      console.log('Computing statistics...'); // 只在bookmarks变化时计算 | Only compute when bookmarks change

      const totalBookmarks = bookmarks.length;

      // 按来源分组 | Group by source
      const bySource = bookmarks.reduce((acc, item) => {
        const source = item.source?.name || 'Unknown';
        acc[source] = (acc[source] || 0) + 1;
        return acc;
      }, {});

      // 最近添加的5个 | Most recent 5
      const recent = [...bookmarks]
        .sort((a, b) => new Date(b.bookmarkedAt) - new Date(a.bookmarkedAt))
        .slice(0, 5);

      return {
        totalBookmarks,
        bySource,
        recent,
      };
    }, [bookmarks]);

    return (
      <View>
        <Text>总书签数 | Total: {statistics.totalBookmarks}</Text>
        <Text>来源分布 | By Source:</Text>
        {Object.entries(statistics.bySource).map(([source, count]) => (
          <Text key={source}>{source}: {count}</Text>
        ))}
      </View>
    );
  };

  // 6. Context Provider优化 | Context Provider optimization
  const OptimizedAppProvider = ({ children }) => {
    // 使用多个Provider嵌套，每个管理独立的状态域
    // Use multiple nested Providers, each managing independent state domain
    return (
      <ThemeProvider>
        <AuthProvider>
          <BookmarksProvider>
            <SettingsProvider>
              {children}
            </SettingsProvider>
          </BookmarksProvider>
        </AuthProvider>
      </ThemeProvider>
    );
  };

  // 7. 性能监控Hook | Performance monitoring Hook
  const useRenderCount = (componentName) => {
    const renderCount = useRef(0);

    useEffect(() => {
      renderCount.current += 1;
      console.log(`${componentName} rendered ${renderCount.current} times`);
    });

    return renderCount.current;
  };

  // 使用示例 | Usage example
  const MonitoredComponent = () => {
    const renderCount = useRenderCount('BookmarksList');

    return (
      <View>
        <Text>This component has rendered {renderCount} times</Text>
      </View>
    );
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要将操作函数放在单独的Context中？| Why put operation functions in a separate Context?
    **答案 | Answer:** 因为函数引用永远不变 | Because function references never change - 使用useCallback和空依赖数组，函数引用稳定，不会触发消费者重渲染。而数据经常变化，分离后只有使用数据的组件才重渲染。
    Using useCallback with empty dependency array, function reference is stable, won't trigger consumer re-renders. While data changes frequently, separation means only components using data re-render.

  - getItemLayout对FlatList有什么性能提升？| How does getItemLayout improve FlatList performance?
    **答案 | Answer:** 跳过布局计算 | Skips layout calculation - FlatList知道每项的精确高度和位置，可以立即跳转到任意位置、更准确地计算滚动范围。对于固定高度的列表项，这是重要的性能优化。
    FlatList knows exact height and position of each item, can instantly jump to any position, more accurately calculate scroll range. Important performance optimization for fixed-height list items.

  **常见误区检查 | Common Misconception Checks:**
  - 是否应该为每个状态都使用useMemo？| Should you use useMemo for every piece of state?
    **答案 | Answer:** 不应该 | No - useMemo本身有开销（记忆和比较）。只在计算昂贵或创建复杂对象/数组时使用。简单的字符串、数字、布尔值不需要useMemo。过度优化反而降低性能。
    useMemo itself has overhead (memoization and comparison). Only use for expensive computations or creating complex objects/arrays. Simple strings, numbers, booleans don't need useMemo. Over-optimization actually reduces performance.

  - React.memo会阻止所有重渲染吗？| Does React.memo prevent all re-renders?
    **答案 | Answer:** 不会 | No - React.memo只做浅比较props。如果传入的props是对象或数组，每次父组件渲染时都会创建新引用，React.memo无效。需要配合useMemo/useCallback使用。
    React.memo only does shallow prop comparison. If passed props are objects or arrays, new references created on every parent render, React.memo ineffective. Must use with useMemo/useCallback.

## 实践项目：完整书签系统 | Practical Project: Complete Bookmark System

### 目标 | Objective
构建一个功能完整、性能优化的新闻应用书签系统，整合Context状态管理、AsyncStorage持久化、手势交互、动画反馈等本日所学的所有核心概念。该系统应该支持跨屏幕同步、离线访问、优雅的用户交互，并且能够处理边界情况和错误。

Build a feature-complete, performance-optimized news app bookmark system, integrating all core concepts learned today: Context state management, AsyncStorage persistence, gesture interactions, animation feedback. The system should support cross-screen synchronization, offline access, elegant user interactions, and handle edge cases and errors.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. Context API和useReducer如何协同工作管理复杂状态？| How do Context API and useReducer work together to manage complex state?
   **答案 | Answer:** Context提供跨组件数据传递机制，useReducer提供集中式状态更新逻辑。将两者结合：useReducer管理状态和dispatch函数，通过Context Provider传递给子组件，子组件通过useContext获取状态和dispatch，调用dispatch更新状态。
   Context provides cross-component data passing mechanism, useReducer provides centralized state update logic. Combining both: useReducer manages state and dispatch function, pass through Context Provider to child components, child components get state and dispatch via useContext, call dispatch to update state.

2. AsyncStorage的数据为什么需要JSON序列化？| Why does AsyncStorage data need JSON serialization?
   **答案 | Answer:** AsyncStorage只能存储字符串类型。JavaScript对象、数组等复杂类型必须通过JSON.stringify()转换为字符串存储，读取时通过JSON.parse()转换回原类型。这是键值存储系统的常见限制。
   AsyncStorage can only store string types. JavaScript objects, arrays and other complex types must be converted to strings via JSON.stringify() for storage, converted back via JSON.parse() when reading. This is a common limitation of key-value storage systems.

3. 为什么Swipeable组件比自己实现手势处理更好？| Why is Swipeable component better than implementing gesture handling yourself?
   **答案 | Answer:** Swipeable提供了经过优化的原生级别手势识别、预定义的动画曲线、阈值处理、回弹效果等。手动实现需要处理大量边界情况、性能优化、跨平台差异。使用成熟组件节省开发时间、减少bug、提升用户体验一致性。
   Swipeable provides optimized native-level gesture recognition, predefined animation curves, threshold handling, bounce effects. Manual implementation requires handling numerous edge cases, performance optimization, cross-platform differences. Using mature components saves development time, reduces bugs, improves UX consistency.

### 步骤 | Steps

1. **项目初始化 | Project Initialization (15分钟 | 15 minutes)**
   - 在现有News Reader项目中创建`/src/contexts/BookmarksContext.js`
   - 创建`/src/components/BookmarkButton.js`组件
   - 安装依赖：`npx expo install @react-native-async-storage/async-storage react-native-gesture-handler expo-haptics`
   - 在`App.js`根组件包裹`BookmarksProvider`

2. **实现BookmarksContext | Implement BookmarksContext (30分钟 | 30 minutes)**
   - 定义bookmarks的数据结构（url、title、description、image、source、bookmarkedAt等）
   - 创建bookmarksReducer处理ADD、REMOVE、CLEAR、LOAD等actions
   - 实现loadBookmarks和saveBookmarks函数与AsyncStorage交互
   - 在Provider的useEffect中自动加载和保存书签
   - 提供addBookmark、removeBookmark、isBookmarked、toggleBookmark等函数
   - 使用useMemo优化Context value对象

3. **ArticleDetail书签按钮 | ArticleDetail Bookmark Button (25分钟 | 25 minutes)**
   - 创建AnimatedBookmarkButton组件
   - 使用useBookmarks Hook获取书签状态和操作函数
   - 实现书签状态的缩放和颜色动画（Animated.sequence）
   - 添加触觉反馈（Haptics API）
   - 在导航栏headerRight中显示书签按钮
   - 处理loading状态，防止重复点击

4. **Bookmarks列表屏幕 | Bookmarks List Screen (40分钟 | 40 minutes)**
   - 创建BookmarksScreen组件
   - 使用FlatList渲染书签列表
   - 为每个列表项添加Swipeable组件实现滑动删除
   - 实现renderRightActions，显示删除按钮并带动画
   - 添加删除确认Alert（可选，或直接删除）
   - 实现ListEmptyComponent显示友好的空状态
   - 添加排序功能（按时间、标题、来源）
   - 在列表头部显示书签总数和"清空全部"按钮

5. **性能优化 | Performance Optimization (20分钟 | 20 minutes)**
   - 使用React.memo包裹BookmarkItem组件
   - 为FlatList添加keyExtractor、getItemLayout、removeClippedSubviews
   - 使用useCallback包裹事件处理函数
   - 测试：连续添加20个书签，观察性能表现
   - 使用React DevTools Profiler分析重渲染

6. **错误处理与边界情况 | Error Handling & Edge Cases (20分钟 | 20 minutes)**
   - 处理AsyncStorage读写失败（显示错误消息，提供重试按钮）
   - 处理重复书签（检查URL是否已存在）
   - 处理缺少图片的文章（显示占位图）
   - 处理超长标题（使用numberOfLines截断）
   - 添加书签数量限制（可选，如最多100个）
   - 实现数据版本控制，为未来迁移做准备

7. **测试与完善 | Testing & Polishing (10分钟 | 10 minutes)**
   - 测试流程：在ArticleDetail添加书签 → 切换到Bookmarks页查看 → 滑动删除 → 返回ArticleDetail确认状态变化
   - 测试应用重启后书签是否保留
   - 测试快速点击书签按钮是否有问题
   - 测试清空全部书签功能
   - 优化动画时长和阈值，确保交互流畅
   - 添加Toast提示"书签已添加"/"书签已移除"（可选）

### 示例代码 | Example Code
```javascript
/**
 * 完整书签系统实现 | Complete Bookmark System Implementation
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - Context API + useReducer全局状态管理 | Context API + useReducer global state management
 * - AsyncStorage数据持久化 | AsyncStorage data persistence
 * - react-native-gesture-handler手势交互 | react-native-gesture-handler gesture interaction
 * - Animated API动画反馈 | Animated API animation feedback
 * - React.memo性能优化 | React.memo performance optimization
 * - 跨屏幕状态同步 | Cross-screen state synchronization
 */

// ==================== 1. BookmarksContext.js ====================
import React, { createContext, useContext, useReducer, useEffect, useCallback, useMemo } from 'react';
import AsyncStorage from '@react-native-async-storage/async-storage';

const BookmarksContext = createContext(null);
const STORAGE_KEY = '@news_reader_bookmarks_v1';
const MAX_BOOKMARKS = 100; // 最大书签数限制 | Max bookmarks limit

// Action类型 | Action types
const ACTIONS = {
  LOAD_SUCCESS: 'LOAD_SUCCESS',
  LOAD_ERROR: 'LOAD_ERROR',
  ADD_BOOKMARK: 'ADD_BOOKMARK',
  REMOVE_BOOKMARK: 'REMOVE_BOOKMARK',
  CLEAR_ALL: 'CLEAR_ALL',
  SYNC_SUCCESS: 'SYNC_SUCCESS',
  SYNC_ERROR: 'SYNC_ERROR',
};

// Reducer函数 | Reducer function
const bookmarksReducer = (state, action) => {
  switch (action.type) {
    case ACTIONS.LOAD_SUCCESS:
      return {
        ...state,
        bookmarks: action.payload,
        isLoading: false,
        error: null,
      };

    case ACTIONS.LOAD_ERROR:
      return {
        ...state,
        isLoading: false,
        error: action.payload,
      };

    case ACTIONS.ADD_BOOKMARK: {
      // 检查是否已存在 | Check if exists
      const exists = state.bookmarks.some(item => item.url === action.payload.url);
      if (exists) return state;

      // 检查是否达到上限 | Check if reached limit
      if (state.bookmarks.length >= MAX_BOOKMARKS) {
        return {
          ...state,
          error: `最多保存${MAX_BOOKMARKS}个书签 | Max ${MAX_BOOKMARKS} bookmarks allowed`,
        };
      }

      return {
        ...state,
        bookmarks: [
          {
            ...action.payload,
            bookmarkedAt: new Date().toISOString(),
            id: Date.now().toString(), // 唯一ID | Unique ID
          },
          ...state.bookmarks, // 新书签在前 | New bookmarks first
        ],
        isSyncing: true,
      };
    }

    case ACTIONS.REMOVE_BOOKMARK:
      return {
        ...state,
        bookmarks: state.bookmarks.filter(item => item.url !== action.payload),
        isSyncing: true,
      };

    case ACTIONS.CLEAR_ALL:
      return {
        ...state,
        bookmarks: [],
        isSyncing: true,
      };

    case ACTIONS.SYNC_SUCCESS:
      return {
        ...state,
        isSyncing: false,
        lastSynced: new Date().toISOString(),
        syncError: null,
      };

    case ACTIONS.SYNC_ERROR:
      return {
        ...state,
        isSyncing: false,
        syncError: action.payload,
      };

    default:
      return state;
  }
};

// Provider组件 | Provider component
export const BookmarksProvider = ({ children }) => {
  const [state, dispatch] = useReducer(bookmarksReducer, {
    bookmarks: [],
    isLoading: true,
    error: null,
    isSyncing: false,
    syncError: null,
    lastSynced: null,
  });

  // 加载书签 | Load bookmarks
  useEffect(() => {
    loadBookmarks();
  }, []);

  // 自动保存 | Auto-save
  useEffect(() => {
    if (!state.isLoading && state.isSyncing) {
      saveBookmarks();
    }
  }, [state.bookmarks, state.isLoading, state.isSyncing]);

  // 从AsyncStorage加载 | Load from AsyncStorage
  const loadBookmarks = async () => {
    try {
      const stored = await AsyncStorage.getItem(STORAGE_KEY);
      const data = stored ? JSON.parse(stored) : { bookmarks: [], version: '1.0' };

      // 数据迁移逻辑（如果需要） | Data migration logic (if needed)
      if (data.version !== '1.0') {
        console.log('Migrating data...');
        // 执行迁移 | Perform migration
      }

      dispatch({ type: ACTIONS.LOAD_SUCCESS, payload: data.bookmarks });
    } catch (error) {
      console.error('Failed to load bookmarks:', error);
      dispatch({ type: ACTIONS.LOAD_ERROR, payload: error.message });
    }
  };

  // 保存到AsyncStorage | Save to AsyncStorage
  const saveBookmarks = async () => {
    try {
      const dataToStore = {
        bookmarks: state.bookmarks,
        version: '1.0',
        lastUpdated: new Date().toISOString(),
      };

      await AsyncStorage.setItem(STORAGE_KEY, JSON.stringify(dataToStore));
      dispatch({ type: ACTIONS.SYNC_SUCCESS });
    } catch (error) {
      console.error('Failed to save bookmarks:', error);
      dispatch({ type: ACTIONS.SYNC_ERROR, payload: error.message });
    }
  };

  // 添加书签 | Add bookmark
  const addBookmark = useCallback((article) => {
    dispatch({ type: ACTIONS.ADD_BOOKMARK, payload: article });
  }, []);

  // 删除书签 | Remove bookmark
  const removeBookmark = useCallback((articleUrl) => {
    dispatch({ type: ACTIONS.REMOVE_BOOKMARK, payload: articleUrl });
  }, []);

  // 清空所有 | Clear all
  const clearAll = useCallback(() => {
    dispatch({ type: ACTIONS.CLEAR_ALL });
  }, []);

  // 检查是否已书签 | Check if bookmarked
  const isBookmarked = useCallback((articleUrl) => {
    return state.bookmarks.some(item => item.url === articleUrl);
  }, [state.bookmarks]);

  // 切换书签 | Toggle bookmark
  const toggleBookmark = useCallback((article) => {
    if (isBookmarked(article.url)) {
      removeBookmark(article.url);
    } else {
      addBookmark(article);
    }
  }, [isBookmarked, addBookmark, removeBookmark]);

  // Context值 | Context value
  const value = useMemo(() => ({
    bookmarks: state.bookmarks,
    isLoading: state.isLoading,
    error: state.error,
    isSyncing: state.isSyncing,
    syncError: state.syncError,
    lastSynced: state.lastSynced,
    addBookmark,
    removeBookmark,
    clearAll,
    isBookmarked,
    toggleBookmark,
  }), [
    state.bookmarks,
    state.isLoading,
    state.error,
    state.isSyncing,
    state.syncError,
    state.lastSynced,
    addBookmark,
    removeBookmark,
    clearAll,
    isBookmarked,
    toggleBookmark,
  ]);

  return (
    <BookmarksContext.Provider value={value}>
      {children}
    </BookmarksContext.Provider>
  );
};

// 自定义Hook | Custom Hook
export const useBookmarks = () => {
  const context = useContext(BookmarksContext);
  if (!context) {
    throw new Error('useBookmarks must be used within BookmarksProvider');
  }
  return context;
};

// ==================== 2. BookmarkButton.js ====================
import React, { useRef, useEffect } from 'react';
import { Pressable, Animated, StyleSheet } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import * as Haptics from 'expo-haptics';
import { useBookmarks } from '../contexts/BookmarksContext';

export const BookmarkButton = ({ article }) => {
  const { isBookmarked, toggleBookmark, isSyncing } = useBookmarks();
  const bookmarked = isBookmarked(article.url);

  // 动画值 | Animation values
  const scaleAnim = useRef(new Animated.Value(1)).current;
  const colorAnim = useRef(new Animated.Value(bookmarked ? 1 : 0)).current;

  // 书签状态变化时播放动画 | Play animation on bookmark state change
  useEffect(() => {
    // 缩放动画 | Scale animation
    Animated.sequence([
      Animated.spring(scaleAnim, {
        toValue: 1.3,
        friction: 3,
        tension: 40,
        useNativeDriver: true,
      }),
      Animated.spring(scaleAnim, {
        toValue: 1,
        friction: 3,
        tension: 40,
        useNativeDriver: true,
      }),
    ]).start();

    // 颜色动画 | Color animation
    Animated.timing(colorAnim, {
      toValue: bookmarked ? 1 : 0,
      duration: 200,
      useNativeDriver: false,
    }).start();

    // 触觉反馈 | Haptic feedback
    if (bookmarked) {
      Haptics.notificationAsync(Haptics.NotificationFeedbackType.Success);
    }
  }, [bookmarked]);

  const handlePress = () => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Medium);
    toggleBookmark(article);
  };

  const iconColor = colorAnim.interpolate({
    inputRange: [0, 1],
    outputRange: ['#999', '#FF6347'],
  });

  return (
    <Pressable
      onPress={handlePress}
      disabled={isSyncing}
      style={({ pressed }) => [
        styles.button,
        pressed && styles.pressed,
      ]}
      hitSlop={10}
    >
      <Animated.View style={{ transform: [{ scale: scaleAnim }] }}>
        <Ionicons
          name={bookmarked ? 'bookmark' : 'bookmark-outline'}
          size={28}
          color={iconColor}
        />
      </Animated.View>
    </Pressable>
  );
};

const styles = StyleSheet.create({
  button: {
    marginRight: 16,
    padding: 4,
  },
  pressed: {
    opacity: 0.6,
  },
});

// ==================== 3. BookmarksScreen.js ====================
import React, { useState, useCallback } from 'react';
import {
  View,
  Text,
  FlatList,
  StyleSheet,
  Image,
  Pressable,
  Alert,
} from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import Swipeable from 'react-native-gesture-handler/Swipeable';
import { useBookmarks } from '../contexts/BookmarksContext';

// 书签列表项组件 | Bookmark list item component
const BookmarkItem = React.memo(({ article, onPress, onDelete }) => {
  const renderRightActions = (progress, dragX) => {
    const backgroundColor = dragX.interpolate({
      inputRange: [-100, -50, 0],
      outputRange: ['#D32F2F', '#FF3B30', '#FF6347'],
      extrapolate: 'clamp',
    });

    return (
      <Animated.View style={[styles.deleteAction, { backgroundColor }]}>
        <Pressable style={styles.deleteButton} onPress={onDelete}>
          <Ionicons name="trash-outline" size={24} color="#fff" />
          <Text style={styles.deleteText}>删除 | Delete</Text>
        </Pressable>
      </Animated.View>
    );
  };

  return (
    <Swipeable
      renderRightActions={renderRightActions}
      rightThreshold={40}
      friction={2}
    >
      <Pressable style={styles.item} onPress={onPress}>
        {article.urlToImage ? (
          <Image
            source={{ uri: article.urlToImage }}
            style={styles.thumbnail}
            resizeMode="cover"
          />
        ) : (
          <View style={[styles.thumbnail, styles.placeholderThumbnail]}>
            <Ionicons name="image-outline" size={32} color="#ccc" />
          </View>
        )}

        <View style={styles.itemContent}>
          <Text style={styles.itemTitle} numberOfLines={2}>
            {article.title}
          </Text>
          <Text style={styles.itemDescription} numberOfLines={2}>
            {article.description}
          </Text>
          <View style={styles.itemMeta}>
            <Text style={styles.itemSource}>{article.source?.name}</Text>
            <Text style={styles.itemDate}>
              {new Date(article.bookmarkedAt).toLocaleDateString()}
            </Text>
          </View>
        </View>
      </Pressable>
    </Swipeable>
  );
}, (prevProps, nextProps) => {
  return prevProps.article.url === nextProps.article.url;
});

// 书签屏幕主组件 | Bookmarks screen main component
export const BookmarksScreen = ({ navigation }) => {
  const { bookmarks, removeBookmark, clearAll, isLoading } = useBookmarks();
  const [sortBy, setSortBy] = useState('date'); // 'date', 'title', 'source'

  // 排序书签 | Sort bookmarks
  const sortedBookmarks = useMemo(() => {
    const sorted = [...bookmarks];
    switch (sortBy) {
      case 'date':
        return sorted.sort((a, b) =>
          new Date(b.bookmarkedAt) - new Date(a.bookmarkedAt)
        );
      case 'title':
        return sorted.sort((a, b) => a.title.localeCompare(b.title));
      case 'source':
        return sorted.sort((a, b) =>
          a.source?.name.localeCompare(b.source?.name)
        );
      default:
        return sorted;
    }
  }, [bookmarks, sortBy]);

  // 处理删除 | Handle delete
  const handleDelete = useCallback((article) => {
    Alert.alert(
      '删除书签 | Delete Bookmark',
      `确定要删除《${article.title}》吗？| Are you sure you want to delete "${article.title}"?`,
      [
        { text: '取消 | Cancel', style: 'cancel' },
        {
          text: '删除 | Delete',
          style: 'destructive',
          onPress: () => removeBookmark(article.url),
        },
      ]
    );
  }, [removeBookmark]);

  // 处理清空全部 | Handle clear all
  const handleClearAll = useCallback(() => {
    if (bookmarks.length === 0) return;

    Alert.alert(
      '清空所有书签 | Clear All Bookmarks',
      `确定要删除全部 ${bookmarks.length} 个书签吗？此操作无法撤销。| Are you sure you want to delete all ${bookmarks.length} bookmarks? This cannot be undone.`,
      [
        { text: '取消 | Cancel', style: 'cancel' },
        {
          text: '清空 | Clear All',
          style: 'destructive',
          onPress: clearAll,
        },
      ]
    );
  }, [bookmarks.length, clearAll]);

  // 渲染列表头 | Render list header
  const renderHeader = useCallback(() => (
    <View style={styles.header}>
      <Text style={styles.headerTitle}>
        我的书签 | My Bookmarks ({bookmarks.length})
      </Text>
      <View style={styles.headerActions}>
        <Pressable style={styles.sortButton} onPress={() => {
          Alert.alert('排序方式 | Sort By', '', [
            { text: '按时间 | By Date', onPress: () => setSortBy('date') },
            { text: '按标题 | By Title', onPress: () => setSortBy('title') },
            { text: '按来源 | By Source', onPress: () => setSortBy('source') },
            { text: '取消 | Cancel', style: 'cancel' },
          ]);
        }}>
          <Ionicons name="funnel-outline" size={20} color="#666" />
        </Pressable>
        {bookmarks.length > 0 && (
          <Pressable onPress={handleClearAll}>
            <Text style={styles.clearText}>清空 | Clear All</Text>
          </Pressable>
        )}
      </View>
    </View>
  ), [bookmarks.length, handleClearAll]);

  // 渲染空状态 | Render empty state
  const renderEmpty = useCallback(() => (
    <View style={styles.emptyContainer}>
      <Ionicons name="bookmark-outline" size={80} color="#ccc" />
      <Text style={styles.emptyTitle}>还没有书签 | No Bookmarks Yet</Text>
      <Text style={styles.emptyDescription}>
        保存您喜欢的文章，稍后阅读{'\n'}
        Bookmark your favorite articles to read later
      </Text>
      <Pressable
        style={styles.browseButton}
        onPress={() => navigation.navigate('Home')}
      >
        <Text style={styles.browseButtonText}>
          浏览文章 | Browse Articles
        </Text>
      </Pressable>
    </View>
  ), [navigation]);

  // 渲染列表项 | Render list item
  const renderItem = useCallback(({ item }) => (
    <BookmarkItem
      article={item}
      onPress={() => navigation.navigate('ArticleDetail', { article: item })}
      onDelete={() => handleDelete(item)}
    />
  ), [navigation, handleDelete]);

  const keyExtractor = useCallback((item) => item.id || item.url, []);

  if (isLoading) {
    return (
      <View style={styles.centerContainer}>
        <ActivityIndicator size="large" color="#FF6347" />
      </View>
    );
  }

  return (
    <View style={styles.container}>
      <FlatList
        data={sortedBookmarks}
        renderItem={renderItem}
        keyExtractor={keyExtractor}
        ListHeaderComponent={renderHeader}
        ListEmptyComponent={renderEmpty}
        contentContainerStyle={
          bookmarks.length === 0 && styles.emptyContentContainer
        }
        ItemSeparatorComponent={() => <View style={styles.separator} />}
        removeClippedSubviews={true}
        maxToRenderPerBatch={10}
        windowSize={10}
      />
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  centerContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    backgroundColor: '#fff',
    padding: 16,
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  headerTitle: {
    fontSize: 20,
    fontWeight: 'bold',
  },
  headerActions: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  sortButton: {
    padding: 8,
    marginRight: 12,
  },
  clearText: {
    color: '#FF6347',
    fontWeight: '600',
  },
  item: {
    flexDirection: 'row',
    backgroundColor: '#fff',
    padding: 12,
    alignItems: 'center',
  },
  thumbnail: {
    width: 80,
    height: 80,
    borderRadius: 8,
    marginRight: 12,
  },
  placeholderThumbnail: {
    backgroundColor: '#f0f0f0',
    justifyContent: 'center',
    alignItems: 'center',
  },
  itemContent: {
    flex: 1,
  },
  itemTitle: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 4,
  },
  itemDescription: {
    fontSize: 14,
    color: '#666',
    marginBottom: 8,
  },
  itemMeta: {
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  itemSource: {
    fontSize: 12,
    color: '#FF6347',
    fontWeight: '500',
  },
  itemDate: {
    fontSize: 12,
    color: '#999',
  },
  separator: {
    height: 1,
    backgroundColor: '#eee',
  },
  deleteAction: {
    justifyContent: 'center',
    alignItems: 'center',
    width: 80,
  },
  deleteButton: {
    alignItems: 'center',
  },
  deleteText: {
    color: '#fff',
    fontSize: 12,
    marginTop: 4,
  },
  emptyContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    paddingHorizontal: 40,
    paddingTop: 100,
  },
  emptyContentContainer: {
    flexGrow: 1,
  },
  emptyTitle: {
    fontSize: 20,
    fontWeight: '600',
    marginTop: 16,
  },
  emptyDescription: {
    fontSize: 14,
    color: '#999',
    textAlign: 'center',
    marginTop: 8,
    lineHeight: 20,
  },
  browseButton: {
    backgroundColor: '#FF6347',
    paddingVertical: 12,
    paddingHorizontal: 24,
    borderRadius: 8,
    marginTop: 24,
  },
  browseButtonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: '600',
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确实现了Context + useReducer状态管理？| Does the project correctly implement Context + useReducer state management?
2. AsyncStorage的加载和保存是否在正确的时机触发？| Do AsyncStorage load and save trigger at correct timings?
3. 书签状态是否在所有屏幕间保持同步？| Is bookmark state synchronized across all screens?
4. 滑动删除手势是否流畅，动画是否自然？| Is swipe-to-delete gesture smooth with natural animations?
5. 触觉反馈是否在合适的操作时提供？| Is haptic feedback provided for appropriate operations?
6. 是否处理了边界情况（重复书签、缺失图片、空状态）？| Are edge cases handled (duplicate bookmarks, missing images, empty state)?
7. 应用重启后书签是否保留？| Are bookmarks retained after app restart?
8. 性能是否良好（列表滚动流畅，无卡顿）？| Is performance good (smooth list scrolling, no lag)?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Context优化练习 | Context Optimization Exercise**
   - **练习描述 | Exercise Description:** 将当前的BookmarksContext拆分为BookmarksDataContext和BookmarksActionsContext两个独立Context。测试拆分前后的性能差异，使用React DevTools Profiler记录组件渲染次数。
   - **概念检查 | Concept Check:** 为什么拆分Context可以提升性能？提示：考虑函数引用的稳定性和数据变化的频率。
   - **学习目标 | Learning Objective:** 深入理解Context性能优化策略，掌握合理拆分Context的原则。

2. **AsyncStorage高级应用 | Advanced AsyncStorage Application**
   - **练习描述 | Exercise Description:** 实现书签数据的版本迁移功能。创建v1.0和v2.0两种数据格式（v2.0增加tags标签字段），在loadBookmarks时检测版本并自动迁移旧数据。
   - **概念检查 | Concept Check:** 为什么需要版本控制？如何在不丢失用户数据的情况下改变数据结构？
   - **学习目标 | Learning Objective:** 学习生产环境中数据持久化的最佳实践，掌握数据迁移策略。

3. **手势交互创新 | Gesture Interaction Innovation**
   - **练习描述 | Exercise Description:** 在Swipeable的基础上，实现向左滑动删除、向右滑动分享的双向手势。为两个方向设置不同的背景色和图标。
   - **概念检查 | Concept Check:** renderLeftActions和renderRightActions如何配合使用？如何区分两个方向的滑动意图？
   - **学习目标 | Learning Objective:** 提高手势处理的灵活性，创造更丰富的交互体验。

4. **动画效果提升 | Animation Effect Enhancement**
   - **练习描述 | Exercise Description:** 为书签列表添加进入动画：每个列表项从右侧淡入并滑入。使用Animated.stagger创建依次播放的效果。
   - **概念检查 | Concept Check:** Animated.stagger与Animated.sequence的区别是什么？如何控制动画的延迟时间？
   - **学习目标 | Learning Objective:** 掌握复杂动画组合技巧，提升应用的视觉吸引力。

5. **书签分类功能 | Bookmark Categorization Feature**
   - **练习描述 | Exercise Description:** 为书签添加标签(tags)功能，用户可以为每个书签添加多个标签（如"技术"、"财经"、"娱乐"）。在Bookmarks屏幕添加标签过滤功能。
   - **概念检查 | Concept Check:** 如何在现有数据结构中增加tags字段？如何实现多标签过滤逻辑？
   - **学习目标 | Learning Objective:** 扩展数据模型复杂度，实现更高级的数据管理功能。

6. **离线同步策略 | Offline Sync Strategy**
   - **练习描述 | Exercise Description:** 实现网络状态检测，当设备离线时禁用需要网络的功能，但书签功能仍可正常使用。添加一个"同步队列"，记录离线时的操作，联网后批量同步。
   - **概念检查 | Concept Check:** 如何使用NetInfo检测网络状态？如何设计离线操作队列？
   - **学习目标 | Learning Objective:** 理解离线优先(Offline-first)架构，提升应用的健壮性。

7. **书签导出与分享 | Bookmark Export & Sharing**
   - **练习描述 | Exercise Description:** 实现"导出书签"功能，将所有书签导出为JSON文件，使用Sharing API分享给其他应用。实现"导入书签"功能，从JSON文件恢复书签。
   - **概念检查 | Concept Check:** 如何使用Expo FileSystem读写文件？如何使用Sharing API分享文件？
   - **学习目标 | Learning Objective:** 掌握文件操作和跨应用分享功能，提供更多用户价值。

## 学习资源 | Learning Resources
- [React Context API官方文档 | React Context API Official Documentation](https://react.dev/learn/passing-data-deeply-with-context)
- [React useReducer Hook文档 | React useReducer Hook Documentation](https://react.dev/reference/react/useReducer)
- [AsyncStorage官方指南 | AsyncStorage Official Guide](https://react-native-async-storage.github.io/async-storage/docs/usage/)
- [react-native-gesture-handler文档 | react-native-gesture-handler Documentation](https://docs.swmansion.com/react-native-gesture-handler/)
- [React Native Animated API | React Native Animated API](https://reactnative.dev/docs/animated)
- [Expo Haptics API | Expo Haptics API](https://docs.expo.dev/versions/latest/sdk/haptics/)
- [React性能优化最佳实践 | React Performance Optimization Best Practices](https://react.dev/learn/render-and-commit)
- [Kent C. Dodds: Application State Management with React | Kent C. Dodds: Application State Management with React](https://kentcdodds.com/blog/application-state-management-with-react)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解Context API与useReducer的协同工作原理 | Understand how Context API and useReducer work together
- [ ] 掌握AsyncStorage的CRUD操作和错误处理 | Master AsyncStorage CRUD operations and error handling
- [ ] 能够实现跨屏幕的状态同步 | Can implement cross-screen state synchronization
- [ ] 熟练使用react-native-gesture-handler实现滑动交互 | Proficient in using react-native-gesture-handler for swipe interactions
- [ ] 能够创建流畅的动画反馈和触觉反馈 | Can create smooth animation and haptic feedback
- [ ] 理解并应用React性能优化技巧（memo、useMemo、useCallback） | Understand and apply React performance optimization (memo, useMemo, useCallback)
- [ ] 掌握FlatList性能优化配置 | Master FlatList performance optimization configuration
- [ ] 能够处理数据持久化的边界情况和错误 | Can handle edge cases and errors in data persistence
- [ ] 完成完整书签系统项目并通过所有检查点 | Complete full bookmark system project and pass all checkpoints
- [ ] 至少完成3个扩展练习 | Complete at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释Context状态管理、AsyncStorage持久化、手势处理、性能优化等核心概念。能够独立构建一个包含本地存储和跨屏幕同步的功能模块。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain core concepts like Context state management, AsyncStorage persistence, gesture handling, performance optimization to others. Can independently build a feature module with local storage and cross-screen synchronization.
