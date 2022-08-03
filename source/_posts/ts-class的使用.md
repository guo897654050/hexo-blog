---
title: ts-class的使用
date: 2022-08-03 20:35:24
tags: typescript
---

### 基本使用
```
class Animal {
  // 可以不声明name变量，但是构造函数的入参要添加public关键字
  name: string;
  construct(name) {
    this.name = name;
  }
}
```

`public`, `private`，`protected`关键字。

ts默认不声明的都是`public`类型。
`private`声明的变量和方法无法在类的外部使用。
`protected`声明的变量和方法可以在被继承
`statci`描述的方法，可以直接使用，而不需要实例化。

### 存取器
```
class Animal {
  constructor(name) {
    this.name = name;
  }
  get name() {
    return 'Jack';
  }
  set name(value) {
    console.log('setter: ' + value);
  }
}

let a = new Animal('Kitty'); // setter: Kitty
a.name = 'Tom'; // setter: Tom
```

<!-- more-->
### 类的继承
```
class Animal {
  construct(public name) {
    this.name = name;
  }
  eat() {
    cosnole.log(`${this.name} can eat everything!`)
  }
}

// 暂时的问题，既然可以直接拿到name，为啥还在构造函数使用super继承构造函数的方法？
class Dog extends Animal {
  // 可以直接拿到name
  say() {
    console.log(9999, this.name)
  }
}


class Snake extends Animal {
  construct(name) {
    // 调用构造函数的方法
    super(name)
  }

  say() {
    super.eat()
  }
}
```


### 抽象类
理解抽象类，有点像函数重载的函数签名，每个类不会写具体的实现方法，需要在其子类中进行完善，且被`abstract`关键字描述的变量或者方法，子类必须实现。

```
abstract class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract sayHi();
}

class Cat extends Animal {
  public sayHi() {
    console.log(`${this.name} is eating.`);
  }
}

let cat = new Cat('Tom');
cat.sayHi();
```
