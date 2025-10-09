# React Native入门 - 第35天：Bookmarks与本地存储 | React Native Introduction - Day 35: Bookmarks & Local Storage

## 学习目标 | Learning Objectives
- 掌握React Context API和useReducer管理复杂应用状态 | Master React Context API and useReducer for complex state management
- 理解AsyncStorage的工作原理及数据持久化最佳实践 | Understand AsyncStorage mechanics and data persistence best practices
- 学会实现书签功能的完整生命周期（添加、显示、删除） | Learn to implement complete bookmark lifecycle (add, display, remove)
- 掌握跨屏幕共享状态的架构设计模式 | Master architectural patterns for cross-screen state sharing
- 理解手势处理库(react-native-gesture-handler)的集成与使用 | Understand gesture handler library integration and usage
- 实现用户偏好设置的持久化存储机制 | Implement persistence mechanisms for user preferences

## 详细内容 | Detailed Content

### 1. Context API与状态管理架构 | Context API & State Management Architecture (2小时 | 2 hours)

- **Context API的核心概念 | Core Concepts of Context API**

  **概念定义 | Concept Definition:**
  Context API是React提供的一种跨组件层级传递数据的机制，无需通过props逐层传递。它创建一个全局状态容器，允许任何子组件访问和修改共享状态。对于书签功能，Context能够让ArticleDetail、Bookmarks等多个屏幕共享和同步书签数据。

  Context API is React's mechanism for passing data across component hierarchies without prop drilling. It creates a global state container allowing any child component to access and modify shared state. For bookmark functionality, Context enables ArticleDetail, Bookmarks, and other screens to share and synchronize bookmark data.

  **核心特征 | Key Characteristics:**
  - Provider/Consumer模式：Provider组件包裹需要访问数据的子组件树 | Provider/Consumer pattern: Provider component wraps the subtree needing data access
  - 自动重渲染：当Context值变化时，所有消费该Context的组件自动重新渲染 | Automatic re-renders: When Context value changes, all consuming components re-render automatically
  - 跨层级传递：避免了props drilling（逐层传递props）的繁琐 | Cross-hierarchy passing: Avoids tedious prop drilling through intermediate components
  - 性能优化考虑：需要合理拆分Context避免不必要的重渲染 | Performance considerations: Requires proper Context splitting to avoid unnecessary re-renders

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Context API是否可以替代所有的props传递？| Can Context API replace all prop passing?
     **答案 | Answer:** 否 | No - Context适用于跨多层级的共享数据（如主题、用户信息），但不应滥用。简单的父子组件通信仍应使用props，因为props更直接、更易追踪。
     Context is suitable for data shared across multiple levels (themes, user info), but shouldn't be overused. Simple parent-child communication should still use props as they're more direct and trackable.

  2. 当Context的value对象引用改变时会发生什么？| What happens when the Context value object reference changes?
     **答案 | Answer:** 所有使用该Context的组件都会重新渲染 | All components using that Context will re-render - 这就是为什么应该使用useMemo优化value对象，避免每次Provider渲染时创建新对象引用。
     This is why you should use useMemo to optimize the value object, avoiding new object references on every Provider render.

  3. 多个Context可以嵌套使用吗？| Can multiple Contexts be nested?
     **答案 | Answer:** 可以 | Yes - 应用中可以同时使用多个Context（如ThemeContext、BookmarksContext、AuthContext），每个负责不同的状态域，这是最佳实践。
     Applications can use multiple Contexts simultaneously (ThemeContext, BookmarksContext, AuthContext), each managing different state domains - this is best practice.

  4. useContext Hook与Context.Consumer的区别是什么？| What's the difference between useContext Hook and Context.Consumer?
     **答案 | Answer:** useContext是更简洁的现代API | useContext is the more concise modern API - 它返回context当前值，而Context.Consumer使用render props模式。在函数组件中应优先使用useContext。
     It returns the current context value, while Context.Consumer uses render props pattern. Prefer useContext in functional components.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 创建书签Context | Create Bookmarks Context
  import React, { createContext, useContext, useReducer, useEffect } from 'react';
  import AsyncStorage from '@react-native-async-storage/async-storage';

  // 1. 定义Context类型和初始状态 | Define Context type and initial state
  const BookmarksContext = createContext(null);

  // 初始状态结构 | Initial state structure
  const initialState = {
    bookmarks: [], // 存储所有书签文章的数组 | Array storing all bookmarked articles
    isLoading: true, // 标识是否正在从AsyncStorage加载 | Indicates if loading from AsyncStorage
    error: null, // 错误信息 | Error message
  };

  // 2. 定义Reducer处理状态更新 | Define Reducer for state updates
  const bookmarksReducer = (state, action) => {
    switch (action.type) {
      case 'SET_BOOKMARKS':
        // 从AsyncStorage加载完成后设置书签 | Set bookmarks after loading from AsyncStorage
        return {
          ...state,
          bookmarks: action.payload,
          isLoading: false,
          error: null,
        };

      case 'ADD_BOOKMARK':
        // 添加新书签（检查是否已存在） | Add new bookmark (check if exists)
        const exists = state.bookmarks.some(
          item => item.url === action.payload.url
        );
        if (exists) return state; // 已存在则不添加 | Don't add if exists

        return {
          ...state,
          bookmarks: [...state.bookmarks, action.payload],
        };

      case 'REMOVE_BOOKMARK':
        // 根据URL删除书签 | Remove bookmark by URL
        return {
          ...state,
          bookmarks: state.bookmarks.filter(
            item => item.url !== action.payload
          ),
        };

      case 'SET_ERROR':
        return {
          ...state,
          isLoading: false,
          error: action.payload,
        };

      default:
        return state;
    }
  };

  // 3. 创建Provider组件 | Create Provider component
  export const BookmarksProvider = ({ children }) => {
    const [state, dispatch] = useReducer(bookmarksReducer, initialState);

    // 应用启动时从AsyncStorage加载书签 | Load bookmarks from AsyncStorage on app start
    useEffect(() => {
      loadBookmarks();
    }, []);

    // 每次书签变化时保存到AsyncStorage | Save to AsyncStorage whenever bookmarks change
    useEffect(() => {
      if (!state.isLoading) {
        saveBookmarks(state.bookmarks);
      }
    }, [state.bookmarks, state.isLoading]);

    // 从本地存储加载书签 | Load bookmarks from local storage
    const loadBookmarks = async () => {
      try {
        const stored = await AsyncStorage.getItem('@bookmarks');
        const bookmarks = stored ? JSON.parse(stored) : [];
        dispatch({ type: 'SET_BOOKMARKS', payload: bookmarks });
      } catch (error) {
        console.error('Failed to load bookmarks:', error);
        dispatch({ type: 'SET_ERROR', payload: error.message });
      }
    };

    // 保存书签到本地存储 | Save bookmarks to local storage
    const saveBookmarks = async (bookmarks) => {
      try {
        await AsyncStorage.setItem('@bookmarks', JSON.stringify(bookmarks));
      } catch (error) {
        console.error('Failed to save bookmarks:', error);
      }
    };

    // 添加书签 | Add bookmark
    const addBookmark = (article) => {
      dispatch({ type: 'ADD_BOOKMARK', payload: article });
    };

    // 删除书签 | Remove bookmark
    const removeBookmark = (articleUrl) => {
      dispatch({ type: 'REMOVE_BOOKMARK', payload: articleUrl });
    };

    // 检查文章是否已被书签 | Check if article is bookmarked
    const isBookmarked = (articleUrl) => {
      return state.bookmarks.some(item => item.url === articleUrl);
    };

    // 使用useMemo避免每次渲染创建新对象 | Use useMemo to avoid creating new object on every render
    const value = React.useMemo(() => ({
      bookmarks: state.bookmarks,
      isLoading: state.isLoading,
      error: state.error,
      addBookmark,
      removeBookmark,
      isBookmarked,
    }), [state.bookmarks, state.isLoading, state.error]);

    return (
      <BookmarksContext.Provider value={value}>
        {children}
      </BookmarksContext.Provider>
    );
  };

  // 4. 创建自定义Hook方便使用 | Create custom Hook for easy usage
  export const useBookmarks = () => {
    const context = useContext(BookmarksContext);
    if (!context) {
      throw new Error('useBookmarks must be used within BookmarksProvider');
    }
    return context;
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 这段代码中为什么要使用useMemo包裹value对象？| Why use useMemo to wrap the value object?
    **答案 | Answer:** 防止value对象在每次BookmarksProvider渲染时都创建新引用，导致所有消费者组件不必要的重渲染。useMemo确保只有依赖项变化时才创建新对象。
    Prevents creating a new object reference on every BookmarksProvider render, causing unnecessary re-renders of all consumer components. useMemo ensures a new object is created only when dependencies change.

  - 如果忘记在App根组件包裹BookmarksProvider会发生什么？| What happens if you forget to wrap BookmarksProvider in the App root?
    **答案 | Answer:** 调用useBookmarks的组件会抛出错误 "useBookmarks must be used within BookmarksProvider"，因为context值为undefined。这是一种防御性编程实践。
    Components calling useBookmarks will throw the error "useBookmarks must be used within BookmarksProvider" because the context value is undefined. This is a defensive programming practice.

  **常见误区检查 | Common Misconception Checks:**
  - Context是否会导致整个应用重渲染？| Does Context cause the entire app to re-render?
    **答案 | Answer:** 不会 | No - 只有使用了useContext或Context.Consumer的组件才会在Context值变化时重渲染。未使用Context的组件不受影响。
    Only components using useContext or Context.Consumer will re-render when Context value changes. Components not using Context are unaffected.

  - 是否应该为每个状态都创建单独的Context？| Should you create a separate Context for every piece of state?
    **答案 | Answer:** 不需要 | Not necessary - 应该根据逻辑关联性分组。例如，书签的loading、error、data应该在同一个Context中，因为它们逻辑相关。过度拆分会增加复杂度。
    Group by logical relatedness. For example, bookmarks' loading, error, and data should be in the same Context because they're logically related. Over-splitting increases complexity.

- **useReducer管理复杂状态 | Managing Complex State with useReducer**

  **概念定义 | Concept Definition:**
  useReducer是React Hook，用于管理包含多个子值或复杂更新逻辑的状态。它接收一个reducer函数和初始状态，返回当前状态和dispatch函数。Reducer函数接收当前状态和action，返回新状态。对于书签功能，需要处理添加、删除、加载等多种操作，useReducer比useState更合适。

  useReducer is a React Hook for managing state with multiple sub-values or complex update logic. It accepts a reducer function and initial state, returning current state and dispatch function. The reducer function receives current state and action, returning new state. For bookmark functionality with operations like add, remove, and load, useReducer is more suitable than useState.

  **核心特征 | Key Characteristics:**
  - 集中式状态更新逻辑：所有状态变更都在reducer函数中处理 | Centralized update logic: All state changes are handled in the reducer function
  - Action驱动：通过dispatch发送action对象来触发状态更新 | Action-driven: Trigger state updates by dispatching action objects
  - 可预测性：相同的state和action总是产生相同的新state | Predictability: Same state and action always produce the same new state
  - 易于测试：Reducer是纯函数，不依赖外部状态 | Easy to test: Reducers are pure functions independent of external state

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. useReducer和useState的主要区别是什么？| What's the main difference between useReducer and useState?
     **答案 | Answer:** useState适合简单状态，useReducer适合复杂状态逻辑 | useState suits simple state, useReducer suits complex state logic - 当状态更新依赖多个子值、有多种更新类型、或更新逻辑复杂时，useReducer提供更好的结构和可维护性。
     When state updates depend on multiple sub-values, have multiple update types, or complex logic, useReducer provides better structure and maintainability.

  2. Reducer函数必须是纯函数吗？| Must reducer functions be pure functions?
     **答案 | Answer:** 是的 | Yes - Reducer不应包含副作用（如API调用、修改外部变量），必须根据输入参数返回新状态。副作用应该在useEffect或事件处理函数中处理。
     Reducers shouldn't contain side effects (API calls, modifying external variables), must return new state based on input parameters. Side effects should be handled in useEffect or event handlers.

  3. 为什么要返回新的状态对象而不是修改现有状态？| Why return a new state object instead of mutating existing state?
     **答案 | Answer:** React依赖引用比较检测变化 | React relies on reference comparison to detect changes - 如果直接修改state对象，React无法检测到变化，组件不会重新渲染。必须返回新对象（使用展开运算符或其他方式）。
     If you mutate the state object directly, React can't detect the change and components won't re-render. Must return a new object (using spread operator or other methods).

  4. dispatch函数会在组件重渲染时改变吗？| Does the dispatch function change between component re-renders?
     **答案 | Answer:** 不会 | No - dispatch函数的引用在组件生命周期中保持稳定，可以安全地添加到useEffect依赖数组或作为props传递，不会导致额外渲染。
     The dispatch function reference remains stable throughout the component lifecycle, can be safely added to useEffect dependency arrays or passed as props without causing extra renders.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 书签Reducer的完整实现与测试 | Complete implementation and testing of Bookmarks Reducer

  // 1. 定义Action类型常量 | Define action type constants
  export const BOOKMARK_ACTIONS = {
    SET_BOOKMARKS: 'SET_BOOKMARKS',
    ADD_BOOKMARK: 'ADD_BOOKMARK',
    REMOVE_BOOKMARK: 'REMOVE_BOOKMARK',
    TOGGLE_BOOKMARK: 'TOGGLE_BOOKMARK',
    CLEAR_ALL: 'CLEAR_ALL',
    SET_LOADING: 'SET_LOADING',
    SET_ERROR: 'SET_ERROR',
  };

  // 2. Reducer函数（纯函数） | Reducer function (pure function)
  export const bookmarksReducer = (state, action) => {
    switch (action.type) {
      case BOOKMARK_ACTIONS.SET_BOOKMARKS:
        return {
          ...state,
          bookmarks: action.payload,
          isLoading: false,
          error: null,
        };

      case BOOKMARK_ACTIONS.ADD_BOOKMARK: {
        // 检查是否已存在（根据URL） | Check if exists (by URL)
        const exists = state.bookmarks.some(
          item => item.url === action.payload.url
        );
        if (exists) {
          return state; // 不修改状态 | Don't modify state
        }

        return {
          ...state,
          bookmarks: [
            ...state.bookmarks,
            {
              ...action.payload,
              bookmarkedAt: new Date().toISOString(), // 添加时间戳 | Add timestamp
            }
          ],
        };
      }

      case BOOKMARK_ACTIONS.REMOVE_BOOKMARK:
        return {
          ...state,
          bookmarks: state.bookmarks.filter(
            item => item.url !== action.payload
          ),
        };

      case BOOKMARK_ACTIONS.TOGGLE_BOOKMARK: {
        // 切换书签状态（已存在则删除，不存在则添加） | Toggle bookmark (remove if exists, add if not)
        const exists = state.bookmarks.some(
          item => item.url === action.payload.url
        );

        if (exists) {
          return {
            ...state,
            bookmarks: state.bookmarks.filter(
              item => item.url !== action.payload.url
            ),
          };
        } else {
          return {
            ...state,
            bookmarks: [
              ...state.bookmarks,
              {
                ...action.payload,
                bookmarkedAt: new Date().toISOString(),
              }
            ],
          };
        }
      }

      case BOOKMARK_ACTIONS.CLEAR_ALL:
        return {
          ...state,
          bookmarks: [],
        };

      case BOOKMARK_ACTIONS.SET_LOADING:
        return {
          ...state,
          isLoading: action.payload,
        };

      case BOOKMARK_ACTIONS.SET_ERROR:
        return {
          ...state,
          isLoading: false,
          error: action.payload,
        };

      default:
        // 未知action类型时返回当前状态 | Return current state for unknown action types
        console.warn(`Unknown action type: ${action.type}`);
        return state;
    }
  };

  // 3. 在组件中使用useReducer | Using useReducer in components
  import { useReducer, useEffect } from 'react';

  const BookmarksManager = () => {
    const [state, dispatch] = useReducer(bookmarksReducer, {
      bookmarks: [],
      isLoading: true,
      error: null,
    });

    // 加载书签 | Load bookmarks
    useEffect(() => {
      const loadBookmarks = async () => {
        dispatch({ type: BOOKMARK_ACTIONS.SET_LOADING, payload: true });
        try {
          const stored = await AsyncStorage.getItem('@bookmarks');
          const bookmarks = stored ? JSON.parse(stored) : [];
          dispatch({ type: BOOKMARK_ACTIONS.SET_BOOKMARKS, payload: bookmarks });
        } catch (error) {
          dispatch({ type: BOOKMARK_ACTIONS.SET_ERROR, payload: error.message });
        }
      };

      loadBookmarks();
    }, []);

    // 添加书签的辅助函数 | Helper function to add bookmark
    const handleAddBookmark = (article) => {
      dispatch({
        type: BOOKMARK_ACTIONS.ADD_BOOKMARK,
        payload: article,
      });
    };

    // 删除书签的辅助函数 | Helper function to remove bookmark
    const handleRemoveBookmark = (articleUrl) => {
      dispatch({
        type: BOOKMARK_ACTIONS.REMOVE_BOOKMARK,
        payload: articleUrl,
      });
    };

    // 切换书签状态的辅助函数 | Helper function to toggle bookmark
    const handleToggleBookmark = (article) => {
      dispatch({
        type: BOOKMARK_ACTIONS.TOGGLE_BOOKMARK,
        payload: article,
      });
    };

    return { state, handleAddBookmark, handleRemoveBookmark, handleToggleBookmark };
  };

  // 4. Reducer单元测试示例 | Reducer unit test example
  describe('bookmarksReducer', () => {
    const initialState = {
      bookmarks: [],
      isLoading: false,
      error: null,
    };

    test('应该正确添加新书签 | should add new bookmark correctly', () => {
      const article = {
        url: 'https://example.com/article1',
        title: 'Test Article',
        author: 'John Doe',
      };

      const newState = bookmarksReducer(initialState, {
        type: BOOKMARK_ACTIONS.ADD_BOOKMARK,
        payload: article,
      });

      expect(newState.bookmarks).toHaveLength(1);
      expect(newState.bookmarks[0].url).toBe(article.url);
      expect(newState.bookmarks[0]).toHaveProperty('bookmarkedAt');
    });

    test('不应该添加重复的书签 | should not add duplicate bookmarks', () => {
      const article = {
        url: 'https://example.com/article1',
        title: 'Test Article',
      };

      const stateWithBookmark = {
        ...initialState,
        bookmarks: [article],
      };

      const newState = bookmarksReducer(stateWithBookmark, {
        type: BOOKMARK_ACTIONS.ADD_BOOKMARK,
        payload: article,
      });

      expect(newState.bookmarks).toHaveLength(1);
      expect(newState).toBe(stateWithBookmark); // 状态未改变 | State unchanged
    });

    test('应该正确删除书签 | should remove bookmark correctly', () => {
      const stateWithBookmarks = {
        ...initialState,
        bookmarks: [
          { url: 'https://example.com/article1', title: 'Article 1' },
          { url: 'https://example.com/article2', title: 'Article 2' },
        ],
      };

      const newState = bookmarksReducer(stateWithBookmarks, {
        type: BOOKMARK_ACTIONS.REMOVE_BOOKMARK,
        payload: 'https://example.com/article1',
      });

      expect(newState.bookmarks).toHaveLength(1);
      expect(newState.bookmarks[0].url).toBe('https://example.com/article2');
    });
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要定义BOOKMARK_ACTIONS常量而不是直接使用字符串？| Why define BOOKMARK_ACTIONS constants instead of using strings directly?
    **答案 | Answer:** 防止拼写错误、提供类型检查、便于重构 | Prevents typos, provides type checking, easier refactoring - 使用常量可以在编译时捕获错误，而字符串拼写错误只能在运行时发现。
    Using constants catches errors at compile time, while string typos are only discovered at runtime.

  - TOGGLE_BOOKMARK action为什么要检查书签是否存在？| Why does TOGGLE_BOOKMARK action check if bookmark exists?
    **答案 | Answer:** 实现切换逻辑 | To implement toggle logic - 如果已存在则删除，不存在则添加，这样一个按钮可以处理两种状态，提供更好的用户体验。
    Remove if exists, add if not - this allows one button to handle both states, providing better user experience.

  **常见误区检查 | Common Misconception Checks:**
  - 可以在Reducer中直接调用AsyncStorage吗？| Can you call AsyncStorage directly in the reducer?
    **答案 | Answer:** 不可以 | No - Reducer必须是纯函数，不应包含副作用。AsyncStorage调用应该在useEffect或事件处理函数中进行，然后dispatch结果。
    Reducers must be pure functions without side effects. AsyncStorage calls should be in useEffect or event handlers, then dispatch the results.

  - 为什么bookmarkedAt时间戳在reducer中添加而不是在dispatch前？| Why add bookmarkedAt timestamp in reducer instead of before dispatching?
    **答案 | Answer:** 保持数据一致性 | To maintain data consistency - 在reducer中统一添加确保所有书签都有时间戳，避免调用方忘记添加。但这也打破了纯函数规则（使用new Date()），在生产环境中应通过action payload传入。
    Adding uniformly in reducer ensures all bookmarks have timestamps, avoiding callers forgetting to add. However, this breaks pure function rules (using new Date()) - in production, pass via action payload.

### 2. AsyncStorage数据持久化 | AsyncStorage Data Persistence (1.5小时 | 1.5 hours)

- **AsyncStorage核心API | AsyncStorage Core API**

  **概念定义 | Concept Definition:**
  AsyncStorage是React Native提供的简单、异步、持久化的key-value存储系统。它在iOS上使用原生的NSUserDefaults，在Android上使用SQLite或RocksDB。所有操作都是异步的，返回Promise。适用于存储少量数据（如用户偏好、书签、缓存等），但不适合存储大量数据或敏感信息。

  AsyncStorage is React Native's simple, asynchronous, persistent key-value storage system. It uses NSUserDefaults natively on iOS and SQLite or RocksDB on Android. All operations are asynchronous, returning Promises. Suitable for storing small amounts of data (user preferences, bookmarks, cache), but not for large datasets or sensitive information.

  **核心特征 | Key Characteristics:**
  - 键值对存储：只能存储字符串，复杂数据需要JSON序列化 | Key-value storage: Only stores strings, complex data requires JSON serialization
  - 异步操作：所有方法返回Promise，避免阻塞UI线程 | Async operations: All methods return Promises, avoiding UI thread blocking
  - 应用级作用域：数据在应用卸载后清除，不同应用间隔离 | App-scoped: Data cleared on app uninstall, isolated between different apps
  - 存储限制：通常限制在6MB左右（平台相关） | Storage limits: Typically around 6MB (platform-dependent)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. AsyncStorage可以直接存储对象吗？| Can AsyncStorage store objects directly?
     **答案 | Answer:** 不可以 | No - AsyncStorage只能存储字符串。必须使用JSON.stringify()将对象转换为字符串存储，读取时使用JSON.parse()转换回对象。
     AsyncStorage only stores strings. Must use JSON.stringify() to convert objects to strings for storage, and JSON.parse() to convert back when reading.

  2. AsyncStorage的操作会阻塞UI渲染吗？| Do AsyncStorage operations block UI rendering?
     **答案 | Answer:** 不会 | No - 所有AsyncStorage操作都是异步的，在后台线程执行，不会阻塞主线程。这就是为什么必须使用await或.then()获取结果。
     All AsyncStorage operations are asynchronous, executed on background threads, won't block the main thread. This is why you must use await or .then() to get results.

  3. 应用卸载后AsyncStorage的数据会保留吗？| Is AsyncStorage data retained after app uninstall?
     **答案 | Answer:** 不会 | No - AsyncStorage数据在应用卸载时会被清除。如果需要跨安装保留数据，需要使用云端存储或其他持久化方案。
     AsyncStorage data is cleared on app uninstall. If you need data to persist across installations, use cloud storage or other persistence solutions.

  4. AsyncStorage适合存储敏感信息（如密码、token）吗？| Is AsyncStorage suitable for storing sensitive information (passwords, tokens)?
     **答案 | Answer:** 不适合 | Not suitable - AsyncStorage数据未加密，可通过设备访问读取。敏感信息应使用Expo SecureStore或react-native-keychain等加密存储方案。
     AsyncStorage data is unencrypted and can be read with device access. Sensitive information should use encrypted storage like Expo SecureStore or react-native-keychain.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // AsyncStorage完整使用指南 | Complete AsyncStorage usage guide
  import AsyncStorage from '@react-native-async-storage/async-storage';

  // 1. 基础CRUD操作 | Basic CRUD operations

  // 存储数据 | Store data
  const storeData = async (key, value) => {
    try {
      // 将对象转换为JSON字符串 | Convert object to JSON string
      const jsonValue = JSON.stringify(value);
      await AsyncStorage.setItem(key, jsonValue);
      console.log('Data stored successfully');
    } catch (error) {
      console.error('Error storing data:', error);
      throw error;
    }
  };

  // 读取数据 | Read data
  const getData = async (key) => {
    try {
      const jsonValue = await AsyncStorage.getItem(key);
      // 如果键不存在，返回null | Returns null if key doesn't exist
      return jsonValue != null ? JSON.parse(jsonValue) : null;
    } catch (error) {
      console.error('Error reading data:', error);
      throw error;
    }
  };

  // 删除数据 | Delete data
  const removeData = async (key) => {
    try {
      await AsyncStorage.removeItem(key);
      console.log('Data removed successfully');
    } catch (error) {
      console.error('Error removing data:', error);
      throw error;
    }
  };

  // 清空所有数据 | Clear all data
  const clearAll = async () => {
    try {
      await AsyncStorage.clear();
      console.log('All data cleared');
    } catch (error) {
      console.error('Error clearing data:', error);
      throw error;
    }
  };

  // 2. 书签管理专用函数 | Bookmark management specific functions

  const BOOKMARKS_KEY = '@news_app_bookmarks'; // 使用应用前缀避免冲突 | Use app prefix to avoid conflicts

  // 保存书签列表 | Save bookmarks list
  const saveBookmarks = async (bookmarks) => {
    try {
      // 添加元数据 | Add metadata
      const dataToStore = {
        bookmarks,
        lastUpdated: new Date().toISOString(),
        version: '1.0', // 用于未来数据迁移 | For future data migration
      };

      await AsyncStorage.setItem(BOOKMARKS_KEY, JSON.stringify(dataToStore));
      console.log(`Saved ${bookmarks.length} bookmarks`);
    } catch (error) {
      console.error('Failed to save bookmarks:', error);
      // 可以在这里添加错误上报逻辑 | Can add error reporting logic here
      throw error;
    }
  };

  // 加载书签列表 | Load bookmarks list
  const loadBookmarks = async () => {
    try {
      const stored = await AsyncStorage.getItem(BOOKMARKS_KEY);

      if (!stored) {
        // 首次运行，返回空数组 | First run, return empty array
        return [];
      }

      const data = JSON.parse(stored);

      // 检查数据版本，处理迁移 | Check data version, handle migration
      if (data.version !== '1.0') {
        console.warn('Old data version detected, migrating...');
        // 这里可以添加数据迁移逻辑 | Can add data migration logic here
      }

      return data.bookmarks || [];
    } catch (error) {
      console.error('Failed to load bookmarks:', error);
      // 发生错误时返回空数组，避免应用崩溃 | Return empty array on error to prevent app crash
      return [];
    }
  };

  // 3. 高级操作：批量更新 | Advanced operations: Batch updates

  // 批量存储多个键值对 | Store multiple key-value pairs in batch
  const multiSet = async (keyValuePairs) => {
    try {
      // keyValuePairs格式: [['@key1', 'value1'], ['@key2', 'value2']]
      // keyValuePairs format: [['@key1', 'value1'], ['@key2', 'value2']]
      await AsyncStorage.multiSet(keyValuePairs);
      console.log('Multiple items stored');
    } catch (error) {
      console.error('Error in multiSet:', error);
      throw error;
    }
  };

  // 批量读取多个键 | Read multiple keys in batch
  const multiGet = async (keys) => {
    try {
      // keys格式: ['@key1', '@key2']
      // keys format: ['@key1', '@key2']
      const values = await AsyncStorage.multiGet(keys);
      // 返回格式: [['@key1', 'value1'], ['@key2', 'value2']]
      // Returns: [['@key1', 'value1'], ['@key2', 'value2']]
      return values;
    } catch (error) {
      console.error('Error in multiGet:', error);
      throw error;
    }
  };

  // 获取所有键 | Get all keys
  const getAllKeys = async () => {
    try {
      const keys = await AsyncStorage.getAllKeys();
      console.log('All keys:', keys);
      return keys;
    } catch (error) {
      console.error('Error getting keys:', error);
      throw error;
    }
  };

  // 4. 实用工具函数 | Utility functions

  // 合并现有值 | Merge with existing value
  const mergeData = async (key, newData) => {
    try {
      // 读取现有数据 | Read existing data
      const existing = await getData(key) || {};

      // 合并新旧数据 | Merge new and old data
      const merged = { ...existing, ...newData };

      // 保存合并后的数据 | Save merged data
      await storeData(key, merged);

      return merged;
    } catch (error) {
      console.error('Error merging data:', error);
      throw error;
    }
  };

  // 带缓存的数据获取 | Data retrieval with caching
  const getCachedData = async (key, fetchFunction, expiryMinutes = 30) => {
    try {
      const cached = await getData(key);

      if (cached && cached.data && cached.timestamp) {
        const age = Date.now() - new Date(cached.timestamp).getTime();
        const maxAge = expiryMinutes * 60 * 1000;

        if (age < maxAge) {
          console.log('Returning cached data');
          return cached.data;
        }
      }

      // 缓存过期或不存在，重新获取 | Cache expired or doesn't exist, fetch again
      console.log('Fetching fresh data');
      const freshData = await fetchFunction();

      await storeData(key, {
        data: freshData,
        timestamp: new Date().toISOString(),
      });

      return freshData;
    } catch (error) {
      console.error('Error in getCachedData:', error);
      throw error;
    }
  };

  // 5. 错误处理和边界情况 | Error handling and edge cases

  // 安全的数据存储（带重试机制） | Safe data storage (with retry mechanism)
  const safeStore = async (key, value, maxRetries = 3) => {
    let retries = 0;

    while (retries < maxRetries) {
      try {
        await storeData(key, value);
        return true;
      } catch (error) {
        retries++;
        console.warn(`Storage attempt ${retries} failed:`, error);

        if (retries === maxRetries) {
          console.error('Max retries reached, storage failed');
          return false;
        }

        // 等待后重试 | Wait before retrying
        await new Promise(resolve => setTimeout(resolve, 1000 * retries));
      }
    }
  };

  // 检查存储空间 | Check storage space
  const checkStorageSize = async () => {
    try {
      const keys = await AsyncStorage.getAllKeys();
      let totalSize = 0;

      for (const key of keys) {
        const value = await AsyncStorage.getItem(key);
        if (value) {
          // 计算字节大小 | Calculate byte size
          totalSize += new Blob([value]).size;
        }
      }

      const sizeInMB = (totalSize / (1024 * 1024)).toFixed(2);
      console.log(`Total storage used: ${sizeInMB} MB`);

      return totalSize;
    } catch (error) {
      console.error('Error checking storage size:', error);
      return 0;
    }
  };
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要在存储的数据中包含version字段？| Why include a version field in stored data?
    **答案 | Answer:** 支持数据迁移 | To support data migration - 当应用更新、数据结构改变时，可以通过version识别旧数据并进行迁移，避免读取失败或数据丢失。
    When the app updates and data structure changes, version helps identify old data and perform migration, avoiding read failures or data loss.

  - getCachedData函数的expiryMinutes参数有什么作用？| What's the purpose of expiryMinutes in getCachedData?
    **答案 | Answer:** 控制缓存有效期 | Controls cache validity period - 避免使用过期数据。例如新闻数据30分钟后重新获取，确保内容新鲜度；用户设置可以设置更长时间。
    Avoids using stale data. For example, refetch news data after 30 minutes to ensure freshness; user settings can have longer expiry.

  **常见误区检查 | Common Misconception Checks:**
  - 每次需要数据时都应该从AsyncStorage读取吗？| Should you read from AsyncStorage every time you need data?
    **答案 | Answer:** 不应该 | No - AsyncStorage虽然异步，但频繁读取仍会影响性能。应该在应用启动时加载到内存（如Context state），之后从内存读取，只在数据变化时写入AsyncStorage。
    Although AsyncStorage is async, frequent reads still impact performance. Load into memory (like Context state) on app start, read from memory afterward, only write to AsyncStorage when data changes.

  - JSON.stringify会保留Date对象吗？| Does JSON.stringify preserve Date objects?
    **答案 | Answer:** 不会 | No - Date对象会被转换为ISO字符串。读取时需要手动转换回Date对象：new Date(dateString)。这也是为什么要存储toISOString()格式。
    Date objects are converted to ISO strings. Must manually convert back when reading: new Date(dateString). This is why storing toISOString() format is recommended.

### 3. 书签UI实现与用户交互 | Bookmark UI Implementation & User Interaction (2小时 | 2 hours)

- **ArticleDetail中的书签按钮 | Bookmark Button in ArticleDetail**

  **概念定义 | Concept Definition:**
  书签按钮是用户与书签功能交互的主要界面元素，通常以图标形式呈现（空心/实心星星或心形）。它需要实时反映当前文章的书签状态，提供即时视觉反馈（如动画效果），并处理添加/删除书签的操作。在ArticleDetail屏幕中，书签按钮通常位于导航栏或文章内容附近的显著位置。

  The bookmark button is the primary UI element for user interaction with bookmark functionality, typically presented as an icon (outlined/filled star or heart). It needs to reflect the current article's bookmark status in real-time, provide immediate visual feedback (like animation), and handle add/remove bookmark operations. In ArticleDetail screen, the bookmark button is usually positioned prominently in the navigation bar or near article content.

  **核心特征 | Key Characteristics:**
  - 状态可视化：通过不同图标样式（空心/实心）清晰表示是否已书签 | Status visualization: Different icon styles (outlined/filled) clearly indicate bookmark status
  - 即时反馈：点击后立即更新UI，无需等待存储操作完成 | Immediate feedback: UI updates instantly on tap, without waiting for storage operations
  - 乐观更新：先更新UI，后台异步保存，提升用户体验 | Optimistic updates: Update UI first, save asynchronously in background, enhancing user experience
  - 动画效果：添加微妙的缩放或颜色变化动画增强交互感 | Animation effects: Add subtle scale or color change animations to enhance interactivity

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 书签按钮应该在保存完成后才更新UI吗？| Should the bookmark button update UI only after save completes?
     **答案 | Answer:** 不应该 | No - 应该使用乐观更新（Optimistic UI）：点击后立即更新UI，异步保存到AsyncStorage。这样用户感知的响应速度最快，即使保存失败也可以回滚UI。
     Should use optimistic updates: Update UI immediately on tap, save to AsyncStorage asynchronously. This provides fastest perceived responsiveness, can rollback UI if save fails.

  2. 如何判断当前文章是否已被书签？| How to determine if the current article is bookmarked?
     **答案 | Answer:** 使用Context提供的isBookmarked函数 | Use isBookmarked function from Context - 传入文章URL，函数在bookmarks数组中查找匹配项。URL是唯一标识符，比标题更可靠。
     Pass article URL, function searches for match in bookmarks array. URL is unique identifier, more reliable than title.

  3. 书签按钮应该显示书签数量吗？| Should the bookmark button display bookmark count?
     **答案 | Answer:** 在详情页不需要 | Not needed in detail page - 详情页只需要显示当前文章的书签状态。书签总数更适合显示在Bookmarks标签的badge或列表顶部。
     Detail page only needs to show current article's bookmark status. Total bookmark count is better suited for badge on Bookmarks tab or list header.

  4. 用户快速点击多次书签按钮会发生什么？| What happens if user rapidly taps bookmark button multiple times?
     **答案 | Answer:** 需要防抖或节流 | Needs debouncing or throttling - 使用loading状态或禁用按钮防止重复操作。但因为使用乐观更新，多次点击只会切换UI状态，最终保存的是最后一次状态。
     Use loading state or disable button to prevent duplicate operations. But with optimistic updates, multiple taps just toggle UI state, final saved state is the last one.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // ArticleDetail屏幕中的书签功能实现 | Bookmark functionality in ArticleDetail screen
  import React, { useState, useEffect } from 'react';
  import {
    View,
    Text,
    ScrollView,
    StyleSheet,
    Image,
    Pressable,
    Animated,
    Alert,
  } from 'react-native';
  import { Ionicons } from '@expo/vector-icons';
  import { useBookmarks } from '../contexts/BookmarksContext';

  const ArticleDetailScreen = ({ route, navigation }) => {
    const { article } = route.params; // 从导航参数获取文章数据 | Get article data from navigation params
    const { isBookmarked, addBookmark, removeBookmark } = useBookmarks();

    // 本地状态：当前书签状态（用于乐观更新） | Local state: current bookmark status (for optimistic updates)
    const [bookmarked, setBookmarked] = useState(false);

    // 动画值：用于书签按钮缩放效果 | Animation value: for bookmark button scale effect
    const scaleAnim = useState(new Animated.Value(1))[0];

    // 加载状态：防止重复点击 | Loading state: prevent duplicate taps
    const [isProcessing, setIsProcessing] = useState(false);

    // 初始化书签状态 | Initialize bookmark status
    useEffect(() => {
      setBookmarked(isBookmarked(article.url));
    }, [article.url, isBookmarked]);

    // 配置导航栏右侧按钮 | Configure navigation bar right button
    useEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <BookmarkButton
            bookmarked={bookmarked}
            onPress={handleToggleBookmark}
            disabled={isProcessing}
          />
        ),
      });
    }, [bookmarked, isProcessing]);

    // 书签动画效果 | Bookmark animation effect
    const animateBookmark = () => {
      Animated.sequence([
        Animated.timing(scaleAnim, {
          toValue: 1.3,
          duration: 150,
          useNativeDriver: true,
        }),
        Animated.timing(scaleAnim, {
          toValue: 1,
          duration: 150,
          useNativeDriver: true,
        }),
      ]).start();
    };

    // 切换书签状态 | Toggle bookmark status
    const handleToggleBookmark = async () => {
      if (isProcessing) return; // 防止重复点击 | Prevent duplicate taps

      setIsProcessing(true);

      try {
        // 乐观更新UI | Optimistic UI update
        const newBookmarkedState = !bookmarked;
        setBookmarked(newBookmarkedState);
        animateBookmark();

        // 准备要保存的文章数据 | Prepare article data to save
        const bookmarkData = {
          url: article.url,
          title: article.title,
          description: article.description,
          urlToImage: article.urlToImage,
          author: article.author,
          source: article.source,
          publishedAt: article.publishedAt,
        };

        // 异步保存到Context（会自动保存到AsyncStorage） | Save to Context asynchronously (auto-saves to AsyncStorage)
        if (newBookmarkedState) {
          await addBookmark(bookmarkData);
        } else {
          await removeBookmark(article.url);
        }

        // 显示简短提示 | Show brief toast
        // 注意：React Native没有内置Toast，这里使用Alert简化
        // Note: React Native doesn't have built-in Toast, using Alert here for simplicity
        // 生产环境应使用react-native-toast-message等库
        // In production, use libraries like react-native-toast-message
      } catch (error) {
        console.error('Failed to toggle bookmark:', error);

        // 保存失败，回滚UI | Save failed, rollback UI
        setBookmarked(!bookmarked);

        Alert.alert(
          '操作失败 | Operation Failed',
          '无法保存书签，请重试 | Failed to save bookmark, please try again',
          [{ text: '确定 | OK' }]
        );
      } finally {
        setIsProcessing(false);
      }
    };

    return (
      <ScrollView style={styles.container}>
        {/* 文章图片 | Article image */}
        {article.urlToImage && (
          <Image
            source={{ uri: article.urlToImage }}
            style={styles.image}
            resizeMode="cover"
          />
        )}

        {/* 文章元信息 | Article metadata */}
        <View style={styles.meta}>
          <Text style={styles.source}>{article.source?.name}</Text>
          <Text style={styles.date}>
            {new Date(article.publishedAt).toLocaleDateString()}
          </Text>
        </View>

        {/* 文章标题 | Article title */}
        <Text style={styles.title}>{article.title}</Text>

        {/* 作者 | Author */}
        {article.author && (
          <Text style={styles.author}>作者 | By: {article.author}</Text>
        )}

        {/* 文章内容 | Article content */}
        <Text style={styles.content}>{article.description}</Text>
        <Text style={styles.content}>{article.content}</Text>

        {/* 内容中的书签按钮（可选） | In-content bookmark button (optional) */}
        <View style={styles.actionBar}>
          <Pressable
            style={[styles.actionButton, bookmarked && styles.bookmarked]}
            onPress={handleToggleBookmark}
            disabled={isProcessing}
          >
            <Animated.View style={{ transform: [{ scale: scaleAnim }] }}>
              <Ionicons
                name={bookmarked ? 'bookmark' : 'bookmark-outline'}
                size={24}
                color={bookmarked ? '#FF6347' : '#666'}
              />
            </Animated.View>
            <Text style={styles.actionText}>
              {bookmarked ? '已书签 | Bookmarked' : '书签 | Bookmark'}
            </Text>
          </Pressable>

          {/* 分享按钮 | Share button */}
          <Pressable style={styles.actionButton}>
            <Ionicons name="share-outline" size={24} color="#666" />
            <Text style={styles.actionText}>分享 | Share</Text>
          </Pressable>
        </View>

        {/* 阅读原文按钮 | Read full article button */}
        <Pressable style={styles.readMoreButton}>
          <Text style={styles.readMoreText}>
            阅读原文 | Read Full Article
          </Text>
          <Ionicons name="arrow-forward" size={20} color="#fff" />
        </Pressable>
      </ScrollView>
    );
  };

  // 独立的书签按钮组件（用于导航栏） | Standalone bookmark button component (for navigation bar)
  const BookmarkButton = ({ bookmarked, onPress, disabled }) => {
    return (
      <Pressable
        onPress={onPress}
        disabled={disabled}
        style={({ pressed }) => [
          styles.navButton,
          pressed && styles.navButtonPressed,
        ]}
        hitSlop={10} // 扩大可点击区域 | Expand tappable area
      >
        <Ionicons
          name={bookmarked ? 'bookmark' : 'bookmark-outline'}
          size={28}
          color={bookmarked ? '#FF6347' : '#000'}
        />
      </Pressable>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#fff',
    },
    image: {
      width: '100%',
      height: 250,
    },
    meta: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      paddingHorizontal: 16,
      paddingVertical: 12,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    source: {
      fontSize: 14,
      fontWeight: '600',
      color: '#FF6347',
    },
    date: {
      fontSize: 12,
      color: '#999',
    },
    title: {
      fontSize: 24,
      fontWeight: 'bold',
      paddingHorizontal: 16,
      paddingTop: 16,
      lineHeight: 32,
    },
    author: {
      fontSize: 14,
      color: '#666',
      paddingHorizontal: 16,
      paddingTop: 8,
      fontStyle: 'italic',
    },
    content: {
      fontSize: 16,
      lineHeight: 24,
      paddingHorizontal: 16,
      paddingTop: 16,
      color: '#333',
    },
    actionBar: {
      flexDirection: 'row',
      justifyContent: 'space-around',
      paddingVertical: 20,
      borderTopWidth: 1,
      borderTopColor: '#eee',
      marginTop: 20,
    },
    actionButton: {
      alignItems: 'center',
      padding: 10,
    },
    bookmarked: {
      // 书签状态的额外样式 | Additional style for bookmarked state
    },
    actionText: {
      fontSize: 12,
      marginTop: 4,
      color: '#666',
    },
    readMoreButton: {
      flexDirection: 'row',
      backgroundColor: '#FF6347',
      marginHorizontal: 16,
      marginVertical: 24,
      paddingVertical: 14,
      paddingHorizontal: 20,
      borderRadius: 8,
      alignItems: 'center',
      justifyContent: 'center',
    },
    readMoreText: {
      color: '#fff',
      fontSize: 16,
      fontWeight: '600',
      marginRight: 8,
    },
    navButton: {
      marginRight: 16,
      padding: 4,
    },
    navButtonPressed: {
      opacity: 0.6,
    },
  });

  export default ArticleDetailScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么要使用Animated.sequence而不是单个Animated.timing？| Why use Animated.sequence instead of a single Animated.timing?
    **答案 | Answer:** 创建连续动画效果 | To create sequential animation - sequence让动画先放大到1.3倍再缩回1倍，产生"弹跳"效果，比单纯的闪烁更有质感，增强用户操作的满足感。
    Sequence makes animation scale up to 1.3x then back to 1x, creating a "bounce" effect that's more satisfying than simple flashing, enhancing user operation satisfaction.

  - hitSlop属性有什么作用？| What does the hitSlop prop do?
    **答案 | Answer:** 扩大触摸区域 | Expands touch area - 在导航栏中按钮较小，hitSlop扩大可触摸区域（上下左右各10像素），让用户更容易点击，特别是在移动设备上。
    Navigation bar buttons are small, hitSlop expands tappable area (10 pixels on all sides), making it easier to tap, especially on mobile devices.

  **常见误区检查 | Common Misconception Checks:**
  - 乐观更新会导致数据不一致吗？| Can optimistic updates cause data inconsistency?
    **答案 | Answer:** 可能会，需要错误处理 | Potentially yes, needs error handling - 如果保存失败，必须回滚UI到原状态并通知用户。但在书签这种低风险操作中，失败概率很低，乐观更新带来的体验提升值得这个风险。
    If save fails, must rollback UI to original state and notify user. But for low-risk operations like bookmarking, failure probability is low, UX improvement from optimistic updates justifies the risk.

  - 应该在每次点击时都播放动画吗？| Should animation play on every tap?
    **答案 | Answer:** 是的 | Yes - 无论是添加还是删除书签，都应该有动画反馈。这提供了一致的交互体验，让用户明确知道操作已被识别和执行。
    Whether adding or removing bookmarks, animation feedback should occur. This provides consistent interaction experience, letting users know the operation was recognized and executed.

