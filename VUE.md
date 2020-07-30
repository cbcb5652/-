# VUE

学习文档:https://cn.vuejs.org/v2/guide/

视图层: HTML + CSS + JS：给用户看，刷新后台的数据

CSS预处理器：SASS 基于 Ruby , LESS 基于NodeJS

VUE =  Angular + React

> 一些常用的UI框架

- Ant-Desgin:阿里巴巴出品的，基于React的UI框架
- ElementUI,Iview,ice :饿了么出品，基于Vue的UI框架
- Bootstrap ： Twitter推出的一个用于前端开发的开源工具包
- AmazeUi：又叫“妹子UI”，一款HTML5跨屏前端框架
- ViewUi
- ElementUI
- ICE
- VantUI
- AtUI

> MVVM 模式的实现者

- Model:模型层，在这里表示JavaScript对象

- View：视图层，在这里表示DOM（HTML操作的元素）

- ViewModel：连接视图和数据中间件，Vue.js就是MVVM中的ViewModel层的实现者

  在MVVM架构中，是不允许数据和视图直接通信的，只能通过ViewModel来通信，而ViewModel就是定义了一个Observer观察者

- ViewModel能够观察到数据的变化，并对视图对应的内容进行更新

- ViewModel能够监听到视图的变化，并能够通知数据发生改变

  一种软件架构设计模式，是一种简单用户界面的事件驱动编程方式。

  核心是：==ViewModel层，负责转换Model中的数据对象来让数据变得更容易管理和使用==

  - 该层向上与视图进行双向数据绑定
  - 向下与Model层通过接口请求进行数据交互

  ![image-20200702112343151](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200702112343151.png)

> 为什么要使用MVVM

MVVM模式和MVC模式是一样的，主要目的是分离视图(View) 和模型(Model)，有几大好处

- **低耦合:**视图( View) 可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变
- **可复用：**你可以把一些视图逻辑放在一个ViewModel里面，让很多View重用这段视图逻辑
- **独立开发：**开发人员可以专注于这些业务逻辑和数据的开发，设计人员可以专注于页面设计
- **可测试：**界面素来是比较难于测试的，而现在测试可以针对ViewModel来写

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

</head>
<body>
<!--view层${} 模板-->
<div id="app">
    {{message}}
</div>


<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        //Model
        data:{
            message:"hello vue!"
        }
    });
</script>
</body>
</html>
```



## 基本语法

### v-bind

绑定v-bind来绑定元素的特性    可以简写为：

v-bind:title="message":   鼠标悬停查看

```html
<span v-bind:title="message">
        鼠标悬停几秒钟查看此处动态绑定信息！
    </span>
```

​	你看到的v-bind等被称为指令。指令带有前缀v-，以表示它们是Vue的特殊特性。可能你已经猜到了，它们会在渲染的DOM上应用特性的响应式行为。在这里，该指令的意思是：“将这个元素节点的title特性和Vue实例的message属性保持一致”。

​	如果你再次打开浏览器的JavaScript控制台，输入vm.message="新消息",就会再一次看到这个绑定了title特性的HTML已经进行了更新。

### v-if /v-else

```html
<div id="app">
    <h1 v-if="ok">Yes</h1>
    <h1 v-else>No</h1>
</div>


<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            ok: true
        }
    });
</script>
```

![image-20200702114830608](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200702114830608.png)

### v-for

```html
<div id="app">
   <!-- 加一个index 可以取遍历的值 -->
   <li v-for="(item ,index) in items">
        {{item.message}}-----{{index}}
    </li>
</div>


<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            items:[
                {message:'chenbin的第一个循环'},
                {message:'学习vue'}
            ]
        }
    });
</script>
```

### v-on 

绑定事件，可以简写为@



### 事件绑定

```html
<div id="app">
    <button v-on:click="sayHi">Click Me</button>
</div>

<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            message:"陈彬的方法"
        },
        methods:{
            // 方法必须定在vue的Method对象中
            sayHi: function () {
                alert(this.message);
            }
        }
    });
</script>
```

> 什么是双向绑定

​	Vue.js是一个框架，即数据双向绑定，即当数据发生变化的时候，视图也就发生变化，当视图发生变化的时候，数据也会跟着同步变化。这也算是Vue.js的精髓了。

```html
<div id="app">
    输入的文本：<input type="text" v-model="message">{{message}}
    <br>
    输入的textarea: <textarea v-model="message"></textarea>
    <br>
    性别：
    <input type="radio" name="sex" value="男" v-model="sex">男
    <input type="radio" name="sex" value="女" v-model="sex">女

    <p>
        选中了谁:{{sex}}
    </p>

    <br>
    <select v-model="selected">
        <option value="" disabled>--请选择--</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>

    <span>value:{{selected}}</span>
