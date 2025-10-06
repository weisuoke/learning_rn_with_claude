# React Native入门 - 第3天：防止返回导航与导航守卫 | React Native Introduction - Day 3: Preventing Back Navigation & Navigation Guards

## 学习目标 | Learning Objectives
- 理解如何禁用特定屏幕的返回按钮 | Understand how to disable back button on specific screens
- 掌握使用beforeRemove监听器拦截导航事件 | Master using beforeRemove listener to intercept navigation events
- 实现表单未保存数据的导航确认对话框 | Implement confirmation dialogs for unsaved form data
- 创建不可返回的结账流程 | Create non-reversible checkout flows
- 使用navigation.reset()重置导航堆栈 | Use navigation.reset() to reset navigation stack
- 理解导航守卫在生产应用中的实际应用场景 | Understand real-world use cases of navigation guards in production apps

## 详细内容 | Detailed Content

### 1. 禁用返回按钮基础 | Back Button Disabling Basics (1小时 | 1 hour)

- **隐藏返回按钮的核心概念 | Core Concept of Hiding Back Button**

  **概念定义 | Concept Definition:**
  在React Navigation中，`headerBackVisible`是一个屏幕选项，用于控制导航标题栏中返回按钮的可见性。当设置为`false`时，返回按钮将被隐藏，但用户仍然可以通过手势（如iOS的边缘滑动）或Android的物理返回键返回。 | In React Navigation, `headerBackVisible` is a screen option that controls the visibility of the back button in the navigation header. When set to `false`, the back button is hidden, but users can still navigate back using gestures (like iOS edge swipe) or Android's hardware back button.

  **核心特征 | Key Characteristics:**
  - `headerBackVisible: false`只隐藏UI上的返回按钮，不阻止导航动作 | `headerBackVisible: false` only hides the UI back button, doesn't prevent navigation actions
  - 需要在`options`对象中设置 | Must be set in the `options` object
  - 通常用于登录后的主屏幕或完成流程后的屏幕 | Commonly used for main screens after login or screens after completing a flow
  - 可以静态设置或动态设置 | Can be set statically or dynamically

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 设置`headerBackVisible: false`后，用户还能通过其他方式返回吗？| Can users still navigate back through other methods after setting `headerBackVisible: false`?
     **答案 | Answer:** 是 | Yes - 用户仍可通过手势或硬件按键返回 | Users can still go back via gestures or hardware buttons
  2. `headerBackVisible`是屏幕选项还是导航器选项？| Is `headerBackVisible` a screen option or navigator option?
     **答案 | Answer:** 屏幕选项 | Screen option - 它在每个屏幕的options中设置 | It's set in each screen's options
  3. 隐藏返回按钮和阻止返回导航是同一个概念吗？| Are hiding the back button and preventing back navigation the same concept?
     **答案 | Answer:** 否 | No - 隐藏按钮只是UI变化，阻止导航需要额外的逻辑 | Hiding the button is just a UI change; preventing navigation requires additional logic
  4. 在Tab导航器的屏幕上需要隐藏返回按钮吗？| Do you need to hide the back button on Tab navigator screens?
     **答案 | Answer:** 否 | No - Tab屏幕默认没有返回按钮 | Tab screens don't have back buttons by default

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 方法1：静态设置屏幕选项 | Method 1: Static screen options
  <Stack.Screen
    name="Home"
    component={HomeScreen}
    options={{
      headerBackVisible: false, // 隐藏返回按钮 | Hide back button
      title: '主页 | Home'
    }}
  />

  // 方法2：在屏幕组件内动态设置 | Method 2: Dynamic setting within screen component
  function HomeScreen({ navigation }) {
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerBackVisible: false // 动态隐藏返回按钮 | Dynamically hide back button
      });
    }, [navigation]);

    return (
      <View style={styles.container}>
        <Text>欢迎来到主页 | Welcome to Home</Text>
      </View>
    );
  }

  // 方法3：在导航器的screenOptions中全局设置 | Method 3: Global setting in navigator's screenOptions
  <Stack.Navigator
    screenOptions={{
      headerBackVisible: false // 所有屏幕都隐藏返回按钮 | Hide back button for all screens
    }}
  >
    <Stack.Screen name="Home" component={HomeScreen} />
  </Stack.Navigator>

  // 实际应用场景：登录后的主屏幕 | Real-world scenario: Main screen after login
  function AuthStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="Signup" component={SignupScreen} />
      </Stack.Navigator>
    );
  }

  function AppStack() {
    return (
      <Stack.Navigator>
        <Stack.Screen
          name="MainTabs"
          component={TabNavigator}
          options={{
            headerShown: false,
            headerBackVisible: false // 登录后无法返回登录页 | Can't go back to login after signing in
          }}
        />
      </Stack.Navigator>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果在Stack.Navigator的screenOptions中设置了headerBackVisible: false，但在某个Screen中设置了headerBackVisible: true，会显示返回按钮吗？| If headerBackVisible: false is set in Stack.Navigator's screenOptions, but headerBackVisible: true is set in a specific Screen, will the back button show?
    **答案 | Answer:** 会显示 | Yes - 屏幕级别的options会覆盖导航器级别的screenOptions | Screen-level options override navigator-level screenOptions
  - 在iOS上，隐藏返回按钮后，边缘滑动手势还能用吗？| On iOS, after hiding the back button, does the edge swipe gesture still work?
    **答案 | Answer:** 默认可以用 | Yes by default - 需要额外配置才能禁用手势 | Additional configuration is needed to disable gestures

  **常见误区检查 | Common Misconception Checks:**
  - 误区：设置headerBackVisible: false就能完全阻止用户返回 | Misconception: Setting headerBackVisible: false completely prevents users from going back
    **答案 | Answer:** 错误 | Incorrect - 这只是隐藏UI按钮，用户仍可通过其他方式返回 | This only hides the UI button; users can still go back through other means
  - 误区：headerBackVisible只在Stack Navigator中有效 | Misconception: headerBackVisible only works in Stack Navigator
    **答案 | Answer:** 正确 | Correct - 这是Stack Navigator特有的选项 | This is a Stack Navigator-specific option

- **gestureEnabled配置 | gestureEnabled Configuration**

  **概念定义 | Concept Definition:**
  `gestureEnabled`是控制屏幕是否可以通过滑动手势关闭的选项。在iOS上，默认启用边缘滑动手势；在Android上，默认行为因版本而异。设置为`false`可以禁用手势导航。 | `gestureEnabled` is an option that controls whether a screen can be dismissed using swipe gestures. On iOS, edge swipe gestures are enabled by default; on Android, default behavior varies by version. Setting it to `false` disables gesture navigation.

  **核心特征 | Key Characteristics:**
  - iOS默认启用边缘滑动手势 | iOS enables edge swipe gesture by default
  - 可以与headerBackVisible配合使用 | Can be used together with headerBackVisible
  - 设置为false可以强制用户使用特定的导航路径 | Setting to false forces users to use specific navigation paths

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. gestureEnabled: false能阻止Android的硬件返回键吗？| Does gestureEnabled: false prevent Android's hardware back button?
     **答案 | Answer:** 否 | No - 需要使用beforeRemove监听器来拦截硬件返回键 | Need to use beforeRemove listener to intercept hardware back button
  2. 为什么在某些流程中需要禁用手势导航？| Why do we need to disable gesture navigation in certain flows?
     **答案 | Answer:** 确保用户完成必要步骤，如支付流程或表单提交 | To ensure users complete necessary steps, like payment flows or form submissions

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 完全禁用返回（按钮+手势）| Completely disable going back (button + gesture)
  <Stack.Screen
    name="PaymentSuccess"
    component={PaymentSuccessScreen}
    options={{
      headerBackVisible: false,    // 隐藏返回按钮 | Hide back button
      gestureEnabled: false,       // 禁用滑动手势 | Disable swipe gesture
      headerLeft: () => null,      // 确保左侧没有任何按钮 | Ensure no buttons on the left
      title: '支付成功 | Payment Success'
    }}
  />

  // 在支付成功页提供"返回首页"按钮 | Provide "Go Home" button on payment success page
  function PaymentSuccessScreen({ navigation }) {
    const goHome = () => {
      // 重置导航堆栈，移除所有之前的屏幕 | Reset navigation stack, remove all previous screens
      navigation.reset({
        index: 0,
        routes: [{ name: 'Home' }],
      });
    };

    return (
      <View style={styles.container}>
        <Text style={styles.successText}>✓ 支付成功！| Payment Successful!</Text>
        <Text style={styles.message}>您的订单已确认 | Your order is confirmed</Text>
        <TouchableOpacity style={styles.button} onPress={goHome}>
          <Text style={styles.buttonText}>返回首页 | Go Home</Text>
        </TouchableOpacity>
      </View>
    );
  }
  ```

### 2. beforeRemove监听器核心机制 | beforeRemove Listener Core Mechanism (1.5小时 | 1.5 hours)

- **beforeRemove事件拦截原理 | beforeRemove Event Interception Principle**

  **概念定义 | Concept Definition:**
  `beforeRemove`是React Navigation提供的导航生命周期事件监听器，在屏幕即将从导航堆栈中移除之前触发。它可以拦截所有类型的返回导航（返回按钮、手势、硬件按键、编程式导航），并允许开发者阻止导航或显示确认对话框。 | `beforeRemove` is a navigation lifecycle event listener provided by React Navigation that triggers before a screen is about to be removed from the navigation stack. It can intercept all types of back navigation (back button, gestures, hardware buttons, programmatic navigation) and allows developers to prevent navigation or show confirmation dialogs.

  **核心特征 | Key Characteristics:**
  - 在导航发生前触发，可以阻止导航 | Triggers before navigation occurs, can prevent navigation
  - 接收一个事件对象，包含action（导航动作）和preventDefault方法 | Receives an event object containing action (navigation action) and preventDefault method
  - 需要在useEffect中添加和清理监听器 | Must add and cleanup listener in useEffect
  - 可以根据条件决定是否阻止导航 | Can conditionally decide whether to prevent navigation
  - 适用于所有导航方式（不仅是返回） | Applies to all navigation methods (not just back)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. beforeRemove监听器能拦截所有类型的返回操作吗？| Can beforeRemove listener intercept all types of back operations?
     **答案 | Answer:** 是 | Yes - 包括按钮点击、手势、硬件按键和编程式导航 | Including button clicks, gestures, hardware buttons, and programmatic navigation
  2. 如果调用了event.preventDefault()，导航会被阻止吗？| If event.preventDefault() is called, will navigation be prevented?
     **答案 | Answer:** 是 | Yes - preventDefault()阻止默认的导航行为 | preventDefault() prevents the default navigation behavior
  3. beforeRemove监听器需要在组件卸载时清理吗？| Does beforeRemove listener need to be cleaned up on component unmount?
     **答案 | Answer:** 是 | Yes - 使用useEffect返回清理函数 | Use useEffect return cleanup function
  4. 可以在beforeRemove中执行异步操作（如显示对话框）吗？| Can you perform async operations (like showing dialogs) in beforeRemove?
     **答案 | Answer:** 是 | Yes - 常见用法是显示Alert确认对话框 | Common usage is showing Alert confirmation dialog

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, TextInput, Alert, TouchableOpacity } from 'react-native';

  // 基础的beforeRemove拦截示例 | Basic beforeRemove interception example
  function FormScreen({ navigation }) {
    const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);
    const [text, setText] = useState('');

    // 添加beforeRemove监听器 | Add beforeRemove listener
    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        // 如果没有未保存的更改，允许正常导航 | If no unsaved changes, allow normal navigation
        if (!hasUnsavedChanges) {
          return;
        }

        // 阻止默认的返回行为 | Prevent default back behavior
        e.preventDefault();

        // 显示确认对话框 | Show confirmation dialog
        Alert.alert(
          '放弃更改？| Discard changes?',
          '您有未保存的更改，确定要离开吗？| You have unsaved changes. Are you sure you want to leave?',
          [
            {
              text: '取消 | Cancel',
              style: 'cancel',
              onPress: () => {}
            },
            {
              text: '放弃 | Discard',
              style: 'destructive',
              onPress: () => {
                // 用户确认后，手动执行导航 | After user confirmation, manually perform navigation
                navigation.dispatch(e.data.action);
              },
            },
          ]
        );
      });

      // 清理监听器 | Cleanup listener
      return unsubscribe;
    }, [navigation, hasUnsavedChanges]);

    return (
      <View style={styles.container}>
        <Text style={styles.label}>输入内容 | Enter content:</Text>
        <TextInput
          style={styles.input}
          value={text}
          onChangeText={(value) => {
            setText(value);
            setHasUnsavedChanges(true); // 标记有未保存更改 | Mark as having unsaved changes
          }}
          placeholder="开始输入... | Start typing..."
        />

        <TouchableOpacity
          style={styles.saveButton}
          onPress={() => {
            // 保存逻辑 | Save logic
            setHasUnsavedChanges(false);
            Alert.alert('已保存 | Saved', '内容已保存 | Content saved');
          }}
        >
          <Text style={styles.buttonText}>保存 | Save</Text>
        </TouchableOpacity>
      </View>
    );
  }

  // 高级示例：条件性拦截 | Advanced example: Conditional interception
  function CheckoutScreen({ navigation, route }) {
    const [currentStep, setCurrentStep] = useState(1); // 结账步骤：1, 2, 3
    const { orderId } = route.params;

    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        // 步骤1和2可以返回，步骤3（支付完成）不能返回 | Steps 1 and 2 can go back, step 3 (payment complete) cannot
        if (currentStep < 3) {
          return; // 允许返回 | Allow going back
        }

        // 步骤3：支付完成后禁止返回 | Step 3: Prevent going back after payment
        e.preventDefault();

        Alert.alert(
          '无法返回 | Cannot Go Back',
          '支付已完成，请点击"完成"按钮 | Payment completed, please click "Done" button',
          [{ text: '知道了 | OK' }]
        );
      });

      return unsubscribe;
    }, [navigation, currentStep]);

    const completeCheckout = () => {
      // 重置到首页，清除所有结账相关屏幕 | Reset to home, clear all checkout screens
      navigation.reset({
        index: 0,
        routes: [{ name: 'Home' }],
      });
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>结账流程 - 步骤 {currentStep}/3</Text>
        <Text style={styles.title}>Checkout - Step {currentStep}/3</Text>

        {currentStep === 1 && (
          <View>
            <Text>配送信息 | Shipping Information</Text>
            <TouchableOpacity onPress={() => setCurrentStep(2)}>
              <Text>下一步 | Next</Text>
            </TouchableOpacity>
          </View>
        )}

        {currentStep === 2 && (
          <View>
            <Text>支付方式 | Payment Method</Text>
            <TouchableOpacity onPress={() => setCurrentStep(3)}>
              <Text>确认支付 | Confirm Payment</Text>
            </TouchableOpacity>
          </View>
        )}

        {currentStep === 3 && (
          <View>
            <Text style={styles.success}>✓ 支付成功！| Payment Successful!</Text>
            <Text>订单号：{orderId} | Order ID: {orderId}</Text>
            <TouchableOpacity
              style={styles.button}
              onPress={completeCheckout}
            >
              <Text style={styles.buttonText}>完成 | Done</Text>
            </TouchableOpacity>
          </View>
        )}
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在上面的FormScreen示例中，如果用户输入了文本但没有点击保存，然后按返回键，会发生什么？| In the FormScreen example above, if a user enters text but doesn't click save, then presses the back button, what happens?
    **答案 | Answer:** 会显示确认对话框询问是否放弃更改 | A confirmation dialog will appear asking to discard changes
  - navigation.dispatch(e.data.action)的作用是什么？| What does navigation.dispatch(e.data.action) do?
    **答案 | Answer:** 执行被preventDefault阻止的原始导航动作 | Executes the original navigation action that was prevented by preventDefault
  - 为什么beforeRemove依赖数组中要包含hasUnsavedChanges？| Why include hasUnsavedChanges in beforeRemove dependency array?
    **答案 | Answer:** 确保监听器使用最新的状态值来判断是否拦截 | Ensures the listener uses the latest state value to decide whether to intercept

  **常见误区检查 | Common Misconception Checks:**
  - 误区：beforeRemove只能拦截返回按钮 | Misconception: beforeRemove only intercepts the back button
    **答案 | Answer:** 错误 | Incorrect - 它拦截所有导致屏幕移除的导航操作 | It intercepts all navigation operations that cause screen removal
  - 误区：调用preventDefault后必须手动导航 | Misconception: After calling preventDefault, you must manually navigate
    **答案 | Answer:** 不一定 | Not necessarily - 可以完全阻止导航，取决于业务逻辑 | You can completely prevent navigation, depending on business logic

- **event.data.action解析 | event.data.action Analysis**

  **概念定义 | Concept Definition:**
  `event.data.action`包含触发屏幕移除的原始导航动作对象。该对象包含`type`（动作类型，如'POP'、'NAVIGATE'）和其他相关参数。通过分析action，可以判断用户是如何尝试离开当前屏幕的。 | `event.data.action` contains the original navigation action object that triggered screen removal. This object includes `type` (action type, such as 'POP', 'NAVIGATE') and other relevant parameters. By analyzing the action, you can determine how the user is attempting to leave the current screen.

  **核心特征 | Key Characteristics:**
  - action.type表示导航动作类型 | action.type indicates the navigation action type
  - 'POP'表示返回操作 | 'POP' represents back operation
  - 'NAVIGATE'表示导航到其他屏幕 | 'NAVIGATE' represents navigating to other screens
  - 可以根据不同的action类型执行不同的逻辑 | Different logic can be executed based on different action types

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. action.type为'POP'时代表什么操作？| What operation does action.type 'POP' represent?
     **答案 | Answer:** 返回操作（返回按钮、手势、硬件按键） | Back operation (back button, gesture, hardware button)
  2. 可以根据action.type区别对待不同的导航吗？| Can you treat different navigations differently based on action.type?
     **答案 | Answer:** 是 | Yes - 例如允许前进但阻止返回 | For example, allow forward navigation but prevent back

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 根据导航类型区分处理 | Differentiate handling based on navigation type
  useEffect(() => {
    const unsubscribe = navigation.addListener('beforeRemove', (e) => {
      console.log('Navigation action:', e.data.action);
      // 输出示例 | Output example:
      // { type: 'POP', source: undefined, target: undefined }

      // 只拦截返回操作，允许前进到其他屏幕 | Only intercept back operations, allow forward navigation
      if (e.data.action.type === 'POP' && hasUnsavedChanges) {
        e.preventDefault();
        Alert.alert('确认返回？| Confirm go back?');
      }
    });

    return unsubscribe;
  }, [navigation, hasUnsavedChanges]);
  ```

### 3. 表单保护实战 | Form Protection Implementation (1小时 | 1 hour)

- **未保存数据检测 | Unsaved Data Detection**

  **概念定义 | Concept Definition:**
  表单保护是指在用户填写表单但未保存时，如果尝试离开页面，系统会提示用户确认是否放弃更改。这需要跟踪表单状态（是否有未保存的更改）并在导航前进行检查。 | Form protection refers to prompting users to confirm if they want to discard changes when attempting to leave a page with unsaved form data. This requires tracking form state (whether there are unsaved changes) and checking before navigation.

  **核心特征 | Key Characteristics:**
  - 使用state跟踪表单是否被修改 | Use state to track whether form is modified
  - 区分"初始状态"和"已修改未保存状态" | Distinguish between "initial state" and "modified but unsaved state"
  - 保存后重置未保存标记 | Reset unsaved flag after saving
  - 可以使用isDirty flag或比较当前值与初始值 | Can use isDirty flag or compare current values with initial values

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 什么时候应该将hasUnsavedChanges设置为true？| When should hasUnsavedChanges be set to true?
     **答案 | Answer:** 用户修改任何表单字段时 | When user modifies any form field
  2. 保存表单后需要做什么？| What needs to be done after saving the form?
     **答案 | Answer:** 将hasUnsavedChanges设置为false | Set hasUnsavedChanges to false
  3. 如果表单有多个字段，需要为每个字段设置标记吗？| If form has multiple fields, do you need to set a flag for each field?
     **答案 | Answer:** 否 | No - 通常使用一个统一的hasUnsavedChanges标记 | Usually use a single hasUnsavedChanges flag

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, TextInput, TouchableOpacity, Alert, ScrollView } from 'react-native';

  function EditProfileScreen({ navigation, route }) {
    // 初始数据（从route.params或API获取）| Initial data (from route.params or API)
    const initialData = {
      name: route.params?.name || '',
      email: route.params?.email || '',
      phone: route.params?.phone || '',
    };

    const [name, setName] = useState(initialData.name);
    const [email, setEmail] = useState(initialData.email);
    const [phone, setPhone] = useState(initialData.phone);
    const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);

    // 检查表单是否被修改 | Check if form is modified
    const checkIfModified = (field, value) => {
      // 方法1：使用标记位 | Method 1: Use flag
      setHasUnsavedChanges(true);

      // 方法2：比较当前值与初始值（更精确）| Method 2: Compare current vs initial (more precise)
      // const isModified =
      //   name !== initialData.name ||
      //   email !== initialData.email ||
      //   phone !== initialData.phone;
      // setHasUnsavedChanges(isModified);
    };

    // beforeRemove拦截 | beforeRemove interception
    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        if (!hasUnsavedChanges) {
          return;
        }

        e.preventDefault();

        Alert.alert(
          '放弃更改？| Discard changes?',
          '您的个人资料有未保存的更改 | Your profile has unsaved changes',
          [
            { text: '继续编辑 | Keep editing', style: 'cancel', onPress: () => {} },
            {
              text: '放弃 | Discard',
              style: 'destructive',
              onPress: () => navigation.dispatch(e.data.action),
            },
          ]
        );
      });

      return unsubscribe;
    }, [navigation, hasUnsavedChanges]);

    // 保存函数 | Save function
    const handleSave = async () => {
      try {
        // 模拟API调用 | Simulate API call
        await new Promise(resolve => setTimeout(resolve, 1000));

        // 保存成功后重置标记 | Reset flag after successful save
        setHasUnsavedChanges(false);

        Alert.alert('成功 | Success', '个人资料已更新 | Profile updated', [
          { text: '确定 | OK', onPress: () => navigation.goBack() }
        ]);
      } catch (error) {
        Alert.alert('错误 | Error', '保存失败 | Save failed');
      }
    };

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.title}>编辑个人资料 | Edit Profile</Text>

        <Text style={styles.label}>姓名 | Name</Text>
        <TextInput
          style={styles.input}
          value={name}
          onChangeText={(value) => {
            setName(value);
            checkIfModified('name', value);
          }}
        />

        <Text style={styles.label}>邮箱 | Email</Text>
        <TextInput
          style={styles.input}
          value={email}
          onChangeText={(value) => {
            setEmail(value);
            checkIfModified('email', value);
          }}
          keyboardType="email-address"
        />

        <Text style={styles.label}>电话 | Phone</Text>
        <TextInput
          style={styles.input}
          value={phone}
          onChangeText={(value) => {
            setPhone(value);
            checkIfModified('phone', value);
          }}
          keyboardType="phone-pad"
        />

        <TouchableOpacity
          style={[styles.button, !hasUnsavedChanges && styles.buttonDisabled]}
          onPress={handleSave}
          disabled={!hasUnsavedChanges}
        >
          <Text style={styles.buttonText}>
            {hasUnsavedChanges ? '保存更改 | Save Changes' : '无更改 | No Changes'}
          </Text>
        </TouchableOpacity>

        {hasUnsavedChanges && (
          <Text style={styles.warning}>
            ⚠️ 您有未保存的更改 | You have unsaved changes
          </Text>
        )}
      </ScrollView>
    );
  }

  // 实用的自定义Hook：useUnsavedChangesWarning | Useful custom hook: useUnsavedChangesWarning
  function useUnsavedChangesWarning(hasUnsavedChanges) {
    const navigation = useNavigation();

    useEffect(() => {
      const unsubscribe = navigation.addListener('beforeRemove', (e) => {
        if (!hasUnsavedChanges) {
          return;
        }

        e.preventDefault();

        Alert.alert(
          '放弃更改？| Discard changes?',
          '您有未保存的更改 | You have unsaved changes',
          [
            { text: '取消 | Cancel', style: 'cancel', onPress: () => {} },
            {
              text: '放弃 | Discard',
              style: 'destructive',
              onPress: () => navigation.dispatch(e.data.action),
            },
          ]
        );
      });

      return unsubscribe;
    }, [navigation, hasUnsavedChanges]);
  }

  // 使用自定义Hook | Using custom hook
  function AnotherFormScreen() {
    const [text, setText] = useState('');
    const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);

    // 一行代码实现表单保护 | Implement form protection in one line
    useUnsavedChangesWarning(hasUnsavedChanges);

    return (
      <View>
        <TextInput
          value={text}
          onChangeText={(value) => {
            setText(value);
            setHasUnsavedChanges(value.length > 0);
          }}
        />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在EditProfileScreen中，如果用户修改了name字段然后又改回初始值，hasUnsavedChanges会是什么？| In EditProfileScreen, if user modifies name field then changes it back to initial value, what will hasUnsavedChanges be?
    **答案 | Answer:** 在方法1中仍为true（因为一旦修改就标记），方法2会检测到值未变化所以为false | In Method 1 still true (marked as soon as modified), Method 2 would detect no change so false
  - useUnsavedChangesWarning这个自定义Hook的优势是什么？| What's the advantage of the useUnsavedChangesWarning custom hook?
    **答案 | Answer:** 代码复用，多个表单页面可以共享相同的保护逻辑 | Code reuse, multiple form pages can share the same protection logic

  **常见误区检查 | Common Misconception Checks:**
  - 误区：只要用户打开了表单页面就应该显示警告 | Misconception: Warning should be shown as soon as user opens form page
    **答案 | Answer:** 错误 | Incorrect - 只有当用户实际修改了数据时才应该警告 | Warning should only appear when user actually modifies data
  - 误区：保存失败后应该允许用户直接返回 | Misconception: After save fails, user should be allowed to go back directly
    **答案 | Answer:** 错误 | Incorrect - 保存失败后hasUnsavedChanges仍应为true，保持保护 | After save fails, hasUnsavedChanges should still be true, maintain protection