- **Bookmarks列表屏幕 | Bookmarks List Screen**

  **概念定义 | Concept Definition:**
  Bookmarks屏幕是用户查看和管理所有已保存文章的中心界面。它需要显示书签列表、处理空状态（无书签时）、支持删除操作（通过滑动手势或按钮），并提供导航到文章详情的功能。良好的Bookmarks屏幕应该直观、响应快速，并提供批量管理选项（如全部清除、排序等）。

  The Bookmarks screen is the central interface for users to view and manage all saved articles. It needs to display the bookmark list, handle empty states (when no bookmarks exist), support deletion operations (via swipe gestures or buttons), and provide navigation to article details. A well-designed Bookmarks screen should be intuitive, responsive, and offer bulk management options (like clear all, sorting).

  **核心特征 | Key Characteristics:**
  - 列表渲染：使用FlatList或ScrollView展示所有书签文章 | List rendering: Use FlatList or ScrollView to display all bookmarked articles
  - 空状态处理：当无书签时显示友好提示和引导 | Empty state handling: Show friendly message and guidance when no bookmarks exist
  - 滑动删除：使用手势库实现iOS风格的滑动删除交互 | Swipe to delete: Use gesture library to implement iOS-style swipe-to-delete interaction
  - 排序选项：按添加时间、标题或来源排序书签 | Sorting options: Sort bookmarks by added time, title, or source

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. Bookmarks屏幕应该直接从AsyncStorage读取数据吗？| Should the Bookmarks screen read data directly from AsyncStorage?
     **答案 | Answer:** 不应该 | No - 应该从BookmarksContext读取，Context已经在应用启动时加载了数据到内存。直接读AsyncStorage会导致每次进入屏幕都重新加载，性能差且可能出现闪烁。
     Should read from BookmarksContext, which already loaded data into memory on app start. Reading AsyncStorage directly would reload on every screen entry, causing poor performance and potential flashing.

  2. 删除书签时是否需要确认对话框？| Do you need a confirmation dialog when deleting bookmarks?
     **答案 | Answer:** 取决于删除方式 | Depends on deletion method - 滑动删除通常不需要确认（因为是故意操作），但批量删除（如"全部清除"）应该有确认对话框，防止误操作造成大量数据丢失。
     Swipe-to-delete usually doesn't need confirmation (intentional gesture), but bulk deletion (like "clear all") should have confirmation dialog to prevent accidental data loss.

  3. 空状态屏幕应该显示什么内容？| What should the empty state screen display?
     **答案 | Answer:** 友好的提示和行动引导 | Friendly message and call-to-action - 例如"您还没有保存任何文章"加上一个"浏览文章"按钮导航到首页。空状态是引导用户使用功能的机会，不应该只是显示"无数据"。
     For example, "You haven't saved any articles yet" with a "Browse Articles" button navigating to home. Empty state is an opportunity to guide users to use the feature, shouldn't just say "No data".

  4. 书签列表是否应该支持下拉刷新？| Should the bookmark list support pull-to-refresh?
     **答案 | Answer:** 不必要 | Not necessary - 书签是本地数据，不需要从服务器刷新。但如果未来要同步到云端，则需要添加刷新功能从云端拉取最新书签。
     Bookmarks are local data, don't need server refresh. But if cloud sync is added in the future, refresh functionality would be needed to pull latest bookmarks from cloud.

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Bookmarks屏幕完整实现 | Complete Bookmarks screen implementation
  import React, { useState } from 'react';
  import {
    View,
    Text,
    FlatList,
    StyleSheet,
    Image,
    Pressable,
    Alert,
  } from 'react-native';
  import { Ionicons } from '@expo/vector-icons';
  import { useBookmarks } from '../contexts/BookmarksContext';
  import Swipeable from 'react-native-gesture-handler/Swipeable';

  const BookmarksScreen = ({ navigation }) => {
    const { bookmarks, removeBookmark, isLoading } = useBookmarks();
    const [sortBy, setSortBy] = useState('date'); // 'date', 'title', 'source'

    // 排序书签 | Sort bookmarks
    const getSortedBookmarks = () => {
      const sorted = [...bookmarks];

      switch (sortBy) {
        case 'date':
          return sorted.sort((a, b) =>
            new Date(b.bookmarkedAt) - new Date(a.bookmarkedAt)
          );
        case 'title':
          return sorted.sort((a, b) =>
            a.title.localeCompare(b.title)
          );
        case 'source':
          return sorted.sort((a, b) =>
            a.source?.name.localeCompare(b.source?.name)
          );
        default:
          return sorted;
      }
    };

    // 处理删除书签 | Handle bookmark deletion
    const handleDelete = (article) => {
      Alert.alert(
        '删除书签 | Delete Bookmark',
        `确定要删除《${article.title}》吗？| Are you sure you want to delete "${article.title}"?`,
        [
          {
            text: '取消 | Cancel',
            style: 'cancel',
          },
          {
            text: '删除 | Delete',
            style: 'destructive',
            onPress: () => removeBookmark(article.url),
          },
        ]
      );
    };

    // 处理清空所有书签 | Handle clearing all bookmarks
    const handleClearAll = () => {
      if (bookmarks.length === 0) return;

      Alert.alert(
        '清空所有书签 | Clear All Bookmarks',
        `确定要删除全部 ${bookmarks.length} 个书签吗？此操作无法撤销。| Are you sure you want to delete all ${bookmarks.length} bookmarks? This cannot be undone.`,
        [
          { text: '取消 | Cancel', style: 'cancel' },
          {
            text: '清空 | Clear All',
            style: 'destructive',
            onPress: () => {
              bookmarks.forEach(article => removeBookmark(article.url));
            },
          },
        ]
      );
    };

    // 渲染滑动删除的右侧按钮 | Render swipe-to-delete right action
    const renderRightActions = (progress, dragX, article) => {
      return (
        <Pressable
          style={styles.deleteButton}
          onPress={() => handleDelete(article)}
        >
          <Ionicons name="trash-outline" size={24} color="#fff" />
          <Text style={styles.deleteButtonText}>删除 | Delete</Text>
        </Pressable>
      );
    };

    // 渲染单个书签项 | Render individual bookmark item
    const renderBookmarkItem = ({ item }) => (
      <Swipeable
        renderRightActions={(progress, dragX) =>
          renderRightActions(progress, dragX, item)
        }
        rightThreshold={40}
        friction={2}
      >
        <Pressable
          style={styles.bookmarkItem}
          onPress={() => navigation.navigate('ArticleDetail', { article: item })}
        >
          {/* 文章缩略图 | Article thumbnail */}
          {item.urlToImage ? (
            <Image
              source={{ uri: item.urlToImage }}
              style={styles.thumbnail}
              resizeMode="cover"
            />
          ) : (
            <View style={[styles.thumbnail, styles.placeholderThumbnail]}>
              <Ionicons name="image-outline" size={32} color="#ccc" />
            </View>
          )}

          {/* 文章信息 | Article info */}
          <View style={styles.itemContent}>
            <Text style={styles.itemTitle} numberOfLines={2}>
              {item.title}
            </Text>

            <Text style={styles.itemDescription} numberOfLines={2}>
              {item.description}
            </Text>

            <View style={styles.itemMeta}>
              <Text style={styles.itemSource}>{item.source?.name}</Text>
              <Text style={styles.itemDate}>
                {new Date(item.bookmarkedAt).toLocaleDateString()}
              </Text>
            </View>
          </View>

          {/* 书签图标 | Bookmark icon */}
          <View style={styles.bookmarkIcon}>
            <Ionicons name="bookmark" size={20} color="#FF6347" />
          </View>
        </Pressable>
      </Swipeable>
    );

    // 渲染空状态 | Render empty state
    const renderEmptyState = () => (
      <View style={styles.emptyContainer}>
        <Ionicons name="bookmark-outline" size={80} color="#ccc" />
        <Text style={styles.emptyTitle}>
          还没有书签 | No Bookmarks Yet
        </Text>
        <Text style={styles.emptyDescription}>
          保存您喜欢的文章，稍后阅读{'\n'}
          Bookmark your favorite articles to read later
        </Text>
        <Pressable
          style={styles.browseButton}
          onPress={() => navigation.navigate('Home')}
        >
          <Text style={styles.browseButtonText}>
            浏览文章 | Browse Articles
          </Text>
        </Pressable>
      </View>
    );

    // 渲染列表头部 | Render list header
    const renderHeader = () => (
      <View style={styles.header}>
        <Text style={styles.headerTitle}>
          我的书签 | My Bookmarks ({bookmarks.length})
        </Text>

        <View style={styles.headerActions}>
          {/* 排序选择器 | Sort selector */}
          <Pressable
            style={styles.sortButton}
            onPress={() => {
              Alert.alert('排序方式 | Sort By', '', [
                {
                  text: '按时间 | By Date',
                  onPress: () => setSortBy('date'),
                },
                {
                  text: '按标题 | By Title',
                  onPress: () => setSortBy('title'),
                },
                {
                  text: '按来源 | By Source',
                  onPress: () => setSortBy('source'),
                },
                { text: '取消 | Cancel', style: 'cancel' },
              ]);
            }}
          >
            <Ionicons name="funnel-outline" size={20} color="#666" />
            <Text style={styles.sortButtonText}>排序 | Sort</Text>
          </Pressable>

          {/* 清空按钮 | Clear all button */}
          {bookmarks.length > 0 && (
            <Pressable
              style={styles.clearButton}
              onPress={handleClearAll}
            >
              <Text style={styles.clearButtonText}>清空 | Clear All</Text>
            </Pressable>
          )}
        </View>
      </View>
    );

    if (isLoading) {
      return (
        <View style={styles.centerContainer}>
          <Text>加载中... | Loading...</Text>
        </View>
      );
    }

    return (
      <View style={styles.container}>
        <FlatList
          data={getSortedBookmarks()}
          renderItem={renderBookmarkItem}
          keyExtractor={(item) => item.url}
          ListHeaderComponent={renderHeader}
          ListEmptyComponent={renderEmptyState}
          contentContainerStyle={
            bookmarks.length === 0 && styles.emptyContentContainer
          }
          ItemSeparatorComponent={() => <View style={styles.separator} />}
        />
      </View>
    );
  };

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      backgroundColor: '#f5f5f5',
    },
    centerContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
    },
    header: {
      backgroundColor: '#fff',
      padding: 16,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    headerTitle: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 12,
    },
    headerActions: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
    },
    sortButton: {
      flexDirection: 'row',
      alignItems: 'center',
      padding: 8,
      backgroundColor: '#f0f0f0',
      borderRadius: 6,
    },
    sortButtonText: {
      marginLeft: 4,
      fontSize: 14,
      color: '#666',
    },
    clearButton: {
      padding: 8,
    },
    clearButtonText: {
      fontSize: 14,
      color: '#FF6347',
      fontWeight: '600',
    },
    bookmarkItem: {
      flexDirection: 'row',
      backgroundColor: '#fff',
      padding: 12,
      alignItems: 'center',
    },
    thumbnail: {
      width: 80,
      height: 80,
      borderRadius: 8,
      marginRight: 12,
    },
    placeholderThumbnail: {
      backgroundColor: '#f0f0f0',
      justifyContent: 'center',
      alignItems: 'center',
    },
    itemContent: {
      flex: 1,
      justifyContent: 'space-between',
    },
    itemTitle: {
      fontSize: 16,
      fontWeight: '600',
      marginBottom: 4,
      color: '#333',
    },
    itemDescription: {
      fontSize: 14,
      color: '#666',
      marginBottom: 8,
    },
    itemMeta: {
      flexDirection: 'row',
      justifyContent: 'space-between',
    },
    itemSource: {
      fontSize: 12,
      color: '#FF6347',
      fontWeight: '500',
    },
    itemDate: {
      fontSize: 12,
      color: '#999',
    },
    bookmarkIcon: {
      marginLeft: 8,
    },
    separator: {
      height: 1,
      backgroundColor: '#eee',
    },
    deleteButton: {
      backgroundColor: '#FF3B30',
      justifyContent: 'center',
      alignItems: 'center',
      width: 80,
      height: '100%',
    },
    deleteButtonText: {
      color: '#fff',
      fontSize: 12,
      marginTop: 4,
    },
    emptyContainer: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      paddingHorizontal: 40,
    },
    emptyContentContainer: {
      flexGrow: 1,
    },
    emptyTitle: {
      fontSize: 20,
      fontWeight: '600',
      marginTop: 16,
      marginBottom: 8,
      color: '#333',
    },
    emptyDescription: {
      fontSize: 14,
      color: '#999',
      textAlign: 'center',
      marginBottom: 24,
      lineHeight: 20,
    },
    browseButton: {
      backgroundColor: '#FF6347',
      paddingVertical: 12,
      paddingHorizontal: 24,
      borderRadius: 8,
    },
    browseButtonText: {
      color: '#fff',
      fontSize: 16,
      fontWeight: '600',
    },
  });

  export default BookmarksScreen;
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么使用Swipeable组件而不是自己实现手势处理？| Why use Swipeable component instead of implementing gesture handling yourself?
    **答案 | Answer:** Swipeable提供了开箱即用的iOS风格滑动删除交互 | Swipeable provides out-of-the-box iOS-style swipe-to-delete interaction - 手动实现需要处理pan手势、动画、阈值判断等复杂逻辑。使用现成组件节省时间、减少bug，并保证跨平台一致性。
    Manual implementation requires handling pan gestures, animations, threshold logic. Using ready-made component saves time, reduces bugs, ensures cross-platform consistency.

  - ListEmptyComponent何时会被渲染？| When is ListEmptyComponent rendered?
    **答案 | Answer:** 当FlatList的data为空数组时 | When FlatList's data is an empty array - 即bookmarks.length === 0时。这是React Native提供的专门处理空状态的API，比条件渲染更优雅。
    When bookmarks.length === 0. This is React Native's dedicated API for handling empty states, more elegant than conditional rendering.

  **常见误区检查 | Common Misconception Checks:**
  - 滑动删除后是否需要调用FlatList的rerender？| Do you need to call FlatList rerender after swipe-to-delete?
    **答案 | Answer:** 不需要 | No - removeBookmark会更新Context state，触发BookmarksScreen重新渲染，FlatList会自动接收新的bookmarks数据并更新UI。React的响应式系统自动处理。
    removeBookmark updates Context state, triggering BookmarksScreen re-render, FlatList automatically receives new bookmarks data and updates UI. React's reactive system handles this automatically.

  - 空状态应该使用单独的Screen吗？| Should empty state use a separate Screen?
    **答案 | Answer:** 不应该 | No - 空状态是列表的一种状态，不是独立屏幕。使用FlatList的ListEmptyComponent在同一屏幕中条件渲染，保持导航结构简单，用户不需要额外的导航操作。
    Empty state is a list state, not a separate screen. Using FlatList's ListEmptyComponent for conditional rendering in same screen keeps navigation structure simple, no extra navigation needed for users.
