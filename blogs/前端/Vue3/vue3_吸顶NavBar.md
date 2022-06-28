---
title: 【Vue3】开发吸顶ToolBar组件
date: 2022-06-29 15:45:35
permalink: /pages/vue3/navBar
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
## 目标效果

> 初始

![Pqdh8H.png](https://s6.jpg.cm/2022/06/29/Pqdh8H.png)

> 滚动

![PqdjmL.png](https://s6.jpg.cm/2022/06/29/PqdjmL.png)

## 需求分析

```vue
<jk-toolbar 
  :navList="navList" 
  :logoUrl="logoUrl"
  fullWidth
  sticky
  >
  
</jk-toolbar>
```

- 需求一览
- 动态绑定路游数组，包含路游名称和路游路径
- 动态传入Logo地址
  - 传入logo时，nav项目右对齐
  - 未传logo时，nav项目居中对齐
- fullWidth参数决定ToolBar是否为宽度100%
- 路由切换显示激活样式
- sticky参数决定ToolBar是否自动吸顶
  - sticky为true的条件下，初始0高度Toolbar透明，字体为深色
  - 向下滚动一次Toolbar背景色和字体色进行切换
  - 滚动时Logo进行色彩反相自适应

## 组件开发

### 自动吸顶功能

> vue3 原生方式

```vue
<script setup>
import {
  onMounted,
  onUnmounted,
  computed,
} from "vue";

// 组件传值属性--------
const props = defineProps({
  modalValue: Boolean,
  logoUrl: String,
  sticky: Boolean,
  fullWidth: Boolean,
  navList: Array,
});

const scrollTop = ref(0);

const updateScrollTop = () => {
  scrollTop.value = document.documentElement.scrollTop;
};

onMounted(() => {
  window.addEventListener("scroll", updateScrollTop);
});

onUnmounted(() => {
  window.removeEventListener("scroll", updateScrollTop);
});

const className = computed(() => {
  return [props.sticky && scrollTop.value > 78 && "jk-navbar-sticky"];
});

</script>

<template>
  <div class="jk-navbar" :class="className">
    <nav :class="{ container: !fullWidth }">
      <div class="logo-area" v-if="logoUrl">
        <router-link to="/">
          <img :src="logoUrl" alt="" />
        </router-link>
      </div>
      <ul :class="{ 'no-logo': !logoUrl }">
        <li v-for="nav in navList" :key="nav.index">
          <router-link :to="nav.link" active-class="link-active">
            {{ nav.name }}
          </router-link>
        </li>
      </ul>
    </nav>
  </div>
</template>

```

> 使用`vueuse`工具库的`useWindowScroll`方法实现

1. 安装
   
   `npm i @vueuse/core`
2. 使用

```vue
<script setup>
import { computed } from "vue";
import { useWindowScroll } from "@vueuse/core";

// 组件传值属性--------
const props = defineProps({
  modalValue: Boolean,
  logoUrl: String,
  sticky: Boolean,
  fullWidth: Boolean,
  navList: Array,
});

// 根据页面滚动高度切换ToolBar吸顶状态
const { y } = useWindowScroll();

const updateScrollTop = () => {
  scrollTop.value = document.documentElement.scrollTop;
};

const className = computed(() => {
  return [props.sticky && y.value > 78 && "jk-navbar-sticky"];
});
</script>

<template>
  <div class="jk-navbar" :class="className">
    <nav :class="{ container: !fullWidth }">
      <div class="logo-area" v-if="logoUrl">
        <router-link to="/">
          <img :src="logoUrl" alt="" />
        </router-link>
      </div>
      <ul :class="{ 'no-logo': !logoUrl }">
        <li v-for="nav in navList" :key="nav.index">
          <router-link :to="nav.link" active-class="link-active">
            {{ nav.name }}
          </router-link>
        </li>
      </ul>
    </nav>
  </div>
</template>
```

### 动态logo文件绑定功能

本地文件需要通过import将文件引入后，再进行动态绑定

远程地址可以直接绑定

```vue
<script setup>
import jkNavbar from "@/components/Navbar/index.vue";
import logoUrl from "@/assets/images/common/logoCt.svg";
</script>

<template>
  // 本地文件
  <jk-navbar :logoUrl="logoUrl"> </jk-navbar>
  // 远程文件
  <jk-navbar logoUrl="https://....../logo.svg"> </jk-navbar>
</template>
```

### logo图片反相功能

使用反相滤镜

```scss
  &.jk-navbar-sticky {
    .logo-area {
      a {
        img {
          filter: invert(71%) sepia(43%) saturate(0%) hue-rotate(152deg)
            brightness(94%) contrast(75%);
        }
      }
    }
```

### 路由激活样式

添加`active-class`属性即可

```vue
  <li v-for="nav in navList" :key="nav.index">
    <router-link :to="nav.link" active-class="link-active">
      {{ nav.name }}
    </router-link>
  </li>
```

## 最终效果

> 100%宽度

![Pqdy9f.png](https://s6.jpg.cm/2022/06/29/Pqdy9f.png)

> 非100%宽度

![PqdTQU.png](https://s6.jpg.cm/2022/06/29/PqdTQU.png)

> 无logo

![PqdaSO.png](https://s6.jpg.cm/2022/06/29/PqdaSO.png)

> 滚动效果

![Pqdi3w.png](https://s6.jpg.cm/2022/06/29/Pqdi3w.png)

## 完整代码

```vue
<script setup>
import { computed } from "vue";
import { useWindowScroll } from "@vueuse/core";

// 组件传值属性--------
const props = defineProps({
  modalValue: Boolean,
  logoUrl: String,
  sticky: Boolean,
  fullWidth: Boolean,
  navList: Array,
});

// 根据页面滚动高度切换ToolBar吸顶状态
const { y } = useWindowScroll();

const updateScrollTop = () => {
  scrollTop.value = document.documentElement.scrollTop;
};

const className = computed(() => {
  return [props.sticky && y.value > 78 && "jk-navbar-sticky"];
});
</script>

<template>
  <div class="jk-navbar" :class="className">
    <nav :class="{ container: !fullWidth }">
      <div class="logo-area" v-if="logoUrl">
        <router-link to="/">
          <img :src="logoUrl" alt="" />
        </router-link>
      </div>
      <ul :class="{ 'no-logo': !logoUrl }">
        <li v-for="nav in navList" :key="nav.index">
          <router-link :to="nav.link" active-class="link-active">
            {{ nav.name }}
          </router-link>
        </li>
      </ul>
    </nav>
  </div>
</template>

<style lang="scss" scoped>
.jk-navbar {
  background-color: rgba(0, 0, 0, 0);
  color: rgba(255, 255, 255, 0.9);
  nav {
    display: flex;
    justify-content: space-around;
    align-items: center;

    .logo-area {
      margin-left: 2rem;
    }
    ul {
      flex: 1;
      display: flex;
      justify-content: flex-end;
      &.no-logo {
        justify-content: space-around;
      }
      li {
        margin-right: 2rem;
        a {
          display: inline-block;
          padding: 1rem;
          transition: 0.3s;
          &:hover {
            color: #0091ea;
            transition: 0.3s;
          }
          &.link-active {
            color: #0091ea;
          }
        }
      }
    }
    transition: 0.5s;
  }

  &.jk-navbar-sticky {
    position: fixed;
    top: 0;
    width: 100%;
    background: rgba(255, 255, 255, 0.9);
    color: rgba(0, 0, 0, 0.9);
    box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px,
      rgba(0, 0, 0, 0.05) 0px 4px 6px -2px;
    transition: 0.3s;
    .logo-area {
      a {
        img {
          filter: invert(71%) sepia(43%) saturate(0%) hue-rotate(152deg)
            brightness(94%) contrast(75%);
        }
      }
    }
  }
}
</style>

```

> 父组件代码

```vue
<script setup>
import jkNavbar from "@/components/Navbar/index.vue";
import logoUrl from "@/assets/images/common/logoCt.svg";
import { navList } from "@/utils/data";
</script>

<template>
  <jk-navbar sticky fullWidth :navList="navList" :logoUrl="logoUrl">
  </jk-navbar>
</template>

<style lang="scss" scoped></style>

```

