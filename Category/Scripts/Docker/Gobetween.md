---
order: 100
icon: note
tags: [docker, docker-compose, self-hosted]
---

# Coder

!!!

이 페이지에서는 [Gobetween](https://gobetween.io/documentation.html)에 대해서 다루고 있습니다.

!!!

## What is `Gobetween`?

[`Gobetween`](https://gobetween.io/documentation.html)은 Go로 개발된 Load Balancer 입니다.

![Gobetween Logo](https://github.com/yyyar/gobetween/blob/master/logo.png?raw=true)

## Prerequisites

- [Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

## How-to
- [Protocols](https://gobetween.io/documentation.html#Protocols)
  - TCP
  - TLS
  - UDP
- [Balancing](https://gobetween.io/documentation.html#Balancing)
- [Discovery](https://gobetween.io/documentation.html#Discovery)
  - static
  - srv
  - docker
  - json
  - plaintext
  - exec
  - consul
  - lxd
- [Healthchecks](https://gobetween.io/documentation.html#Healthchecks)
  - ping
  - exec
  - probe
- [Access-Control](https://gobetween.io/documentation.html#Access-Control)
- [Proxy-Protocol](https://gobetween.io/documentation.html#Proxy-Protocol)
- [Tls-Proxying](https://gobetween.io/documentation.html#Tls-Proxying)
- [Server-Name-Indication](https://gobetween.io/documentation.html#)
- etc..


---

## Install

+++ Docker-compose.yml

```
version: '3.3'
services:
  gobetween:
    image: yyyar/gobetween
    container_name: gobetween
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./gobetween_data/config.toml:/etc/gobetween/conf/gobetween.toml
```

+++ gobetween.toml

하단의 예제는 80, 443 포트에 대해 Load Balancer 설정된 파일입니다.

```
[servers.https]
bind = ":443"
protocol = "tcp"
balance = "weight"

max_connections = 1000
client_idle_timeout = "10m"
backend_idle_timeout = "10m"
backend_connection_timeout = "2s"

[servers.https.discovery]
kind = "static"
static_list = [
    "192.168.0.130:443 weight=1"
    "192.168.0.131:443 weight=1"
]

[servers.http]
bind = ":80"
protocol = "tcp"
balance = "weight"

max_connections = 1000
client_idle_timeout = "10m"
backend_idle_timeout = "10m"
backend_connection_timeout = "2s"

[servers.http.discovery]
kind = "static"
static_list = [
    "192.168.0.130:80 weight=1"
    "192.168.0.131:80 weight=1"
]
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
