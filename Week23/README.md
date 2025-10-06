# Week 23: Push Notifications & Background Tasks

## Overview

Welcome to Phase 4: Production & Deployment! This week, you'll implement push notifications - a critical feature for engagement in mobile apps. You'll learn to send notifications from your backend, handle them in the app, and manage notification permissions. You'll also explore background tasks for syncing data. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Push Notifications Fundamentals & Setup
*   **Theory:** Read about push notifications architecture: APNS (iOS), FCM (Android), notification payloads, tokens.
*   **Practice:**
    - Choose notification service:
      - Option 1: **Expo Notifications** (easiest, works with Expo Go)
      - Option 2: **Firebase Cloud Messaging** (most popular, works everywhere)
      - Option 3: **OneSignal** (feature-rich, free tier available)
    - Install Expo Notifications: `npx expo install expo-notifications`
    - Set up notification permissions:
      - Request permission: `Notifications.requestPermissionsAsync()`
      - Check current permissions: `Notifications.getPermissionsAsync()`
      - Handle iOS permission UI
      - Handle Android permission (auto-granted on most versions)
    - Get push token (device identifier):
      - iOS: APNS token
      - Android: FCM token
      - Store token in backend (associate with user)
    - Test basic notification reception

### Day 2: Receiving & Displaying Notifications
*   **Task:**
    - Configure notification handlers:
      - **Foreground handler** (app is open):
        ```js
        Notifications.setNotificationHandler({
          handleNotification: async () => ({
            shouldShowAlert: true,
            shouldPlaySound: true,
            shouldSetBadge: true,
          }),
        });
        ```
      - **Background/quit handler** (app closed)
    - Listen for notifications:
      - `Notifications.addNotificationReceivedListener()` - notification received
      - `Notifications.addNotificationResponseReceivedListener()` - user tapped notification
    - Handle notification tap:
      - Parse notification data
      - Navigate to relevant screen (post, profile, etc.)
      - Example: notification about comment → navigate to PostDetail
    - Display local notifications (for testing):
      - `Notifications.scheduleNotificationAsync()`
      - Test different notification styles
      - Test with images, actions, categories
    - Implement notification badge:
      - Update app icon badge count
      - `Notifications.setBadgeCountAsync(count)`

### Day 3: Sending Push Notifications from Backend
*   **Task:**
    - Set up backend notification sending:
      - Option A: Use Expo Push API (if using Expo):
        - Send POST to `https://exp.host/--/api/v2/push/send`
        - Include recipient push token and message
      - Option B: Use Firebase Admin SDK:
        - Send via FCM using Firebase Admin
        - Handle iOS APNS and Android FCM
    - Implement notification triggers in SocialConnect:
      - Send notification when someone:
        - Likes your post
        - Comments on your post
        - Follows you
        - Mentions you in a comment
    - Create notification payload structure:
      ```js
      {
        to: userPushToken,
        title: "New Like!",
        body: "John liked your post",
        data: { type: "like", postId: "123", userId: "456" },
        badge: 1
      }
      ```
    - Test notification sending:
      - Use Postman to send test notifications
      - Verify notifications appear on device
      - Test on both iOS and Android
    - Handle notification errors:
      - Invalid tokens (user uninstalled app)
      - Token refresh
      - Rate limiting

### Day 4: Advanced Notifications - Categories, Actions, Rich Content
*   **Task:**
    - Implement notification categories:
      - "Like" category: no actions needed
      - "Comment" category: "Reply" and "View" actions
      - "Follow" category: "Follow Back" action
    - Add notification actions (buttons):
      - iOS: appears when long-pressing notification
      - Android: appears as buttons below notification
      - Handle action responses in app
    - Rich notifications:
      - Add images to notifications (post thumbnails)
      - Add avatars (user who triggered notification)
      - Test on iOS (rich notifications better supported)
    - Notification grouping (Android):
      - Group notifications by type
      - Show summary: "You have 5 new likes"
    - Notification sounds:
      - Use default or custom sounds
      - Configure per notification category
    - Test advanced features:
      - Long-press notifications on iOS
      - Swipe actions on Android
      - Notification grouping with multiple notifications

