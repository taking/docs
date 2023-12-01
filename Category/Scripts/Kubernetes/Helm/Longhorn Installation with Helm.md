---
order: 100
icon: note
tags: [kubernetes, helm]
---

# Longhorn Installation with Helm

!!!

이 페이지에서는 [Longhorn](https://longhorn.io)을 Helm을 통해서 설치하는 방법을 다루고 있습니다.

!!!

## What is `Longhorn`?

[`Longhorn`](https://longhorn.io)은 Kubernetes에서 활용할 수 있는 오픈소스 분산 블록 스토리지 시스템(distributed block storage system)이며, Rancher Lab에서 개발되었습니다.

![Longhorn Dashboard Screenshot](https://longhorn.io/img/screenshots/getting-started/longhorn-ui.png)

## Prerequisites

- [Kubernetes v1.18+](https://kubernetes.io) is installed using either [`helm v3.2.0+`](https://helm.sh).
- (All Nodes) folder (`mkdir -p /data/longhorn`)
- (All Nodes) iscsi (`apt install open-iscsi -y`)

---

## Install

+++ Helm update

```
helm repo add longhorn https://charts.longhorn.io
helm repo update longhorn
```

+++ Helm Install

```
mkdir -p /data/longhorn
helm install longhorn longhorn/longhorn \
    --create-namespace \
    --namespace longhorn-system \
    --set defaultSettings.defaultDataPath="/data/longhorn" \
    --set defaultSettings.defaultDataLocality="best-effort"
```

+++ Default set StorageClass

```
kubectl patch storageclass longhorn -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
