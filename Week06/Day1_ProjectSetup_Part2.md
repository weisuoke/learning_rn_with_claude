# React Native入门 - 第1天：项目架构与工程化设置（第2部分）| React Native Introduction - Day 1: Project Architecture & Engineering Setup (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 3. API集成规划与密钥管理 | API Integration Planning & Key Management (45分钟 | 45 minutes)

- **新闻API选择与注册 | News API Selection & Registration**

  **概念定义 | Concept Definition:**
  第三方API集成是指将外部服务提供的数据接口整合到应用中的过程。对于新闻阅读器应用，我们需要选择一个可靠的新闻API服务，如NewsAPI.org（提供免费层级，每天100个请求）或New York Times API（需要注册但更稳定）。API密钥（API Key）是服务商用来识别和授权API请求的令牌，必须妥善管理以防止滥用和泄露。| Third-party API integration refers to the process of incorporating data interfaces provided by external services into an application. For a news reader app, we need to select a reliable news API service, such as NewsAPI.org (offering free tier with 100 requests per day) or New York Times API (requires registration but more stable). API Key is a token used by service providers to identify and authorize API requests, which must be properly managed to prevent abuse and leakage.

  **核心特征 | Key Characteristics:**
  - **免费层级限制**：NewsAPI.org免费版每天限100请求，开发时足够，生产环境需考虑升级或缓存策略 | **Free Tier Limits**: NewsAPI.org free version limits 100 requests per day, sufficient for development, production needs upgrade or caching strategy consideration
  - **端点（Endpoints）**：现代REST API提供多个端点如/top-headlines（头条）、/everything（搜索所有文章）、/sources（新闻来源）| **Endpoints**: Modern REST APIs provide multiple endpoints like /top-headlines (headlines), /everything (search all articles), /sources (news sources)
  - **查询参数**：通过URL参数（query parameters）控制返回结果，如country=us、category=technology、q=bitcoin | **Query Parameters**: Control return results through URL parameters, like country=us, category=technology, q=bitcoin
  - **响应格式**：通常返回JSON格式，包含status、totalResults、articles等字段 | **Response Format**: Usually returns JSON format, containing fields like status, totalResults, articles
  - **速率限制（Rate Limiting）**：API服务会限制单位时间内的请求次数，超限会返回429错误 | **Rate Limiting**: API services limit requests within unit time, exceeding limit returns 429 error

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 可以将API密钥直接写在代码中并提交到GitHub吗？| Can you write API keys directly in code and commit to GitHub?
     **答案 | Answer:** 否 | No - 这是严重的安全隐患。API密钥应存储在环境变量或配置文件中（如.env文件），并将该文件添加到.gitignore，防止泄露后被滥用产生巨额账单 | This is a serious security risk. API keys should be stored in environment variables or config files (like .env file), and add that file to .gitignore to prevent leakage and abuse causing huge bills
  2. NewsAPI的/top-headlines端点和/everything端点有什么区别？| What's the difference between NewsAPI's /top-headlines and /everything endpoints?
     **答案 | Answer:** 用途不同 | Different purposes - /top-headlines返回当前正在头条位置的新闻（通常是最新最热的），可按国家和类别筛选；/everything返回符合搜索条件的所有历史新闻（最多一个月内），适合关键词搜索 | /top-headlines returns current headline news (usually latest and hottest), can filter by country and category; /everything returns all historical news matching search criteria (up to one month), suitable for keyword searches
  3. 如果免费API的请求次数用完了，应该怎么办？| If you've used up the free API request quota, what should you do?
     **答案 | Answer:** 开发时的解决方案 | Solutions during development - 1) 注册多个API密钥（注意遵守服务条款）；2) 使用mock数据进行开发和测试；3) 实现请求缓存减少重复调用；4) 升级到付费计划 | 1) Register multiple API keys (follow terms of service); 2) Use mock data for development and testing; 3) Implement request caching to reduce duplicate calls; 4) Upgrade to paid plan
  4. API响应中的totalResults字段表示什么？| What does the totalResults field in API response represent?
     **答案 | Answer:** 匹配查询的文章总数 | Total number of articles matching the query - 不是当前返回的文章数量，而是所有符合条件的文章总数。通过pageSize和page参数可以分页获取这些文章 | Not the number of articles currently returned, but total articles matching criteria. Can paginate through these articles using pageSize and page parameters

  **代码示例与验证 | Code Examples and Verification:**
  ```bash
  # 注册NewsAPI并获取API密钥 | Register NewsAPI and obtain API key
  # 1. 访问 https://newsapi.org/register | Visit https://newsapi.org/register
  # 2. 填写邮箱和密码进行注册 | Fill in email and password to register
  # 3. 在Dashboard中复制API Key | Copy API Key from Dashboard

  # 安装环境变量管理工具 | Install environment variable management tool
  npm install react-native-dotenv
  # 或使用Expo内置的环境变量支持（expo-constants）| Or use Expo's built-in environment variable support (expo-constants)
  npx expo install expo-constants
  ```

  ```javascript
  // .env - 环境变量文件（此文件不应提交到版本控制）| Environment variable file (should NOT be committed to version control)
  NEWS_API_KEY=your_actual_api_key_here
  NEWS_API_BASE_URL=https://newsapi.org/v2
  ```

  ```gitignore
  # .gitignore - 确保添加以下行 | Ensure adding following lines
  .env
  .env.local
  .env.*.local
  ```

  ```javascript
  // src/config/api.config.js - API配置文件 | API configuration file
  import Constants from 'expo-constants';

  /**
   * API配置中心 | API Configuration Center
   *
   * 安全提示: | Security Note:
   * - 生产环境中，考虑使用环境变量或安全存储 | In production, consider using environment variables or secure storage
   * - 不要将API密钥硬编码在源代码中 | Never hard-code API keys in source code
   * - 对于高度敏感的应用，考虑使用代理服务器隐藏真实API密钥 | For highly sensitive apps, consider using proxy server to hide real API keys
   */
  export const API_CONFIG = {
    NEWS_API: {
      BASE_URL: 'https://newsapi.org/v2',
      // 开发环境：从环境变量读取 | Development: read from environment variables
      // 注意：Expo中使用Constants.expoConfig.extra读取app.json中的extra字段 | Note: In Expo, use Constants.expoConfig.extra to read extra field from app.json
      API_KEY: Constants.expoConfig?.extra?.newsApiKey || 'demo_key',
      ENDPOINTS: {
        TOP_HEADLINES: '/top-headlines',
        EVERYTHING: '/everything',
        SOURCES: '/sources',
      },
      DEFAULT_PARAMS: {
        country: 'us',     // 默认国家 | Default country
        pageSize: 20,      // 每页文章数 | Articles per page
        language: 'en',    // 语言 | Language
      },
    },
  };

  // 可用的新闻分类 | Available news categories
  export const NEWS_CATEGORIES = [
    'business',
    'entertainment',
    'general',
    'health',
    'science',
    'sports',
    'technology',
  ];
  ```

  ```json
  // app.json - 在Expo配置中添加环境变量 | Add environment variables in Expo configuration
  {
    "expo": {
      "name": "NewsReader",
      "slug": "news-reader",
      "extra": {
        "newsApiKey": "your_actual_api_key_here"
      }
    }
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如何测试API密钥是否有效？| How to test if API key is valid?
    **答案 | Answer:** 使用curl或Postman发送测试请求 | Use curl or Postman to send test request:
    ```bash
    curl "https://newsapi.org/v2/top-headlines?country=us&apiKey=YOUR_API_KEY"
    # 成功返回status: "ok"和文章列表 | Success returns status: "ok" and article list
    # 失败返回status: "error"和错误信息 | Failure returns status: "error" and error message
    ```
  - 在Expo应用中，如何在运行时访问app.json中的extra字段？| In Expo app, how to access extra field from app.json at runtime?
    **答案 | Answer:** 使用expo-constants包：`import Constants from 'expo-constants'; const apiKey = Constants.expoConfig.extra.newsApiKey;` | Use expo-constants package: `import Constants from 'expo-constants'; const apiKey = Constants.expoConfig.extra.newsApiKey;`

  **常见误区检查 | Common Misconception Checks:**
  - 在React Native中可以直接使用process.env访问环境变量吗？| Can you directly use process.env to access environment variables in React Native?
    **答案 | Answer:** 不完全可以 | Not entirely - 与Node.js不同，React Native默认不支持process.env。需要使用babel-plugin-transform-inline-environment-variables或react-native-dotenv等工具，或在Expo中使用app.json的extra字段配合expo-constants | Unlike Node.js, React Native doesn't support process.env by default. Need to use tools like babel-plugin-transform-inline-environment-variables or react-native-dotenv, or in Expo use app.json's extra field with expo-constants
  - 将API密钥存储在AsyncStorage中是安全的做法吗？| Is storing API keys in AsyncStorage a secure practice?
    **答案 | Answer:** 不推荐 | Not recommended - AsyncStorage在iOS和Android上都是未加密的存储，容易被越狱/root设备访问。真正的密钥应该存储在服务端，客户端只存储临时令牌（token）。对于新闻API这类低敏感场景，可以将密钥编译到应用中（通过app.json），但不应存储用户敏感信息 | AsyncStorage is unencrypted storage on both iOS and Android, easily accessible on jailbroken/rooted devices. Real keys should be stored on server-side, client should only store temporary tokens. For low-sensitivity scenarios like news API, can compile key into app (via app.json), but shouldn't store user-sensitive information

- **API服务层架构设计 | API Service Layer Architecture Design**

  **概念定义 | Concept Definition:**
  API服务层是应用中专门负责与外部服务通信的模块，它封装了所有HTTP请求逻辑、错误处理、数据转换等功能，向应用其他部分提供干净的、类型化的数据接口。良好的服务层设计使得API调用可复用、可测试，并且易于替换底层HTTP客户端（如从axios切换到fetch）。| API service layer is a module in the application specifically responsible for communicating with external services. It encapsulates all HTTP request logic, error handling, data transformation, etc., providing clean, typed data interfaces to other parts of the application. Good service layer design makes API calls reusable, testable, and easy to switch underlying HTTP clients (like from axios to fetch).

  **核心特征 | Key Characteristics:**
  - **单一职责**：服务层只负责数据获取，不包含UI逻辑或状态管理 | **Single Responsibility**: Service layer only handles data fetching, no UI logic or state management
  - **统一错误处理**：在服务层统一处理HTTP错误（404、500）、网络错误、超时等，返回标准化的错误对象 | **Unified Error Handling**: Uniformly handle HTTP errors (404, 500), network errors, timeouts in service layer, return standardized error objects
  - **数据转换（Mapping）**：将API返回的原始数据转换为应用内部使用的数据模型，隐藏API结构变化 | **Data Transformation (Mapping)**: Transform raw data returned by API into data models used internally in the app, hiding API structure changes
  - **可测试性**：服务层函数应该是纯异步函数，容易编写单元测试和模拟（mock）| **Testability**: Service layer functions should be pure async functions, easy to write unit tests and mocks
  - **请求拦截器（Interceptors）**：可以在所有请求前自动添加认证头、日志记录等 | **Request Interceptors**: Can automatically add authentication headers, logging, etc. before all requests

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果NewYork Times API替换成了另一个新闻API，应该修改哪些代码？| If NewYork Times API is replaced with another news API, which code should be modified?
     **答案 | Answer:** 主要修改服务层 | Mainly modify service layer - 理想情况下，只需修改src/services/newsAPI.js中的请求逻辑和数据转换，应用的其他部分（组件、hooks）不应感知API的变化，因为服务层返回的数据格式保持一致 | Ideally, only need to modify request logic and data transformation in src/services/newsAPI.js, other parts of the app (components, hooks) shouldn't sense API changes, because data format returned by service layer remains consistent
  2. 服务层函数应该返回什么？原始的axios响应对象还是处理后的数据？| What should service layer functions return? Raw axios response objects or processed data?
     **答案 | Answer:** 处理后的数据 | Processed data - 服务层应该返回清洗过的、业务相关的数据（如文章数组），而不是原始的HTTP响应对象。这样调用者不需要知道response.data.articles的结构，只需要articles | Service layer should return cleaned, business-relevant data (like article arrays), not raw HTTP response objects. This way callers don't need to know the structure of response.data.articles, only need articles
  3. 错误处理应该在服务层还是在调用服务的组件中进行？| Should error handling be done in service layer or in components calling the service?
     **答案 | Answer:** 两者都需要 | Both are needed - 服务层应该捕获和标准化底层错误（如网络异常、HTTP状态码错误），然后抛出或返回统一格式的错误；组件层负责决定如何向用户展示这些错误（显示Toast、错误页面等）| Service layer should catch and standardize low-level errors (like network exceptions, HTTP status code errors), then throw or return uniformly formatted errors; component layer decides how to display these errors to users (show Toast, error page, etc.)
  4. 为什么需要为API服务创建单独的文件夹（/services）而不是直接在组件中写fetch代码？| Why create a separate folder (/services) for API services instead of writing fetch code directly in components?
     **答案 | Answer:** 复用性和可维护性 | Reusability and maintainability - 同一个API可能被多个组件调用（如HomeScreen和CategoryScreen都需要获取文章），将逻辑集中在服务层避免重复代码。同时便于统一修改请求逻辑、添加缓存、切换API等 | Same API may be called by multiple components (like HomeScreen and CategoryScreen both need to fetch articles), centralizing logic in service layer avoids duplicate code. Also facilitates unified modification of request logic, adding caching, switching APIs, etc.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // src/services/newsAPI.js - 新闻API服务层完整实现 | News API service layer complete implementation
  import axios from 'axios';
  import { API_CONFIG } from '../config/api.config';

  /**
   * 创建配置好的axios实例 | Create configured axios instance
   * 所有通过此实例发送的请求都会自动添加基础配置 | All requests sent through this instance automatically add base configuration
   */
  const newsAPIClient = axios.create({
    baseURL: API_CONFIG.NEWS_API.BASE_URL,
    timeout: 10000, // 10秒超时 | 10 seconds timeout
    headers: {
      'Content-Type': 'application/json',
    },
  });

  /**
   * 请求拦截器 - 在每个请求发送前自动添加API密钥 | Request interceptor - Automatically add API key before each request
   */
  newsAPIClient.interceptors.request.use(
    (config) => {
      // 将API密钥添加到查询参数中 | Add API key to query parameters
      config.params = {
        ...config.params,
        apiKey: API_CONFIG.NEWS_API.API_KEY,
      };
      return config;
    },
    (error) => {
      return Promise.reject(error);
    }
  );

  /**
   * 响应拦截器 - 统一处理HTTP错误 | Response interceptor - Uniformly handle HTTP errors
   */
  newsAPIClient.interceptors.response.use(
    (response) => {
      // 检查NewsAPI特定的错误格式 | Check NewsAPI-specific error format
      if (response.data.status === 'error') {
        throw new Error(response.data.message || 'API请求失败 | API request failed');
      }
      return response;
    },
    (error) => {
      // 网络错误或超时 | Network error or timeout
      if (!error.response) {
        throw new Error('网络连接失败，请检查网络设置 | Network connection failed, please check network settings');
      }

      // HTTP状态码错误处理 | HTTP status code error handling
      const { status } = error.response;
      switch (status) {
        case 401:
          throw new Error('API密钥无效或已过期 | API key invalid or expired');
        case 429:
          throw new Error('请求过于频繁，请稍后再试 | Too many requests, please try again later');
        case 500:
          throw new Error('服务器错误，请稍后再试 | Server error, please try again later');
        default:
          throw new Error(`请求失败: ${status} | Request failed: ${status}`);
      }
    }
  );

  /**
   * 数据转换函数 - 将API返回的文章数据转换为应用内部格式 | Data transformation - Convert article data from API to internal format
   * 这一层抽象使得应用不依赖于NewsAPI的具体数据结构 | This abstraction makes app independent of NewsAPI's specific data structure
   */
  const mapArticle = (apiArticle) => ({
    id: apiArticle.url, // 使用URL作为唯一标识 | Use URL as unique identifier
    title: apiArticle.title,
    description: apiArticle.description,
    content: apiArticle.content,
    url: apiArticle.url,
    imageUrl: apiArticle.urlToImage,
    publishedAt: apiArticle.publishedAt,
    source: {
      id: apiArticle.source.id,
      name: apiArticle.source.name,
    },
    author: apiArticle.author,
  });

  /**
   * NewsAPI服务对象 - 导出所有新闻相关的API调用函数 | NewsAPI service object - Export all news-related API call functions
   */
  export const newsAPI = {
    /**
     * 获取头条新闻 | Get top headlines
     * @param {Object} params - 查询参数 | Query parameters
     * @param {string} params.country - 国家代码 (如 'us', 'cn') | Country code (like 'us', 'cn')
     * @param {string} params.category - 新闻类别 | News category
     * @param {number} params.page - 页码（从1开始）| Page number (starts from 1)
     * @returns {Promise<Array>} - 返回文章数组 | Returns article array
     */
    getTopHeadlines: async (params = {}) => {
      try {
        const response = await newsAPIClient.get(API_CONFIG.NEWS_API.ENDPOINTS.TOP_HEADLINES, {
          params: {
            country: params.country || API_CONFIG.NEWS_API.DEFAULT_PARAMS.country,
            category: params.category,
            page: params.page || 1,
            pageSize: params.pageSize || API_CONFIG.NEWS_API.DEFAULT_PARAMS.pageSize,
          },
        });

        // 转换数据格式 | Transform data format
        const articles = response.data.articles.map(mapArticle);
        return {
          articles,
          totalResults: response.data.totalResults,
        };
      } catch (error) {
        console.error('获取头条新闻失败: | Failed to fetch top headlines:', error.message);
        throw error;
      }
    },

    /**
     * 按类别获取新闻 | Get news by category
     * @param {string} category - 新闻类别 | News category
     * @returns {Promise<Array>} - 返回文章数组 | Returns article array
     */
    getArticlesByCategory: async (category) => {
      return newsAPI.getTopHeadlines({ category });
    },

    /**
     * 搜索新闻文章 | Search news articles
     * @param {string} query - 搜索关键词 | Search keyword
     * @param {Object} params - 额外参数 | Additional parameters
     * @returns {Promise<Array>} - 返回搜索结果 | Returns search results
     */
    searchArticles: async (query, params = {}) => {
      try {
        const response = await newsAPIClient.get(API_CONFIG.NEWS_API.ENDPOINTS.EVERYTHING, {
          params: {
            q: query,
            language: params.language || API_CONFIG.NEWS_API.DEFAULT_PARAMS.language,
            sortBy: params.sortBy || 'publishedAt', // 可选: publishedAt, relevancy, popularity
            page: params.page || 1,
            pageSize: params.pageSize || API_CONFIG.NEWS_API.DEFAULT_PARAMS.pageSize,
          },
        });

        const articles = response.data.articles.map(mapArticle);
        return {
          articles,
          totalResults: response.data.totalResults,
        };
      } catch (error) {
        console.error('搜索文章失败: | Failed to search articles:', error.message);
        throw error;
      }
    },
  };

  export default newsAPI;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果要添加请求日志记录，应该在代码的哪个位置添加？| If you want to add request logging, where in the code should it be added?
    **答案 | Answer:** 在axios拦截器中 | In axios interceptors - 在请求拦截器中记录发送的请求，在响应拦截器中记录返回的响应，这样所有API调用都会自动记录，无需在每个函数中重复代码 | Log sent requests in request interceptor, log returned responses in response interceptor, so all API calls are automatically logged without repeating code in each function
  - mapArticle函数的作用是什么？可以省略它直接返回API数据吗？| What's the purpose of mapArticle function? Can you omit it and return API data directly?
    **答案 | Answer:** 数据标准化和解耦 | Data standardization and decoupling - 可以省略，但不推荐。mapArticle将API的数据结构转换为应用的数据模型，如果将来API结构改变或切换到其他API，只需修改mapArticle函数，而不需要修改所有使用数据的组件 | Can omit, but not recommended. mapArticle converts API data structure to app's data model. If API structure changes or switches to another API in the future, only need to modify mapArticle function, not all components using the data

  **常见误区检查 | Common Misconception Checks:**
  - 每次API调用都需要手动添加try-catch来捕获错误吗？| Does every API call need manual try-catch to catch errors?
    **答案 | Answer:** 取决于设计 | Depends on design - 如果在服务层的每个函数中已经try-catch并重新抛出标准化错误，调用者仍需要try-catch来处理这些错误。另一种模式是让服务层函数返回`{data, error}`对象而不抛出异常，避免调用者必须try-catch | If each function in service layer already has try-catch and rethrows standardized errors, callers still need try-catch to handle these errors. Another pattern is to have service layer functions return `{data, error}` objects instead of throwing exceptions, avoiding mandatory try-catch for callers
  - 设置axios的timeout为10秒后，所有请求都会在10秒后自动失败吗？| After setting axios timeout to 10 seconds, will all requests automatically fail after 10 seconds?
    **答案 | Answer:** 是的，但有细节 | Yes, but with nuances - timeout是指整个请求-响应周期的最大时长。如果10秒内服务器没有响应完成，axios会取消请求并触发超时错误。但如果响应已经开始返回（如大文件下载），timeout可能不会中断正在进行的数据传输 | timeout refers to maximum duration of entire request-response cycle. If server doesn't complete response within 10 seconds, axios cancels request and triggers timeout error. But if response has already started returning (like large file download), timeout may not interrupt ongoing data transfer

### 4. 设计系统与主题配置 | Design System & Theme Configuration (30分钟 | 30 minutes)

- **设计令牌（Design Tokens）| Design Tokens**

  **概念定义 | Concept Definition:**
  设计令牌是设计系统中最小的、不可再分的设计决策单元，包括颜色、字体大小、间距、圆角等视觉属性。通过将这些硬编码的样式值提取为常量，可以确保整个应用的视觉一致性，并且便于全局修改主题（如从浅色主题切换到深色主题）。设计令牌是Material Design、iOS Human Interface Guidelines等设计规范的基础实现。| Design tokens are the smallest, indivisible design decision units in a design system, including colors, font sizes, spacing, border radius, and other visual properties. By extracting these hard-coded style values as constants, you can ensure visual consistency across the entire application and facilitate global theme modifications (like switching from light to dark theme). Design tokens are the foundational implementation of design specifications like Material Design and iOS Human Interface Guidelines.

  **核心特征 | Key Characteristics:**
  - **语义化命名**：使用primary、secondary、background而不是blue、gray，便于理解用途 | **Semantic Naming**: Use primary, secondary, background instead of blue, gray for easier understanding of purpose
  - **层级结构**：分为基础令牌（如colorBlue500）和语义令牌（如colorPrimary = colorBlue500）| **Hierarchical Structure**: Divided into base tokens (like colorBlue500) and semantic tokens (like colorPrimary = colorBlue500)
  - **平台适配**：可以为iOS和Android定义不同的默认值（如iOS的#007AFF vs Android的#2196F3）| **Platform Adaptation**: Can define different default values for iOS and Android (like iOS's #007AFF vs Android's #2196F3)
  - **主题切换支持**：通过Context API提供令牌，组件从Context读取，切换主题时自动重新渲染 | **Theme Switching Support**: Provide tokens through Context API, components read from Context, automatically re-render when theme switches
  - **单一真相源（Single Source of Truth）**：所有样式值来自一个地方，避免"魔法数字"分散在代码中 | **Single Source of Truth**: All style values come from one place, avoiding "magic numbers" scattered in code

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果直接在组件样式中写backgroundColor: '#007AFF'而不使用设计令牌，会有什么问题？| What's the problem with writing backgroundColor: '#007AFF' directly in component styles instead of using design tokens?
     **答案 | Answer:** 难以维护和不一致 | Difficult to maintain and inconsistent - 如果将来需要改变主色调，需要搜索整个代码库中所有的'#007AFF'并手动替换，容易遗漏。且不同开发者可能使用略有不同的颜色值（如#007AFF vs #0077FF），导致视觉不一致 | If you need to change the primary color in the future, you need to search the entire codebase for all '#007AFF' and manually replace, easy to miss. Different developers may use slightly different color values (like #007AFF vs #0077FF), causing visual inconsistency
  2. 语义令牌（semantic tokens）和基础令牌（base tokens）的区别是什么？| What's the difference between semantic tokens and base tokens?
     **答案 | Answer:** 用途 vs 具体值 | Purpose vs Specific Value - 基础令牌是具体的样式值（如gray100: '#F5F5F5'），语义令牌是根据用途命名的引用（如backgroundSecondary: gray100）。这样切换主题时，只需修改语义令牌的引用，而不改变基础令牌 | Base tokens are specific style values (like gray100: '#F5F5F5'), semantic tokens are purpose-named references (like backgroundSecondary: gray100). When switching themes, only need to modify semantic token references, not change base tokens
  3. 为什么间距（spacing）也需要标准化为设计令牌？| Why does spacing also need to be standardized as design tokens?
     **答案 | Answer:** 保持节奏和一致性 | Maintain rhythm and consistency - 使用4或8的倍数作为间距单位（如4, 8, 12, 16, 24, 32）可以创建视觉节奏感，避免组件间距混乱（如一个地方是7px，另一个地方是9px）。标准化间距还便于响应式设计，可以统一缩放 | Using multiples of 4 or 8 as spacing units (like 4, 8, 12, 16, 24, 32) creates visual rhythm, avoids chaotic component spacing (like 7px in one place, 9px in another). Standardized spacing also facilitates responsive design, can uniformly scale
  4. React Native中可以使用CSS变量（--primary-color）吗？| Can you use CSS variables (--primary-color) in React Native?
     **答案 | Answer:** 否 | No - React Native不支持CSS和CSS变量，样式是通过JavaScript对象定义的。设计令牌在RN中通过JavaScript常量或Context实现，而不是CSS变量 | React Native doesn't support CSS and CSS variables, styles are defined through JavaScript objects. Design tokens in RN are implemented through JavaScript constants or Context, not CSS variables

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // src/constants/colors.js - 颜色设计令牌 | Color design tokens
  import { Platform } from 'react-native';

  /**
   * 基础颜色调色板 | Base color palette
   * 这些是具体的颜色值，不直接在组件中使用 | These are specific color values, not directly used in components
   */
  const BaseColors = {
    // iOS标准蓝色 | iOS standard blue
    blue500: '#007AFF',
    blue600: '#0051D5',

    // 中性色灰度 | Neutral grayscale
    gray50: '#FAFAFA',
    gray100: '#F5F5F5',
    gray200: '#EEEEEE',
    gray300: '#E0E0E0',
    gray400: '#BDBDBD',
    gray500: '#9E9E9E',
    gray600: '#757575',
    gray700: '#616161',
    gray800: '#424242',
    gray900: '#212121',

    // 语义色 | Semantic colors
    red500: '#FF3B30',    // 错误 | Error
    green500: '#34C759',  // 成功 | Success
    yellow500: '#FFCC00', // 警告 | Warning

    // 纯色 | Pure colors
    white: '#FFFFFF',
    black: '#000000',
  };

  /**
   * 浅色主题 | Light theme
   * 语义化的颜色令牌，组件应该使用这些令牌而不是BaseColors | Semantic color tokens, components should use these instead of BaseColors
   */
  export const LightTheme = {
    // 主色调 | Primary colors
    primary: BaseColors.blue500,
    primaryDark: BaseColors.blue600,

    // 背景色 | Background colors
    background: BaseColors.white,
    backgroundSecondary: BaseColors.gray50,
    surface: BaseColors.white,

    // 文本色 | Text colors
    textPrimary: BaseColors.gray900,
    textSecondary: BaseColors.gray600,
    textDisabled: BaseColors.gray400,

    // 边框和分隔线 | Borders and dividers
    border: BaseColors.gray300,
    divider: BaseColors.gray200,

    // 状态色 | Status colors
    error: BaseColors.red500,
    success: BaseColors.green500,
    warning: BaseColors.yellow500,

    // 卡片阴影（iOS风格）| Card shadow (iOS style)
    shadow: BaseColors.black,
  };

  /**
   * 深色主题 | Dark theme
   * 反转明暗关系，背景变暗，文本变亮 | Invert light-dark relationships, darken background, lighten text
   */
  export const DarkTheme = {
    primary: BaseColors.blue500,
    primaryDark: BaseColors.blue600,

    background: BaseColors.black,
    backgroundSecondary: BaseColors.gray900,
    surface: BaseColors.gray800,

    textPrimary: BaseColors.white,
    textSecondary: BaseColors.gray400,
    textDisabled: BaseColors.gray600,

    border: BaseColors.gray700,
    divider: BaseColors.gray800,

    error: BaseColors.red500,
    success: BaseColors.green500,
    warning: BaseColors.yellow500,

    shadow: BaseColors.black,
  };

  // 默认导出根据平台选择的主题 | Default export theme selected based on platform
  export default LightTheme;
  ```

  ```javascript
  // src/constants/spacing.js - 间距设计令牌 | Spacing design tokens
  /**
   * 间距系统 - 基于8pt网格 | Spacing system - Based on 8pt grid
   * 使用8的倍数确保在不同分辨率下的一致性 | Using multiples of 8 ensures consistency across different resolutions
   */
  export const SPACING = {
    xs: 4,    // 极小间距：图标与文本间距 | Extra small: icon-text spacing
    sm: 8,    // 小间距：相关元素间距 | Small: related elements spacing
    md: 16,   // 中等间距：组件内部padding | Medium: component internal padding
    lg: 24,   // 大间距：组件间margin | Large: component margin
    xl: 32,   // 超大间距：屏幕边距 | Extra large: screen margins
    xxl: 48,  // 巨大间距：分区间距 | Extra extra large: section spacing
  };

  // 常用组合间距 | Common combination spacing
  export const LAYOUT = {
    screenHorizontalPadding: SPACING.md,  // 屏幕左右边距 | Screen horizontal margin
    cardPadding: SPACING.md,               // 卡片内边距 | Card padding
    listItemPadding: SPACING.md,           // 列表项内边距 | List item padding
    sectionSpacing: SPACING.xl,            // 章节间距 | Section spacing
  };
  ```

  ```javascript
  // src/constants/typography.js - 字体设计令牌 | Typography design tokens
  import { Platform } from 'react-native';

  /**
   * 字体系统 | Typography system
   * iOS默认使用San Francisco，Android默认使用Roboto | iOS defaults to San Francisco, Android defaults to Roboto
   */
  export const FONTS = {
    // 字体家族 | Font families
    regular: Platform.select({
      ios: 'System',
      android: 'Roboto',
      default: 'System',
    }),
    medium: Platform.select({
      ios: 'System',
      android: 'Roboto-Medium',
      default: 'System',
    }),
    bold: Platform.select({
      ios: 'System',
      android: 'Roboto-Bold',
      default: 'System',
    }),
  };

  /**
   * 字体大小 - 基于模块化比例 | Font sizes - Based on modular scale
   * 比例: 1.25 (大三度音阶) | Ratio: 1.25 (major third scale)
   */
  export const FONT_SIZES = {
    xs: 12,    // 辅助文本：时间戳、标签 | Supporting text: timestamps, tags
    sm: 14,    // 次要文本：描述、说明 | Secondary text: descriptions, captions
    md: 16,    // 正文：文章内容 | Body: article content
    lg: 20,    // 小标题：卡片标题 | Subheading: card titles
    xl: 24,    // 标题：屏幕标题 | Heading: screen titles
    xxl: 32,   // 大标题：欢迎页 | Large heading: welcome page
  };

  /**
   * 行高倍数 | Line height multipliers
   * 行高 = 字体大小 × 行高倍数 | Line height = font size × multiplier
   */
  export const LINE_HEIGHTS = {
    tight: 1.2,   // 紧凑：标题 | Tight: headings
    normal: 1.5,  // 正常：正文 | Normal: body text
    loose: 1.8,   // 松散：长文 | Loose: long text
  };

  /**
   * 字重 | Font weights
   */
  export const FONT_WEIGHTS = {
    regular: '400',
    medium: '500',
    semibold: '600',
    bold: '700',
  };

  /**
   * 预设文本样式 - 结合字体大小、行高、字重 | Preset text styles - Combining font size, line height, weight
   * 直接在组件中使用这些预设 | Use these presets directly in components
   */
  export const TEXT_STYLES = {
    h1: {
      fontFamily: FONTS.bold,
      fontSize: FONT_SIZES.xxl,
      lineHeight: FONT_SIZES.xxl * LINE_HEIGHTS.tight,
      fontWeight: FONT_WEIGHTS.bold,
    },
    h2: {
      fontFamily: FONTS.bold,
      fontSize: FONT_SIZES.xl,
      lineHeight: FONT_SIZES.xl * LINE_HEIGHTS.tight,
      fontWeight: FONT_WEIGHTS.bold,
    },
    h3: {
      fontFamily: FONTS.semibold,
      fontSize: FONT_SIZES.lg,
      lineHeight: FONT_SIZES.lg * LINE_HEIGHTS.normal,
      fontWeight: FONT_WEIGHTS.semibold,
    },
    body: {
      fontFamily: FONTS.regular,
      fontSize: FONT_SIZES.md,
      lineHeight: FONT_SIZES.md * LINE_HEIGHTS.normal,
      fontWeight: FONT_WEIGHTS.regular,
    },
    caption: {
      fontFamily: FONTS.regular,
      fontSize: FONT_SIZES.sm,
      lineHeight: FONT_SIZES.sm * LINE_HEIGHTS.normal,
      fontWeight: FONT_WEIGHTS.regular,
    },
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在组件中应该使用SPACING.md还是直接写16？| Should you use SPACING.md or write 16 directly in components?
    **答案 | Answer:** 使用SPACING.md | Use SPACING.md - 即使当前值是16，将来可能根据设计系统调整为12或20，使用常量可以一处修改全局生效。同时代码更具可读性（SPACING.md vs 16，前者更易理解）| Even though current value is 16, it may be adjusted to 12 or 20 based on design system in the future, using constants allows one-place modification with global effect. Also code is more readable (SPACING.md vs 16, former is easier to understand)
  - Platform.select的作用是什么？| What's the purpose of Platform.select?
    **答案 | Answer:** 平台特定值选择 | Platform-specific value selection - 根据运行平台（ios、android、web等）返回不同的值。在字体定义中用于iOS返回'System'，Android返回'Roboto'，确保使用系统原生字体 | Returns different values based on running platform (ios, android, web, etc.). Used in font definitions to return 'System' for iOS, 'Roboto' for Android, ensuring use of system native fonts

  **常见误区检查 | Common Misconception Checks:**
  - 创建了设计令牌后，所有组件都必须使用它们吗？| After creating design tokens, must all components use them?
    **答案 | Answer:** 应该但非强制 | Should but not mandatory - 最佳实践是所有组件都使用设计令牌以确保一致性，但特殊情况（如第三方库组件、一次性的原型）可以例外。可以通过ESLint规则禁止魔法数字（no-magic-numbers）强制使用常量 | Best practice is all components use design tokens to ensure consistency, but exceptions can be made for special cases (like third-party library components, one-off prototypes). Can use ESLint rule to prohibit magic numbers (no-magic-numbers) to enforce using constants
  - 如果设计师提供的间距是15px，我应该调整为16px（SPACING.md）来符合8pt网格吗？| If designer provides 15px spacing, should I adjust to 16px (SPACING.md) to conform to 8pt grid?
    **答案 | Answer:** 应该沟通确认 | Should communicate and confirm - 首先与设计师沟通15px是特殊需求还是误差。如果是特殊需求，可以在令牌中添加custom: 15；如果是误差，统一为16px。保持设计系统的完整性比完全复现设计稿更重要 | First communicate with designer whether 15px is special requirement or error. If special requirement, can add custom: 15 to tokens; if error, unify to 16px. Maintaining design system integrity is more important than exact replication of design mockups

## 实践项目：NewsReader应用初始化 | Practical Project: NewsReader App Initialization

### 目标 | Objective
完成NewsReader项目的工程化设置，建立清晰的项目结构、配置导航框架、集成API服务，并创建基础的设计系统。这个项目将成为本周剩余6天开发的基础，确保所有架构决策都已到位，可以直接开始功能开发。| Complete engineering setup for NewsReader project, establish clear project structure, configure navigation framework, integrate API services, and create basic design system. This project will serve as the foundation for the remaining 6 days of development this week, ensuring all architectural decisions are in place to start feature development directly.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. Expo CLI创建的项目与React Native CLI创建的项目有什么主要区别？| What's the main difference between projects created with Expo CLI vs React Native CLI?
   **答案 | Answer:** Expo管理所有原生依赖和配置，提供开箱即用的原生功能；React Native CLI需要手动配置原生代码（Xcode/Android Studio）和链接原生模块，但提供更多的自定义空间 | Expo manages all native dependencies and configuration, provides out-of-the-box native features; React Native CLI requires manual native code configuration (Xcode/Android Studio) and linking native modules, but provides more customization space

2. 为什么要将导航器（Navigators）和屏幕（Screens）分别放在不同的文件夹？| Why separate navigators and screens into different folders?
   **答案 | Answer:** 关注点分离：导航器负责定义应用的导航结构和层级关系，屏幕负责具体的UI实现。分离后，修改导航结构不会影响屏幕代码，屏幕可以被不同的导航器复用 | Separation of concerns: navigators define app's navigation structure and hierarchy relationships, screens handle specific UI implementation. After separation, modifying navigation structure doesn't affect screen code, screens can be reused by different navigators

3. API密钥应该如何在React Native中安全存储和使用？| How should API keys be securely stored and used in React Native?
   **答案 | Answer:** 开发环境中存储在app.json的extra字段或.env文件（不提交到Git）。生产环境中，敏感密钥应存储在服务端，客户端通过认证获取临时令牌。对于公开的API（如新闻API），可以编译到应用中但要实施速率限制 | In development, store in app.json's extra field or .env file (not committed to Git). In production, sensitive keys should be stored on server-side, client obtains temporary tokens through authentication. For public APIs (like news API), can compile into app but implement rate limiting

### 步骤 | Steps

1. **创建并配置Expo项目 | Create and Configure Expo Project**
   - 运行 `npx create-expo-app NewsReader`
   - 进入项目目录并启动开发服务器验证初始化成功
   - 安装React Navigation及相关依赖
   - 安装HTTP客户端（axios）

2. **建立项目文件夹结构 | Establish Project Folder Structure**
   - 创建src目录及其子目录（screens, components, navigation等）
   - 按功能模块组织screens子目录（Home, ArticleDetail, Categories等）
   - 创建constants目录存放设计令牌

3. **配置API服务 | Configure API Service**
   - 注册NewsAPI并获取API密钥
   - 在app.json中配置API密钥（extra字段）
   - 创建API配置文件（src/config/api.config.js）
   - 实现API服务层（src/services/newsAPI.js）
   - 使用Postman或curl测试API连接

4. **创建设计系统 | Create Design System**
   - 定义颜色令牌（LightTheme和DarkTheme）
   - 定义间距令牌（SPACING和LAYOUT）
   - 定义字体令牌（FONTS, FONT_SIZES, TEXT_STYLES）
   - 验证设计令牌的可用性（在测试组件中使用）

5. **搭建导航架构 | Build Navigation Architecture**
   - 创建RootNavigator（Bottom Tab Navigator）
   - 为每个Tab创建Stack Navigator（HomeStack, CategoriesStack等）
   - 创建占位屏幕组件（HomeScreen, SettingsScreen等）
   - 测试Tab切换和Stack导航功能

### 示例代码 | Example Code

```bash
# 完整的项目初始化脚本 | Complete project initialization script

