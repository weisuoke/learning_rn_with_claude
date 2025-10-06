# Week 24: CI/CD with Fastlane or EAS Build

## Overview

This week, you'll set up Continuous Integration and Continuous Deployment (CI/CD) for your React Native app. You'll automate testing, building, and deploying to beta channels using GitHub Actions, Fastlane (for bare React Native), or EAS Build (for Expo). This is essential for professional development workflows and saves hours of manual work. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 1: Understanding CI/CD & Choosing Your Tools
*   **Theory:** Read about [CI/CD for mobile apps](https://docs.expo.dev/build/introduction/). Understand the pipeline: code push → tests run → build → deploy.
*   **Practice:**
    - Choose your CI/CD approach:
      - **Expo projects:** EAS Build + EAS Submit (easiest)
      - **Bare React Native:** Fastlane + GitHub Actions or Bitrise
      - **Hybrid:** Mix of both
    - Set up version control best practices:
      - Use semantic versioning (1.0.0, 1.0.1, 1.1.0)
      - Create develop and main branches
      - Create release branches for app store builds
    - Choose CI platform:
      - **GitHub Actions** (free for public repos, includes private repos in free tier)
      - **Bitrise** (built for mobile, great UI, free tier available)
      - **CircleCI** (popular, good mobile support)
    - Create project on chosen CI platform
    - Link your GitHub repository

### Day 2: Setting Up GitHub Actions for Testing
*   **Task:**
    - Create `.github/workflows/test.yml`:
      ```yaml
      name: Test
      on:
        push:
          branches: [main, develop]
        pull_request:
          branches: [main, develop]

      jobs:
        test:
          runs-on: ubuntu-latest
          steps:
            - uses: actions/checkout@v3
            - uses: actions/setup-node@v3
              with:
                node-version: '18'
            - run: npm install
            - run: npm test -- --coverage --watchAll=false
            - run: npm run lint
      ```
    - Add linting to project: ESLint + Prettier
    - Configure coverage threshold (fail if < 70%)
    - Test the workflow:
      - Push code to GitHub
      - Verify workflow runs automatically
      - Check test results in Actions tab
    - Add status badge to README:
      - ![Tests](https://github.com/user/repo/workflows/Test/badge.svg)
    - Set up branch protection:
      - Require tests to pass before merge
      - Require code reviews for main branch

### Day 3: EAS Build Setup (Expo Projects)
*   **Task (for Expo projects):**
    - Install EAS CLI: `npm install -g eas-cli`
    - Login: `eas login`
    - Configure EAS Build: `eas build:configure`
    - Review `eas.json` configuration:
      ```json
      {
        "build": {
          "development": {
            "developmentClient": true,
            "distribution": "internal"
          },
          "preview": {
            "distribution": "internal",
            "ios": { "simulator": true }
          },
          "production": {
            "ios": { "simulator": false }
          }
        }
      }
      ```
    - Build for iOS simulator (fast test):
      - `eas build --platform ios --profile preview`
      - Wait for build to complete (~10-20 minutes)
      - Download and run in simulator
    - Build for Android APK (fast test):
      - `eas build --platform android --profile preview`
      - Download APK to device or emulator
    - Test builds thoroughly:
      - Verify all features work
      - Check app icon and splash screen
      - Test on multiple devices

### Day 3 Alternative: Fastlane Setup (Bare React Native)
*   **Task (for bare React Native):**
    - Install Fastlane: `brew install fastlane` (Mac) or `gem install fastlane`
    - Initialize Fastlane in iOS/Android folders:
      - `cd ios && fastlane init`
      - `cd android && fastlane init`
    - Create Fastlane lanes for iOS (`ios/fastlane/Fastfile`):
      ```ruby
      lane :beta do
        increment_build_number
        build_app(scheme: "SocialConnect")
        upload_to_testflight
      end
      ```
    - Create Fastlane lanes for Android:
      ```ruby
      lane :beta do
        gradle(task: "bundle", build_type: "Release")
        upload_to_play_store(track: "internal")
      end
      ```
    - Test lanes locally:
      - `fastlane ios beta` (requires Apple Developer account)
      - `fastlane android beta` (requires Google Play Console setup)
    - Handle code signing (complex - next day)

### Day 4: Code Signing & Credentials Management
*   **Task:**
    - **For Expo/EAS:**
      - Let EAS manage credentials: `eas credentials`
      - Or manually configure in `eas.json`
      - EAS handles provisioning profiles and certificates automatically
    - **For iOS (bare RN):**
      - Create Apple Developer account ($99/year)
      - Create App ID in Apple Developer Portal
      - Generate distribution certificate
      - Create provisioning profile for TestFlight
      - Use Fastlane Match for team certificate sharing:
        - `fastlane match init`
        - Stores certificates in encrypted Git repo
    - **For Android (bare RN):**
      - Generate keystore: `keytool -genkeypair -v -keystore my-release-key.keystore`
      - Store keystore securely (never commit to Git!)
      - Configure `android/gradle.properties` with keystore info
      - Use encrypted secrets in CI
    - **Store secrets securely:**
      - Use GitHub Secrets for sensitive data
      - Use environment variables in CI
      - Never commit credentials to version control
    - **Test code signing:**
      - Build signed iOS IPA
      - Build signed Android AAB
      - Verify signatures are valid

### Day 5: Automated Builds with GitHub Actions
*   **Task:**
    - Create `.github/workflows/build-ios.yml`:
      ```yaml
      name: Build iOS
      on:
        push:
          tags:
            - 'v*'

      jobs:
        build:
          runs-on: macos-latest
          steps:
            - uses: actions/checkout@v3
            - uses: actions/setup-node@v3
            - run: npm install
            - name: Build with EAS
              run: |
                npm install -g eas-cli
                eas build --platform ios --profile production --non-interactive
              env:
                EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
      ```
    - Create `.github/workflows/build-android.yml` (similar for Android)
    - **Alternative for Fastlane:**
      - Use fastlane action: `maierj/fastlane-action@v2.0.0`
      - Configure with Apple ID and passwords in secrets
    - Test automated build:
      - Create Git tag: `git tag v1.0.0 && git push --tags`
      - Verify workflow runs
      - Check build artifacts
    - Set up build caching to speed up CI:
      - Cache node_modules
      - Cache Gradle dependencies (Android)
      - Cache CocoaPods (iOS)

### Day 6: Deploying to TestFlight & Google Play Beta
*   **Task:**
    - **iOS TestFlight:**
      - Configure EAS Submit: `eas submit --platform ios`
      - Or use Fastlane: `upload_to_testflight`
      - Add external testers in App Store Connect
      - Set up beta app information
      - Submit build for beta review (usually fast)
    - **Android Internal Testing:**
      - Configure EAS Submit: `eas submit --platform android`
      - Or use Fastlane: `upload_to_play_store(track: 'internal')`
      - Create internal testing release in Google Play Console
      - Add testers (email list or Google Group)
    - **Automate deployment:**
      - Add deployment step to CI workflow
      - Deploy automatically on version tag push
      - Or require manual approval in workflow
    - **Test beta distribution:**
      - Install app from TestFlight on iOS device
      - Install app from Play Console on Android device
      - Verify all features work in production build
      - Collect feedback from beta testers

### Day 7: OTA Updates, Monitoring & CI/CD Best Practices
*   **Task:**
    - **Set up OTA (Over-The-Air) updates:**
      - **Expo:** Use `expo-updates`:
        - Publish updates: `eas update --branch production`
        - Users get JS updates without app store release
        - Configure update frequency and fallback
      - **Bare RN:** Use CodePush:
        - Install: `npm install react-native-code-push`
        - Integrate with app
        - Push updates: `code-push release-react`
    - **Implement version checks:**
      - Check for updates on app start
      - Prompt user to update if new version available
      - Force update for critical bug fixes
    - **Set up app monitoring:**
      - Sentry for crash reporting (already done in Week 20)
      - Firebase Analytics for user behavior
      - Performance monitoring with Firebase Performance
      - Track app version adoption rate
    - **CI/CD Best Practices:**
      - Use environment variables for different builds (dev, staging, prod)
      - Implement blue-green deployments (gradual rollout)
      - Set up automatic changelog generation from Git commits
      - Create release notes automatically
      - Tag releases in Git
      - Keep CI builds fast (< 10 minutes)
    - **Documentation:**
      - Document entire CI/CD pipeline
      - Add build instructions to README
      - Document how to trigger deployments
      - Create runbook for common CI issues
    - **Final Test:**
      - Make a code change
      - Push to GitHub
      - Verify tests run automatically
      - Create version tag
      - Verify build triggers
      - Check deployment to TestFlight/Play Beta
      - Celebrate automated pipeline! 🎉

## Week 24 Deliverables Checklist

- ✅ CI/CD pipeline set up and automated
- ✅ GitHub Actions workflows for testing and building
- ✅ EAS Build or Fastlane configured
- ✅ Code signing configured and secure
- ✅ Automated deployment to TestFlight (iOS)
- ✅ Automated deployment to Google Play Beta (Android)
- ✅ OTA updates configured
- ✅ App monitoring and analytics integrated
- ✅ Documentation for CI/CD process
- ✅ Successful end-to-end automated release

## Key Takeaways

By the end of this week, you should:
- ✅ Master CI/CD for React Native apps
- ✅ Automate testing, building, and deployment
- ✅ Understand code signing and certificate management
- ✅ Be able to deploy to beta channels with one command
- ✅ Implement OTA updates for rapid iteration
- ✅ Have a professional, production-grade deployment pipeline

## Resources

- [EAS Build Documentation](https://docs.expo.dev/build/introduction/)
- [Fastlane for iOS](https://docs.fastlane.tools/getting-started/ios/setup/)
- [Fastlane for Android](https://docs.fastlane.tools/getting-started/android/setup/)
- [GitHub Actions for Mobile](https://github.com/features/actions)
- [CodePush Documentation](https://github.com/microsoft/react-native-code-push)
- [Expo Updates](https://docs.expo.dev/versions/latest/sdk/updates/)

## Looking Ahead to Week 25

Next week, you'll navigate the **App Store Deployment Process** for both iOS and Android. You'll learn about app store guidelines, prepare all required assets (icons, screenshots, descriptions), and submit your app for review. This is the final step before your app goes live!
