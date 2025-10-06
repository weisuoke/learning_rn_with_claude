# Week 21: Testing - Unit & Component Tests with Testing Library

## Overview

This week, you'll master testing in React Native. You'll write unit tests for business logic, component tests with React Native Testing Library, and prepare for E2E testing. Testing is essential for production apps - it prevents regressions, documents expected behavior, and gives you confidence to refactor. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Portfolio Project: SocialConnect - Week 5/6

**This Week's Goals:**
- Set up Jest for React Native
- Write unit tests for utilities, hooks, and state logic
- Write component tests with React Native Testing Library
- Test user interactions and navigation
- Achieve meaningful test coverage on critical paths

## Daily Plan

### Day 1: Jest Setup & Unit Testing Fundamentals
*   **Theory:** Review [Jest documentation](https://jestjs.io/) and [testing best practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library).
*   **Practice:**
    - Verify Jest is configured (included by default in RN projects)
    - Create `__tests__` folder structure:
      ```
      /src
        /utils
          /__tests__/validators.test.js
        /hooks
          /__tests__/useAuth.test.js
        /components
          /__tests__/PostCard.test.js
      ```
    - Write unit tests for utility functions:
      - Email validation: `validateEmail(email)`
      - Password strength: `checkPasswordStrength(password)`
      - Date formatting: `formatTimestamp(timestamp)`
      - Distance calculation: `calculateDistance(lat1, lon1, lat2, lon2)`
    - Run tests: `npm test` or `yarn test`
    - Check coverage: `npm test -- --coverage`
    - Aim for 100% coverage on utilities

### Day 2: Testing Custom Hooks
*   **Theory:** Learn about `@testing-library/react-hooks` for testing custom hooks (now part of React Testing Library).
*   **Practice:**
    - Install: `npm install --save-dev @testing-library/react-native @testing-library/jest-native`
    - Test `useAuth` hook:
      - Test login action updates state correctly
      - Test logout clears user data
      - Test token persistence
      - Test auto-login on mount
    - Test `usePosts` hook:
      - Test fetching posts updates state
      - Test loading states
      - Test error handling
      - Test pagination logic
    - Test `useCreatePost` hook:
      - Test image upload flow
      - Test optimistic updates
      - Test error rollback
    - Use `renderHook` from React Testing Library
    - Mock AsyncStorage with `@react-native-async-storage/async-storage/jest/async-storage-mock`
    - Mock API calls with `jest.mock()`

### Day 3: Component Testing with React Native Testing Library
*   **Theory:** Study [React Native Testing Library](https://callstack.github.io/react-native-testing-library/). Understand "testing from user's perspective."
*   **Practice:**
    - Set up testing library utilities:
      - Custom render function with providers (Redux, Navigation)
      - Mock navigation
      - Mock state
    - Test simple components:
      - Test Button renders correctly
      - Test Button onPress is called when tapped
      - Test Input displays value and handles changes
    - Use queries:
      - `getByText`, `getByTestId`, `getByRole`
      - `queryBy` for elements that may not exist
      - `findBy` for async elements
    - Test component states:
      - Loading state shows spinner
      - Error state shows error message
      - Success state shows content
    - Use `fireEvent` to simulate user interactions:
      - `fireEvent.press(button)`
      - `fireEvent.changeText(input, 'new text')`

### Day 4: Testing Complex Components - PostCard & Feed
*   **Task:**
    - Test PostCard component:
      - Renders user info, image, caption correctly
      - Like button toggles on press
      - Like count updates
      - Navigate to post detail on comment button press
      - Navigate to profile on username press
    - Test FeedScreen:
      - Renders list of posts
      - Shows loading state while fetching
      - Shows error state on fetch failure
      - Pull-to-refresh refetches data
      - Infinite scroll loads more posts
    - Mock dependencies:
      - Mock FlatList (or use real FlatList but with small data)
      - Mock image components (fast-image, expo-image)
      - Mock navigation
      - Mock Redux/Zustand state
    - Test edge cases:
      - Empty feed (no posts)
      - Single post
      - Post without image
      - Long captions
    - Use `waitFor` for async operations:
      - `await waitFor(() => expect(getByText('Post loaded')).toBeTruthy())`

### Day 5: Testing Forms & User Flows
*   **Task:**
    - Test Login form:
      - Renders email and password inputs
      - Submit button disabled when form invalid
      - Shows validation errors
      - Calls login function on submit
      - Shows loading state during login
      - Shows error message on failure
      - Navigates to app on success
    - Test Registration form:
      - All fields required
      - Password strength indicator works
      - Passwords must match
      - Shows success message on registration
    - Test Create Post flow:
      - Caption input updates state
      - Publish button disabled until image uploaded
      - Shows upload progress
      - Shows success message
    - Use `userEvent` for more realistic interactions (if available)
    - Test form validation logic thoroughly
    - Test async submission flows with `waitFor`

### Day 6: Testing State Management & Navigation
*   **Task:**
    - Test Redux slices/Zustand stores:
      - Test reducers/actions in isolation
      - Test async thunks
      - Test selectors
      - Test initial state
      - Test error handling in thunks
    - Test navigation flows:
      - Mock `@react-navigation/native`:
        ```js
        jest.mock('@react-navigation/native', () => ({
          useNavigation: () => ({ navigate: jest.fn() }),
          useRoute: () => ({ params: {} })
        }))
        ```
      - Test navigation is called with correct params
      - Test navigation guards (auth required)
      - Test deep linking routes
    - Integration tests:
      - Test entire user flows (login → feed → create post)
      - Render entire navigation tree
      - Simulate multi-screen interactions
    - Snapshot testing (use sparingly):
      - Take snapshots of component output
      - Review snapshot changes in PR reviews
      - Update snapshots when intentional changes made

### Day 7: Test Coverage, CI Integration & Best Practices
*   **Task:**
    - **Achieve Test Coverage Goals:**
      - Utils: 100% coverage
      - Hooks: 90%+ coverage
      - Components: 80%+ coverage
      - State management: 90%+ coverage
      - Overall: 70%+ coverage
    - **Write tests for critical paths:**
      - User can register
      - User can login
      - User can view feed
      - User can create post
      - User can like post
      - User can comment
    - **Configure CI for testing:**
      - Create GitHub Actions workflow:
        ```yaml
        - run: npm install
        - run: npm test -- --coverage --watchAll=false
        ```
      - Fail build if tests fail
      - Upload coverage to Codecov (optional)
    - **Testing Best Practices:**
      - Test behavior, not implementation
      - Don't test internal state directly
      - Use semantic queries (getByRole > getByTestId)
      - Avoid snapshot tests for complex components
      - Mock external dependencies (API, navigation)
      - Keep tests DRY with helper functions
    - **Documentation:**
      - Document how to run tests
      - Add testing guidelines to project README
      - Comment complex test setups
    - **Refactoring:**
      - Refactor hard-to-test code
      - Extract logic from components for easier testing
      - Use dependency injection for testability

## Week 21 Deliverables Checklist

- ✅ Jest configured and running
- ✅ Unit tests for all utility functions (100% coverage)
- ✅ Tests for custom hooks
- ✅ Component tests for all major components
- ✅ Tests for forms and user interactions
- ✅ Tests for state management logic
- ✅ Tests for navigation flows
- ✅ 70%+ overall test coverage
- ✅ All tests passing and running in CI
- ✅ Testing documentation in README

## Key Takeaways

By the end of this week, you should:
- ✅ Be proficient with Jest and React Native Testing Library
- ✅ Write effective unit, component, and integration tests
- ✅ Test user interactions and navigation
- ✅ Mock dependencies and external services
- ✅ Achieve meaningful test coverage
- ✅ Integrate testing into CI/CD pipeline

## Resources

- [React Native Testing Library](https://callstack.github.io/react-native-testing-library/)
- [Jest Documentation](https://jestjs.io/)
- [Testing Library Best Practices](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)
- [Testing Recipes](https://reactnative.dev/docs/testing-overview)
- [Mock Service Worker](https://mswjs.io/) - API mocking

## Looking Ahead to Week 22

Next week is the final week of building SocialConnect! You'll add E2E tests with Detox, implement the final features (user search, notifications UI, settings), polish the entire app, and prepare it for deployment. This is where everything comes together into a complete, production-ready application.
