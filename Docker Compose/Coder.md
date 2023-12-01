---
order: 100
icon: note
tags: [docker, self-hosted]
---

# Coder

!!!

이 페이지에서는 [Coder v2](https://coder.com/docs/v2/latest)에 대해서 다루고 있습니다.

!!!

## What is `Coder`?

[`Coder`](https://coder.com/)는 [`Visual Studio Code`](https://code.visualstudio.com/)를 포팅하여, Self-hosted로 설치를 통해 클라우드에서 [VS Code](https://code.visualstudio.com/)를 사용할 수 있는 플랫폼입니다.

- [Coder v2](https://coder.com/docs/v2/latest) (워크스페이스 별로 VS Code 를 관리할 수 있는 플랫폼)
- [Coder v1](https://coder.com/docs/code-server/latest) (VS Code 단일 서비스)

![Coder v2 Screenshot](https://coder.com/_next/image?url=https%3A%2F%2Fraw.githubusercontent.com%2Fcoder%2Fcoder%2Fmain%2Fdocs%2Fimages%2Fhero-image.png&w=3840&q=100)

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
POSTGRES_PASSWORD={your_postgres_password}
POSTGRES_DB=coder

CODER_ACCESS_URL=code.taking.kr
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
