---
title: vite整理
date: 2022-08-01 11:17:03
tags: vite
---


### 关于动态路由的使用
在迁移webpack项目到vite的过程中，有些路由是动态导入的，例如:
```
 const routes = [
  {
    path: 't1',
    name: 't1',
    component: () => import (`@/views/${item}.vue`)
  }
 ]
```

这样的话，vite在启动的时候会报错。如下。
> The above dynamic import cannot be analyzed by vite.
See https://github.com/rollup/plugins/tree/master/packages/dynamic-import-vars#limitations for supported dynamic import formats. If this is intended to be left as-is, you can use the /* @vite-ignore */ comment inside the import() call to suppress this warning.



### 在项目文件中的动态导入
例如在xx.vue文件中
```
<script setup>
const importModule = (name) => import (`../../utils/${name}.js`)

importModule('a').then(() => {
  // 正常执行没问题。
  console.log(a.method())
})
</srcipt>
```