# React Native入门 - 第5天：TextInput与表单处理 | React Native Introduction - Day 5: TextInput and Forms

## 学习目标 | Learning Objectives
- 掌握TextInput组件的核心特性和使用方法 | Master the core features and usage of the TextInput component
- 理解受控组件模式在移动端表单中的应用 | Understand controlled component patterns in mobile forms
- 学会使用不同的键盘类型优化用户输入体验 | Learn to use different keyboard types to optimize user input experience
- 掌握表单验证和错误处理的最佳实践 | Master best practices for form validation and error handling
- 理解移动端表单与Web表单的关键差异 | Understand key differences between mobile and web forms
- 能够构建完整的登录/注册表单组件 | Be able to build complete login/registration form components

## 详细内容 | Detailed Content

### 1. TextInput基础概念 | TextInput Fundamentals (1小时 | 1 hour)

- **受控TextInput组件 | Controlled TextInput Component**

  **概念定义 | Concept Definition:**
  受控TextInput是指通过React state管理输入值的组件，其value属性由state控制，所有输入变化通过onChangeText回调更新state。这确保了React state是唯一的数据源。 | A controlled TextInput is a component whose input value is managed by React state, with its value prop controlled by state and all input changes updating state through the onChangeText callback. This ensures React state is the single source of truth.

  **核心特征 | Key Characteristics:**
  - TextInput的value必须绑定到state变量 | TextInput's value must be bound to a state variable
  - 用户输入通过onChangeText回调触发state更新 | User input triggers state updates through onChangeText callback
  - state变化导致组件重新渲染，显示新值 | State changes cause component re-render, displaying new value
  - 这种单向数据流使表单状态可预测和可控 | This unidirectional data flow makes form state predictable and controllable

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在受控TextInput中，value属性应该绑定到什么？| In a controlled TextInput, what should the value prop be bound to?
     **答案 | Answer:** state变量 | A state variable - 这确保React state是数据的唯一来源 | This ensures React state is the single source of truth
  2. 当用户在TextInput中输入文字时，哪个回调会被触发？| When a user types in a TextInput, which callback is triggered?
     **答案 | Answer:** onChangeText - 不是onChange，这是与Web的input元素的关键区别 | onChangeText - not onChange, this is a key difference from web input elements
  3. 如果不设置onChangeText回调，用户能在TextInput中输入文字吗？| If you don't set an onChangeText callback, can users type in the TextInput?
     **答案 | Answer:** 不能 | No - 因为value被固定为state值，没有回调来更新state，输入无法显示 | Because value is fixed to the state value, without a callback to update state, input won't display
  4. 受控组件模式在表单验证方面有什么优势？| What advantage does the controlled component pattern have for form validation?
     **答案 | Answer:** 可以在onChangeText中实时验证和格式化输入 | Can validate and format input in real-time within onChangeText

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet } from 'react-native';

  // 受控TextInput基础示例 | Basic controlled TextInput example
  function ControlledInputDemo() {
    // 创建state来存储输入值 | Create state to store input value
    const [text, setText] = useState('');

    return (
      <View style={styles.container}>
        {/* TextInput的value绑定到state | TextInput value bound to state */}
        {/* onChangeText更新state | onChangeText updates state */}
        <TextInput
          style={styles.input}
          value={text}
          onChangeText={setText}  // 简写形式 | Shorthand form
          placeholder="在此输入... | Type here..."
        />

        {/* 显示当前输入值 | Display current input value */}
        <Text style={styles.output}>
          你输入了 | You typed: {text}
        </Text>

        {/* 显示字符数 | Display character count */}
        <Text style={styles.counter}>
          字符数 | Characters: {text.length}
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    input: {
      borderWidth: 1,
      borderColor: '#ccc',
      padding: 10,
      borderRadius: 5,
      fontSize: 16,
    },
    output: {
      marginTop: 10,
      fontSize: 14,
      color: '#333',
    },
    counter: {
      marginTop: 5,
      fontSize: 12,
      color: '#666',
    },
  });

  export default ControlledInputDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果将onChangeText改为`onChangeText={(newText) => setText(newText.toUpperCase())}`会发生什么？| What happens if you change onChangeText to `onChangeText={(newText) => setText(newText.toUpperCase())}`?
    **答案 | Answer:** 所有输入会自动转换为大写字母 | All input will automatically convert to uppercase - 展示了在输入时进行转换的能力 | Demonstrates the ability to transform input as it's typed
  - 为什么不使用onChange而使用onChangeText？| Why use onChangeText instead of onChange?
    **答案 | Answer:** onChangeText直接提供文本字符串，更简洁；onChange提供事件对象，需要event.nativeEvent.text | onChangeText provides the text string directly, which is more concise; onChange provides an event object requiring event.nativeEvent.text

  **常见误区检查 | Common Misconception Checks:**
  - TextInput会自动保存输入值吗？| Does TextInput automatically save the input value?
    **答案 | Answer:** 不会 | No - 必须通过state管理，否则输入会丢失 | Must be managed through state, otherwise input will be lost
  - 可以像Web一样使用defaultValue而不使用state吗？| Can you use defaultValue like in web without using state?
    **答案 | Answer:** 可以，但这是非受控组件，失去了验证和控制能力 | Yes, but this creates an uncontrolled component, losing validation and control capabilities

- **TextInput核心属性 | TextInput Core Props**

  **概念定义 | Concept Definition:**
  TextInput提供了丰富的属性来控制输入行为、外观和键盘类型。这些属性帮助开发者创建符合移动端UX标准的表单。 | TextInput provides rich props to control input behavior, appearance, and keyboard type. These props help developers create forms that meet mobile UX standards.

  **核心特征 | Key Characteristics:**
  - placeholder: 占位符文本，输入为空时显示 | placeholder: Placeholder text shown when input is empty
  - keyboardType: 控制显示的键盘类型（数字、邮箱等）| keyboardType: Controls the type of keyboard displayed (numeric, email, etc.)
  - secureTextEntry: 密码输入模式，隐藏输入内容 | secureTextEntry: Password input mode, hides input content
  - autoCapitalize: 自动大写模式（none, sentences, words, characters）| autoCapitalize: Auto-capitalization mode (none, sentences, words, characters)
  - autoCorrect: 是否启用自动纠错 | autoCorrect: Whether to enable auto-correction
  - multiline: 是否支持多行输入 | multiline: Whether to support multi-line input

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. keyboardType='numeric'会阻止用户输入字母吗？| Does keyboardType='numeric' prevent users from entering letters?
     **答案 | Answer:** 不会完全阻止 | Not completely - 它只改变显示的键盘类型，但用户可能通过其他方式输入字母，需要配合验证 | It only changes the keyboard type displayed, but users might enter letters through other means, needs to work with validation
  2. secureTextEntry和password keyboardType有什么区别？| What's the difference between secureTextEntry and password keyboardType?
     **答案 | Answer:** secureTextEntry隐藏输入内容（显示点），keyboardType优化键盘布局 | secureTextEntry hides input content (shows dots), keyboardType optimizes keyboard layout - 两者通常一起使用 | Both are typically used together
  3. placeholder文本会被包含在TextInput的value中吗？| Is placeholder text included in the TextInput's value?
     **答案 | Answer:** 不会 | No - placeholder只是视觉提示，不影响实际值 | Placeholder is only a visual hint, doesn't affect actual value
  4. autoCapitalize='none'在什么场景下重要？| In what scenario is autoCapitalize='none' important?
     **答案 | Answer:** 邮箱和用户名输入 | Email and username input - 因为这些字段通常不应该自动大写 | Because these fields typically shouldn't auto-capitalize

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet } from 'react-native';

  // 不同TextInput属性的演示 | Demonstration of different TextInput props
  function TextInputPropsDemo() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [phone, setPhone] = useState('');
    const [bio, setBio] = useState('');

    return (
      <View style={styles.container}>
        {/* 邮箱输入：邮箱键盘 + 不自动大写 + 不自动纠错 */}
        {/* Email input: email keyboard + no auto-capitalize + no auto-correct */}
        <Text style={styles.label}>邮箱 | Email</Text>
        <TextInput
          style={styles.input}
          value={email}
          onChangeText={setEmail}
          placeholder="example@email.com"
          keyboardType="email-address"  // 显示邮箱键盘，包含@符号 | Shows email keyboard with @ symbol
          autoCapitalize="none"  // 禁用自动大写 | Disable auto-capitalization
          autoCorrect={false}  // 禁用自动纠错 | Disable auto-correction
        />

        {/* 密码输入：隐藏文本 */}
        {/* Password input: hide text */}
        <Text style={styles.label}>密码 | Password</Text>
        <TextInput
          style={styles.input}
          value={password}
          onChangeText={setPassword}
          placeholder="输入密码 | Enter password"
          secureTextEntry={true}  // 隐藏输入内容 | Hide input content
          autoCapitalize="none"
        />

        {/* 手机号输入：数字键盘 */}
        {/* Phone input: numeric keyboard */}
        <Text style={styles.label}>手机号 | Phone</Text>
        <TextInput
          style={styles.input}
          value={phone}
          onChangeText={setPhone}
          placeholder="1234567890"
          keyboardType="phone-pad"  // 显示电话号码键盘 | Shows phone number keyboard
        />

        {/* 多行文本输入 */}
        {/* Multi-line text input */}
        <Text style={styles.label}>个人简介 | Bio</Text>
        <TextInput
          style={[styles.input, styles.multiline]}
          value={bio}
          onChangeText={setBio}
          placeholder="介绍一下自己... | Tell us about yourself..."
          multiline={true}  // 启用多行输入 | Enable multi-line input
          numberOfLines={4}  // iOS上的初始行数 | Initial number of lines on iOS
          textAlignVertical="top"  // Android上文本顶部对齐 | Text aligns to top on Android
        />
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    label: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 5,
      marginTop: 15,
      color: '#333',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    multiline: {
      height: 100,  // 多行输入需要固定高度 | Multi-line input needs fixed height
    },
  });

  export default TextInputPropsDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 将phone的keyboardType改为'numeric'会有什么不同？| What's different if you change phone's keyboardType to 'numeric'?
    **答案 | Answer:** 'phone-pad'显示电话键盘（带*#等），'numeric'只显示数字 | 'phone-pad' shows phone keyboard (with *# etc.), 'numeric' shows only numbers
  - multiline TextInput需要设置高度吗？| Does a multiline TextInput need a height set?
    **答案 | Answer:** 是的 | Yes - 否则在某些平台上可能只显示一行 | Otherwise it might only show one line on some platforms

  **常见误区检查 | Common Misconception Checks:**
  - keyboardType可以完全阻止错误类型的输入吗？| Can keyboardType completely prevent wrong type input?
    **答案 | Answer:** 不能 | No - 它只是UX优化，仍需要在代码中进行验证 | It's only a UX optimization, still need code validation
  - secureTextEntry会加密密码吗？| Does secureTextEntry encrypt the password?
    **答案 | Answer:** 不会 | No - 它只是视觉隐藏，不提供任何加密或安全功能 | It only provides visual hiding, no encryption or security features

