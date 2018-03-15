# Модуль Buffer {#модуль-buffer}

## **Цель** {#цель}

Ознакомится с модулем [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buffer) и его основными методами.

## Предварительные**требования** {#предварительные-требования}

Установленны Node.js и npm

Умение работать с модулями Node.js

## Введение {#введение}

Язык JavaScript сам по себе содержит только строковый тип данных, и не содержит двоичного типа данных.

Но при работе с такими потоками, как поток TCP или файлами, вы должны использовать двоичные данные. Таким образом, в Node.js был определен класса [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer), который используется для создания какого-либо специального буфера для хранения двоичных данных.

Класс [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) используется для хранения данных и позволяет Node.js обрабатывать двоичные данные. Всякий раз, когда вам нужно обрабатывать операции ввода/вывода для перемещения данных Node.js будет использовать библиотеку буфера, при этом исходные данные хранятся в экземпляре класса [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer).

Экземпляры класса [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) похожи на массивы целых чисел, однако соотносятся с фиксированным, сырым распределением памяти вне движка V8. Размер для экземпляров [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) устанавливается в процессе создания и не может быть изменен.

Класс [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) является глобальным в Node.js, поэтому его можно не подключать с помощью команды`require(‘buffer’)`.

## Создание экземпляра {#создание-экземпляра}

Исторически сложилось так, что экземпляры [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) создавались с помощью конструктора`Buffer`, который распределяет возвращаемый [Buffer](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_buffer) по-разному, основываясь на заданных аргументах:

