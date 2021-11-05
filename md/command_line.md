# `Command Line` Cheatsheet :metal:

[На главную](../README.md)

### Содержание

- [`bash`](#bash)
- [`ssh`](#ssh)
- [`curl`](#curl)
- [`git`](#git)
- [`npm`](#npm)
- [`yarn`](#yarn)
- [`package.json`](#packagejson)

## `bash`

```bash
# list - покажи список файлов и директорий
ls
# -a включая скрытые файлы
# -l подробную информацию о файлах
# -s размеры файлов
# -lh подробную информацию о файлах, включая их размеры в удобочитаемом формате
ls -lh


# change directory - измени (текущую) директорию
cd [dirname]
# .. поднимись на 1 уровень вверх
# ../.. на 2 уровня
# - вернись к предыдущей директории
# / перейди в корневую директорию
# ~ перейди в домашнюю директорию
# !$ перейди в директорию, созданную с помощью mkdir
mkdir test
cd !$


# make directory - создай директорию
mkdir [dirname]
mkdir test


# remove directory - удали директорию
rmdir [name]
rmdir test


# создай файл
touch [filename]
touch test.txt


# remove - удали файл
rm [filename]
# -r удали директорию
# -f принудительно
# * [dirname] удали все файлы в директории
rm test.txt
rm -rf test


# покажи содержимое файла
cat [filename]
cat test.txt
# создай файл посредством объединения
cat test1.txt test2.txt > test3.txt


# покажи полный путь к текущей (рабочей) директории
pwd


# copy - копируй файл
cp [source] [destination]
# -r директорию со всеми файлами
# -n если destination уже существует, не перезаписывай его
# -u source, только если он отличается от destination
# -f удали destination и создай новый файл
# -a архивируй файл


# move - перемести файл
mv [source] [destination]


# архивируй файл или директорию
zip [dirname]
# -r не удаляй директорию после архивации
# -m удали директорию после архивации
# -d удали файл, содержащийся в архиве
# -u обнови файл, содержащийся в архиве


# разархивируй файл или директорию
unzip [dirname]
# -x [filename] исключи файл из директории при деархивации


# найди строку в файле
grep [search] [filename]
grep 'test' test.txt
# -i выполняй поиск без учета регистра
# -c покажи количество вхождений строки
# -l покажи список файлов, содержащих строку
# -n покажи номер строки, на которой встречается строка для поиска


# search
find [dirname] [options] [search]
# dir:
  # / вся система
  # . рабочая директория
  # ~ домашняя директория
# options:
  # -name
  # -user
  # -size
  # -type -d поиск только в директориях
  # -type -f поиск только в файлах
find . -name 'test'


# покажи список последних команд (по умолчанию 50)
history [number]


# clear - очисти терминал
clear


# создай или распакую сжатый архив
tar [command] [name] [path]
# создай
tar cvzf test.tar.gz /path/to/dir
# распакуй
tar xvzf test.tar.gz
# x извлеки файлы
# c создай архив
# v мне нужны детали
# z распакуй архив
# f создай архив с указанным именем


# покажи объем используемой директорией памяти
du
# -sh в удобочитаемой форме
# -sh * для каждой директории и файла в отдельности


# покажи текущие сетевые соединения
netstat
```

## `ssh`

```bash
ssh [hostname_or_ip]
ssh 192.168.56.10
ssh test.server.com


# Аутентификация
ssh username@hostname_or_ip
# -p port


# Генерация ключей
ssh-keygen -t rsa


# Отправка публичного ключа на сервер
ssh-copy-id [hostname_or_ip]


# | pipe или туннель: позволяет соединять несколько команд в одну
# > означает создание файла (существующие файлы перезаписываются)
# >> означает добавление данных в файл (если файл отсутствует, он создается)
# < означает отправку содержимого файла в команду
```

## `curl`

```bash
# GET
curl https://jsonplaceholder.typicode.com/todos/1


# Только заголовки
curl -I https://jsonplaceholder.typicode.com


# POST
curl -d "some_key1=some_value1&some_key2=some_value2" https://jsonplaceholder.typicode.com/todos
# -d данные
# в формате JSON
# -H заголовок
curl -H 'Content-Type: application/json' -d '{ "someKey1": "someVal1", "someKey2": "someVal2" }' https://jsonplaceholder.typicode.com/todos
# Метод запроса
curl -X PUT ...


# Аутентификация
curl -u username:password ...


# Скачивание файла
# С сохранением оригинального названия
curl -O https://example.com/some_file.pdf
# Переименование
curl -o my_file https://example.com/some_file.pdf
# Продолжение загрузки
curl -C - -O https://example.com/some_file.pdf
# Сохранить как HTML
curl https://www.google.com > google.html


# Загрузка файла
# multipart/form-data
curl -F @field_name=@path/to/some_file.png
# FTP
curl -u username:password -T my_file.png ftp://ftp.example.com


# Отправка email
curl --mail-from from@mail.com --mail-rcpt to@mail.com smtp://example.com
```

## `git`

Символ `|` означает `ИЛИ`

__Проверка установки__

```js
git --version
```
__Справка__

```js
git help
git help [command]
git [command] --help | -h
```
__Минимальные настройки__

```js
// --local - настройки для текущего репозитория
// --global - настройки для текущего пользователя
// --system - настройки для всей системы, т.е. для всех пользователей
git config --global user.name "My Name"
git config --global user.email "myemail@example.com"
```

__Дополнительные настройки__

```js
// список глобальных настроек
git config --list | -l --global

// редактирование глобальных настроек
git config --global --edit | -e
```

__Инициализация репозитория__

```js
git init
```

__Очистка репозитория__

```js
// -d - включая директории, -x - включая игнорируемые файлы, -f - принудительная
git clean | -dxf
```

__Удаление файлов и директорий__

```js
git rm [file-name]
git rm -r [dir-name]

git rm --force | -f
```

__Перемещение файлов__

```js
// git add + git remove
git mv [old-file] [new-file]
```

__Просмотр состояния репозитория__

```js
git status
```

__Добавление изменений__

```js
git add [file-name]

git add --force | -f

// все изменения
git add . | --all | -A

// для добавления пустой директории
// создаем в ней пустой файл `.gitkeep`
```

__Добавление сообщения (коммита)__

```js
// редактирование коммита
git commit

// коммит для одного изменения, если не выполнялось git add . | -A
// если выполнялось, сообщение будет добавлено для всех изменений
git commit --message | -m "My Message"

// для всех изменений, если git add [file-name] выполнялось несколько раз
git commit --all | -a -m | -am "My Message"

// исправление коммита
git commit --amend "My Message" | --no-edit
```

__Просмотр коммита__

```js
// последний коммит
git show

// другой коммит
git show [hash] // 4 и более первых символа

// поиск изменений по сообщению или его части
git show :/[string]

// поиск коммита по тегу
git show [tag-name]
```

__Просмотр разницы между коммитами__

```js
git diff HEAD | @
// HEAD - как правило, текущая ветка; @ - синоним для HEAD

// staged
git diff --staged | --cached

git diff [hash1] [hash2]

// разница между ветками
git diff [branch1]...[branch2]

// просмотр разницы между коммитами при редактировании сообщения
git commit --verbose | -v

// кастомизация выводимого сообщения
git diff --word-diff | --color-words
```

__Просмотр истории изменений__

```js
git log

// n - количество изменений
git log -n
// --since, --after - после
// --until, --before - до

// разница
git log -p

// быстрое форматирование
git log --graph --oneline --stat

// кастомизация вывода
git log --pretty=format
// пример
git log --pretty=format:'%C(red)%h %C(green)%cd %C(reset)| %C(blue)%s%d %C(yellow)[%an]' --date=short | format-local:'%F %R'

// поиск изменений по слову, файлу, ветке; i - без учета регистра
git log --grep | -G [string] | [file] | [branch] & -i

// поиск по нескольким строкам
git log --grep [string1] --grep [string2] --all-match

// поиск в определенном блоке файла
git log -L '/<head>/','/<\/head>/':index.html

// поиск по автору
git log --author=[name]
```

__Отмена изменений__

```js
git reset
// --hard - включая рабочую директорию и индекс
// --soft - без рабочей директории и индекса
// --mixed - по умолчанию: без рабочей директории, но с индексом

git reset --hard [hash] | @~ // @~ - последний коммит в HEAD

// аналогично
git reset --hard HEAD

// не путать с переключением ветки
git checkout

git restore
```

__Работа с ветками__

```js
// список веток
git branch

// создание ветки
git branch [branch-name]

// переключение на ветку
git checkout [branch-name]

// branch + checkout
git checkout -b [branch-name]

// переименование ветки
git branch -m [old-branch] [new-branch]

// удаление ветки
git branch -d [branch-name]

// слияние веток
git merge [branch-name]
```

__Разрешение конфликтов при слиянии__

```js
// обычно, при возникновении конфликта, открывается редактор кода

// принять изменения из сливаемой ветки
git checkout --ours

// принять изменения из текущей ветки
git checkout --theirs

// отмена слияния
git reset --merge
git merge --abort

// получение дополнительной информации
git checkout --conflict=diff3 --merge [file-name]

// продолжить слияние
git merge --continue
```

__Удаленный репозиторий__

```js
// клонирование
git clone [url] & [dir]

// просмотр
git remote
git remote show
git remote add [shortname] [url]
git remote rename [old-name] [new-name]

// получение изменений
// git fetch + git merge
git pull

// отправка изменений
git push
```

__Теги__

```js
// просмотр
git tag

// легковесная метка
git tag [tag-name]
//пример
git tag v1-beta

// аннотированная метка
git tag -a v1 -m "Version 1"

// удаление
git tag -d [tag-name]
```

__Отладка__

```js
git bisect

git blame

git grep
```

__Сохранение незакоммиченных изменений__

```js
// сохранение
git stash

// извлечение
git stash pop
```

__Копирование коммита__

```js
git cherry-pick | -x [hash]

// если возник конфликт
// отмена
git cherry-pick --abort

// продолжить
git cherry-pick --continue

git cherry-pick --no-commit | -n

// --cherry = --cherry-mark --left-right --no-merges
git log --oneline --cherry [branch1] [branch2]
```

__Перебазирование__

```js
git rebase [branch]

// при возникновении конфликта
// отмена
git rebase --abort

// пропустить
git rebase --skip

// продолжить
git rebase --continue

// предпочтение коммитов слияния
git rebase --preserve-merges | -p

// интерактивное перебазирование
git rebase -i [branch]
```

__Автоматическое разрешение повторных конфликтов__

```js
// rerere - reuse recorder resolution
// rerere.enabled true | false
// rerere.autoUpdate true | false
// rerere-train.sh - скрипт для обучения rerere
git rerere forget [file-name]
```

__Обратные коммиты__

```js
git revert @ | [hash]

// отмена слияния
// git reset --hard @~ не сработает
git revert [hash] -m 1

// git merge [branch] не сработает
// отмена отмены
git revert [hash]

// повторное слияние с rebase
git rebase [branch1] [branch2] | --onto [branch1] [hash] [branch2]

git merge [branch]

git rebase [hash] --no-ff
```

__Пример сокращений для `.gitconfig`__

```js
[alias]
  aa = add .
  cb = checkout -b
  cm = commit -m
  lp = log --pretty=format:'%C(red)%h %C(green)%cd %C(reset)| %C(blue)%s%d %C(yellow)[%an]' --date=short
```

## `npm`

__Проверка установки__

```js
npm --version | -v
```

__Обновление__

```js
npm i -g npm@latest
```

__Список доступных команд__

```js
npm help
npm help [command]
```

__Инициализация проекта__

```js
npm init

// auto
npm init --yes | -y
```

__Установка зависимостей__

```js
npm install | i

// проверка конкретной зависимости
npm explore [package]

// проверка всех зависимостей
npm doctor

// очистка
npm ci
```

__Принудительная переустановка зависимостей__

```js
npm i --force | -f
```

__Установка только продакшн-пакетов__

```js
npm i --only=production | --only=prod
```

__Добавление зависимости__

```js
npm i [package]
npm i [package@version]

// пример
npm i express
```

__Добавление зависимости для разработки__

```js
npm i --save-dev | -D [package]

// пример
npm i -D nodemon
```

__Обновление зависимости__

```js
npm update | up [package-name]
```

__Удаление зависимости__

```js
// dependency
npm remove | rm | r [package]

// devDependency
npm r -D [package]
```

__Глобальная установка/обновление/удаление пакета__

```js
npm i/up/r -g [package]

// пример
npm i -g create-react-app
// использование
create-react-app [app-name]
```

__Определение устаревших пакетов__

```js
npm outdated
npm outdated [package]
```

__Список установленных зависимостей__

```js
npm list | ls

// top level
npm ls --depth=0 | --depth 0

// global + top level
npm ls -g --depth 0
```

__Информация о пакете__

```js
npm view | v [package]

// пример
npm v react
npm v react.description
```

__Запуск скрипта/выполнение команды__

```js
npm run [script]

// пример
// package.json: "scripts": { "dev": "nodemon server.js" }
npm run dev

npm start
npm stop
```

__Удаление дублирующихся пакетов__

```js
npm dedupe | ddp
```

__Удаление посторонних пакетов__

```js
npm prune
```

__Обнаружение уязвимостей (угроз безопасности)__

```js
npm audit
// json
npm audit --json
// plain text
npm audit --parseable
```

__Автоматическое исправление уязвимостей__

```js
npm audit fix
```

## `yarn`

__Установка__

```js
npm i -g yarn
```

Команда `yarn dlx` позволяет запускать исполняемые файлы без установки: `yarn dlx create-react-app my-app`. Для этого `yarn` необходимо обновить до второй версии: `yarn set version berry`.

__Проверка установки__

```js
yarn --version | -v
```

__Обновление__

```js
yarn set version latest
```

__Список доступных команд__

```js
yarn help
yarn help [command]
```

__Инициализация проекта__

```js
yarn init

// auto
yarn init --yes | -y

// "private": true
yarn init --private | -p

// auto + private
yarn init -yp
```

__Установка зависимостей__

```js
yarn
// или
yarn install

// тихая установка
yarn install --silent | -s

// проверка файлов перед установкой
yarn --check-files
```

__Принудительная переустановка зависимостей__

```js
yarn install --force
```

__Установка только продакшн-пакетов__

```js
yarn install --production | --prod
```

__Добавление зависимости__

```js
yarn add [package]
yarn add [package@version]

// пример
yarn add express

// тихая установка
yarn add --silent
// или
yarn add -s
```

__Добавление зависимости для разработки__

```js
yarn add --dev | -D [package]

// пример
yarn add -D nodemon
```

__Обновление зависимости__

```js
yarn upgrade [package]
```

__Удаление зависимости__

```js
yarn remove [package]
```

__Глобальная установка/обновление/удаление пакета__

```js
yarn global add/upgrade/remove [package]

// пример
yarn global add create-react-app
// использование
create-react-app [app-name]
```

__Список установленных зависимостей__

```js
yarn list

// top level
yarn list --depth=0 | --depth 0
```

__Информация о пакете__

```js
yarn info [package]
// или
yarn why [package]

// пример
yarn info react
yarn info react description
yarn why webpack
```

__Запуск скрипта/выполнение команды__

```js
yarn [script]
// или
yarn run [script]

// пример
// package.json: "scripts": { "dev": "nodemon server.js" }
yarn dev
```

## `package.json`

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "This is My App",
  "keywords": [
    "awesome",
    "best",
    "useful"
  ],
  "private": true,
  "main": "server.js",
  "license": "MIT",
  "homepage": "https://example.com",
  "repository": {
    "type": "git",
    "url": "https://github.com/user/repo.git"
  },
  "repository": "github:user/repo",
  "author": {
    "name": "My Name",
    "email": "myemail@example.com",
    "url": "https://example.com"
  },
  "author": "My Name <myemail@example.com> (https://example.com)",
  "contributers": [
    {
      "name": "His Name",
      "email": "hisemail@example.com",
      "url": "https://friend-website.com"
    }
  ],
  "contributors": "His Name <hisemail.com> (https://friend-website.com)",
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "dev": "nodemon server.js"
  }
}
```

- `name` - название проекта
- `version` - версия проекта (см. версионирование)
- `description` - описание проекта (зачем нужен пакет?)
- `keywords` - ключевые слова (облегчает поиск в реестре `npm`)
- `private` - установка значения в `true` предотвращает случайную публикацию пакета в реестре `npm`
- `main` - основная точка входа для запуска проекта
- `repository` - ссылка на репозиторий (один из вариантов)
- `author` - автор проекта (один из вариантов)
- `contributors` - участники проекта (люди, внесшие вклад в проект)
- `dependencies` - зависимости проекта (пакеты, без которых приложение не будет работать)
- `devDependencies` - зависимости для разработки (пакеты, без которых приложение будет работать)
- `scripts` - команды (выполняемые сценарии, задачи), предназначенные для автоматизации, например, команда `yarn dev` запустит `nodemon server.js`

[Полный список доступных полей](https://docs.npmjs.com/files/package.json)

Файлы "package-lock.json" и "yarn.lock" содержат более полную информацию об установленных пакетах, чем package.json, например, конкретные версии пакетов вместо диапазона допустимых версий.

__Версионирование__

Каждый пакет имеет версию, представленную тремя цифрами (например, `1.0.0`), где первая цифра - мажорная версия (`major`), вторая - минорная (`minor`), третья - патчевая (`patch`). Выпуск новой версии называется релизом.

Увеличение каждой из этих цифр согласно правилам семантического версионирования (`semver`) означает следующее:

- `major` - внесение несовместимых с предыдущей версией изменений
- `minor` - новая функциональность, совместимая с предыдущей версией
- `patch` - исправление ошибок, незначительные улучшения

Диапазоны версий или допустимые релизы определяются с помощью следующих операторов (компараторов):

- `*` - любая версия (аналогично пустой строке)
- `<1.0.0` - любая версия, которая меньше `1.0.0`
- `<=1.0.0` - любая версия, которая меньше или равна `1.0.0`
- `>1.0.0` - любая версия, которая больше `1.0.0`
- `>=1.0.0` - любая версия, которая больше или равна `1.0.0`
- `=1.0.0` - только версия `1.0.0` (оператор `=` можно опустить)
- `>=1.0.0 <2.0.0` - больше или равно `1.0.0` и меньше `2.0.0`
- `1.0.0-2.0.0` - набор версий включительно
- `^1.0.0` - минорные и патчевые релизы (`>=1.0.0 <2.0.0`)
- `~.1.0.0` - только патчевые релизы (`>=1.0.0 <1.1.0`)

[Подробные сведения о `semver`](https://github.com/npm/node-semver)