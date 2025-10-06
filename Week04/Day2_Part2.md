# React Native入门 - 第4周第2天（续）：传递屏幕间数据 | React Native Introduction - Week 4 Day 2 (Continued): Passing Data Between Screens

## 详细内容（续） | Detailed Content (Continued)

### 4. 列表到详情页的导航模式 | List-to-Detail Navigation Pattern (1小时 | 1 hour)

- **实现产品列表到产品详情的导航 | Implementing Product List to Product Detail Navigation**

  **概念定义 | Concept Definition:**
  列表到详情页（List-to-Detail）是移动应用中最常见的导航模式之一。用户从一个包含多个项目的列表（如产品、文章、联系人）中选择一个项目，然后导航到显示该项目完整详细信息的屏幕。这种模式涉及从列表项中提取标识符或完整对象，通过导航参数传递到详情屏幕，然后在详情屏幕中显示或获取完整数据。| List-to-Detail is one of the most common navigation patterns in mobile apps. Users select an item from a list containing multiple items (such as products, articles, contacts), then navigate to a screen displaying the complete details of that item. This pattern involves extracting an identifier or complete object from the list item, passing it to the detail screen via navigation parameters, then displaying or fetching complete data in the detail screen.

  **核心特征 | Key Characteristics:**
  - 列表屏幕渲染可点击的项目集合，每个项目都可以导航到详情 | List screen renders a collection of clickable items, each can navigate to details
  - 通常传递项目ID或完整对象作为导航参数 | Usually passes item ID or complete object as navigation parameter
  - 详情屏幕根据接收的参数显示或获取数据 | Detail screen displays or fetches data based on received parameters
  - 支持返回导航（goBack）回到列表，保持列表的滚动位置 | Supports back navigation (goBack) to list, maintaining list scroll position
  - 可以在详情页修改数据，返回时更新列表（需要状态管理） | Can modify data in detail screen, update list when returning (requires state management)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. 应该传递完整对象还是只传递ID到详情屏幕？| Should I pass the complete object or just the ID to the detail screen?
     **答案 | Answer:** 取决于场景 | Depends on scenario - 如果数据较小且不会变化，可以传递对象实现即时显示；如果数据大或可能更新，传递ID然后在详情屏幕获取最新数据 | If data is small and won't change, can pass object for instant display; if data is large or may update, pass ID then fetch latest data in detail screen

  2. FlatList的renderItem中的导航函数会每次渲染都重新创建吗？| Will the navigation function in FlatList's renderItem be recreated on every render?
     **答案 | Answer:** 是的，除非使用useCallback优化 | Yes, unless optimized with useCallback - 应将导航函数用useCallback包裹，或提取为组件避免重新创建 | Should wrap navigation function with useCallback or extract as component to avoid recreation

  3. 如果列表中有1000个项目，每个都创建onPress函数会有性能问题吗？| If the list has 1000 items, will creating an onPress function for each cause performance issues?
     **答案 | Answer:** 可能会 | Possibly - FlatList的虚拟化只渲染可见项，但仍应优化。最佳实践是使用数据驱动的导航或提取列表项为单独组件 | FlatList's virtualization only renders visible items, but still should optimize. Best practice is data-driven navigation or extracting list items as separate components

  4. 从详情页返回后，列表会重新渲染吗？| Will the list re-render after returning from detail screen?
     **答案 | Answer:** 不一定 | Not necessarily - 默认情况下列表保持状态。如需更新（如数据被修改），使用navigation focus事件或状态管理库 | By default the list maintains its state. To update (if data was modified), use navigation focus events or state management library

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 列表到详情导航的完整实现 | Complete implementation of list-to-detail navigation

  // 方法1：传递完整对象（适合小数据） | Method 1: Pass complete object (suitable for small data)
  function ProductListScreen({ navigation }) {
    const products = [
      {
        id: '1',
        name: 'Wireless Headphones',
        price: 99.99,
        description: 'High-quality wireless headphones',
        image: 'https://example.com/headphones.jpg',
        inStock: true,
        rating: 4.5
      },
      {
        id: '2',
        name: 'Smart Watch',
        price: 299.99,
        description: 'Feature-rich smartwatch',
        image: 'https://example.com/watch.jpg',
        inStock: true,
        rating: 4.8
      },
      // ... 更多产品 | ... more products
    ];

    const renderProduct = ({ item }) => (
      <TouchableOpacity
        style={styles.productCard}
        onPress={() => {
          // 传递完整产品对象 | Pass complete product object
          navigation.navigate('ProductDetail', {
            product: item,
            fromScreen: 'ProductList'  // 可选：跟踪来源 | Optional: track source
          });
        }}
      >
        <Image source={{ uri: item.image }} style={styles.productImage} />
        <View style={styles.productInfo}>
          <Text style={styles.productName}>{item.name}</Text>
          <Text style={styles.productPrice}>${item.price}</Text>
          <View style={styles.ratingContainer}>
            <Text>⭐ {item.rating}</Text>
            <Text style={{ color: item.inStock ? 'green' : 'red' }}>
              {item.inStock ? 'In Stock' : 'Out of Stock'}
            </Text>
          </View>
        </View>
      </TouchableOpacity>
    );

    return (
      <View style={styles.container}>
        <FlatList
          data={products}
          keyExtractor={item => item.id}
          renderItem={renderProduct}
          contentContainerStyle={styles.listContent}
        />
      </View>
    );
  }

  // 详情屏幕接收对象 | Detail screen receiving object
  function ProductDetailScreen({ route, navigation }) {
    const { product, fromScreen } = route.params;

    React.useLayoutEffect(() => {
      // 使用产品名称作为header标题 | Use product name as header title
      navigation.setOptions({
        title: product.name,
      });
    }, [navigation, product.name]);

    return (
      <ScrollView style={styles.container}>
        <Image source={{ uri: product.image }} style={styles.detailImage} />
        <View style={styles.detailContent}>
          <Text style={styles.detailName}>{product.name}</Text>
          <Text style={styles.detailPrice}>${product.price}</Text>
          <Text style={styles.detailDescription}>{product.description}</Text>
          <View style={styles.detailMeta}>
            <Text>Rating: {product.rating} ⭐</Text>
            <Text style={{ color: product.inStock ? 'green' : 'red' }}>
              {product.inStock ? 'Available' : 'Sold Out'}
            </Text>
          </View>
          <Button
            title="Add to Cart"
            disabled={!product.inStock}
            onPress={() => addToCart(product)}
          />
        </View>
      </ScrollView>
    );
  }

  // 方法2：只传递ID，在详情屏幕获取数据（适合大数据或动态数据） | Method 2: Pass only ID, fetch data in detail screen (suitable for large or dynamic data)
  function ArticleListScreen({ navigation }) {
    const [articles, setArticles] = React.useState([]);
    const [loading, setLoading] = React.useState(true);

    React.useEffect(() => {
      fetchArticles().then(data => {
        setArticles(data);
        setLoading(false);
      });
    }, []);

    const navigateToArticle = React.useCallback((articleId, articleTitle) => {
      // 只传递ID和标题（用于即时header更新） | Pass only ID and title (for instant header update)
      navigation.navigate('ArticleDetail', {
        articleId: articleId,
        articleTitle: articleTitle  // 可选：用于快速显示 | Optional: for quick display
      });
    }, [navigation]);

    const renderArticle = ({ item }) => (
      <TouchableOpacity
        style={styles.articleCard}
        onPress={() => navigateToArticle(item.id, item.title)}
      >
        <Text style={styles.articleTitle}>{item.title}</Text>
        <Text style={styles.articleExcerpt}>{item.excerpt}</Text>
        <View style={styles.articleMeta}>
          <Text>{item.author}</Text>
          <Text>{item.date}</Text>
        </View>
      </TouchableOpacity>
    );

    if (loading) {
      return <ActivityIndicator size="large" />;
    }

    return (
      <FlatList
        data={articles}
        keyExtractor={item => item.id}
        renderItem={renderArticle}
      />
    );
  }

  // 详情屏幕获取完整数据 | Detail screen fetches complete data
  function ArticleDetailScreen({ route, navigation }) {
    const { articleId, articleTitle } = route.params;
    const [article, setArticle] = React.useState(null);
    const [loading, setLoading] = React.useState(true);

    React.useEffect(() => {
      // 根据ID获取完整文章数据 | Fetch complete article data by ID
      fetchArticleById(articleId)
        .then(data => {
          setArticle(data);
          setLoading(false);
          // 获取后更新header标题（如果需要） | Update header title after fetching (if needed)
          navigation.setOptions({ title: data.title });
        })
        .catch(error => {
          console.error('Failed to load article:', error);
          setLoading(false);
        });
    }, [articleId, navigation]);

    // 使用参数中的标题先显示 | Show title from params first
    React.useLayoutEffect(() => {
      if (articleTitle) {
        navigation.setOptions({ title: articleTitle });
      }
    }, [navigation, articleTitle]);

    if (loading) {
      return (
        <View style={styles.loadingContainer}>
          <ActivityIndicator size="large" />
          <Text>Loading article...</Text>
        </View>
      );
    }

    if (!article) {
      return (
        <View style={styles.errorContainer}>
          <Text>Article not found</Text>
          <Button title="Go Back" onPress={() => navigation.goBack()} />
        </View>
      );
    }

    return (
      <ScrollView style={styles.container}>
        <Text style={styles.articleTitle}>{article.title}</Text>
        <View style={styles.articleMeta}>
          <Text>By {article.author}</Text>
          <Text>{new Date(article.publishedAt).toLocaleDateString()}</Text>
        </View>
        <Text style={styles.articleContent}>{article.content}</Text>
      </ScrollView>
    );
  }

  // 方法3：优化性能的列表项组件提取 | Method 3: Extract list item component for performance optimization
  const ProductItem = React.memo(({ product, onPress }) => {
    return (
      <TouchableOpacity
        style={styles.productCard}
        onPress={() => onPress(product)}
      >
        <Image source={{ uri: product.image }} style={styles.productImage} />
        <View style={styles.productInfo}>
          <Text style={styles.productName}>{product.name}</Text>
          <Text style={styles.productPrice}>${product.price}</Text>
        </View>
      </TouchableOpacity>
    );
  });

  function OptimizedProductListScreen({ navigation }) {
    const [products, setProducts] = React.useState([]);

    // 使用useCallback避免每次渲染都创建新函数 | Use useCallback to avoid creating new function on every render
    const handleProductPress = React.useCallback((product) => {
      navigation.navigate('ProductDetail', { product });
    }, [navigation]);

    const renderProduct = React.useCallback(({ item }) => (
      <ProductItem product={item} onPress={handleProductPress} />
    ), [handleProductPress]);

    return (
      <FlatList
        data={products}
        keyExtractor={item => item.id}
        renderItem={renderProduct}
        // 性能优化props | Performance optimization props
        removeClippedSubviews={true}
        maxToRenderPerBatch={10}
        updateCellsBatchingPeriod={50}
        initialNumToRender={10}
        windowSize={21}
      />
    );
  }

  // 方法4：支持刷新的列表（从详情页返回后更新） | Method 4: List with refresh support (update after returning from detail)
  function RefreshableListScreen({ navigation }) {
    const [products, setProducts] = React.useState([]);
    const [refreshing, setRefreshing] = React.useState(false);

    const loadProducts = async () => {
      const data = await fetchProducts();
      setProducts(data);
    };

    // 初始加载 | Initial load
    React.useEffect(() => {
      loadProducts();
    }, []);

    // 监听屏幕focus事件，从详情页返回时刷新 | Listen to screen focus event, refresh when returning from detail
    React.useEffect(() => {
      const unsubscribe = navigation.addListener('focus', () => {
        // 屏幕获得焦点时重新加载数据 | Reload data when screen gains focus
        loadProducts();
      });

      return unsubscribe;
    }, [navigation]);

    const handleRefresh = async () => {
      setRefreshing(true);
      await loadProducts();
      setRefreshing(false);
    };

    const navigateToDetail = (product) => {
      navigation.navigate('EditableProductDetail', {
        product,
        // 回调函数：详情页可以调用它来通知列表更新 | Callback: detail page can call it to notify list to update
        onUpdate: (updatedProduct) => {
          setProducts(prevProducts =>
            prevProducts.map(p =>
              p.id === updatedProduct.id ? updatedProduct : p
            )
          );
        }
      });
    };

    return (
      <FlatList
        data={products}
        keyExtractor={item => item.id}
        renderItem={({ item }) => (
          <TouchableOpacity onPress={() => navigateToDetail(item)}>
            <Text>{item.name} - ${item.price}</Text>
          </TouchableOpacity>
        )}
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={handleRefresh} />
        }
      />
    );
  }

  // 可编辑的详情屏幕 | Editable detail screen
  function EditableProductDetailScreen({ route, navigation }) {
    const { product, onUpdate } = route.params;
    const [editedProduct, setEditedProduct] = React.useState(product);

    const saveProduct = async () => {
      // 保存到服务器 | Save to server
      const updated = await updateProductOnServer(editedProduct);

      // 调用回调更新列表 | Call callback to update list
      if (onUpdate) {
        onUpdate(updated);
      }

      // 更新当前屏幕参数 | Update current screen parameters
      navigation.setParams({ product: updated });

      Alert.alert('Success', 'Product updated');
    };

    return (
      <View>
        <TextInput
          value={editedProduct.name}
          onChangeText={(text) =>
            setEditedProduct({ ...editedProduct, name: text })
          }
        />
        <TextInput
          value={String(editedProduct.price)}
          onChangeText={(text) =>
            setEditedProduct({ ...editedProduct, price: parseFloat(text) })
          }
          keyboardType="numeric"
        />
        <Button title="Save" onPress={saveProduct} />
      </View>
    );
  }
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在ArticleDetailScreen中，为什么要在参数中包含articleTitle，如果我们已经传递了articleId？| In ArticleDetailScreen, why include articleTitle in params if we already passed articleId?
    **答案 | Answer:** 用户体验优化 | UX optimization - articleTitle可以立即在header中显示，而完整数据在后台加载，避免用户看到空白header | articleTitle can be displayed immediately in the header while complete data loads in background, avoiding user seeing blank header

  - 在OptimizedProductListScreen中，如果product对象改变，ProductItem会重新渲染吗？| In OptimizedProductListScreen, will ProductItem re-render if the product object changes?
    **答案 | Answer:** 会 | Yes - React.memo只对props进行浅比较，如果product对象引用改变就会重新渲染。可以提供自定义比较函数优化 | React.memo only does shallow comparison of props, will re-render if product object reference changes. Can provide custom comparison function for optimization

  **常见误区检查 | Common Misconception Checks:**
  - 我应该总是传递完整对象而不是ID，因为这样更快？| Should I always pass complete objects instead of IDs because it's faster?
    **答案 | Answer:** 不一定 | Not necessarily - 传对象快速显示，但如果数据可能更新（如其他用户编辑），传ID获取最新数据更可靠。还要考虑deep linking和state serialization | Passing objects shows faster, but if data may update (like other users editing), passing ID to fetch latest data is more reliable. Also consider deep linking and state serialization

  - FlatList会自动优化，所以我不需要担心性能？| FlatList automatically optimizes, so I don't need to worry about performance?
    **答案 | Answer:** 部分正确 | Partially correct - FlatList提供虚拟化，但仍需优化renderItem（用useCallback）、提取组件（React.memo）、优化数据结构等 | FlatList provides virtualization, but still need to optimize renderItem (with useCallback), extract components (React.memo), optimize data structures, etc.

