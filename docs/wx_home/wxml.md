# 小程序三板斧：WXML、WXSS、JavaScript

## wxml

> WXML（WeiXin Markup Language） 是小程序用于构建页面的标签语言，结合基础组件、事件系统，可以构建出页面的结构

## wxss

> WXSS（WeiXin Style Sheets） 是一套样式语言，用于描述 WXML 的组件样式。用来决定 WXML 的组件应该怎么显示。

!>先整体划分页面结构，定好页面布局，再具体编写局部区域的样式

### 知识要点

1. 除非特别需要，否则尺寸单位一律使用 rpx，由底层框架实现不同屏幕尺寸大小的兼容处理。
2. flex 弹性布局是主流且推荐的页面布局样式解决方案，需要重点了解。
3. 常备一个 CSS 样式的说明文档，多查多看多调试

## JavaScript

> JavaScript ( JS ) 是一种具有函数优先的轻量级，解释型或即时编译型的编程语言。虽然它是作为开发Web 页面的脚本语言而出名的，但是
> 也被用到了很多非浏览器环境中，例如 Node.js、 Apache CouchDB 和 Adobe Acrobat。JavaScript 是一种基于原型编程、多范式的动态脚
> 语言，并且支持面向对象、命令式和声明式（如函数式编程）风格。