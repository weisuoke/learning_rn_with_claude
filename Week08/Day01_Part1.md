# React Native入门 - 第08周第1天：TextInput深入探索与键盘类型 | React Native Introduction - Week 08 Day 1: TextInput Deep Dive & Keyboard Types

## 学习目标 | Learning Objectives
- 理解TextInput组件的核心概念和在移动端的重要性 | Understand the core concepts of TextInput component and its importance in mobile development
- 掌握不同键盘类型(keyboardType)的使用场景和最佳实践 | Master the usage scenarios and best practices of different keyboard types
- 理解受控组件(Controlled Components)在React Native表单中的应用 | Understand the application of controlled components in React Native forms
- 掌握文本输入的自动化配置(autoCapitalize, autoCorrect, autoComplete) | Master automated text input configurations (autoCapitalize, autoCorrect, autoComplete)
- 学会为TextInput应用样式以提升用户体验 | Learn to apply styles to TextInput for better user experience
- 理解onChangeText与onChange的区别及其使用场景 | Understand the difference between onChangeText and onChange and their usage scenarios

## 详细内容 | Detailed Content

### 1. TextInput组件基础 | TextInput Component Fundamentals (1.5小时 | 1.5 hours)

- **TextInput核心概念 | Core TextInput Concept**

  **概念定义 | Concept Definition:**
  TextInput是React Native中用于接收用户文本输入的核心组件。与Web开发中的`<input>`不同，TextInput必须处理移动设备特有的虚拟键盘、触摸交互和平台差异。它是构建移动表单的基石，理解其工作原理对创建优质用户体验至关重要。

  TextInput is the core component in React Native for receiving text input from users. Unlike `<input>` in web development, TextInput must handle mobile-specific virtual keyboards, touch interactions, and platform differences. It's the foundation of mobile forms, and understanding how it works is crucial for creating excellent user experiences.

  **核心特征 | Key Characteristics:**
  - **平台自适应 | Platform Adaptive**: 在iOS和Android上自动渲染为原生输入控件，提供平台特定的体验 | Automatically renders as native input controls on iOS and Android, providing platform-specific experiences
  - **虚拟键盘触发 | Virtual Keyboard Trigger**: 聚焦时自动显示虚拟键盘，可通过props配置键盘类型 | Automatically displays virtual keyboard on focus, keyboard type configurable via props
  - **受控组件模式 | Controlled Component Pattern**: 必须使用`value`和`onChangeText`配合实现状态管理 | Must use `value` and `onChangeText` together for state management
  - **丰富的配置选项 | Rich Configuration Options**: 提供30+个props来精确控制输入行为、键盘类型、自动化功能等 | Provides 30+ props to precisely control input behavior, keyboard types, automation features, etc.

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. TextInput在iOS和Android上会渲染为相同的HTML元素吗？| Does TextInput render as the same HTML element on iOS and Android?
     **答案 | Answer:** 否 | No - TextInput渲染为各平台的原生输入控件(iOS的UITextField/UITextView，Android的EditText) | TextInput renders as native input controls for each platform (UITextField/UITextView on iOS, EditText on Android)

  2. 当用户点击TextInput时会发生什么？| What happens when a user taps on a TextInput?
     **答案 | Answer:** 组件获得焦点并自动显示虚拟键盘 | The component gains focus and automatically displays the virtual keyboard

  3. TextInput可以在没有`value`和`onChangeText`的情况下使用吗？| Can TextInput be used without `value` and `onChangeText`?
     **答案 | Answer:** 可以，但这是非受控组件模式，不推荐在生产环境使用 | Yes, but this is uncontrolled component pattern, not recommended for production use

  4. Web开发中的`<input>`和React Native的TextInput最大的区别是什么？| What's the biggest difference between web `<input>` and React Native TextInput?
     **答案 | Answer:** TextInput必须处理虚拟键盘交互和移动设备触摸行为，而web input主要处理鼠标和物理键盘 | TextInput must handle virtual keyboard interactions and mobile touch behaviors, while web input primarily handles mouse and physical keyboard

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 基础TextInput示例 | Basic TextInput Example
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet } from 'react-native';

  export default function BasicTextInputExample() {
    // 使用state管理输入值(受控组件模式) | Use state to manage input value (controlled component pattern)
    const [text, setText] = useState('');

    return (
      <View style={styles.container}>
        <Text style={styles.label}>输入您的姓名 | Enter Your Name:</Text>

        {/* 受控TextInput组件 | Controlled TextInput Component */}
        <TextInput
          style={styles.input}
          value={text}                    // 绑定state值 | Bind to state value
          onChangeText={setText}          // 文本改变时更新state | Update state on text change
          placeholder="请输入... | Please enter..."  // 占位符提示 | Placeholder hint
        />

        {/* 实时显示输入内容 | Display input content in real-time */}
        <Text style={styles.output}>
          当前输入 | Current Input: {text}
        </Text>
        <Text style={styles.info}>
          字符数 | Character Count: {text.length}
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      backgroundColor: '#fff',
    },
    label: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 8,
      color: '#333',
    },
    input: {
      borderWidth: 1,           // 边框宽度 | Border width
      borderColor: '#ddd',      // 边框颜色 | Border color
      borderRadius: 8,          // 圆角 | Border radius
      padding: 12,              // 内边距 | Padding
      fontSize: 16,             // 字体大小 | Font size
      backgroundColor: '#f9f9f9', // 背景色 | Background color
    },
    output: {
      marginTop: 16,
      fontSize: 14,
      color: '#666',
    },
    info: {
      marginTop: 4,
      fontSize: 12,
      color: '#999',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中如果移除`value={text}`会发生什么？| What happens if we remove `value={text}` from this code?
    **答案 | Answer:** 组件变成非受控组件，React state不再控制输入值，输入仍然可见但state不会更新 | Component becomes uncontrolled, React state no longer controls the input value, input is still visible but state won't update

  - 如果将`onChangeText={setText}`改为`onChangeText={(newText) => setText(newText.toUpperCase())}`会如何？| What if we change `onChangeText={setText}` to `onChangeText={(newText) => setText(newText.toUpperCase())}`?
    **答案 | Answer:** 所有输入会自动转换为大写字母，这是受控组件的强大之处——可以在更新state前转换数据 | All input will be automatically converted to uppercase, this is the power of controlled components—data can be transformed before updating state

  **常见误区检查 | Common Misconception Checks:**
  - TextInput的`value`属性是可选的吗？| Is the `value` prop of TextInput optional?
    **答案 | Answer:** 技术上是可选的，但在生产应用中应始终使用受控组件模式（value + onChangeText），以便更好地管理和验证表单数据 | Technically optional, but in production apps should always use controlled component pattern (value + onChangeText) for better form data management and validation

  - 在移动端，用户输入的文本会立即显示在TextInput中吗？| On mobile, will user-typed text immediately display in TextInput?
    **答案 | Answer:** 在受控组件模式下，只有当onChangeText调用setText更新state后，文本才会通过value prop显示。这个循环是即时的但可能因性能问题延迟 | In controlled component mode, text only displays via the value prop after onChangeText calls setText to update state. This cycle is instant but may lag due to performance issues

### 2. 键盘类型深入理解 | Keyboard Type Deep Dive (1小时 | 1 hour)

- **keyboardType属性详解 | keyboardType Prop Explanation**

  **概念定义 | Concept Definition:**
  `keyboardType`是TextInput最重要的props之一，它决定了当用户聚焦输入框时显示的虚拟键盘类型。不同的键盘类型针对不同的输入场景优化，正确选择键盘类型能显著提升用户体验和输入效率。

  `keyboardType` is one of the most important props of TextInput, determining the type of virtual keyboard displayed when users focus on the input field. Different keyboard types are optimized for different input scenarios, and choosing the correct keyboard type can significantly improve user experience and input efficiency.

  **核心特征 | Key Characteristics:**
  - **场景适配性 | Scenario Adaptability**: 每种键盘类型针对特定输入场景设计（数字、邮箱、电话等） | Each keyboard type is designed for specific input scenarios (numbers, email, phone, etc.)
  - **平台差异性 | Platform Differences**: 某些键盘类型在iOS和Android上显示效果不同 | Some keyboard types display differently on iOS and Android
  - **用户体验优化 | UX Optimization**: 减少用户切换键盘的操作，提供最相关的按键布局 | Reduces user keyboard switching operations, provides most relevant key layout
  - **输入约束 | Input Constraints**: 部分键盘类型会限制可输入字符（如numeric只允许数字） | Some keyboard types restrict input characters (e.g., numeric only allows numbers)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. keyboardType='numeric'会阻止用户输入字母吗？| Does keyboardType='numeric' prevent users from entering letters?
     **答案 | Answer:** 不完全是，它只是显示数字键盘，但在某些平台上用户仍可能通过其他方式输入非数字字符。需要额外的验证逻辑来完全限制输入。| Not entirely, it only displays numeric keyboard, but on some platforms users may still enter non-numeric characters through other means. Additional validation logic is needed to fully restrict input.

  2. 为邮箱输入使用'email-address'键盘类型的好处是什么？| What's the benefit of using 'email-address' keyboard type for email input?
     **答案 | Answer:** 键盘会显示常用的邮箱符号（@、.），并且部分设备会提供邮箱自动完成建议，减少输入错误和时间 | Keyboard displays common email symbols (@, .), and some devices provide email autocomplete suggestions, reducing input errors and time

  3. 所有keyboardType值在iOS和Android上都表现一致吗？| Do all keyboardType values behave identically on iOS and Android?
     **答案 | Answer:** 否，某些类型如'phone-pad'在两个平台上显示不同的键盘布局，开发时需要在两个平台上测试 | No, some types like 'phone-pad' display different keyboard layouts on the two platforms, testing on both platforms is required during development

  4. 什么时候应该使用'decimal-pad'而不是'numeric'？| When should you use 'decimal-pad' instead of 'numeric'?
     **答案 | Answer:** 当需要输入小数（如价格、体重）时使用decimal-pad，因为它包含小数点键 | Use decimal-pad when decimal input is needed (like price, weight), as it includes a decimal point key

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 不同键盘类型对比示例 | Different Keyboard Types Comparison Example
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet, ScrollView } from 'react-native';

  export default function KeyboardTypeDemo() {
    const [inputs, setInputs] = useState({
      default: '',
      numeric: '',
      email: '',
      phone: '',
      decimal: '',
    });

    // 更新特定输入字段的辅助函数 | Helper function to update specific input field
    const updateInput = (key, value) => {
      setInputs(prev => ({ ...prev, [key]: value }));
    };

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.title}>键盘类型演示 | Keyboard Type Demo</Text>

        {/* Default Keyboard - 默认键盘（完整字符集）| Default keyboard (full character set) */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>默认键盘 | Default Keyboard</Text>
          <TextInput
            style={styles.input}
            value={inputs.default}
            onChangeText={(text) => updateInput('default', text)}
            placeholder="姓名 | Name"
            keyboardType="default"  // 默认键盘，包含所有字符 | Default keyboard with all characters
          />
          <Text style={styles.hint}>
            用于：姓名、地址等一般文本 | Use for: names, addresses, general text
          </Text>
        </View>

        {/* Numeric Keyboard - 数字键盘（仅数字）| Numeric keyboard (numbers only) */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>数字键盘 | Numeric Keyboard</Text>
          <TextInput
            style={styles.input}
            value={inputs.numeric}
            onChangeText={(text) => updateInput('numeric', text)}
            placeholder="年龄 | Age"
            keyboardType="numeric"  // 仅显示数字键盘 | Display numeric keyboard only
          />
          <Text style={styles.hint}>
            用于：年龄、数量、楼层号 | Use for: age, quantity, floor number
          </Text>
        </View>

        {/* Email Keyboard - 邮箱键盘（含@和.）| Email keyboard (includes @ and .) */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>邮箱键盘 | Email Keyboard</Text>
          <TextInput
            style={styles.input}
            value={inputs.email}
            onChangeText={(text) => updateInput('email', text)}
            placeholder="example@email.com"
            keyboardType="email-address"  // 邮箱专用键盘 | Email-specific keyboard
            autoCapitalize="none"          // 禁用自动大写（邮箱不需要）| Disable auto-capitalize (emails don't need it)
            autoCorrect={false}            // 禁用自动更正（邮箱是精确输入）| Disable autocorrect (emails require exact input)
          />
          <Text style={styles.hint}>
            用于：邮箱地址输入 | Use for: email address input
          </Text>
        </View>

        {/* Phone Keyboard - 电话键盘 | Phone keyboard */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>电话键盘 | Phone Keyboard</Text>
          <TextInput
            style={styles.input}
            value={inputs.phone}
            onChangeText={(text) => updateInput('phone', text)}
            placeholder="+1 (555) 123-4567"
            keyboardType="phone-pad"  // 电话号码专用键盘 | Phone number-specific keyboard
          />
          <Text style={styles.hint}>
            用于：电话号码（含+、*、#等符号）| Use for: phone numbers (includes +, *, # symbols)
          </Text>
        </View>

        {/* Decimal Keyboard - 小数键盘（数字+小数点）| Decimal keyboard (numbers + decimal point) */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>小数键盘 | Decimal Keyboard</Text>
          <TextInput
            style={styles.input}
            value={inputs.decimal}
            onChangeText={(text) => updateInput('decimal', text)}
            placeholder="19.99"
            keyboardType="decimal-pad"  // 带小数点的数字键盘 | Numeric keyboard with decimal point
          />
          <Text style={styles.hint}>
            用于：价格、体重、距离等小数值 | Use for: price, weight, distance (decimal values)
          </Text>
        </View>

        {/* 显示所有输入值 | Display all input values */}
        <View style={styles.outputSection}>
          <Text style={styles.outputTitle}>当前输入值 | Current Input Values:</Text>
          <Text style={styles.outputText}>默认 | Default: {inputs.default || '(empty)'}</Text>
          <Text style={styles.outputText}>数字 | Numeric: {inputs.numeric || '(empty)'}</Text>
          <Text style={styles.outputText}>邮箱 | Email: {inputs.email || '(empty)'}</Text>
          <Text style={styles.outputText}>电话 | Phone: {inputs.phone || '(empty)'}</Text>
          <Text style={styles.outputText}>小数 | Decimal: {inputs.decimal || '(empty)'}</Text>
        </View>
      </ScrollView>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
      padding: 16,
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      color: '#333',
      textAlign: 'center',
    },
    inputGroup: {
      marginBottom: 24,
      backgroundColor: '#fff',
      padding: 16,
      borderRadius: 12,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    label: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 8,
      color: '#333',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      borderRadius: 8,
      padding: 12,
      fontSize: 16,
      backgroundColor: '#fafafa',
    },
    hint: {
      marginTop: 6,
      fontSize: 12,
      color: '#666',
      fontStyle: 'italic',
    },
    outputSection: {
      marginTop: 20,
      padding: 16,
      backgroundColor: '#e8f4f8',
      borderRadius: 12,
      borderWidth: 1,
      borderColor: '#b3d9e8',
    },
    outputTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 12,
      color: '#0066cc',
    },
    outputText: {
      fontSize: 14,
      color: '#333',
      marginBottom: 6,
      fontFamily: 'monospace',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果用户需要输入邮箱但使用了`keyboardType="default"`会怎样？| What happens if user needs to enter email but `keyboardType="default"` is used?
    **答案 | Answer:** 用户仍可输入邮箱，但需要手动切换到符号键盘找@和.，体验较差且容易出错 | User can still enter email, but must manually switch to symbol keyboard to find @ and ., providing poor experience and prone to errors

  - 在numeric键盘上，用户可以输入负数吗？| Can users enter negative numbers on numeric keyboard?
    **答案 | Answer:** 取决于平台，某些平台的numeric键盘不包含负号，需要使用number-pad或自定义验证逻辑 | Depends on platform, some platforms' numeric keyboards don't include minus sign, may need to use number-pad or custom validation logic

  **常见误区检查 | Common Misconception Checks:**
  - keyboardType会自动验证输入格式吗？| Does keyboardType automatically validate input format?
    **答案 | Answer:** 否，keyboardType只改变显示的键盘类型，不进行任何验证。你仍需要在onChangeText中添加验证逻辑来确保数据正确性。| No, keyboardType only changes the displayed keyboard type, doesn't perform any validation. You still need to add validation logic in onChangeText to ensure data correctness.

  - 使用email-address键盘类型就能保证用户输入有效的邮箱地址吗？| Does using email-address keyboard type guarantee users enter valid email addresses?
    **答案 | Answer:** 不能，它只是提供便利的键盘布局，无法阻止用户输入无效格式。必须使用正则表达式或验证库来验证邮箱格式。| No, it only provides a convenient keyboard layout, cannot prevent users from entering invalid formats. Must use regex or validation libraries to validate email format.

- **键盘类型选择最佳实践 | Keyboard Type Selection Best Practices**

  **概念定义 | Concept Definition:**
  选择正确的键盘类型不仅是技术决策，更是用户体验设计的重要部分。最佳实践包括根据数据类型、用户期望和平台特性来选择最合适的键盘类型，并结合其他TextInput属性来优化输入体验。

  Choosing the correct keyboard type is not only a technical decision but also an important part of user experience design. Best practices include selecting the most appropriate keyboard type based on data type, user expectations, and platform characteristics, combined with other TextInput properties to optimize input experience.

  **核心最佳实践清单 | Core Best Practices Checklist:**
  - **匹配数据类型 | Match Data Type**: 输入数字时用numeric/decimal，输入邮箱用email-address | Use numeric/decimal for number input, email-address for email
  - **测试跨平台 | Test Cross-Platform**: 在iOS和Android上都测试键盘行为 | Test keyboard behavior on both iOS and Android
  - **结合其他属性 | Combine with Other Props**: 配合autoCapitalize、autoCorrect等优化体验 | Combine with autoCapitalize, autoCorrect to optimize experience
  - **提供视觉提示 | Provide Visual Hints**: 使用placeholder和label明确输入期望 | Use placeholder and label to clarify input expectations

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 为信用卡号输入应该使用哪种键盘类型？| Which keyboard type should be used for credit card number input?
     **答案 | Answer:** 使用'number-pad'（iOS）或'numeric'（Android），因为信用卡号是纯数字且通常较长 | Use 'number-pad' (iOS) or 'numeric' (Android), as credit card numbers are pure digits and typically long

  2. 如果输入字段接受多种格式（如可以是电话或邮箱），应该如何选择键盘类型？| If an input field accepts multiple formats (like phone or email), how should keyboard type be chosen?
     **答案 | Answer:** 使用'default'键盘并添加格式检测逻辑，或者提供选项让用户选择输入类型 | Use 'default' keyboard and add format detection logic, or provide options for users to choose input type

  3. 在两个平台上测试键盘类型为什么重要？| Why is testing keyboard types on both platforms important?
     **答案 | Answer:** 因为iOS和Android的某些键盘类型显示不同的布局和功能，可能影响用户输入体验 | Because some keyboard types on iOS and Android display different layouts and features, which may affect user input experience

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 键盘类型最佳实践综合示例 | Keyboard Type Best Practices Comprehensive Example
  import React, { useState } from 'react';
  import { View, TextInput, Text, StyleSheet, Platform } from 'react-native';

  export default function KeyboardBestPractices() {
    const [formData, setFormData] = useState({
      fullName: '',
      email: '',
      phone: '',
      age: '',
      income: '',
      zipCode: '',
    });

    const updateField = (field, value) => {
      setFormData(prev => ({ ...prev, [field]: value }));
    };

    return (
      <View style={styles.container}>
        <Text style={styles.header}>最佳实践表单 | Best Practices Form</Text>

        {/* 全名输入 - 默认键盘+自动大写 | Full Name - Default keyboard + auto-capitalize */}
        <View style={styles.field}>
          <Text style={styles.label}>全名 | Full Name *</Text>
          <TextInput
            style={styles.input}
            value={formData.fullName}
            onChangeText={(text) => updateField('fullName', text)}
            placeholder="张伟 | Zhang Wei"
            keyboardType="default"       // 默认键盘适合姓名 | Default keyboard suits names
            autoCapitalize="words"       // 每个单词首字母大写 | Capitalize first letter of each word
            autoCorrect={false}          // 姓名是专有名词，禁用自动更正 | Names are proper nouns, disable autocorrect
            textContentType="name"       // iOS自动填充提示 | iOS autofill hint
            autoComplete="name"          // Android自动填充 | Android autofill
          />
          <Text style={styles.helpText}>
            ✓ 使用words自动大写提升专业性 | Using words auto-capitalize for professionalism
          </Text>
        </View>

        {/* 邮箱输入 - 邮箱键盘+禁用大写和更正 | Email - Email keyboard + disable caps and correction */}
        <View style={styles.field}>
          <Text style={styles.label}>邮箱地址 | Email Address *</Text>
          <TextInput
            style={styles.input}
            value={formData.email}
            onChangeText={(text) => updateField('email', text)}
            placeholder="your@email.com"
            keyboardType="email-address"  // 显示@和.键 | Display @ and . keys
            autoCapitalize="none"         // 邮箱全小写 | Emails are all lowercase
            autoCorrect={false}           // 邮箱是精确格式 | Emails require exact format
            textContentType="emailAddress"
            autoComplete="email"
          />
          <Text style={styles.helpText}>
            ✓ email-address键盘 + 禁用大写和更正 | email-address keyboard + disabled caps and correction
          </Text>
        </View>

        {/* 电话输入 - 电话键盘 | Phone - Phone keyboard */}
        <View style={styles.field}>
          <Text style={styles.label}>手机号 | Phone Number</Text>
          <TextInput
            style={styles.input}
            value={formData.phone}
            onChangeText={(text) => updateField('phone', text)}
            placeholder="+86 138 0000 0000"
            keyboardType="phone-pad"      // 显示电话号码键盘 | Display phone number keyboard
            textContentType="telephoneNumber"
            autoComplete="tel"
          />
          <Text style={styles.helpText}>
            ✓ phone-pad包含+、*、#等电话符号 | phone-pad includes +, *, # phone symbols
          </Text>
        </View>

        {/* 年龄输入 - 数字键盘 | Age - Numeric keyboard */}
        <View style={styles.field}>
          <Text style={styles.label}>年龄 | Age</Text>
          <TextInput
            style={styles.input}
            value={formData.age}
            onChangeText={(text) => {
              // 额外验证：只允许数字 | Additional validation: only allow numbers
              const numericText = text.replace(/[^0-9]/g, '');
              updateField('age', numericText);
            }}
            placeholder="25"
            keyboardType="numeric"        // 仅数字键盘 | Numeric keyboard only
            maxLength={3}                 // 限制最多3位数 | Limit to max 3 digits
          />
          <Text style={styles.helpText}>
            ✓ numeric键盘 + 验证逻辑确保仅数字 | numeric keyboard + validation ensures numbers only
          </Text>
        </View>

        {/* 收入输入 - 小数键盘 | Income - Decimal keyboard */}
        <View style={styles.field}>
          <Text style={styles.label}>年收入(万元) | Annual Income (¥10k)</Text>
          <TextInput
            style={styles.input}
            value={formData.income}
            onChangeText={(text) => {
              // 允许数字和一个小数点 | Allow numbers and one decimal point
              const parts = text.split('.');
              if (parts.length > 2) return; // 防止多个小数点 | Prevent multiple decimal points
              const validText = text.replace(/[^0-9.]/g, '');
              updateField('income', validText);
            }}
            placeholder="50.5"
            keyboardType="decimal-pad"    // 含小数点的数字键盘 | Numeric keyboard with decimal point
          />
          <Text style={styles.helpText}>
            ✓ decimal-pad适合货币和小数值 | decimal-pad suits currency and decimal values
          </Text>
        </View>

        {/* 邮编输入 - 平台自适应 | Zip Code - Platform adaptive */}
        <View style={styles.field}>
          <Text style={styles.label}>邮政编码 | Zip Code</Text>
          <TextInput
            style={styles.input}
            value={formData.zipCode}
            onChangeText={(text) => updateField('zipCode', text)}
            placeholder="100000"
            keyboardType={Platform.OS === 'ios' ? 'number-pad' : 'numeric'}  // iOS和Android优化 | iOS and Android optimization
            maxLength={6}
          />
          <Text style={styles.helpText}>
            ✓ 平台自适应：iOS用number-pad，Android用numeric | Platform adaptive: number-pad for iOS, numeric for Android
          </Text>
        </View>

        {/* 数据预览 | Data Preview */}
        <View style={styles.preview}>
          <Text style={styles.previewTitle}>表单数据预览 | Form Data Preview:</Text>
          <Text style={styles.previewText}>姓名 | Name: {formData.fullName}</Text>
          <Text style={styles.previewText}>邮箱 | Email: {formData.email}</Text>
          <Text style={styles.previewText}>电话 | Phone: {formData.phone}</Text>
          <Text style={styles.previewText}>年龄 | Age: {formData.age}</Text>
          <Text style={styles.previewText}>收入 | Income: {formData.income}</Text>
          <Text style={styles.previewText}>邮编 | Zip: {formData.zipCode}</Text>
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 16,
      backgroundColor: '#f8f9fa',
    },
    header: {
      fontSize: 22,
      fontWeight: 'bold',
      marginBottom: 20,
      color: '#2c3e50',
      textAlign: 'center',
    },
    field: {
      marginBottom: 20,
    },
    label: {
      fontSize: 14,
      fontWeight: '600',
      marginBottom: 6,
      color: '#34495e',
    },
    input: {
      backgroundColor: '#fff',
      borderWidth: 1,
      borderColor: '#ddd',
      borderRadius: 8,
      padding: 12,
      fontSize: 16,
      color: '#2c3e50',
    },
    helpText: {
      marginTop: 4,
      fontSize: 11,
      color: '#27ae60',
      fontStyle: 'italic',
    },
    preview: {
      marginTop: 24,
      padding: 16,
      backgroundColor: '#fff',
      borderRadius: 12,
      borderWidth: 1,
      borderColor: '#3498db',
    },
    previewTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 12,
      color: '#2980b9',
    },
    previewText: {
      fontSize: 14,
      color: '#34495e',
      marginBottom: 6,
      fontFamily: Platform.OS === 'ios' ? 'Courier' : 'monospace',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么邮箱输入字段要设置`autoCapitalize="none"`和`autoCorrect={false}`？| Why should email input fields set `autoCapitalize="none"` and `autoCorrect={false}`?
    **答案 | Answer:** 因为邮箱地址是区分大小写且格式精确的，自动大写会导致邮箱无效，自动更正可能改变正确的邮箱格式 | Because email addresses are case-sensitive and require exact formatting, auto-capitalize makes emails invalid, autocorrect may change correct email format

  - 示例中为什么年龄和收入字段都在onChangeText中添加了额外的验证逻辑？| Why do age and income fields in the example add extra validation logic in onChangeText?
    **答案 | Answer:** 因为keyboardType只改变键盘显示，不能阻止用户粘贴或通过其他方式输入无效字符，验证逻辑确保state中只存储有效数据 | Because keyboardType only changes keyboard display, cannot prevent users from pasting or entering invalid characters through other means, validation logic ensures only valid data is stored in state

### 3. 受控组件与非受控组件 | Controlled vs Uncontrolled Components (1小时 | 1 hour)

- **受控组件核心概念 | Controlled Component Core Concept**

  **概念定义 | Concept Definition:**
  受控组件是指其值完全由React state控制的组件。在TextInput中，这意味着组件的`value`属性始终绑定到state，所有更改通过`onChangeText`处理并更新state。这是React Native表单开发的推荐模式，因为它提供了完全的数据控制权和可预测的行为。

  A controlled component is one whose value is entirely controlled by React state. For TextInput, this means the component's `value` prop is always bound to state, and all changes are handled through `onChangeText` and update the state. This is the recommended pattern for React Native form development as it provides complete data control and predictable behavior.

  **核心特征 | Key Characteristics:**
  - **单一数据源 | Single Source of Truth**: State是唯一的数据来源，组件显示的值由state决定 | State is the single source of truth, component displays value determined by state
  - **可预测性 | Predictability**: 值的变化总是通过明确的state更新，便于调试和追踪 | Value changes always through explicit state updates, easy to debug and trace
  - **完全控制 | Full Control**: 可以在更新state前验证、转换或限制输入 | Can validate, transform, or restrict input before updating state
  - **必需的Props | Required Props**: 必须同时使用`value`和`onChangeText` props | Must use both `value` and `onChangeText` props together

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在受控组件中，TextInput显示的值来自哪里？| In a controlled component, where does the TextInput's displayed value come from?
     **答案 | Answer:** 来自绑定到value prop的state，而不是组件的内部状态 | From the state bound to the value prop, not from the component's internal state

  2. 如果只设置`value`而不设置`onChangeText`会发生什么？| What happens if you only set `value` without setting `onChangeText`?
     **答案 | Answer:** TextInput变成只读的，用户无法输入因为没有回调函数来更新state | TextInput becomes read-only, users cannot input because there's no callback function to update state

  3. 受控组件模式允许在用户输入时修改数据吗？| Does controlled component pattern allow modifying data during user input?
     **答案 | Answer:** 是的，可以在onChangeText回调中转换数据后再更新state，例如转大写、过滤特殊字符等 | Yes, can transform data in onChangeText callback before updating state, such as converting to uppercase, filtering special characters, etc.

  4. 受控组件和非受控组件哪个更适合生产环境？| Which is more suitable for production: controlled or uncontrolled components?
     **答案 | Answer:** 受控组件，因为它提供更好的数据验证、状态管理和可测试性 | Controlled components, as they provide better data validation, state management, and testability

  **代码示例与验证 | Code Examples and Verification:**
  ```jsx
  // 受控组件 vs 非受控组件对比 | Controlled vs Uncontrolled Components Comparison
  import React, { useState, useRef } from 'react';
  import { View, TextInput, Text, StyleSheet, Button } from 'react-native';

  export default function ControlledVsUncontrolled() {
    // 受控组件的state | Controlled component state
    const [controlledValue, setControlledValue] = useState('');

    // 非受控组件的ref（不推荐）| Uncontrolled component ref (not recommended)
    const uncontrolledRef = useRef(null);
    const [uncontrolledDisplay, setUncontrolledDisplay] = useState('');

    // 获取非受控组件的值（需要手动触发）| Get uncontrolled component value (requires manual trigger)
    const getUncontrolledValue = () => {
      // 注意：这在React Native中不太适用，主要展示概念差异
      // Note: This doesn't work well in React Native, mainly showing conceptual difference
      setUncontrolledDisplay('非受控组件需要ref访问，不推荐 | Uncontrolled requires ref access, not recommended');
    };

    return (
      <View style={styles.container}>
        <Text style={styles.title}>受控 vs 非受控组件 | Controlled vs Uncontrolled</Text>

        {/* 受控组件示例 | Controlled Component Example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>✅ 受控组件（推荐）| Controlled Component (Recommended)</Text>

          <TextInput
            style={styles.input}
            value={controlledValue}                    // 值由state控制 | Value controlled by state
            onChangeText={setControlledValue}          // 更新state | Update state
            placeholder="输入文本... | Enter text..."
          />

          <View style={styles.infoBox}>
            <Text style={styles.infoLabel}>实时State值 | Real-time State Value:</Text>
            <Text style={styles.infoValue}>{controlledValue || '(空 | empty)'}</Text>
            <Text style={styles.infoLabel}>字符数 | Character Count:</Text>
            <Text style={styles.infoValue}>{controlledValue.length}</Text>
          </View>

          <Text style={styles.explanation}>
            ✓ 值变化立即反映在state中{'\n'}
            ✓ 可以在onChangeText中验证和转换{'\n'}
            ✓ 组件重渲染时值保持一致{'\n'}
            {'\n'}
            ✓ Value changes immediately reflected in state{'\n'}
            ✓ Can validate and transform in onChangeText{'\n'}
            ✓ Value remains consistent on component re-render
          </Text>
        </View>

        {/* 非受控组件示例 | Uncontrolled Component Example */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>❌ 非受控组件（不推荐）| Uncontrolled Component (Not Recommended)</Text>

          <TextInput
            ref={uncontrolledRef}
            style={[styles.input, styles.inputWarning]}
            defaultValue=""                             // 使用defaultValue而非value | Use defaultValue instead of value
            placeholder="输入文本... | Enter text..."
            // 注意：没有onChangeText，state不会更新 | Note: No onChangeText, state won't update
          />

          <Button
            title="获取值（手动）| Get Value (Manual)"
            onPress={getUncontrolledValue}
          />

          <View style={[styles.infoBox, styles.infoBoxWarning]}>
            <Text style={styles.infoLabel}>显示信息 | Display Info:</Text>
            <Text style={styles.infoValue}>{uncontrolledDisplay || '点击按钮获取 | Click button to get'}</Text>
          </View>

          <Text style={[styles.explanation, styles.explanationWarning]}>
            ✗ 需要手动获取值（通过ref）{'\n'}
            ✗ 无法实时验证输入{'\n'}
            ✗ 组件重渲染可能丢失状态{'\n'}
            ✗ 在React Native中不推荐使用{'\n'}
            {'\n'}
            ✗ Requires manual value retrieval (via ref){'\n'}
            ✗ Cannot validate input in real-time{'\n'}
            ✗ May lose state on component re-render{'\n'}
            ✗ Not recommended in React Native
          </Text>
        </View>

        {/* 受控组件高级示例：实时验证 | Controlled Component Advanced: Real-time Validation */}
        <View style={styles.section}>
          <Text style={styles.sectionTitle}>🎯 受控组件高级用法 | Controlled Component Advanced Usage</Text>
          <ControlledWithValidation />
        </View>
      </View>
    );
  }

  // 带验证的受控组件示例 | Controlled Component with Validation
  function ControlledWithValidation() {
    const [email, setEmail] = useState('');
    const [isValid, setIsValid] = useState(true);

    const handleEmailChange = (text) => {
      setEmail(text);

      // 实时验证邮箱格式 | Real-time email validation
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      setIsValid(text === '' || emailRegex.test(text));
    };

    return (
      <View>
        <TextInput
          style={[
            styles.input,
            !isValid && styles.inputError  // 无效时显示红色边框 | Show red border when invalid
          ]}
          value={email}
          onChangeText={handleEmailChange}
          placeholder="输入邮箱 | Enter email"
          keyboardType="email-address"
          autoCapitalize="none"
          autoCorrect={false}
        />

        {!isValid && (
          <Text style={styles.errorText}>
            ⚠️ 邮箱格式无效 | Invalid email format
          </Text>
        )}

        <View style={styles.infoBox}>
          <Text style={styles.infoLabel}>验证状态 | Validation Status:</Text>
          <Text style={[styles.infoValue, isValid ? styles.valid : styles.invalid]}>
            {isValid ? '✓ 有效 | Valid' : '✗ 无效 | Invalid'}
          </Text>
        </View>

        <Text style={styles.explanation}>
          💡 受控组件允许实时验证和反馈{'\n'}
          💡 Controlled components enable real-time validation and feedback
        </Text>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 16,
      backgroundColor: '#f5f5f5',
    },
    title: {
      fontSize: 22,
      fontWeight: 'bold',
      marginBottom: 24,
      color: '#2c3e50',
      textAlign: 'center',
    },
    section: {
      backgroundColor: '#fff',
      padding: 16,
      borderRadius: 12,
      marginBottom: 20,
      shadowColor: '#000',
      shadowOffset: { width: 0, height: 2 },
      shadowOpacity: 0.1,
      shadowRadius: 4,
      elevation: 3,
    },
    sectionTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 16,
      color: '#34495e',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      borderRadius: 8,
      padding: 12,
      fontSize: 16,
      backgroundColor: '#fafafa',
      marginBottom: 12,
    },
    inputWarning: {
      borderColor: '#ff9800',
      backgroundColor: '#fff3e0',
    },
    inputError: {
      borderColor: '#e74c3c',
      backgroundColor: '#ffebee',
    },
    infoBox: {
      backgroundColor: '#e8f4f8',
      padding: 12,
      borderRadius: 8,
      marginTop: 8,
      marginBottom: 12,
    },
    infoBoxWarning: {
      backgroundColor: '#fff3e0',
    },
    infoLabel: {
      fontSize: 12,
      color: '#666',
      fontWeight: '600',
      marginTop: 4,
    },
    infoValue: {
      fontSize: 14,
      color: '#2c3e50',
      fontWeight: 'bold',
      marginBottom: 8,
    },
    explanation: {
      fontSize: 12,
      color: '#27ae60',
      lineHeight: 18,
      fontStyle: 'italic',
    },
    explanationWarning: {
      color: '#e67e22',
    },
    errorText: {
      color: '#e74c3c',
      fontSize: 12,
      marginTop: -8,
      marginBottom: 8,
    },
    valid: {
      color: '#27ae60',
    },
    invalid: {
      color: '#e74c3c',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在受控组件示例中，如果删除`onChangeText={setControlledValue}`会怎样？| In the controlled component example, what happens if we remove `onChangeText={setControlledValue}`?
    **答案 | Answer:** TextInput变成只读的，用户无法输入任何内容，因为value始终是state的初始值且没有更新机制 | TextInput becomes read-only, users cannot input anything because value is always the initial state value with no update mechanism

  - 为什么带验证的受控组件可以实时显示错误信息？| Why can the controlled component with validation display error messages in real-time?
    **答案 | Answer:** 因为每次输入都会触发onChangeText，在其中执行验证逻辑并更新isValid state，从而立即反映在UI上 | Because every input triggers onChangeText, which executes validation logic and updates isValid state, immediately reflecting in the UI

  **常见误区检查 | Common Misconception Checks:**
  - 受控组件比非受控组件性能更差吗？| Do controlled components have worse performance than uncontrolled components?
    **答案 | Answer:** 不一定，虽然受控组件每次输入都会触发重渲染，但React的优化机制使性能影响通常可以忽略。正确使用受控组件的好处远大于潜在的性能开销。| Not necessarily, although controlled components trigger re-render on every input, React's optimization mechanisms make performance impact usually negligible. Benefits of properly using controlled components far outweigh potential performance overhead.

  - 可以在同一个表单中混用受控和非受控组件吗？| Can you mix controlled and uncontrolled components in the same form?
    **答案 | Answer:** 技术上可以，但强烈不推荐，会导致数据流混乱、难以维护和调试。应该统一使用受控组件模式。| Technically yes, but strongly not recommended, leads to chaotic data flow, difficult to maintain and debug. Should consistently use controlled component pattern.
