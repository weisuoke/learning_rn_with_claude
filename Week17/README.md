# Week 17: Portfolio Project Kickoff - Authentication Module

## Overview

This week marks the beginning of your **Portfolio Capstone Project**: "SocialConnect" - a full-featured social media application. Over the next 6 weeks, you'll build a production-quality app that demonstrates all the skills you've learned. Week 17 focuses on the foundation: user authentication, protected routes, profile management, and project architecture. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Project: SocialConnect - A Modern Social Media App

**Final Product Features:**
- User authentication (JWT-based)
- User profiles with avatars and bios
- Feed of posts with images
- Create posts with camera/gallery
- Like and comment on posts
- Real-time updates
- Offline support
- Push notifications
- Production deployment

## Daily Plan

### Day 1: Project Setup & Architecture Planning
*   **Task:**
    - Create new project: `npx create-expo-app SocialConnect` or React Native CLI
    - Install core dependencies:
      - Navigation: `@react-navigation/native`, `@react-navigation/stack`, `@react-navigation/bottom-tabs`
      - State management: Redux Toolkit/Zustand (your choice from Week 16)
      - HTTP client: `axios`
      - Forms: `formik` or `react-hook-form`
      - UI: `react-native-paper` or build custom components
    - Set up project structure:
      ```
      /src
        /screens (Auth, Home, Profile, Post, etc.)
        /components (Button, Input, Card, etc.)
        /navigation (AuthNavigator, AppNavigator, RootNavigator)
        /services (api.js, auth.js, storage.js)
        /store or /state (Redux slices or Zustand stores)
        /hooks (useAuth, usePosts, etc.)
        /utils (validators, formatters, etc.)
        /constants (colors, typography, API endpoints)
        /assets (images, fonts)
      ```
    - Choose and set up backend:
      - Option 1: Firebase (Authentication, Firestore, Storage)
      - Option 2: Supabase (open-source Firebase alternative)
      - Option 3: Custom Node.js backend (if you have one)
      - Option 4: Mock API with json-server for development
    - Document your tech stack choices in README.md

### Day 2: Authentication Backend Integration
*   **Task:**
    - Set up Firebase Authentication or your chosen backend
    - Create API service layer in `/services/api.js`:
      - `register(email, password, name)`
      - `login(email, password)`
      - `logout()`
      - `refreshToken()`
      - `getCurrentUser()`
    - Implement token storage in AsyncStorage (or MMKV)
    - Create auth utilities:
      - `getToken()` - retrieve stored token
      - `setToken(token)` - store token
      - `clearToken()` - remove token on logout
    - Add axios interceptors:
      - Request interceptor: attach auth token to headers
      - Response interceptor: handle 401 errors (token expired)
    - Test all auth endpoints with Postman or in-app

### Day 3: Authentication State Management
*   **Task:**
    - Create auth state management (Redux slice or Zustand store):
      - State: `user`, `token`, `isAuthenticated`, `loading`, `error`
      - Actions: `loginUser`, `registerUser`, `logoutUser`, `loadUser`
    - Implement async login flow:
      - Dispatch login action
      - Call API, wait for response
      - On success: store token, update user state
      - On error: show error message
    - Persist auth state with redux-persist or Zustand persist middleware
    - Implement auto-login on app start (check for stored token)
    - Create `useAuth()` custom hook for easy access to auth state
    - Handle loading states during auth operations

### Day 4: Auth Screens UI - Login & Registration
*   **Task:**
    - Create Login Screen:
      - Email input (with email keyboard type)
      - Password input (secureTextEntry)
      - "Login" button (disabled while loading)
      - "Forgot Password?" link
      - "Don't have an account? Sign Up" link
      - Loading spinner during login
      - Error message display
    - Create Registration Screen:
      - Full name input
      - Email input with validation
      - Password input with strength indicator
      - Confirm password input
      - "Create Account" button
      - Terms and conditions checkbox
      - "Already have an account? Login" link
    - Implement form validation with Formik or React Hook Form
    - Add real-time validation feedback
    - Style forms with consistent design system (colors, spacing, typography)

