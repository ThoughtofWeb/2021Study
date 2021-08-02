##### TypeScript

###### 产生背景

- JS使用率越来越频繁

- 弱类型语言，没有类型约束，代码质量参差不齐，维护成本高，运行时出错率高

###### 类型分类

- 弱类型（允许进行隐式类型转换）

- 静态类型（根据类型检查的时机判断是什么类型，由于js没有编译阶段，在运行时报错，所以是动态类型，而ts在编译阶段进行类型检查，所以是静态类型）

###### 安装

```bash
npm install -g typescript
```

###### 编译

```bash
tsc hello.ts
```

###### 数据类型

- 布尔值（let isDone: boolean = false;）

- 数值（let decLiteral: number = 6;）
- 字符串（let myName: string = 'Tom';）
- 空值 （let unusable: void = undefined;） 只能将它赋值为 `undefined` 和 `null` 
- null和undefined （let num: number = undefined;）所有类型的子类型
- 任意值any (可访问任何属性和方法)

```ts
let something: any; ===> let something;（定义未被赋值则被认定为为any类型）
something = 'seven';
something = 7;
something.setName('Tom');
```

- 联合类型（let myFavoriteNumber: string | number;）

- 对象类型Interface （变量的形状和接口**必须**一致）

```ts
interface Person {
    name: string;
    age: number;
}
等价于
let tom: Person = {
    name: 'Tom',
    age: 25
};

interface Person {
    name: string;
    age?: number;（可选属性：该属性可以不存在，但是不可添加未定义的）
}
等价于
let tom: Person = {
    name: 'Tom'
};

interface Person {
    name: string;
    age?: number;
    [propName: string]: string | number;
    （一个接口中只能定义一个任意属性。如果接口中有多个类型的属性，则可以在任意属性中使用联合类型）
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};
```

- 数组类型（let fibonacci: number[ ] = [1, 1, 2, 3, 5];）

```ts
类数组（arguments 不是数组类型，需要用接口定义）
interface IArguments {
    [index: number]: any;
    length: number;
    callee: Function;
}
let args: IArguments = arguments;

any数组 （let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];）
```

- 函数类型

```ts
function sum(x: number, y: number): number {
    return x + y;
}
```

- 类型断言（不确定联合类型的变量是哪个类型）

```ts
1. 值 as 类型
2. <类型>值

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

-  声明文件、声明语句

1. [`declare var`](https://ts.xcatliu.com/basics/declaration-files.html#declare-var) 声明全局变量
2. [`declare function`](https://ts.xcatliu.com/basics/declaration-files.html#declare-function) 声明全局方法
3. [`declare class`](https://ts.xcatliu.com/basics/declaration-files.html#declare-class) 声明全局类
4. [`declare enum`](https://ts.xcatliu.com/basics/declaration-files.html#declare-enum) 声明全局枚举类型
5. [`declare namespace`](https://ts.xcatliu.com/basics/declaration-files.html#declare-namespace) 声明（含有子属性的）全局对象
6. [`interface` 和 `type`](https://ts.xcatliu.com/basics/declaration-files.html#interface-和-type) 声明全局类型
7. [`export`](https://ts.xcatliu.com/basics/declaration-files.html#export) 导出变量
8. [`export namespace`](https://ts.xcatliu.com/basics/declaration-files.html#export-namespace) 导出（含有子属性的）对象
9. [`export default`](https://ts.xcatliu.com/basics/declaration-files.html#export-default) ES6 默认导出
10. [`export =`](https://ts.xcatliu.com/basics/declaration-files.html#export-1) commonjs 导出模块
11. [`export as namespace`](https://ts.xcatliu.com/basics/declaration-files.html#export-as-namespace) UMD 库声明全局变量
12. [`declare global`](https://ts.xcatliu.com/basics/declaration-files.html#declare-global) 扩展全局变量
13. [`declare module`](https://ts.xcatliu.com/basics/declaration-files.html#declare-module) 扩展模块
14. [`/// `](https://ts.xcatliu.com/basics/declaration-files.html#san-xie-xian-zhi-ling) 三斜线指令（引入依赖文件）

-  内置对象

```ts
ECMAScript
let b: Boolean = new Boolean(1);
let e: Error = new Error('Error occurred');
let d: Date = new Date();
let r: RegExp = /[a-z]/;

DOM和BOM
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
document.addEventListener('click', function(e: MouseEvent) {
  // Do something
});
```

- 字符串字面量类型（用type定义）

```ts
type EventNames = 'click' | 'scroll' | 'mousemove';
```

- 元组（合并不同类型的对象）

```ts
let tom: [string, number] = ['Tom', 25];
```

- 枚举（用于被限定在一定范围内的取值）

```ts
enum Days {Sun, Mon, Tue, Wed, Thu, Fri, Sat};
```

- 类
 ```
  static 静态方法、属性，它们不需要实例化，而是直接通过类来调用
  public 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 public 的
  private 私有方法、属性，不能在声明的类外部使用
  protected 受保护的，和 private 类似，区别是它在子类中也是允许被访问的
  ```
  
```ts
// 静态方法  
class Animal {
    static isAnimal(a) {
      return a instanceof Animal;
    }
  }
  let a = new Animal('Jack');
  Animal.isAnimal(a); // true
  a.isAnimal(a)
```

- 泛型 （在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。）
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

