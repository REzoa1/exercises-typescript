
В этом уроке мы разберем опциональные параметры в функциях. 

## Использование опциональных параметров в функциях

Опциональные параметры в функциях используются во встроенных функциях JavaScript, когда параметр необязателен. Опциональные параметры задаются с помощью знака вопроса после имени переменной перед двоеточием.

Например, функция `split()` разбивает строку на массив строк по разделителю:

```typescript
function split(str: string, separator?: string)

split('hexlet');
split('hexlet,code-basics', ',');
```

В таком случае реальный тип переменной `separator` будет `string | undefined` (`string` или `undefined`).

Другой вариант задать опциональный параметр — присвоить значение по умолчанию:

```typescript
// Знака вопроса больше нет, так как есть значение по умолчанию
function split(str: string, separator: string = ',') {
  // ...
}

split('hexlet');
split('hexlet,code-basics', ',');
```

Разработчики пытаются применять эту логику к колбекам, но сталкиваются с ошибками. Разберем, почему так происходит.

## Колбек функции

Представим функцию `filter()`, которая фильтрует числовые массивы по переданному предикату:

```typescript
// Необязательный параметр index
function filter(coll: number[], callback: (arg: number, index?: number) => boolean) {
  const result: number[] = [];
  coll.forEach((n, index) => {
    // Здесь он передается в колбек
    if (callback(n, index)) {
      result.push(n);
    }
  });
  return result;
}
```

Как и во встроенном методе `filter()`, в этой функции колбек принимает вторым параметром индекс обрабатываемого элемента массива. Обычно его не используют, но иногда фильтрация массива делается на основе индексов. И в этой ситуации его указывают. Для этого мы пометили этот параметр как необязательный.

Проблема определения выше в том, что оно не работает:

```typescript
// Выполнится без ошибок
filter([1, 2], (n) => n > 1);
// Object is possibly 'undefined'
filter([1, 2], (n, index) => index > n);
```

В данном случае ошибка обозначает, что из-за необязательности внутри колбека в теории может оказаться `undefined`. Хотя по смыслу это происходить не может, так как индекс всегда определен.

Чтобы решить эту ситуацию, нужно отказаться от опциональности:

```typescript
function filter(coll: number[], callback: (arg: number, index: number) => boolean) {
  // ...
}
```

Если В JavaScript функция вызывается с большим количеством параметров, чем определено, то лишние игнорируются. TypeScript ведет себя так же. Колбеки с меньшим числом параметров всегда могут появляться там, где они же ожидаются с большим числом параметров, при условии, что типы общих параметров совпадают.

В примере выше аргументом передается функция `(n) => n > 1`, а вызывается она как `callback(n, index)`. Это приводит к игнорированию `index`, при этом никаких ошибок типов не возникает.