</div>

<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            sex:'',
            message:"123",
            selected:""
        },
    });
</script>
```

模板绑定:

```html
<div id="app">
    <!-- 组件：传递给组件中的值：props-->
    <chenbin v-for="item in items" v-bind:it="item"></chenbin>
</div>


<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    //定义一个vue组件component
    Vue.component("chenbin",{
        props:['it'],
        template:'<li>{{it}}</li>'
    });
    var vm = new Vue({
        el:"#app",
        data:{
            items:["Java","Linux","前端"]
        }
    });
</script>
```

## Axios异步通信

> 什么是Axios

​	Axios是一个开源的开源用在浏览器和NodeJS的异步通信框架，她的主要作用就是实现AJAX异步通信，其他功能如下：

- 从浏览器中创建 XMLHttpRequests
- 从node.js创建http请求
- 支持promise API[JS中链式编程]
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换JSON数据
- 客户端支持防御XSRF(跨站请求伪造)

```html
<div id="vue" v-cloak>
    <div>{{info.name}}</div>
    <div>{{info.address.city}}</div>

    <a v-bind:href="info.url">点我</a>
</div>

<!--引入JS文件-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
    var vm = new Vue({
        el:'#vue',
        data(){
            return{
                //请求的返回参数，必须和json字符串一样
                info:{
                    name:null,
                    age:null,
                    address: {
                        street: null,
                        city: null,
                        country: null
                    },
                    url:null
                }
            }
        },
        mounted(){//钩子函数 ES6新特性
            axios.get('data.json').then(response=>(console.log(this.info = response.data)));
        }
    })
</script>
```

## 计算属性

> 什么是计算属性

​	计算属性的重点突出在属性两个字上 (属性是名词)，首先它是个属性其次这个属性有计算的能力(计算是动词)，这里的计算就是函数；简单点说，它就是一个能够计算结果缓存起来的属性(将行为转换成了静态的属性)，仅此而已；开源想象为缓存!

```html
<div id="app">
    <p>currentTime1:{{currentTime1()}}</p>
    <p>currentTime2:{{currentTime2()}}</p>
</div>

<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
           message:"hello,chenbin"
        },
        methods:{
            currentTime1:function () {
                return Date.now();//返回当前时间戳
            }
        },
        computed:{
            //计算属性：methods，computed 方法名不能重名，重名之后，只会调用methods的方法
            currentTim2:function () {
                return Date.now();//返回一个事件戳
            }
        }
    });
</script>
```

**说明：**

- methods:定义方法，调用方法使用currentTime1()，需要代括号
- computed：定义计算属性，调用属性使用currentTime2，不需要代括号；this.message是为了能够让currentTime2观察到数据的变化而变化。
- 如果在方法的值发生了变化，则缓存就会刷新！可以在控制台使用vm.message="chenbin",改变数据的值，再次测试观察效果！

**结论：**

​	调用方法时，每次都需要进行计算，既然有计算过程则必定产生系统开销，那如果这个结果是不经常变化的呢？ 此时就可以考虑将这个结果缓存起来，采用计算属性可以很方便的做到这一点，计算属性的主要特征就是为了将不经常变化的计算结果进行缓存，以节约我们的系统开销；

## slot插槽

```html
<div id="app">
    <todo>
        <todo-title slot="todo-title" :title="title"></todo-title>
        <todo-items slot="todo-items" v-for="item in todoItems" :item="item"></todo-items>
    </todo>
</div>

<!-- 1.导入vue.js -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script>
    //slot:插槽
    Vue.component("todo",{
        template:'<div>' +
                      '<slot name="todo-title"></slot>' +
                      '<ul>' +
                          ' <slot name="todo-items"></slot>' +
                      '</ul>' +
                  '</div>'
    });

    Vue.component("todo-title",{
        props:['title'],
       template:'<div>{{title}}</div>'
    });
    Vue.component("todo-items",{
        props:['item'],
        template:'<li>{{item}}</li>'
    });
    var vm = new Vue({
        el:"#app",
        data:{
            title:"学习内容",
            todoItems:["Java","python","Linux","Hadoop","zookeeper","spark"]
        }

    });
