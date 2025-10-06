# Week 20: Performance Optimization & Debugging with Flipper

## Overview

This week, you'll become proficient at performance optimization and debugging in React Native. You'll use Flipper (Facebook's mobile debugging platform) to profile your app, identify performance bottlenecks, fix memory leaks, and optimize bundle size. You'll also learn advanced debugging techniques to solve complex issues. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Portfolio Project: SocialConnect - Week 4/6

**This Week's Goals:**
- Set up and master Flipper for debugging
- Profile app performance and identify bottlenecks
- Optimize rendering, memory, and bundle size
- Fix any performance issues in SocialConnect
- Implement monitoring and crash reporting

## Daily Plan

### Day 1: Flipper Setup & Introduction to Debugging Tools
*   **Theory:** Read about [Flipper](https://fbflipper.com/) and its plugins. Understand React DevTools, Network Inspector, and Layout Inspector.
*   **Practice:**
    - Install Flipper desktop app
    - Connect your app to Flipper (automatic for new RN projects)
    - Install essential plugins:
      - React DevTools
      - Network
      - Layout
      - Shared Preferences (AsyncStorage viewer)
      - Databases
      - Crash Reporter
      - Performance (Hermes)
    - Explore React DevTools:
      - Inspect component tree
      - View props and state
      - Highlight re-renders
    - Use Network Inspector:
      - Monitor API calls
      - View request/response headers and bodies
      - Measure request duration
      - Mock API responses

### Day 2: Performance Profiling - CPU & Rendering
*   **Task:**
    - Enable Hermes (if not already enabled) for better performance
    - Use React DevTools Profiler:
      - Record a session while scrolling the feed
      - Identify components with high render counts
      - Find unnecessary re-renders
      - Check render duration for each component
    - Use Performance Monitor in React Native:
      - Enable: Shake device → "Show Perf Monitor"
      - Monitor: JS frame rate, UI frame rate, RAM usage, Views
      - Identify screens/actions that cause frame drops
    - Profile SocialConnect:
      - Profile feed scrolling (should be 60fps)
      - Profile post creation flow
      - Profile navigation transitions
    - Document findings:
      - Which screens are slow?
      - Which components re-render unnecessarily?
      - What causes frame drops?

### Day 3: Optimization - Memoization & Re-render Prevention
*   **Task:**
    - Optimize component re-renders:
      - Wrap expensive components with `React.memo()`
      - Use `useMemo()` for expensive calculations
      - Use `useCallback()` for function props
      - Implement proper key props in lists
    - Fix identified issues from Day 2:
      - PostCard re-rendering on every feed update? → Memoize it
      - Timestamp formatting on every render? → useMemo
      - Callbacks causing child re-renders? → useCallback
    - Optimize selectors:
      - Use `reselect` or Zustand's selectors correctly
      - Avoid creating new objects/arrays in selectors
    - Test optimizations:
      - Re-profile with Flipper
      - Verify reduced re-render count
      - Check frame rate improvements
    - Measure impact:
      - Before: X renders per second, Y fps
      - After: X renders per second, Y fps

### Day 4: Memory Profiling & Leak Detection
*   **Task:**
    - Monitor memory usage with Flipper:
      - Navigate through app screens
      - Watch RAM usage in Performance Monitor
      - Memory should not continuously increase
    - Identify memory leaks:
      - Common causes in RN:
        - Event listeners not removed
        - Timers (setTimeout/setInterval) not cleared
        - Subscriptions not cancelled
        - Large images not released
        - Navigation listeners not cleaned up
    - Fix memory leaks in SocialConnect:
      - Check all `useEffect` cleanup functions
      - Ensure navigation listeners are removed
      - Clear intervals/timeouts on unmount
      - Clean up Animated values
    - Optimize images:
      - Use `react-native-fast-image` for better caching
      - Resize images before display
      - Implement progressive loading
      - Clear image cache when memory is low
    - Test on low-end device:
      - App should remain stable with prolonged use
      - Memory should plateau, not continuously grow

### Day 5: Bundle Size Optimization & Code Splitting
*   **Task:**
    - Analyze bundle size:
      - For Expo: use `npx expo export` and check output size
      - For RN CLI: use `react-native-bundle-visualizer`
      - Identify large dependencies
    - Reduce bundle size:
      - Enable Hermes (reduces bundle size by 30-50%)
      - Remove unused dependencies
      - Use module imports instead of full library imports:
        - ❌ `import _ from 'lodash'`
        - ✅ `import debounce from 'lodash/debounce'`
      - Implement code splitting with `React.lazy()` (limited in RN)
      - Lazy load heavy screens
    - Optimize assets:
      - Compress images (use WebP format on Android)
      - Remove unused assets
      - Use vector icons instead of image icons
    - Enable ProGuard (Android) and strip debug symbols
    - Measure results:
      - Before: bundle size X MB
      - After: bundle size Y MB

### Day 6: Crash Reporting & Error Monitoring
*   **Task:**
    - Set up crash reporting:
      - Option 1: [Sentry](https://sentry.io/) for React Native
      - Option 2: [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics)
      - Option 3: [Bugsnag](https://www.bugsnag.com/)
    - Install and configure Sentry:
      - `npm install @sentry/react-native`
      - Initialize in App.js with DSN
      - Test with intentional crash
      - Verify crash appears in Sentry dashboard
    - Implement error boundaries:
      - Create ErrorBoundary component
      - Wrap app or screens with ErrorBoundary
      - Log errors to Sentry
      - Show user-friendly error screen
    - Add custom error tracking:
      - Track failed API calls
      - Track failed image uploads
      - Track navigation errors
      - Add breadcrumbs for debugging
    - Set up source maps:
      - Upload source maps to Sentry for readable stack traces
      - Configure for both iOS and Android
    - Test error scenarios:
      - Trigger errors intentionally
      - Verify they appear in Sentry
      - Check stack traces are readable

### Day 7: Advanced Debugging & Final Optimization Pass
*   **Task:**
    - **Advanced Debugging Techniques:**
      - Use Flipper's Layout Inspector to debug UI issues
      - Use Redux debugger to track state changes
      - Use Network Inspector to debug API issues
      - Use Console plugin for logs
      - Set breakpoints in VS Code (requires config)
    - **Accessibility Audit:**
      - Use Flipper's Accessibility plugin
      - Add accessibility labels to all interactive elements
      - Test with screen reader (TalkBack/VoiceOver)
      - Ensure color contrast meets WCAG standards
    - **Final Performance Pass:**
      - Re-profile entire app with Flipper
      - Run performance tests on low-end Android device
      - Ensure all screens run at 60fps
      - Check memory usage stays stable
      - Verify bundle size is optimized
    - **Create Performance Benchmarks:**
      - Document key metrics:
        - App startup time
        - Feed scroll FPS
        - Image upload time
        - Navigation transition speed
      - Set performance budgets for future development
    - **Code Quality:**
      - Remove all console.logs (use __DEV__ flag for debug logs)
      - Remove commented code
      - Fix all linter warnings
      - Update documentation with performance notes

## Week 20 Deliverables Checklist

- ✅ Flipper set up and integrated with SocialConnect
- ✅ Performance profiling completed (CPU, rendering, memory)
- ✅ Optimizations implemented (memoization, lazy loading)
- ✅ Memory leaks identified and fixed
- ✅ Bundle size optimized (Hermes, tree-shaking, asset compression)
- ✅ Crash reporting set up (Sentry/Crashlytics)
- ✅ Error boundaries implemented
- ✅ App runs at 60fps on mid-range devices
- ✅ Memory usage is stable during prolonged use
- ✅ All critical user flows tested and optimized

## Key Takeaways

By the end of this week, you should:
- ✅ Be proficient with Flipper and React Native debugging tools
- ✅ Know how to identify and fix performance bottlenecks
- ✅ Understand memory management in React Native
- ✅ Be able to optimize bundle size effectively
- ✅ Have production-level monitoring and crash reporting set up
- ✅ Deliver a smooth, performant app experience

## Resources

- [Flipper Documentation](https://fbflipper.com/docs/getting-started/)
- [Hermes Engine](https://hermesengine.dev/)
- [React Native Performance](https://reactnative.dev/docs/performance)
- [Sentry React Native](https://docs.sentry.io/platforms/react-native/)
- [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics/get-started?platform=react-native)
- [React DevTools Profiler](https://react.dev/learn/react-developer-tools)

## Looking Ahead to Week 21

Next week, you'll focus on **Testing** your React Native app. You'll write unit tests for business logic, component tests with React Native Testing Library, and end-to-end tests with Detox. Testing is crucial for production apps and highly valued by employers.
