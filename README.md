# Микросервис на Python🐍 для проекта PrettyPet 

Микросервис написан при помощи фреймворка Django.

Описание всех выполненных задач находится в папке [README_FILES](./README_FILES).

В папке ```autotests``` находятся интеграционные и сценарные автотесты. Распределены по папкам, в зависимости от названия django-приложения.

Unit-тесты находятся внутри папки каждого из приложений в файле ```tests.py``` и реализованы через TestCase. 

Микросервис использует следующие приложения:
- account(Авторизация/Аутентификация пользовтаеля)

### Документация:
[drf-spectacular](https://drf-spectacular.readthedocs.io/en/latest/)

Почему именно DRF Spectacular?
drf-spectacular - единственный проект, который сейчас активно поддерживается. django-rest-swagger и drf-yasg устарели и не обновлялись уже несколько лет.

- `docs/` - скачать документацию .yml
- `docs/swagger-ui/` - документация swagger-ui
- `docs/redoc/` - документация redoc

drf-spectacular - подключен к классу drf_spectacular.openapi.AutoSchema, то есть Swagger-UI и ReDoc документации будут строиться сами. Для более гибкой настройки в основном нужно использовать декоратор [`@extend_schema`](https://drf-spectacular.readthedocs.io/en/latest/readme.html#customization-by-using-extend-schema).

## <p align="center" >Всем добра!</p>
<p align="center">
  <img width="300" height="300" src="https://media.tenor.com/67iB7B7g59YAAAAM/siu-ronaldo-siu.gif">
</p>
