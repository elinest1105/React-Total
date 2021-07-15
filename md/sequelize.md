# Sequelize Guide :metal:

[На главную](../README.md)

<a href="https://sequelize.org/master/">`Sequelize`</a> - это <a href="https://ru.wikipedia.org/wiki/ORM">`ORM`</a> (Object-Relational Mapping - объектно-реляционное отображение или преобразование) для работы с такими <a href="https://ru.wikipedia.org/wiki/%D0%A1%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0_%D1%83%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F_%D0%B1%D0%B0%D0%B7%D0%B0%D0%BC%D0%B8_%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85">СУБД</a> (системами управления (реляционными) базами данных, Relational Database Management System, RDBMS), как `Postgres`, `MySQL`, `MariaDB`, `SQLite` и `MSSQL`. Это далеко не единственная `ORM` для работы с названными базами данных (далее - БД), но, на мой взгляд, одна из самых продвинутых и, что называется, "battle tested" (проверенных временем).

## Содержание

- [Начало работы](#начало-работы)
- [Модели](#модели)
- [Экземпляры](#экземпляры)
- [Основы выполнения запросов](#основы-выполнения-запросов)
- [Поисковые запросы](#поисковые-запросы)
- [Геттеры, сеттеры и виртуальные атрибуты](#геттеры-сеттеры-и-виртуальные-атрибуты)
- [Валидация и ограничения](#валидация-и-ограничения)
- [Необработанные запросы](#необработанные-запросы)
- [Ассоциации](#ассоциации)
- ["Параноик"](#параноик)
- [Нетерпеливая загрузка](#нетерпеливая-загрузка)
- [Создание экземпляров с ассоциациями](#создание-экземпляров-с-помощью-ассоциаций)
- [Продвинутые ассоциации M:N](#продвинутые-ассоциации-mn)
- [Область видимости ассоциаций](#область-видимости-ассоциаций)
- [Полиморфные ассоциации](#полиморфные-ассоциации)
- [Транзакции](#транзакции)
- [Хуки](#хуки)
- [Интерфейс запросов](#интерфейс-запросов)
- [Стратегии именования](#стратегии-именования)
- [Области видимости](#области-видимости)
- [Подзапросы](#подзапросы)
- [Ограничения и циклические ссылки](#ограничения-и-циклические-ссылки)
- [Индексы](#индексы)
- [Пул соединений](#пул-соединений)
- [Миграции](#миграции)

## Начало работы

**Установка**

```bash
yarn add sequelize
# или
npm i sequelize
```

**Подключение к БД**

```javascript
const { Sequelize } = require('sequelize')

// Вариант 1: передача `URI` для подключения
const sequelize = new Sequelize('sqlite::memory:') // для `sqlite`
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname') // для `postgres`

// Вариант 2: передача параметров по отдельности
const sequelize = new Sequelize({
  dialect: 'sqlite',
  storage: 'path/to/database.sqlite'
})

// Вариант 2: передача параметров по отдельности (для других диалектов)
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* 'mysql' | 'mariadb' | 'postgres' | 'mssql' */
})
```

**Проверка подключения**

```javascript
try {
  await sequelize.authenticate()
  console.log('Соединение с БД было успешно установлено')
} catch (e) {
  console.log('Невозможно выполнить подключение к БД: ', e)
}
```

По умолчанию после того, как установки соединения, оно остается открытым. Для его закрытия следует вызвать метод `sequelize.close()`.

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Модели

Модель - это абстракция, представляющая таблицу в БД.

Модель сообщает `Sequelize` несколько вещей о сущности (entity), которую она представляет: название таблицы, то, какие колонки она содержит (и их типы данных) и др.

У каждой модели есть название. Это название не обязательно должно совпадать с названием соответствующей таблицы. Обычно, модели именуются в единственном числе (например, `User`), а таблицы - во множественном (например, `Users`). `Sequelize` выполняет плюрализацию (перевод значения из единственного числа во множественное) автоматически.

Модели могут определяться двумя способами:

- путем вызова `sequelize.define(modelName, attributes, options)`
- путем расширения класса `Model` и вызова `init(attributes, options)`

После определения, модель доступна через `sequelize.model` + название модели.

В качестве примера создадим модель `User` с полями `firstName` и `lastName`.

**`sequelize.define`**

```javascript
const { Sequelize, DataTypes } = require('sequelize')
const sequelize = new Sequelize('sqlite::memory:')

const User = sequelize.define(
  'User',
  {
    // Здесь определяются атрибуты модели
    firstName: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    lastName: {
      type: DataTypes.STRING,
      // allowNull по умолчанию имеет значение true
    },
  },
  {
    // Здесь определяются другие настройки модели
  }
)

// `sequelize.define` возвращает модель
console.log(User === sequelize.models.User) // true
```

**Расширение `Model`**

```javascript
const { Sequelize, DataTypes, Model } = require('sequelize')
const sequelize = new Sequelize('sqlite::memory:')

class User extends Model {}

User.init(
  {
    // Здесь определяются атрибуты модели
    firstName: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    lastName: {
      type: DataTypes.STRING,
    },
  },
  {
    // Здесь определяются другие настройки модели
    sequelize, // Экземпляр подключения (обязательно)
    modelName: 'User', // Название модели (обязательно)
  }
)

console.log(User === sequelize.models.User) // true
```

`sequelize.define` под капотом использует `Model.init`.

В дальнейшем я буду использовать только _первый вариант_.

Автоматическую плюрализацию названия таблицы можно отключить с помощью настройки `freezeTableName`:

```javascript
sequelize.define(
  'User',
  {
    // ...
  },
  {
    freezeTableName: true,
  }
)
```

или глобально:

```javascript
const sequelize = new Sequelize('sqlite::memory:', {
  define: {
    freeTableName: true,
  },
})
```

В этом случае таблица будет называться `User`.

Название таблицы может определяться в явном виде:

```javascript
sequelize.define(
  'User',
  {
    // ...
  },
  {
    tableName: 'Employees',
  }
)
```

В этом случае таблица будет называться `Employees`.

Синхронизация модели с таблицей:

- `User.sync()` - создает таблицу при отсутствии (существующая таблица остается неизменной)
- `User.sync({ force: true })` - удаляет существующую таблицу и создает новую
- `User.sync({ alter: true })` - приводит таблицу в соответствие с моделью

Пример:

```javascript
// Возвращается промис
await User.sync({ force: true })
console.log('Таблица для модели `User` только что была создана заново!')
```

Синхронизация всех моделей:

```javascript
await sequelize.sync({ force: true })
console.log('Все модели были успешно синхронизированы.')
```

Удаление таблицы:

```javascript
await User.drop()
console.log('Таблица `User` была удалена.')
```

Удаление всех таблиц:

```javascript
await sequelize.drop()
console.log('Все таблицы были удалены.')
```

`Sequelize` принимает настройку `match` с регулярным выражением, позволяющую определять группу синхронизируемых таблиц:

```javascript
// Выполняем синхронизацию только тех моделей, названия которых заканчиваются на `_test`
await sequelize.sync({ force: true, match: /_test$/ })
```

_Обратите внимание_: вместо синхронизации в продакшне следует использовать миграции.

По умолчанию `Sequelize` автоматически добавляет в создаваемую модель поля `createAt` и `updatedAt` с типом `DataTypes.DATE`. Это можно изменить:

```javascript
sequelize.define(
  'User',
  {
    // ...
  },
  {
    timestamps: false,
  }
)
```

Названные поля можно отключать по отдельности и переименовывать:

```javascript
sequelize.define(
  'User',
  {
    // ...
  },
  {
    timestamps: true,
    // Отключаем `createdAt`
    createdAt: false,
    // Изменяем название `updatedAt`
    updatedAt: 'updateTimestamp',
  }
)
```

Если для колонки определяется только тип данных, синтаксис определения атрибута может быть сокращен следующим образом:

```javascript
// до
sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
  },
})

// после
sequelize.define('User', {
  name: DataTypes.STRING,
})
```

По умолчанию значением колонки является `NULL`. Это можно изменить с помощью настройки `defaultValue` (определив "дефолтное" значение):

```javascript
sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    defaultValue: 'John Smith',
  },
})
```

В качестве дефолтных могут использоваться специальные значения:

```javascript
sequelize.define('Foo', {
  bar: {
    type: DataTypes.DATE,
    // Текущие дата и время, определяемые в момент создания
    defaultValue: Sequelize.NOW,
  },
})
```

**Типы данных**

Каждая колонка должна иметь определенный тип данных.

```javascript
// Импорт встроенных типов данных
const { DataTypes } = require('sequelize')

// Строки
DataTypes.STRING // VARCHAR(255)
DataTypes.STRING(1234) // VARCHAR(1234)
DataTypes.STRING.BINARY // VARCHAR BINARY
DataTypes.TEXT // TEXT
DataTypes.TEXT('tiny') // TINYTEXT
DataTypes.CITEXT // CITEXT - только для `PostgreSQL` и `SQLite`

// Логические значения
DataTypes.BOOLEAN // BOOLEAN

// Числа
DataTypes.INTEGER // INTEGER
DataTypes.BIGINT // BIGINT
DataTypes.BIGINT(11) // BIGINT(11)

DataTypes.FLOAT // FLOAT
DataTypes.FLOAT(11) // FLOAT(11)
DataTypes.FLOAT(11, 10) // FLOAT(11, 10)

DataTypes.REAL // REAL - только для `PostgreSQL`
DataTypes.REAL(11) // REAL(11) - только для `PostgreSQL`
DataTypes.REAL(11, 12) // REAL(11,12) - только для `PostgreSQL`

DataTypes.DOUBLE // DOUBLE
DataTypes.DOUBLE(11) // DOUBLE(11)
DataTypes.DOUBLE(11, 10) // DOUBLE(11, 10)

DataTypes.DECIMAL // DECIMAL
DataTypes.DECIMAL(10, 2) // DECIMAL(10, 2)

// только для `MySQL`/`MariaDB`
DataTypes.INTEGER.UNSIGNED
DataTypes.INTEGER.ZEROFILL
DataTypes.INTEGER.UNSIGNED.ZEROFILL

// Даты
DataTypes.DATE // DATETIME для `mysql`/`sqlite`, TIMESTAMP с временной зоной для `postgres`
DataTypes.DATE(6) // DATETIME(6) для `mysql` 5.6.4+
DataTypes.DATEONLY // DATE без времени

// UUID
DataTypes.UUID
```

`UUID` может генерироваться автоматически:

```javascript
{
  type: DataTypes.UUID,
  defaultValue: Sequelize.UUIDV4
}
```

Другие типы данных:

```javascript
// Диапазоны (только для `postgres`)
DataTypes.RANGE(DataTypes.INTEGER) // int4range
DataTypes.RANGE(DataTypes.BIGINT) // int8range
DataTypes.RANGE(DataTypes.DATE) // tstzrange
DataTypes.RANGE(DataTypes.DATEONLY) // daterange
DataTypes.RANGE(DataTypes.DECIMAL) // numrange

// Буферы
DataTypes.BLOB // BLOB
DataTypes.BLOB('tiny') // TINYBLOB
DataTypes.BLOB('medium') // MEDIUMBLOB
DataTypes.BLOB('long') // LONGBLOB

// Перечисления - могут определяться по-другому (см. ниже)
DataTypes.ENUM('foo', 'bar')

// JSON (только для `sqlite`/`mysql`/`mariadb`/`postres`)
DataTypes.JSON

// JSONB (только для `postgres`)
DataTypes.JSONB

// другие
DataTypes.ARRAY(/* DataTypes.SOMETHING */) // массив DataTypes.SOMETHING. Только для `PostgreSQL`

DataTypes.CIDR // CIDR - только для `PostgreSQL`
DataTypes.INET // INET - только для `PostgreSQL`
DataTypes.MACADDR // MACADDR - только для `PostgreSQL`

DataTypes.GEOMETRY // Пространственная колонка. Только для `PostgreSQL` (с `PostGIS`) или `MySQL`
DataTypes.GEOMETRY('POINT') // Пространственная колонка с геометрическим типом. Только для `PostgreSQL` (с `PostGIS`) или `MySQL`
DataTypes.GEOMETRY('POINT', 4326) // Пространственная колонка с геометрическим типом и `SRID`. Только для `PostgreSQL` (с `PostGIS`) или `MySQL`
```

**Настройки колонки**

```javascript
const { DataTypes, Defferable } = require('sequelize')

sequelize.define('Foo', {
  // Поле `flag` логического типа по умолчанию будет иметь значение `true`
  flag: { type: DataTypes.BOOLEAN, allowNull: false, defaultValue: true },

  // Дефолтным значением поля `myDate` будет текущие дата и время
  myDate: { type: DataTypes.DATE, defaultValue: DataTypes.NOW },

  // Настройка `allowNull` со значением `false` запрещает запись в колонку нулевых значений (NULL)
  title: { type: DataTypes.STRING, allowNull: false },

  // Создание двух объектов с одинаковым набором значений, обычно, приводит к возникновению ошибки.
  // Значением настройки `unique` может быть строка или булевое значение. В данном случае формируется составной уникальный ключ
  uniqueOne: { type: DataTypes.STRING, unique: 'compositeIndex' },
  uniqueTwo: { type: DataTypes.INTEGER, unique: 'compositeIndex' },

  // `unique` используется для обозначения полей, которые должны содержать только уникальные значения
  someUnique: { type: DataTypes.STRING, unique: true },

  // Первичные или основные ключи будут подробно рассмотрены далее
  identifier: { type: DataTypes.STRING, primaryKey: true },

  // Настройка `autoIncrement` может использоваться для создания колонки с автоматически увеличивающимися целыми числами
  incrementMe: { type: DataTypes.INTEGER, autoIncrement: true },

  // Настройка `field` позволяет кастомизировать название колонки
  fieldWithUnderscores: { type: DataTypes.STRING, field: 'field_with_underscores' },

  // Внешние ключи также будут подробно рассмотрены далее
  bar_id: {
    type: DataTypes.INTEGER,

    references: {
      // ссылка на другую модель
      model: Bar,

      // название колонки модели-ссылки с первичным ключом
      key: 'id',

      // в случае с `postres`, можно определять задержку получения внешних ключей
      deferrable: Deferrable.INITIALLY_IMMEDIATE
      /*
        `Deferrable.INITIALLY_IMMEDIATE` - проверка внешних ключей выполняется незамедлительно
        `Deferrable.INITIALLY_DEFERRED` - проверка внешних ключей откладывается до конца транзакции
        `Deferrable.NOT` - без задержки: это не позволит динамически изменять правила в транзакции
      */

      // Комментарии можно добавлять только в `mysql`/`mariadb`/`postres` и `mssql`
      commentMe: {
        type: DataTypes.STRING,
        comment: 'Комментарий'
      }
    }
  }
}, {
  // Аналог атрибута `someUnique`
  indexes: [{
    unique: true,
    fields: ['someUnique']
  }]
})
```

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Экземпляры

Наш начальный код будет выглядеть следующим образом:

```javascript
const { Sequelize, DataTypes } = require('sequelize')
const sequelize = new Sequelize('sqlite::memory:')

// Создаем модель для пользователя со следующими атрибутами
const User = sequelize.define('User', {
  // имя
  name: DataTypes.STRING,
  // любимый цвет - по умолчанию зеленый
  favouriteColor: {
    type: DataTypes.STRING,
    defaultValue: 'green',
  },
  // возраст
  age: DataTypes.INTEGER,
  // деньги
  cash: DataTypes.INTEGER,
})

;(async () => {
  // Пересоздаем таблицу в БД
  await sequelize.sync({ force: true })
  // дальнейший код
})()
```

Создание экземпляра:

```javascript
// Создаем объект
const jane = User.build({ name: 'Jane' })
// и сохраняем его в БД
await jane.save()

// Сокращенный вариант
const jane = await User.create({ name: 'Jane' })
console.log(jane.toJSON())
console.log(JSON.stringify(jane, null, 2))
```

Обновление экземпляра:

```javascript
const john = await User.create({ name: 'John' })
// Вносим изменение
john.name = 'Bob'
// и обновляем соответствующую запись в БД
await john.save()
```

Удаление экземпляра:

```javascript
await john.destroy()
```

"Перезагрузка" экземпляра:

```javascript
const john = await User.create({ name: 'John' })
john.name = 'Bob'

// Перезагрузка экземпляра приводит к сбросу всех полей к дефолтным значениям
await john.reload()
console.log(john.name) // John
```

Сохранение отдельных полей:

```javascript
const john = await User.create({ name: 'John' })
john.name = 'Bob'
john.favouriteColor = 'blue'
// Сохраняем только изменение имени
await john.save({ fields: ['name'] })

await john.reload()
console.log(john.name) // Bob
// Изменение цвета не было зафиксировано
console.log(john.favouriteColor) // green
```

Автоматическое увеличение значения поля:

```javascript
const john = await User.create({ name: 'John', age: 98 })

const incrementResult = await john.increment('age', { by: 2 })
// При увеличении значение на 1, настройку `by` можно опустить - increment('age')

// Обновленный пользователь будет возвращен только в `postres`, в других БД он будет иметь значение `undefined`
```

Автоматическое увеличения значений нескольких полей:

```javascript
const john = await User.create({ name: 'John', age: 98, cash: 1000 })

await john.increment({
  age: 2,
  cash: 500,
})
```

Также имеется возможность автоматического уменьшения значений полей (`decrement()`).

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Основы выполнения запросов

Создание экземпляра:

```javascript
const john = await User.create({
  firstName: 'John',
  lastName: 'Smith',
})
```

Создание экземпляра с определенными полями:

```javascript
const user = await User.create(
  {
    username: 'John',
    isAdmin: true,
  },
  {
    fields: ['username'],
  }
)

console.log(user.username) // John
console.log(user.isAdmin) // false
```

Получение экземпляра:

```javascript
// Получение одного (первого) пользователя
const firstUser = await User.find()

// Получение всех пользователей
const allUsers = await User.findAll() // SELECT * FROM ...;
```

Выборка полей:

```javascript
// Получение полей `foo` и `bar`
Model.findAll({
  attributes: ['foo', 'bar'],
}) // SELECT foo, bar FROM ...;

// Изменение имени поля `bar` на `baz`
Model.findAll({
  attributes: ['foo', ['bar', 'baz'], 'qux'],
}) // SELECT foo, bar AS baz, qux FROM ...;

// Выполнение агрегации
// Синоним `n_hats` является обязательным
Model.findAll({
  attributes: [
    'foo',
    [sequelize.fn('COUNT', sequelize.col('hats')), 'n_hats'],
    'bar',
  ],
}) // SELECT foo, COUNT(hats) AS n_hats, bar FROM ...;
// instance.n_hats

// Сокращение - чтобы не перечислять все атрибуты при агрегации
Model.findAll({
  attributes: {
    include: [[sequelize.fn('COUNT', sequelize.col('hats')), 'n_hast']],
  },
})

// Исключение поля из выборки
Model.findAll({
  attributes: {
    exclude: ['baz'],
  },
})
```

Настройка `where` позволяет выполнять фильтрацию возвращаемых данных. Существует большое количество операторов, которые могут использоваться совместно с `where` через `Op` (см. ниже).

```javascript
// Выполняем поиск поста по идентификатору его автора
// предполагается `Op.eq`
Post.findAll({
  where: {
    authorId: 2,
  },
}) // SELECT * FROM post WHERE authorId = 2;

// Полный вариант
const { Op } = require('sequelize')
Post.findAll({
  where: {
    authorId: {
      [Op.eq]: 2,
    },
  },
})

// Фильтрация по нескольким полям
// предполагается `Op.and`
Post.findAll({
  where: {
    authorId: 2,
    status: 'active',
  },
}) // SELECT * FROM post WHERE authorId = 2 AND status = 'active';

// Полный вариант
Post.findAll({
  where: {
    [Op.and]: [{ authorId: 2 }, { status: 'active' }],
  },
})

// ИЛИ
Post.findAll({
  where: {
    [Op.or]: [{ authorId: 2 }, { authorId: 3 }],
  },
}) // SELECT * FROM post WHERE authorId = 12 OR authorId = 13;

// Одинаковые названия полей можно опускать
Post.destroy({
  where: {
    authorId: {
      [Op.or]: [2, 3],
    },
  },
}) // DELETE FROM post WHERE authorId = 2 OR authorId = 3;
```

**Операторы**

```javascript
const { Op } = require('sequelize')

Post.findAll({
  where: {
    [Op.and]: [{ a: 1, b: 2 }],   // (a = 1) AND (b = 2)
    [Op.or]: [{ a: 1, b: 2 }],    // (a = 1) OR (b = 2)
    someAttr: {
      // Основные
      [Op.eq]: 3,       // = 3
      [Op.ne]: 4,       // != 4
      [Op.is]: null,    // IS NULL
      [Op.not]: true,   // IS NOT TRUE
      [Op.or]: [5, 6],  // (someAttr = 5) OR (someAttr = 6)

      // Использование диалекта определенной БД (`postgres`, в данном случае)
      [Op.col]: 'user.org_id',    // = 'user'.'org_id'

      // Сравнение чисел
      [Op.gt]: 6,               // > 6
      [Op.gte]: 6,              // >= 6
      [Op.lt]: 7,               // < 7
      [Op.lte]: 7,              // <= 7
      [Op.between]: [8, 10],    // BETWEEN 8 AND 10
      [Op.notBetween]: [8, 10], // NOT BETWEEN 8 AND 10

      // Другие
      [Op.all]: sequelize.literal('SELECT 1'), // > ALL (SELECT 1)

      [Op.in]: [10, 12],    // IN [1, 2]
      [Op.notIn]: [10, 12]  // NOT IN [1, 2]

      [Op.like]: '%foo',      // LIKE '%foo'
      [Op.notLike]: '%foo',   // NOT LIKE '%foo'
      [Op.startsWith]: 'foo', // LIKE 'foo%'
      [Op.endsWith]: 'foo',   // LIKE '%foo'
      [Op.substring]: 'foo',  // LIKE '%foo%'
      [Op.iLike]: '%foo',     // ILIKE '%foo' (учет регистра, только для `postgres`)
      [Op.notILike]: '%foo',        // NOT ILIKE '%foo'
      [Op.regexp]: '^[b|a|r]',      // REGEXP/~ '^[b|a|r]' (только для `mysql`/`postgres`)
      [Op.notRegexp]: '^[b|a|r]',   // NOT REGEXP/!~ '^[b|a|r]' (только для `mysql`/`postgres`),
      [Op.iRegexp]: '^[b|a|r]',     // ~* '^[b|a|r]' (только для `postgres`)
      [Op.notIRegexp]: '^[b|a|r]',  // !~* '^[b|a|r]' (только для `postgres`)

      [Op.any]: [2, 3], // ANY ARRAY[2, 3]::INTEGER (только для `postgres`)

      [Op.like]: { [Op.any]: ['foo', 'bar'] } // LIKE ANY ARRAY['foo', 'bar'] (только для `postgres`)

      // и т.д.
    }
  }
})
```

Передача массива в `where` приводит к неявному применению оператора `IN`:

```javascript
Post.findAll({
  where: {
    id: [1, 2, 3], // id: { [Op.in]: [1, 2, 3] }
  },
}) // ... WHERE 'post'.'id' IN (1, 2, 3)
```

Операторы `Op.and`, `Op.or` и `Op.not` могут использоваться для создания сложных операций, связанных с логическими сравнениями:

```javascript
const { Op } = require('sequelize')

Foo.findAll({
  where: {
    rank: {
      [Op.or]: {
        [Op.lt]: 1000,
        [Op.eq]: null
      }
    }, // rank < 1000 OR rank IS NULL
    {
      createdAt: {
        [Op.lt]: new Date(),
        [Op.gt]: new Date(new Date() - 24 * 60 * 60 * 1000)
      }
    }, // createdAt < [timestamp] AND createdAt > [timestamp]
    {
      [Op.or]: [
        {
          title: {
            [Op.like]: 'Foo%'
          }
        },
        {
          description: {
            [Op.like]: '%foo%'
          }
        }
      ]
    } // title LIKE 'Foo%' OR description LIKE '%foo%'
  }
})

// НЕ
Project.findAll({
  where: {
    name: 'Some Project',
    [Op.not]: [
      { id: [1, 2, 3] },
      {
        description: {
          [Op.like]: 'Awe%'
        }
      }
    ]
  }
})
/*
  SELECT *
  FROM 'Projects'
  WHERE (
    'Projects'.'name' = 'Some Project'
    AND NOT (
      'Projects'.'id' IN (1, 2, 3)
      OR
      'Projects'.'description' LIKE 'Awe%'
    )
  )
*/
```

"Продвинутые" запросы:

```javascript
Post.findAll({
  where: sequelize.where(
    sequelize.fn('char_length', sequelize.col('content')),
    7
  ),
}) // WHERE char_length('content') = 7

Post.findAll({
  where: {
    [Op.or]: [
      sequelize.where(sequelize.fn('char_length', sequelize.col('content')), 7),
      {
        content: {
          [Op.like]: 'Hello%',
        },
      },
      {
        [Op.and]: [
          { status: 'draft' },
          sequelize.where(
            sequelize.fn('char_length', sequelize.col('content')),
            {
              [Op.gt]: 8,
            }
          ),
        ],
      },
    ],
  },
})

/*
  ...
  WHERE (
    char_length("content") = 7
    OR
    "post"."content" LIKE 'Hello%'
    OR (
      "post"."status" = 'draft'
      AND
      char_length("content") > 8
    )
  )
*/
```

Длинное получилось лирическое отступление. Двигаемся дальше.

Обновление экземпляра:

```javascript
// Изменяем имя пользователя с `userId = 2`
await User.update(
  {
    firstName: 'John',
  },
  {
    where: {
      userId: 2,
    },
  }
)
```

Удаление экземпляра:

```javascript
// Удаление пользователя с `id = 2`
await User.destroy({
  where: {
    userId: 2,
  },
})

// Удаление всех пользователей
await User.destroy({
  truncate: true,
})
```

Создание нескольких экземпляров одновременно:

```javascript
const users = await User.bulkCreate([{ name: 'John' }, { name: 'Jane' }])

// Настройка `validate` со значением `true` заставляет `Sequelize` выполнять валидацию каждого объекта, создаваемого с помощью `bulkCreate()`
// По умолчанию валидация таких объектов не проводится
const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    validate: {
      len: [2, 10],
    },
  },
})

await User.bulkCreate([{ name: 'John' }, { name: 'J' }], { validate: true }) // Ошибка!

// Настройка `fields` позволяет определять поля для сохранения
await User.bulkCreate([{ name: 'John' }, { name: 'Jane', age: 30 }], {
  fields: ['name'],
}) // Сохраняем только имена пользователей
```

**Сортировка и группировка**

Настройка `order` определяет порядок сортировки возвращаемых объектов:

```javascript
Submodel.findAll({
  order: [
    // Сортировка по заголовку (по убыванию)
    ['title', 'DESC'],

    // Сортировка по максимальному возврасту
    sequelize.fn('max', sequelize.col('age')),

    // Тоже самое, но по убыванию
    [sequelize.fn('max', sequelize.col('age')), 'DESC'],

    // Сортировка по `createdAt` из связанной модели
    [Model, 'createdAt', 'DESC'],

    // Сортировка по `createdAt` из двух связанных моделей
    [Model, AnotherModel, 'createdAt', 'DESC'],

    // и т.д.
  ],

  // Сортировка по максимальному возврасту (по убыванию)
  order: sequelize.literal('max(age) DESC'),

  // Сортировка по максимальному возрасту (по возрастанию - направление сортировки по умолчанию)
  order: sequelize.fn('max', sequelize.col('age')),

  // Сортировка по возрасту (по возрастанию)
  order: sequelize.col('age'),

  // Случайная сортировка
  order: sequelize.random(),
})

Model.findOne({
  order: [
    // возвращает `name`
    ['name'],
    // возвращает `'name' DESC`
    ['name', 'DESC'],
    // возвращает `max('age')`
    sequelize.fn('max', sequelize.col('age')),
    // возвращает `max('age') DESC`
    [sequelize.fn('max', sequelize.col('age')), 'DESC'],

    // и т.д.
  ],
})
```

Синтаксис группировки идентичен синтаксису сортировки, за исключением того, что при группировке не указывается направление. Кроме того, синтаксис группировки может быть сокращен до строки:

```javascript
Project.findAll({ group: 'name' }) // GROUP BY name
```

Настройки `limit` и `offset` позволяют ограничивать и/или пропускать определенное количество возвращаемых объектов:

```javascript
// Получаем 10 проектов
Project.findAll({ limit: 10 })

// Пропускаем 5 первых объектов
Project.findAll({ offset: 5 })

// Пропускаем 5 первых объектов и возвращаем 10
Project.findAll({ offset: 5, limit: 10 })
```

`Sequelize` предоставляет несколько полезных утилит:

```javascript
// Определяем число вхождений
console.log(
  `В настоящий момент в БД находится ${await Project.count()} проектов.`
)

const amount = await Project.count({
  where: {
    projectId: {
      [Op.gt]: 25,
    },
  },
})
console.log(
  `В настоящий момент в БД находится ${amount} проектов с идентификатором больше 25.`
)

// max, min, sum
// Предположим, что у нас имеется 3 пользователя 20, 30 и 40 лет
await User.max('age') // 40
await User.max('age', { where: { age: { [Op.lt]: 31 } } }) // 30
await User.min('age') // 20
await User.min('age', { where: { age: { [Op.gt]: 21 } } }) // 30
await User.sum('age') // 90
await User.sum('age', { where: { age: { [op.gt]: 21 } } }) // 70
```

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Поисковые запросы

Настройка `raw` со значением `true` отключает "оборачивание" ответа, возвращаемого `SELECT`, в экземпляр модели.

- `findAll()` - возвращает все экземпляры модели
- `findByPk()` - возвращает один экземпляр по первичному ключу

```javascript
const project = await Project.findByPk(123)
```

- `findOne()` - возвращает первый или один экземпляр модели (это зависит от того, указано ли условие для поиска)

```javascript
const project = await Project.findOne({ where: { projectId: 123 } })
```

- `findOrCreate()` - возвращает или создает и возвращает экземпляр, а также логическое значение - индикатор создания экземпляра. Настройка `defaults` используется для определения значений по умолчанию. При ее отсутствии, для заполнения полей используется значение, указанное в условии

```javascript
// Предположим, что у нас имеется пустая БД с моделью `User`, у которой имеются поля `username` и `job`
const [user, created] = await User.findOrCreate({
  where: { username: 'John' },
  defaults: {
    job: 'JavaScript Developer',
  },
})
```

- `findAndCountAll()` - комбинация `findAll()` и `count`. Может быть полезным при использовании настроек `limit` и `offset`, когда мы хотим знать точное число записей, совпадающих с запросом. Возвращает объект с двумя свойствами:
  - `count` - количество записей, совпадающих с запросом (целое число)
  - `rows` - массив объектов

```javascript
const { count, rows } = await Project.findAndCountAll({
  where: {
    title: {
      [Op.like]: 'foo%',
    },
  },
  offset: 10,
  limit: 5,
})
```

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Геттеры, сеттеры и виртуальные атрибуты

`Sequelize` позволяет определять геттеры и сеттеры для атрибутов моделей, а также _виртуальные атрибуты_ - атрибуты, которых не существует в таблице и которые заполняются или наполняются (имеется ввиду популяция) `Serquelize` автоматически. Последние могут использоваться, например, для упрощения кода.

Геттер - это функция `get()`, определенная для колонки:

```javascript
const User = sequelize.define('User', {
  username: {
    type: DataTypes.STRING,
    get() {
      const rawValue = this.getDataValue(username)
      return rawValue ? rawValue.toUpperCase() : null
    },
  },
})
```

Геттер вызывается автоматически при чтении поля.

_Обратите внимание_: для получения значения поля в геттере мы использовали метод `getDataValue()`. Если вместо этого указать `this.username`, то мы попадем в бесконечный цикл.

Сеттер - это функция `set()`, определенная для колонки. Она принимает значение для установки:

```javascript
const User = sequelize.define('user', {
  username: DataTypes.STRING,
  password: {
    type: DataTypes.STRING,
    set(value) {
      // Перед записью в БД пароли следует "хэшировать" с помощью криптографической функции
      this.setDataValue('password', hash(value))
    },
  },
})
```

Сеттер вызывается автоматически при создании экземпляра.

В сеттере можно использовать значения других полей:

```javascript
const User = sequelize.define('User', {
  username: DatTypes.STRING,
  password: {
    type: DataTypes.STRING,
    set(value) {
      // Используем значение поля `username`
      this.setDataValue('password', hash(this.username + value))
    },
  },
})
```

Геттеры и сеттеры можно использовать совместно. Допустим, что у нас имеется модель `Post` с полем `content` неограниченной длины, и в целях экономии памяти мы решили хранить в БД содержимое поста в сжатом виде. _Обратите внимание_: многие современные БД выполняют сжатие (компрессию) данных автоматически.

```javascript
const { gzipSync, gunzipSync } = require('zlib')

const Post = sequelize.define('post', {
  content: {
    type: DataTypes.TEXT,
    get() {
      const storedValue = this.getDataValue('content')
      const gzippedBuffer = Buffer.from(storedValue, 'base64')
      const unzippedBuffer = gunzipSync(gzippedBuffer)
      return unzippedBuffer.toString()
    },
    set(value) {
      const gzippedBuffer = gzipSync(value)
      this.setDataValue('content', gzippedBuffer.toString('base64'))
    },
  },
})
```

Представим, что у нас имеется модель `User` с полями `firstName` и `lastName`, и мы хотим получать полное имя пользователя. Для этого мы можем создать виртуальный атрибут со специальным типом `DataTypes.VIRTUAL`:

```javascript
const User = sequelize.define('user', {
  firstName: DataTypes.STRING,
  lastName: DataTypes.STRING,
  fullName: {
    type: DataTypes.VIRTUAL,
    get() {
      return `${this.firstName} ${this.lastName}`
    },
    set(value) {
      throw new Error('Нельзя этого делать!')
    },
  },
})
```

В таблице не будет колонки `fullName`, однако мы сможем получать значение этого поля, как если бы оно существовало на самом деле.

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Валидация и ограничения

Наша моделька будет выглядеть так:

```javascript
const { Sequelize, Op, DataTypes } = require('sequelize')
const sequelize = new Sequelize('sqlite::memory:')

const User = sequelize.define('user', {
  username: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
  hashedPassword: {
    type: DataTypes.STRING(64),
    is: /^[0-9a-f]{64}$/i,
  },
})
```

Отличие между выполнением валидации и применением или наложением органичение на значение поля состоит в следующем:

- валидация выполняется на уровне `Sequelize`; для ее выполнения можно использовать любую функцию, как встроенную, так и кастомную; при провале валидации, SQL-запрос в БД не отправляется;
- ограничение определяется на уровне `SQL`; примером ограничения является настройка `unique`; при провале ограничения, запрос в БД все равно отправляется

В приведенном примере мы ограничили уникальность имени пользователя с помощью настройки `unique`. При попытке записать имя пользователя, которое уже существует в БД, возникнет ошибка `SequelizeUniqueConstraintError`.

По умолчанию колонки таблицы могут быть пустыми (нулевыми). Настройка `allowNull` со значением `false` позволяет это запретить. _Обратите внимание_: без установки данной настройки хотя бы для одного поля, можно будет выполнить такой запрос: `User.create({})`.

Валидаторы позволяют проводить проверку в отношении каждого атрибута модели. Валидация автоматически выполняется при запуске методов `create()`, `update()` и `save()`. Ее также можно запустить вручную с помощью `validate()`.

Как было отмечено ранее, мы можем определять собственные валидаторы или использовать встроенные (предоставляемые библиотекой <a href="https://github.com/validatorjs/validator.js">`validator.js`</a>).

```javascript
sequelize.define('foo', {
  bar: {
    type: DataTypes.STRING,
    validate: {
      is: /^[a-z]+$/i, // определение совпадения с регулярным выражением
      not: /^[a-z]+$/i, // определение отсутствия совпадения с регуляркой
      isEmail: true,
      isUrl: true,
      isIP: true,
      isIPv4: true,
      isIPv6: true,
      isAlpha: true,
      isAlphanumeric: true,
      isNumeric: true,
      isInt: true,
      isFloat: true,
      isDecimal: true,
      isLowercase: true,
      isUppercase: true,
      notNull: true,
      isNull: true,
      notEmpty: true,
      equals: 'определенное значение',
      contains: 'foo', // определение наличия подстроки
      notContains: 'bar', // определение отсутствия подстроки
      notIn: [['foo', 'bar']], // определение того, что значение НЕ является одним из указанных
      isIn: [['foo', 'bar']], // определение того, что значение является одним из указанных
      len: [2, 10], // длина строки должна составлять от 2 до 10 символов
      isUUID: true,
      isDate: true,
      isAfter: '2021-06-12',
      isBefore: '2021-06-15',
      max: 65,
      min: 18,
      isCreditCard: true,

      // Примеры кастомных валидаторов
      isEven(value) {
        if (parseInt(value) % 2 !== 0) {
          throw new Error('Разрешены только четные числа!')
        }
      },
      isGreaterThanOtherField(value) {
        if (parseInt(value) < parseInt(this.otherField)) {
          throw new Error(
            `Значение данного поля должно быть больше значения ${otherField}!`
          )
        }
      },
    },
  },
})
```

Для кастомизации сообщения об ошибке можно использовать объект со свойством `msg`:

```javascript
isInt: {
  msg: 'Значение должно быть целым числом!'
}
```

В этом случае для указания аргументов используется свойство `args`:

```javascript
isIn: {
  args: [['ru', 'en']],
  msg: 'Язык должен быть русским или английским!'
}
```

Для поля, которое может иметь значение `null`, встроенные валидаторы пропускаются. Это означает, что мы, например, можем определить поле, которое либо должно содержать строку длиной 5-10 символов, либо должно быть пустым:

```javascript
const User = sequelize.define('user', {
  username: {
    type: DataTypes.STRING,
    allowNull: true,
    validate: {
      len: [5, 10],
    },
  },
})
```

_Обратите внимание_, что для нулевых полей кастомные валидаторы выполняются:

```javascript
const User = sequelize.define('user', {
  age: DataTypes.INTEGER,
  name: {
    type: DataTypes.STRING,
    allowNull: true,
    validate: {
      customValidator(value) {
        if (value === null && this.age < 18) {
          throw new Error('Нулевые значения разрешены только совершеннолетним!')
        }
      },
    },
  },
})
```

Мы можем выполнять валидацию не только отдельных полей, но и модели в целом. В следующем примере мы проверяем наличие или отсутствии как поля `latitude`, так и поля `longitude` (либо должны быть указаны оба поля, либо не должно быть указано ни одного):

```javascript
const Place = sequelize.define(
  'place',
  {
    name: DataTypes.STRING,
    address: DataTypes.STRING,
    latitude: {
      type: DataTypes.INTEGER,
      validate: {
        min: -90,
        max: 90,
      },
    },
    longitude: {
      type: DataTypes.INTEGER,
      validate: {
        min: -180,
        max: 180,
      },
    },
  },
  {
    validate: {
      bothCoordsOrNone() {
        if (!this.latitude !== !this.longitude) {
          throw new Error(
            'Либо укажите и долготу, и широту, либо ничего не указывайте!'
          )
        }
      },
    },
  }
)
```

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>

## Необработанные запросы

`sequelize.query()` позволяет выполнять необработанные `SQL-запросы` (raw queries). По умолчанию данная функция возвращает массив с результатами и объект с метаданными, при этом, содержание последнего зависит от используемого диалекта.

```javascript
const [results, metadata] = await sequelize.query(
  "UPDATE users SET username = 'John' WHERE userId = 123"
)
```

Если нам не нужны метаданные, для правильного форматирования результата можно воспользоваться специальными типами запроса (query types):

```javascript
const { QueryTypes } = require('sequelize')

const users = await sequelize.query('SELECT * FROM users', {
  // тип запроса - выборка
  type: QueryTypes.SELECT,
})
```

Для привязки результатов необработанного запроса к модели используются настройки `model` и, опционально, `mapToModel`:

```javascript
const projects = await sequelize.query('SELECT * FROM projects', {
  model: Project,
  mapToModel: true,
})
```

Пример использования других настроек:

```javascript
sequelize.query('SELECT 1', {
  // "логгирование" - функция или `false`
  logging: console.log,

  // если `true`, возвращается только первый результат
  plain: false,

  // если `true`, для выполнения запроса не нужна модель
  raw: false,

  // тип выполняемого запроса
  type: QueryTypes.SELECT,
})
```

Если название атрибута в таблице содержит точки, то результирующий объект может быть преобразован во вложенные объекты с помощью настройки `nest`.

Без `nest: true`:

```javascript
const records = await sequelize.query('SELECT 1 AS `foo.bar.baz`', {
  type: QueryTypes.SELECT,
})
console.log(JSON.stringify(records[0], null, 2))
// { 'foo.bar.baz': 1 }
```

С `nest: true`:

```javascript
const records = await sequelize.query('SELECT 1 AS `foo.bar.baz`', {
  type: QueryTypes.SELECT,
  nest: true,
})
console.log(JSON.stringify(records[0], null, 2))
/*
{
  'foo': {
    'bar': {
      'baz': 1
    }
  }
}
*/
```

Замены при выполнении запроса могут производиться двумя способами:

- с помощью именованных параметров (начинающихся с `:`)
- с помощью неименованных параметров (представленных `?`)

Заменители (placeholders) передаются в настройку `replacements` в виде массива (для неименованных параметров) или в виде объекта (для именованных параметров):

- если передан массив, `?` заменяется элементами массива в порядке их следования
- если передан объект, `:key` заменяются ключами объекта. При отсутствии в объекте ключей для заменяемых значений, а также в случае, когда ключей в объекте больше, чем заменяемых значений, выбрасывается исключение

```javascript
sequelize.query('SELECT * FROM projects WHERE status = ?', {
  replacements: ['active'],
  type: QueryTypes.SELECT,
})

sequelize.query('SELECT * FROM projects WHERE status = :status', {
  replacements: { status: 'active' },
  type: QueryTypes.SELECT,
})
```

Продвинутые примеры замены:

```javascript
// Замена производится при совпадении с любым значением из массива
sequelize.query('SELECT * FROM projects WHERE status IN(:status)', {
  replacements: { status: ['active', 'inactive'] },
  type: QueryTypes.SELECT,
})

// Замена выполняется для всех пользователей, имена которых начинаются с `J`
sequelize.query('SELECT * FROM users WHERE name LIKE :search_name', {
  replacements: { search_name: 'J%' },
  type: QueryTypes.SELECT,
})
```

Кроме замены, можно выполнять привязку (bind) параметров. Привязка похожа на замену, но заменители обезвреживаются (escaped) и вставляются в запрос, отправляемый в БД, а связанные параметры отправляются в БД по отдельности. Связанные параметры обозначаются с помощью `$число` или `$строка`:

- если передан массив, `$1` будет указывать на его первый элемент (`bind[0]`)
- если передан объект, `$key` будет указывать на `object['key']`. Каждый ключ объекта должен начинаться с буквы. `$1` является невалидным ключом, даже если существует `object['1']`
- в обоих случаях для сохранения знака `$` может использоваться `$$`

Связанные параметры не могут быть ключевыми словами `SQL`, названиями таблиц или колонок. Они игнорируются внутри текста, заключенного в кавычки. Кроме того, в `postgres` может потребоваться указывать тип связываемого параметра в случае, когда он не может быть выведен на основании контекста - `$1::varchar`.

```javascript
sequelize.query(
  'SELECT *, "текст с литеральным $$1 и литеральным $$status" AS t FROM projects WHERE status = $1',
  {
    bind: ['active'],
    type: QueryTypes.SELECT,
  }
)

sequelize.query(
  'SELECT *, "текст с литеральным $$1 и литеральным $$status" AS t FROM projects WHERE status = $status',
  {
    bind: { status: 'active' },
    type: QueryTypes.SELECT,
  }
)
```

<div align="right">
  <b><a href="#">↑ Наверх</a></b>
</div>