### 2. 表单验证与状态管理 | Form Validation and State Management (1小时 | 1 hour)

- **实时验证与错误处理 | Real-time Validation and Error Handling**

  **概念定义 | Concept Definition:**
  实时验证是在用户输入时立即检查输入有效性的技术。在React Native中，这通过在onChangeText或onBlur回调中执行验证逻辑来实现，提供即时反馈。 | Real-time validation is a technique that immediately checks input validity as the user types. In React Native, this is achieved by executing validation logic in onChangeText or onBlur callbacks, providing instant feedback.

  **核心特征 | Key Characteristics:**
  - 使用state存储验证错误信息 | Use state to store validation error messages
  - 在输入改变时执行验证规则 | Execute validation rules when input changes
  - 通过视觉反馈显示错误（颜色、边框、错误文本）| Display errors through visual feedback (color, border, error text)
  - 区分"已触碰"和"未触碰"字段，避免过早显示错误 | Distinguish between "touched" and "untouched" fields to avoid showing errors prematurely

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应该在每次按键时都显示验证错误吗？| Should validation errors be displayed on every keystroke?
     **答案 | Answer:** 不一定 | Not necessarily - 最佳实践是在字段失去焦点（onBlur）后显示，或使用"touched"状态 | Best practice is to show after field loses focus (onBlur), or use "touched" state
  2. 验证逻辑应该放在哪里？| Where should validation logic be placed?
     **答案 | Answer:** 可以在onChangeText中（实时）或onBlur中（失焦时），也可以在提交时 | Can be in onChangeText (real-time) or onBlur (on blur), or on submission - 取决于UX需求 | Depends on UX requirements
  3. 如何防止显示"字段不能为空"错误在用户还未输入时？| How to prevent showing "field cannot be empty" error before user has typed?
     **答案 | Answer:** 使用"touched"状态，只在用户交互过该字段后显示错误 | Use "touched" state, only show errors after user has interacted with the field
  4. 客户端验证足够吗？| Is client-side validation sufficient?
     **答案 | Answer:** 不够 | No - 仍需服务器端验证，客户端验证只是UX改进 | Still need server-side validation, client-side is only UX improvement

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet } from 'react-native';

  // 带验证的邮箱输入组件 | Email input component with validation
  function ValidatedEmailInput() {
    const [email, setEmail] = useState('');
    const [error, setError] = useState('');
    const [touched, setTouched] = useState(false);

    // 邮箱验证函数 | Email validation function
    const validateEmail = (text) => {
      // 简单的邮箱正则表达式 | Simple email regex
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;

      if (!text) {
        return '邮箱不能为空 | Email cannot be empty';
      }

      if (!emailRegex.test(text)) {
        return '请输入有效的邮箱地址 | Please enter a valid email address';
      }

      return ''; // 无错误 | No error
    };

    // 处理文本改变 | Handle text change
    const handleChangeText = (text) => {
      setEmail(text);
      // 实时验证 | Real-time validation
      const validationError = validateEmail(text);
      setError(validationError);
    };

    // 处理失去焦点 | Handle blur
    const handleBlur = () => {
      setTouched(true); // 标记为已触碰 | Mark as touched
    };

    // 计算是否显示错误 | Determine whether to show error
    const shouldShowError = touched && error;

    return (
      <View style={styles.container}>
        <Text style={styles.label}>邮箱地址 | Email Address</Text>

        <TextInput
          style={[
            styles.input,
            // 根据验证状态改变边框颜色 | Change border color based on validation state
            shouldShowError && styles.inputError,
            touched && !error && styles.inputValid,
          ]}
          value={email}
          onChangeText={handleChangeText}
          onBlur={handleBlur}
          placeholder="example@email.com"
          keyboardType="email-address"
          autoCapitalize="none"
          autoCorrect={false}
        />

        {/* 只在touched且有错误时显示错误消息 */}
        {/* Only show error message when touched and has error */}
        {shouldShowError && (
          <Text style={styles.errorText}>{error}</Text>
        )}

        {/* 显示验证成功提示 */}
        {/* Show validation success hint */}
        {touched && !error && email && (
          <Text style={styles.successText}>✓ 邮箱格式正确 | Email format is valid</Text>
        )}
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    label: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 5,
      color: '#333',
    },
    input: {
      borderWidth: 2,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    inputError: {
      borderColor: '#ff3b30', // 红色边框表示错误 | Red border for error
    },
    inputValid: {
      borderColor: '#34c759', // 绿色边框表示有效 | Green border for valid
    },
    errorText: {
      color: '#ff3b30',
      fontSize: 12,
      marginTop: 5,
    },
    successText: {
      color: '#34c759',
      fontSize: 12,
      marginTop: 5,
    },
  });

  export default ValidatedEmailInput;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果移除`touched`状态会怎样？| What happens if you remove the `touched` state?
    **答案 | Answer:** 错误会在组件加载时立即显示（因为初始email为空），体验不好 | Errors will show immediately on component load (because initial email is empty), poor UX
  - 为什么在onChangeText和onBlur中都有逻辑？| Why is there logic in both onChangeText and onBlur?
    **答案 | Answer:** onChangeText进行实时验证更新错误状态，onBlur标记字段为touched控制错误显示 | onChangeText does real-time validation updating error state, onBlur marks field as touched controlling error display

  **常见误区检查 | Common Misconception Checks:**
  - 验证通过就意味着数据安全吗？| Does validation passing mean the data is secure?
    **答案 | Answer:** 不是 | No - 客户端验证可被绕过，必须在服务器端也进行验证 | Client-side validation can be bypassed, must also validate on server-side
  - 正则表达式验证邮箱是完美的吗？| Is regex email validation perfect?
    **答案 | Answer:** 不是 | No - 只能检查格式，不能确认邮箱真实存在，完整验证需要发送确认邮件 | Can only check format, can't confirm email actually exists, complete validation requires sending confirmation email

