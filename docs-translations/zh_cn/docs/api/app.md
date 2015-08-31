# app

`app`模块负责控制应用程序的生命周期。

下面的例子展示了如何在关闭最后一个窗口时退出整个应用程序:

```javascript
var app = require('app');
app.on('window-all-closed', function() {
  app.quit();
});
```

## 事件

`app`对象触发以下事件：

### 事件: 'will-finish-launching'

Emitted when the application has finished basic startup. On Windows and Linux,
the `will-finish-launching` event is the same as the `ready` event; on OS X,
this event represents the `applicationWillFinishLaunching` notification of
`NSApplication`. You would usually set up listeners for the `open-file` and
`open-url` events here, and start the crash reporter and auto updater.

多数情况下，你应该在`ready`事件触发后执行你要做的事情。

### 事件: 'ready'

当Electron完成初始化时触发。

### 事件: 'window-all-closed'

当所有的窗口都被关闭时触发。

这个事件仅在程序尚未退出的时候触发。
如果用户按下 `Cmd + Q`，或开发者主动调用`app.quit()`，Electron将会先尝试关闭所有的窗口并触发 `will-quit`事件，在这种情况下， `window-all-closed` 并不会被触发.

### Event: 'before-quit'

返回值:

* `event` Event

Emitted before the application starts closing its windows.
Calling `event.preventDefault()` will prevent the default behaviour, which is
terminating the application.

### Event: 'will-quit'

返回值:

* `event` Event

Emitted when all windows have been closed and the application will quit.
Calling `event.preventDefault()` will prevent the default behaviour, which is
terminating the application.

See the description of the `window-all-closed` event for the differences between
the `will-quit` and `window-all-closed` events.

### Event: 'quit'

当应用程序退出时被触发。

### Event: 'open-file'

返回值:

* `event` Event
* `path` String

Emitted when the user wants to open a file with the application. The `open-file`
event is usually emitted when the application is already open and the OS wants
to reuse the application to open the file. `open-file` is also emitted when a
file is dropped onto the dock and the application is not yet running. Make sure
to listen for the `open-file` event very early in your application startup to
handle this case (even before the `ready` event is emitted).

You should call `event.preventDefault()` if you want to handle this event.

### Event: 'open-url'

返回:

* `event` Event
* `url` String

Emitted when the user wants to open a URL with the application. The URL scheme
must be registered to be opened by your application.

You should call `event.preventDefault()` if you want to handle this event.

### Event: 'activate-with-no-open-windows'

Emitted when the application is activated while there are no open windows, which
usually happens when the user has closed all of the application's windows and
then clicks on the application's dock icon.

### Event: 'browser-window-blur'

返回:

* `event` Event
* `window` BrowserWindow

Emitted when a [browserWindow](browser-window.md) gets blurred.

### Event: 'browser-window-focus'

Returns:

* `event` Event
* `window` BrowserWindow

Emitted when a [browserWindow](browser-window.md) gets focused.

### Event: 'select-certificate'

Emitted when a client certificate is requested.

Returns:

