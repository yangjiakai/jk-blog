---
title: 【CSS】SCSS 语法
date: 2022-05-30 02:25:00
permalink: /pages/css/scssBase
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

## 嵌套规则

```scss
// scss
#id {
    color: black;
    .name {
        color: blue;
        .child {
            color: yellow;
        }
    }
}
```

```css
// css
#id {
    color: black;
}

#id .name {
    color: blue;
}

#id .name .child {
    color: yellow;
}
```

## 引用父选择器 &

```scss
// scss
.btn {
    background-color: #fff;
    &:active {
      background-color: red;
    }
    &:hover{
      background-color: blue;
    }
    &:visited{
      background-color: yellow;
    }
    &-success {
      background-color: pink;
    }

}
```

```css
// css
.btn {
    background-color: #fff;
}

.btn.active {
    background-color: red;
}

.btn:hover {
    background-color: blue;
}

.btn:visited {
    background-color: yellow;
}

.btn-success {
    background-color: pink;
}
```

## 嵌套属性规则

```scss
// scss
.attr {
    font: {
        family: fantasy;
        size: 1.2em;
        line-hight: 1.4;
    };
    border: {
        radius: 20px;
        color: blue;
    };
}
```

```css
// css
.attr {
    font-family: fantasy;
    font-size: 1.2em;
    font-line-hight: 1.4;
    border-radius: 20px;
    border-color: blue;
}
```

## 声明变量

```scss
// scss
$width: 50px;

.wt {
    width: $width;
}
```

```css
// css
.wt {
    width: 50px;
}
```

## 数学运算

```scss
// scss
p {
  font: 10px/8px;             // 纯 CSS，不是除法运算
  $width: 1000px;
  width: $width/2;            // 使用了变量，是除法运算
  width: round(1.5px)/2;        // 使用了函数，是除法运算
  height: (500px/2);          // 使用了圆括号，是除法运算
  margin-left: 5px + 8px/2px; // 使用了加（+）号，是除法运算
  padding-left: + 100px / 2;  
}
```

```css
// css
p {
    font: 10px/8px;
    width: 500px;
    width: 1px;
    height: 250px;
    margin-left: 9px;
    padding-left: 50px;
}
```

## 颜色运算符

```scss
// scss
p {
  color: #001100 + #040506;
}
p {
  color: #010 + #040506;
}
```

```css
// css
p {
    color: #041606;
}

p {
    color: #041606;
}
```

***

# Map变量

```scss
$blue-grey: (
  'base': #607d8b,
  'lighten-5': #eceff1,
  'lighten-4': #cfd8dc,
  'lighten-3': #b0bec5,
  'lighten-2': #90a4ae,
  'lighten-1': #78909c,
  'darken-1': #546e7a,
  'darken-2': #455a64,
  'darken-3': #37474f,
  'darken-4': #263238
);

$grey: (
  'base': #9e9e9e,
  'lighten-5': #fafafa,
  'lighten-4': #f5f5f5,
  'lighten-3': #eeeeee,
  'lighten-2': #e0e0e0,
  'lighten-1': #bdbdbd,
  'darken-1': #757575,
  'darken-2': #616161,
  'darken-3': #424242,
  'darken-4': #212121
);

$shades: (
  'black': #000000,
  'white': #FFFFFF,
  'transparent': transparent
);

$colors: (
  'blue-grey': $blue-grey,
  'grey': $grey,
  'shades': $shades
);

```

```scss
$back-base: #111b27;
$text-base: #e5eaed;

$theme-dark-colors: (
  '0': #000000,
  '1': darken($back-base, 7.5%),
  '2': darken($back-base, 5%),
  '3': darken($back-base, 2.5%),
  '4': $back-base,
  '6': lighten($back-base, 2.5%),
  '8': lighten($back-base, 5%),
  '12': lighten($back-base, 7.5%),
  '16': lighten($back-base, 10%),
  '24': lighten($back-base, 12.5%),
);

$material-dark: (
  'app-bar': map-get($theme-dark-colors, '1'),
  'background': map-get($theme-dark-colors, '4'),
  'bottom-navigation': map-get($theme-dark-colors, '1'),
  'surface': map-get($theme-dark-colors, '4'),
  'calendar': (
    'background-color': map-get($theme-dark-colors, '5'),
    'outside-background-color': map-get($theme-dark-colors, '2'),
    'line-color': map-get($theme-dark-colors, '6'),
    'interval-color': map-get($theme-dark-colors, '12'),
    'interval-line-color': map-get($theme-dark-colors, '8')
    ),
  'cards': map-get($theme-dark-colors, '1'),
  'text': (
    'theme': $text-base,
    'primary': lighten($text-base, 2.5%)
  ),
  'icons': (
    'active': $text-base
  ),
  'inputs': (
    'box': $text-base
  ),
  'navigation-drawer': map-get($theme-dark-colors, '1'),
  'skeleton': linear-gradient(90deg, transparent, rgba(255, 255, 255, .05), transparent),
  'table': (
    'active': #192328,
    'hover': #354553,
    'group': #354553
  ),
  'toolbar': map-get($theme-dark-colors, '4'),
  'fg-color': $text-base,
  'text-color': $text-base
);

```
