# Project 02: Jenkins CI/CD Voting App

## Upstream Version

Base image pinned to commit: `a3b4c5d` (tag: `v1.0`)  
Source: https://github.com/dockersamples/example-voting-app

## Architecture

- **vote** — Python web app (порт 8080) — страница голосования
- **result** — Node.js web app (порт 8081) — страница результатов
- **worker** — .NET сервис — обрабатывает голоса
- **redis** — очередь сообщений (порт 6379)
- **postgres** — база данных (порт 5432)

## Ports and Dependencies

| Сервис   | Порт | Технология |
|----------|------|------------|
| vote     | 8080 | Python/Flask |
| result   | 8081 | Node.js |
| redis    | 6379 | Redis |
| postgres | 5432 | PostgreSQL |

## How to Run Locally

```bash
git clone https://github.com/marta77784/project-02-jenkins-cicd-voting-app.git
cd project-02-jenkins-cicd-voting-app
docker compose up --build
```

## Verification URLs

| Сервис    | URL |
|-----------|-----|
| Vote UI   | http://localhost:8080 |
| Result UI | http://localhost:8081 |

## Health Checks

- Vote app: `curl http://localhost:8080`
- Result app: `curl http://localhost:8081`
