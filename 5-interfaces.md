# Интерфейсы

Информация взята с официальной документации TypeScript - https://www.typescriptlang.org/docs/handbook/interfaces.html

Один из основных принципов TypeScript заключается в том, что проверка типов фокусируется на форме, которую имеют значения. Иногда это называют «утиной типизацией» или «структурным подтипом». В TypeScript интерфейсы выполняют роль именования этих типов и являются мощным способом определения контрактов в вашем коде, а также контрактов с кодом вне вашего проекта.

Утиная типизация (Duck typing) - 

## Наш первый интерфейс
Начнем с простого примера, чтобы понять как работают интерфейсы:
```TypeScript
function printLabel(labeledObj: { label: string }) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

Средство проверки типов проверяет вызов `printLabel`. Функция `printLabel` имеет единственный параметр, который требует, чтобы переданный объект имел свойство с названием `label` и типом `string`. Обратите внимание, что у объекта на самом деле больше свойств, чем `label`, но компилятор проверяет только наличие хотя бы необходимых свойств и соответствие заданным типам.
Мы можем написать такой же пример, но на этот раз используя интерфейс для описания контракта со свойством `label`, которое является строкой:
```TypeScript
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```
Интерфейс `LabeledValue` - это сущность, которую мы можем использовать для описания контракта (аргументов) функции в предыдущем примере. Нам не нужно явно указывать, что объект, который мы передаем в `printLabel`, реализует этот интерфейс, как это могло бы потребоваться в других языках. Здесь имеет значение только форма. Если объект, который мы передаем функции, соответствует перечисленным требованиям, то компилятор не выдаст ошибки.

Стоит отметить, что средство проверки типов не требует, чтобы эти свойства располагались в каком-либо порядке, только чтобы свойства, которые требуются интерфейсу, присутствовали и имели требуемый тип.

## Необязательные свойства
Не все свойства интерфейса могут быть обязательными. Некоторые существуют при определенных условиях или могут не существовать вовсе. Такие свойства помечаются знаком вопроса в конце.
```TypeScript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig) {
  let newSquare = { color: "white", area: 100 };
  // Необходимо добавлять проверку на наличие свойства color в объекте config
  // потому что свойство является необязательным и его может не быть, тогда мы будем обращаться к пустоте
  if (config.color) {
    newSquare.color = config.color;
  }
  // Тут тоже необходимо проверить существует ли свойство width в объекте config
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({ color: "black" });
```

## Свойства только для чтения
Некоторые свойства следует изменять только при первом создании объекта. Вы можете указать это, поставив перед именем свойства только чтение:
```TypeScript
interface Point {
  readonly x: number;
  readonly y: number;
}
```
Вы можете создать объект Point, используя литерал объекта. После присвоения значений x и y их уже нельзя будет изменить.
```TypeScript
const p1: Point = { x: 10, y: 20 };
p1.x = 5; // Ошибка
// Cannot assign to 'x' because it is a read-only property.
```
В TypeScript есть тип массива `ReadonlyArray<T>`, который нельзя изменять после присвоения ему значений.
```TypeScript
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;

ro[0] = 12; // Ошибка
// Index signature in type 'readonly number[]' only permits reading.
ro.push(5); // Ошибка
// Property 'push' does not exist on type 'readonly number[]'.
ro.length = 100; // Ошибка
// Cannot assign to 'length' because it is a read-only property.
a = ro; // Ошибка
// The type 'readonly number[]' is 'readonly' and cannot be assigned to the mutable type 'number[]'.
```
В последней строке видно, что мы не можем присвоить обычному массиву неизменяемый массив, их типы не совпадают. Но сможем сделать это с помощью утверждения типа (type assertion). 
```TypeScript
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;

a = ro as number[];
```
### readonly vs const
TypeScript позволяет перезаписывать свойства объекта, даже если мы создали его с помощью ключевого слова `const`. Это говорит о том, что мы не можем присвоить этой переменной ссылку на другой объект, но можем изменить текущий:
```TypeScript
const point = {
  x: 1,
  y: 2,
}
o.a = 3; // Объект успешно изменен, но ссылка на него осталась прежней
```
Чтобы избежать нежелательного изменения объекта и был придуман тип readonly.
```TypeScript
interface Point {
  readonly x: number;
  readonly y: number;
}
const point: Point = {
  x: 1,
  y: 2,
}
o.a = 3; // Ошибка, компилятор запретит перезаписывать свойства этого объекта
```
## Проверка на лишние свойства
При присваивании литералов объектов другим переменным или передачи их в качестве аргументов функции, они подвергаются проверке на лишние или избыточные свойства. Если литерал объекта имеет какие-либо свойства, которых нет у «целевого типа», то компилятор выдаст сообщение об ошибке:
```TypeScript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  return {
    color: config.color || "red",
    area: config.width ? config.width * config.width : 20,
  };
}

// Функция ниже выдаст ошибку
let mySquare = createSquare({ colour: "red", width: 100 });
// Argument of type '{ colour: string; width: number; }' is not assignable to parameter of type 'SquareConfig'.
// Object literal may only specify known properties, but 'colour' does not exist in type 'SquareConfig'. Did you mean to write 'color'?
```
Однако если мы хотим указать, что объект может иметь свойства color и width и еще какой-то набор неизвестных нам свойств, то мы можем использовать индексную сигнатуру для этого:
```TypeScript
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}
```
Таким образом мы указываем, что SquareConfig может принимать неограниченное количество свойств с любым типом.

Однако если мы не передадим ни свойство color, ни свойство width. То компилятор выдаст ошибку.
```TypeScript
let squareOptions = { height: 10 }; // Ошибка
let mySquare = createSquare(squareOptions);
// Type '{ colour: string; }' has no properties in common with type 'SquareConfig'.
```
Но если передадим хотя бы одно из свойств, то все будет работать:
```TypeScript
let squareOptions = { height: 10, width: 100 }; // OK
let mySquare = createSquare(squareOptions);
```

## Типы функций
С помощью интерфейсов можно описывать различные формы, которые могут принимать объекты JavaScript. Помимо описания объекта со свойствами, интерфейсы также могут описывать типы функций. Чтобы описать тип функции в интерфейсе, нам необходимо описать сигнатуру функции. 

Сигнатура функции - это описание функции. Оно похоже на объявлении функции, но без ее тела, а только с описанием типа ее аргументов и типа возвращаемого значения.
```TypeScript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```
Мы можем использовать этот интерфейс типа функции, как и другие интерфейсы выше. Создадим переменную типа `SearchFunc` и присвоим ей значение функции того же типа.
```TypeScript
let mySearch: SearchFunc;

mySearch = function (source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
};
```
Чтобы функция корректно проходила проверку типа, имена параметров не обязательно должны совпадать. Мы могли бы, например, написать приведенный выше пример следующим образом:
```TypeScript
let mySearch: SearchFunc;

mySearch = function (src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
};
```