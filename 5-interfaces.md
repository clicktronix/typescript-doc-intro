# Интерфейсы

Информация взята с официальной документации TypeScript - https://www.typescriptlang.org/docs/handbook/interfaces.html

Один из основных принципов TypeScript заключается в том, что проверка типов фокусируется на форме, которую имеют значения. Иногда это называют «утиной типизацией» или «структурным подтипом». В TypeScript интерфейсы выполняют роль именования этих типов и являются мощным способом определения контрактов в вашем коде, а также контрактов с кодом вне вашего проекта.

Утиная типизация (Duck typing) - 

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