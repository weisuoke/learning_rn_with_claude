# Week 13: Touch Gestures & Animations Basics

## Overview

This week, we will explore touch gestures and animations - the features that make mobile apps feel native and delightful. You'll learn React Native Gesture Handler for complex touch interactions and the Animated API for creating smooth animations. Great gesture and animation UX separates good apps from great apps. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Understanding the Gesture System & React Native Gesture Handler
*   **Theory:** Read about [React Native Gesture Handler](https://docs.swmansion.com/react-native-gesture-handler/). Understand why it's better than the built-in touch system.
*   **Practice:**
    - Install: `npx expo install react-native-gesture-handler`
    - Understand the event lifecycle: onStart → onActive → onEnd → onCancel
    - Implement TapGestureHandler for detecting taps (single, double, long press)
    - Use PanGestureHandler to track finger dragging
    - Extract translation values: `event.nativeEvent.translationX/Y`
    - Implement a draggable box that follows your finger
    - Handle gesture states: BEGAN, ACTIVE, END, FAILED, CANCELLED
    - Replace TouchableOpacity with GestureDetector for better performance

### Day 2: Swipe Gestures & Gesture Composition
*   **Theory:** Learn about directional swipes and combining multiple gestures.
*   **Practice:**
    - Detect swipe direction: left, right, up, down
    - Implement swipe-to-delete on FlatList items (like iOS Mail app)
    - Use Swipeable component for swipe actions (delete, archive, edit)
    - Combine gestures: PanGesture + TapGesture on the same component
    - Implement gesture blocking: prevent ScrollView from scrolling while dragging
    - Create a Tinder-style swipe card interface
    - Use `simultaneousHandlers` and `waitFor` to control gesture interactions

### Day 3: Introduction to Animated API
*   **Theory:** Study the [Animated API](https://reactnative.dev/docs/animated) documentation. Understand Animated.Value and interpolation.
*   **Practice:**
    - Create an Animated.Value: `const opacity = new Animated.Value(0)`
    - Animate it with `Animated.timing()`, `Animated.spring()`, `Animated.decay()`
    - Use Animated.View, Animated.Text, Animated.Image (not regular View!)
    - Implement fade-in animation on component mount
    - Create a scale animation (pulse effect)
    - Chain animations with `Animated.sequence()`
    - Run animations in parallel with `Animated.parallel()`
    - Use `useNativeDriver: true` for 60fps animations

### Day 4: Interpolation & Complex Animations
*   **Theory:** Learn about `interpolate()` to map animated values to different ranges (colors, degrees, positions).
*   **Practice:**
    - Interpolate opacity: input [0, 1] → output [0.3, 1]
    - Interpolate rotation: input [0, 1] → output ['0deg', '360deg']
    - Create a sliding drawer animation (translateY)
    - Implement a rotating loading spinner
    - Animate background color (requires `Animated.createAnimatedComponent()`)
    - Build a progress bar that animates width from 0% to 100%
    - Create a parallax scrolling effect with `Animated.ScrollView`

### Day 5: Combining Gestures with Animations
*   **Theory:** Learn how to drive animations with gesture values for interactive experiences.
*   **Practice:**
    - Drive animation with PanGesture: box follows finger with Animated.event
    - Implement spring-back animation when gesture ends
    - Create a bottom sheet that drags up/down and snaps to positions
    - Build a pull-to-refresh with custom animation
    - Implement a slider component (drag to change value)
    - Create an onboarding carousel with snap-to-page
    - Use `Animated.event` to connect gesture translation to Animated.Value

### Day 6: Project Application - Interactive Animation Gallery
*   **Task:** Build a showcase app demonstrating various gesture and animation patterns:
    - **Home Screen - Animation Demos:**
      - Grid of cards (FlatList), each demonstrates a different animation/gesture
      - Tap a card to navigate to its demo screen
    - **Demo Screens to Implement:**
      1. **Draggable Elements:**
         - Draggable balls that snap back when released
         - Drag to reorder list items
      2. **Swipe Gestures:**
         - Tinder-style swipe cards (swipe left to reject, right to accept)
         - Swipe-to-delete list items
      3. **Animated Transitions:**
         - Fade-in gallery of images
         - Slide-up modal with spring animation
         - Rotating cards flip animation
      4. **Interactive Animations:**
         - Pull-to-refresh with custom loader
         - Expandable/collapsible accordion list
         - Bottom sheet that drags and snaps
      5. **Scroll-Based Animations:**
         - Parallax header that shrinks on scroll
         - Animated tab bar that hides/shows based on scroll direction
         - Sticky header with fade-in title
    - **Technical Requirements:**
      - Use React Native Gesture Handler for all gestures
      - Use Animated API for all animations
      - All animations run at 60fps (useNativeDriver: true where possible)
      - Smooth spring animations (use Animated.spring)
      - Proper gesture state handling
    - **Bonus:**
      - Add haptic feedback on gesture interactions
      - Implement skeleton loading animations
      - Create animated onboarding screens with dot pagination
    - **Goal:** Build a comprehensive animation reference app demonstrating production-quality interactions

### Day 7: Review and Refactor
*   **Task:**
    - Review all gesture and animation code from the week
    - Profile animation performance: use React DevTools Profiler to check frame rates
    - Ensure all animations use `useNativeDriver: true` (except for properties like backgroundColor)
    - Refactor: create reusable animated components (AnimatedCard, SwipeableRow, DraggableItem)
    - Create custom hooks: `useFadeIn()`, `useSlideUp()`, `useDraggable()`
    - Test on lower-end Android device: do animations still run smoothly?
    - Document animation patterns and when to use timing vs spring vs decay
    - Compare Animated API to Reanimated (which you'll learn next week)
    - Prepare for Week 14: Reanimated 2/3 is more powerful but more complex

## Key Takeaways

By the end of this week, you should:
- ✅ Master React Native Gesture Handler for complex touch interactions
- ✅ Implement swipe, drag, tap, and multi-gesture patterns
- ✅ Understand the Animated API and create smooth animations
- ✅ Use interpolation to create complex animated effects
- ✅ Combine gestures with animations for interactive experiences
- ✅ Build animations that run at 60fps with native driver

## Resources

- [React Native Gesture Handler Docs](https://docs.swmansion.com/react-native-gesture-handler/)
- [Animated API Reference](https://reactnative.dev/docs/animated)
- [Gesture Handler Examples](https://github.com/software-mansion/react-native-gesture-handler/tree/main/example)
- [Animation Performance](https://reactnative.dev/docs/performance#use-nativedriver)
- [William Candillon's YouTube](https://www.youtube.com/c/wcandillon) - Excellent animation tutorials

## Looking Ahead to Week 14

Next week, we'll level up with React Native Reanimated 2/3 - the most powerful animation library for React Native. You'll learn worklets, shared values, and how to create complex, performant animations that were impossible with the standard Animated API.
