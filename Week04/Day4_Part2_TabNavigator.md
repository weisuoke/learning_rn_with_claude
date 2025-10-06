# React Native入门 - 第28天：底部标签导航 (第二部分) | React Native Introduction - Day 28: Bottom Tab Navigator (Part 2)

## 详细内容（续）| Detailed Content (Continued)

### 4. 嵌套导航器：标签内嵌入堆栈导航 | Nested Navigators: Stack Navigation Inside Tabs (1.5小时 | 1.5 hours)

- **嵌套导航器的核心概念 | Core Concept of Nested Navigators**

  **概念定义 | Concept Definition:**
  嵌套导航器（Nested Navigators）是指在一个导航器内部使用另一个导航器的模式。在底部标签导航的场景中，最常见的嵌套模式是在每个标签内嵌入一个堆栈导航器。这样做的原因是：标签导航器提供应用的顶层结构（如首页、搜索、个人资料），而每个标签内部可能需要层级导航（如从列表到详情页）。嵌套导航器使得每个标签可以拥有自己独立的导航历史栈。

  Nested Navigators is a pattern where you use one navigator inside another navigator. In bottom tab navigation scenarios, the most common nesting pattern is embedding a Stack Navigator inside each tab. The reason for this is: the Tab Navigator provides the top-level structure of the app (like Home, Search, Profile), while each tab internally may need hierarchical navigation (like from a list to a detail screen). Nested navigators allow each tab to have its own independent navigation history stack.

  **核心特征 | Key Characteristics:**
  - 独立导航栈：每个标签有自己的导航历史，在Tab A中导航不会影响Tab B的历史 | Independent navigation stacks: Each tab has its own navigation history; navigating in Tab A doesn't affect Tab B's history
  - 标签栏持久性：即使在堆栈导航的深层页面，标签栏仍然可见 | Tab bar persistence: Even on deep pages in stack navigation, the tab bar remains visible
  - 常见模式：Tab Navigator作为根导航器，Stack Navigator作为每个标签的子导航器 | Common pattern: Tab Navigator as root navigator, Stack Navigator as child navigator for each tab
  - 后退行为：在标签内的堆栈中按后退按钮会返回该标签的上一个屏幕，而不是切换标签 | Back behavior: Pressing back in a tab's stack returns to the previous screen in that tab, not switching tabs

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果在"Home"标签的堆栈中导航到详情页，然后切换到"Search"标签，再切回"Home"标签，你会看到什么？| If you navigate to a detail screen in the "Home" tab's stack, then switch to "Search" tab, then switch back to "Home" tab, what will you see?
     **答案 | Answer:** 仍然是详情页 | Still the detail screen - 每个标签的导航状态是独立保存的 | Each tab's navigation state is preserved independently

  2. 在嵌套导航器中，标签栏会在堆栈导航的详情页中消失吗？| In nested navigators, does the tab bar disappear on detail screens in stack navigation?
     **答案 | Answer:** 默认不会 | Not by default - 标签栏会保持可见，除非你明确配置隐藏它 | The tab bar remains visible unless you explicitly configure to hide it

  3. 为什么要在标签内嵌套堆栈导航器，而不是直接使用标签导航器？| Why nest Stack Navigators inside tabs instead of just using a Tab Navigator?
     **答案 | Answer:** 为了支持层级导航 | To support hierarchical navigation - 标签导航是平行的，不能处理"从列表到详情"这样的层级关系，需要堆栈导航器 | Tab navigation is parallel and can't handle hierarchical relationships like "from list to detail"; Stack Navigator is needed

  4. 如果用户在"Home"标签的详情页按设备的后退按钮，会发生什么？| If a user presses the device back button on a detail screen in the "Home" tab, what happens?
     **答案 | Answer:** 返回Home标签的上一个屏幕（如列表页） | Returns to the previous screen in the Home tab (like the list screen) - 不会切换到其他标签 | Won't switch to another tab

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { Ionicons } from '@expo/vector-icons';
  import { View, Text, Button, FlatList, TouchableOpacity, StyleSheet } from 'react-native';

  const Tab = createBottomTabNavigator();
  const HomeStack = createNativeStackNavigator(); // Home标签的堆栈 | Stack for Home tab
  const SearchStack = createNativeStackNavigator(); // Search标签的堆栈 | Stack for Search tab

  // ===== Home标签的屏幕组件 | Screen components for Home tab =====
  function HomeListScreen({ navigation }) {
    // 模拟文章列表数据 | Mock article list data
    const articles = [
      { id: '1', title: 'React Native基础 | React Native Basics' },
      { id: '2', title: '导航深入解析 | Deep Dive into Navigation' },
      { id: '3', title: '性能优化技巧 | Performance Optimization Tips' },
    ];

    return (
      <View style={styles.container}>
        <Text style={styles.header}>文章列表 | Article List</Text>
        <FlatList
          data={articles}
          keyExtractor={item => item.id}
          renderItem={({ item }) => (
            <TouchableOpacity
              style={styles.listItem}
              onPress={() => navigation.navigate('HomeDetail', { article: item })}
            >
              <Text>{item.title}</Text>
              <Ionicons name="chevron-forward" size={20} color="#999" />
            </TouchableOpacity>
          )}
        />
      </View>
    );
  }

  function HomeDetailScreen({ route, navigation }) {
    const { article } = route.params;
    return (
      <View style={styles.container}>
        <Text style={styles.header}>文章详情 | Article Detail</Text>
        <Text style={styles.detailText}>{article.title}</Text>
        <Text style={styles.content}>
          这是文章的详细内容... | This is the detailed content of the article...
        </Text>
        <Button title="返回列表 | Go Back" onPress={() => navigation.goBack()} />
      </View>
    );
  }

  // Home标签的堆栈导航器 | Stack Navigator for Home tab
  function HomeStackNavigator() {
    return (
      <HomeStack.Navigator>
        <HomeStack.Screen
          name="HomeList"
          component={HomeListScreen}
          options={{ title: '首页 | Home' }}
        />
        <HomeStack.Screen
          name="HomeDetail"
          component={HomeDetailScreen}
          options={{ title: '详情 | Detail' }}
        />
      </HomeStack.Navigator>
    );
  }

  // ===== Search标签的屏幕组件 | Screen components for Search tab =====
  function SearchListScreen({ navigation }) {
    const searchResults = [
      { id: '1', title: '搜索结果1 | Search Result 1' },
      { id: '2', title: '搜索结果2 | Search Result 2' },
    ];

    return (
      <View style={styles.container}>
        <Text style={styles.header}>搜索结果 | Search Results</Text>
        <FlatList
          data={searchResults}
          keyExtractor={item => item.id}
          renderItem={({ item }) => (
            <TouchableOpacity
              style={styles.listItem}
              onPress={() => navigation.navigate('SearchDetail', { result: item })}
            >
              <Text>{item.title}</Text>
              <Ionicons name="chevron-forward" size={20} color="#999" />
            </TouchableOpacity>
          )}
        />
      </View>
    );
  }

  function SearchDetailScreen({ route, navigation }) {
    const { result } = route.params;
    return (
      <View style={styles.container}>
        <Text style={styles.header}>搜索详情 | Search Detail</Text>
        <Text style={styles.detailText}>{result.title}</Text>
        <Button title="返回 | Go Back" onPress={() => navigation.goBack()} />
      </View>
    );
  }

  // Search标签的堆栈导航器 | Stack Navigator for Search tab
  function SearchStackNavigator() {
    return (
      <SearchStack.Navigator>
        <SearchStack.Screen
          name="SearchList"
          component={SearchListScreen}
          options={{ title: '搜索 | Search' }}
        />
        <SearchStack.Screen
          name="SearchDetail"
          component={SearchDetailScreen}
          options={{ title: '详情 | Detail' }}
        />
      </SearchStack.Navigator>
    );
  }

  // ===== Profile标签（无嵌套堆栈）| Profile tab (no nested stack) =====
  function ProfileScreen() {
    return (
      <View style={styles.container}>
        <Text style={styles.header}>个人资料 | Profile</Text>
        <Text>用户信息 | User Information</Text>
      </View>
    );
  }

  // ===== 主App：Tab Navigator包含Stack Navigators | Main App: Tab Navigator containing Stack Navigators =====
  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator
          screenOptions={{
            tabBarActiveTintColor: '#e91e63',
            tabBarInactiveTintColor: 'gray',
            // 隐藏Tab Navigator的头部，使用嵌套Stack的头部 | Hide Tab Navigator header, use nested Stack's header
            headerShown: false,
          }}
        >
          {/* Home标签：包含一个Stack Navigator | Home tab: contains a Stack Navigator */}
          <Tab.Screen
            name="HomeTab"
            component={HomeStackNavigator} // 注意这里传入的是Stack Navigator组件 | Note: passing Stack Navigator component
            options={{
              tabBarLabel: '首页',
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="home" size={size} color={color} />
              ),
            }}
          />

          {/* Search标签：包含一个Stack Navigator | Search tab: contains a Stack Navigator */}
          <Tab.Screen
            name="SearchTab"
            component={SearchStackNavigator}
            options={{
              tabBarLabel: '搜索',
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="search" size={size} color={color} />
              ),
            }}
          />

          {/* Profile标签：单个屏幕，无嵌套导航 | Profile tab: single screen, no nested navigation */}
          <Tab.Screen
            name="ProfileTab"
            component={ProfileScreen}
            options={{
              tabBarLabel: '我的',
              tabBarIcon: ({ color, size }) => (
                <Ionicons name="person" size={size} color={color} />
              ),
              // Profile标签需要显示头部 | Profile tab needs to show header
              headerShown: true,
              title: '个人资料 | Profile',
            }}
          />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      padding: 20,
      backgroundColor: '#fff',
    },
    header: {
      fontSize: 24,
      fontWeight: 'bold',
      marginBottom: 20,
    },
    listItem: {
      flexDirection: 'row',
      justifyContent: 'space-between',
      alignItems: 'center',
      padding: 15,
      borderBottomWidth: 1,
      borderBottomColor: '#eee',
    },
    detailText: {
      fontSize: 18,
      fontWeight: '600',
      marginBottom: 10,
    },
    content: {
      fontSize: 14,
      color: '#666',
      marginBottom: 20,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在这个示例中，有几个Stack Navigator？| How many Stack Navigators are in this example?
    **答案 | Answer:** 2个 | 2 - HomeStack和SearchStack，Profile标签没有嵌套堆栈 | HomeStack and SearchStack; Profile tab has no nested stack

  - 如果用户在HomeDetailScreen（文章详情页）中，然后点击"Search"标签，再点击回"Home"标签，会看到什么屏幕？| If a user is on HomeDetailScreen (article detail) and taps the "Search" tab, then taps back to "Home" tab, what screen will they see?
    **答案 | Answer:** 仍然是HomeDetailScreen | Still HomeDetailScreen - 每个标签的导航状态是独立保存的 | Each tab's navigation state is preserved independently

  - 为什么Tab.Navigator的screenOptions中设置了headerShown: false？| Why is headerShown: false set in Tab.Navigator's screenOptions?
    **答案 | Answer:** 避免重复的头部 | To avoid duplicate headers - 嵌套的Stack Navigator有自己的头部，Tab Navigator的头部会导致双重头部 | Nested Stack Navigators have their own headers; Tab Navigator's header would cause double headers

  **常见误区检查 | Common Misconception Checks:**
  - 能否在Tab.Screen的component属性中直接传入HomeListScreen，然后在HomeListScreen中导航到HomeDetailScreen？| Can you directly pass HomeListScreen to Tab.Screen's component prop and then navigate from HomeListScreen to HomeDetailScreen?
    **答案 | Answer:** 否 | No - 没有嵌套Stack Navigator，HomeListScreen无法导航到另一个屏幕。必须先创建Stack Navigator才能实现层级导航 | Without a nested Stack Navigator, HomeListScreen can't navigate to another screen. You must create a Stack Navigator first to implement hierarchical navigation

  - 所有标签都必须嵌套Stack Navigator吗？| Must all tabs have a nested Stack Navigator?
    **答案 | Answer:** 否 | No - 只有需要层级导航的标签才需要。如Profile标签通常是单个屏幕，不需要嵌套堆栈 | Only tabs that need hierarchical navigation require it. Tabs like Profile are usually single screens and don't need a nested stack

- **导航到嵌套屏幕的高级技巧 | Advanced Techniques for Navigating to Nested Screens**

  **概念定义 | Concept Definition:**
  在嵌套导航器的场景中，有时需要从一个标签直接导航到另一个标签的深层屏幕（例如，从Home标签导航到Search标签的详情页）。React Navigation使用嵌套导航参数语法来支持这种跨标签的导航。语法是：`navigation.navigate('TabName', { screen: 'ScreenName', params: {...} })`。这允许你同时指定目标标签和该标签内的具体屏幕。

  In nested navigator scenarios, sometimes you need to navigate from one tab directly to a deep screen in another tab (e.g., from Home tab to Search tab's detail screen). React Navigation supports this cross-tab navigation using nested navigation parameter syntax: `navigation.navigate('TabName', { screen: 'ScreenName', params: {...} })`. This allows you to specify both the target tab and the specific screen within that tab.

  **核心特征 | Key Characteristics:**
  - 嵌套参数语法：使用screen和params属性来指定嵌套屏幕 | Nested parameter syntax: Use screen and params properties to specify nested screens
  - 跨导航器导航：可以从一个导航器导航到另一个导航器内的屏幕 | Cross-navigator navigation: Can navigate from one navigator to a screen inside another navigator
  - 标签自动切换：导航到嵌套屏幕会自动切换到对应的标签 | Automatic tab switching: Navigating to a nested screen automatically switches to the corresponding tab
  - 多层嵌套支持：语法可以扩展到更深层的嵌套（虽然不推荐嵌套太深） | Multi-level nesting support: Syntax can extend to deeper nesting (though deep nesting is not recommended)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 要从Home标签导航到Search标签的SearchDetail屏幕，正确的语法是什么？| What's the correct syntax to navigate from Home tab to SearchDetail screen in Search tab?
     **答案 | Answer:** navigation.navigate('SearchTab', { screen: 'SearchDetail', params: {...} }) - 第一个参数是标签名，screen指定标签内的屏幕 | First parameter is tab name, screen specifies the screen within the tab

  2. 使用嵌套导航语法导航到另一个标签的屏幕时，标签会自动切换吗？| When using nested navigation syntax to navigate to a screen in another tab, does the tab automatically switch?
     **答案 | Answer:** 是 | Yes - React Navigation会自动切换到目标标签并显示指定的屏幕 | React Navigation automatically switches to the target tab and displays the specified screen

  3. 如果省略params属性，只指定screen，代码会报错吗？| If you omit the params property and only specify screen, will the code error?
     **答案 | Answer:** 否 | No - params是可选的，如果目标屏幕不需要参数可以省略 | params is optional; it can be omitted if the target screen doesn't need parameters

  4. 嵌套导航语法中的screen值应该是什么？| What should the screen value be in nested navigation syntax?
     **答案 | Answer:** 目标Stack Navigator中定义的屏幕名称 | The screen name defined in the target Stack Navigator - 例如'SearchDetail'，而不是组件名SearchDetailScreen | For example, 'SearchDetail', not the component name SearchDetailScreen

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  import { NavigationContainer } from '@react-navigation/native';
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';
  import { View, Text, Button, StyleSheet } from 'react-native';

  const Tab = createBottomTabNavigator();
  const HomeStack = createNativeStackNavigator();
  const SearchStack = createNativeStackNavigator();

  // Home标签的列表屏幕 | Home tab list screen
  function HomeListScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>首页列表 | Home List</Text>

        {/* 导航到同一标签内的详情页 | Navigate to detail within same tab */}
        <Button
          title="查看Home详情 | View Home Detail"
          onPress={() => navigation.navigate('HomeDetail', { id: 1 })}
        />

        {/* 跨标签导航：直接导航到Search标签的详情页 | Cross-tab navigation: navigate to Search tab's detail */}
        <Button
          title="跨标签：查看Search详情 | Cross-tab: View Search Detail"
          onPress={() =>
            navigation.navigate('SearchTab', {
              screen: 'SearchDetail',
              params: { query: 'React Native', id: 42 },
            })
          }
        />

        {/* 导航到Search标签的列表页（不指定screen，默认到第一个屏幕）| Navigate to Search tab's list (no screen specified, defaults to first screen) */}
        <Button
          title="切换到Search标签 | Switch to Search Tab"
          onPress={() => navigation.navigate('SearchTab')}
        />
      </View>
    );
  }

  function HomeDetailScreen({ route, navigation }) {
    const { id } = route.params;
    return (
      <View style={styles.container}>
        <Text style={styles.title}>Home详情 | Home Detail</Text>
        <Text>ID: {id}</Text>
        <Button title="返回 | Go Back" onPress={() => navigation.goBack()} />
      </View>
    );
  }

  function HomeStackNavigator() {
    return (
      <HomeStack.Navigator>
        <HomeStack.Screen
          name="HomeList"
          component={HomeListScreen}
          options={{ title: '首页 | Home' }}
        />
        <HomeStack.Screen
          name="HomeDetail"
          component={HomeDetailScreen}
          options={{ title: 'Home详情 | Home Detail' }}
        />
      </HomeStack.Navigator>
    );
  }

  // Search标签的屏幕 | Search tab screens
  function SearchListScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text style={styles.title}>搜索列表 | Search List</Text>
        <Button
          title="查看Search详情 | View Search Detail"
          onPress={() => navigation.navigate('SearchDetail', { query: 'JavaScript' })}
        />
      </View>
    );
  }

  function SearchDetailScreen({ route, navigation }) {
    const { query, id } = route.params || {};
    return (
      <View style={styles.container}>
        <Text style={styles.title}>搜索详情 | Search Detail</Text>
        <Text>查询 | Query: {query}</Text>
        {id && <Text>ID: {id}</Text>}
        <Button title="返回 | Go Back" onPress={() => navigation.goBack()} />

        {/* 从Search详情页导航回Home标签 | Navigate back to Home tab from Search detail */}
        <Button
          title="返回Home标签 | Return to Home Tab"
          onPress={() => navigation.navigate('HomeTab')}
        />
      </View>
    );
  }

  function SearchStackNavigator() {
    return (
      <SearchStack.Navigator>
        <SearchStack.Screen
          name="SearchList"
          component={SearchListScreen}
          options={{ title: '搜索 | Search' }}
        />
        <SearchStack.Screen
          name="SearchDetail"
          component={SearchDetailScreen}
          options={{ title: 'Search详情 | Search Detail' }}
        />
      </SearchStack.Navigator>
    );
  }

  // 主App | Main App
  export default function App() {
    return (
      <NavigationContainer>
        <Tab.Navigator screenOptions={{ headerShown: false }}>
          <Tab.Screen name="HomeTab" component={HomeStackNavigator} />
          <Tab.Screen name="SearchTab" component={SearchStackNavigator} />
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  const styles = StyleSheet.create({
    container: {
      flex: 1,
      justifyContent: 'center',
      alignItems: 'center',
      padding: 20,
    },
    title: {
      fontSize: 20,
      fontWeight: 'bold',
      marginBottom: 20,
    },
  });
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在HomeListScreen中点击"跨标签：查看Search详情"按钮后，会发生什么？| What happens when you click the "Cross-tab: View Search Detail" button in HomeListScreen?
    **答案 | Answer:** 自动切换到SearchTab，并直接显示SearchDetail屏幕（跳过SearchList）| Automatically switches to SearchTab and directly displays SearchDetail screen (skipping SearchList)

  - SearchDetailScreen能接收到从HomeListScreen传递的params吗？| Can SearchDetailScreen receive the params passed from HomeListScreen?
    **答案 | Answer:** 是 | Yes - params: { query: 'React Native', id: 42 } 会正常传递给SearchDetailScreen的route.params | params: { query: 'React Native', id: 42 } are properly passed to SearchDetailScreen's route.params

  - 如果在HomeListScreen中调用navigation.navigate('SearchTab')（不指定screen），会显示SearchTab的哪个屏幕？| If you call navigation.navigate('SearchTab') in HomeListScreen (without specifying screen), which screen of SearchTab will be displayed?
    **答案 | Answer:** SearchList - SearchStack.Navigator的第一个定义的屏幕 | SearchList - the first defined screen in SearchStack.Navigator

  **常见误区检查 | Common Misconception Checks:**
  - 跨标签导航时，navigation.navigate('SearchDetail')可以直接到达Search标签的SearchDetail屏幕吗？| When navigating across tabs, can navigation.navigate('SearchDetail') directly reach SearchDetail screen in Search tab?
    **答案 | Answer:** 否 | No - 必须使用嵌套语法navigation.navigate('SearchTab', { screen: 'SearchDetail' })，因为'SearchDetail'只存在于SearchStack中 | Must use nested syntax navigation.navigate('SearchTab', { screen: 'SearchDetail' }) because 'SearchDetail' only exists within SearchStack

  - 嵌套导航语法中的'SearchTab'指的是什么？| What does 'SearchTab' refer to in the nested navigation syntax?
    **答案 | Answer:** Tab.Screen的name属性 | The name prop of Tab.Screen - 即<Tab.Screen name="SearchTab" ... />中定义的名称 | The name defined in <Tab.Screen name="SearchTab" ... />

### 5. 标签与堆栈导航的使用时机 | When to Use Tab vs Stack Navigation (30分钟 | 30 minutes)

- **选择合适的导航模式 | Choosing the Right Navigation Pattern**

  **概念定义 | Concept Definition:**
  选择使用标签导航还是堆栈导航取决于内容的组织结构和用户的使用流程。标签导航适用于应用的顶层结构，用于在平行的功能区域之间切换（如首页、搜索、设置）。堆栈导航适用于层级性的内容流，用于从概览到详情的深入浏览（如文章列表→文章详情→评论详情）。理解这两种模式的区别和适用场景是构建良好导航体验的关键。

  Choosing between tab navigation and stack navigation depends on the content organization structure and user workflow. Tab navigation is suitable for the top-level structure of an app, for switching between parallel functional areas (like Home, Search, Settings). Stack navigation is suitable for hierarchical content flows, for drilling down from overview to detail (like article list → article detail → comment detail). Understanding the differences and applicable scenarios of these two patterns is key to building a good navigation experience.

  **核心特征 | Key Characteristics:**
  - 标签导航特征：平行结构、持久可见、状态保持、顶层功能划分 | Tab navigation characteristics: Parallel structure, persistently visible, state preservation, top-level functional division
  - 堆栈导航特征：层级结构、返回导航、历史管理、详情深入 | Stack navigation characteristics: Hierarchical structure, back navigation, history management, detail drilling
  - 组合使用：大多数应用同时使用两者，通常是Tab作为根，Stack嵌套在Tab内 | Combined use: Most apps use both, typically Tab as root with Stack nested inside tabs
  - 用户期望：符合移动应用的导航惯例能提升用户体验 | User expectations: Following mobile app navigation conventions improves user experience

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 如果应用有"首页"、"消息"、"通知"、"我的"四个主要功能区域，应该使用什么导航模式？| If an app has four main functional areas: "Home", "Messages", "Notifications", "Profile", what navigation pattern should be used?
     **答案 | Answer:** 标签导航 | Tab navigation - 这些是平行的顶层功能区域，适合用标签区分 | These are parallel top-level functional areas, suitable for tab separation

  2. 如果用户需要从产品列表页进入产品详情页，再进入评论列表，再进入单条评论的详情，应该使用什么导航模式？| If a user needs to go from product list to product detail, then to comment list, then to individual comment detail, what navigation pattern should be used?
     **答案 | Answer:** 堆栈导航 | Stack navigation - 这是明显的层级结构，每一步都在深入内容 | This is a clear hierarchical structure, each step drills deeper into content

  3. 在一个电商应用中，"商品"、"购物车"、"订单"、"我的"标签，以及每个标签内的详情页，应该如何组织导航？| In an e-commerce app with "Products", "Cart", "Orders", "Profile" tabs, and detail pages within each tab, how should navigation be organized?
     **答案 | Answer:** Tab Navigator作为根，每个Tab内嵌套Stack Navigator处理详情页 | Tab Navigator as root, with Stack Navigator nested in each tab to handle detail pages - 这是典型的嵌套导航模式 | This is the typical nested navigation pattern

  4. 标签导航是否适合用于显示产品的不同类别（如"电子产品"、"服装"、"食品"）？| Is tab navigation suitable for displaying different product categories (like "Electronics", "Clothing", "Food")?
     **答案 | Answer:** 取决于应用设计 | Depends on app design - 如果类别是应用的核心结构，可以用标签；但通常类别更适合用列表+堆栈导航或Drawer导航 | If categories are the app's core structure, tabs can work; but usually categories are better with list + stack navigation or drawer navigation

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 示例1：典型的电商应用导航结构 | Example 1: Typical e-commerce app navigation structure
  import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
  import { createNativeStackNavigator } from '@react-navigation/native-stack';

  const Tab = createBottomTabNavigator();
  const ProductsStack = createNativeStackNavigator();
  const CartStack = createNativeStackNavigator();
  const OrdersStack = createNativeStackNavigator();

  // Products标签的堆栈 | Products tab stack
  function ProductsStackNavigator() {
    return (
      <ProductsStack.Navigator>
        {/* 层级1：产品列表 | Level 1: Product list */}
        <ProductsStack.Screen name="ProductList" component={ProductListScreen} />
        {/* 层级2：产品详情 | Level 2: Product detail */}
        <ProductsStack.Screen name="ProductDetail" component={ProductDetailScreen} />
        {/* 层级3：评论列表 | Level 3: Reviews list */}
        <ProductsStack.Screen name="Reviews" component={ReviewsScreen} />
        {/* 层级4：单条评论详情 | Level 4: Single review detail */}
        <ProductsStack.Screen name="ReviewDetail" component={ReviewDetailScreen} />
      </ProductsStack.Navigator>
    );
  }

  // Cart标签的堆栈 | Cart tab stack
  function CartStackNavigator() {
    return (
      <CartStack.Navigator>
        <CartStack.Screen name="CartList" component={CartListScreen} />
        <CartStack.Screen name="Checkout" component={CheckoutScreen} />
        <CartStack.Screen name="Payment" component={PaymentScreen} />
      </CartStack.Navigator>
    );
  }

  // Orders标签的堆栈 | Orders tab stack
  function OrdersStackNavigator() {
    return (
      <OrdersStack.Navigator>
        <OrdersStack.Screen name="OrderList" component={OrderListScreen} />
        <OrdersStack.Screen name="OrderDetail" component={OrderDetailScreen} />
      </OrdersStack.Navigator>
    );
  }

  // 主应用：Tab Navigator包含多个Stack Navigators | Main app: Tab Navigator containing multiple Stack Navigators
  function EcommerceApp() {
    return (
      <Tab.Navigator>
        {/* 顶层：四个平行的功能标签 | Top level: Four parallel functional tabs */}
        <Tab.Screen name="ProductsTab" component={ProductsStackNavigator} />
        <Tab.Screen name="CartTab" component={CartStackNavigator} />
        <Tab.Screen name="OrdersTab" component={OrdersStackNavigator} />
        <Tab.Screen name="ProfileTab" component={ProfileScreen} />
      </Tab.Navigator>
    );
  }

  // 示例2：错误的导航结构（反面教材）| Example 2: Incorrect navigation structure (anti-pattern)
  function BadNavigationExample() {
    return (
      // ❌ 错误：将详情页作为独立标签 | Wrong: Making detail pages independent tabs
      <Tab.Navigator>
        <Tab.Screen name="ProductList" component={ProductListScreen} />
        <Tab.Screen name="ProductDetail" component={ProductDetailScreen} /> {/* 不应该是标签 | Should not be a tab */}
        <Tab.Screen name="Cart" component={CartScreen} />
      </Tab.Navigator>
    );
  }

  // 示例3：正确的导航结构 | Example 3: Correct navigation structure
  function GoodNavigationExample() {
    return (
      // ✅ 正确：使用堆栈处理列表到详情的层级关系 | Correct: Use stack to handle hierarchical relationship from list to detail
      <ProductsStack.Navigator>
        <ProductsStack.Screen name="ProductList" component={ProductListScreen} />
        <ProductsStack.Screen name="ProductDetail" component={ProductDetailScreen} />
      </ProductsStack.Navigator>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在EcommerceApp示例中，为什么ProfileTab不需要嵌套Stack Navigator？| In the EcommerceApp example, why doesn't ProfileTab need a nested Stack Navigator?
    **答案 | Answer:** Profile通常是单个屏幕，不需要层级导航 | Profile is typically a single screen without hierarchical navigation needs - 如果未来需要"编辑资料"等子页面，可以再添加Stack | If future needs like "Edit Profile" arise, a Stack can be added later

  - BadNavigationExample中的问题是什么？| What's the problem with BadNavigationExample?
    **答案 | Answer:** ProductDetail不应该是标签 | ProductDetail should not be a tab - 详情页是从列表深入的层级内容，应该在堆栈中，而不是作为顶层标签 | Detail pages are hierarchical content drilled down from lists, should be in a stack, not as top-level tabs

  - 如果用户在ProductDetailScreen中点击后退按钮，期望返回哪里？| If a user presses back on ProductDetailScreen, where do they expect to return?
    **答案 | Answer:** ProductListScreen - 返回到上一层级（列表页） | ProductListScreen - return to the previous level (list page) - 这是堆栈导航的自然行为 | This is the natural behavior of stack navigation

  **常见误区检查 | Common Misconception Checks:**
  - 是否所有屏幕都应该作为标签？| Should all screens be tabs?
    **答案 | Answer:** 否 | No - 只有顶层的平行功能区域才适合做标签，详情页、设置子页等应该用堆栈导航 | Only top-level parallel functional areas are suitable as tabs; detail pages, settings sub-pages, etc. should use stack navigation

  - 堆栈导航是否可以独立使用，不嵌套在标签内？| Can stack navigation be used independently without being nested in tabs?
    **答案 | Answer:** 是 | Yes - 有些应用不需要标签（如单一流程的应用），只用堆栈导航也是合理的 | Some apps don't need tabs (like single-flow apps); using only stack navigation is also reasonable

### 6. 最佳实践与常见问题 | Best Practices & Common Issues (30分钟 | 30 minutes)

- **底部标签导航的最佳实践 | Best Practices for Bottom Tab Navigation**

  **核心原则 | Key Principles:**
  - 限制标签数量：通常3-5个标签，不超过5个以保持清晰 | Limit tab count: Typically 3-5 tabs, no more than 5 to maintain clarity
  - 标签含义明确：图标和文本应该清晰传达功能，避免歧义 | Clear tab meanings: Icons and text should clearly convey function, avoid ambiguity
  - 保持一致性：标签顺序和位置应该在整个应用中保持一致 | Maintain consistency: Tab order and position should remain consistent throughout the app
  - 合理嵌套：需要层级导航的标签内嵌套Stack Navigator | Reasonable nesting: Nest Stack Navigator in tabs that need hierarchical navigation
  - 性能优化：使用lazy选项延迟加载不常用的标签 | Performance optimization: Use lazy option to defer loading of infrequently used tabs

  **实践验证问题 | Practice Verification Questions:**
  1. 为什么建议标签数量不超过5个？| Why is it recommended to have no more than 5 tabs?
     **答案 | Answer:** 用户体验 | User experience - 太多标签会让标签栏拥挤，图标和文字变小，难以点击和识别 | Too many tabs make the tab bar crowded, icons and text become small, hard to tap and recognize

  2. 如何判断一个功能是否应该作为独立标签？| How do you determine if a function should be an independent tab?
     **答案 | Answer:** 看是否是核心功能且需要频繁访问 | Check if it's a core function and needs frequent access - 核心的、平行的、频繁使用的功能适合做标签 | Core, parallel, frequently used functions are suitable as tabs

  3. 标签栏在所有屏幕中都应该可见吗？| Should the tab bar be visible on all screens?
     **答案 | Answer:** 通常是，但可以在特定屏幕隐藏 | Usually yes, but can be hidden on specific screens - 如全屏视频播放、登录页面等可以隐藏标签栏 | Like fullscreen video playback, login pages can hide the tab bar

- **常见问题与解决方案 | Common Issues & Solutions**

  **问题1：标签栏在某些屏幕中意外消失 | Issue 1: Tab bar unexpectedly disappears on some screens**
  ```javascript
  // 原因：Tab.Navigator的headerShown设置与Stack.Navigator冲突 | Cause: headerShown conflict between Tab.Navigator and Stack.Navigator
  // 解决方案 | Solution:
  <Tab.Navigator screenOptions={{ headerShown: false }}>
    <Tab.Screen name="HomeTab" component={HomeStackNavigator} />
  </Tab.Navigator>
  ```

  **问题2：标签图标不随选中状态变化 | Issue 2: Tab icons don't change with selected state**
  ```javascript
  // 错误示例 | Incorrect example:
  tabBarIcon: () => <Ionicons name="home" size={24} />  // ❌ 没有使用focused参数 | Not using focused parameter

  // 正确示例 | Correct example:
  tabBarIcon: ({ focused, color }) => (
    <Ionicons name={focused ? 'home' : 'home-outline'} color={color} size={24} />
  )  // ✅ 根据focused状态改变图标 | Change icon based on focused state
  ```

  **问题3：嵌套导航时无法正确导航到深层屏幕 | Issue 3: Can't navigate correctly to deep screens in nested navigation**
  ```javascript
  // 错误示例 | Incorrect example:
  navigation.navigate('SearchDetail')  // ❌ 找不到路由 | Route not found

  // 正确示例 | Correct example:
  navigation.navigate('SearchTab', { screen: 'SearchDetail', params: {...} })  // ✅ 使用嵌套语法 | Use nested syntax
  ```

  **问题4：标签切换时组件状态丢失 | Issue 4: Component state lost when switching tabs**
  ```javascript
  // 原因：使用了unmountOnBlur选项 | Cause: Using unmountOnBlur option
  // 解决方案：移除unmountOnBlur或使用全局状态管理 | Solution: Remove unmountOnBlur or use global state management
  <Tab.Navigator
    screenOptions={{
      // unmountOnBlur: true,  // 移除这一行 | Remove this line
    }}
  >
  ```

## 实践项目：多功能新闻阅读应用 | Practical Project: Multi-functional News Reader App

### 目标 | Objective
构建一个包含底部标签导航和嵌套堆栈导航的新闻阅读应用，综合应用本日所学的所有概念：创建多个标签、添加图标、自定义样式、嵌套堆栈导航器、跨标签导航等。

Build a news reader app with bottom tab navigation and nested stack navigation, comprehensively applying all concepts learned today: creating multiple tabs, adding icons, customizing styles, nesting stack navigators, cross-tab navigation, etc.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 底部标签导航器的Tab.Navigator和Tab.Screen的关系是什么？| What's the relationship between Tab.Navigator and Tab.Screen?
   **答案 | Answer:** Tab.Navigator是容器，Tab.Screen定义每个标签页；Tab.Screen必须作为Tab.Navigator的直接子组件 | Tab.Navigator is the container, Tab.Screen defines each tab; Tab.Screen must be a direct child of Tab.Navigator

2. 如何在标签内实现从列表到详情的导航？| How do you implement navigation from list to detail within a tab?
   **答案 | Answer:** 在标签的component属性中传入一个Stack Navigator，Stack中定义列表和详情屏幕 | Pass a Stack Navigator to the tab's component prop, define list and detail screens in the Stack

3. tabBarIcon函数的focused参数有什么作用？| What's the purpose of the focused parameter in the tabBarIcon function?
   **答案 | Answer:** 指示标签是否被选中，用于显示不同的图标或样式 | Indicates whether the tab is selected, used to display different icons or styles

### 步骤 | Steps

1. **创建项目基础结构 | Create project base structure**
   - 安装必要的依赖：@react-navigation/native、@react-navigation/bottom-tabs、@react-navigation/native-stack、@expo/vector-icons
   - 设置NavigationContainer

2. **创建四个标签：首页、分类、收藏、我的 | Create four tabs: Home, Categories, Favorites, Profile**
   - 使用Ionicons为每个标签添加图标
   - 自定义标签栏颜色和样式

3. **为首页和分类标签添加堆栈导航 | Add stack navigation for Home and Categories tabs**
   - 首页：新闻列表 → 新闻详情
   - 分类：分类列表 → 分类新闻列表 → 新闻详情

4. **实现跨标签导航 | Implement cross-tab navigation**
   - 在新闻详情页添加"收藏"按钮，点击后跳转到"收藏"标签
   - 在收藏标签中显示收藏的新闻列表，点击可查看详情

5. **添加完善的数据传递 | Add comprehensive data passing**
   - 新闻列表传递新闻对象到详情页
   - 详情页接收并显示新闻标题、内容、作者等信息

### 示例代码 | Example Code

```javascript
import React, { useState } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { Ionicons } from '@expo/vector-icons';
import {
  View,
  Text,
  FlatList,
  TouchableOpacity,
  StyleSheet,
  Button,
  ScrollView,
} from 'react-native';

const Tab = createBottomTabNavigator();
const HomeStack = createNativeStackNavigator();
const CategoriesStack = createNativeStackNavigator();

// ===== 模拟数据 | Mock Data =====
const newsData = [
  { id: '1', title: 'React Native 6.0发布 | React Native 6.0 Released', category: 'Tech', author: 'John Doe' },
  { id: '2', title: '移动开发最佳实践 | Mobile Development Best Practices', category: 'Tech', author: 'Jane Smith' },
  { id: '3', title: '体育新闻：冠军赛事 | Sports News: Championship Event', category: 'Sports', author: 'Mike Johnson' },
];

const categories = [
  { id: '1', name: '科技 | Tech', icon: 'hardware-chip' },
  { id: '2', name: '体育 | Sports', icon: 'football' },
  { id: '3', name: '娱乐 | Entertainment', icon: 'musical-notes' },
];

// ===== 首页标签屏幕 | Home Tab Screens =====
function HomeListScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.header}>最新新闻 | Latest News</Text>
      <FlatList
        data={newsData}
        keyExtractor={item => item.id}
        renderItem={({ item }) => (
          <TouchableOpacity
            style={styles.newsItem}
            onPress={() => navigation.navigate('HomeDetail', { news: item })}
          >
            <Text style={styles.newsTitle}>{item.title}</Text>
            <Text style={styles.newsAuthor}>作者 | By: {item.author}</Text>
            <Ionicons name="chevron-forward" size={20} color="#999" />
          </TouchableOpacity>
        )}
      />
    </View>
  );
}

function HomeDetailScreen({ route, navigation }) {
  const { news } = route.params;
  return (
    <ScrollView style={styles.container}>
      <Text style={styles.detailTitle}>{news.title}</Text>
      <Text style={styles.detailMeta}>作者 | Author: {news.author}</Text>
      <Text style={styles.detailMeta}>分类 | Category: {news.category}</Text>
      <Text style={styles.detailContent}>
        这是新闻的详细内容... Lorem ipsum dolor sit amet, consectetur adipiscing elit.
        | This is the detailed news content... Lorem ipsum dolor sit amet, consectetur adipiscing elit.
      </Text>
      <Button
        title="添加到收藏 | Add to Favorites"
        onPress={() => {
          // 跨标签导航到收藏标签 | Cross-tab navigation to Favorites tab
          navigation.navigate('FavoritesTab');
          alert('已添加到收藏 | Added to favorites');
        }}
      />
    </ScrollView>
  );
}

function HomeStackNavigator() {
  return (
    <HomeStack.Navigator>
      <HomeStack.Screen
        name="HomeList"
        component={HomeListScreen}
        options={{ title: '首页 | Home' }}
      />
      <HomeStack.Screen
        name="HomeDetail"
        component={HomeDetailScreen}
        options={{ title: '新闻详情 | News Detail' }}
      />
    </HomeStack.Navigator>
  );
}

// ===== 分类标签屏幕 | Categories Tab Screens =====
function CategoriesListScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.header}>新闻分类 | News Categories</Text>
      <FlatList
        data={categories}
        keyExtractor={item => item.id}
        renderItem={({ item }) => (
          <TouchableOpacity
            style={styles.categoryItem}
            onPress={() => navigation.navigate('CategoryNews', { category: item })}
          >
            <Ionicons name={item.icon} size={30} color="#e91e63" />
            <Text style={styles.categoryName}>{item.name}</Text>
            <Ionicons name="chevron-forward" size={20} color="#999" />
          </TouchableOpacity>
        )}
      />
    </View>
  );
}

function CategoryNewsScreen({ route, navigation }) {
  const { category } = route.params;
  const filteredNews = newsData.filter(news => news.category === category.name.split(' | ')[1]);

  return (
    <View style={styles.container}>
      <Text style={styles.header}>{category.name}</Text>
      <FlatList
        data={filteredNews}
        keyExtractor={item => item.id}
        renderItem={({ item }) => (
          <TouchableOpacity
            style={styles.newsItem}
            onPress={() => navigation.navigate('CategoryDetail', { news: item })}
          >
            <Text style={styles.newsTitle}>{item.title}</Text>
            <Text style={styles.newsAuthor}>作者 | By: {item.author}</Text>
          </TouchableOpacity>
        )}
        ListEmptyComponent={<Text style={styles.emptyText}>暂无新闻 | No news available</Text>}
      />
    </View>
  );
}

function CategoryDetailScreen({ route, navigation }) {
  const { news } = route.params;
  return (
    <ScrollView style={styles.container}>
      <Text style={styles.detailTitle}>{news.title}</Text>
      <Text style={styles.detailMeta}>作者 | Author: {news.author}</Text>
      <Text style={styles.detailContent}>
        分类新闻详情... | Category news detail...
      </Text>
      <Button title="返回 | Go Back" onPress={() => navigation.goBack()} />
    </ScrollView>
  );
}

function CategoriesStackNavigator() {
  return (
    <CategoriesStack.Navigator>
      <CategoriesStack.Screen
        name="CategoriesList"
        component={CategoriesListScreen}
        options={{ title: '分类 | Categories' }}
      />
      <CategoriesStack.Screen
        name="CategoryNews"
        component={CategoryNewsScreen}
        options={({ route }) => ({ title: route.params.category.name })}
      />
      <CategoriesStack.Screen
        name="CategoryDetail"
        component={CategoryDetailScreen}
        options={{ title: '详情 | Detail' }}
      />
    </CategoriesStack.Navigator>
  );
}

// ===== 收藏标签屏幕（无嵌套堆栈）| Favorites Tab Screen (no nested stack) =====
function FavoritesScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.header}>我的收藏 | My Favorites</Text>
      <Text style={styles.emptyText}>暂无收藏 | No favorites yet</Text>
    </View>
  );
}

// ===== 我的标签屏幕 | Profile Tab Screen =====
function ProfileScreen() {
  return (
    <View style={styles.container}>
      <Text style={styles.header}>个人中心 | Profile</Text>
      <Text>用户名 | Username: Demo User</Text>
      <Text>邮箱 | Email: demo@example.com</Text>
    </View>
  );
}

// ===== 主应用：底部标签导航 | Main App: Bottom Tab Navigation =====
export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        screenOptions={{
          headerShown: false, // 隐藏Tab Navigator头部，使用Stack的头部 | Hide Tab Navigator header, use Stack's header
          tabBarActiveTintColor: '#e91e63',
          tabBarInactiveTintColor: '#757575',
          tabBarStyle: {
            backgroundColor: '#ffffff',
            borderTopWidth: 1,
            borderTopColor: '#e0e0e0',
            height: 60,
            paddingBottom: 8,
            paddingTop: 8,
          },
          tabBarLabelStyle: {
            fontSize: 12,
            fontWeight: '600',
          },
        }}
      >
        <Tab.Screen
          name="HomeTab"
          component={HomeStackNavigator}
          options={{
            tabBarLabel: '首页',
            tabBarIcon: ({ focused, color, size }) => (
              <Ionicons name={focused ? 'home' : 'home-outline'} size={size} color={color} />
            ),
          }}
        />

        <Tab.Screen
          name="CategoriesTab"
          component={CategoriesStackNavigator}
          options={{
            tabBarLabel: '分类',
            tabBarIcon: ({ focused, color, size }) => (
              <Ionicons name={focused ? 'grid' : 'grid-outline'} size={size} color={color} />
            ),
          }}
        />

        <Tab.Screen
          name="FavoritesTab"
          component={FavoritesScreen}
          options={{
            tabBarLabel: '收藏',
            tabBarIcon: ({ focused, color, size }) => (
              <Ionicons name={focused ? 'heart' : 'heart-outline'} size={size} color={color} />
            ),
            headerShown: true, // 收藏标签显示头部（因为没有嵌套Stack）| Show header for Favorites (no nested Stack)
            title: '我的收藏 | My Favorites',
          }}
        />

        <Tab.Screen
          name="ProfileTab"
          component={ProfileScreen}
          options={{
            tabBarLabel: '我的',
            tabBarIcon: ({ focused, color, size }) => (
              <Ionicons name={focused ? 'person' : 'person-outline'} size={size} color={color} />
            ),
            headerShown: true,
            title: '个人中心 | Profile',
          }}
        />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    padding: 16,
  },
  header: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 16,
    color: '#333',
  },
  newsItem: {
    padding: 16,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
  },
  newsTitle: {
    fontSize: 16,
    fontWeight: '600',
    flex: 1,
    color: '#333',
  },
  newsAuthor: {
    fontSize: 12,
    color: '#666',
    marginRight: 10,
  },
  categoryItem: {
    flexDirection: 'row',
    alignItems: 'center',
    padding: 20,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  categoryName: {
    fontSize: 18,
    marginLeft: 15,
    flex: 1,
    color: '#333',
  },
  detailTitle: {
    fontSize: 22,
    fontWeight: 'bold',
    marginBottom: 10,
    color: '#333',
  },
  detailMeta: {
    fontSize: 14,
    color: '#666',
    marginBottom: 5,
  },
  detailContent: {
    fontSize: 16,
    lineHeight: 24,
    color: '#444',
    marginTop: 15,
    marginBottom: 20,
  },
  emptyText: {
    textAlign: 'center',
    color: '#999',
    marginTop: 50,
    fontSize: 16,
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确创建了4个标签页？| Does the project correctly create 4 tabs?
2. 首页和分类标签是否成功嵌套了Stack Navigator？| Do Home and Categories tabs successfully nest Stack Navigator?
3. 是否能从新闻列表导航到新闻详情，并传递数据？| Can you navigate from news list to news detail and pass data?
4. 标签图标是否根据选中状态变化？| Do tab icons change based on selected state?
5. 是否实现了从新闻详情页跨标签导航到收藏页？| Is cross-tab navigation from news detail to favorites implemented?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **tabBarIcon深入理解练习 | Deep Understanding of tabBarIcon Exercise**
   - **练习描述 | Exercise Description:** 修改项目中的标签图标，使选中时图标放大20%，并添加轻微的颜色过渡动画
   - **概念检查 | Concept Check:** tabBarIcon函数接收哪些参数？如何使用focused参数改变图标大小？
   - **学习目标 | Learning Objective:** 深入理解tabBarIcon的动态配置能力

2. **嵌套导航参数传递练习 | Nested Navigation Parameter Passing Exercise**
   - **练习描述 | Exercise Description:** 在首页添加一个按钮，点击后直接导航到"分类"标签的"科技"分类新闻列表
   - **概念检查 | Concept Check:** 如何使用嵌套导航语法？如何传递多层参数？
   - **学习目标 | Learning Objective:** 掌握跨标签的深层导航和参数传递

3. **标签栏样式自定义练习 | Tab Bar Style Customization Exercise**
   - **练习描述 | Exercise Description:** 自定义标签栏：添加阴影效果、圆角、背景渐变色
   - **概念检查 | Concept Check:** tabBarStyle接收什么类型的值？如何设置tabBarLabelStyle？
   - **学习目标 | Learning Objective:** 提高UI自定义能力

4. **动态徽章显示练习 | Dynamic Badge Display Exercise**
   - **练习描述 | Exercise Description:** 在"收藏"标签上显示收藏数量徽章，收藏为0时隐藏徽章
   - **概念检查 | Concept Check:** tabBarBadge如何动态设置？如何在值为0时隐藏徽章？
   - **学习目标 | Learning Objective:** 理解动态options配置

5. **标签状态保持练习 | Tab State Preservation Exercise**
   - **练习描述 | Exercise Description:** 在首页列表中添加一个搜索框，切换到其他标签再切回来，搜索框的内容应该保持
   - **概念检查 | Concept Check:** 标签切换时组件状态如何保持？什么情况下状态会丢失？
   - **学习目标 | Learning Objective:** 理解React Navigation的状态保持机制

6. **隐藏标签栏练习 | Hide Tab Bar Exercise**
   - **练习描述 | Exercise Description:** 在新闻详情页隐藏标签栏，提供沉浸式阅读体验
   - **概念检查 | Concept Check:** 如何在特定屏幕隐藏标签栏？tabBarStyle和tabBarVisible的区别？
   - **学习目标 | Learning Objective:** 掌握标签栏的显示控制

7. **多层级嵌套导航练习 | Multi-level Nested Navigation Exercise**
   - **练习描述 | Exercise Description:** 在"分类详情"页面添加"相关新闻"列表，点击后进入第四层级的详情页
   - **概念检查 | Concept Check:** Stack Navigator支持多少层级？如何管理深层导航的后退逻辑？
   - **学习目标 | Learning Objective:** 理解复杂导航结构的设计和管理

## 学习资源 | Learning Resources
- [React Navigation - Tab Navigator官方文档](https://reactnavigation.org/docs/bottom-tab-navigator)
- [React Navigation - Nesting Navigators](https://reactnavigation.org/docs/nesting-navigators)
- [Expo Vector Icons目录](https://icons.expo.fyi/)
- [React Navigation - Configuring the Tab Bar](https://reactnavigation.org/docs/bottom-tab-navigator#tabbaroptions)
- [移动应用导航设计最佳实践](https://material.io/design/navigation/understanding-navigation.html)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解底部标签导航器的概念和使用场景 | Understand concept and use cases of Bottom Tab Navigator
- [ ] 能够创建多个标签并配置基本属性 | Can create multiple tabs and configure basic properties
- [ ] 掌握使用@expo/vector-icons添加标签图标 | Master adding tab icons with @expo/vector-icons
- [ ] 理解tabBarIcon函数的参数（focused, color, size）| Understand tabBarIcon function parameters (focused, color, size)
- [ ] 能够自定义标签栏外观（颜色、样式、标签）| Can customize tab bar appearance (colors, styles, labels)
- [ ] 掌握在标签内嵌套Stack Navigator的核心模式 | Master core pattern of nesting Stack Navigator inside tabs
- [ ] 理解嵌套导航器中各标签的独立导航状态 | Understand independent navigation state of each tab in nested navigators
- [ ] 能够使用嵌套导航语法跨标签导航 | Can use nested navigation syntax for cross-tab navigation
- [ ] 理解标签导航与堆栈导航的使用时机 | Understand when to use tab vs stack navigation
- [ ] 完成新闻阅读应用实践项目 | Complete news reader app practical project

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释：底部标签导航器的工作原理、嵌套导航器的概念、以及如何选择合适的导航模式。

Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain to others: how Bottom Tab Navigator works, the concept of nested navigators, and how to choose the appropriate navigation pattern.