### 5. 返回导航和堆栈管理 | Back Navigation and Stack Management (45分钟 | 45 minutes)

- **使用navigation.goBack()返回 | Using navigation.goBack() to Go Back**

  **概念定义 | Concept Definition:**
  navigation.goBack()是返回到导航堆栈中上一个屏幕的方法。它从堆栈中移除当前屏幕，并显示之前的屏幕。这是实现"返回"按钮功能的主要方式，模拟了移动应用中标准的后退行为。goBack()不接受参数，总是返回到堆栈中的上一个屏幕，如果当前屏幕是堆栈中唯一的屏幕，调用goBack()将不会有任何效果。| navigation.goBack() is the method to return to the previous screen in the navigation stack. It removes the current screen from the stack and displays the previous screen. This is the primary way to implement "back" button functionality, simulating the standard back behavior in mobile apps. goBack() takes no parameters and always returns to the previous screen in the stack; if the current screen is the only screen in the stack, calling goBack() will have no effect.

  **核心特征 | Key Characteristics:**
  - 从导航堆栈中移除当前屏幕，不保留其状态（除非配置了状态持久化） | Removes current screen from navigation stack, doesn't preserve its state (unless state persistence is configured)
  - 自动显示平台特定的返回动画（iOS从右滑入，Android淡入） | Automatically shows platform-specific back animation (iOS slide from right, Android fade in)
  - 如果当前屏幕是堆栈的根屏幕，goBack()无效果 | If current screen is the root screen of the stack, goBack() has no effect
  - 与硬件返回按钮（Android）自动集成 | Automatically integrates with hardware back button (Android)
  - 可以传递数据回上一个屏幕（需要使用其他模式，如回调或navigation events） | Can pass data back to previous screen (requires other patterns like callbacks or navigation events)

  **概念检查问题 | Concept Checking Questions (CCQs):**
  1. goBack()会将我带回特定的屏幕吗（如Home屏幕）？| Will goBack() take me back to a specific screen (like Home screen)?
     **答案 | Answer:** 否 | No - goBack()总是返回堆栈中的上一个屏幕，无论它是什么。要返回特定屏幕，使用navigation.navigate() | goBack() always returns to the previous screen in the stack, whatever it is. To go to a specific screen, use navigation.navigate()

  2. 调用goBack()后，当前屏幕的state会被保存吗？| After calling goBack(), will the current screen's state be saved?
     **答案 | Answer:** 否 | No - 屏幕从堆栈移除，其state丢失。如需保存，在goBack前使用setParams或全局状态 | Screen is removed from stack, its state is lost. To save, use setParams or global state before goBack

  3. 如果我在堆栈的第一个屏幕调用goBack()会发生什么？| What happens if I call goBack() on the first screen in the stack?
     **答案 | Answer:** 什么也不发生 | Nothing happens - 因为没有上一个屏幕可以返回，调用会被忽略 | Because there's no previous screen to return to, the call is ignored

  4. Android的硬件返回按钮和goBack()是同一回事吗？| Is Android's hardware back button the same as goBack()?
     **答案 | Answer:** 基本是 | Essentially yes - React Navigation自动处理硬件返回按钮，效果和goBack()相同。可以用BackHandler自定义 | React Navigation automatically handles hardware back button with same effect as goBack(). Can customize with BackHandler

  **代码示例与验证 | Code Examples and Verification:**
  ```javascript
  // 返回导航的各种用法 | Various uses of back navigation

  // 1. 基础返回 | Basic back navigation
  function DetailScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text>Detail Screen</Text>

        {/* 方式1：使用Button的onPress | Method 1: Use Button's onPress */}
        <Button
          title="Go Back"
          onPress={() => navigation.goBack()}
        />

        {/* 方式2：简写形式 | Method 2: Shorthand */}
        <Button
          title="Go Back"
          onPress={navigation.goBack}
        />
      </View>
    );
  }

  // 2. 确认后返回 | Back with confirmation
  function EditScreen({ route, navigation }) {
    const [hasUnsavedChanges, setHasUnsavedChanges] = React.useState(false);
    const [formData, setFormData] = React.useState({});

    const handleGoBack = () => {
      if (hasUnsavedChanges) {
        Alert.alert(
          'Discard changes?',
          'You have unsaved changes. Are you sure you want to go back?',
          [
            { text: 'Cancel', style: 'cancel' },
            {
              text: 'Discard',
              style: 'destructive',
              onPress: () => navigation.goBack()
            },
          ]
        );
      } else {
        navigation.goBack();
      }
    };

    // 覆盖header的返回按钮 | Override header back button
    React.useLayoutEffect(() => {
      navigation.setOptions({
        headerLeft: () => (
          <TouchableOpacity onPress={handleGoBack} style={styles.headerButton}>
            <Icon name="arrow-back" size={24} />
          </TouchableOpacity>
        ),
      });
    }, [navigation, hasUnsavedChanges]);

    // 处理Android硬件返回按钮 | Handle Android hardware back button
    React.useEffect(() => {
      const backHandler = BackHandler.addEventListener(
        'hardwareBackPress',
        () => {
          if (hasUnsavedChanges) {
            handleGoBack();
            return true;  // 阻止默认行为 | Prevent default behavior
          }
          return false;  // 允许默认行为（goBack） | Allow default behavior (goBack)
        }
      );

      return () => backHandler.remove();
    }, [hasUnsavedChanges]);

    return (
      <View>
        <TextInput
          value={formData.title}
          onChangeText={(text) => {
            setFormData({ ...formData, title: text });
            setHasUnsavedChanges(true);
          }}
        />
        <Button title="Custom Back" onPress={handleGoBack} />
      </View>
    );
  }

  // 3. 返回并传递数据（使用navigate模式） | Go back and pass data (using navigate pattern)
  function SelectionScreen({ route, navigation }) {
    const { onSelect } = route.params;  // 回调函数 | Callback function

    const handleSelect = (item) => {
      // 调用回调传递选择的数据 | Call callback to pass selected data
      if (onSelect) {
        onSelect(item);
      }
      // 返回上一屏幕 | Go back to previous screen
      navigation.goBack();
    };

    const items = ['Option A', 'Option B', 'Option C'];

    return (
      <View>
        {items.map((item, index) => (
          <TouchableOpacity
            key={index}
            onPress={() => handleSelect(item)}
            style={styles.optionItem}
          >
            <Text>{item}</Text>
          </TouchableOpacity>
        ))}
      </View>
    );
  }

  // 调用SelectionScreen的父屏幕 | Parent screen calling SelectionScreen
  function ParentScreen({ navigation }) {
    const [selectedOption, setSelectedOption] = React.useState(null);

    const openSelectionScreen = () => {
      navigation.navigate('Selection', {
        onSelect: (item) => {
          setSelectedOption(item);
          console.log('Selected:', item);
        }
      });
    };

    return (
      <View>
        <Text>Selected: {selectedOption || 'None'}</Text>
        <Button title="Select Option" onPress={openSelectionScreen} />
      </View>
    );
  }

  // 4. 检查是否可以返回 | Check if can go back
  function ConditionalBackScreen({ navigation }) {
    const canGoBack = navigation.canGoBack();

    return (
      <View>
        <Text>Can go back: {canGoBack ? 'Yes' : 'No'}</Text>

        {canGoBack ? (
          <Button title="Go Back" onPress={() => navigation.goBack()} />
        ) : (
          <Text>This is the first screen</Text>
        )}
      </View>
    );
  }

  // 5. 返回多层（返回到特定屏幕） | Go back multiple levels (return to specific screen)
  function DeepNestedScreen({ navigation }) {
    const goBackToHome = () => {
      // 不要用goBack()多次，而是导航到目标屏幕 | Don't use goBack() multiple times, navigate to target screen instead
      navigation.navigate('Home');
    };

    const goBackTwoScreens = () => {
      // 如果确实需要返回N层 | If really need to go back N levels
      navigation.pop(2);  // 仅在Stack Navigator中可用 | Only available in Stack Navigator
    };

    return (
      <View>
        <Button title="Go Back One Screen" onPress={() => navigation.goBack()} />
        <Button title="Go Back to Home" onPress={goBackToHome} />
        <Button title="Go Back 2 Screens" onPress={goBackTwoScreens} />
      </View>
    );
  }

  // 6. 监听goBack事件（beforeRemove） | Listen to goBack event (beforeRemove)
  function FormScreen({ route, navigation }) {
    const [isDirty, setIsDirty] = React.useState(false);

    React.useEffect(() => {
      const beforeRemoveListener = navigation.addListener('beforeRemove', (e) => {
        if (!isDirty) {
          // 如果表单没有修改，允许正常返回 | If form isn't dirty, allow normal back
          return;
        }

        // 阻止默认的返回行为 | Prevent default back behavior
        e.preventDefault();

        Alert.alert(
          'Discard changes?',
          'You have unsaved changes. Discard them and leave the screen?',
          [
            { text: "Don't leave", style: 'cancel' },
            {
              text: 'Discard',
              style: 'destructive',
              onPress: () => navigation.dispatch(e.data.action),
            },
          ]
        );
      });

      return beforeRemoveListener;
    }, [navigation, isDirty]);

    return (
      <View>
        <TextInput
          placeholder="Type something..."
          onChangeText={(text) => setIsDirty(text.length > 0)}
        />
      </View>
    );
  }

  // 7. 返回并刷新上一屏幕 | Go back and refresh previous screen
  function CreateItemScreen({ route, navigation }) {
    const [itemName, setItemName] = React.useState('');

    const createItem = async () => {
      const newItem = await createItemOnServer({ name: itemName });

      // 方式1：使用回调 | Method 1: Use callback
      if (route.params?.onItemCreated) {
        route.params.onItemCreated(newItem);
      }

      // 方式2：导航到列表并传递刷新标志 | Method 2: Navigate to list with refresh flag
      navigation.navigate('ItemList', {
        refresh: true,
        newItemId: newItem.id
      });

      // 方式3：简单返回，列表使用focus事件刷新 | Method 3: Simple goBack, list refreshes with focus event
      // navigation.goBack();
    };

    return (
      <View>
        <TextInput
          value={itemName}
          onChangeText={setItemName}
          placeholder="Item name"
        />
        <Button title="Create & Go Back" onPress={createItem} />
      </View>
    );
  }

  function ItemListScreen({ route, navigation }) {
    const [items, setItems] = React.useState([]);

    // 使用focus事件自动刷新 | Auto refresh with focus event
    React.useEffect(() => {
      const unsubscribe = navigation.addListener('focus', () => {
        // 每次屏幕获得焦点时重新加载 | Reload every time screen gains focus
        loadItems().then(setItems);
      });

      return unsubscribe;
    }, [navigation]);

    // 或检查特定刷新标志 | Or check specific refresh flag
    React.useEffect(() => {
      if (route.params?.refresh) {
        loadItems().then(setItems);
        // 清除刷新标志 | Clear refresh flag
        navigation.setParams({ refresh: false });
      }
    }, [route.params?.refresh]);

    const openCreateScreen = () => {
      navigation.navigate('CreateItem', {
        onItemCreated: (newItem) => {
          // 立即添加到列表 | Add to list immediately
          setItems(prevItems => [...prevItems, newItem]);
        }
      });
    };

    return (
      <View>
        <FlatList
          data={items}
          renderItem={({ item }) => <Text>{item.name}</Text>}
        />
        <Button title="Create Item" onPress={openCreateScreen} />
      </View>
    );
  }

  // 8. 自定义返回动画和手势 | Custom back animation and gestures
  function CustomBackScreen({ navigation }) {
    return (
      <View style={styles.container}>
        <Text>Swipe from left edge to go back (iOS)</Text>
        <Text>Or use hardware back button (Android)</Text>
        <Button title="Go Back" onPress={() => navigation.goBack()} />
      </View>
    );
  }

  // 在Stack.Screen中配置返回手势 | Configure back gestures in Stack.Screen
  <Stack.Screen
    name="CustomBack"
    component={CustomBackScreen}
    options={{
      gestureEnabled: true,  // 启用手势（默认启用） | Enable gestures (enabled by default)
      gestureDirection: 'horizontal',  // 手势方向 | Gesture direction
      // 自定义动画 | Custom animation
      animation: 'slide_from_right',
      // 或完全自定义 | Or fully custom
      cardStyleInterpolator: ({ current, layouts }) => {
        return {
          cardStyle: {
            transform: [
              {
                translateX: current.progress.interpolate({
                  inputRange: [0, 1],
                  outputRange: [layouts.screen.width, 0],
                }),
              },
            ],
          },
        };
      },
    }}
  />
  ```

  **实践检查问题 | Practice Checking Questions:**
  - 在EditScreen中，如果用户点击Android硬件返回按钮，会显示确认对话框吗？| In EditScreen, if the user presses the Android hardware back button, will the confirmation dialog be shown?
    **答案 | Answer:** 是的，如果hasUnsavedChanges为true | Yes, if hasUnsavedChanges is true - BackHandler监听器会拦截硬件返回并调用handleGoBack | BackHandler listener intercepts hardware back and calls handleGoBack

  - 在FormScreen中，beforeRemove监听器什么时候触发？| In FormScreen, when does the beforeRemove listener trigger?
    **答案 | Answer:** 当屏幕即将从堆栈移除时 | When the screen is about to be removed from the stack - 包括goBack、navigate到其他屏幕、或硬件返回按钮 | Including goBack, navigating to other screen, or hardware back button

  **常见误区检查 | Common Misconception Checks:**
  - 我可以多次调用goBack()来返回多层屏幕吗？| Can I call goBack() multiple times to go back multiple screens?
    **答案 | Answer:** 技术上可以但不推荐 | Technically yes but not recommended - 第一次goBack()后，当前组件卸载，后续调用可能导致问题。应使用navigation.pop(n)或navigate到目标屏幕 | After first goBack(), current component unmounts, subsequent calls may cause issues. Should use navigation.pop(n) or navigate to target screen

  - goBack()和navigation.navigate('PreviousScreen')是一样的吗？| Are goBack() and navigation.navigate('PreviousScreen') the same?
    **答案 | Answer:** 不一样 | No - goBack()移除当前屏幕，返回堆栈中的上一个；navigate可能创建新实例或跳转到现有实例，不一定移除当前屏幕 | goBack() removes current screen, returns to previous in stack; navigate may create new instance or jump to existing one, doesn't necessarily remove current screen

