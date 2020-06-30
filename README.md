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

从整体设计来看，Deno 并不定位为 Nodejs 的替代品，更倾向于统一JS 在前后端开发全流程。



