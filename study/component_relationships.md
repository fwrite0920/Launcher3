# Launcher3 核心组件关系图

## 1. 核心组件交互

```
 +----------------+       +----------------+       +----------------+
 |   Launcher     |<----->| LauncherModel  |<----->| LauncherProvider|
 | (主Activity)   |       | (数据模型)     |       | (数据存储)     |
 +-------+--------+       +-------+--------+       +----------------+
         |                        |
         v                        v
 +----------------+       +----------------+
 | LauncherAppState|<---->| IconCache      |
 | (应用状态)     |       | (图标缓存)     |
 +-------+--------+       +----------------+
         |
         v
 +----------------+
 | DeviceProfile  |
 | (设备配置)     |
 +----------------+
```

## 2. 视图层组件层次

```
Launcher (主Activity)
 |
 +-- DragLayer (拖放层)
 |    |
 |    +-- Workspace (工作区)
 |    |    |
 |    |    +-- CellLayout (单个屏幕)
 |    |         |
 |    |         +-- ShortcutAndWidgetContainer
 |    |              |
 |    |              +-- BubbleTextView (应用图标)
 |    |              +-- FolderIcon (文件夹)
 |    |              +-- LauncherAppWidgetHostView (小部件)
 |    |
 |    +-- Hotseat (底部Dock栏)
 |    |
 |    +-- AllAppsContainerView (所有应用视图)
 |    |
 |    +-- WidgetsContainerView (小部件选择器)
 |    |
 |    +-- DropTargetBar (拖放目标栏)
 |
 +-- LauncherStateTransitionAnimation (状态转换动画)
 |
 +-- DragController (拖放控制器)
```

## 3. 模型层类继承关系

```
ItemInfo (桌面项目基类)
 |
 +-- ShortcutInfo (应用快捷方式)
 |
 +-- FolderInfo (文件夹)
 |
 +-- LauncherAppWidgetInfo (小部件)
 |
 +-- PendingAddItemInfo (待添加项目)
```

## 4. 核心数据流

```
系统事件 (包安装/卸载) 
  |
  v
LauncherModel (监听系统事件)
  |
  v
加载/更新数据 
  |
  v
通过Callbacks接口通知Launcher
  |
  v
Launcher更新UI

---

用户交互 (点击/拖动/长按)
  |
  v
Launcher或子视图捕获
  |
  v
DragController (处理拖放)
  |
  v
LauncherModel (更新数据)
  |
  v
LauncherProvider (持久化到数据库)
```

## 5. 状态转换流程

```
Launcher.State 
  |
  +-- WORKSPACE (主屏幕工作区)
  |
  +-- APPS (所有应用视图)
  |
  +-- WIDGETS (小部件选择器)
  |
  +-- WORKSPACE_SPRING_LOADED (拖放目标状态)
  |
  +-- APPS_SPRING_LOADED
  |
  +-- WIDGETS_SPRING_LOADED
```

状态转换通过 `LauncherStateTransitionAnimation` 管理动画和UI更新。 