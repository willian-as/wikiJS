O Wiki.JS se divide entre o banco (PostgreSQL) e a aplicaçao em si. Com isso, foram criados os seguintes manifestos:

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

Apos a criaçao de todos os recursos, podemos verifica-los e acessar a aplicaçao.

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