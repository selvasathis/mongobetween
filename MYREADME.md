## Apply the Deployment and Service 
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongobetween-test-svc
  namespace: mongobetween
  labels:
    app: mongobetween-test
spec:
  type: NodePort
  selector:
    app: mongobetween-test-pod
  ports:
    - name: mongo
      port: 27016
      targetPort: 27016
      protocol: TCP
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: mongobetween-test
  name: mongobetween-test-deploy
  namespace: mongobetween
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongobetween-test-pod
  template:
    metadata:
      labels:
        app: mongobetween-test-pod
    spec:
      containers:
      - name: mongobetween
        image: deepak130598/mongobetween:v1
        command: ["/mongobetween"]
        args: [
          "-pretty",
          "-loglevel",
          "debug", 
          "-enable-sdam-metrics",
          "-enable-sdam-logging",
          "0.0.0.0:27016=mongodb+srv://<username>:<password>@mongobetween-test.xs4ln.mongodb.net/?maxPoolSize=200"
        ] # use password without any special character
        imagePullPolicy: Always
        ports:
        - containerPort: 27016
          name: mongo
          protocol: TCP
        livenessProbe:
          tcpSocket:
            port: 27016
          initialDelaySeconds: 15
          periodSeconds: 20
        readinessProbe:
          tcpSocket:
            port: 27016
          initialDelaySeconds: 10
          periodSeconds: 5
        resources:
          limits:
            cpu: 1
            memory: 1Gi
          requests:
            cpu: 1
            memory: 1Gi
```

reference 
https://blogs.opsflow.in/mongobetween-a-smart-proxy-for-mongodb-7c53b4047ba3
