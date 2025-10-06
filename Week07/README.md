# Week 07: Lists & ScrollViews - Handling Dynamic Content

## Overview

This week marks the beginning of Phase 2: Interacting with the Native World. We'll focus on one of the most critical components in mobile development: performant list rendering. You'll learn the difference between ScrollView and FlatList, master the FlatList component for rendering thousands of items efficiently, and understand mobile-specific performance optimizations. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: ScrollView vs FlatList - When to Use Each
*   **Theory:** Read about [ScrollView](https://reactnative.dev/docs/scrollview) and [FlatList](https://reactnative.dev/docs/flatlist). Understand the key difference: ScrollView renders all children immediately, FlatList renders items lazily.
*   **Practice:**
    - Create a ScrollView with 100 items - measure performance and memory usage
    - Convert the same list to use FlatList - observe the performance improvement
    - Use FlatList's `data` and `renderItem` props
    - Add a `keyExtractor` to provide stable keys for list items
    - Understand when to use ScrollView (known, small lists) vs FlatList (large, dynamic lists)

### Day 2: FlatList Basics & Performance Optimization
*   **Theory:** Study [FlatList performance optimization](https://reactnative.dev/docs/optimizing-flatlist-configuration). Learn about `windowSize`, `maxToRenderPerBatch`, and `initialNumToRender`.
*   **Practice:**
    - Create a FlatList with 1000+ items using dummy data
    - Implement `getItemLayout` for fixed-height items to skip measurement
    - Use `removeClippedSubviews` to unmount off-screen components
    - Optimize `renderItem` by extracting it to a separate memoized component
    - Use `React.memo()` on list item components to prevent unnecessary re-renders
    - Monitor performance with React DevTools Profiler

### Day 3: Pull-to-Refresh, Infinite Scroll & Loading States
*   **Theory:** Read about [RefreshControl](https://reactnative.dev/docs/refreshcontrol) and implementing pagination in FlatList.
*   **Practice:**
    - Implement pull-to-refresh using the `refreshing` and `onRefresh` props
    - Add infinite scroll: fetch more data when user reaches the end using `onEndReached`
    - Control when to trigger load more with `onEndReachedThreshold`
    - Show a loading spinner at the bottom using `ListFooterComponent`
    - Show empty state using `ListEmptyComponent` when data array is empty
    - Implement proper loading states: initial load, refreshing, loading more

### Day 4: SectionList for Grouped Data
*   **Theory:** Learn about [SectionList](https://reactnative.dev/docs/sectionlist) for rendering sectioned lists (like iOS Settings app).
*   **Practice:**
    - Convert a flat list to a SectionList with sections
    - Use `sections` prop with section headers and data
    - Customize `renderSectionHeader` to show sticky headers
    - Implement `stickySectionHeadersEnabled` for iOS-style sticky headers
    - Create a contacts list app with alphabetical sections
    - Style section headers differently from regular items

### Day 5: Advanced FlatList Patterns - Horizontal Lists, Grid Layouts
*   **Theory:** Explore horizontal scrolling and multi-column layouts with FlatList.
*   **Practice:**
    - Create a horizontal FlatList using `horizontal={true}`
    - Build a Netflix-style horizontal scrolling row of cards
    - Use `numColumns` prop to create a grid layout
    - Implement a Pinterest-style masonry grid (requires third-party library like `react-native-masonry-list`)
    - Create a stories/carousel component with `snapToInterval` for snap scrolling
    - Handle `onViewableItemsChanged` to track which items are currently visible

### Day 6: Project Application - Social Media Feed
*   **Task:** Build a social media feed application with advanced list features:
    - **Main Feed (FlatList):**
      - Display 100+ posts with images, text, author info, and engagement metrics
      - Each post card shows: avatar, username, post image, caption, like/comment counts
      - Implement pull-to-refresh to reload the feed
      - Infinite scroll: load 20 posts at a time, fetch more when scrolling to bottom
      - Show loading spinner while fetching more posts
    - **Stories Row:**
      - Horizontal FlatList at the top showing user stories (circular avatars)
      - Snap-to-interval scrolling for smooth navigation
    - **Performance Optimizations:**
      - Memoize post components with React.memo
      - Implement `getItemLayout` if posts have fixed height
      - Use `windowSize` and `maxToRenderPerBatch` for optimal performance
    - **Bonus Features:**
      - Search bar at top with filtered results (use a separate FlatList)
      - Tab between "Feed" and "Following" using conditional data
    - **Goal:** Build a production-quality feed that can handle thousands of items smoothly

### Day 7: Review and Refactor
*   **Task:**
    - Review all list implementations from the week
    - Profile the Social Media Feed app using React DevTools - identify bottlenecks
    - Refactor: ensure all list items are properly memoized
    - Test scrolling performance on lower-end Android emulator
    - Compare memory usage between initial implementation and optimized version
    - Document the performance optimizations you applied and their impact
    - Read about [FlashList](https://shopify.github.io/flash-list/) - a more performant alternative to FlatList from Shopify (note for future projects)

## Key Takeaways

By the end of this week, you should:
- ✅ Understand the critical difference between ScrollView and FlatList
- ✅ Be able to render thousands of items efficiently with FlatList
- ✅ Implement pull-to-refresh and infinite scroll pagination
- ✅ Know how to use SectionList for grouped data
- ✅ Master FlatList performance optimizations for production apps
- ✅ Build horizontal lists, grids, and carousels

## Resources

- [FlatList Official Docs](https://reactnative.dev/docs/flatlist)
- [Optimizing FlatList Configuration](https://reactnative.dev/docs/optimizing-flatlist-configuration)
- [SectionList Docs](https://reactnative.dev/docs/sectionlist)
- [FlashList - High-Performance List](https://shopify.github.io/flash-list/)
- [React.memo Documentation](https://react.dev/reference/react/memo)

## Looking Ahead to Week 08

Next week, we'll focus on user input and forms in React Native. You'll learn about TextInput best practices, keyboard handling, form validation, and creating great mobile form experiences - a crucial skill for any mobile developer.
