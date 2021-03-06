Параллельное выполнение асинхронных функций

В этом задании необходимо реализовать функцию parallel, которая выполняет асинхронные операции параллельно.

Функция parallel принимает два аргумента: массив операций и результирующий callback.

var parallel = require('./index.js);

parallel(
    [
        function (next) { /* асинхронная операция 1 */ },
        function (next) { /* асинхронная операция 2 */ },
        // ...
    ],
    function(err, result) {
        // обработка результата выполнения операций
    }
)

Условия
Операции
Каждая операция – это синхронная или асинхронная функция. На вход ей приходит callback-функция next. По завершению работы операция вызывает функцию next либо с результатом выполнения, либо с ошибкой.

Если операция успешно завершилась, то вызывается callback-функция с первым аргументом null, а вторым – результатом выполнения:

function (next) {
    setTimeout(function () {
        next(null, 'data');
    }, 1000);
}

Если операция завершается с ошибкой, то callback-функция вызывается с единственным аргументом – возникшей ошибкой:

function (next) {
    setTimeout(function () {
        next(new Error('Что-то пошло не так'));
    }, 1000);
}

Результирующий callback
Результирующий callback (второй параметр функции parallel) вызывается по окончанию выполнения всех операций или при возникновения ошибки в одной из них.

Если все операции завершились успешно, в callback передаётся первым аргументом null, а вторым – массив с результатами выполнения операций. Порядок данных должен соответствовать порядку операций в массиве, а не их вызову.

Если не переданы операции (передан пустой массив), то результатом выполнения должен быть пустой массив.

Если хотя бы одна из операций закончилась ошибкой, то callback-функция вызывается с произошедшей ошибкой. Если несколько операцией завершились ошибкой, то callback будет вызван с первой из них.

Важно. Callback вызывается ровно один раз: либо с первой пойманной ошибкой, либо с результатами выполнения операций.

Ограничения
Гарантируется корректность вызова функции parallel и передача операций в правильном формате.
Гарантируется, что функция next всегда вызывается в операциях.
Пояснения
В данном задании используется термин callback. При этом подходе в асинхронную функцию передается другая функция, которую исходная должна вызвать по завершению. Это способ асинхронной функции сообщить о своем завершении.

setTimeout(function callback() {
    // Функция setTimeout вызовет нашу callback-функцию, когда завершит отсчет 1000 ms
}, 1000);

По условию задачи мы имеем дело с двумя callback-функциями: 1. Функция next — передается в операцию, которая по своему завершению должна вызвать функцию next. 2. Вторая функция используется внутри операции, чтобы узнать о завершении работы setTimeout.

function operation(next) {
  setTimeout(function callback() {
      // Данная функция вызывается по завершению setTimeout
      // Мы вызываем функцию next, чтобы сообщить о завершении работы функции operation
      next(null, 'data');
  }, 1000);
}

В данном примере функция setTimeout сама вызывает callback, а для функции operation callback-функцию (next) мы вызываем вручную. Использование функции operation практически не отличается от использования функции setTimeout:

// Вызываем функцию operation, в которую передаем callback
operation(function callback(err, data) {
    // Данная функция будет вызвана по завершению асинхронной функции operation
})

Подсказка
Сохранение порядка можно сделать через замыкания, для каждой операции создавая функцию next:

