---
order: 100
icon: note
tags: [docker, self-hosted]
---

# Zitadel (SSO)

!!!

이 페이지에서는 [Zitadel](https://zitadel.com/)에 대해서 다루고 있습니다.

!!!

## What is `Zitadel`?

[`Zitadel`](https://zitadel.com)은 인증을 위한 플랫폼으로, SSO 또는 Social Logins(Google, Gitlab, Github, Microsoft) 등과 Multifactor 인증, 패스키 인증, API 인증 등 여러 인증을 관리할 수 있습니다.

- SSO
- Social Logins(Google, Gitlab, Github, Microsoft)
- Multifactor Authentication
- Passkeys
- Role-Base access control

![Zitadel Screenshot 01](/static/attachments/zitadel_01.png)
![Zitadel Screenshot 02](/static/attachments/zitadel_02.png)
![Zitadel Screenshot 03](/static/attachments/zitadel_03.png)

## Prerequisites

- [Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

---

## Install

- externaldomain 을 맞춰야, instance not found가 발생하지 않음
- 관리자 설정에서 smtp 설정하기

+++ Docker-compose.yml

```
version: '3.8'

services:
  zitadel:
    image: 'ghcr.io/zitadel/zitadel:latest'
    container_name: 'zitadel'
    restart: 'unless-stopped'
    command: 'start-from-init --masterkey "j6B2VFUXrSDZYLtWwDLEtESb45zr6KhD" --tlsMode disabled'
    environment:
      # https://zitadel.com/docs/self-hosting/manage/configure
      - 'ZITADEL_DATABASE_COCKROACH_HOST=crdb'
      - 'ZITADEL_EXTERNALSECURE=false'
      - 'ZITADEL_EXTERNALDOMAIN={your_domain}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_NAME=taking'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_USERNAME={your_username}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_PASSWORD={your_password}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_EMAIL_ADDRESS={your_email}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_EMAIL_VERIFIED=true'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_PASSWORDCHANGEREQUIRED=false'
    depends_on:
      crdb:
        condition: 'service_healthy'
    ports:
      - '8080:8080'

  crdb:
    image: 'cockroachdb/cockroach:latest-v22.2'
    restart: 'unless-stopped'
    container_name: 'crdb'
    command: 'start-single-node --insecure'
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:8080/health?ready=1"]
      interval: '10s'
      timeout: '30s'
      retries: 5
      start_period: '20s'
    ports:
      - '9090:8080'
      - '26257:26257'
    volumes:
      - "./cockroach-data:/cockroach/cockroach-data"
```

+++

+++ Docker-compose.yml (https domain)

```
version: '3.8'

services:
  zitadel:
    image: 'ghcr.io/zitadel/zitadel:latest'
    container_name: 'zitadel'
    restart: 'unless-stopped'
    command: 'start-from-init --masterkey "j6B2VFUXrSDZYLtWwDLEtESb45zr6KhD" --tlsMode external'
    environment:
      # https://zitadel.com/docs/self-hosting/manage/configure
      - 'ZITADEL_DATABASE_COCKROACH_HOST=crdb'
      - 'ZITADEL_EXTERNALPORT=443'
      - 'ZITADEL_EXTERNALSECURE=true'
      - 'ZITADEL_TLS_ENABLED=false'
      - 'ZITADEL_EXTERNALDOMAIN={your_domain}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_NAME=taking'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_USERNAME={your_username}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_PASSWORD={your_password}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_EMAIL_ADDRESS={your_email}'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_EMAIL_VERIFIED=true'
      - 'ZITADEL_FIRSTINSTANCE_ORG_HUMAN_PASSWORDCHANGEREQUIRED=false'
    depends_on:
      crdb:
        condition: 'service_healthy'
    ports:
      - '8080:8080'

  crdb:
    image: 'cockroachdb/cockroach:latest-v22.2'
    restart: 'unless-stopped'
    container_name: 'crdb'
    command: 'start-single-node --insecure'
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1:8080/health?ready=1"]
      interval: '10s'
      timeout: '30s'
      retries: 5
      start_period: '20s'
    ports:
      - '9090:8080'
      - '26257:26257'
    volumes:
      - "./cockroach-data:/cockroach/cockroach-data"
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