- **表单状态管理模式 | Form State Management Patterns**

  **概念定义 | Concept Definition:**
  表单状态管理是指组织和维护表单中所有输入字段、验证错误、提交状态等数据的方法。在复杂表单中，使用对象或自定义Hook来集中管理状态比单独管理每个字段更高效。 | Form state management refers to methods of organizing and maintaining data for all input fields, validation errors, submission status, etc. in a form. In complex forms, using objects or custom hooks to centrally manage state is more efficient than managing each field individually.

  **核心特征 | Key Characteristics:**
  - 使用单个state对象存储所有表单字段 | Use a single state object to store all form fields
  - 统一的字段更新处理函数 | Unified field update handler function
  - 集中的验证逻辑 | Centralized validation logic
  - 提交状态跟踪（loading, success, error）| Submission status tracking (loading, success, error)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为多个字段使用单个state对象比每个字段一个state有什么优势？| What's the advantage of using a single state object for multiple fields vs one state per field?
     **答案 | Answer:** 更易扩展、减少重复代码、便于统一处理和验证 | More scalable, reduces code duplication, easier to handle and validate uniformly
  2. 表单提交时应该检查什么？| What should be checked when submitting a form?
     **答案 | Answer:** 所有字段的验证状态、是否有未填必填字段、是否已在提交中（防止重复提交）| Validation status of all fields, whether required fields are filled, whether already submitting (prevent duplicate submission)
  3. 如何实现一个通用的字段更新函数？| How to implement a generic field update function?
     **答案 | Answer:** 使用字段名作为参数，通过计算属性名更新对应字段 | Use field name as parameter, update corresponding field through computed property name
  4. 表单重置应该做什么？| What should form reset do?
     **答案 | Answer:** 清空所有字段值、清除所有错误、重置touched状态 | Clear all field values, clear all errors, reset touched state

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, TouchableOpacity, StyleSheet } from 'react-native';

  // 使用集中状态管理的注册表单 | Registration form with centralized state management
  function RegistrationForm() {
    // 单个state对象管理所有表单数据 | Single state object manages all form data
    const [formData, setFormData] = useState({
      username: '',
      email: '',
      password: '',
      confirmPassword: '',
    });

    // 错误状态 | Error state
    const [errors, setErrors] = useState({});

    // 已触碰字段 | Touched fields
    const [touched, setTouched] = useState({});

    // 提交状态 | Submission state
    const [isSubmitting, setIsSubmitting] = useState(false);

    // 通用字段更新函数 | Generic field update function
    const handleFieldChange = (fieldName, value) => {
      // 更新字段值 | Update field value
      setFormData(prev => ({
        ...prev,
        [fieldName]: value, // 使用计算属性名 | Use computed property name
      }));

      // 实时验证该字段 | Real-time validate this field
      const error = validateField(fieldName, value);
      setErrors(prev => ({
        ...prev,
        [fieldName]: error,
      }));
    };

    // 字段失焦处理 | Field blur handler
    const handleFieldBlur = (fieldName) => {
      setTouched(prev => ({
        ...prev,
        [fieldName]: true,
      }));
    };

    // 单字段验证 | Single field validation
    const validateField = (fieldName, value) => {
      switch (fieldName) {
        case 'username':
          if (!value) return '用户名不能为空 | Username cannot be empty';
          if (value.length < 3) return '用户名至少3个字符 | Username must be at least 3 characters';
          return '';

        case 'email':
          if (!value) return '邮箱不能为空 | Email cannot be empty';
          const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
          if (!emailRegex.test(value)) return '邮箱格式无效 | Invalid email format';
          return '';

        case 'password':
          if (!value) return '密码不能为空 | Password cannot be empty';
          if (value.length < 6) return '密码至少6个字符 | Password must be at least 6 characters';
          return '';

        case 'confirmPassword':
          if (!value) return '请确认密码 | Please confirm password';
          if (value !== formData.password) return '密码不匹配 | Passwords do not match';
          return '';

        default:
          return '';
      }
    };

    // 验证整个表单 | Validate entire form
    const validateForm = () => {
      const newErrors = {};
      Object.keys(formData).forEach(fieldName => {
        const error = validateField(fieldName, formData[fieldName]);
        if (error) newErrors[fieldName] = error;
      });
      setErrors(newErrors);
      return Object.keys(newErrors).length === 0; // 返回是否有效 | Return whether valid
    };

    // 处理表单提交 | Handle form submission
    const handleSubmit = async () => {
      // 标记所有字段为touched | Mark all fields as touched
      const allTouched = {};
      Object.keys(formData).forEach(key => {
        allTouched[key] = true;
      });
      setTouched(allTouched);

      // 验证表单 | Validate form
      const isValid = validateForm();
      if (!isValid) {
        console.log('表单验证失败 | Form validation failed');
        return;
      }

      // 提交表单 | Submit form
      setIsSubmitting(true);
      try {
        // 模拟API调用 | Simulate API call
        await new Promise(resolve => setTimeout(resolve, 2000));
        console.log('表单提交成功 | Form submitted successfully', formData);
        alert('注册成功！| Registration successful!');
      } catch (error) {
        console.error('提交失败 | Submission failed', error);
        alert('注册失败，请重试 | Registration failed, please try again');
      } finally {
        setIsSubmitting(false);
      }
    };

    // 渲染表单字段 | Render form field
    const renderField = (fieldName, placeholder, additionalProps = {}) => {
      const hasError = touched[fieldName] && errors[fieldName];
      const isValid = touched[fieldName] && !errors[fieldName] && formData[fieldName];

      return (
        <View style={styles.fieldContainer}>
          <TextInput
            style={[
              styles.input,
              hasError && styles.inputError,
              isValid && styles.inputValid,
            ]}
            value={formData[fieldName]}
            onChangeText={(text) => handleFieldChange(fieldName, text)}
            onBlur={() => handleFieldBlur(fieldName)}
            placeholder={placeholder}
            {...additionalProps}
          />
          {hasError && (
            <Text style={styles.errorText}>{errors[fieldName]}</Text>
          )}
        </View>
      );
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>用户注册 | User Registration</Text>

        {renderField('username', '用户名 | Username', {
          autoCapitalize: 'none',
        })}

        {renderField('email', '邮箱 | Email', {
          keyboardType: 'email-address',
          autoCapitalize: 'none',
          autoCorrect: false,
        })}

        {renderField('password', '密码 | Password', {
          secureTextEntry: true,
          autoCapitalize: 'none',
        })}

        {renderField('confirmPassword', '确认密码 | Confirm Password', {
          secureTextEntry: true,
          autoCapitalize: 'none',
        })}

        <TouchableOpacity
          style={[
            styles.submitButton,
            isSubmitting && styles.submitButtonDisabled,
          ]}
          onPress={handleSubmit}
          disabled={isSubmitting}
        >
          <Text style={styles.submitButtonText}>
            {isSubmitting ? '提交中... | Submitting...' : '注册 | Register'}
          </Text>
        </TouchableOpacity>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    fieldContainer: {
      marginBottom: 15,
    },
    input: {
      borderWidth: 2,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    inputError: {
      borderColor: '#ff3b30',
    },
    inputValid: {
      borderColor: '#34c759',
    },
    errorText: {
      color: '#ff3b30',
      fontSize: 12,
      marginTop: 5,
    },
    submitButton: {
      backgroundColor: '#007AFF',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
      marginTop: 10,
    },
    submitButtonDisabled: {
      backgroundColor: '#ccc',
    },
    submitButtonText: {
      color: '#fff',
      fontSize: 16,
      fontWeight: '600',
    },
  });

  export default RegistrationForm;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - handleFieldChange函数如何处理不同的字段？| How does handleFieldChange handle different fields?
    **答案 | Answer:** 使用fieldName参数和计算属性名[fieldName]动态更新对应字段 | Uses fieldName parameter and computed property name [fieldName] to dynamically update corresponding field
  - 为什么在提交时要标记所有字段为touched？| Why mark all fields as touched on submission?
    **答案 | Answer:** 确保所有验证错误都显示出来，让用户知道哪些字段需要修正 | Ensures all validation errors are displayed, letting users know which fields need correction

  **常见误区检查 | Common Misconception Checks:**
  - 集中状态管理会导致性能问题吗？| Does centralized state management cause performance issues?
    **答案 | Answer:** 对于中小型表单不会 | Not for small to medium forms - 大型表单可考虑使用表单库或分割状态 | For large forms consider using form libraries or splitting state
  - 是否需要第三方表单库？| Is a third-party form library needed?
    **答案 | Answer:** 不是必须的 | Not required - 简单表单可用原生state，复杂表单可选用Formik或React Hook Form | Simple forms can use native state, complex forms can optionally use Formik or React Hook Form

### 3. 键盘交互与用户体验 | Keyboard Interaction and User Experience (1小时 | 1 hour)

- **键盘类型与移动端优化 | Keyboard Types and Mobile Optimization**

  **概念定义 | Concept Definition:**
  键盘类型（keyboardType）是React Native提供的一种移动端优化特性，通过为不同输入类型显示专门的键盘布局，提高输入效率和准确性。 | Keyboard type (keyboardType) is a mobile optimization feature provided by React Native that improves input efficiency and accuracy by displaying specialized keyboard layouts for different input types.

  **核心特征 | Key Characteristics:**
  - default: 标准全键盘 | Standard full keyboard
  - email-address: 邮箱键盘（包含@和.）| Email keyboard (includes @ and .)
  - numeric: 纯数字键盘 | Pure numeric keyboard
  - phone-pad: 电话键盘（数字加*#）| Phone keyboard (numbers plus *#)
  - decimal-pad: 带小数点的数字键盘 | Numeric keyboard with decimal point
  - number-pad: 数字键盘（iOS）| Number pad (iOS)
  - url: URL键盘（包含/和.com）| URL keyboard (includes / and .com)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. keyboardType='numeric'和'phone-pad'的区别是什么？| What's the difference between keyboardType='numeric' and 'phone-pad'?
     **答案 | Answer:** 'numeric'只有数字，'phone-pad'包含*#等电话符号 | 'numeric' has only numbers, 'phone-pad' includes phone symbols like *#
  2. 设置keyboardType后还需要验证输入吗？| Do you still need to validate input after setting keyboardType?
     **答案 | Answer:** 是的 | Yes - keyboardType只是建议性的，不强制限制输入 | keyboardType is only suggestive, doesn't enforce input restrictions
  3. Web的input type和React Native的keyboardType一样吗？| Is web's input type the same as React Native's keyboardType?
     **答案 | Answer:** 概念相似但不完全一样 | Similar concept but not exactly the same - Web的type有更多功能（如验证），RN的keyboardType只控制键盘 | Web's type has more functionality (like validation), RN's keyboardType only controls keyboard
  4. 哪个keyboardType适合信用卡号输入？| Which keyboardType is suitable for credit card number input?
     **答案 | Answer:** 'number-pad'或'numeric' | 'number-pad' or 'numeric' - 取决于平台和是否需要格式化 | Depends on platform and whether formatting is needed

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet, ScrollView } from 'react-native';

  // 不同键盘类型演示 | Different keyboard types demonstration
  function KeyboardTypesDemo() {
    const [inputs, setInputs] = useState({
      email: '',
      phone: '',
      number: '',
      decimal: '',
      url: '',
    });

    const handleChange = (field, value) => {
      setInputs(prev => ({ ...prev, [field]: value }));
    };

    // 键盘类型配置 | Keyboard type configurations
    const keyboardConfigs = [
      {
        label: '邮箱地址 | Email Address',
        field: 'email',
        placeholder: 'user@example.com',
        keyboardType: 'email-address',
        description: '显示邮箱键盘，包含@和. | Shows email keyboard with @ and .',
      },
      {
        label: '电话号码 | Phone Number',
        field: 'phone',
        placeholder: '(123) 456-7890',
        keyboardType: 'phone-pad',
        description: '显示电话键盘，包含*#等 | Shows phone keyboard with *# etc.',
      },
      {
        label: '数量 | Quantity',
        field: 'number',
        placeholder: '0',
        keyboardType: 'number-pad',
        description: '纯数字键盘（iOS）| Pure number pad (iOS)',
      },
      {
        label: '价格 | Price',
        field: 'decimal',
        placeholder: '0.00',
        keyboardType: 'decimal-pad',
        description: '带小数点的数字键盘 | Number keyboard with decimal point',
      },
      {
        label: '网址 | URL',
        field: 'url',
        placeholder: 'https://example.com',
        keyboardType: 'url',
        description: 'URL键盘，包含/和.com | URL keyboard with / and .com',
      },
    ];

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.header}>
          键盘类型演示 | Keyboard Types Demo
        </Text>
        <Text style={styles.subheader}>
          点击输入框查看不同的键盘类型 | Tap input fields to see different keyboard types
        </Text>

        {keyboardConfigs.map((config) => (
          <View key={config.field} style={styles.fieldContainer}>
            <Text style={styles.label}>{config.label}</Text>
            <Text style={styles.description}>{config.description}</Text>
            <TextInput
              style={styles.input}
              value={inputs[config.field]}
              onChangeText={(text) => handleChange(config.field, text)}
              placeholder={config.placeholder}
              keyboardType={config.keyboardType}
              autoCapitalize="none"
            />
            <Text style={styles.inputValue}>
              当前值 | Current: {inputs[config.field] || '(空 | empty)'}
            </Text>
          </View>
        ))}
      </ScrollView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#f5f5f5',
    },
    header: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
      textAlign: 'center',
    },
    subheader: {
      fontSize: 14,
      color: '#666',
      marginBottom: 20,
      textAlign: 'center',
    },
    fieldContainer: {
      marginBottom: 25,
      backgroundColor: '#fff',
      padding: 15,
      borderRadius: 10,
    },
    label: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 5,
      color: '#333',
    },
    description: {
      fontSize: 12,
      color: '#666',
      marginBottom: 10,
      fontStyle: 'italic',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
      backgroundColor: '#fafafa',
    },
    inputValue: {
      fontSize: 12,
      color: '#007AFF',
      marginTop: 5,
    },
  });

  export default KeyboardTypesDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在Android和iOS上，相同的keyboardType显示的键盘会一样吗？| Do the same keyboardType show identical keyboards on Android and iOS?
    **答案 | Answer:** 不完全一样 | Not exactly the same - 每个平台有自己的键盘设计，但功能相似 | Each platform has its own keyboard design, but functionality is similar
  - 如何强制只允许输入数字？| How to force only numeric input?
    **答案 | Answer:** 使用keyboardType='numeric'并在onChangeText中过滤非数字字符 | Use keyboardType='numeric' and filter non-numeric characters in onChangeText

  **常见误区检查 | Common Misconception Checks:**
  - keyboardType可以阻止用户粘贴不合适的内容吗？| Can keyboardType prevent users from pasting inappropriate content?
    **答案 | Answer:** 不能 | No - 用户仍可粘贴任何内容，必须在代码中验证 | Users can still paste any content, must validate in code
  - 所有keyboardType在所有平台上都可用吗？| Are all keyboardType available on all platforms?
    **答案 | Answer:** 大部分可用，但有些是平台特定的（如number-pad是iOS特有）| Most are available, but some are platform-specific (like number-pad is iOS-specific)

