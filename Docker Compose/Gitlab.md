---
order: 100
icon: note
tags: [docker, self-hosted]
---

# Gitlab

!!!

이 페이지에서는 [Gitlab](https://about.gitlab.com)에 대해서 다루고 있습니다.

!!!

## What is `Gitlab`?

[`Gitlab`](https://about.gitlab.com)는 [`Git`](https://git-scm.com/)을 관리하기 위한 플랫폼으로 Self-hosted로 설치를 통해 Git Repository 프로젝트를 관리할 수 있습니다.

![Gitlab Screenshot 01](https://p158.p1.n0.cdn.getcloudapp.com/items/bLuynGkz/572c46f9-2799-4907-a36d-62340b8ca085.png?source=viewer&v=2eb98c3ce491b34d2fc61c7ba5b335bb)
![Gitlab Screenshot 01](https://p158.p1.n0.cdn.getcloudapp.com/items/Apul0Oz6/ff5a4fe9-10e0-4f2e-9125-aa3a4055c9d3.png?source=viewer&v=5ceccc538fba89f52bfdf779f3360ae1)

## Prerequisites

[Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

---

## Install

+++ Docker-compose.yml

```
version: '3.7'
services:
 gitlab:
   image: 'gitlab/gitlab-ce:15.7.0-ce.0'
   container_name: 'gitlab'
   hostname: 'repo.taking.kr'
   restart: unless-stopped
   environment:
     GITLAB_OMNIBUS_CONFIG: |
             external_url 'https://repo.taking.kr'
             #gitlab_rails['gitlab_ssh_host'] = '{your_public_ip}'
             gitlab_rails['gitlab_shell_ssh_port'] = 22
             gitlab_rails['gitlab_signin_enabled'] = false
             nginx['listen_port'] = 80
             nginx['listen_https'] = false
             nginx['proxy_set_headers'] = {  "X-Forwarded-Proto" => "https", "X-Forwarded-Ssl" => "on" }
     # Add any other gitlab.rb configuration here, each on its own line
     GITLAB_SKIP_UNMIGRATED_DATA_CHECK: true
   ports:
     - '10030:80'
     - '10031:443'
     - '22:22'
 gitlab-runner:
   image: 'gitlab/gitlab-runner:latest'
   container_name: gitlab-runner
   volumes:
     - './gitlab-runner_data/config:/etc/gitlab-runner'
     - '/var/run/docker.sock:/var/run/docker.sock'
   restart: unless-stopped
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

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
