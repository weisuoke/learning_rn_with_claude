根据 @prompt/prompt.md 和 @prompt/template_minimal.md 生成内容 

需要参考 @Week08/README.md 中每一天的上下文 和 Learning Path.md

来生成 Day 1的内容

本次会超出output token maximum，拆分成2个markdown文档输出

# Week 08: User Input & Forms in React Native

## Overview

This week, we will master user input and form handling in React Native. Forms on mobile are fundamentally different from web due to virtual keyboards, touch interactions, and platform-specific behaviors. You'll learn to create production-quality forms with validation, proper keyboard handling, and excellent UX. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: TextInput Deep Dive & Keyboard Types
*   **Theory:** Comprehensive study of [TextInput](https://reactnative.dev/docs/textinput) component and all its props. Understand platform differences.
*   **Practice:**
    - Create TextInputs with different `keyboardType`: 'default', 'email-address', 'numeric', 'phone-pad', 'decimal-pad'
    - Use `autoCapitalize`: 'none', 'sentences', 'words', 'characters'
    - Implement `secureTextEntry` for password fields
    - Use `autoCorrect`, `autoComplete`, and `textContentType` (iOS) for better UX
    - Style TextInput: borders, padding, placeholders
    - Handle `onChangeText` vs `onChange` - understand the difference
    - Implement controlled components with `value` prop
