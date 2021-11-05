# `Beautiful Mind` :metal:

[На главную](../README.md)

> [Сниппеты](./javascript_snippets.md)&nbsp;&nbsp;👀

### Содержание

- [Сумма чисел из последовательности Фибоначчи](#сумма-чисел-из-последовательности-фибоначчи)
- [Факториал числа](#факториал-числа)
- [Сложение матриц](#сложение-матриц)
- [Транспонирование матрицы](#транспонирование-матрицы)
- [Рисование пирамиды](#рисование-пирамиды)
- [Fizz Buzz](#fizz-buzz)
- [Спиральная матрица](#спиральная-матрица)
- [Шифр Цезаря](#шифр-цезаря)
- [Являются ли строки анаграммами?](#являются-ли-строки-анаграммами)
- [Является ли строка палиндромом?](#является-ли-строка-палиндромом)
- [Количество гласных букв в строке](#количество-гласных-букв-в-строке)
- [Самый длинный элемент массива](#самый-длинный-элемент-массива)
- [Является ли второй массив подмножеством первого?](#является-ли-второй-массив-подмножеством-первого)
- [Можно ли объединить два массива без дубликатов?](#можно-ли-объединить-два-массива-без-дубликатов)
- [Сдвиг массива вправо на указанное количество элементов](#сдвиг-массива-вправо-на-указанное-количество-элементов)
- [Инверсия элементов массива без `reverse()`](#инверсия-элементов-массива-без-reverse)
- [Наиболее часто встречающийся элемент массива](#наиболее-часто-встречающийся-элемент-массива)
- [Цикличный генератор](#цикличный-генератор)
- [Наибольший и наименьший элемент массива без `Math.max()` и `Math.min()`](#наибольший-и-наименьший-элемент-массива-без-mathmax-и-mathmin)
- [Распаковка массива (рекурсивная реализация `flat()`)](#распаковка-массива-рекурсивная-реализация-flat)
- [Проверка уникальности элементов с помощью условия - функции](#проверка-уникальности-элементов-с-помощью-условия---функции)
- [Удаление всех ложных значений из объекта](#удаление-всех-ложных-значений-из-объекта)
- [Упаковка и распаковка объекта](#упаковка-и-распаковка-объекта)
- [Группировка свойств объекта по условию - функции](#группировка-свойств-объекта-по-условию---функции)
- [Хеширование объекта](#хеширование-объекта)
- [Является ли второй объект подмножеством первого?](#является-ли-второй-объект-подмножеством-первого)
- [Синхронное выполнение функций](#синхронное-выполнение-функций)


## Сумма чисел из последовательности Фибоначчи

```js
const fib = (n) => {
  if (n < 0) return 'invalid'
  if (n <= 1) return n

  let a = 1,
    b = 1

  for (let i = 3; i <= n; i++) {
    let c = a + b
    a = b
    b = c
  }

  return b
}
```

## Факториал числа

```js
const fact = (n) => {
  if (n < 0) return 'invalid'
  if (n <= 1) return 1

  return n * fact(n - 1)
}
```

## Сложение матриц

```js
function sumMatrices(x, y) {
 const result = [
   [0, 0, 0],
   [0, 0, 0],
   [0, 0, 0]
 ]

 for (const i in x) {
   for (const j in x[0]) {
     result[i][j] = x[i][j] + y[i][j]
   }
 }

 return result
}

const x = [
 [1, 2, 3],
 [4, 5, 6],
 [7, 8, 9]
]

const y = [
 [9, 8, 7],
 [3, 2, 1],
 [6, 5, 4]
]

sumMatrices(x, y)
/*
[
 [10, 10, 10],
 [7, 7, 7],
 [13, 13, 13]
]
*/

// or
const sumMatrices = (x, y) =>
 x.map((_, i) => x[i].map((_, j) => x[i][j] + y[i][j]))
```

## Транспонирование матрицы

```js
function transposeMatrix(x) {
 const result = [
   [0, 0, 0],
   [0, 0, 0]
 ]

 for (const i in x) {
   for (const j in x[0]) {
     result[j][i] = x[i][j]
   }
 }

 return result
}

const x = [
 [1, 2],
 [4, 5],
 [7, 8]
]

transposeMatrix(x)
/*
[
 [1, 4, 7],
 [2, 5, 8]
]
*/

// or
const transposeMatrix = (x) =>
 x[0].map((_, i) => x.map((_, j) => x[j][i]))
```

## Рисование пирамиды

```js
function drawHalfPyramid(rows) {
  let pyramid = ''
  for (let i = 1; i <= rows; i++) {
    for (let j = 0; j < i; j++) {
      pyramid += '#'
    }
    pyramid += '\n'
  }

  return pyramid
}

drawHalfPyramid(5)
/*
#
##
##
###
####
*/

function drawInvertedHalfPyramid(rows) {
  let pyramid = ''
  for (let i = rows; i > 0; i--) {
    for (let j = 0; j < i; j++) {
      pyramid += '#'
    }
    pyramid += '\n'
  }
  return pyramid
}

drawInvertedHalfPyramid(5)
/*
####
###
##
##
#
*/

function drawFullPyramid(rows) {
  let levels = ''

  const mid = ~~((2 * rows - 1) / 2)

  for (let row = 0; row < rows; row++) {
    let level = ''

    for (let col = 0; col < 2 * rows - 1; col++) {
      level += mid - row <= col && col <= mid + row ? '#' : ' '
    }

    levels += level + '\n'
  }

  return levels
}

drawFullPyramid(5)
/*
    #
   ###
  #####
 ######
########
*/
```

## Fizz Buzz

```js
const fizzBuzz = (n) => {
  const arr = []
  do {
    if (n % 15 === 0) arr.push('Fizz Buzz')
    else if (n % 3 === 0) arr.push('Fizz')
    else if (n % 5 === 0) arr.push('Buzz')
    else arr.push(n)
  } while (n-- > 1)
  return arr
}

fizzBuzz(15)
// ["Fizz Buzz", 14, 13, "Fizz", 11, "Buzz", "Fizz", 8, 7, "Fizz", "Buzz", 4, "Fizz", 2, 1]

// or
const fizzBuzz = (n) => {
  const arr = []
  for (let i = 1; i <= n; i++) {
    let a = i % 3 === 0,
      b = i % 5 === 0
    arr.push(a ? (b ? 'FizzBuzz' : 'Fizz') : b ? 'Buzz' : i)
  }
  return arr
}

fizzBuzz(15)
// [1, 2, "Fizz", 4, "Buzz", "Fizz", 7, 8, "Fizz", "Buzz", 11, "Fizz", 13, 14, "FizzBuzz"]
```

## Спиральная матрица

```js
const createSpiralMatrix = (n) => {
  let counter = 1
  let startR = 0,
    endR = n - 1
  let startC = 0,
    endC = n - 1

  const matrix = []

  for (let i = 0; i < n; i++) matrix.push([])

  while (startC <= endC && startR <= endR) {
    for (let i = startC; i <= endC; i++) {
      matrix[startR][i] = counter
      counter++
    }
    startR++

    for (let i = startR; i <= endR; i++) {
      matrix[i][endC] = counter
      counter++
    }
    endC--

    for (let i = endC; i >= startC; i--) {
      matrix[endR][i] = counter
      counter++
    }
    endR--

    for (let i = endR; i >= startR; i--) {
      matrix[i][startC] = counter
      counter++
    }
    startC++
  }

  return matrix
}

createSpiralMatrix(3)
/*
[
  [1, 2, 3],
  [8, 9, 4],
  [7, 6, 5]
]
*/
```

## Шифр Цезаря

```js
const caesarCipher = (str, num) => {
  const arr = [...'abcdefghijklmnopqrstuvwxyz']
  let newStr = ''

  for (const char of str) {
    const lower = char.toLowerCase()

    if (!arr.includes(lower)) {
      newStr += char
      continue
    }

    let index = arr.indexOf(lower) + (num % 26)

    if (index > 25) index -= 26
    if (index < 0) index += 26

    newStr +=
      char === char.toUpperCase() ? arr[index].toUpperCase() : arr[index]
  }
  return newStr
}

caesarCipher('Hello World', 100) // Dahhk Sknhz
caesarCipher('Dahhk Sknhz', -100) // Hello World
```

## Являются ли строки анаграммами?

```js
const sortStr = (str) => [...str.replace(/\W/g, '').toLowerCase()].sort().join('')

const isAnagrams = (a, b) => sortStr(a) === sortStr(b)
isAnagrams('listen', 'silent') // true

// or
const isAnagrams = (x, y) =>
  x.length === y.length &&
  [...x.toLowerCase()].every((c) => y.toLowerCase().includes(c))

isAnagrams('the eyes', 'they see') // true
```

## Является ли строка палиндромом?

```js
const isPalindrome = (str) =>
  [...str.toLowerCase().replace(/\W/g, '')].every(
    (c, i) => c === str[str.length - 1 - i]
  )

isPalindrome('А роза упала на лапу Азора') // true

// or
const isPalindrome = (str) => (
  (str = str.toLowerCase().replace(/\W/g, '')),
  str === [...str].reverse().join('')
)

isPalindrome('Borrow or rob') // true
```

## Количество гласных букв в строке

```js
const getVowelsCount = (str) => str.match(/[aeiouyауоиэыяюеё]/gi)?.length || 0

getVowelsCount('hello world привет народ') // 7
```

## Самый длинный элемент массива

```js
const getLongestItem = (...args) =>
  args.reduce((a, b) => (b.length > a.length ? b : a))

getLongestItem('a', 'abc', 'ab') // abc
getLongestItem(...['abc', 'a', 'ab'], 'abcd') // abcd
getLongestItem([1, 2, 3], [1, 2], [1, 2, 3, 4]) // [1, 2, 3, 4]
```

## Является ли второй массив подмножеством первого?

```js
const isSubset = (x, y) => {
  const _x = new Set(x)
  const _y = new Set(y)
  return [..._x].every((i) => _y.has(i))
}

isSubset([1, 2, 3, 4], [2, 4]) // true
```

## Можно ли объединить два массива без дубликатов?

```js
const isJoinable = (a, b) => {
  const _a = new Set(a)
  const _b = new Set(b)
  return [..._a].every((i) => !_b.has(i))
}

isJoinable([1, 2], [3, 4]) // true
isJoinable([1, 2], [1, 3]) // false
```

## Сдвиг массива вправо на указанное количество элементов

```js
const offset = (arr, o) => [...arr.slice(o), ...arr.slice(0, o)]

offset([1, 2, 3, 4, 5], 2) // [3, 4, 5, 1, 2]
offset([1, 2, 3, 4, 5], -2) // [4, 5, 1, 2, 3]
```

## Инверсия элементов массива без `reverse()`

```js
const reverse = (arr) => {
  for (let i = 0; i < arr.length / 2; i++) {
    ;[arr[i], arr[arr.length - 1 - i]] = [arr[arr.length - 1 - i], arr[i]]
  }
  return arr
}

reverse([1, 2, 3]) // [3, 2, 1]

// or
const reverse = (arr) => {
  const newArr = []
  while (arr.length) {
    newArr.push(...arr.splice(arr.length - 1))
  }
  return newArr
}
```

## Наиболее часто встречающийся элемент массива

```js
const getMostFreqItem = (arr) =>
  Object.entries(
    arr.reduce((a, c) => {
      a[c] = a[c] ? a[c] + 1 : 1
      return a
    }, {})
  ).reduce((a, c) => (c[1] >= a[1] ? c : a), [null, 0])[0]

getMostFreqItem(['a', 'b', 'c', 'b', 'b', 'a']) // b
```

## Цикличный генератор

```js
const cycleGen = function* (arr) {
  let i = 0
  while (true) {
    yield arr[i % arr.length]
    i++
  }
}

const binaryCycle = cycleGenerator([0, 1])

binaryCycle.next() // { value: 0, done: false }
binaryCycle.next() // { value: 1, done: false }
binaryCycle.next() // { value: 0, done: false }
binaryCycle.next() // { value: 1, done: false }
```

## Наибольший и наименьший элемент массива без `Math.max()` и `Math.min()`

```js
// max
const max = (arr) => {
  let len = arr.length
  let max = -Infinity
  while (len--) {
    if (arr[len] > max) {
      max = arr[len]
    }
  }
  return max
}

max([3, 2, 4]) // 4

// or
const max = (arr, n = 1) => [...arr].sort((x, y) => y - x).slice(0, n)

max([1, 2, 3]) // [3]
max([1, 2, 3], 2) // [3, 2]

// min
const min = (arr) => {
  let len = arr.length
  let min = Infinity

  while (len--) {
    if (arr[len] < min) {
      min = arr[len]
    }
  }

  return min
}

min([3, 2, 4]) // 2

// or
const min = (arr, n = 1) => arr.sort((x, y) => x - y).slice(0, n)

min([1, 2, 3]) // [1]
min([1, 2, 3], 2) // [1, 2]
```

## Распаковка массива (рекурсивная реализация `flat()`)

```js
const flatBy = (arr, dep = 1) =>
  arr.reduce(
    (a, v) => a.concat(dep > 1 && Array.isArray(v) ? flatBy(v, dep - 1) : v),
    []
  )

flatBy([1, [2], 3, 4]) // [1, 2, 3, 4]
flatBy([1, [2, [3, [4, 5], 6], 7], 8], 2) // [1, 2, 3, [4, 5], 6, 7, 8]
```

## Проверка уникальности элементов с помощью условия - функции

```js
const uniqueBy = (arr, fn) => arr.length === new Set(arr.map(fn)).size

uniqueBy([1.2, 2.4, 2.9], Math.round) // true
uniqueBy([1.2, 2.3, 2.4], Math.round) // false
```

## Удаление всех ложных значений из объекта

```js
const compactObj = (v) => {
  const d = Array.isArray(v) ? v.filter(Boolean) : v
  return Object.keys(d).reduce(
    (a, k) => {
      const v = d[k]
      if (Boolean(v)) a[k] = typeof v === 'object' ? compactObject(v) : v
      return a
    },
    Array.isArray(v) ? [] : {}
  )
}

const obj = {
  a: null,
  b: false,
  c: true,
  d: 0,
  e: 1,
  f: '',
  g: 'a',
  h: [null, false, '', true, 1, 'a'],
  i: { j: 0, k: false, l: 'a' }
}

compactObj(obj)
// { c: true, e: 1, g: 'a', h: [ true, 1, 'a' ], i: { l: 'a' } }
```

## Упаковка и распаковка объекта

```js
const flatObj = (obj, prefix = '') =>
  Object.keys(obj).reduce((a, k) => {
    const pre = prefix.length ? `${prefix}.` : ''
    if (
      typeof obj[k] === 'object' &&
      obj[k] !== null &&
      Object.keys(obj[k]).length > 0
    )
      Object.assign(a, flatObj(obj[k], pre + k))
    else a[pre + k] = obj[k]
    return a
  }, {})

flatObj({ a: { b: { c: 1 } }, d: 2 }) // { a.b.c: 1, d: 2 }

const unflatObj = (obj) =>
  Object.keys(obj).reduce((acc, key) => {
    key
      .split('.')
      .reduce(
        (a, e, i, keys) =>
          a[e] ||
          (a[e] = isNaN(Number(keys[i + 1]))
            ? keys.length - 1 === i
              ? obj[key]
              : {}
            : []),
        acc
      )
    return acc
  }, {})

unflatObj({ 'a.b.c': 1, d: 2 }) // { a: { b: { c: 1 } }, d: 2 }
unflatObj({ 'a.b': 1, 'a.c': 2, d: 3 }) // { a: { b: 1, c: 2 }, d: 3 }
```

## Группировка свойств объекта по условию - функции

```js
// { значение: [ свойства ]  }
const invertKeyVal = (obj, fn) =>
  Object.keys(obj).reduce((acc, key) => {
    const val = fn ? fn(obj[key]) : obj[key]
    acc[val] = acc[val] || []
    acc[val].push(key)
    return acc
  }, {})

invertKeyVal({ a: 1, b: 2, c: 1 }) // { 1: [ 'a', 'c' ], 2: [ 'b' ] }
invertKeyVal({ a: 1, b: 2, c: 1 }, (v) => '#' + v)
// { #1: [ 'a', 'c' ], #2: [ 'b' ] }
```

## Хеширование объекта

```js
const hashObj = (obj, key) =>
  Array.prototype.reduce.call(
    obj,
    (acc, v, i) => ((acc[!key ? i : v[key]] = v), acc),
    {}
  )

const users = [
  { id: '1', name: 'John' },
  { id: '2', name: 'Jane' },
  { id: '3', name: 'Alice' },
  { id: '4', name: 'Bob' }
]
const managers = [{ manager: 1, employees: [2, 3, 4] }]

managers.forEach(
  (m) =>
    (m.employees = m.employees.map(function (id) {
      return this[id]
    }, hashObj(users, 'id')))
)

managers
/*
[
  {
    manager: 1,
    employees:
      [
        { id: 2, name: 'Jane' },
        { id: 3, name: 'Alice' },
        { id: 4, name: 'Bob' }
      ]
  }
]
*/
```

## Является ли второй объект подмножеством первого?

```js
const matches = (obj, source) =>
  Object.keys(source).every(
    (key) => obj.hasOwnProperty(key) && obj[key] === source[key]
  )

matches(
  {
    age: 25,
    hair: 'long',
    beard: true
  },
  {
    hair: 'long',
    beard: true
  }
) // true
matches(
  {
    hair: 'long',
    beard: true
  },
  {
    age: 25,
    hair: 'long',
    beard: true
  }
) // false
```

## Синхронное выполнение функций

```js
const juxt = (...fns) => (...args) => [...fns].map((fn) => [...args.map(fn)])

juxt(
  (x) => x + 1,
  (x) => x - 1,
  (x) => x * 10
)(1, 2, 3)
// [ [2, 3, 4], [0, 1, 2], [10, 20, 30]

juxt(
  (s) => s.length,
  (s) => s.split(' ').join('-')
)('JavaScript is cool')
// [ [21], ['JavaScript-is-cool'] ]
```