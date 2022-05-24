---
title: 【Vue3】自定义组件双向数据绑定
date: 2022-05-25 02:25:00
permalink: /pages/vue/vmodel
sidebar: "auto"
author: Yang J.K.
# keys:
#   - 123456
categories:
  - Vue3
tags:
  - Vue3 
---

![LZ6Ey8.png](https://s6.jpg.cm/2022/05/22/LZ6Ey8.png)

<!-- more -->
## Vue3中自定义组件使用v-model实现双向绑定

## v-model原理

> 原理文档可以参考[Vue3组件基础](https://v3.cn.vuejs.org/guide/component-basics.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-v-model)和[Vue3深入组件](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-%E5%8F%82%E6%95%B0)

双向数据绑定分为

- 视图随data更新，即`:value="searchText"`

- 视图更新data，即`@input="searchText = $event.target.value"`

即

```html
<input v-model="searchText" />
```

等价于:

```html
<input :value="searchText" @input="searchText = $event.target.value" />
```

## Demo

```vue
<!-- 子组件 -->
<template>
  <div>
    <input type="text" :value="msg" @input="$emit('update:msg', $event.target.value)" />
  </div>
</template>

<script>
export default {
  props: {
    msg: {
      type: String
    }
  }
}
</script>
```

```vue
<!-- 父组件 -->
// 父组件
<template>
  <h1>vue3中使用v-model {{msg}}</h1>
  <testModel v-model:msg="msg"></testModel>
</template>
```

## 多个 v-model 绑定

```vue
<!-- 子组件 -->
<template>
  <input 
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)">

  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)">
</template>

<script>
export default {
  props: {
    firstName: {
      type: String
    },
      lastName: {
      type: String
    }
  }
}
</script>
```

```html
<!-- 父组件 -->
<div id="v-model-example" class="demo">
   <p>First name: {{ firstName }}</p>
   <p>Last name: {{ lastName }}</p>
   <user-name
    v-model:first-name="firstName"
    v-model:last-name="lastName"
  ></user-name>
</div>
```
