# Кастомные типы

Система типов TypeScript позволяет создавать новые типы из существующих, используя большое количество операторов. Теперь, когда мы знаем, как написать несколько типов, пора начать комбинировать их интересными способами.

## Тип объединения (_Union Types_)

Первый способ комбинирования типов, который вы можете увидеть, - это тип объединения. Тип объединения - это тип, образованный из двух или более других типов, представляющих значения, которые могут принимать значение одного из этих типов.

Напишем функцию, которая может работать со строками или числами:

```TypeScript
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
// Ошибка
printId({ myID: 22342 });
// Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
// Type '{ myID: number; }' is not assignable to type 'number'.
```

Предоставить значение, соответствующее типу объединения, несложно - просто передайте тип, соответствующий любому из перечисленных членов объединения.

TypeScript позволит вам делать что-то с объединением, только если эта вещь действительна для каждого члена объединения. Например, если у вас есть объединение `string | number`, вы не можете использовать методы, которые доступны только для строки:

```TypeScript
function printId(id: number | string) {
  console.log(id.toUpperCase());
// Property 'toUpperCase' does not exist on type 'string | number'.
// Property 'toUpperCase' does not exist on type 'number'.
}
```

Решение состоит в том, чтобы сузить (_narrow_) область возможных значений объединение с помощью кода, так же, как в JavaScript - без аннотаций типов. Сужение области значений происходит тогда, когда TypeScript может вывести более конкретный тип для значения на основе структуры кода.

Например, TypeScript знает, что только строковая переменная будет иметь значение `typeof` равное `"string"`:

```TypeScript
function printId(id: number | string) {
  if (typeof id === "string") {
    // В этой ветке, аргумент id будет типа 'string'
    console.log(id.toUpperCase());
  } else {
    // Здесь id будет типа 'number'
    console.log(id);
  }
}
```

Другой пример - использование такой функции, как `Array.isArray`:

```TypeScript
function welcomePeople(x: string[] | string) {
  if (Array.isArray(x)) {
    // Тут 'x' это массив строк 'string[]'
    console.log("Hello, " + x.join(" and "));
  } else {
    // Тут 'x' это просто строка 'string'
    console.log("Welcome lone traveler " + x);
  }
}
```

Обратите внимание, что в ветке `else` нам не нужно делать ничего особенного - если `x` не был массивом строк `string[]`, значит, это должна была быть строка.

Иногда бывает объединения, в котором у всех его членов есть что-то общее. Например, и массивы, и строки имеют метод `splice`. Если у каждого члена объединения есть общее свойство, вы можете использовать это свойство без сужения (_narrowing_):

```TypeScript
// Возвращаемый выводится как number[] | string
function getFirstThree(x: number[] | string) {
  return x.slice(0, 3);
}
```

<sub><sup>Может показаться, что объединение (_union_) типов имеет пересечение (_intersection_) свойств этих типов. Это не случайно - название объединение происходит от теории типов. Объединение `number | string` состоит из объединения значений каждого типа. Обратите внимание, что для двух групп с соответствующими данными о каждой группе, только наличие общих данных об одной и о другой группе применяется в объединении самих групп. Например, если бы у нас была комната с высокими людьми в шляпах и другая комната с испаноязычными в шляпах, после объединения этих комнат единственное, что мы знаем о каждом человеке, - это то, что они должны быть в шляпе.</sup></sub>

## Псевдонимы типов (_Type Aliases_)

Мы использовали типы объектов и типы объединений, записывая их как аннотации типов `const person: { name: string; age: string | number }`. Это удобно, но часто возникает необходимость использовать один и тот же тип более одного раза и обращаться к нему по одному имени.

Псевдоним типа (_type alias_) позволяет задавать имя для любого созданного или уже существующего типа:

```TypeScript
type Point = {
  x: number;
  y: number;
};

function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}

printCoord({ x: 100, y: 100 });
```

Мы также можем задать имя для объединения:

```TypeScript
type ID = number | string;
```

Обратите внимание, что мы не можем использовать псевдонимы типов для создания разных «версий» одного и того же типа. Если мы используем псевдоним, то типом будет являться его сигнатура, которая была описана при его создании.

```TypeScript
type UserInputSanitizedString = string;

function sanitizeInput(str: string): UserInputSanitizedString {
  return sanitize(str);
}

let userInput = sanitizeInput(getInput());

// Все еще может быть перезаписан строкой string
userInput = "new input";
```

## Утверждение типа (_Type Assertions_)

Иногда у вас будет информация о типе, которой TypeScript не может знать.

Например, если вы используете `document.getElementById`, TypeScript знает только, что это вернет какой-то `HTMLElement`, но вы можете знать, что на вашей странице всегда будет `HTMLCanvasElement` с заданным идентификатором.

В этой ситуации вы можете использовать утверждение типа, чтобы указать более конкретный тип:

```TypeScript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement;
```

Как и аннотация типа, утверждения типа удаляются компилятором и не влияют на поведение вашего кода во время выполнения.

