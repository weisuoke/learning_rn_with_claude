# React Native入门 - 第1天：项目架构与工程化设置（第3部分）| React Native Introduction - Day 1: Project Architecture & Engineering Setup (Part 3)

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **Expo工作流理解强化练习 | Expo Workflow Understanding Reinforcement Exercise**
   - **练习描述 | Exercise Description:** 阅读Expo官方文档中关于Managed Workflow和Bare Workflow的对比，创建一个决策树文档，说明在什么情况下应该选择Managed Workflow，什么情况下需要使用expo prebuild转换为Bare Workflow。列举至少5个场景和对应的推荐方案。| Read Expo official documentation comparing Managed Workflow and Bare Workflow, create a decision tree document explaining when to choose Managed Workflow and when to use expo prebuild to convert to Bare Workflow. List at least 5 scenarios with corresponding recommendations.
   - **概念检查 | Concept Check:**
     - Expo Managed Workflow的主要限制是什么？| What are the main limitations of Expo Managed Workflow?
     - 执行expo prebuild后，项目结构会发生什么变化？| What changes occur to project structure after running expo prebuild?
   - **学习目标 | Learning Objective:** 深入理解Expo的两种工作流，能够根据项目需求做出合理的技术选型决策 | Deeply understand Expo's two workflows, able to make reasonable technical selection decisions based on project requirements

2. **导航架构实验练习 | Navigation Architecture Experimentation Exercise**
   - **练习描述 | Exercise Description:** 在当前的Bottom Tab + Stack混合架构基础上，尝试添加一个Modal Stack（用于显示全屏弹窗，如登录页面）。研究如何在RootNavigator中嵌套三层导航器：NavigationContainer > Modal Stack > Tab Navigator > Stack Navigator。实现一个"登录"按钮，点击后以模态方式显示登录屏幕。| Based on current Bottom Tab + Stack hybrid architecture, try adding a Modal Stack (for displaying fullscreen modals like login page). Research how to nest three navigator layers in RootNavigator: NavigationContainer > Modal Stack > Tab Navigator > Stack Navigator. Implement a "Login" button that displays login screen in modal mode when clicked.
   - **概念检查 | Concept Check:**
     - Modal模式的导航与普通Stack导航有什么视觉和交互差异？| What visual and interaction differences exist between Modal mode navigation and regular Stack navigation?
     - 如何从嵌套的深层屏幕中导航到Modal Stack的屏幕？| How to navigate to Modal Stack screen from deeply nested screen?
   - **学习目标 | Learning Objective:** 掌握复杂的多层导航架构设计，理解不同导航器的组合使用场景 | Master complex multi-layer navigation architecture design, understand combination usage scenarios of different navigators

3. **API错误处理深化练习 | API Error Handling Deepening Exercise**
   - **练习描述 | Exercise Description:** 扩展newsAPI.js服务层，实现以下高级错误处理功能：1) 网络离线检测（使用@react-native-community/netinfo）；2) 自动重试机制（失败后重试3次，每次间隔递增）；3) 错误日志记录（将错误发送到Sentry或控制台）；4) 用户友好的错误消息映射（将HTTP状态码转换为可读文本）。| Extend newsAPI.js service layer to implement following advanced error handling features: 1) Network offline detection (using @react-native-community/netinfo); 2) Automatic retry mechanism (retry 3 times after failure, with increasing intervals); 3) Error logging (send errors to Sentry or console); 4) User-friendly error message mapping (convert HTTP status codes to readable text).
   - **概念检查 | Concept Check:**
     - 网络请求失败（error.response为undefined）和HTTP错误响应（如404）有什么区别？| What's the difference between network request failure (error.response is undefined) and HTTP error response (like 404)?
     - 自动重试时，如何避免对已经失败的请求无限重试？| How to avoid infinite retries for already failed requests when implementing auto-retry?
   - **学习目标 | Learning Objective:** 提高API服务层的健壮性，学会处理各种网络异常场景 | Improve robustness of API service layer, learn to handle various network exception scenarios

