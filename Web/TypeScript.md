---
title: TypeScript
date: 2022-03-23 13:01:28
tags: TypeScript
categories: Web开发
---

> 参考：https://www.tslang.cn/docs/home.html

# 为什么要用TS

我们都知道，JS是一个**动态类型**的语言，一个`var`就能解决变量类型问题，从静态类型语言转过来的时候感觉会非常爽，而且JS还是个**弱类型**的语言，你可以随时转换变量类型，有一种一招鲜吃遍天的感觉。

但是从实际开发的角度看，这是一个好事吗？

动态类型的语言意味着它只会在运行的时候做类型校验，因此我们在编译的时候，即使出了类型错误也不会有相关的报错。这也意味着一些IDE无法及时给出提示。或者我们误传了一个`undefined`作为参数，但是也不会进行报错，给调试带来了困难。而且JS没有命名空间，导致很难模块化（其实symbol就是一个解决方案）。

我在看`create-react-app`的文档的时候，发现了一篇有关`Flow`的文章，`Flow`也是一种将JS转变成静态语言的一种解决方案。前半部分讲的使用静态语言的原因挺不错的，感兴趣可以[看看](https://medium.com/free-code-camp/why-use-static-types-in-javascript-part-1-8382da1e0adb)。

# 变量类型

TS支持对变量定义类型，比如：

```typescript
let temp: string = 2 //此时我们只能对temp赋上字符串类型的值
```

TS总共有以下变量类型：

- number

- string

- boolean

- undefined

- null和undefined

  默认`null`和`undefined`是所有类型的子类型，所以可以把它们赋给`number`等类型的变量，但是当我们开启`strictNullChecks`时，`null`和`undefined`只可被赋给`void`

- array

  数组有两种定义方法：

  - 在类型后面加上`[]`，例如：`number[ ]`
  - 使用数组泛型，例如：`Array<number>`

- tuple

  元组允许表示一个已知元素数量和类型的数组，数组中的元素类型不必相同。

  例如：

  ```
  let x: [string, number];
  x = ['hello', 10]
  ```

  访问一个元素时会得到正确的类型，访问越界的元素时，会使用联合类型

- enum

  跟c++类型的语言一样，可以给一组数值起一个友好的名字

- any

  `any`提供了一种可选择地包含或移除类型检查的方法，与`Object`不同的是，`any`可以调用任意的方法，而`Object`不行。

- void

  void变量只能赋予`undefined`和`null`

- never

  `never`类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是`never`类型，当它们被永不为真的类型保护所约束时。

# 类型断言

我们在C++中存在类型强制转换，那在TS中，我们也有类似的手段，我们称为类型断言。能够让TS不对齐进行特殊的数据检查，能够顺利通过编译。

类型断言有两种形式：

- 尖括号语法（jsx中无法使用）：

  ```typescript
  let someValue: any = "this is a string";
  let strLength: number = (<string>someValue).length; 
  ```

- as语法：

  ```typescript
  let someValue: any = "this is a string";
  let strLength: number = (someValue as string).length
  ```

# 变量声明

- 在TS中建议使用`let`和`const`来代替`var`

- 对象解构赋值中有一个比较混乱的语法：

  ```typescript
  let {a: newName1, b: newName2} = o
  ```

  这里的冒号不是指定类型，而是变量重命名，指定类型应该像下面这样写：

  ```typescript
  let {a: newName1, b: newName2}: {a: string, b: number} = o
  ```

  其实可以用一个规律来判断：

  在解构赋值括号内的冒号是重命名，括号外的冒号才是指定类型

# 接口

TS的接口有点像Java，给函数和类提供了一个类型检查的作用。

一个简单的例子：

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

类型检查器会查看`printLabel`的调用，检查那些必需的属性是否存在，并且其类型是否匹配， 但是类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。

## 可选属性

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。比如一些可选参数的函数，我们可以使用`?:`定义：

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}
```

## 只读属性

对于一些我们已经确定的属性，我们可以把它设置为只读，类似于使用`const`定义变量，我们使用`readonly`定义只读属性

```typescript
interface Point {
    readonly x: number;
    readonly y: number;
}
```

对于数组，TS提供了`ReadonlyArray<T>`类型，这个类型把所有的方法去掉了，所以我们无法对这种数组进行修改。当我们想修改它时，必须使用断言将其转换成正常数组。

## 额外的类型检查

当我们定义好接口，即使已经使用了可选属性，在传入的数据存在其他属性的时候，TS还是会报错，比如：

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```