### Day 5: Protected Routes & Auth Navigation Flow
*   **Task:**
    - Create navigation structure:
      - `AuthNavigator`: Login, Register, ForgotPassword (Stack)
      - `AppNavigator`: Main app with tabs (Stack within Tabs)
      - `RootNavigator`: Conditionally renders Auth or App based on auth state
    - Implement conditional navigation:
      ```jsx
      {isAuthenticated ? <AppNavigator /> : <AuthNavigator />}
      ```
    - Create a splash screen that checks auth state on app start
    - Smooth transition between Auth and App navigators
    - Prevent "back" navigation from App to Auth (reset navigation on login)
    - Create AuthGuard HOC for protecting specific screens
    - Test navigation flow: login → app, logout → login

### Day 6: User Profile & Edit Profile Screens
*   **Task:**
    - Create Profile Screen:
      - Profile avatar (circular image)
      - Username and full name
      - Bio text
      - Stats: post count, followers, following (placeholder numbers)
      - "Edit Profile" button (navigates to edit screen)
      - "Logout" button
    - Create Edit Profile Screen:
      - Avatar picker (camera or gallery)
      - Edit username input
      - Edit bio (multiline TextInput)
      - "Save Changes" button
      - Implement avatar upload to cloud storage
      - Update user data in backend
      - Update Redux/Zustand state on save
    - Implement profile data fetching:
      - Load profile on screen focus
      - Handle loading and error states
    - Create ProfileHeader component (reusable)
    - Test profile update flow end-to-end

### Day 7: Review, Testing & Authentication Hardening
*   **Task:**
    - **Security Review:**
      - Ensure tokens are stored securely (AsyncStorage with encryption if needed)
      - Validate user input on client and server
      - Implement rate limiting for auth endpoints (backend)
      - Add CSRF protection if using cookies
    - **Testing:**
      - Test registration flow with various inputs
      - Test login with correct/incorrect credentials
      - Test logout and verify token is cleared
      - Test auto-login on app restart
      - Test token expiration handling
      - Test network errors (airplane mode)
    - **Error Handling:**
      - Add user-friendly error messages for all auth errors
      - Handle specific errors: email already exists, invalid password, network error
      - Implement retry logic for network failures
    - **Accessibility:**
      - Add accessibility labels to all form inputs
      - Ensure keyboard navigation works
      - Test with screen reader (TalkBack/VoiceOver)
    - **Documentation:**
      - Document auth flow in README
      - Add code comments for complex logic
      - Create a TODO list for Week 18 features
    - **Refactoring:**
      - Extract reusable components (AuthInput, AuthButton)
      - Create custom hooks (useLogin, useRegister)
      - Clean up console.logs and debug code

## Week 17 Deliverables Checklist

- ✅ Project set up with proper folder structure
- ✅ Backend integration (Firebase/Supabase/Custom API)
- ✅ User registration and login functionality
- ✅ JWT token storage and refresh logic
- ✅ Auth state management (Redux/Zustand)
- ✅ Login and Registration screens with validation
- ✅ Protected routes and auth navigation flow
- ✅ User profile display and edit functionality
- ✅ Avatar upload with camera/gallery integration
- ✅ Logout functionality
- ✅ Auto-login on app restart
- ✅ Error handling and loading states
- ✅ Code is clean, documented, and follows best practices

## Key Takeaways

By the end of this week, you should have:
- ✅ A solid foundation for your portfolio project
- ✅ Production-quality authentication system
- ✅ Deep understanding of auth flows in mobile apps
- ✅ Reusable components and utilities for the rest of the project
- ✅ Confidence in building secure, user-friendly auth experiences

## Resources

- [Firebase Authentication Docs](https://firebase.google.com/docs/auth)
- [Supabase Auth](https://supabase.com/docs/guides/auth)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)
- [React Navigation Auth Flow](https://reactnavigation.org/docs/auth-flow)
- [Formik Validation](https://formik.org/docs/guides/validation)

## Looking Ahead to Week 18

Next week, you'll build the core feature of SocialConnect: **The Feed**. You'll implement FlatList optimization for thousands of posts, pull-to-refresh, infinite scroll, and post interactions (like, comment). This is where your app starts to feel like a real social network!
