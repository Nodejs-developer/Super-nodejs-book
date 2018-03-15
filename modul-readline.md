# Модуль Readline {#модуль-readline}

## **Цель** {#цель}

Ознакомится с модулем [Readline](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_readline) и его основными методами.

## Предварительные**требования** {#предварительные-требования}

Установленны Node.js и npm

Умение работать с модулями Node.js

Знание потоков

## Определение и использование {#определение-и-использование}

Модуль [Readline](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_readline) предоставляет интерфейс для чтения данных из [Readable потока](https://nodejs-junior-developer-traini.gitbooks.io/super-book-of-node-js/content/potoki.html) \(например, process.stdin\) по одной строке за раз. Для его подключения необходимо выполнить команду `require('readline')`.

Экземпляры класса [readline.Interface](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_class_interface) построены с использованием метода [readline.createInterface\(options\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_readline_createinterface_options). Каждый экземпляр связан с одним входным \(`input`\) [Readable](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html#stream_readable_streams) потоком и одним выходным \(`output`\) [Writable](https://nodejs.org/dist/latest-v8.x/docs/api/stream.html#stream_writable_streams) потоком. Поток `output` используется для вывода на экран приглашения ввода данных пользователем, которые поступают и считываются с `input` потока.

В процессе работы модуля генерируются несколько событий:

* Событие 'close' вызывается если вызван метод [rl.close\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_close), закончился входной поток или была нажата одна из комбинаций клавиш &lt;ctrl&gt;-D или &lt;ctrl&gt;-C
* Событие 'line' выводится всякий раз, когда входной поток получает конец строки \(`\n`, `\r` или `\r\n`\)
* Событие ‘pause’ вызывается, когда входной поток был переостановлен
* Событие ‘resume’ генерируется, когда возобновляется поток input.
* Событие ‘SIGCONT’ генерируется, если процесс Node.js был предварительно перемещен в фоновый режим через &lt;ctrl&gt;-Z и при это поток input не был преостановлен
* Событие 'SIGINT' генерируется, когда стрим input получает ввод &lt;ctrl&gt;-C, известный, как SIGINT

Класса [readline.Interface](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_class_interface) содержит несколько методов. Основные приведены в таблице ниже, с остальными ,как и сметодами самого модуля можно ознакомится в [документации](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html)

| Метод | Описание |
| :--- | :--- |
| [rl.close\(\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_close) | Закрывает экземпляр readline.Interface |
| [rl.prompt\(\[preserveCursor\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_prompt_preservecursor) | Выводит настроенный prompt на новую строку в output и ожидает дальнейших действий от пользователя |
| [rl.question\(query, callback\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_question_query_callback) | Отображает **query **в output, ожидает ответ от пользователя, затем вызывает функцию **callback**, которая передает предоставленные пользователем данные в качестве первого аргумента. |
| [rl.setPrompt\(prompt\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_setprompt_prompt) | Устанавливает prompt, который будет записан в output каждый раз, когда вызывается rl.prompt\(\). |
| [rl.write\(data, \[, key\]\)](https://nodejs.org/dist/latest-v8.x/docs/api/readline.html#readline_rl_write_data_key) | Метод rl.write\(\) записывает **data **либо последовательность ключей, определенных **key**. Если **key **задан, **data **игнорируется. |

Пример использования:

```js
const rl = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

r.question("What do you think of node.js?", function(answer) {

  console.log("Thank you for your valuable feedback.");
  r.close();
});
```

## Практическое задание {#практическое-задание}

Выведите в консоль два вопроса и после ответов на оба, вывидите полученную информцию.

## Глоссарий {#глоссарий}

| Термин | Значение |
| :--- | :--- |
| **Prompt** | В контексте данного урока это понятие означает знак или текст, который служит символом начала команды в интерфейсе командной строки |

## Сcылки {#список-литературы}



