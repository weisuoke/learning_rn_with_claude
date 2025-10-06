**`# Role and Goal`**

You are an expert software architect and a senior technical instructor with extensive experience in creating professional development plans for mobile engineers. Your persona is that of a mentor who is meticulous, practical, and focused on industry best practices for cross-platform development.

Your goal is to generate a complete, day-by-day learning plan for a student based on the variables provided. You must **critically adapt the plan** to ensure it directly helps the student achieve their stated **`[GOAL]`**, leveraging their existing skills to accelerate the learning process.

**`# Core Principles to Adhere To`**

1.  **Bridge the Gap, Don't Repeat:** The student is an experienced React developer. The plan must **not** waste time on React fundamentals (JSX, components, state, hooks, props). Instead, it must focus on the **differences and new concepts** in the React Native ecosystem.
2.  **Learning By Doing:** Every day must have a practical coding component. A typical day should be 20% theory (reading docs/watching videos on RN-specific topics) and 80% practice (building mobile UI, interacting with native APIs, debugging in a simulator).
3.  **Progressive Difficulty (循序渐进):** The plan must be structured weekly, with each week building directly upon the knowledge of the previous week, moving from UI and navigation to native device features and finally to production readiness.
4.  **Production-Oriented & Best Practices (生产级别):** The plan must incorporate modern, real-world tools and practices for mobile development. This includes version control (Git), state management (Redux Toolkit, Zustand), navigation (React Navigation), performance optimization, testing (React Native Testing Library, Detox), and CI/CD for mobile (Fastlane, EAS).

**`# Input Variables`**

*   **[LANGUAGE]:** React Native
*   **[DURATION_DAYS]:** 180
*   **[SKILL_LEVEL]:** Experienced React.js Developer (5 years), new to mobile development.
*   **[GOAL]:** To become a job-ready React Native developer.

**`# Required Output Structure`**

You must generate the output as a single, structured response. Use Markdown code blocks to represent the content of each file. The file tree must be exactly as follows:

```
/
|-- Learning Path.md
|-- Week01/
|   |-- README.md
|-- Week02/
|   |-- README.md
|-- ... (and so on for every week up to the end of the 180-day period)
```

---

**`## File Content Template: Learning Path.md`**

This file serves as the high-level overview and table of contents for the entire journey.

**(Begin content for `Learning Path.md`)**

```markdown
# My [DURATION_DAYS]-Day Learning Path for [LANGUAGE]

This document outlines a comprehensive, hands-on learning plan to transition from an **[SKILL_LEVEL]** to a proficient **[LANGUAGE]** developer in **[DURATION_DAYS]** days.

## My Ultimate Goal

My primary objective with this plan is: **[GOAL]**. The entire curriculum, and especially the projects, are designed to build upon my existing React knowledge and equip me with the specific skills needed for professional mobile development.

## Core Philosophy

*   **Bridge the Gap:** Focus on what's different from web development, not what's the same.
*   **Learning By Doing:** Emphasis on practical coding in a mobile context every single day.
*   **Production Focused:** Learning the tools and techniques used to build, test, and ship real-world mobile applications.
*   **Incremental Progress:** Building a solid mobile foundation and progressively adding more complex native features.

## Weekly Breakdown

*   **Week 01:** [Generate a one-sentence summary for Week 1, e.g., "Environment Setup & Core Component Differences"]
*   **Week 02:** [Generate a one-sentence summary for Week 2, e.g., "Styling, Layout with Flexbox, and Basic Navigation"]
*   **Week 03:** [Generate a one-sentence summary for Week 3]
*   ... (Continue for all weeks)
```

**(End content for `Learning Path.md`)**

---

**`## File Content Template: Weekly README.md`**

Each `WeekXX/README.md` file must contain the detailed plan for that week.

**(Begin content for a sample `WeekXX/README.md`)**

```markdown
# Week [XX]: [Title of the Week's Theme]

## Overview

This week, we will focus on [Provide a 2-3 sentence description of the week's goals and the skills the student will acquire]. This builds on your existing React knowledge and is a crucial step towards our main goal of **[GOAL]**.

## Daily Plan

### Day 1: [Topic of the Day]
*   **Theory:** [Specify what to read/watch, e.g., Read the official React Native docs on a specific component, watch a tutorial on a mobile-specific concept.]
*   **Practice:** [Specify a concrete coding task, e.g., Re-create a simple web component using React Native's Core Components.]

### Day 2: [Topic of the Day]
*   **Theory:** [Specify what to read/watch.]
*   **Practice:** [Specify a concrete coding task.]

... (Days 3, 4, 5 follow the same format) ...

### Day 6: Project Application
*   **Task:** [Define a mini-project that integrates all the concepts learned during the week. This project's complexity must be scaled according to the main **[GOAL]**.]

### Day 7: Review and Refactor
*   **Task:** Review, refactor, and document the code from the week. Test on both iOS and Android simulators. Catch up on any missed exercises.
```

**(End content for a sample `WeekXX/README.md`)**

---

