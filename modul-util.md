# Модуль Util {#модуль-util}

## **Цель** {#цель}

Ознакомится с модулем [Util](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util) и его основными методами.

## Предварительные**требования** {#предварительные-требования}

Установленны Node.js и npm

Умение работать с модулями Node.js

## Определение и методы {#определение-и-методы}

Модуль [Util](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util) предоставляет доступ к некоторым функциям утилиты. Модуль необходимо подключать с помощью команды `require(‘util’)`.

Список и определение всех методов данного модуля можно посмотреть в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/util.html).

Рассмотри методы, которые чаще всего используются на практике:

* Метод [util.inspect\(object\[, options\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_inspect_object_options) позволят вывести структуру любого объект в виде строки, даже если у этого объекта есть ссылка на самого себя. При этом, если у объекта есть свойства _inspect_, и оно равно функции, то эта функция будет вызвана и именно ее результат будет возвращен. Таким образом, поведение метода [util.inspect](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_inspect_object_options)
  несколько напоминает _toString_. И этот метод используется в console автоматически, если она хочет добавить в log какой-то объект. Пример использования:

```
var util = require('util');
var obj = {
    a: 5,
    b: 6,
    inspect: function() {
        return 123;
    }
};
obj.self = obj;
console.log( util.inspect(obj) );
```

* Метод [util.format\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_format_format_args) возвращает форматированную строку, используя первый аргумент как формат. Первый аргумент - это строка, содержащая ноль или более токенов-заполнителей. Каждый токен-заполнитель заменяется преобразованным значением из соответствующего аргумента. Если первый аргумент не является строкой, [util.format\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_format_format_args) возвращает строку, которая является конкатенацией всех аргументов, разделенных пробелами. Если методу передан только один параметр, то он будет возвращен без форматирования. Метод [util.format\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_format_format_args) также используется в console не явно.

```
var util = require('util');

var str = util.format("My %s %d %j", "code:", 123, {name: "apple"});

console.log(str); // My code: 123 {"name": "apple"}
```

* Благодаря методу [util.inherits\(constructor, superConstructor\)](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_inherits_constructor_superconstructor) мы можем сделать класс, переданный первым параметром, наследником класса, переданного во втором параметре. Метод позволяет наследовать прототипы от одного конструктора к другому. Прототипу конструктора будет присвоен новый объект, созданный из **superConstructor**. Но использовать этот метод не рекомендуется, так как благодаря ES6 его можно использование ключевых слов `class` и `extends`.

## Практическое задание {#практическое-задание}

Создайте объект который будет содержать ледующую информацию о вас: имя, фамилия, возраст, дата рождения. И выведете этот объект на консоль так, чтоб в результате вывелась строка с вашей биографией. Необходимо использовать методы [util.format](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_format_format_args) и [util.inspect](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_inspect_object_options).

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| Токены-заполнители | Специальные символы, используемые в строке для определения формата данных, которыми они в последствии будут заменены. Некоторые из них: **%s** - cтрока, **%d** - число, **%j** - JSON |

## Ссылки {#ссылки}



