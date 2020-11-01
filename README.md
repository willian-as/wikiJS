# Rodando o [Wiki.js](https://github.com/Requarks/wiki) em um cluster Kubernetes

## Criação do cluster

Utilizando o [k3d](https://github.com/rancher/k3d), criamos o cluster com 3 nodes (1 master e 2 workers):

```
$ k3d cluster create esig --agents 2
INFO[0000] Created network 'k3d-esig'                   
INFO[0000] Created volume 'k3d-esig-images'             
INFO[0001] Creating node 'k3d-esig-server-0'            
INFO[0013] Creating node 'k3d-esig-agent-0'             
INFO[0017] Creating node 'k3d-esig-agent-1'             
INFO[0020] Creating LoadBalancer 'k3d-esig-serverlb'    
INFO[0024] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access 
INFO[0040] Successfully added host record to /etc/hosts in 4/4 nodes and to the CoreDNS ConfigMap 
INFO[0040] Cluster 'esig' created successfully!         
INFO[0040] You can now use it like this:                
kubectl cluster-info
```

O Wiki.js se divide entre o banco (PostgreSQL) e a aplicaçao em si.
Com isso, foram criados os seguintes manifestos:

```
~/projeto-esig/wikijs/postgres$ ls -l
total 20
-rw-rw-r-- 1 willian willian   82 nov  1 14:15 Namespace.yaml
-rw-rw-r-- 1 willian willian  424 nov  1 14:46 PersistentVolume.yaml
-rw-rw-r-- 1 willian willian  200 nov  1 14:36 Secret.yaml
-rw-rw-r-- 1 willian willian  182 nov  1 14:15 Service.yaml
-rw-rw-r-- 1 willian willian 1227 nov  1 14:45 StatefulSet.yaml

~/projeto-esig/wikijs/wiki$ ls -l
total 8
-rw-rw-r-- 1 willian willian 936 nov  1 14:33 Deployment.yaml
-rw-rw-r-- 1 willian willian 199 nov  1 14:50 Service.yaml
```

Apos a criação de todos os recursos, podemos verificá-los e acessar a aplicação:

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

![Captura](https://github.com/willian-as/wikiJS/blob/main/images/Captura%20de%20tela%20de%202020-11-01%2019-30-50.png)
