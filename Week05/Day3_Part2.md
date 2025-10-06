# React Native入门 - 第3天：防止返回导航与导航守卫（第二部分）| React Native Introduction - Day 3: Preventing Back Navigation & Navigation Guards (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. 不可逆流程设计 | Irreversible Flow Design (1.5小时 | 1.5 hours)

- **结账流程的导航控制 | Checkout Flow Navigation Control**

  **概念定义 | Concept Definition:**
  不可逆流程是指一旦用户完成特定步骤（如支付、提交订单），就不应该允许返回到之前的步骤。这种模式用于防止重复提交、保护数据完整性、提供清晰的用户流程。典型应用包括支付流程、表单提交确认、账号注册完成等场景。 | Irreversible flow refers to preventing users from going back to previous steps once they complete specific actions (like payment, order submission). This pattern prevents duplicate submissions, protects data integrity, and provides clear user flows. Typical applications include payment processes, form submission confirmations, and account registration completion.

  **核心特征 | Key Characteristics:**
  - 多步骤流程，每步有明确的前进条件 | Multi-step process with clear progression criteria for each step
  - 特定步骤完成后禁止返回（如支付完成） | Prevent going back after specific steps (like payment completion)
  - 使用navigation.reset()清除历史堆栈 | Use navigation.reset() to clear navigation history
  - 提供明确的"完成"或"返回首页"出口 | Provide clear "Done" or "Go Home" exit points
  - 结合beforeRemove阻止意外返回 | Combine with beforeRemove to prevent accidental back navigation

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么支付完成后需要禁止返回？| Why prevent going back after payment completion?
     **答案 | Answer:** 防止重复支付、避免状态混乱、保护交易完整性 | Prevent duplicate payments, avoid state confusion, protect transaction integrity
  2. navigation.reset()和navigation.goBack()有什么区别？| What's the difference between navigation.reset() and navigation.goBack()?
     **答案 | Answer:** reset清除整个导航堆栈并设置新的路由，goBack只返回一步 | reset clears entire navigation stack and sets new routes, goBack only goes back one step
  3. 在结账流程的前几步需要禁止返回吗？| Should going back be prevented in early steps of checkout?
     **答案 | Answer:** 否 | No - 早期步骤应允许返回修改，只在关键步骤后禁止 | Early steps should allow going back to modify, only prevent after critical steps
  4. 如何判断流程的哪一步需要设置不可返回？| How to determine which step needs to prevent going back?
     **答案 | Answer:** 涉及不可逆操作的步骤（支付、提交、发送等） | Steps involving irreversible operations (payment, submission, sending, etc.)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, TouchableOpacity, Alert, ActivityIndicator } from 'react-native';

  // 完整的3步结账流程 | Complete 3-step checkout flow
  function CheckoutFlowScreen({ navigation, route }) {
    const { cartItems, totalAmount } = route.params;
    const [currentStep, setCurrentStep] = useState(1);
    const [isProcessing, setIsProcessing] = useState(false);
    const [orderId, setOrderId] = useState(null);

    // 步骤3（支付完成后）禁止返回 | Step 3 (after payment) prevents going back
    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        // 步骤1和2允许返回 | Steps 1 and 2 allow going back
        if (currentStep < 3) {
          return;
        }

        // 步骤3：支付完成，禁止返回 | Step 3: Payment complete, prevent going back
        e.preventDefault();

        Alert.alert(
          '无法返回 | Cannot Go Back',
          '订单已完成，请点击"返回首页"按钮 | Order completed, please click "Go Home" button',
          [{ text: '知道了 | OK', style: 'cancel' }]
        );
      });

      return unsubscribe;
    }, [navigation, currentStep]);

    // 步骤1：配送信息 | Step 1: Shipping Information
    const renderShippingStep = () => (
      <View style={styles.stepContainer}>
        <Text style={styles.stepTitle}>步骤 1/3：配送信息 | Step 1/3: Shipping</Text>
        <Text style={styles.info}>配送地址：北京市朝阳区... | Address: Beijing Chaoyang...</Text>
        <Text style={styles.info}>收货人：张三 | Recipient: Zhang San</Text>
        <Text style={styles.info}>电话：138****1234 | Phone: 138****1234</Text>

        <View style={styles.buttonRow}>
          <TouchableOpacity
            style={[styles.button, styles.secondaryButton]}
            onPress={() => navigation.goBack()}
          >
            <Text style={styles.secondaryButtonText}>返回购物车 | Back to Cart</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.button, styles.primaryButton]}
            onPress={() => setCurrentStep(2)}
          >
            <Text style={styles.buttonText}>下一步 | Next</Text>
          </TouchableOpacity>
        </View>
      </View>
    );

    // 步骤2：确认订单 | Step 2: Confirm Order
    const renderConfirmStep = () => (
      <View style={styles.stepContainer}>
        <Text style={styles.stepTitle}>步骤 2/3：确认订单 | Step 2/3: Confirm</Text>

        <View style={styles.orderSummary}>
          <Text style={styles.sectionTitle}>订单摘要 | Order Summary</Text>
          {cartItems.map((item, index) => (
            <View key={index} style={styles.orderItem}>
              <Text>{item.name} x {item.quantity}</Text>
              <Text>¥{item.price * item.quantity}</Text>
            </View>
          ))}
          <View style={styles.totalRow}>
            <Text style={styles.totalLabel}>总计 | Total:</Text>
            <Text style={styles.totalAmount}>¥{totalAmount}</Text>
          </View>
        </View>

        <View style={styles.buttonRow}>
          <TouchableOpacity
            style={[styles.button, styles.secondaryButton]}
            onPress={() => setCurrentStep(1)}
          >
            <Text style={styles.secondaryButtonText}>上一步 | Previous</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.button, styles.primaryButton]}
            onPress={handlePayment}
            disabled={isProcessing}
          >
            {isProcessing ? (
              <ActivityIndicator color="#fff" />
            ) : (
              <Text style={styles.buttonText}>确认支付 | Pay Now</Text>
            )}
          </TouchableOpacity>
        </View>
      </View>
    );

    // 处理支付 | Handle payment
    const handlePayment = async () => {
      setIsProcessing(true);

      try {
        // 模拟支付API调用 | Simulate payment API call
        await new Promise(resolve => setTimeout(resolve, 2000));

        // 生成订单号 | Generate order ID
        const newOrderId = 'ORD' + Date.now();
        setOrderId(newOrderId);

        // 进入步骤3（此时已不能返回）| Enter step 3 (can't go back now)
        setCurrentStep(3);
      } catch (error) {
        Alert.alert('支付失败 | Payment Failed', '请重试 | Please try again');
      } finally {
        setIsProcessing(false);
      }
    };

    // 步骤3：支付成功（不可返回）| Step 3: Payment Success (irreversible)
    const renderSuccessStep = () => (
      <View style={styles.stepContainer}>
        <View style={styles.successIcon}>
          <Text style={styles.successCheckmark}>✓</Text>
        </View>

        <Text style={styles.successTitle}>支付成功！| Payment Successful!</Text>
        <Text style={styles.successMessage}>您的订单已确认 | Your order is confirmed</Text>

        <View style={styles.orderDetails}>
          <Text style={styles.detailLabel}>订单号 | Order ID:</Text>
          <Text style={styles.detailValue}>{orderId}</Text>

          <Text style={styles.detailLabel}>支付金额 | Amount Paid:</Text>
          <Text style={styles.detailValue}>¥{totalAmount}</Text>

          <Text style={styles.detailLabel}>预计送达 | Estimated Delivery:</Text>
          <Text style={styles.detailValue}>2-3个工作日 | 2-3 business days</Text>
        </View>

        <TouchableOpacity
          style={[styles.button, styles.primaryButton]}
          onPress={goToHome}
        >
          <Text style={styles.buttonText}>返回首页 | Go Home</Text>
        </TouchableOpacity>

        <TouchableOpacity
          style={[styles.button, styles.secondaryButton, styles.marginTop]}
          onPress={viewOrderDetails}
        >
          <Text style={styles.secondaryButtonText}>查看订单详情 | View Order Details</Text>
        </TouchableOpacity>
      </View>
    );

    // 返回首页（重置导航堆栈）| Go home (reset navigation stack)
    const goToHome = () => {
      // 重置导航到首页，清除所有结账相关屏幕 | Reset navigation to home, clear all checkout screens
      navigation.reset({
        index: 0,
        routes: [{ name: 'Home' }],
      });
    };

    // 查看订单详情 | View order details
    const viewOrderDetails = () => {
      // 重置到订单详情页 | Reset to order details page
      navigation.reset({
        index: 0,
        routes: [
          { name: 'Home' },
          { name: 'OrderDetails', params: { orderId } }
        ],
      });
    };

    // 根据当前步骤渲染对应内容 | Render content based on current step
    return (
      <View style={styles.container}>
        {/* 步骤指示器（步骤3不显示）| Step indicator (hidden in step 3) */}
        {currentStep < 3 && (
          <View style={styles.progressBar}>
            <View style={[styles.progressDot, currentStep >= 1 && styles.progressDotActive]} />
            <View style={[styles.progressLine, currentStep >= 2 && styles.progressLineActive]} />
            <View style={[styles.progressDot, currentStep >= 2 && styles.progressDotActive]} />
            <View style={[styles.progressLine, currentStep >= 3 && styles.progressLineActive]} />
            <View style={[styles.progressDot, currentStep >= 3 && styles.progressDotActive]} />
          </View>
        )}

        {currentStep === 1 && renderShippingStep()}
        {currentStep === 2 && renderConfirmStep()}
        {currentStep === 3 && renderSuccessStep()}
      </View>
    );
  }

  // 使用示例：从购物车导航到结账流程 | Usage example: Navigate from cart to checkout
  function CartScreen({ navigation }) {
    const cartItems = [
      { id: 1, name: '商品A | Product A', price: 99, quantity: 2 },
      { id: 2, name: '商品B | Product B', price: 149, quantity: 1 },
    ];

    const totalAmount = cartItems.reduce((sum, item) => sum + item.price * item.quantity, 0);

    const startCheckout = () => {
      navigation.navigate('CheckoutFlow', {
        cartItems,
        totalAmount,
      });
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>购物车 | Shopping Cart</Text>
        {cartItems.map((item) => (
          <View key={item.id} style={styles.cartItem}>
            <Text>{item.name}</Text>
            <Text>¥{item.price} x {item.quantity}</Text>
          </View>
        ))}
        <View style={styles.totalRow}>
          <Text style={styles.totalLabel}>总计 | Total:</Text>
          <Text style={styles.totalAmount}>¥{totalAmount}</Text>
        </View>
        <TouchableOpacity
          style={[styles.button, styles.primaryButton]}
          onPress={startCheckout}
        >
          <Text style={styles.buttonText}>去结账 | Checkout</Text>
        </TouchableOpacity>
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在CheckoutFlowScreen中，如果用户在步骤2按了Android的硬件返回键会发生什么？| In CheckoutFlowScreen, what happens if user presses Android hardware back button in step 2?
    **答案 | Answer:** 允许返回到步骤1或购物车（currentStep < 3） | Allowed to go back to step 1 or cart (currentStep < 3)
  - navigation.reset()的index参数是什么意思？| What does the index parameter in navigation.reset() mean?
    **答案 | Answer:** 指定当前活跃的屏幕在routes数组中的索引 | Specifies the index of currently active screen in routes array
  - 为什么在步骤3需要提供"查看订单详情"按钮？| Why provide "View Order Details" button in step 3?
    **答案 | Answer:** 用户可能需要查看订单信息，但不能返回修改 | Users may need to view order info but can't go back to modify

  **常见误区检查 | Common Misconception Checks:**
  - 误区：所有步骤都应该禁止返回才安全 | Misconception: All steps should prevent going back for safety
    **答案 | Answer:** 错误 | Incorrect - 只在关键不可逆步骤禁止，早期步骤应允许修改 | Only prevent at critical irreversible steps, early steps should allow modification
  - 误区：navigation.reset()会丢失所有导航状态 | Misconception: navigation.reset() loses all navigation state
    **答案 | Answer:** 是的 | Yes - 这是设计目的，用于完全重置导航流程 | That's the design purpose, to completely reset navigation flow

- **navigation.reset()深入应用 | navigation.reset() Advanced Usage**

  **概念定义 | Concept Definition:**
  `navigation.reset()`方法用于完全重置导航状态，替换整个导航堆栈。它接受一个state对象，包含`index`（当前活跃屏幕索引）和`routes`（新的屏幕数组）。常用于登出、完成流程、清除历史等场景。 | `navigation.reset()` method completely resets navigation state, replacing the entire navigation stack. It accepts a state object containing `index` (current active screen index) and `routes` (new screen array). Commonly used for logout, flow completion, clearing history.

  **核心特征 | Key Characteristics:**
  - 清除所有现有屏幕并设置新的屏幕堆栈 | Clears all existing screens and sets new screen stack
  - index指定当前显示的屏幕（从0开始）| index specifies currently displayed screen (0-indexed)
  - routes是新的屏幕数组，每个包含name和可选的params | routes is new screen array, each containing name and optional params
  - 重置后无法返回到之前的屏幕 | Cannot navigate back to previous screens after reset
  - 适用于登出、完成多步流程、错误恢复等场景 | Suitable for logout, completing multi-step flows, error recovery

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. navigation.reset()后用户还能按返回键回到之前的屏幕吗？| Can users press back button to return to previous screens after navigation.reset()?
     **答案 | Answer:** 否 | No - 之前的导航历史被完全清除 | Previous navigation history is completely cleared
  2. index值可以大于routes数组长度吗？| Can index value be greater than routes array length?
     **答案 | Answer:** 否 | No - index必须是有效的routes数组索引 | index must be a valid routes array index
  3. 什么时候使用reset而不是goBack或navigate？| When to use reset instead of goBack or navigate?
     **答案 | Answer:** 需要清除导航历史的场景，如登出、完成流程 | Scenarios requiring clearing navigation history, like logout, flow completion
  4. reset后导航堆栈会立即更新吗？| Does navigation stack update immediately after reset?
     **答案 | Answer:** 是 | Yes - 这是同步操作 | It's a synchronous operation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { CommonActions } from '@react-navigation/native';

  // 示例1：登出后重置到登录页 | Example 1: Reset to login after logout
  function ProfileScreen({ navigation }) {
    const handleLogout = () => {
      Alert.alert(
        '确认登出？| Confirm Logout?',
        '您确定要退出登录吗？| Are you sure you want to logout?',
        [
          { text: '取消 | Cancel', style: 'cancel' },
          {
            text: '登出 | Logout',
            style: 'destructive',
            onPress: () => {
              // 清除所有导航历史，重置到登录页 | Clear all history, reset to login
              navigation.reset({
                index: 0,
                routes: [{ name: 'Login' }],
              });

              // 可选：清除用户数据 | Optional: Clear user data
              // AsyncStorage.removeItem('userToken');
            },
          },
        ]
      );
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>个人中心 | Profile</Text>
        <TouchableOpacity
          style={[styles.button, styles.dangerButton]}
          onPress={handleLogout}
        >
          <Text style={styles.buttonText}>退出登录 | Logout</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 示例2：完成新用户引导后重置 | Example 2: Reset after onboarding
  function OnboardingCompleteScreen({ navigation }) {
    const completeOnboarding = async () => {
      // 标记引导完成 | Mark onboarding complete
      // await AsyncStorage.setItem('onboardingComplete', 'true');

      // 重置到主应用，且不保留引导页历史 | Reset to main app without onboarding history
      navigation.reset({
        index: 0,
        routes: [{ name: 'MainTabs' }],
      });
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>全部完成！| All Done!</Text>
        <Text style={styles.message}>欢迎使用我们的应用 | Welcome to our app</Text>
        <TouchableOpacity
          style={[styles.button, styles.primaryButton]}
          onPress={completeOnboarding}
        >
          <Text style={styles.buttonText}>开始使用 | Get Started</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 示例3：错误恢复 - 重置到安全状态 | Example 3: Error recovery - reset to safe state
  function ErrorBoundaryScreen({ navigation }) {
    const recoverFromError = () => {
      // 发生严重错误时，重置到主页 | When severe error occurs, reset to home
      navigation.reset({
        index: 0,
        routes: [{ name: 'Home' }],
      });
    };

    return (
      <View style={styles.container}>
        <Text style={styles.errorTitle}>出错了 | Something Went Wrong</Text>
        <Text style={styles.errorMessage}>应用遇到了问题 | App encountered an issue</Text>
        <TouchableOpacity
          style={[styles.button, styles.primaryButton]}
          onPress={recoverFromError}
        >
          <Text style={styles.buttonText}>返回首页 | Back to Home</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 示例4：使用CommonActions.reset()（推荐方式）| Example 4: Using CommonActions.reset() (recommended)
  function AnyScreen({ navigation }) {
    const resetNavigation = () => {
      // 使用CommonActions更清晰 | Using CommonActions is clearer
      navigation.dispatch(
        CommonActions.reset({
          index: 0,
          routes: [
            { name: 'Home' },
            { name: 'Profile', params: { userId: 123 } }, // 可以带参数 | Can include params
          ],
        })
      );
    };

    return (
      <TouchableOpacity onPress={resetNavigation}>
        <Text>重置导航 | Reset Navigation</Text>
      </TouchableOpacity>
    );
  }

  // 示例5：多层嵌套导航的重置 | Example 5: Reset in nested navigators
  function NestedNavigationReset({ navigation }) {
    const resetToSpecificTab = () => {
      // 重置到主标签页导航器，并指定活跃标签 | Reset to main tab navigator with specific active tab
      navigation.reset({
        index: 0,
        routes: [
          {
            name: 'MainTabs',
            state: {
              // 嵌套状态：指定Tab导航器内的状态 | Nested state: specify state within Tab navigator
              routes: [
                { name: 'HomeTab' },
                { name: 'ProfileTab' }, // 这个标签将是活跃的 | This tab will be active
              ],
              index: 1, // ProfileTab是活跃的（索引1）| ProfileTab is active (index 1)
            },
          },
        ],
      });
    };

    return (
      <TouchableOpacity onPress={resetToSpecificTab}>
        <Text>重置到个人资料标签 | Reset to Profile Tab</Text>
      </TouchableOpacity>
    );
  }

  // 实用工具函数：重置到首页 | Utility function: Reset to home
  export const resetToHome = (navigation) => {
    navigation.reset({
      index: 0,
      routes: [{ name: 'Home' }],
    });
  };

  // 实用工具函数：重置到认证流程 | Utility function: Reset to auth flow
  export const resetToAuth = (navigation) => {
    navigation.reset({
      index: 0,
      routes: [{ name: 'Login' }],
    });
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果routes数组有3个屏幕，index设为2，会显示哪个屏幕？| If routes array has 3 screens and index is set to 2, which screen will display?
    **答案 | Answer:** 第3个屏幕（索引从0开始）| The 3rd screen (index starts from 0)
  - navigation.reset()和navigation.navigate('Home')的主要区别是什么？| What's the main difference between navigation.reset() and navigation.navigate('Home')?
    **答案 | Answer:** reset清除历史堆栈，navigate保留历史可返回 | reset clears history stack, navigate preserves history allowing back

  **常见误区检查 | Common Misconception Checks:**
  - 误区：reset只能用于登出场景 | Misconception: reset is only for logout scenarios
    **答案 | Answer:** 错误 | Incorrect - 任何需要清除导航历史的场景都可以用 | Can be used in any scenario requiring clearing navigation history
  - 误区：reset后屏幕会有转场动画 | Misconception: reset will have transition animation
    **答案 | Answer:** 通常没有 | Usually not - reset是立即切换，但可以通过配置添加 | reset is immediate switch, but can be configured to add animation

### 5. Android硬件返回键处理 | Android Hardware Back Button Handling (45分钟 | 45 minutes)

- **BackHandler API使用 | Using BackHandler API**

  **概念定义 | Concept Definition:**
  `BackHandler`是React Native提供的API，用于监听和处理Android设备的硬件返回键。虽然React Navigation的`beforeRemove`监听器可以处理大多数返回场景，但在某些情况下（如在根屏幕按返回键退出应用），需要直接使用BackHandler。 | `BackHandler` is a React Native API for listening to and handling Android device hardware back button. While React Navigation's `beforeRemove` listener can handle most back scenarios, in some cases (like pressing back on root screen to exit app), direct BackHandler usage is needed.

  **核心特征 | Key Characteristics:**
  - 只在Android平台有效，iOS无硬件返回键 | Only effective on Android platform, iOS has no hardware back button
  - 返回true表示已处理，阻止默认行为 | Returning true means handled, prevents default behavior
  - 返回false或不返回表示未处理，继续默认行为 | Returning false or nothing means not handled, continues default behavior
  - 可以注册多个监听器，按注册顺序逆序调用 | Can register multiple listeners, called in reverse registration order
  - 需要在useEffect中注册并清理 | Must register and cleanup in useEffect

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. BackHandler在iOS上会生效吗？| Does BackHandler work on iOS?
     **答案 | Answer:** 否 | No - iOS没有硬件返回键 | iOS doesn't have hardware back button
  2. BackHandler回调函数返回true时会发生什么？| What happens when BackHandler callback returns true?
     **答案 | Answer:** 阻止默认返回行为，表示已处理 | Prevents default back behavior, indicating it's handled
  3. 可以同时使用beforeRemove和BackHandler吗？| Can beforeRemove and BackHandler be used together?
     **答案 | Answer:** 是 | Yes - 但通常beforeRemove已足够，BackHandler用于特殊场景 | But beforeRemove is usually sufficient, BackHandler for special scenarios
  4. 如果忘记移除BackHandler监听器会怎样？| What happens if you forget to remove BackHandler listener?
     **答案 | Answer:** 内存泄漏，监听器在组件卸载后仍然存在 | Memory leak, listener persists after component unmounts

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { BackHandler, Alert } from 'react-native';
  import { useEffect } from 'react';

  // 示例1：根屏幕双击返回退出应用 | Example 1: Double-press back on root screen to exit
  function HomeScreen({ navigation }) {
    const [lastBackPressed, setLastBackPressed] = useState(0);

    useEffect(() => {
      const backAction = () => {
        const now = Date.now();

        // 2秒内按两次退出 | Press twice within 2 seconds to exit
        if (now - lastBackPressed < 2000) {
          BackHandler.exitApp(); // 退出应用 | Exit app
          return true; // 已处理 | Handled
        }

        // 第一次按返回键，显示提示 | First back press, show hint
        setLastBackPressed(now);
        Alert.alert('', '再按一次退出应用 | Press again to exit app');
        return true; // 阻止默认返回 | Prevent default back
      };

      // 注册监听器 | Register listener
      const backHandler = BackHandler.addEventListener('hardwareBackPress', backAction);

      // 清理监听器 | Cleanup listener
      return () => backHandler.remove();
    }, [lastBackPressed]);

    return (
      <View style={styles.container}>
        <Text style={styles.title}>主页 | Home</Text>
        <Text style={styles.hint}>按两次返回键退出 | Press back twice to exit</Text>
      </View>
    );
  }

  // 示例2：特定屏幕禁用返回键 | Example 2: Disable back button on specific screen
  function LoadingScreen() {
    useEffect(() => {
      const backAction = () => {
        // 加载中不允许返回 | Don't allow back during loading
        Alert.alert('请稍候 | Please Wait', '正在加载中... | Loading...');
        return true; // 阻止返回 | Prevent back
      };

      const backHandler = BackHandler.addEventListener('hardwareBackPress', backAction);

      return () => backHandler.remove();
    }, []);

    return (
      <View style={styles.container}>
        <ActivityIndicator size="large" />
        <Text>加载中... | Loading...</Text>
      </View>
    );
  }

  // 示例3：结合beforeRemove和BackHandler | Example 3: Combine beforeRemove and BackHandler
  function CriticalFormScreen({ navigation }) {
    const [hasChanges, setHasChanges] = useState(false);

    // beforeRemove处理导航内的返回 | beforeRemove handles in-navigation back
    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        if (!hasChanges) return;

        e.preventDefault();
        showExitConfirmation(() => navigation.dispatch(e.data.action));
      });

      return unsubscribe;
    }, [navigation, hasChanges]);

    // BackHandler处理硬件返回键（额外保险）| BackHandler handles hardware back (extra safety)
    useEffect(() => {
      const backAction = () => {
        if (hasChanges) {
          showExitConfirmation(() => navigation.goBack());
          return true;
        }
        return false; // 让navigation处理 | Let navigation handle
      };

      const backHandler = BackHandler.addEventListener('hardwareBackPress', backAction);
      return () => backHandler.remove();
    }, [hasChanges, navigation]);

    const showExitConfirmation = (onConfirm) => {
      Alert.alert(
        '放弃更改？| Discard Changes?',
        '您有未保存的更改 | You have unsaved changes',
        [
          { text: '取消 | Cancel', style: 'cancel' },
          { text: '放弃 | Discard', style: 'destructive', onPress: onConfirm },
        ]
      );
    };

    return (
      <View style={styles.container}>
        <TextInput
          placeholder="输入重要内容 | Enter important content"
          onChangeText={(text) => setHasChanges(text.length > 0)}
        />
      </View>
    );
  }

  // 自定义Hook：useBackHandler | Custom hook: useBackHandler
  function useBackHandler(handler) {
    useEffect(() => {
      const backHandler = BackHandler.addEventListener('hardwareBackPress', handler);
      return () => backHandler.remove();
    }, [handler]);
  }

  // 使用自定义Hook | Using custom hook
  function AnyScreen() {
    useBackHandler(() => {
      Alert.alert('返回被拦截 | Back intercepted');
      return true;
    });

    return <View><Text>屏幕内容 | Screen content</Text></View>;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - BackHandler的回调函数中返回false和不返回有区别吗？| Is there a difference between returning false and not returning in BackHandler callback?
    **答案 | Answer:** 没有 | No - 都表示未处理，继续默认行为 | Both indicate not handled, continue default behavior
  - 如果在HomeScreen按返回键3秒后再按，会发生什么？| If you press back on HomeScreen and press again after 3 seconds, what happens?
    **答案 | Answer:** 再次显示"再按一次退出"提示（超过2秒重置）| Shows "press again to exit" hint again (resets after 2 seconds)

  **常见误区检查 | Common Misconception Checks:**
  - 误区：BackHandler可以在iOS上显示确认对话框 | Misconception: BackHandler can show confirmation dialog on iOS
    **答案 | Answer:** 错误 | Incorrect - iOS没有硬件返回键，需要用beforeRemove | iOS has no hardware back button, use beforeRemove instead
  - 误区：返回true就会关闭应用 | Misconception: Returning true will close the app
    **答案 | Answer:** 错误 | Incorrect - 返回true只是阻止默认行为，需要显式调用BackHandler.exitApp()退出 | Returning true only prevents default, need explicit BackHandler.exitApp() to exit

### 6. 综合导航守卫模式 | Comprehensive Navigation Guard Patterns (30分钟 | 30 minutes)

- **生产应用中的导航守卫最佳实践 | Navigation Guard Best Practices in Production**

  **概念定义 | Concept Definition:**
  导航守卫是一套用于控制和保护应用导航流程的策略和技术，包括身份验证检查、权限验证、数据保护、流程完整性保证等。在生产应用中，需要综合运用headerBackVisible、gestureEnabled、beforeRemove、BackHandler和navigation.reset()来实现完善的导航控制。 | Navigation guards are a set of strategies and techniques for controlling and protecting app navigation flows, including authentication checks, permission verification, data protection, and flow integrity assurance. In production apps, comprehensive navigation control requires combining headerBackVisible, gestureEnabled, beforeRemove, BackHandler, and navigation.reset().

  **核心特征 | Key Characteristics:**
  - 分层防护：UI层（隐藏按钮）+ 事件层（拦截操作）| Layered protection: UI layer (hide buttons) + Event layer (intercept operations)
  - 条件性守卫：根据状态决定是否拦截 | Conditional guards: Decide whether to intercept based on state
  - 用户友好：提供清晰的提示和替代路径 | User-friendly: Provide clear prompts and alternative paths
  - 代码复用：使用自定义Hook封装通用逻辑 | Code reuse: Use custom hooks to encapsulate common logic
  - 平台兼容：考虑iOS手势和Android硬件键 | Platform compatible: Consider iOS gestures and Android hardware buttons

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 只隐藏返回按钮（headerBackVisible: false）足够安全吗？| Is only hiding back button (headerBackVisible: false) safe enough?
     **答案 | Answer:** 否 | No - 还需要禁用手势和拦截硬件键 | Also need to disable gestures and intercept hardware buttons
  2. 所有需要保护的屏幕都应该使用相同的守卫策略吗？| Should all protected screens use the same guard strategy?
     **答案 | Answer:** 否 | No - 根据业务场景选择合适的策略 | Choose appropriate strategy based on business scenario
  3. 导航守卫应该在哪一层实现？| At which layer should navigation guards be implemented?
     **答案 | Answer:** 屏幕组件内，使用useEffect和监听器 | Within screen components, using useEffect and listeners
  4. 如何测试导航守卫是否有效？| How to test if navigation guards are effective?
     **答案 | Answer:** 测试所有返回方式：按钮、手势、硬件键、编程式导航 | Test all back methods: button, gesture, hardware button, programmatic navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 最佳实践：完整的导航守卫系统 | Best practice: Complete navigation guard system

  // 1. 可复用的守卫Hook | Reusable guard hooks
  // useNavigationGuard.js
  import { useEffect } from 'react';
  import { Alert, BackHandler, Platform } from 'react-native';

  /**
   * 通用导航守卫Hook | Universal navigation guard hook
   * @param {boolean} shouldBlock - 是否应该阻止返回 | Whether to block navigation
   * @param {string} title - 确认对话框标题 | Confirmation dialog title
   * @param {string} message - 确认对话框消息 | Confirmation dialog message
   * @param {function} onConfirm - 确认后的回调 | Callback after confirmation
   */
  export function useNavigationGuard(navigation, shouldBlock, title, message, onConfirm) {
    // beforeRemove监听器（适用于所有平台）| beforeRemove listener (works on all platforms)
    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        if (!shouldBlock) {
          return;
        }

        e.preventDefault();

        Alert.alert(title, message, [
          { text: '取消 | Cancel', style: 'cancel' },
          {
            text: '确认 | Confirm',
            style: 'destructive',
            onPress: () => {
              if (onConfirm) {
                onConfirm();
              }
              navigation.dispatch(e.data.action);
            },
          },
        ]);
      });

      return unsubscribe;
    }, [navigation, shouldBlock, title, message, onConfirm]);

    // Android硬件返回键（额外保护）| Android hardware back (extra protection)
    useEffect(() => {
      if (Platform.OS !== 'android') {
        return;
      }

      const backHandler = BackHandler.addEventListener('hardwareBackPress', () => {
        if (shouldBlock) {
          Alert.alert(title, message, [
            { text: '取消 | Cancel', style: 'cancel' },
            {
              text: '确认 | Confirm',
              style: 'destructive',
              onPress: () => {
                if (onConfirm) {
                  onConfirm();
                }
                navigation.goBack();
              },
            },
          ]);
          return true;
        }
        return false;
      });

      return () => backHandler.remove();
    }, [shouldBlock, title, message, navigation, onConfirm]);
  }

  // 2. 使用示例：表单页面 | Usage example: Form page
  function EditPostScreen({ navigation, route }) {
    const { postId } = route.params;
    const [title, setTitle] = useState('');
    const [content, setContent] = useState('');
    const [hasChanges, setHasChanges] = useState(false);

    // 使用导航守卫 | Use navigation guard
    useNavigationGuard(
      navigation,
      hasChanges,
      '放弃更改？| Discard changes?',
      '文章有未保存的更改 | Post has unsaved changes',
      () => {
        console.log('用户选择放弃更改 | User chose to discard changes');
        // 可以在这里清理状态 | Can cleanup state here
      }
    );

    // 同时配置屏幕选项禁用手势（在navigator中配置）| Also configure screen options to disable gestures (in navigator)
    useEffect(() => {
      navigation.setOptions({
        gestureEnabled: !hasChanges, // 有更改时禁用手势 | Disable gesture when has changes
      });
    }, [navigation, hasChanges]);

    const handleSave = async () => {
      try {
        // await savePost(postId, { title, content });
        setHasChanges(false);
        Alert.alert('保存成功 | Saved', '', [
          { text: '确定 | OK', onPress: () => navigation.goBack() }
        ]);
      } catch (error) {
        Alert.alert('保存失败 | Save Failed');
      }
    };

    return (
      <View style={styles.container}>
        <TextInput
          placeholder="标题 | Title"
          value={title}
          onChangeText={(text) => {
            setTitle(text);
            setHasChanges(true);
          }}
          style={styles.input}
        />
        <TextInput
          placeholder="内容 | Content"
          value={content}
          onChangeText={(text) => {
            setContent(text);
            setHasChanges(true);
          }}
          multiline
          style={[styles.input, styles.contentInput]}
        />
        <TouchableOpacity
          style={[styles.button, !hasChanges && styles.buttonDisabled]}
          onPress={handleSave}
          disabled={!hasChanges}
        >
          <Text style={styles.buttonText}>保存 | Save</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 3. 导航器配置：完整的结账流程 | Navigator configuration: Complete checkout flow
  function CheckoutNavigator() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="Cart"
          component={CartScreen}
          options={{ title: '购物车 | Cart' }}
        />
        <Stack.Screen
          name="Checkout"
          component={CheckoutScreen}
          options={{
            title: '结账 | Checkout',
            // 步骤1-2允许返回 | Steps 1-2 allow back
            headerBackVisible: true,
            gestureEnabled: true,
          }}
        />
        <Stack.Screen
          name="PaymentSuccess"
          component={PaymentSuccessScreen}
          options={{
            title: '支付成功 | Success',
            // 支付成功后禁止返回 | Prevent back after payment success
            headerBackVisible: false,
            gestureEnabled: false,
            headerLeft: () => null,
          }}
        />
      </Stack.Navigator>
    );
  }

  // 4. 导航守卫配置对照表 | Navigation guard configuration reference table
  const NAVIGATION_GUARD_PATTERNS = {
    // 模式1：完全开放（默认）| Pattern 1: Fully open (default)
    default: {
      headerBackVisible: true,
      gestureEnabled: true,
      beforeRemove: false,
    },

    // 模式2：表单保护 | Pattern 2: Form protection
    formProtection: {
      headerBackVisible: true,
      gestureEnabled: true, // 动态控制 | Dynamically controlled
      beforeRemove: true, // 条件性拦截 | Conditional interception
      condition: 'hasUnsavedChanges',
    },

    // 模式3：不可逆流程 | Pattern 3: Irreversible flow
    irreversible: {
      headerBackVisible: false,
      gestureEnabled: false,
      headerLeft: null,
      beforeRemove: true, // 完全阻止 | Completely prevent
      useReset: true, // 提供reset方式退出 | Provide reset method to exit
    },

    // 模式4：身份验证 | Pattern 4: Authentication
    authRequired: {
      beforeEnter: 'checkAuth', // 进入前检查 | Check before entering
      redirectTo: 'Login',
    },
  };

  // 5. 导航守卫决策树 | Navigation guard decision tree
  const NavigationGuardDecisionTree = () => {
    /*
    是否需要导航守卫？| Need navigation guard?
    ├─ 是 | Yes
    │  ├─ 保护类型？| Protection type?
    │  │  ├─ 数据保护（表单）| Data protection (form)
    │  │  │  └─ 使用：beforeRemove + 条件拦截 | Use: beforeRemove + conditional interception
    │  │  ├─ 流程保护（结账）| Flow protection (checkout)
    │  │  │  └─ 使用：headerBackVisible: false + gestureEnabled: false + beforeRemove | Use: headerBackVisible: false + gestureEnabled: false + beforeRemove
    │  │  └─ 权限保护（认证）| Permission protection (auth)
    │  │     └─ 使用：条件渲染 + navigation.reset() | Use: conditional rendering + navigation.reset()
    └─ 否 | No
       └─ 使用默认配置 | Use default configuration
    */
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - useNavigationGuard Hook同时处理beforeRemove和BackHandler的优势是什么？| What's the advantage of useNavigationGuard Hook handling both beforeRemove and BackHandler?
    **答案 | Answer:** 提供全面保护，一次配置适用所有返回方式 | Provides comprehensive protection, one configuration for all back methods
  - 为什么在表单页面动态设置gestureEnabled而不是静态设置？| Why dynamically set gestureEnabled on form page instead of statically?
    **答案 | Answer:** 没有更改时允许手势返回，有更改时禁用，用户体验更好 | Allow gesture back when no changes, disable when has changes, better UX

  **常见误区检查 | Common Misconception Checks:**
  - 误区：所有重要页面都应该禁止返回 | Misconception: All important pages should prevent going back
    **答案 | Answer:** 错误 | Incorrect - 只在特定场景（有未保存数据、不可逆操作）时使用 | Only use in specific scenarios (unsaved data, irreversible operations)
  - 误区：导航守卫会影响应用性能 | Misconception: Navigation guards affect app performance
    **答案 | Answer:** 基本不会 | Basically no - 监听器很轻量，正确清理不会有性能问题 | Listeners are lightweight, proper cleanup causes no performance issues

## 实践项目：多场景导航守卫应用 | Practical Project: Multi-scenario Navigation Guard Application

### 目标 | Objective
构建一个包含多种导航守卫场景的完整应用，综合应用本日所学的headerBackVisible、gestureEnabled、beforeRemove、BackHandler和navigation.reset()技术，实现表单保护、结账流程和应用退出确认。 | Build a complete application containing multiple navigation guard scenarios, comprehensively applying today's learned technologies including headerBackVisible, gestureEnabled, beforeRemove, BackHandler, and navigation.reset() to implement form protection, checkout flow, and app exit confirmation.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. headerBackVisible: false能完全阻止用户返回吗？| Can headerBackVisible: false completely prevent users from going back?
   **答案 | Answer:** 否，还需要禁用手势和拦截硬件键 | No, also need to disable gestures and intercept hardware buttons
2. beforeRemove监听器什么时候触发？| When does beforeRemove listener trigger?
   **答案 | Answer:** 屏幕即将从导航堆栈移除之前 | Before screen is about to be removed from navigation stack
3. navigation.reset()的主要用途是什么？| What's the main purpose of navigation.reset()?
   **答案 | Answer:** 清除导航历史，重置到新的屏幕堆栈 | Clear navigation history, reset to new screen stack

### 步骤 | Steps
1. **项目结构搭建** | **Project Structure Setup**
   - 创建Stack Navigator包含以下屏幕：Home、NewPost、EditProfile、Cart、Checkout、PaymentSuccess
   - 配置基础导航

2. **实现表单保护（NewPost & EditProfile）** | **Implement Form Protection (NewPost & EditProfile)**
   - 创建NewPost屏幕（标题+内容输入框）
   - 实现hasUnsavedChanges状态跟踪
   - 添加beforeRemove监听器显示确认对话框
   - 创建EditProfile屏幕应用相同逻辑

3. **实现不可逆结账流程（Cart → Checkout → PaymentSuccess）** | **Implement Irreversible Checkout Flow (Cart → Checkout → PaymentSuccess)**
   - 创建3步结账流程（配送、确认、成功）
   - 步骤1-2允许返回，步骤3禁止返回
   - 支付成功后使用navigation.reset()返回首页

4. **实现双击退出应用（Home屏幕）** | **Implement Double-press Exit (Home Screen)**
   - 在Home屏幕添加BackHandler监听器
   - 实现2秒内双击返回键退出应用逻辑

5. **创建可复用的useNavigationGuard Hook** | **Create Reusable useNavigationGuard Hook**
   - 封装beforeRemove和BackHandler逻辑
   - 在NewPost和EditProfile中使用

### 示例代码 | Example Code
```javascript
/**
 * 导航守卫综合应用 | Navigation Guard Comprehensive Application
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - headerBackVisible: 隐藏返回按钮 | Hide back button
 * - gestureEnabled: 禁用手势导航 | Disable gesture navigation
 * - beforeRemove: 拦截导航事件 | Intercept navigation events
 * - BackHandler: 处理Android硬件返回键 | Handle Android hardware back button
 * - navigation.reset(): 重置导航堆栈 | Reset navigation stack
 */

// App.js - 主导航配置 | Main navigation configuration
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from './screens/HomeScreen';
import NewPostScreen from './screens/NewPostScreen';
import EditProfileScreen from './screens/EditProfileScreen';
import CartScreen from './screens/CartScreen';
import CheckoutScreen from './screens/CheckoutScreen';
import PaymentSuccessScreen from './screens/PaymentSuccessScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: '首页 | Home' }}
        />
        <Stack.Screen
          name="NewPost"
          component={NewPostScreen}
          options={{ title: '新建文章 | New Post' }}
        />
        <Stack.Screen
          name="EditProfile"
          component={EditProfileScreen}
          options={{ title: '编辑资料 | Edit Profile' }}
        />
        <Stack.Screen
          name="Cart"
          component={CartScreen}
          options={{ title: '购物车 | Cart' }}
        />
        <Stack.Screen
          name="Checkout"
          component={CheckoutScreen}
          options={{ title: '结账 | Checkout' }}
        />
        <Stack.Screen
          name="PaymentSuccess"
          component={PaymentSuccessScreen}
          options={{
            title: '支付成功 | Success',
            headerBackVisible: false,
            gestureEnabled: false,
            headerLeft: () => null,
          }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// screens/HomeScreen.js - 主页（双击退出）| Home screen (double-press exit)
import React, { useState, useEffect } from 'react';
import { View, Text, TouchableOpacity, Alert, BackHandler, StyleSheet } from 'react-native';

export default function HomeScreen({ navigation }) {
  const [lastBackPressed, setLastBackPressed] = useState(0);

  // Android双击返回退出 | Android double-press back to exit
  useEffect(() => {
    const backAction = () => {
      const now = Date.now();

      if (now - lastBackPressed < 2000) {
        BackHandler.exitApp();
        return true;
      }

      setLastBackPressed(now);
      Alert.alert('', '再按一次退出应用 | Press again to exit');
      return true;
    };

    const backHandler = BackHandler.addEventListener('hardwareBackPress', backAction);
    return () => backHandler.remove();
  }, [lastBackPressed]);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>导航守卫示例 | Navigation Guard Examples</Text>

      <TouchableOpacity
        style={styles.button}
        onPress={() => navigation.navigate('NewPost')}
      >
        <Text style={styles.buttonText}>新建文章（表单保护）| New Post (Form Protection)</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() => navigation.navigate('EditProfile')}
      >
        <Text style={styles.buttonText}>编辑资料（表单保护）| Edit Profile (Form Protection)</Text>
      </TouchableOpacity>

      <TouchableOpacity
        style={styles.button}
        onPress={() => navigation.navigate('Cart')}
      >
        <Text style={styles.buttonText}>购物车（结账流程）| Cart (Checkout Flow)</Text>
      </TouchableOpacity>

      <Text style={styles.hint}>按两次返回键退出应用 | Press back twice to exit</Text>
    </View>
  );
}

// hooks/useNavigationGuard.js - 可复用的导航守卫Hook | Reusable navigation guard hook
import { useEffect } from 'react';
import { Alert, BackHandler, Platform } from 'react-native';

export function useNavigationGuard(navigation, shouldBlock, title, message) {
  useEffect(() => {
    const unsubscribe = navigation.addListener('beforeRemove', (e) => {
      if (!shouldBlock) return;

      e.preventDefault();

      Alert.alert(title, message, [
        { text: '取消 | Cancel', style: 'cancel' },
        {
          text: '放弃 | Discard',
          style: 'destructive',
          onPress: () => navigation.dispatch(e.data.action),
        },
      ]);
    });

    return unsubscribe;
  }, [navigation, shouldBlock, title, message]);

  useEffect(() => {
    if (Platform.OS !== 'android') return;

    const backHandler = BackHandler.addEventListener('hardwareBackPress', () => {
      if (shouldBlock) {
        Alert.alert(title, message, [
          { text: '取消 | Cancel', style: 'cancel' },
          {
            text: '放弃 | Discard',
            style: 'destructive',
            onPress: () => navigation.goBack(),
          },
        ]);
        return true;
      }
      return false;
    });

    return () => backHandler.remove();
  }, [shouldBlock, title, message, navigation]);
}

// screens/NewPostScreen.js - 新建文章（使用Hook）| New post screen (using hook)
import React, { useState, useEffect } from 'react';
import { View, TextInput, TouchableOpacity, Alert, StyleSheet } from 'react-native';
import { useNavigationGuard } from '../hooks/useNavigationGuard';

export default function NewPostScreen({ navigation }) {
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  const [hasChanges, setHasChanges] = useState(false);

  // 使用导航守卫Hook | Use navigation guard hook
  useNavigationGuard(
    navigation,
    hasChanges,
    '放弃更改？| Discard changes?',
    '文章有未保存的更改 | Post has unsaved changes'
  );

  // 动态控制手势 | Dynamically control gesture
  useEffect(() => {
    navigation.setOptions({ gestureEnabled: !hasChanges });
  }, [navigation, hasChanges]);

  const handleSave = () => {
    setHasChanges(false);
    Alert.alert('保存成功 | Saved', '', [
      { text: '确定 | OK', onPress: () => navigation.goBack() }
    ]);
  };

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        placeholder="标题 | Title"
        value={title}
        onChangeText={(text) => {
          setTitle(text);
          setHasChanges(true);
        }}
      />
      <TextInput
        style={[styles.input, styles.contentInput]}
        placeholder="内容 | Content"
        value={content}
        onChangeText={(text) => {
          setContent(text);
          setHasChanges(true);
        }}
        multiline
      />
      <TouchableOpacity
        style={[styles.button, !hasChanges && styles.buttonDisabled]}
        onPress={handleSave}
        disabled={!hasChanges}
      >
        <Text style={styles.buttonText}>保存 | Save</Text>
      </TouchableOpacity>
    </View>
  );
}

// screens/CheckoutScreen.js - 结账流程 | Checkout flow
import React, { useState, useEffect } from 'react';
import { View, Text, TouchableOpacity, Alert, StyleSheet } from 'react-native';

export default function CheckoutScreen({ navigation }) {
  const [step, setStep] = useState(1);

  // 步骤3禁止返回 | Step 3 prevents going back
  useEffect(() => {
    const unsubscribe = navigation.addListener('beforeRemove', (e) => {
      if (step < 3) return;

      e.preventDefault();
      Alert.alert('无法返回 | Cannot Go Back', '请点击"完成"按钮 | Please click "Done"');
    });

    return unsubscribe;
  }, [navigation, step]);

  const handlePayment = () => {
    // 模拟支付 | Simulate payment
    setTimeout(() => {
      setStep(3);
    }, 1000);
  };

  const handleComplete = () => {
    // 重置到首页 | Reset to home
    navigation.reset({
      index: 0,
      routes: [{ name: 'Home' }],
    });
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>步骤 {step}/3 | Step {step}/3</Text>

      {step === 1 && (
        <TouchableOpacity style={styles.button} onPress={() => setStep(2)}>
          <Text style={styles.buttonText}>下一步 | Next</Text>
        </TouchableOpacity>
      )}

      {step === 2 && (
        <TouchableOpacity style={styles.button} onPress={handlePayment}>
          <Text style={styles.buttonText}>确认支付 | Pay</Text>
        </TouchableOpacity>
      )}

      {step === 3 && (
        <View>
          <Text style={styles.success}>✓ 支付成功！| Payment Successful!</Text>
          <TouchableOpacity style={styles.button} onPress={handleComplete}>
            <Text style={styles.buttonText}>完成 | Done</Text>
          </TouchableOpacity>
        </View>
      )}
    </View>
  );
}

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
  button: {
    backgroundColor: '#007AFF',
    padding: 15,
    borderRadius: 8,
    marginVertical: 10,
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    textAlign: 'center',
    fontWeight: '600',
  },
  buttonDisabled: {
    backgroundColor: '#ccc',
  },
  input: {
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 8,
    padding: 12,
    marginBottom: 15,
    fontSize: 16,
  },
  contentInput: {
    height: 150,
    textAlignVertical: 'top',
  },
  success: {
    fontSize: 32,
    textAlign: 'center',
    color: '#4CD964',
    marginBottom: 30,
  },
  hint: {
    textAlign: 'center',
    color: '#666',
    marginTop: 20,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确实现了表单保护（NewPost和EditProfile）？| Does the project correctly implement form protection (NewPost and EditProfile)?
2. 结账流程的步骤3是否完全禁止了返回？| Does checkout flow step 3 completely prevent going back?
3. Home屏幕是否实现了双击返回退出应用？| Does Home screen implement double-press back to exit app?
4. useNavigationGuard Hook是否被正确复用？| Is useNavigationGuard Hook properly reused?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **禁用手势导航练习 | Disable Gesture Navigation Exercise**
   - **练习描述 | Exercise Description:** 创建一个视频播放页面，播放时禁用返回手势，暂停时允许返回
   - **概念检查 | Concept Check:** gestureEnabled如何动态控制？| How to dynamically control gestureEnabled?
   - **学习目标 | Learning Objective:** 理解gestureEnabled的动态配置 | Understand dynamic configuration of gestureEnabled

2. **复杂表单保护练习 | Complex Form Protection Exercise**
   - **练习描述 | Exercise Description:** 创建多步注册表单，只有在当前步骤有未保存数据时才拦截返回
   - **概念检查 | Concept Check:** 如何根据多个状态条件决定是否拦截？| How to decide whether to intercept based on multiple state conditions?
   - **学习目标 | Learning Objective:** 掌握条件性导航守卫 | Master conditional navigation guards

3. **导航重置练习 | Navigation Reset Exercise**
   - **练习描述 | Exercise Description:** 实现密码修改流程，成功后重置到设置页面并清除之前的所有步骤
   - **概念检查 | Concept Check:** navigation.reset()的routes数组如何配置？| How to configure navigation.reset() routes array?
   - **学习目标 | Learning Objective:** 熟练使用navigation.reset() | Proficiently use navigation.reset()

4. **平台差异处理练习 | Platform Difference Handling Exercise**
   - **练习描述 | Exercise Description:** 创建一个页面，iOS使用beforeRemove，Android额外使用BackHandler
   - **概念检查 | Concept Check:** 为什么Android需要额外的BackHandler？| Why does Android need additional BackHandler?
   - **学习目标 | Learning Objective:** 理解平台差异和兼容性处理 | Understand platform differences and compatibility handling

5. **自定义确认对话框练习 | Custom Confirmation Dialog Exercise**
   - **练习描述 | Exercise Description:** 用自定义Modal替代Alert.alert实现更友好的退出确认
   - **概念检查 | Concept Check:** 如何在beforeRemove中显示自定义UI组件？| How to show custom UI components in beforeRemove?
   - **学习目标 | Learning Objective:** 提升用户体验设计能力 | Enhance UX design capability

6. **导航守卫测试练习 | Navigation Guard Testing Exercise**
   - **练习描述 | Exercise Description:** 为导航守卫功能编写测试用例，覆盖所有返回场景
   - **概念检查 | Concept Check:** 如何测试beforeRemove监听器？| How to test beforeRemove listener?
   - **学习目标 | Learning Objective:** 掌握导航守卫的测试方法 | Master testing methods for navigation guards

7. **生产应用集成练习 | Production App Integration Exercise**
   - **练习描述 | Exercise Description:** 在Week 4的新闻应用中添加文章草稿保护功能
   - **概念检查 | Concept Check:** 如何在现有项目中集成导航守卫？| How to integrate navigation guards in existing projects?
   - **学习目标 | Learning Objective:** 应用到实际项目开发 | Apply to real project development

## 学习资源 | Learning Resources
- [React Navigation - Preventing Going Back](https://reactnavigation.org/docs/preventing-going-back)
- [React Navigation - Navigation Lifecycle](https://reactnavigation.org/docs/navigation-lifecycle)
- [React Native - BackHandler](https://reactnative.dev/docs/backhandler)
- [React Navigation - Reset Navigation State](https://reactnavigation.org/docs/navigation-actions#reset)
- [Common Navigation Patterns](https://reactnavigation.org/docs/common-mistakes)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解headerBackVisible和gestureEnabled的作用 | Understand headerBackVisible and gestureEnabled
- [ ] 掌握beforeRemove监听器的使用 | Master beforeRemove listener usage
- [ ] 能够实现表单未保存数据的保护 | Can implement unsaved form data protection
- [ ] 理解不可逆流程的设计模式 | Understand irreversible flow design pattern
- [ ] 熟练使用navigation.reset()重置导航堆栈 | Proficiently use navigation.reset() to reset stack
- [ ] 掌握Android BackHandler的使用 | Master Android BackHandler usage
- [ ] 能够创建可复用的导航守卫Hook | Can create reusable navigation guard hooks
- [ ] 完成实践项目并测试所有导航守卫场景 | Complete practical project and test all guard scenarios
- [ ] 理解生产应用中的导航守卫最佳实践 | Understand navigation guard best practices in production
- [ ] 至少完成3个扩展练习 | Complete at least 3 extension exercises

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释每个核心概念。特别是要能够说明：
1. headerBackVisible、gestureEnabled和beforeRemove的区别和联系
2. 什么场景需要使用导航守卫
3. 如何综合使用多种技术实现完整的导航保护

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain each core concept to others. Particularly, you should be able to explain:
1. Differences and relationships between headerBackVisible, gestureEnabled, and beforeRemove
2. What scenarios require navigation guards
3. How to comprehensively use multiple techniques to implement complete navigation protection
