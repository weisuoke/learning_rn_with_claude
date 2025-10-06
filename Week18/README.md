# Week 18: Building the Feed - FlatList Optimization

## Overview

This week, you'll build the heart of SocialConnect: the feed. You'll implement a highly optimized, infinite-scrolling feed that can handle thousands of posts smoothly. This involves FlatList optimization, pull-to-refresh, pagination, and post interactions. By the end of the week, your app will feel like a real social network. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Portfolio Project: SocialConnect - Week 2/6

**This Week's Goals:**
- Build an optimized, infinite-scrolling feed
- Implement post display with images, captions, engagement metrics
- Add pull-to-refresh and pagination
- Create like/unlike functionality with optimistic updates
- Build comment viewing (creation comes next week)

## Daily Plan

### Day 1: Feed Data Model & API Integration
*   **Task:**
    - Design post data structure:
      ```js
      {
        id, userId, username, userAvatar,
        content, imageUrl, imageWidth, imageHeight,
        likesCount, commentsCount, timestamp,
        isLikedByMe
      }
      ```
    - Create posts API service:
      - `fetchPosts(page, limit)` - fetch paginated posts
      - `likePost(postId)` - like a post
      - `unlikePost(postId)` - unlike a post
      - `fetchComments(postId)` - fetch post comments
    - Set up posts state management:
      - Redux slice or Zustand store
      - State: `posts`, `loading`, `error`, `hasMore`, `page`
      - Actions: `fetchPosts`, `likePost`, `unlikePost`, `addPost`
    - Implement posts fetching with pagination
    - Use Firebase/Supabase or mock data for development
    - Normalize posts data if using Redux (EntityAdapter)

### Day 2: Post Card Component & Feed Screen
*   **Task:**
    - Create PostCard component:
      - User info header: avatar, username, timestamp
      - Post image (if exists) with proper aspect ratio
      - Caption text (with "Read More" for long captions)
      - Engagement bar: like button, comment button, share button
      - Like count and comment count
      - "View all comments" link
    - Style PostCard:
      - Clean, Instagram-like design
      - Proper spacing and typography
      - Shadows for cards
      - Tap areas large enough for fingers
    - Create FeedScreen:
      - FlatList of PostCard components
      - Initial loading state (skeleton screens)
      - Empty state when no posts
      - Error state with retry button
    - Optimize PostCard with `React.memo()`
    - Extract sub-components: PostHeader, PostImage, PostEngagement

### Day 3: FlatList Optimization for Performance
*   **Task:**
    - Implement performance optimizations:
      - `keyExtractor={(item) => item.id}`
      - `getItemLayout` if posts have fixed heights
      - `maxToRenderPerBatch={10}`
      - `windowSize={5}`
      - `removeClippedSubviews={true}` (Android)
      - `initialNumToRender={5}`
    - Optimize images:
      - Use `resizeMode="cover"`
      - Implement progressive image loading (blur-to-sharp)
      - Add image caching headers
      - Consider using `expo-image` or `react-native-fast-image`
    - Memoize expensive calculations:
      - Format timestamps with useMemo
      - Memoize selectors in Redux/Zustand
    - Profile with React DevTools:
      - Check render count
      - Identify unnecessary re-renders
      - Optimize parent components

### Day 4: Pull-to-Refresh & Infinite Scroll
*   **Task:**
    - Implement pull-to-refresh:
      - Add `refreshing` state
      - Add `onRefresh` handler that fetches page 1
      - Show RefreshControl spinner
      - Reset `page` to 1 on refresh
      - Clear existing posts before loading new ones
    - Implement infinite scroll:
      - Add `onEndReached` handler
      - Fetch next page when user scrolls to bottom
      - Set `onEndReachedThreshold={0.5}` (trigger halfway from bottom)
      - Show loading spinner in `ListFooterComponent`
      - Stop fetching when `hasMore === false`
      - Handle rapid scroll events (debounce if needed)
    - Test pagination:
      - Verify no duplicate posts
      - Check that page increments correctly
      - Test with slow network (throttle in DevTools)

