### 在TypeScript中使用“extends”的三种方式

在编写 TypeScript 代码时，经常会看到“extends”关键字。 但请小心！ 该关键字根据其上下文的不同而发挥不同的作用。 在本文中，我将解释如何在 TypeScript 中，以三种不同的方式使用“extends”。

#### 1. 继承

“extends ”关键字会用于**接口继承**（或类继承）。 下面是一个接口继承的例子：

```typescript
interface Vehicle {
    wheels: number;
    maker?: string;
}

interface Car extends Vehicle {
    power: "gas" | "electricity";
}

interface Bicycle extends Vehicle {
    folding: boolean;
}

const myCar: Car = {
    wheels: 4,
    maker: "Toyota",
    power: "gas"
} // 正确使用

const car: Car = {
    wheels: 4,
    maker: "Honda",
    power: "gas",
    folding: false
} // 错误使用
// Type '{ wheels: number; maker: string; power: "gas"; folding: boolean; }' is not assignable to type 'Car'.
// 对象字面量只能指定已知属性，并且“Car”类型中不存在“folding”。

const bicycle: Bicycle = {
    wheels: 2,
} // 错误使用
// Property 'folding' is missing in type '{ wheels: number; }' but required in type 'Bicycle'.
```

可以从基接口创建继承接口。（在此示例中，Vehicle 是基接口，Car 和 Bicycle 是继承接口。）

如果想创建一个新接口来接管其他接口的属性，则此关键字很有用，不必再次从头开始声明新接口。

#### 2. 通用约束

如果你是中级或高级 TypeScript 程序员，希望在代码中使用通用类型。 例如，如果你想从一个对象的属性中获取值，你可以编写下面的代码：

```typescript
const testObj = { x: 10, y: "Hello", z: true };

function getProperty<T>(obj: T, key: keyof T) {
  return obj[key];
}

const xValue = getProperty(testObj, 'x');
const yValue = getProperty(testObj, 'y');
```

上面的代码是具有一定功能的，但是还不够满足类型安全。当我们把鼠标移到`xValue`和`yValue`时，可以看到它们的类型如下。

```typescript
const xValue = getProperty(testObj, 'x');
// const xValue: string | number | boolean

const yValue = getProperty(testObj, 'y');
// const yValue: string | number | boolean
```

你可能认为`xValue`的类型应该为`number`，`yValue`的类型应该为`string`。然而，TypeScript的类型系统无法缩小`getProperty`函数的返回类型。结果就是，结果，函数返回值的类型变成了`testObj`属性的联合类型。

如果你想要写一个类型安全的方法，你应该使用一个技巧——通用约束，用它来缩小返回值的类型。让我们来重构前面的示例代码：

```typescript
const testObj = { x: 10, y: "Hello", z: true };

function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const xValue = getProperty(testObj, 'x');
// const xValue: number
const yValue = getProperty(testObj, 'y');
// const yValue: string
```

如你所见，`xValue`和`yValue`都被缩小到`testObj`属性的特定类型。

在上面的示例代码中，`extends`关键字用于约束泛型K的类型。换言之，为K选择T的一个键，而不是允许任何键作为key参数的类型。因此，可以将函数限制为只有一种返回类型，而不是联合类型。

#### 3. 条件类型

TypeScript有能力来创建一个逻辑去根据赋给泛型的类型动态生成一个类型。

下面是一个例子：

```typescript
type IsString<T> = T extends string ? true : false;

type x = IsString<'hello'>;
// type x = true

type y = IsString<number>;
// type y = false

type z = IsString<string>;
// type z = true
```

`IsString`类型就是**条件类型**。`IsString`的类型根据它的泛型`T`而不同。

让我来解释一下`T extends string ? true : false`

如果`T`的类型是`string`的子类型，换言之，如果`T`的类型可以赋值给`string`类型，则返回`true`。否则返回`false`。

#### 总结

总之，TypeScript的**extends**关键字是一个多用途的工具，在接口继承、泛型约束和条件类型中起着至关重要的作用。掌握这些概念能帮助你写出更加干净、更加健壮、更加灵活的TypeScript代码。

然而，这个关键字也可能会让你在阅读其他开发人员编写的代码库时感到困惑，因为`extends`关键字根据上下文的不同而工作。

因此，你应该记住关键字有这几个含义，**继承**、**泛型约束**、**条件类型**。