---
title: 泛型类
---

## 泛型类

[原文地址](https://www.typescripttutorial.net/typescript-tutorial/typescript-generic-classes/)

在本教程中，你将学习 如何开发 TypeScript 中的泛型类

### TypeScript 中的泛型类介绍

[泛型](/8-generics/1-generics/) 类的语法如下，泛型类型参数列表在尖括号 `<>` 中，跟着名称后面：

```ts
class className<T> {
  //...
}
```

TypeScript 允许你在类型参数列表中有多个泛型类型类型，如下所示：

```ts
class className<K, T> {
  //...
}
```

[泛型约束](/8-generics/2-generic-constraints/) 也可以应用于类中的泛型类型：

```ts
class className<T extends TypeA> {
  //...
}
```

在类上放置类型参数允许你开发相同类型的方法和属性

### TypeScript 泛型类例子

在这个例子中，我们将开发一个 `Stack` 泛型类

栈是一个基于后进先出(LIFO)原则的数据结构，这就意味着你第一个放入到栈中的元素会是你从栈中最后一个获取到的元素

通常栈有大小限制，默认为空，栈有两个主要的操作：

- Push: 将一个元素推入到栈中
- Pop: 从栈中弹出一个元素

下面展示一个完整的栈泛型类，名为 `Stack<T>`：

```ts
class Stack<T> {
  private elements: T[] = [];

  constructor(private size: number) {}
  isEmpty(): boolean {
    return this.elements.length === 0;
  }
  isFull(): boolean {
    return this.elements.length === this.size;
  }
  push(element: T): void {
    if (this.elements.length === this.size) {
      throw new Error('The stack is overflow!');
    }
    this.elements.push(element);
  }
  pop(): T {
    if (this.elements.length == 0) {
      throw new Error('The stack is empty!');
    }
    return this.elements.pop();
  }
}
```

下面创建了一个新的数字栈：

```ts
let numbers = new Stack<number>(5);
```

下面的函数返回两个数字 `low` 和 `high` 之间的随机数：

```ts
function randBetween(low: number, high: number): number {
  return Math.floor(Math.random() * (high - low + 1) + low);
}
```

现在你可以使用 `randBetween()` 函数生成随机数，然后推入到 `numbers` 栈中：

```ts
let numbers = new Stack<number>(5);

while (!numbers.isFull()) {
  let n = randBetween(1, 10);
  console.log(`Push ${n} into the stack.`);
  numbers.push(n);
}
```

输出：

```sh
Push 3 into the stack.
Push 2 into the stack.
Push 1 into the stack.
Push 8 into the stack.
Push 9 into the stack.
```

下面演示如何从栈中弹出元素，直到栈为空：

```ts
while (!numbers.isEmpty()) {
  let n = numbers.pop();
  console.log(`Pop ${n} from the stack.`);
}
```

输出：

```sh
Pop 9 from the stack.
Pop 8 from the stack.
Pop 1 from the stack.
Pop 2 from the stack.
Pop 3 from the stack.
```

类似的，你可以创建一个字符串栈，例如：

```ts
let words = 'The quick brown fox jumps over the lazy dog'.split(' ');

let wordStack = new Stack<string>(words.length);

// push words into the stack
words.forEach((word) => wordStack.push(word));

// pop words from the stack
while (!wordStack.isEmpty()) {
  console.log(wordStack.pop());
}
```

它是如何工作的：

- 首先，把橘子拆成单词
- 然后，创建一个栈，大小等于单词数组的单词数
- 第三，将单词数组中的单词推入栈中
- 最后，将栈中的单词弹出，直到栈为空
