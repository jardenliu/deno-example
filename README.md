# 浅谈 Deno 
> Node.js 的替代品？`Deno` 是 `node.js` 的作者 `Ryan Dahl` 编写的，这是否意味着 Deno 实际上就是 Node.js 的替代品，我们是否应该开始计划重构冲刺呢？ 如果你想了解更多，请往下看。

## Deno由来

&ensp;&ensp;&ensp;&ensp;Ryan Dahl在2007-2012年一直在维护NodeJS，后来他把Nodejs移交给其他开发者，自己转而研究AI。他研究的主要方向就是机器学习里面的图像着色和超解像技术。

&ensp;&ensp;&ensp;&ensp;提到机器学习和人工智能就不得不提python, Node之父始终不太喜欢Python，久而久之，就想搞一个JavaScript的人工智能开发框架，但是当他再次使用NodeJS时他却发现这个框架已经背离了他的初衷，有一些无法忽视的问题。

2018年的JS开发者会上列出了自己设计NodeJS的十个错误：
+ 没有坚持使用Promise
+ 没有注重安全性
+ 没有从GYP构建系统转到GN
+ 继续使用GYP，没有提供FFI
+ package.json以及依赖了npm
+ 在任何地方都可以require(”somemodule“)
+ package.json 提供了错误的module概念
+ 设计了软件界黑洞node_modules
+ require("module")可以不写.js
+ index.js

从整体设计来看，Deno 的定位不完全是 Nodejs 的替代品，更倾向于统一JS在前后端开发的全流程。

## 简介
&ensp;&ensp;&ensp;&ensp;Deno是基于V8，Rust和Tokio构建的JavaScript/TypeScript的运行时，具备了默认安全设置和出色的开发体验。它内置了 V8 引擎，用来解释 JavaScript。同时，也内置了 tsc 引擎，解释 TypeScript。它使用 Rust 语言开发，由于 Rust 原生支持 WebAssembly，所以它也能直接运行 WebAssembly。它的异步操作不使用 libuv 这个库，而是使用 Rust 语言的 Tokio 库，来实现事件循环（Event Loop）。

#### 功能亮点
+ 安全性，默认没有开启文件、网络、环境的访问权限。
+ 支持TS, 内嵌了tsc，开箱即用。
+ 单执行文件（deno）。
+ 开发实用工具集成，内嵌了单元测试、文件格式化、语法检查器等。
+ 标准的内部模块，均进过审查，可以和deno一起使用。
+ 脚本代码能够打包到一个js的文件中。
+ 兼容浏览器api，完全用JavaScript编写并且不使用全局Deno名称空间的的子集，应该能够在现代Web浏览器中运行而无需更改。
+ 使用Import URLs，不需要NPM。使用ES模块，不支持require。
+ 所有异步操作都使用`Promise`。

#### Deno VS Node

|                    | Node                                     | Deno                 |
| ------------------ | ---------------------------------------- | -------------------- |
| API 引用方式       | 模块导入                                 | 全局对象             |
| 模块系统           | CommonJS & 新版 node 实验性 ES Module    | ES Module 浏览器实现 |
| 安全               | 无安全限制                               | 默认安全             |
| Typescript         | 第三方，如通过 ts-node 支持              | 原生支持             |
| 包管理             | npm + node_modules                       | 原生支持             |
| 异步操作           | 回调                                     | Promise              |
| 包分发             | 中心化 npmjs.com                         | 去中心化 import url  |
| 入口               | package.json 配置                        | import url 直接引入  |
| 打包、测试、格式化 | 第三方如 eslint、gulp、webpack、babel 等 | 原生支持             |



## 安装

Deno可在macOS，Linux和Windows上运行。Deno是单个二进制可执行文件.不需要额外的依赖文件

