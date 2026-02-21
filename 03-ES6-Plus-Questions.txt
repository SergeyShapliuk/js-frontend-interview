```markdown
# ТЕМА 1: СТРЕЛОЧНЫЕ ФУНКЦИИ (ARROW FUNCTIONS)

## Содержание
1. [Что такое стрелочные функции?](#что-такое-стрелочные-функции)
2. [Синтаксис](#синтаксис)
3. [Отличия от обычных функций](#отличия-от-обычных-функций)
4. [Особенности работы с this](#особенности-работы-с-this)
5. [Когда использовать, а когда нет](#когда-использовать-а-когда-нет)
6. [Примеры](#примеры)
7. [Часто задаваемые вопросы](#часто-задаваемые-вопросы)

---

## Что такое стрелочные функции?

**Стрелочные функции (arrow functions)** — это сокращенный синтаксис для написания функций, введенный в ECMAScript 6 (ES6). Они предоставляют более лаконичный способ объявления функций и имеют важные отличия в работе с контекстом `this`.

```javascript
// Обычная функция
function add(a, b) {
  return a + b;
}

// Стрелочная функция
const add = (a, b) => a + b;
```

---

## Синтаксис

### 1. Базовый синтаксис
```javascript
const functionName = (param1, param2) => {
  // тело функции
  return result;
};
```

### 2. С неявным возвратом (implicit return)
```javascript
// Если тело функции - одно выражение, фигурные скобки и return можно опустить
const add = (a, b) => a + b;
```

### 3. С одним параметром
```javascript
// Скобки вокруг параметра можно опустить, если параметр один
const square = x => x * x;

// То же самое, но со скобками
const square = (x) => x * x;
```

### 4. Без параметров
```javascript
// Если параметров нет, скобки обязательны
const greet = () => 'Hello World';
```

### 5. Возврат объекта
```javascript
// Чтобы вернуть объект, нужно обернуть его в круглые скобки
const getUser = () => ({ name: 'John', age: 30 });
// Без скобок: const getUser = () => { name: 'John' }; // undefined!
```

### 6. Многострочная функция
```javascript
const processData = (data) => {
  const filtered = data.filter(item => item.active);
  const transformed = filtered.map(item => item.value * 2);
  return transformed;
};
```

---

## Отличия от обычных функций

| Характеристика | Обычная функция | Стрелочная функция |
|----------------|-----------------|---------------------|
| **Синтаксис** | function keyword | => operator |
| **Свой this** | Есть свой this | Нет своего this, наследует из контекста |
| **Конструктор** | Можно использовать с `new` | Нельзя использовать с `new` |
| **arguments** | Есть объект arguments | Нет arguments |
| **Методы объекта** | Подходит для методов | Не подходит для методов (проблемы с this) |
| **super** | Есть доступ | Нет доступа |
| **yield** | Можно использовать | Нельзя использовать |

---

## Особенности работы с this

### Самое важное отличие: **наследование this**

#### Обычная функция — свой this
```javascript
const person = {
  name: 'Иван',
  greet: function() {
    console.log(this); // person
    
    function inner() {
      console.log(this); // window/undefined (потеря контекста!)
    }
    inner();
  }
};

person.greet();
// Вывод: {name: 'Иван'}, затем window или undefined
```

#### Стрелочная функция — наследует this
```javascript
const person = {
  name: 'Иван',
  greet: function() {
    console.log(this); // person
    
    const inner = () => {
      console.log(this); // person (унаследован!)
    };
    inner();
  }
};

person.greet();
// Вывод: {name: 'Иван'}, затем {name: 'Иван'}
```

### Пример с setTimeout
```javascript
// Проблема с обычной функцией
const person = {
  name: 'Иван',
  greet: function() {
    setTimeout(function() {
      console.log('Привет, ' + this.name); // Привет, undefined
    }, 1000);
  }
};

// Решение со стрелочной функцией
const person = {
  name: 'Иван',
  greet: function() {
    setTimeout(() => {
      console.log('Привет, ' + this.name); // Привет, Иван
    }, 1000);
  }
};

// Альтернативное решение с bind
const person = {
  name: 'Иван',
  greet: function() {
    setTimeout(function() {
      console.log('Привет, ' + this.name);
    }.bind(this), 1000);
  }
};
```

---

## Когда использовать, а когда нет

### ✅ **Используйте стрелочные функции когда:**

1. **Колбэки и обработчики событий**, где нужно сохранить контекст
   ```javascript
   button.addEventListener('click', () => {
     console.log(this); // this из внешнего контекста
   });
   ```

2. **Методы массивов** (map, filter, reduce)
   ```javascript
   const numbers = [1, 2, 3];
   const doubled = numbers.map(n => n * 2);
   ```

3. **Функции высшего порядка**, возвращающие функции
   ```javascript
   const multiply = x => y => x * y;
   ```

4. **Простые однострочные функции**
   ```javascript
   const isEven = num => num % 2 === 0;
   ```

### ❌ **НЕ используйте стрелочные функции когда:**

1. **Методы объекта**, которым нужен доступ к объекту через `this`
   ```javascript
   // Плохо
   const person = {
     name: 'Иван',
     greet: () => {
       console.log(this.name); // undefined, this не person
     }
   };
   
   // Хорошо
   const person = {
     name: 'Иван',
     greet() {
       console.log(this.name); // Иван
     }
   };
   ```

2. **Конструкторы** (нельзя использовать с `new`)
   ```javascript
   // Плохо
   const User = (name) => {
     this.name = name; // Ошибка!
   };
   const user = new User('Иван'); // TypeError
   ```

3. **Обработчики событий**, если нужен доступ к `event.target`
   ```javascript
   // Если нужен this элемента, используйте обычную функцию
   button.addEventListener('click', function() {
     console.log(this); // button
   });
   ```

4. **Функции с динамическим контекстом**
   ```javascript
   // Плохо для call/apply/bind
   const func = () => console.log(this);
   func.call({name: 'test'}); // this не изменится!
   ```

5. **Функции, которым нужен объект arguments**
   ```javascript
   // Плохо
   const sum = () => {
     return Array.from(arguments).reduce((s, n) => s + n, 0);
   };
   
   // Хорошо
   const sum = (...args) => {
     return args.reduce((s, n) => s + n, 0);
   };
   ```

---

## Примеры

### Пример 1: Простые арифметические операции
```javascript
// Обычные функции
function multiply(a, b) {
  return a * b;
}