* `event` Event
* `webContents` [WebContents](browser-window.md#class-webcontents)
* `url` String
* `certificateList` [Objects]
  * `data` PEM encoded data
  * `issuerName` Issuer's Common Name
* `callback` Function

```javascript
app.on('select-certificate', function(event, host, url, list, callback) {
  event.preventDefault();
  callback(list[0]);
})
```

The `url` corresponds to the navigation entry requesting the client certificate
and `callback` needs to be called with an entry filtered from the list. Using
`event.preventDefault()` prevents the application from using the first
certificate from the store.

### Event: 'gpu-process-crashed'

Emitted when the gpu process crashes.

## Methods

The `app` object has the following methods:

**Note** Some methods are only available on specific operating systems and are labeled as such.

### `app.quit()`

Try to close all windows. The `before-quit` event will emitted first. If all
windows are successfully closed, the `will-quit` event will be emitted and by
default the application will terminate.

This method guarantees that all `beforeunload` and `unload` event handlers are
correctly executed. It is possible that a window cancels the quitting by
returning `false` in the `beforeunload` event handler.

### `app.getAppPath()`

Returns the current application directory.

### `app.getPath(name)`

* `name` String

Retrieves a path to a special directory or file associated with `name`. On
failure an `Error` is thrown.

You can request the following paths by the name:

* `home` User's home directory.
* `appData` Per-user application data directory, which by default points to:
  * `%APPDATA%` on Windows
  * `$XDG_CONFIG_HOME` or `~/.config` on Linux
  * `~/Library/Application Support` on OS X
* `userData` The directory for storing your app's configuration files, which by
  default it is the `appData` directory appended with your app's name.
* `cache` Per-user application cache directory, which by default points to:
  * `%APPDATA%` on Windows (which doesn't have a universal cache location)
  * `$XDG_CACHE_HOME` or `~/.cache` on Linux
  * `~/Library/Caches` on OS X
* `userCache` The directory for placing your app's caches, by default it is the
 `cache` directory appended with your app's name.
* `temp` Temporary directory.
* `userDesktop` The current user's Desktop directory.
* `exe` The current executable file.
* `module` The `libchromiumcontent` library.

### `app.setPath(name, path)`

* `name` String
* `path` String

Overrides the `path` to a special directory or file associated with `name`. If
the path specifies a directory that does not exist, the directory will be
created by this method. On failure an `Error` is thrown.

You can only override paths of a `name` defined in `app.getPath`.

By default, web pages's cookies and caches will be stored under the `userData`
directory. If you want to change this location, you have to override the
`userData` path before the `ready` event of the `app` module is emitted.

### `app.getVersion()`

Returns the version of the loaded application. If no version is found in the
application's `package.json` file, the version of the current bundle or
executable is returned.

### `app.getName()`

Returns the current application's name, which is the name in the application's
`package.json` file.

Usually the `name` field of `package.json` is a short lowercased name, according
to the npm modules spec. You should usually also specify a `productName`
field, which is your application's full capitalized name, and which will be
preferred over `name` by Electron.

### `app.resolveProxy(url, callback)`

* `url` URL
* `callback` Function

Resolves the proxy information for `url`. The `callback` will be called with
`callback(proxy)` when the request is performed.

### `app.addRecentDocument(path)`

* `path` String

Adds `path` to the recent documents list.

This list is managed by the OS. On Windows you can visit the list from the task
bar, and on OS X you can visit it from dock menu.

### `app.clearRecentDocuments()`

Clears the recent documents list.

### `app.setUserTasks(tasks)` _Windows_

* `tasks` Array - Array of `Task` objects

Adds `tasks` to the [Tasks][tasks] category of the JumpList on Windows.

`tasks` is an array of `Task` objects in following format:

`Task` Object
* `program` String - Path of the program to execute, usually you should
  specify `process.execPath` which opens the current program.
* `arguments` String - The command line arguments when `program` is
  executed.
* `title` String - The string to be displayed in a JumpList.
* `description` String - Description of this task.
* `iconPath` String - The absolute path to an icon to be displayed in a
  JumpList, which can be an arbitrary resource file that contains an icon. You
  can usually specify `process.execPath` to show the icon of the program.
* `iconIndex` Integer - The icon index in the icon file. If an icon file
  consists of two or more icons, set this value to identify the icon. If an
  icon file consists of one icon, this value is 0.


### `app.commandLine.appendSwitch(switch[, value])`

Append a switch (with optional `value`) to Chromium's command line.

**Note:** This will not affect `process.argv`, and is mainly used by developers
to control some low-level Chromium behaviors.

### `app.commandLine.appendArgument(value)`

Append an argument to Chromium's command line. The argument will be quoted
correctly.

**Note:** This will not affect `process.argv`.

### `app.dock.bounce([type])` _OS X_

* `type` String (optional) - Can be `critical` or `informational`. The default is
 `informational`

When `critical` is passed, the dock icon will bounce until either the
application becomes active or the request is canceled.

When `informational` is passed, the dock icon will bounce for one second.
However, the request remains active until either the application becomes active
or the request is canceled.

Returns an ID representing the request.

### `app.dock.cancelBounce(id)` _OS X_

* `id` Integer

Cancel the bounce of `id`.

### `app.dock.setBadge(text)` _OS X_

* `text` String

Sets the string to be displayed in the dock’s badging area.

### `app.dock.getBadge()` _OS X_

Returns the badge string of the dock.

### `app.dock.hide()` _OS X_

Hides the dock icon.

### `app.dock.show()` _OS X_

Shows the dock icon.

### `app.dock.setMenu(menu)` _OS X_

* `menu` Menu

Sets the application's [dock menu][dock-menu].

[dock-menu]:https://developer.apple.com/library/mac/documentation/Carbon/Conceptual/customizing_docktile/concepts/dockconcepts.html#//apple_ref/doc/uid/TP30000986-CH2-TPXREF103
[tasks]:http://msdn.microsoft.com/en-us/library/windows/desktop/dd378460(v=vs.85).aspx#tasks
