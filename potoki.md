# Потоки {#потоки}

## **Цель** {#цель}

Рассмотреть использоваия потоков в Node.js

## Предварительные**требования** {#предварительные-требования}

Знание основ JavaScript.

Установить Node.js и npm

## Потоки это

**Поток **\(\_en.stream\) -  абстрактная последовательность инструкций или данных, привязанная к соответствующему  имени потока.

Потоки являются удобным унифицированным программнымин терфейсом для чтения или записи файлов \(в том числе специальных, в частности связанных с устройством\),сокетов и передачи данных между процессами.

Поддержка потоков включена в большинство языков программирования и современных операционных систем.

При запуске процесса ему предоставляются предопределённые стандартные потоки.

Возможность [перенаправления](https://dic.academic.ru/dic.nsf/ruwiki/477360) потоков позволяет связывать различные программы, и придаёт системе гибкость, являющуюся частью [философии Unix](https://dic.academic.ru/dic.nsf/ruwiki/69810).

## Потоки в Node.js

Если мы работаем с довольно большим файлом, то node будет буферезировать весь файл при каждом запросе, вследствии чего программа начнемт потреблять большое количество памяти, особенно при большом количестве подключенных пользователей с медленными каналами связи и при этом пользователю придется ждать пока весь файл не будет считан в память на сервере перед отправкой.  Более оптимальным способом для чтения, записи а также для отправки на клиент файлов будет использование потоков. При использовании потоков файл доставляется по частям по мере чтения его с диска.

Существует 4 вида потоков:

* на чтение \(readable\) - потоки, из которых можно считывать данные
* на запись \(writeable\) - потоки, в которые могут быть записаны данные
* дуплексные \(duplex\) - потоки, которые являются читаемыми и записываемыми
* трансформирующие \(transform\) - дуплексные потоки, которые могут изменять или преобразовывать данные по мере их записи и чтения

Отметим, что при работе с сервером, параметры \(req, res\) являются потоками.

Любой из потоков может использовать метод `.pipe()`. Метод `readable.pipe()` присоединяет записываемый поток к читаемому, заставляя Readable поток автоматически выталкивать все свои данные в прикрепленный Writable. Поток данных при этом автоматически регулируется, таким образом, чтобы поток Writable не был перегружен более быстрым потоком Readable.

Модуль [`stream`](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html#stream_stream) подключается с помощью команды `require( 'stream' ).`

Всем пользователям Node.js важно понимать, как работают стримы, и модуль [`stream`](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html#stream_stream) сам по себе является самым удобным для разработчиков, которые создают новые типы экземпляров стримов. Разработчики, которые изначально изучили объекты стримов, редко будут нуждаться \(если вообще будут\) в прямом использовании модуля`stream`.

### Потоки чтения \(readable\) {#потоки-чтения-readable}

Все [Readable](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_class_stream_readable) потоки реализуют интерфейс, определенный классом `stream.Readable`.

[Readable](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_class_stream_readable) потоки эффективно работают в одном из двух режимов: flowing и paused.

В flowing \(потоковом\) режиме данные считываются из базовой системы автоматически и предоставляются в приложение как можно быстрее, используя события через интерфейс EventEmitter.

В paused\(приостановленном\) режиме метод [`stream.read()`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_read_size) должен быть явно вызван для чтения фрагментов данных из потока.

Изначально для Readable потоков установлен paused режим. Для того чтобы перейти в потоковый режим, можно выполнить следующие действия:

* Добавить обработчик события [`'data'`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_event_data);
* Вызвать метод [`stream.resume()`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_resume);
* Вызвать метод [`stream.pipe()`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_pipe_destination_options) для передачи данных в поток записи

Для обратно перехода , используйте один из следующих способов:

* Если не привязки к другому потоку с помощью метода `pipe()`, вызовите метод [`stream.pause()`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_pause);
* Если есть, то для их удаления используйте метод [`stream.unpipe()`](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_unpipe_destination)

Для создания потока чтения используется метод [fs.createReadStream\(path\[, options\]\)](https://nodejs.org/docs/latest-v8.x/api/fs.html#fs_fs_createreadstream_path_options). Для его использования необходимо подключить модуль '[fs](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_file_system)'

Рассмотрим небольшой пример, допустим нас есть файл _test.html_, который бы мы хотели показать на странице.

```js
var http = require('http');
var fs = require('fs');

http.createServer(function (req, res) {
    var stream = fs.createReadStream(__dirname + '/test.html'); // Создаем поток чтения файла
    stream.pipe(res); //выводим на страницу считанные данные
}).listen(8000);
```

Если у нас нет готового файла и мы просто хотим создать пустой поток Readable и записать в него информацию, то нам потребуется подключить модуль '[stream](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html#stream_stream)'.

Добавление данных в поток реализуется с помощью метода [readable.push\(chunk\[, encoding\]\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_push_chunk_encoding), в который передаются данные.

Пример:

```js
const { Readable } = require('stream');
const rs = new Readable;

rs.push('Hello, world\n');
rs.push('Goodbye');
rs.push(null);
rs.pipe(process.stdout); //вывод текста в консоль
```

Тут `rs.push(null)` сообщает потребителю, что `rs` закончил вывод данных.

Когда вы посылаете с помощью `.push()` данные в поток на чтение, они буферизируются до тех пор пока потребитель не будет готов их прочитать.

Тем не менее, в большинстве случаев будет лучше если мы не будем их буферизировать совсем, вместо этого будем генерировать их только когда данные запрашиваются потребителем.

Мы можем посылать данные кусками, определив функцию [readable.\_read\(size\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_read_size_1):

```js
const { Readable } = require('stream');
const rs = new Readable;

rs._read = function () {
    rs.push('Hello, world\n');
    rs.push('Goodbye');
    rs.push(null);
};
rs.pipe(process.stdout); //вывод текста в консоль
```

Также важно отметить что при открытии потока чтения, когда данные становятся доступными, возникает событие `'readable'`, и вы можете вызвать [readable.read\(\[size\]\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_readable_read_size) чтобы получить следующую порцию данных из буфера.

Когда поток завершится, `.read()` вернет null, потому что не останется доступных для чтения байтов.

```js
process.stdin.on('readable', function () {
    var buf = process.stdin.read();
    console.dir(buf);
});
```

### Потоки записи \(writable\) {#потоки-записи-writable}

Все [Writable](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_class_stream_writable) потоки реализуют интерфейс, определенный классом stream.Writable.

Хотя конкретные экземпляры потоков Writable могут различаться различными способами, все записываемые потоки следуют одному и тому же основному шаблону использования, как показано в примере ниже:

```js
const myStream = getWritableStreamSomehow();
myStream.write('some data');
myStream.write('some more data');
myStream.end('done writing data');
```

Чтобы передать данные в поток на запись - вызовите [writable.write\(chunk\[, encoding\]\[, callback\]\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_writable_write_chunk_encoding_callback), где chunk это набор данных которые вы хотите записать.

Если вы хотите сообщить что вы закончили запись - вызовите [writable.end\(\[chunk\]\[, encoding\]\[, callback\]\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_writable_end_chunk_encoding_callback).

Для прерывания записи используют метод [writable.destroy\(\[error\]\)](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_writable_destroy_error).

### Трансформирующий поток {#трансформирующий-поток}

[Transform](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_class_stream_transform) потоки это частный случай [Duplex](https://nodejs.org/docs/latest-v8.x/api/stream.html#stream_class_stream_duplex) потоков \(в обоих случаях они могут использоваться как для записи, так и чтения\). Разница в том, что в случае трансформации отдаваемые данные так или иначе зависят от того что подается на вход.

Примеры включают потоки [zlib](https://nodejs.org/docs/latest-v8.x/api/zlib.html) или [crypto](https://nodejs.org/docs/latest-v8.x/api/crypto.html) потоки, которые сжимают, шифруют или дешифруют данные.

Также можно создать свой трансформирующий поток, как экземпляр класса [Transform](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#).

Например:

```js
const { Transform } = require('stream');

const upperCaseTr = new Transform({
    transform(chunk, encoding, callback) {
        this.push(chunk.toString().toUpperCase());
        callback();
    }
});
process.stdin.pipe(upperCaseTr).pipe(process.stdout);
```

В этом примере мы получаем с консоли текст, меняем ему регистр и записываем обратно в консоль.

#### process {#process}

##### [process.stdin](https://nodejs.org/docs/latest-v8.x/api/process.html#process_process_stdin) {#processstdin}

Поток на чтение содержит стандартный системный поток ввода для вашей программы.

Если process.stdin указывает на терминал\(проверяется вызовом [`tty.isatty()`](http://nodejs.org/docs/latest/api/tty.html#tty_tty_isatty_fd)\), тогда входящие данные будут буферизироваться построчно. Вы можете выключить построчную буферизацию вызвав `process.stdin.setRawMode(true)`. Однако, имейте ввиду что в этом случае обработчики системных нажатий \(таких как `^C` и `^D`\) будут удалены.

##### [process.stdout](https://nodejs.org/docs/latest-v8.x/api/process.html#process_process_stdout) {#processstdout}

Поток на запись, содержащий стандартный системный вывод для вашей программы. Посылайте туда данные, если вам нужно передать их в stdout.

##### [process.stderr](https://nodejs.org/docs/latest-v8.x/api/process.html#process_process_stderr) {#processstderr}

Поток на запись, содержащий стандартный системный вывод ошибок для вашей программы. Посылайте туда данные, если вам нужно передать их в stderr.

## Практическое задание {#практическое-задание}

Создать два потока чтения и записи на основе файлов read.txt и write.txt. Необходимо переместить данные с файла для чтения в файл записи, так, чтобы записались только слова, в которых встречается буква 'a'.

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Буферизация** | Метод организации обмена, в частности, ввода и вывода данных в компьютерах и других вычислительных устройствах, который подразумевает использование буфера для временного хранения данных. |
| **Стандартный системный поток ввода/вывода** | Потоки ввода-вывода с заранее оговоренным смыслом и направлением; также заранее оговорены \(стандартизованы\) их метки \(номера\). Иногда их еще называют «**терминалы**» \(terminal\) или «**консоли**» \(console\) |

## Ссылки {#список-литературы}



