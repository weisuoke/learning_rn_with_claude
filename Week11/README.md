# Week 11: Device APIs - Camera & Photo Library

## Overview

This week, we will explore native device APIs, starting with the camera and photo library. This is where React Native truly differentiates itself from web development - you'll have direct access to device hardware. You'll learn permission handling, image manipulation, and how to integrate media into your apps. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Understanding Permissions in Mobile Apps
*   **Theory:** Read about [Permission Model](https://docs.expo.dev/guides/permissions/) in iOS and Android. Understand runtime vs install-time permissions.
*   **Practice:**
    - Learn the permission flow: request → user grants/denies → handle response
    - Use Expo's permissions API: `expo-permissions` or module-specific permissions
    - Request camera permission: `Camera.requestCameraPermissionsAsync()`
    - Request media library permission: `MediaLibrary.requestPermissionsAsync()`
    - Handle permission denial: show explanation, redirect to settings
    - Implement `checkPermissions()` function that checks before accessing features
    - Understand iOS vs Android permission differences

### Day 2: Camera Access with Expo Camera
*   **Theory:** Study [expo-camera](https://docs.expo.dev/versions/latest/sdk/camera/) documentation.
*   **Practice:**
    - Install: `npx expo install expo-camera`
    - Create a camera screen with `Camera` component
    - Implement take photo functionality with `takePictureAsync()`
    - Record video with `recordAsync()`
    - Switch between front and back camera
    - Toggle flash modes: on, off, auto
    - Display camera preview with proper aspect ratio
    - Save captured photos to device with expo-media-library

### Day 3: Image Picker - Selecting from Gallery
*   **Theory:** Read [expo-image-picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/) documentation.
*   **Practice:**
    - Install: `npx expo install expo-image-picker`
    - Implement "Pick from Library" with `launchImageLibraryAsync()`
    - Implement "Take Photo" with `launchCameraAsync()`
    - Configure image quality and compression
    - Set `allowsEditing: true` to enable cropping
    - Handle multiple image selection with `allowsMultipleSelection`
    - Get image URI, width, height, and file size
    - Display selected images in a grid

### Day 4: Image Manipulation & Optimization
*   **Theory:** Learn about [expo-image-manipulator](https://docs.expo.dev/versions/latest/sdk/imagemanipulator/) for image editing.
*   **Practice:**
    - Install: `npx expo install expo-image-manipulator`
    - Resize images before upload to reduce file size
    - Crop images to specific dimensions
    - Rotate and flip images
    - Apply filters (not built-in, but understand the concept)
    - Compress images with quality parameter
    - Convert between image formats
    - Build a simple image editor UI with crop and rotate controls

### Day 5: Uploading Images to a Backend
*   **Theory:** Learn how to upload images to cloud storage (Firebase Storage, Cloudinary, AWS S3).
*   **Practice:**
    - Create a multipart/form-data request to upload images
    - Use `fetch()` with FormData to upload image files
    - Implement upload progress tracking
    - Show progress bar during upload (use `XMLHttpRequest` for progress events)
    - Upload to Firebase Storage with expo-firebase-storage or Firebase SDK
    - Upload to Cloudinary using their API
    - Handle upload errors and implement retry logic
    - Display uploaded image from remote URL

### Day 6: Project Application - Instagram-Style Photo App
*   **Task:** Build a photo-sharing app with camera, gallery, and editing features:
    - **Home Screen:**
      - Feed of images (FlatList) - use placeholder data or local images for now
      - Each image has a caption and like button
      - Pull-to-refresh
    - **Create Post Screen:**
      - Two options: "Take Photo" (opens camera) or "Choose from Library" (opens gallery)
      - Camera screen with:
        - Live camera preview
        - Capture button
        - Flip camera button
        - Flash toggle
      - After taking/selecting photo, navigate to Edit screen
    - **Edit Screen:**
      - Show selected image
      - Crop tool (use allowsEditing or manual implementation)
      - Resize to square (Instagram style)
      - Add caption TextInput
      - "Post" button → saves to local storage (AsyncStorage)
    - **Profile Screen:**
      - User's posted photos in a grid (3 columns)
      - Change profile picture (camera or library)
      - Profile picture editing: crop to circle
    - **Technical Implementation:**
      - Request permissions before accessing camera/library
      - Handle permission denial gracefully
      - Compress images before saving
      - Store image URIs and captions in AsyncStorage
      - Show loading states during image processing
    - **Bonus Features:**
      - Delete posts with swipe gesture
      - Share photos to native share sheet
      - Save photos to device gallery
    - **Goal:** Build a production-quality photo app with full camera integration

### Day 7: Review and Refactor
*   **Task:**
    - Review all camera and image code from the week
    - Test the photo app on a physical device (camera simulators are limited)
    - Optimize image handling: ensure large images don't cause memory issues
    - Refactor: create reusable components (CameraPicker, ImagePicker, ImageEditor)
    - Create custom hooks: `useCameraPermissions()`, `useImagePicker()`
    - Test edge cases: what if user denies permissions? What if device has no camera?
    - Implement analytics: track how often each photo source (camera vs library) is used
    - Document permission flows and image upload architecture

## Key Takeaways

By the end of this week, you should:
- ✅ Master permission handling for camera and media library
- ✅ Implement camera functionality with live preview and capture
- ✅ Use image picker to select photos from device gallery
- ✅ Manipulate images: resize, crop, compress, rotate
- ✅ Upload images to remote storage with progress tracking
- ✅ Build photo-centric apps like Instagram, camera apps, profile editors

## Resources

- [Expo Camera API](https://docs.expo.dev/versions/latest/sdk/camera/)
- [Expo Image Picker](https://docs.expo.dev/versions/latest/sdk/imagepicker/)
- [Expo Image Manipulator](https://docs.expo.dev/versions/latest/sdk/imagemanipulator/)
- [Expo Media Library](https://docs.expo.dev/versions/latest/sdk/media-library/)
- [Firebase Storage Setup](https://firebase.google.com/docs/storage/react-native/start)
- [Cloudinary Upload API](https://cloudinary.com/documentation/upload_images)

## Looking Ahead to Week 12

Next week, we'll explore geolocation and maps integration. You'll learn to access the device's GPS, track user location, display maps with markers, and build location-based features - essential skills for delivery, travel, and social apps.
