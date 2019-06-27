---
layout: default
title: 软件设计文档
---

# 软件设计文档
{:.no_toc}

* 目录
{:toc}

## 1. Client

### 1.1 用户端

#### 1.1.1 技术选型及理由

**微信小程序框架**

我们选择了微信小程序开发框架作为用户端的前端框架。

* 小程序在视图层与逻辑层间提供了数据传输和事件系统，可以让开发者能够更好地专注于数据与逻辑。
* 框架的核心是一个响应的数据绑定系统，可以让数据与视图非常简单地保持同步。当做数据修改的时候，只需要在逻辑层修改数据，视图层就会做相应的更新。因此，框架可以提供很好的用户交互体验支持，也减少了开发者在交互上的精力。
* 框架管理了整个小程序的页面路由，可以做到页面间的无缝切换，并给以页面完整的生命周期，开发者只需要简单的进行页面注册。
* 框架提供基础的组件和丰富的微信原生 API。通过框架，可以直接使用自带微信风格样式的组件进行页面设计，还可以方便的调起微信提供的能力，因此可以通过简单、高效的方式让开发者可以在微信中开发具有原生 APP 体验的服务。
* 微信小程序的普及性可以推动挣闲钱系统的推行，并且免安装以及微信绑定等特性对于用户也更加快捷和便利。

基于以上理由，选择微信小程序作为前端框架对用户和开发者都十分友好。

#### 1.1.2 架构设计

**WePY小程序组件化开发框架**

WePY 是一款让小程序支持组件化开发的框架，通过预编译的手段让开发者可以选择自己喜欢的开发风格去开发小程序。WePY 框架在开发过程中参考了 Vue 等现有框架的一些语法风格和功能特性，对原生小程序的开发模式进行了再次封装，更贴近于 MVVM 架构模式, 并支持ES6/7的一些新特性。

特性：

* 开发风格：接近于 Vue.js，支持组件 Props 传值，自定义事件、组件分布式复用Mixin、计算属性函数。computed、模板内容分发slot等等
* 组件化：组件化开发，完美解决组件隔离，组件嵌套，组件通信等问题。
* NPM：支持使用第三方 npm 资源，自动处理 npm 资源之间的依赖关系，完美兼容所有无平台依赖的 npm 资源包。
* Promise：通过 polyfill 让小程序完美支持 Promise，解决回调烦恼。
* 优化：对小程序本身的优化，如请求列对处理，优雅的事件处理，生命周期的补充，性能的优化等等。

#### 1.1.3 模块划分

1. 页面模块

根据用例图和UI设计确定页面，根据功能有以下页面：

* 用户
    1. 用户授权登录获取信息
    2. 修改个人信息
    3. 身份实名认证
    4. 账户余额充值。
* 任务
    1. 获取当前任务列表
    2. 获取用户自己发布的任务列表
    3. 获取用户自己接受的任务列表
    4. 获取任务详情
    5. 用户发布、中止、接受、放弃、完成任务。
* 组织
    1. 创建组织
    2. 加入组织
    3. 退出组织
* 关于
    1. 隐私权条款
    2. 用户行为规范
    3. 任务发布条例

页面渲染、路由控制等操作都由微信小程序框架完成，采用组件化开发的思想，将每个页面抽象为一个组件，页面中也可以包括子组件。

2. 组件模块

根据功能需求，定义了以下组件：

* 日期时间选择组件，解决小程序组件日期时间无法同时选择的问题
* 弹起多选组件，对多选组件进行封装并实现弹起功能
* 滑动组件，自动滑动图片，使样式更美观
* list组件，根据样式和交互事件的不同封装成了任务list、用户list、组织list

3. 资源模块

系统的每个界面可能会使用到公共的资源

* style样式库，包括阿里的iconfont和微信官方weui样式库；
* assets触发静态资源，如图片、图标等。

4. 配置模块

小程序的全局的配置文件、逻辑文件构成配置模块

消除

最后用户端的结构如下：

