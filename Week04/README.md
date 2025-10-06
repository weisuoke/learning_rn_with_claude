# Week 04: Navigation Fundamentals with React Navigation

## Overview

This week, we will master React Navigation, the industry-standard navigation library for React Native. Navigation in mobile apps is fundamentally different from web routing - there's no URL bar, and users expect native transitions and gestures. You'll learn Stack, Tab, and Drawer navigators, and understand how to structure multi-screen applications. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Navigation Concepts & Stack Navigator Setup
*   **Theory:** Read the [React Navigation Getting Started guide](https://reactnavigation.org/docs/getting-started). Understand the mental model: screens are components, navigation is via JavaScript, not URLs.
*   **Practice:**
    - Install React Navigation: `npm install @react-navigation/native`
    - Install dependencies: `expo install react-native-screens react-native-safe-area-context`
    - Install Stack Navigator: `npm install @react-navigation/native-stack`
    - Set up `NavigationContainer` in your App.js
    - Create a basic Stack Navigator with 2 screens (Home and Details)
    - Navigate between screens using `navigation.navigate('Details')`

### Day 2: Passing Data Between Screens
*   **Theory:** Read about [Passing parameters to routes](https://reactnavigation.org/docs/params). Learn how navigation params work vs URL query strings in web.
*   **Practice:**
    - Pass parameters when navigating: `navigation.navigate('Details', { itemId: 42 })`
    - Access params in the target screen using `route.params`
    - Update params using `navigation.setParams()`
    - Create a list screen that navigates to a detail screen with the selected item's data
    - Implement a "Go Back" button using `navigation.goBack()`

### Day 3: Header Customization & Options
*   **Theory:** Study [Header configuration options](https://reactnavigation.org/docs/headers). Learn about static vs dynamic header options.
*   **Practice:**
    - Set screen titles using the `options` prop or `navigation.setOptions()`
    - Customize header style (background color, text color)
    - Add header buttons (right button, left button) using `headerRight` and `headerLeft`
    - Hide the header for specific screens with `headerShown: false`
    - Create a dynamic header title that updates based on screen state

### Day 4: Tab Navigator - Bottom Tabs
*   **Theory:** Read about [Bottom Tab Navigator](https://reactnavigation.org/docs/bottom-tab-navigator). Understand when to use tabs vs stack navigation.
*   **Practice:**
    - Install: `npm install @react-navigation/bottom-tabs`
    - Create a Tab Navigator with 3-4 tabs (Home, Search, Profile)
    - Add icons to tabs using a library like `@expo/vector-icons`
    - Customize tab bar appearance (active color, inactive color, background)
    - Combine Stack and Tab navigators: each tab can have its own stack
    - **Key pattern:** Nest Stack Navigators inside Tab Navigator screens

### Day 5: Drawer Navigator & Deep Linking Basics
*   **Theory:** Read about [Drawer Navigator](https://reactnavigation.org/docs/drawer-navigator) and [Deep Linking](https://reactnavigation.org/docs/deep-linking).
*   **Practice:**
    - Install: `npm install @react-navigation/drawer` and `react-native-gesture-handler react-native-reanimated`
    - Create a Drawer Navigator with menu items
    - Customize the drawer content with a custom component
    - Open the drawer programmatically with `navigation.openDrawer()`
    - Set up basic deep linking configuration (understand how it works, don't worry about full implementation yet)

### Day 6: Project Application - Multi-Screen News App
*   **Task:** Build a "News Reader" app with complex navigation:
    - **Tab Navigator** as the root with 3 tabs:
      - Home Tab: Stack Navigator → Home Screen → Article Detail Screen
      - Categories Tab: Stack Navigator → Categories List → Category Articles → Article Detail
      - Profile Tab: Single screen with user settings
    - Home screen shows a list of articles (use dummy data)
    - Tapping an article navigates to Article Detail screen with the article data passed as params
    - Article Detail screen has a custom header with a "Share" button (just console.log for now)
    - Categories tab has multiple categories; tapping one shows articles for that category
    - **Bonus:** Add a Drawer Navigator that wraps the Tab Navigator with "Settings" and "About" menu items
    - **Goal:** Practice nesting navigators, passing params, and building real-world navigation flows

### Day 7: Review and Refactor
*   **Task:**
    - Review your navigation structure - ensure it follows React Navigation best practices
    - Draw a diagram of your navigation hierarchy (Root → Tabs → Stacks → Screens)
    - Refactor navigation code: extract screen options into separate objects for reusability
    - Test navigation flows: can you reach every screen? Do back buttons work correctly?
    - Test on both iOS and Android - note differences in transition animations
    - Read about [Navigation Lifecycle](https://reactnavigation.org/docs/navigation-lifecycle) - understand `focus` and `blur` events

## Key Takeaways

By the end of this week, you should:
- ✅ Be proficient with React Navigation's Stack, Tab, and Drawer navigators
- ✅ Understand how to pass data between screens using params
- ✅ Know how to nest navigators to create complex navigation hierarchies
- ✅ Be able to customize headers and navigation UI
- ✅ Recognize navigation patterns used in production mobile apps

## Resources

- [React Navigation Official Docs](https://reactnavigation.org/)
- [React Navigation Fundamentals](https://reactnavigation.org/docs/navigating)
- [Navigation Patterns](https://reactnavigation.org/docs/navigation-patterns)
- [Common Navigation Mistakes](https://reactnavigation.org/docs/common-mistakes)
- [TypeScript Guide for React Navigation](https://reactnavigation.org/docs/typescript)

## Looking Ahead to Week 05

Next week, we'll explore advanced navigation patterns like modal screens, authentication flows, and navigation state persistence. You'll also learn how to integrate navigation with state management libraries and handle deep linking properly.