### Day 5: Notification Preferences & Opt-Out
*   **Task:**
    - Create notification settings screen:
      - Toggle for each notification type:
        - ☑ Likes
        - ☑ Comments
        - ☑ New followers
        - ☑ Mentions
      - Push notification toggle (master switch)
      - Email notification preferences (for future)
      - Quiet hours (don't send between 10pm-8am)
    - Store preferences in backend:
      - User model: `notificationPreferences` object
      - Update when user changes settings
      - Respect preferences when sending notifications
    - Implement opt-out:
      - Allow users to disable all notifications
      - Unregister push token from backend
      - Clear badge count
    - Handle notification permission denial:
      - If user denies, show explanation
      - Provide button to open app settings
      - Use `Linking.openSettings()` to open device settings
    - Test all preference scenarios:
      - All enabled
      - Some disabled
      - All disabled
      - Permission denied at OS level

### Day 6: Background Tasks & Data Syncing
*   **Task:**
    - Learn about background tasks in React Native:
      - **Background Fetch** (periodic updates)
      - **Background Notifications** (silent notifications to wake app)
      - **Task Manager** (Expo) or **Headless JS** (bare RN)
    - Implement background fetch with Expo:
      - Install: `npx expo install expo-background-fetch expo-task-manager`
      - Register background task:
        ```js
        TaskManager.defineTask(TASK_NAME, async () => {
          // Sync data, check for new content
          return BackgroundFetch.BackgroundFetchResult.NewData;
        });
        ```
      - Fetch new posts/notifications in background
      - Update badge count
      - Limit frequency (iOS limits to ~few times per day)
    - Implement silent push notifications:
      - Send `content-available: 1` (iOS) or `priority: high` (Android)
      - Wake app to sync data without showing notification
      - Update UI when app comes to foreground
    - Background location tracking (if needed):
      - Request background location permission
      - Track location updates in background
      - Note: App Store requires strong justification
    - Test background tasks:
      - Verify tasks run when app is closed
      - Check battery impact
      - Monitor execution frequency

### Day 7: Testing, Analytics & Notification Best Practices
*   **Task:**
    - **Comprehensive Notification Testing:**
      - Test all notification types on iOS and Android
      - Test notification tap navigation
      - Test with app in foreground, background, killed
      - Test notification permissions flow
      - Test notification preferences
      - Test badge counts
    - **Analytics & Monitoring:**
      - Track notification metrics:
        - Delivery rate (sent vs received)
        - Open rate (tapped vs delivered)
        - Action rate (action taken vs delivered)
      - Use Firebase Analytics or Segment
      - Monitor notification errors
    - **Best Practices:**
      - Don't spam users (respect preferences)
      - Personalize notifications (use user's name)
      - Use clear, actionable copy
      - Test notification copy on actual devices
      - Localize notifications (multiple languages)
      - Respect quiet hours and time zones
      - Batch notifications (don't send 10 at once)
    - **Prepare for Production:**
      - Ensure APNS certificates are configured (iOS)
      - Ensure FCM server key is set (Android)
      - Test with production push tokens
      - Set up notification logs for debugging
    - **Documentation:**
      - Document notification architecture
      - List all notification types and triggers
      - Add setup instructions for backend team

## Week 23 Deliverables Checklist

- ✅ Push notifications fully implemented and working
- ✅ Notification permissions handled gracefully
- ✅ All notification types working (like, comment, follow, mention)
- ✅ Notification tap navigates to correct screen
- ✅ Rich notifications with images and actions
- ✅ Notification preferences in settings
- ✅ Background fetch implemented
- ✅ Silent push notifications for data sync
- ✅ Badge counts updating correctly
- ✅ Tested on both iOS and Android
- ✅ Analytics tracking notification engagement

## Key Takeaways

By the end of this week, you should:
- ✅ Master push notifications on iOS and Android
- ✅ Understand APNS and FCM architecture
- ✅ Implement background tasks for data syncing
- ✅ Handle notification permissions and preferences
- ✅ Know notification best practices for user engagement
- ✅ Have a production-ready notification system

## Resources

- [Expo Notifications](https://docs.expo.dev/versions/latest/sdk/notifications/)
- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging)
- [React Native Push Notifications](https://github.com/zo0r/react-native-push-notification)
- [Expo Background Fetch](https://docs.expo.dev/versions/latest/sdk/background-fetch/)
- [Push Notification Best Practices](https://onesignal.com/blog/push-notification-best-practices/)

## Looking Ahead to Week 24

Next week, you'll set up **CI/CD pipelines** to automate building and testing your app. You'll use GitHub Actions or Bitrise, configure Fastlane (for bare RN) or EAS Build (for Expo), and prepare automated deployments to TestFlight and Google Play Beta.