```
.
├── package.json                    # 项目总体信息，依赖列表
├── package-lock.json               # 依赖 lock
├── project.config.json             # 小程序部分设置项
├── src                             # 小程序源代码部分
│   ├── app.wpy                     # 小程序入口
│   ├── assets                      # 静态资源
│   │   └── ...
│   ├── components                  # 复用组件
│   │   └── ...
│   ├── lib                         # 杂项库
│   │   ├── cookieJar.js            # cookie库
│   │   └──  request.js             # 网络请求库
│   ├── style                       # wxss 全局变量
│   │   ├── iconfont.wxss           # iconfont 资源
│   │   └── weui.wxss               # weui 样式库
│   ├── mixins                      # 混合组件
│   │   └── ...
│   └── pages                       # 小程序页面
│       ├── index.wpy               # 主页
│       ├── authorize.wpy           # 授权页面
│       ├── search.wpy              # 搜索页面
│       ├── about.wpy               # 小程序关于页面
│       ├── user                    # 用户
│       │   ├── mine.wpy            # 我的 页面
│       │   ├── info.wpy            # 信息修改 页面
│       │   ├── prove.wpy           # 身份认证 页面
│       │   ├── balance.wpy         # 余额 页面
│       │   ├── team.wpy            # 组织 页面
│       │   ├── teamdetail.wpy      # 组织详情 页面
│       │   └── setting.wpy         # 设置 页面
│       ├── task                    # task
│       │   ├── task.wpy            # 任务 页面
│       │   ├── published.wpy       # 我的已发布任务 页面
│       │   ├── accepted.wpy        # 我的已接受任务 页面
│       │   ├── detail.wpy          # 任务详情 页面
│       │   ├── accepter.wpy        # 任务的接收者 页面
│       │   ├── job.wpy             # 任务对应工作 页面
│       │   └── jobdetail.wpy       # 工作详情 页面
│       ├── publish                 # 发布
│       |   ├── publish.wpy         # 发布入口 页面
│       |   ├── normaltask.wpy      # 普通任务发布 页面
│       |   └── wjx.wpy             # 问卷星问卷任务发布 页面
|       └── about                   # 关于
|           ├── behaviorItem.wpy    # 用户行为规范
|           ├── privacyItem.wpy     # 隐私权条款
|           └── publishItem.wpy     # 用户发布条例
└── wepy.config.js                  # wepy 构建配置
```

#### 1.1.4 软件设计技术

**WePY组件化**

WePY的开发风格接近于Vue.js，提供了类vue的MVVM+组件化模式，通过.wpy文件进行组件化开发。

### 1.2 管理员端

#### 1.2.1 技术选型及理由

**webpack+vue+vue-router+ElementUI+axios+ECharts**

- vue：一套用于构建用户界面的渐进式框架
- vue-router：Vue.js的官方路由器
- ElementUI：一个ui库，它不依赖于vue。但是却是当前和vue配合做项目开发的一个比较好的ui框架。
- webpack：用来打包js工程的工具
- axios：一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中
- ECharts：一个纯 Javascript 的图表库，可以流畅的运行在 PC 和移动设备上

**Vue**

Vue是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

**vue-router**

vue-router 和 vuex 是官方提供的前端路由和数据状态管理的库，能够完美地结合 Vue 框架，实现前端路由和应用数据状态管理，代码结构清晰，程序复杂度降低。

**Element UI**

一个ui库，它不依赖于vue。但是却是当前和vue配合做项目开发的一个比较好的ui框架。
该框架封装了多种 UI 组件，使用方便，极具效率，设计简洁直观的操作流程；语言表达清晰且表意明确，让用户快速理解进而作出决策；界面简单直白，让用户快速识别而非回忆，减少用户记忆负担。

**webpack**

当下前端最流行的构建打包工具，利用 webpack 搭配相应的 loader，可以在前端项目中使用 es6 进行开发，效率更高，开发完成后，webpack 还可以将代码编译到 es5 以兼容大部分浏览器；
webpack 2优化了构建打包的算法，优化前端的模块加载，整合各个文件的代码、图片、字体、样式表等等，使得构建打包后的产品代码体积更小，也减少了这些资源在传输给用户时的时间损耗。

**axios**

axios是基于 Promise 的 HTTP 请求客户端，可同时在浏览器和 node.js 中使用
在浏览器中发送 XMLHttpRequests 请求；在 node.js 中发送 http请求；支持 Promise API；拦截请求和响应；转换请求和响应数据；自动转换 JSON 数据；客户端支持保护安全免受 XSRF 攻击；简单、轻量，不会影响前端的加载速度。

**ECharts**

