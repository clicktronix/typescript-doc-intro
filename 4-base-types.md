# Базовые типы

Информация взята с официальной документации TypeScript - https://www.typescriptlang.org/docs/handbook

- Boolean - самый базовый тип данных, которые принимает значение true или false, то есть он может быть либо положительлным, либо отрицательным
  ```TypeScript
  let isDone: boolean = false;
  ```
- Number - Как и в JavaScript, все числа в TypeScript являются либо значениями с плавающей запятой, либо BigInteger. Эти числа с плавающей запятой получают номер типа, а BigInteger - тип bigint. В дополнение к шестнадцатеричным и десятичным литералам TypeScript также поддерживает двоичные и восьмеричные литералы, представленные в ECMAScript 2015.
  ```TypeScript
  let decimal: number = 6;
  let hex: number = 0xf00d;
  let binary: number = 0b1010;
  let octal: number = 0o744;
  let big: bigint = 100n;
  ```
- String - Другая фундаментальная часть создания программ на JavaScript для веб-страниц и серверов - это работа с текстовыми данными. Как и в других языках, мы используем строку типа для ссылки на эти текстовые типы данных. Как и JavaScript, TypeScript также использует двойные кавычки (") или одинарные кавычки (') для окружения строковых данных. Вы также можете использовать строки шаблона, которые могут охватывать несколько строк и содержать встроенные выражения. Эти строки окружены символом обратной кавычки / обратной кавычки (`), а встроенные выражения имеют форму $ {expr}.
  ```TypeScript
  let color: string = "blue";
  color = 'red';
  let fullName: string = `Bob Bobbington`;
  let age: number = 37;
  let sentence: string = `Hello, my name is ${fullName}.

  I'll be ${age + 1} years old next month.`;
  ```
- Null and Undefined - В TypeScript оба типа `undefined` и `null` фактически имеют имена `undefined` и `null` соответственно. Как и void, сами по себе они не особо полезны:
  ```TypeScript
  let u: undefined = undefined;
  let n: null = null;
  ```
  По умолчанию null и undefined являются подтипами всех других типов. Это означает, что вы можете присвоить null и undefined чему-то вроде числа.
  Однако при использовании флага `--strictNullChecks` значения `null` и `undefined` могут быть присвоены только unknown, any и их соответствующим типам (единственное исключение - undefined также присваивается void).
  Это помогает избежать многих распространенных ошибок. В случаях, когда вы хотите передать либо строку, либо null или undefined, вы можете использовать строку типа объединения | null | неопределенный.
  В качестве примечания: мы поощряем использование `--strictNullChecks`, когда это возможно, но для целей этого руководства мы предполагаем, что он отключен.
- Object and object - это тип, представляющий непримитивный тип, т.е. все, что не является числом, строкой, логическим значением, bigint, symbol, null или undefined.
- Array - TypeScript, как и JavaScript, позволяет работать с массивами значений. Типы массивов можно записать одним из двух способов. В первом вы используете тип элементов, за которым следует [], чтобы обозначить массив этого типа элемента:
  ```TypeScript
  let list: number[] = [1, 2, 3];
  ```
  Второй способ использует общий тип массива Array <elemType>:
  ```TypeScript
  let list: Array<number> = [1, 2, 3];
  ```
- Tuple - Типы кортежей позволяют выразить массив с фиксированным числом элементов, типы которых известны, но не обязательно должны быть одинаковыми. Например, вы можете представить значение как пару строки и числа:
  ```TypeScript
  // Задаем тип кортежа
  let x: [string, number];
  // Инициализируем его с корректными данными
  x = ["hello", 10];
  // Инициализируем с не корректными данными
  x = [10, "hello"]; // Ошибка
  Type 'number' is not assignable to type 'string'.
  Type 'string' is not assignable to type 'number'.
  ```
  При доступе к элементу с известным индексом извлекается правильный тип:
  ```TypeScript
  console.log(x[0].substring(1));
  console.log(x[1].substring(1));
  ```
  Доступ к элементу вне набора известных индексов завершается ошибкой:
  ```TypeScript
  x[3] = "world";
  ```
  Tuple type '[string, number]' of length '2' has no element at index '3'.
- Enum - Полезным дополнением к стандартному набору типов данных из JavaScript является enum. Как и в таких языках, как C #, перечисление - это способ дать более понятные имена наборам числовых значений. По умолчанию перечисления начинают нумерацию своих членов, начиная с 0. Вы можете изменить это, вручную установив значение одного из его членов. Например, мы можем начать предыдущий пример с 1 вместо 0 или даже вручную установить все значения в перечислении:
  ```TypeScript
  enum Color {
    Red = 1,
    Green,
    Blue,
  }
  let c: Color = Color.Green;
  ```
  Удобной функцией перечислений является то, что вы также можете перейти от числового значения к имени этого значения в перечислении. Например, если у нас есть значение 2, но мы не уверены, что оно отображается в перечислении Color выше, мы могли бы найти соответствующее имя:
  ```TypeScript
  enum Color {
    Red = 1,
    Green,
    Blue,
  }
  let colorName: string = Color[2];
  ```
- Any - В некоторых ситуациях доступна не вся информация о типе или ее объявление потребует несоответствующих усилий. Это может произойти для значений из кода, который был написан без TypeScript или сторонней библиотеки. В этих случаях мы можем отказаться от проверки типов. Для этого мы помечаем эти значения типом any:
  ```TypeScript
  declare function getValue(key: string): any;
  // OK, return value of 'getValue' is not checked
  const str: string = getValue("myString");
  ```
  Тип any - это мощный способ работы с существующим JavaScript, позволяющий постепенно включать и отключать проверку типов во время компиляции.
  В конце концов, помните, что все удобство достигается за счет потери безопасности типов. Безопасность типов - одна из основных причин использования TypeScript, и вы должны стараться избегать их использования, когда это не нужно.
- Void - void чем-то похож на противоположность any: полное отсутствие какого-либо типа. Вы можете часто видеть это как возвращаемый тип функций, которые не возвращают значение:
  ```TypeScript
  function warnUser(): void {
    console.log("This is my warning message");
  }
  ```
  Объявление переменных типа void бесполезно, потому что вы можете присвоить им только значение null (только если --strictNullChecks не указано, см. Следующий раздел) или undefined:
  ```TypeScript
  let unusable: void = undefined;
  // OK if `--strictNullChecks` is not given
  unusable = null;
  ```
- Never - Тип never представляет собой тип значений, которые никогда не встречаются. Например, никогда не является типом возвращаемого значения для выражения функции или выражения функции стрелки, которое всегда вызывает исключение или никогда не возвращает. Переменные также приобретают тип никогда, когда сужаются какими-либо защитниками типа, которые никогда не могут быть истинными.
  Тип never является подтипом каждого типа и может быть ему назначен; однако ни один тип не является подтипом и не присваивается никогда (кроме самого себя). Даже любое не может быть назначено никогда.
  ```TypeScript
  // Function returning never must not have a reachable end point
  function error(message: string): never {
    throw new Error(message);
  }

  // Inferred return type is never
  function fail() {
    return error("Something failed");
  }

  // Function returning never must not have a reachable end point
  function infiniteLoop(): never {
    while (true) {}
  }
  ```
- Unknown - Нам может потребоваться описать тип переменных, о которых мы не знаем, когда пишем приложение. Эти значения могут поступать из динамического содержимого - например, от пользователя - или мы можем намеренно принять все значения в нашем API. В этих случаях мы хотим предоставить тип, который сообщает компилятору и будущим читателям, что эта переменная может быть чем угодно, поэтому мы даем ей неизвестный тип.
  ```TypeScript
  let notSure: unknown = 4;
  notSure = "maybe a string instead";

  // OK, definitely a boolean
  notSure = false;
  ```
  Если у вас есть переменная с неизвестным типом, вы можете сузить ее до чего-то более конкретного, выполнив проверки типа, проверки сравнения или более сложные меры защиты типов, которые будут обсуждаться в следующей главе:
  ```TypeScript
  declare const maybe: unknown;
  // 'maybe' could be a string, object, boolean, undefined, or other types
  const aNumber: number = maybe;
  Type 'unknown' is not assignable to type 'number'.
  Type 'unknown' is not assignable to type 'number'.
  if (maybe === true) {
    // TypeScript knows that maybe is a boolean now
    const aBoolean: boolean = maybe;
    // So, it cannot be a string
    const aString: string = maybe;
  Type 'boolean' is not assignable to type 'string'.
  Type 'boolean' is not assignable to type 'string'.
  }

  if (typeof maybe === "string") {
    // TypeScript knows that maybe is a string
    const aString: string = maybe;
    // So, it cannot be a boolean
    const aBoolean: boolean = maybe;
  Type 'string' is not assignable to type 'boolean'.
  Type 'string' is not assignable to type 'boolean'.
  }
  ```