[deno_install](https://github.com/denoland/deno_install)提供了便捷的脚本来下载和安装二进制文件。

Shell（macOS和Linux）：

```bash
$ curl -fsSL https://deno.land/x/install/install.sh | sh
```

PowerShell（Windows）：

```bash
$ iwr https://deno.land/x/install/install.ps1 -useb | iex
```

[Scoop](https://scoop.sh/)（Windows）：

```bash
$ scoop install deno
```

[Chocolatey](https://chocolatey.org/packages/deno)（Windows）：

```bash
$ choco install deno
```

[brew](https://formulae.brew.sh/formula/deno)（macOS）：

```bash
$ brew install deno
```

使用[Cargo](https://crates.io/crates/deno)（Windows，macOS，Linux）：

```bash
$ cargo install deno
```

Deno二进制文件也可以通过在[deno release](https://github.com/denoland/deno/releases)下载zip文件来手动安装 。把文件解压后可以设计环境变量，使得deno能够正常运行，默认deno的执行路径是 `$HOME/.deno/bin`。

#### 测试安装

尝试在终端运行`deno --version`。如果这将Deno版本打印到控制台，则安装成功。

#### 更新

要更新以前安装的Deno版本，您可以运行：

```bash
$ deno upgrade
```

也可以执行更新的版本

您也可以使用此实用程序来安装特定版本的Deno：

```bash
$ deno upgrade --version 1.0.1
```

## 命令行指令

查看主要帮助的方法有以下三种：

```bash
# 使用help子命令
deno help

# 使用 -h 标志
deno -h

# 使用 --help 长标志
deno --help
```

Deno的CLI是基于子命令的。上面的命令应该显示支持的列表，例如`deno bundle`。要查看的子命令`bundle`的帮助 ，您可以类似地运行以下之一：

```bash
deno help bundle
deno bundle -h
deno bundle --help
```



## 运行

##### 数据源

Deno 可以从多处获取来源：本地文件名、URL、从stdin中读取文件，然后使用“-”中获取脚本。

```bash
deno run main.ts
deno run https://deno.land/std/examples/welcome.ts
cat main.ts | deno run -
```

##### 权限

许多程序使用HTTP请求从Web服务器获取数据。尝试运行官方的示例 `https://deno.land/std/examples/curl.ts`. 代码如下：

```ts
const url = Deno.args[0];
const res = await fetch(url);

const body = new Uint8Array(await res.arrayBuffer());
await Deno.stdout.write(body);
```

就像在浏览器中一样，使用网络标准 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)API进行HTTP调用，读取到数据以后进行输出。

尝试：

```bash
$ deno run https://deno.land/std/examples/curl.ts https://example.com
```

会得到 `Uncaught PermissionDenied: network access to "https://example.com/"`的错误。这是因为deno默认没有开启网络权限。

尝试添加 `--allow-net`标志参数再次运行：

```bash
$ deno run --allow-net=example.com https://deno.land/std/examples/curl.ts https://example.com
```



默认情况下，Deno是关闭访问文件，网络或环境的权限的。要访问安全敏感的区域或功能，需要在命令行上使用授予deno进程的权限。

权限清单如下:

- **-A，--** allow **-all**允许所有权限。这将禁用所有安全性。
- **--allow-env **允许环境访问，例如获取和设置环境变量。
- **--allow-hrtime** 允许高分辨率时间测量。高分辨率时间可用于定时攻击和指纹识别。
- **--allow-net = \ <allow-net >**允许网络访问。您可以指定一个可选的，用逗号分隔的域列表，以提供允许域的允许列表。
- **--allow-plugin**允许加载插件。请注意--allow-plugin是一个不稳定的功能。
- **--allow-read = \ <允许读取>**允许文件系统读取访问。您可以指定一个可选的，用逗号分隔的目录或文件列表，以提供允许的文件系统访问的允许列表。
- **--allow-run**允许运行子进程。请注意，子流程未在沙箱中运行，因此与deno流程没有相同的安全限制。因此，请谨慎使用。
- **--allow-write = \ <允许写>**允许文件系统写访问。您可以指定一个可选的，用逗号分隔的目录或文件列表，以提供允许的文件系统访问的允许列表。



`--allow-read`和`--allow-net`可以单独使用，也可以进行白名单权限的控制。单独使用时默认开启全部权限；指定白名单时，可以进行细粒度的权限控制。如：`--allow-read=/etc` 。多个目标可以使用逗号隔开，`--allow-net=github.com,deno.land`

##### 参数

运行脚本时执行的参数添加到 脚本地址的后面，使用空格分隔：

```bash
$ deno run main.ts a b -c --quiet
// main.ts
console.log(Deno.args); // [ "a", "b", "-c", "--quiet" ]
```

**请注意，在脚本名称之后传递的所有内容都将作为脚本参数传递，而不用作Deno运行时标志。**

```bash
# 会赋予网络访问的权限
deno run --allow-net net_client.ts

# 不会赋予网络访问的权限，会被作为是net_client.ts的参数
deno run net_client.ts --allow-net
```

##### 其他运行时标志

```null
--lock <FILE>                 检查lock文件
--lock-write                  输出一个lock.json的文件
--config <FILE>               读取配置文件，如 tsconfig.json
--importmap <FILE>            读取一个import的Map，尚不稳定的api(例)
--no-remote                   不使用远程模块
--reload=<CACHE_BLOCKLIST>    重新读取远程文件，重新编译（清缓存）
--unstable                    允许使用不稳定api

--cached-only                只使用缓存
--inspect=<HOST:PORT>        启动审查服务
--inspect-brk=<HOST:PORT>    启动断点审查服务，会在程序第一行添点断点，等待审查工具接入
--seed <NUMBER>              生成随机数
--v8-flags=<v8-flags>         V8引擎命令行参数
```

##### 使用typescript

Deno在运行时同时支持JavaScript和TypeScript作为一级语言。这意味着它需要标准的模块名称，包括扩展名（或提供正确媒体类型的服务器）。此外，deno将导入的模块指定为文件（包括扩展名）或完全限定的URL导入。Typescript模块可以直接导入。例如

```null
import { Response } from "https://deno.land/std@0.53.0/http/server.ts";
import { queue } from "./collections.ts";
```

自定义编辑选项文件

您需要通过在执行应用程序时设置`-c`（或`--config`）参数来明确告诉Deno在哪里寻找此配置。

```bash
$ deno run -c tsconfig.json mod.ts
```



##### WebAssembly支持

Deno可以执行[WebAssembly](https://webassembly.org/)二进制文件。

```js
const wasmCode = new Uint8Array([
  0, 97, 115, 109, 1, 0, 0, 0, 1, 133, 128, 128, 128, 0, 1, 96, 0, 1, 127,
  3, 130, 128, 128, 128, 0, 1, 0, 4, 132, 128, 128, 128, 0, 1, 112, 0, 0,
  5, 131, 128, 128, 128, 0, 1, 0, 1, 6, 129, 128, 128, 128, 0, 0, 7, 145,
  128, 128, 128, 0, 2, 6, 109, 101, 109, 111, 114, 121, 2, 0, 4, 109, 97,
  105, 110, 0, 0, 10, 138, 128, 128, 128, 0, 1, 132, 128, 128, 128, 0, 0,
  65, 42, 11
]);
const wasmModule = new WebAssembly.Module(wasmCode);
const wasmInstance = new WebAssembly.Instance(wasmModule);
console.log(wasmInstance.exports.main().toString());
```



##### 生命周期

Deno支持与浏览器兼容的生命周期事件：`load`和`unload`。您可以使用这些事件在对程序进行添加设置或清除代码等操作。

`load`事件的监听器可以是异步的，将会被等待。`unload`事件的监听器是同步的。

例：

**main.ts**

```ts
import "./imported.ts";

const handler = (e: Event): void => {
  console.log(`got ${e.type} event in event handler (main)`);
};

window.addEventListener("load", handler);

window.addEventListener("unload", handler);

window.onload = (e: Event): void => {
  console.log(`got ${e.type} event in onload function (main)`);
};

window.onunload = (e: Event): void => {
  console.log(`got ${e.type} event in onunload function (main)`);
};

console.log("log from main script");
```

**imported.ts**

```ts
const handler = (e: Event): void => {
  console.log(`got ${e.type} event in event handler (imported)`);
};

window.addEventListener("load", handler);
window.addEventListener("unload", handler);

window.onload = (e: Event): void => {
  console.log(`got ${e.type} event in onload function (imported)`);
};

window.onunload = (e: Event): void => {
  console.log(`got ${e.type} event in onunload function (imported)`);
};

console.log("log from imported script");
```

请注意，您可以同时使用`window.addEventListener`和 `window.onload`/ `window.onunload`来定义事件的处理程序。它们之间有一个主要区别，让我们运行示例：

```bash
$ deno run main.ts
log from imported script
log from main script
got load event in onload function (main)
got load event in event handler (imported)
got load event in event handler (main)
got unload event in onunload function (main)
got unload event in event handler (imported)
got unload event in event handler (main)
```

