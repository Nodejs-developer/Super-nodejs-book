
---

# Работа с базами данных. Часть II {#работа-с-базами-данных-часть-ii}

## Цель {#цель}

Освоить основы работы с построителями запросов и ORM-оболочками на примере Knex.js и Bookshelf.js.

## Предварительные требования {#предварительные-требования}

Элементарные занния о реляционных и документных базах данных. Пройденный урок [Работа с базами данных. Часть I](/rabota-s-bazami-dannih-chast-i.md).

## Построители запросов {#построители-запросов}

Выборка данных и манипуляция данными в реляционных СУБД осуществляется с помощью специального языка SQL. При традиционном подходе команды этого языка передаются в СУБД в виде строки. Этот подход имеет две проблемы:

* От разработчика системы требуется глубокое знание SQL
* Различные СУБД используют разные диалекты SQL, что затрудняет абстрагирование системы от СУБД

Для преодоления подобных проблем разработаны специальные модули - построители запросов. Их цель - формирование команд SQL посредством привычных для конкретного языка \(в нашем случае javascript\) конструкций.

## Knex.js {#knexjs}

Одной из библиотек, выполняющих функции построителя запросов, является Knex.js. Он позволяет строить SQL запросы с использованием функций javascript и точечной нотации. Пример:

```js
knex.select('title', 'author', 'year').from('books');
```

Такой вызов формирует следующий SQL запрос:

```js
select `title`, `author`, `year` from `books`
```

#### Установка {#установка}

Knex устанавливается как модуль Node.js:

```js
$ npm install knex --save
```

Knex нативно работает сразу с несколькими СУБД:

* PostgreSQL
* SQLite
* MySQL
* MariaSQL
* Oracle
* MSSQL

Для работы с соответствующей СУБД необходимо установить ее драйвер \(добавить флаг --save при необходимости\):

```js
$ npm install pg
$ npm install sqlite3
$ npm install mysql
$ npm install mysql2
$ npm install mariasql
$ npm install strong-oracle
$ npm install oracle
$ npm install mssql
```

#### Инициализация {#инициализация}

Knex инициализируется вызовом функции, которая принимает объект конфигурации в качестве аргумента. Обязательным свойством этого объекта являестя `client`:

```js
var knex = require('knex')({
  client: 'pg',
  version: '7.2',
  connection: {
    host : '127.0.0.1',
    user : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test'
  }
});
```

Свойство connection на прямую передается в драйвер базы данных для соединения. Установленные соединения могут сохраняться в пуле для повторного использования, для настройки пула используется параметр `pool`.

