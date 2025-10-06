# Week 02: Core Components - Mapping Web Elements to Mobile

## Overview

This week, we will master React Native's Core Components library and learn how to think in terms of mobile UI primitives rather than HTML elements. You'll discover the mobile-specific components for scrolling, handling user input, and displaying lists. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Views and Containers
*   **Theory:** Deep dive into the [`View` component documentation](https://reactnative.dev/docs/view). Understand when to use `View` vs `ScrollView` vs `SafeAreaView`.
*   **Practice:**
    - Create nested `View` layouts with different background colors to visualize the component tree
    - Use `SafeAreaView` to handle iPhone notches and Android system bars
    - Build a card component using `View` with borders and shadows (via `style` prop)
    - Compare behavior of `View` with `overflow: 'hidden'` vs web's div overflow

### Day 2: Text Components and Typography
*   **Theory:** Read about the [`Text` component](https://reactnative.dev/docs/text) and its props. Learn about font loading in React Native (system fonts vs custom fonts).
*   **Practice:**
    - Create different text styles (heading, body, caption) using the `Text` component
    - Implement text nesting - put a `Text` inside another `Text` with different styles
    - Try to style text with `fontWeight`, `fontSize`, `color`, `textAlign`
    - **Important:** Try to put text directly inside a `View` without wrapping it in `Text` - observe the error

### Day 3: Images and Media
*   **Theory:** Study the [`Image` component](https://reactnative.dev/docs/image). Understand `resizeMode`, local vs remote images, and image caching.
*   **Practice:**
    - Load images from URLs using `source={{ uri: 'https://...' }}`
    - Import and use local images with `require('./assets/image.png')`
    - Experiment with different `resizeMode` values: 'cover', 'contain', 'stretch', 'center'
    - Build an avatar component with a circular image (using `borderRadius`)
    - Handle loading states for remote images

### Day 4: ScrollView and User Interaction
*   **Theory:** Read [`ScrollView` documentation](https://reactnative.dev/docs/scrollview). Learn about the `Button` and `Pressable` components for user interaction.
*   **Practice:**
    - Create a `ScrollView` with content taller than the screen
    - Use horizontal `ScrollView` to create a side-scrolling image gallery
    - Implement a `Button` with an `onPress` handler (compare to web's `onClick`)
    - Use `Pressable` to create a custom button with press feedback (opacity change, scale animation)
    - Learn about `TouchableOpacity`, `TouchableHighlight` - legacy components you'll see in codebases

### Day 5: TextInput and Forms
*   **Theory:** Study [`TextInput` component](https://reactnative.dev/docs/textinput). Learn about keyboard types, autocorrect, and form handling on mobile.
*   **Practice:**
    - Create controlled `TextInput` components with `value` and `onChangeText`
    - Experiment with `keyboardType`: 'email-address', 'numeric', 'phone-pad'
    - Style `TextInput` with borders, padding, and placeholder text
    - Handle form submission with a button press
    - Build a simple login form (email + password) with validation

### Day 6: Project Application - Recipe Card App
*   **Task:** Build a "Recipe Viewer" application that displays a list of recipes in a scrollable view:
    - Use `ScrollView` to make the entire screen scrollable
    - Each recipe card should contain:
      - An `Image` (recipe photo)
      - `Text` for recipe name and description
      - A "View Recipe" `Pressable` button that logs to console
    - Include a `SafeAreaView` to handle device-specific spacing
    - Add a horizontal `ScrollView` at the top showing category tags
    - **Bonus:** Add a simple search `TextInput` that filters recipes (client-side only)
    - **Goal:** Practice using multiple Core Components together in a realistic mobile UI

### Day 7: Review and Refactor
*   **Task:**
    - Review all code from the week - identify any web-specific thinking you need to unlearn
    - Refactor the Recipe app: create reusable components (RecipeCard, CategoryTag, SearchBar)
    - Test on both iOS and Android - note differences in default styling of `TextInput` and `Button`
    - Experiment with the app on different screen sizes (iPhone SE vs iPhone Pro Max simulators)
    - Document any "gotchas" you discovered (e.g., Text wrapping, Image sizing quirks)

## Key Takeaways

By the end of this week, you should:
- ✅ Be fluent in using `View`, `Text`, `Image`, `ScrollView`, and `TextInput`
- ✅ Understand the constraint that all text must be wrapped in `Text` components
- ✅ Know how to handle user input and button presses
- ✅ Be comfortable building multi-component mobile layouts
- ✅ Recognize the mobile-specific considerations (SafeAreaView, keyboard types)

## Resources

- [React Native Core Components API](https://reactnative.dev/docs/components-and-apis)
- [Image Performance Best Practices](https://reactnative.dev/docs/images)
- [Handling Touches](https://reactnative.dev/docs/handling-touches)
- [Expo Image Component](https://docs.expo.dev/versions/latest/sdk/image/) - Enhanced alternative

## Looking Ahead to Week 03

Next week, we'll focus on styling and layout. You'll learn how React Native's Flexbox implementation differs from web, master the `StyleSheet` API, and build responsive mobile layouts that work across different device sizes.
