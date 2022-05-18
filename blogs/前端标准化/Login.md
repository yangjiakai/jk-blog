---
title: 通用登录方案
date: 2022-04-18 15:45:35
permalink: /pages/frontend-standard/login
sidebar: "auto"
author: Yang J.K.
# keys:
#   - 123456
categories:
  - 前端标准化
tags:
  - 前端登录标准化 
  - 前端标准化
---


## 登录方案解析

1. axios模块封装
2. 接口请求模块封装
3. 登录请求动作
4. Token 缓存
5. 登录鉴权

<!-- more -->

## 配置环境变量封装 axios 模块

### 根据开发和生产模式的区别去设定不同BaseUrl

对于 @vue/cli 来说，它具备三种不同的模式：

>1.`development`
>2.`test`
>3.`production`

我们可以在项目中创建两个文件：

`.env.development`
`.env.production`
它们分别对应 **开发状态** 和 **生产状态**。

```js

import axios from "axios";

const service = axios.create({
  baseURL: process.env.VUE_APP_BASE_API,
  timeout: 5000,
});

export default service;

```

---

## 封装请求动作

### 封装接口请求模块

创建 `api` 文件夹，创建 `sys.js`：

```js
import request from '@/utils/request'

/**
 * 登录
 */
export const login = data => {
  return request({
    url: '/sys/login',
    method: 'POST',
    data
  })
}

```

### 封装登录请求动作

该动作我们期望把它封装到 `vuex` 的 `action` 中

在 store 下创建 modules 文件夹，创建 user.js 模块，用于处理所有和 用户相关 的内容

> 此处需要使用第三方包 md5

```js
import { login } from '@/api/sys'
import md5 from 'md5'
export default {
  namespaced: true,
  state: () => ({}),
  mutations: {},
  actions: {
    login(context, userInfo) {
      const { username, password } = userInfo
      return new Promise((resolve, reject) => {
        login({
          username,
          password: md5(password)
        })
          .then(data => {
            resolve()
          })
          .catch(err => {
            reject(err)
          })
      })
    }
  }
}

```

在`store/index` 中完成注册：

```js

import { createStore } from 'vuex'
import user from './modules/user.js'
export default createStore({
  modules: {
    user
  }
})

```

### 登录动作绑定

```vue

<template>
  <el-button
  type="primary"
  style="width: 100%; margin-bottom: 30px"
  :loading="loading"
  @click="handleLogin"
  >登录</el-button>
</template>
<script setup>
import { ref } from 'vue'
import { validatePassword } from './rules'
import { useStore } from 'vuex'
...

// 登录动作处理
const loading = ref(false)
const loginFromRef = ref(null)
const store = useStore()
const handleLogin = () => {
  loginFromRef.value.validate(valid => {
    if (!valid) return

    loading.value = true
    store
      .dispatch('user/login', loginForm.value)
      .then(() => {
        loading.value = false
        // TODO: 登录后操作
      })
      .catch(err => {
        console.log(err)
        loading.value = false
      })
  })
}
</script>
```

### 添加代理

在`vite.config.js`中添加代理，讲以`/api`打头的请求全部转发到提供好的外部api借口哦

```js
export default defineConfig({
  // 服务器配置
  server: {
    // 本项目请求端口设定，不设置的话为vite提供的默认3000端口
    port: 8081,
    open: true,
    // http: true,
    // ssr: false,
    // 设置代理
    proxy: {
      // 可以配置不同的外部转发URL把不同的规则的路径转发给不同的地址
      // http://localhost:8081/api/sys/login → https://api.imooc-admin.lgdsunday.club/api/sys/login
      "/api": {
        target: "https://api.imooc-admin.lgdsunday.club/",
        changeOrigin: true,
        // rewrite: (path) => path.replace(/^\/api/, ""),
      },
      // http://localhost:8081/app/posts → http://jsonplaceholder.typicode.com/posts
      "/app": {
        target: "http://jsonplaceholder.typicode.com",
        changeOrigin: true,
        // rewrite可以将请求路径的一部分进行替换
        rewrite: (path) => path.replace(/^\/app/, ""),
      },
    },
  },
}
```

登录成功的响应内容,返回token

```json
{
    "success": true,
    "code": 200,
    "data": {
        "token": "3e24e2a9-907d-4ece-b6df-3d67a99d7e53"
    },
    "message": "登录成功"
}
```

### 本地缓存处理方案

拿到token后会进行缓存，缓存方式一般有两种

1. 本地缓存`LocalStorage` 方便进行自动登录

2. 全局状态管理使用`vuex`

### LocalStory

1. 创建`utils/storage.js`,对LocalStorage进行二次封装,需要注意当需要存取对象时需要进行json的转化

```js
/**
 * 存储数据
 */
export const setItem = (key, value) => {
  // 将数组、对象类型的数据转化为 JSON 字符串进行存储
  if (typeof value === 'object') {
    value = JSON.stringify(value)
  }
  window.localStorage.setItem(key, value)
}

/**
 * 获取数据
 */
export const getItem = key => {
  const data = window.localStorage.getItem(key)
  try {
    return JSON.parse(data)
  } catch (err) {
    return data
  }
}

/**
 * 删除数据
 */
export const removeItem = key => {
  window.localStorage.removeItem(key)
}

/**
 * 删除所有数据
 */
export const removeAllItem = key => {
  window.localStorage.clear()
}

```

在vuex中，对`token`进行保存处理
需要注意追加`constant.js`进行项目中的常量管理

```js
import { login } from '@/api/sys'
import md5 from 'md5'
import { setItem, getItem } from '@/utils/storage'
import { TOKEN } from '@/constant'
export default {
  namespaced: true,
  state: () => ({
    token: getItem(TOKEN) || ''
  }),
  mutations: {
    setToken(state, token) {
      state.token = token
      setItem(TOKEN, token)
    }
  },
  actions: {
    login(context, userInfo) {
      ...
          .then(data => {
            this.commit('user/setToken', data.data.data.token)
            resolve()
          })
          ...
      })
    }
  }
}
```

### 登录鉴权

创建`prmission.js`文件

```js
import router from './router'
import store from './store'

// 白名单
const whiteList = ['/login']
/**
 * 路由前置守卫
 */
router.beforeEach(async (to, from, next) => {
  // 存在 token ，进入主页
  // if (store.state.user.token) {
  // 快捷访问
  if (store.getters.token) {
    if (to.path === '/login') {
      next('/')
    } else {
      next()
    }
  } else {
    // 没有token的情况下，可以进入白名单
    if (whiteList.indexOf(to.path) > -1) {
      next()
    } else {
      next('/login')
    }
  }
})
```
