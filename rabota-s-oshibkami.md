# Работа с ошибками {#работа-с-ошибками}

### Цель {#цель}

Научиться отслеживать и обрабатывать ошибки в синхронных и асинхронных алгоритмах Node.js. Научиться генерировать ошибки.

### Начальные требования {#начальные-требования}

Базовые знания Javascript и Node.js

## Ошибки в Node.js

Приложение, работающее в Node.js в общем может сталкиватся с четырьмя категориями ошибок:

* Стандартные ошибки JavaScript, такие как:
  * [&lt;EvalError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/EvalError): возникает при неуспешном вызове `eval()`.
  * [&lt;SyntaxError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SyntaxError): возникает в ответ на неправильный JavaScript синтаксис.
  * [&lt;RangeError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RangeError): возникает если значение выходит за ожидаемый диапазон.
  * [&lt;ReferenceError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError): возникает при использовании неопределенных переменных.
  * [&lt;TypeError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError): возникает при передаче аргументов неправильных типов.
  * [&lt;URIError&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/URIError): возникает если глобальная функция обработки URI используется неправильно.
* Системные ошибки, вызываемые операционной системой - такие как попытка открыть файл, который не сущетсвует, попытка отправит данный на закрытый сокет и т. д.
* Определенные пользователем ошибки, вызываемые кодом приложения.
* Assertion Errors - специальный класс ошибок, которые могут возникнуть, если Node.js обнаруживает ошибочное нарушение логики, которое не должно произойти. Ошибки такого класса вызываеются обычно модулем `assert.`

Все ошибки JavaScript и системные, вызываемые Node.js наследуются или являются экземплярами стандартного класс JavaScript[&lt;Error&gt;](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error), и гарантированно содержат как минимум свойства доступные в этом классе.

## Распространение и перехват ошибок {#распространение-и-перехват-ошибок}

Node.js поддерживает несколько механизмов распространения и обработки ошибок, возникающих при работе приложения. То, как эти ошибки сообщаются и обрабатывается, зависит полностью от типа ошибки и от вызываемого API.

