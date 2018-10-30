App for the Microservices 

```
Edit Dockerfiles

```


Edit services/config.yaml
```
AMQP_URI: amqp://${RABBIT_USER:guest}:${RABBIT_PASSWORD:guest}@${RABBIT_HOST:rabbitmq}:${RABBIT_PORT:5673}/
RABBIT_CTL_URI: http://guest:guest@rabbitmq:15673
WEB_SERVER_ADDRESS: '0.0.0.0:5000'
RPC_EXCHANGE: 'simplebank-rpc'
```
```
docker-compose build
```
```
Create dockerhub repos @https://hub.docker.com/u/davarski/

for i in `docker images|grep chapter12k8s|awk '{print $1}'`; do docker tag $i:latest davarski/$i:latest;done

for i in `docker images|grep chapter12k8s|awk '{print $1}'`; do docker push $i:latest;done
```
```
generating k8s from docker-compose.yaml
kompose convert 
INFO Kubernetes file "account-transactions-service.yaml" created 
INFO Kubernetes file "fees-service.yaml" created  
INFO Kubernetes file "gateway-service.yaml" created 
INFO Kubernetes file "market-service.yaml" created 
......

Edit *-deployment.yaml and setup image: - image: davarski/chapter12k8s_{service}:latest

Deploy ... kubectl create -f account-transactions-deployment.yaml,account-transactions-service.yaml,...

davar@home ~/LABS/microservices-in-action/chapter7-k8s $ kubectl get pod --namespace defaultNAME                                   READY   STATUS    RESTARTS   AGE
account-transactions-5995ff6ff-tzsfh   1/1     Running   1          50m
fees-6b8df49bb5-96c6n                  1/1     Running   0          50m
gateway-c74956cfd-bkrdg                1/1     Running   0          50m
hello-65ff564d-dvhq5                   1/1     Running   2          1d
market-8d4b85fd8-z2qq5                 1/1     Running   0          50m
orders-56cff44957-thvjr                1/1     Running   0          29m
rabbitmq-7bfffd5f7c-nb6sz              1/1     Running   0          1h
redis-776cf5d95c-x42vc                 1/1     Running   0          55m
statsd-agent-646b6c47bd-grpdl          1/1     Running   0          1h
davar@home ~/LABS/microservices-in-action/chapter7-k8s $ kubectl get svc --namespace default
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)              AGE
account-transactions   ClusterIP   10.100.13.195    <none>        5003/TCP             49m
fees                   ClusterIP   10.99.15.121     <none>        5004/TCP             49m
gateway                ClusterIP   10.100.169.37    <none>        5001/TCP             49m
hello                  ClusterIP   10.109.91.30     <none>        80/TCP               1d
kubernetes             ClusterIP   10.96.0.1        <none>        443/TCP              3d
market                 ClusterIP   10.102.3.54      <none>        5005/TCP             49m
orders                 ClusterIP   10.98.165.21     <none>        5002/TCP             29m
rabbitmq               ClusterIP   10.104.42.208    <none>        5673/TCP,15673/TCP   1h
redis                  ClusterIP   10.108.88.43     <none>        6380/TCP             55m
statsd-agent           ClusterIP   10.107.209.171   <none>        8126/TCP             1h

davar@home ~/LABS/microservices-in-action/chapter7-k8s $ minikube ssh
                         _             _            
            _         _ ( )           ( )           
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __  
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

$ curl -X POST http://10.100.169.37:5001/shares/sell -H 'cache-control: no-cache' -H 'content-type: application/json'
{"ok": "sell order 2e6ff024-ccbf-45f3-adeb-9768f6aa9939 placed"}$ 

$ docker logs c901974b3a10
starting services: orders_service
Connected to amqp://guest:**@rabbitmq:5673//
```
```
davar@home ~/LABS/microservices-in-action/chapter7-k8s $ cat gateway-service.yaml 
apiVersion: v1
kind: Service
metadata:
  annotations:
    kompose.cmd: kompose convert
    kompose.version: 1.1.0 (36652f6)
  creationTimestamp: null
  labels:
    io.kompose.service: gateway
  name: gateway
spec:
  type: NodePort
  ports:
  - name: "5001"
    port: 5001
    targetPort: 5000
    nodePort: 30623
  selector:
    io.kompose.service: gateway
status:
  loadBalancer: {}

  480  kubectl delete svc/gateway
  481  kubectl create -f gateway-service.yaml 


davar@home ~/LABS/microservices-in-action/chapter7-k8s $ curl -X POST http://`minikube ip`:30623/shares/sell -H 'cache-control: no-cache' -H 'content-type: application/json'
{"ok": "sell order bfbd8605-4362-40c9-8512-bfc1e44a5929 placed"}davar@home ~/LABS/microservices-in-action/chapter7-k8s $
```
