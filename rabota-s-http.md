# Работа с HTTP {#работа-с-http}

## **Цель** {#цель}

Разобраться в основных возможностях модуля 'http', создать простой сервер.

## Предварительные**требования** {#предварительные-требования}

Знание основ JavaScript.

Базовые знания о HTTP

Установить Node.js и npm

Умение работать с модулями Node.js

## Ход урока {#ход-урока}

Для использования HTTP-сервера и клиента в Node.js необходимо подключить модуль ['http'](https://nodejs.org/docs/latest-v8.x/api/http.html) \(`require('http')`\).

Интерфейсы HTTP в Node.js предназначены для поддержки многих функций протокола. Параметры запроса и ответа являются потоками.

Чтобы создать сервер, следует вызвать метод [http.createServer\(\[requestListener\]\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_http_createserver_requestlistener). Метод `createServer()` возвращает объект [`http.Server`](https://nodejs.org/docs/latest-v8.x/api/http.html#http_class_http_server). Но чтобы сервер мог прослушивать и обрабатывать входящие подключения, у объекта сервера необходимо вызвать метод [`server.listen()`](https://nodejs.org/docs/latest-v8.x/api/net.html#net_server_listen), в который в качестве параметра передается номер порта, по которому запускается сервер.

Пример использования:

```js
const http = require('http');
const hostname = '127.0.0.1';
const port = 3000;
http.createServer().listen(port, hostname);
```

Работать с полученными с сервера данными можно несколькими способами.

Первый способ - это обработка события ['request'](https://nodejs.org/docs/latest-v8.x/api/http.html#http_event_request). Это событие срабатывает каждый раз, как происходит запрос.

```js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3002;

const server = http.createServer();

server.on('request', (req, res) => {
    res.end('Hello world');
})

server.listen(port, hostname);
```

Функция его обработки принимает два параметра:

* request: хранит информацию о запросе
* response: управляет отправкой ответа

Взамен обработки этого события, вы можете просто включить такую же самую функцию обработки в качестве параметра метода [http.createServer\(\[requestListener\]\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_http_createserver_requestlistener).

```js
const hostname = '127.0.0.1';
const port = 3002;

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});

server.listen(port, hostname);
```

requestListener - это функция, которая автоматически добавляется к событию ['request'](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#).

### request {#request}

Параметр request позволяет получить информацию о запросе и представляет объект [http.IncomingMessage](https://nodejs.org/docs/latest-v8.x/api/http.html#http_class_http_incomingmessage). Отметим некоторые основные свойства этого объекта:

* [headers](https://nodejs.org/docs/latest-v8.x/api/http.html#http_message_headers) - Пара ключей-значений имен и значений заголовков. Названия заголовков приходят в нижнем регистре.
* [method](https://nodejs.org/docs/latest-v8.x/api/http.html#http_message_method) - Метод запроса как строка. Пример: «GET», «DELETE».
* [url](https://nodejs.org/docs/latest-v8.x/api/http.html#http_message_url) - URL-адрес запроса. Содержит только URL-адрес, который присутствует в фактическом HTTP-запросе.

### response {#response}

Параметр response управляет отправкой ответа и представляет объект [http.ServerResponse](https://nodejs.org/docs/latest-v8.x/api/http.html#http_class_http_serverresponse). Среди его функциональности можно выделить следующие методы:

* [end\(\[data\]\[, encoding\]\[, callback\]\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_end_data_encoding_callback) - Этот метод сигнализирует серверу о том, что все заголовки ответа и тело отправлены и сервер должен понять что ответ завершен. Метод, response.end \(\), обязательно должен быть вызван для каждого ответа. При этом в метод мы можем передавать данные, которые будут добавляться к телу ответа

* [statusCode](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_statuscode) - Устанавливает статусный код ответа

* [statusMessage](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_statusmessage) - Устанавливает сообщение, отправляемое вместе со статусным кодом

* [write\(chunk\[, encoding\]\[, callback\]\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_write_chunk_encoding_callback) - Посылает часть тела ответа. Этот метод можно вызвать несколько раз, чтобы обеспечить последовательный вывод частей. Если этот метод вызван, то [writeHead](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_writehead_statuscode_statusmessage_headers) уже вызван не будет.

* [setHeader\(name, value\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_setheader_name_value) - Устанавливает одно значение заголовка для неявных заголовков. Если этот заголовок уже существует в заголовках, подлежащих отправке, его значение будет заменено.

* [writeHead\(statusCode\[, statusMessage\]\[, headers\]\)](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_writehead_statuscode_statusmessage_headers) - Отправляет ответный заголовок в запрос. `statusCode` представляет собой трехзначный код состояния HTTP, например 404. Последний аргумент, [`headers`](https://nodejs.org/docs/latest-v8.x/api/http.html#http_response_writehead_statuscode_statusmessage_headers), является заголовками ответов. При желании в качестве второго аргумента можно записать человеко читаемый `statusMessage`.

## Практическое задание {#практическое-задание}

Дополните задание с главы [Работа с модулями в Node.js](/rabota-s-modulyami-v-nodejs.md) следующим образом:

* создайте сервер, который будет слушать 3005 порт
* из url запроса необходимо получать имя и возраст пользователя и выводить информацию о пользователе на экран
* проверьте на url `127.0.0.1:3005?name=Vasya&age=20`

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Поток данных** | Абстракция, используемая для чтения или записи файлов, сокетов и т. п. в единой манере. |
| **Интерфейс** | Общая граница между двумя функциональными объектами, требования к которой определяются стандартом; совокупность средств, методов и правил взаимодействия \(управления, контроля и т.д.\) между элементами системы. |
| **Заголовок ответа** | Список поддерживаемых методов всего сервера или конкретного ресурса. |
| **Тело ответа** | Данные, которые отправляются на клиенскую часть |
| **Сервер \(**Веб-сервер\) | Это сервер, принимающий HTTP-запросы от клиентов, обычно веб-браузеров, и выдающий им HTTP-ответы, как правило, вместе с HTML-страницей, изображением, файлом, медиа-потоком или другими данными. |

## Ссылки {#список-литературы}



