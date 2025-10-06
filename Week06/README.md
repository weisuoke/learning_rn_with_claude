# Week 06: Building a Multi-Screen Static Application (News Reader Project)

## Overview

This week is a consolidation week where you'll apply everything you've learned in Weeks 1-5 to build a complete, polished news reader application. This project will integrate real API calls, complex navigation, responsive layouts, and production-quality UI. You'll spend the entire week on this single project, treating it like a real client deliverable. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Project Setup & Architecture Planning
*   **Task:**
    - Set up a new Expo project: `npx create-expo-app NewsReader`
    - Install dependencies: React Navigation (Stack + Bottom Tabs), Axios or Fetch API
    - Choose a news API: [NewsAPI.org](https://newsapi.org/) (free tier) or [New York Times API](https://developer.nytimes.com/)
    - Plan your navigation structure:
      - Bottom Tabs: Home, Categories, Bookmarks, Settings
      - Each tab has its own Stack Navigator
    - Create folder structure:
      ```
      /src
        /screens (HomeScreen, ArticleDetailScreen, CategoryScreen, etc.)
        /components (ArticleCard, CategoryButton, etc.)
        /navigation (RootNavigator, HomeStack, etc.)
        /services (api.js for API calls)
        /constants (colors.js, typography.js)
        /utils (helpers.js)
      ```
    - Set up a theme/constants file with consistent colors and spacing

### Day 2: API Integration & Data Fetching
*   **Task:**
    - Create an API service layer in `/services/api.js`
    - Implement functions to fetch:
      - Top headlines: `getTopHeadlines()`
      - Articles by category: `getArticlesByCategory(category)`
      - Search articles: `searchArticles(query)`
    - Create a custom hook `useArticles()` that handles loading states
    - Build the Home screen with:
      - Loading spinner while fetching
      - Error state with retry button
      - Success state showing articles
    - Use `useEffect` to fetch data on screen mount
    - Implement pull-to-refresh using `RefreshControl` in ScrollView

### Day 3: Building the Article List & Detail Screens
*   **Task:**
    - Create an `ArticleCard` component with:
      - Article image (with fallback if no image)
      - Title, description, source, and publish date
      - Pressable to navigate to detail screen
    - Build the `ArticleDetailScreen` with:
      - Full article image
      - Article content
      - Custom header with "Share" and "Bookmark" buttons
      - "Read Full Article" button that opens external URL (use `Linking.openURL()`)
    - Pass article data as navigation params from list to detail screen
    - Implement proper loading states for images
    - Style with cards, shadows, and proper typography hierarchy

### Day 4: Categories & Search Functionality
*   **Task:**
    - Build the Categories screen with:
      - Horizontal scrollable list of category buttons
      - Tapping a category fetches articles for that category
      - Loading and error states
    - Implement search functionality:
      - Search bar using `TextInput` with debouncing (use a custom `useDebounce` hook)
      - Display search results in a list
      - Show "No results" state
      - Clear search button
    - Add navigation between categories and article details
    - Ensure search history is cleared when user navigates away

### Day 5: Bookmarks & Local Storage
*   **Task:**
    - Implement bookmark functionality:
      - Create a bookmarks context using `useContext` and `useReducer`
      - Store bookmarked articles in `AsyncStorage`
      - Load bookmarks on app start
      - Add "Bookmark" button in ArticleDetail screen
      - Show visual feedback when article is bookmarked (filled vs outline icon)
    - Build the Bookmarks screen:
      - Display all bookmarked articles
      - Allow removing bookmarks with swipe gesture (use `react-native-gesture-handler`)
      - Show empty state when no bookmarks exist
    - Persist bookmarks across app restarts

### Day 6: Settings, Polish & Platform Optimization
*   **Task:**
    - Build Settings screen with:
      - Theme selector (Light/Dark mode) - implement basic theme switching
      - Text size preference (Small, Medium, Large)
      - Clear cache button
      - About section with app version
    - Implement dark mode:
      - Create a theme context
      - Update all screens to respect theme
      - Use `useColorScheme()` to detect system preference
    - Polish UI across all screens:
      - Consistent spacing and colors
      - Smooth transitions
      - Platform-specific styling (iOS vs Android)
    - Add loading skeletons instead of spinners for better UX
    - Ensure all images have proper `resizeMode`

### Day 7: Testing, Debugging & Documentation
*   **Task:**
    - Test the entire app end-to-end on both iOS and Android
    - Create a test checklist:
      - ✅ All navigation flows work correctly
      - ✅ Data fetching handles errors gracefully
      - ✅ Bookmarks persist across app restarts
      - ✅ Search debouncing works correctly
      - ✅ Theme switching updates all screens
      - ✅ External links open correctly
      - ✅ Images load with fallbacks
    - Fix any bugs discovered during testing
    - Optimize performance: check for unnecessary re-renders using React DevTools
    - Write a README for the project:
      - Features list
      - Setup instructions
      - Screenshots (take screenshots on simulator)
      - Technologies used
    - Refactor any messy code
    - **Bonus:** Record a demo video of the app in action

## Project Requirements Checklist

Your News Reader app must include:

- ✅ **Navigation:** Bottom Tabs + Stack Navigators in each tab
- ✅ **API Integration:** Fetch real news data from an external API
- ✅ **Screens:** Home, Article Detail, Categories, Search, Bookmarks, Settings (minimum 6 screens)
- ✅ **State Management:** Context API for theme and bookmarks
- ✅ **Local Storage:** AsyncStorage for bookmarks and settings
- ✅ **Responsive UI:** Works on different screen sizes and orientations
- ✅ **Loading States:** Proper handling of loading, error, and empty states
- ✅ **External Links:** Open full articles in browser
- ✅ **Dark Mode:** User can toggle between light and dark themes
- ✅ **Pull-to-Refresh:** Refresh article lists
- ✅ **Search:** Debounced search functionality
- ✅ **Platform Optimization:** Looks native on both iOS and Android

## Key Takeaways

By the end of this week, you should:
- ✅ Have built a complete, production-quality React Native application
- ✅ Be confident integrating external APIs with error handling
- ✅ Understand how to structure a multi-screen app with proper separation of concerns
- ✅ Have experience with AsyncStorage and persistent state
- ✅ Know how to implement theming and user preferences
- ✅ Have your first portfolio-worthy project to showcase

## Resources

- [NewsAPI Documentation](https://newsapi.org/docs)
- [Expo Linking API](https://docs.expo.dev/versions/latest/sdk/linking/)
- [AsyncStorage Guide](https://react-native-async-storage.github.io/async-storage/)
- [RefreshControl](https://reactnative.dev/docs/refreshcontrol)
- [useColorScheme Hook](https://reactnative.dev/docs/usecolorscheme)

## Looking Ahead to Week 07

Next week, we enter Phase 2: Interacting with the Native World. You'll learn to work with FlatList for performant scrolling of large datasets, and start exploring device APIs. This is where React Native truly differentiates itself from web development.
