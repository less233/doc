# JavaScript基础 - 对象+类

## 对象基础

在JavaScript中的所有事物都是对象，包括字符串、数值、数组、函数等等。对象只是带有属性和方法的特殊数据类型，String、Date、Array、Map等都是JavaScript中内置的对象，并且允许用户自定义。通常在JavaScript中都是使用字面量的方式创建对象，读取时可以使用点表示法和括号表示法获取保存在该对象中的信息。如例

``` javascript
const person = {
  name: "less233",
  2: ["赖克宝", "金蟾"],
  ["property" + 3]: 3,
  introduceSelf: function () {
    console.log(`你好！我是 ${this.name}。`);
  },
};

person.name;
person.name;
person["name"];
person.introduceSelf();
// "你好！我是 less233。"
delete person.name;
```

对象的属性也有权限之分，属性的可枚举性是指那些内部 `Enumerable` 标志设置为 `true` 的属性，在通过 `Object.defineProperty` 定义的属性，该标识值默认为 `false` 。可枚举的属性可以通过 [for...in](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 循环进行遍历（除非该属性名是一个 [Symbol](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)）。属性的所有权是通过判断该属性是否直接属于某个对象决定的，而不是通过原型链继承的。一个对象的所有的属性可以一次性的获取到。



## 类基础

在JavaScript，类其实是大部分原型对象机制声明的抽象方式。类属性在默认情况下是公有的，但可以使用增加哈希前缀 `#` 的方法来定义私有类字段，这一隐秘封装的类特性由 JavaScript 自身强制执行。也可以通过 **static** 关键字定义静态方法

``` javascript
class Person {
  static species = "Animal";
  #age;
  static {
    this.species = "Human";
  }
  constructor(name, age) {
    this.name = name;
    this.#age = age;
  }
  static init(name) {
    return new Person(name);
  }
  getName() {
    return this.name;
  }
  getAge() {
    return this.#age;
  }
}

const coco = new Person("coco", "18");
const lili = Person.init("lili", '19');
console.log(coco.getName()); // coco
console.log(coco.getAge()); // 18
console.log(lili.getName()); // lili
console.log(lili.getAge()); // undefined
```





> `封装` `继承` `多态` 是如今多种对象编程语言实现编程范式基本概念，虽然JavaScript也有提供类的声明方式，但因为它是动态且没有静态类型的语言，这点和其他基于类的语言如 Java 来说是不太一样的。

## 原型（继承）

在谈到继承时，JavaScript只有对象这一种结构，每个对象（object）都有一个私有属性指向另一个名为**原型**（prototype）的对象。原型对象也有一个自己的原型，层层向上直到一个对象的原型为 `null`。根据定义，`null` 没有原型，并作为这个**原型链**（prototype chain）中的最后一个环节。可以改变原型链中的任何成员，甚至可以在运行时换出原型，因此 JavaScript 中不存在[静态分派](https://en.wikipedia.org/wiki/Static_dispatch)的概念。如图

<img src="./img/mydate-prototype-chain.svg" alt="img" />

在 ECMAScript 标准中，符号 [[Prototype]] 被浏览器用于表示一个对象的原型。[[Prototype]] 内部属性可以通过 `Object.getPrototypeOf(obj)` 和 `Object.setPrototypeOf(obj)` 函数来访问，等同于 `obj.__proto__` 或 `obj.[[Prototype]]` 。

``` javascript
const parent = {
  // __proto__ 设置了 [[Prototype]]。它在这里被指定为另一个对象字面量。
  __proto__: {
  	value: 0,
  },
}

parent.prototype.method = function () {
  return this.value + 1;
}

const child = {
  parent.call(this)
  value: 2,
};

// { value: 2 } ---> {} ---> { value: 0 } ---> Object.prototype ---> null

console.log(parent.method())  //1
console.log(child.method()) // 3
```

类继承

``` javascript
class grandfather {
  constructor(value = 0) {
    this.value = value;
  }
}

class parent extends grandfather {
  constructor(value) {
    super(value);
  }
  method() {
    return this.value + 1;
  }
}

class child extends parent {
  constructor(value) {
    super(value);
  }
}

console.log(new parent().method()); //1
console.log(new child(2).method()); // 3
```



## 类与实例（多态）

在下面中 `Professor` 类用代码描述如下：

``` javascript
class Professor {
  constructor(name, teaches) {
    this.name = name;
    this.teaches = teaches;
  }
  grade(paper) {}
  introduceSelf() {
    return `我是${this.name}，我是你们的${this.teaches}老师。`;
  }
}
```

`Professor` 类的定义包括如下内容：

- 两个属性：姓名 `name` 和所教的课程 `teaches`
- 两个方法：`grade()` 方法用于为学生的论文打分；`introduceSelf()` 方法用于介绍自己。

定义构造函数后，我们就可以创建出具体的教授了。编程语言通常使用 `new` 关键字来表示执行构造函数。

``` javascript
walsh = new Professor("沃尔什", "心理学");
lillian = new Professor("丽莲", "诗歌");

walsh.teaches; // '心理学'
walsh.introduceSelf(); // '我是沃尔什，我是你们的心理学老师。'

lillian.teaches; // '丽莲'
lillian.introduceSelf(); // '我是丽莲，我是你们的诗歌老师'
```



## 封装

封装可以保持对象内部状态的私有性，明确划分对象的公共接口和内部状态并减少耦合，保护自身的内部状态不会被外部代码随意修改，增强代码的安全性、可重用性、可维护性

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  #privateName() {
    return this.name;
  }
  getName() {
    return this.#privateName();
  }
}

class Student extends Person {
  #year;
  constructor(name, year) {
    super(name);
    this.#year = year;
  }
  getYear() {
    return this.#year;
  }
}

const student = new Student("Weber", 1);
console.log(student.name); // Weber
console.log(student.getName()); // Weber
console.log(student.year); // undefined
console.log(student.getYear()); // 1
```