</script>
```

插槽为自定义自己的标签，但是如果你取数据的时候需要绑定好对应的数据，这样就可以从对应的数据里面查询了。



## ES6模块

EsmaScript6标准增加了JavaScript语言层面的模块体系定义。ES6模块的设计思想，是尽量静态化，使编译时就能确定模块的依赖关系，以及输入和输出变量。CommonsJS和AMD模块，都只能在运行时确定这些东西。

```html
import "jquery"
export function doStuff(){}
module "localModule" {}
```

**优点**

- 容易进行静态分析
- 面向未来的EsmaScript标准

**缺点**

- 原生浏览器端还没有实现该标准
- 全新的命令，新版的NodeJS才支持

**实现**

- Babel

## 新建vue项目

### 使用命令初始化vue项目

```vue
vue init webpack hello-vue
```

安装完成之后，全部选择第一个，其他都是no

Author：自己写

### 安装依赖

> 我们需要安装vue-router,element-ui,sass-loader和node-sass四个插件

```shell
# 进入安装目录
cd hello-vue
# 安装vue-router
npm install vue-router --save-dev
# 安装 element-ui
npm i element-ui -S
# 安装依赖
npm install
# 安装SASS 加载器
cnpm install sass-loader node-sass --save-dev
#启动测试
npm run dev
```

**解释**

- npm install moduleName: 安装模块到项目目录下
- npm install -g moduleName :-g 的意思是将模块安装到全局，具体安装到磁盘的哪个位置，要看npm config prefix的位置
- npm install -save moduleName: --save的意思是将模块安装到项目目录下，并在package文件的dependencies节点写入依赖，-S为该命令缩写
- npm install -save-dev moduleName: --save-dev 的意思是将模块安装到项目目录下，并在package文件的devDependencies节点写入依赖，-D为该命令的缩写

## Element UI 

> **点击按钮激活对话框dialog**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">
    <el-button @click="flag = true">Button</el-button>
    <el-dialog :visible.sync="flag" title="Hello world">
        <p>Try Element</p>
    </el-dialog>
</div>
</body>
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<!-- import JavaScript -->
<script src="https://unpkg.com/element-ui/lib/index.js"></script>
<script>
    new Vue({
        el: '#app',
        data: function() {
            return {
                flag: false
            }
        }
    })
</script>
</html>
```

效果:

![image-20200708090438679](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200708090438679.png)

**fade 消失动态**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<script src="//unpkg.com/vue/dist/vue.js"></script>
<script src="//unpkg.com/element-ui@2.13.2/lib/index.js"></script>
<div id="app">
  <template>
    <div>
      <el-button @click="show = !show">Click Me</el-button>

      <div style="display: flex; margin-top: 20px; height: 100px;">
<!--        el-zoom-in-center,el-zoom-in-top,el-zoom-in-bottom三种效果-->
        <transition name="el-zoom-in-center">
          <div v-show="show" class="transition-box">缩放</div>
        </transition>
        <transition name="el-fade-in">
          <div v-show="show" class="transition-box">渐变</div>
        </transition>
      </div>
<!--      折叠展开-->
      <div style="margin-top: 20px; height: 200px;">
        <el-collapse-transition>
          <div v-show="show">
            <div class="transition-box">折叠展开</div>
            <div class="transition-box">折叠展开</div>
          </div>
        </el-collapse-transition>
    </div>
  </template>
</div>
</body>
<style>
  @import url("//unpkg.com/element-ui@2.13.2/lib/theme-chalk/index.css");
  .transition-box {
    margin-bottom: 10px;
    width: 200px;
    height: 100px;
    border-radius: 4px;
    background-color: #409EFF;
    text-align: center;
    color: #fff;
    padding: 40px 20px;
    box-sizing: border-box;
    margin-right: 20px;
  }
</style>
<script>
  var Main = {
    data: () => ({
      show: true
    })
  }
  var Ctor = Vue.extend(Main)
  new Ctor().$mount('#app')
