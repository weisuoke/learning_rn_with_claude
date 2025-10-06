# React Native入门 - 第5天：Props、State 和 Hooks - 保持不变的核心 | React Native Introduction - Day 5: Props, State, and Hooks - What Stays the Same

## 学习目标 | Learning Objectives
- 理解React核心概念在React Native中的完全兼容性 | Understand the complete compatibility of React core concepts in React Native
- 掌握useState在移动应用中的状态管理 | Master useState for state management in mobile applications
- 学会使用useEffect处理组件生命周期事件 | Learn to use useEffect for component lifecycle events
- 创建和使用自定义Hooks来复用移动应用逻辑 | Create and use custom hooks to reuse mobile application logic
- 实现受控组件模式处理移动端表单输入 | Implement controlled component pattern for mobile form inputs
- 识别Web React和React Native在Hooks使用上的细微差异 | Identify subtle differences in Hooks usage between Web React and React Native

## 详细内容 | Detailed Content

### 1. useState - 移动端的状态管理 | useState - State Management on Mobile (1小时 | 1 hour)

- **useState Hook 基础 | useState Hook Fundamentals**

  **概念定义 | Concept Definition:**
  useState是React提供的Hook，用于在函数组件中添加状态。在React Native中，它的工作方式与Web React完全相同，允许组件存储和更新本地状态，触发重新渲染。| useState is a React Hook that adds state to functional components. In React Native, it works identically to Web React, allowing components to store and update local state, triggering re-renders.

  **核心特征 | Key Characteristics:**
  - 在React Native组件中的行为与Web React完全一致 | Behavior is identical to Web React in React Native components
  - 状态更新触发组件重新渲染和UI刷新 | State updates trigger component re-renders and UI refresh
  - 可以存储任何JavaScript值（数字、字符串、对象、数组等）| Can store any JavaScript value (numbers, strings, objects, arrays, etc.)
  - 每次渲染时保持状态值的持久性 | Maintains state value persistence across renders

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useState在React Native中的工作方式与Web React不同吗？| Does useState work differently in React Native compared to Web React?
     **答案 | Answer:** 否 | No - useState在两个平台上的工作方式完全相同，这是React的核心特性。
  2. 调用setState函数会立即更新状态值吗？| Does calling setState function immediately update the state value?
     **答案 | Answer:** 否 | No - 状态更新是异步的，会在下次渲染时体现。State updates are asynchronous and reflect in the next render.
  3. 可以在一个组件中使用多个useState Hook吗？| Can you use multiple useState Hooks in one component?
     **答案 | Answer:** 是 | Yes - 这是推荐的做法，为不同的状态值使用不同的useState调用。This is recommended practice for different state values.
  4. 在移动应用中状态更新会触发UI重新渲染吗？| Do state updates trigger UI re-renders in mobile apps?
     **答案 | Answer:** 是 | Yes - 这是React的核心机制，在React Native中保持一致。This is React's core mechanism, consistent in React Native.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';

  // 简单计数器组件 - 展示useState基础用法 | Simple counter component - demonstrates useState basics
  function Counter() {
    // 声明状态变量 | Declare state variable
    const [count, setCount] = useState(0); // 初始值为0 | Initial value is 0

    // 处理增加 | Handle increment
    const handleIncrement = () => {
      setCount(count + 1); // 更新状态 | Update state
    };

    // 处理减少 | Handle decrement
    const handleDecrement = () => {
      setCount(count - 1);
    };

    // 重置计数器 | Reset counter
    const handleReset = () => {
      setCount(0); // 重置为初始值 | Reset to initial value
    };

    return (
      <View style={styles.container}>
        <Text style={styles.countText}>计数 | Count: {count}</Text>
        <View style={styles.buttonContainer}>
          <Button title="增加 | +" onPress={handleIncrement} />
          <Button title="减少 | -" onPress={handleDecrement} />
          <Button title="重置 | Reset" onPress={handleReset} />
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    container: {
      padding: 20,
      alignItems: 'center',
    },
    countText: {
      fontSize: 24,
      marginBottom: 20,
    },
    buttonContainer: {
      flexDirection: 'row',
      gap: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中，点击"增加"按钮会发生什么？| What happens when you click the "Increment" button in this code?
    **答案 | Answer:** count状态增加1，组件重新渲染，Text组件显示新的计数值 | count state increases by 1, component re-renders, Text component shows new count value
  - 如果在handleIncrement中连续调用两次setCount(count + 1)会发生什么？| What happens if you call setCount(count + 1) twice in handleIncrement?
    **答案 | Answer:** count只会增加1，因为两次调用使用的是同一次渲染的count值。应该使用函数式更新：setCount(prev => prev + 1) | count will only increase by 1 because both calls use the same render's count value. Should use functional update: setCount(prev => prev + 1)

  **常见误区检查 | Common Misconception Checks:**
  - 状态更新后立即读取状态会得到新值吗？| Will reading state immediately after updating it give you the new value?
    **答案 | Answer:** 否 - 状态更新是异步的，新值在下次渲染时才可用 | No - state updates are asynchronous, new value is available in next render
  - 直接修改状态对象（如count++）会触发重新渲染吗？| Will directly modifying state (like count++) trigger re-render?
    **答案 | Answer:** 否 - 必须使用setState函数才能触发重新渲染 | No - must use setState function to trigger re-render

- **复杂状态管理 | Complex State Management**

  **概念定义 | Concept Definition:**
  当状态是对象或数组时，需要特别注意不可变性原则。在React Native中更新复杂状态时，应该创建新的对象/数组而不是修改现有的。| When state is an object or array, immutability principles require special attention. When updating complex state in React Native, create new objects/arrays instead of modifying existing ones.

  **核心特征 | Key Characteristics:**
  - 必须使用展开运算符或其他方法创建新对象/数组 | Must use spread operator or other methods to create new objects/arrays
  - 直接修改状态对象不会触发重新渲染 | Directly modifying state objects won't trigger re-renders
  - 可以使用多个useState或单个对象状态 | Can use multiple useState calls or single object state
  - 对象状态更新需要手动合并属性 | Object state updates require manual property merging

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以直接修改状态对象的属性然后调用setState吗？| Can you directly modify state object properties then call setState?
     **答案 | Answer:** 否 | No - 必须创建新对象，React通过引用比较来检测变化。Must create new object, React detects changes by reference comparison.
  2. 更新状态对象的一个属性时，其他属性会自动保留吗？| When updating one property of state object, are other properties automatically preserved?
     **答案 | Answer:** 否 | No - 必须手动使用展开运算符{...state, property: value}来保留其他属性。Must manually use spread operator {...state, property: value} to preserve other properties.
  3. 数组状态使用push()方法添加元素会触发重新渲染吗？| Will using array.push() to add elements trigger re-render?
     **答案 | Answer:** 否 | No - push()修改原数组，应使用[...array, newItem]或concat()创建新数组。push() mutates original array, should use [...array, newItem] or concat() to create new array.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import { View, Text, TextInput, Button, StyleSheet } from 'react-native';

  // 用户表单组件 - 展示对象状态管理 | User form component - demonstrates object state management
  function UserForm() {
    // 对象状态 | Object state
    const [user, setUser] = useState({
      name: '',
      email: '',
      age: '',
    });

    // 更新单个字段 | Update single field
    const updateField = (field, value) => {
      setUser(prevUser => ({
        ...prevUser,        // 保留其他字段 | Preserve other fields
        [field]: value,     // 更新特定字段 | Update specific field
      }));
    };

    // 提交处理 | Submit handler
    const handleSubmit = () => {
      console.log('用户数据 | User data:', user);
    };

    return (
      <View style={styles.formContainer}>
        <TextInput
          style={styles.input}
          placeholder="姓名 | Name"
          value={user.name}
          onChangeText={(text) => updateField('name', text)}
        />
        <TextInput
          style={styles.input}
          placeholder="邮箱 | Email"
          value={user.email}
          onChangeText={(text) => updateField('email', text)}
          keyboardType="email-address"
        />
        <TextInput
          style={styles.input}
          placeholder="年龄 | Age"
          value={user.age}
          onChangeText={(text) => updateField('age', text)}
          keyboardType="numeric"
        />
        <Button title="提交 | Submit" onPress={handleSubmit} />
      </View>
    );
  }

  // 待办事项列表 - 展示数组状态管理 | Todo list - demonstrates array state management
  function TodoList() {
    const [todos, setTodos] = useState([]);
    const [inputText, setInputText] = useState('');

    // 添加待办事项 | Add todo
    const addTodo = () => {
      if (inputText.trim()) {
        // 创建新数组，不修改原数组 | Create new array, don't mutate original
        setTodos([...todos, { id: Date.now(), text: inputText, done: false }]);
        setInputText(''); // 清空输入 | Clear input
      }
    };

    // 切换完成状态 | Toggle done status
    const toggleTodo = (id) => {
      setTodos(todos.map(todo =>
        todo.id === id ? { ...todo, done: !todo.done } : todo
      ));
    };

    // 删除待办事项 | Remove todo
    const removeTodo = (id) => {
      setTodos(todos.filter(todo => todo.id !== id));
    };

    return (
      <View style={styles.todoContainer}>
        <View style={styles.inputRow}>
          <TextInput
            style={styles.todoInput}
            placeholder="添加待办事项 | Add todo"
            value={inputText}
            onChangeText={setInputText}
          />
          <Button title="添加 | Add" onPress={addTodo} />
        </View>

        {todos.map(todo => (
          <View key={todo.id} style={styles.todoItem}>
            <Text style={[styles.todoText, todo.done && styles.todoDone]}>
              {todo.text}
            </Text>
            <View style={styles.todoActions}>
              <Button
                title={todo.done ? "撤销 | Undo" : "完成 | Done"}
                onPress={() => toggleTodo(todo.id)}
              />
              <Button title="删除 | Delete" onPress={() => removeTodo(todo.id)} />
            </View>
          </View>
        ))}
      </View>
    );
  }

  const styles = StyleSheet.create({
    formContainer: {
      padding: 20,
    },
    input: {
      borderWidth: 1,
      borderColor: '#ccc',
      padding: 10,
      marginBottom: 10,
      borderRadius: 5,
    },
    todoContainer: {
      padding: 20,
    },
    inputRow: {
      flexDirection: 'row',
      marginBottom: 20,
    },
    todoInput: {
      flex: 1,
      borderWidth: 1,
      borderColor: '#ccc',
      padding: 10,
      marginRight: 10,
      borderRadius: 5,
    },
    todoItem: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      padding: 10,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    todoText: {
      flex: 1,
      fontSize: 16,
    },
    todoDone: {
      textDecorationLine: 'line-through',
      color: '#999',
    },
    todoActions: {
      flexDirection: 'row',
      gap: 5,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在UserForm中，为什么使用prevUser => ({...prevUser, [field]: value})而不是直接{...user, [field]: value}？| In UserForm, why use prevUser => ({...prevUser, [field]: value}) instead of {...user, [field]: value}?
    **答案 | Answer:** 使用函数式更新确保基于最新状态值，避免多次快速更新时的竞态条件 | Functional update ensures working with latest state value, avoiding race conditions with rapid updates
  - 在TodoList中，如果使用todos.push()而不是[...todos, newItem]会怎样？| In TodoList, what happens if using todos.push() instead of [...todos, newItem]?
    **答案 | Answer:** 界面不会更新，因为push()修改原数组，React检测不到引用变化 | UI won't update because push() mutates original array, React doesn't detect reference change

  **常见误区检查 | Common Misconception Checks:**
  - 对象展开运算符{...state}会创建深拷贝吗？| Does object spread {...state} create a deep copy?
    **答案 | Answer:** 否 - 只是浅拷贝，嵌套对象仍然是引用。需要深拷贝时应使用专门的深拷贝方法 | No - only shallow copy, nested objects are still references. Use dedicated deep copy methods when needed

### 2. useEffect - 组件生命周期管理 | useEffect - Component Lifecycle Management (1小时 | 1 hour)

- **useEffect Hook 基础 | useEffect Hook Fundamentals**

  **概念定义 | Concept Definition:**
  useEffect是用于处理副作用的Hook，如数据获取、订阅、DOM操作等。在React Native中，它管理组件的生命周期事件，如挂载、更新和卸载。| useEffect is a Hook for handling side effects like data fetching, subscriptions, and DOM operations. In React Native, it manages component lifecycle events like mounting, updating, and unmounting.

  **核心特征 | Key Characteristics:**
  - 在每次渲染后执行（可通过依赖数组控制）| Runs after every render (controllable via dependency array)
  - 可以返回清理函数，在组件卸载或下次effect前执行 | Can return cleanup function, executed on unmount or before next effect
  - 依赖数组决定effect何时重新执行 | Dependency array determines when effect re-runs
  - 在React Native中用于处理移动端特定的副作用 | Used in React Native for mobile-specific side effects

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useEffect在组件每次渲染后都会执行吗？| Does useEffect run after every render?
     **答案 | Answer:** 默认是，但可以通过依赖数组控制执行时机 | By default yes, but execution timing can be controlled via dependency array
  2. 空依赖数组[]的useEffect会执行几次？| How many times does useEffect with empty dependency array [] run?
     **答案 | Answer:** 只在组件挂载时执行一次，类似componentDidMount | Only once on component mount, similar to componentDidMount
  3. useEffect的清理函数什么时候执行？| When does useEffect's cleanup function execute?
     **答案 | Answer:** 组件卸载时或下次effect执行前 | On component unmount or before next effect execution
  4. 在React Native中可以在useEffect中调用console.log吗？| Can you call console.log in useEffect in React Native?
     **答案 | Answer:** 是 - 输出会显示在Metro Bundler终端中 | Yes - output appears in Metro Bundler terminal

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, Button, StyleSheet } from 'react-native';

  // 生命周期演示组件 | Lifecycle demonstration component
  function LifecycleDemo() {
    const [count, setCount] = useState(0);
    const [isVisible, setIsVisible] = useState(true);

    // Effect 1: 每次渲染后执行 | Runs after every render
    useEffect(() => {
      console.log('组件已渲染 | Component rendered');
      console.log('当前计数 | Current count:', count);
    }); // 没有依赖数组 | No dependency array

    // Effect 2: 仅在挂载时执行 | Runs only on mount
    useEffect(() => {
      console.log('组件已挂载 | Component mounted');

      // 清理函数 | Cleanup function
      return () => {
        console.log('组件将卸载 | Component will unmount');
      };
    }, []); // 空依赖数组 | Empty dependency array

    // Effect 3: count变化时执行 | Runs when count changes
    useEffect(() => {
      console.log('计数已变化为 | Count changed to:', count);

      // 设置定时器 | Set timer
      const timer = setTimeout(() => {
        console.log('定时器触发 | Timer fired for count:', count);
      }, 1000);

      // 清理定时器 | Cleanup timer
      return () => {
        console.log('清理定时器 | Cleaning up timer');
        clearTimeout(timer);
      };
    }, [count]); // 依赖count | Depends on count

    return (
      <View style={styles.lifecycleContainer}>
        <Text style={styles.title}>生命周期演示 | Lifecycle Demo</Text>
        <Text style={styles.countText}>计数 | Count: {count}</Text>
        <Button title="增加 | Increment" onPress={() => setCount(count + 1)} />
        <Text style={styles.hint}>查看控制台日志 | Check console logs</Text>
      </View>
    );
  }

  // 计时器组件 - 展示effect清理 | Timer component - demonstrates effect cleanup
  function Timer() {
    const [seconds, setSeconds] = useState(0);
    const [isRunning, setIsRunning] = useState(false);

    useEffect(() => {
      let interval = null;

      if (isRunning) {
        // 启动定时器 | Start timer
        interval = setInterval(() => {
          setSeconds(prevSeconds => prevSeconds + 1);
        }, 1000);

        console.log('定时器已启动 | Timer started');
      }

      // 清理函数：停止定时器 | Cleanup function: stop timer
      return () => {
        if (interval) {
          clearInterval(interval);
          console.log('定时器已停止 | Timer stopped');
        }
      };
    }, [isRunning]); // 依赖isRunning | Depends on isRunning

    const toggleTimer = () => {
      setIsRunning(!isRunning);
    };

    const resetTimer = () => {
      setIsRunning(false);
      setSeconds(0);
    };

    return (
      <View style={styles.timerContainer}>
        <Text style={styles.timerText}>{seconds}秒 | {seconds}s</Text>
        <View style={styles.timerButtons}>
          <Button
            title={isRunning ? "暂停 | Pause" : "开始 | Start"}
            onPress={toggleTimer}
          />
          <Button title="重置 | Reset" onPress={resetTimer} />
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    lifecycleContainer: {
      padding: 20,
      alignItems: 'center',
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    countText: {
      fontSize: 18,
      marginBottom: 10,
    },
    hint: {
      marginTop: 10,
      color: '#666',
      fontStyle: 'italic',
    },
    timerContainer: {
      padding: 20,
      alignItems: 'center',
    },
    timerText: {
      fontSize: 48,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    timerButtons: {
      flexDirection: 'row',
      gap: 10,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在Timer组件中，为什么需要在useEffect中返回清理函数？| In Timer component, why is cleanup function needed in useEffect?
    **答案 | Answer:** 防止内存泄漏，确保组件卸载或isRunning变化时清除定时器 | Prevents memory leaks, ensures timer is cleared when component unmounts or isRunning changes
  - 如果LifecycleDemo的第一个useEffect有依赖数组[count]，行为会如何改变？| If LifecycleDemo's first useEffect has dependency array [count], how would behavior change?
    **答案 | Answer:** 只在count变化时执行，而不是每次渲染 | Would only run when count changes, not on every render

  **常见误区检查 | Common Misconception Checks:**
  - useEffect可以是async函数吗？| Can useEffect be an async function?
    **答案 | Answer:** 否 - useEffect不能直接是async，但可以在内部定义并调用async函数 | No - useEffect cannot be async directly, but can define and call async function inside
  - 在useEffect中访问的state值会自动更新吗？| Will state values accessed in useEffect automatically update?
    **答案 | Answer:** 否 - effect使用的是创建时的state值，除非将state加入依赖数组 | No - effect uses state values from when it was created, unless state is in dependency array

- **移动端特定的副作用 | Mobile-Specific Side Effects**

  **概念定义 | Concept Definition:**
  React Native中的某些副作用是移动平台特有的，如键盘事件监听、应用状态监听、屏幕方向变化等。这些需要在useEffect中正确设置和清理。| Certain side effects in React Native are mobile-platform specific, such as keyboard event listeners, app state listeners, and screen orientation changes. These need proper setup and cleanup in useEffect.

  **核心特征 | Key Characteristics:**
  - 需要订阅移动端特定的事件监听器 | Requires subscribing to mobile-specific event listeners
  - 必须在清理函数中取消订阅，避免内存泄漏 | Must unsubscribe in cleanup function to avoid memory leaks
  - 某些API返回订阅对象，需要调用remove()或unsubscribe() | Some APIs return subscription objects requiring remove() or unsubscribe()
  - 在开发中可能需要刷新多次才能看到效果 | May need multiple refreshes in development to see effects

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Native中添加事件监听器后必须移除吗？| Must you remove event listeners after adding them in React Native?
     **答案 | Answer:** 是 - 不移除会导致内存泄漏和重复监听 | Yes - not removing causes memory leaks and duplicate listeners
  2. Keyboard事件监听器在组件卸载后仍然存在吗？| Do Keyboard event listeners persist after component unmounts?
     **答案 | Answer:** 是，如果没有在清理函数中移除 | Yes, if not removed in cleanup function
  3. 可以在一个useEffect中添加多个事件监听器吗？| Can you add multiple event listeners in one useEffect?
     **答案 | Answer:** 是 - 但要在清理函数中移除所有监听器 | Yes - but must remove all in cleanup function

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import {
    View,
    Text,
    TextInput,
    Keyboard,
    StyleSheet,
  } from 'react-native';

  // 键盘监听示例 | Keyboard listener example
  function KeyboardExample() {
    const [keyboardVisible, setKeyboardVisible] = useState(false);
    const [keyboardHeight, setKeyboardHeight] = useState(0);

    useEffect(() => {
      // 添加键盘事件监听器 | Add keyboard event listeners
      const showListener = Keyboard.addListener('keyboardDidShow', (e) => {
        console.log('键盘显示 | Keyboard shown');
        setKeyboardVisible(true);
        setKeyboardHeight(e.endCoordinates.height);
      });

      const hideListener = Keyboard.addListener('keyboardDidHide', () => {
        console.log('键盘隐藏 | Keyboard hidden');
        setKeyboardVisible(false);
        setKeyboardHeight(0);
      });

      // 清理函数：移除监听器 | Cleanup function: remove listeners
      return () => {
        console.log('移除键盘监听器 | Removing keyboard listeners');
        showListener.remove();
        hideListener.remove();
      };
    }, []); // 只在挂载时设置 | Setup only on mount

    return (
      <View style={styles.keyboardContainer}>
        <Text style={styles.statusText}>
          键盘状态 | Keyboard Status: {keyboardVisible ? '显示 | Visible' : '隐藏 | Hidden'}
        </Text>
        {keyboardVisible && (
          <Text style={styles.heightText}>
            键盘高度 | Keyboard Height: {keyboardHeight}px
          </Text>
        )}
        <TextInput
          style={styles.input}
          placeholder="点击输入以显示键盘 | Tap to show keyboard"
        />
      </View>
    );
  }

  const styles = StyleSheet.create({
    keyboardContainer: {
      padding: 20,
    },
    statusText: {
      fontSize: 16,
      marginBottom: 10,
    },
    heightText: {
      fontSize: 14,
      color: '#666',
      marginBottom: 10,
    },
    input: {
      borderWidth: 1,
      borderColor: '#ccc',
      padding: 10,
      borderRadius: 5,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果忘记在清理函数中移除键盘监听器会怎样？| What happens if you forget to remove keyboard listeners in cleanup?
    **答案 | Answer:** 每次组件重新挂载都会添加新监听器，导致内存泄漏和多次触发回调 | New listeners added each time component remounts, causing memory leaks and multiple callback triggers

  **常见误区检查 | Common Misconception Checks:**
  - 在useEffect外部添加事件监听器可以吗？| Can you add event listeners outside useEffect?
    **答案 | Answer:** 不推荐 - 难以正确清理，应该在useEffect中管理副作用 | Not recommended - hard to cleanup properly, should manage side effects in useEffect

### 3. 自定义Hooks - 复用逻辑 | Custom Hooks - Reusing Logic (1小时 | 1 hour)

- **自定义Hooks 概念 | Custom Hooks Concept**

  **概念定义 | Concept Definition:**
  自定义Hook是以"use"开头的JavaScript函数，内部可以调用其他Hooks。它们允许在组件之间复用状态逻辑，在React Native中用于提取常见的移动应用模式。| Custom Hooks are JavaScript functions starting with "use" that can call other Hooks internally. They allow reusing stateful logic between components, used in React Native to extract common mobile app patterns.

  **核心特征 | Key Characteristics:**
  - 函数名必须以"use"开头 | Function name must start with "use"
  - 可以调用其他Hooks（useState、useEffect等）| Can call other Hooks (useState, useEffect, etc.)
  - 每次调用自定义Hook都创建独立的状态 | Each call to custom Hook creates independent state
  - 在React Native和Web React中工作方式相同 | Works identically in React Native and Web React

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 自定义Hook的名称可以不以"use"开头吗？| Can custom Hook names not start with "use"?
     **答案 | Answer:** 技术上可以，但违反React规则，会失去Hook的lint检查和优化 | Technically yes, but violates React rules, loses Hook linting and optimizations
  2. 两个组件使用同一个自定义Hook会共享状态吗？| Will two components using the same custom Hook share state?
     **答案 | Answer:** 否 - 每个组件调用Hook都创建独立的状态实例 | No - each component call creates independent state instance
  3. 自定义Hook可以返回JSX吗？| Can custom Hooks return JSX?
     **答案 | Answer:** 可以，但通常返回状态值和函数，返回JSX的应该是组件 | Can, but typically return state values and functions, those returning JSX should be components
  4. 可以在自定义Hook中使用useEffect吗？| Can you use useEffect in custom Hooks?
     **答案 | Answer:** 是 - 自定义Hook可以使用任何React Hook | Yes - custom Hooks can use any React Hook

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useEffect } from 'react';
  import { View, Text, Button, Switch, StyleSheet } from 'react-native';

  // 自定义Hook: useToggle - 管理布尔状态 | Custom Hook: useToggle - manages boolean state
  function useToggle(initialValue = false) {
    const [value, setValue] = useState(initialValue);

    // 切换函数 | Toggle function
    const toggle = () => setValue(prev => !prev);

    // 设置为true | Set to true
    const setTrue = () => setValue(true);

    // 设置为false | Set to false
    const setFalse = () => setValue(false);

    return [value, { toggle, setTrue, setFalse }];
  }

  // 使用useToggle的组件 | Component using useToggle
  function ToggleExample() {
    const [isOn, { toggle, setTrue, setFalse }] = useToggle(false);

    return (
      <View style={styles.toggleContainer}>
        <Text style={styles.statusText}>
          状态 | Status: {isOn ? '开启 | ON' : '关闭 | OFF'}
        </Text>
        <View style={styles.toggleButtons}>
          <Button title="切换 | Toggle" onPress={toggle} />
          <Button title="开启 | On" onPress={setTrue} />
          <Button title="关闭 | Off" onPress={setFalse} />
        </View>
      </View>
    );
  }

  // 自定义Hook: useCounter - 计数器逻辑 | Custom Hook: useCounter - counter logic
  function useCounter(initialValue = 0, step = 1) {
    const [count, setCount] = useState(initialValue);

    const increment = () => setCount(prev => prev + step);
    const decrement = () => setCount(prev => prev - step);
    const reset = () => setCount(initialValue);
    const setValue = (value) => setCount(value);

    return {
      count,
      increment,
      decrement,
      reset,
      setValue,
    };
  }

  // 使用useCounter的组件 | Component using useCounter
  function CounterExample() {
    const counter = useCounter(0, 5); // 初始值0，步长5 | Initial 0, step 5

    return (
      <View style={styles.counterContainer}>
        <Text style={styles.countDisplay}>计数 | Count: {counter.count}</Text>
        <View style={styles.counterButtons}>
          <Button title="+5" onPress={counter.increment} />
          <Button title="-5" onPress={counter.decrement} />
          <Button title="重置 | Reset" onPress={counter.reset} />
        </View>
      </View>
    );
  }

  // 自定义Hook: useInterval - 定时器 | Custom Hook: useInterval - timer
  function useInterval(callback, delay) {
    useEffect(() => {
      if (delay === null) return; // 暂停 | Paused

      const interval = setInterval(callback, delay);

      return () => clearInterval(interval);
    }, [callback, delay]);
  }

  // 使用useInterval的组件 | Component using useInterval
  function ClockExample() {
    const [time, setTime] = useState(new Date());
    const [isPaused, setIsPaused] = useState(false);

    // 每秒更新时间 | Update time every second
    useInterval(() => {
      setTime(new Date());
    }, isPaused ? null : 1000); // null时暂停 | null pauses

    return (
      <View style={styles.clockContainer}>
        <Text style={styles.timeText}>
          {time.toLocaleTimeString()}
        </Text>
        <Button
          title={isPaused ? "恢复 | Resume" : "暂停 | Pause"}
          onPress={() => setIsPaused(!isPaused)}
        />
      </View>
    );
  }

  // 自定义Hook: useKeyboard - 键盘状态 | Custom Hook: useKeyboard - keyboard state
  function useKeyboard() {
    const [keyboardVisible, setKeyboardVisible] = useState(false);
    const [keyboardHeight, setKeyboardHeight] = useState(0);

    useEffect(() => {
      const showListener = Keyboard.addListener('keyboardDidShow', (e) => {
        setKeyboardVisible(true);
        setKeyboardHeight(e.endCoordinates.height);
      });

      const hideListener = Keyboard.addListener('keyboardDidHide', () => {
        setKeyboardVisible(false);
        setKeyboardHeight(0);
      });

      return () => {
        showListener.remove();
        hideListener.remove();
      };
    }, []);

    return { keyboardVisible, keyboardHeight };
  }

  const styles = StyleSheet.create({
    toggleContainer: {
      padding: 20,
    },
    statusText: {
      fontSize: 18,
      marginBottom: 15,
      textAlign: 'center',
    },
    toggleButtons: {
      flexDirection: 'row',
      justifyContent: 'space-around',
    },
    counterContainer: {
      padding: 20,
      alignItems: 'center',
    },
    countDisplay: {
      fontSize: 32,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    counterButtons: {
      flexDirection: 'row',
      gap: 10,
    },
    clockContainer: {
      padding: 20,
      alignItems: 'center',
    },
    timeText: {
      fontSize: 28,
      marginBottom: 20,
      fontFamily: 'monospace',
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - useToggle返回数组而useCounter返回对象，哪种方式更好？| useToggle returns array while useCounter returns object, which is better?
    **答案 | Answer:** 取决于使用场景：数组适合需要重命名的少量值，对象适合多个命名属性 | Depends on use case: arrays for few values needing renaming, objects for multiple named properties
  - 在useInterval中，为什么delay为null时不运行定时器？| In useInterval, why doesn't timer run when delay is null?
    **答案 | Answer:** 这是暂停机制，null作为特殊值跳过setInterval设置 | This is pause mechanism, null as special value skips setInterval setup

  **常见误区检查 | Common Misconception Checks:**
  - 自定义Hook是组件吗？| Are custom Hooks components?
    **答案 | Answer:** 否 - 它们是函数，返回值供组件使用，不返回JSX | No - they are functions returning values for components to use, don't return JSX
  - 可以在条件语句中调用自定义Hook吗？| Can you call custom Hooks conditionally?
    **答案 | Answer:** 否 - 遵循Hook规则，必须在组件顶层调用 | No - follows Hook rules, must be called at component top level

### 4. 受控组件 - 表单处理 | Controlled Components - Form Handling (1小时 | 1 hour)

- **受控组件模式 | Controlled Component Pattern**

  **概念定义 | Concept Definition:**
  受控组件是其值由React state控制的输入组件。在React Native中，TextInput组件通过value和onChangeText props实现受控，确保state是唯一的数据源。| Controlled components are input components whose value is controlled by React state. In React Native, TextInput components are controlled via value and onChangeText props, ensuring state is the single source of truth.

  **核心特征 | Key Characteristics:**
  - 输入值存储在state中 | Input value stored in state
  - 通过onChangeText更新state | State updated via onChangeText
  - TextInput的value prop绑定到state | TextInput's value prop bound to state
  - 可以轻松验证和转换输入 | Easy to validate and transform input

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 受控TextInput的value必须来自state吗？| Must controlled TextInput's value come from state?
     **答案 | Answer:** 是 - 这是受控组件的定义，state是唯一数据源 | Yes - this is definition of controlled component, state is single source of truth
  2. React Native使用onChangeText还是onChange？| Does React Native use onChangeText or onChange?
     **答案 | Answer:** onChangeText - 直接接收文本值，而Web用onChange接收事件对象 | onChangeText - receives text value directly, while Web uses onChange with event object
  3. 可以在onChangeText中验证和过滤输入吗？| Can you validate and filter input in onChangeText?
     **答案 | Answer:** 是 - 这是受控组件的优势之一 | Yes - this is one advantage of controlled components
  4. 非受控组件在React Native中常用吗？| Are uncontrolled components common in React Native?
     **答案 | Answer:** 否 - React Native主要使用受控组件模式 | No - React Native primarily uses controlled component pattern

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState } from 'react';
  import {
    View,
    Text,
    TextInput,
    Button,
    Alert,
    StyleSheet,
  } from 'react-native';

  // 简单登录表单 - 受控组件示例 | Simple login form - controlled component example
  function LoginForm() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [errors, setErrors] = useState({});

    // 验证邮箱 | Validate email
    const validateEmail = (text) => {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return emailRegex.test(text);
    };

    // 处理邮箱输入 | Handle email input
    const handleEmailChange = (text) => {
      setEmail(text);
      // 实时验证 | Real-time validation
      if (text && !validateEmail(text)) {
        setErrors(prev => ({ ...prev, email: '邮箱格式无效 | Invalid email format' }));
      } else {
        setErrors(prev => ({ ...prev, email: null }));
      }
    };

    // 处理密码输入 | Handle password input
    const handlePasswordChange = (text) => {
      setPassword(text);
      // 密码长度验证 | Password length validation
      if (text && text.length < 6) {
        setErrors(prev => ({ ...prev, password: '密码至少6个字符 | Password min 6 characters' }));
      } else {
        setErrors(prev => ({ ...prev, password: null }));
      }
    };

    // 提交表单 | Submit form
    const handleSubmit = () => {
      if (!email || !password) {
        Alert.alert('错误 | Error', '请填写所有字段 | Please fill all fields');
        return;
      }

      if (errors.email || errors.password) {
        Alert.alert('错误 | Error', '请修正错误后提交 | Please fix errors before submitting');
        return;
      }

      // 模拟登录 | Simulate login
      Alert.alert('成功 | Success', `登录为 | Logged in as: ${email}`);

      // 清空表单 | Clear form
      setEmail('');
      setPassword('');
    };

    return (
      <View style={styles.formContainer}>
        <Text style={styles.formTitle}>登录 | Login</Text>

        {/* 邮箱输入 | Email input */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>邮箱 | Email</Text>
          <TextInput
            style={[styles.input, errors.email && styles.inputError]}
            value={email}
            onChangeText={handleEmailChange}
            placeholder="your@email.com"
            keyboardType="email-address"
            autoCapitalize="none"
            autoCorrect={false}
          />
          {errors.email && <Text style={styles.errorText}>{errors.email}</Text>}
        </View>

        {/* 密码输入 | Password input */}
        <View style={styles.inputGroup}>
          <Text style={styles.label}>密码 | Password</Text>
          <TextInput
            style={[styles.input, errors.password && styles.inputError]}
            value={password}
            onChangeText={handlePasswordChange}
            placeholder="最少6个字符 | Min 6 characters"
            secureTextEntry
            autoCapitalize="none"
          />
          {errors.password && <Text style={styles.errorText}>{errors.password}</Text>}
        </View>

        <Button title="登录 | Login" onPress={handleSubmit} />
      </View>
    );
  }

  // 带格式化的输入示例 | Input with formatting example
  function FormattedInputExample() {
    const [phone, setPhone] = useState('');
    const [creditCard, setCreditCard] = useState('');

    // 格式化电话号码 (123) 456-7890 | Format phone number
    const formatPhone = (text) => {
      // 移除非数字字符 | Remove non-digit characters
      const cleaned = text.replace(/\D/g, '');

      // 限制10位 | Limit to 10 digits
      const limited = cleaned.slice(0, 10);

      // 格式化 | Format
      if (limited.length <= 3) {
        return limited;
      } else if (limited.length <= 6) {
        return `(${limited.slice(0, 3)}) ${limited.slice(3)}`;
      } else {
        return `(${limited.slice(0, 3)}) ${limited.slice(3, 6)}-${limited.slice(6)}`;
      }
    };

    // 处理电话输入 | Handle phone input
    const handlePhoneChange = (text) => {
      const formatted = formatPhone(text);
      setPhone(formatted);
    };

    // 格式化信用卡号 1234 5678 9012 3456 | Format credit card number
    const formatCreditCard = (text) => {
      const cleaned = text.replace(/\D/g, '');
      const limited = cleaned.slice(0, 16);

      // 每4位添加空格 | Add space every 4 digits
      return limited.replace(/(\d{4})/g, '$1 ').trim();
    };

    // 处理信用卡输入 | Handle credit card input
    const handleCreditCardChange = (text) => {
      const formatted = formatCreditCard(text);
      setCreditCard(formatted);
    };

    return (
      <View style={styles.formContainer}>
        <Text style={styles.formTitle}>格式化输入 | Formatted Input</Text>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>电话号码 | Phone Number</Text>
          <TextInput
            style={styles.input}
            value={phone}
            onChangeText={handlePhoneChange}
            placeholder="(123) 456-7890"
            keyboardType="phone-pad"
          />
        </View>

        <View style={styles.inputGroup}>
          <Text style={styles.label}>信用卡 | Credit Card</Text>
          <TextInput
            style={styles.input}
            value={creditCard}
            onChangeText={handleCreditCardChange}
            placeholder="1234 5678 9012 3456"
            keyboardType="number-pad"
          />
        </View>
      </View>
    );
  }

  const styles = StyleSheet.create({
    formContainer: {
      padding: 20,
    },
    formTitle: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
      textAlign: 'center',
    },
    inputGroup: {
      marginBottom: 15,
    },
    label: {
      fontSize: 16,
      marginBottom: 5,
      fontWeight: '600',
    },
    input: {
      borderWidth: 1,
      borderColor: '#ddd',
      padding: 12,
      borderRadius: 8,
      fontSize: 16,
    },
    inputError: {
      borderColor: '#ff0000',
    },
    errorText: {
      color: '#ff0000',
      fontSize: 12,
      marginTop: 5,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么在LoginForm中使用实时验证而不是提交时验证？| Why use real-time validation in LoginForm instead of validation on submit?
    **答案 | Answer:** 提供更好的用户体验，用户可以立即看到输入错误并修正 | Provides better UX, users can immediately see input errors and correct them
  - formatPhone函数如何防止用户输入超过10位数字？| How does formatPhone prevent users from entering more than 10 digits?
    **答案 | Answer:** 使用slice(0, 10)截取前10位 | Uses slice(0, 10) to take only first 10 digits

  **常见误区检查 | Common Misconception Checks:**
  - 可以在TextInput中使用defaultValue代替value吗？| Can you use defaultValue instead of value in TextInput?
    **答案 | Answer:** 可以，但那是非受控组件，失去了state控制和验证能力 | Yes, but that's uncontrolled component, loses state control and validation capability
  - onChangeText和onChange在React Native中可以互换使用吗？| Are onChangeText and onChange interchangeable in React Native?
    **答案 | Answer:** 否 - onChangeText接收文本值，onChange接收事件对象，在RN中应使用onChangeText | No - onChangeText receives text value, onChange receives event object, should use onChangeText in RN

### 5. useContext - 跨组件状态共享 | useContext - Cross-Component State Sharing (30分钟 | 30 minutes)

- **Context API 在移动端 | Context API on Mobile**

  **概念定义 | Concept Definition:**
  useContext允许组件订阅React context，避免通过props逐层传递数据。在React Native应用中，常用于主题、认证状态、语言设置等全局数据。| useContext allows components to subscribe to React context, avoiding prop drilling. In React Native apps, commonly used for themes, authentication state, language settings, and other global data.

  **核心特征 | Key Characteristics:**
  - 在React Native中工作方式与Web React相同 | Works identically to Web React in React Native
  - 提供全局状态，无需props传递 | Provides global state without prop passing
  - Context值变化时，所有消费者重新渲染 | All consumers re-render when context value changes
  - 适合不常变化的全局数据 | Suitable for infrequently changing global data

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useContext在React Native和Web React中有区别吗？| Is useContext different between React Native and Web React?
     **答案 | Answer:** 否 - 完全相同的API和行为 | No - identical API and behavior
  2. Context Provider的value变化时会发生什么？| What happens when Context Provider's value changes?
     **答案 | Answer:** 所有使用该context的组件都会重新渲染 | All components using that context will re-render
  3. 可以在一个应用中使用多个不同的Context吗？| Can you use multiple different Contexts in one app?
     **答案 | Answer:** 是 - 这是常见做法，如ThemeContext、AuthContext等 | Yes - this is common practice, like ThemeContext, AuthContext, etc.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import React, { useState, useContext, createContext } from 'react';
  import { View, Text, Button, Switch, StyleSheet } from 'react-native';

  // 创建主题Context | Create theme context
  const ThemeContext = createContext();

  // 主题Provider组件 | Theme Provider component
  function ThemeProvider({ children }) {
    const [isDark, setIsDark] = useState(false);

    const theme = {
      isDark,
      colors: {
        background: isDark ? '#000' : '#fff',
        text: isDark ? '#fff' : '#000',
        primary: isDark ? '#4da6ff' : '#007bff',
      },
      toggleTheme: () => setIsDark(prev => !prev),
    };

    return (
      <ThemeContext.Provider value={theme}>
        {children}
      </ThemeContext.Provider>
    );
  }

  // 自定义Hook简化context使用 | Custom Hook to simplify context usage
  function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) {
      throw new Error('useTheme must be used within ThemeProvider');
    }
    return context;
  }

  // 使用主题的组件 | Component using theme
  function ThemedScreen() {
    const { colors, isDark, toggleTheme } = useTheme();

    return (
      <View style={[styles.screen, { backgroundColor: colors.background }]}>
        <Text style={[styles.title, { color: colors.text }]}>
          主题示例 | Theme Example
        </Text>
        <Text style={[styles.subtitle, { color: colors.text }]}>
          当前模式 | Current Mode: {isDark ? '深色 | Dark' : '浅色 | Light'}
        </Text>
        <Button
          title="切换主题 | Toggle Theme"
          onPress={toggleTheme}
          color={colors.primary}
        />
      </View>
    );
  }

  // 应用根组件 | App root component
  function ContextExample() {
    return (
      <ThemeProvider>
        <ThemedScreen />
      </ThemeProvider>
    );
  }

  const styles = StyleSheet.create({
    screen: {
      flex: 1,
      padding: 20,
      justifyContent: 'center',
      alignItems: 'center',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 10,
    },
    subtitle: {
      fontSize: 16,
      marginBottom: 20,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么创建useTheme自定义Hook而不是直接使用useContext？| Why create useTheme custom Hook instead of using useContext directly?
    **答案 | Answer:** 提供类型安全、错误检查，并简化API使用 | Provides type safety, error checking, and simplifies API usage

  **常见误区检查 | Common Misconception Checks:**
  - Context适合存储频繁变化的状态吗？| Is Context suitable for frequently changing state?
    **答案 | Answer:** 不太适合 - 每次变化会导致所有消费者重新渲染，应考虑状态管理库 | Not ideal - each change causes all consumers to re-render, consider state management libraries

### 6. 知识巩固与最佳实践 | Knowledge Consolidation and Best Practices (30分钟 | 30 minutes)

- **React Hooks 最佳实践 | React Hooks Best Practices**

  **关键原则 | Key Principles:**
  - 只在组件顶层调用Hooks，不在循环、条件或嵌套函数中 | Call Hooks only at top level, not in loops, conditions, or nested functions
  - 只在React函数组件或自定义Hooks中调用Hooks | Call Hooks only from React function components or custom Hooks
  - 使用ESLint插件`eslint-plugin-react-hooks`确保规则遵守 | Use ESLint plugin `eslint-plugin-react-hooks` to ensure rule compliance
  - 在useEffect依赖数组中包含所有使用的外部变量 | Include all used external variables in useEffect dependency array
  - 优先使用函数式更新避免过时的state | Prefer functional updates to avoid stale state

  **实践验证问题 | Practice Verification Questions:**
  1. 可以在条件语句中调用useState吗？| Can you call useState in a conditional statement?
     **答案 | Answer:** 否 - 违反Hooks规则，会导致运行时错误 | No - violates Hooks rules, causes runtime errors
  2. useEffect依赖数组中遗漏变量会怎样？| What happens if you omit variables from useEffect dependency array?
     **答案 | Answer:** 可能使用过时的值，导致bug，ESLint会警告 | May use stale values causing bugs, ESLint will warn
  3. 为什么使用setCount(prev => prev + 1)比setCount(count + 1)更好？| Why is setCount(prev => prev + 1) better than setCount(count + 1)?
     **答案 | Answer:** 函数式更新确保基于最新state，避免批量更新中的竞态条件 | Functional update ensures working with latest state, avoids race conditions in batched updates

  **综合概念检查 | Comprehensive Concept Check:**
  1. React Native中的Hooks与Web React有根本性区别吗？| Are Hooks fundamentally different in React Native vs Web React?
     **答案 | Answer:** 否 - API和规则完全相同，只是处理的是移动UI组件 | No - API and rules are identical, just working with mobile UI components
  2. 状态管理、副作用处理、自定义逻辑复用的工具分别是什么？| What are the tools for state management, side effect handling, and custom logic reuse?
     **答案 | Answer:** useState用于状态，useEffect用于副作用，自定义Hooks用于逻辑复用 | useState for state, useEffect for side effects, custom Hooks for logic reuse
  3. 受控组件相比非受控组件的主要优势是什么？| What's the main advantage of controlled components over uncontrolled?
     **答案 | Answer:** State是唯一数据源，可以轻松验证、格式化和控制输入 | State is single source of truth, easy to validate, format, and control input
  4. 何时应该使用Context而不是props传递？| When should you use Context instead of prop passing?
     **答案 | Answer:** 数据需要在多个层级的多个组件中共享时 | When data needs to be shared across multiple components at different nesting levels
  5. 创建自定义Hook的主要目的是什么？| What's the main purpose of creating custom Hooks?
     **答案 | Answer:** 提取和复用有状态的逻辑，在多个组件间共享 | Extract and reuse stateful logic across multiple components

## 实践项目：个人信息管理应用 | Practical Project: Personal Information Manager

### 目标 | Objective
构建一个完整的个人信息管理应用，综合应用useState管理多个状态、useEffect处理副作用、自定义Hooks复用逻辑、受控组件处理表单输入，以及useContext管理全局主题。| Build a complete personal information management app that comprehensively applies useState for multiple states, useEffect for side effects, custom Hooks for logic reuse, controlled components for form input, and useContext for global theme management.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. useState可以管理对象和数组状态吗？如何正确更新它们？| Can useState manage object and array states? How to update them correctly?
   **答案 | Answer:** 是，使用展开运算符创建新对象/数组：{...obj, key: value}或[...arr, newItem] | Yes, use spread operator to create new object/array: {...obj, key: value} or [...arr, newItem]

2. useEffect的清理函数何时执行，为什么重要？| When does useEffect's cleanup function execute and why is it important?
   **答案 | Answer:** 组件卸载或下次effect前执行，防止内存泄漏和清理订阅 | Executes on component unmount or before next effect, prevents memory leaks and cleans up subscriptions

3. 自定义Hook必须遵循什么命名约定？| What naming convention must custom Hooks follow?
   **答案 | Answer:** 必须以"use"开头，如useToggle、useCounter | Must start with "use", like useToggle, useCounter

### 步骤 | Steps

1. **设置项目结构 | Setup Project Structure**
   - 创建ThemeContext用于主题管理
   - 创建自定义Hooks：useFormInput、useLocalStorage（模拟）
   - 创建主要组件：ProfileForm、SkillsList、ThemeToggle

2. **实现主题系统 | Implement Theme System**
   - 使用Context提供深色/浅色主题
   - 所有组件响应主题变化
   - 使用useContext消费主题

3. **构建个人信息表单 | Build Profile Form**
   - 使用受控组件管理姓名、邮箱、简介输入
   - 实现实时验证
   - 使用useState管理表单状态

4. **技能列表管理 | Skills List Management**
   - 添加、删除、编辑技能
   - 使用数组状态管理
   - 实现技能等级评分（1-5星）

5. **数据持久化模拟 | Simulate Data Persistence**
   - 使用useEffect在状态变化时记录（控制台）
   - 创建useLocalStorage自定义Hook模拟存储

### 示例代码 | Example Code

```javascript
import React, { useState, useEffect, useContext, createContext } from 'react';
import {
  View,
  Text,
  TextInput,
  Button,
  FlatList,
  Switch,
  StyleSheet,
  SafeAreaView,
  Alert,
} from 'react-native';

/*
 * 个人信息管理应用 | Personal Information Manager
 *
 * 本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
 * - useState：管理表单、列表、主题状态 | useState: manage form, list, theme states
 * - useEffect：数据持久化和日志 | useEffect: data persistence and logging
 * - 自定义Hooks：逻辑复用 | Custom Hooks: logic reuse
 * - 受控组件：表单输入管理 | Controlled components: form input management
 * - useContext：全局主题管理 | useContext: global theme management
 */

// ============================================================
// 1. 主题Context设置 | Theme Context Setup
// ============================================================

const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [isDark, setIsDark] = useState(false);

  const theme = {
    isDark,
    colors: {
      background: isDark ? '#1a1a1a' : '#f5f5f5',
      cardBackground: isDark ? '#2d2d2d' : '#ffffff',
      text: isDark ? '#ffffff' : '#000000',
      textSecondary: isDark ? '#b0b0b0' : '#666666',
      border: isDark ? '#404040' : '#dddddd',
      primary: isDark ? '#4da6ff' : '#007bff',
      danger: '#ff4444',
      success: '#00C851',
    },
    toggleTheme: () => setIsDark(prev => !prev),
  };

  return (
    <ThemeContext.Provider value={theme}>
      {children}
    </ThemeContext.Provider>
  );
}

function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}

// ============================================================
// 2. 自定义Hooks | Custom Hooks
// ============================================================

// 自定义Hook: 管理表单输入 | Custom Hook: manage form input
function useFormInput(initialValue = '', validator = null) {
  const [value, setValue] = useState(initialValue);
  const [error, setError] = useState(null);

  const handleChange = (text) => {
    setValue(text);

    // 验证输入 | Validate input
    if (validator && text) {
      const validationError = validator(text);
      setError(validationError);
    } else {
      setError(null);
    }
  };

  const reset = () => {
    setValue(initialValue);
    setError(null);
  };

  return {
    value,
    error,
    onChange: handleChange,
    reset,
  };
}

// 自定义Hook: 模拟本地存储 | Custom Hook: simulate local storage
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(initialValue);

  // 保存到"存储" | Save to "storage"
  const setValue = (value) => {
    setStoredValue(value);
    console.log(`📦 已保存到存储 [${key}] | Saved to storage [${key}]:`, value);
  };

  // 模拟从存储加载 | Simulate loading from storage
  useEffect(() => {
    console.log(`📂 从存储加载 [${key}] | Loading from storage [${key}]`);
  }, []);

  return [storedValue, setValue];
}

// ============================================================
// 3. 验证函数 | Validation Functions
// ============================================================

const validateEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(email)) {
    return '邮箱格式无效 | Invalid email format';
  }
  return null;
};

const validateName = (name) => {
  if (name.length < 2) {
    return '姓名至少2个字符 | Name must be at least 2 characters';
  }
  return null;
};

// ============================================================
// 4. 组件：个人信息表单 | Component: Profile Form
// ============================================================

function ProfileForm({ onSave }) {
  const { colors } = useTheme();

  // 使用自定义Hook管理表单输入 | Use custom Hook to manage form inputs
  const name = useFormInput('', validateName);
  const email = useFormInput('', validateEmail);
  const [bio, setBio] = useState('');

  // 提交处理 | Submit handler
  const handleSubmit = () => {
    // 验证所有字段 | Validate all fields
    if (!name.value || !email.value) {
      Alert.alert('错误 | Error', '请填写所有必填字段 | Please fill all required fields');
      return;
    }

    if (name.error || email.error) {
      Alert.alert('错误 | Error', '请修正错误后提交 | Please fix errors before submitting');
      return;
    }

    // 保存数据 | Save data
    onSave({
      name: name.value,
      email: email.value,
      bio,
    });

    // 重置表单 | Reset form
    name.reset();
    email.reset();
    setBio('');

    Alert.alert('成功 | Success', '个人信息已保存 | Profile saved successfully');
  };

  return (
    <View style={[styles.card, { backgroundColor: colors.cardBackground }]}>
      <Text style={[styles.cardTitle, { color: colors.text }]}>
        个人信息 | Personal Information
      </Text>

      {/* 姓名输入 | Name input */}
      <View style={styles.inputGroup}>
        <Text style={[styles.label, { color: colors.text }]}>
          姓名 * | Name *
        </Text>
        <TextInput
          style={[
            styles.input,
            { backgroundColor: colors.background, color: colors.text, borderColor: colors.border },
            name.error && styles.inputError,
          ]}
          value={name.value}
          onChangeText={name.onChange}
          placeholder="请输入姓名 | Enter your name"
          placeholderTextColor={colors.textSecondary}
        />
        {name.error && <Text style={styles.errorText}>{name.error}</Text>}
      </View>

      {/* 邮箱输入 | Email input */}
      <View style={styles.inputGroup}>
        <Text style={[styles.label, { color: colors.text }]}>
          邮箱 * | Email *
        </Text>
        <TextInput
          style={[
            styles.input,
            { backgroundColor: colors.background, color: colors.text, borderColor: colors.border },
            email.error && styles.inputError,
          ]}
          value={email.value}
          onChangeText={email.onChange}
          placeholder="your@email.com"
          placeholderTextColor={colors.textSecondary}
          keyboardType="email-address"
          autoCapitalize="none"
          autoCorrect={false}
        />
        {email.error && <Text style={styles.errorText}>{email.error}</Text>}
      </View>

      {/* 简介输入 | Bio input */}
      <View style={styles.inputGroup}>
        <Text style={[styles.label, { color: colors.text }]}>
          简介 | Bio
        </Text>
        <TextInput
          style={[
            styles.input,
            styles.textArea,
            { backgroundColor: colors.background, color: colors.text, borderColor: colors.border },
          ]}
          value={bio}
          onChangeText={setBio}
          placeholder="介绍一下你自己 | Tell us about yourself"
          placeholderTextColor={colors.textSecondary}
          multiline
          numberOfLines={4}
        />
      </View>

      <Button title="保存 | Save" onPress={handleSubmit} color={colors.primary} />
    </View>
  );
}

// ============================================================
// 5. 组件：技能列表 | Component: Skills List
// ============================================================

function SkillsList() {
  const { colors } = useTheme();

  // 技能数组状态 | Skills array state
  const [skills, setSkills] = useState([
    { id: 1, name: 'React Native', level: 4 },
    { id: 2, name: 'JavaScript', level: 5 },
  ]);

  const [newSkill, setNewSkill] = useState('');
  const [newSkillLevel, setNewSkillLevel] = useState(3);

  // 添加技能 | Add skill
  const addSkill = () => {
    if (!newSkill.trim()) {
      Alert.alert('错误 | Error', '请输入技能名称 | Please enter skill name');
      return;
    }

    const skill = {
      id: Date.now(),
      name: newSkill,
      level: newSkillLevel,
    };

    // 创建新数组 | Create new array
    setSkills([...skills, skill]);

    // 重置输入 | Reset input
    setNewSkill('');
    setNewSkillLevel(3);
  };

  // 删除技能 | Remove skill
  const removeSkill = (id) => {
    setSkills(skills.filter(skill => skill.id !== id));
  };

  // 更新技能等级 | Update skill level
  const updateSkillLevel = (id, delta) => {
    setSkills(skills.map(skill => {
      if (skill.id === id) {
        const newLevel = Math.max(1, Math.min(5, skill.level + delta));
        return { ...skill, level: newLevel };
      }
      return skill;
    }));
  };

  // 渲染星级 | Render stars
  const renderStars = (level) => {
    return '⭐'.repeat(level) + '☆'.repeat(5 - level);
  };

  // 渲染技能项 | Render skill item
  const renderSkill = ({ item }) => (
    <View style={[styles.skillItem, { borderBottomColor: colors.border }]}>
      <View style={styles.skillInfo}>
        <Text style={[styles.skillName, { color: colors.text }]}>
          {item.name}
        </Text>
        <Text style={styles.skillStars}>{renderStars(item.level)}</Text>
      </View>

      <View style={styles.skillActions}>
        <Button
          title="-"
          onPress={() => updateSkillLevel(item.id, -1)}
          disabled={item.level === 1}
          color={colors.textSecondary}
        />
        <Button
          title="+"
          onPress={() => updateSkillLevel(item.id, 1)}
          disabled={item.level === 5}
          color={colors.success}
        />
        <Button
          title="删除 | Del"
          onPress={() => removeSkill(item.id)}
          color={colors.danger}
        />
      </View>
    </View>
  );

  return (
    <View style={[styles.card, { backgroundColor: colors.cardBackground }]}>
      <Text style={[styles.cardTitle, { color: colors.text }]}>
        技能列表 | Skills List
      </Text>

      {/* 添加技能表单 | Add skill form */}
      <View style={styles.addSkillForm}>
        <TextInput
          style={[
            styles.input,
            styles.skillInput,
            { backgroundColor: colors.background, color: colors.text, borderColor: colors.border },
          ]}
          value={newSkill}
          onChangeText={setNewSkill}
          placeholder="技能名称 | Skill name"
          placeholderTextColor={colors.textSecondary}
        />

        <View style={styles.levelSelector}>
          <Text style={[styles.levelLabel, { color: colors.text }]}>
            等级 | Level: {newSkillLevel}
          </Text>
          <View style={styles.levelButtons}>
            <Button
              title="-"
              onPress={() => setNewSkillLevel(Math.max(1, newSkillLevel - 1))}
              disabled={newSkillLevel === 1}
            />
            <Button
              title="+"
              onPress={() => setNewSkillLevel(Math.min(5, newSkillLevel + 1))}
              disabled={newSkillLevel === 5}
            />
          </View>
        </View>

        <Button title="添加技能 | Add Skill" onPress={addSkill} color={colors.primary} />
      </View>

      {/* 技能列表 | Skills list */}
      <FlatList
        data={skills}
        renderItem={renderSkill}
        keyExtractor={item => item.id.toString()}
        ListEmptyComponent={
          <Text style={[styles.emptyText, { color: colors.textSecondary }]}>
            暂无技能，添加一个吧！| No skills yet, add one!
          </Text>
        }
      />
    </View>
  );
}

// ============================================================
// 6. 组件：主题切换 | Component: Theme Toggle
// ============================================================

function ThemeToggle() {
  const { isDark, toggleTheme, colors } = useTheme();

  return (
    <View style={[styles.themeToggle, { backgroundColor: colors.cardBackground }]}>
      <Text style={[styles.themeText, { color: colors.text }]}>
        {isDark ? '🌙 深色模式 | Dark Mode' : '☀️ 浅色模式 | Light Mode'}
      </Text>
      <Switch
        value={isDark}
        onValueChange={toggleTheme}
        trackColor={{ false: '#767577', true: colors.primary }}
        thumbColor={isDark ? '#f4f3f4' : '#f4f3f4'}
      />
    </View>
  );
}

// ============================================================
// 7. 主应用组件 | Main App Component
// ============================================================

function PersonalInfoApp() {
  const { colors } = useTheme();

  // 使用自定义Hook模拟持久化存储 | Use custom Hook to simulate persistent storage
  const [profile, setProfile] = useLocalStorage('profile', null);

  // 监听profile变化 | Monitor profile changes
  useEffect(() => {
    if (profile) {
      console.log('✅ 个人信息已更新 | Profile updated:', profile);
    }
  }, [profile]);

  // 组件挂载/卸载日志 | Component mount/unmount logging
  useEffect(() => {
    console.log('🚀 应用已启动 | App mounted');

    return () => {
      console.log('👋 应用已关闭 | App unmounted');
    };
  }, []);

  return (
    <SafeAreaView style={[styles.container, { backgroundColor: colors.background }]}>
      <View style={styles.header}>
        <Text style={[styles.appTitle, { color: colors.text }]}>
          个人信息管理 | Personal Info Manager
        </Text>
        <ThemeToggle />
      </View>

      <ProfileForm onSave={setProfile} />

      {profile && (
        <View style={[styles.card, { backgroundColor: colors.cardBackground }]}>
          <Text style={[styles.cardTitle, { color: colors.text }]}>
            当前信息 | Current Info
          </Text>
          <Text style={[styles.profileText, { color: colors.text }]}>
            姓名 | Name: {profile.name}
          </Text>
          <Text style={[styles.profileText, { color: colors.text }]}>
            邮箱 | Email: {profile.email}
          </Text>
          {profile.bio && (
            <Text style={[styles.profileText, { color: colors.text }]}>
              简介 | Bio: {profile.bio}
            </Text>
          )}
        </View>
      )}

      <SkillsList />
    </SafeAreaView>
  );
}

// ============================================================
// 8. 导出包裹了Provider的应用 | Export App Wrapped with Provider
// ============================================================

export default function App() {
  return (
    <ThemeProvider>
      <PersonalInfoApp />
    </ThemeProvider>
  );
}

// ============================================================
// 样式 | Styles
// ============================================================

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 10,
  },
  header: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 15,
    paddingHorizontal: 10,
  },
  appTitle: {
    fontSize: 20,
    fontWeight: 'bold',
  },
  themeToggle: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 8,
    borderRadius: 8,
    gap: 10,
  },
  themeText: {
    fontSize: 14,
  },
  card: {
    padding: 15,
    borderRadius: 10,
    marginBottom: 15,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 4,
    elevation: 3,
  },
  cardTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 15,
  },
  inputGroup: {
    marginBottom: 15,
  },
  label: {
    fontSize: 14,
    fontWeight: '600',
    marginBottom: 5,
  },
  input: {
    borderWidth: 1,
    padding: 10,
    borderRadius: 6,
    fontSize: 16,
  },
  inputError: {
    borderColor: '#ff4444',
  },
  textArea: {
    height: 80,
    textAlignVertical: 'top',
  },
  errorText: {
    color: '#ff4444',
    fontSize: 12,
    marginTop: 3,
  },
  profileText: {
    fontSize: 14,
    marginBottom: 5,
  },
  addSkillForm: {
    marginBottom: 15,
  },
  skillInput: {
    marginBottom: 10,
  },
  levelSelector: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 10,
  },
  levelLabel: {
    fontSize: 14,
  },
  levelButtons: {
    flexDirection: 'row',
    gap: 5,
  },
  skillItem: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    paddingVertical: 10,
    borderBottomWidth: 1,
  },
  skillInfo: {
    flex: 1,
  },
  skillName: {
    fontSize: 16,
    fontWeight: '500',
    marginBottom: 3,
  },
  skillStars: {
    fontSize: 14,
  },
  skillActions: {
    flexDirection: 'row',
    gap: 5,
  },
  emptyText: {
    textAlign: 'center',
    fontStyle: 'italic',
    marginTop: 10,
  },
});
```

### 项目完成检查 | Project Completion Check

1. 项目是否正确使用useState管理多个独立状态？| Does the project correctly use useState to manage multiple independent states?
   - ✅ ProfileForm中的表单字段
   - ✅ SkillsList中的技能数组
   - ✅ ThemeProvider中的主题状态

2. useEffect的使用是否包含适当的清理函数？| Does useEffect usage include appropriate cleanup functions?
   - ✅ 键盘事件监听器被正确清理
   - ✅ 生命周期日志展示挂载/卸载

3. 是否创建并使用了自定义Hooks？| Were custom Hooks created and used?
   - ✅ useFormInput用于表单输入管理
   - ✅ useLocalStorage模拟数据持久化
   - ✅ useTheme简化Context使用

4. 表单是否使用受控组件模式并包含验证？| Do forms use controlled component pattern with validation?
   - ✅ 所有TextInput组件的value绑定到state
   - ✅ 实时验证邮箱和姓名格式
   - ✅ 错误状态正确显示

5. Context是否正确用于全局主题管理？| Is Context correctly used for global theme management?
   - ✅ ThemeProvider包裹整个应用
   - ✅ 所有组件通过useTheme访问主题
   - ✅ 主题切换触发全局UI更新

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **useState深化练习 | useState Deepening Exercise**
   - **练习描述 | Exercise Description:** 创建一个购物车组件，使用useState管理商品列表（对象数组），支持添加商品、更新数量、删除商品、计算总价
   - **概念检查 | Concept Check:**
     - 更新对象数组中特定项的属性需要创建新数组吗？| Does updating a property of specific item in object array require creating new array?
     - 如何基于当前state计算派生值（如总价）？| How to calculate derived values (like total price) based on current state?
   - **学习目标 | Learning Objective:** 深入理解复杂状态的不可变更新模式

2. **useEffect深化练习 | useEffect Deepening Exercise**
   - **练习描述 | Exercise Description:** 实现一个搜索组件，使用useEffect实现防抖（debounce），在用户停止输入500ms后才执行搜索
   - **概念检查 | Concept Check:**
     - useEffect返回的清理函数在每次重新执行前都会调用吗？| Is useEffect's cleanup function called before each re-execution?
     - 如何利用这个特性实现防抖？| How to leverage this feature to implement debouncing?
   - **学习目标 | Learning Objective:** 掌握useEffect的高级模式和清理机制

3. **自定义Hooks深化练习 | Custom Hooks Deepening Exercise**
   - **练习描述 | Exercise Description:** 创建useWindowDimensions Hook，监听屏幕尺寸变化（使用Dimensions API），返回当前宽高和方向
   - **概念检查 | Concept Check:**
     - 自定义Hook内部需要清理Dimensions监听器吗？| Does custom Hook need to cleanup Dimensions listener inside?
     - 如何确保每个使用此Hook的组件都有独立的状态？| How to ensure each component using this Hook has independent state?
   - **学习目标 | Learning Objective:** 创建处理移动端特定API的自定义Hooks

4. **受控组件深化练习 | Controlled Components Deepening Exercise**
   - **练习描述 | Exercise Description:** 实现一个密码强度检查器，实时显示密码强度（弱/中/强），包含至少8字符、大小写、数字、特殊字符的规则
   - **概念检查 | Concept Check:**
     - 如何在不影响用户输入的情况下实时验证？| How to validate in real-time without affecting user input?
     - 派生状态（密码强度）应该存储在state还是计算？| Should derived state (password strength) be stored in state or calculated?
   - **学习目标 | Learning Objective:** 掌握复杂表单验证和用户体验优化

5. **useContext深化练习 | useContext Deepening Exercise**
   - **练习描述 | Exercise Description:** 创建多语言支持系统（中/英切换），使用Context提供翻译函数和当前语言，所有文本通过翻译键获取
   - **概念检查 | Concept Check:**
     - Context值对象变化会导致所有消费者重新渲染吗？| Does Context value object change cause all consumers to re-render?
     - 如何优化以减少不必要的重新渲染？| How to optimize to reduce unnecessary re-renders?
   - **学习目标 | Learning Objective:** 理解Context的性能影响和优化策略

6. **Hooks组合练习 | Hooks Combination Exercise**
   - **练习描述 | Exercise Description:** 创建一个倒计时器应用，结合useState（剩余时间）、useEffect（定时器）、useRef（避免闭包陷阱）
   - **概念检查 | Concept Check:**
     - 为什么在定时器中直接使用state可能导致闭包问题？| Why does using state directly in timer cause closure problems?
     - useRef如何解决这个问题？| How does useRef solve this problem?
   - **学习目标 | Learning Objective:** 理解Hooks之间的协作和常见陷阱

7. **完整应用练习 | Complete Application Exercise**
   - **练习描述 | Exercise Description:** 扩展实践项目，添加：编辑个人信息功能、技能排序（按名称/等级）、数据导出（JSON格式console.log）、撤销/重做功能
   - **概念检查 | Concept Check:**
     - 实现撤销/重做需要维护什么数据结构？| What data structure is needed to implement undo/redo?
     - 如何确保state更新的不可变性？| How to ensure immutability in state updates?
   - **学习目标 | Learning Objective:** 构建功能完整的React Native应用

## 学习资源 | Learning Resources

- [React Hooks官方文档](https://react.dev/reference/react) | [React Hooks Official Documentation](https://react.dev/reference/react)
- [React Native TextInput组件](https://reactnative.dev/docs/textinput) | [React Native TextInput Component](https://reactnative.dev/docs/textinput)
- [React Native Keyboard API](https://reactnative.dev/docs/keyboard) | [React Native Keyboard API](https://reactnative.dev/docs/keyboard)
- [useState深度指南](https://react.dev/reference/react/useState) | [useState In-Depth Guide](https://react.dev/reference/react/useState)
- [useEffect完整指南](https://overreacted.io/a-complete-guide-to-useeffect/) | [A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)
- [自定义Hooks最佳实践](https://react.dev/learn/reusing-logic-with-custom-hooks) | [Custom Hooks Best Practices](https://react.dev/learn/reusing-logic-with-custom-hooks)

---

✅ **完成检查清单 | Completion Checklist**

- [ ] 理解useState在React Native中与Web React工作方式相同 | Understand useState works identically in React Native and Web React
- [ ] 能够正确更新对象和数组状态，保持不可变性 | Can correctly update object and array states maintaining immutability
- [ ] 掌握useEffect的依赖数组和清理函数机制 | Master useEffect dependency array and cleanup function mechanism
- [ ] 能够处理移动端特定的副作用（键盘事件等）| Can handle mobile-specific side effects (keyboard events, etc.)
- [ ] 创建并使用自定义Hooks复用逻辑 | Create and use custom Hooks to reuse logic
- [ ] 实现受控组件模式并添加实时验证 | Implement controlled component pattern with real-time validation
- [ ] 理解useContext用于全局状态管理 | Understand useContext for global state management
- [ ] 完成个人信息管理应用实践项目 | Complete Personal Info Manager practical project
- [ ] 所有代码示例都能运行并理解其工作原理 | All code examples run and understand how they work
- [ ] 能够向他人解释Hooks规则和最佳实践 | Can explain Hooks rules and best practices to others

**概念掌握验证 | Concept Mastery Verification:**

在标记完成前，请确保能够正确回答本日所有CCQs，并能够清晰解释：
- 为什么React Hooks在React Native中与Web完全相同
- 何时使用函数式更新vs直接状态更新
- useEffect清理函数的重要性
- 自定义Hooks的价值和创建规则
- 受控组件相比非受控组件的优势

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain:
- Why React Hooks work identically in React Native and Web
- When to use functional updates vs direct state updates
- Importance of useEffect cleanup functions
- Value and creation rules of custom Hooks
- Advantages of controlled components over uncontrolled
