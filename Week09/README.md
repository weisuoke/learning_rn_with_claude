# Week 09: Networking & Data Fetching for Mobile

## Overview

This week, we will master data fetching in React Native with a focus on mobile-specific challenges: intermittent connectivity, background app states, and optimistic updates. You'll learn to use modern data fetching libraries like TanStack Query (React Query) and RTK Query that provide caching, retry logic, and excellent developer experience. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Fetch API & Axios in React Native
*   **Theory:** Review networking basics. Read about [Networking in React Native](https://reactnative.dev/docs/network). Understand that fetch works the same as web, but mobile has connectivity challenges.
*   **Practice:**
    - Use `fetch()` to make GET, POST, PUT, DELETE requests to a REST API
    - Install and use Axios: `npm install axios`
    - Create an API client service with Axios instance and base configuration
    - Set default headers (Content-Type, Authorization)
    - Implement request and response interceptors for logging and auth tokens
    - Handle different HTTP error status codes (400, 401, 404, 500)
    - Create custom error classes for better error handling

### Day 2: Loading, Error, and Empty States
*   **Theory:** Learn UX best practices for mobile data loading states. Study skeleton screens vs spinners.
*   **Practice:**
    - Implement loading state with `useState` and `useEffect`
    - Show error messages with retry buttons
    - Create empty states when API returns no data
    - Build a custom hook `useFetch(url)` that returns `{ data, loading, error, refetch }`
    - Implement skeleton screens for better perceived performance
    - Use libraries like `react-native-skeleton-placeholder` for skeleton UI
    - Test with intentionally slow API calls (use setTimeout to delay responses)

### Day 3: Network Connectivity Detection
*   **Theory:** Read about [@react-native-community/netinfo](https://github.com/react-native-netinfo/react-native-netinfo) for detecting network status.
*   **Practice:**
    - Install NetInfo: `npx expo install @react-native-community/netinfo`
    - Detect current network state: connected, disconnected, cellular, wifi
    - Listen to network changes with `NetInfo.addEventListener`
    - Show a banner when user goes offline
    - Queue API requests when offline and retry when connection is restored
    - Create a `useNetworkStatus()` hook that provides connection state
    - Prevent API calls when offline and show appropriate message

### Day 4: TanStack Query (React Query) for Mobile
*   **Theory:** Deep dive into [TanStack Query](https://tanstack.com/query/latest/docs/framework/react/overview). Learn why it's perfect for mobile: caching, background refetching, optimistic updates.
*   **Practice:**
    - Install React Query: `npm install @tanstack/react-query`
    - Set up QueryClientProvider at app root
    - Use `useQuery` hook to fetch data with automatic caching
    - Implement `refetchOnWindowFocus` and `refetchOnReconnect` for mobile
    - Use `useMutation` for POST/PUT/DELETE requests
    - Implement optimistic updates: update UI immediately, rollback on error
    - Configure cache time, stale time, and retry logic
    - Use React Query DevTools to visualize cache state

### Day 5: Pagination, Infinite Queries & Prefetching
*   **Theory:** Learn about `useInfiniteQuery` for infinite scroll and data prefetching strategies.
*   **Practice:**
    - Implement pagination with `useQuery` and page state
    - Use `useInfiniteQuery` for infinite scroll with FlatList
    - Connect `useInfiniteQuery` to FlatList's `onEndReached`
    - Implement `getNextPageParam` to determine next page
    - Prefetch data: use `queryClient.prefetchQuery()` to load data before user navigates
    - Implement pull-to-refresh with React Query's `refetch()`
    - Set up background refetching when app comes to foreground

### Day 6: Project Application - Product Catalog with Cart
*   **Task:** Build an e-commerce product catalog with advanced data fetching:
    - **Product List Screen:**
      - Fetch products from a mock API (use [FakeStore API](https://fakestoreapi.com/) or [DummyJSON](https://dummyjson.com/))
      - Implement infinite scroll with `useInfiniteQuery` and FlatList
      - Pull-to-refresh to reload products
      - Category filter: fetch products by category
      - Search functionality with debounced queries
      - Show loading skeletons for first load
    - **Product Detail Screen:**
      - Fetch single product details
      - Prefetch product detail when user scrolls past it in the list
      - Show loading state while fetching
      - "Add to Cart" mutation with optimistic update
    - **Cart Screen:**
      - Display cart items (stored in React Query cache)
      - Update quantity with `useMutation` and optimistic updates
      - Remove items from cart
      - Show total price calculation
    - **Network Features:**
      - Detect offline status with NetInfo
      - Show offline banner when not connected
      - Cache API responses for offline viewing
      - Retry failed requests automatically
    - **Goal:** Build a production-quality app with robust data fetching and offline support

### Day 7: Review and Refactor
*   **Task:**
    - Review all networking code from the week
    - Compare manual fetch/Axios approach vs React Query - identify benefits of React Query
    - Refactor API calls: ensure consistent error handling across the app
    - Optimize React Query configuration: adjust cache times for your use case
    - Test offline scenarios: airplane mode, slow 3G connection
    - Monitor network requests using React Native Debugger
    - Document your API service architecture and data fetching patterns
    - Explore RTK Query as an alternative if you're using Redux

## Key Takeaways

By the end of this week, you should:
- ✅ Master data fetching with Fetch API and Axios in React Native
- ✅ Implement proper loading, error, and empty states
- ✅ Handle network connectivity detection and offline scenarios
- ✅ Use TanStack Query for advanced data fetching with caching and background sync
- ✅ Implement infinite scroll and pagination
- ✅ Build apps with optimistic updates and prefetching

## Resources

- [React Native Networking Docs](https://reactnative.dev/docs/network)
- [TanStack Query Documentation](https://tanstack.com/query/latest/docs/framework/react/overview)
- [React Query for React Native](https://tanstack.com/query/latest/docs/framework/react/react-native)
- [NetInfo Documentation](https://github.com/react-native-netinfo/react-native-netinfo)
- [RTK Query (Redux Toolkit)](https://redux-toolkit.js.org/rtk-query/overview)
- [FakeStore API](https://fakestoreapi.com/)

## Looking Ahead to Week 10

Next week, we'll explore local data persistence with AsyncStorage and more advanced mobile databases like WatermelonDB and Realm. You'll learn when to use each, how to implement offline-first apps, and how to sync local data with remote servers.
