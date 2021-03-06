# ApMailer
Библиотека для отправки писем через SMTP. Основная идея данного приложения - простое подключение и быстрое использование функции отправки писем как из консоли, так и из PHP-кода.

## Использование в PHP

Подключите библиотеку в своём проекте, проинициализируйти её, и можно отправлять письма:
```` php
<?php
include 'phar:///path/to/mailer.phar/lib.php';

Mailer()->init(include '/path/to/mailer.config.php');

$message = Mailer()->newTextMessage();
$message->setContent('Hello world!')
    ->setSubject('Mail test')
    ->addRecipient('myemail@example.org');
    
if (Mailer()->sendMessage($message)) {
    echo 'Сообщение успешно отправлено.';
} else {
    echo 'Во время отправки возникли какие-то ошибки, проверьте логи для большей информации.';
}
````

## Использование в консоли

```` shell
$ ./mailer.phar --help
Отправка писем через консоль, используя SMTP.

Использование: mailer.phar [ПАРАМЕТРЫ]
ПАРАМЕТРЫ
    -t, --text            Текст сообщения
    -s, --subject         Тема сообщения
    -r, --recipient       Получатель письма
    -f, --from            Отправитель письма
    -c, --config          Путь к конфигу с настройками почтовика
    -a, --attach          Вложить файл в письмо как Attachment
    -i, --related         Включить файл в письмо как Related (индентификатором при этом будет название файла)
    --html                Отправить письмо как HTML
    -d, --display-eml     Вывести содержимое письма в формате EML
    --config-example      Показать пример конфига
    -h, --help            Показать справочную информацию
    -v, --version         Показать версию приложения
    
Порядок обработки конфигов:
    1. Файл mailer.config.php, который находится в директории с mailer.phar
    2. Файл mailer.config.php, который находится в текущей рабочей директории
    3. Файлы, которые указаны через -c и --config
````

### Примеры использования

Отправить пустое письмо без темы:
```` shell
$ ./mailer.phar -r yourmail@example.com
````

Отправить письмо с темой и данными из `stdin`:
```` shell
$ uname -a | ./mailer.phar -r yourmail@example.com -s 'My system' -t- 
````

Отправить письмо как HTML-шаблон:
```` shell
$ uname -a | ./mailer.phar -r yourmail@example.com -s 'My system' --html \
  -t '<html><body><pre><code>' \
  -t - \
  -t '</code></pre>' \
  -t "<hr> `date`" \
  -t '</body></html>'
````

Отправить письмо с вложениями:
```` shell
$ uname -a | ./mailer.phar -r yourmail@example.com -s 'My system' -t- -a file1.dat -a file2.dat
````

Отправить письмо в шаблоне с картинками:
```` shell
$ uname -a | ./mailer.phar -r yourmail@example.com -s 'My system' --html \
  -t '<html><body><pre><code>' \
  -t - \
  -t '</code></pre>' \
  -t '<img src="cid:myimg.png">' -i ./myimg.png \
  -t '</body></html>'
````

Не отправлять письмо, а вывести его в `stdout`:
```` shell
$ uname -a | ./mailer.phar -r yourmail@example.com -s 'My system' --html \
  -t '<html><body><pre><code>' \
  -t - \
  -t '</code></pre>' \
  -t '<img src="cid:myimg.png">' -i ./myimg.png \
  -t '</body></html>' \
  -d
````