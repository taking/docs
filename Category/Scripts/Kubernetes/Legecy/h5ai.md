---
order: 100
icon: note
tags: [kubernetes]
---

# h5ai

!!!

이 페이지에서는 [h5ai](https://larsjung.de/h5ai/)을 kubernetes에서 설치하는 방법을 다루고 있습니다.

!!!

## What is `h5ai`?

[`h5ai`](ttps://larsjung.de/h5ai/)는 모던하고 심플한 파일 서버로 간단하게 설치하여 사용할 수 있다.

![h5ai Screenshot](/static/h5ai_01.png)

## Prerequisites

- [Kubernetes v1.18+](https://kubernetes.io) is installed

---

## Install

+++ All-in-one

```
cat <<EOF | kubectl apply -f -
---
kind: Pod
apiVersion: v1
metadata:
  name: h5ai
  labels:
    app: h5ai
spec:
  containers:
    - name: h5ai
      image: awesometic/h5ai:latest
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
      volumeMounts:
        - mountPath: "/h5ai"
          name: storage-volume
      env:
        - name: PUID
          value: "1000"
        - name: PGID
          value: "1000"
        - name: TZ
          value: "Asia/Seoul"
  volumes:
    - name: storage-volume
      hostPath:
        path: /mnt/nfs
---
kind: Service
apiVersion: v1
metadata:
  name: h5ai
spec:
  selector:
    app: h5ai
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000
  type: NodePort
EOF
```

+++ Pod

```
kind: Pod
apiVersion: v1
metadata:
  name: h5ai
  labels:
    app: h5ai
spec:
  containers:
    - name: h5ai
      image: awesometic/h5ai:latest
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 80
      volumeMounts:
        - mountPath: "/h5ai"
          name: storage-volume
      env:
        - name: PUID
          value: "1000"
        - name: PGID
          value: "1000"
        - name: TZ
          value: "Asia/Seoul"
  volumes:
    - name: storage-volume
      hostPath:
        path: /mnt/nfs
```

+++ Service

```
kind: Service
apiVersion: v1
metadata:
  name: h5ai
spec:
  selector:
    app: h5ai
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000
  type: NodePort
```

+++

That's it! :tada:

---

## memo

!!!

변경사항(업데이트)가 있을 시, 안내 없이 변경 될 수 있습니다.

!!!
