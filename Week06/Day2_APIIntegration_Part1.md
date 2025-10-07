# React Native入门 - 第2天：API集成与数据获取 | React Native Introduction - Day 2: API Integration & Data Fetching

## 学习目标 | Learning Objectives
- 理解如何在React Native中创建API服务层以实现关注点分离 | Understand how to create an API service layer in React Native for separation of concerns
- 掌握使用fetch或axios进行HTTP请求的最佳实践 | Master best practices for making HTTP requests using fetch or axios
- 学会创建自定义Hook来管理数据获取的加载、错误和成功状态 | Learn to create custom hooks to manage loading, error, and success states for data fetching
- 实现useEffect钩子在组件挂载时获取数据 | Implement useEffect hook to fetch data on component mount
- 掌握使用RefreshControl实现下拉刷新功能 | Master implementing pull-to-refresh functionality using RefreshControl
- 理解如何处理API错误并提供用户友好的错误状态 | Understand how to handle API errors and provide user-friendly error states

## 详细内容 | Detailed Content

### 1. API服务层架构 | API Service Layer Architecture (1小时 | 1 hour)

- **API服务层的概念 | API Service Layer Concept**

  **概念定义 | Concept Definition:**
  API服务层是一个独立的模块，专门负责处理应用程序与外部API之间的所有通信。它将网络请求逻辑从UI组件中分离出来，使代码更易于维护、测试和重用。 | The API service layer is an independent module specifically responsible for handling all communication between the application and external APIs. It separates network request logic from UI components, making code easier to maintain, test, and reuse.

  **核心特征 | Key Characteristics:**
  - **关注点分离**：UI组件不直接处理API调用细节，只关注数据展示 | **Separation of Concerns**: UI components don't directly handle API call details, only focus on data presentation
  - **可复用性**：同一个API函数可以在多个组件中使用 | **Reusability**: The same API function can be used across multiple components
  - **集中管理**：所有API endpoints和配置集中在一个地方 | **Centralized Management**: All API endpoints and configurations are centralized in one place
  - **易于测试**：可以独立测试API层而不需要渲染组件 | **Easy to Test**: API layer can be tested independently without rendering components

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. API服务层应该包含UI渲染逻辑吗？| Should the API service layer contain UI rendering logic?
     **答案 | Answer:** 否 | No - API服务层只负责数据获取，不处理UI | The API service layer only handles data fetching, not UI
  2. 多个组件可以使用同一个API服务函数吗？| Can multiple components use the same API service function?
     **答案 | Answer:** 是 | Yes - 这正是服务层的优势，提高代码复用性 | This is exactly the advantage of service layer, improving code reusability
  3. 如果我们需要修改API的base URL，需要在多少个地方修改？| If we need to modify the API base URL, how many places need to be changed?
     **答案 | Answer:** 一个地方 | One place - 在API服务配置文件中集中管理 | Centrally managed in the API service configuration file
  4. API服务层应该处理错误还是将错误传递给调用者？| Should the API service layer handle errors or pass them to the caller?
     **答案 | Answer:** 两者都可以 | Both - 服务层可以处理通用错误（如网络错误），但应该将错误传递给调用者以便UI层显示具体错误信息 | Service layer can handle generic errors (like network errors), but should pass errors to caller for UI layer to display specific error messages

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/services/api.js
  // API服务层的基本结构 | Basic structure of API service layer

  // 1. 配置部分 | Configuration section
  const API_KEY = 'your_newsapi_key_here'; // 从 newsapi.org 获取 | Get from newsapi.org
  const BASE_URL = 'https://newsapi.org/v2';

  // 2. 通用请求处理器 | Generic request handler
  // 这个函数封装了所有HTTP请求的共同逻辑 | This function encapsulates common logic for all HTTP requests
  const apiRequest = async (endpoint, options = {}) => {
    try {
      // 构建完整URL | Build full URL
      const url = `${BASE_URL}${endpoint}`;

      // 发送请求 | Send request
      const response = await fetch(url, {
        ...options,
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
      });

      // 检查HTTP状态 | Check HTTP status
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }

      // 解析JSON响应 | Parse JSON response
      const data = await response.json();

      // 检查API特定的错误 | Check API-specific errors
      if (data.status === 'error') {
        throw new Error(data.message || 'API request failed');
      }

      return data;
    } catch (error) {
      // 统一错误处理 | Unified error handling
      console.error('API Request Error:', error);
      throw error; // 将错误传递给调用者 | Pass error to caller
    }
  };

  // 3. 导出API函数 | Export API functions
  export const api = {
    // 获取头条新闻 | Get top headlines
    getTopHeadlines: async (country = 'us') => {
      return apiRequest(`/top-headlines?country=${country}&apiKey=${API_KEY}`);
    },

    // 按类别获取文章 | Get articles by category
    getArticlesByCategory: async (category) => {
      return apiRequest(`/top-headlines?category=${category}&apiKey=${API_KEY}`);
    },

    // 搜索文章 | Search articles
    searchArticles: async (query) => {
      return apiRequest(`/everything?q=${encodeURIComponent(query)}&apiKey=${API_KEY}`);
    },
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中的`apiRequest`函数的作用是什么？| What is the purpose of the `apiRequest` function in this code?
    **答案 | Answer:** 封装通用的HTTP请求逻辑，包括错误处理、响应解析和状态检查，避免重复代码 | Encapsulates common HTTP request logic including error handling, response parsing, and status checking to avoid code duplication
  - 如果API返回404错误会发生什么？| What happens if the API returns a 404 error?
    **答案 | Answer:** `response.ok`会是false，代码会抛出一个包含状态码的错误 | `response.ok` will be false, and the code will throw an error containing the status code

  **常见误区检查 | Common Misconception Checks:**
  - 误区：API调用应该直接在组件中使用fetch | Misconception: API calls should use fetch directly in components
    **答案 | Answer:** 错误 | Wrong - 应该通过服务层抽象，使组件更简洁且易于测试 | Should be abstracted through service layer to make components cleaner and easier to test
  - 误区：每个API端点都需要单独的错误处理逻辑 | Misconception: Each API endpoint needs separate error handling logic
    **答案 | Answer:** 错误 | Wrong - 通用错误处理应该在`apiRequest`中集中处理 | Generic error handling should be centralized in `apiRequest`

- **环境变量与API密钥管理 | Environment Variables & API Key Management**

  **概念定义 | Concept Definition:**
  环境变量是一种安全存储敏感配置（如API密钥）的方法，避免将其硬编码在代码中。在React Native中，我们使用`.env`文件和库如`react-native-dotenv`来管理环境变量。 | Environment variables are a secure way to store sensitive configurations (like API keys), avoiding hardcoding them in the code. In React Native, we use `.env` files and libraries like `react-native-dotenv` to manage environment variables.

  **核心特征 | Key Characteristics:**
  - **安全性**：敏感信息不会被提交到版本控制系统 | **Security**: Sensitive information is not committed to version control
  - **灵活性**：可以为不同环境（开发、测试、生产）设置不同的值 | **Flexibility**: Different values can be set for different environments (development, testing, production)
  - **易于维护**：修改配置无需修改代码 | **Easy Maintenance**: Configuration changes don't require code modifications

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. API密钥应该直接写在代码文件中吗？| Should API keys be written directly in code files?
     **答案 | Answer:** 否 | No - 应该存储在环境变量中以提高安全性 | Should be stored in environment variables for better security
  2. `.env`文件应该提交到Git仓库吗？| Should the `.env` file be committed to Git repository?
     **答案 | Answer:** 否 | No - 应该添加到`.gitignore`中 | Should be added to `.gitignore`
  3. 如何在开发环境和生产环境使用不同的API密钥？| How to use different API keys in development and production environments?
     **答案 | Answer:** 使用不同的`.env`文件（如`.env.development`和`.env.production`） | Use different `.env` files (like `.env.development` and `.env.production`)

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 1. 安装依赖 | Install dependencies
  // npm install react-native-dotenv

  // 2. 创建 .env 文件 | Create .env file
  // NEWS_API_KEY=your_actual_api_key_here
  // NEWS_API_BASE_URL=https://newsapi.org/v2

  // 3. 配置 babel.config.js | Configure babel.config.js
  module.exports = function(api) {
    api.cache(true);
    return {
      presets: ['babel-preset-expo'],
      plugins: [
        ['module:react-native-dotenv', {
          moduleName: '@env',
          path: '.env',
        }]
      ]
    };
  };

  // 4. 在 api.js 中使用环境变量 | Use environment variables in api.js
  import { NEWS_API_KEY, NEWS_API_BASE_URL } from '@env';

  const API_KEY = NEWS_API_KEY;
  const BASE_URL = NEWS_API_BASE_URL;

  // 5. 创建 .gitignore 条目 | Create .gitignore entry
  // .env
  // .env.local
  // .env.development
  // .env.production
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果忘记重启开发服务器，环境变量的修改会生效吗？| Will environment variable changes take effect if you forget to restart the dev server?
    **答案 | Answer:** 否 | No - 需要重启开发服务器才能加载新的环境变量 | Need to restart dev server to load new environment variables

  **常见误区检查 | Common Misconception Checks:**
  - 误区：`.env`文件中的变量可以在运行时动态修改 | Misconception: Variables in `.env` file can be dynamically modified at runtime
    **答案 | Answer:** 错误 | Wrong - 环境变量在构建时被嵌入，运行时不可更改 | Environment variables are embedded at build time and cannot be changed at runtime

### 2. HTTP请求与错误处理 | HTTP Requests & Error Handling (1.5小时 | 1.5 hours)

- **Fetch API的核心概念 | Fetch API Core Concepts**

  **概念定义 | Concept Definition:**
  Fetch API是现代JavaScript提供的用于发送HTTP请求的接口。它返回一个Promise，使得异步操作更加优雅。在React Native中，fetch是内置的，无需额外安装。 | Fetch API is a modern JavaScript interface for sending HTTP requests. It returns a Promise, making asynchronous operations more elegant. In React Native, fetch is built-in and requires no additional installation.

  **核心特征 | Key Characteristics:**
  - **基于Promise**：使用async/await语法简化异步代码 | **Promise-based**: Simplifies async code using async/await syntax
  - **流式响应**：支持处理大文件和流式数据 | **Streaming Response**: Supports handling large files and streaming data
  - **灵活配置**：可以设置headers、method、body等请求参数 | **Flexible Configuration**: Can set headers, method, body and other request parameters
  - **不自动拒绝HTTP错误**：需要手动检查`response.ok` | **No Automatic HTTP Error Rejection**: Need to manually check `response.ok`

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. fetch函数返回什么类型的值？| What type of value does the fetch function return?
     **答案 | Answer:** Promise - 可以使用.then()或async/await处理 | Promise - can be handled with .then() or async/await
  2. 如果服务器返回404错误，fetch会自动抛出错误吗？| If the server returns a 404 error, will fetch automatically throw an error?
     **答案 | Answer:** 否 | No - 只有网络错误才会导致Promise被reject，HTTP错误需要检查response.ok | Only network errors cause Promise rejection; HTTP errors require checking response.ok
  3. 如何在fetch请求中发送JSON数据？| How to send JSON data in a fetch request?
     **答案 | Answer:** 设置Content-Type header为application/json，并使用JSON.stringify()转换body | Set Content-Type header to application/json and use JSON.stringify() to convert body
  4. response.json()方法是同步还是异步的？| Is the response.json() method synchronous or asynchronous?
     **答案 | Answer:** 异步 | Asynchronous - 返回Promise，需要await或.then() | Returns Promise, needs await or .then()

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/services/api.js
  // 完整的API服务实现，包含错误处理 | Complete API service implementation with error handling

  import { NEWS_API_KEY } from '@env';

  const BASE_URL = 'https://newsapi.org/v2';

  // 自定义错误类 | Custom error class
  class APIError extends Error {
    constructor(message, status, data) {
      super(message);
      this.name = 'APIError';
      this.status = status;
      this.data = data;
    }
  }

  // 通用请求函数 | Generic request function
  const apiRequest = async (endpoint, options = {}) => {
    const url = `${BASE_URL}${endpoint}`;

    try {
      // 发送请求 | Send request
      const response = await fetch(url, {
        ...options,
        headers: {
          'Content-Type': 'application/json',
          ...options.headers,
        },
      });

      // 解析响应 | Parse response
      const data = await response.json();

      // 检查HTTP状态 | Check HTTP status
      if (!response.ok) {
        throw new APIError(
          data.message || 'Request failed',
          response.status,
          data
        );
      }

      // 检查API特定错误 | Check API-specific errors
      if (data.status === 'error') {
        throw new APIError(data.message, response.status, data);
      }

      return data;
    } catch (error) {
      // 处理网络错误 | Handle network errors
      if (error instanceof TypeError) {
        throw new APIError('Network error. Please check your connection.', 0, null);
      }

      // 重新抛出已知错误 | Re-throw known errors
      throw error;
    }
  };

  // API端点函数 | API endpoint functions
  export const newsAPI = {
    // 获取头条新闻 | Get top headlines
    getTopHeadlines: async (country = 'us', page = 1) => {
      const endpoint = `/top-headlines?country=${country}&page=${page}&pageSize=20&apiKey=${NEWS_API_KEY}`;
      const data = await apiRequest(endpoint);
      return data.articles; // 直接返回文章数组 | Return articles array directly
    },

    // 按类别获取文章 | Get articles by category
    getArticlesByCategory: async (category, page = 1) => {
      const endpoint = `/top-headlines?category=${category}&page=${page}&pageSize=20&apiKey=${NEWS_API_KEY}`;
      const data = await apiRequest(endpoint);
      return data.articles;
    },

    // 搜索文章 | Search articles
    searchArticles: async (query, page = 1) => {
      const encodedQuery = encodeURIComponent(query);
      const endpoint = `/everything?q=${encodedQuery}&page=${page}&pageSize=20&sortBy=publishedAt&apiKey=${NEWS_API_KEY}`;
      const data = await apiRequest(endpoint);
      return data.articles;
    },
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么需要自定义APIError类？| Why do we need a custom APIError class?
    **答案 | Answer:** 为了携带额外信息（status、data），使错误处理更精确，UI层可以根据错误类型显示不同消息 | To carry additional information (status, data) for more precise error handling, UI layer can display different messages based on error type
  - 如果用户没有网络连接会发生什么？| What happens if the user has no network connection?
    **答案 | Answer:** fetch会抛出TypeError，被catch块捕获并转换为友好的APIError | fetch will throw TypeError, caught by catch block and converted to friendly APIError
  - 为什么API函数返回`data.articles`而不是整个`data`对象？| Why do API functions return `data.articles` instead of the whole `data` object?
    **答案 | Answer:** 简化调用者的使用，他们只需要文章数组，不需要关心API响应的完整结构 | Simplifies caller usage, they only need the articles array without caring about full API response structure

  **常见误区检查 | Common Misconception Checks:**
  - 误区：fetch会自动重试失败的请求 | Misconception: fetch automatically retries failed requests
    **答案 | Answer:** 错误 | Wrong - 需要手动实现重试逻辑 | Need to manually implement retry logic
  - 误区：可以用try-catch直接捕获404错误 | Misconception: Can use try-catch to directly catch 404 errors
    **答案 | Answer:** 错误 | Wrong - 404是成功的HTTP响应，需要检查response.ok | 404 is a successful HTTP response, need to check response.ok

- **网络错误的分类与处理 | Network Error Classification & Handling**

  **概念定义 | Concept Definition:**
  网络请求可能因多种原因失败：网络连接问题、服务器错误、客户端错误、超时等。正确分类和处理这些错误对于提供良好的用户体验至关重要。 | Network requests can fail for various reasons: network connection issues, server errors, client errors, timeouts, etc. Properly classifying and handling these errors is crucial for providing a good user experience.

  **错误类型 | Error Types:**
  - **网络错误（Network Errors）**：无网络连接、DNS失败 | No network connection, DNS failure
  - **客户端错误（4xx）**：400 Bad Request、401 Unauthorized、404 Not Found | 400 Bad Request, 401 Unauthorized, 404 Not Found
  - **服务器错误（5xx）**：500 Internal Server Error、503 Service Unavailable | 500 Internal Server Error, 503 Service Unavailable
  - **超时错误（Timeout）**：请求时间过长 | Request takes too long
  - **解析错误（Parse Errors）**：响应不是有效的JSON | Response is not valid JSON

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 401错误和网络连接错误应该向用户显示相同的消息吗？| Should 401 error and network connection error show the same message to users?
     **答案 | Answer:** 否 | No - 401需要提示登录，网络错误需要提示检查连接 | 401 needs login prompt, network error needs connection check prompt
  2. 如何区分网络错误和HTTP错误？| How to distinguish network errors from HTTP errors?
     **答案 | Answer:** 网络错误会导致fetch Promise被reject（TypeError），HTTP错误需要检查response.ok | Network errors cause fetch Promise rejection (TypeError), HTTP errors need response.ok check
  3. 超时错误应该由谁处理？| Who should handle timeout errors?
     **答案 | Answer:** 可以在API服务层统一处理，使用AbortController和setTimeout | Can be handled uniformly in API service layer using AbortController and setTimeout

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // /src/utils/errorHandler.js
  // 错误处理工具 | Error handling utility

  export const getErrorMessage = (error) => {
    // 网络错误 | Network error
    if (error.status === 0) {
      return {
        title: 'No Internet Connection',
        message: 'Please check your network settings and try again.',
        retryable: true,
      };
    }

    // 客户端错误 | Client errors
    if (error.status >= 400 && error.status < 500) {
      const messages = {
        401: { title: 'Unauthorized', message: 'Please check your API key.', retryable: false },
        404: { title: 'Not Found', message: 'The requested resource was not found.', retryable: false },
        429: { title: 'Rate Limited', message: 'Too many requests. Please try again later.', retryable: true },
      };
      return messages[error.status] || {
        title: 'Request Error',
        message: error.message,
        retryable: false,
      };
    }

    // 服务器错误 | Server errors
    if (error.status >= 500) {
      return {
        title: 'Server Error',
        message: 'Something went wrong on our end. Please try again later.',
        retryable: true,
      };
    }

    // 未知错误 | Unknown error
    return {
      title: 'Error',
      message: error.message || 'An unexpected error occurred.',
      retryable: true,
    };
  };

  // 带超时的fetch | Fetch with timeout
  export const fetchWithTimeout = async (url, options = {}, timeout = 10000) => {
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), timeout);

    try {
      const response = await fetch(url, {
        ...options,
        signal: controller.signal,
      });
      clearTimeout(timeoutId);
      return response;
    } catch (error) {
      clearTimeout(timeoutId);
      if (error.name === 'AbortError') {
        throw new Error('Request timeout');
      }
      throw error;
    }
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - `retryable`字段的作用是什么？| What is the purpose of the `retryable` field?
    **答案 | Answer:** 告诉UI层是否应该显示"重试"按钮，某些错误（如401）重试无意义 | Tells UI layer whether to show "Retry" button, some errors (like 401) are pointless to retry
  - AbortController如何实现超时？| How does AbortController implement timeout?
    **答案 | Answer:** setTimeout在指定时间后调用abort()，导致fetch Promise被reject | setTimeout calls abort() after specified time, causing fetch Promise to be rejected

  **常见误区检查 | Common Misconception Checks:**
  - 误区：所有错误都应该显示技术性的错误消息 | Misconception: All errors should display technical error messages
    **答案 | Answer:** 错误 | Wrong - 应该显示用户友好的消息，技术细节只记录到控制台 | Should display user-friendly messages, technical details only logged to console