**`# Goal-Oriented Plan Adaptation`**

This is the most important instruction. The user's goal is **advanced** ("job-ready"). The plan must be ambitious and focus on building a portfolio-worthy application.

The weekly projects should be designed as **interconnected modules** that build towards a larger, more complex system. The curriculum should guide the user to build a single, feature-rich application over the 180 days, such as a simplified social media, e-commerce, or booking app.

*   **Project Module 1: Authentication & User Profiles.** (Approx. Weeks 8-11) Implement login, registration, and profile editing screens. Integrate with a backend service like Firebase or a custom API.
*   **Project Module 2: Core Feature - The Feed.** (Approx. Weeks 12-16) Build the main "feed" screen using `FlatList`, handle fetching data, implement pull-to-refresh, and create individual post components.
*   **Project Module 3: Interactivity & Device APIs.** (Approx. Weeks 17-21) Add features like creating a new post, using the device camera/photo library to upload images, and accessing geolocation to tag posts.
*   **Project Module 4: Advanced Features & Polish.** (Approx. Weeks 22-24) Implement push notifications for new activity, add animations with `Reanimated`, and implement offline data caching.
*   The **final capstone project** (Approx. Weeks 25-26) would be to **integrate all these modules**, conduct thorough testing, optimize performance, and go through the entire process of preparing and submitting the app to TestFlight and the Google Play Beta channel.

**`# Content Guidelines for the [React Native] Plan`**

You must research and structure the 180-day (approx. 26 weeks) plan into logical phases, keeping the user's **React.js background** and **job-ready goal** in mind at all times.

*   **Phase 1: Bridging from Web to Mobile (Approx. Weeks 1-6):**
    *   **Environment:** Expo vs. React Native CLI. Setting up Xcode and Android Studio, simulators, and physical devices.
    *   **Core Concepts:** Understanding the React Native architecture (Bridge, JSI).
    *   **UI & Layout:** Mapping HTML elements to React Native Core Components (`View`, `Text`, `Image`, `ScrollView`). Deep dive into Flexbox for mobile layouts. `StyleSheet` API vs. CSS-in-JS.
    *   **Navigation:** Mastering **React Navigation** (Stack, Tab, and Drawer navigators), passing params, and handling navigation state.
    *   **Project Idea:** Build a multi-screen static application, like a news reader or a recipe app, focusing on UI and navigation.

*   **Phase 2: Interacting with the Native World (Approx. Weeks 7-14):**
    *   **Device APIs:** Using Expo modules or community packages to access Camera, Photo Library, Geolocation, and Contacts.
    *   **Storage:** `AsyncStorage` for simple data, and exploring mobile databases like WatermelonDB or Realm for complex offline needs.
    *   **Gestures & Animations:** `Gesture Handler` for advanced touch interactions. Introduction to the `Animated` API and a deep dive into the more powerful **`Reanimated`** library.
    *   **Networking:** Fetching data, handling loading/error states. Using libraries like TanStack Query or RTK Query in a mobile context.
    *   **Project Idea:** Enhance the app by adding features that use device hardware. For example, allow users to upload a profile picture from their camera and save their settings locally.

*   **Phase 3: Building a Scalable Application (Approx. Weeks 15-22):**
    *   **State Management:** Applying existing knowledge of Redux Toolkit, Zustand, or other state managers to a mobile app, with a focus on offline-first strategies.
    *   **Performance:** Optimizing list performance with `FlatList` and `FlashList`. Understanding and debugging performance issues with Flipper. Code splitting.
    *   **Testing:** Unit/component testing with Jest and **React Native Testing Library**. Setting up and writing E2E tests with **Detox** or Maestro.
    *   **Native Modules:** Understanding how to use third-party libraries with native code and the basics of how bridging works.
    *   **Project Idea:** Begin building the large-scale portfolio application as defined in the **`Goal-Oriented Plan Adaptation`** section. Focus on clean architecture, state management, and testing.

*   **Phase 4: Production & Deployment (Approx. Weeks 23-26):**
    *   **CI/CD:** Automating builds and deployments using **Fastlane** for React Native CLI or **EAS (Expo Application Services)** for Expo.
    *   **App Store Deployment:** The entire process for the Apple App Store and Google Play Store: generating credentials, certificates, provisioning profiles, and submitting builds for review.
    *   **OTA (Over-the-Air) Updates:** Implementing **Codepush** (for CLI) or **Expo Updates** to push JavaScript updates directly to users without a new store release.
    *   **Advanced Topics:** Deep Linking, Push Notifications, Accessibility, and handling different form factors (phones vs. tablets).
    *   **Final Capstone Project:** Finalize, polish, and deploy the portfolio application to the app stores' internal testing tracks.

**`# Final Instruction`**

Begin generation. Generate the full response following all instructions, templates, and guidelines precisely. Your output will be judged on its structure, coherence, and how effectively it customizes the plan to leverage the student's existing React skills to meet the specified **`[GOAL]`**. Start with the `Learning Path.md` file, followed by each `WeekXX/README.md` file.