4. **设计系统响应式扩展练习 | Design System Responsive Extension Exercise**
   - **练习描述 | Exercise Description:** 当前的设计令牌是固定值，在大屏设备（如平板）上可能显得过小。创建一个响应式设计系统，使用Dimensions API检测屏幕宽度，当屏幕宽度大于768px时，自动将FONT_SIZES和SPACING按1.2倍缩放。实现一个useResponsiveValue自定义Hook来简化组件中的响应式值获取。| Current design tokens are fixed values, may appear too small on large screen devices (like tablets). Create a responsive design system, use Dimensions API to detect screen width, automatically scale FONT_SIZES and SPACING by 1.2x when screen width exceeds 768px. Implement a useResponsiveValue custom Hook to simplify responsive value retrieval in components.
   - **概念检查 | Concept Check:**
     - React Native中如何监听屏幕尺寸变化（如设备旋转）？| How to listen to screen size changes (like device rotation) in React Native?
     - 响应式设计是否应该在每个组件中单独处理，还是集中在设计令牌层？| Should responsive design be handled individually in each component, or centralized in design token layer?
   - **学习目标 | Learning Objective:** 理解移动端响应式设计的实现方式，为多设备适配打下基础 | Understand implementation of mobile responsive design, lay foundation for multi-device adaptation

5. **项目结构优化练习 | Project Structure Optimization Exercise**
   - **练习描述 | Exercise Description:** 当前的导入路径使用相对路径（如../../constants/colors），在深层嵌套时会变得复杂。配置Babel Module Resolver或TypeScript的paths别名，将src目录映射为@符号，使得可以使用@/constants/colors这样的绝对路径导入。测试在不同深度的文件中使用别名导入，确保自动补全和点击跳转功能正常工作。| Current import paths use relative paths (like ../../constants/colors), which become complex in deep nesting. Configure Babel Module Resolver or TypeScript's paths alias to map src directory as @ symbol, allowing imports like @/constants/colors using absolute paths. Test alias imports in files at different depths, ensure auto-completion and click-to-jump functionality work properly.
   - **概念检查 | Concept Check:**
     - Babel Module Resolver配置后，为什么还需要在VSCode的jsconfig.json中配置paths？| After configuring Babel Module Resolver, why also need to configure paths in VSCode's jsconfig.json?
     - 路径别名会影响打包后的应用性能吗？| Do path aliases affect app performance after bundling?
   - **学习目标 | Learning Objective:** 提升项目开发效率，学会配置现代前端工具链 | Improve project development efficiency, learn to configure modern frontend toolchain

6. **API缓存策略练习 | API Caching Strategy Exercise**
   - **练习描述 | Exercise Description:** 为了减少API请求次数和提高应用响应速度，实现一个简单的内存缓存层。创建一个cacheManager工具，可以缓存API响应5分钟，相同的请求在5分钟内直接返回缓存数据。扩展newsAPI服务，使getTopHeadlines支持缓存功能。考虑如何处理缓存失效、手动清除缓存（pull-to-refresh时）等场景。| To reduce API request count and improve app responsiveness, implement a simple in-memory cache layer. Create a cacheManager utility that can cache API responses for 5 minutes, same requests within 5 minutes directly return cached data. Extend newsAPI service to support caching in getTopHeadlines. Consider how to handle cache invalidation, manual cache clearing (during pull-to-refresh), etc.
   - **概念检查 | Concept Check:**
     - 内存缓存和AsyncStorage缓存有什么区别？各适用于什么场景？| What's the difference between in-memory cache and AsyncStorage cache? What scenarios are each suitable for?
     - 如果用户打开应用、查看新闻、关闭应用、5分钟后重新打开，内存缓存还有效吗？| If user opens app, views news, closes app, reopens after 5 minutes, is in-memory cache still valid?
   - **学习目标 | Learning Objective:** 理解前端缓存策略，提高应用性能和用户体验 | Understand frontend caching strategies, improve app performance and user experience