# 1. 创建项目 | Create project
npx create-expo-app NewsReader
cd NewsReader

# 2. 安装依赖 | Install dependencies
npx expo install @react-navigation/native @react-navigation/stack @react-navigation/bottom-tabs
npx expo install react-native-screens react-native-safe-area-context
npm install axios

# 3. 创建文件夹结构 | Create folder structure
mkdir -p src/{screens/{Home,ArticleDetail,Categories,Bookmarks,Settings},components/{common,articles},navigation,services,constants,utils,hooks,contexts,config}

# 4. 创建初始文件 | Create initial files
touch src/constants/{colors,spacing,typography}.js
touch src/config/api.config.js
touch src/services/newsAPI.js
touch src/navigation/{RootNavigator,HomeStack,CategoriesStack,BookmarksStack,SettingsStack}.js
touch src/screens/Home/HomeScreen.js
touch src/screens/Settings/SettingsScreen.js

# 5. 启动开发服务器 | Start development server
npx expo start
```

```javascript
// App.js - 应用入口文件 | App entry file
import React from 'react';
import RootNavigator from './src/navigation/RootNavigator';

/**
 * NewsReader应用入口 | NewsReader app entry
 *
 * 项目结构 | Project structure:
 * - src/navigation: 导航配置 | Navigation configuration
 * - src/screens: 屏幕组件 | Screen components
 * - src/components: 可复用组件 | Reusable components
 * - src/services: API服务层 | API service layer
 * - src/constants: 设计令牌 | Design tokens
 */
