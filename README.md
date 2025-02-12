# Микросервис на Python🐍 для проекта PrettyPet

Микросервис написан при помощи фреймворка Django.

Описание всех выполненных задач находится в папке [README_FILES](./README_FILES).

В папке [autotests](./autotests/) находятся интеграционные и сценарные автотесты. Распределены по папкам, в зависимости от названия django-приложения.

Unit-тесты находятся внутри папки каждого из приложений в файле ```tests.py``` и реализованы через TestCase.

## Содержание <!-- omit from toc -->
- [Разработка](#разработка)
  - [Настройка и запуск проекта](#настройка-и-запуск-проекта)
- [Состав приложений](#состав-приложений)
- [Документирование API](#документирование-api)
- [Логирование](#логирование)
- [Аутентификация](#аутентификация)
  - [Модель User:](#модель-user)
  - [Модель Profile:](#модель-profile)
  - [Механизм аутентификации:](#механизм-аутентификации)
  - [Шаблоны:](#шаблоны)
- [Модели:](#модели)
- [Команды make](#команды-make)
- [Всем добра!](#всем-добра)

## Разработка

Для разработки необходимо создать свой **fork** репозитория. При создании необходимо убрать галочку с поля `Copy the main branch only`.

После этого вы можете клонировать свой **fork** и начинать работу. Необходимо создать ветку в формате `feature/fix/hotfix-{краткое описание}` (например `fix-switch-lang-to-eng`).

### Настройка и запуск проекта


Необходимо создать и активировать **виртуальное окружение**:
```
python -m venv venv
source venv/bin/activate
```

Затем необходимо установить **зависимости**:
```
pip install -r requirements.txt
```

Перед началом разработки необходимо установить **pre-commit хуки**:
```
pre-commit install
```

После этого перед каждым вашим коммитом необходимые линтеры будут запускаться автоматически. Если линтер исправил ваши файлы, их необходимо снова добавить в `staged` и создать коммит повторно.

## Состав приложений

Микросервис использует следующие приложения:
- account(Авторизация/Аутентификация пользователя)
- common(Общие элементы приложения. Пример: модель, которая относится сразу к нескольким сущностям)
- core(Базовые утилиты проекта. Пример: базовый mixin, добавленный в 80% моделей)
- hackathons(Логика и модели, необходимые для Хакатона)
- main(Корневая папка Django-проекта)
- profiles(Всё, что связан ос профилем пользователя)
- projects(Всё, что связано с проектами)

## Документирование API
Для документирования API используется библиотека [drf-spectacular](https://drf-spectacular.readthedocs.io/en/latest/).

Почему именно DRF Spectacular?
drf-spectacular - единственный проект, который сейчас активно поддерживается. django-rest-swagger и drf-yasg устарели и не обновлялись уже несколько лет.

- `docs/` - скачать документацию .yml
- `docs/swagger-ui/` - документация swagger-ui
- `docs/redoc/` - документация redoc

drf-spectacular - подключен к классу drf_spectacular.openapi.AutoSchema, то есть Swagger-UI и ReDoc документации будут строиться сами. Для более гибкой настройки в основном нужно использовать декоратор [`@extend_schema`](https://drf-spectacular.readthedocs.io/en/latest/readme.html#customization-by-using-extend-schema).

## Логирование
- В корне репозитория есть папка `/logs` (если её нет, она создастся автоматически), куда пишутся файлы логов.
- Файл, созданный в текущий день, называется `logs`. Файлы, созданные в предыдущие
дни имеют имя `logs.{дата создания файла}`. По окончании текущего дня (в 00:00) при попытке записать новый лог файл
`logs` меняет имя на `logs.{дата только что минувшего дня, она же - дата создания файла}`, и создаётся новый пустой файл с именем
`logs`, куда и будет записан этот новый лог (и все следующие логи наступившего дня).
- Всего в `/logs` могут находиться 4 файла: `logs` и три файла, созданных в предыдущие три дня. Все файлы старше трёх
дней уничтожаются.
- В файл `logs` пишутся все логи текущего дня уровня `WARNING` и выше в формате
`{levelname} - {asctime} - {module} - {message}`.
- В консоль выводятся логи всех уровней в формате `{levelname} - {asctime} - {module} - {message}`.
- В `/settings/logging.py` находится всё, что связано с логами. В частности, там находится функция `example_logs()` (а
также её вызов), которая производит тестовые логи. При первичном старте сервера она выполняется два раза, но далее всё
работает как нужно.
- **Примечание** <br>
Предположим, что у нас есть файл `logs` и запущен сервер. Если при этом сменится день (неважно, естественным путём или
ручной сменой даты), после чего произойдёт перезапуск сервера путём `autoreload`, то файл `logs` не будет переименован
ввиду ошибки по типу:
`PermissionError: [WinError 32] Процесс не может получить доступ к файлу, так как этот файл занят другим процессом:
'C:\\work\\microservice-python-pp-web\\logs\\logs' -> 'C:\\work\\microservice-python-pp-web\\logs\\logs.2024-08-27'.`, а
также логи перестанут записываться в файл. Скорее всего, это связано с работой файловой системы Windows. При ручном
перезапуске сервера ошибок не возникает.

## Аутентификация
В данном проекте реализована система аутентификации и управления профилями пользователей. Все функции, связанные с
аутентификацией, размещены в приложении account. Проект включает следующие компоненты:

### Модель User:
- Валидация на номер телефона.
- Валидация на кодовое слово: не более 12 слов.
- Валидация на пароль: не менее 8 символов, обязательно буквы и цифры, одна заглавная буква.

### Модель Profile:
- Все поля сделаны необязательными.
= Ограничение на размер фото. (не больше 4мб и разрешение максимум 200x200)
- Валидация на возраст.
- Валидация на город: первая буква заглавная.

### Механизм аутентификации:
При трех неудачных попытках входа пользователь может аутентифицироваться по 4-значному коду из почты или (пока нет) номеру телефона. Кодовое слово хранится в базе данных в зашифрованном виде.

### Шаблоны:
Созданы три тестовых шаблона для:

- Регистрации данных в поле User.
- Регистрации данных в поле Profile.
- Входа в приложение (переход на auth/hi после успешного входа).

## Модели:
- Описание:
  -  Модели составлены на основании описания проекта и бизнес-моделей.
- Чертёж хранится по ссылке <br>
https://dbdiagram.io/d/Pretty-Pet-66c894f4a346f9518ce84d0f <br>
Редактировать чертёж может Александр (tg: chutzp4h, discord: ne_pedofil_a_beta_tester).
- Чертёж в виде svg: <br>
![Pretty Pet.svg](README_FILES%2FREADME_IMAGES%2FPretty%20Pet.svg)
- Чертёж в виде DBML (Database Markup Language): <br>
[database_structure.md](README_FILES%2FREADME_TEXT_FILES%2Fdatabase_structure.md)


## Команды make
Для упрощения работы в приложении созданы **команды make**, перечисленные ниже. Для их использования необходимо установить пакет **make** при помощи команды ```sudo apt-get install make```. Если у вас **windows**, вам необходимо установить **cmake** или работать через **wsl**.
- ```make freeze``` - перезаписывает файл requirements.txt текущими зависимостями;
- ```make install``` - устанавливает зависимости из файла requirements.txt;

## <p align="center" >Всем добра!</p>
<p align="center">
  <img width="300" height="300" src="https://media.tenor.com/67iB7B7g59YAAAAM/siu-ronaldo-siu.gif">
</p>
