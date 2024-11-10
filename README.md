# Simple deployment demo
this demo will example, how we deploy application,
and how to upgarde version.
`version1.yaml` to `version2.yaml`

then if heath check has some issue, will stop rolling update.
`version2.yaml` to `version3.yaml`

last, we will demo how to change target pod as blue green deployment.
`version3.yaml` to `version4.yaml`

# How to deploy
in this demo, we will use `kubectl` to deploy application.
and we using `nginx` as example
```
# apply version1
kubectl apply -f version1.yaml
# cehck status
kubectl get pods

# you will see 2 pods running
# NAME                                READY   STATUS    RESTARTS   AGE
# nginx-deployment-6cf764d747-l9sgr   1/1     Running   0          23m
# nginx-deployment-6cf764d747-m2ffv   1/1     Running   0          23m

# check service api
curl http://localhost:8080
# you will see
# version 1
```

you can upgrade deployment by apply new version
in this case, we will upgrade deployemnt and configmap.

also, change env variable `VERSION` to `2`,
let deployment know, we want to upgrade to version 2
```
# apply version2
kubectl apply -f version2.yaml
# cehck status
kubectl get pods

# check service api
curl http://localhost:8080
# you will see
# version 2
```

and we will apply a error version, to see how rolling update stop
```
# apply version3
kubectl apply -f version3.yaml
# cehck status
kubectl get pods
# you can see, one pod is not running
# NAME                                READY   STATUS    RESTARTS     AGE
# nginx-deployment-57b8975498-7cwq2   1/1     Running   0            6m40s
# nginx-deployment-57b8975498-9twqv   1/1     Running   0            6m47s
# nginx-deployment-58c66498bf-fjpl8   0/1     Running   1 (3s ago)   12s


# check service api
curl http://localhost:8080
# you will see
# version 2
```

last we will apply new version, 
let service change target pod as blue green deployment
```
# apply version4
kubectl apply -f version4.yaml
# cehck status
kubectl get pods
# you can see, new deployemnt nginx2 is running
# NAME                                 READY   STATUS             RESTARTS      AGE
# nginx-deployment-57b8975498-7cwq2    1/1     Running            0             24m
# nginx-deployment-57b8975498-9twqv    1/1     Running            0             24m
# nginx-deployment-58c66498bf-fjpl8    0/1     CrashLoopBackOff   11 (7s ago)   17m
# nginx2-deployment-6978745784-jdcqq   1/1     Running            0             13s
# nginx2-deployment-6978745784-llpnt   1/1     Running            0             13s
# nginx2-deployment-6978745784-lmfzw   1/1     Running            0             13s

# check service api
curl http://localhost:8080
# you will see
# version 4
```
