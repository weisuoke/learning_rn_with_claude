# Week 15: State Management at Scale - Redux Toolkit for Mobile

## Overview

This week marks the beginning of Phase 3: Building Scalable Applications. You'll master Redux Toolkit for managing complex application state in React Native. While you're familiar with Redux from web development, you'll learn mobile-specific patterns: offline-first state, persisting state across app restarts, and optimizing Redux for mobile performance. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Redux Toolkit Setup & Basics
*   **Theory:** Review [Redux Toolkit documentation](https://redux-toolkit.js.org/). Understand why Redux Toolkit is the modern standard (eliminates boilerplate, includes Immer, DevTools integration).
*   **Practice:**
    - Install: `npm install @reduxjs/toolkit react-redux`
    - Create a Redux store with `configureStore()`
    - Create a slice with `createSlice()` - includes reducers and actions automatically
    - Set up Provider at app root: `<Provider store={store}>`
    - Use `useSelector()` to read state
    - Use `useDispatch()` to dispatch actions
    - Create a counter app with Redux Toolkit to verify setup
    - Set up Redux DevTools for debugging

### Day 2: Slices & Complex State Structure
*   **Theory:** Learn best practices for structuring Redux state in a large mobile app. Understand feature-based slice organization.
*   **Practice:**
    - Create multiple slices: `authSlice`, `userSlice`, `postsSlice`, `uiSlice`
    - Organize slices by feature, not by type
    - Use `createEntityAdapter` for normalized state (list of items with IDs)
    - Implement CRUD operations: add, update, remove items from state
    - Combine slices in the store configuration
    - Use selectors with `createSelector` for memoization and derived state
    - Structure state: separate loading states per feature, not global loading state

### Day 3: Async Logic with createAsyncThunk
*   **Theory:** Study [`createAsyncThunk`](https://redux-toolkit.js.org/api/createAsyncThunk) for handling async operations like API calls.
*   **Practice:**
    - Create an async thunk to fetch data: `fetchPosts = createAsyncThunk(...)`
    - Handle three states: pending, fulfilled, rejected in `extraReducers`
    - Set loading, success, and error states based on thunk lifecycle
    - Dispatch thunks from components with `useEffect` or button press
    - Pass arguments to thunks (e.g., `fetchUserById(userId)`)
    - Chain thunks: dispatch one after another completes
    - Cancel in-flight requests when component unmounts
    - Implement retry logic for failed thunks

### Day 4: Redux Persist - State Persistence
*   **Theory:** Learn [redux-persist](https://github.com/rt2zz/redux-persist) for persisting Redux state to AsyncStorage.
*   **Practice:**
    - Install: `npm install redux-persist`
    - Configure persistor with AsyncStorage
    - Wrap store with `persistStore()`
    - Wrap app with `PersistGate` to delay render until state is rehydrated
    - Configure which slices to persist (e.g., persist auth, don't persist UI state)
    - Use blacklist/whitelist to control what's persisted
    - Handle state migrations when your state shape changes across app versions
    - Test persistence: close and reopen app, verify state is restored

### Day 5: RTK Query - Data Fetching & Caching
*   **Theory:** Explore [RTK Query](https://redux-toolkit.js.org/rtk-query/overview) - Redux Toolkit's powerful data fetching solution (alternative to React Query).
*   **Practice:**
    - Set up an API slice with `createApi()`
    - Define endpoints: `getPost`, `getPosts`, `createPost`, `updatePost`, `deletePost`
    - Use auto-generated hooks: `useGetPostsQuery()`, `useCreatePostMutation()`
    - Understand automatic caching and refetching
    - Configure cache invalidation with tags
    - Implement optimistic updates with mutations
    - Handle loading and error states with RTK Query
    - Compare RTK Query vs React Query - when to use each?

### Day 6: Project Application - Social Media App with Redux
*   **Task:** Build a social media app with comprehensive Redux state management:
    - **State Structure (Slices):**
      - `authSlice`: user authentication, tokens, isLoggedIn
      - `userSlice`: current user profile, settings
      - `postsSlice`: feed posts (normalized with EntityAdapter)
      - `commentsSlice`: comments on posts
      - `uiSlice`: theme, loading states, modals
    - **Features to Implement:**
      1. **Authentication Flow:**
         - Login/Signup screens (UI only, mock API)
         - Store auth token in Redux
         - Persist auth state with redux-persist
         - Conditional navigation based on `isLoggedIn` state
         - Logout clears auth state
      2. **Posts Feed:**
         - Fetch posts with `createAsyncThunk` or RTK Query
         - Display in FlatList with loading/error states
         - Pull-to-refresh refetches posts
         - Infinite scroll pagination
         - Like/unlike posts (optimistic updates)
      3. **Create Post:**
         - Form to create new post
         - Dispatch action to add post to state
         - Optimistically add to feed, then sync with API
         - Show success message
      4. **Comments:**
         - Fetch comments for a post
         - Add comment with optimistic update
         - Store comments in normalized state
      5. **User Profile:**
         - Display user info from Redux state
         - Edit profile updates Redux state
         - Changes persist across app restarts
    - **Technical Requirements:**
      - All data managed in Redux Toolkit
      - Redux Persist for auth and user data
      - Normalized state for posts and comments (EntityAdapter)
      - Async thunks for all API calls with proper loading states
      - Selectors for derived data (e.g., post count, liked posts)
    - **Goal:** Build a production-scale app with robust Redux architecture

### Day 7: Review and Refactor
*   **Task:**
    - Review all Redux code from the week
    - Profile Redux performance: use React DevTools to check for unnecessary re-renders
    - Use `React.memo()` and memoized selectors to optimize
    - Refactor: ensure slices follow best practices (feature-based, normalized state)
    - Document Redux architecture: create a state diagram showing all slices
    - Add TypeScript types for Redux state and actions (if using TypeScript)
    - Test Redux logic: write unit tests for reducers and selectors
    - Compare Redux Toolkit vs Context API - when to use each?
    - Decide: Redux Toolkit vs Zustand for your portfolio project (Week 16 explores Zustand)

## Key Takeaways

By the end of this week, you should:
- ✅ Master Redux Toolkit for managing complex application state
- ✅ Structure Redux state for large-scale mobile apps
- ✅ Handle async operations with createAsyncThunk and RTK Query
- ✅ Persist Redux state across app restarts with redux-persist
- ✅ Implement optimistic updates and normalized state
- ✅ Build production-quality apps with scalable state management

## Resources

- [Redux Toolkit Official Docs](https://redux-toolkit.js.org/)
- [Redux Essentials Tutorial](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)
- [RTK Query Documentation](https://redux-toolkit.js.org/rtk-query/overview)
- [Redux Persist GitHub](https://github.com/rt2zz/redux-persist)
- [Redux Performance Best Practices](https://redux.js.org/usage/deriving-data-selectors)

## Looking Ahead to Week 16

Next week, we'll explore alternative state management solutions: Zustand, Jotai, and MobX. You'll learn when to choose each, compare them to Redux, and understand the trade-offs. This will help you make informed architectural decisions for your portfolio project.