是百度的一个开源的数据可视化工具，一个纯 Javascript 的图表库，能够在 PC 端和移动设备上流畅运行，兼容当前绝大部分浏览器（IE6/7/8/9/10/11，chrome，firefox，Safari等），底层依赖轻量级的 Canvas 库 ZRender，ECharts 提供直观，生动，可交互，可高度个性化定制的数据可视化图表。创新的拖拽重计算、数据视图、值域漫游等特性大大增强了用户体验，赋予了用户对数据进行挖掘、整合的能力。
ECharts 属于开源软件，并且我们提供了非常炫酷的图形界面，特色是地图，另外还提供了柱状图、折线图、饼图、气泡图及四象限图等； 
ECharts 使用简单，在官网中为我们封装了 JS，只要会引用就会得到完美的展示效果； 
ECharts 种类多，ECharts 实现简单，各类图形都有；相应的模板，还有丰富的 API 及文档说明，非常详细； 
ECharts 兼容性好，基于HTML5，有着良好的动画渲染效果。 

#### 1.2.2 架构设计

挣闲钱后台管理系统主要包括用户身份认证，审核发布的任务和查看用户目录等开发内容，还应提供配置项用于构建项目打包时的目标路径及服务器地址等信息，同时还要进行单元测试与集成测试。根据需要，项目确定有以下四个包：

- build：开发环境和构建项目的代码

- config：项目的webpack打包配置

- node_modules：elementui的控件样式源码

- src：项目的源代码，主要都在src中进行开发

- test：单元测试等系列测试代码

#### 1.2.3 模块划分

根据业务逻辑和UI设计图，将挣闲钱后台管理系统前端分为6个页面：

- login：后台管理者登录页面
- index：后台管理功能页面
- changewd：管理员密码修改页面
- user_exam：用户信息认证页面
- user_list：用户信息列表页面
- information：管理员信息页面
- task_list：任务信息列表页面
- task_exam：任务审核列表页面

挣闲钱后台管理系统各界面渲染、路由控制等操作都在前端通过axios完成，采取组件化开发的思想，将每个页面抽象为一个组件，页面中也可以包含子组件，实现将多个页面在首页作为一个控件进行跳转。

系统的多个功能界面全部整合在 components 中；
系统的功能界面作为主页index的<router-view>控件进行显示；
系统的界面跳转由前端路由 router 进行控制；
系统的与服务器交互的接口在config中实现配置
assets 存放前端的静态资源，例如图片、图标和字体等。

综合考虑使用的技术框架，并根据应用需要的组件，可以得到详细的项目结构如下所示。如果过需要新增界面、功能，引入新组件，也可以很方便地将新内容添加到项目中去。

```
.
├── index.html # 管理系统主界面框架
├── build      # 提供开发环境和构建项目的代码
├── config     # 项目的webpack打包配置
├── server     # 处理web端和服务器数据传递涉及的跨域等问题
├── src        # 商家管理系统开发代码
|   ├── api # axios封装成函数
|   ├── assets # 存放前端的静态资源，例如图片、图标和字体等
│   ├── components # 页面
│   │   ├── changepwd.vue # 修改密码页面
|   |   ├── home.vue       # 首页统计页面
│   │   ├── information.vue  # 管理员信息页面
│   │   ├── user_exam.vue  # 用户审核页面
│   │   ├── user_list.vue  # 用户列表页面
│   │   ├── task_list.vue  # 任务目录页面
│   │   └── task_exam.vue  # 任务审核页面
|   |
│   ├── router # 页面组件
│   │   └── index.js # 页面跳转路由
|   |
│   ├── view # 主界面页面
│   |   ├── login.vue       # 登录界面
│   |   └── index.vue       # 主页
|   |
│   ├── service   # 公共服务
│   |   ├── api.js    # 与服务器进行数据传递的地址与方法处理
│   |   └── auth.js   # 授权操作
|   |
│   ├── main.js     # 全局初始化
|   └── App.vue   # 网页进入入口  
|   |
├── test       # 测试
|   ├── unit       # 单元测试
|   └── e2e        # 端到端测试
|   |
├── .babelrc       # babel编译参数，vue开发需要babel编译 
└── package.json   # 项目文件，记载着一些命令和依赖还有简要的项目描述信息
```

## 2. Server
## 2. Server

### 2.1 技术栈

​	我们主要使用了Nginx、Python Flask、Redis、Mysql，选择这些技术的理由如下：

##### Nginx:

