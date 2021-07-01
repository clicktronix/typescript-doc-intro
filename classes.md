# Классы

TypeScript предлагает полную поддержку ключевого слова class, представленного в ES2015.

Как и для других фич языка JavaScript, TypeScript добавляет аннотации типов и другой вспомогательный синтаксис для того, чтобы вы могли описывать отношения между классами и другими типами.

## Члены класса

Вот самый простой класс - пустой:

```TypeScript
class Point {}
```

Этот класс пока не очень полезен, поэтому давайте начнем добавлять членов класса.

### Поля

Объявление поля создает публичное изменяемое свойство класса:

```TypeScript
class Point {
  x: number;
  y: number;
}

const pt = new Point();
pt.x = 0;
pt.y = 0;
```

Аннотация типа для поля является необязательной, но если не указать тип, то он будет равен `any`.

Поля также могут быть инициализированы различными значениями.

```TypeScript
class Point {
  x = 0;
  y = 0;
}

const pt = new Point();
console.log(`${pt.x}, ${pt.y}`);
```

Как и в случае с `const`, `let` и `var`, после инициализации поля, его тип будет равен типу значения, которым поле было инициализировано.

```TypeScript
const pt = new Point();
pt.x = "0";
// Type 'string' is not assignable to type 'number'.
```

### --strictPropertyInitialization

Параметр `strictPropertyInitialization` определяет, нужно ли инициализировать поля класса в конструкторе.

```TypeScript
class BadGreeter {
  name: string;
  // Property 'name' has no initializer and is not definitely assigned in the constructor.
}
```

```TypeScript
class GoodGreeter {
  name: string;

  constructor() {
    this.name = "hello";
  }
}
```

Обратите внимание, что поле необходимо инициализировать в самом конструкторе. TypeScript не анализирует методы, вызываемые вами из конструктора, для обнаружения инициализаций, поскольку производный класс может переопределить эти методы и не выполнить инициализацию членов. Если вы намерены инициализировать поле средствами, отличными от конструктора (например, внешняя библиотека заполняет часть вашего класса за вас), вы можете использовать оператор утверждения присваивания `!`:

```TypeScript
class OKGreeter {
  // Поле не инициализировано, но ошибки нет
  name!: string;
}
```

### `readonly`

Поля могут иметь модификатор с префиксом `readonly`. Это защищает поле от присвоения ему значения вне конструктора.

```TypeScript
class Greeter {
  readonly name: string = "world";

  constructor(otherName?: string) {
    if (otherName !== undefined) {
      this.name = otherName;
    }
  }

  err() {
    this.name = "not ok";
    // Cannot assign to 'name' because it is a read-only property.
  }
}
const g = new Greeter();
g.name = "also not ok";
// Cannot assign to 'name' because it is a read-only property.
```

### Конструкторы

Конструкторы классов очень похожи на функции. Вы можете добавлять параметры с аннотациями типов, значениями по умолчанию и перегрузками:

```TypeScript
class Point {
  x: number;
  y: number;

  // Сигнатура функции со значениями по дефолту
  constructor(x = 0, y = 0) {
    this.x = x;
    this.y = y;
  }
}
```

```TypeScript
class Point {
  // Перегрузки
  constructor(x: number, y: string);
  constructor(s: string);
  constructor(xs: any, y?: any) {
    ...
  }
}
```

Между сигнатурами конструктора классов и сигнатурами функций есть лишь несколько различий:

- Конструкторы не могут иметь параметров типа - они принадлежат объявлению внешнего класса, о котором мы узнаем позже.
- У конструкторов нет типа возвращаемого значения - тип экземпляра класса всегда будем сам класс.

### Вызовы `super`

Как и в JavaScript, если у вас есть базовый класс, вам нужно вызвать `super();` в теле вашего конструктора, прежде чем использовать поля базового класса через `this.`:

```TypeScript
class Base {
  k = 4;
}

class Derived extends Base {
  constructor() {
    // Выбрасывает исключение в ES6
    console.log(this.k);
    // 'super' must be called before accessing 'this' in the constructor of a derived class.
    super();
  }
}
```

Забыть вызвать `super` - это частая ошибка в JavaScript, но TypeScript сообщит вам, когда это необходимо.

### Методы

Функции в свойствах класса называется методами. Методы могут использовать аннотации тех же типов, что и функции и конструкторы:

```TypeScript
class Point {
  x = 10;
  y = 10;

  scale(n: number): void {
    this.x *= n;
    this.y *= n;
  }
}
```

Кроме стандартных аннотаций типов, TypeScript не добавляет ничего нового в методы. Обратите внимание, что внутри тела метода по-прежнему обязательно получать доступ к полям и другим методам через `this.`. Если обратиться к переменной без `this`, то TypeScript будет искать эту переменную во внешнем скоупе, вместо скоупа класса.

```TypeScript
let x: number = 0;

class C {
  x: string = "hello";

  m() {
    // Пытаемся модифицировать 'x', который не является свойством класса
    x = "world";
    // Type 'string' is not assignable to type 'number'.
  }
}
```

### Setters/Getters (сеттеры и геттеры)

Классы также могут иметь аксессоры (accessor properties):

```TypeScript
class C {
  _length = 0;
  get length() {
    return this._length;
  }
  set length(value) {
    this._length = value;
  }
}
```