### 6. 最佳实践和常见陷阱 | Best Practices and Common Pitfalls (30分钟 | 30 minutes)

- **参数传递的最佳实践 | Best Practices for Parameter Passing**

  **关键原则 | Key Principles:**
  - 保持参数简单：传递原始值或小对象，避免大数据或函数 | Keep parameters simple: pass primitive values or small objects, avoid large data or functions
  - 使用TypeScript定义参数类型，提高类型安全 | Use TypeScript to define parameter types, improving type safety
  - 对于大数据或动态数据，传递ID然后在目标屏幕获取 | For large or dynamic data, pass IDs then fetch in target screen
  - 提供默认值处理可选参数，避免undefined错误 | Provide default values for optional parameters, avoiding undefined errors
  - 不要传递函数；使用回调模式、navigation events或全局状态 | Don't pass functions; use callback pattern, navigation events, or global state
  - 验证必需参数，提供友好的错误处理 | Validate required parameters, provide friendly error handling

  **实践验证问题 | Practice Verification Questions:**
  1. 为什么应该避免传递函数作为导航参数？| Why should we avoid passing functions as navigation parameters?
     **答案 | Answer:** 函数不能序列化，会破坏深度链接、状态持久化和时间旅行调试 | Functions cannot be serialized, breaking deep linking, state persistence, and time-travel debugging

  2. 什么时候应该传递完整对象，什么时候只传递ID？| When should we pass complete objects, and when just IDs?
     **答案 | Answer:** 小对象且不会变化时传递对象（即时显示）；大对象或可能更新的数据传递ID（确保最新） | Pass objects for small data that won't change (instant display); pass IDs for large or updateable data (ensure latest)

  3. TypeScript如何帮助参数传递的类型安全？| How does TypeScript help with type safety in parameter passing?
     **答案 | Answer:** 定义参数类型可以在编译时捕获错误，提供自动完成，确保传递和接收的参数一致 | Defining parameter types catches errors at compile time, provides autocomplete, ensures consistency between passed and received parameters

  **代码示例 | Code Examples:**
  ```javascript
  // 最佳实践示例 | Best practices examples

  // 1. 使用TypeScript定义参数类型 | Define parameter types with TypeScript
  type RootStackParamList = {
    Home: undefined;  // 无参数 | No parameters
    Profile: { userId: string };  // 必需参数 | Required parameter
    Article: {
      articleId: number;
      articleTitle?: string;  // 可选参数 | Optional parameter
    };
    Settings: {
      section?: 'general' | 'privacy' | 'notifications';  // 联合类型 | Union type
      theme?: 'light' | 'dark';
    };
  };

  // 在组件中使用类型 | Use types in components
  type ArticleScreenProps = NativeStackScreenProps<RootStackParamList, 'Article'>;

  function ArticleScreen({ route, navigation }: ArticleScreenProps) {
    // TypeScript知道route.params的类型 | TypeScript knows route.params type
    const { articleId, articleTitle } = route.params;
    // articleId是number类型 | articleId is number type
    // articleTitle是string | undefined | articleTitle is string | undefined

    // TypeScript会检查导航调用 | TypeScript checks navigation calls
    navigation.navigate('Profile', { userId: '123' });  // ✅ 正确 | Correct
    // navigation.navigate('Profile', { userId: 123 });  // ❌ 类型错误 | Type error
    // navigation.navigate('Profile');  // ❌ 缺少必需参数 | Missing required parameter
  }

  // 2. 参数验证和默认值 | Parameter validation and default values
  function SafeDetailScreen({ route, navigation }) {
    // 方式1：解构提供默认值 | Method 1: Destructuring with default values
    const {
      itemId,
      title = 'Untitled',
      showActions = true,
      metadata = {}
    } = route.params || {};

    // 方式2：验证必需参数 | Method 2: Validate required parameters
    React.useEffect(() => {
      if (!itemId) {
        Alert.alert(
          'Error',
          'Invalid item ID',
          [{ text: 'OK', onPress: () => navigation.goBack() }]
        );
      }
    }, [itemId, navigation]);

    // 方式3：早期返回处理无效状态 | Method 3: Early return for invalid state
    if (!itemId) {
      return (
        <View style={styles.errorContainer}>
          <Text>Error: Item ID is required</Text>
          <Button title="Go Back" onPress={() => navigation.goBack()} />
        </View>
      );
    }

    return (
      <View>
        <Text>Item: {itemId}</Text>
        <Text>Title: {title}</Text>
      </View>
    );
  }

  // 3. 避免传递函数的替代方案 | Alternatives to passing functions
  // ❌ 不推荐：传递函数 | Not recommended: passing functions
  function BadExample({ navigation }) {
    const handleUpdate = (data) => {
      console.log('Updated:', data);
    };

    // 不要这样做！| Don't do this!
    navigation.navigate('Editor', { onUpdate: handleUpdate });
  }

  // ✅ 推荐：使用回调模式 | Recommended: Use callback pattern
  function GoodExample({ navigation }) {
    const [data, setData] = React.useState(null);

    navigation.navigate('Editor', {
      initialData: data,
      // 不传递函数，而是在返回时检查参数 | Don't pass function, check params on return instead
    });

    // 使用focus事件监听更新 | Use focus event to listen for updates
    React.useEffect(() => {
      const unsubscribe = navigation.addListener('focus', () => {
        if (route.params?.updatedData) {
          setData(route.params.updatedData);
          navigation.setParams({ updatedData: undefined });  // 清除 | Clear
        }
      });
      return unsubscribe;
    }, [navigation]);
  }

  // ✅ 推荐：使用全局状态（Context, Redux等） | Recommended: Use global state (Context, Redux, etc.)
  const DataContext = React.createContext();

  function BestExample({ navigation }) {
    const { updateData } = React.useContext(DataContext);

    navigation.navigate('Editor');
    // Editor屏幕可以访问相同的updateData函数通过context | Editor screen can access same updateData via context
  }

  // 4. 智能参数传递：ID vs 对象 | Smart parameter passing: ID vs Object
  function SmartListScreen({ navigation }) {
    const navigateToProduct = (product) => {
      // 传递策略：小预览数据 + ID | Passing strategy: small preview data + ID
      navigation.navigate('ProductDetail', {
        productId: product.id,  // 必需：用于获取完整数据 | Required: for fetching complete data
        preview: {  // 可选：用于即时显示 | Optional: for instant display
          name: product.name,
          price: product.price,
          image: product.thumbnail  // 缩略图，不是完整图 | Thumbnail, not full image
        }
      });
    };

    return <ProductList onProductPress={navigateToProduct} />;
  }

  function SmartDetailScreen({ route, navigation }) {
    const { productId, preview } = route.params;
    const [product, setProduct] = React.useState(preview);  // 先显示预览 | Show preview first
    const [loading, setLoading] = React.useState(true);

    React.useEffect(() => {
      // 后台获取完整数据 | Fetch complete data in background
      fetchProductById(productId).then(fullProduct => {
        setProduct(fullProduct);
        setLoading(false);
      });
    }, [productId]);

    return (
      <View>
        {/* 立即显示预览数据 | Show preview data immediately */}
        <Text style={styles.title}>{product?.name || 'Loading...'}</Text>
        <Text style={styles.price}>${product?.price}</Text>

        {/* 加载完整数据后显示更多细节 | Show more details after full data loads */}
        {!loading && (
          <>
            <Text>{product.fullDescription}</Text>
            <Image source={{ uri: product.fullSizeImage }} />
            <Text>Reviews: {product.reviews.length}</Text>
          </>
        )}
      </View>
    );
  }

  // 5. 处理深层嵌套参数 | Handling deeply nested parameters
  function NestedParamsScreen({ route }) {
    // ❌ 不安全：可能抛出错误 | Unsafe: may throw error
    // const city = route.params.user.address.city;

    // ✅ 安全：使用可选链 | Safe: use optional chaining
    const city = route.params?.user?.address?.city || 'Unknown';

    // ✅ 安全：解构带默认值 | Safe: destructuring with defaults
    const {
      user: {
        address: {
          city: cityName = 'Unknown',
          zipCode = '00000'
        } = {}
      } = {}
    } = route.params || {};

    return (
      <View>
        <Text>City: {city}</Text>
        <Text>City Name: {cityName}</Text>
        <Text>Zip: {zipCode}</Text>
      </View>
    );
  }

  // 6. 参数序列化用于深度链接 | Parameter serialization for deep linking
  const linking = {
    prefixes: ['myapp://'],
    config: {
      screens: {
        ProductDetail: {
          path: 'product/:productId',
          parse: {
            productId: (id) => parseInt(id, 10),  // 解析为数字 | Parse to number
          },
          stringify: {
            productId: (id) => String(id),  // 转换为字符串 | Convert to string
          },
        },
        Article: {
          path: 'article/:articleId',
          parse: {
            articleId: Number,
            // 复杂对象需要特殊处理 | Complex objects need special handling
            tags: (tags) => tags ? tags.split(',') : [],
          },
        },
      },
    },
  };
  ```

