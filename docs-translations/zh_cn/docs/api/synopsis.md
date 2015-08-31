# 概览

All of [node.js's built-in modules](http://nodejs.org/api/) are available in
Electron, and third-party node modules are fully supported too (including the
[native modules](../tutorial/using-native-node-modules.md)).

Electron also provides some extra built-in modules for developing native
desktop applications. Some modules are only available on the main process, some
are only available on the renderer process, and some can be used on both processes.
The basic rule is: if a module is GUI or low-level system related, then it should
be only available on the main process. You need to be familiar with the concept of
[main process vs. renderer process](../tutorial/quick-start.md#the-main-process)
scripts to be able to use those modules.

主进程脚本和普通的`node.js`别无二致：

```javascript
var app = require('app');
var BrowserWindow = require('browser-window');

var window = null;

app.on('ready', function() {
  window = new BrowserWindow({width: 800, height: 600});
  window.loadUrl('https://github.com');
});
```

页面脚本(`web page`)除了拥有额外的调用node模块的能力外，和普通的网页也没有什么不同：

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      var remote = require('remote');
      console.log(remote.require('app').getVersion());
    </script>
  </body>
</html>
```

运行app，请阅读[Run your app](../tutorial/quick-start.md#run-your-app).