- **键盘行为管理 | Keyboard Behavior Management**

  **概念定义 | Concept Definition:**
  键盘行为管理涉及控制键盘的出现、隐藏、以及键盘与UI的交互。在移动端，键盘会占据屏幕空间，需要妥善处理以避免遮挡内容和提供良好的用户体验。 | Keyboard behavior management involves controlling keyboard appearance, dismissal, and keyboard-UI interaction. On mobile, keyboards occupy screen space and need proper handling to avoid blocking content and provide good UX.

  **核心特征 | Key Characteristics:**
  - returnKeyType: 设置键盘返回键类型（done, go, next, search等）| Sets keyboard return key type (done, go, next, search, etc.)
  - onSubmitEditing: 返回键被按下时的回调 | Callback when return key is pressed
  - blurOnSubmit: 提交时是否失去焦点 | Whether to blur on submit
  - KeyboardAvoidingView: 自动调整UI避开键盘 | Automatically adjust UI to avoid keyboard
  - Keyboard.dismiss(): 编程方式关闭键盘 | Programmatically dismiss keyboard

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. returnKeyType='next'有什么作用？| What does returnKeyType='next' do?
     **答案 | Answer:** 将返回键显示为"下一步"，配合onSubmitEditing可自动聚焦到下一个输入框 | Displays return key as "Next", can auto-focus next input with onSubmitEditing
  2. KeyboardAvoidingView和ScrollView有什么区别？| What's the difference between KeyboardAvoidingView and ScrollView?
     **答案 | Answer:** KeyboardAvoidingView调整位置避开键盘，ScrollView提供滚动功能 | KeyboardAvoidingView adjusts position to avoid keyboard, ScrollView provides scrolling - 通常一起使用 | Often used together
  3. 如何在用户点击表单外部时关闭键盘？| How to dismiss keyboard when user taps outside the form?
     **答案 | Answer:** 用TouchableWithoutFeedback包裹，onPress调用Keyboard.dismiss() | Wrap with TouchableWithoutFeedback, call Keyboard.dismiss() in onPress
  4. blurOnSubmit默认值是什么？| What's the default value of blurOnSubmit?
     **答案 | Answer:** 单行输入为true，多行输入为false | true for single-line, false for multi-line

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useRef } from 'react';
  import {
    View,
    TextInput,
    Text,
    TouchableOpacity,
    StyleSheet,
    KeyboardAvoidingView,
    Platform,
    TouchableWithoutFeedback,
    Keyboard,
  } from 'react-native';

  // 键盘行为管理示例 | Keyboard behavior management example
  function KeyboardBehaviorDemo() {
    const [formData, setFormData] = useState({
      name: '',
      email: '',
      message: '',
    });

    // 创建refs用于聚焦管理 | Create refs for focus management
    const emailInputRef = useRef(null);
    const messageInputRef = useRef(null);

    const handleChange = (field, value) => {
      setFormData(prev => ({ ...prev, [field]: value }));
    };

    const handleSubmit = () => {
      Keyboard.dismiss(); // 关闭键盘 | Dismiss keyboard
      console.log('表单提交 | Form submitted:', formData);
      alert(`提交成功！| Submitted!\nName: ${formData.name}`);
    };

    return (
      // TouchableWithoutFeedback: 点击外部关闭键盘 | Tap outside to dismiss keyboard
      <TouchableWithoutFeedback onPress={Keyboard.dismiss}>
        {/* KeyboardAvoidingView: 自动避开键盘 | Automatically avoid keyboard */}
        <KeyboardAvoidingView
          style={styles.container}
          behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
          keyboardVerticalOffset={Platform.OS === 'ios' ? 64 : 0}
        >
          <View style={styles.formContainer}>
            <Text style={styles.title}>
              联系表单 | Contact Form
            </Text>

            {/* 第一个输入框：returnKeyType='next'，按下后聚焦到下一个 */}
            {/* First input: returnKeyType='next', focuses next on press */}
            <Text style={styles.label}>姓名 | Name</Text>
            <TextInput
              style={styles.input}
              value={formData.name}
              onChangeText={(text) => handleChange('name', text)}
              placeholder="输入你的姓名 | Enter your name"
              returnKeyType="next"  // 显示"下一步"键 | Show "Next" key
              onSubmitEditing={() => emailInputRef.current?.focus()}  // 聚焦到邮箱 | Focus email
              blurOnSubmit={false}  // 不失去焦点 | Don't blur
            />

            {/* 第二个输入框：邮箱，也是next */}
            {/* Second input: email, also next */}
            <Text style={styles.label}>邮箱 | Email</Text>
            <TextInput
              ref={emailInputRef}  // 设置ref | Set ref
              style={styles.input}
              value={formData.email}
              onChangeText={(text) => handleChange('email', text)}
              placeholder="输入你的邮箱 | Enter your email"
              keyboardType="email-address"
              autoCapitalize="none"
              returnKeyType="next"
              onSubmitEditing={() => messageInputRef.current?.focus()}  // 聚焦到消息 | Focus message
              blurOnSubmit={false}
            />

            {/* 第三个输入框：多行，returnKeyType='done' */}
            {/* Third input: multiline, returnKeyType='done' */}
            <Text style={styles.label}>消息 | Message</Text>
            <TextInput
              ref={messageInputRef}
              style={[styles.input, styles.multilineInput]}
              value={formData.message}
              onChangeText={(text) => handleChange('message', text)}
              placeholder="输入你的消息... | Enter your message..."
              multiline
              numberOfLines={4}
              textAlignVertical="top"
              returnKeyType="done"  // 多行输入用"完成" | Use "Done" for multiline
              blurOnSubmit={true}  // 多行输入按done时失去焦点 | Blur on done for multiline
            />

            <TouchableOpacity
              style={styles.submitButton}
              onPress={handleSubmit}
            >
              <Text style={styles.submitButtonText}>
                提交 | Submit
              </Text>
            </TouchableOpacity>

            <Text style={styles.hint}>
              提示：点击表单外部可关闭键盘 | Tip: Tap outside to dismiss keyboard
            </Text>
          </View>
        </KeyboardAvoidingView>
      </TouchableWithoutFeedback>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    formContainer: {
      padding: 20,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    label: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 5,
      marginTop: 10,
      color: '#333',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    multilineInput: {
      height: 100,
    },
    submitButton: {
      backgroundColor: '#007AFF',
      padding: 15,
      borderRadius: 8,
      alignItems: 'center',
      marginTop: 20,
    },
    submitButtonText: {
      color: '#fff',
      fontSize: 16,
      fontWeight: '600',
    },
    hint: {
      fontSize: 12,
      color: '#666',
      textAlign: 'center',
      marginTop: 15,
      fontStyle: 'italic',
    },
  });

  export default KeyboardBehaviorDemo;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么前两个输入框设置blurOnSubmit={false}？| Why set blurOnSubmit={false} for the first two inputs?
    **答案 | Answer:** 保持键盘打开状态，直接聚焦到下一个输入框，提供流畅的输入体验 | Keep keyboard open and directly focus next input, providing smooth input experience
  - KeyboardAvoidingView的behavior属性有哪些值？| What values does KeyboardAvoidingView's behavior prop have?
    **答案 | Answer:** 'height', 'position', 'padding' - iOS通常用padding，Android用height | 'height', 'position', 'padding' - iOS typically uses padding, Android uses height

  **常见误区检查 | Common Misconception Checks:**
  - KeyboardAvoidingView会自动解决所有键盘遮挡问题吗？| Does KeyboardAvoidingView automatically solve all keyboard blocking issues?
    **答案 | Answer:** 不一定 | Not always - 复杂布局可能需要调整或配合ScrollView使用 | Complex layouts might need adjustment or use with ScrollView
  - ref只能用于聚焦管理吗？| Are refs only used for focus management?
    **答案 | Answer:** 不是 | No - 也可用于获取输入值、触发方法等，但React推荐用state管理值 | Can also get input values, trigger methods, but React recommends state for values

### 4. 移动端表单最佳实践 | Mobile Form Best Practices (45分钟 | 45 minutes)

- **移动端UX优化策略 | Mobile UX Optimization Strategies**

  **概念定义 | Concept Definition:**
  移动端表单UX优化是指通过设计和技术手段，使表单在小屏幕设备上更易用、更高效、错误更少的一系列最佳实践。 | Mobile form UX optimization refers to best practices that make forms more usable, efficient, and error-free on small-screen devices through design and technical approaches.

  **核心特征 | Key Characteristics:**
  - 使用合适的keyboardType减少输入错误 | Use appropriate keyboardType to reduce input errors
  - 提供清晰的标签和占位符 | Provide clear labels and placeholders
  - 实时验证和友好的错误提示 | Real-time validation and friendly error messages
  - 合理的表单字段排序和分组 | Logical form field ordering and grouping
  - 自动聚焦和智能Tab键导航 | Auto-focus and smart tab navigation
  - 适当的输入框大小，适合手指点击 | Appropriate input size suitable for finger taps

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么移动端表单字段应该垂直排列而不是水平？| Why should mobile form fields be arranged vertically instead of horizontally?
     **答案 | Answer:** 屏幕宽度有限，垂直排列更易读、更易点击 | Limited screen width, vertical arrangement is more readable and tappable
  2. 占位符可以完全替代标签吗？| Can placeholders completely replace labels?
     **答案 | Answer:** 不能 | No - 输入内容后占位符消失，用户可能忘记字段含义 | Placeholder disappears after input, users might forget field meaning
  3. 何时应该使用autoFocus？| When should autoFocus be used?
     **答案 | Answer:** 单字段表单（如搜索框）或表单是页面主要内容时 | Single-field forms (like search) or when form is main page content - 避免在多表单页面使用 | Avoid in multi-form pages
  4. 移动端表单应该一次显示多少个字段？| How many fields should a mobile form show at once?
     **答案 | Answer:** 尽可能少，复杂表单分步骤或分屏显示 | As few as possible, complex forms split into steps or screens

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet, TouchableOpacity } from 'react-native';

  // 移动端优化表单示例 | Mobile-optimized form example
  function OptimizedMobileForm() {
    const [phoneNumber, setPhoneNumber] = useState('');
    const [amount, setAmount] = useState('');

    // 格式化电话号码 | Format phone number
    const formatPhoneNumber = (text) => {
      // 移除非数字字符 | Remove non-numeric characters
      const cleaned = text.replace(/\D/g, '');

      // 限制为10位 | Limit to 10 digits
      const limited = cleaned.slice(0, 10);

      // 格式化为(123) 456-7890 | Format as (123) 456-7890
      if (limited.length <= 3) {
        return limited;
      } else if (limited.length <= 6) {
        return `(${limited.slice(0, 3)}) ${limited.slice(3)}`;
      } else {
        return `(${limited.slice(0, 3)}) ${limited.slice(3, 6)}-${limited.slice(6)}`;
      }
    };

    // 处理电话号码输入 | Handle phone number input
    const handlePhoneChange = (text) => {
      const formatted = formatPhoneNumber(text);
      setPhoneNumber(formatted);
    };

    // 格式化金额 | Format amount
    const formatAmount = (text) => {
      // 只允许数字和小数点 | Only allow numbers and decimal point
      const cleaned = text.replace(/[^0-9.]/g, '');

      // 确保只有一个小数点 | Ensure only one decimal point
      const parts = cleaned.split('.');
      if (parts.length > 2) {
        return parts[0] + '.' + parts.slice(1).join('');
      }

      // 限制小数位数为2 | Limit decimal places to 2
      if (parts[1] && parts[1].length > 2) {
        return parts[0] + '.' + parts[1].slice(0, 2);
      }

      return cleaned;
    };

    // 处理金额输入 | Handle amount input
    const handleAmountChange = (text) => {
      const formatted = formatAmount(text);
      setAmount(formatted);
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>快速转账 | Quick Transfer</Text>

        {/* 电话号码输入 - 自动格式化 */}
        {/* Phone number input - auto-formatted */}
        <View style={styles.fieldContainer}>
          <Text style={styles.label}>
            收款人电话 | Recipient Phone
          </Text>
          <TextInput
            style={styles.input}
            value={phoneNumber}
            onChangeText={handlePhoneChange}
            placeholder="(123) 456-7890"
            keyboardType="phone-pad"  // 电话键盘 | Phone keyboard
            maxLength={14}  // 格式化后的最大长度 | Max length after formatting
          />
          <Text style={styles.hint}>
            自动格式化为(123) 456-7890 | Auto-formats to (123) 456-7890
          </Text>
        </View>

        {/* 金额输入 - 自动格式化 */}
        {/* Amount input - auto-formatted */}
        <View style={styles.fieldContainer}>
          <Text style={styles.label}>
            金额 | Amount
          </Text>
          <View style={styles.amountContainer}>
            <Text style={styles.currencySymbol}>$</Text>
            <TextInput
              style={styles.amountInput}
              value={amount}
              onChangeText={handleAmountChange}
              placeholder="0.00"
              keyboardType="decimal-pad"  // 带小数点的数字键盘 | Decimal number keyboard
              returnKeyType="done"
            />
          </View>
          <Text style={styles.hint}>
            最多两位小数 | Up to 2 decimal places
          </Text>
        </View>

        <TouchableOpacity
          style={[
            styles.submitButton,
            (!phoneNumber || !amount) && styles.submitButtonDisabled,
          ]}
          disabled={!phoneNumber || !amount}
        >
          <Text style={styles.submitButtonText}>
            发送 ${amount || '0.00'}
          </Text>
        </TouchableOpacity>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      backgroundColor: '#fff',
    },
    title: {
      fontSize: 28,
      fontWeight: 'bold',
      marginBottom: 30,
    },
    fieldContainer: {
      marginBottom: 25,
    },
    label: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 8,
      color: '#333',
    },
    input: {
      borderWidth: 2,
      borderColor: '#007AFF',
      padding: 15,
      borderRadius: 10,
      fontSize: 18,
      backgroundColor: '#f9f9f9',
    },
    hint: {
      fontSize: 12,
      color: '#666',
      marginTop: 5,
      fontStyle: 'italic',
    },
    amountContainer: {
      flexDirection: 'row',
      alignItems: 'center',
      borderWidth: 2,
      borderColor: '#34c759',
      borderRadius: 10,
      backgroundColor: '#f9f9f9',
      paddingLeft: 15,
    },
    currencySymbol: {
      fontSize: 24,
      fontWeight: 'bold',
      color: '#34c759',
      marginRight: 5,
    },
    amountInput: {
      flex: 1,
      padding: 15,
      fontSize: 24,
      fontWeight: '600',
    },
    submitButton: {
      backgroundColor: '#007AFF',
      padding: 18,
      borderRadius: 10,
      alignItems: 'center',
      marginTop: 20,
    },
    submitButtonDisabled: {
      backgroundColor: '#ccc',
    },
    submitButtonText: {
      color: '#fff',
      fontSize: 18,
      fontWeight: 'bold',
    },
  });

  export default OptimizedMobileForm;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 自动格式化电话号码的好处是什么？| What are the benefits of auto-formatting phone numbers?
    **答案 | Answer:** 提高可读性、减少输入错误、提供即时反馈 | Improves readability, reduces input errors, provides instant feedback
  - 为什么金额输入用decimal-pad而不是numeric？| Why use decimal-pad instead of numeric for amount input?
    **答案 | Answer:** decimal-pad提供小数点，适合货币输入 | decimal-pad provides decimal point, suitable for currency input

  **常见误区检查 | Common Misconception Checks:**
  - 自动格式化会影响表单提交的值吗？| Does auto-formatting affect the submitted form value?
    **答案 | Answer:** 取决于实现 | Depends on implementation - 可能需要在提交前清理格式（如移除括号和破折号）| Might need to clean format before submission (like removing parentheses and dashes)
  - maxLength会阻止所有超长输入吗？| Does maxLength prevent all over-length input?
    **答案 | Answer:** 对于键盘输入是的，但粘贴可能绕过，需要在onChangeText中验证 | Yes for keyboard input, but paste might bypass, need to validate in onChangeText

