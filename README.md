# Rodando o [Wiki.js](https://github.com/Requarks/wiki) em um cluster local de Kubernetes

## Criação do cluster

Utilizando o [k3d](https://github.com/rancher/k3d), criamos o cluster com 3 nodes (1 server e 2 workers):

```
$ k3d cluster create wikijs --agents 2 --api-port 6550 -p "8081:80@loadbalancer"
```

## Subindo o projeto

### Criação dos objetos
```
$ kubectl create -f postgres/
$ kubectl create -f wiki/
```

### Overview
```
$ kubectl get all -n wikijs -o wide
NAME                          READY   STATUS    RESTARTS   AGE   IP          NODE               NOMINATED NODE   READINESS GATES
pod/postgres-0                1/1     Running   0          75m   10.42.0.4   k3d-dtp-agent-0    <none>           <none>
pod/wikijs-5bc4c7444d-jcwvh   1/1     Running   0          37m   10.42.3.7   k3d-dtp-server-0   <none>           <none>

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE   SELECTOR
service/postgres   ClusterIP   10.43.110.146   <none>        5432/TCP   75m   app=postgres
service/wikijs     ClusterIP   10.43.64.243    <none>        3000/TCP   37m   app=wikijs

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES            SELECTOR
deployment.apps/wikijs   1/1     1            1           37m   wikijs       requarks/wiki:2   app=wikijs

NAME                                DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES            SELECTOR
replicaset.apps/wikijs-5bc4c7444d   1         1         1       37m   wikijs       requarks/wiki:2   app=wikijs,pod-template-hash=5bc4c7444d

NAME                        READY   AGE   CONTAINERS   IMAGES
statefulset.apps/postgres   1/1     75m   postgres     postgres:12
```
### Acesso à aplicação

```
$ http://localhost:8081/
```

![Captura](https://github.com/willian-as/wikiJS/blob/main/images/Captura%20de%20tela%20de%202020-11-01%2019-30-50.png)
