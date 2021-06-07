---
title : Vue项目实现国际化
date:
tags : 
  - 国际化
categories : 
  - Vue
description: 
top_img: 
cover: http://resources.flagsnow.top/20200706120031.png
---

# Vue项目实现国际化

## 一：自己配置 `i18n` 完成国际化

- ### 1、创建项目，在项目中安装 vue-i18n

```cmd
	vue cerate vue_i18n
    yarn add vue-i18n
```
- ###2、在`main.js`中引入组件并且注册

``` javaScript
	import VueI18n from 'vue-i18n'		//引入vue-i18n组件
 
    Vue.use(VueI18n)		//use一下
 
    //注册i8n实例并引入语言文件
    const i18n = new VueI18n({
    	locale: 'zh',		//默认显示的语言 可以使用sessionStorage.getItem('lang') || 'zh',
        messages: {
        	'zh':require('./assets/locales/zh'),	//索引语言文件
    		'en':require('./assets/locales/en')
        },
        silentTranslationWarn: true		//禁止打印警告信息
    })

```
- ###3、在`new Vue` 中添加 `i18n`

```javaScript
	new Vue({
  		router,
 		 i18n,
  		render: h => h(App)
	}).$mount('#app')
```
- ###4、配置语言文件（文件有两种格式可以选择`.js`和`.json`）
	- 1、`.js`的方式
	```javascript
    	module.exports = {
  			language: {
    			zh:'中文',
    			en:'英文',
  			},
  			navbar: {
    			home: '首页',
    			introduction: '简介',
    			contact: '联系我们',
    			jing_product_name: '金融服务',
    			jing_product_toast_msg: '开发中，敬请期待'
  			}
		}
    ```
	- 2、`.json`的方式
	```json
    {
  		"language": {
   			"en": "英文",
				"zh": "中文"
 	 	},
  		"message": "你好 i18n !!"
	}
    ```
- ###5、事件绑定，进行中英文切换
	- 1、在vue组件中进行事件绑定
	```HTML
    <button @click="changeLangue('zh')">{{$t('language.zh')}}</button>
    <button @click="changeLangue('en')">{{$t('language.en')}}</button>
    ```
    - 2、在`methods`的生命周期中进行切换
    ```javaScript
    	changeLangue(lang){
        	if(lang === 'zh'){
            	sessionStorage.setItem("lang","zh");
                this.$i18n.locale = sessionStorage.getItem("lang");
                //这里可以加上路由刷新，看自己的需求
                this.$router.go(0)
            }else if (lang ==='en') {
            	sessionStorage.setItem("lang","en");
                this.$i18n.locale = sessionStorage.getItem("lang");
                //这里可以加上路由刷新，看自己的需求
                this.$router.go(0)
            }
        }
    ```

## 二：使用`i18n`提供的方法用`vue-cli`来快速搭建环境
- ###1、创建项目，在项目中安装 vue-i18n
```cmd
	vue create vue_i18n
    cd vue_i18n
    vue add i18n
```
- ###2、浏览一下自动创建主要生成的目录和文件
	- 1、 `locales`文件夹，该文件夹里面有一个默认的语言文件`en.json`
	
	- 2、 `i18n.js`文件，该文件主要是`i18n`的配置信息

	- 3、 在`main.js`的 `new Vue`中也为我们自动添加了 i8n

	- 4、 `vue.config.js` 不做解释

- ###3、对`i18n.js`的理解
	- 1、该文件主要是对`i18n`做了统一整理和管理
	```javascript
    export default new VueI18n({
  		locale: process.env.VUE_APP_I18N_LOCALE || 'en',	//设置页面的默认语言
        	// process.env.VUE_APP_I18N_LOCALE 默认是英语显示，我们要修改它为自定义
            // 可以通过sessionStorage.getItem('lang') 的方式
            // locale: sessionStorage.getItem('lang') || 'en'
  		fallbackLocale: process.env.VUE_APP_I18N_FALLBACK_LOCALE || 'en',		//可以理解为第二语言
  		messages: loadLocaleMessages()		//加载的语言文件，我们不需要修改，在上面已经为我们自动创建好了
	})
    ```
- ###4、实现中英文切换
	- 1、在vue组件中进行事件绑定
	```HTML
    <button @click="changeLangue('zh')">{{$t('language.zh')}}</button>
    <button @click="changeLangue('en')">{{$t('language.en')}}</button>
    ```
    - 2、在`methods`中进行切换
    ```javaScript
    	changeLangue(lang){
        	if(lang === 'zh'){
            	sessionStorage.setItem("lang","zh");
                this.$i18n.locale = sessionStorage.getItem("lang");
                //这里可以加上路由刷新，看自己的需求， 单独的语言文件时需要重载路由才能显示的
                this.$router.go(0)
            }else if (lang ==='en') {
            	sessionStorage.setItem("lang","en");
                this.$i18n.locale = sessionStorage.getItem("lang");
                //这里可以加上路由刷新，看自己的需求， 单独的语言文件时需要重载路由才能显示的
                this.$router.go(0)
            }
        }
    ```
- ###5、自动构建工具是使用`vue-cli-plugin-i18n`来就完成的
	- 惊喜

		- 在组件中，我们得到了一个`<i18n></i18n>`的标签，在该标签中我们可以使用`json`格式来直接编辑语言文件
		
        ```json
        {
  			"en": {
    			"hello": "Hello i18n in SFC!"
  		},
  			"zh":{
    			"hello": "你好 i18n in SFC!"
  			}
		}
        ```
	- 在vue组件中的语言配置，和在单独文件中配置有何异同
		- 1、在vue组件中直接配置语言文件，更加直观
		- 2、在vue组件中的配置比加载单独文件迅速
		- 3、我不知道了，欢迎各位补充

- ### 6、使用
       - 1、在组件中使用`{{$t(value)}}`的方式
       - 2、在标签中使用`<img src='$t(VALUE)' />`的方式
- ### 7、必要说明
	- 1、以上为个人观点，可能会用不足
	- 2、有错误的地方希望指正，谢谢