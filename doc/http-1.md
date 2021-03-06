# HTTP-сервер своими руками
Ограничения:
* запрещено использовать модуль http
* запрещено пользоваться синхронными версиями функций (с **Sync**) на конце


## Уровень сложности 0
(Этот уровень сложности задания присылать не надо - вы получите "расширенную версию задания" после второй лекции по потокам во вторник, ее уже надо будет присылать и будет описан требуемый формат)

Реализовать HTTP-сервер, который слушает порт 3000, или указанный в переменной окружении PORT и умеет следующее:

1. Выводит на консоль все полученные заголовки в формате ключ-значение
2. При обращении допустим к `http://localhost:3000/foo.html` если в папке `static` проекта есть файл `foo.html` - он будет корректно работать
3. Скрипт корректно работает для бинарных файлов (к примеру картинок)
4. Скрипт корректно возвращает `404` если файл отсутствует в файловой системе
5. Скрипт корректно возвращает `400` если файл недоступен на чтение (привет правам доступа файловой системы)

Спецификация протокола HTTP для тех кто с ней не знаком: 
https://habrahabr.ru/post/215117/

## Уровень сложности 1

Код, который демонстрирует использование нашего HTTP-сервера:
https://gist.github.com/xanf/fb7f1018fa7700ecf510f99c2029e0cb 

* Модуль http должен реализовывать одну функцию createServer, которая возвращает экземпляр класса HttpServer, объвленный в файле `./http.js`
* Класс HttpServer должен иметь метод listen, получающий на вход порт, и стартующий сервер на заданном порту
* Когда к серверу подключается HTTP-клиент, сервер должен сгенерирова ть событие `request`. Аргументами события являются объекты req, res являющиеся экземплярами HttpRequest и HttpResponse (смотри далее) соответственно
Событие request не должно вызываться ДО ТОГО, как были получены все заголовки запроса
* Класс HttpRequest должен быть ReadableStream и содержать в себе ВСЕ ТЕЛО http-запроса (минус заголовки)
* Класс HttpRequest должен иметь поля headers(объект, ключами являются имена заголовков, значениями - значения заголовков), поле method (метод, с которым выполняется HTTP-запрос), поле url (адрес http-запроса)
Класс HttpResponse должен быть WritableStream. Все данные, отправленные в этот WritableStream должны быть отправлены клиенту
* Класс HttpResponse должен представлять методы setHeader(headerName, value) - осуществляет установку заголовков (но не отправляет их) и writeHead (осуществляет отправку HTTP-статуса запроса)
* Класс HttpResponse должен осуществлять отправку заголовков перед отправкой тела запроса клиенту.
* Вызов setHeader после того как заголовки были отправлены должен генерировать ошибку
* У объекта класса HttpResponse есть метод setStatus(code), позволяющий задать HTTP-код ответа
* Вызов setStatus после того как заголовки были отправлены должен генерировать ошибку
* По умолчанию статус ответа - 200

```
https://www.ietf.org/rfc/rfc2616.txt