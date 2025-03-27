# Launcher3 架构说明

Launcher3采用MVC (Model-View-Controller) 架构模式，主要组件如下：

## 1. 模型层 (Model)

主要负责数据的管理和存储：

- **LauncherModel**：核心数据模型，管理内存中的Launcher状态
- **AllAppsList**：管理所有已安装的应用程序列表
- **WidgetsModel**：管理可用的小部件信息
- **ItemInfo及其子类**：表示桌面项目（如应用图标、文件夹和小部件）
- **LauncherProvider**：数据存储的ContentProvider，用于持久化桌面布局
- **IconCache**：管理应用程序图标的缓存

## 2. 视图层 (View)

负责UI展示和用户交互：

- **Launcher**：主Activity，负责显示整个桌面UI
- **Workspace**：管理主屏幕工作区，包含多个屏幕
- **CellLayout**：网格布局，每个工作区屏幕的基本容器
- **Hotseat**：底部常驻区域（Dock栏）
- **AllAppsContainerView**：所有应用程序的容器视图
- **WidgetsContainerView**：小部件选择器视图
- 各种UI组件：如FolderIcon、BubbleTextView、LauncherAppWidgetHostView等

## 3. 控制器层 (Controller)

负责协调模型层和视图层：

- **LauncherAppState**：应用状态管理，连接应用程序和模型层
- **DragController**：管理拖放操作
- **LauncherStateTransitionAnimation**：管理界面状态转换的动画
- **DeviceProfile**：适配不同设备屏幕尺寸和密度

## 4. 主要数据流和交互

- 系统事件（应用安装/卸载等）-> 模型层更新 -> 通知视图层刷新
- 用户交互 -> 视图层捕获 -> 控制器处理 -> 模型层更新 -> 视图层刷新

## 5. 重要子系统

- **拖放系统**：DragController、DragLayer、DropTarget
- **小部件系统**：AppWidgetHost、LauncherAppWidgetHostView
- **数据存储系统**：LauncherProvider、数据库操作 