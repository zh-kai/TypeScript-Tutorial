---
theme: jzman
---

- [TypeScript Tutorial 中文版 - 项目介绍](https://juejin.cn/post/6984281217168310302)
- [TypeScript Tutorial 中文版 - Section 0. 前言](https://juejin.cn/post/6984281996449021966)
- [TypeScript Tutorial 中文版 - Section 1. 入门](https://juejin.cn/post/6984290303880478757)
- [TypeScript Tutorial 中文版 - Section 2. 基本类型](https://juejin.cn/post/6984309148553445406)
- [TypeScript Tutorial 中文版 - Section 3. 控制流语句](https://juejin.cn/post/6984313301530312734)

# Section 7. 高级类型

## 交叉类型

[原文地址](https://www.typescripttutorial.net/typescript-tutorial/typescript-intersection-types/)

在本教程中，你将学习 TypeScript 中的交叉类型

### TypeScript 中的交叉类型介绍

价差类型通过组合多有现有类型来创建新的类型，新的类型具有现有类型的所有属性

使用 `&` 操作符来组合类型，如下所示：

```ts
type typeAB = typeA & typeB;
```

`typeAB` 会有 `typeA` 和 `typeB` 的所有属性

注意，联合类型使用 `|` 操作符，定义一个可以保存 `typeA` 或者 `typeB` 类型的值

```ts
let varName = typeA | typeB; // union type
```

假设你有三个接口：`BusinessPartner`, `Identity` 和 `Contact`

```ts
interface BusinessPartner {
  name: string;
  credit: number;
}

interface Identity {
  id: number;
  name: string;
}

interface Contact {
  email: string;
  phone: string;
}
```

下面定义了两种交叉类型：

```ts
type Employee = Identity & Contact;
type Customer = BusinessPartner & Contact;
```

`Employee` 类包含 `Identity` 和 `Contact` 类型中的所有属性：

```ts
type Employee = Identity & Contact;

let e: Employee = {
  id: 100,
  name: 'John Doe',
  email: 'john.doe@example.com',
  phone: '(408)-897-5684',
};
```

`Customer` 类型包含 `BusinessPartner` 和 `Contact` 类型中的所有属性：

```ts
type Customer = BusinessPartner & Contact;

let c: Customer = {
  name: 'ABC Inc.',
  credit: 1000000,
  email: 'sales@abcinc.com',
  phone: '(408)-897-5735',
};
```

之后，如果你想实现销售员工，你可以创建一个新的交叉类型包含 `Identity`, `Contact` 和 `BusinessPartner` 三个类型中的所有属性：

```ts
type Employee = Identity & BusinessPartner & Contact;

let e: Employee = {
  id: 100,
  name: 'John Doe',
  email: 'john.doe@example.com',
  phone: '(408)-897-5684',
  credit: 1000,
};
```

注意 `BusinessPartner` 和 `Identity` 有相同类型的 `name` 属性，如果它们类型不同，你将会得到一个错误提示

### 类型顺序

当你进行类型交叉的时候，类型的顺序并不重要，比如：

```ts
type typeAB = typeA & typeB;
type typeBA = typeB & typeA;
```

在这个例子中，`typeAB` 和 `typeBA` 有相同的属性

### 小结

- 交叉类型可以结合了两个或者更多的类型，创建具有现有类型的所有属性的新类型
- 在组合类型的时候，类型的顺序并不重要


## 类型保护

[原文地址](https://www.typescripttutorial.net/typescript-tutorial/typescript-type-guards/)

在本教程中，你将学习 TypeScript 中的类型保护

类型保护允许你使用 [条件代码块](https://juejin.cn/post/6984313301530312734) 来限定变量的类型范围

### typeof

让我们看看下面的例子：

```ts
type alphanumeric = string | number;

function add(a: alphanumeric, b: alphanumeric) {
  if (typeof a === 'number' && typeof b === 'number') {
    return a + b;
  }

  if (typeof a === 'string' && typeof b === 'string') {
    return a.concat(b);
  }

  throw new Error(
    'Invalid arguments. Both arguments must be either numbers or strings.',
  );
}
```

它是如何工作的：

- 属性，定义了 `alphanumeric` 类型，它可以保存 [字符串类型](https://juejin.cn/post/6984309148553445406) 或者 [数字类型](https://juejin.cn/post/6984309148553445406) 值
- 接下来，定义了 `add()` 函数，它把类型为 `alphanumeric` 的 `a` 变量与 `b` 变量进行相加
- 然后使用 `typeof` 操作符检查两个参数的类型是否都为 `number` 类型，如果是的话，使用 `+` 操作符计算参数之和
- 再然后使用 `typeof` 操作符检查两个参数的类型是否都为 `string` 类型，如果是的话，把两个字符串参数拼接起来
- 最后，如果两个参数不都全是数字或者字符串的话，抛出错误提示

在这个例子中，TypeScript 直到如何在条件代码块中使用 `typeof` 操作符，在下面的 [if](https://juejin.cn/post/6984313301530312734) 块中，TypeScript 认为 `a` 和 `b` 都是数字类型

```ts
if (typeof a === 'number' && typeof b === 'number') {
  return a + b;
}
```

类似地，在下面的 `if` 代码块中，TypeScript 将 `a` 和 `b` 作为字符串处理，因此，你可以把它们拼接成一个字符串：

```ts
if (typeof a === 'string' && typeof b === 'string') {
  return a.concat(b);
}
```

### instanceof

与 `typeof` 操作符类型，TypeScript 也知道 `instanceof` 操作符的使用，例如：

```ts
class Customer {
  isCreditAllowed(): boolean {
    // ...
    return true;
  }
}

class Supplier {
  isInShortList(): boolean {
    // ...
    return true;
  }
}

type BusinessPartner = Customer | Supplier;

function signContract(partner: BusinessPartner): string {
  let message: string;
  if (partner instanceof Customer) {
    message = partner.isCreditAllowed()
      ? 'Sign a new contract with the customer'
      : 'Credit issue';
  }

  if (partner instanceof Supplier) {
    message = partner.isInShortList()
      ? 'Sign a new contract the supplier'
      : 'Need to evaluate further';
  }

  return message;
}
```

它是如何工作的：

- 首先，声明了 `Customer` 和 `Supplier` 两个类
- 第二， 创建一个类型别名 `BusinessPartner`，它是`Customer` 和 `Supplier` 的联合类型
- 第三，定义一个函数 `signContract()`，它接受一个类型为 `BusinessPartner` 的参数
- 最后，检查 `partner` 是否是 `Customer` 或者 `Supplier` 类的实例，然后进行对应的逻辑处理

在下面的 `if` 代码块中，TypeScript 通过 `instanceof` 操作符知道 `partner` 是 `Customer` 类型的一个实例：

```ts
if (partner instanceof Customer) {
  message = partner.isCreditAllowed()
    ? 'Sign a new contract with the customer'
    : 'Credit issue';
}
```

同样的方式，在下面的 `if` 代码块中，TypeScript 知道 `partner` 是 `Supplier` 类型的一个实例：

```ts
if (partner instanceof Supplier) {
  message = partner.isInShortList()
    ? 'Sign a new contract with the supplier'
    : 'Need to evaluate further';
}
```

当 `if` 代码块限定了一种类型，TypeScript 知道在 `else` 里面会是另外一种类型，例如：

```ts
function signContract(partner: BusinessPartner): string {
  let message: string;
  if (partner instanceof Customer) {
    message = partner.isCreditAllowed()
      ? 'Sign a new contract with the customer'
      : 'Credit issue';
  } else {
    // must be Supplier
    message = partner.isInShortList()
      ? 'Sign a new contract with the supplier'
      : 'Need to evaluate further';
  }
  return message;
}
```

### in

`in` 操作符判断对象上是否存在某个属性来进行安全检查，你也可以将它用作类型保护，例如：

```ts
function signContract(partner: BusinessPartner): string {
  let message: string;
  if ('isCreditAllowed' in partner) {
    message = partner.isCreditAllowed()
      ? 'Sign a new contract with the customer'
      : 'Credit issue';
  } else {
    // must be Supplier
    message = partner.isInShortList()
      ? 'Sign a new contract the supplier '
      : 'Need to evaluate further';
  }
  return message;
}
```

### 用户定义的类型保护

用户定义的类型保护允许你使用函数的时候定义类型保护或者帮助 TypeScript 推断类型

用户定义的类型保护函数是一个简单返回 `arg is aType` 判断的函数，比如：

```ts
function isCustomer(partner: any): partner is Customer {
  return partner instanceof Customer;
}
```

在这个例子中，`isCustomer()` 是一个用户定义的类型保护函数，现在你可以按照下面的例子来使用它：

```ts
function signContract(partner: BusinessPartner): string {
  let message: string;
  if (isCustomer(partner)) {
    message = partner.isCreditAllowed()
      ? 'Sign a new contract with the customer'
      : 'Credit issue';
  } else {
    message = partner.isInShortList()
      ? 'Sign a new contract with the supplier'
      : 'Need to evaluate further';
  }

  return message;
}
```

### 小结

- 类型保护限定了条件代码块中变量的类型
- 使用 `typeof` 和 `instanceof` 操作符在条件代码块中的实现类型保护


## 类型转换

[原文地址](https://www.typescripttutorial.net/typescript-tutorial/type-casting/)

在本教程中，你将学习 TypeScript 中的类型转换，它允许你将变量从一种类型转换到另外一种类型

JavaScript 没有类型转换的概念，因为变量具有动态类型的特性，而 TypeScript 中的变量都有类型，类型转换允许你将变量从一种类型转换到另外一种类型

TypeScript 中你可以使用 `as` 关键字或者 `<>` 操作符进行类型转换

### 使用 as 关键字进行类型转换

下面使用 [querySelector()](https://zh.javascript.info/searching-elements-dom#querySelector) 方法选择第一个输入元素：

```ts
let input = document.querySelector('input["type="text"]');
```

因为 `document.querySelector()` 方法的返回类型是 `Element` 类型，下面的代码会导致编译错误：

```ts
console.log(input.value);
```

因为 `Element` 类型不存在 `value` 属性，这个属性只存在 `HTMLInputElement` 类型上

为了解决这个问题，你可以使用类型转换，即使用关键字 `as` 把 `Element` 类型转换为 `HTMLInputElement` 类型，如下所示：

```ts
let input = document.querySelector('input[type="text"]') as HTMLInputElement;
```

现在，`input` 变量的类型是 `HTMLInputElement`，所以访问它的 `value` 属性不会导致任何错误，下面的代码可以正常工作：

```ts
console.log(input.value);
```

另外一种把 `Element` 类型转换为 `HTMLInputElement` 类型来访问属性的方法如下所示：

```ts
let enteredText = (input as HTMLInputElement).value;
```

注意 `HTMLInputElement` 方法扩展了 `HTMLElement` 类型，而 `HTMLElement` 类型扩展了 `Element` 类型。把 `HTMLElement` 类型转换为 `HTMLInputElement` 类型被称为向下转换

也可以如下进行向下转换：

```ts
let el: HTMLElement;
el = new HTMLInputElement();
```

在这个例子中，`el` 变量的类型是 `HTMLElement` 类型，你可以给它指定一个 `HTMLInputElement` 类型的实例，因为 `HTMLInputElement` 类型是 `HTMLElement` 类型的子类

将类型从 `typeA` 转换为 `typeB` 的语法如下：

```ts
let a: typeA;
let b = a as typeB;
```

### 使用 <> 操作符进行类型转换

除了 `as` 关键词，你可以使用 `<>` 操作符进行类型转换，如下所示：

```ts
let input = <HTMLInputElement>document.querySelector('input[type="text"]');

console.log(input.value);
```

使用 `<>` 操作符进行类型转换的语法是：

```ts
let a: typeA;
let b = <typeB>a;
```

### 小结

- 类型转换允许你将变量从一种类型转换到另外一种类型
- 使用 `as` 关键字或者 `<>` 操作符进行类型转换


## 类型断言

[原文地址](https://www.typescripttutorial.net/typescript-tutorial/type-assertions/)

在本教程中，你将学习 TypeScript 中的类型断言

### TypeScript 中的类型断言介绍

类型断言让 TypeScript 编译器将某个值视为指定的类型，它使用 `as` 关键字来做到这点：

```ts
expression as targetType;
```

类型断言也被称为类型收缩，它允许你从 [联合类型](https://juejin.cn/post/6984309148553445406) 收缩类型范围。让我们看下下面简单的函数：

```ts
function getNetPrice(
  price: number,
  discount: number,
  format: boolean,
): number | string {
  let netPrice = price * (1 - discount);
  return format ? `$${netPrice}` : netPrice;
}
```

`getNetPrice()` 函数接受 `price`, `discount` 和 `format` 三个参数，返回一个联合类型为 `number | string` 的值

如果 `format` 的值为 `true`，`getNetPrice()` 函数以字符串形式返回格式化后的净价格，否则以数字的形式返回净价格

下面使用 `as` 关键字告诉编译器，赋值给 `netPrice` 的值是一个字符串：

```ts
let netPrice = getNetPrice(100, 0.05, true) as string;
console.log(netPrice);
```

输出：

```sh
$95
```

同样的，下面的例子使用 `as` 关键字告诉编译器，赋值给 `netPrice` 的值是一个数字：

```ts
let netPrice = getNetPrice(100, 0.05, false) as number;
console.log(netPrice);
```

输出:

```sh
95
```

注意类型断言不做任何 [类型转换](https://juejin.cn/post/6984314053757763592) 的事情，它只是告诉编译器为了类型检查的目的，应该使用哪种类型应用于该值

### 可选的类型断言语法

你也可以使用尖括号语法 `<>` 来断言一个类型，比如：

```ts
<targetType>value;
```

例如：

```ts
let netPrice = <number>getNetPrice(100, 0.05, false);
```

注意你不能在 React 等库中使用尖括号语法 `<>`，出于这个原因，你应该进行类型断言的时候都是用 `as` 关键字

### 小结

- 类型断言告诉编译器把一个值视为指定的类型
- 类型断言不做任何转型转换的事情
- 类型断言使用 `as` 关键字或尖括号 `<>` 的语法
