# Week 25: App Store Deployment Process (iOS & Android)

## Overview

This is the week you've been working towards - deploying your app to the Apple App Store and Google Play Store! You'll learn the complete submission process, app store guidelines, how to prepare required assets, and navigate the review process. By the end of the week, your app will be live (or in review) and available for download by real users. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: App Store Guidelines & Pre-Submission Checklist
*   **Theory:** Read [Apple App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) and [Google Play Policy](https://play.google.com/about/developer-content-policy/).
*   **Practice:**
    - **Understand guidelines:**
      - No misleading content or functionality
      - No violating privacy (must request permissions appropriately)
      - No offensive or inappropriate content
      - Must have core functionality (not just a web view)
      - Must comply with data privacy laws (GDPR, CCPA)
    - **Pre-submission checklist:**
      - ✅ App is stable (no crashes)
      - ✅ All features work on production build
      - ✅ All placeholder content removed
      - ✅ Privacy policy and terms of service links work
      - ✅ All third-party content is properly licensed
      - ✅ App requests permissions with clear explanations
      - ✅ No development/test code in production build
      - ✅ App handles poor network gracefully
      - ✅ App works on all supported iOS/Android versions
    - **Test on real devices:**
      - Test on oldest supported iOS device
      - Test on oldest supported Android device
      - Test on tablets (iPad, Android tablet)
      - Verify all screen sizes are supported
    - **Fix any compliance issues:**
      - Add privacy policy (required!)
      - Add terms of service
      - Configure app permissions with proper descriptions
      - Set minimum iOS/Android versions

### Day 2: App Icons, Splash Screens & Visual Assets
*   **Task:**
    - **Create App Icon:**
      - Design a 1024x1024px icon (iOS requirement)
      - Use tools: Figma, Sketch, or Adobe Illustrator
      - Follow icon design best practices:
        - Simple, recognizable
        - Works at small sizes
        - No transparency (filled background)
        - No text (or minimal text)
      - Generate all icon sizes:
        - iOS: use Xcode Asset Catalog or online generator
        - Android: use Android Studio Image Asset or online generator
      - Test icon on home screen (looks good at actual size?)
    - **Create Splash Screen:**
      - Design a launch screen
      - Keep it simple (logo + background color)
      - Avoid text or complex graphics
      - Configure in `app.json` (Expo) or native projects
    - **Create App Store Screenshots:**
      - **iOS:** Required sizes (2-10 screenshots per device type):
        - 6.7" iPhone (Pro Max)
        - 6.5" iPhone (Plus)
        - 5.5" iPhone (older Plus models)
        - iPad Pro 12.9" (if supporting iPad)
      - **Android:** Required sizes:
        - Phone (1080x1920px or higher)
        - 7" Tablet (optional)
        - 10" Tablet (optional)
      - Take screenshots:
        - Use simulator/emulator
        - Capture key screens: login, feed, post detail, profile, create post
        - Or create marketing screenshots with frames and text
    - **Create Promotional Graphics (Android):**
      - Feature Graphic: 1024x500px (required for Google Play)
      - Promotional Video (optional, 30 seconds max)
    - **Tools for asset generation:**
      - [App Icon Generator](https://appicon.co/)
      - [Screenshot Designer](https://screenshots.pro/)
      - [Fastlane Frameit](https://docs.fastlane.tools/actions/frameit/)

### Day 3: App Store Connect Setup & iOS Submission Prep
*   **Task:**
    - **Create App in App Store Connect:**
      - Log into [App Store Connect](https://appstoreconnect.apple.com/)
      - Click "My Apps" → "+"
      - Create new app:
        - Platform: iOS
        - Name: Your app name (must be unique)
        - Primary Language
        - Bundle ID: Must match your app's bundle ID
        - SKU: Unique identifier (e.g., com.yourname.socialconnect)
    - **Fill in App Information:**
      - App Name (30 characters max)
      - Subtitle (30 characters max)
      - Privacy Policy URL (required!)
      - Category (Social Networking, Photo & Video, etc.)
      - Content Rights (do you have necessary rights?)
    - **Write App Description:**
      - What's your app about? (4000 characters max)
      - Highlight key features
      - Use keywords for ASO (App Store Optimization)
      - Make it compelling but accurate
    - **Add Keywords:**
      - 100 characters to describe your app for search
      - Comma-separated list
      - Research popular keywords in your category
    - **Configure Pricing & Availability:**
      - Free or Paid?
      - Available in all countries or specific regions?
      - Pre-order (if app not ready)?
    - **Upload Screenshots:**
      - Drag and drop to App Store Connect
      - For each required device size
      - Order them strategically (most important first)
    - **App Preview Video (optional but recommended):**
      - 15-30 second video showing app usage
      - Upload to App Store Connect

### Day 4: iOS Build Submission & Review Process
*   **Task:**
    - **Create Production Build:**
      - With EAS: `eas build --platform ios --profile production`
      - With Fastlane: `fastlane ios release`
      - Or manually with Xcode:
        - Open project in Xcode
        - Select "Any iOS Device"
        - Product → Archive
        - Upload to App Store Connect
    - **Wait for build processing:**
      - Builds take 10-30 minutes to process in App Store Connect
      - You'll receive email when ready
    - **Select build in App Store Connect:**
      - Go to "TestFlight" tab
      - Select build
      - Provide export compliance info (encryption usage)
      - Add to external testers (if doing beta first)
    - **Fill in version-specific info:**
      - What's New in This Version (release notes)
      - Build number
      - Version string (e.g., 1.0.0)
    - **Configure Age Rating:**
      - Answer questionnaire about content
      - Get appropriate rating
    - **Submit for Review:**
      - Click "Submit for Review"
      - Provide any necessary review notes
      - Include demo account credentials if login required
      - Explain any unusual permissions
    - **Wait for review:**
      - Usually 24-48 hours
      - Can take up to a week
      - Check status in App Store Connect
    - **Respond to rejection (if happens):**
      - Read rejection reason carefully
      - Fix the issue
      - Resubmit
      - Common rejections: missing features, bugs, guideline violations

### Day 5: Google Play Console Setup & Android Submission
*   **Task:**
    - **Create Developer Account:**
      - Go to [Google Play Console](https://play.google.com/console)
      - Pay $25 one-time registration fee
      - Complete account verification (can take 1-2 days)
    - **Create App in Play Console:**
      - Click "Create App"
      - App name, default language
      - App or game? Free or paid?
      - Accept policies
    - **Fill in Store Listing:**
      - App name (30 characters)
      - Short description (80 characters)
      - Full description (4000 characters)
      - App icon (512x512px, PNG)
      - Feature graphic (1024x500px)
      - Screenshots (2-8 per device type)
      - Promotional video (YouTube link, optional)
    - **Set up Content Rating:**
      - Complete questionnaire
      - Get IARC rating (International Age Rating Coalition)
    - **Select App Category:**
      - Social, Communication, Photography, etc.
      - Add tags
    - **Set up Pricing & Distribution:**
      - Countries available
      - Pricing (free or paid)
      - Contains ads? (yes/no)
      - Primarily for children? (COPPA compliance)
    - **Create Privacy Policy:**
      - Required for all apps
      - Use template or hire lawyer
      - Host on website (not in PDF)
      - Explain what data you collect and why

### Day 6: Android Build Submission & Release Management
*   **Task:**
    - **Create Production Build (AAB):**
      - With EAS: `eas build --platform android --profile production`
      - With Fastlane: `fastlane android release`
      - Or manually:
        ```bash
        cd android
        ./gradlew bundleRelease
        ```
    - **Upload to Play Console:**
      - Go to "Production" or "Internal Testing"
      - Create new release
      - Upload AAB file
      - Google scans for security issues (can take minutes)
    - **Fill in Release Details:**
      - Release name (e.g., "1.0.0")
      - Release notes (what's new)
      - Can localize release notes
    - **Review and Roll Out:**
      - Review changes summary
      - Submit for review
      - Google review is usually faster (few hours to 1 day)
    - **Staged Rollout (recommended):**
      - Start with 10% of users
      - Monitor crashes and ratings
      - Increase to 25%, 50%, 100% over days
      - Halt rollout if issues found
    - **Test Internal Testing Track First:**
      - Upload to Internal Testing
      - Install on device and test
      - Once confident, promote to Production
    - **Wait for approval:**
      - Check status in Play Console
      - Respond to any issues Google finds
      - Fix and resubmit if needed

### Day 7: Post-Launch - Monitoring, Updates & App Store Optimization
*   **Task:**
    - **Monitor Launch:**
      - Check crash reports (Sentry, Crashlytics)
      - Monitor app reviews and ratings
      - Check download numbers
      - Monitor server load (if you have backend)
      - Fix any critical bugs immediately
    - **Respond to User Reviews:**
      - Reply to reviews in App Store Connect and Play Console
      - Thank users for positive reviews
      - Address issues in negative reviews
      - Update app to fix reported bugs
    - **App Store Optimization (ASO):**
      - Monitor keyword rankings
      - A/B test app icons and screenshots
      - Optimize description based on user feedback
      - Encourage happy users to leave reviews
      - Track conversion rate (page views → installs)
    - **Plan for Updates:**
      - Create roadmap for v1.1, v1.2, etc.
      - Collect user feedback and feature requests
      - Fix bugs reported by users
      - Release updates regularly (every 2-4 weeks)
    - **Marketing & Distribution:**
      - Share on social media
      - Create Product Hunt launch
      - Write blog post about building the app
      - Submit to app review websites
      - Create landing page for the app
      - Use app install ads (if budget allows)
    - **Celebrate Your Launch! 🎉🚀**
      - You've shipped a production app to app stores!
      - Add to your portfolio and resume
      - Share with friends and family
      - Prepare to demo in job interviews
      - Reflect on your 25-week journey

## Week 25 Deliverables Checklist

- ✅ App complies with App Store and Play Store guidelines
- ✅ All visual assets created (icons, screenshots, graphics)
- ✅ Privacy policy and terms of service published
- ✅ App Store Connect listing complete
- ✅ iOS app submitted for review
- ✅ Google Play Console listing complete
- ✅ Android app submitted for review
- ✅ Demo account credentials provided to reviewers
- ✅ Monitoring and crash reporting active
- ✅ App is live (or in review)!

## Key Takeaways

By the end of this week, you should:
- ✅ Know the complete app store submission process for iOS and Android
- ✅ Understand app store guidelines and compliance requirements
- ✅ Be able to create all required assets for app stores
- ✅ Navigate App Store Connect and Google Play Console
- ✅ Handle app reviews and rejections
- ✅ Have a live app in production!

## Resources

- [App Store Connect Help](https://help.apple.com/app-store-connect/)
- [Google Play Console Help](https://support.google.com/googleplay/android-developer/)
- [iOS App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [Google Play Policy Center](https://play.google.com/about/developer-content-policy/)
- [App Store Optimization Guide](https://www.apptamin.com/blog/app-store-optimization-guide/)
- [Privacy Policy Generator](https://www.privacypolicies.com/app-privacy-policy-generator/)

## Looking Ahead to Week 26

Congratulations! Your app is live or in review. Next week is the **final week** of your 180-day journey. You'll learn about advanced React Native topics (deep linking, accessibility, animations), optimize your portfolio, prepare for technical interviews, and plan your next steps as a job-ready React Native developer. The finish line is here! 🏁