### Day 5: Like Functionality with Optimistic Updates
*   **Task:**
    - Implement like button UI:
      - Heart icon (outline when not liked, filled when liked)
      - Animate heart on tap (scale + color change)
      - Update like count immediately
    - Add optimistic updates:
      - When user taps like:
        1. Immediately update UI (toggle heart, increment count)
        2. Send API request in background
        3. If request fails, revert UI changes
        4. Show error toast if like fails
    - Update posts state:
      - Toggle `isLikedByMe` field
      - Update `likesCount` (+1 or -1)
    - Implement double-tap to like:
      - Use Gesture Handler to detect double tap
      - Show heart animation on image
      - Same optimistic update logic
    - Handle edge cases:
      - Prevent multiple rapid taps (debounce)
      - Queue likes when offline (sync later)

### Day 6: Comments Preview & Navigation
*   **Task:**
    - Add comments preview to PostCard:
      - Show first 2 comments below post
      - Format: "**username** comment text"
      - "View all X comments" button if more exist
      - Tapping navigates to PostDetailScreen
    - Create PostDetailScreen:
      - Show full post at top
      - Scrollable list of all comments below
      - Comment input at bottom (implementation next week)
      - Navigate here from:
        - Tapping "View comments"
        - Tapping comment button
        - Tapping comment count
    - Fetch comments for post:
      - Load when PostDetailScreen mounts
      - Show loading state
      - Handle empty state (no comments yet)
    - Create CommentItem component:
      - Avatar, username, comment text
      - Timestamp
      - Like button on comment (optional)
    - Optimize comments list (FlatList, memoization)

### Day 7: Polish, Testing & Performance Review
*   **Task:**
    - **Visual Polish:**
      - Add subtle animations (fade-in for new posts)
      - Improve loading skeletons
      - Add haptic feedback on like (Haptics.impactAsync())
      - Ensure consistent spacing and alignment
      - Test dark mode if implemented
    - **Performance Testing:**
      - Test with 1000+ posts in feed
      - Measure FPS while scrolling (use Flipper or Perf Monitor)
      - Check memory usage (should stay stable)
      - Test on lower-end Android device
      - Optimize any performance bottlenecks found
    - **Functionality Testing:**
      - Test pull-to-refresh multiple times
      - Test infinite scroll to end
      - Test like/unlike multiple posts
      - Test offline like (queue and sync)
      - Test rapid scrolling
      - Test navigation to post details
    - **Error Handling:**
      - Handle image load failures (show placeholder)
      - Handle API errors gracefully
      - Add retry logic for failed requests
      - Show user-friendly error messages
    - **Code Quality:**
      - Refactor duplicated code
      - Add PropTypes or TypeScript types
      - Write comments for complex logic
      - Remove debug code and console.logs
    - **Documentation:**
      - Update README with Week 18 progress
      - Take screenshots of the feed
      - Document any technical decisions

## Week 18 Deliverables Checklist

- ✅ Feed screen with optimized FlatList of posts
- ✅ PostCard component with user info, image, caption, engagement
- ✅ Pull-to-refresh functionality
- ✅ Infinite scroll pagination
- ✅ Like/unlike with optimistic updates and animations
- ✅ Comments preview on posts
- ✅ PostDetailScreen with full comment list
- ✅ Performance optimizations (60fps scrolling with 1000+ posts)
- ✅ Image optimization and caching
- ✅ Error handling and loading states
- ✅ Smooth animations and haptic feedback

## Key Takeaways

By the end of this week, you should have:
- ✅ Mastery of FlatList optimization techniques
- ✅ Experience implementing optimistic updates
- ✅ Understanding of pagination and infinite scroll patterns
- ✅ A production-quality feed that rivals real social apps
- ✅ Portfolio-worthy feature to showcase in interviews

## Resources

- [FlatList Performance](https://reactnative.dev/docs/optimizing-flatlist-configuration)
- [React.memo Documentation](https://react.dev/reference/react/memo)
- [Optimistic Updates Pattern](https://redux-toolkit.js.org/rtk-query/usage/optimistic-updates)
- [Expo Image](https://docs.expo.dev/versions/latest/sdk/image/)
- [React Native Fast Image](https://github.com/DylanVann/react-native-fast-image)

## Looking Ahead to Week 19

Next week, you'll implement the **Create Post** feature with camera and image upload. Users will be able to create posts with photos from their camera or gallery, add captions, and publish to the feed. You'll also add the ability to create comments on posts.
