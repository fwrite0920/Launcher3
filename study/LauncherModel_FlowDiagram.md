# LauncherModel 流程图

## 初始化和加载流程

```
LauncherModel.initialize(Callbacks)
    |
    ├── resetLoadedState()
    |    └── (重置工作区/所有应用已加载标志)
    |
    └── startLoader()
         |
         └── 创建并启动 LoaderTask
              |
              ├── loadWorkspace()
              |    |
              |    ├── 从数据库加载项目
              |    ├── 填充 sBgWorkspaceItems, sBgAppWidgets, sBgFolders
              |    └── 加载工作区屏幕
              |
              ├── bindWorkspace()
              |    |
              |    ├── bindWorkspaceScreens()
              |    ├── bindWorkspaceItems()
              |    └── (通过 Callbacks 更新 UI)
              |
              ├── loadAllApps()
              |    |
              |    ├── 获取已安装的应用程序
              |    └── 填充 mBgAllAppsList
              |
              ├── bindAllApps()
              |    └── (通过 Callbacks 更新 UI)
              |
              └── loadAndBindDeepShortcuts()
                   └── (更新深层快捷方式映射和 UI)
```

## 包更新流程

```
onReceive(Intent) → 包相关意图
    |
    └── LauncherAppsCompat 回调 (onPackageAdded, onPackageRemoved 等)
         |
         └── 创建带有适当操作的 PackageUpdatedTask
              |
              └── PackageUpdatedTask.run()
                   |
                   ├── OP_ADD: 添加新应用
                   |    └── → bindAppsAdded()
                   |
                   ├── OP_UPDATE: 更新现有应用
                   |    └── → bindAppsUpdated()
                   |
                   ├── OP_REMOVE: 移除应用
                   |    └── → bindWorkspaceComponentsRemoved()
                   |
                   └── 其他操作
                        └── (通过 Callbacks 进行适当的 UI 更新)
```

## 项目管理流程

```
addItemToDatabase(ItemInfo)
    |
    └── 添加到数据库
         |
         └── updateItemInDatabaseHelper()
              |
              ├── 使用 ContentValues 更新数据库
              └── updateItemArrays()
                   └── 更新 sBgItemsIdMap, sBgWorkspaceItems 等
```

## 工作区绑定流程

```
bindWorkspace()
    |
    ├── filterCurrentWorkspaceItems()
    |    └── (按当前屏幕/其他屏幕分组项目)
    |
    ├── sortWorkspaceItemsSpatially()
    |    └── (按屏幕上的位置排序)
    |
    ├── bindWorkspaceScreens()
    |    └── → callbacks.bindScreens()
    |
    ├── bindWorkspaceItems()
    |    |
    |    ├── → callbacks.bindItems()
    |    └── → callbacks.bindAppWidget()
    |
    └── 加载剩余页面
         └── (处理其他屏幕)
```

## 数据绑定流程

```
                     +-------------------------+
                     | LauncherModel (后台数据) |
                     +-------------------------+
                                |
                                | (后台加载)
                                v
 +------------------+     +-------------+     +------------------+
 |    数据库加载     | --> |  LoaderTask  | --> |   内存中的模型   |
 +------------------+     +-------------+     +------------------+
                                |
                                | (通过 Callbacks 绑定)
                                v
                     +-------------------------+
                     |   Launcher UI (主线程)   |
                     +-------------------------+
``` 