## 实践项目：联系人管理应用 | Practical Project: Contact Management App

### 目标 | Objective
创建一个完整的联系人管理应用，综合应用本日所学的所有导航参数传递概念：列表到详情导航、参数传递、参数更新、返回导航和数据回传。| Create a complete contact management app that comprehensively applies all navigation parameter passing concepts learned today: list-to-detail navigation, parameter passing, parameter updates, back navigation, and data return.

### 概念应用检查 | Concept Application Check
在开始项目前，请确认对以下概念的理解 | Before starting the project, please confirm understanding of the following concepts:

1. 导航参数和URL查询字符串的主要区别是什么？| What is the main difference between navigation parameters and URL query strings?
   **答案 | Answer:** 导航参数存储在内存中，可以传递任何JavaScript类型；URL查询字符串是字符串格式且在URL中可见 | Navigation parameters are stored in memory and can pass any JavaScript type; URL query strings are string-formatted and visible in URLs

2. 什么时候应该使用navigation.setParams()而不是重新导航？| When should you use navigation.setParams() instead of re-navigating?
   **答案 | Answer:** 当需要更新当前屏幕的参数而不创建新屏幕实例时 | When you need to update current screen's parameters without creating a new screen instance

3. goBack()会保存当前屏幕的状态吗？| Does goBack() save the current screen's state?
   **答案 | Answer:** 否，屏幕从堆栈移除，状态丢失。需要在goBack前使用setParams或全局状态保存 | No, screen is removed from stack and state is lost. Need to save with setParams or global state before goBack

