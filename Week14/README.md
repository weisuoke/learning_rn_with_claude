# Week 14: Advanced Animations with Reanimated

## Overview

This week, we will master React Native Reanimated - the most powerful animation library in the React Native ecosystem. Reanimated allows you to create complex, 60fps animations that run entirely on the native thread using worklets. You'll learn shared values, animated styles, gesture-driven animations, and advanced techniques used in production apps. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Reanimated Fundamentals - Shared Values & Worklets
*   **Theory:** Read [Reanimated 3 Fundamentals](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/getting-started/). Understand the concept of the UI thread vs JS thread.
*   **Practice:**
    - Install: `npx expo install react-native-reanimated`
    - Configure babel.config.js: add `react-native-reanimated/plugin`
    - Create a shared value: `const width = useSharedValue(100)`
    - Understand worklets: functions that run on the UI thread (marked with 'worklet')
    - Use `useAnimatedStyle` to create animated styles
    - Animate with `withTiming()`, `withSpring()`, `withDecay()`
    - Update shared values: `width.value = withSpring(200)`
    - Create a box that expands when tapped using Reanimated

### Day 2: Animated Styles & Transforms
*   **Theory:** Learn about all animatable style properties and transform operations.
*   **Practice:**
    - Animate multiple properties: width, height, opacity, backgroundColor
    - Use transform animations: translateX, translateY, scale, rotate
    - Combine multiple transforms in a single animation
    - Implement a card flip animation (rotateY from 0deg to 180deg)
    - Create a bouncing ball animation with `withRepeat()` and `withSequence()`
    - Use `interpolate()` for complex value mappings
    - Implement color interpolation for smooth color transitions
    - Create a loading skeleton with shimmer effect

### Day 3: Gesture-Driven Animations with Reanimated
*   **Theory:** Study [Gesture Handler integration with Reanimated](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/handling-gestures/).
*   **Practice:**
    - Use `useAnimatedGestureHandler` to handle Pan gestures
    - Track finger position in real-time with `translationX/Y`
    - Implement a draggable element that follows your finger
    - Add snap-back animation when gesture ends with `withSpring()`
    - Create boundaries: prevent dragging outside screen bounds with `clamp()`
    - Implement velocity-based animations with `withDecay()`
    - Build a throwable item that continues moving after release based on velocity
    - Use `runOnJS()` to trigger JavaScript functions from the UI thread

### Day 4: Layout Animations & Entering/Exiting Animations
*   **Theory:** Learn about [Layout Animations](https://docs.swmansion.com/react-native-reanimated/docs/layout-animations/layout-animations/) in Reanimated.
*   **Practice:**
    - Use entering animations: `FadeIn`, `SlideInLeft`, `ZoomIn`, `BounceIn`
    - Use exiting animations: `FadeOut`, `SlideOutRight`, `ZoomOut`
    - Apply layout animations when list items are added/removed
    - Use `Layout.springify()` for smooth layout transitions
    - Implement animated item reordering in a list
    - Create a notification toast with slide-in and auto-dismiss animations
    - Build an animated onboarding flow with page transitions

### Day 5: Advanced Patterns - Shared Element Transitions
*   **Theory:** Explore shared element transitions between screens (similar to Hero animations on web).
*   **Practice:**
    - Implement basic shared element transition: image from list to detail screen
    - Use `useAnimatedRef` to reference elements
    - Measure element positions with `measure()`
    - Animate element from start position to end position
    - Handle shared element transitions during navigation
    - Create a photo gallery with zoom transition on tap
    - Implement an app-store-style card expansion animation
    - **Note:** Consider using libraries like `react-native-shared-element` for production

### Day 6: Project Application - Advanced UI Interactions App
*   **Task:** Build an app showcasing advanced Reanimated patterns:
    - **Animated Onboarding (3-4 screens):**
      - Swipe horizontally to navigate between screens
      - Dot pagination that animates based on scroll position
      - Parallax effect: background images move slower than foreground
      - Skip button that fades in after first screen
      - "Get Started" button on last screen with spring animation
    - **Gesture-Driven Bottom Sheet:**
      - Drag from bottom to open sheet
      - Snap to positions: closed (0%), half-open (50%), fully-open (100%)
      - Backdrop overlay that fades in/out based on sheet position
      - Handle quick swipes (velocity-based snapping)
      - Dismiss by dragging down or tapping backdrop
    - **Animated List:**
      - Items enter with stagger effect (FadeInDown with delay)
      - Swipe-to-delete with spring animation
      - Deleted items exit with SlideOutLeft
      - Drag-to-reorder functionality (hold and drag to reorder)
      - Add new item button with scale animation on press
    - **Photo Gallery with Transitions:**
      - Grid of photos (3 columns)
      - Tap photo to expand to fullscreen with shared element transition
      - Pinch to zoom in fullscreen mode
      - Swipe horizontally to navigate between photos in fullscreen
      - Close button returns to grid with reverse transition
    - **Advanced Interactions:**
      - Pull-to-refresh with custom animation (rotate + scale)
      - Floating Action Button (FAB) that hides/shows based on scroll direction
      - Parallax header that collapses on scroll with sticky title
    - **Technical Requirements:**
      - All animations run at 60fps on the UI thread
      - Use worklets for all animations
      - Proper gesture handling with Gesture Handler + Reanimated
      - Smooth spring physics for natural feel
    - **Goal:** Build a portfolio-quality app demonstrating mastery of advanced animations

### Day 7: Review and Refactor
*   **Task:**
    - Review all Reanimated code from the week
    - Profile performance: ensure all animations run at 60fps
    - Compare Reanimated to Animated API: when to use each?
    - Refactor: create reusable animated components (AnimatedBottomSheet, SwipeableCard, etc.)
    - Create custom hooks: `useSharedTransition()`, `useGestureAnimation()`
    - Test on lower-end Android device to ensure performance
    - Document advanced animation patterns you learned
    - Read Reanimated best practices and common pitfalls
    - Prepare animation components library for future projects

## Key Takeaways

By the end of this week, you should:
- ✅ Master React Native Reanimated for high-performance animations
- ✅ Understand worklets and the UI thread vs JS thread
- ✅ Create gesture-driven animations that feel native
- ✅ Implement layout animations and entering/exiting effects
- ✅ Build complex UI patterns like bottom sheets, onboarding, parallax
- ✅ Know when to use Reanimated vs the standard Animated API

## Resources

- [Reanimated 3 Documentation](https://docs.swmansion.com/react-native-reanimated/)
- [Reanimated Examples](https://github.com/software-mansion/react-native-reanimated/tree/main/app/src/examples)
- [William Candillon - Can it be done in React Native?](https://www.youtube.com/c/wcandillon)
- [Catalin Miron - Animation Tutorials](https://www.youtube.com/c/catalinmirondev)
- [Shared Element Transitions](https://github.com/IjzerenHein/react-native-shared-element)

## Looking Ahead to Week 15

Next week marks the beginning of Phase 3: Building Scalable Applications. You'll dive deep into state management with Redux Toolkit, learning how to structure state for large-scale mobile apps, handle complex data flows, and integrate Redux with React Native's unique challenges.

**Congratulations on completing Phase 2!** You now have mastery over native device APIs, animations, and gestures - the features that make mobile apps truly feel native. In Phase 3, you'll learn to build production-scale applications with proper architecture, testing, and deployment.
