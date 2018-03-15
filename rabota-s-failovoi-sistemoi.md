# Работа с файловой системой {#работа-с-файловой-системой}

## **Цель** {#цель}

В этом уроке мы научимся работать с директориями и файлами.

## Предварительные**требования** {#предварительные-требования}

Знание основ JavaScript.

Установить Node.js и npm

Умение работать с модулями Node.js

## Введение {#введение}

Для работы с файловой системой есть большое количество методов. Полный список методов можно найти в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_mkdir_path_mode_callback). Каждый метод имеют синхронную и ассинхронную форму. Для того, чтобы вызвать метод в синхронной форме, вконце имени дописывается `Sync`.

Для работы с файлами необходимо подключить встроенный модуль `required('fs')`.

## Работа с директорией {#работа-с-директорией}

Для создания директории в Node.js используется функции [fs.mkdir\(path\[, mode\], callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_mkdir_path_mode_callback) и [fs.mkdirSync\(path\[, mode\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_mkdirsync_path_mode). В качестве основного аргумента path они принимают путь, по которому необходимо создать директорию.

Чтобы выяснить существует ли уже необходимая директория можно воспользоваться методам [fs.existsSync\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_existssync_path) который возвращает true - если файл существует и false - если нет. При этом метод [fs.exists\(path, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_exists_path_callback) считается устаревшим и не рекомендуется к использованию.

Для получения полного пути к директории текущего модуля используют переменную`__dirname`

Рассмотрим небольшой пример создания директории:

```js
const fs = require('fs');
let dir = __dirname + '/field';
if (!fs.existsSync(dir)) {
    fs.mkdirSync(dir);
}
```

Здесь мы создаем директорию, если она еще не была созданна.

Для удаления директорий есть методы [fs.rmdir\(path, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_rmdir_path_callback) и [fs.rmdirSync\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_rmdirsync_path).

## Работа с файлами {#работа-с-файлами}

### 1.Запись в файл {#1запись-в-файл}

Для записи файла используются функции [fs.writeFile\(file, data\[, options\], callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_writefile_file_data_options_callback) и [fs.writeFileSync\(file, data\[, options\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_writefilesync_file_data_options), которые в качестве параметра принимает путь к файлу и записываемые данные:

```js
fs.writeFile("hello.txt","Hello!", function(error){
    if(error) throw error; // если возникла ошибка
})
```

Если файл уже существовал и в него были записанны какие-нибудь данные, то после вызова этого метода, файл будет переписан.

При этом если вы не хотите, чтобы файл был перезаписан, а только дозаписан, то необходимо воспользоваться методами [fs.appendFile\(file, data\[, options\], callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_appendfile_file_data_options_callback) и [fs.appendFileSync\(file, data\[, options\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_appendfilesync_file_data_options).

Для проверки находится ли по заданному пути файл или директория и получения информации о нем используют методы [fs.stat\(path, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_stat_path_callback) и [fs.statSync\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_statsync_path).

Пример работы

```js
fs.stat(__filename, function(err, stats) {
    console.log(stats.isFile()); // true
    console.log(stats); // информация о файле
});
```

Переменная `__filename` в данном примере возвращает полный путь к файлу модуля в котором была использована.

### 2. Чтение из файла {#2-чтение-из-файла}

Для записи файла используются функции [fs.readFile\(path\[, options\], callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_readfile_path_options_callback) и [fs.readFileSync\(path\[, options\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_readfilesync_path_options). В метод передается путь к файлу относительно корня проекта или абсолютный путь, а в качестве второго необязательного параметра указывается кодировка для получения текстового содержимого файла. На выходе получаем считанный текст. При асинхронном вызове в качестве третьего параметра передается функция обратного вызова, которая выполняется после завершения чтения.

Пример

```js
fs.readFile('test.txt', {encoding: 'utf-8'}, function(err, data) {
    if (err) throw err
    console.log(data); // выведет текс, который записан в файле test.txt
})
```

### 3. Удаление и переименование файла {#3-удаление-и-переименование-файла}

Переименовать файл можно с помощью методов [fs.rename\(oldPath, newPath, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_rename_oldpath_newpath_callback) и [fs.renameSync\(oldPath, newPath\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_renamesync_oldpath_newpath).

Первым параметром необходимо передать старый путь к файлу, а вторым новый с измененным именем.

Удаление происходит с помощью методов [fs.unlink\(path, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_unlink_path_callback) и [fs.unlinkSync\(path\)](https://nodejs.org/dist/latest-v8.x/docs/api/fs.html#fs_fs_unlinksync_path).

## Практическое задание {#практическое-задание}

С помощью изученного материала програмно создайте в корне проета папку 'folder' и добавте в нее файл 'test1.txt'. Запишите в него произвольную строку, далее прочтите этот файл, после чего переименуйте на 'test2.txt', допишите в полученный файл еще одну строку и удалите файл.

## Ссылки {#ссылки}