### 5. 实战：登录表单组件 | Practical: Login Form Component (30分钟 | 30 minutes)

- **完整登录表单实现 | Complete Login Form Implementation**

  **概念定义 | Concept Definition:**
  登录表单是移动应用中最常见的表单类型，综合了受控组件、验证、键盘管理、错误处理等多个概念。一个好的登录表单应该安全、易用、提供清晰的反馈。 | Login form is the most common form type in mobile apps, combining controlled components, validation, keyboard management, error handling and other concepts. A good login form should be secure, usable, and provide clear feedback.

  **核心特征 | Key Characteristics:**
  - 邮箱/用户名输入（email-address keyboard, no auto-capitalize）| Email/username input (email-address keyboard, no auto-capitalize)
  - 密码输入（secureTextEntry, 显示/隐藏切换）| Password input (secureTextEntry, show/hide toggle)
  - 输入验证和错误显示 | Input validation and error display
  - 提交状态管理（loading, disabled）| Submit state management (loading, disabled)
  - 键盘优化（returnKeyType, onSubmitEditing）| Keyboard optimization (returnKeyType, onSubmitEditing)
  - 辅助功能（忘记密码、注册链接）| Supporting features (forgot password, register link)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为什么密码字段需要"显示/隐藏"切换功能？| Why does the password field need a show/hide toggle?
     **答案 | Answer:** 让用户确认输入正确，提高可用性和减少错误 | Lets users verify correct input, improves usability and reduces errors
  2. 登录按钮应该在什么时候禁用？| When should the login button be disabled?
     **答案 | Answer:** 字段为空时、验证未通过时、正在提交时 | When fields are empty, validation fails, or while submitting
  3. 密码字段应该使用什么returnKeyType？| What returnKeyType should the password field use?
     **答案 | Answer:** 'done'或'go' - 表示完成输入，可触发登录 | 'done' or 'go' - indicates input completion, can trigger login
  4. 登录失败时应该如何处理？| How should login failure be handled?
     **答案 | Answer:** 显示错误消息、清除密码（可选）、保持邮箱、恢复按钮状态 | Show error message, clear password (optional), keep email, restore button state

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useRef } from 'react';
  import {
    View,
    TextInput,
    Text,
    TouchableOpacity,
    StyleSheet,
    KeyboardAvoidingView,
    Platform,
    ActivityIndicator,
    Alert,
  } from 'react-native';

  // 完整的登录表单组件 | Complete login form component
  function LoginForm() {
    // 表单状态 | Form state
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [showPassword, setShowPassword] = useState(false);
    const [errors, setErrors] = useState({});
    const [touched, setTouched] = useState({});
    const [isLoading, setIsLoading] = useState(false);

    // Refs for focus management
    const passwordInputRef = useRef(null);

    // 验证邮箱 | Validate email
    const validateEmail = (text) => {
      if (!text) return '邮箱不能为空 | Email cannot be empty';
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailRegex.test(text)) return '邮箱格式无效 | Invalid email format';
      return '';
    };

    // 验证密码 | Validate password
    const validatePassword = (text) => {
      if (!text) return '密码不能为空 | Password cannot be empty';
      if (text.length < 6) return '密码至少6个字符 | Password must be at least 6 characters';
      return '';
    };

    // 处理邮箱改变 | Handle email change
    const handleEmailChange = (text) => {
      setEmail(text);
      const error = validateEmail(text);
      setErrors(prev => ({ ...prev, email: error }));
    };

    // 处理密码改变 | Handle password change
    const handlePasswordChange = (text) => {
      setPassword(text);
      const error = validatePassword(text);
      setErrors(prev => ({ ...prev, password: error }));
    };

    // 处理登录 | Handle login
    const handleLogin = async () => {
      // 标记所有字段为touched | Mark all fields as touched
      setTouched({ email: true, password: true });

      // 验证所有字段 | Validate all fields
      const emailError = validateEmail(email);
      const passwordError = validatePassword(password);

      setErrors({
        email: emailError,
        password: passwordError,
      });

      // 如果有错误，不提交 | Don't submit if there are errors
      if (emailError || passwordError) {
        Alert.alert(
          '验证失败 | Validation Failed',
          '请修正表单错误 | Please fix form errors'
        );
        return;
      }

      // 开始登录 | Start login
      setIsLoading(true);

      try {
        // 模拟API调用 | Simulate API call
        await new Promise(resolve => setTimeout(resolve, 2000));

        // 模拟登录成功/失败 | Simulate login success/failure
        const isSuccess = Math.random() > 0.3; // 70% success rate

        if (isSuccess) {
          Alert.alert(
            '登录成功 | Login Successful',
            `欢迎回来！| Welcome back!\nEmail: ${email}`
          );
          // 这里可以导航到主屏幕 | Navigate to main screen here
        } else {
          throw new Error('Invalid credentials');
        }
      } catch (error) {
        Alert.alert(
          '登录失败 | Login Failed',
          '邮箱或密码错误 | Invalid email or password',
          [{ text: '确定 | OK' }]
        );
        // 可选：清除密码 | Optional: clear password
        setPassword('');
      } finally {
        setIsLoading(false);
      }
    };

    // 检查是否可以提交 | Check if can submit
    const canSubmit = email && password && !errors.email && !errors.password && !isLoading;

    return (
      <KeyboardAvoidingView
        style={styles.container}
        behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      >
        <View style={styles.formContainer}>
          {/* 标题 | Title */}
          <Text style={styles.title}>欢迎回来 | Welcome Back</Text>
          <Text style={styles.subtitle}>登录你的账户 | Sign in to your account</Text>

          {/* 邮箱输入 | Email input */}
          <View style={styles.fieldContainer}>
            <Text style={styles.label}>邮箱 | Email</Text>
            <TextInput
              style={[
                styles.input,
                touched.email && errors.email && styles.inputError,
                touched.email && !errors.email && email && styles.inputValid,
              ]}
              value={email}
              onChangeText={handleEmailChange}
              onBlur={() => setTouched(prev => ({ ...prev, email: true }))}
              placeholder="your@email.com"
              keyboardType="email-address"
              autoCapitalize="none"
              autoCorrect={false}
              returnKeyType="next"
              onSubmitEditing={() => passwordInputRef.current?.focus()}
              blurOnSubmit={false}
              editable={!isLoading}
            />
            {touched.email && errors.email && (
              <Text style={styles.errorText}>{errors.email}</Text>
            )}
          </View>

          {/* 密码输入 | Password input */}
          <View style={styles.fieldContainer}>
            <Text style={styles.label}>密码 | Password</Text>
            <View style={styles.passwordContainer}>
              <TextInput
                ref={passwordInputRef}
                style={[
                  styles.passwordInput,
                  touched.password && errors.password && styles.inputError,
                  touched.password && !errors.password && password && styles.inputValid,
                ]}
                value={password}
                onChangeText={handlePasswordChange}
                onBlur={() => setTouched(prev => ({ ...prev, password: true }))}
                placeholder="输入密码 | Enter password"
                secureTextEntry={!showPassword}
                autoCapitalize="none"
                returnKeyType="done"
                onSubmitEditing={handleLogin}
                editable={!isLoading}
              />
              {/* 显示/隐藏密码按钮 | Show/hide password button */}
              <TouchableOpacity
                style={styles.showPasswordButton}
                onPress={() => setShowPassword(!showPassword)}
                disabled={isLoading}
              >
                <Text style={styles.showPasswordText}>
                  {showPassword ? '隐藏 | Hide' : '显示 | Show'}
                </Text>
              </TouchableOpacity>
            </View>
            {touched.password && errors.password && (
              <Text style={styles.errorText}>{errors.password}</Text>
            )}
          </View>

          {/* 忘记密码链接 | Forgot password link */}
          <TouchableOpacity style={styles.forgotPassword}>
            <Text style={styles.forgotPasswordText}>
              忘记密码？| Forgot Password?
            </Text>
          </TouchableOpacity>

          {/* 登录按钮 | Login button */}
          <TouchableOpacity
            style={[
              styles.loginButton,
              !canSubmit && styles.loginButtonDisabled,
            ]}
            onPress={handleLogin}
            disabled={!canSubmit}
          >
            {isLoading ? (
              <ActivityIndicator color="#fff" />
            ) : (
              <Text style={styles.loginButtonText}>
                登录 | Sign In
              </Text>
            )}
          </TouchableOpacity>

          {/* 注册链接 | Register link */}
          <View style={styles.registerContainer}>
            <Text style={styles.registerText}>
              还没有账户？| Don't have an account?{' '}
            </Text>
            <TouchableOpacity>
              <Text style={styles.registerLink}>
                注册 | Sign Up
              </Text>
            </TouchableOpacity>
          </View>
        </View>
      </KeyboardAvoidingView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    formContainer: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
    },
    title: {
      fontSize: 32,
      fontWeight: 'bold',
      marginBottom: 10,
      textAlign: 'center',
      color: '#333',
    },
    subtitle: {
      fontSize: 16,
      color: '#666',
      textAlign: 'center',
      marginBottom: 40,
    },
    fieldContainer: {
      marginBottom: 20,
    },
    label: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 8,
      color: '#333',
    },
    input: {
      borderWidth: 2,
      borderColor: '#ddd',
      padding: 15,
      borderRadius: 10,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    inputError: {
      borderColor: '#ff3b30',
    },
    inputValid: {
      borderColor: '#34c759',
    },
    passwordContainer: {
      flexDirection: 'row',
      alignItems: 'center',
    },
    passwordInput: {
      flex: 1,
      borderWidth: 2,
      borderColor: '#ddd',
      padding: 15,
      borderRadius: 10,
      fontSize: 16,
      backgroundColor: '#fff',
    },
    showPasswordButton: {
      position: 'absolute',
      right: 15,
    },
    showPasswordText: {
      color: '#007AFF',
      fontSize: 14,
      fontWeight: '600',
    },
    errorText: {
      color: '#ff3b30',
      fontSize: 12,
      marginTop: 5,
    },
    forgotPassword: {
      alignSelf: 'flex-end',
      marginBottom: 20,
    },
    forgotPasswordText: {
      color: '#007AFF',
      fontSize: 14,
    },
    loginButton: {
      backgroundColor: '#007AFF',
      padding: 18,
      borderRadius: 10,
      alignItems: 'center',
      marginBottom: 20,
    },
    loginButtonDisabled: {
      backgroundColor: '#ccc',
    },
    loginButtonText: {
      color: '#fff',
      fontSize: 18,
      fontWeight: 'bold',
    },
    registerContainer: {
      flexDirection: 'row',
      justifyContent: 'center',
      alignItems: 'center',
    },
    registerText: {
      color: '#666',
      fontSize: 14,
    },
    registerLink: {
      color: '#007AFF',
      fontSize: 14,
      fontWeight: '600',
    },
  });

  export default LoginForm;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么密码输入框使用绝对定位放置"显示"按钮？| Why use absolute positioning for the "Show" button in password input?
    **答案 | Answer:** 使按钮出现在输入框内部右侧，不占用额外空间 | Makes button appear inside input on right side, without taking extra space
  - ActivityIndicator在登录按钮中的作用是什么？| What's the role of ActivityIndicator in the login button?
    **答案 | Answer:** 显示加载状态，让用户知道登录请求正在处理中 | Shows loading state, lets users know login request is being processed

  **常见误区检查 | Common Misconception Checks:**
  - 在客户端验证密码强度就足够了吗？| Is client-side password strength validation sufficient?
    **答案 | Answer:** 不够 | No - 服务器端也必须验证，客户端验证只是UX改进 | Server-side must also validate, client-side is only UX improvement
  - 登录成功后需要清除密码吗？| Need to clear password after successful login?
    **答案 | Answer:** 通常不需要 | Usually no - 因为会导航到其他页面，但登录失败时清除可以提高安全性 | Will navigate to other page, but clearing on failure improves security

