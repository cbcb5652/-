# Vue

## 前端项目初始化

### 安装脚手架

> npm install vue-cli -g

\# 必须全局安装vue-cli，否则无法使用vue命令

\# 安装完成之后使用vue -V检查vue-cli是否安装成功及版本信息

$ npm install vue-cli -g

$ vue -V

### 构建项目

> vue init webpack   项目名称

在e盘创建一个新文件夹vueProject，然后使用vue-cli创建一个新的Vue项目first-vue

​     备注：vue init webpack first-vue 这个命令，意思是初始化一个项目，其中webpack是构建工具，也就是整个项目是基于webpack的。其中first-vue是整个项目文件夹的名称，这个文件夹会自动生成在你指定的目录vueProject中，此时会让用户输入几个基本的选项，如项目名称，描述，作者等信息，如果不想填直接回车默认就好。如下图

![img](https://upload-images.jianshu.io/upload_images/5418302-1c34da2b5f6fa69b.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

### 安装依赖资源

> npm install

此时项目现在还只是一个结构框架，整个项目需要的依赖资源都还没有安装，接下来我们需要安装依赖资源，如下图:

首先cd到项目文件夹（first-vue文件夹） cd first-vue，

然后运行命令 npm install ，等待安装。

![img](https://upload-images.jianshu.io/upload_images/5418302-5a425c2f62a7ba08.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

 ==运行==

> npm run dev

## 开始创建

### 基本创建

**①在components目录下创建一个组件Login.vue**

> style里面的`scoped`属性指明了只在本组件内部生效，每个组件是单一的基本上要加scoped

```vue
<template>
    <div>
        登录组件
    </div>
</template>

<script>
export default {
    
}
</script>

<style lang="less" scoped>
</style>
```

==上述的style标签里，如果没有属性的话会报错，没有写的时候建议不要写==

**②在新建好上述的组件之后，需要添加默认的路由 ----> router/index.js**

```js
import Vue from 'vue'
import Router from 'vue-router'

import Login from '@/components/Login.vue'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path:'/',	// 重定向到首页
      redirect:'/login'
    },
    {
      path:'/login',
      component:Login
    }
  ]
})
```

**③导入全局样式**

编写css文件

```css
/** 全局css **/
html,body,#App{
    height: 100%;
    margin: 0;
    padding: 0;
}
```

导入全局 --->  main.js

```js
import Vue from 'vue'
import App from './App'
import router from './router'
// 导入全局样式表
import './assets/css/global.css'   //  <------ 导入这一条

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

### 导入组件库

> 以ant-design-vue为例，导入组件

引入组件：npm i --save ant-design-vue

只需要在main.js  文件中，全局导入即可使用了。

```js
import Vue from 'vue'
import App from './App'
import router from './router'
// 导入全局样式表
import './assets/css/global.css'       
import Antd from 'ant-design-vue'			<--------------------  1
import 'ant-design-vue/dist/antd.css'		<--------------------  2

Vue.config.productionTip = false
Vue.use(Antd)								<--------------------  3

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```

到官网就可以看到用法了：https://www.antdv.com/components/form-cn/

### 表单的数据绑定

对于一个嵌套的表单，可以在最高级的那一层绑定:model="loginForm"

对于往下子目录可以指定这个绑定组件下的嵌套 v-model="loginForm.username"/v-model="loginForm.password"

```vue
<!-- 登录表单区域 -->
      <a-form-model-model :model="loginForm" class="login_form">
        <!-- 用户名 -->
        <a-form-model-item>
            <a-input v-model="loginForm.username">
                <a-icon slot="prefix" type="user" style="color:rgba(0,0,0,.25)" />
            </a-input>
        </a-form-model-item>
        <!-- 密码 -->
        <a-form-model-item>
            <a-input v-model="loginForm.password" type="password">
                <a-icon slot="prefix" type="lock" style="color:rgba(0,0,0,.25)" />
            </a-input>
        </a-form-model-item>
        <a-form-model-item class="btns">
            <a-button type="primary">登录</a-button>
             <a-button type="dashed">重置</a-button>
        </a-form-model-item>
      </a-form-model-model >
```

这样，只需要在js里面绑定这两个组件就可以动态显示了。

```javascript
<script>
export default {
  data(){
    return{
      // 这是登录表单的数据绑定对象
      loginForm:{
        username:'cb',
        password:'123'
      }
    }
  }
}
</script>
```

**效果：**

![image-20200801230322502](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200801230322502.png)



https://www.bilibili.com/video/BV1EE411B7SU?p=33









































