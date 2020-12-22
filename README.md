---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-qfusion
  name: qfusion-axure
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  selector:
    app: nginx-qfusion
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: qfusion-axure
  labels:
    app: nginx-qfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-qfusion
  template:
    metadata:
      labels:
        app: nginx-qfusion
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: work-dir
      initContainers:
      - name: git
        image: registry.woqutech.com/library/golang:1.12
        command: ['sh', '-c', "cd /mnt ; git clone https://qfusion-compile:qfusion-compile@gitlab.woqutech.com/pengfei.ma/qfusion-axure ."]
        volumeMounts:
        - mountPath: /mnt
          name: work-dir
      volumes:
      - name: work-dir
        emptyDir: {}
