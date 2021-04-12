# `Axios` Cheatsheet :metal:

[На главную](../README.md)

> #### `Axios` - это основанный на промисах HTTP-клиент для браузера и node.js.

## Оглавление

- [Возможности](#capabilities)
- [Установка](#install)
- [Примеры отправки GET и POST-запросов](#get)
- [Пример отправки нескольких запросов](#multiple)
- [Настройки запроса](#config)
- [Схема ответа](#schema)
- [Настройки по умолчанию](#defaults)
- [Перехватчики](#interceptors)
- [Обработка ошибок](#errors)
- [Отмена запроса](#cancel)

## Возможности <a name="capabilities"></a>

- Отправка XMLHttpRequest из браузера
- Отправка http-запросов из node.js
- Поддержка Promise API
- Перехват запроса и ответа
- Преобразование данных запроса и ответа
- Отмена запроса
- Автоматическое преобразование данных в формате JSON
- Автоматическая защита от XSRF

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Установка <a name="install"></a>

```bash
yarn add axios
# или
npm i axios
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Примеры отправки GET и POST-запросов <a name="get"></a>

```js
// GET-запрос
const getUserById = async (userId) => {
  try {
    const response = await axios.get(`/users?id=${userId}`)
    return response.data
  } catch (err) {
    console.error(err.toJSON())
  }
}
getUserById('1')

// POST-запрос
const addNewUser = async (newUser) => {
  try {
    const response = await axios.post('/users', newUser)
    return response.data
  } catch (err) {
    console.error(err.toJSON())
  }
}
addNewUser({ firstName: 'John', lastName: 'Smith' })
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Пример отправки нескольких запросов <a name="multiple"></a>

```js
// Первый запрос
const getUserAccount = () => axios.get(`/user/123`)
// Второй запрос
const getUserPermissions = () => axios.get('/user/123/permissions')
// Отправка обоих запросов
const getUserInfo = async () => {
  const [account, permissions] = await Promise.all([getUserAccount(), getUserPermissions()])

  return {
    account,
    permissions
  }
}
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Настройки запроса <a name="config"></a>

```js
{
  url: '/users',
  method: 'get', // default
  baseURL: 'http://example.com',
  // Преобразование запроса
  transfromRequest: [(data, headers) => {
    return data
  }],
  // Преобразование ответа
  transformResponse: [(data) => {
    return data
  }],
  headers: {
    'Authorization': 'Bearer [token]'
  },
  data: {
    firstName: 'John'
  },
  // Параметры строки запроса
  params: {
    id: '123'
  },
  withCredentials: false, // default
  responseType: 'json', // default
  responseEncoding: 'utf8', // default
  // Прогресс загрузки файлов
  onUploadProgress: (e) => {},
  // Прогресс скачивания файлов
  onDownloadProgress: (e) => {},
  // Максимальный размер ответа в байтах
  maxContentLength: 2048,
  // Максимальный размер запроса в байтах
  maxBodyLength: 2048,
  proxy: {
    protocol: 'https',
    host: '127.0.0.1',
    port: 5000,
    auth: {
      username: 'John',
      password: 'secret'
    }
  },
  // Токен для отмены запроса
  cancelToken: new CancelToken((cancel) => {})
}
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Схема ответа <a name="schema"></a>

```js
{
  data: {},
  status: 200,
  statusText: 'OK',
  headers: {},
  config: {},
  request: {}
}
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Настройки по умолчанию <a name="defaults"></a>

```js
axios.defaults.baseURL = 'http://example.com'
// Дефолтные настройки общих заголовков
axios.defaults.headers.common['Authorization'] = TOKEN
// Дефолтные настройки для POST-запроса
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Перехватчики <a name="interceptors"></a>

Мы можем перехватывать запросы или ответы перед их обработкой в `then` или `catch`.

```js
// Перехватчик запроса
axios.interceptors.request.use((config) => {
  return config
}, (error) => {
  return Promise.reject(error)
})

// Перехватчик ответа
axios.interceptors.response.use((response) => {
  return response
}, (error) => {
  return Promise.reject(error)
})
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Обработка ошибок <a name="errors"></a>

```js
const getUserById = (userId) => {
  try {
    const { data } = await axios.get(`/users?id=${userId}`)
    return data
  } catch (error) {
    if (error.response) {
      // Статус ответа выходит за пределы 2xx
      const { data, status, headers } = error.response
      console.error(data)
    } else if (error.request) {
      // Отсутствует тело ответа
      console.error(error.request)
    } else {
      // Ошибка, связанная с неправильной настройкой запроса
      console.error(error.message)
    }
    // Другая ошибка
    console.error(error.config)
    // Подробная информация об ошибке
    console.error(error.toJSON())
  }
}
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

## Отмена запроса <a name="cancel"></a>

```js
const { CancelToken } = axios
const source = CancelToken.source()

const getUserById = (userId) => {
  try {
    const { data } = await axios.get(`/users?id=${userId}`)
    return data
  } catch (thrown) {
    if (axios.isCancel(thrown)) {
      console.error(thrown.message)
    } else {
      // Обработка ошибки
    }
  }
}

// Отмена запроса (параметр `message` является опциональным)
source.cancel('Выполнение операции отменено')
```

<div align="right">
  <b><a href="#">↥ Наверх</a></b>
</div>

Пожалуй, это все, что нам нужно знать об `axios`.