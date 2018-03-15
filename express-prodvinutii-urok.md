# Express: продвинутый урок {#express-продвинутый-урок}

#### Цель {#цель}

Освоить веб-фреймворк Express в объеме, достаточном для построения сложных интернет приложений на базе [Node.js](https://nodejs.org/) с использованием Logger, Cookies, Sessions, HTTPS, Compression, CORS и т. д.

#### Предварительные требования {#предварительные-требования}

Понимание принципов работы веб-приложений и веб-API. Базовые знания javascript, [Node.js](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#), [npm](https://www.npmjs.com/), освоенный урок [Express: начальный урок](https://nodejs-junior-developer-traini.gitbooks.io/super-book-of-node-js/content/fast-unopinionated-minimalist-web-framework-for-nodejs.html)

# Генератор приложений Express {#генератор-приложений-express}

Для быстрого создания “скелета” приложения используется инструмент для генерации приложений`express`.

Установите`express`с помощью следующей команды:

```js
$ npm install express-generator -g
```

Для просмотра опций команды воспользуйтесь опцией`-h`:

```js
$ express -h

  Usage: express [options][dir]

  Options:

    -h, --help          output usage information
        --version       output the version number
    -e, --ejs           add ejs engine support
        --hbs           add handlebars engine support
        --pug           add pug engine support
    -H, --hogan         add hogan.js engine support
    -v, --view 

<

engine

>

 add view 

<

engine

>

 support (ejs|hbs|hjs|jade|pug|twig|vash) (defaults to jade)
    -c, --css 

<

engine

>

  add stylesheet 

<

engine

>

 support (less|stylus|compass|sass) (defaults to plain css)
        --git           add .gitignore
    -f, --force         force on non-empty directory
```

Например, следующая команда создает приложение Express с именем _myapp _в текущем рабочем каталоге:

```js
$ express --view=pug myapp

   create : myapp
   create : myapp/package.json
   create : myapp/app.js
   create : myapp/public
   create : myapp/public/javascripts
   create : myapp/public/images
   create : myapp/routes
   create : myapp/routes/index.js
   create : myapp/routes/users.js
   create : myapp/public/stylesheets
   create : myapp/public/stylesheets/style.css
   create : myapp/views
   create : myapp/views/index.pug
   create : myapp/views/layout.pug
   create : myapp/views/error.pug
   create : myapp/bin
   create : myapp/bin/www
```

Затем установите зависимости:

```js
$ cd myapp
$ npm install
```

В MacOS или Linux запустите приложение с помощью следующей команды:

```js
$ DEBUG=myapp:* npm start
```

В Windows используется следующая команда:

```js
> set DEBUG=myapp:* & npm start
```

Затем откройте страницу [http://localhost:3000/](http://localhost:3000/) в браузере для доступа к приложению.

Структура каталогов сгенерированного приложения выглядит следующим образом:

```
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug

7 directories, 9 files
```

> Структура приложения, сгенерированная с помощью генератора, является всего лишь одним из множества способов организации структуры приложений Express. Вы можете использовать данную структуру или изменять ее в соответствии со своими потребностями.

# Углубленно о маршрутизации {#маршрутизация}

_**Маршрутизация **_определяет, как приложение отвечает на клиентский запрос к конкретному адресу \(URI\). Вводную информацию о маршрутизации можно найти в разделе [Основы маршрутизации](http://expressjs.com/ru/starter/basic-routing.html).

Приведенный ниже код служит примером одного из самых простых маршрутов.

```js
var express = require('express');
var app = express();

// respond with "hello world" when a GET request is made to the homepage
app.get('/', function(req, res) {
  res.send('hello world');
});
```

#### Методы Route {#методы-route}

Метод route является производным от одного из методов HTTP и присоединяется к экземпляру класса `express`.

Приведенный ниже код служит примером маршрутов, определенных для методов запросов GET и POST к корневому каталогу приложения.

```js
// GET method route
app.get('/', function (req, res) {
  res.send('GET request to the homepage');
});

// POST method route
app.post('/', function (req, res) {
  res.send('POST request to the homepage');
});
```

Express поддерживает перечисленные далее методы маршрутизации, соответствующие методам HTTP:`get`,`post`,`put`,`head`,`delete`,`options`,`trace`,`copy`,`lock`,`mkcol`,`move`,`purge`,`propfind`,`proppatch`,`unlock`,`report`,`mkactivity`,`checkout`,`merge`,`m-search`,`notify`,`subscribe`,`unsubscribe`,`patch`,`search`и`connect`.

> Для методов route, преобразуемых в недействительные имена переменных JavaScript, используйте нотацию в квадратных скобках. Например,`app['m-search']('/', function ...`

Существует особый метод маршрутизации,`app.all()`, не являющийся производным от какого-либо метода HTTP. Этот метод используется для загрузки функций промежуточной обработки в пути для всех методов запросов.

В приведенном ниже примере обработчик будет запущен для запросов, адресованных “/secret”, независимо от того, используется ли GET, POST, PUT, DELETE или какой-либо другой метод запроса HTTP, поддерживаемый в [модуле http](https://nodejs.org/api/http.html#http_http_methods).

```js
app.all('/secret', function (req, res, next) {
  console.log('Accessing the secret section ...');
  next(); // pass control to the next handler
});
```

#### Пути маршрутов {#пути-маршрутов}

Пути маршрутов, в сочетании с методом запроса, определяют конкретные адреса \(конечные точки\), в которых могут быть созданы запросы. Пути маршрутов могут представлять собой строки, шаблоны строк или регулярные выражения.

В Express для сопоставления путей маршрутов используется [path-to-regexp](https://www.npmjs.com/package/path-to-regexp); в документации к path-to-regexp описаны все возможные варианты определения путей маршрутов. [Express Route Tester](http://forbeslindesay.github.io/express-route-tester/) - удобный инструмент для тестирования простых маршрутов в Express, хотя и не поддерживает сопоставление шаблонов.

Строки запросов не являются частью пути маршрута.

Ниже приводятся примеры путей маршрутов на основе строк.

> Символы ?, +, \* и \(\) представляют собой подмножества соответствующих им регулярных выражений. Дефис \(-\) и точка \(.\) интерпретируются буквально в путях на основе строк.

```js
// Данный путь маршрута сопоставляет запросы с корневым маршрутом
app.get('/', function (req, res) { res.send('root'); });

// Данный путь маршрута сопоставляет запросы с /about
app.get('/about', function (req, res) { res.send('about'); });

// Данный путь маршрута сопоставляет запросы с /random.text
app.get('/random.text', function (req, res) { res.send('random.text'); });

// Приведенный ниже путь маршрута сопоставляет acd и abcd
app.get('//ab?cd', function (req, res) { res.send('ab?cd'); });

// Этот путь маршрута сопоставляет abcd, abbcd, abbbcd и т.д.
app.get('/ab+cd', function (req, res) { res.send('ab+cd'); });

// Этот путь маршрута сопоставляет abcd, abxcd, abRABDOMcd, ab123cd и т.д.
app.get('/ab(cd)?e', function (req, res) { res.send('ab(cd)?e'); });

// Данный путь маршрута сопоставляет любой элемент с “a” в имени маршрута.
app.get(/a/, function (req, res) { res.send('/a/'); });

// Данный маршрут сопоставляет butterfly и dragonfly, но не butterflyman, dragonfly man и т.д.
app.get(/.*fly$/, function (req, res) { res.send('/.*fly$/'); });
```

#### Обработчики маршрутов {#обработчики-маршрутов}

Для обработки запроса можно указать несколько функций обратного вызова, подобных [middleware](http://expressjs.com/ru/guide/using-middleware.html). Единственным исключением является то, что эти обратные вызовы могут инициировать `next('route')`для обхода остальных обратных вызовов маршрута. С помощью этого механизма можно включить в маршрут предварительные условия, а затем передать управление последующим маршрутам, если продолжать работу с текущим маршрутом не нужно.

Обработчики маршрутов могут принимать форму функции, массива функций или их сочетания, как показано в примерах ниже.

Одна функция обратного вызова может обрабатывать один маршрут. Например:

```js
// Одна функция обратного вызова может обрабатывать один маршрут:
app.get('/example/a', function (req, res) {
  res.send('Hello from A!');
});

// Один маршрут может обрабатываться несколькими функциями обратного вызова (обязательно укажите объектnext):
app.get('/example/b', function (req, res, next) {
  console.log('the response will be sent by the next function ...');
  next();
}, function (req, res) {
  res.send('Hello from B!');
});

// Массив функций обратного вызова может обрабатывать один маршрут:
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}
var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}
var cb2 = function (req, res) {
  res.send('Hello from C!');
}
app.get('/example/c', [cb0, cb1, cb2]);

// Маршрут может обрабатываться сочетанием независимых функций и массивов функций:
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}
var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}
app.get('/example/d', [cb0, cb1], function (req, res, next) {
  console.log('the response will be sent by the next function ...');
  next();
}, function (req, res) {
  res.send('Hello from D!');
});
```

#### Методы ответа {#методы-ответа}

Методы в объекте ответа \(`res`\), перечисленные в таблице ниже, могут передавать ответ клиенту и завершать цикл “запрос-ответ”. Если ни один из этих методов не будет вызван из обработчика маршрута, клиентский запрос зависнет.

| Метод | Описание |
| :--- | :--- |
| [res.download\(\)](http://expressjs.com/ru/4x/api.html#res.download) | Приглашение загрузки файла. |
| [res.end\(\)](http://expressjs.com/ru/4x/api.html#res.end) | Завершение процесса ответа. |
| [res.json\(\)](http://expressjs.com/ru/4x/api.html#res.json) | Отправка ответа JSON. |
| [res.jsonp\(\)](http://expressjs.com/ru/4x/api.html#res.jsonp) | Отправка ответа JSON с поддержкой JSONP. |
| [res.redirect\(\)](http://expressjs.com/ru/4x/api.html#res.redirect) | Перенаправление ответа. |
| [res.render\(\)](http://expressjs.com/ru/4x/api.html#res.render) | Вывод шаблона представления. |
| [res.send\(\)](http://expressjs.com/ru/4x/api.html#res.send) | Отправка ответа различных типов. |
| [res.sendFile](http://expressjs.com/ru/4x/api.html#res.sendFile) | Отправка файла в виде потока октетов. |
| [res.sendStatus\(\)](http://expressjs.com/ru/4x/api.html#res.sendStatus) | Установка кода состояния ответа и отправка представления в виде строки в качестве тела ответа. |

#### app.route\(\) {#approute}

Метод `app.route()` позволяет создавать обработчики маршрутов, образующие цепочки, для пути маршрута. Поскольку путь указан в одном расположении, удобно создавать модульные маршруты, чтобы минимизировать избыточность и количество опечаток. Дополнительная информация о маршрутах приводится в документации [Router\(\)](http://expressjs.com/ru/4x/api.html#router).

Ниже приведен пример объединенных в цепочку обработчиков маршрутов, определенных с помощью функции `app.route()`.

```js
app.route('/book')
  .get(function(req, res) {
    res.send('Get a random book');
  })
  .post(function(req, res) {
    res.send('Add a book');
  })
  .put(function(req, res) {
    res.send('Update the book');
  });
```

#### express.Router {#expressrouter}

С помощью класса `express.Router` можно создавать модульные, монтируемые обработчики маршрутов. Экземпляр `Router` представляет собой комплексную систему промежуточных обработчиков и маршрутизации; по этой причине его часто называют “мини-приложением”.

В приведенном ниже примере создается маршрутизатор в виде модуля, в него загружается функция промежуточной обработки, определяется несколько маршрутов, и модуль маршрутизатора монтируется в путь в основном приложении.

Создайте файл маршрутизатора с именем  `birds.js` в каталоге приложения со следующим содержанием:

```js
var express = require('express');
var router = express.Router();

// middleware that is specific to this router
router.use(function timeLog(req, res, next) {
  console.log('Time: ', Date.now());
  next();
});
// define the home page route
router.get('/', function(req, res) {
  res.send('Birds home page');
});
// define the about route
router.get('/about', function(req, res) {
  res.send('About birds');
});

module.exports = router;
```

Потом загрузите модуль маршрутизации в приложение:

```js
var birds = require('./birds');
...
app.use('/birds', birds);
```

Данное приложение теперь сможет обрабатывать запросы, адресованные ресурсам `/birds` и `/birds/about`, а также вызывать специальную функцию промежуточной обработки `timeLog` данного маршрута.

## Важные модули middleware для полноценного веб сервера {#важные-модули-middleware-для-полноценного-веб-сервера}

Полноценная работа Express как веб сервера невозможна без дополнительных модулей middleware. Ранее эти модули были включены в состав Express, но теперь они существуют самостоятельню

Обзор самых важных модулей начнем с примера:

```js
var express = require("express");  
var cors = require("cors");  
var morgan = require("morgan");  
var compression = require("compression");  
var fs = require("fs");  
var https = require("https");  
var helmet = require("helmet");  
var app = express();

app.use(morgan("common"));  
app.use(helmet());  
app.use(cors({  
    origin: ["http://localhost:3001"],
    methods: ["GET", "POST"],
    allowedHeaders: ["Content-Type", "Authorization"]
}));
app.use(compression());

app.get("/", function(req, res) {  
    res.json({
        status: "My API is alive!"
    });
});

var credentials = {  
    key: fs.readFileSync("my-api.key", "utf8"),
    cert: fs.readFileSync("my-api.cert", "utf8")
};
https  
    .createServer(credentials, app)
    .listen(3000, function() {
        console.log("My API is running...");
    });

module.exports = app;
```

Рассмотрим подробно подключенные модули.

#### CORS {#cors}

CORS \(совместное использование ресурсов с перекрестными ресурсами\) является важным механизмом HTTP. Он отвечает за разрешение или запрет асинхронных запросов от других доменов. Нам необходимо включить промежуточное ПО CORS для конечных точек, которые станут общедоступными. Для установки модуля**cors**достаточно вызвать `npm install cors`. Далее подключаем его с помощью `app.use`, как указано в примере. Использование функции `cors()` без параметров освободит полный доступ к API. Рекомендуется контролировать, какие клиентские домены могут иметь доступ, и какие методы они могут использовать,, какие заголовки должны требоваться клиентам сообщать в запросе. Для этого в качестве параметра функции необходимо передать объект конфигурации \(см. пример выше\)

#### Логирование {#логирование}

Для генерации логов можно использовать любое существующее _middleware_, либо же написать его самостоятельно. В примере выше используется модуль **morgan**. Для установки модуля достаточно запустить `npm install morgan`и подключить его с помощью `app.use`\(см. пример выше\). В качестве параметра в функцию morgan следует передать формат строки лога \(предустановленный, кастомный или функцию \), подробнее см.[документацию](https://github.com/expressjs/morgan).

#### Сжатие данных {#сжатие-данных}

Чтобы сделать запросы более легкими и загружаемыми быстрее, следует включить промежуточное программное обеспечение, которое будет отвечать за сжатие ответов JSON, а также статическиих файлов. Сжатие происходить в формате GZIP, который поддерживается основными браузерами. Это достигается простым включением модуля **compression**. Для установки модуля достаточно вызвать `npm install compression`. Далее подключаем его с помощью `app.use`, как указано в примере.

#### HTTPS {#https}

В настоящее время обеспечения безопасной работы приложений необходимо обеспечить безопасное соединение между сервером и клиентом. Для этого многие приложения используют сертификаты безопасности для обеспечения соединения SSL \(Secure Sockets Layer\) через протокол HTTPS.

Чтобы реализовать соединение по протоколу HTTPS, необходимо приобрести цифровой сертификат для использования в production. Допустим, такой сертификат уже есть в наличии. Поместите два файла \(один файл использует расширение .key, а другой - файл .cert\) в корне проекта. После этого можно использовать встроенный модуль **https**, чтобы позволить нашему серверу начать использовать протокол HTTPS, и модуль **fs **для открытия и чтения файлов сертификатов: `my-api.key` и `my-api.cert`, которые будут использоваться в качестве параметров учетных данных для запуска сервера в режиме HTTPS. Для нужно заменить функцию `app.listen()` на функцию `https.createServer(credentials,app).listen()`\(см. пример выше\).

#### Защита веб сервера от атак {#защита-веб-сервера-от-атак}

Модуль **helmet **обеспечиват защиту веб сервера от основных уязвимостей протокола HTTP. Этот модуль осуществвляет следующие функции:

* Настраивает политику безопасности контента;
* Удаляет заголовок X-Powered-By, который информирует имя и версию сервера;
* Настраивает правила для HTTP Public Key Pinning;
* Настраивает правила для HTTP Strict Transport Security;
* Обрабатывает заголовок X-Download-Options для Internet Explorer 8+;
* Отключает кеширование на стороне клиента;
* Предотвращает сниффинг по Mime Type;
* Предотвращает атаки ClickJacking;
* Защищает от атак XSS \(Cross-Site Scripting\).

Для установки модуля достаточно вызвать `npm install helmet`. Далее подключаем его с помощью `app.use`, как указано в примере.

## Сессии и cookies {#сессии-и-cookies}

В основе сессий в Express лежит соответствующий middleware, который, в свою очередь, опирается на механизм хранения данных. Существует хранилище в памяти, а так же сторонние хранилища, включая [connect-redis](https://github.com/visionmedia/connect-redis) и [connect-mongodb](https://github.com/masylum/connect-mongodb). В качестве альтернативы так же можно рассматривать [cookie-sessions](https://github.com/caolan/cookie-sessions), который хранит данные сессии в пользовательской куке \(cookie\).

Поддержка сессий может быть включена следующим образом:

```js
var cookieSession = require('cookie-session');

app.use(cookieSession({
  name: 'session',
  keys: [/* secret keys */],

  // Cookie Options
  maxAge: 24 * 60 * 60 * 1000 // 24 hours
}))
```

Теперь в HTTP-обработчиках будет доступна переменная `req.session`:

```js
app.get('/item', function(req, res) {
  req.session.message = 'Hello World';
});
```

Следующий пример показывает использование MongoDB и Redis в качестве хранишища данных сессии с помощью модуля `express-session`:

```js
var mongoose = require('mongoose');

mongoose.connect();

app.use(express.session({
    secret: 'a4f8071f-c873-4447-8ee2',
    cookie: { maxAge: 2628000000 },
    store: new (require('express-sessions'))({
        storage: 'mongodb',
        instance: mongoose, // optional 
        host: 'localhost', // optional 
        port: 27017, // optional 
        db: 'test', // optional 
        collection: 'sessions', // optional 
        expire: 86400 // optional 
    })
}));
```

```js
var redis = require('redis');
var client = redis.createClient(6379, 'localhost');

app.use(express.session({
    secret: 'a4f8071f-c873-4447-8ee2',
    cookie: { maxAge: 2628000000 },
    store: new (require('express-sessions'))({
        storage: 'redis',
        instance: client, // optional 
        host: 'localhost', // optional 
        port: 6379, // optional 
        collection: 'sessions', // optional 
        expire: 86400 // optional 
    })
}));
```

## Практическое задание {#практическое-задание}

1. Использовать веб сервер из урока [Express: начальный урок](https://nodejs-junior-developer-traini.gitbooks.io/super-book-of-node-js/content/fast-unopinionated-minimalist-web-framework-for-nodejs.html)
2. Сделать рефаткор сервера с испозльзованием генератора приложения Express
3. Настроить маршрутизацию с использованием регулярных выражений, разместить маршруты в отдельных модулях.
4. Обеспечить отправку следующих видов ответа: redirect, json, status, file, render
5. Настроить CORS
6. Настроить логирование
7. Настроить сжатие
8. Обеспечить сохранение данных сессии в cookies

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| CORS \(Cross-Origin Resource Sharing\) | Механизм, использующий дополнительные HTTP-заголовки, чтобы дать возможность агенту пользователя получать разрешения на доступ к выбранным ресурсам с сервера на источнике \(домене\), отличном от того, что сайт использует в данный момент. |
| GZIP \(сокращение от GNU Zip\) | Утилита сжатия и восстановления \(декомпрессии\) файлов, использующая алгоритм Deflate. Применяется в основном в UNIX-системах, в ряде которых является стандартом де-факто для сжатия данных. |
| SSL \(Secure Sockets Layer — уровень защищённых cокетов\) | Криптографический протокол, который подразумевает более безопасную связь. Он использует асимметричную криптографию для аутентификации ключей обмена, симметричное шифрование для сохранения конфиденциальности, коды аутентификации сообщений для целостности сообщений. |
| HTTP Public Key Pinning | Механизм безопасности в Интернете, предоставляемый через HTTP-заголовок, который позволяет веб-сайтам HTTPS противостоять персонализации злоумышленников с использованием ошибочных или иных мошеннических сертификатов. Для этого он предоставляет набор открытых ключей клиенту \(браузеру\), который должен быть единственным, которому доверяют подключения к этому домену. |
| HTTP Strict Transport Security | Механизм, принудительно активирующий защищённое соединение через протокол HTTPS. Данная политика безопасности позволяет сразу же устанавливать безопасное соединение вместо использования HTTP-протокола. |
| Сниффинг \(sniffing\) | Вид атаки, предусматривающий просмотр всех сетевых пакетов специальным приложением \(сниффером\) с целью получения различной информации |
| Clickjacking | Механизм обмана пользователей интернета, при котором злоумышленник может получить доступ к конфиденциальной информации или даже получить доступ к компьютеру пользователя, заманив его на внешне безобидную страницу или внедрив вредоносный код на безопасную страницу. |
| XSS \(Cross-Site Scripting\) | Тип атаки на веб-системы, заключающийся во внедрении в выдаваемую веб-системой страницу вредоносного кода \(который будет выполнен на компьютере пользователя при открытии им этой страницы\) и взаимодействии этого кода с веб-сервером злоумышленника |

## Ссылки {#ссылки}

1. [developer.mozilla.org/en-US/docs/Web/HTTP/Access\_control\_CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) - Документация CORS



