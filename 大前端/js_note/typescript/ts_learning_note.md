## 简介

TypeScript就是添加类型系统的JavaScirpt，适用于任何规模的项目。完全兼容了JavaScript，它不会修改JavaScript运行时的特性。
### 为什么要需要
wo的理解是引入了类型系统，js过于灵活导致问题在运行时才出现，所以ts提前进行了类型检查，对于后端来说，这样无疑更加符合强类型语言的开发习惯。

## 基础

### 基本数据类型

#### 数值number

typescript中任何变量都要携带类型，如果不携带并且与类型推论的不一致的话，无法通过编译，除了any类型

```typescript
letdecLiteral:number=6;

lethexLiteral:number=0xf00d;

// ES6 中的二进制表示法

letbinaryLiteral:number=0b1010;

// ES6 中的八进制表示法

letoctalLiteral:number=0o744;

letnotANumber:number=NaN;

letinfinityNumber:number=Infinity;
```

#### 字符串 string

也是有模板字符串的

```typescript
letsentence:string=`Hello, my name is ${myName}.

I'll be ${myAge + 1} years old next month.`;
```

#### 空值

##### 函数void

```typescript
function alertName(){
	alert('My name is Tom');
}
```

##### 变量void

```typescript
let unusable: void =undefined;
```

#### 布尔类型 boolean

```typescript
let isDone:boolean=false;
```

### 任意值

任意值（Any）用来表示允许赋值为任意类型。

```typescript
// 普通类型不允许修改类型

letmyFavoriteNumber:string='seven';

// myFavoriteNumber = 7;

// any类型允许修改类型

letmyFavoriteNumber2:any='seven';

myFavoriteNumber2=7;
```

### 类型推论

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型，也就是说上面说的

```ts
let myFavoriteNumber;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。

```typescript
//访问变量
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;

console.log(myFavoriteNumber);

//访问联合类型的属性或方法
function getString(something: string | number): string {
  return something.toString();
}
```

### 接口

在面向对象语言中，接口（Interfaces）是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）