### 6. 知识巩固与检查 | Knowledge Consolidation and Review (15分钟 | 15 minutes)

- **综合概念检查 | Comprehensive Concept Check:**

  1. 受控TextInput组件的三个必要组成部分是什么？| What are the three necessary components of a controlled TextInput?
     **答案 | Answer:** state变量、value属性绑定到state、onChangeText回调更新state | State variable, value prop bound to state, onChangeText callback updating state

  2. keyboardType属性如何改善移动端表单UX？| How does the keyboardType prop improve mobile form UX?
     **答案 | Answer:** 为不同输入类型显示优化的键盘布局，减少输入错误，提高输入效率 | Displays optimized keyboard layout for different input types, reduces input errors, improves input efficiency

  3. 什么时候应该显示表单验证错误？| When should form validation errors be displayed?
     **答案 | Answer:** 在字段失去焦点后（onBlur）或提交时，使用touched状态避免过早显示 | After field loses focus (onBlur) or on submission, use touched state to avoid showing too early

  4. 为什么需要KeyboardAvoidingView？| Why is KeyboardAvoidingView needed?
     **答案 | Answer:** 在移动端，键盘会遮挡输入框，KeyboardAvoidingView自动调整布局避免遮挡 | On mobile, keyboard blocks inputs, KeyboardAvoidingView automatically adjusts layout to avoid blocking

  5. 表单状态集中管理相比单独管理每个字段有什么优势？| What advantages does centralized form state management have over managing each field individually?
     **答案 | Answer:** 减少重复代码、更易扩展、便于统一验证和处理、更好的代码组织 | Reduces code duplication, easier to scale, convenient for unified validation and handling, better code organization

## 实践项目：完整的用户注册表单 | Practical Project: Complete User Registration Form

### 目标 | Objective
构建一个功能完整的用户注册表单应用，综合应用TextInput、表单验证、键盘管理、状态管理等本日所学概念。 | Build a fully functional user registration form app, comprehensively applying concepts learned today including TextInput, form validation, keyboard management, and state management.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 你能解释受控组件模式吗？| Can you explain the controlled component pattern?
   **答案 | Answer:** TextInput的value由state控制，用户输入通过onChangeText更新state，形成单向数据流 | TextInput's value is controlled by state, user input updates state through onChangeText, forming unidirectional data flow

2. 如何实现实时表单验证？| How to implement real-time form validation?
   **答案 | Answer:** 在onChangeText回调中执行验证逻辑，更新错误状态，配合touched状态控制错误显示 | Execute validation logic in onChangeText callback, update error state, use touched state to control error display

3. 为什么需要不同的keyboardType？| Why are different keyboardType needed?
   **答案 | Answer:** 为不同输入优化键盘布局，提高输入效率和准确性 | Optimize keyboard layout for different inputs, improve input efficiency and accuracy

### 步骤 | Steps

1. **创建项目结构 | Create project structure**
   - 创建RegistrationApp.js作为主组件 | Create RegistrationApp.js as main component
   - 规划需要的表单字段：用户名、邮箱、电话、密码、确认密码 | Plan required form fields: username, email, phone, password, confirm password

2. **实现状态管理 | Implement state management**
   - 使用单个state对象管理所有表单字段 | Use single state object to manage all form fields
   - 创建errors和touched状态对象 | Create errors and touched state objects
   - 实现通用的字段更新函数 | Implement generic field update function

3. **构建表单输入组件 | Build form input components**
   - 为每个字段选择合适的keyboardType | Choose appropriate keyboardType for each field
   - 实现密码显示/隐藏功能 | Implement password show/hide functionality
   - 添加电话号码自动格式化 | Add phone number auto-formatting

4. **添加验证逻辑 | Add validation logic**
   - 用户名：非空，至少3个字符 | Username: not empty, at least 3 characters
   - 邮箱：正则表达式验证格式 | Email: regex validation for format
   - 电话：10位数字 | Phone: 10 digits
   - 密码：至少6个字符，包含字母和数字 | Password: at least 6 characters, contains letters and numbers
   - 确认密码：与密码匹配 | Confirm password: matches password

5. **优化键盘交互 | Optimize keyboard interaction**
   - 设置合适的returnKeyType（next, done）| Set appropriate returnKeyType (next, done)
   - 实现字段间的自动聚焦 | Implement auto-focus between fields
   - 添加KeyboardAvoidingView | Add KeyboardAvoidingView
   - 实现点击外部关闭键盘 | Implement tap outside to dismiss keyboard