- **Nginx**是开源的轻量级的高性能服务器。Flask等Web框架易于开发但其所带web服务器不适应生产环境。在生产环境中一般使用Nginx作为服务器底层，用于处理静态文件的访问以及将请求转发给上层Flask、Django等web框架做其他处理。

##### Python Flask：

- **Flask**是使用python编写的Web应用框架，内置开发服务器和快速调试器，集成支持单元测试。

- **Flask**遵循极简主义原则，为项目的开发阶段提供了更多控制权，提供了更高的迭代速度。

- **Flask**的代码更加轻巧简洁，加快了前后端访问测试的速度，且高可扩展性加快开发速度。

##### Redis：

- Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。在此项目中，使用Redis存储session，实现小程序的登录持久化。

Mysql：

- 代码开源，不需要支付额外的费用
- 使用标准的SQL查询语句，后台开发成员都有一定的基础，降低学习成本。
- MySQL是完全网络化的，可以用过网络对服务器上的数据库进行访问，使开发更加便捷
- 配合使用PyMySQL操作库，在后端开发中更加快捷的实现事务、存储过程、批量执行、数据库读写异常处理的开发，

### 2.2 架构设计

![系统架构图](./structure.png)


### 2.3 模块划分

​	我们将后台分为**certificationServer**和**swaggerServer**两个部分，其中**certificationSever**用于与管理平台进行交互，为管理员提供用户管理、信息认证等功能接口；而**swaggerServer**用于与移动端的小程序进行交互，实现单独用户的创建、认证等一系列功能。

- ​	文件结构组织

```
.
├── server
│   ├── main.py
│   ├── c_server.py
│   ├── certificationServer
│   │   ├── routers
│   │   │   ├── prove.py
│   ├── swagger_server
│   │   ├── config.py
│   │   ├── constants.py
│   │   ├── encoder.py
│   │   ├── util.py
│   │   ├── model*
│   │   ├── moudules*
│   │   ├── controllers*
│   │   ├── routers
│   │   │   ├── auth.py
│   │   │   ├── img.py
│   │   │   ├── prove.py
│   │   ├── swagger
│   │   │   ├── swagger.yaml
│   │   ├── test
│   │   │   ├── ...
│   │   ├── utils
│   │   │   ├── ...
```

- `model`类代码文件结构

```
├── model
│   ├── Adminstrator.py
│   ├── Group.py
│   ├── Task.py
│   ├── User.py
```

- `module`包含各类管理系统以及登录持久化的实现:

```
├── moudules
│   ├── accessControlSystem.py
│   ├── AdminPlatform.py
│   ├── loginPersistent.py
│   ├── mailSystem.py
│   ├── taskManagementSystem.py
│   ├── userManagementSystem.py
```

- `controllers`包含各类路由所分发向的controller文件。真正的Controller模块实际上是controller文件以及modules文件夹下的同名模块共同结合发挥作用

```
├── controllers
│   ├── group_controller.py
│   ├── system_controller.py
│   ├── task_controller.py
│   ├── user_controller.py
```

### 2.4 软件设计技术

#### 2.4.1 Objected-Oriented Programming

​	我们将数据库中的主要模型封装为一个类型，使用 **\_\_slot\_\_**设置实例属性节省内存，同时避免了对关键词的操作失误。对于每一个模型，我们都为其编写单独的Table类，将所有的底层数据库操作函数封装在其中，这样在进行更高层的函数设计时，就可以不用考虑数据库层面的操作。

#### 2.4.2 LoginPersistent 登录持久化

登录持久化层利用了session, redis,flask以及python的装饰器实现。

在进行过登录后，loginPersistent模块存储下用户的关键信息至redis的session数据中。loginPersistent模块定义了一个中间件，会在用户访问时与调用，把session所存储的数据再从数据库进行更新，并临时存储用户的所有信息，供全局使用。

#### 2.4.3 AccessCcontrol 访问控制
访问控制层是在登录持久化的基础上实现的
访问控制层提供了login required作为登录需求装饰器，identity required作为用户身份需求装饰器， 还有owner requied拥有者需求装饰器等供其他路由方法方便的进行访问控制。
登录持久化中间件获得用户的身份信息。访问控制模块也定义了一个身份装饰器获取身份信息并映射到不同的身份类型。

#### 2.4.4 前后端分离
由前端通过swagger工具按照RESTful 风格定义了API, 后端服务器通过swagger-code-gen工具生成路由以及controller。后端负责开发各个management模块的功能，在生成好的controller中进行调用。

