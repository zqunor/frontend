# Vue.js 和 Element UI 开发管理后台

## 创建 Vue 项目

1、安装node和npm

2、安装vue

```bash
npm install -g @vue/cli
```

3、创建项目

```bash
vue create vue-manage-system
```

4、安装插件

package.json文件

```json
{
  "name": "vue-manage-system",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "axios": "^0.19.2",
    "babel-polyfill": "^6.26.0",
    "core-js": "^3.6.5",
    "element-ui": "^2.13.2",
    "mavon-editor": "^2.9.0",
    "vue": "^2.6.11",
    "vue-cropperjs": "^4.1.0",
    "vue-i18n": "^8.18.2",
    "vue-quill-editor": "^3.0.6",
    "vue-router": "^3.3.4",
    "vue-schart": "^2.0.0",
    "vuedraggable": "^2.24.0"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "^4.4.0",
    "@vue/cli-plugin-eslint": "^4.4.0",
    "@vue/cli-service": "^4.4.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^6.2.2",
    "vue-template-compiler": "^2.6.11"
  },
  "eslintConfig": {
    "root": true,
    "env": {
      "node": true
    },
    "extends": [
      "plugin:vue/essential",
      "eslint:recommended"
    ],
    "parserOptions": {
      "parser": "babel-eslint"
    },
    "rules": {}
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead"
  ]
}
```

5、初始化项目目录

```info
node-modules/ : 项目开发依赖的模块
public/： 不会经过weback，需要用绝对路径引用
-- index.html
src/ : 项目源码位置
-- api/： 新建接口模块并使用 axios 实例
-- assets/： 模块资源目录。会被 webpack处理
-- components/： 模块组件
-- router/：路由配置目录(去中心化后的)
-- utils/： 项目公用的js文件
-- App.vue：主应用程序组件，项目的根组件
-- main.js：项目的核心入口文件
static/  : 资源目录。样式文件、字体文件、图片文件等
package.json： 项目配置文件，包含引入的插件的配置信息和一些与项目有关的配置
```

## 创建组件配置路由

### 使用路由

使用 Vue Router 一共需要五步：

1. 引入 vue-router
2. 执行 VueRouter
3. 设置路由配置并引入对应的组件
4. 实例化 VueRouter，并将路由配置文件传递到 VueRouter 实例上
5. 将 VueRouter 挂载到 Vue 实例中

### 嵌套路由

vue 通过 ` <router-view></router-view> ` 作为组件的出口。

其中`src/App.vue` 中的` <router-view></router-view> `是所有组件的根出口，通过路由调控的组件都会被渲染到这个出口位置。
在某个组件中继续使用组件，那增加的组件就是这个组件的子组件，父子组件之前也是通过` <router-view></router-view> `进行渲染的。而父组件是如何知道该子组件具体是哪个组件的？

这就需要在路由配置文件中配置：

```javascript
{
    // 设置 URL
    path: "/home",
    // 设置对应组件
    component: vHome,
    // 设置相应元信息
    meta: {
      title: "Home组件",
    },
    // 设置子组件
    children: [
        {
            // 要注意，以 / 开头的嵌套路径会被当作根路径。 使用嵌套组件无须设置嵌套的路径。
            path: "child",
            component: vChild,
        }
    ]
},
```

### 路由配置简化

原先的写法：

```javascript
// 先导入
import vHome from "../components/common/Home.vue";
import vChild1 from "../components/page/Children1.vue";
import vChild2 from "../components/page/Children2.vue";

// 再使用
{
    path: "/home",
    component: vHome,
     children: [
        {
            path: "child1",
            component: vChild1,
        },
        {
            path: "child2",
            component: vChild2,
        },
    ]
  },
```

实际可以简化成：

```javascript
{
    path: "/home",
    component: () => import("../components/page/Home.vue"),
    children: [
        {
            path: "child1",
            component: () => import("../components/page/Children1.vue"),
        },
        {
            path: "child2",
            component: () => import("../components/page/Children2.vue"),
        },
    ]
  },
```

## Element UI 框架

### 按需引入

根据需求对 Element UI 里的组件单独引入。

- 优点：体积小
- 缺点：增加了手动引入的工作量；代码繁杂紊乱。

1、安装 babel-plugin-component 插件（实现按需引入）

```bash
npm install  babel-plugin-component -D
```

2、修改项目中的 `babel.config.js` 文件

```js
module.exports = {
  presets: ["@vue/app", ["@babel/preset-env", { modules: false }]],
  plugins: [
    [
      "component",
      {
        libraryName: "element-ui",
        styleLibraryName: "theme-chalk",
      },
    ],
  ],
};
```

3、引入并加载组件

```js
// 在 main.js 中 import App from './App.vue' 下方添加以下代码
// 引入所需样式组件
import { Button, Select, Option } from "element-ui";

// 执行引入的样式组件
Vue.component(Button.name, Button);
Vue.component(Select.name, Select);
Vue.component(Option.name, Option);
/* 或写为
 * Vue.use(Button)
 * Vue.use(Select)
 * Vue.use(Option)
 */
```

