<h1 align="center">Организация работы над проектами</h1>

**Общая информация для поддержки и всеобщего понимания кода. При разработке производим следующие действия**

* [1.Пишем документацию и указываем сопутствующей файлы.](#1документация)
    + [1.0 Прописываем файл README.md.](#10-readmemd)
    + [1.1 Прописываем файл .env.example.](#11-envexample)
    + [1.2 [ **Опционально** ] Прописываем допольнительные файлы для запуска.](#12-файлы-для-запуска)
* [2.Пишем тесты.](#2тесты)
* [3.Пишем Dockerfile и docker-compose.yml.](#3dockerfile-и-docker-composeyml)
* [4.Структурирование кода.](#4стиль-кода-и-линтинг)
* [5.Работа с ветками.](#5git-flow)
* [6.Логирование.](#6логирование-и-мониторинг)

## 1.Документация
В каждом репозитории должна лежать краткая сводка о приложении, что оно делает и какие креды необходимы для запуска.

### 1.0 README.md
Здесь прописываем инфу о сервисе, на чьих сервисах завязан, для каких сервисов предназначен и в кратце прописываем поля для .env файла.

### 1.1 .env.example
Заполненный файл .env без **ВСТАВЛЕННЫХ ВНУТРЬ ЗНАЧЕНИЙ**. Пример:
```
DATABASE_USER=
DATABASE_PASSWORD=
...

RABBITMQ_USER=
RABBITMQ_PASSWORD=
...
```
**Ни в коем случае не использовать DATABASE_URL!** При использовании ждет анальное наказание

### 1.2 Файлы для запуска
Дополнительные файлы, небходимые для старта либо поддержания приложения. Это может быть файл **example.sql** с sql скриптом для пуша в бд какой-либо инфы либо доп директории, необходимые для исполнения в процессе.

## 2.Тесты
Желательно иметь модуль тестирования + **гайд** по его запуску для внедрения в CI/CD.

## 3.Dockerfile и docker-compose.yml
В каждом сервисе писать Dockerfile и оформлять на него docker-compose.yml. volume - любые, network - для сервиса и общий (для связи). Сеть на проде для разных сервисов - ```bot-network```. Пример:
```
version: "3.9"

services:
  ...:
    container_name: ...
    hostname: ...
    build:
      context: .
      dockerfile: Dockerfile

    networks:
      - bot-network
    ports:
      ...
    env_file:
      - .env

    restart: always

    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 10s

    volumes:
      ...

networks:
  bot-network:
    name: bot-network
    external: true
```
Желательно присутствие healthcheck. Все креды прописывать в .env файле и подтягивать из него в случае необходимости

## 4.Стиль кода и линтинг

* Используем [Black](https://github.com/psf/black) / [Ruff](https://github.com/astral-sh/ruff) для Python.
* Все правила прописаны в `pyproject.toml`.
* Настроен `pre-commit`, пример установки:
```bash
pip install pre-commit
pre-commit install
```
Проверка перед коммитом:

```bash
pre-commit run --all-files
```

## 5.Git flow

* Основная ветка: `main`
* Все коммиты на стадии разработки делаем в `dev`
* Новые фичи — от `dev`, в `feature/<название>`
* PR — только через ревью, без прямых пушей в `main`
* Названия коммитов:
  * `feat: добавлена новая логика`
  * `fix: починен баг с авторизацией`
  * `chore: обновлён README`

* **Не забываем делать squash merge**

## 6.Логирование и мониторинг

* Логгер на базе `structlog` / `logging` с выводом в stdout
* Уровни логов: DEBUG, INFO, WARNING, ERROR
* Healthcheck API: `GET /health` (используется в Docker)

Опционально, но желательно в ключевых сервисах:
* Интеграция мониторинга через Prometheus + Grafana

---
# Анальная кара за жесткие косяки, моральное унижение за косяки поменьше

<p align="center">
  <img src="./1.png" alt="Анальный каратель" width="300"/>
</p>
<a href="https://github.com/4ernonayzer"><p align="center"><strong>Анальный каратель</strong></p></a>