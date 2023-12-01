---
order: 100
icon: note
tags: [docker]
---

## Prerequisites

[Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

---

## Install

+++ Docker-compose.yml

```
version: "3.9"
services:
  coder:
    image: ghcr.io/coder/coder:${CODER_VERSION:-latest}
    restart: always
    environment:
      CODER_PG_CONNECTION_URL: "postgresql://${POSTGRES_USER:-username}:${POSTGRES_PASSWORD:-password}@database/${POSTGRES_DB:-coder}?sslmode=disable"
      CODER_HTTP_ADDRESS: "0.0.0.0:3000"
      CODER_ACCESS_URL: "https://${CODER_ACCESS_URL}"
      CODER_WILDCARD_ACCESS_URL: "*${CODER_ACCESS_URL}"
    group_add:
      - "998" # docker group on host
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "3000:3000"
    depends_on:
      database:
        condition:
  database:
    image: "postgres:14.2"
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-username}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-password}
      POSTGRES_DB: ${POSTGRES_DB:-coder}
    volumes:
      - ./coder_data:/var/lib/postgresql/data
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "pg_isready -U ${POSTGRES_USER:-username} -d ${POSTGRES_DB:-coder}",
        ]
      interval: 5s
      timeout: 5s
      retries: 5
```

+++ .env

```
POSTGRES_USER=taking
POSTGRES_PASSWORD=As6SF8L3RdzbfwnE5h9
POSTGRES_DB=coder

CODER_ACCESS_URL=code.taking.kr
```

+++ yarn

```
yarn global add retypeapp
retype watch
```

+++ dotnet

```
dotnet tool install retypeapp --global
retype watch
```

+++

That's it! :tada:

---

## memo

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있는 콘텐츠입니다.