Также можно использовать синтаксис угловых скобок `<...>` (кроме случаев, когда код находится в файле `.tsx`). Например:

```TypeScript
const myCanvas = <HTMLCanvasElement>document.getElementById("main_canvas");
```

<sub><sup>Поскольку утверждения типа удаляются во время компиляции, во время рантайма не происходит проверок связанных с утверждением типа. Если утверждение типа указано неверно, то вы можете получить ошибку во время исполнения кода.</sub></sup>

TypeScript допускает только утверждения типа, которые преобразуются в более конкретную или менее конкретную версию типа. Это правило предотвращает «невозможные» утверждения, такие как:

```TypeScript
const x = "hello" as number;
// Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
```

Иногда это правило может запрещать более сложные утверждения типов, которые могут быть допустимыми. Если это произойдет, вы можете использовать два утверждения: задать тип `any` или тип `unknown`, затем задать желаемый тип:

```TypeScript
const a = (expr as any) as T;
```

## Литеральные типы (_Literal Types_)

Помимо общих типов `string` и `number`, мы можем ссылаться на конкретные строки и числа типах.

Можно рассмотреть это на примере объявления переменных в JavaScript. Есть разные способы объявления переменной. И `var`, и `let` позволяют изменять то, что хранится внутри переменной, а `const` - нет. Это отражено в том, как TypeScript создает типы для литералов.

```TypeScript
let changingString = "Hello World";
changingString = "Ola Mundo";
// Так как `changeString` может представлять любую возможную строку
// TypeScript описывает это в системе типов как
changingString;
// ^ = let changingString: string

const constantString = "Hello World";
// Так как `changeString` может быть только одной возможной строкой
// н имеет литеральное представление типа
constantString;
// ^ = const constantString: "Hello World"
```

Сами по себе литеральные типы не представляют особой ценности:

```TypeScript
let x: "hello" = "hello";
// OK
x = "hello";
// ...
x = "howdy";
// Type '"howdy"' is not assignable to type '"hello"'.
```

Бесполезно иметь переменную, которая может иметь только одно значение.

Но, создав из литералов объединение, вы можете выразить гораздо более полезную концепцию - например, функции, которые принимают только определенный набор известных значений:

```TypeScript
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
// Argument of type '"centre"' is not assignable to parameter of type '"left" | "right" | "center"'.
```

Числовые литеральные типы работают также:

```TypeScript
function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}
```

Конечно, вы можете комбинировать их с обычными типами:

```TypeScript
interface Options {
  width: number;
}
function configure(x: Options | "auto") {
  // ...
}
configure({ width: 100 });
configure("auto");
configure("automatic");
// Argument of type '"automatic"' is not assignable to parameter of type 'Options | "auto"'.
```

Есть еще один тип литерала: логические литералы. Существует только два типа логических литералов, и, как вы могли догадаться, это типы `true` и `false`. Сам тип `boolean` на самом деле является просто псевдонимом для типа объединения `true | false`.

### Вывод литералов (_Literal Inference_)

Когда вы инициализируете переменную с помощью объекта, TypeScript предполагает, что свойства этого объекта могут изменить свои значения позже. Например, если вы написали такой код:

```TypeScript
const obj = { counter: 0 };
if (someCondition) {
  obj.counter = 1;
}
```

TypeScript не предполагает, что присвоение `1` полю, которое раньше имело значение `0`, является ошибкой. Другими словами, `obj.counter` должен иметь тип `number`, а не `0`, потому что типы используются как для чтения, так и для записи.

Тоже самое и для строк:

```TypeScript
const req = { url: "https://example.com", method: "GET" };
handleRequest(req.url, req.method);
// Argument of type 'string' is not assignable to parameter of type '"GET" | "POST"'.
```

В приведенном выше примере `req.method` считается строкой, а не константой `"GET"`. Поскольку код может быть изменен между созданием `req` и вызовом `handleRequest`, например, свойству `method` может быть назначена новая строка `"GUESS"`. TypeScript считает, что этот код содержит ошибку.

Есть два способа обойти это.

1. Вы можете изменить вывод типа, добавив утверждение типа в любом месте:
   ```TypeScript
   // Утверждение типа 1:
   const req = { url: "https://example.com", method: "GET" as "GET" };
   // Утверждение типа 2
   handleRequest(req.url, req.method as "GET");
   ```
   Утверждение 1 означает: «Мы указываем, что `req.method` всегда имеет литеральный тип `"GET"`», предотвращая возможное присвоение `"GUESS"` этому полю после.
2. Вы можете использовать утверждение типа `as const` для преобразования всего объекта в литеральный тип:
   `TypeScript const req = { url: "https://example.com", method: "GET" } as const; handleRequest(req.url, req.method); `
   Префикс `as const` действует как константа `const`, но для системы типов, гарантируя, что всем свойствам присваивается литеральный тип вместо более общего типа, такого как `string` или `number`.
