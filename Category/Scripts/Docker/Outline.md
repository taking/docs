---
order: 100
icon: note
tags: [docker, docker-compose, self-hosted]
---

# Outline

!!!

이 페이지에서는 [Outline](https://github.com/outline/outline)에 대해서 다루고 있습니다.

!!!

## What is `Outline`?

[`Outline`](https://github.com/outline/outline)는 Notion 같은 Knowledge Base 플랫폼입니다.

![Outline Screenshot](/static/attachments/outline_image.png)

## Prerequisites

- [Docker](https://docs.docker.com/engine/install/) is installed using either [`docker compose`](https://docs.docker.com/compose/).

---

## Install

+++ Docker-compose.yml

```
version: "3.2"
services:
#
#
  outline:
    image: docker.getoutline.com/outlinewiki/outline:latest
    env_file: .env
    ports:
      - "3000:3000"
    volumes:
      - ./outline_data:/var/lib/outline/data
    depends_on:
      - postgres
      - redis
#
#
  # Redis - Key-value Store
  redis:
    image: 'redis:latest'
    # container_name: 'redis'
    restart: unless-stopped
    entrypoint: redis-server --appendonly yes --maxmemory 4g --maxmemory-policy allkeys-lru
    security_opt:
      - no-new-privileges:true
    # ports:
    #   - "6379:6379"
    volumes:
      - ./redis_data:/data
      - /etc/localtime:/etc/localtime:ro
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 30s
      retries: 3
#
#
  postgres:
    image: postgres:16.1-bullseye
    # ports:
    #   - "5432:5432"
    volumes:
      - ./pg_data:/var/lib/postgresql/data
    env_file: .env
    healthcheck:
      test: ["CMD", "pg_isready"]
      interval: 30s
      timeout: 20s
      retries: 3
```

+++ .env

```
# –––––––––––––––– REQUIRED ––––––––––––––––

NODE_ENV=production

# Generate a hex-encoded 32-byte random key. You should use `openssl rand -hex 32`
# in your terminal to generate a random value.
SECRET_KEY=

# Generate a unique random key. The format is not important but you could still use
# `openssl rand -hex 32` in your terminal to produce this.
UTILS_SECRET=

# Postgres
POSTGRES_USER=outline
POSTGRES_PASSWORD=password
POSTGRES_DB=outline

# For production point these at your databases, in development the default
# should work out of the box.
DATABASE_URL=postgres://outline:password@postgres:5432/outline
DATABASE_URL_TEST=postgres://outline:password@postgres:5432/outline-test
DATABASE_CONNECTION_POOL_MIN=
DATABASE_CONNECTION_POOL_MAX=
# Uncomment this to disable SSL for connecting to Postgres
PGSSLMODE=disable

# For redis you can either specify an ioredis compatible url like this
REDIS_URL=redis://redis:6379
# or alternatively, if you would like to provide additional connection options,
# use a base64 encoded JSON connection option object. Refer to the ioredis documentation
# for a list of available options.
# Example: Use Redis Sentinel for high availability
# {"sentinels":[{"host":"sentinel-0","port":26379},{"host":"sentinel-1","port":26379}],"name":"mymaster"}
# REDIS_URL=ioredis://eyJzZW50aW5lbHMiOlt7Imhvc3QiOiJzZW50aW5lbC0wIiwicG9ydCI6MjYzNzl9LHsiaG9zdCI6InNlbnRpbmVsLTEiLCJwb3J0IjoyNjM3OX1dLCJuYW1lIjoibXltYXN0ZXIifQ==

# URL should point to the fully qualified, publicly accessible URL. If using a
# proxy the port in URL and PORT may be different.
URL=https://wiki.taking.kr
PORT=3000

# See [documentation](docs/SERVICES.md) on running a separate collaboration
# server, for normal operation this does not need to be set.
COLLABORATION_URL=

# To support uploading of images for avatars and document attachments an
# s3-compatible storage must be provided. AWS S3 is recommended for redundancy
# however if you want to keep all file storage local an alternative such as
# minio (https://github.com/minio/minio) can be used.

# A more detailed guide on setting up S3 is available here:
# => https://wiki.generaloutline.com/share/125de1cc-9ff6-424b-8415-0d58c809a40f
#
AWS_ACCESS_KEY_ID=get_a_key_from_aws
AWS_SECRET_ACCESS_KEY=get_the_secret_of_above_key
AWS_REGION=xx-xxxx-x
AWS_S3_ACCELERATE_URL=
AWS_S3_UPLOAD_BUCKET_URL=http://s3:4569
AWS_S3_UPLOAD_BUCKET_NAME=bucket_name_here
AWS_S3_FORCE_PATH_STYLE=true
AWS_S3_ACL=private

# Specify what storage system to use. Possible value is one of "s3" or "local".
# For "local", the avatar images and document attachments will be saved on local disk.
FILE_STORAGE=local

# If "local" is configured for FILE_STORAGE above, then this sets the parent directory under
# which all attachments/images go. Make sure that the process has permissions to create
# this path and also to write files to it.
FILE_STORAGE_LOCAL_ROOT_DIR=/var/lib/outline/data

# Maximum allowed size for the uploaded attachment.
FILE_STORAGE_UPLOAD_MAX_SIZE=26214400

# –––––––––––––– AUTHENTICATION ––––––––––––––

# Third party signin credentials, at least ONE OF EITHER Google, Slack,
# or Microsoft is required for a working installation or you'll have no sign-in
# options.

# To configure Slack auth, you'll need to create an Application at
# => https://api.slack.com/apps
#
# When configuring the Client ID, add a redirect URL under "OAuth & Permissions":
# https://<URL>/auth/slack.callback
SLACK_CLIENT_ID=
SLACK_CLIENT_SECRET=

# To configure Google auth, you'll need to create an OAuth Client ID at
# => https://console.cloud.google.com/apis/credentials
#
# When configuring the Client ID, add an Authorized redirect URI:
# https://<URL>/auth/google.callback
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# To configure Microsoft/Azure auth, you'll need to create an OAuth Client. See
# the guide for details on setting up your Azure App:
# => https://wiki.generaloutline.com/share/dfa77e56-d4d2-4b51-8ff8-84ea6608faa4
AZURE_CLIENT_ID=
AZURE_CLIENT_SECRET=
AZURE_RESOURCE_APP_ID=

# To configure generic OIDC auth, you'll need some kind of identity provider.
# See documentation for whichever IdP you use to acquire the following info:
# Redirect URI is https://<URL>/auth/oidc.callback
OIDC_CLIENT_ID=
OIDC_CLIENT_SECRET=
OIDC_AUTH_URI=https://sso.taking.kr/oauth/v2/authorize
OIDC_TOKEN_URI=https://sso.taking.kr/oauth/v2/token
OIDC_USERINFO_URI=https://sso.taking.kr/oidc/v1/userinfo

# Specify which claims to derive user information from
# Supports any valid JSON path with the JWT payload
OIDC_USERNAME_CLAIM=preferred_username

# Display name for OIDC authentication
OIDC_DISPLAY_NAME=Zitadel

# Space separated auth scopes.
OIDC_SCOPES=openid profile email


# –––––––––––––––– OPTIONAL ––––––––––––––––

# Base64 encoded private key and certificate for HTTPS termination. This is only
# required if you do not use an external reverse proxy. See documentation:
# https://wiki.generaloutline.com/share/1c922644-40d8-41fe-98f9-df2b67239d45
SSL_KEY=
SSL_CERT=

# If using a Cloudfront/Cloudflare distribution or similar it can be set below.
# This will cause paths to javascript, stylesheets, and images to be updated to
# the hostname defined in CDN_URL. In your CDN configuration the origin server
# should be set to the same as URL.
CDN_URL=

# Auto-redirect to https in production. The default is true but you may set to
# false if you can be sure that SSL is terminated at an external loadbalancer.
FORCE_HTTPS=false

# Have the installation check for updates by sending anonymized statistics to
# the maintainers
ENABLE_UPDATES=false

# How many processes should be spawned. As a reasonable rule divide your servers
# available memory by 512 for a rough estimate
WEB_CONCURRENCY=1

# Override the maximum size of document imports, could be required if you have
# especially large Word documents with embedded imagery
MAXIMUM_IMPORT_SIZE=5120000

# You can remove this line if your reverse proxy already logs incoming http
# requests and this ends up being duplicative
DEBUG=http

# Configure lowest severity level for server logs. Should be one of
# error, warn, info, http, verbose, debug and silly
LOG_LEVEL=info

# For a complete Slack integration with search and posting to channels the
# following configs are also needed, some more details
# => https://wiki.generaloutline.com/share/be25efd1-b3ef-4450-b8e5-c4a4fc11e02a
#
SLACK_VERIFICATION_TOKEN=
SLACK_APP_ID=
SLACK_MESSAGE_ACTIONS=true

# Optionally enable google analytics to track pageviews in the knowledge base
GOOGLE_ANALYTICS_ID=

# Optionally enable Sentry (sentry.io) to track errors and performance,
# and optionally add a Sentry proxy tunnel for bypassing ad blockers in the UI:
# https://docs.sentry.io/platforms/javascript/troubleshooting/#using-the-tunnel-option)
SENTRY_DSN=
SENTRY_TUNNEL=

# To support sending outgoing transactional emails such as "document updated" or
# "you've been invited" you'll need to provide authentication for an SMTP server
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USERNAME=
SMTP_PASSWORD=
SMTP_FROM_EMAIL=
SMTP_REPLY_EMAIL=
SMTP_TLS_CIPHERS=TLSv1.2
SMTP_SECURE=false

# The default interface language. See translate.getoutline.com for a list of
# available language codes and their rough percentage translated.
DEFAULT_LANGUAGE=ko_KR

# Optionally enable rate limiter at application web server
RATE_LIMITER_ENABLED=true

# Configure default throttling parameters for rate limiter
RATE_LIMITER_REQUESTS=1000
RATE_LIMITER_DURATION_WINDOW=60

# Iframely API config
# IFRAMELY_URL=
# IFRAMELY_API_KEY=

# Enable unsafe-inline in script-src CSP directive
# Setting it to true allows React dev tools add-on in
# Firefox to successfully detect the project
DEVELOPMENT_UNSAFE_INLINE_CSP=false
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
