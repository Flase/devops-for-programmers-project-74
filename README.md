### Hexlet tests and linter status:
[![Actions Status](https://github.com/Flase/devops-for-programmers-project-74/actions/workflows/hexlet-check.yml/badge.svg)
![Build and Push to Docker Hub](https://github.com/Flase/devops-for-programmers-project-74/actions/workflows/push.yml/badge.svg)](https://github.com/Flase/devops-for-programmers-project-74/actions)

# DevOps for Programmers Project 74

## Описание проекта

Этот проект предназначен для демонстрации навыков DevOps с использованием Docker и Docker Compose. В рамках проекта создается и настраивается приложение на Node.js с использованием Fastify, а также PostgreSQL в качестве базы данных.

## Требования к системе

- Docker версии 20.10.7 и выше
- Docker Compose версии 1.27.0 и выше

## Установка

### Шаг 1: Клонирование репозитория

```bash
git clone https://github.com/Flase/devops-for-programmers-project-74.git
cd devops-for-programmers-project-74
```

### Шаг 2: Создание файлов конфигурации

#### .dockerignore

Создайте файл `.dockerignore` в корне проекта:

```plaintext
node_modules
```

#### Dockerfile

Создайте файл `Dockerfile` в корне проекта:

```Dockerfile
# Используем базовый образ node версии 20.12.2
FROM node:20.12.2

# Устанавливаем рабочую директорию
WORKDIR /app

```

#### docker-compose.yml

Создайте файл `docker-compose.yml` в корне проекта:

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.production
    image: dsradozhitskiy/devops-for-programmers-project-74
    command: make test
    depends_on:
      db:
        condition: service_healthy
    environment:
      DATABASE_NAME: postgres
      DATABASE_USERNAME: postgres
      DATABASE_PASSWORD: password
      DATABASE_HOST: db
      DATABASE_PORT: 5432
    volumes:
      - app:/app

  db:
    image: postgres
    restart: always
    shm_size: 128mb
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  app:
```

#### docker-compose.override.yml

Создайте файл `docker-compose.override.yml` в корне проекта:

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - ./app:/app
    ports:
      - "8080:8080"
    command: make dev
    depends_on:
      db:
        condition: service_healthy
    environment:
      DATABASE_NAME: postgres
      DATABASE_USERNAME: postgres
      DATABASE_PASSWORD: password
      DATABASE_HOST: db
      DATABASE_PORT: 5432

  caddy:
    image: caddy
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./services/caddy/Caddyfile:/etc/caddy/Caddyfile
    depends_on:
      - app
```

### Шаг 3: Запуск приложения

Для установки зависимостей выполните:

```bash
make setup
```

Для запуска приложения в режиме разработки выполните:

```bash
make dev
```

Приложение будет доступно по адресу [http://localhost:8080](http://localhost:8080).

### Шаг 4: Запуск тестов

Для запуска тестов выполните:

```bash
make test
```

### Makefile

```Makefile
ci:
    docker compose -f docker-compose.yml up --abort-on-container-exit --exit-code-from app

setup:
    docker-compose run --rm app make setup

test:
    docker-compose -f docker-compose.yml up --abort-on-container-exit --exit-code-from app

dev:
    docker-compose up
```

## Ссылка на Docker Hub образ

Образ приложения доступен на Docker Hub: [dsradozhitskiy/devops-for-programmers-project-74](https://hub.docker.com/r/dsradozhitskiy/devops-for-programmers-project-74)

## Заключение

Этот проект демонстрирует процесс настройки и управления приложением с использованием Docker и Docker Compose, что облегчает его развертывание и тестирование.