</script>
</html>
```

![image-20200708102745623](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200708102745623.png)

**layout布局**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<script src="//unpkg.com/vue/dist/vue.js"></script>
<script src="//unpkg.com/element-ui@2.13.2/lib/index.js"></script>
<div id="app">
  <p><b>基础布局:</b>通过 row 和 col 组件，并通过 col 组件的 span 属性我们就可以自由地组合布局。</p>
  <el-row >
    <el-col :span="24">
      <div class="grid-content bg-purple-dark"></div>
    </el-col>
  </el-row>
  <el-row >
    <el-col :span="12">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="12">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
  </el-row>
  <el-row>
    <el-col :span="8">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="8">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
    <el-col :span="8">
      <div class="grid-content bg-purple"></div>
    </el-col>
  </el-row>
  <el-row>
    <el-col :span="6">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="6">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
    <el-col :span="6">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="6">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
  </el-row>
  <el-row>
    <el-col :span="4">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="4">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
    <el-col :span="4">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="4">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
    <el-col :span="4">
      <div class="grid-content bg-purple"></div>
    </el-col>
    <el-col :span="4">
      <div class="grid-content bg-purple-light"></div>
    </el-col>
  </el-row>
  <p><b>指定间隔:</b>Row 组件 提供 gutter 属性来指定每一栏之间的间隔，默认间隔为 0。</p>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <p><b>混合布局:</b>通过基础的 1/24 分栏任意扩展组合形成较为复杂的混合布局。</p>
  <el-row :gutter="20">
    <el-col :span="16"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row :gutter="20">
    <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="8"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row :gutter="20">
    <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="16"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="4"><div class="grid-content bg-purple"></div></el-col>
  </el-row>

  <p><b>分栏偏移:</b>通过制定 col 组件的 offset 属性可以指定分栏偏移的栏数。</p>
  <el-row :gutter="20">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6" :offset="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row :gutter="20">
    <el-col :span="6" :offset="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6" :offset="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row :gutter="20">
    <el-col :span="12" :offset="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>

  <p><b>对齐方式:</b>将 type 属性赋值为 'flex'，可以启用 flex 布局，并可通过 justify 属性来指定 start, center, end, space-between, space-around 其中的值来定义子元素的排版方式。</p>
  <el-row type="flex" class="row-bg">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row type="flex" class="row-bg" justify="center">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row type="flex" class="row-bg" justify="end">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row type="flex" class="row-bg" justify="space-between">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>
  <el-row type="flex" class="row-bg" justify="space-around">
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :span="6"><div class="grid-content bg-purple"></div></el-col>
  </el-row>

  <p>响应式布局：</p>
  <el-row :gutter="10">
    <el-col :xs="8" :sm="6" :md="4" :lg="3" :xl="1"><div class="grid-content bg-purple"></div></el-col>
    <el-col :xs="4" :sm="6" :md="8" :lg="9" :xl="11"><div class="grid-content bg-purple-light"></div></el-col>
    <el-col :xs="4" :sm="6" :md="8" :lg="9" :xl="11"><div class="grid-content bg-purple"></div></el-col>
    <el-col :xs="8" :sm="6" :md="4" :lg="3" :xl="1"><div class="grid-content bg-purple-light"></div></el-col>
  </el-row>


</div>
</body>
<style>
  @import url("//unpkg.com/element-ui@2.13.2/lib/theme-chalk/index.css");
  .el-row {
    margin-bottom: 20px;
  &:last-child {
     margin-bottom: 0;
   }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }

</style>
<script>
  new Vue().$mount('#app')
</script>
</html>
```

data数据对象的操作

![image-20200708105855692](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200708105855692.png)

> v-on 事件监听

平时我们的写法是<kbd>v-on:click = "method"</kbd>  可以简写为:<kbd>@click = "methood"</kbd>

![image-20200708112415003](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200708112415003.png)

- v-on指令的作用是:为元素绑定事件
- 事件名不需要写on
- 指令可以简写为@
- 绑定的方法定义在methods属性中

>  使用v-show指令

![image-20200708113337080](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200708113337080.png)



**axios 网络请求库**

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

//发送get请求
axios.get(地址？key=value1&key2=values).then(function(response){},function(err){})

//发送post请求
axios.post(地址,{key:value,key2:value2}).then(function(response){},function(err){})
```

> 随机获取笑话的接口

- 请求地址 ： https://autumnfish.cn/api/joke/list
- 请求方法:    get
- 请求参数:     num(笑话条数)
- 响应内容： 随机笑话

> 用户注册接口

- 请求地址:  https://autumnfish.cn/api/user/reg
- 请求方法:  post
- 请求参数:  username
- 响应内容:  注册成功或失败









































