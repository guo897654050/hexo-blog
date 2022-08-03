---
title: tsconfig配置
date: 2022-08-03 13:50:52
tags: typescript
---


### tsconfig的基本配置

如果新建一份`tsconfig`的话，可以使用`npx tsc --init`会在当前目录下生成t`sconfig`文件。

1. include: 指定需要编译的文件，否则默认除了exclude之外的所有`.ts .d.ts .tsx`文件。
2. exclude: 排除某些不需要编译的文件。例如，['test.ts']
3. compilerOptions下的配置
   1. target: 指定目标编译版本。
   2. module: 指定使用模块，可选`commonjs, amd, system, umd`
   3. lib: 指定一些polyfill, 对于 --target ES5: DOM,ES5,ScriptHost，对于 --target ES6: DOM,
   对于 ES6,DOM.Iterable,ScriptHost，TS 绝不会在您的代码中注入polyfill,所以需要你自己制定编译lib.
   4. declaration: true 是否生成声明文件。
   5. outDir: './dist' 输入文件目录
   6. esModuleInterop: true 通过为导入内容创建命名空间，实现commonjs和esmodule的互相转化。
   7. expermientalDecorators: true 是否开启装饰器特性。



### 关于compilerOptions额外的字段的解释
  
#### rootDir
// 假设我的目录结构如下
```
-- src
    version1
        test.ts
    version2
        demo.ts
// 如果我们设置 "rootDir": "./src",那么我们的编译后的文件结构如下,需要注意的是项目中除了src目录中,其他地方不能有ts文件了
--build
    version1
        test.ts
    version2
        demo.ts
```

#### baseUrl使用绝对路径有效
```
// 假设我们路径如下,此时我们在test.ts中引用 import test2 from '../test2',
-- src
version1
    test.ts
version2
    demo.ts
test2.ts
// 如果我们设置"baseUrl": "./src" 那么我们在ts中引入test2可以写为
import test2 from 'test2',需要注意的是只有我们引用的是绝对路径的时候才会使用baseUrl去解析文件
```

#### 路径映射 paths是相对于baseUrl更复杂映射
```
// 如果我们tsconfig使用如下配置
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": [
        "*",
        "version2/*"
      ]
    }
}
// 我们的项目目录如下,此时我们在test.ts中引用 import test2 from 'test2',
// 首先匹配 baseUrl/test2如果找到则停止否则开始寻找 baseUrl/version2/test2
-- src
version1
    test.ts
version2
    demo.ts
test2.ts
```


#### 虚拟目录rootDirs
```
// 如果我们tsconfig使用如下配置, 这个时候我们生成了一个虚拟的根目录,这个根目录下存放了version2,version3目录下文件
{
  "compilerOptions": {
    "rootDirs": [
      "src/version2",
      "src/version3",
    ],
}

// 我们的项目目录如下,此时我们在test.ts中引用demo就可以这样使用了 import demo from './demo',
-- src
version1
    test.ts
version2
    demo.ts
test2.ts
```


