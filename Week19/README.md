# Week 19: Creating Posts with Camera & Image Upload

## Overview

This week, you'll implement content creation features in SocialConnect. Users will be able to create posts with photos from their camera or gallery, add captions and location tags, and publish to the feed. You'll also add the ability to create comments on posts. These features complete the core social interaction loop. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Portfolio Project: SocialConnect - Week 3/6

**This Week's Goals:**
- Implement create post flow with camera and gallery
- Add image editing (crop, filters)
- Upload images to cloud storage
- Add caption and location to posts
- Implement comment creation
- Update feed with new posts in real-time

## Daily Plan

### Day 1: Create Post UI & Navigation
*   **Task:**
    - Add "Create Post" button to main navigation:
      - Floating Action Button (FAB) on HomeScreen
      - Or center tab button that opens modal
    - Create CreatePostNavigator (Stack):
      - SelectMediaScreen → EditPhotoScreen → AddCaptionScreen
    - Present CreatePostNavigator as modal:
      - Use `presentation: 'modal'` for iOS style
      - Add "Cancel" button in header that confirms before closing
    - Create SelectMediaScreen:
      - Two large buttons: "Take Photo" and "Choose from Library"
      - Recent photos grid (optional)
      - Request camera and media library permissions
      - Handle permission denial gracefully
    - Navigate to EditPhotoScreen after image selection

### Day 2: Camera Integration & Image Selection
*   **Task:**
    - Implement camera functionality:
      - Use expo-camera or react-native-camera
      - Full-screen camera preview
      - Capture button with animation
      - Switch front/back camera button
      - Flash toggle button
      - Captured photo preview with "Use Photo" or "Retake" options
    - Implement gallery picker:
      - Use expo-image-picker
      - Allow selecting single image
      - Set allowsEditing: true for basic cropping
      - Configure image quality (0.8 for balance)
    - Handle image selection:
      - Store selected image URI in state
      - Extract image dimensions
      - Navigate to EditPhotoScreen with image data
    - Create ImagePreview component:
      - Display selected/captured image
      - Show image dimensions
      - Loading indicator while processing

### Day 3: Image Editing & Optimization
*   **Task:**
    - Create EditPhotoScreen:
      - Display selected image
      - Crop tool (square or custom aspect ratios)
      - Rotate/flip buttons
      - Basic filters (optional: grayscale, sepia, brightness)
      - "Next" button to proceed to caption
    - Implement image manipulation:
      - Use expo-image-manipulator
      - Resize to max 1080px width (Instagram standard)
      - Compress to reduce file size (quality: 0.8)
      - Crop to selected area
      - Apply selected filter
    - Optimize for performance:
      - Show loading indicator during manipulation
      - Run manipulation off main thread if possible
      - Cache manipulated image
    - Create before/after preview:
      - Show original vs edited side-by-side
      - Reset button to undo all edits
    - Navigate to AddCaptionScreen with edited image

### Day 4: Image Upload to Cloud Storage
*   **Task:**
    - Set up cloud storage (Firebase Storage, Cloudinary, or AWS S3)
    - Create upload service:
      - `uploadImage(imageUri, userId)` → returns uploaded image URL
      - Generate unique filename (e.g., `userId_timestamp.jpg`)
      - Upload with multipart/form-data or SDK
      - Track upload progress
    - Implement upload progress UI:
      - Progress bar showing upload percentage
      - Cancel upload button
      - Retry on failure
    - Handle upload errors:
      - Network timeout
      - File too large
      - Storage quota exceeded
      - Show user-friendly error messages
    - Optimize uploads:
      - Upload compressed image (not original)
      - Upload in background (allow user to continue using app)
      - Queue uploads when offline, retry when online
    - Test upload with large images and slow network

### Day 5: Add Caption & Location, Publish Post
*   **Task:**
    - Create AddCaptionScreen:
      - Image thumbnail at top
      - Multiline TextInput for caption
      - Character counter (e.g., max 2200 like Instagram)
      - "Add Location" button (opens location picker)
      - "Share" button to publish post
    - Implement location tagging:
      - Get current location with expo-location
      - Reverse geocode to get place name
      - Allow searching for places (Google Places API or custom)
      - Display selected location with remove option
    - Create post submission:
      - Upload image first, get image URL
      - Create post object with:
        - imageUrl, caption, location, userId, timestamp
      - Call `createPost(postData)` API
      - Show loading state while publishing
      - Handle success: dismiss modal, show success toast
      - Handle error: keep data, allow retry
    - Update feed with new post:
      - Add post to top of feed state (optimistic update)
      - Or trigger feed refresh to fetch new posts
      - Scroll feed to top to show new post
    - Add draft saving:
      - Save draft to AsyncStorage if user cancels
      - Restore draft when reopening create flow