除了可用于[对类的一部分行为进行抽象](https://ts.xcatliu.com/advanced/class-and-interfaces.html#%E7%B1%BB%E5%AE%9E%E7%8E%B0%E6%8E%A5%E5%8F%A3)以外，也常用于对「对象的形状（Shape）」进行描述

```typescript
//和Java中非常相似，接口interface是对于行为的抽象，具体实现交给类去实现
interface Person {
    name: string;
    age: number;
}

let tom: Person={
    name:'Tom',
    age:25
}

console.log(tom);
```

接口普通属性在实现类中都要有

#### 可选属性

普通属性就是确定属性，可选属性在实现类中可以有也可以没有

```typescript
interface Person2 {
    name: string;
    age?: number;
}

let tom2: Person2={
    name:'Tom'
}
```

#### 任意属性

属性名称是任意的

```
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

注：任意属性的类型必须大于确定属性和可选属性

#### 只读属性

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

tom.id = 9527;
// index.ts(14,5): error TS2540: Cannot assign to 'id' because it is a constant or a read-only property.

```

### 数组的类型

#### 类型+方括号表示

```
let fibonacci: number[] = [1, 1, 2, 3, 5];

```

#### 数组泛型

```
let fibonacci: Array<number> = [1, 1, 2, 3, 5];

```

#### 接口表示(不推荐)

##### 类数组

没看懂，跳过，遇到时候再说吧

### 函数的类型

先来看下javascript中的函数声明和函数表达式

```
// 函数声明（Function Declaration）
function sum(x, y) {
    return x + y;
}

// 函数表达式（Function Expression）
let mySum = function (x, y) {
    return x + y;
};
```

#### 函数声明&函数表达式

声明

```
function sum(x: number, y: number): number {
    return x + y;
}
```

表达式

```
let mySum = function (x: number, y: number): number {
    return x + y;
};
```

##### 用接口来定义函数的形状

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

#### 可选参数

```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

注：可选参数必须放在必需参数后面

#### 参数默认值

```
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');
```

#### 剩余参数

ES6 中，可以使用 `...rest` 的方式获取函数中的剩余参数（rest 参数）：

```js
function push(array, ...items) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a: any[] = [];
push(a, 1, 2, 3);
```

#### 重载

和Java中的重载一样，方法的签名不一样，其他都一样

```typescript
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

### 类型断言

类型断言可以用来手动指定一个值的类型

值 as 类型 或者 `<类型>值`

```typescript
interface Cat {
    name: string;
    run(): void;
}
interface Fish {
    name: string;
    swim(): void;
}

function isFish(animal: Cat | Fish) {
    if (typeof (animal as Fish).swim === 'function') {
        return true;
    }
    return false;
}
```

### 声明文件

当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能。

#### 声明语句和声明文件

例如，声明JQuery

```
declare var jQuery: (selector: string) => any;

jQuery('#foo');

```

文件就是.d.ts

```autoit
/path/to/project
├── src
|  ├── index.ts
|  └── jQuery.d.ts
└── tsconfig.json
```

#### 书写声明文件

##### 全局变量

一般来说，全局变量很少变动

```ts

declare const jQuery: (selector: string) => any;

jQuery('#foo');
```

##### npm包

[推荐链接](https://ts.xcatliu.com/basics/declaration-files.html#npm-%E5%8C%85)

就看它的package.json的 types字段或者使用npm install @types/foo --save-dev能不能自动地构建声明文件，如果不行的话再按照上面的教程去自己编写

### 内置对象

#### 常用内置对象

```
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;
```

#### DOM和BOM内置对象

```
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

## 进阶

### 类型别名

类型别名用来给一个类型起个新名字。

```
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}

```

### 字符串字面量类型

字符串字面量类型用来约束取值只能是某几个字符串中的一个。

```
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dblclick'); // 报错，event 不能为 'dblclick'

```

### 元组

数组合并了相同类型的对象，而元组（Tuple）合并了不同类型的对象。

元组起源于函数编程语言（如 F#），这些语言中会频繁使用元组。

#### 简单的例子

```
let tom: [string, number] = ['Tom', 25];

```

### 枚举

枚举（Enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
```

### 类

传统方法中，JavaScript 通过构造函数实现类的概念，通过原型链实现继承。而在 ES6 中，我们终于迎来了 `class`。

* 类（Class）：定义了一件事物的抽象特点，包含它的属性和方法
* 对象（Object）：类的实例，通过 `new` 生成
* 面向对象（OOP）的三大特性：封装、继承、多态
* 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
* 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
* 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 `Cat` 和 `Dog` 都继承自 `Animal`，但是分别实现了自己的 `eat` 方法。此时针对某一个实例，我们无需了解它是 `Cat` 还是 `Dog`，就可以直接调用 `eat` 方法，程序会自动判断出来应该如何执行 `eat`
* 存取器（getter & setter）：用以改变属性的读取和赋值行为
* 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 `public` 表示公有属性或方法
* 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
* 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

#### 类继承

```js
class Cat extends Animal {
  constructor(name) {
    super(name); // 调用父类的 constructor(name)
    console.log(this.name);
  }
  sayHi() {
    return 'Meow, ' + super.sayHi(); // 调用父类的 sayHi()
  }
}

let c = new Cat('Tom'); // Tom
console.log(c.sayHi()); // Meow, My name is Tom
```

#### 存取器

```js
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
console.log(a.name); // Jack
```

#### 静态方法

```js
class Animal {
  static isAnimal(a) {
    return a instanceof Animal;
  }
}

let a = new Animal('Jack');
Animal.isAnimal(a); // true
a.isAnimal(a); // TypeError: a.isAnimal is not a function
```

### 类&接口

比较特殊的是，typescript中接口可以继承类

### 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。

```ts
function createArray(length: number, value: any): Array<any> {
    let result = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```

这段代码编译不会报错，但是一个显而易见的缺陷是，它并没有准确的定义返回值的类型：

```ts
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

### 声明合并

如果定义了两个相同名字的函数、接口或类，那么它们会合并成一个类型：

这个不知道是干啥的，跳过

### 装饰器

装饰器模式是一种允许动态地往一个对象上添加自定义行为，而又不影响该对象所属的类的其他实例的一种设计模式


```ts
class Animal {
  type: string
  constructor(type: string) {
	  this.type = type
  }

  @yelling
  greet() {
    console.log(`Hello, I'm a(n) ${this.type}!`)
  }
}

const typeToYellingMap = {
  cat: 'meow~ meow~'
}

function yelling(originalMethod: any, context: ClassMethodDecoratorContext) {
  return function(...args: any[]) {
    console.log(typeToYellingMap[this.type])
    originalMethod.call(this, ...args)
  }
}

const xcat = new Animal('cat')
xcat.greet() // meow~ meow~
```
