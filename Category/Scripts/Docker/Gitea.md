---
order: 100
icon: note
tags: [docker, docker-compose, self-hosted]
---

# Gitea

!!!

이 페이지에서는 [Gitea](https://about.gitea.com)에 대해서 다루고 있습니다.

!!!

## What is `Gitea`?

[`Gitea`](https://about.gitea.com)는 [`Git`](https://git-scm.com/)을 관리하기 위한 플랫폼으로 Self-hosted로 설치를 통해 Git Repository 프로젝트를 관리할 수 있습니다.

![Gitea Screenshot](https://about.gitea.com/img/home-screenshot.png)

## Prerequisites

- [Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

---

## Install

+++ Docker-compose.yml

```
version: '3.7'
services:
 gitea:
   image: gitea/gitea:1.19.4-linux-amd64
   container_name: gitea
   restart: unless-stopped
   environment:
     - USER_UID=1000
     - USER_GID=1000
   volumes:
     - ./gitea_data:/data
     - /etc/timezone:/etc/timezone:ro
     - /etc/localtime:/etc/localtime:ro
   ports:
     - "3000:3000"
     - "22:22"
   env_file:
     - ./.env
 gitea-runner:
   image: gitea/act_runner:0.2.3
   restart: unless-stopped
   depends_on:
     - gitea
   volumes:
     - ./act_runner:/data
     - /var/run/docker.sock:/var/run/docker.sock
   environment:
     - GITEA_INSTANCE_URL=https://repo.taking.kr
     - GITEA_RUNNER_REGISTRATION_TOKEN=token
     - GITEA_RUNNER_NAME=gitea-runner
```

+++ .env

```

# gitea

GITEA__mailer__ENABLED=true
GITEA__mailer__FROM={your_gmail_account}
GITEA__mailer__MAILER_TYPE=smtp
GITEA__mailer__HOST=smtp.gmail.com:465
GITEA__mailer__IS_TLS_ENABLED=false
GITEA__mailer__USER={your_gmail_account}
GITEA__mailer__PASSWD={your_gmail_application_password}

```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
