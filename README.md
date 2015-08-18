enb-bem-i18n
============

[![NPM version](http://img.shields.io/npm/v/enb-bem-i18n.svg?style=flat)](https://www.npmjs.org/package/enb-bem-i18n)
[![Build Status](http://img.shields.io/travis/enb-bem/enb-bem-i18n/master.svg?style=flat&label=tests)](https://travis-ci.org/enb-bem/enb-bem-i18n)
[![Build status](https://img.shields.io/appveyor/ci/blond/enb-bh.svg?style=flat&label=windows)](https://ci.appveyor.com/project/blond/enb-bh)
[![Coverage Status](https://img.shields.io/coveralls/enb-bem/enb-bem-i18n.svg?style=flat)](https://coveralls.io/r/enb-bem/enb-bem-i18n?branch=master)
[![devDependency Status](http://img.shields.io/david/enb-bem/enb-bem-i18n.svg?style=flat)](https://david-dm.org/enb-bem/enb-bem-i18n)

Пакет предоставляет набор ENB-технологий для сборки файлов, обеспечивающих мультиязыковую поддержку проектов, созданных по [БЭМ-методологии](https://ru.bem.info/method/). Под мультиязыковой поддержкой понимается интернационализация (далее по тектсу также i18n).

С помощью технологий пакета `enb-bem-i18n` осуществляется сборка модуля для интернационализации (`i18n`).

**Технологии пакета `enb-bem-i18n`:**

* [keysets](/api.ru.md#keysets) — служебная технология для сборки исходных файлов с переводами.
* [i18n](/api.ru.md#i18n) — технология для формирования бандлов с переводами для каждого языка, которые будут использоваться в дальнейшем.
* [i18n-keysets-xml](/api.ru.md#i18n-keysets-xml) — технология для локализации сервисов, использующих XSLT для шаблонизации.

Принципы работы технологий и их API описаны в документе [API технологий](/api.ru.md).

**Совместимость:** пакет поддерживает следующие библиотеки:

* [bem-bl](https://ru.bem.info/libs/bem-bl/dev/)
* [bem-core](https://ru.bem.info/libs/bem-core/)

>Особенности реализации для каждой библиотеки описаны в разделе [подключение библиотек](#Поддержка-библиотек).

## Установка

Установите пакет `enb-bem-i18n`:
```
npm install --save-dev enb-bem-i18n
```

**Требования:** зависимость от пакета `enb` версии `0.11.0` или выше.

## Обзор документа

<!-- TOC -->
- [Основные понятия](#Основные-понятия)
  - [Исходные данные — keysets](#Исходные-данные--keysets)
    - [Расположение в файловой системе](#Расположение-в-файловой-системе)
  - [Ядро `i18n`](#Ядро-i18n)
- [Работа с технологиями](#Работа-с-технологиями)
  - [Объединение данных](#Объединение-данных)
  - [Обработка данных](#Обработка-данных)
- [Использование](#Использование)
  - [В JavaScript](#В-javascript)
    - [Использование в Node.js](#Использование-в-nodejs)
    - [Использование в браузере](#Использование-в-браузере)
  - [В шаблонах](#В-шаблонах)
- [API `i18n`](#api-i18n)
  - [Инициализация](#Инициализация)
  - [Параметризация значений](#Параметризация-значений)
- [Отличия в работе](#Отличия-в-работе)
  - [Переключение между языками во время исполнения](#Переключение-между-языками-во-время-исполнения)
  - [Хранение общих keysets-файлов с переводами](#Хранение-общих-keysets-файлов-с-переводами)
  - [Расположение ядра `i18n`](#Расположение-ядра-i18n)

<!-- TOC END -->


## Основные понятия

### Исходные данные — keysets

`keysets` — исходные файлы с переводами для поддержки интернационализации. Перевод представляет собой набор ключей и их значений. Ключ определяет, какое из значений должно быть выбрано для указанного языка.

Пример для русского языка:

```js
{
    hello: 'Привет!'
}
```
Пример для английского языка:

```js
{
    hello: 'Hello!'
}
```

Набор данных `ключ: 'значение'` передается с указанием контекста (`scope`). Обычно контекстом служит имя блока.

Пример keysets-файла:

```js
module.exports = {
    scope: {
        key: 'val'
    }
};
```
Пример keysets-файла для русского языка:

```js
module.exports = {
    greeting: {
        hello: 'Привет!''
    }
};
```

#### Расположение в файловой системе

Переводы (keysets) хранятся в файлах `<lang>.js` (например, `en.js`).

Файлы `<lang>.js` для каждой БЭМ-сущности находятся в отдельной директории `<block-name>.i18n` наряду с другими файлами технологий.

```
block/
    block.css
    block.js
    block.i18n/
        ru.js        # Исходный файл с переводом для русского языка.
        en.js        # Исходный файл с переводом для английского языка.
```

Также есть возможность объединять одинаковые для всех языков переводы в общие файлы:

* В `bem-bl` — в файл `all.js`.
* В `bem-core` — в файл `<block-name>.i18n.js`.

Файлы `all.js` и `<block-name>.i18n.js` Может содержать [ядро `i18n`](#Ядро-i18n) для библиотеки `bem-bl` и `bem-core`, соответственно.

```
common.blocks/
    block1/
        block1.css
        block1.js
        block1.i18n.js       # Исходный файл с переводом, содержащий
                             # общие переводы для всех языков.
                             # Может содержать ядро `i18n` для библиотеки `
                             # bem-core`.
        block1.i18n/         # Директория для хранения файлов с переводами для разных языков.
            en.js
            ru.js
            all.js           # Исходный файл с переводом, содержащий
                             # общие переводы (в данном случае для
                             # русского и английского языков).
                             # Может содержать ядро `i18n` для библиотеки `bem-bl`.
```

### Ядро `i18n`

Ядро `i18n` — это библиотека для интернационализации. Ядро находится в keysets-файлах (`<block-name>.i18n.js` или `<block-name>.all.js`) в одной из базовых библиотек блоков.

Пакет `enb-bem-i18n` поддерживает разные реализации ядра интернационализации для библиотек `bem-bl` и `bem-core`.

* В `bem-bl` — ядро `BEM.I18N`.
* В `bem-core` — ядро `i18n`.

> Далее по тексту для названия ядра будет использоваться `i18n`.

>Подробнее о расположении keysets-файлов, содержащих ядро, в файловой системе читайте в разделе [Расположение в файловой системе](#Расположение-в-файловой-системе).

Ядро `i18n` в библиотеках `bem-core` и `bem-bl` хранится в keysets-файлах по-разному:

* В `bem-bl` (файл `all.js`):

  ```js
  {
      all: {
          '': { // пустая строка
              /* код ядра */
          }
      }
  }
  ```

* В `bem-core` (файл `i18n.js`):

  ```js
  {
      i18n: {
          i18n: {
              /* код ядра */
          }
      }
  }
  ```
Перед использованием ядро должно быть инициализировано данными из keysets-файлов.

**Важно!** Для получения ядра необходимо добавить `mustDeps`-зависимость блокам, которые используют i18n.

* Для bem-bl:

  ```js
  ({
      mustDeps: { block: 'i-bem', elem: 'i18n' }
  })
  ```

* Для bem-core:

  ```js
  ({
      mustDeps: { block: 'i18n' }
  })
```

>Подробно про API использования ядра `i18n` читайте в разделе [API `i18n`](#api-i18n).

## Работа с технологиями

Данные из keysets-файлов `<lang>.js` во время сборки проходят несколько этапов:

* [Объединение данных исходных файлов в один для указанного языка](#Объединение-данных)
* [Обработка данных из объединенного файла](#Обработка-данных)

### Объединение данных

Технология [keysets](api.ru.md#keysets) объединяет исходные файлы `<lang>.js` для каждого языка в общий файл (`?.keysets.<lang>.js`). Набор языков, для которых будут собраны `?.keysets.<lang>.js`-файлы, задается с помощью опции [lang](#/api.ru.md#lang) в конфигурационном файле (`.enb/make.js`).

`?.keysets.<lang>.js`-файл — это промежуточный результат сборки, который в дальнейшем используется технологией [i18n](api.ru.md#i18n).

Например, для блоков `greeting` и `login` результирующий `?.keysets.en.js`-файл будет собран следующим образом.

Исходный файл `en.js` блока `greeting`:

```js
module.exports = {
  greeting: {
      hello: 'Hello',
      unknown: 'stranger'
  }
};
```

Исходный файл `en.js` блока `login`:

```js
module.exports = {
  login: {
      login: 'Login',
      pass: 'Password'
  }
};
```

Результирующий `?.keysets.en.js`-файл:

```js
module.exports = {
  greeting: {
      hello: 'Hello',
      unknown: 'stranger'
  },
  login: {
      login: 'Login',
      pass: 'Password'
  }
};
```

>Примеры всех вариантов использования ядра рассмотрены в [тестах к технологии](https://github.com/enb-bem/enb-bem-i18n/blob/master/test/techs/i18n-merge-keysets.test.js).

### Обработка данных

Данные из объединенного файла `?.keysets.<lang>.js` обрабатываются технологией [i18n](/api.ru.md#i18n). Результатом работы является функция `i18n`, которая при вызове из [шаблонов](#В-шаблонах) или [клиентского JavaScript](#В-javascript) принимает ключ и отдает значение (строку) для конкретного языка.

>API взаимодействия с ядром `i18n` описан в разделе [API `i18n`](#api-i18n).
Результатом являются `lang.<lang>.js`-файлы, содержащие строки переводов, соответствующие запрошенным ключам.

## Использование

Функция `i18n` может использоваться:

* [в JavaScript](#В-javascript)
* [в шаблонах](#В-шаблонах)

### В JavaScript

Cпособы использования `i18n` в JavaScript зависят от наличия модульной системы и ее типа. Файлы могут подключаться как [в Node.js](#Использование-в-node.js), так и [в браузере](#Использование-в-браузере), независимо от используемой библиотеки (`bem-bl` или `bem-core`).

#### Использование в Node.js

Скомпилированный файл подключается как модуль в формате CommonJS.

```js
var i18n = require('bundle.lang.en.js'); // Путь до скомпилированного файла

i18n('scope', 'key'); // 'val'
```

#### Использование в браузере

В браузере применение скомпиллированных `?.lang.<lang>.js`-файлов зависит от наличия модульной системы:

* **В браузере без YModules как `BEM.I18N`**

  ```js
  BEM.I18N('scope', 'key'); // Ядро `i18n` предоставляется в глобальную видимость в переменную `BEM.I18N`.
  ```

* **В браузере с YModules как `i18n`-модуль**

  ```js
  modules.require('i18n', function (i18n) {
      i18n('scope', 'key'); // 'val'
  });
  ```

>**Важно!** В проект с модульной системой ядро библиотеки интернационализации подключаются одинаково, как модуль `i18n`, вне зависимости от используемой библиотеки `bem-core` или `bem-bl`.

### В шаблонах

Использование функции `i18n` в шаблонах обеспечивают следующие пакеты, которые осуществляют поддержку `i18n` для ENB:

* [enb-bemxjst-i18n](https://github.com/enb-bem/enb-bemxjst-i18n)
* [enb-xjst-i18n](https://github.com/enb-bem/enb-xjst-i18n)
* [enb-bh-i18n](https://github.com/enb-bem/enb-bh-i18n)

## API `i18n`

### Инициализация

* **В `bem-bl`**

  ```js
  var core = /* ... */,
      keyset = {
          hello: 'Привет!'
      },
      lang = 'ru';

  core.decl('greeting', keyset, lang);
  core.lang(lang);

  core('greeting', 'hello'); // Привет!
  ```

* **В `bem-core`**

  ```js
  var core = /* ... */,
      keysets = {
          greeting: {
              hello: "Привет!"
          }
      };

  var i18n = core().decl(keysets);

  i18n('greeting', 'hello'); // Привет!
  ```

### Параметризация значений

Функция принимает следующие параметры:

* **scope** — область видимости ключа. Обычно имя блока.
* **key** — ключ, соответствующий значению для указанного языка.
* **params** — входные данные для функции.

Результат выполнения: строка, содержащая перевод.

* **В `bem-bl`**

  Задавать значения ключей можно не только как строку. Также возможность параметризации значений реализована через XML. В строку передается XML, который обрабатывается с помощью специальной программы.

  ```js
  {
      'scope-1': {
          key: 'val'
      },
      'scope-2': {
          key: 'Hello <i18n:param>who</i18n:param>!'
      }
  }
  ```


* **В `bem-core`**

  Задавать значения ключей можно не только как строку. Параметризация значений реализуется через функцию.

  Пример:

  ```js
  {
      'scope-1': {
          key: 'val'
      },
      'scope-2': {
          key: function (params, i18n) {
              return i18n(params.scope, params.key);
          }
      }
  }
  ```

  ```js
  modules.require('i18n', function (i18n) {
      i18n('scope-2', 'key', { scope: 'scope-1', key: 'key' }); // Значение
  });
  ```

## Отличия в работе

### Переключение между языками во время исполнения

* В `bem-bl` реализована возможность переключения между языками в реальном времени.
* В `bem-core` такой возможности нет.

### Хранение общих keysets-файлов с переводами

* В `bem-bl` переводы, одинаковые для всех языков, хранятся в файле `<block-name>.i18n/all.js`.
* В `bem-core` — в файле `*.i18n.js`.

### Расположение ядра `i18n`

* В `bem-bl` — элемент `i18n` блока `i-bem`.
* В `bem-core` — блок `i18n`.

© 2014 YANDEX LLC. Код лицензирован [Mozilla Public License 2.0](LICENSE.txt).
