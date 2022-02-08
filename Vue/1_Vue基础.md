# Vue基础

- 学之前的前置知识？
  - readme中的框架之前的知识！

- 主要参考自：https://www.bilibili.com/video/BV18E411a7mC?spm_id_from=333.999.0.0
- 学完后应该明白：
  - MVVM是什么？为什么？
  - vue的基本使用？单向绑定？双向绑定？if else for？事件？函数？组件？slot？自定义事件？网络通信？计算属性？
  - vue的实际开发中的使用？nodejs？vue-cli？webpack？vue router？路由怎么带参数？嵌套路由？重定向？404？路由何时使用axios加载数据？
- 未来：
  - 直接选择一个vue+SpringBoot上手实践开发即可！

# 1.Vue基础使用

- 来源：前端核心要解决的问题、前端发展、什么是MVVM？
- MVVM：
  - ![image-20220127135014487](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127135014487.png) 
- Vue的基本使用：建议直接看官方文档学习：
  - https://cn.vuejs.org/v2/guide/installation.html

## 1.1 第一个Vue程序

- ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
  </head>
  <body>
  
      <!-- view层：模版 -->
      <div id="app1">
          {{message}}
      </div>
  
      <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
      <script>
          // View Model层
          var vm = new Vue({
              el:"#app1",
  
              // Model层：数据
              data:{
                  message: "hello vue",
              }
          });
  
      </script>
  </body>
  </html>
  ```

## 1.2 vue指令

- 绑定数据，if，else，for

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1">
            {{message}}
            <!-- 绑定数据 -->
            <span v-bind:title="message"> 鼠标悬浮看效果！</span>
    
            <h1 v-if="ok==true">Yes</h1>
            <h1 v-else>No</h1>
            <br>
            <h1 v-if="type==='A'">A</h1>
            <h1 v-else-if="type==='B'">B</h1>
            <h1 v-else-if="type==='C'">C</h1>
            <h1 v-else>D</h1>
            <br>
            <li v-for="item in arrays">
                {{item.m}}
            </li>
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script>
            // View Model层
            var vm = new Vue({
                el:"#app1",
    
                // Model层：数据
                data:{
                    message: "hello vue",
                    ok: true,
                    type: 'A',
                    arrays: [
                        {m: "aaa"},
                        {m: 'xxx'},
                    ],
                }
            });
    
        </script>
    </body>
    </html>
    ```

- 事件和函数

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1">
            <button v-on:click="sayHi"> click me</button>
        
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script>
            // View Model层
            var vm = new Vue({
                el:"#app1",
    
                // Model层：数据
                data: {
                    message: "xxxx"
                },
                methods: { // 方法必须定义在Vue的Methods对象中
                    sayHi: function(){
                        alert("say Hi!"+this.message);
                    }
    
                }
            });
    
        </script>
    </body>
    </html>
    ```

- 双向绑定：v-model

  -  你可以用v-model 指令在表单<input>、 <textarea> 及<select> 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但v-model本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

  - 注意: v-model会忽略所有表单元素的value, checked. selected 特性的初始值而总是将Vue实例的数据作为数据来源。你应该通过JavaScript在组件的data选项中声明初始值!

  - 注意:如果v-model 表达式的初始值未能匹配任何选项，<select> 元素将被渲染为“未选中”状态。在ios中，这会使用户无法选择第一个选项。因为这样的情况下，ios不会触发change事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1">
            输入数据实现双向绑定：<input v-model="message">{{message}}
            <br>
            <input type="radio" value="nan" v-model="rd"> nan
            <input type="radio" value="nui" v-model="rd"> nui
            {{rd}}
            <br>
            <select v-model="select">
                <option value="" disabled>--请选择--</option>
                <option>A</option>
                <option>B</option>
                <option>C</option>
            </select>
            <h1>value:{{select}}</h1>
        
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script>
            // View Model层
            var vm = new Vue({
                el:"#app1",
    
                // Model层：数据
                data: {
                    message: "xxxx",
                    rd: "xxx",
                    select: "",
                },
            });
    
        </script>
    </body>
    </html>
    ```