### Day 6: Comments Creation & Real-Time Updates
*   **Task:**
    - Add comment input to PostDetailScreen:
      - Fixed input bar at bottom (like messaging apps)
      - TextInput with "Post" button
      - Avatar of current user
      - Keyboard handling (KeyboardAvoidingView)
    - Implement comment creation:
      - API: `createComment(postId, commentText)`
      - Optimistically add comment to list
      - Show loading indicator on Post button
      - Clear input after successful post
      - Handle errors and retry
    - Update comments state:
      - Add new comment to top or bottom of list
      - Increment post's comment count
      - Scroll to new comment
    - Add comment notifications (basic):
      - When user comments, notify post author (prepare for push notifications in Week 23)
      - Store notification in backend
    - Implement @ mentions (optional):
      - Detect @username in comments
      - Show autocomplete while typing
      - Create links to user profiles
    - Test comment flow thoroughly:
      - Comment on own posts
      - Comment on others' posts
      - Rapid commenting
      - Long comments
      - Offline commenting (queue and sync)

### Day 7: Polish, Edge Cases & Integration
*   **Task:**
    - **Polish Create Post Flow:**
      - Smooth transitions between screens
      - Add animations (fade, slide)
      - Haptic feedback on actions
      - Improve image previews with pinch-to-zoom
    - **Handle Edge Cases:**
      - What if user rotates device during create flow?
      - What if app is killed during upload?
      - What if user denies permissions?
      - What if image upload fails multiple times?
      - Large images causing memory issues?
    - **Improve UX:**
      - Show "Posting..." indicator on feed while publishing
      - Disable "Share" during upload to prevent duplicates
      - Add "Discard Draft?" confirmation when canceling
      - Remember user's last used camera (front/back)
    - **Integration Testing:**
      - Complete flow: camera → edit → caption → publish → see in feed
      - Test on both iOS and Android
      - Test with low storage (handle quota errors)
      - Test with airplane mode (offline queue)
    - **Performance:**
      - Ensure camera doesn't cause memory leaks
      - Optimize image processing speed
      - Test with very large images (10MB+)
    - **Code Quality:**
      - Refactor create post logic into custom hook `useCreatePost()`
      - Extract reusable components (ImageEditor, LocationPicker)
      - Add error boundaries
      - Clean up and document code

## Week 19 Deliverables Checklist

- ✅ Create post flow with camera and gallery integration
- ✅ Image editing (crop, resize, filters)
- ✅ Image upload to cloud storage with progress
- ✅ Caption input with character limit
- ✅ Location tagging
- ✅ Publish post and update feed
- ✅ Comment creation on posts
- ✅ Optimistic updates for posts and comments
- ✅ Draft saving and restoration
- ✅ Error handling and offline support
- ✅ Smooth animations and transitions
- ✅ Tested on both iOS and Android

## Key Takeaways

By the end of this week, you should have:
- ✅ Complete content creation pipeline for a social app
- ✅ Expertise in camera, image picker, and image manipulation
- ✅ Cloud storage integration skills
- ✅ Understanding of complex multi-step user flows
- ✅ Production-quality create post feature

## Resources

- [Expo Camera](https://docs.expo.dev/versions/latest/sdk/camera/)
- [Expo Image Picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/)
- [Expo Image Manipulator](https://docs.expo.dev/versions/latest/sdk/imagemanipulator/)
- [Firebase Storage](https://firebase.google.com/docs/storage)
- [Cloudinary Upload](https://cloudinary.com/documentation/upload_images)

## Looking Ahead to Week 20

Next week, you'll focus on **Performance Optimization & Debugging**. You'll use Flipper to profile your app, identify bottlenecks, optimize bundle size, implement code splitting, and ensure your app runs smoothly on all devices. You'll also learn advanced debugging techniques.