全局配置：

```js
// main.js
Vue.prototype.$ELEMENT = { size: "small", zIndex: 3000 };
```

- size：用于改变组件的默认尺寸，默认尺寸为 "small"。
- zIndex：用于设置弹框的初始 z-index（默认为 2000)。

### 全局引入

- 优点：方便
- 缺点：项目体积大

引入方式：

```js
// 引入 Element UI
import ElementUI from "element-ui";
// 单独引入样式文件
import "element-ui/lib/theme-chalk/index.css";

// 执行 ElementUI
Vue.use(ElementUI, { size: "small", zIndex: 3000 });

Vue.config.productionTip = false;
```

### element ui 小案例

1、效果预览：

![element_ui_demo.png](https://ws1.sinaimg.cn/large/005EgYNMly1gh0750x509j30q80bjdgz.jpg)

2、基本使用

(0) 容器布局

- 外层容器：`el-container`
- 侧边栏容器：`el-aside`
- 主要区域容器：`el-main`

(1) 折叠菜单

- `el-menu`
- `el-submenu`
- `template` 设置每一级菜单的显示（标题、图标）
- `el-menu-item-group` 包裹一个子菜单组
- `el-menu-item`： 子菜单元素

```js
<el-menu :default-openeds="['1']">
    <el-submenu index="1">
        <template slot="title">
            <i class="el-icon-message"></i>导航
        </template>
        <el-menu-item-group>
            <template slot="title">分组一</template>
            <el-menu-item index="1-1">选项 1</el-menu-item>
            <el-menu-item index="1-2">选项 2</el-menu-item>
        </el-menu-item-group>
    </el-submenu>
</el-menu>
```

(2) 下拉菜单

- `el-dropdown`
- `el-dropdown-menu`
- `el-dropdown-item`： 子菜单元素

```js
<el-dropdown>
    <i class="el-icon-setting" style="margin-right: 15px"></i>
    <el-dropdown-menu slot="dropdown">
        <el-dropdown-item>查看</el-dropdown-item>
        <el-dropdown-item>新增</el-dropdown-item>
        <el-dropdown-item>删除</el-dropdown-item>
    </el-dropdown-menu>
</el-dropdown>
```

(3) 表格

- `el-table`
- `el-table-column`

```js
<el-table :data="tableData" style="border: 1px solid #eee">
    <el-table-column prop="date" label="日期" width="140"></el-table-column>
    <el-table-column prop="name" label="姓名" width="120"></el-table-column>
</el-table>
```

表格数据填充

```js
export default {
  data() {
    const item = {
      date: "2019-09-17",
      name: "shiyanlou"
    };
    return {
      tableData: Array(10).fill(item)
    };
  }
};
```

## 完善组件模块

### 非父子组件通信

- 父组件Home: 挂载多个同级子组件
- 子组件A: 触发事件(任意)，传出数据($emit事件)
- 子组件B：监听事件(mount)，接收数据($on事件)

0、实现载体

```js
// utils/bus.js
import Vue from "vue";

// 使用 Event Bus来实现非父子组件之间的通信
const bus = new Vue();

export default bus;
```

1、父组件挂载

```vue
<template>
  <div class="home-wrap">
    <span>Home组件</span>
    <v-fir></v-fir>
    <v-sec></v-sec>
  </div>
</template>
<script>
  import vFir from "../page/First";
  import vSec from "../page/Second";
  export default {
    components: {
      vFir,
      vSec,
    },
  };
</script>
```

2、子组件A 制定触发事件，设置并传出通信数据

```vue
// components/page/First.vue
<template>
  <div class="wrapper">
    <hr />
    <br />
    <button @click="send">send</button>
  </div>
</template>
<script>
  import Bus from "../../utils/bus";
  export default {
    data() {
      return {
        message: "",
      };
    },
    methods: {
      send() {
        this.message = "这是传递到 second 中的数据!";
        Bus.$emit("msg", this.message);
      },
    },
  };
</script>
```

3、子组件B 设置监听，接收通信数据

```vue
// components/page/Second.vue
<template>
  <div class="wrapper">
    <hr />
    <br />
    <p>{{message}}</p>
  </div>
</template>
<script>
  import Bus from "../../utils/bus";
  export default {
    data() {
      return {
        message: "",
      };
    },
    mounted() {
      let self = this;
      Bus.$on("msg", (e) => {
        self.message = e;
      });
    },
  };
</script>
```

4、设置路由

```js
// router/js
// 设置 URL
path: "/home",
// 设置对应组件
component: () => import("../components/common/Home.vue"),
children: [
    {
      path: "first",
      component: () => import("../components/page/First.vue"),
    },
    {
      path: "second",
      component: () => import("../components/page/Second.vue"),
    },
]
```