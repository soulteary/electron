# 使用原生Node模块

Electron支持原生的Node模块， but since Electron is
using a different V8 version from official Node, you have to manually specify
the location of Electron's headers when building native modules.

## Native Node module compatibility

Since Node v0.11.x there were vital changes in the V8 API. So generally all
native modules written for Node v0.10.x wouldn't work for newer Node or io.js versions. And
because Electron internally uses __io.js v3.1.0__, it carries with the same
problem.

To solve this, you should use modules that support Node v0.11.x or later,
[many modules](https://www.npmjs.org/browse/depended/nan) do support both now.
For old modules that only support Node v0.10.x, you should use the
[nan](https://github.com/rvagg/nan) module to port it to v0.11.x or later versions of Node or io.js.

## How to install native modules

### The Easy Way

The most straightforward way to rebuild native modules is via the 
[`electron-rebuild`](https://github.com/paulcbetts/electron-rebuild) package, 
which handles the manual steps of downloading headers and building native modules:

```sh
npm install --save-dev electron-rebuild

# Every time you run npm install, run this too
./node_modules/.bin/electron-rebuild
```

### The node-gyp way

To build Node modules with headers of Electron, you need to tell `node-gyp`
where to download headers and which version to use:

```bash
$ cd /path-to-module/
$ HOME=~/.electron-gyp node-gyp rebuild --target=0.29.1 --arch=x64 --dist-url=https://atom.io/download/atom-shell
```

The `HOME=~/.electron-gyp` changes where to find development headers. The
`--target=0.29.1` is version of Electron. The `--dist-url=...` specifies
where to download the headers. The `--arch=x64` says the module is built for
64bit system.

### The npm way

You can also use `npm` to install modules, the steps are exactly the same with
Node modules, except that you need to setup some environment variables:

```bash
export npm_config_disturl=https://atom.io/download/atom-shell
export npm_config_target=0.29.1
export npm_config_arch=x64
HOME=~/.electron-gyp npm install module-name
```