### 步骤 | Steps

#### 步骤1：设置项目结构 | Step 1: Set up project structure
创建以下屏幕结构：
- ContactListScreen：显示联系人列表
- ContactDetailScreen：显示联系人详细信息
- AddContactScreen：添加新联系人
- EditContactScreen：编辑现有联系人

#### 步骤2：实现联系人列表屏幕 | Step 2: Implement contact list screen
- 使用FlatList显示联系人列表
- 每个联系人项可点击，导航到详情页
- 传递联系人ID或完整对象
- 包含"添加联系人"按钮

#### 步骤3：实现详情屏幕 | Step 3: Implement detail screen
- 接收联系人参数
- 显示完整联系人信息
- Header包含"编辑"按钮
- 实现"删除"功能并返回列表

#### 步骤4：实现添加/编辑屏幕 | Step 4: Implement add/edit screens
- 使用表单收集联系人信息
- 使用setParams保存临时草稿
- 实现保存并返回功能
- 处理未保存更改的确认

#### 步骤5：实现数据回传 | Step 5: Implement data return
- 从编辑屏幕返回时更新详情页
- 从添加屏幕返回时刷新列表
- 使用回调或navigation events

### 示例代码 | Example Code
```javascript
"""
联系人管理应用 | Contact Management App
项目展示导航参数传递的综合应用 | Project demonstrating comprehensive navigation parameter passing

本项目演示以下概念的综合应用：| This project demonstrates comprehensive application of:
- 列表到详情导航 | List-to-detail navigation
- 参数传递（ID和对象） | Parameter passing (IDs and objects)
- navigation.setParams()动态更新 | Dynamic updates with navigation.setParams()
- navigation.goBack()返回导航 | Back navigation with navigation.goBack()
- 数据回传和列表刷新 | Data return and list refresh
"""

import React from 'react';
import {
  View,
  Text,
  FlatList,
  TouchableOpacity,
  TextInput,
  Button,
  Alert,
  StyleSheet,
  Image
} from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

// 模拟数据存储 | Mock data store
let contactsData = [
  {
    id: '1',
    name: 'John Doe',
    phone: '+1 234 567 8900',
    email: 'john@example.com',
    avatar: 'https://i.pravatar.cc/150?img=1',
    address: '123 Main St, New York, NY',
    notes: 'Friend from college'
  },
  {
    id: '2',
    name: 'Jane Smith',
    phone: '+1 234 567 8901',
    email: 'jane@example.com',
    avatar: 'https://i.pravatar.cc/150?img=2',
    address: '456 Oak Ave, Boston, MA',
    notes: 'Colleague'
  },
  {
    id: '3',
    name: 'Bob Johnson',
    phone: '+1 234 567 8902',
    email: 'bob@example.com',
    avatar: 'https://i.pravatar.cc/150?img=3',
    address: '789 Pine Rd, Seattle, WA',
    notes: 'Neighbor'
  },
];

// 屏幕1：联系人列表 | Screen 1: Contact List
function ContactListScreen({ route, navigation }) {
  const [contacts, setContacts] = React.useState(contactsData);

  // 监听从其他屏幕返回 | Listen for returns from other screens
  React.useEffect(() => {
    const unsubscribe = navigation.addListener('focus', () => {
      // 刷新联系人列表 | Refresh contact list
      setContacts([...contactsData]);

      // 检查是否有新添加的联系人 | Check for newly added contact
      if (route.params?.newContactId) {
        const newContact = contactsData.find(c => c.id === route.params.newContactId);
        if (newContact) {
          Alert.alert('Success', `${newContact.name} has been added!`);
        }
        // 清除参数 | Clear parameter
        navigation.setParams({ newContactId: undefined });
      }

      // 检查是否有删除的联系人 | Check for deleted contact
      if (route.params?.deletedContactName) {
        Alert.alert('Deleted', `${route.params.deletedContactName} has been removed`);
        navigation.setParams({ deletedContactName: undefined });
      }
    });

    return unsubscribe;
  }, [navigation, route.params]);

  // 配置header按钮 | Configure header button
  React.useLayoutEffect(() => {
    navigation.setOptions({
      headerRight: () => (
        <TouchableOpacity
          onPress={() => navigation.navigate('AddContact', {
            onContactAdded: (newContact) => {
              setContacts(prev => [...prev, newContact]);
            }
          })}
          style={styles.headerButton}
        >
          <Text style={styles.headerButtonText}>+ Add</Text>
        </TouchableOpacity>
      ),
    });
  }, [navigation]);

  const renderContact = ({ item }) => (
    <TouchableOpacity
      style={styles.contactItem}
      onPress={() => {
        // 传递完整对象用于即时显示 | Pass complete object for instant display
        navigation.navigate('ContactDetail', {
          contact: item,
          contactId: item.id  // 也传递ID用于刷新 | Also pass ID for refresh
        });
      }}
    >
      <Image source={{ uri: item.avatar }} style={styles.avatar} />
      <View style={styles.contactInfo}>
        <Text style={styles.contactName}>{item.name}</Text>
        <Text style={styles.contactPhone}>{item.phone}</Text>
      </View>
      <Text style={styles.chevron}>›</Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      <FlatList
        data={contacts}
        keyExtractor={item => item.id}
        renderItem={renderContact}
        ListEmptyComponent={
          <View style={styles.emptyContainer}>
            <Text style={styles.emptyText}>No contacts yet</Text>
            <Text style={styles.emptySubtext}>Tap + Add to create your first contact</Text>
          </View>
        }
      />
    </View>
  );
}

// 屏幕2：联系人详情 | Screen 2: Contact Detail
function ContactDetailScreen({ route, navigation }) {
  const { contact: initialContact, contactId } = route.params;
  const [contact, setContact] = React.useState(initialContact);

  // 配置header按钮 | Configure header buttons
  React.useLayoutEffect(() => {
    navigation.setOptions({
      title: contact.name,
      headerRight: () => (
        <TouchableOpacity
          onPress={() => {
            navigation.navigate('EditContact', {
              contact: contact,
              onContactUpdated: (updatedContact) => {
                // 更新本地状态 | Update local state
                setContact(updatedContact);
                // 更新全局数据 | Update global data
                const index = contactsData.findIndex(c => c.id === updatedContact.id);
                if (index !== -1) {
                  contactsData[index] = updatedContact;
                }
              }
            });
          }}
          style={styles.headerButton}
        >
          <Text style={styles.headerButtonText}>Edit</Text>
        </TouchableOpacity>
      ),
    });
  }, [navigation, contact]);

  const handleDelete = () => {
    Alert.alert(
      'Delete Contact',
      `Are you sure you want to delete ${contact.name}?`,
      [
        { text: 'Cancel', style: 'cancel' },
        {
          text: 'Delete',
          style: 'destructive',
          onPress: () => {
            // 从全局数据中删除 | Delete from global data
            contactsData = contactsData.filter(c => c.id !== contact.id);

            // 返回到列表并传递删除信息 | Go back to list with deletion info
            navigation.navigate('ContactList', {
              deletedContactName: contact.name
            });
          }
        }
      ]
    );
  };

  return (
    <View style={styles.container}>
      <View style={styles.detailHeader}>
        <Image source={{ uri: contact.avatar }} style={styles.detailAvatar} />
        <Text style={styles.detailName}>{contact.name}</Text>
      </View>

      <View style={styles.detailSection}>
        <Text style={styles.detailLabel}>Phone</Text>
        <Text style={styles.detailValue}>{contact.phone}</Text>
      </View>

      <View style={styles.detailSection}>
        <Text style={styles.detailLabel}>Email</Text>
        <Text style={styles.detailValue}>{contact.email}</Text>
      </View>

      <View style={styles.detailSection}>
        <Text style={styles.detailLabel}>Address</Text>
        <Text style={styles.detailValue}>{contact.address}</Text>
      </View>

      <View style={styles.detailSection}>
        <Text style={styles.detailLabel}>Notes</Text>
        <Text style={styles.detailValue}>{contact.notes || 'No notes'}</Text>
      </View>

      <TouchableOpacity style={styles.deleteButton} onPress={handleDelete}>
        <Text style={styles.deleteButtonText}>Delete Contact</Text>
      </TouchableOpacity>
    </View>
  );
}

// 屏幕3：添加联系人 | Screen 3: Add Contact
function AddContactScreen({ route, navigation }) {
  const { onContactAdded } = route.params || {};

  const [formData, setFormData] = React.useState({
    name: '',
    phone: '',
    email: '',
    address: '',
    notes: ''
  });

  const [hasChanges, setHasChanges] = React.useState(false);

  const updateField = (field, value) => {
    setFormData(prev => ({ ...prev, [field]: value }));
    setHasChanges(true);
  };

  const handleSave = () => {
    if (!formData.name.trim()) {
      Alert.alert('Error', 'Name is required');
      return;
    }

    const newContact = {
      id: Date.now().toString(),
      name: formData.name,
      phone: formData.phone,
      email: formData.email,
      address: formData.address,
      notes: formData.notes,
      avatar: `https://i.pravatar.cc/150?img=${Math.floor(Math.random() * 70)}`
    };

    // 添加到全局数据 | Add to global data
    contactsData.push(newContact);

    // 调用回调 | Call callback
    if (onContactAdded) {
      onContactAdded(newContact);
    }

    // 返回到列表并传递新联系人ID | Go back to list with new contact ID
    navigation.navigate('ContactList', {
      newContactId: newContact.id
    });
  };

  const handleCancel = () => {
    if (hasChanges) {
      Alert.alert(
        'Discard changes?',
        'You have unsaved changes. Are you sure you want to cancel?',
        [
          { text: 'Keep Editing', style: 'cancel' },
          { text: 'Discard', style: 'destructive', onPress: () => navigation.goBack() }
        ]
      );
    } else {
      navigation.goBack();
    }
  };

  // 配置header | Configure header
  React.useLayoutEffect(() => {
    navigation.setOptions({
      headerLeft: () => (
        <TouchableOpacity onPress={handleCancel}>
          <Text style={styles.headerButtonText}>Cancel</Text>
        </TouchableOpacity>
      ),
      headerRight: () => (
        <TouchableOpacity onPress={handleSave}>
          <Text style={[styles.headerButtonText, styles.saveButton]}>Save</Text>
        </TouchableOpacity>
      ),
    });
  }, [navigation, formData, hasChanges]);

  return (
    <View style={styles.container}>
      <View style={styles.formField}>
        <Text style={styles.formLabel}>Name *</Text>
        <TextInput
          style={styles.formInput}
          value={formData.name}
          onChangeText={(text) => updateField('name', text)}
          placeholder="Enter name"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Phone</Text>
        <TextInput
          style={styles.formInput}
          value={formData.phone}
          onChangeText={(text) => updateField('phone', text)}
          placeholder="Enter phone number"
          keyboardType="phone-pad"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Email</Text>
        <TextInput
          style={styles.formInput}
          value={formData.email}
          onChangeText={(text) => updateField('email', text)}
          placeholder="Enter email"
          keyboardType="email-address"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Address</Text>
        <TextInput
          style={styles.formInput}
          value={formData.address}
          onChangeText={(text) => updateField('address', text)}
          placeholder="Enter address"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Notes</Text>
        <TextInput
          style={[styles.formInput, styles.formTextArea]}
          value={formData.notes}
          onChangeText={(text) => updateField('notes', text)}
          placeholder="Enter notes"
          multiline
          numberOfLines={4}
        />
      </View>
    </View>
  );
}

