# React Native入门 - 第4周第2天：传递屏幕间数据 | React Native Introduction - Week 4 Day 2: Passing Data Between Screens

## 学习目标 | Learning Objectives
- 理解React Navigation中的参数传递机制及其与Web URL查询字符串的区别 | Understand parameter passing mechanism in React Navigation and how it differs from web URL query strings
- 掌握使用navigation.navigate()传递参数到目标屏幕的方法 | Master passing parameters to target screens using navigation.navigate()
- 学会在目标屏幕中访问和使用route.params获取传递的数据 | Learn to access and use route.params to retrieve passed data in target screens
- 能够使用navigation.setParams()动态更新屏幕参数 | Be able to dynamically update screen parameters using navigation.setParams()
- 实现列表到详情页的数据传递模式和返回导航功能 | Implement list-to-detail data passing pattern and back navigation functionality
- 理解参数传递的最佳实践和常见陷阱 | Understand best practices and common pitfalls in parameter passing

## 详细内容 | Detailed Content

### 1. 导航参数基础概念 | Navigation Parameters Fundamentals (1小时 | 1 hour)

- **导航参数的本质 | The Nature of Navigation Parameters**

  **概念定义 | Concept Definition:**
  在React Navigation中，参数（params）是一种在不同屏幕之间传递数据的机制。与Web开发中的URL查询字符串不同，导航参数存储在JavaScript内存中，而不是URL中。这使得数据传递更加灵活，可以传递任何JavaScript值，包括对象、数组和函数。| In React Navigation, parameters (params) are a mechanism for passing data between different screens. Unlike URL query strings in web development, navigation parameters are stored in JavaScript memory rather than in URLs. This makes data passing more flexible, allowing any JavaScript value to be passed, including objects, arrays, and functions.

  **核心特征 | Key Characteristics:**
  - 参数存储在导航状态中，随屏幕一起管理生命周期 | Parameters are stored in navigation state and managed with screen lifecycle
  - 可以传递复杂数据结构，不限于字符串类型 | Can pass complex data structures, not limited to string types
  - 参数变化会触发屏幕重新渲染（如果使用了该参数） | Parameter changes trigger screen re-renders (if the parameter is used)
  - 每个屏幕实例都有独立的参数对象 | Each screen instance has an independent params object
  - 参数是可选的，屏幕可以定义默认值 | Parameters are optional, screens can define default values

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 导航参数存储在URL中吗？| Are navigation parameters stored in URLs?
     **答案 | Answer:** 否 | No - 导航参数存储在JavaScript内存的导航状态中，不是URL的一部分 | Navigation parameters are stored in navigation state in JavaScript memory, not as part of URLs

  2. 我们可以通过导航参数传递对象和数组吗？| Can we pass objects and arrays through navigation parameters?
     **答案 | Answer:** 是 | Yes - React Navigation允许传递任何JavaScript值，包括复杂对象 | React Navigation allows passing any JavaScript value, including complex objects

  3. 当参数改变时，屏幕会重新渲染吗？| Does the screen re-render when parameters change?
     **答案 | Answer:** 是 | Yes - 如果组件使用了route.params，参数变化会触发重新渲染 | If the component uses route.params, parameter changes trigger re-renders

  4. 每个屏幕实例是否共享相同的参数对象？| Do all screen instances share the same params object?
     **答案 | Answer:** 否 | No - 堆栈中的每个屏幕实例都有自己独立的参数对象 | Each screen instance in the stack has its own independent params object

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 导航参数基础示例 | Basic navigation parameters example

  // 在源屏幕中传递参数 | Passing parameters from source screen
  function HomeScreen({ navigation }) {
    const handleNavigate = () => {
      // 传递单个参数 | Pass single parameter
      navigation.navigate('Details', { itemId: 42 });
    };

    const handleNavigateMultiple = () => {
      // 传递多个参数 | Pass multiple parameters
      navigation.navigate('Details', {
        itemId: 86,
        itemTitle: 'First Post',
        itemAuthor: 'Dan',
        otherParam: { nested: 'object' }  // 可以传递嵌套对象 | Can pass nested objects
      });
    };

    return (
      <View style={styles.container}>
        <Button title="Go to Details" onPress={handleNavigate} />
        <Button title="Go with Multiple Params" onPress={handleNavigateMultiple} />
      </View>
    );
  }

  // 在目标屏幕中接收参数 | Receiving parameters in target screen
  function DetailsScreen({ route, navigation }) {
    // 解构获取参数 | Destructure to get parameters
    const { itemId, itemTitle, otherParam } = route.params;

    return (
      <View style={styles.container}>
        <Text>Item ID: {itemId}</Text>
        <Text>Title: {itemTitle || 'No title'}</Text>  {/* 提供默认值 | Provide default value */}
        {otherParam && <Text>Nested: {otherParam.nested}</Text>}
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 如果导航到Details屏幕时没有传递itemTitle参数，上面的代码会显示什么？| What will the code above display if no itemTitle parameter is passed when navigating to Details screen?
    **答案 | Answer:** 显示 "Title: No title" | Displays "Title: No title" - 因为使用了逻辑或运算符提供默认值 | Because the logical OR operator is used to provide a default value

  - 如果我想传递一个函数作为参数，这样做安全吗？| Is it safe to pass a function as a parameter?
    **答案 | Answer:** 技术上可行，但不推荐 | Technically possible, but not recommended - 函数不能序列化，会影响深度链接和状态持久化功能 | Functions cannot be serialized, affecting deep linking and state persistence features

  **常见误区检查 | Common Misconception Checks:**
  - 导航参数和组件props是同一回事吗？| Are navigation parameters and component props the same thing?
    **答案 | Answer:** 不是 | No - 参数通过navigation.navigate传递并存储在导航状态中，而props是React组件的属性。参数通过route.params访问 | Parameters are passed through navigation.navigate and stored in navigation state, while props are React component properties. Parameters are accessed via route.params

  - 修改route.params对象会更新屏幕参数吗？| Does modifying the route.params object update screen parameters?
    **答案 | Answer:** 不会 | No - route.params是只读的。要更新参数必须使用navigation.setParams() | route.params is read-only. To update parameters, you must use navigation.setParams()

- **参数传递 vs URL查询字符串 | Parameters vs URL Query Strings**

  **概念定义 | Concept Definition:**
  在Web开发中，数据通过URL查询字符串传递（如 ?id=42&name=John），这些数据是字符串格式且对用户可见。在React Native移动应用中，没有地址栏，参数通过JavaScript对象传递，可以是任何数据类型，且仅存在于应用内存中。这种差异影响了数据传递的方式和最佳实践。| In web development, data is passed through URL query strings (like ?id=42&name=John), which are string-formatted and visible to users. In React Native mobile apps, there's no address bar, parameters are passed as JavaScript objects, can be of any data type, and exist only in application memory. This difference affects how data is passed and best practices.

  **核心特征 | Key Characteristics:**
  - URL查询字符串只能传递字符串，需要手动序列化/反序列化 | URL query strings can only pass strings, requiring manual serialization/deserialization
  - 导航参数可以直接传递JavaScript对象，无需转换 | Navigation parameters can directly pass JavaScript objects without conversion
  - URL在浏览器地址栏可见，参数在移动应用中不可见 | URLs are visible in browser address bar, parameters are invisible in mobile apps
  - URL有长度限制，导航参数没有实际长度限制（仅受内存限制） | URLs have length limits, navigation parameters have no practical length limits (only memory-limited)
  - URL可以被收藏和分享，导航参数是临时的应用状态 | URLs can be bookmarked and shared, navigation parameters are temporary app state

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 在React Navigation中，我需要将对象转换为字符串才能传递吗？| In React Navigation, do I need to convert objects to strings before passing them?
     **答案 | Answer:** 否 | No - 可以直接传递JavaScript对象，不需要JSON.stringify | Can directly pass JavaScript objects without JSON.stringify

  2. 导航参数对用户可见吗？| Are navigation parameters visible to users?
     **答案 | Answer:** 否 | No - 参数存储在内存中，用户看不到，除非你在UI中显示它们 | Parameters are stored in memory and invisible to users unless you display them in the UI

  3. 我可以在Web和React Native中使用相同的参数传递方式吗？| Can I use the same parameter passing approach in Web and React Native?
     **答案 | Answer:** 概念相似但实现不同 | Concepts are similar but implementations differ - Web使用URL查询字符串，React Native使用导航参数对象 | Web uses URL query strings, React Native uses navigation params objects

  4. 如果想实现深度链接（从外部URL打开应用），导航参数足够吗？| If I want to implement deep linking (opening the app from external URLs), are navigation parameters sufficient?
     **答案 | Answer:** 不够 | Not sufficient - 深度链接需要配置URL到屏幕的映射，并从URL解析参数 | Deep linking requires configuring URL-to-screen mapping and parsing parameters from URLs

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // Web方式（URL查询字符串）vs React Native方式（导航参数）
  // Web approach (URL query strings) vs React Native approach (navigation params)

  // ❌ Web方式 - 不适合React Native | Web approach - not suitable for React Native
  // URL: /details?id=42&title=Hello%20World&tags=react,native
  // 需要手动解析和编码 | Requires manual parsing and encoding
  const queryString = '?id=42&title=Hello%20World&tags=react,native';
  const params = new URLSearchParams(queryString);
  const id = params.get('id');  // 返回字符串 "42" | Returns string "42"
  const title = params.get('title');  // 需要解码 | Needs decoding

  // ✅ React Native方式 - 推荐 | React Native approach - recommended
  // 传递复杂对象，无需序列化 | Pass complex objects without serialization
  navigation.navigate('Details', {
    id: 42,  // 数字类型 | Number type
    title: 'Hello World',  // 字符串，无需编码 | String, no encoding needed
    tags: ['react', 'native'],  // 数组 | Array
    user: {  // 嵌套对象 | Nested object
      name: 'John',
      verified: true
    },
    timestamp: new Date()  // 甚至可以传递Date对象 | Can even pass Date objects
  });

  // 在目标屏幕中直接使用，类型安全 | Use directly in target screen with type safety
  function DetailsScreen({ route }) {
    const { id, title, tags, user, timestamp } = route.params;

    return (
      <View>
        <Text>ID: {id}</Text>  {/* id已经是数字 | id is already a number */}
        <Text>Title: {title}</Text>
        <Text>Tags: {tags.join(', ')}</Text>  {/* tags是真实数组 | tags is a real array */}
        <Text>User: {user.name} ({user.verified ? '✓' : '✗'})</Text>
        <Text>Time: {timestamp.toLocaleString()}</Text>  {/* Date对象方法可用 | Date object methods available */}
      </View>
    );
  }

  // 对比：如果使用Web方式需要的额外工作 | Comparison: extra work needed with Web approach
  // ❌ 不推荐在React Native中这样做 | Not recommended in React Native
  const webStyleParams = {
    id: '42',  // 必须是字符串 | Must be string
    title: 'Hello World',
    tags: 'react,native',  // 数组需要序列化 | Array needs serialization
    user: JSON.stringify({ name: 'John', verified: true }),  // 对象需要JSON序列化 | Object needs JSON serialization
    timestamp: new Date().toISOString()  // 日期需要转换为字符串 | Date needs conversion to string
  };

  // 在目标屏幕需要反序列化 | Deserialization needed in target screen
  const id = parseInt(route.params.id);  // 转换回数字 | Convert back to number
  const tags = route.params.tags.split(',');  // 转换回数组 | Convert back to array
  const user = JSON.parse(route.params.user);  // 解析JSON | Parse JSON
  const timestamp = new Date(route.params.timestamp);  // 转换回Date | Convert back to Date
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 为什么在React Native中直接传递JavaScript对象比序列化字符串更好？| Why is directly passing JavaScript objects better than serializing strings in React Native?
    **答案 | Answer:** 1) 无需序列化/反序列化开销 2) 保持类型安全 3) 代码更简洁易读 4) 避免编码/解码错误 | 1) No serialization/deserialization overhead 2) Maintains type safety 3) More concise and readable code 4) Avoids encoding/decoding errors

  - 什么情况下我们仍然需要序列化参数？| When do we still need to serialize parameters?
    **答案 | Answer:** 实现深度链接时 | When implementing deep linking - 因为外部URL必须是字符串格式，需要配置linking配置来解析URL参数 | Because external URLs must be string-formatted, requiring linking configuration to parse URL parameters

  **常见误区检查 | Common Misconception Checks:**
  - 由于没有URL，React Native应用不能被外部链接打开？| Since there's no URL, React Native apps cannot be opened from external links?
    **答案 | Answer:** 错误 | Wrong - 通过配置深度链接（deep linking），可以使用URL scheme（如myapp://）或通用链接（universal links）打开应用并导航到特定屏幕 | Through deep linking configuration, you can use URL schemes (like myapp://) or universal links to open the app and navigate to specific screens

  - 导航参数会永久保存吗？| Are navigation parameters permanently saved?
    **答案 | Answer:** 否 | No - 参数只在导航堆栈中存在，应用重启后会丢失。如需持久化，应使用AsyncStorage或其他存储方案 | Parameters only exist in the navigation stack and are lost after app restart. For persistence, use AsyncStorage or other storage solutions

