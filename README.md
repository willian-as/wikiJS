# Rodando o [Wiki.js](https://github.com/Requarks/wiki) em um cluster local de Kubernetes

## Criação do cluster

Utilizando o [k3d](https://github.com/rancher/k3d), criamos o cluster com 3 nodes (1 server e 2 workers):

```
$ k3d cluster create esig --agents 2
```

## Subindo o projeto

### Namespace da aplicação
```
$ kubectl create -f postgres/Namespace.yaml
namespace/wikijs created
```

### Criação dos objetos
```
$ kubectl create -f postgres/Service.yaml
$ kubectl create -f postgres/PersistentVolume.yaml
$ kubectl create -f postgres/Secret.yaml
$ kubectl create -f postgres/StatefulSet.yaml
$ kubectl create -f wiki/Service.yaml
$ kubectl create -f wiki/Deployment.yaml
```

### Overview
```
$ kubectl get all -n wikijs 
NAME                                 READY   STATUS        RESTARTS   AGE
pod/postgres-statefulset-0           1/1     Running       0          4h29m
pod/postgres-statefulset-1           1/1     Running       0          61m
pod/wikijs-deploy-5fd9cfb959-mjbx7   1/1     Running       0          29s
pod/svclb-wikijs-service-rbtrf       1/1     Running       0          23s
pod/svclb-wikijs-service-5tnbg       1/1     Running       0          12s
pod/svclb-wikijs-service-88dtt       1/1     Running       0          3s

NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/postgres-service   ClusterIP      10.43.48.94     <none>        5432/TCP         4h29m
service/wikijs-service     LoadBalancer   10.43.162.253   172.17.0.2    3000:32743/TCP   55m

NAME                                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/svclb-wikijs-service   3         3         3       3            3           <none>          55m

NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/wikijs-deploy   1/1     1            1           13m

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/wikijs-deploy-5fd9cfb959   1         1         1       29s

NAME                                    READY   AGE
statefulset.apps/postgres-statefulset   2/2     4h29m
```
### Acesso à aplicação

![Captura](https://github.com/willian-as/wikiJS/blob/main/images/Captura%20de%20tela%20de%202020-11-01%2019-30-50.png)
