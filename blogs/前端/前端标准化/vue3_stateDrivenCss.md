---
title: Vue3 状态驱动动态CSS
date: 2022-04-18 15:45:35
permalink: /pages/frontend-standard/stateDrivenCss
sidebar: "auto"
author: Yang J.K.
# keys:
#   - 123456
categories:
  - Vue3
tags:
  - Vue3 
---

![ESLint 配置](/images/header/frontend_solution.png)

<!-- more -->
## 状态驱动动态CSS

可以很容易把vue里面的动态变量绑定到css样式内部
实现css内写js

## Demo

```vue
<template>
<el-form-item prop="password">
  <el-input
    v-model="loginForm.password"
    prefix-icon="Lock"
    placeholder="password"
    name="password"
    :type="passwordType"
    tabindex="1"
  >
    <template #suffix>
      <el-icon class="el-input__icon" @click="onChangePasswordType">
        <search v-if="passwordType == 'password'" />
        <camera v-else />
      </el-icon>
    </template>
  </el-input>

</el-form-item>
</template>

<script  setup>
  const passwordType = ref("password");
  const onChangePasswordType = () => {
    passwordType.value = passwordType.value === "password" ? "text" : "password";
  };
</script>
```

## Vue2写法

```vue
<template>
  <div>
    <input type="text" v-model="color" />
    <div class="user-input-color">
      {{ color }}
    </div>
  </div>
</template>
 
<script>
export default {
  data: () => ({
    color: 'white'
  })
}
</script>
 
<style scoped>
.user-input-color {
  background-color: v-bind(color)
}
</style>

```

## Vue3的Composition API写法

```vue
<template>
  <div>
    <input type="text" v-model="color" />
    <div class="user-input-color">
        {{ color }}
    </div>
  </div>
</template>
 
<script setup>
import { ref } from 'vue'
  
const color = ref('white')
</script>
 
<style scoped>
.user-input-color {
  background-color: v-bind(color)
}
</style>

```

## Demo 表单验证

在表单验证时显示不同样式

```vue
<template>
  <h3>Form Validation Example</h3>
  <p>
    Name is required input & Lucky Number must be a nubmer
  </p>
  <div class="validation-check">
    <div v-if="!errors.length">
      <h3>Success!</h3>
      <p>This form is valid. No errors 😁👍</p>
    </div>
    <ul v-else>
      <li v-for="(error, key) in errors" :key="key">
        {{error}}
      </li>
    </ul>
  </div>
  <form @submit.prevent="handleSubmit">
    <input type="text" v-model="name" placeholder="Name">
    <input type="text" v-model="luckyNumber" placeholder="Lucky Number">
    <input type="submit" value="Submit">
  </form>
</template>
 
<script setup>
import { ref } from 'vue'
 
const name = ref('')
const luckyNumber = ref('')
const errors = ref([])
const validationState = ref('lime')
 
const handleSubmit = () => {
  errors.value = []
 
  if (!name.value.length > 0) {
    errors.value.push('Name is required')
  }
 
  if (luckyNumber.value.match(/D/g)) {
    errors.value.push('Lucky number must be a number')
  }
 
  if (errors.value.length) {
    validationState.value = 'red'
  } else {
    validationState.value = 'lime'
  }
}
</script>
 
<style scoped>
.validation-check {
  padding: 2rem;
  background-color: v-bind('validationState')
}
 
* {
  font-family: Arial, Helvetica, sans-serif;
}
 
form {
  display: flex;
  flex-direction: column;
  margin-top: 50px;
}
input {
  margin-bottom: 5px;
  padding: 10px;
}
</style>

```