### 2. 传递和接收参数 | Passing and Receiving Parameters (1小时 | 1 hour)

- **使用navigation.navigate()传递参数 | Passing Parameters with navigation.navigate()**

  **概念定义 | Concept Definition:**
  navigation.navigate()是React Navigation中导航到其他屏幕的主要方法。它接受两个参数：屏幕名称（字符串）和可选的参数对象。参数对象中的所有属性都会成为目标屏幕的route.params的一部分。这个方法不仅导航到屏幕，还传递数据，是实现屏幕间通信的核心机制。| navigation.navigate() is the primary method for navigating to other screens in React Navigation. It accepts two parameters: screen name (string) and an optional params object. All properties in the params object become part of the target screen's route.params. This method not only navigates to screens but also passes data, being the core mechanism for inter-screen communication.

  **核心特征 | Key Characteristics:**
  - 第一个参数是目标屏幕的名称（必须与导航器中定义的名称匹配） | First parameter is the target screen name (must match the name defined in navigator)
  - 第二个参数是可选的参数对象，可以包含任意数量的属性 | Second parameter is an optional params object that can contain any number of properties
  - 如果屏幕已在堆栈中，navigate会导航到现有实例（不创建新实例） | If the screen is already in the stack, navigate goes to the existing instance (doesn't create a new one)
  - 参数会与目标屏幕现有参数合并（如果屏幕已存在） | Parameters merge with existing parameters of the target screen (if screen already exists)
  - 传递的参数对象会被浅拷贝，不影响原始对象 | The passed params object is shallow-copied, not affecting the original object

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. navigation.navigate()必须提供参数对象吗？| Must navigation.navigate() provide a params object?
     **答案 | Answer:** 否 | No - 参数对象是可选的，可以只传递屏幕名称进行导航 | The params object is optional, you can navigate with just the screen name

  2. 如果我导航到一个已经在堆栈中的屏幕，会创建新实例吗？| If I navigate to a screen already in the stack, will it create a new instance?
     **答案 | Answer:** 否 | No - navigate会返回到现有实例，并更新其参数 | navigate returns to the existing instance and updates its parameters

  3. 我可以在参数对象中传递多少个属性？| How many properties can I pass in the params object?
     **答案 | Answer:** 没有硬性限制 | No hard limit - 可以传递任意数量的属性，但应保持合理以避免性能问题 | Can pass any number of properties, but keep it reasonable to avoid performance issues

  4. 如果屏幕名称不存在会发生什么？| What happens if the screen name doesn't exist?
     **答案 | Answer:** 会抛出错误 | Throws an error - React Navigation会警告找不到该屏幕 | React Navigation will warn that the screen cannot be found

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 传递参数的各种方式 | Various ways to pass parameters

  // 1. 基础参数传递 | Basic parameter passing
  function HomeScreen({ navigation }) {
    return (
      <View style={styles.container}>
        {/* 传递单个参数 | Pass single parameter */}
        <Button
          title="Go to Details"
          onPress={() => navigation.navigate('Details', { itemId: 86 })}
        />

        {/* 传递多个参数 | Pass multiple parameters */}
        <Button
          title="Go with Multiple Params"
          onPress={() => {
            navigation.navigate('Details', {
              itemId: 86,
              otherParam: 'anything you want here',
            });
          }}
        />

        {/* 不传递参数 | Navigate without parameters */}
        <Button
          title="Go without Params"
          onPress={() => navigation.navigate('Details')}
        />
      </View>
    );
  }

  // 2. 传递复杂数据结构 | Passing complex data structures
  function ProductListScreen({ navigation }) {
    const products = [
      { id: 1, name: 'Laptop', price: 999, inStock: true },
      { id: 2, name: 'Phone', price: 699, inStock: false },
      { id: 3, name: 'Tablet', price: 449, inStock: true },
    ];

    const navigateToProduct = (product) => {
      // 传递整个对象 | Pass entire object
      navigation.navigate('ProductDetail', {
        product: product,  // 对象 | Object
        source: 'product_list',  // 字符串 | String
        timestamp: new Date(),  // Date对象 | Date object
        analytics: {  // 嵌套对象 | Nested object
          category: 'electronics',
          action: 'view'
        }
      });
    };

    return (
      <FlatList
        data={products}
        keyExtractor={item => item.id.toString()}
        renderItem={({ item }) => (
          <TouchableOpacity onPress={() => navigateToProduct(item)}>
            <Text>{item.name} - ${item.price}</Text>
          </TouchableOpacity>
        )}
      />
    );
  }

  // 3. 动态参数传递 | Dynamic parameter passing
  function SearchScreen({ navigation }) {
    const [searchQuery, setSearchQuery] = React.useState('');
    const [filters, setFilters] = React.useState({
      minPrice: 0,
      maxPrice: 1000,
      category: 'all'
    });

    const performSearch = () => {
      // 基于用户输入传递动态参数 | Pass dynamic parameters based on user input
      navigation.navigate('SearchResults', {
        query: searchQuery,
        filters: filters,
        searchTime: new Date().toISOString(),
        resultsPerPage: 20
      });
    };

    return (
      <View>
        <TextInput
          value={searchQuery}
          onChangeText={setSearchQuery}
          placeholder="Search..."
        />
        <Button title="Search" onPress={performSearch} />
      </View>
    );
  }

  // 4. 使用变量存储参数 | Using variables to store parameters
  function UserListScreen({ navigation }) {
    const navigateToUserProfile = (userId) => {
      // 先构建参数对象 | Build params object first
      const params = {
        userId: userId,
        showEditButton: true,
        referrer: 'user_list',
        timestamp: Date.now()
      };

      // 可以在传递前修改或验证参数 | Can modify or validate params before passing
      if (userId === 'admin') {
        params.adminMode = true;
      }

      navigation.navigate('UserProfile', params);
    };

    return (
      <View>
        <Button title="View User 1" onPress={() => navigateToUserProfile('user1')} />
        <Button title="View Admin" onPress={() => navigateToUserProfile('admin')} />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在上面的ProductListScreen示例中，如果我点击"Phone"产品，route.params.product.name会是什么？| In the ProductListScreen example above, if I click the "Phone" product, what will route.params.product.name be?
    **答案 | Answer:** 'Phone' | 整个product对象被传递，所以可以访问其所有属性 | The entire product object is passed, so all its properties are accessible

  - 如果我在SearchScreen中没有输入任何内容就点击搜索，searchQuery参数会是什么？| If I click search without entering anything in SearchScreen, what will the searchQuery parameter be?
    **答案 | Answer:** 空字符串 '' | Empty string '' - 因为useState的初始值是空字符串 | Because the initial value of useState is an empty string

  **常见误区检查 | Common Misconception Checks:**
  - 如果我修改传递前的原始对象，目标屏幕的参数也会改变吗？| If I modify the original object before passing, will the target screen's parameters change?
    **答案 | Answer:** 取决于修改时机 | Depends on timing - 如果在navigate调用前修改会影响，调用后修改不会影响（浅拷贝） | Modifications before navigate call will affect it, modifications after won't (shallow copy)

  - 我能传递函数作为参数吗？| Can I pass functions as parameters?
    **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - 函数不能序列化，会破坏深度链接和状态持久化，应使用回调或导航事件代替 | Functions can't be serialized, breaking deep linking and state persistence; use callbacks or navigation events instead

- **在目标屏幕中访问route.params | Accessing route.params in Target Screen**

  **概念定义 | Concept Definition:**
  route.params是目标屏幕组件接收的route对象的一个属性，包含了从源屏幕传递过来的所有参数。每个屏幕组件都会自动接收route和navigation两个props，route.params就是访问传递参数的主要途径。如果没有传递参数，route.params将是undefined。| route.params is a property of the route object received by the target screen component, containing all parameters passed from the source screen. Each screen component automatically receives route and navigation as props, and route.params is the primary way to access passed parameters. If no parameters are passed, route.params will be undefined.

  **核心特征 | Key Characteristics:**
  - route.params是只读的，不应直接修改（要更新参数使用setParams） | route.params is read-only and should not be modified directly (use setParams to update)
  - 如果没有传递参数，route.params是undefined而不是空对象 | If no parameters are passed, route.params is undefined, not an empty object
  - 可以使用解构语法方便地提取参数 | Can use destructuring syntax to conveniently extract parameters
  - 参数变化会触发组件重新渲染 | Parameter changes trigger component re-renders
  - 每次重新渲染都会获得最新的params值 | Each re-render gets the latest params value

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果没有传递任何参数，route.params会是空对象{}吗？| If no parameters are passed, will route.params be an empty object {}?
     **答案 | Answer:** 否 | No - 会是undefined，所以访问前应该检查或提供默认值 | It will be undefined, so you should check before accessing or provide default values

  2. 我可以直接修改route.params.itemId吗？| Can I directly modify route.params.itemId?
     **答案 | Answer:** 否 | No - route.params是只读的，应使用navigation.setParams()来更新 | route.params is read-only, use navigation.setParams() to update

  3. 如果参数对象很大，每次都解构会有性能问题吗？| If the params object is large, will destructuring every time cause performance issues?
     **答案 | Answer:** 通常不会 | Usually no - 解构是浅层操作，性能影响可忽略；但如果参数包含大量数据，应考虑优化数据传递方式 | Destructuring is a shallow operation with negligible performance impact; but if params contain massive data, consider optimizing data passing approach

  4. route对象除了params还有什么其他有用的属性？| What other useful properties does the route object have besides params?
     **答案 | Answer:** route.name（屏幕名称）和route.key（唯一标识符） | route.name (screen name) and route.key (unique identifier) - 还有route.path用于深度链接 | Also route.path for deep linking

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 访问和使用route.params的各种方式 | Various ways to access and use route.params

  // 1. 基础参数访问 | Basic parameter access
  function DetailsScreen({ route, navigation }) {
    // 方式1：直接访问 | Method 1: Direct access
    const itemId = route.params.itemId;
    const otherParam = route.params.otherParam;

    return (
      <View>
        <Text>Item ID: {itemId}</Text>
        <Text>Other: {otherParam}</Text>
      </View>
    );
  }

  // 2. 使用解构（推荐） | Using destructuring (recommended)
  function ProductDetailScreen({ route }) {
    // 解构提取需要的参数 | Destructure to extract needed parameters
    const { product, source, analytics } = route.params;

    return (
      <View>
        <Text>{product.name}</Text>
        <Text>Price: ${product.price}</Text>
        <Text>Source: {source}</Text>
        <Text>Category: {analytics.category}</Text>
      </View>
    );
  }

  // 3. 提供默认值处理可选参数 | Providing default values for optional parameters
  function UserProfileScreen({ route }) {
    // 使用默认值避免undefined | Use default values to avoid undefined
    const {
      userId,
      showEditButton = false,  // 默认值 | Default value
      adminMode = false,
      theme = 'light'  // 如果没有传递theme参数 | If theme param not passed
    } = route.params || {};  // 防止route.params是undefined | Prevent route.params being undefined

    // 或者使用逻辑或运算符 | Or use logical OR operator
    const referrer = route.params?.referrer || 'unknown';

    return (
      <View>
        <Text>User ID: {userId}</Text>
        {showEditButton && <Button title="Edit Profile" />}
        {adminMode && <Text style={styles.adminBadge}>Admin</Text>}
        <Text>Theme: {theme}</Text>
        <Text>Referred from: {referrer}</Text>
      </View>
    );
  }

  // 4. 使用TypeScript进行类型安全的参数访问 | Type-safe parameter access with TypeScript
  // 定义参数类型 | Define parameter types
  type DetailsScreenParams = {
    itemId: number;
    itemTitle?: string;  // 可选参数 | Optional parameter
    itemAuthor?: string;
  };

  function DetailsScreenTS({ route }: { route: { params: DetailsScreenParams } }) {
    const { itemId, itemTitle, itemAuthor } = route.params;

    return (
      <View>
        <Text>ID: {itemId}</Text>
        {/* TypeScript知道这些是可选的 | TypeScript knows these are optional */}
        {itemTitle && <Text>Title: {itemTitle}</Text>}
        {itemAuthor && <Text>Author: {itemAuthor}</Text>}
      </View>
    );
  }

  // 5. 条件渲染基于参数 | Conditional rendering based on parameters
  function SearchResultsScreen({ route }) {
    const { query, filters, resultsPerPage } = route.params;
    const [results, setResults] = React.useState([]);

    React.useEffect(() => {
      // 使用参数获取数据 | Use parameters to fetch data
      fetchResults(query, filters, resultsPerPage).then(setResults);
    }, [query, filters, resultsPerPage]);  // 参数变化时重新获取 | Re-fetch when parameters change

    return (
      <View>
        <Text>Searching for: {query}</Text>
        <Text>Price range: ${filters.minPrice} - ${filters.maxPrice}</Text>
        {results.length === 0 ? (
          <Text>No results found</Text>
        ) : (
          <FlatList data={results} renderItem={renderResult} />
        )}
      </View>
    );
  }

  // 6. 访问嵌套参数 | Accessing nested parameters
  function OrderDetailScreen({ route }) {
    const { order } = route.params;

    // 安全访问嵌套属性 | Safely access nested properties
    const customerName = order?.customer?.name || 'Guest';
    const shippingAddress = order?.shipping?.address || 'No address';

    // 或使用解构 | Or use destructuring
    const {
      id,
      total,
      items = [],  // 默认空数组 | Default empty array
      customer: { name, email } = {},  // 嵌套解构带默认值 | Nested destructuring with default
    } = order || {};

    return (
      <View>
        <Text>Order #{id}</Text>
        <Text>Total: ${total}</Text>
        <Text>Customer: {name} ({email})</Text>
        <Text>Items: {items.length}</Text>
      </View>
    );
  }

  // 7. 参数验证和错误处理 | Parameter validation and error handling
  function ArticleDetailScreen({ route, navigation }) {
    const { articleId } = route.params || {};

    // 参数验证 | Parameter validation
    React.useEffect(() => {
      if (!articleId) {
        // 如果缺少必需参数，显示错误或返回 | If required parameter is missing, show error or go back
        Alert.alert('Error', 'Article ID is required');
        navigation.goBack();
        return;
      }

      // 验证参数类型 | Validate parameter type
      if (typeof articleId !== 'number') {
        console.warn('articleId should be a number');
      }
    }, [articleId, navigation]);

    if (!articleId) {
      return <Text>Loading...</Text>;
    }

    return <Text>Article {articleId}</Text>;
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在UserProfileScreen示例中，如果没有传递任何参数，解构会抛出错误吗？| In the UserProfileScreen example, will destructuring throw an error if no parameters are passed?
    **答案 | Answer:** 不会 | No - 因为使用了 `route.params || {}`，确保总是解构一个对象 | Because `route.params || {}` is used, ensuring an object is always destructured

  - 在SearchResultsScreen中，如果query参数改变，会发生什么？| In SearchResultsScreen, what happens if the query parameter changes?
    **答案 | Answer:** useEffect会重新运行 | useEffect will re-run - 因为query在依赖数组中，会触发新的数据获取 | Because query is in the dependency array, triggering a new data fetch

  **常见误区检查 | Common Misconception Checks:**
  - route.params和props.params是一样的吗？| Are route.params and props.params the same?
    **答案 | Answer:** 不一样 | No - route.params是route对象的属性，而props.params不存在。正确的访问方式是props.route.params | route.params is a property of the route object, while props.params doesn't exist. The correct access is props.route.params

  - 如果我在组件内部修改从route.params解构出的变量，原始参数会改变吗？| If I modify variables destructured from route.params inside the component, will the original parameters change?
    **答案 | Answer:** 对于基本类型不会，对于对象可能会 | For primitive types no, for objects possibly yes - 解构创建了新变量绑定，但对象是引用类型。最佳实践是不修改参数，而是使用本地state | Destructuring creates new variable bindings, but objects are reference types. Best practice is not to modify parameters but use local state

### 3. 动态更新参数 | Dynamically Updating Parameters (1小时 | 1 hour)

- **使用navigation.setParams()更新参数 | Updating Parameters with navigation.setParams()**

  **概念定义 | Concept Definition:**
  navigation.setParams()是一个方法，用于更新当前屏幕的参数，而不需要重新导航或创建新的屏幕实例。它接受一个对象参数，该对象会与现有参数浅合并（shallow merge）。这对于需要根据用户交互或数据变化动态更新屏幕状态的场景非常有用，比如编辑表单、更新标题或改变配置选项。| navigation.setParams() is a method used to update the current screen's parameters without re-navigating or creating a new screen instance. It accepts an object parameter that will be shallow-merged with existing parameters. This is very useful for scenarios requiring dynamic screen state updates based on user interaction or data changes, such as editing forms, updating titles, or changing configuration options.

  **核心特征 | Key Characteristics:**
  - setParams会浅合并新参数和现有参数，不会完全替换 | setParams shallow-merges new parameters with existing ones, doesn't completely replace them
  - 参数更新会触发屏幕重新渲染 | Parameter updates trigger screen re-renders
  - 可以在屏幕自身内部调用来更新自己的参数 | Can be called within the screen itself to update its own parameters
  - 更新的参数会立即反映在route.params中 | Updated parameters are immediately reflected in route.params
  - 常用于动态更新header标题、按钮状态等UI元素 | Commonly used to dynamically update header titles, button states, and other UI elements

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. setParams会完全替换现有参数吗？| Does setParams completely replace existing parameters?
     **答案 | Answer:** 否 | No - 它执行浅合并，只更新提供的属性，保留其他现有参数 | It performs shallow merging, only updating provided properties while keeping other existing parameters

  2. 调用setParams会导航到新屏幕吗？| Does calling setParams navigate to a new screen?
     **答案 | Answer:** 否 | No - 它只更新当前屏幕的参数，不会创建新的屏幕实例或导航 | It only updates the current screen's parameters, doesn't create new screen instance or navigate

  3. 我可以从一个屏幕更新另一个屏幕的参数吗？| Can I update another screen's parameters from one screen?
     **答案 | Answer:** 可以但需要获取目标屏幕的navigation对象 | Yes but you need the target screen's navigation object - 通常使用navigation.navigate传参或navigation.setParams更新当前屏幕 | Usually use navigation.navigate to pass params or navigation.setParams to update current screen

  4. setParams更新参数后，route.params会立即更新吗？| After setParams updates parameters, will route.params update immediately?
     **答案 | Answer:** 是 | Yes - 更新是同步的，组件会重新渲染并获得新的route.params | The update is synchronous, the component will re-render with the new route.params

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 使用setParams动态更新参数的各种场景 | Various scenarios of dynamically updating parameters with setParams

  // 1. 基础用法：更新单个参数 | Basic usage: updating a single parameter
  function EditProfileScreen({ route, navigation }) {
    const { userName = 'Guest' } = route.params || {};
    const [inputName, setInputName] = React.useState(userName);

    const handleSave = () => {
      // 更新参数 | Update parameter
      navigation.setParams({ userName: inputName });
      Alert.alert('Saved', 'Profile updated');
    };

    return (
      <View>
        <Text>Current name: {userName}</Text>
        <TextInput
          value={inputName}
          onChangeText={setInputName}
          placeholder="Enter name"
        />
        <Button title="Save" onPress={handleSave} />
      </View>
    );
  }

  // 2. 动态更新header标题 | Dynamically updating header title
  function NoteEditorScreen({ route, navigation }) {
    const { noteTitle = 'Untitled' } = route.params || {};
    const [title, setTitle] = React.useState(noteTitle);
    const [content, setContent] = React.useState('');

    // 标题改变时更新参数和header | Update params and header when title changes
    React.useEffect(() => {
      navigation.setParams({ noteTitle: title || 'Untitled' });
    }, [title, navigation]);

    // 在screen options中使用参数 | Use parameter in screen options
    React.useLayoutEffect(() => {
      navigation.setOptions({
        title: route.params?.noteTitle || 'Untitled',
      });
    }, [navigation, route.params?.noteTitle]);

    return (
      <View>
        <TextInput
          value={title}
          onChangeText={setTitle}
          placeholder="Note title"
          style={styles.titleInput}
        />
        <TextInput
          value={content}
          onChangeText={setContent}
          placeholder="Note content"
          multiline
          style={styles.contentInput}
        />
      </View>
    );
  }

  // 3. 更新多个参数 | Updating multiple parameters
  function SettingsScreen({ route, navigation }) {
    const {
      theme = 'light',
      fontSize = 14,
      notifications = true
    } = route.params || {};

    const updateSettings = (updates) => {
      // 一次更新多个参数 | Update multiple parameters at once
      navigation.setParams(updates);
    };

    return (
      <View>
        <Text>Theme: {theme}</Text>
        <Button
          title="Toggle Theme"
          onPress={() => updateSettings({
            theme: theme === 'light' ? 'dark' : 'light'
          })}
        />

        <Text>Font Size: {fontSize}</Text>
        <Button
          title="Increase Font"
          onPress={() => updateSettings({ fontSize: fontSize + 2 })}
        />

        <Switch
          value={notifications}
          onValueChange={(value) => updateSettings({ notifications: value })}
        />
      </View>
    );
  }

  // 4. 浅合并行为演示 | Demonstrating shallow merge behavior
  function MergeDemo({ route, navigation }) {
    const initialParams = {
      user: { name: 'John', age: 30 },
      settings: { theme: 'light' },
      count: 0
    };

    React.useEffect(() => {
      // 设置初始参数（仅为演示） | Set initial parameters (for demonstration only)
      if (!route.params) {
        navigation.setParams(initialParams);
      }
    }, []);

    const updateCount = () => {
      // 只更新count，user和settings保持不变 | Only update count, user and settings remain unchanged
      navigation.setParams({
        count: (route.params?.count || 0) + 1
      });
    };

    const updateUser = () => {
      // ⚠️ 注意：这会完全替换user对象 | Warning: this completely replaces user object
      navigation.setParams({
        user: { name: 'Jane' }  // age属性会丢失！| age property will be lost!
      });
    };

    const updateUserCorrectly = () => {
      // ✅ 正确方式：保留现有属性 | Correct way: preserve existing properties
      navigation.setParams({
        user: {
          ...route.params.user,  // 展开现有user属性 | Spread existing user properties
          name: 'Jane'  // 只更新name | Only update name
        }
      });
    };

    return (
      <View>
        <Text>Count: {route.params?.count}</Text>
        <Text>User: {JSON.stringify(route.params?.user)}</Text>
        <Button title="Increment Count" onPress={updateCount} />
        <Button title="Update User (Wrong)" onPress={updateUser} />
        <Button title="Update User (Correct)" onPress={updateUserCorrectly} />
      </View>
    );
  }

  // 5. 在header按钮中使用setParams | Using setParams in header buttons
  function ArticleScreen({ route, navigation }) {
    const { articleId, isFavorite = false } = route.params || {};

    const toggleFavorite = React.useCallback(() => {
      const newFavoriteStatus = !isFavorite;
      navigation.setParams({ isFavorite: newFavoriteStatus });

      // 同时更新服务器 | Also update server
      updateFavoriteOnServer(articleId, newFavoriteStatus);
    }, [isFavorite, articleId, navigation]);

    // 在header中显示favorite按钮 | Display favorite button in header
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <TouchableOpacity onPress={toggleFavorite}>
            <Icon
              name={isFavorite ? 'heart' : 'heart-outline'}
              size={24}
              color={isFavorite ? 'red' : 'gray'}
            />
          </TouchableOpacity>
        ),
      });
    }, [navigation, isFavorite, toggleFavorite]);

    return (
      <View>
        <Text>Article {articleId}</Text>
        <Text>Favorite: {isFavorite ? 'Yes' : 'No'}</Text>
      </View>
    );
  }

  // 6. 条件更新和验证 | Conditional updates and validation
  function FormScreen({ route, navigation }) {
    const { formData = {} } = route.params || {};
    const [email, setEmail] = React.useState(formData.email || '');
    const [isValid, setIsValid] = React.useState(false);

    React.useEffect(() => {
      // 验证email | Validate email
      const valid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
      setIsValid(valid);

      // 只有有效时才更新参数 | Only update parameter when valid
      if (valid) {
        navigation.setParams({
          formData: {
            ...formData,
            email: email,
            isValid: true
          }
        });
      }
    }, [email, navigation]);

    return (
      <View>
        <TextInput
          value={email}
          onChangeText={setEmail}
          placeholder="Email"
          keyboardType="email-address"
        />
        <Text style={{ color: isValid ? 'green' : 'red' }}>
          {isValid ? '✓ Valid' : '✗ Invalid'}
        </Text>
      </View>
    );
  }

  // 7. 使用setParams进行状态同步 | Using setParams for state synchronization
  function FilterScreen({ route, navigation }) {
    const initialFilters = route.params?.filters || {
      category: 'all',
      minPrice: 0,
      maxPrice: 1000,
      sortBy: 'name'
    };

    const [filters, setFilters] = React.useState(initialFilters);

    // 当filters改变时，同步到params | Synchronize to params when filters change
    const updateFilter = (key, value) => {
      const newFilters = { ...filters, [key]: value };
      setFilters(newFilters);
      navigation.setParams({ filters: newFilters });
    };

    // 应用过滤器并返回 | Apply filters and go back
    const applyFilters = () => {
      navigation.navigate('ProductList', {
        filters: filters,
        filtersApplied: true
      });
    };

    return (
      <View>
        <Picker
          selectedValue={filters.category}
          onValueChange={(value) => updateFilter('category', value)}
        >
          <Picker.Item label="All" value="all" />
          <Picker.Item label="Electronics" value="electronics" />
          <Picker.Item label="Books" value="books" />
        </Picker>

        <Slider
          value={filters.minPrice}
          onValueChange={(value) => updateFilter('minPrice', value)}
          minimumValue={0}
          maximumValue={500}
        />
        <Text>Min Price: ${filters.minPrice}</Text>

        <Button title="Apply Filters" onPress={applyFilters} />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在MergeDemo示例中，如果我调用updateUser()，user.age属性会发生什么？| In the MergeDemo example, what happens to the user.age property if I call updateUser()?
    **答案 | Answer:** 会丢失 | Will be lost - 因为setParams执行浅合并，整个user对象被新对象替换，新对象只有name属性 | Because setParams performs shallow merging, the entire user object is replaced by the new object which only has the name property

  - 在ArticleScreen中，当toggleFavorite被调用时，header图标会立即更新吗？| In ArticleScreen, when toggleFavorite is called, will the header icon update immediately?
    **答案 | Answer:** 是 | Yes - setParams更新isFavorite参数，触发重新渲染，useLayoutEffect运行并更新header | setParams updates the isFavorite parameter, triggering a re-render, useLayoutEffect runs and updates the header

  **常见误区检查 | Common Misconception Checks:**
  - setParams和setState是同一回事吗？| Are setParams and setState the same thing?
    **答案 | Answer:** 不是 | No - setParams更新导航参数（影响route.params），setState更新组件本地状态。参数是导航状态的一部分，跨屏幕访问；state是组件内部的 | setParams updates navigation parameters (affecting route.params), setState updates component local state. Parameters are part of navigation state, accessible across screens; state is internal to components

  - 我需要在setParams之后手动调用forceUpdate吗？| Do I need to manually call forceUpdate after setParams?
    **答案 | Answer:** 不需要 | No - setParams会自动触发组件重新渲染，route.params会更新为新值 | setParams automatically triggers component re-render, route.params will update to new values

- **参数更新的常见使用场景 | Common Use Cases for Parameter Updates**

  **概念定义 | Concept Definition:**
  参数更新在实际应用中有许多实用场景，从简单的状态跟踪到复杂的表单管理。理解这些常见模式可以帮助你更有效地设计应用架构。主要场景包括：动态header更新、表单数据临时存储、用户偏好设置、搜索和过滤状态管理、以及编辑模式切换等。每个场景都有特定的最佳实践和需要注意的陷阱。| Parameter updates have many practical use cases in real applications, from simple state tracking to complex form management. Understanding these common patterns helps you design application architecture more effectively. Main scenarios include: dynamic header updates, temporary form data storage, user preference settings, search and filter state management, and edit mode toggling. Each scenario has specific best practices and pitfalls to watch out for.

  **解决的问题 | Problems It Solves:**
  - 在屏幕间传递临时状态，而无需全局状态管理 | Pass temporary state between screens without global state management
  - 动态更新导航UI（header标题、按钮等）基于用户交互 | Dynamically update navigation UI (header title, buttons) based on user interaction
  - 保持编辑表单数据，即使用户离开后返回 | Maintain edit form data even when user navigates away and returns
  - 管理复杂的过滤和搜索条件 | Manage complex filter and search criteria
  - 实现屏幕间的回调通信模式 | Implement callback communication pattern between screens

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应该用参数还是全局状态管理来存储用户的登录信息？| Should I use parameters or global state management to store user login information?
     **答案 | Answer:** 全局状态管理 | Global state management - 登录信息需要在整个应用中访问，应使用Context、Redux等全局方案，参数适合临时、特定屏幕的数据 | Login information needs to be accessed throughout the app, should use global solutions like Context or Redux; parameters are for temporary, screen-specific data

  2. 使用参数存储大量数据（如完整的产品列表）是好的做法吗？| Is it good practice to store large amounts of data (like a complete product list) in parameters?
     **答案 | Answer:** 不是 | No - 大数据应存储在全局状态或本地数据库中，参数中只传递标识符（如IDs）然后在目标屏幕获取数据 | Large data should be stored in global state or local database; only pass identifiers (like IDs) in parameters and fetch data in target screen

  3. 参数更新会在应用重启后保留吗？| Will parameter updates persist after app restart?
     **答案 | Answer:** 否 | No - 参数只在应用运行时的导航状态中存在，重启后丢失。需持久化使用AsyncStorage或数据库 | Parameters only exist in navigation state during app runtime, lost after restart. For persistence, use AsyncStorage or database

  4. 什么时候应该用setParams而不是简单地重新导航？| When should I use setParams instead of simply re-navigating?
     **答案 | Answer:** 当需要更新当前屏幕状态而不创建新实例时 | When you need to update current screen state without creating a new instance - setParams保持在同一屏幕，重新导航会创建新实例或跳转到现有实例 | setParams stays on the same screen, re-navigating creates a new instance or jumps to existing instance

  **实际应用示例 | Real-world Application Examples:**
  ```javascript
  // 常见参数更新场景的实际应用 | Real-world applications of common parameter update scenarios

  // 场景1：动态更新搜索建议header | Scenario 1: Dynamically updating search suggestion header
  function SearchScreen({ route, navigation }) {
    const { query = '' } = route.params || {};
    const [searchText, setSearchText] = React.useState(query);
    const [results, setResults] = React.useState([]);

    // 实时更新参数以便其他屏幕访问搜索词 | Update parameter in real-time for other screens to access search term
    React.useEffect(() => {
      const timeoutId = setTimeout(() => {
        if (searchText !== query) {
          navigation.setParams({ query: searchText });
        }
      }, 300);  // 防抖 | Debounce

      return () => clearTimeout(timeoutId);
    }, [searchText, query, navigation]);

    // 更新header显示结果数量 | Update header to show result count
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerTitle: () => (
          <View>
            <Text style={styles.headerTitle}>Search</Text>
            <Text style={styles.headerSubtitle}>
              {results.length} results
            </Text>
          </View>
        ),
      });
    }, [navigation, results.length]);

    return (
      <View>
        <SearchBar value={searchText} onChangeText={setSearchText} />
        <FlatList data={results} renderItem={renderResult} />
      </View>
    );
  }

  // 场景2：多步骤表单数据收集 | Scenario 2: Multi-step form data collection
  function Step1Screen({ route, navigation }) {
    const { formData = {} } = route.params || {};
    const [email, setEmail] = React.useState(formData.email || '');
    const [name, setName] = React.useState(formData.name || '');

    const goToNextStep = () => {
      // 保存当前步骤数据到参数 | Save current step data to parameters
      const updatedFormData = {
        ...formData,
        email,
        name,
        step: 1
      };

      navigation.navigate('Step2', {
        formData: updatedFormData
      });
    };

    return (
      <View>
        <Text>Step 1: Personal Information</Text>
        <TextInput
          value={name}
          onChangeText={setName}
          placeholder="Name"
        />
        <TextInput
          value={email}
          onChangeText={setEmail}
          placeholder="Email"
        />
        <Button title="Next" onPress={goToNextStep} />
      </View>
    );
  }

  function Step2Screen({ route, navigation }) {
    const { formData = {} } = route.params || {};
    const [address, setAddress] = React.useState(formData.address || '');
    const [phone, setPhone] = React.useState(formData.phone || '');

    const goBack = () => {
      // 返回时保存当前输入 | Save current input when going back
      navigation.navigate('Step1', {
        formData: {
          ...formData,
          address,
          phone
        }
      });
    };

    const submitForm = () => {
      const completeFormData = {
        ...formData,
        address,
        phone
      };

      // 提交完整表单 | Submit complete form
      submitToServer(completeFormData);
      navigation.navigate('Success');
    };

    return (
      <View>
        <Text>Step 2: Contact Information</Text>
        <Text>Name: {formData.name}</Text>
        <Text>Email: {formData.email}</Text>
        <TextInput
          value={address}
          onChangeText={setAddress}
          placeholder="Address"
        />
        <TextInput
          value={phone}
          onChangeText={setPhone}
          placeholder="Phone"
        />
        <Button title="Back" onPress={goBack} />
        <Button title="Submit" onPress={submitForm} />
      </View>
    );
  }

  // 场景3：编辑模式切换 | Scenario 3: Edit mode toggling
  function ProfileScreen({ route, navigation }) {
    const { userId, isEditMode = false } = route.params || {};
    const [profile, setProfile] = React.useState(null);
    const [editedProfile, setEditedProfile] = React.useState(null);

    React.useEffect(() => {
      fetchUserProfile(userId).then(data => {
        setProfile(data);
        setEditedProfile(data);
      });
    }, [userId]);

    const toggleEditMode = () => {
      if (isEditMode) {
        // 退出编辑模式，保存更改 | Exit edit mode, save changes
        saveProfile(editedProfile);
        setProfile(editedProfile);
      }
      navigation.setParams({ isEditMode: !isEditMode });
    };

    const cancelEdit = () => {
      // 取消编辑，恢复原始数据 | Cancel edit, restore original data
      setEditedProfile(profile);
      navigation.setParams({ isEditMode: false });
    };

    // 根据编辑模式更新header按钮 | Update header buttons based on edit mode
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <View style={{ flexDirection: 'row' }}>
            {isEditMode && (
              <Button title="Cancel" onPress={cancelEdit} />
            )}
            <Button
              title={isEditMode ? 'Save' : 'Edit'}
              onPress={toggleEditMode}
            />
          </View>
        ),
      });
    }, [navigation, isEditMode]);

    if (!profile) return <ActivityIndicator />;

    return (
      <View>
        {isEditMode ? (
          <>
            <TextInput
              value={editedProfile.name}
              onChangeText={(text) =>
                setEditedProfile({ ...editedProfile, name: text })
              }
            />
            <TextInput
              value={editedProfile.bio}
              onChangeText={(text) =>
                setEditedProfile({ ...editedProfile, bio: text })
              }
              multiline
            />
          </>
        ) : (
          <>
            <Text style={styles.name}>{profile.name}</Text>
            <Text style={styles.bio}>{profile.bio}</Text>
          </>
        )}
      </View>
    );
  }

  // 场景4：过滤器状态管理（复杂示例） | Scenario 4: Filter state management (complex example)
  function ProductListScreen({ route, navigation }) {
    const {
      filters = {
        category: 'all',
        priceRange: [0, 1000],
        brands: [],
        rating: 0,
        inStock: false
      },
      sortBy = 'name'
    } = route.params || {};

    const [products, setProducts] = React.useState([]);
    const [appliedFilters, setAppliedFilters] = React.useState(filters);

    // 获取产品时使用过滤器 | Use filters when fetching products
    React.useEffect(() => {
      fetchProducts(appliedFilters, sortBy).then(setProducts);
    }, [appliedFilters, sortBy]);

    const openFilterScreen = () => {
      navigation.navigate('Filters', {
        currentFilters: appliedFilters,
        onApplyFilters: (newFilters) => {
          // 回调模式：接收新过滤器 | Callback pattern: receive new filters
          setAppliedFilters(newFilters);
          navigation.setParams({ filters: newFilters });
        }
      });
    };

    // 更新header显示激活的过滤器数量 | Update header to show count of active filters
    const activeFilterCount = React.useMemo(() => {
      let count = 0;
      if (filters.category !== 'all') count++;
      if (filters.brands.length > 0) count++;
      if (filters.rating > 0) count++;
      if (filters.inStock) count++;
      return count;
    }, [filters]);

    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerRight: () => (
          <TouchableOpacity onPress={openFilterScreen}>
            <View style={styles.filterButton}>
              <Icon name="filter" size={24} />
              {activeFilterCount > 0 && (
                <View style={styles.badge}>
                  <Text style={styles.badgeText}>{activeFilterCount}</Text>
                </View>
              )}
            </View>
          </TouchableOpacity>
        ),
      });
    }, [navigation, activeFilterCount]);

    return (
      <View>
        <FlatList
          data={products}
          renderItem={({ item }) => <ProductItem product={item} />}
          ListEmptyComponent={<Text>No products found</Text>}
        />
      </View>
    );
  }

  // 场景5：临时草稿保存 | Scenario 5: Temporary draft saving
  function ComposeMessageScreen({ route, navigation }) {
    const { draft = { to: '', subject: '', body: '' } } = route.params || {};
    const [message, setMessage] = React.useState(draft);

    // 自动保存草稿 | Auto-save draft
    React.useEffect(() => {
      const saveInterval = setInterval(() => {
        navigation.setParams({ draft: message });
        console.log('Draft saved');
      }, 5000);  // 每5秒保存 | Save every 5 seconds

      return () => clearInterval(saveInterval);
    }, [message, navigation]);

    const sendMessage = async () => {
      await sendMessageToServer(message);
      // 清除草稿 | Clear draft
      navigation.setParams({ draft: null });
      navigation.goBack();
    };

    // 离开前保存草稿 | Save draft before leaving
    React.useEffect(() => {
      return () => {
        // 清理函数：组件卸载时保存 | Cleanup: save when component unmounts
        if (message.body.length > 0) {
          saveDraftToLocalStorage(message);
        }
      };
    }, [message]);

    return (
      <View>
        <TextInput
          value={message.to}
          onChangeText={(text) => setMessage({ ...message, to: text })}
          placeholder="To"
        />
        <TextInput
          value={message.subject}
          onChangeText={(text) => setMessage({ ...message, subject: text })}
          placeholder="Subject"
        />
        <TextInput
          value={message.body}
          onChangeText={(text) => setMessage({ ...message, body: text })}
          placeholder="Message"
          multiline
          style={{ height: 200 }}
        />
        <Button title="Send" onPress={sendMessage} />
      </View>
    );
  }

  // 场景6：深度链接参数预填充 | Scenario 6: Deep link parameter pre-population
  function ShareArticleScreen({ route, navigation }) {
    const {
      articleUrl,
      prefilledText = '',
      hashtags = []
    } = route.params || {};

    const [shareText, setShareText] = React.useState(prefilledText);
    const [selectedPlatforms, setSelectedPlatforms] = React.useState([]);

    // 如果通过深度链接打开，更新参数以跟踪来源 | If opened via deep link, update params to track source
    React.useEffect(() => {
      if (route.params?.fromDeepLink) {
        navigation.setParams({
          ...route.params,
          openedAt: new Date().toISOString(),
          fromDeepLink: true
        });

        // 记录分析 | Log analytics
        logEvent('article_share_opened', {
          source: 'deep_link',
          articleUrl
        });
      }
    }, []);

    return (
      <View>
        <Text>Share: {articleUrl}</Text>
        <TextInput
          value={shareText}
          onChangeText={setShareText}
          placeholder="Add your comment..."
          multiline
        />
        <View>
          {hashtags.map(tag => (
            <Chip key={tag} label={`#${tag}`} />
          ))}
        </View>
        <Button title="Share" onPress={() => shareArticle(articleUrl, shareText)} />
      </View>
    );
  }
  ```

  **综合应用检查 | Comprehensive Application Check:**
  - 在多步骤表单示例中，如果用户在Step2点击返回，之前在Step1输入的数据会丢失吗？| In the multi-step form example, if the user clicks back on Step2, will the data entered in Step1 be lost?
    **答案 | Answer:** 不会 | No - goBack函数通过navigate传递formData回Step1，保留了两个步骤的数据 | The goBack function passes formData back to Step1 via navigate, preserving data from both steps

  - 为什么ComposeMessageScreen使用定时器自动保存，而不是在每次文本改变时保存？| Why does ComposeMessageScreen use a timer for auto-save instead of saving on every text change?
    **答案 | Answer:** 性能优化 | Performance optimization - 每次按键都调用setParams会导致频繁重新渲染和不必要的开销，定时保存减少了调用频率 | Calling setParams on every keystroke causes frequent re-renders and unnecessary overhead; timed saving reduces call frequency

  - 在ProductListScreen中，如果同时更新filters和sortBy，应该调用setParams几次？| In ProductListScreen, if updating both filters and sortBy simultaneously, how many times should setParams be called?
    **答案 | Answer:** 一次 | Once - 应该在一个setParams调用中传递两个参数：`navigation.setParams({ filters: newFilters, sortBy: newSort })` | Should pass both parameters in one setParams call: `navigation.setParams({ filters: newFilters, sortBy: newSort })`
