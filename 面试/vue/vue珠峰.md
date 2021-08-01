**第一章：**



### vue

灵活  方便 比较好学 实用

vue+component+vue-router+vuex

### 框架和库

库:相当于功能比较全的产品，用时取库中的一部分产品来实现我们的功能
框架:为了解决一系类问题而生产出的产品,使用时只能按照他规定的模板来使用
vue是MVVM模式框架 

### {{}}语法

### 实例上的属性

### 指令  v-xxx  有特定的功能 ，可以操作DOM元素

v-for 遍历  想遍历谁，就在谁上面加v-for   key保证元素唯一性
v-text  默认会渲染成文本
v-html  渲染成标签  
v-once  只渲染一次，再次更改时,它是从缓存中获取，而不是再最新的数据
v-bind 简写成: 绑定动态的属性
v-on 简写成@   绑定事件
v-show true/false 更改DOM样式，让其显示和隐藏
v-if/v-else  操作DOM元素
v-model  双向绑定







**第二章**



### modifies 修饰符

v-for 数组，对象 ，数字，字符串
v-once 
v-html 
v-bind 简写:  
v-on  简写@

- 事件修饰符 .stop .prevent .capture(捕获) .self .once .passive (表示处理事件函数中不会调用preventDefault函数，就会减少了额外的监听，从而提高了性能；所以不能和.prevent修饰符一同使用，否则浏览器会报错)
  v-model 操作表单元素 实现双向绑定
