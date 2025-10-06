# Week 05: Advanced Navigation Patterns & State Management

## Overview

This week, we will explore advanced navigation patterns that are essential for production mobile apps: authentication flows, modal screens, and preventing back navigation. You'll also learn how to integrate navigation with global state management and handle the navigation state lifecycle. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Modal Screens & Screen Presentation Styles
*   **Theory:** Read about [Modal screens](https://reactnavigation.org/docs/modal) and different presentation styles (card, modal, transparentModal).
*   **Practice:**
    - Create a modal screen that slides up from the bottom (iOS style)
    - Implement a fullscreen modal for creating new content
    - Use `presentation: 'modal'` in screen options
    - Add a close button to modals (typically top-left "Cancel" or "X")
    - Create a transparent modal overlay (like a bottom sheet)
    - Understand the difference between pushing a screen vs presenting a modal

### Day 2: Authentication Flow Navigation Pattern
*   **Theory:** Read about [Auth Flow pattern](https://reactnavigation.org/docs/auth-flow). Learn the industry-standard approach to handling logged-in vs logged-out states.
*   **Practice:**
    - Create two separate Stack Navigators: `AuthStack` (Login, Signup) and `AppStack` (Main app with tabs)
    - Use conditional rendering at the root level to show AuthStack or AppStack based on auth state
    - Implement a simple auth context with `isSignedIn` state
    - Create Login and Signup screens (UI only, no real authentication yet)
    - Implement sign in/sign out functionality that switches navigation stacks
    - **Key concept:** Navigation structure changes based on authentication state

### Day 3: Preventing Back Navigation & Navigation Guards
*   **Theory:** Learn about [Preventing going back](https://reactnavigation.org/docs/preventing-going-back) and using `beforeRemove` listener.
*   **Practice:**
    - Disable the back button on specific screens using `headerBackVisible: false`
    - Implement a confirmation dialog when user tries to navigate back from a form with unsaved changes
    - Use `navigation.addListener('beforeRemove', ...)` to intercept navigation
    - Create a checkout flow where users can't go back after payment
    - Reset navigation stack using `navigation.reset()` (useful after logout or completing a flow)

### Day 4: Deep Linking & Universal Links
*   **Theory:** Read comprehensive guide on [Deep Linking](https://reactnavigation.org/docs/deep-linking) and [Configuring Links](https://reactnavigation.org/docs/configuring-links).
*   **Practice:**
    - Configure deep linking in `NavigationContainer` with a `linking` config
    - Define URL paths for your screens (e.g., `/article/:id` → ArticleDetail screen)
    - Test deep links in development using `npx uri-scheme open` command
    - Handle initial URL when app is opened from a deep link
    - Parse URL parameters and pass them to the correct screen
    - **Note:** Full implementation with iOS Universal Links and Android App Links requires native configuration (covered later)

### Day 5: Navigation State Persistence & React Navigation + State Management
*   **Theory:** Read about [State Persistence](https://reactnavigation.org/docs/state-persistence) and integrating with Redux/Zustand.
*   **Practice:**
    - Implement navigation state persistence using `AsyncStorage`
    - Restore navigation state when app reopens (user returns to same screen)
    - Access navigation from outside components using `navigationRef`
    - Integrate navigation with global state: navigate based on Redux actions or Zustand state changes
    - Create a notification handler that navigates to specific screens when notification is tapped
    - Use `useFocusEffect` hook to refetch data when screen comes into focus

### Day 6: Project Application - E-commerce App with Advanced Navigation
*   **Task:** Build a mini e-commerce app with production-level navigation:
    - **Authentication Flow:**
      - AuthStack: Login and Signup screens
      - AppStack: Main app (only shown when authenticated)
    - **Main App Structure (Tab Navigator):**
      - Home Tab: Products list → Product Detail (Stack)
      - Cart Tab: Shopping cart screen (modal to checkout)
      - Profile Tab: User profile and settings
    - **Advanced Features:**
      - Product Detail has "Add to Cart" button - shows a success modal
      - Cart screen has "Checkout" button - navigates to modal checkout flow (3 steps, can't go back)
      - Logout button in Profile resets navigation to AuthStack
      - Deep link to specific products: `/product/:id`
      - Persist cart items across app restarts using AsyncStorage
    - **Goal:** Implement authentication flow, modal screens, navigation guards, and state persistence

### Day 7: Review and Refactor
*   **Task:**
    - Review navigation architecture - ensure it's scalable and maintainable
    - Document your navigation structure with a diagram or README
    - Refactor: extract navigation configuration into separate files
    - Add TypeScript types for navigation params (if using TypeScript)
    - Test all navigation flows end-to-end on both platforms
    - Test deep links thoroughly
    - Write a reflection: What navigation patterns will you use in your portfolio project?

## Key Takeaways

By the end of this week, you should:
- ✅ Master advanced navigation patterns used in production apps
- ✅ Implement authentication flows with conditional navigation
- ✅ Handle modal screens, navigation guards, and preventing back navigation
- ✅ Understand deep linking and URL-based navigation
- ✅ Know how to persist and restore navigation state
- ✅ Integrate navigation with global state management

## Resources

- [React Navigation Advanced Guides](https://reactnavigation.org/docs/guide-quick-start)
- [Authentication Flow Example](https://reactnavigation.org/docs/auth-flow)
- [Navigation Lifecycle Events](https://reactnavigation.org/docs/navigation-lifecycle)
- [Navigating Without Navigation Prop](https://reactnavigation.org/docs/navigating-without-navigation-prop)
- [Screen Tracking for Analytics](https://reactnavigation.org/docs/screen-tracking)

## Looking Ahead to Week 06

Next week, you'll consolidate all the skills from Weeks 1-5 by building a complete multi-screen application. This will be a polished news reader app with real API integration, proper navigation, and production-quality UI - your first portfolio-worthy project.
