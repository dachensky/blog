---
title : Vue不用重新打包更改baseUrl
date:
tags : 
  - 打包
categories : 
  - Vue
description: 
top_img: 
cover: http://resources.flagsnow.top/20200706115125.jpg
---
> 在工作中，我们经常碰到后端更改端口号的问题、搭建测试环境等，这不得不让我们重新打包项目，如此简单又恶心的事情还总是反反复复，那么有没有办法在之前打包后文件里面直接更改呢，这样不仅仅省去了我们打包的时间，而且提供给测试人员他们又可以反复的修改测试，如此一来世界都变得美好了

### 思路：
- 创建一个`js`或者`json`然后在`axios`中动态的引入，设置`baseURL`为动态引入的变量
- 这个`js`、`json`文件在`vue`打包的时候不被编译，保留在最外层

### 实现：
- 新建一个`js`文件，把baseURL的变量设置为`window`的属性
```javascript
// 新建一个 baseURL.js 的文件
window.g = {
  baseURL: "http://192.168.1.1:8088/test"
}
```
- 在`vue`里面提供了一个`public`文件夹，我们可以在该文件夹里面新建一个`static`的静态文件夹，在这个文件夹里面的资源是不被编译的
- 如次我们还需要在`vue`项目中的`index.html`中引入我们新建的这个`baseURL.js`文件，在引用的时候我们尽量采用绝对路径
```html
<script src="/static/baseURL.js"></script>
```
- 这时候，我们创建了文件，也引入到了项目中在项目中，我们就可以使用`window.g.baseURL`来获取这个地址了


*如此我们就实现了之前提到的功能，我们每次修改的时候，只需要修改baseURL.js中的变量值就好了*