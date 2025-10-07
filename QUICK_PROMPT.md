根据 @prompt/prompt.md 和 @prompt/template_minimal.md 生成内容 

需要参考 @Week06/README.md 中每一天的上下文 和 Learning Path.md

来生成 Day 3的内容

本次会超出output token maximum，拆分成3个markdown文档输出

# Week 06: Building a Multi-Screen Static Application (News Reader Project)

## Overview

This week is a consolidation week where you'll apply everything you've learned in Weeks 1-5 to build a complete, polished news reader application. This project will integrate real API calls, complex navigation, responsive layouts, and production-quality UI. You'll spend the entire week on this single project, treating it like a real client deliverable. This builds on your existing React knowledge and is a crucial step towards our main goal of **becoming a job-ready React Native developer**.

## Daily Plan

### Day 3: Building the Article List & Detail Screens
*   **Task:**
    - Create an `ArticleCard` component with:
      - Article image (with fallback if no image)
      - Title, description, source, and publish date
      - Pressable to navigate to detail screen
    - Build the `ArticleDetailScreen` with:
      - Full article image
      - Article content
      - Custom header with "Share" and "Bookmark" buttons
      - "Read Full Article" button that opens external URL (use `Linking.openURL()`)
    - Pass article data as navigation params from list to detail screen
    - Implement proper loading states for images
    - Style with cards, shadows, and proper typography hierarchy