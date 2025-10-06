# Week 01: Environment Setup & Understanding React Native Architecture

## Overview

This week, we will focus on setting up your React Native development environment and understanding the fundamental architecture differences between React for web and React Native. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Choosing Your Path - Expo vs React Native CLI
*   **Theory:** Read the official React Native documentation on [Environment Setup](https://reactnative.dev/docs/environment-setup). Understand the key differences between Expo and React Native CLI. Watch [Expo vs React Native CLI comparison video](https://www.youtube.com/results?search_query=expo+vs+react+native+cli+2024).
*   **Practice:**
    - Install Node.js (if not already installed)
    - Install Expo CLI: `npm install -g expo-cli`
    - Create your first Expo app: `npx create-expo-app@latest MyFirstApp`
    - Run the app on iOS simulator (Mac) and/or Android emulator
    - Explore the project structure - note how it differs from a React web project

### Day 2: Development Tools & Simulators
*   **Theory:** Read about [React Native Debugger tools](https://reactnative.dev/docs/debugging). Learn about React DevTools for mobile and how to use the in-app developer menu.
*   **Practice:**
    - Install Xcode (Mac) or Android Studio (Mac/Windows/Linux)
    - Set up iOS Simulator and Android Emulator
    - Install React Native Debugger or learn to use Chrome DevTools with your app
    - Practice hot reloading: make changes to `App.js` and see them reflect instantly
    - Trigger the dev menu (Cmd+D on iOS, Cmd+M on Android) and explore its options

### Day 3: Understanding the React Native Architecture
*   **Theory:** Read about [React Native's New Architecture](https://reactnative.dev/docs/the-new-architecture/landing-page) and the Bridge concept. Watch a video explaining how JavaScript communicates with native code.
*   **Practice:**
    - Create a simple component that renders "Hello Mobile World"
    - Use `console.log()` and observe where the output appears (terminal vs browser console)
    - Compare rendering time between web React and React Native
    - Read about the Metro bundler - how it differs from Webpack

### Day 4: Core Components - The Equivalents to HTML
*   **Theory:** Read the [Core Components documentation](https://reactnative.dev/docs/components-and-apis). Study the mapping: `div` → `View`, `span`/`p` → `Text`, `img` → `Image`.
*   **Practice:**
    - Replace any web HTML elements you'd normally use with React Native equivalents
    - Create a component with nested `View` components (like nested divs)
    - Try to use `Text` outside a `Text` component and observe the error - understand that **all text must be wrapped**
    - Render an image from a URL and from local assets using the `Image` component

### Day 5: Props, State, and Hooks - What Stays the Same
*   **Theory:** Quick review of React hooks if needed. Read about any mobile-specific considerations for `useState`, `useEffect`, `useContext`.
*   **Practice:**
    - Build a simple counter app using `useState` - notice it works exactly like React web
    - Use `useEffect` to log component mount/unmount lifecycle events
    - Create a custom hook (e.g., `useToggle`) and use it in a React Native component
    - Build a simple form with a `TextInput` and controlled component pattern

### Day 6: Project Application - Personal Card App
*   **Task:** Build a "Personal Card" mobile application that displays:
    - Your name, photo, and a short bio
    - A list of your skills (using multiple `Text` components)
    - A button that toggles between light and dark mode (changes background color)
    - All content should be in separate components (Header, Bio, SkillsList, ThemeToggle)
    - **Goal:** Practice component composition, state management (theme toggle), and using Core Components

### Day 7: Review and Refactor
*   **Task:**
    - Review all code from the week - ensure it follows React best practices
    - Refactor the Personal Card app: extract reusable components, add PropTypes or TypeScript types
    - Test the app on both iOS and Android simulators - note any visual differences
    - Write a brief reflection: What surprised you most about React Native this week? What's still unclear?
    - Catch up on any exercises from Days 1-5 that you didn't complete

## Key Takeaways

By the end of this week, you should:
- ✅ Have a fully functional React Native development environment
- ✅ Understand the conceptual differences between React DOM and React Native
- ✅ Be comfortable using Core Components instead of HTML elements
- ✅ Know that your existing React knowledge (hooks, props, state) transfers directly
- ✅ Have built and tested your first mobile app on simulators

## Resources

- [React Native Official Docs](https://reactnative.dev/)
- [Expo Documentation](https://docs.expo.dev/)
- [React Native Express](https://www.reactnative.express/) - Great visual guide
- [Metro Bundler Documentation](https://facebook.github.io/metro/)

## Looking Ahead to Week 02

Next week, we'll dive deeper into Core Components and start learning about styling and layout in React Native - including Flexbox, which works slightly differently on mobile than on web.