- 组件(自定义标签)

  - 组件是可复用的Vue 实例，说白了就是一组可以重复使用的模板，跟JSTL的自定义标签、Thymeleaf的th:fragment 等框架有着异曲同工之妙。通常一个应用会以一棵嵌套的组件树的形式来组织:

  - ![image-20220127143931274](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127143931274.png)

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1">
    
            <!-- // 将外部vue中的数据传递给主键，使用v-bind -->
            <my_name v-for="item in items" v-bind:qin="item"></my_name>
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script>
            // 定义一个Vue主键
            Vue.component("my_name",{
                props: ["qin"], // 传递参数
                template: '<li>{{qin}}</li>'
            });
    
            // View Model层
            var vm = new Vue({
                el:"#app1",
                data: {
                    items: ["aa","bb","cc"]
                }
            });
    
        </script>
    </body>
    </html>
    ```

- 网络通信：

  - axios:Axios是一个开源的可以用在浏览器端和NodeJs 的异步通信框架，它的主要作用就是实现AJAX异步通信

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    
        <!-- 解释网络闪烁问题 -->
        <style>
            [v-clock]{
                display: none;
            }
        </style>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1" v-clock>
            <div>
                {{info.name}}
            </div>
            <a v-bind:href="info.url">点我</a>
    
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
        <script>
    
            // View Model层
            var vm = new Vue({
                el:"#app1",
                // vue的生命周期中的几个钩子函数
                mounted(){
                    // 链式编程 ES6新特性
                    axios.get("./data.json")
                    .then(Response=>(console.log(Response.data),this.info=Response.data));
                },
    
                //data()方法
                data(){
                    return{
                        // 可以用来接收axios.get中的返回值
                        // 该返回值需要和请求的返回参数格式一致 
                        info:{
                            "name": "",
                            "url": "",
                            "flag": true,
                            "address": {
                                "aa": "",
                                "bb": "",
                                "cc": ""
                            }
                        }
                    }
                },
            
            });
    
        </script>
    </body>
    </html>
    ```

- 计算属性:

  - 计算出来的结果保存在属性中，保存在内存中，类似缓存

  - 注意：调用方法时，每次都需要进行计算，既然有计算过程则必定产生系统开销，那如果这个结果是不经常变化的呢?此时就可以考忠将这个结果缓存起来，采用计算属性可以很方便的做到这一点计算属性的主要特性就是为了将不经常变化的计算结果进行缓存，以节约我们的系统开销;

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    
        <!-- 解释网络闪烁问题 -->
    
        <style>
            [v-clock]{
                display: none;
            }
        </style>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1" v-clock>
            <p> currentTime1 : {{currentTime1}}</p>
            <p> currentTime1() : {{currentTime1()}}</p>
            <p> currentTime2 : {{currentTime2}}</p>
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
        <script>
    
            // View Model层
            var vm = new Vue({
                el:"#app1",
                methods: {
                    currentTime1: function(){
                        return Date.now();
                    }
                },
    
                // 计算属性
                computed: {
                    // 如果不刷新或者里面的数据不变化，currentTime2的值也不会发生变化
                    // 就像缓存一样！
                    currentTime2: function(){
                        this.message;
                        return Date.now();
                    }
                }
            });
    
        </script>
    </body>
    </html>
    ```

-  Slot:

  - 在Vue.js中我们使用<slot> 元素作为承载分发内容的出口，作者称其为插槽，可以应用在组合组件的场景中

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    
        <!-- 解释网络闪烁问题 -->
    
        <style>
            [v-clock]{
                display: none;
            }
        </style>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1" v-clock>
            <todo>
                <todo-title slot="todo-title" :title="title"></todo-title>
                <todo-items slot="todo-items" v-for="item in items" :item="item"></todo-items>
            </todo>
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
        <script>
    
            Vue.component("todo",{
                template: 
                    '<div>\
                        <slot name="todo-title"> </slot>\
                        <ul>\
                            <slot name="todo-items"></slot>\
                        </ul>\
                    </div>'
            });
    
            Vue.component("todo-title",{
                props: ['title'],
                template: '<div>{{title}}</div>'
            });
    
            Vue.component("todo-items",{
                props: ['item'],
                template: '<li>{{item}}</li>'
            });
    
            // View Model层
            var vm = new Vue({
                el:"#app1",
                data: {
                    title: "hhhh",
                    items: ["xxx","xxx","aaa"]
                }
                
            });
    
        </script>
    </body>
    </html>
    ```