7. **环境变量管理最佳实践练习 | Environment Variable Management Best Practices Exercise**
   - **练习描述 | Exercise Description:** 创建多环境配置方案，支持开发（development）、测试（staging）、生产（production）三个环境，每个环境使用不同的API密钥和BaseURL。研究如何使用app.config.js替代app.json，根据环境变量动态返回配置。实现脚本命令（如npm run start:staging）来启动不同环境的开发服务器。| Create multi-environment configuration solution supporting development, staging, and production environments, each using different API keys and BaseURLs. Research how to use app.config.js instead of app.json to dynamically return configuration based on environment variables. Implement script commands (like npm run start:staging) to start development server for different environments.
   - **概念检查 | Concept Check:**
     - app.json和app.config.js有什么区别？为什么后者可以动态配置？| What's the difference between app.json and app.config.js? Why can the latter dynamically configure?
     - 在生产构建时，如何确保正确的环境变量被编译到应用中？| When building for production, how to ensure correct environment variables are compiled into the app?
   - **学习目标 | Learning Objective:** 掌握专业的环境配置管理，为团队协作和CI/CD做准备 | Master professional environment configuration management, prepare for team collaboration and CI/CD

## 学习资源 | Learning Resources

### 官方文档 | Official Documentation
- [Expo Documentation](https://docs.expo.dev/) - Expo完整文档，包含所有API和配置说明 | Complete Expo documentation with all APIs and configuration instructions
- [React Navigation v6 Documentation](https://reactnavigation.org/docs/getting-started) - React Navigation官方指南，包含所有导航器类型和用法 | Official React Navigation guide with all navigator types and usage
- [NewsAPI Documentation](https://newsapi.org/docs) - NewsAPI的端点、参数和响应格式说明 | NewsAPI endpoints, parameters, and response format documentation
- [Axios Documentation](https://axios-http.com/docs/intro) - Axios的请求配置、拦截器、错误处理 | Axios request configuration, interceptors, error handling

### 设计系统资源 | Design System Resources
- [iOS Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) - Apple官方设计规范，包含颜色、字体、间距等设计令牌 | Apple official design specifications with color, font, spacing design tokens
- [Material Design 3](https://m3.material.io/) - Google的Material Design系统，可参考其设计原则 | Google's Material Design system, reference for design principles
- [Design Tokens Community Group](https://www.designtokens.org/) - 设计令牌标准化组织，提供最佳实践 | Design tokens standardization organization with best practices
- [8-Point Grid System](https://spec.fm/specifics/8-pt-grid) - 8点网格系统详解，理解间距令牌的设计原理 | Detailed explanation of 8-point grid system, understand spacing token design principles

### 项目架构参考 | Project Architecture References
- [React Native Folder Structure](https://www.reactnative.guide/3-react-native-styles/3.1-style-introduction.html) - React Native项目结构最佳实践 | React Native project structure best practices
- [Bulletproof React](https://github.com/alan2207/bulletproof-react) - React应用架构指南（可应用于React Native）| React application architecture guide (applicable to React Native)
- [Feature-Sliced Design](https://feature-sliced.design/) - 现代前端架构方法论（适用于大型项目）| Modern frontend architecture methodology (suitable for large projects)

### 视频教程 | Video Tutorials
- [React Navigation Deep Dive](https://www.youtube.com/results?search_query=react+navigation+v6+tutorial) - YouTube上的React Navigation深度教程 | React Navigation deep dive tutorials on YouTube
- [Expo for Beginners](https://www.youtube.com/results?search_query=expo+react+native+tutorial) - Expo入门完整课程 | Complete Expo beginner course

### 工具和库 | Tools and Libraries
- [React Native Debugger](https://github.com/jhen0409/react-native-debugger) - 强大的React Native调试工具 | Powerful React Native debugging tool
- [Reactotron](https://github.com/infinitered/reactotron) - React Native开发辅助工具，支持网络监控、状态查看 | React Native development assistant tool with network monitoring, state viewing
- [Expo Snack](https://snack.expo.dev/) - 在线React Native编辑器，用于快速原型验证 | Online React Native editor for quick prototype validation

### 社区和问答 | Community and Q&A
- [React Navigation Discord](https://discord.gg/reactnavigation) - React Navigation官方Discord频道 | Official React Navigation Discord channel
- [Expo Forums](https://forums.expo.dev/) - Expo社区论坛，可提问和搜索问题 | Expo community forum for asking and searching questions
- [Stack Overflow - React Native](https://stackoverflow.com/questions/tagged/react-native) - React Native相关问题的最大问答社区 | Largest Q&A community for React Native questions

---

## ✅ 完成检查清单 | Completion Checklist

### 核心概念理解检查 | Core Concept Understanding Check
- [ ] 能够解释Expo Managed Workflow和Bare Workflow的区别和适用场景 | Can explain difference between Expo Managed Workflow and Bare Workflow and applicable scenarios
- [ ] 理解Bottom Tab Navigator和Stack Navigator的嵌套关系和导航状态独立性 | Understand nesting relationship and navigation state independence of Bottom Tab Navigator and Stack Navigator
- [ ] 掌握API服务层的职责边界（数据获取vs数据展示）| Master responsibility boundaries of API service layer (data fetching vs data presentation)
- [ ] 理解设计令牌的作用和语义化命名的重要性 | Understand role of design tokens and importance of semantic naming

### 实践项目完成检查 | Practical Project Completion Check
- [ ] NewsReader项目已创建并可在Expo Go或模拟器中成功运行 | NewsReader project created and successfully running in Expo Go or simulator
- [ ] 文件夹结构已按照规划创建（src/screens, src/navigation等）| Folder structure created according to plan (src/screens, src/navigation, etc.)
- [ ] React Navigation已安装并配置，可以在4个Tab间自由切换 | React Navigation installed and configured, can freely switch between 4 Tabs
- [ ] 每个Tab的Stack Navigator已创建，可以从Tab根屏幕导航到子屏幕（即使是占位屏幕）| Stack Navigator for each Tab created, can navigate from Tab root screen to sub-screens (even placeholder screens)
- [ ] API服务层已实现，getTopHeadlines函数可以成功返回新闻数据（通过console.log验证）| API service layer implemented, getTopHeadlines function can successfully return news data (verified via console.log)
- [ ] 设计令牌已创建（colors.js, spacing.js, typography.js）并可以在组件中导入使用 | Design tokens created (colors.js, spacing.js, typography.js) and importable in components

### 代码质量检查 | Code Quality Check
- [ ] 所有组件都使用设计令牌（COLORS, SPACING, TEXT_STYLES）而不是硬编码的魔法数字 | All components use design tokens (COLORS, SPACING, TEXT_STYLES) instead of hard-coded magic numbers
- [ ] API密钥已存储在app.json的extra字段中，且.gitignore已配置（如果使用.env文件）| API key stored in app.json's extra field, and .gitignore configured (if using .env file)
- [ ] 代码中包含适当的注释，解释关键概念（如导航层级、拦截器功能）| Code includes appropriate comments explaining key concepts (like navigation hierarchy, interceptor functionality)
- [ ] 所有导入路径清晰可读（如果配置了路径别名则使用别名）| All import paths clear and readable (use aliases if path aliases configured)

### 错误处理和边界情况检查 | Error Handling and Edge Cases Check
- [ ] API服务层包含axios拦截器，统一处理HTTP错误（401, 429, 500等）| API service layer includes axios interceptors, uniformly handles HTTP errors (401, 429, 500, etc.)
- [ ] 测试了网络离线场景，应用不会崩溃（即使数据获取失败）| Tested network offline scenario, app doesn't crash (even if data fetch fails)
- [ ] NavigationContainer包裹在正确的位置（App.js或RootNavigator.js的最外层）| NavigationContainer wrapped in correct position (outermost layer of App.js or RootNavigator.js)

### 环境和工具检查 | Environment and Tools Check
- [ ] 开发服务器可以通过npm start或npx expo start成功启动 | Development server can successfully start via npm start or npx expo start
- [ ] 可以在iOS模拟器或Android模拟器中查看应用（或使用Expo Go扫码）| Can view app in iOS simulator or Android emulator (or use Expo Go scan)
- [ ] 已安装并配置了React Native开发工具（如React Native Debugger或使用Chrome DevTools）| Installed and configured React Native development tools (like React Native Debugger or using Chrome DevTools)

### 文档和知识沉淀检查 | Documentation and Knowledge Retention Check
- [ ] 能够用自己的话向他人解释本项目的导航架构（画出导航层级图）| Can explain project's navigation architecture to others in own words (draw navigation hierarchy diagram)
- [ ] 理解为什么需要将API逻辑分离到services层而不是直接在组件中fetch | Understand why API logic needs to be separated into services layer instead of fetching directly in components
- [ ] 能够列举至少3个设计令牌的好处（如一致性、可维护性、主题切换）| Can list at least 3 benefits of design tokens (like consistency, maintainability, theme switching)

### 扩展学习检查 | Extended Learning Check
- [ ] 至少完成3个扩展练习，验证了对概念的深入理解 | Completed at least 3 extension exercises, verified deep understanding of concepts
- [ ] 浏览了React Navigation官方文档，了解其他导航器类型（Drawer, Material Top Tabs）| Browsed React Navigation official documentation, learned about other navigator types (Drawer, Material Top Tabs)
- [ ] 阅读了Expo的Managed vs Bare Workflow文档，理解权衡取舍 | Read Expo's Managed vs Bare Workflow documentation, understand trade-offs

---

## 概念掌握验证 | Concept Mastery Verification

在标记完成前，请确保能够正确回答以下综合问题，并能够向他人清晰解释：| Before marking as complete, ensure you can correctly answer following comprehensive questions and explain clearly to others:

### 综合概念问题 | Comprehensive Concept Questions

1. **项目初始化决策 | Project Initialization Decision**
   - 如果你要开发一个需要使用蓝牙和NFC功能的应用，应该选择Expo Managed Workflow还是React Native CLI？为什么？| If you need to develop an app using Bluetooth and NFC features, should you choose Expo Managed Workflow or React Native CLI? Why?
   - **参考答案 | Reference Answer:** 取决于Expo是否支持这些功能。检查expo-bluetooth和相关包的可用性。如果Expo不支持，可以选择：1) 使用Expo Bare Workflow（expo prebuild）后添加原生模块；2) 直接使用React Native CLI。对于本新闻应用，Expo Managed足够 | Depends on whether Expo supports these features. Check availability of expo-bluetooth and related packages. If Expo doesn't support, can choose: 1) Use Expo Bare Workflow (expo prebuild) then add native modules; 2) Directly use React Native CLI. For this news app, Expo Managed is sufficient

2. **导航架构设计 | Navigation Architecture Design**
   - 为什么不直接在App.js中创建所有屏幕的Stack Navigator，而是采用Tab Navigator嵌套Stack Navigator的架构？| Why not directly create Stack Navigator for all screens in App.js, but adopt Tab Navigator nesting Stack Navigator architecture?
   - **参考答案 | Reference Answer:** 分层导航提供更好的用户体验和状态管理：1) 每个Tab维护独立的导航历史，切换Tab不丢失状态；2) 符合移动应用的导航模式（底部Tab是主导航）；3) 便于权限控制（如某些Tab需要登录）；4) 更容易扩展（添加新Tab不影响现有Tab）| Layered navigation provides better user experience and state management: 1) Each Tab maintains independent navigation history, switching Tabs doesn't lose state; 2) Conforms to mobile app navigation pattern (bottom Tab is main navigation); 3) Facilitates permission control (like some Tabs require login); 4) Easier to extend (adding new Tab doesn't affect existing Tabs)

3. **API服务层设计 | API Service Layer Design**
   - 如果将来需要将NewsAPI替换为Guardian API，需要修改哪些代码？如何设计服务层使这种替换的影响最小化？| If you need to replace NewsAPI with Guardian API in the future, what code needs modification? How to design service layer to minimize impact of such replacement?
   - **参考答案 | Reference Answer:** 理想情况下只修改src/services/newsAPI.js：1) 修改BaseURL和端点；2) 修改mapArticle函数将Guardian API的响应转换为应用内部数据模型；3) 服务层对外接口（getTopHeadlines等）保持不变，调用者无感知。关键是数据映射层的设计 | Ideally only modify src/services/newsAPI.js: 1) Modify BaseURL and endpoints; 2) Modify mapArticle function to transform Guardian API response to app's internal data model; 3) Service layer's external interface (getTopHeadlines, etc.) remains unchanged, callers unaware. Key is data mapping layer design

4. **设计令牌应用 | Design Token Application**
   - 如果产品经理要求实现"大字体模式"（所有字体放大1.5倍），应该如何利用设计令牌系统快速实现？| If product manager requests implementing "large font mode" (all fonts scaled 1.5x), how to quickly implement using design token system?
   - **参考答案 | Reference Answer:** 创建一个scaleFactor变量（存储在Context或AsyncStorage），修改FONT_SIZES导出为计算值（如xs: 12 * scaleFactor）。所有使用TEXT_STYLES的组件会自动响应变化。更优雅的方案是创建两套字体令牌（FONT_SIZES_NORMAL和FONT_SIZES_LARGE），根据用户设置切换 | Create a scaleFactor variable (stored in Context or AsyncStorage), modify FONT_SIZES export to computed values (like xs: 12 * scaleFactor). All components using TEXT_STYLES automatically respond to changes. More elegant solution is to create two font token sets (FONT_SIZES_NORMAL and FONT_SIZES_LARGE), switch based on user settings

5. **错误处理策略 | Error Handling Strategy**
   - 当用户在没有网络的情况下打开应用，应该在哪一层检测并处理这个错误？组件层、服务层还是拦截器层？| When user opens app without network, which layer should detect and handle this error? Component layer, service layer, or interceptor layer?
   - **参考答案 | Reference Answer:** 多层协作：1) 拦截器层捕获网络异常并抛出标准化错误；2) 服务层捕获并记录错误（可选：添加重试逻辑）；3) 组件层（或自定义Hook）处理错误并向用户展示友好消息（如"请检查网络连接"）。对于离线检测，可以在应用启动时使用NetInfo监听网络状态，全局提示用户 | Multi-layer collaboration: 1) Interceptor layer catches network exceptions and throws standardized errors; 2) Service layer catches and logs errors (optional: add retry logic); 3) Component layer (or custom Hook) handles errors and displays friendly message to user (like "Please check network connection"). For offline detection, can use NetInfo to listen to network state at app startup, globally notify user

能够清晰回答这些问题，说明你已经扎实掌握了Day 1的核心概念，可以自信地进入Day 2的学习。| Being able to clearly answer these questions indicates you have solidly mastered Day 1's core concepts and can confidently proceed to Day 2 learning.

---

**下一步 | Next Steps:**
完成今天的学习后，你已经建立了一个结构良好的React Native项目基础。明天（Day 2）我们将聚焦于API集成和数据获取，实现真正的新闻列表展示，包括加载状态、错误处理、下拉刷新等用户体验优化。| After completing today's learning, you have established a well-structured React Native project foundation. Tomorrow (Day 2) we will focus on API integration and data fetching, implementing real news list display, including loading states, error handling, pull-to-refresh and other UX optimizations.

**记住 | Remember:**
良好的项目架构和工程化设置是成功应用的基石。今天投入的时间会在未来一周的开发中得到回报——清晰的结构让添加新功能变得简单，设计令牌让UI调整变得轻松，服务层让API切换变得容易。| Good project architecture and engineering setup is the cornerstone of successful applications. Time invested today will pay off in the coming week's development — clear structure makes adding new features simple, design tokens make UI adjustments easy, service layer makes API switching easy.
