# Модуль Net {#модуль-net}

## **Цель** {#цель}

Ознакомится с модулем [Net](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net) и его основными методами.

## Предварительные**требования** {#предварительные-требования}

Установленны Node.js и npm

Умение работать с модулями Node.js

Знания работы с событиями

Ознакомиться с потоками

## Определение и методы {#определение-и-методы}

Модуль [Net](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net) предоставляет асинхронное сетевое API для создания как серверов, так и клиентов, основанных на потоках. Этот модуль можно включить через `require(‘net’)`.

Список и определение всех методов данного модуля и его классов можно посмотреть в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/net.html).

Рассмотри методы модуля, которые чаще всего используются на практике:

* Метод [net.createServer\(\[options\]\[, connectionListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createserver_options_connectionlistener) создает новый TCP-сервер. Аргумент **connectionListener**
  автоматически устанавливается как прослушиватель для события «connection».
* [net.createConnection\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createconnection) это функция фабрики, которая создает новый net.Socket, немедленно инициирует соединение с `socket.connect()`, а затем возвращает net.Socket, который запускает соединение. Когда соединение установлено, при возвращении сокета будет срабатывать событие «connect». Последний параметр **connectListener**, если он указан, будет добавлен как слушатель для события «connect» один раз. Возможные вариации:

  * [net.connect\(options\[, connectListener\]\)](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#) подключается к указанному в опциях адресу или порту.
  * [net.connect\(port\[, host\]\[, connectListener\]\)](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#) создает TCP-соединение с портом на хостинге.
  * [net.connect\(path\[, connectListener\]\)](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#) cоздает [IPC](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_ipc_support)-соединение по пути.

* Функция [net.connect\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_connect) является псевдонимом функции [net.createConnection\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createconnection). Также имеет несколько вариантов вызова:

  * [net.createConnection\(options\[, connectListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createconnection_options_connectlistener) подключается к указанному в опциях адресу или порту.
  * [net.createConnection\(port\[, host\]\[, connectListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createconnection_port_host_connectlistener) создает TCP-соединение с портом на хостинге.
  * [net.createConnection\(path\[, connectListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_net_createconnection_path_connectlistener) cоздает [IPC](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_ipc_support)-соединение по пути.

## Классы модуля и их методы {#классы-модуля-и-их-методы}

Класс[net.Server](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_server)используется для создания TCP или локального сервера. [net.Server](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_server) – это `EventEmitter` с такими событиями:

* [listening](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_listening) — Запускается, когда сервер был установлен после вызова server.listen.
* [connection](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_connection) — Запускается при создании нового соединения. Объект socket, объект connection доступны для обработчика событий. Socket — это экземпляр net.Socket.
* [close](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_close) — Запускается при закрытии сервера. Обратите внимание: если соединения существуют, это событие не будет выдаваться до тех пор, пока все соединения не будут завершены.
* [error](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_error) — Запускается при возникновении ошибки. Событие ‘close’ будет вызываться непосредственно после этого события.

Самым часто используемым методом данного класса является [server.listen\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_server_listen). Метод запускае сервер, который прослушивает подключения. [net.Server](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_server) может быть TCP или IPC-сервером в зависимости от того, что он слушает.

Возможные варианты вызова:

* [server.listen\(handle\[, backlog\]\[, callback\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_server_listen_handle_backlog_callback)
* [server.listen\(options\[, callback\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_server_listen_options_callback)
* [server.listen\(path\[, backlog\]\[, callback\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_server_listen_path_backlog_callback) для IPC серверов
* [server.listen\(\[port\]\[, host\]\[, backlog\]\[, callback\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_server_listen_port_host_backlog_callback) для TCP серверов

```js
server.listen({
  host: 'localhost',
  port: 80,
  exclusive: true
});
```

Эта функция асинхронна. Когда сервер начинает прослушиваться, будет выведено событие ['listening'](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_listening). Последний обратный вызов параметра будет добавлен в качестве слушателя для события ['listening'](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_listening).

Следующим рассмотрим класс [net.Socket](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_socket). Этот объект является абстракцией TCP или локального сокета. Экземпляры net.Socket реализуют интерфейс дуплексного потока. Они могут быть созданы пользователем и использоваться клиентом \(через connect\(\)\) или создаваться Node.js и передаваться пользователю через событие ‘connection’ на сервере.

Экземпляры [net.Socket](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_socket) являются `EventEmitter` со следующими событиями:

* [lookup](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_lookup) — Запускается после обработки имени хоста, но до соединения. Не применимо к сокетам UNIX.
* [connect](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_connect) — Запускается, когда соединение сокета успешно установлено.
* [data](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_data) — Запускается при получении данных. Данные аргумента представляют собой буфер или строку. Кодировка данных устанавливается с помощью socket.setEncoding\(\).
* [end](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_end) — Выдается, когда другой конец сокета отправляет пакет FIN.
* [timeout](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_timeout) — Запускается, если сокет простаивает из-за неактивности. Используется только для уведомления о простое сокета. Пользователь должен вручную закрыть соединение.
* [drain](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_drain) — Запускается, когда буфер записи очищается. Может использоваться для дроссельной загрузки.
* [error](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_error_1) — Запускается при возникновении ошибки. Событие ‘close’ будет вызываться непосредственно после этого события.
* [close](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_close_1) — Запускается после полного закрытия сокета. Аргумент has\_error является логическим, указывающим, был ли сокет закрыт из-за ошибки передачи.

Рассмотрем несколько методов класса [net.Socket](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_class_net_socket):

* [new net.Socket\(\[options\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_new_net_socket_options) — Создает новый объект сокета.
* [socket.connect\(port\[, host\]\[, connectListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_connect_port_host_connectlistener) — Открывает соединение для данного сокета. Если заданы port и host, то сокет будет открыт как сокет TCP, если host опущен, предполагается localhost.
* [socket.connect\(path\[, connectListener\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_connect_path_connectlistener) — Открывает соединение для данного сокета. Если задан путь, сокет будет открыт как сокет Unix для этого пути.

* [socket.write\(data\[, encoding\]\[, callback\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_write_data_encoding_callback) — Отправляет данные на сокет. Второй параметр определяет кодировку, если задается строка – по умолчанию его значение `UTF8`.

* [socket.end\(\[data\]\[, encoding\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_end_data_encoding) — Наполовину закрывает сокет, т.е., отправляет пакет `FIN`. Возможно, что сервер будет продолжать отправлять данные. Если задано `data`, функция эквивалентна вызову `socket.write(data, encoding)` в связке с вызовом `socket.end()`.

* [socket.destroy\(\[exception\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_destroy_exception) — Обеспечивает, чтобы в этом сокете не содержалось операций ввода-вывода данных. Необходимо только в случае ошибок \(ошибка парсинга или подобная\).

* [socket.pause\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_pause) — Приостанавливает чтение данных. То есть, события '[data](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_event_data)' не будут запускаться. Используется для дросселирования загрузки.

* [socket.resume\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/net.html#net_socket_resume) — Возобновляет чтение после вызова `pause()`.

```js
const net = require("net");
const s = new net.Socket();

//server
net.createServer(function (socket) {
    console.log("connected");

    socket.on('data', function (data) {
        console.log(data.toString());

    });
}).listen(8080);

// client
s.connect(8080);
s.write('Hello');
s.end();
```

## Практическое задание {#практическое-задание}

Создайте два файла server.js и client.js. В первом файле создайте сервер, который будет слушать порт 8080 и выводить на страницу какой-то текст, а во втором файле подключитесь к этому порту и добавив обработчик события 'data' вывидете текст сос страницы в консоль. Запустите файлы в разных терминалах.

## Глоссарий {#глоссарий}

## Список литературы {#список-литературы}



