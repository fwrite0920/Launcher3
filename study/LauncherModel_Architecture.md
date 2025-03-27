# LauncherModel 架构概述

## 基本结构
`LauncherModel` 是 Launcher3 的核心组件，继承自 `BroadcastReceiver` 并实现了 `LauncherAppsCompat.OnAppsChangedCallbackCompat` 接口。它管理启动器的数据模型，包括应用程序、小部件和工作区项目。

## 主要职责
- 管理应用程序和工作区数据
- 加载和绑定工作区项目和应用程序
- 处理包变更（安装、卸载、更新）
- 管理启动器项目的数据库操作
- 协调数据与UI组件的绑定

## 主要组件

### 数据结构
- **Workspace Items**: `sBgWorkspaceItems` - 存储所有工作区项目
- **App Widgets**: `sBgAppWidgets` - 存储所有应用小部件
- **Folders**: `sBgFolders` - 存储所有文件夹
- **Workspace Screens**: `sBgWorkspaceScreens` - 存储工作区屏幕ID
- **Item ID Map**: `sBgItemsIdMap` - 将项目ID映射到ItemInfo对象

### 线程管理
- **Worker Thread**: `sWorkerThread` - 用于加载操作的后台线程
- **Handler**: `sWorker` - 工作线程的处理器
- **DeferredHandler**: `mHandler` - 用于将操作延迟到适当的线程

### 模型组件
- **AllAppsList**: `mBgAllAppsList` - 管理所有应用程序的列表
- **WidgetsModel**: `mBgWidgetsModel` - 管理可用的小部件
- **DeepShortcutMap**: `mBgDeepShortcutMap` - 将组件映射到深层快捷方式

### 加载和绑定
- **LoaderTask**: 负责从数据库加载数据的内部类
- **Callbacks Interface**: 用于与UI组件通信的接口

### 包管理
- **PackageUpdatedTask**: 处理包的添加、移除和更新
- **ShortcutsChangedTask**: 处理快捷方式变更

## 同步
- 使用多个锁（`mLock`, `sBgLock`）进行线程同步
- 小心协调UI和后台加载线程之间的关系

## 数据库交互
- 提供添加、更新和移除数据库项目的方法
- 同步内存数据结构和数据库状态 