---
order: 100
icon: note
tags: [docker, self-hosted]
---

# What is `Gitea`?

[`Gitea`](https://about.gitea.com/)는 [`Git`](https://git-scm.com/)을 관리하기 위한 플랫폼으로 Self-hosted로 설치를 통해 Git Repository 프로젝트를 관리할 수 있습니다.

![Gitea Screenshot](https://about.gitea.com/img/home-screenshot.png)

!!!

Gitea runner에 대한 정보를 포함하고 있습니다.

!!!

## Prerequisites

[Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

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

```

+++ .env

```

# gitea

GITEA**mailer**ENABLED=true
GITEA**mailer**FROM={your_gmail_account}
GITEA**mailer**MAILER_TYPE=smtp
GITEA**mailer**HOST=smtp.gmail.com:465
GITEA**mailer**IS_TLS_ENABLED=false
GITEA**mailer**USER={your_gmail_account}
GITEA**mailer**PASSWD={your_gmail_application_password}

```

+++

That's it! :tada:

---

## memo

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있는 콘텐츠입니다.
```