// 屏幕4：编辑联系人 | Screen 4: Edit Contact
function EditContactScreen({ route, navigation }) {
  const { contact, onContactUpdated } = route.params;

  const [formData, setFormData] = React.useState({
    name: contact.name,
    phone: contact.phone,
    email: contact.email,
    address: contact.address,
    notes: contact.notes
  });

  const [hasChanges, setHasChanges] = React.useState(false);

  const updateField = (field, value) => {
    setFormData(prev => ({ ...prev, [field]: value }));
    setHasChanges(true);
  };

  const handleSave = () => {
    if (!formData.name.trim()) {
      Alert.alert('Error', 'Name is required');
      return;
    }

    const updatedContact = {
      ...contact,
      ...formData
    };

    // 调用回调更新详情屏幕 | Call callback to update detail screen
    if (onContactUpdated) {
      onContactUpdated(updatedContact);
    }

    Alert.alert('Success', 'Contact updated', [
      { text: 'OK', onPress: () => navigation.goBack() }
    ]);
  };

  const handleCancel = () => {
    if (hasChanges) {
      Alert.alert(
        'Discard changes?',
        'You have unsaved changes. Are you sure you want to cancel?',
        [
          { text: 'Keep Editing', style: 'cancel' },
          { text: 'Discard', style: 'destructive', onPress: () => navigation.goBack() }
        ]
      );
    } else {
      navigation.goBack();
    }
  };

  React.useLayoutEffect(() => {
    navigation.setOptions({
      title: 'Edit Contact',
      headerLeft: () => (
        <TouchableOpacity onPress={handleCancel}>
          <Text style={styles.headerButtonText}>Cancel</Text>
        </TouchableOpacity>
      ),
      headerRight: () => (
        <TouchableOpacity onPress={handleSave}>
          <Text style={[styles.headerButtonText, styles.saveButton]}>Save</Text>
        </TouchableOpacity>
      ),
    });
  }, [navigation, formData, hasChanges]);

  return (
    <View style={styles.container}>
      <View style={styles.formField}>
        <Text style={styles.formLabel}>Name *</Text>
        <TextInput
          style={styles.formInput}
          value={formData.name}
          onChangeText={(text) => updateField('name', text)}
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Phone</Text>
        <TextInput
          style={styles.formInput}
          value={formData.phone}
          onChangeText={(text) => updateField('phone', text)}
          keyboardType="phone-pad"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Email</Text>
        <TextInput
          style={styles.formInput}
          value={formData.email}
          onChangeText={(text) => updateField('email', text)}
          keyboardType="email-address"
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Address</Text>
        <TextInput
          style={styles.formInput}
          value={formData.address}
          onChangeText={(text) => updateField('address', text)}
        />
      </View>

      <View style={styles.formField}>
        <Text style={styles.formLabel}>Notes</Text>
        <TextInput
          style={[styles.formInput, styles.formTextArea]}
          value={formData.notes}
          onChangeText={(text) => updateField('notes', text)}
          multiline
          numberOfLines={4}
        />
      </View>
    </View>
  );
}