export default function App() {
  return <RootNavigator />;
}
```

```javascript
// src/screens/Home/HomeScreen.js - 主屏幕占位组件 | Home screen placeholder component
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { SPACING } from '../../constants/spacing';
import { TEXT_STYLES } from '../../constants/typography';
import Colors from '../../constants/colors';

/**
 * 主屏幕 - 显示最新头条新闻 | Home Screen - Shows latest headline news
 * (Day 2将实现完整功能) | (Full functionality to be implemented in Day 2)
 */
export default function HomeScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>今日头条 | Top Headlines</Text>
      <Text style={styles.subtitle}>新闻列表将在Day 2实现 | News list to be implemented in Day 2</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: Colors.background,
    padding: SPACING.md,
    justifyContent: 'center',
    alignItems: 'center',
  },
  title: {
    ...TEXT_STYLES.h2,
    color: Colors.textPrimary,
    marginBottom: SPACING.sm,
  },
  subtitle: {
    ...TEXT_STYLES.body,
    color: Colors.textSecondary,
    textAlign: 'center',
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否成功创建并可以在Expo Go中运行？| Is the project successfully created and running in Expo Go?
2. 文件夹结构是否清晰，每个目录的职责是否明确？| Is folder structure clear, is each directory's responsibility well-defined?
3. 设计令牌（颜色、间距、字体）是否已创建并可在组件中导入？| Are design tokens (colors, spacing, fonts) created and importable in components?
4. Bottom Tab导航是否正常工作，可以在4个Tab间切换？| Is Bottom Tab navigation working properly, can switch between 4 Tabs?
5. API服务层是否已配置，使用curl测试API是否返回数据？| Is API service layer configured, does curl test return data from API?
6. 代码是否使用了设计令牌而不是硬编码的魔法数字？| Does code use design tokens instead of hard-coded magic numbers?
