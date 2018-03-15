# Работа с путями {#работа-с-путями}

## **Цель** {#цель}

Ознакомится с модулем [Path](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path) и его основными методами.

## Предварительные**требования** {#предварительные-требования}

Установленны Node.js и npm

## Описание модуля и методы {#описание-модуля-и-методы}

Модуль [Path](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path) обеспечивает способ работы с каталогами и путями файлов. Для его подключения в коде необходимо выполнить команду `require('path')`.

Работа по умолчанию для модуля `path` зависит от операционной системы, на которой запущено приложение Node.js. В частности, при работе в операционной системе Windows модуль path будет предполагать, что используются пути в стиле Windows.

Данный модуль содержит несколько методов для работы с путями. Основные приведены в таблице ниже, с остальными можно ознакомится в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/path.html)

| Метод | Описание |
| :--- | :--- |
| [path.basename\(path\[, ext\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path_basename_path_ext) | Возвращает последнюю часть пути \(после последнего '**/**'\) |
| [path.dirname\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path_dirname_path) | Bозвращает имя каталога пути |
| [path.extname\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path_extname_path) | Возвращает расширение файла по пути, заданному в параметре |
| [path.isAbsolute\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path_isabsolute_path) | Возвращает true, если путь является абсолютным путем, иначе false |
| [path.parse\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/path.html#path_path_parse_path) | Возвращает объект со свойствами: root, dir, base, ext, name |

## Практическое задание {#практическое-задание}

Для пути './home/user/dir/file.js' с помощью представленных выше методов вывидете имя файла, расширение файла, директорию, в которой файл находится и является ли путь абсолютным.

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Абсолютный путь** | Абсолютный \(или полный\) путь начинается с буквы диска, за которой следует двоеточие, например, D:. |
| **Расширение файла** | Последовательность символов, добавляемых к имени файла и предназначенных для идентификации типа \(формата\) файла. |

## Ссылки {#ссылки}

[          
](https://nodejs-junior-developer-traini.gitbooks.io/super-book-of-node-js/content/rabota-s-modulyami-v-nodejs.html)