<sub><sup>Обратите внимание, что пара get/set без дополнительной логики очень редко используется в JavaScript. Если вам не нужно добавлять дополнительную логику во время операций get/set, можно публичные поля класса и работать с ними напрямую.</sup></sub>

TypeScript имеет некоторые специальные правила вывода для аксессоров:

- Если существует аксессор `get`, но не задан аксессор `set`, то поле автоматически становится `readonly`
- Если тип параметра сеттера не указан, он выводится из типа возвращаемого значения геттера.
- Геттеры и сеттеры должны иметь одинаковую видимость членов (см. ниже)

Начиная с TypeScript 4.3, можно использовать аксессоры с разными типами для геттера и сеттера.

```TypeScript
class Thing {
    _size = 0;

    get size(): number {
        return this._size;
    }

    set size(value: string | number | boolean) {
        let num = Number(value);

        // Не разрешаем значения NaN, Infinity
        if (!Number.isFinite(num)) {
            this._size = 0;
            return;
        }

        this._size = num;
    }
}
```

### Индексные сигнатуры

Классы могут объявлять индексные сигнатуры; они работают так же, как и для других типов объектов:

```TypeScript
class MyClass {
  [s: string]: boolean | ((s: string) => boolean);

  check(s: string) {
    return this[s] as boolean;
  }
}
```

Поскольку тип сигнатуры индекса должен также описывать типы методов, использовать такие типы с пользой непросто. Как правило, индексированные данные лучше хранить в другом месте, а не в самом экземпляре класса.

## Наследование классов

Как и в других языках с объектно-ориентированным подходом, классы в JavaScript могут наследоваться от базовых классов.

### Инструмент `implements`

Вы можете использовать ключевое солово `implements`, чтобы проверить, соответствует ли класс определенному интерфейсу. Если класс реализовывает этот интерфейс не верно, будет выдана ошибка:

```TypeScript
interface Pingable {
  ping(): void;
}

class Sonar implements Pingable {
  ping() {
    console.log("ping!");
  }
}

class Ball implements Pingable {
Class 'Ball' incorrectly implements interface 'Pingable'.
  Property 'ping' is missing in type 'Ball' but required in type 'Pingable'.
  pong() {
    console.log("pong!");
  }
}
```

Классы также могут реализовывать несколько интерфейсов, например `class C implements A, B {`.

<b>Меры предосторожности</b>

Важно понимать, что выражение `implements` реализует только проверку того, что класс можно рассматривать как тип интерфейса. Он не меняет тип класса или его методы.

Распространенная ошибка - предполагать, что выражение `implements` изменит тип класса, но это не так!

```TypeScript
interface Checkable {
  check(name: string): boolean;
}

class NameChecker implements Checkable {
  check(s) {
  // Parameter 's' implicitly has an 'any' type.
    return s.toLowercse() === "ok"; // Обратите внимание, что параметр s автоматически не подхватывает тип из интерфейса выше
  }
}
```

В этом примере мы, предполагаем, что тип параметра `s` будет наследоваться от типа параметра в интерфейсе `Checkable` и будет `name: string`. Это не так. Выражение `implements` говорит о том, что класс должен реализовывать такой интерфейс, то есть методу `check` необходимо полностью прописать сигнатуру функции, чтобы типы с интерфейсом `Checkable` были сопоставимы.

Точно так же реализация интерфейса с необязательным свойством не создает этого свойства:

```TypeScript
interface A {
  x: number;
  y?: number;
}
class C implements A {
  x = 0;
}
const c = new C();
c.y = 10;
// Property 'y' does not exist on type 'C'.
```

### Инструмент `extends`

Классы могут наследоваться `extend` от базового класса. Производный класс имеет все свойства и методы своего базового класса, а также определяет дополнительные члены.

```TypeScript
class Animal {
  move() {
    console.log("Moving along!");
  }
}

class Dog extends Animal {
  woof(times: number) {
    for (let i = 0; i < times; i++) {
      console.log("woof!");
    }
  }
}

const d = new Dog();
// Наследуемый метод базового класса
d.move();
// Созданный метод в текущем классе
d.woof(3);
```

Производный класс может переопределять поле или свойство базового класса. Вы можете использовать `super` синтаксис для доступа к методам базового класса. Обратите внимание: поскольку классы JavaScript представляют собой простой объект, то у нас нет возможности получить поле класса через `super`, но можем использовать для этого `this`.

TypeScript требует, чтобы производный класс всегда был подтипом своего базового класса.

Например, вот хороший способ переопределить метод:

```TypeScript
class Base {
  greet() {
    console.log("Hello, world!");
  }
}

class Derived extends Base {
  greet(name?: string) {
    if (name === undefined) {
      super.greet();
    } else {
      console.log(`Hello, ${name.toUpperCase()}`);
    }
  }
}

const d = new Derived();
d.greet();
d.greet("reader");
```

Важно, чтобы производный класс следовал контракту базового класса. И помните, что можно ссылаться на экземпляр производного класса через ссылку на базовый класс:

```TypeScript
// Присваиваем производный класс переменной с типом базового класса
const b: Base = d;
// Ошибок нет
b.greet();
```

Что, если `Derived` не соблюдает контракт `Base`?