6. **实现提交处理 | Implement submission handling**
   - 验证所有字段 | Validate all fields
   - 显示加载状态 | Show loading state
   - 模拟API调用 | Simulate API call
   - 处理成功/失败情况 | Handle success/failure cases

7. **添加视觉反馈 | Add visual feedback**
   - 有效/无效输入的边框颜色 | Border color for valid/invalid input
   - 错误消息显示 | Error message display
   - 按钮禁用状态 | Button disabled state
   - 加载指示器 | Loading indicator

### 示例代码 | Example Code

```javascript
/**
 * 完整的用户注册表单应用 | Complete User Registration Form App
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - 受控TextInput组件 | Controlled TextInput components
 * - 表单状态集中管理 | Centralized form state management
 * - 实时验证和错误处理 | Real-time validation and error handling
 * - 键盘类型优化 | Keyboard type optimization
 * - 键盘行为管理 | Keyboard behavior management
 * - 移动端UX最佳实践 | Mobile UX best practices
 */

import React, { useState, useRef } from 'react';
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  StyleSheet,
  KeyboardAvoidingView,
  Platform,
  ScrollView,
  TouchableWithoutFeedback,
  Keyboard,
  ActivityIndicator,
  Alert,
} from 'react-native';

function RegistrationApp() {
  // 概念应用1: 集中状态管理 | Concept Application 1: Centralized state management
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    phone: '',
    password: '',
    confirmPassword: '',
  });

  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [showPassword, setShowPassword] = useState(false);
  const [showConfirmPassword, setShowConfirmPassword] = useState(false);
  const [isSubmitting, setIsSubmitting] = useState(false);

  // Refs for keyboard navigation
  const emailRef = useRef(null);
  const phoneRef = useRef(null);
  const passwordRef = useRef(null);
  const confirmPasswordRef = useRef(null);

  // 概念应用2: 输入格式化函数 | Concept Application 2: Input formatting functions
  const formatPhoneNumber = (text) => {
    const cleaned = text.replace(/\D/g, '').slice(0, 10);
    if (cleaned.length <= 3) return cleaned;
    if (cleaned.length <= 6) return `(${cleaned.slice(0, 3)}) ${cleaned.slice(3)}`;
    return `(${cleaned.slice(0, 3)}) ${cleaned.slice(3, 6)}-${cleaned.slice(6)}`;
  };

  // 概念应用3: 验证函数 | Concept Application 3: Validation functions
  const validators = {
    username: (value) => {
      if (!value) return '用户名不能为空 | Username cannot be empty';
      if (value.length < 3) return '用户名至少3个字符 | Username must be at least 3 characters';
      if (!/^[a-zA-Z0-9_]+$/.test(value)) return '用户名只能包含字母、数字和下划线 | Username can only contain letters, numbers, and underscores';
      return '';
    },
    email: (value) => {
      if (!value) return '邮箱不能为空 | Email cannot be empty';
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailRegex.test(value)) return '邮箱格式无效 | Invalid email format';
      return '';
    },
    phone: (value) => {
      const cleaned = value.replace(/\D/g, '');
      if (!cleaned) return '电话号码不能为空 | Phone number cannot be empty';
      if (cleaned.length !== 10) return '电话号码必须是10位数字 | Phone number must be 10 digits';
      return '';
    },
    password: (value) => {
      if (!value) return '密码不能为空 | Password cannot be empty';
      if (value.length < 6) return '密码至少6个字符 | Password must be at least 6 characters';
      if (!/(?=.*[a-zA-Z])(?=.*[0-9])/.test(value)) return '密码必须包含字母和数字 | Password must contain letters and numbers';
      return '';
    },
    confirmPassword: (value) => {
      if (!value) return '请确认密码 | Please confirm password';
      if (value !== formData.password) return '密码不匹配 | Passwords do not match';
      return '';
    },
  };

  // 概念应用4: 通用字段处理 | Concept Application 4: Generic field handling
  const handleFieldChange = (fieldName, value) => {
    let processedValue = value;

    // 特殊处理：电话号码格式化 | Special handling: phone number formatting
    if (fieldName === 'phone') {
      processedValue = formatPhoneNumber(value);
    }

    setFormData(prev => ({
      ...prev,
      [fieldName]: processedValue,
    }));

    // 实时验证 | Real-time validation
    const error = validators[fieldName](fieldName === 'confirmPassword' ? value : processedValue);
    setErrors(prev => ({
      ...prev,
      [fieldName]: error,
    }));

    // 如果是密码字段改变，重新验证确认密码 | If password changes, revalidate confirm password
    if (fieldName === 'password' && formData.confirmPassword) {
      const confirmError = validators.confirmPassword(formData.confirmPassword);
      setErrors(prev => ({
        ...prev,
        confirmPassword: confirmError,
      }));
    }
  };

  const handleFieldBlur = (fieldName) => {
    setTouched(prev => ({
      ...prev,
      [fieldName]: true,
    }));
  };

  // 概念应用5: 表单提交处理 | Concept Application 5: Form submission handling
  const handleSubmit = async () => {
    // 标记所有字段为touched | Mark all fields as touched
    const allTouched = {};
    Object.keys(formData).forEach(key => {
      allTouched[key] = true;
    });
    setTouched(allTouched);

    // 验证所有字段 | Validate all fields
    const newErrors = {};
    Object.keys(formData).forEach(fieldName => {
      const error = validators[fieldName](formData[fieldName]);
      if (error) newErrors[fieldName] = error;
    });

    setErrors(newErrors);

    // 如果有错误，不提交 | Don't submit if there are errors
    if (Object.values(newErrors).some(error => error)) {
      Alert.alert(
        '验证失败 | Validation Failed',
        '请修正表单中的错误 | Please fix errors in the form'
      );
      return;
    }

    // 提交表单 | Submit form
    Keyboard.dismiss();
    setIsSubmitting(true);

    try {
      // 模拟API调用 | Simulate API call
      await new Promise(resolve => setTimeout(resolve, 2500));

      Alert.alert(
        '注册成功！| Registration Successful!',
        `欢迎，${formData.username}！\n你的账户已创建。\n\nWelcome, ${formData.username}!\nYour account has been created.`,
        [
          {
            text: '确定 | OK',
            onPress: () => {
              // 重置表单 | Reset form
              setFormData({
                username: '',
                email: '',
                phone: '',
                password: '',
                confirmPassword: '',
              });
              setErrors({});
              setTouched({});
            },
          },
        ]
      );
    } catch (error) {
      Alert.alert(
        '注册失败 | Registration Failed',
        '发生错误，请重试 | An error occurred, please try again'
      );
    } finally {
      setIsSubmitting(false);
    }
  };

  // 概念应用6: 可复用的表单字段渲染 | Concept Application 6: Reusable form field rendering
  const renderField = (config) => {
    const {
      fieldName,
      label,
      placeholder,
      keyboardType = 'default',
      autoCapitalize = 'none',
      secureTextEntry = false,
      returnKeyType = 'next',
      onSubmitEditing,
      inputRef,
      showToggle = false,
      toggleState,
      onToggle,
    } = config;

    const hasError = touched[fieldName] && errors[fieldName];
    const isValid = touched[fieldName] && !errors[fieldName] && formData[fieldName];

    return (
      <View style={styles.fieldContainer} key={fieldName}>
        <Text style={styles.label}>{label}</Text>
        <View style={styles.inputWrapper}>
          <TextInput
            ref={inputRef}
            style={[
              styles.input,
              hasError && styles.inputError,
              isValid && styles.inputValid,
              showToggle && styles.inputWithToggle,
            ]}
            value={formData[fieldName]}
            onChangeText={(text) => handleFieldChange(fieldName, text)}
            onBlur={() => handleFieldBlur(fieldName)}
            placeholder={placeholder}
            keyboardType={keyboardType}
            autoCapitalize={autoCapitalize}
            autoCorrect={false}
            secureTextEntry={secureTextEntry && !toggleState}
            returnKeyType={returnKeyType}
            onSubmitEditing={onSubmitEditing}
            blurOnSubmit={returnKeyType === 'done'}
            editable={!isSubmitting}
          />
          {showToggle && (
            <TouchableOpacity
              style={styles.toggleButton}
              onPress={onToggle}
              disabled={isSubmitting}
            >
              <Text style={styles.toggleButtonText}>
                {toggleState ? '隐藏 | Hide' : '显示 | Show'}
              </Text>
            </TouchableOpacity>
          )}
        </View>
        {hasError && (
          <Text style={styles.errorText}>{errors[fieldName]}</Text>
        )}
        {isValid && (
          <Text style={styles.successText}>✓</Text>
        )}
      </View>
    );
  };

  const canSubmit = Object.keys(formData).every(key => formData[key]) &&
                    Object.keys(errors).every(key => !errors[key]) &&
                    !isSubmitting;

  return (
    <TouchableWithoutFeedback onPress={Keyboard.dismiss}>
      <KeyboardAvoidingView
        style={styles.container}
        behavior={Platform.OS === 'ios' ? 'padding' : undefined}
        keyboardVerticalOffset={Platform.OS === 'ios' ? 64 : 0}
      >
        <ScrollView
          style={styles.scrollView}
          contentContainerStyle={styles.scrollViewContent}
          keyboardShouldPersistTaps="handled"
        >
          <View style={styles.header}>
            <Text style={styles.title}>创建账户 | Create Account</Text>
            <Text style={styles.subtitle}>
              填写以下信息注册 | Fill in the information below to register
            </Text>
          </View>

          {/* 概念应用7: 字段配置驱动的表单渲染 | Concept Application 7: Configuration-driven form rendering */}
          {renderField({
            fieldName: 'username',
            label: '用户名 | Username',
            placeholder: '选择一个用户名 | Choose a username',
            returnKeyType: 'next',
            onSubmitEditing: () => emailRef.current?.focus(),
          })}

          {renderField({
            fieldName: 'email',
            label: '邮箱 | Email',
            placeholder: 'your@email.com',
            keyboardType: 'email-address',
            returnKeyType: 'next',
            onSubmitEditing: () => phoneRef.current?.focus(),
            inputRef: emailRef,
          })}

          {renderField({
            fieldName: 'phone',
            label: '电话号码 | Phone Number',
            placeholder: '(123) 456-7890',
            keyboardType: 'phone-pad',
            returnKeyType: 'next',
            onSubmitEditing: () => passwordRef.current?.focus(),
            inputRef: phoneRef,
          })}

          {renderField({
            fieldName: 'password',
            label: '密码 | Password',
            placeholder: '至少6个字符 | At least 6 characters',
            secureTextEntry: true,
            returnKeyType: 'next',
            onSubmitEditing: () => confirmPasswordRef.current?.focus(),
            inputRef: passwordRef,
            showToggle: true,
            toggleState: showPassword,
            onToggle: () => setShowPassword(!showPassword),
          })}

          {renderField({
            fieldName: 'confirmPassword',
            label: '确认密码 | Confirm Password',
            placeholder: '再次输入密码 | Re-enter password',
            secureTextEntry: true,
            returnKeyType: 'done',
            onSubmitEditing: handleSubmit,
            inputRef: confirmPasswordRef,
            showToggle: true,
            toggleState: showConfirmPassword,
            onToggle: () => setShowConfirmPassword(!showConfirmPassword),
          })}

          <TouchableOpacity
            style={[
              styles.submitButton,
              !canSubmit && styles.submitButtonDisabled,
            ]}
            onPress={handleSubmit}
            disabled={!canSubmit}
          >
            {isSubmitting ? (
              <ActivityIndicator color="#fff" />
            ) : (
              <Text style={styles.submitButtonText}>
                注册 | Register
              </Text>
            )}
          </TouchableOpacity>

          <View style={styles.loginContainer}>
            <Text style={styles.loginText}>
              已有账户？| Already have an account?{' '}
            </Text>
            <TouchableOpacity>
              <Text style={styles.loginLink}>
                登录 | Sign In
              </Text>
            </TouchableOpacity>
          </View>
        </ScrollView>
      </KeyboardAvoidingView>
    </TouchableWithoutFeedback>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f8f9fa',
  },
  scrollView: {
    flex: 1,
  },
  scrollViewContent: {
    padding: 20,
  },
  header: {
    marginBottom: 30,
    marginTop: 20,
  },
  title: {
    fontSize: 32,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
  },
  subtitle: {
    fontSize: 16,
    color: '#666',
  },
  fieldContainer: {
    marginBottom: 20,
  },
  label: {
    fontSize: 14,
    fontWeight: '600',
    color: '#333',
    marginBottom: 8,
  },
  inputWrapper: {
    position: 'relative',
  },
  input: {
    borderWidth: 2,
    borderColor: '#ddd',
    padding: 15,
    borderRadius: 10,
    fontSize: 16,
    backgroundColor: '#fff',
  },
  inputWithToggle: {
    paddingRight: 70,
  },
  inputError: {
    borderColor: '#ff3b30',
    backgroundColor: '#fff5f5',
  },
  inputValid: {
    borderColor: '#34c759',
    backgroundColor: '#f0fdf4',
  },
  toggleButton: {
    position: 'absolute',
    right: 15,
    top: 15,
  },
  toggleButtonText: {
    color: '#007AFF',
    fontSize: 14,
    fontWeight: '600',
  },
  errorText: {
    color: '#ff3b30',
    fontSize: 12,
    marginTop: 5,
  },
  successText: {
    color: '#34c759',
    fontSize: 16,
    position: 'absolute',
    right: 15,
    top: 43,
  },
  submitButton: {
    backgroundColor: '#007AFF',
    padding: 18,
    borderRadius: 10,
    alignItems: 'center',
    marginTop: 10,
    marginBottom: 20,
  },
  submitButtonDisabled: {
    backgroundColor: '#ccc',
  },
  submitButtonText: {
    color: '#fff',
    fontSize: 18,
    fontWeight: 'bold',
  },
  loginContainer: {
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
    marginBottom: 30,
  },
  loginText: {
    color: '#666',
    fontSize: 14,
  },
  loginLink: {
    color: '#007AFF',
    fontSize: 14,
    fontWeight: '600',
  },
});

export default RegistrationApp;
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确应用了受控组件模式？| Does the project correctly apply the controlled component pattern?
   - 检查：所有TextInput的value都绑定到state，所有输入都通过onChangeText更新 | Check: All TextInput values bound to state, all inputs update through onChangeText

2. 表单验证是否全面且提供清晰反馈？| Is form validation comprehensive with clear feedback?
   - 检查：每个字段都有验证规则，错误信息清晰，视觉反馈明显 | Check: Each field has validation rules, error messages are clear, visual feedback is obvious

3. 键盘交互是否流畅和优化？| Is keyboard interaction smooth and optimized?
   - 检查：正确的keyboardType，returnKeyType设置，字段间导航流畅 | Check: Correct keyboardType, returnKeyType settings, smooth field navigation

4. 代码是否展现了良好的组织和可维护性？| Does the code show good organization and maintainability?
   - 检查：使用了集中状态管理，可复用的renderField函数，清晰的验证逻辑 | Check: Uses centralized state management, reusable renderField function, clear validation logic

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **受控组件深化理解练习 | Controlled Component Deep Understanding Exercise**
   - **练习描述 | Exercise Description:** 创建一个搜索框组件，实时显示过滤结果。理解受控组件如何实现实时搜索。 | Create a search box component that displays filtered results in real-time. Understand how controlled components enable real-time search.
   - **概念检查 | Concept Check:** 为什么实时搜索必须使用受控组件？| Why must real-time search use controlled components? (答案：需要访问每次输入的值来过滤结果 | Answer: Need to access each input value to filter results)
   - **学习目标 | Learning Objective:** 加深对受控组件单向数据流的理解 | Deepen understanding of controlled component unidirectional data flow

2. **键盘类型应用练习 | Keyboard Type Application Exercise**
   - **练习描述 | Exercise Description:** 创建一个订单表单，包含产品数量（numeric）、折扣码（default）、配送地址（default）等字段，选择最佳的keyboardType。 | Create an order form with fields for product quantity (numeric), discount code (default), shipping address (default), etc., choosing the best keyboardType for each.
   - **概念检查 | Concept Check:** 不同的keyboardType如何影响用户输入速度和准确性？| How do different keyboardTypes affect user input speed and accuracy?
   - **学习目标 | Learning Objective:** 提高选择合适键盘类型的能力 | Improve ability to choose appropriate keyboard types

3. **验证策略整合练习 | Validation Strategy Integration Exercise**
   - **练习描述 | Exercise Description:** 实现三种验证时机：输入时（onChangeText）、失焦时（onBlur）、提交时。比较它们的优缺点。 | Implement three validation timings: on input (onChangeText), on blur (onBlur), on submit. Compare their pros and cons.
   - **概念检查 | Concept Check:** 什么时候应该在onChangeText中验证，什么时候在onBlur中验证？| When should validation be in onChangeText vs onBlur?
   - **学习目标 | Learning Objective:** 发展选择合适验证策略的判断力 | Develop judgment for choosing appropriate validation strategies

4. **复杂表单状态管理练习 | Complex Form State Management Exercise**
   - **练习描述 | Exercise Description:** 创建一个多步骤表单（个人信息→地址信息→支付信息），管理跨步骤的状态和验证。 | Create a multi-step form (personal info → address info → payment info), managing state and validation across steps.
   - **概念检查 | Concept Check:** 如何在多步骤间保持和传递表单状态？| How to maintain and pass form state between multiple steps?
   - **学习目标 | Learning Objective:** 培养管理复杂表单状态的能力 | Develop ability to manage complex form state

5. **自定义TextInput组件练习 | Custom TextInput Component Exercise**
   - **练习描述 | Exercise Description:** 创建一个可复用的FormInput组件，封装TextInput、label、错误显示、验证逻辑。 | Create a reusable FormInput component encapsulating TextInput, label, error display, and validation logic.
   - **概念检查 | Concept Check:** 如何设计组件API使其既灵活又易用？| How to design component API to be both flexible and easy to use?
   - **学习目标 | Learning Objective:** 提高组件抽象和复用能力 | Improve component abstraction and reuse capability

6. **移动端表单UX优化练习 | Mobile Form UX Optimization Exercise**
   - **练习描述 | Exercise Description:** 优化登录表单的UX：添加"记住我"功能、社交登录按钮、密码强度指示器。 | Optimize login form UX: add "Remember Me" feature, social login buttons, password strength indicator.
   - **概念检查 | Concept Check:** 这些UX改进如何提高表单的可用性？| How do these UX improvements enhance form usability?
   - **学习目标 | Learning Objective:** 发展移动端UX设计思维 | Develop mobile UX design thinking

7. **表单性能优化练习 | Form Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 在大型表单（20+字段）中，使用React.memo和useCallback优化渲染性能。 | In a large form (20+ fields), use React.memo and useCallback to optimize rendering performance.
   - **概念检查 | Concept Check:** 为什么大型表单可能有性能问题，如何解决？| Why might large forms have performance issues, how to solve?
   - **学习目标 | Learning Objective:** 提高React性能优化能力 | Improve React performance optimization skills

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [TextInput - React Native](https://reactnative.dev/docs/textinput) - TextInput组件完整API文档 | Complete TextInput component API documentation
- [Handling Text Input - React Native](https://reactnative.dev/docs/handling-text-input) - 处理文本输入的官方指南 | Official guide for handling text input
- [KeyboardAvoidingView - React Native](https://reactnative.dev/docs/keyboardavoidingview) - 键盘避让视图文档 | Keyboard avoiding view documentation

### 表单库 | Form Libraries
- [Formik for React Native](https://formik.org/docs/guides/react-native) - 流行的表单状态管理库 | Popular form state management library
- [React Hook Form](https://react-hook-form.com/) - 高性能表单库 | High-performance form library
- [Yup](https://github.com/jquense/yup) - 验证schema库 | Validation schema library

### 最佳实践文章 | Best Practices Articles
- [Mobile Form Design Best Practices](https://www.smashingmagazine.com/2018/08/best-practices-for-mobile-form-design/) - 移动端表单设计最佳实践 | Mobile form design best practices
- [Form Validation UX in React Native](https://blog.logrocket.com/form-validation-in-react-native/) - React Native表单验证UX | Form validation UX in React Native

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解受控TextInput组件的工作原理 | Understand how controlled TextInput components work
- [ ] 能够选择和使用合适的keyboardType | Can choose and use appropriate keyboardType
- [ ] 掌握表单验证和错误处理技术 | Master form validation and error handling techniques
- [ ] 理解touched状态在验证中的作用 | Understand the role of touched state in validation
- [ ] 能够实现表单的集中状态管理 | Can implement centralized form state management
- [ ] 掌握KeyboardAvoidingView和键盘交互优化 | Master KeyboardAvoidingView and keyboard interaction optimization
- [ ] 能够实现密码显示/隐藏功能 | Can implement password show/hide functionality
- [ ] 理解returnKeyType和字段间导航 | Understand returnKeyType and field navigation
- [ ] 能够实现输入的自动格式化（如电话号码）| Can implement automatic input formatting (like phone numbers)
- [ ] 完成完整的注册表单实践项目 | Complete the full registration form practical project
- [ ] 所有CCQs能够正确回答 | Can correctly answer all CCQs
- [ ] 代码示例都已理解并运行 | All code examples understood and executed

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释受控组件、表单验证、键盘优化等核心概念。尝试不看代码实现一个简单的登录表单。
Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain core concepts like controlled components, form validation, and keyboard optimization to others. Try implementing a simple login form without looking at the code.
