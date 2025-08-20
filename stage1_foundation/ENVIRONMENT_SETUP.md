# React Native 开发环境搭建指南

## 🎯 环境搭建目标

为React Native开发配置完整的开发环境，支持iOS和Android平台的开发和调试。

## 📋 系统要求

### macOS (推荐，支持iOS和Android开发)
- macOS 10.15 (Catalina) 或更高版本
- Xcode 12 或更高版本  
- 至少8GB内存，推荐16GB
- 至少50GB可用存储空间

### Windows (仅支持Android开发)
- Windows 10 或更高版本
- 至少8GB内存，推荐16GB
- 至少30GB可用存储空间

### Linux (仅支持Android开发)  
- Ubuntu 18.04 LTS 或更高版本
- 至少8GB内存，推荐16GB
- 至少30GB可用存储空间

---

## 🛠️ 核心工具安装

### 1. Node.js 安装
React Native需要Node.js 16或更高版本。

#### macOS/Linux
```bash
# 使用nvm安装和管理Node.js版本
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# 重启终端或运行
source ~/.bashrc

# 安装最新LTS版本Node.js
nvm install --lts
nvm use --lts

# 验证安装
node --version  # 应显示 v18.x.x 或更高
npm --version   # 应显示 9.x.x 或更高
```

#### Windows
1. 访问 [Node.js官网](https://nodejs.org/)
2. 下载LTS版本安装包
3. 运行安装程序，按默认设置安装
4. 在命令提示符中验证安装：
```cmd
node --version
npm --version
```

### 2. React Native CLI 安装
```bash
# 全局安装React Native CLI
npm install -g react-native-cli

# 或使用npx (推荐)
# npx react-native --version
```

### 3. 代码编辑器推荐
推荐使用 **Visual Studio Code**，并安装以下扩展：
- React Native Tools
- ES7+ React/Redux/React-Native snippets
- Prettier - Code formatter
- ESLint
- Auto Rename Tag
- Bracket Pair Colorizer
- GitLens

---

## 📱 Android 开发环境

### 1. Java Development Kit (JDK)
```bash
# macOS (使用Homebrew)
brew install openjdk@11

# 添加到环境变量 (~/.zshrc 或 ~/.bash_profile)
export JAVA_HOME=/usr/local/opt/openjdk@11
export PATH=$JAVA_HOME/bin:$PATH

# Linux
sudo apt update
sudo apt install openjdk-11-jdk

# Windows - 下载Oracle JDK 11或OpenJDK 11
```

### 2. Android Studio 安装
1. 访问 [Android Studio官网](https://developer.android.com/studio)
2. 下载并安装最新版本
3. 首次启动时选择 "Standard" 安装类型
4. 安装过程会自动下载Android SDK

### 3. Android SDK 配置
在Android Studio中：
1. 打开 **SDK Manager** (Tools > SDK Manager)
2. 在 **SDK Platforms** 选项卡中，确保已安装：
   - Android 13 (API Level 33)
   - Android 12 (API Level 31) 
   - Android 11 (API Level 30)
3. 在 **SDK Tools** 选项卡中，确保已安装：
   - Android SDK Build-Tools
   - Android Emulator
   - Android SDK Platform-Tools
   - Intel x86 Emulator Accelerator (HAXM installer)

### 4. 环境变量设置
将以下内容添加到 `~/.zshrc` 或 `~/.bash_profile` (macOS/Linux) 或系统环境变量 (Windows)：

```bash
# Android 环境变量
export ANDROID_HOME=$HOME/Library/Android/sdk  # macOS
# export ANDROID_HOME=$HOME/Android/Sdk        # Linux  
# Windows: ANDROID_HOME=c:\\Users\\USERNAME\\AppData\\Local\\Android\\Sdk

export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin
```

重新加载环境变量：
```bash
# macOS/Linux
source ~/.zshrc  # 或 source ~/.bash_profile

# 验证配置
adb --version
emulator -version
```

### 5. Android 模拟器设置
1. 在Android Studio中打开 **AVD Manager**
2. 点击 **Create Virtual Device**
3. 选择设备类型 (推荐Pixel 4)
4. 选择系统镜像 (推荐API Level 30+)
5. 配置AVD设置，点击 **Finish**

---

## 🍎 iOS 开发环境 (仅macOS)

### 1. Xcode 安装
1. 从App Store安装Xcode (免费)
2. 安装完成后，启动Xcode并同意许可协议
3. 安装命令行工具：
```bash
xcode-select --install
```

### 2. CocoaPods 安装
```bash
# 安装CocoaPods (iOS依赖管理工具)
sudo gem install cocoapods

# 设置CocoaPods
pod setup

# 验证安装
pod --version
```

### 3. iOS 模拟器设置
1. 打开Xcode
2. 选择 **Window > Devices and Simulators**
3. 在 **Simulators** 选项卡中，确保有可用的iOS模拟器
4. 推荐安装iPhone 14和iPad模拟器

---

## ✅ 环境验证

### 1. 创建测试项目
```bash
# 创建新的React Native项目
npx react-native init RNTestProject

# 进入项目目录
cd RNTestProject
```

### 2. 运行Android版本
```bash
# 启动Android模拟器
emulator -avd YOUR_AVD_NAME

# 在新终端窗口中运行项目
npx react-native run-android
```

### 3. 运行iOS版本 (仅macOS)
```bash
# 运行iOS版本
npx react-native run-ios

# 或指定特定设备
npx react-native run-ios --simulator="iPhone 14"
```

### 4. 验证热重载
1. 在模拟器中摇动设备 (Ctrl+M 在Android，Cmd+D 在iOS)
2. 选择 **Enable Hot Reloading**
3. 修改 `App.js` 文件中的文本
4. 保存文件，应用应自动更新

---

## 🐛 常见问题解决

### Android 相关问题

#### 问题1: `ANDROID_HOME` 未设置
**解决方案**：
```bash
# 找到Android SDK路径
~/Library/Android/sdk  # macOS默认路径

# 添加到环境变量文件
export ANDROID_HOME=~/Library/Android/sdk
```

#### 问题2: 模拟器启动失败
**解决方案**：
1. 检查HAXM是否正确安装
2. 在BIOS中启用虚拟化技术
3. 尝试创建新的AVD

#### 问题3: ADB连接问题
**解决方案**：
```bash
# 重启ADB服务
adb kill-server
adb start-server

# 检查设备连接
adb devices
```

### iOS 相关问题 (macOS)

#### 问题1: CocoaPods安装失败
**解决方案**：
```bash
# 更新Ruby gems
sudo gem update --system

# 使用Homebrew安装CocoaPods
brew install cocoapods
```

#### 问题2: iOS构建失败
**解决方案**：
1. 清理项目：
```bash
cd ios
pod deintegrate
pod install
cd ..
npx react-native run-ios
```

#### 问题3: 模拟器无法启动
**解决方案**：
1. 重启Xcode
2. 删除并重新创建模拟器
3. 重启macOS

### 通用问题

#### 问题1: Metro服务器端口冲突
**解决方案**：
```bash
# 杀死占用8081端口的进程
lsof -ti:8081 | xargs kill -9

# 或指定其他端口启动
npx react-native start --port=8082
```

#### 问题2: Node.js权限问题
**解决方案**：
```bash
# 配置npm全局安装路径
npm config set prefix '~/.npm-global'

# 添加到PATH
export PATH=~/.npm-global/bin:$PATH
```

---

## 🔧 开发工具推荐

### 1. 调试工具
- **Flipper**: Facebook官方调试工具
- **React DevTools**: React组件调试
- **Redux DevTools**: 状态管理调试

### 2. 性能监控
- **React Native Performance**: 性能分析
- **Flipper Profiler**: 内存和CPU监控

### 3. 代码质量
- **ESLint**: JavaScript代码检查
- **Prettier**: 代码格式化
- **TypeScript**: 类型检查

### 4. 测试框架
- **Jest**: 单元测试框架
- **React Native Testing Library**: 组件测试
- **Detox**: E2E测试框架

---

## 📚 下一步行动

环境搭建完成后，建议按以下顺序进行：

1. **验证环境** ✅
   - [ ] Android开发环境正常工作
   - [ ] iOS开发环境正常工作 (macOS)
   - [ ] 热重载功能正常

2. **熟悉工具** 📱
   - [ ] 掌握模拟器基本操作
   - [ ] 了解调试工具使用
   - [ ] 配置代码编辑器

3. **开始学习** 🎯
   - [ ] 阅读React Native官方文档
   - [ ] 完成Day 2的第一个应用创建
   - [ ] 加入学习社区和论坛

---

## 🆘 获取帮助

如果遇到环境搭建问题，可以寻求以下资源：

- **官方文档**: [React Native环境搭建](https://reactnative.dev/docs/environment-setup)
- **社区论坛**: [React Native Community](https://github.com/react-native-community)
- **Stack Overflow**: 搜索具体错误信息
- **GitHub Issues**: 查看相关项目的issue

---

**恭喜！🎉 完成环境搭建后，你就可以开始React Native的学习之旅了！**

记住：
- 保持工具和SDK的更新
- 定期备份重要配置
- 遇到问题先查文档，再寻求帮助

**下一步**: 前往 `stage1_foundation/week1_basics/day2_first_app/` 开始创建你的第一个React Native应用！