这是因为对于对象字面量，TS会增加额外属性检查，当赋值给变量或者作为参数传递时，TS会检查是否存在“目标类型”不包含的参数。

这时，我们有两种解决方法：

- 使用断言：

  ```typescript
  let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);
  ```

- 赋值给另一个变量：

  ```typescript
  let squareOptions = { colour: "red", width: 100 };
  let mySquare = createSquare(squareOptions);
  ```

## 函数类型

接口也可以表示函数类型，形如：

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

使用方法为：

```typescript
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```

{% note warning %}

函数的参数名不需要与接口里定义的名字相匹配，只需要顺序相同即可，比如上面也可以写为：

```typescript
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  let result = src.search(sub);
  return result > -1;
}
```

{% endnote %}

## 可索引的类型

可索引类型具有一个索引签名，它描述了对象索引的类型，还有相应的索引返回值类型：                

```ts
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

上面表示对于数字索引所返回的值必须都为字符串。

{% note warning %}

TS的索引支持两种类型，数字和字符串。

但是由于`dictionary`的机制，数字索引会先被转换为字符串，所以数字索引的返回值，必须为字符串索引返回值的子类型，不然会报错。

{% endnote %}

我们也可以将索引签名设置为只读，这也是一种防止索引修改的方法（与`ReadOnlyArray<T>`不同，这样设置只是防止改写，还是可以调用方法进行切割等操作的）：

```typescript
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

# 泛型

首先我们提出一个需求，我们想要一个参数类型和返回类型一致的函数：

```typescript
//显然，使用any会丢失类型一致这个要求
function identity(arg: any): any{
	return arg;
}
//如果我们针对每个类型都写一个的话：
function identity(arg: number): number {
    return arg;
}
function identity(arg: string): string {
    return arg;
}
//...是不是很麻烦
```

所以我们引出了泛型这个语法，泛型可以用来创建可重用的组件，使这个组件可以支持多种类型的数据。我们使用`<T>`来表示，例如：

```typescript
function identity<T>(arg: T): T{
	return arg;
}
```

是不是让我们想起了C++的模板，还有JAVA的泛型？

使用泛型函数后，我们可以有两种调用方法：

- 传入所有的参数，包含类型参数：`identity<string>("myString");`

- 利用类型推论，编译器会自动帮我们确定T的类型：`identity("myString")`

注意我们在使用泛型的时候，应该考虑`T`可以代表的所有类型，比如：

```typescript
function identity<T>(arg: T): T{
	console.log(arg.length); //这里会报错，因为不是所有的类型都有length
	return arg;
}
```

我们可以改为：

```typescript
function identity<T>(arg: T[]): T[]{
	console.log(arg.length); //这里会报错，因为不是所有的类型都有length
	return arg;
}
```

Tips：我们也可以用不同的泛型参数名，比如`<U>`，只要数量和使用方式能对应即可。

## !非空断言符号

忽略null和undefined类型

```typescript
function myFunc(maybeString: string | undefined | null) {
  const onlyString: string = maybeString; // Error
  const ignoreUndefinedAndNull: string = maybeString!; // Ok
}
```

调用函数时忽略undefined类型

```typescript
type NumGenerator = () => number;

function myFunc(numGenerator: NumGenerator | undefined) {
  const num1 = numGenerator(); // Error
  const num2 = numGenerator!(); //OK
}
```

确定赋值断言 ：

```typescript
@shopInfo.State("performance")
performance!: Performance;
```

## 泛型接口

我们可能想把泛型参数当作整个接口的一个参数。 这样我们就能清楚的知道使用的具体是哪个泛型类型。

```typescript
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
} 

let myIdentity: GenericIdentityFn<number> = identity;
```

## 泛型类

除了泛型接口，我们还可以创建泛型类，但是无法创建泛型枚举和泛型命名空间。

泛型类的时候与接口差不多，将`<T>`跟在类型后面：

```typescript
class MyClass<T>{
	value: T;
    add: (x: T, y: T) => T;
}

let myNewClass = new MyClass<number>();
myNewClass.value = 0;
myNewClass.add = function(x,y){return x + y;};
```

可以看出，这个有点像C++的模板类

## 泛型约束

前面我们提到，要想访问`length`必须保证每种类型都有`length`属性。我们当时使用了`T[]`来解决这个问题。那么如果不是`length`而是一个自定义的属性呢？

所以我们引出了泛型约束，对传入的属性进行限制：只要传入的类型有这个属性，我们就允许，就是说至少包含这一属性。

我们可以使用接口和`extends`来实现约束

代码示例：

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

