---
order: 100
icon: note
tags: [linux, tip]
---

# Ubuntu Mirror 변경

+++ Script 1

## Code

```sh
sed -i 's/htt[p|ps]:\/\/archive.ubuntu.com\/ubuntu\//mirror:\/\/mirrors.ubuntu.com\/mirrors.txt/g' /etc/apt/sources.list
```

+++ Script 2 (kakao)

```sh
sed -i 's/htt[p|ps]:\/\/archive.ubuntu.com\/ubuntu\//https:\/\/mirror.kakao.com\/ubuntu/g' /etc/apt/sources.list
```

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