* Если первому аргументу `Buffer()` передается число \(например, `new Buffer(10)`\), то создается новый объект буфера заданного размера. До версии 8.0 память для каждого экземпляра буфера выделялась, но не заполнялась и ее приходилось заполнять вручную. Такое поведение является предусмотренным для того, чтобы повысить производительность, однако, опыт разработки показывает, что есть явное различие между созданием быстрого, но не инициализированного буфера и созданием медленного, но безопасного.
* Если первому аргументу передается строка, массив или буфер, то переданные объекты копируются в буфер.
* [`ArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer) в качестве аргумента возвращает `Buffer`, который разделяет выделенную память с данным буфером массива.

Так как поведение `new Buffer()` существенно изменяется в зависимости от задаваемого первого аргумента, приложения, которые не проверяют точно входящие аргументы, передаваемые в `new Buffer()`, или те, которые не могут должным образом инициализировать новое содержимое буфера, могут поставить под угрозу безопасность и надежность кода.

Для того, чтобы сделать процесс создания объектов более надежным и менее склонным к ошибкам были представленны новые способы создания экземпляров:

1. [Buffer.from\(array\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_from_array) возвращает новый буфер, содержащий копию существующих данных.
2. [Buffer.from\(arrayBuffer\[, byteOffset\[, length\]\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_from_arraybuffer_byteoffset_length) возвращает новые буфер, который делится выделенной памятью с `ArrayBuffer`.

3. [Buffer.from\(buffer\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_from_buffer) возвращает новый буфер, содержащий копию содержимого данного буфера.

4. [Buffer.from\(string\[, encoding\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_from_string_encoding) возвращает новый буфер, содержащий копию заданной строки.

5. [Buffer.alloc\(size\[, fill\[, encoding\]\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_alloc_size_fill_encoding) возвращает «заполненный» экземпляр буфера заданного размера. Этот метод может быть существенно медленнее, чем `Buffer.allocUnsafe(size)`, но обеспечивает то, что новосозданный буфер не будет содержать старые или потенциально незащищенные данные.

6. [Buffer.allocUnsafe\(size\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_class_method_buffer_allocunsafe_size) возвращает новый буфер заданного размера, чье содержимое должно быть инициализировано с помощью `buff.fill(0)` или полностью написано вручную.

## Запись в буфер {#запись-в-буфер}

Для записи в буфер используется метод \[buf.write\(string\[, offset\[, length\]\]\[, encoding\]\)\]\([https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html\#buffer\_buf\_write\_string\_offset\_length\_encoding](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_write_string_offset_length_encoding)\), при этом если кодировка не заданна, то по умолчанию используется 'utf-8'. Так же есть специальные методы для записи в определенном, числовом формате, например:

| Метод | Описание |
| :--- | :--- |
| [buf.writeInt8\(value, offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_writeint8_value_offset_noassert) | Записывает **value **в буфер при заданном смещении. Аргумент **value **должен быть валидным знаковым 8-битным целым числом. |
| [buf.writeDoubleLE\(value, offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_writedoublele_value_offset_noassert) | Записывает **value **в буфер при заданном смещении с указанным endian форматом \(**writeDoubleBE\(\) **записывает big-endian, **writeDoubleLE\(\) **– little-endian\). Аргумент value должен быть валидным 64-битным числом типа_double_. |
| [buf.writeFloatBE\(value, offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_writefloatbe_value_offset_noassert) | Записывает **value **в буфер при заданном смещении с указанным endian форматом \(**writeFloatBE\(\) **записывает big-endian, **writeFloatLE\(\) **– little-endian\). Аргумент **value **должен быть валидным 32-битным числом типа _float_. |
| [buf.writeIntLE\(value, offset, byteLength\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_writeintle_value_offset_bytelength_noassert) | Записывает **value **в буфер при заданном смещении и **byteLength**. Поддерживает точность до 48 бит. |
| [buf.writeUInt8\(value, offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_writeuint8_value_offset_noassert) | Записывает **value **в буфер при заданном смещении. Аргумент **value **должен быть валидным беззнаковым 8-битным целым числом. |

## Чтение из буфера {#чтение-из-буфера}

Для чтения данных из буфера в произвольной кодировке используется метод [buf.toString\(\[encoding\[, start\[, end\]\]\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_tostring_encoding_start_end). По умолчанию также используется кодировка 'utf-8'. Также есть методы для чтения уже в определенном формате.

| Метод | Описание |
| :--- | :--- |
| [buf.readInt8\(offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_readint8_offset_noassert) | Считывает **value **в буфер при заданном смещении. Аргумент **value **должен быть валидным знаковым 8-битным целым числом. |
| [buf.readDoubleLE\(offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_readdoublebe_offset_noassert) | Считывает 64-битное число типа_double_из буфера при заданном смещении с указанным endian форматом \(**readDoubleBE\(\) **возвращает big-endian, **readDoubleLE\(\) **возвращает little-endian\) |
| [buf.readFloatLE\(offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_readfloatle_offset_noassert) | Считывает 32-битное число типа_float_из буфера при заданном смещении с указанным endian форматом \(**readFloatBE\(\) **возвращает big-endian, **readFloatLE\(\) **возвращает little-endian\) |
| [buf.readIntBE\(offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_readintbe_offset_bytelength_noassert) | Считывает количество байт byteLength из буфера при заданном смещении и интерпретирует результат как пару взаимозаменяемых значений. Подерживает точность до 48 бит. |
| [buf.readUInt8\(offset\[, noAssert\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_readuint8_offset_noassert) | Считывает 8-битное беззнаковое число типа integer из буфера при заданном смещении. |

Для определения длины буффера используется функция [buf.length](https://nodejs.org/dist/latest-v8.x/docs/api/buffer.html#buffer_buf_length)

## Практическое задание {#практическое-задание}

Создайте буффер, передав числовой массив. С помощью метода [buf.writeInt8](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#) допишите значения и вывидите с помощью [buf.readInt8](https://www.gitbook.com/book/nodejs-junior-developer-traini/super-book-of-node-js/edit#) полученный буфер на консоль.

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Движок V8** | Движок JavaScript с открытым программным кодом, распространяемый по лицензии BSD. Разработан датским отделением компании Google. |
| **Big-endian** | Порядок от старшего к младшему \(англ. big-endian — большим концом\) соответствует привычному порядку записи арабских цифр, например, число сто двадцать три было бы записано при таком порядке как 123. В этом же порядке принято записывать байты в технической и учебной литературе, если другой порядок явно не обозначен. Этот порядок является стандартным для протоколов TCP/IP, он используется в заголовках пакетов данных и во многих протоколах более высокого уровня, разработанных для использования поверх TCP/IP. Поэтому порядок байтов от старшего к младшему часто называют «сетевым порядком байтов» \(англ. network byte order\). |
| **Little-endian** | Порядок от младшего к старшему \(англ. little-endian — малым концом\) - Это обратный порядок по отношению к привычному порядку записи арабских цифр, например, число сто двадцать три было бы записано при таком порядке как 321. Этот порядок записи принят в памяти персональных компьютеров с процессорами архитектуры x86, в связи с чем иногда его называют интеловский порядок байтов. |

## Ссылки {#список-литературы}



