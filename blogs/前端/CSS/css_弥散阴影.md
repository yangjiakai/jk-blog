---
title: 【CSS】弥散阴影
date: 2022-05-30 02:25:00
permalink: /pages/vue/shadow
sidebar: "auto"
author: Yang J.K.
# keys:
#   - 123456
categories:
  - CSS
tags:
  - CSS
---

![LZ6Ey8.png](https://s6.jpg.cm/2022/05/22/LZ6Ey8.png)

<!-- more -->

## 弥散阴影原理

> 原理文档可以参考[Vue3 组件基础](https://v3.cn.vuejs.org/guide/component-basics.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-v-model)和[Vue3 深入组件](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-%E5%8F%82%E6%95%B0)

双向数据绑定分为

- 视图随 data 更新，即`:value="searchText"`

- 视图更新 data，即`@input="searchText = $event.target.value"`

即

```html
<input v-model="searchText" />
```

等价于:

```html
<input :value="searchText" @input="searchText = $event.target.value" />
```

## Demo
