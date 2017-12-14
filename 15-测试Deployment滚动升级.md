# 测试Deployment的rolling-update

kubernetes Deployment是一个更高级别的抽象，就像文章开头那幅示意图那样，Deployment会创建一个Replica Set，用来保证Deployment中Pod的副本数。由于kubectl rolling-update仅支持replication controllers，因此要想rolling-updata deployment中的Pod，你需要修改Deployment自己的manifest文件并应用。这个修改会创建一个新的Replica Set，在scale up这个Replica Set的Pod数的同时，减少原先的Replica Set的Pod数，直至zero。而这一切都发生在Server端，并不需要kubectl参与。
我们同样来看一个例子。
我们建立第一个版本的deployment manifest文件：deployment-demo-v0.1.yaml。
```bash
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: deployment-demo
spec:
  replicas: 4
  selector:
    matchLabels:
      app: deployment-demo-nginx
  minReadySeconds: 10
  template:
    metadata:
      labels:
        app: deployment-demo-nginx
        version: v0.1
    spec:
      containers:
        - name: deployment-demo
          image: nginx:1.10.1
          ports:
            - containerPort: 80
              protocol: TCP
          env:
            - name: DEPLOYMENT_DEMO_VER
              value: v0.1
```