// 导航器设置 | Navigator setup
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="ContactList"
        screenOptions={{
          headerStyle: { backgroundColor: '#007AFF' },
          headerTintColor: '#fff',
          headerTitleStyle: { fontWeight: 'bold' },
        }}
      >
        <Stack.Screen
          name="ContactList"
          component={ContactListScreen}
          options={{ title: 'Contacts' }}
        />
        <Stack.Screen
          name="ContactDetail"
          component={ContactDetailScreen}
        />
        <Stack.Screen
          name="AddContact"
          component={AddContactScreen}
          options={{ title: 'New Contact' }}
        />
        <Stack.Screen
          name="EditContact"
          component={EditContactScreen}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// 样式 | Styles
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  headerButton: {
    paddingHorizontal: 15,
  },
  headerButtonText: {
    color: '#fff',
    fontSize: 16,
  },
  saveButton: {
    fontWeight: 'bold',
  },
  contactItem: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#fff',
    padding: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  avatar: {
    width: 50,
    height: 50,
    borderRadius: 25,
    marginRight: 15,
  },
  contactInfo: {
    flex: 1,
  },
  contactName: {
    fontSize: 16,
    fontWeight: 'bold',
    marginBottom: 4,
  },
  contactPhone: {
    fontSize: 14,
    color: '#666',
  },
  chevron: {
    fontSize: 24,
    color: '#ccc',
  },
  emptyContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 40,
    marginTop: 100,
  },
  emptyText: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 8,
  },
  emptySubtext: {
    fontSize: 14,
    color: '#666',
  },
  detailHeader: {
    alignItems: 'center',
    padding: 20,
    backgroundColor: '#fff',
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  detailAvatar: {
    width: 100,
    height: 100,
    borderRadius: 50,
    marginBottom: 15,
  },
  detailName: {
    fontSize: 24,
    fontWeight: 'bold',
  },
  detailSection: {
    backgroundColor: '#fff',
    padding: 15,
    marginTop: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#e0e0e0',
  },
  detailLabel: {
    fontSize: 12,
    color: '#666',
    marginBottom: 4,
  },
  detailValue: {
    fontSize: 16,
  },
  deleteButton: {
    backgroundColor: '#ff3b30',
    padding: 15,
    margin: 20,
    borderRadius: 8,
    alignItems: 'center',
  },
  deleteButtonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: 'bold',
  },
  formField: {
    backgroundColor: '#fff',
    padding: 15,
    marginBottom: 1,
  },
  formLabel: {
    fontSize: 12,
    color: '#666',
    marginBottom: 8,
  },
  formInput: {
    fontSize: 16,
    padding: 10,
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 5,
  },
  formTextArea: {
    height: 100,
    textAlignVertical: 'top',
  },
});
```

### 项目完成检查 | Project Completion Check
1. 项目是否正确应用了列表到详情的导航模式？| Does the project correctly apply the list-to-detail navigation pattern?
2. 参数传递是否使用了合适的策略（ID vs 完整对象）？| Does parameter passing use appropriate strategies (ID vs complete object)?
3. 是否实现了数据回传和列表刷新功能？| Is data return and list refresh functionality implemented?
4. 返回导航是否处理了未保存更改的确认？| Does back navigation handle unsaved changes confirmation?
5. 代码是否体现了参数传递的最佳实践？| Does the code reflect best practices in parameter passing?

## 扩展练习 | Extension Exercises

### 概念深化练习 | Concept Deepening Exercises

1. **参数类型安全练习 | Parameter Type Safety Exercise**
   - **练习描述 | Exercise Description:** 使用TypeScript为联系人应用的所有导航参数定义类型，确保类型安全
   - **概念检查 | Concept Check:** TypeScript如何帮助捕获参数传递中的类型错误？| How does TypeScript help catch type errors in parameter passing?
   - **学习目标 | Learning Objective:** 掌握在React Navigation中使用TypeScript定义和使用类型安全的参数

2. **深度链接集成练习 | Deep Linking Integration Exercise**
   - **练习描述 | Exercise Description:** 为联系人应用配置深度链接，使其可以通过URL打开特定联系人详情
   - **概念检查 | Concept Check:** 深度链接中的参数需要序列化吗？| Do parameters in deep links need serialization?
   - **学习目标 | Learning Objective:** 理解导航参数和URL参数的转换关系

3. **性能优化练习 | Performance Optimization Exercise**
   - **练习描述 | Exercise Description:** 优化联系人列表的渲染性能，特别是处理大量联系人（1000+）时的导航
   - **概念检查 | Concept Check:** 为什么应该使用useCallback包裹导航函数？| Why should navigation functions be wrapped with useCallback?
   - **学习目标 | Learning Objective:** 学习在列表导航场景中的性能优化技巧

4. **状态同步练习 | State Synchronization Exercise**
   - **练习描述 | Exercise Description:** 实现全局状态管理（使用Context或Redux），避免通过参数传递大量数据
   - **概念检查 | Concept Check:** 什么时候应该使用全局状态而不是导航参数？| When should you use global state instead of navigation parameters?
   - **学习目标 | Learning Objective:** 理解导航参数和全局状态管理的适用场景

5. **复杂数据流练习 | Complex Data Flow Exercise**
   - **练习描述 | Exercise Description:** 在联系人应用中添加"分组"功能，实现：列表 → 分组 → 分组内联系人 → 详情的多层导航和数据传递
   - **概念检查 | Concept Check:** 在多层导航中如何高效传递和更新数据？| How to efficiently pass and update data in multi-level navigation?
   - **学习目标 | Learning Objective:** 掌握复杂导航层次中的数据流管理

6. **错误处理练习 | Error Handling Exercise**
   - **练习描述 | Exercise Description:** 为所有参数访问添加完善的错误处理和验证，处理缺失或无效参数的情况
   - **概念检查 | Concept Check:** 如何优雅地处理缺失的必需参数？| How to gracefully handle missing required parameters?
   - **学习目标 | Learning Objective:** 学习健壮的参数验证和错误处理模式

7. **导航历史管理练习 | Navigation History Management Exercise**
   - **练习描述 | Exercise Description:** 实现"最近查看的联系人"功能，追踪用户的导航历史
   - **概念检查 | Concept Check:** 如何在不影响正常导航的情况下追踪导航历史？| How to track navigation history without affecting normal navigation?
   - **学习目标 | Learning Objective:** 理解导航事件和状态管理的结合使用

## 学习资源 | Learning Resources
- [React Navigation - Passing Parameters](https://reactnavigation.org/docs/params)
- [React Navigation - Navigating Between Screens](https://reactnavigation.org/docs/navigating)
- [React Navigation - Moving Between Screens](https://reactnavigation.org/docs/navigation-prop)
- [TypeScript with React Navigation](https://reactnavigation.org/docs/typescript)
- [Navigation Lifecycle](https://reactnavigation.org/docs/navigation-lifecycle)

---

✅ **完成检查清单 | Completion Checklist**
- [ ] 理解导航参数与URL查询字符串的区别 | Understand difference between navigation parameters and URL query strings
- [ ] 能够使用navigation.navigate()传递各种类型的参数 | Can pass various types of parameters using navigation.navigate()
- [ ] 掌握在目标屏幕中访问和使用route.params | Master accessing and using route.params in target screens
- [ ] 能够使用navigation.setParams()动态更新参数 | Can dynamically update parameters using navigation.setParams()
- [ ] 理解参数浅合并的行为和注意事项 | Understand shallow merge behavior of parameters and considerations
- [ ] 实现了列表到详情的标准导航模式 | Implemented standard list-to-detail navigation pattern
- [ ] 能够使用navigation.goBack()并处理数据回传 | Can use navigation.goBack() and handle data return
- [ ] 理解参数传递的最佳实践（ID vs 对象、避免函数等） | Understand best practices for parameter passing (ID vs object, avoid functions, etc.)
- [ ] 完成了联系人管理应用实践项目 | Completed contact management app practical project
- [ ] 能够处理复杂的多屏幕数据流和状态同步 | Can handle complex multi-screen data flow and state synchronization

**概念掌握验证 | Concept Mastery Verification:**
在标记完成前，请确保能够正确回答本日所有CCQs，并能够向他人清晰解释导航参数传递的核心概念、使用场景和最佳实践。尝试不看代码示例，独立实现一个包含列表、详情、编辑和添加功能的多屏幕应用。| Before marking as complete, ensure you can correctly answer all CCQs from today and clearly explain core concepts, use cases, and best practices of navigation parameter passing to others. Try to independently implement a multi-screen app with list, detail, edit, and add functionality without looking at code examples.
