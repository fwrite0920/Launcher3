# LauncherModel 关键函数分析

## 初始化函数

### `initialize(Callbacks callbacks)`
- 模型初始化的入口点
- 设置UI交互的回调
- 准备模型以加载数据

### `startLoader(int synchronousBindPage)`
- 启动加载过程
- 创建并启动新的LoaderTask
- 接受一个可选参数指定要同步绑定的页面（用于更快的可见性）

### `resetLoadedState(boolean resetAllAppsLoaded, boolean resetWorkspaceLoaded)`
- 重置状态标志以控制加载顺序
- 用于强制重新加载工作区或应用程序

## LoaderTask 函数

### `loadWorkspace()`
- 从数据库加载工作区项目
- 填充静态数据结构：
  - `sBgWorkspaceItems`: 所有工作区项目
  - `sBgAppWidgets`: 所有应用小部件
  - `sBgFolders`: 所有文件夹
  - `sBgWorkspaceScreens`: 所有工作区屏幕

### `bindWorkspace(int synchronizeBindPage)`
- 将加载的工作区项目绑定到UI
- 按当前屏幕对项目进行分组
- 在屏幕内按空间位置排序项目
- 使用回调更新UI项目

### `loadAllApps()`
- 加载所有已安装的应用程序
- 根据可见性和可用性过滤应用程序
- 填充 `mBgAllAppsList`

### `bindAllApps()`
- 将所有应用程序绑定到UI
- 使用回调更新所有应用视图

## 包管理函数

### `onPackageAdded(String packageName, UserHandleCompat user)`
- 处理新包安装
- 创建带有OP_ADD操作的PackageUpdatedTask

### `onPackageRemoved(String packageName, UserHandleCompat user)`
- 处理包卸载
- 创建带有OP_REMOVE操作的PackageUpdatedTask

### `onPackageChanged(String packageName, UserHandleCompat user)`
- 处理包更新
- 创建带有OP_UPDATE操作的PackageUpdatedTask

### `PackageUpdatedTask.run()`
- 处理包变更的核心函数
- 基于操作类型（添加、移除、更新）的不同行为
- 更新模型数据并通过回调触发UI更新

## 数据库管理函数

### `addItemToDatabase(Context context, ItemInfo item, long container, long screenId, int cellX, int cellY)`
- 将新项目添加到数据库
- 更新内存数据结构
- 从多个地方调用的静态方法

### `deleteItemFromDatabase(Context context, ItemInfo item)`
- 从数据库中移除项目
- 更新内存数据结构
- 当项目被移除时调用的静态方法

### `updateItemInDatabase(Context context, ItemInfo item)`
- 更新数据库中的现有项目
- 当项目属性变更时调用

### `moveItemInDatabase(Context context, ItemInfo item, long container, long screenId, int cellX, int cellY)`
- 更新数据库中的项目位置
- 当工作区上的项目被移动时调用

## 实用函数

### `updateItemArrays(ItemInfo item, long itemId, StackTraceElement[] stackTrace)`
- 用项目变更更新静态数据结构
- 确保内存模型的一致性
- 各种验证检查

### `findSpaceForItem(Context context, ArrayList<Long> workspaceScreens, ArrayList<Long> addedWorkspaceScreensFinal, int spanX, int spanY)`
- 为新项目查找可用空间
- 如果需要则创建新屏幕
- 返回屏幕ID和单元格坐标

### `isValidPackageActivity(Context context, ComponentName cn, UserHandleCompat user)`
- 检查组件是否有效和可用
- 用于过滤无效的快捷方式和应用程序

## 线程和同步函数

### `runOnMainThread(Runnable r)`
- 确保任务在主线程上运行
- 如果需要，使用处理器发布到主线程

### `runOnWorkerThread(Runnable r)`
- 确保任务在工作线程上运行
- 使用工作处理器发布到后台线程

## 回调通信

### `getCallback()`
- 返回当前的Callbacks接口
- 用于向UI发送更新

### `Callbacks Interface Methods`
- `bindItems`: 将工作区项目绑定到UI
- `bindScreens`: 绑定工作区屏幕
- `bindAllApplications`: 将所有应用绑定到UI
- `bindAppsAdded`, `bindAppsUpdated`: 处理应用变更
- 以及用于UI绑定不同方面的许多其他方法 