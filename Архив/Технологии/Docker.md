# Image

**Образ Docker (Docker Image)** - это неизменяемый файл, содержащий исходный код, библиотеки, зависимости, инструменты и другие файлы, необходимые для запуска приложения

Из-за того, что образы предназначены только для чтения их иногда называют снимками (snapshot)

Образ - это шаблон, на основе которого создается контейнер, существует отдельно и не может быть изменен.

---

## Команды для Docker

- **Сборка Image:** docker build -t getting-started .
    
- **Запуск контейнера:** docker run -dp 3000:3000 getting-started
    
- Информация о контейнерах: docker rs
    
- Остановить контейнер: docker stop
    
- **Удалить контейнер:** docker rm
    

docker login -u YOUR-USER-NAME

docker tag getting-started YOUR-USER-NAME/getting-started

docker push YOUR-USER-NAME/getting-started

[https://hub.docker.com/repositories/itsmailov](https://hub.docker.com/repositories/itsmailov)

# Таблица с командами 

| Что нужно                | Команда/пример                   |
| :----------------------- | :------------------------------- |
| 📦 Скачать образ         | `docker pull nginx`              |
| 🚀 Запустить             | `docker run -d -p 8080:80 nginx` |
| 🔍 Посмотреть контейнеры | `docker ps -a`                   |
| 🛠️ Войти в контейнер    | `docker exec -it <id> bash`      |
| 🧱 Собрать образ         | `docker build -t myapp .`        |
| 🔄 Поднять compose       | `docker-compose up -d`           |
| 🧹 Остановить compose    | `docker-compose down`            |