Все ошибки JavaScript обрабатываются как исключения, которые немедленно генерируют и выбрасывают ошибку используя стандартрый механизм JavaScript `throw`. Они обрабатываются с использованием [`try / catch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) из языка JavaScript.

```js
// Throws with a ReferenceError because z is undefined
try {
  const m = 1;
  const n = m + z;
} catch (err) {
  // Handle the error here.
}
```

Любое использование JavaScript `throw`вызовет исключение, которое должно быть обработано с помощью `try / catch`, иначе процесс Node.js немедленно завершится.

За небольшим исключением синхронные API \(любые блокирующие методы, которые не принимают колбэк, такие как [`fs.readFileSync`](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_readfilesync_path_options)\) используют `throw` для сообщения об ошибке.

Ошибки, возникающие в асинхронных API могут сообщбаться множеством способов.

* Большинство асинхронных методов, принимающих колбэк функцию, принимают также объект ошибки, передаваемый как первый параметр в эту функцию. Если этот перый аргумент не равен `null`, и является экземпляром `Error`, значит произошла ошибка, которая должна быть обработана.\);

```js
  const fs = require('fs');
  fs.readFile('a file that does not exist', (err, data) => {
    if (err) {
      console.error('There was an error reading the file!', err);
      return;
    }
    // Otherwise handle the data
  });
```

* Если асинхронный метод вызван объектом, который является `EventEmitter`, ошибки могут быть перенаправлены в событие `'error'` этого объекта.

```js
const net = require('net');
const connection = net.connect('localhost');

// Adding an 'error' event handler to a stream:
connection.on('error', (err) => {
  // If the connection is reset by the server, or if it can't
  // connect at all, or on any sort of error encountered by
  // the connection, the error will be sent here.
  console.error(err);
});

connection.pipe(process.stdout);
```

* Небольшое количтество типично асинхронных методов в Node.js API могут использовать механизм `throw`
  для вызова исключений, которые должны быть обработаны с использованием `try / catch`. Не существует исчерпывающего списка этих методов, следует обращаться к документации каждого метода для определенифя подходящего механизма обработки ошибок.

Использование события `'error'` является наиболее общим для [stream-based](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html) и [event emitter-based](https://nodejs.org/dist/latest-v8.x/docs/api/events.html#events_class_eventemitter) API, которые сами по себе представляю срию асинхронных операций во времени \(в отличии от единственной операции, которая может пройти или упасть\).

Для всех объектов типа `EventEmitter`, если обработчик события `'error'` не указан, будет выброшена ошибка, что завставит процесс Node.js сообщить о необработанном исключении и прерваться, за исключением случаев, когда подключен модуль [`domain`](https://nodejs.org/dist/latest-v8.x/docs/api/domain.html) или зарегистрирован обработчик события типа [`process.on('uncaughtException')`](https://nodejs.org/dist/latest-v8.x/docs/api/process.html#process_event_uncaughtexception).

```js
const EventEmitter = require('events');
const ee = new EventEmitter();

setImmediate(() => {
  // This will crash the process because no 'error' event
  // handler has been added.
  ee.emit('error', new Error('This will crash'));
});
```

Генерируемые таким образом ошибки не могут быть перехвачены с использованием`try / catch`, поскольку они выброшены после того, как вызывающий код окончил работу.

## Колбэки в стиле Node.js {#колбэки-в-стиле-nodejs}

Большинство асинхронных методов представленный в базовом API Node.js следуют простому шаблону, называемому "колбэк в стиле Node.js". По этому шаблону колбэк функция передается в метод в качестве аргумента. Когда опреация завершается, или же возникает ошибка, вызывается эта функция, с объектом ошибки \(при наличии\), передаваемом как первый аргумент. Если ошибок не произошло, первый аргумент передается равным `null`.

```js
const fs = require('fs');

function nodeStyleCallback(err, data) {
  if (err) {
    console.error('There was an error', err);
    return;
  }
  console.log(data);
}

fs.readFile('/some/file/that/does-not-exist', nodeStyleCallback);
fs.readFile('/some/file/that/does-exist', nodeStyleCallback);
```

Механизм JavaScript `try / catch` не может быть использован для прехвата ошибок, генерируемых асинхронными методами API. Распространенная ошибка новичков - попытка использовать `throw` внутри колбека в стиле Node.js:

```js
// THIS WILL NOT WORK:
const fs = require('fs');

try {
  fs.readFile('/some/file/that/does-not-exist', (err, data) => {
    // mistaken assumption: throwing here...
    if (err) {
      throw err;
    }
  });
} catch (err) {
  // This will not catch the throw!
  console.error(err);
}
```

Это не будет работать, поскольку колбэк-функция передаваемая в `fs.readFile()` вызывается асинхронно. В то время, когда коолбек вызван, окружающий код \(включая `try { } catch (err) { }`\) уже окончил работу. Выбрасываение ошибки внутри колбэка может завалить процесс Node.js в большинстве случаев**.**

## Класс Error {#класс-error}

Родной объект JavaScript Error не содержит информации об обстоятельствах, при которых произошла ошибка и ее причину. Этот объект захватыевает стек вызова с деталями где именно в коде произошла ошибка, и может содержать текст с описанием ошибки.

Все ошибки JavaScript и системные, вызываемые Node.js наследуются или являются экземплярами класса Error.+

Подробное описание свойств и методов класса Error, а также его подклассов, см. в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/errors.html#errors_class_error).

## Практическое задание {#практическое-задание}

1. Составить синхронный алгоритм, генерирующий ошибку и написать код, обрабатывающий её.
2. Составить асинхронный алгоритм, генерирующий ошибку, и написать код, обрабатывающи её.
3. Составить алгоритм с генерацией событий, генерирующий ошибку, и написать код, обрабатывающий её.



