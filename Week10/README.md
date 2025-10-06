# Week 10: AsyncStorage & Local Data Persistence

## Overview

This week, we will master local data persistence in React Native. Mobile apps need to store data locally for offline access, user preferences, and caching. You'll learn AsyncStorage for simple key-value storage and explore more advanced database options for complex data models. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: AsyncStorage Fundamentals
*   **Theory:** Read about [@react-native-async-storage/async-storage](https://react-native-async-storage.github.io/async-storage/). Understand it's an asynchronous, persistent key-value storage system.
*   **Practice:**
    - Install AsyncStorage: `npx expo install @react-native-async-storage/async-storage`
    - Store data: `AsyncStorage.setItem('key', 'value')`
    - Retrieve data: `AsyncStorage.getItem('key')`
    - Remove data: `AsyncStorage.removeItem('key')`
    - Clear all data: `AsyncStorage.clear()`
    - **Important:** AsyncStorage only stores strings - must `JSON.stringify()` objects
    - Create a helper module with `setObject()` and `getObject()` functions
    - Handle AsyncStorage errors with try/catch

### Day 2: Building a Storage Hook & Persisting User Preferences
*   **Theory:** Learn patterns for integrating AsyncStorage with React state using custom hooks.
*   **Practice:**
    - Create a custom hook `useAsyncStorage(key, defaultValue)` that:
      - Returns `[value, setValue]` like `useState`
      - Persists to AsyncStorage on every update
      - Loads initial value from AsyncStorage on mount
    - Build a theme preference system:
      - Store user's theme choice (light/dark) in AsyncStorage
      - Load theme on app start and apply it
      - Update theme and persist when user changes it
    - Store user settings: language, text size, notification preferences
    - Implement a "Clear Cache" button that removes specific keys

### Day 3: Caching API Responses & Offline-First Patterns
*   **Theory:** Learn offline-first architecture patterns for mobile apps. Understand cache invalidation strategies.
*   **Practice:**
    - Fetch data from API, store response in AsyncStorage
    - On subsequent app opens, show cached data immediately, then fetch fresh data
    - Implement "cache-first, then network" pattern
    - Add timestamps to cached data to determine freshness
    - Invalidate cache after a certain time (e.g., 1 hour, 1 day)
    - Queue write operations when offline, sync when connection restored
    - Create a `CacheManager` class to handle all caching logic

### Day 4: Advanced Storage - MMKV for High Performance
*   **Theory:** Learn about [react-native-mmkv](https://github.com/mrousavy/react-native-mmkv), a faster alternative to AsyncStorage.
*   **Practice:**
    - Install MMKV: `npm install react-native-mmkv` (requires rebuild for bare React Native, Expo with development build)
    - Compare MMKV vs AsyncStorage performance with 1000 write/read operations
    - Use MMKV for frequently accessed data (auth tokens, user session)
    - Implement encryption for sensitive data with MMKV's built-in encryption
    - Migrate existing AsyncStorage data to MMKV
    - Create a unified storage interface that works with both AsyncStorage and MMKV

### Day 5: Introduction to Mobile Databases - WatermelonDB & Realm
*   **Theory:** Read about [WatermelonDB](https://nozbe.github.io/WatermelonDB/) and [Realm](https://www.mongodb.com/docs/realm/sdk/react-native/). Understand when to use a database vs key-value storage.
*   **Practice:**
    - Compare storage solutions:
      - AsyncStorage/MMKV: Simple key-value, user preferences, tokens
      - SQLite/WatermelonDB: Relational data, complex queries, large datasets
      - Realm: Object-oriented database, real-time sync capabilities
    - Install WatermelonDB and set up a simple schema (e.g., Todo items)
    - Create, read, update, delete (CRUD) operations with WatermelonDB
    - Query data with filters and sorting
    - Understand observers and reactive queries (auto-update UI when data changes)
    - **Note:** Full implementation next week; today is exposure and comparison

### Day 6: Project Application - Offline Note-Taking App
*   **Task:** Build a comprehensive note-taking app with offline-first architecture:
    - **Core Features:**
      - Create, edit, delete notes (title + content)
      - Notes stored locally in AsyncStorage
      - Notes persist across app restarts
      - Real-time save (debounced) as user types
      - Last edited timestamp for each note
      - Search notes by title or content
    - **Storage Implementation:**
      - Store notes array in AsyncStorage as JSON
      - Implement auto-save with 1-second debounce
      - Show "Saving..." indicator while writing to storage
      - Handle storage errors gracefully
    - **Advanced Features:**
      - Sort notes by: date created, date modified, alphabetically
      - Archive notes (move to separate archived list)
      - Undo delete functionality (keep deleted notes in trash for 7 days)
      - Export all notes as JSON file
      - Import notes from JSON
      - Dark mode preference persisted in AsyncStorage
    - **UI/UX:**
      - Notes list screen (FlatList)
      - Note editor screen (multiline TextInput)
      - Empty state when no notes exist
      - Swipe to delete gesture (use react-native-gesture-handler)
      - Pull-to-refresh to force reload from storage
    - **Goal:** Build a production-quality offline app with robust data persistence

### Day 7: Review and Refactor
*   **Task:**
    - Review all storage implementations from the week
    - Benchmark AsyncStorage performance: test large data sets (1000+ notes)
    - Identify potential issues: What happens if storage is full? How to handle?
    - Refactor the note-taking app: create a `StorageService` abstraction layer
    - Add data validation: ensure data integrity when reading from storage
    - Implement migration logic: how would you handle schema changes in future updates?
    - Test edge cases: what if AsyncStorage.setItem fails? Implement retry logic
    - Document your storage architecture and patterns

## Key Takeaways

By the end of this week, you should:
- ✅ Master AsyncStorage for key-value data persistence
- ✅ Build custom hooks for seamless state persistence
- ✅ Implement offline-first patterns and cache API responses
- ✅ Know when to use AsyncStorage vs MMKV vs SQLite/Realm
- ✅ Handle storage errors and edge cases
- ✅ Build offline apps with excellent UX

## Resources

- [AsyncStorage Documentation](https://react-native-async-storage.github.io/async-storage/)
- [MMKV GitHub](https://github.com/mrousavy/react-native-mmkv)
- [WatermelonDB Docs](https://nozbe.github.io/WatermelonDB/)
- [Realm React Native](https://www.mongodb.com/docs/realm/sdk/react-native/)
- [Offline First Patterns](https://offlinefirst.org/)

## Looking Ahead to Week 11

Next week, we'll dive into device APIs - specifically the camera and photo library. You'll learn to access native device features, handle permissions, and integrate media into your apps. This is where React Native truly shows its power over web technologies.
