根据 @prompt/prompt.md 和 @prompt/template_minimal.md 生成内容 

需要参考 @Week05/README.md 中每一天的上下文 和 Learning Path.md

来生成 Day 4的内容

本次会超出output token maximum，拆分成两个markdown文档输出

# Week 05: Advanced Navigation Patterns & State Management

## Overview

This week, we will explore advanced navigation patterns that are essential for production mobile apps: authentication flows, modal screens, and preventing back navigation. You'll also learn how to integrate navigation with global state management and handle the navigation state lifecycle. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 4: Deep Linking & Universal Links
*   **Theory:** Read comprehensive guide on [Deep Linking](https://reactnavigation.org/docs/deep-linking) and [Configuring Links](https://reactnavigation.org/docs/configuring-links).
*   **Practice:**
    - Configure deep linking in `NavigationContainer` with a `linking` config
    - Define URL paths for your screens (e.g., `/article/:id` → ArticleDetail screen)
    - Test deep links in development using `npx uri-scheme open` command
    - Handle initial URL when app is opened from a deep link
    - Parse URL parameters and pass them to the correct screen
    - **Note:** Full implementation with iOS Universal Links and Android App Links requires native configuration (covered later)