// Стрелочные функции
const multiply = (a, b) => a * b;

console.log(multiply(5, 3)); // 15
```

### Пример 2: Фильтрация массива
```javascript
const users = [
  { name: 'Иван', age: 25, active: true },
  { name: 'Мария', age: 30, active: false },
  { name: 'Петр', age: 35, active: true }
];

// Получаем активных пользователей
const activeUsers = users.filter(user => user.active);
// [{ name: 'Иван', age: 25, active: true }, { name: 'Петр', age: 35, active: true }]

// Получаем имена пользователей старше 30
const namesOver30 = users
  .filter(user => user.age > 30)
  .map(user => user.name);
// ['Петр']
```

### Пример 3: Фабрика функций (замыкание)
```javascript
// Создаем функцию-множитель
const createMultiplier = multiplier => number => number * multiplier;

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### Пример 4: Сортировка
```javascript
const products = [
  { name: 'Ноутбук', price: 1000 },
  { name: 'Мышь', price: 25 },
  { name: 'Монитор', price: 300 }
];

// Сортировка по цене
const sortedByPrice = products.sort((a, b) => a.price - b.price);
```

### Пример 5: Обработка событий с сохранением контекста
```javascript
class Counter {
  constructor() {
    this.count = 0;
    
    // Стрелочная функция сохраняет контекст экземпляра класса
    this.handleClick = () => {
      this.count++;
      console.log(this.count);
    };
    
    document.getElementById('btn').addEventListener('click', this.handleClick);
  }
}
```

---

## Часто задаваемые вопросы

### Вопрос 1: Можно ли использовать стрелочные функции как методы объекта?
**Нет**, если методу нужен доступ к объекту через `this`. Стрелочная функция возьмет `this` из внешней области видимости (скорее всего, глобальный объект).

```javascript
const obj = {
  value: 42,
  
  // Плохо - this не obj
  bad: () => console.log(this.value),
  
  // Хорошо - this = obj
  good() {
    console.log(this.value);
  },
  
  // Тоже хорошо
  good2: function() {
    console.log(this.value);
  }
};
```

### Вопрос 2: Можно ли изменить this в стрелочной функции с помощью call/apply/bind?
**Нет**. Стрелочные функции игнорируют привязку `this` через call, apply или bind. Они всегда используют `this` из того места, где были созданы.

```javascript
const arrow = () => console.log(this);

arrow.call({name: 'test'}); // this останется прежним (не {name: 'test'})

const boundArrow = arrow.bind({name: 'test'});
boundArrow(); // тоже не сработает
```

### Вопрос 3: Есть ли у стрелочных функций свойство prototype?
**Нет**. Поскольку стрелочные функции нельзя использовать как конструкторы, у них нет свойства `prototype`.

```javascript
const arrow = () => {};
console.log(arrow.prototype); // undefined

function regular() {}
console.log(regular.prototype); // {constructor: ƒ}
```

### Вопрос 4: Как получить доступ к arguments в стрелочной функции?
Используйте rest-параметры (`...args`) вместо `arguments`.

```javascript
// Вместо arguments
const sum = (...args) => args.reduce((total, num) => total + num, 0);

console.log(sum(1, 2, 3, 4)); // 10
```

### Вопрос 5: Что будет, если вернуть объект в стрелочной функции без скобок?
```javascript
// Неправильно - интерпретируется как тело функции
const getObj = () => { name: 'John' };
console.log(getObj()); // undefined

// Правильно - объект в скобках
const getObj = () => ({ name: 'John' });
console.log(getObj()); // {name: 'John'}
```

---

## Краткое резюме

| Что нужно помнить | Почему это важно |
|-------------------|-------------------|
| Стрелочные функции не имеют своего `this` | Они наследуют `this` из внешнего контекста |
| Нельзя использовать с `new` | Не могут быть конструкторами |
| Нет `arguments` | Используйте rest-параметры |
| Компактный синтаксис | Идеально для колбэков |
| Не подходят для методов | Если нужен доступ к объекту через `this` |
| Игнорируют `call/apply/bind` | `this` нельзя переопределить |

---

## Проверь себя

```javascript
// Вопрос 1: Что выведет этот код?
const person = {
  name: 'Иван',
  greet: () => {
    console.log(this.name);
  }
};
person.greet(); // ?

// Вопрос 2: А этот?
const person = {
  name: 'Иван',
  greet: function() {
    const inner = () => console.log(this.name);
    inner();
  }
};
person.greet(); // ?

// Вопрос 3: Можно ли так сделать?
const User = (name) => {
  this.name = name;
};
const user = new User('Иван');

// Ответы:
// 1: undefined (this - глобальный объект)
// 2: 'Иван' (inner наследует this от greet)
// 3: Ошибка! Стрелочные функции не конструкторы
```
```
