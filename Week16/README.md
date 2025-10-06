# Week 16: Alternative State Management - Zustand & Jotai

## Overview

This week, we will explore modern, lightweight alternatives to Redux: Zustand and Jotai. These libraries offer simpler APIs with less boilerplate while maintaining power and flexibility. You'll learn when to choose each state management solution and understand the trade-offs. By the end of the week, you'll make an informed decision about which to use for your portfolio project. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Zustand Fundamentals
*   **Theory:** Read [Zustand documentation](https://github.com/pmndrs/zustand). Understand its philosophy: minimal API, no providers, no boilerplate.
*   **Practice:**
    - Install: `npm install zustand`
    - Create a store with `create()`: `const useStore = create((set) => ({...}))`
    - Access state in components: `const count = useStore(state => state.count)`
    - Update state: `set((state) => ({ count: state.count + 1 }))`
    - Use `get()` to access current state in actions
    - Create a counter app with Zustand - notice the simplicity vs Redux
    - No Provider needed - stores are just hooks!

### Day 2: Zustand Advanced Patterns
*   **Theory:** Learn about slices, middleware, and persistence in Zustand.
*   **Practice:**
    - Split large stores into slices for better organization
    - Use Zustand middleware: `persist`, `devtools`, `immer`
    - Persist state to AsyncStorage with `persist` middleware
    - Enable Redux DevTools integration with `devtools` middleware
    - Use `immer` middleware for mutable state updates
    - Create async actions (no special async handling needed - just use async functions)
    - Implement selectors with `useShallow` to prevent unnecessary re-renders
    - Compare Zustand vs Redux: 90% less code for the same functionality

### Day 3: Zustand for Mobile - Real-World Patterns
*   **Theory:** Study mobile-specific Zustand patterns: offline-first, optimistic updates, normalized state.
*   **Practice:**
    - Build an auth store with login/logout, token storage, persist to AsyncStorage
    - Create a posts store with CRUD operations
    - Implement optimistic updates: update UI immediately, rollback on error
    - Add loading and error states to stores
    - Create derived state with selectors (computed values)
    - Implement store subscriptions for side effects (e.g., navigate on logout)
    - Use `subscribeWithSelector` middleware for targeted subscriptions
    - Handle API calls within Zustand actions

### Day 4: Jotai Introduction - Atomic State Management
*   **Theory:** Read [Jotai documentation](https://jotai.org/). Understand atomic state management: bottom-up approach vs top-down (Redux/Zustand).
*   **Practice:**
    - Install: `npm install jotai`
    - Create atoms with `atom()`: `const countAtom = atom(0)`
    - Read atoms: `const [count] = useAtom(countAtom)`
    - Write atoms: `const [, setCount] = useAtom(countAtom)`
    - Create derived atoms (like computed values): `atom((get) => get(countAtom) * 2)`
    - Understand atom composition: build complex state from simple atoms
    - Use write-only atoms for actions
    - No Provider needed for basic usage (optional for advanced scenarios)

### Day 5: Jotai Advanced - Async Atoms & Utils
*   **Theory:** Learn about async atoms, atom families, and Jotai utilities.
*   **Practice:**
    - Create async atoms that fetch data
    - Use Suspense boundaries with async atoms
    - Implement atom families for parametric atoms (e.g., `userAtom(userId)`)
    - Use `atomWithStorage` for AsyncStorage persistence
    - Reset atoms with `RESET` symbol
    - Use `useAtomValue` (read-only) and `useSetAtom` (write-only) for optimization
    - Implement `atomWithReducer` for complex state logic
    - Build a small app with Jotai - compare to Zustand and Redux

### Day 6: Project Application - State Management Comparison
*   **Task:** Build the **same app three times** using different state management solutions:
    - **App Requirements (Todo App with Auth):**
      - Login screen (mock auth)
      - Todo list screen (fetch from API)
      - Add, edit, delete, toggle todos
      - Filter: All, Active, Completed
      - Persist auth state
      - Offline support: cache todos
      - Loading and error states
    - **Implementation 1: Redux Toolkit**
      - Full Redux setup with slices, thunks, persist
      - Normalized state with EntityAdapter
      - RTK Query for API calls (optional)
    - **Implementation 2: Zustand**
      - Zustand stores for auth and todos
      - Persist middleware for AsyncStorage
      - DevTools middleware
      - Async actions in stores
    - **Implementation 3: Jotai**
      - Atoms for auth, todos, filters
      - Async atoms for data fetching
      - atomWithStorage for persistence
      - Derived atoms for filtered todos
    - **Comparison Task:**
      - Measure lines of code for each implementation
      - Compare bundle size impact
      - Evaluate developer experience: which was fastest to build?
      - Assess performance: re-render frequency
      - Consider maintainability for large apps
      - Document pros/cons of each approach
    - **Goal:** Make an informed decision about which state management solution to use for your portfolio project (Weeks 17-22)

### Day 7: Review and Architectural Decision
*   **Task:**
    - Review all three implementations side-by-side
    - Create a comparison table:
      | Feature | Redux Toolkit | Zustand | Jotai |
      |---------|---------------|---------|-------|
      | Lines of Code | | | |
      | Learning Curve | | | |
      | DevTools Support | | | |
      | Async Handling | | | |
      | Persistence | | | |
      | Performance | | | |
      | Community/Ecosystem | | | |
    - Consider your portfolio project needs:
      - How complex is the state?
      - Do you need time-travel debugging?
      - Team size and preferences?
      - Mobile-specific requirements?
    - **Make your decision:** Which will you use for your portfolio project?
    - Document your decision with reasoning
    - Refactor the best implementation: add TypeScript types, error boundaries, edge case handling
    - Prepare to start portfolio project next week with your chosen state management

## Key Takeaways

By the end of this week, you should:
- ✅ Master Zustand for lightweight, flexible state management
- ✅ Understand Jotai's atomic state management approach
- ✅ Know when to choose Redux vs Zustand vs Jotai vs Context API
- ✅ Implement persistence, async actions, and optimistic updates in any library
- ✅ Make informed architectural decisions based on project requirements
- ✅ Be ready to build scalable apps with your preferred state management solution

## Resources

- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [Jotai Documentation](https://jotai.org/)
- [State Management Comparison](https://leerob.io/blog/react-state-management)
- [When to Use Redux vs Zustand](https://www.reddit.com/r/reactjs/comments/n8cqkp/redux_vs_zustand/)
- [Atomic State Management](https://jotai.org/docs/introduction)

## Looking Ahead to Week 17

Next week, you'll begin your **Portfolio Capstone Project** - a full-featured social media application built over 6 weeks (Weeks 17-22). Week 17 focuses on the Authentication Module: user registration, login, JWT tokens, protected routes, and user profiles. Get ready to build a production-quality app!
