---
title : Vue项目打包上线随笔
date:
tags : 
  - 打包
categories : 
  - Vue
description: 
top_img: 
cover: http://resources.flagsnow.top/20200706115914.png
---
# Vue-router 默认模式（hash模式）

- 执行 `npm run build`
- 将生成的 `dist` 文件夹直接放到服务器里面即可
- 访问的时候要找到 `index.html` 文件
-我们要把Linux的防火墙关闭掉 `systemctl disable firewalld`

# Vue-router 去掉 `#` 的时候 采用（history模式）

- 使用 `vue-cli3` 创建的项目，要在项目的根目录新建一个 `vue.config.js` 的配置文件
  ```javaScript
  module.exports = {
    publicPath: "./",   //更改输出的 dist 文件夹为相对路径 否则会出现白屏或者404错误
    configureWebpack: {
      performance: {
        hints: false
      }
    }
  };
  ```
- 将 `dist` 文件夹直接放到服务器中的 `webapps`文件夹中（测试中使用的是 `tomcat` 服务）
- 更改 `tomcat` 的访问主页为项目主页
- 在 `tomcat` 中的 `conf` 文件夹中 使用 `vim` 打开 `Server.xml`
- 在 `<Host> </Host>` 中的最后面插入
  ```html
    <Context path="" docBase="dist" debug="0" reloadable="true" />
  ```
- 其中 `dist` 就是 `Vue` 项目打包后的文件夹
- 这样访问服务器的 IP 就可以打开自己的项目
- 注意：更改 `tomcat` 的默认主页后要重启 `tomcat` 服务
- (linux 中使用 `i` 进行编辑，`shift + :` 然后 `wq` 保存)

# 解决F5刷新报错
- 在`dist`文件夹中新建一个`WEB-INF  `文件夹
- 在`WEB-INF`文件夹中新建一个`web.xml`的配置文件
- 编辑`web.xml`文件
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
    version="3.1" metadata-complete="true">
    <display-name>Router for Tomcat</display-name>
    <error-page>
        <error-code>404</error-code>
        <location>/index.html</location>
    </error-page>
  </web-app>
  ```

# 将打包后的项目文件放到服务器
- 使用VSCode插件[SFTP](https://marketplace.visualstudio.com/items?itemName=liximomo.sftp)
- 使用快捷键 Ctrl+Shift+P 调出命名输入框，输入`SFTP:Config`，然后回车
  ![点击 - SFTP:Config](http://flagsnow.top/20009450-369fe84c7041dc25.png)
- 项目目录里面会出来一个`.vscode`文件夹，里面有一个`sftp.json`文件
  ```js
    "name": "My Server",    // 连接名(可以随意)
    "host": "localhost",    // 服务器地址，例如：192.127.1.1
    "protocol": "sftp",    // 上传的协议，我这里选择的是sftp
    "port": 22,    //这里我默认，没有设置
    "username": "username",    // 服务器的名字
    "context":"./dist",    //  要上传的文件夹
    "remotePath": "/",    // 上传到的服务器的地址。例如：（/home/dachen/apache-tomcat-8.5.50/webapps/dist）
    "uploadOnSave": true
  ````
- 配置好之后快捷键Ctrl+Shift+P调出命令框口，输入`SFTP:Upload Project`
- 之后按照提示，看检查要上传的文件夹，输入密码之后就可以上传成功

# 在 Linux  中搭建服务器环境
  - 下载并安装 [tomcat](http://us.mirrors.quenda.co/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.zip)
  - 解压 `tomcat` 并放在 Linux 中一个特定的文件夹（自己知道的文件夹里面）
  - `cd` 到 `tomact` 中的 `bin` 目录
  - 直接在终端中执行 `./startup.sh` 即可启动服务
  - 在终端中执行 `./shutdown.sh` 即可终止服务
  - 如果执行任务的时候遇到报错，检查一下 `startup.sh` 、 `shutdown.sh` 和 `catalina.sh`文件是否拥有可执行权限
  
# Linux 系统里面的一点小命令

- 插入使用 `i o u `
- 保存的时候 先按 `esc` 然后使用快捷键 `shift + :` 输入 `wq`，也可以使用 `wq!` 强制保存
- `chmod 777 + 文件名 ` 可以给文件所有的权限
- `reboot` 重启系统
- 在 Linux 中关闭防火墙的指令 `systemctl disable firewalld`