- 自定义事件

  - 通过以上代码不难发现，数据项在Vue的实例中，但删除操作要在组件中完成，那么组件如何才能删除Vue实例中的数据呢?此时就涉及到参数传递与事件分发了，Vue为我们提供了自定义事件的功能很好的帮助我们解决了这个问题;使用this.$emit(自定义事件名',参数)，操作过程如下: 

  - ![image-20220127165520451](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127165520451.png)

  - ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    
        <!-- 解释网络闪烁问题 -->
    
        <style>
            [v-clock]{
                display: none;
            }
        </style>
    </head>
    <body>
    
        <!-- view层：模版 -->
        <div id="app1" v-clock>
            <todo>
                <todo-title slot="todo-title" :title="title"></todo-title>
                <todo-items slot="todo-items" v-for="(item,index) in items" 
                :item="item" :index="index" :key="index" v-on:out_remove="removeItems(index)"
                ></todo-items>
            </todo>
        </div>
    
        <script src="https://lib.baomitu.com/vue/2.6.14/vue.js"></script>
        <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
        <script>
    
            Vue.component("todo",{
                template: 
                    '<div>\
                        <slot name="todo-title"> </slot>\
                        <ul>\
                            <slot name="todo-items"></slot>\
                        </ul>\
                    </div>'
            });
    
            Vue.component("todo-title",{
                props: ['title'],
                template: '<div>{{title}}</div>'
            });
    
            Vue.component("todo-items",{
                props: ['item','index'],
                template: '<li>{{index}}:{{item}}\
                    <button @click="remove">删除</button>\
                    </li>',
                methods: {
                    remove: function(index){
                        alert("xxx");
                        this.$emit("out_remove",index);
                    }
                }
            });
    
            // View Model层
            var vm = new Vue({
                el:"#app1",
                data: {
                    title: "hhhh",
                    items: ["xxx","xxx","aaa"]
                },
                methods:{
                    removeItems: function(index){
                        this.items.splice(index,1);
                    }
                }
                
            });
    
        </script>
    </body>
    </html>
    ```

# 2.Vue实际开发

## 2.1 vue-cli

- 什么是vue-cli？

  - vue-cli官方提供的一个脚手架,用于快速生成一个vue的项目模板;预先定义好的目录结构及基础代码，就好比咱们在创建Maven项目时可以选择创建一个骨架项目，这个骨架项目就是脚手架，我们的开发更加的快速;
  - 主要的功能:
    - 统一的目录结构
    - 本地调试
    - 热部署
    - 单元测试
    - 集成打包上线

- 如何使用vue-cli？

  - 安装 node js：https://nodejs.org/zh-cn/download/

    - cmd下输入node -v,查看是否能够正确打印出版本号即可!

    - cmd下输入npm-v ,查看是否能够正确打印出版本号即可!

    - 这个npm,就是一个软件包管理工具，就和linux下的apt软件安装差不多!

    - 安装Node.js淘宝镜像加速器(cnpm)

    - ```bash
      # -g 就是全局安装.
      npm install cnpm -g
      # 或使用如下语句解决npm速度慢的问题
      npm install --registry=https://registry.npm.taobao.org
      ```

  - 安装vue-cli

    - ```bash
      cnpm install vue-cli -g
      
      # 查看是否安装成功
      vue list
      ```

  - 创建一个vue-cli项目：

    - ```bash
      # 到对应文件夹下面：初始化一个webpack项目，名字是myvue，
      vue init webpack myvue
      # 一路n理解其他文件夹的作用
      Project name:项目名称，默认回车即可
      Project description:项目描述，默认回车即可
      Author:项目作者，默认回车即可
      Install vue-router:是否安装vue-router,选择n不安装(后期需要再手动添加)
      Use ESLint to lint your code:是否使用ESLint做代码检查，选择n不安装(后期需要再手
      动添加)
      Set up unit tests:单元测试相关，选择n不安装(后期需要再手动添加)
      Setup e2e tests with Nightwatch:单元测试相关，选择n不安装(后期需要再手动添加)
      Should we run npm install for you after the project has been created:创建完成后直
      接初始化，选择n,我们手动执行;运行结果!
      ```

  - 初始化并且运行：

    - ```bash
       cd myvue
       npm install (or if using yarn: yarn)
       npm run dev
      ```

    - 理解前端运行结构？

## 2.2 webpack

- 什么是webpack？就是一个打包工具，将之前模块化的代码打包成一个js文件！

  - 本质上，webpack是一个现代JavaScript应用程序的静态模块打包器(module bundler)。当webpack处理应用程序时，它会递归地构建一一个依赖关系图(dependency graph),其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个bundle.
  - Webpack是当下最热门的前端资源模块化管理和打包工具，它可以将许多松散耦合的模块按照依赖和规则打包成符合生产环境部署的前端资源。还可以将按需加载的模块进行代码分离，等到实际需要时再异步加载。通过loader转换，任何形式的资源都可以当做模块，比如CommonsJS，AMD、ES6、CSS. JSON、CoffeeScript. LESS 等
  - 前端模块化演进？
    - 目前已经是ES6了：EcmaScript6标准增加了JavaScript语言层面的模块体系定义。ES6 模块的设计思想，是尽量静态化，使编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonsJS 和AMD模块，都只能在运行时确定这些东西。

- 安装webpack?

  - ```bash
    npm install webpack -g
    npm install webpack-cli -g
    
    # 测试是否安装成功
    webpack -v
    webpack-cli -v
    
    webpack.config.js文件
    
    entry:入口文件，指定WebPack用哪个文件作为项目的入口.
    output:输出，指定WebPack把处理完成的文件放置到指定路径
    module:模块，用于处理各种类型的文件
    plugins:插件，如:热更新、代码重用等
    resolve:设置路径指向
    watch:监听，用于设置文件改动后直接打包
    
    module.exports = {
      context: path.resolve(__dirname, '../'),
      entry: {
        app: './src/main.js'
      },
      output: {
      },
      resolve: {
      },
      module: {
        rules: [
          {
            test: /\.js$/,
            loader: 'babel-loader',
            include: [resolve('src'), resolve('test'), resolve('node_modules/webpack-dev-server/client')]
          },
          }
        ]
      },
      node: {
    
      }
    }
    
    ```

- 使用webpack?

  1. 创建项目

  2. 创建一个名为modules的目录，用于放置JS模块等资源文件

  3. 在modules下创建模块文件，如hello.js，用于编写JS模块相关代码

     - ```js
       // 暴露一个方法：sayHi
       exports.sayHi = function(){
         document.write("hello webpack")
       };
       ```

  4. 在modules下创建一个名为main.js的入口文件，用于打包时设置entry属性

     - ```js
       // 导入一个模块，类似java导入对象，调用方法
       var hello = require("./hello")
       hello.sayHi();
       ```

  5. 在项目目录下创建webpack.config.js配置文件，使用webpack命令打包

     - ```js
       module.exports={
         entry: "./modules/main.js",
         output: {
           	filename: "./js/bundle.js"
         }
       }
       ```

  6. 在项目目录下使用webpack命令进行打包，会在output指定的文件夹里面生成文件

  7. 编写index.html导入打包后的文件，打开html就会自动渲染显示

     - ```html
       <!DOCTYPE html>
       <html lang="en">
       <head>
           <meta charset="UTF-8">
           <meta http-equiv="X-UA-Compatible" content="IE=edge">
           <meta name="viewport" content="width=device-width, initial-scale=1.0">
           <title>Document</title>
       </head>
       <body>
       	<script src="dist/js/bundle.js"></script>
       </body>
       ```

## 2.3 vue router

- 什么是Vue router?

  - Vue Router是Vue.js官方的路由管理器。它和Vue.js的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有:
    - 嵌套的路由/视图表
    - 模块化的、基于组件的路由配置
    - 路由参数、查询、通配符
    - 基于Vue.js过渡系统的视图过渡效果
    - 细粒度的导航控制
    - 带有自动激活的CSS class的链接
    - HTML5历史模式或hash模式，在IE9 中自动降级
    - 自定义的滚动条行为

- 如何安装vue router?

  - 在要使用的项目文件目录下

  - ```bash
    # 也就是在node_modules目录下新增了vue-router代码
    npm install vue-router --save-dev
    ```

- 如何使用vue router？

  - 在router文件夹下面编写index.js文件进行路由页面导入，跳转即可

  - 在main.js中配置，加载路由

  - ```js
    import Vue from 'vue'
    import App from './App'
    import router from './router' // 自动扫描里面的路由配置 ./router/index.js
    
    Vue.config.productionTip = false
    
    /* eslint-disable no-new */
    new Vue({
      el: '#app',
      router, // 配置路由
      components: { App },
      template: '<App/>'
    })
    ```

  - 在App.vue中导入使用路由路径即可

    - ```vue
      <template>
        <div id="app">
          <img src="./assets/logo.png">
          <h1>Vue Router</h1>
          <router-link to="/content">内容页</router-link>
          <router-link to="/main">主页</router-link>
          <!-- 显示路由页面 -->
          <router-view></router-view>
        </div>
      </template>
      
      <script>
      export default {
        name: 'App'
      }
      </script>
      
      <style>
      #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
      }
      </style>
      ```

- 如何进行实际的前端开发？vue+elementUI？

  - 建议看视频：https://www.bilibili.com/video/BV18E411a7mC?p=16&spm_id_from=pageDriver
  - 整合前面所学即可

- 什么是嵌套路由？

  - 嵌套路由又称子路由，在实际应用中，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如:
  - ![image-20220127210150144](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127210150144.png)

  - ![image-20220127210448206](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127210448206.png)

- 路由链接如何传递参数？如何重定向？

  - https://www.bilibili.com/video/BV18E411a7mC?p=18&spm_id_from=pageDriver

- 路由模式？404？

  - 路由模式有两种

    - hash: 路径带#符号，如http://localhost/#/login
    - history:路径不带#符号，如http://localhost/login

  - ```vue
    export default new Router({
    	mode: 'history',
    	routes:[
    	{...}
    ]
    });
    ```

  - 处理404只需要创建一个NotFound.vue的视图组件，然后在router/index.js中添加路由路径即可

  - ```html
    ...
    {
    	path: "*",
    	component: NotFound
    }
    ```

- 路由钩子和异步请求？

  - beforeRouteEnter：在进入路由前执行
  - beforeRouteLeave：在离开路由前执行
  - https://www.bilibili.com/video/BV18E411a7mC?p=19
  - ![image-20220127213231695](1_Vue%E5%9F%BA%E7%A1%80.assets/image-20220127213231695.png)

