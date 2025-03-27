# LauncherModel 类结构

```
LauncherModel
├── Implements
│   ├── BroadcastReceiver
│   └── LauncherAppsCompat.OnAppsChangedCallbackCompat
│
├── Inner Classes
│   ├── Callbacks (Interface)
│   │   └── 用于UI交互的方法
│   │
│   ├── ItemInfoFilter (Interface)
│   │   └── filterItem() 方法
│   │
│   ├── LoaderTask
│   │   ├── Implements: Runnable
│   │   └── 方法: loadWorkspace(), bindWorkspace(), loadAllApps() 等
│   │
│   ├── PackageUpdatedTask
│   │   ├── Implements: Runnable
│   │   └── 操作: OP_ADD, OP_UPDATE, OP_REMOVE 等
│   │
│   ├── ShortcutsChangedTask
│   │   └── Implements: Runnable
│   │
│   ├── UserLockStateChangedTask
│   │   └── Implements: Runnable
│   │
│   ├── AppsAvailabilityCheck
│   │   └── Extends: BroadcastReceiver
│   │
│   └── DeferredMainThreadExecutor
│       └── Implements: Executor
│
├── Major Static Fields
│   ├── sBgLock (Object)
│   ├── sBgWorkspaceItems (ArrayList<ItemInfo>)
│   ├── sBgAppWidgets (ArrayList<LauncherAppWidgetInfo>)
│   ├── sBgFolders (LongArrayMap<FolderInfo>)
│   ├── sBgWorkspaceScreens (ArrayList<Long>)
│   ├── sBgItemsIdMap (LongArrayMap<ItemInfo>)
│   └── sWorkerThread (HandlerThread)
│
├── Major Instance Fields
│   ├── mApp (LauncherAppState)
│   ├── mBgAllAppsList (AllAppsList)
│   ├── mBgWidgetsModel (WidgetsModel)
│   ├── mCallbacks (WeakReference<Callbacks>)
│   ├── mLock (Object)
│   ├── mHandler (DeferredHandler)
│   └── mLoaderTask (LoaderTask)
│
└── Major Methods
    ├── initialize(Callbacks)
    ├── startLoader(int)
    ├── stopLoader()
    ├── resetLoadedState(boolean, boolean)
    ├── 生命周期方法: onPackageAdded, onPackageRemoved 等
    ├── 数据库方法: addItemToDatabase, deleteItemFromDatabase 等
    └── 工具方法: findSpaceForItem, isValidPackage 等
```

## 相关类

```
ItemInfo (项目的基类)
├── ShortcutInfo (应用图标/快捷方式)
├── FolderInfo (文件夹)
│   └── Folder.FolderIcon (UI组件)
├── LauncherAppWidgetInfo (小部件)
└── AppInfo (所有应用条目)
```

```
AllAppsList
├── data (ArrayList<AppInfo>)
└── 方法
    ├── add(AppInfo)
    ├── clear()
    ├── addPackage(...)
    └── removePackage(...)
```

```
WidgetsModel
├── widgetsMap (MultiHashMap)
└── 方法
    ├── update(...)
    └── getWidgetsForPackage(...)
```

## 关键关系

1. **LauncherModel ↔ Launcher (Activity)**
   - 通过Callbacks接口进行通信
   - Launcher实现Callbacks接口
   - LauncherModel持有对Callbacks的弱引用

2. **LauncherModel → LauncherAppState**
   - LauncherModel由LauncherAppState拥有
   - 访问LauncherAppState以获取共享资源

3. **LauncherModel → Database**
   - 对LauncherProvider数据库执行CRUD操作
   - 管理工作区项目的持久化

4. **LauncherModel → System Services**
   - 与LauncherAppsCompat交互
   - 访问PackageManager和其他系统服务
   - 接收包变更的广播 