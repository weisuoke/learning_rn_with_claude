# Week 03: Styling & Flexbox Layout for Mobile Screens

## Overview

This week, we will master React Native's styling system and understand how Flexbox layout works on mobile. While you're familiar with Flexbox from web development, there are key differences in React Native that you need to internalize. You'll learn to create responsive mobile layouts that adapt to different screen sizes. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: StyleSheet API vs Inline Styles vs CSS-in-JS
*   **Theory:** Read about [`StyleSheet` API](https://reactnative.dev/docs/stylesheet). Understand why it exists and its performance benefits over plain objects.
*   **Practice:**
    - Create styles using `StyleSheet.create()` vs plain objects - measure performance (you likely won't notice a difference in small apps)
    - Understand that there is NO CSS - all styling is via JavaScript objects
    - Compare property names: `backgroundColor` (camelCase) vs `background-color` (kebab-case in CSS)
    - Learn which CSS properties are NOT available (e.g., `display: grid`, `float`, `position: fixed`)
    - Experiment with style composition using array syntax: `style={[styles.base, styles.highlighted]}`

### Day 2: Flexbox - The Default Layout System
*   **Theory:** Read [React Native Layout with Flexbox](https://reactnative.dev/docs/flexbox). Focus on the **key differences from web Flexbox**:
    - Default `flexDirection` is `column` (not `row` like web)
    - `flex: 1` behaves differently - it makes a component expand to fill available space
    - No `display: flex` needed - all Views are flex containers by default
*   **Practice:**
    - Create layouts with `flexDirection: 'row'` and `'column'`
    - Use `justifyContent` and `alignItems` to position children
    - Experiment with `flex: 1`, `flex: 2` to create proportional layouts
    - Build a header with left icon, center title, right icon using Flexbox
    - Create a footer with evenly spaced buttons

### Day 3: Positioning and Dimensions
*   **Theory:** Study `position: 'absolute'` and `position: 'relative'` in React Native. Learn about the `Dimensions` API for screen size.
*   **Practice:**
    - Use absolute positioning to overlay elements (e.g., a badge on an image)
    - Calculate positions with `top`, `left`, `right`, `bottom`
    - Get device screen dimensions using `Dimensions.get('window')`
    - Calculate percentage-based widths manually (e.g., 50% of screen width)
    - Create a floating action button (FAB) positioned at bottom-right using absolute positioning

### Day 4: Responsive Design and Platform-Specific Styles
*   **Theory:** Read about [`Platform` module](https://reactnative.dev/docs/platform-specific-code) for detecting iOS vs Android. Learn about the `useWindowDimensions` hook.
*   **Practice:**
    - Use `Platform.OS === 'ios'` to apply iOS-specific styles
    - Create `.ios.js` and `.android.js` file extensions for platform-specific components
    - Use `Platform.select()` to choose styles based on platform
    - Use `useWindowDimensions()` hook to create responsive layouts that adapt to screen rotation
    - Build a component that changes layout from horizontal on tablets to vertical on phones

### Day 5: Shadows, Borders, and Advanced Styling
*   **Theory:** Learn about shadow properties on iOS (`shadowColor`, `shadowOffset`, `shadowOpacity`) vs Android (`elevation`).
*   **Practice:**
    - Create card components with drop shadows that look consistent on iOS and Android
    - Use `borderRadius` to create rounded corners and circular elements
    - Experiment with `borderWidth`, `borderColor`, and `borderStyle`
    - Create gradient backgrounds using `expo-linear-gradient` or a third-party library
    - Build a modern card design with subtle shadows, rounded corners, and proper spacing

### Day 6: Project Application - Dashboard Layout
*   **Task:** Build a mobile dashboard with a complex layout:
    - A fixed header with a logo (left), title (center), and profile icon (right)
    - A scrollable content area with:
      - A stats section showing 3 metric cards in a row (using `flex`)
      - A chart placeholder (full width)
      - A list of recent activities (vertical cards)
    - A bottom tab bar with 4 icons evenly spaced
    - Use `SafeAreaView` for proper spacing on all devices
    - Ensure the layout works in both portrait and landscape orientations
    - Apply platform-specific shadows/elevation to cards
    - **Goal:** Master Flexbox, responsive design, and creating production-quality layouts

### Day 7: Review and Refactor
*   **Task:**
    - Review all styling code from the week - consolidate repeated styles into reusable objects
    - Create a theme object with colors, spacing, and typography constants
    - Refactor the Dashboard app to use the theme for consistency
    - Test on multiple device sizes: iPhone SE, iPhone 14 Pro, iPad simulator
    - Test on Android emulators with different screen densities
    - Write down the top 3 Flexbox "gotchas" that tripped you up this week

## Key Takeaways

By the end of this week, you should:
- ✅ Be proficient with the `StyleSheet` API and React Native styling patterns
- ✅ Understand Flexbox in React Native and its differences from web Flexbox
- ✅ Know how to create responsive layouts that work across device sizes
- ✅ Be able to apply platform-specific styles for iOS and Android
- ✅ Build production-quality UI with proper spacing, shadows, and visual hierarchy

## Resources

- [React Native Styling Cheat Sheet](https://github.com/vhpoet/react-native-styling-cheat-sheet)
- [Flexbox Froggy](https://flexboxfroggy.com/) - Practice Flexbox (web, but mostly applies)
- [React Native Styling Best Practices](https://thoughtbot.com/blog/structure-for-styling-in-react-native)
- [Dimensions API](https://reactnative.dev/docs/dimensions)
- [useWindowDimensions Hook](https://reactnative.dev/docs/usewindowdimensions)

## Looking Ahead to Week 04

Next week, we'll dive into navigation - the most important difference between web and mobile development. You'll learn React Navigation, the industry-standard library for handling screen transitions, tab bars, and navigation state in React Native apps.