Кроме этого, конфигурационный объект Knex может содеражть другие параметры, специфические для определенных СУБД. Более подробно о конфигурации Knex см. [документацию](http://knexjs.org/#Installation-client).

#### Построение запросов {#построение-запросов}

Сердцем библиотеки Knex является построитель запросов -- интерфейс, служащий для построения и выполнения стандартных SQL запросов, таких как `select, insert, update, delete`.

Идентификаторы - такие как имя таблицы или имя колонки - могут передавться в методы Knex. В основном требуется просто имя таблицы и имя колонки, но во многих случаях нужно также передавть псевдоним \(alias\), под которым этот идентификатор будет использоваться вдальнейшем в запросе. Для определения псевдонима есть два способа - передача строки типа `'table1 as t1'` или объекта `{ t1: 'table1' }`.

> Имя схемы базы данных не должно передаваться в идентификаторе, для этого существует специальная функция `.withSchema('schemaName')`

Построитель запускается либо вызовом функции knex с указанием таблицы, по отношению к которой строится запрос, либо вызовом одного из методов knex:

```js
knex('users').where({
  first_name: 'Test',
  last_name:  'User'
}).select('id');

knex.select('title', 'author', 'year').from('books');
```

Knex содержит множество методов, совокупно позволяющих построить запрос практически любой сложности, приведем несколько примеров.

Вложенный запрос, агрегация среднего значения и суммы, группировка \(методы `as, avg, sum, groupBy`\):

```js
knex.avg('sum_column1').from(function() {
  this.sum('column1 as sum_column1').from('t1').groupBy('column1').as('t1')
}).as('ignored_alias')

// select avg(`sum_column1`) from (select sum(`column1`) as `sum_column1` from `t1` group by `column1`) as `t1`
```

Оператоы WITH, FROM, WHERE \(методы `with, from, where`\)

```js
knex.with('with_alias', (qb) => {
  qb.select('*').from('books').where('author', 'Test')
}).select('*').from('with_alias')

// with `with_alias` as (select * from `books` where `author` = 'Test') select * from `with_alias`
```

Обновление записи по условию \(методы `update, where`\)

```js
knex('books')
.where('published_date', '<', 2000)
.update({
  status: 'archived',
  thisKeyIsSkipped: undefined
})

// update `books` set `status` = 'archived' where `published_date` < 2000
```

Knex содержит множество методов различного назначения, их описание можно найти в [документации](http://knexjs.org/#Builder).

#### Описание схемы {#описание-схемы}

Помимо операторов CRUD, Knex подддерживает так же построения запросов, описывающих схему данных, таких как создание и удаление таблиц, модификация колонок, создание индексов и т. д. Ниже приведен пример создания двух таблиц с взаимосвязью:

```js
// create table 'users' with a primary key using 'increments()'
knex.schema.createTable('users', function (table) {
  table.increments('userId');
  table.string('name');
});

// reference the 'users' primary key in new table 'posts'
knex.schema.createTable('posts', function (table) {
  table.integer('author').unsigned().notNullable();
  table.string('title', 30);
  table.string('content');

  table.foreign('author').references('userId').inTable('users');
});
```

#### Raw {#raw}

Несмотря на то, что методы Knex могут конструировать практически любой запрос, для особо сложных случаев предусмотрена поддержка включения чистого неизмененного SQL синтаксиса в построитель. Для этого используется спциальная функция knex.raw, принимающая в качестве параметра строку с SQL синтаксисом. В приведенном ниже примере в построитель включаются готовые SQL выражежния:

```js
knex('users')
.select(knex.raw('count(*) as user_count, status'))
.where(knex.raw(1))
.orWhere(knex.raw('status <> ?', [1]))
.groupBy('status')

// select count(*) as user_count, status from `users` where 1 or status <> 1 group by `status`
```

#### Работа с результатами запроса {#работа-с-результатами-запроса}

Knex предусматривает несколько вариантов работы с результатами запроса. Следующие методы доступны для построителя запросов, описания схемы и Raw-запросов:

Промисы - предпочтительный способ работы с результатами запросов, одним из важных их преимуществ я вляется возможность перехватывать ошибки:

```js
knex.select('name')
.from('users')
.where('id', '>', 20)
.andWhere('id', '<', 200)
.limit(10)
.offset(x)
.then(function(rows) {
  return _.pluck(rows, 'name');
})
.then(function(names) {
  return knex.select('id').from('nicknames').whereIn('nickname', names);
})
.then(function(rows) {
  console.log(rows);
})
.catch(function(error) {
  console.error(error)
});
```

Колбэки также поддерживаются, для их работы существует функция asCallback:

```js
knex.select('name').from('users')
.where('id', '>', 20)
.andWhere('id', '<', 200)
.limit(10)
.offset(x)
.asCallback(function(err, rows) {
  if (err) return console.error(err);
  knex.select('id').from('nicknames')
    .whereIn('nickname', _.pluck(rows, 'name'))
    .asCallback(function(err, rows) {
      if (err) return console.error(err);
      console.log(rows);
    });
});
```

Кроме этого Knex поддерживат следующие способы работы с результатами:

* Потоки
* Обработчики событий
* Получение текста запроса

Более подробно об этих способах можно узнать в [документации](http://knexjs.org/#Interfaces-Streams).

## ORM {#orm}

ORM \(Object-Relational Mapping\) - технология программирования, которая связывает базы данных с концепциями объектно-ориентированных языков программирования, создавая «виртуальную объектную базу данных». Данная технология позволяет решить следующие задачи:

* обеспечить работу с данными в терминах классов, а не таблиц данных и напротив,
* преобразовать термины и данные классов в данные, пригодные для хранения в СУБД,
* обеспечить интерфейс для [CRUD](https://ru.wikipedia.org/wiki/CRUD) операций над данными.

На практике данная технология реализована в виде различных программных комплексов или модулей, которые преобразуют объектные операции в SQL-код и наоборот, полученные данные отражают в объектной модели приложения.

## Bookshelf.js {#bookshelfjs}

Bookshelf.js это написанная javascript ORM система для Node.js, использующая построитель запросов Knex. Она представляет интерфейс на базе промисов и колбэков, поддержку транзакций, загрузку связанных данных, полиморфное связывание и поддержку отношений один-к-одному, один-ко-многим и много-ко-многим. Разработана для работы с следующими СУБД:

* PostgreSQL
* MySQL
* SQLite3

#### Установка {#установка}

Помимо bookshelf необходимо установить knex, а также драйвер одной из СУБД:

```js
$ npm install knex@0.13 --save
$ npm install bookshelf --save

# Then add one of the following:
$ npm install pg
$ npm install mysql
$ npm install mariasql
$ npm install sqlite3
```

#### Инициализация {#инициализация}

Библиотека bookshelf инициализируется с передачей инициализированного экземпляра knex:

```js
var knex = require('knex')({
  client: 'mysql',
  connection: {
    host     : '127.0.0.1',
    user     : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test',
    charset  : 'utf8'
  }
});

var bookshelf = require('bookshelf')(knex);

var User = bookshelf.Model.extend({
  tableName: 'users'
});
```

Инициализация должна происходить в приложении только один раз.

#### Использование {#использование}

Рассмотрим использование библиотеки bookshelf на следующем примере:

```js
var knex = require('knex')({client: 'mysql', connection: process.env.MYSQL_DATABASE_CONNECTION });
var bookshelf = require('bookshelf')(knex);

var User = bookshelf.Model.extend({
  tableName: 'users',
  posts: function() {
    return this.hasMany(Posts);
  }
});

var Posts = bookshelf.Model.extend({
  tableName: 'messages',
  tags: function() {
    return this.belongsToMany(Tag);
  }
});

var Tag = bookshelf.Model.extend({
  tableName: 'tags'
})

User.where('id', 1).fetch({withRelated: ['posts.tags']}).then(function(user) {
  console.log(user.related('posts').toJSON());
}).catch(function(err) {
  console.error(err);
});
```

Остановимся подробно на частях этого примера.

В начале мы видим инициализацию knex и bookshelf.

Далее мы описываем три модели: User, Posts и Tag. Это производится расщирением базового класса bookshelf.Model - добавлением имени таблицы \(маппингом на таблицу\) и добавлением ссылки, отражающей отношение данной модели и других моделей \(функции `hasMany, belongsToMany`\).

Далее, используя построенную из моделей схему данных, мы делаем выборку - для пользователя с `id == 1` выбираем его данные, а также подгружаем связанные с его постами тэги.

#### Модель {#модель}

Модель является ключевым элементом библиотеки bookshelf. Модель представляет собой простой объект, соответствующий одной записи базы данных. Он содержит имя таблицы и любые отношения с другими моделями. Модели могут быть расширены любыми методами, связанными с предметной областью, которые могут обеспечивать работу валидации, вычисляемых полей, а также контроль доступа. Вызов конструтора модели порождает ее экземпляр, в качетсве параметров конструктор может принимать начальные значения атрибутов.

```js
new Book({
  title: "One Thousand and One Nights",
  author: "Scheherazade"
});
```

Модель содерржит следующие статические методы:

* [collection](http://bookshelfjs.org/#Model-static-collection) - создает коллекцию моделей данного класса
* [count](http://bookshelfjs.org/#Model-static-count) - считает количетсво моделей \(строк в таблице\)
* [extend](http://bookshelfjs.org/#Model-static-extend) - используется для конструирования новых классов модели \(см. пример выше\)
* [fetchAll](http://bookshelfjs.org/#Model-static-fetchAll) - получение всех экземпляров модели
* [forge](http://bookshelfjs.org/#Model-static-forge) - фабрика длдя создания новых моделей без использования new \(\)

Полное описание свойств и методов модели см. в[документации](http://bookshelfjs.org/#section-Model)по модели.

#### Коллекция {#коллекция}

Коллекции это упорядоченные наборы моделей, возвращаемые из базы данных и из функции `fetchAll()`. Коллекции также могут создаваться с помощью конструтора:

```js
let tabs = new TabSet([tab1, tab2, tab3]);
```

Коллекции содержат множество удобных методов работы со множеством моделей. Ниже приведен пример работы с коллекцией:

```js
// select * from authors where site_id = 1 and id = 2 limit 1;
new Site({id:1})
  .authors()
  .query({where: {id: 2}})
  .fetchOne()
  .then(function(model) {
    // ...
  });
```

Полное описание свойств и методов коллекции см. в [документации](http://bookshelfjs.org/#section-Collection) по коллекции.

## Практическое задание {#практическое-задание}

1. Используя любую реляционную базу данных подключить ее драйвер в проект
2. Установить соединение с момощью Knex
3. Используя Knex создать таблицу FOODS\(NAME. QUANTITY\)
4. Используя Knex добавить в нее записи и получить обратно
5. Установить в проект Bookshelf
6. Создать класс модели Foods, создать новую запись в таблице посредством модели.

## Ссылки {#ссылки}

1. [http://knexjs.or](http://knexjs.org/) - документация по Knex
2. [http://bookshelfjs.org](http://bookshelfjs.org/) - документация по Bookshelf



