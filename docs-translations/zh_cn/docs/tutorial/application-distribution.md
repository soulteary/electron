# 程序部署

使用Electron部署你的app需要将文件夹命名为`app`，并将它放在Electron的资源目录下 (在OS X中为`Electron.app/Contents/Resources/`，Linux和Windows为
`resources/`)，例如：

OS X:

```text
electron/Electron.app/Contents/Resources/app/
├── package.json
├── main.js
└── index.html
```

Windows和Linux:

```text
electron/resources/app
├── package.json
├── main.js
└── index.html
```

接着执行`Electron.app`(Linux为`electron`，Windows为`electron.exe`)，Electron将会作为你的app执行。`electron`目录将会分发给最终用户。

## 将你的app打包成一个文件

Apart from shipping your app by copying all its sources files, you can also
package your app into an [asar](https://github.com/atom/asar) archive 来避免暴露你的应用源代码给用户.

使用`asar` archive 来替换`app`文件夹, 你需要重命名archive文件为`app.asar`, and put it under Electron's resources directory like
below, and Electron will then try read the archive and start from it.

On OS X:

```text
electron/Electron.app/Contents/Resources/
└── app.asar
```

On Windows and Linux:

```text
electron/resources/
└── app.asar
```

More details can be found in [Application packaging](application-packaging.md).

## Rebranding with downloaded binaries

After bundling your app into Electron, you will want to rebrand Electron
before distributing it to users.

### Windows

You can rename `electron.exe` to any name you like, and edit its icon and other
information with tools like [rcedit](https://github.com/atom/rcedit) or
[ResEdit](http://www.resedit.net).

### OS X

You can rename `Electron.app` to any name you want, and you also have to rename
the `CFBundleDisplayName`, `CFBundleIdentifier` and `CFBundleName` fields in
following files:

* `Electron.app/Contents/Info.plist`
* `Electron.app/Contents/Frameworks/Electron Helper.app/Contents/Info.plist`

You can also rename the helper app to avoid showing `Electron Helper` in the
Activity Monitor, but make sure you have renamed the helper app's executable
file's name.

The structure of a renamed app would be like:

```
MyApp.app/Contents
├── Info.plist
├── MacOS/
│   └── MyApp
└── Frameworks/
    ├── MyApp Helper EH.app
    |   ├── Info.plist
    |   └── MacOS/
    |       └── MyApp Helper EH
    ├── MyApp Helper NP.app
    |   ├── Info.plist
    |   └── MacOS/
    |       └── MyApp Helper NP
    └── MyApp Helper.app
        ├── Info.plist
        └── MacOS/
            └── MyApp Helper
```

### Linux

你可以将可执行文件`electron`重命名为任何你喜欢的文件。

## Rebranding by rebuilding Electron from source

It is also possible to rebrand Electron by changing the product name and
building it from source. To do this you need to modify the `atom.gyp` file and
have a clean rebuild.

### grunt-build-atom-shell

Manually checking out Electron's code and rebuilding could be complicated, so
a Grunt task has been created that will handle this automatically:
[grunt-build-atom-shell](https://github.com/paulcbetts/grunt-build-atom-shell).

This task will automatically handle editing the `.gyp` file, building from
source, then rebuilding your app's native Node modules to match the new
executable name.
