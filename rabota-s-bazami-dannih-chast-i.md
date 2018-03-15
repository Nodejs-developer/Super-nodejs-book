# Работа с базами данных. Часть I {#работа-с-базами-данных-часть-i}

## Цель {#цель}

Освоить приципы взаимодействия node.js и баз данных. Научиться выполняль простые операции по выборке данных и манипуляции данными.

## Предварительные требования {#предварительные-требования}

Элементарные занния о реляционных и документных базах данных.

## Основы MongoDB {#основы-mongodb}

**MongoDB **\(от [англ.](https://ru.wikipedia.org/wiki/Английский_язык) humongous — огромный\) —[документоориентированная](https://ru.wikipedia.org/wiki/Документоориентированная_СУБД)[система управления базами данных](https://ru.wikipedia.org/wiki/Система_управления_базами_данных) \(СУБД\) с открытым исходным кодом, не требующая описания схемы таблиц. MongoDB классифицирована как [NoSQL](https://ru.wikipedia.org/wiki/NoSQL). Она использует [JSON](https://ru.wikipedia.org/wiki/JSON)-подобные документы и схему базы данных.

Запись в MongoDB это документ, структура данных, составленная из пар field: value \(поле: значение\). Документы MongoDB похожи на объекты JSON. Значения полей могут включать другие документы, массивы и массивы документов. Документы хронятся в коллекциях.

![](https://docs.mongodb.com/manual/_images/crud-annotated-document.bakedsvg.svg)

Преимущество использования документов:

* Документы \(иначе говоря объекты\) соответствуют родным типам данных во многих языках программирования
* Встроенные документы и массивы понижают необходимость тяжелых объединений данных
* Динамическая схема поддерживает свободный полиморфизм

#### Операции создания {#операции-создания}

Создание или вставка документов добавляет новые документы \([documents](https://docs.mongodb.com/manual/core/document/#bson-document-format)\) в коллекцию \([collection](https://docs.mongodb.com/manual/core/databases-and-collections/#collections)\). Если коллекция в данный момент не существует, операция вставки создаст ее.

MongoDB предоставляет следующие методы для вставки документов:

* [`db.collection.insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)
* [`db.collection.insertMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/#db.collection.insertMany)

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-insertOne.bakedsvg.svg)

В MongoDB операция вставки осуществляется в одну коллекцию. Все операции записи в MongoDB атомарны \([atomic](https://docs.mongodb.com/manual/core/write-operations-atomicity/)\) на уровне одного документа.

#### Операции чтения {#операции-чтения}

Операции чтения получают документы из коллекций, иначе говоря запрашивают коллекции для получения документов. MongoDB предоставляет следующий метод для чтения документов:

* [`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)

Можно определить фильтры \([query filters or criteria](https://docs.mongodb.com/manual/tutorial/query-documents/#read-operations-query-argument)\) определяющие возвращаемый документ.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-find.bakedsvg.svg)

#### Операции обновления {#операции-обновления}

Операции обновления изменяют существующий документ в коллекции. MongoDB предоставляет следующие методы для обновления документов

* [`db.collection.updateOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne)
* [`db.collection.updateMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany)
* [`db.collection.replaceOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne)

В MongoDB операции обновления затрагивают одну коллекцию.

Можно задать критерии или фильтры для идентификации обновляемых документов. Фильтры имеют такой же синтаксис, как и для чтения документов.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-updateMany.bakedsvg.svg)

#### Операции удаления {#операции-удаления}

Операции удаления, как ни странно, удаляют документ из коллекции. MongoDB предоставляет следующие методы для удаления документов из коллекции:

* [`db.collection.deleteOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne)
* [`db.collection.deleteMany()`](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany)

В MongoDB операции удаления затрагивают одну коллекцию.

Можно задать критерии или фильтры для идентификации удаляемых документов. Фильтры имеют такой же синтаксис, как и для чтения документов.

![](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-deleteMany.bakedsvg.svg)

#### Взаимодействие с Node.js {#взаимодействие-с-nodejs}

MongoDB взаимодействует Node.js с помощью так называемого драйвера. По сути это модуль, позволяющий содиниться с базой данных, передавать на нее команды и получать данные. Установить модуль можно с помощью npm:

```js
npm install mongodb --save
```

Следующий пример показывает использование драйвера для создания и поиска документов:

```js
const MongoClient = require('mongodb').MongoClient;

// Connection URL
const url = 'mongodb://localhost:27017';

// Database Name
const dbName = 'myproject';

const insertDocuments = function(db, callback) {
  // Get the documents collection
  const collection = db.collection('documents');
  // Insert some documents
  collection.insertMany([
    {a : 1}, {a : 2}, {a : 3}
  ], function(err, result) {
    console.log("Inserted 3 documents into the collection");
    callback(result);
  });
}

const findDocuments = function(db, callback) {
  // Get the documents collection
  const collection = db.collection('documents');
  // Find some documents
  collection.find({}).toArray(function(err, docs) {
    console.log("Found the following records");
    console.log(docs)
    callback(docs);
  });
}

// Use connect method to connect to the server
MongoClient.connect(url, function(err, client) {
  console.log("Connected successfully to server");

  const db = client.db(dbName);

  insertDocuments(db, function() {
    findDocuments(db, function() {
      client.close();
    });
  });
});
```

Подробнее о работе драйвера см. [документацию](http://mongodb.github.io/node-mongodb-native/3.0/quick-start/quick-start/)

## Основы работы с Mongoose {#основы-работы-с-mongoose}

Прямая работа с данными MongoDB через драйвер представляется сложной для описания реальной бизнес-логики, поэтому существуют различные обертки, позволяющие приложению элегантно взаимодействовать с базой данных. Одна из таких оберток - модуль Mongoose.

Mongoose - библиотека для работы node.js приложения с базой данных MongoDB. Он обеспечивает краткое, основанное на схемах решение для моделирования данных приложений. Mongoose включает встроенное отражение типов данных, валидацию, построение запросов, привязку бизнес-логики и многое другое.

Установка:

```js
$ npm install mongoose
```

Включение в проект и соединение с базой данных с `test`на локальном экземпляре MongoDB.

```js
// getting-started.js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');
```

Установка обработчиков успешного соединения с базой данных или ошибки:

```js
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function() {
  // we're connected!
});
```

Колбэк будет вызван как только соединение будет установлено. В Mongoose всё происходит от Схемы \([Schema](http://mongoosejs.com/docs/guide.html)\). Можно взять ссылку на схему и определить свою схему.

```js
var kittySchema = mongoose.Schema({
  name: String
});
```

Мы получили схему с одним свойством `name` типа `String`. Следующий шаг - скомпилировать схему в модель \([Model](http://mongoosejs.com/docs/models.html)\).

```js
var Kitten = mongoose.model('Kitten', kittySchema);
```

Модель это класс с помощью которого мы конструируем документы. В нашем случае каждй документ будет `Kitten` со свойствами и поведением задекларированными в схеме. Создадим документ:

```js
var silence = new Kitten({ name: 'Silence' });
console.log(silence.name); // 'Silence'
```

Добавим функциональность `speak` в наш документ:

```js
// Метод должен быть добавлен в схему перед ее компиляцией в mongoose.model()
kittySchema.methods.speak = function () {
  var greeting = this.name
    ? "Meow name is " + this.name
    : "I don't have a name";
  console.log(greeting);
}

var Kitten = mongoose.model('Kitten', kittySchema); kittySchema);
```

Функции, добавленные в свойство `methods` property компилируются в прототип модели и становятся доступны в каждом экземпляре - документе.

```js
var fluffy = new Kitten({ name: 'fluffy' });
fluffy.speak(); // "Meow name is fluffy"
```

Сохраним созданные документы в MongoDB. Каждый документ может быть добавлен в базу данных с помощью его метода [save](http://mongoosejs.com/docs/api.html#model_Model-save). Первый аргумент колбэка при этом - ошибка, в случае её возникновения.

```js
  fluffy.save(function (err, fluffy) {
    if (err) return console.error(err);
    fluffy.speak();
  });
```

Получить доступ к сохраненным документам через модель:

```js
Kitten.find(function (err, kittens) {
  if (err) return console.error(err);
  console.log(kittens);
})
```

Так мы вывели все модели в консоль. Если мы хотим отфильтровать документы, Mongoose поддерживает синтаксис запросов MongoDB \([querying](http://mongoosejs.com/docs/queries.html)\) .

```js
Kitten.find({ name: /^fluff/ }, callback);
```

## Практическое задание {#практическое-задание}

1. Установить локальный экземпляр MongoDB
2. Создать коллекцию для хранения информации о постах и коментариях блога. Документы коментариев должны быть в свойстве поста - массиве документов.
3. Написать скрипт для создания, чтения, обновления и удаления постов и коментариев.
4. Сделать рефакторинг скрипта используя Mongoose

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| CRUD | Create + Read + Update + Delete: объединенное понятия для элементарных операций с данными в базе данных. |
| Документная база данных | база данныхбаза данных, специально предназначенная для хранения иерархических структур данных \(документов\) и обычно реализуемая с помощью подхода NoSQL |
| Реляционная база данных | база данных, основанная на реляционной модели данных. Понятие «реляционный» основано на англ. relation \(«отношение», «зависимость», «связь»\). |
| NoSQL | термин, обозначающий ряд подходов, направленных на реализацию хранилищ баз данных, имеющих существенные отличия от моделей, используемых в традиционных реляционных СУБД с доступом к данным средствами языка SQL. |

## Ссылки {#ссылки}

1. [http://www.bazhukov.net/little-mongodb-book/](http://www.bazhukov.net/little-mongodb-book/) - Краткий курс по MongoDB на русском языке
2. [http://mongoosejs.com/docs/guide.html](http://mongoosejs.com/docs/guide.html) - Официальная документация по Mongoose
3. [https://docs.mongodb.com/manual/](https://docs.mongodb.com/manual/)- Официальное руководство по MongoDB