- .number(强制转换成数类型) .lazy(相当于调用change事件） .trim(去掉左右空格)
  v-if/v-else 操作DOM元素的显示和隐藏
  v-show 通过样式来控制DOM元素显示和隐藏

### 指令 directive

### watch和computed

### animate

- before-enter 触发enter之前
- before-leave 触发leave之前
- enter 进入动画过程
- leave 离开动画过程
- after-enter 进入动画结束
- after-leave 离开动画结束

### 



**第三章**



## 组件

 组件的好处：可复用 可以提高协同开发的效率，方便后期的维护和修改 可以减少渲染

## 全局组件&局部组件

## 安装工具

- npm install @vue-cli@3 -g
- npm install -g @vue/cli-service-global@3 -g
- vue serve App.vue

## 组件通信

- 父传递给子数据 props emit sync  
- $attrs(批量的把属性往下传) $listeners(批量的可以把事件往下传)
- provide inject  和 context （可以在父组件中声明一个公共数据），在子组件中可以注入原理 (比较混乱，名称问题 他不会在业务代码中使用) 组件库 多级通信为了方便你可以使用provide
- $parent $children
- ref 获取真实dom元素，如果放到组件上 代表的是 当前组件的实例 ,父组件中可以直接获取子组件的方法或者数据
- eventbus ($parent,$children) 绑定$on 只能通过绑定$on的那个组件来触发 (混乱)

### Props传递数据

```tree
components
   ├── Grandson1.vue // 孙子1
   ├── Grandson2.vue // 孙子2
   ├── Parent.vue   // 父亲
   ├── Son1.vue     // 儿子1
   └── Son2.vue     // 儿子2
```

在父组件中使用儿子组件

```html
<template>
 <div>
  父组件:{{mny}}
  <Son1 :mny="mny"></Son1>
 </div>
</template>
<script>
import Son1 from "./Son1";
export default {
 components: {
  Son1
 },
 data() {
  return { mny: 100 };
 }
};
</script>
```

子组件接受父组件的属性

```
<template>
 <div>子组件1: {{mny}}</div>
</template>
<script>
export default {
 props: {
  mny: {
   type: Number
  }
 }
};
</script>
```

### $emit使用

子组件触发父组件方法,通过回调的方式将修改的内容传递给父组件

```html
<template>
 <div>
  父组件:{{mny}}
  <Son1 :mny="mny" @input="change"></Son1>
 </div>
</template>
<script>
import Son1 from "./Son1";
export default {
 methods: {
  change(mny) {
   this.mny = mny;
  }
 },
 components: {
  Son1
 },
 data() {
  return { mny: 100 };
 }
};
</script>
```

子组件触发绑定自己身上的方法

```html
<template>
 <div>
  子组件1: {{mny}}
  <button @click="$emit('input',200)">更改</button>
 </div>
</template>
<script>
export default {
 props: {
  mny: {
   type: Number
  }
 }
};
</script>
```

> 这里的主要目的就是同步父子组件的数据,->语法糖的写法

#### .sync

```html
<Son1 :mny.sync="mny"></Son1>
<!-- 触发的事件名 update:(绑定.sync属性的名字) -->
<button @click="$emit('update:mny',200)">更改</button>
```

#### v-model

```html
<Son1 v-model="mny"></Son1>
<template>
 <div>
  子组件1: {{value}} // 触发的事件只能是input
  <button @click="$emit('input',200)">更改</button>
 </div>
</template>
<script>
export default {
 props: {
  value: { // 接收到的属性名只能叫value
   type: Number
  }
 }
};
</script>
```

### $parent、$children

继续将属性传递

```html
<Grandson1 :value="value"></Grandson1>
<template>
 <div>
  孙子:{{value}}
  <!-- 调用父组件的input事件 -->
  <button @click="$parent.$emit('input',200)">更改</button>
 </div>
</template>
<script>
export default {
 props: {
  value: {
   type: Number
  }
 }
};
</script>
```



### $attrs、$listeners

#### $attrs

批量向下传入属性

```html
<Son2 name="小珠峰" age="10"></Son2>

<!-- 可以在son2组件中使用$attrs属性,可以将属性继续向下传递 -->
<div>
  儿子2: {{$attrs.name}}
  <Grandson2 v-bind="$attrs"></Grandson2>
</div>


<template>
 <div>孙子:{{$attrs}}</div>
</template>

```

#### $listeners

批量向下传入方法

```html
<Son2 name="小珠峰" age="10" @click="()=>{this.mny = 500}"></Son2>
<!-- 可以在son2组件中使用listeners属性,可以将方法继续向下传递 -->
<Grandson2 v-bind="$attrs" v-on="$listeners"></Grandson2>

<button @click="$listeners.click()">更改</button>

```

### Provide & Inject

#### Provide

在父级中注入数据

```javascript
provide() {
  return { parentMsg: "父亲" };
},

```

#### Inject

在任意子组件中可以注入父级数据

```javascript
inject: ["parentMsg"] // 会将数据挂载在当前实例上

```

### Ref使用

获取组件实例

```html
<Grandson2 v-bind="$attrs" v-on="$listeners" ref="grand2"></Grandson2>
mounted() { // 获取组件定义的属性
  console.log(this.$refs.grand2.name);
}

```

### EventBus

用于跨组件通知(不复杂的项目可以使用这种方式)

```javascript
Vue.prototype.$bus = new Vue();
```

Son2组件和Grandson1相互通信

```javascript
 mounted() {
  this.$bus.$on("my", data => {
   console.log(data);
  });
 },
```

```javascript
mounted() {
  this.$nextTick(() => {
   this.$bus.$emit("my", "我是Grandson1");
  });
 },
```







**第四章**



## Vue中的生命周期

- beforeCreate 在实例初始化之后，数据观测(data observer) 和 event/watcher 事件配置之前被调用。
- created 实例已经创建完成之后被调用。在这一步，实例已完成以下的配置：数据观测(data observer)，属性和方法的运算， watch/event 事件回调。这里没有$el
- beforeMount 在挂载开始之前被调用：相关的 render 函数首次被调用。
- mounted el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子。
- beforeUpdate 数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。
- updated 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
- beforeDestroy 实例销毁之前调用。在这一步，实例仍然完全可用。
- destroyed Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。 该钩子在服务器端渲染期间不被调用。

### 钩子函数中该做的事情

- created 实例已经创建完成，因为它是最早触发的原因可以进行一些数据，资源的请求。
- mounted 实例已经挂载完成，可以进行一些DOM操作
- beforeUpdate 可以在这个钩子中进一步地更改状态，这不会触发附加的重渲染过程。
- updated 可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致更新无限循环。 该钩子在服务器端渲染期间不被调用。
- destroyed 可以执行一些优化操作,清空定时器，解除绑定事件





**第五章**



实现树菜单接口
引入element-ui
通过axios调用接口
在组件中获取数据
格式化数据-转化树列表
自定义tree组件
扩展操作列表
点击修改切换输入框
确认修改
取消修改
删除文件或文件夹
调用接口删除文件

let express = require('express');
let app = express();
app.all('*', function (req, res, next) {
  res.header('Access-Control-Allow-Origin', '*');
  if(req.method === 'OPTIONS'){
      return res.send();
  }
  next();
});
app.get('/getTreeList',function(req,res){
  res.json({
      code:0,
      parent:[
              {name:'文件夹1',pid:0,id:1},
              {name:'文件夹2',pid:0,id:2},
              {name:'文件夹3',pid:0,id:3},
              {name:'文件夹1-1',pid:1,id:4},
              {name:'文件夹2-1',pid:2,id:5},
      ],
      child:[
              {name:'文件1',pid:1,id:10001},
              {name:'文件2',pid:1,id:10002},
              {name:'文件2-1',pid:2,id:10003},
              {name:'文件2-2',pid:2,id:10004},
              {name:'文件1-1-1',pid:4,id:10005},
              {name:'文件2-1-1',pid:5,id:10006}
      ]
  });
});
app.listen(3000);





**第六章**



# JWT认证

## 一.什么是jwt？

- JSON Web Token（JWT）是目前最流行的跨域身份验证解决方案

  **解决问题**：session不支持分布式架构，无法支持横向扩展，只能通过数据库来保存会话数据实现共享。如果持久层失败会出现认证失败。

  **优点**：服务器不保存任何会话数据，即服务器变为无状态，使其更容易扩展。

### JWT包含了使用`.`分隔的三部分

- Header 头部 

  ```javascript
  { "alg": "HS256", "typ": "JWT"}   
  // algorithm => HMAC SHA256
  // type => JWT
  ```

- Payload 负载、载荷

  ```
  JWT 规定了7个官方字段
  iss (issuer)：签发人
  exp (expiration time)：过期时间
  sub (subject)：主题
  aud (audience)：受众
  nbf (Not Before)：生效时间
  iat (Issued At)：签发时间
  jti (JWT ID)：编号
  ```

- Signature 签名

  对前两部分的签名，防止数据篡改

  ```javascript
  HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret)
  ```

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 api.example.com/?token=xxx）。Base64 有三个字符`+`、`/`和`=`，在 URL 里面有特殊含义，所以要被替换掉：`=`被省略、`+`替换成`-`，`/`替换成`_` 。这就是 Base64URL 算法。

### 使用方式

HTTP 请求的头信息`Authorization`字段里面

```
Authorization: Bearer <token>
```

通过url传输

```
http://www.xxx.com/pwa?token=xxxxx
```

如果是post请求也可以放在请求体中

## 二.服务端返回TOKEN

```javascript
let express = require('express');
let app = express();
let bodyParser = require('body-parser');
let jwt = require('jsonwebtoken');
app.use((req,res,next)=>{
    res.header("Access-Control-Allow-Origin", "http://localhost:8080");
    res.header("Access-Control-Allow-Methods", "GET,HEAD,OPTIONS,POST,PUT");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, Authorization");
    if(req.method.toLowerCase() === 'options'){
        return res.end();
    }
    next();
})
app.use(bodyParser.json());
let secret = 'zfjg';
app.get('/test',(req,res)=>{
    res.end({test:'test'})
})
app.post('/login',(req,res)=>{
   let {username} = req.body;
   if(username === 'admin'){ // 如果访问的是admin 种植cookie
        res.json({
            code:0,
            username:'admin',
            token:jwt.sign({username:'admin'},secret,{
                expiresIn:20  
            })
        })
   }else{
       res.json({
           code:1,
           data:'用户名不存在'
       })
   }
});
app.get('/validate',(req,res)=>{
    let token = req.headers.authorization;
    jwt.verify(token,secret,(err,decode)=>{ // 验证token的可靠性
        if(err){
            return res.json({
                code:1,
                data:'token失效了'
            })
        }else{
            res.json({ 
                username:decode.username,
                code:0, // 延长tokne的过期时间
                token:jwt.sign({username:'admin'},secret,{
                    expiresIn:20  
                })
            })
        }
    });
});

app.listen(3000);
```

## 三.路由配置

- Home.vue     首页
- Profile.vue  个人中心
- Login.vue    登录页面

```javascript
export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'home',
      component: Home,
    },
    {
      path: '/profile',
      name: 'profile',
      component: Profile,
      meta: { needLogin: true }, // 必须要登录才能访问
    },
    {
      path: '/login',
      name: 'login',
      component: Login,
    },
  ],
});
```

###login 
 输入框 用户名 和按钮



## 四.axios封装

```javascript
import axios from 'axios';


class FetchData {
  constructor() {
    this.baseURL = process.env.NODE_ENV === 'development' ? 'http://localhost:3000' : '/'; // 请求路径 
    this.timeout = 3000; // 设置超时时间
  }

  setInterceptor(instance) { // 设置拦截器
    instance.interceptors.request.use(config => {
      config.headers.Authorization = `${localStorage.getItem('token')}`;
      return config; // 增加token
    }, (err) => {
      Promise.reject(err);
    });

    instance.interceptors.response.use(res => res.data, (err) => {
      Promise.reject(err);
    });
  }

  request(request) {
    const instance = axios.create();
    const config = {
      baseURL: this.baseURL,
      timeout: this.timeout,
      ...request,
    }; // 合并配置
    this.setInterceptor(instance);
    return instance(config);
  }
}

export default new FetchData();
```

## 五.测试接口

```javascript
export const getTest = () => fetchData.request({ url: '/test' });
export const login = username => fetchData.request({
  url: '/login',
  method: 'POST',
  data: {
    username,
  },
});
export const validate = () => fetchData.request({ url: '/validate' });
```

## 六.在vuex中发送请求

```javascript
export default new Vuex.Store({
  state: {
    username: '',
  },
  mutations: {
    setUsername(state, username) {
      state.username = username;
    },
  },
  actions: {
    async login({ commit }, username) {
      const r = await login(username); // 登录成功后返回用户名信息
      if (r.token) { // 如果有返回token说明成功
        commit('setUsername', username); // 将用户存入state中
        localStorage.setItem('token', r.token); // 将token存放起来
      } else { // 否则返回失败的promise
        return Promise.reject(r);
      }
    },
  },
});
```

## 七.权限认证

```javascript
async validate({ commit }) {
    const r = await validate();
    if (r.code === 1) {
        return false;
    }
    commit('setUsername', r.username);
    localStorage.setItem('token', r.token); // 将token存放起来
    return true;
}
```

判断用户访问权限

```javascript
router.beforeEach(async (to, from, next) => {
  // 如果不需要校验可以设置白名单
  const isLogin = await store.dispatch('validate');
  if (isLogin) {
    // 如果是登录
    if (to.name === 'login') {
      next('/profile');
    } else {
      next();
    }
  } else {
    const flag = to.matched.some(item => item.meta.needLogin);
    if (flag) {
      next('/login');
    } else {
      next();
    }
  }
});

```

## 八、需要掌握的

1. Data中为什么是个函数返回

```
	因为vue的data相当于他的数据是全局的，全局数据不能污染，如果加上return的话就只在当前组件生效，不会影响到其他的组件。
```

	2. vue最大的特点是？

```
	Vue最大的特点就是数据驱动视图
```

3. slot的使用

![image-20200626152013286](C:\Users\醉倾城\AppData\Roaming\Typora\typora-user-images\image-20200626152013286.png)