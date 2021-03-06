# События в Node.js {#события-в-nodejs}

## **Цель** {#цель}

Мы посмотрим, как работать с событиями в Node.js.

## Предварительные**требования** {#предварительные-требования}

Знание основ JavaScript.

Установить Node.js и npm

Умение работать с модулями Node.js

## Событие это

**Событие** — это сообщение, которое возникает в различных точках [исполняемого кода](https://ru.wikipedia.org/wiki/Машинный_код) при выполнении определённых условий.

События предназначены для того, чтобы иметь возможность предусмотреть реакцию программного обеспечения.

Для решения поставленной задачи создаются обработчики событий: как только [программа](https://ru.wikipedia.org/wiki/Компьютерная_программа) попадает в заданное состояние, происходит событие, посылается сообщение, а обработчик перехватывает это сообщение. В общем случае в обработчик не передаётся ничего, либо передаётся [ссылка](https://ru.wikipedia.org/wiki/Ссылка_%28программирование%29) на объект, инициировавший \(породивший\) обрабатываемое событие. В особых случаях в обработчик передаются значения некоторых переменных или ссылки на какие-то другие [объекты](https://ru.wikipedia.org/wiki/Объект_%28программирование%29), чтобы обработка данного события могла учесть контекст возникновения события.

Самое простое событие — это событие, сообщающее о начале или о завершении некоторой [процедуры](https://ru.wikipedia.org/wiki/Подпрограмма#Виды_подпрограмм). Событие, по сути, сообщает об изменении состояния некоторого объекта. Наиболее наглядно события представлены в пользовательском [интерфейсе](https://ru.wikipedia.org/wiki/Интерфейс), когда каждое действие пользователя порождает цепочку событий, которые, затем обрабатываются в [приложении](https://ru.wikipedia.org/wiki/Прикладное_программное_обеспечение). \[1\]

## Введение в события {#введение-в-события}

Большая часть основного API-интерфейса Node.js построена вокруг асинхронной  архитектуры, управляемой событиями, в которой определенные типы объектов \(называемые «emitters»\) периодически генерируют события, вызывая при этом функции-обработчики \(«listeners»\).

Все объекты, которые генерируют события, являются экземплярами класса [EventEmitter](https://nodejs.org/docs/latest-v8.x/api/events.html#events_class_eventemitter). Эти объекты используют функцию [emitter.on\(eventName, listener\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_on_eventname_listener), чтобы подключить обработчик одного или нескольких событий к объекту.

Когда объект [EventEmitter](https://nodejs.org/docs/latest-v8.x/api/events.html#events_class_eventemitter) генерирует событие, все обработчитки, связанные с этим конкретным событием, вызываются _синхронно,_то есть в том порядке, в котором были назначены \(что отличает вызов событий в Node.js от вызова событий в браузере\). Любые значения, возвращаемые вызываемыми слушателями, _игнорируются _и будут отброшены.

Для генерации событий используют метод [emitter.emit\(eventName\[, ...args\]\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_emit_eventname_args), в который передается название события. Также в качестве второго параметра в функцию`emit()`можно передавать некоторый объект, который передается в функцию обработчика события.

Рассмотрим пример создания и обработки события.

```js
const EventEmitter = require('events');
class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();
myEmitter.on('event', (req) => {
    console.log(req.data);
});
myEmitter.emit('event', {data: 'My event'});
```

В этом примере создается событие `'event'` и к нему добавляется обработчик, который выводит данные, переданные в событие.

При вызови событие `'error'` будет автоматически выбрасываться исключение, если не был подключен обработчик этого события.

С помощью метода [emitter.once\(eventName, listener\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_once_eventname_listener), можно зарегистрировать обработчик событий, который вызывается всего один раз для конкретного события. После того, как событие генерируется, обработчик становится незарегистрированными, а затем вызывается.

По умолчанию, можно зарегистрировать максимум **10 **обработчиков для любого отдельного события.

Это ограничение может быть изменено для отдельных экземпляров класса EventEmitter с использованием метода [emitter.setMaxListeners\(n\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_setmaxlisteners_n). Чтобы изменить значение по умолчанию для всех экземпляров `EventEmitter`, может быть использовано свойство [EventEmitter.defaultMaxListeners](https://nodejs.org/docs/latest-v8.x/api/events.html#events_eventemitter_defaultmaxlisteners).

Соблюдайте осторожность при установке [EventEmitter.defaultMaxListeners](https://nodejs.org/docs/latest-v8.x/api/events.html#events_eventemitter_defaultmaxlisteners), поскольку это изменение влияет на все экземпляры класса EventEmitter, в том числе созданные до применения этого изменения . Тем не менее, вызов [emitter.setMaxListeners\(n\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_setmaxlisteners_n) по-прежнему имеет больший приоритет над [EventEmitter.defaultMaxListeners](https://nodejs.org/docs/latest-v8.x/api/events.html#events_eventemitter_defaultmaxlisteners).

Обратите внимание, что это не жесткое ограничение. Экземпляр EventEmitter позволит добавление большего количества обработчиков, , но выдаст предупреждение о том, что возможна утечка памяти.

Для любого отдельного экземпляра [EventEmitter](https://nodejs.org/docs/latest-v8.x/api/events.html#events_class_eventemitter), могут быть использованы методы [emitter.getMaxListeners\(\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_getmaxlisteners) и [emitter.setMaxListeners\(n\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_setmaxlisteners_n) чтобы временно избежать этого предупреждения.

Для того чтобы удалить подписчика используется метод [emitter.removeListener\(eventName, listener\)](https://nodejs.org/docs/latest-v8.x/api/events.html#events_emitter_removelistener_eventname_listener).

## Практическое задание {#практическое-задание}

Сгенерируйте 2 события и добавте к каждому по 2-3 обработчика, при этом для одного с использование метода once\(\), а для второго on\(\). Посмотрите как отработает код.

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Архитектура, управляемая событиями **\(англ. event-driven architecture\) | Шаблон архитектуры программного обеспечения, позволяющим создание, определение, потребление и реакцию на события. |

## Ссылки {#ссылки}

1. Определение термина события - [https://ru.wikipedia.org/wikii/Событие\_\(объектно-ориентированное\_программирование\)](https://ru.wikipedia.org/wiki/Событие_%28объектно-ориентированное_программирование%29)



