# Week 08: User Input & Forms in React Native

## Overview

This week, we will master user input and form handling in React Native. Forms on mobile are fundamentally different from web due to virtual keyboards, touch interactions, and platform-specific behaviors. You'll learn to create production-quality forms with validation, proper keyboard handling, and excellent UX. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: TextInput Deep Dive & Keyboard Types
*   **Theory:** Comprehensive study of [TextInput](https://reactnative.dev/docs/textinput) component and all its props. Understand platform differences.
*   **Practice:**
    - Create TextInputs with different `keyboardType`: 'default', 'email-address', 'numeric', 'phone-pad', 'decimal-pad'
    - Use `autoCapitalize`: 'none', 'sentences', 'words', 'characters'
    - Implement `secureTextEntry` for password fields
    - Use `autoCorrect`, `autoComplete`, and `textContentType` (iOS) for better UX
    - Style TextInput: borders, padding, placeholders
    - Handle `onChangeText` vs `onChange` - understand the difference
    - Implement controlled components with `value` prop

### Day 2: Keyboard Handling & KeyboardAvoidingView
*   **Theory:** Read about [KeyboardAvoidingView](https://reactnative.dev/docs/keyboardavoidingview) and the [Keyboard API](https://reactnative.dev/docs/keyboard).
*   **Practice:**
    - Wrap forms in `KeyboardAvoidingView` with `behavior="padding"` (Android) and `behavior="position"` or `"height"` (iOS)
    - Use `keyboardVerticalOffset` to fine-tune positioning
    - Implement `Keyboard.dismiss()` when user taps outside the input
    - Use `TouchableWithoutFeedback` wrapper to dismiss keyboard on tap
    - Handle keyboard events with `Keyboard.addListener('keyboardDidShow', ...)` and `keyboardDidHide`
    - Scroll to active input in a ScrollView when keyboard appears
    - Test on both iOS and Android - they behave differently!

### Day 3: Form State Management & Validation
*   **Theory:** Learn form management patterns in React Native. Explore libraries like [Formik](https://formik.org/) or [React Hook Form](https://react-hook-form.com/).
*   **Practice:**
    - Build a registration form with manual state management: email, password, confirm password
    - Implement real-time validation: email format, password strength, matching passwords
    - Show validation errors below each input field
    - Disable submit button until form is valid
    - Integrate Formik or React Hook Form for easier form management
    - Use validation schemas with Yup or Zod
    - Implement `onBlur` validation (validate when user leaves the field)

### Day 4: Advanced Input Types - Pickers, Switches, Sliders
*   **Theory:** Study [Switch](https://reactnative.dev/docs/switch), [Slider](https://reactnative.dev/docs/slider), and explore Picker alternatives (deprecated in core, use community packages).
*   **Practice:**
    - Create toggle switches using the `Switch` component
    - Implement sliders for numeric ranges (e.g., age, price range)
    - Use `@react-native-picker/picker` for dropdowns (install it first)
    - Create a date picker using `@react-native-community/datetimepicker`
    - Build a custom dropdown/select component with modal
    - Implement checkbox functionality (no native checkbox - use custom component)
    - Create a multi-select component with chips

### Day 5: File Uploads - Images & Documents
*   **Theory:** Learn about [expo-image-picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/) and [expo-document-picker](https://docs.expo.dev/versions/latest/sdk/document-picker/).
*   **Practice:**
    - Install expo-image-picker: `npx expo install expo-image-picker`
    - Request camera and media library permissions
    - Allow user to select an image from their library
    - Allow user to take a photo with camera
    - Display selected image preview
    - Upload image to a backend (use a test API like [Cloudinary](https://cloudinary.com/) or Firebase Storage)
    - Implement progress indicator during upload
    - Handle upload errors gracefully

### Day 6: Project Application - User Profile & Settings Form
*   **Task:** Build a comprehensive user profile and settings screen:
    - **Profile Form:**
      - Profile picture upload (camera + library options)
      - Text inputs: Full name, username, bio (multiline TextInput)
      - Email input with validation
      - Phone number input with proper keyboard type
      - Date of birth picker
      - Gender selector (radio buttons or picker)
    - **Settings Screen:**
      - Toggle switches: Push notifications, Email notifications, Dark mode
      - Picker: Language preference
      - Slider: Text size preference
      - Multi-select: Interests/categories (show chips)
    - **Form Features:**
      - Real-time validation with error messages
      - Form dirty state tracking (show "Unsaved changes" warning)
      - Save button that's disabled until form is valid and dirty
      - Loading state during save operation
      - Success/error toast messages after save
      - KeyboardAvoidingView for smooth keyboard handling
      - Dismiss keyboard when tapping outside
    - **Goal:** Create a production-quality form with excellent UX and proper validation

### Day 7: Review and Refactor
*   **Task:**
    - Review all form implementations from the week
    - Refactor the Profile form: extract reusable input components (FormInput, FormSwitch, FormPicker)
    - Create a custom hook: `useForm()` that handles common form logic
    - Test forms thoroughly on both platforms
    - Test keyboard behavior: does it cover inputs? Does scrolling work?
    - Ensure accessibility: add `accessibilityLabel` and `accessibilityHint` to inputs
    - Compare manual form handling vs Formik/React Hook Form - which do you prefer?
    - Document form validation patterns and reusable components you created

## Key Takeaways

By the end of this week, you should:
- ✅ Master TextInput and all its configuration options
- ✅ Handle keyboards properly on both iOS and Android
- ✅ Implement form validation and state management
- ✅ Use advanced input components: pickers, switches, sliders, date pickers
- ✅ Handle image uploads with camera and library
- ✅ Build production-quality forms with great UX

## Resources

- [TextInput Docs](https://reactnative.dev/docs/textinput)
- [KeyboardAvoidingView Guide](https://reactnative.dev/docs/keyboardavoidingview)
- [Formik for React Native](https://formik.org/docs/guides/react-native)
- [React Hook Form](https://react-hook-form.com/)
- [Expo Image Picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/)
- [Yup Validation](https://github.com/jquense/yup)

## Looking Ahead to Week 09

Next week, we'll dive deep into networking and data fetching for mobile apps. You'll learn how to handle API calls, implement retry logic, handle offline scenarios, and integrate modern data fetching libraries like React Query for mobile.
