#references
#--------------------Kubernetics----------------
https://www.tutorialspoint.com/kubernetes/kubernetes_architecture.htm
https://www.javatpoint.com/kubernetes
https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/

https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

#------------------------------------------------------------


# kubernetes
Kubernetes learning

C:\Users\echypal>minikube config set memory 2048
! These changes will take effect upon a minikube delete and then a minikube start

C:\Users\echypal>minikube config set cpus 1
! These changes will take effect upon a minikube delete and then a minikube start

#location of images
C:\Users\echypal\.minikube\cache


# start minicube with custome resources and docker runtime


note: docker desktop is running 
note: Docker desktop with wsl2 as hyperviser running, minikube started with docker driver: Installation Successful but external IP not working
      Docker desktop with hyper-v as hyperviser running, minikube started with docker driver: Installation Successful but external IP not working
      Docker desktop not running, minikube started with hyper-v as hyperviser: Installation Successful with external switch.
      (https://minikube.sigs.k8s.io/docs/drivers/hyperv/) 
	  (C:\Users\echypal>minikube start --memory=2048 --cpus=2 --disk-size=5g --driver=hyperv --hyperv-use-external-switch)
       it will create new virtual switch. Hyper-V -> select minikube -> Virtual Switch Manager -> minikibe  
	  
minikube delete --all --purge
 

C:\Users\echypal>minikube start --memory=2048 --cpus=2 --disk-size=5g  [--driver=hyperv] [--alsologtostderr]

C:\Users\echypal>minikube start --memory=2048 --cpus=2 --disk-size=5g --driver=hyperv --hyperv-use-external-switch  (worked in windows, Minikube + hyperv, no docker)

* minikube v1.21.0 on Microsoft Windows 10 Enterprise 10.0.18363 Build 18363
* Automatically selected the docker driver. Other choices: hyperv, virtualbox, ssh
* Starting control plane node minikube in cluster minikube
* Pulling base image ...
    > gcr.io/k8s-minikube/kicbase...: 359.09 MiB / 359.09 MiB  100.00% 678.07 K
* Creating docker container (CPUs=2, Memory=2048MB) ...
* Preparing Kubernetes v1.20.7 on Docker 20.10.7 ...
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Verifying Kubernetes components...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* Enabled addons: storage-provisioner, default-storageclass
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

C:\Users\echypal>

# minikube status

C:\Users\echypal>minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured


C:\Users\echypal>

# minikube nodes
C:\Users\echypal>kubectl get nodes
NAME       STATUS   ROLES                  AGE     VERSION
minikube   Ready    control-plane,master   4m22s   v1.20.7


# minikube version

C:\Users\echypal>minikube version
minikube version: v1.21.0
commit: 76d74191d82c47883dc7e1319ef7cebd3e00ee11

C:\Users\echypal>kubectl version
Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.3", GitCommit:"1e11e4a2108024935ecfcb2912226cedeafd99df", GitTreeState:"clean", BuildDate:"2020-10-14T12:50:19Z", GoVersion:"go1.15.2", Compiler:"gc", Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.7", GitCommit:"132a687512d7fb058d0f5890f07d4121b3f0a2e2", GitTreeState:"clean", BuildDate:"2021-05-12T12:32:49Z", GoVersion:"go1.15.12", Compiler:"gc", Platform:"linux/amd64"}

# Check pod
C:\Users\echypal>kubectl get pod
No resources found in default namespace.

#check services

C:\Users\echypal>kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7m17s

#create deployment of nginx
C:\Users\echypal>kubectl create deployment nginx-depl --image=nginx
deployment.apps/nginx-depl created

#check deployment

C:\Users\echypal>kubectl get deployment
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
nginx-depl   0/1     1            0           25s

#check pods
C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS              RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   0/1     ContainerCreating   0          34s

C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS              RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   0/1     ContainerCreating   0          41s

#check replicaset
C:\Users\echypal>kubectl get replicaset
or
C:\Users\echypal>kubectl get rs

NAME                    DESIRED   CURRENT   READY   AGE
nginx-depl-5c8bf76b5b   1         1         0       55s

C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS              RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   0/1     ContainerCreating   0          73s

C:\Users\echypal>


C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   1/1     Running   0          110s

C:\Users\echypal>

#View cluster events:
C:\Users\echypal>kubectl get events
LAST SEEN   TYPE      REASON                    OBJECT                                   MESSAGE
38m         Normal    Starting                  node/minikube                            Starting kubelet.
38m         Normal    NodeHasSufficientMemory   node/minikube                            Node minikube status is now: NodeHasSufficientMemory
38m         Normal    NodeHasNoDiskPressure     node/minikube                            Node minikube status is now: NodeHasNoDiskPressure
38m         Normal    NodeHasSufficientPID      node/minikube                            Node minikube status is now: NodeHasSufficientPID
38m         Normal    NodeAllocatableEnforced   node/minikube                            Updated Node Allocatable limit across pods
38m         Normal    Starting                  node/minikube                            Starting kube-proxy.
38m         Normal    RegisteredNode            node/minikube                            Node minikube event: Registered Node minikube in Controller
38m         Normal    SandboxChanged            pod/mongo-express-78fcf796b8-pfrdc       Pod sandbox changed, it will be killed and re-created.
37m         Normal    Pulling                   pod/mongo-express-78fcf796b8-pfrdc       Pulling image "mongo-express"
38m         Normal    Pulled                    pod/mongo-express-78fcf796b8-pfrdc       Successfully pulled image "mongo-express" in 23.5480394s
37m         Normal    Created                   pod/mongo-express-78fcf796b8-pfrdc       Created container mongo-express
37m         Normal    Started                   pod/mongo-express-78fcf796b8-pfrdc       Started container mongo-express
37m         Warning   BackOff                   pod/mongo-express-78fcf796b8-pfrdc       Back-off restarting failed container
37m         Normal    Pulled                    pod/mongo-express-78fcf796b8-pfrdc       Successfully pulled image "mongo-express" in 3.992821976s
38m         Normal    SandboxChanged            pod/mongodb-deployment-8f6675bc5-bqckw   Pod sandbox changed, it will be killed and re-created.
38m         Normal    Pulling                   pod/mongodb-deployment-8f6675bc5-bqckw   Pulling image "mongo"
38m         Normal    Pulled                    pod/mongodb-deployment-8f6675bc5-bqckw   Successfully pulled image "mongo" in 4.814404629s
38m         Normal    Created                   pod/mongodb-deployment-8f6675bc5-bqckw   Created container mongodb
38m         Normal    Started                   pod/mongodb-deployment-8f6675bc5-bqckw   Started container mongodb

C:\Users\echypal>

#edit image version in deployment

C:\Users\echypal>kubectl edit deployment nginx-depl
deployment.apps/nginx-depl edited

C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS              RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   1/1     Running             0          5m47s
nginx-depl-7fc44fc5d4-26qpw   0/1     ContainerCreating   0          10s

C:\Users\echypal>

C:\Users\echypal>kubectl get replicaset
NAME                    DESIRED   CURRENT   READY   AGE
nginx-depl-5c8bf76b5b   1         1         1       6m11s
nginx-depl-7fc44fc5d4   1         1         0       34s

C:\Users\echypal>

C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS        RESTARTS   AGE
nginx-depl-5c8bf76b5b-cgpwx   0/1     Terminating   0          6m36s
nginx-depl-7fc44fc5d4-26qpw   1/1     Running       0          59s

#check logs of nginx pod (no log)
C:\Users\echypal>kubectl logs nginx-depl-7fc44fc5d4-26qpw

C:\Users\echypal>

#create another deployment mongo-db and check logs of the pod
note: 
-For pod: kubectl logs <pod-name>
-For K8s resources: kubectl describe <resource like pod|deployment|service>  <name of the resource>


1) C:\Users\echypal>kubectl create deployment mongo-depl --image=mongo
deployment.apps/mongo-depl created

2) C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS              RESTARTS   AGE
mongo-depl-5fd6b7d4b4-tk44q   0/1     ContainerCreating   0          19s
nginx-depl-7fc44fc5d4-26qpw   1/1     Running             0          4m39s

3) C:\Users\echypal>kubectl logs mongo-depl-5fd6b7d4b4-tk44q
Error from server (BadRequest): container "mongo" in pod "mongo-depl-5fd6b7d4b4-tk44q" is waiting to start: ContainerCreating

4) C:\Users\echypal>kubectl describe pod mongo-depl-5fd6b7d4b4-tk44q
Name:           mongo-depl-5fd6b7d4b4-tk44q
Namespace:      default
Priority:       0
Node:           minikube/192.168.49.2
Start Time:     Sun, 27 Jun 2021 17:41:06 +0530
Labels:         app=mongo-depl
                pod-template-hash=5fd6b7d4b4
Annotations:    <none>
Status:         Pending
IP:
IPs:            <none>
Controlled By:  ReplicaSet/mongo-depl-5fd6b7d4b4
Containers:
  mongo:
    Container ID:
    Image:          mongo
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z6vl8 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-z6vl8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z6vl8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  112s  default-scheduler  Successfully assigned default/mongo-depl-5fd6b7d4b4-tk44q to minikube
  Normal  Pulling    111s  kubelet            Pulling image "mongo"

C:\Users\echypal>

5) C:\Users\echypal>kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
mongo-depl-5fd6b7d4b4-tk44q   1/1     Running   0          4m29s
nginx-depl-7fc44fc5d4-26qpw   1/1     Running   0          8m49s

6) C:\Users\echypal>kubectl describe pod mongo-depl-5fd6b7d4b4-tk44q
Name:         mongo-depl-5fd6b7d4b4-tk44q
Namespace:    default
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Sun, 27 Jun 2021 17:41:06 +0530
Labels:       app=mongo-depl
              pod-template-hash=5fd6b7d4b4
Annotations:  <none>
Status:       Running
IP:           172.17.0.3
IPs:
  IP:           172.17.0.3
Controlled By:  ReplicaSet/mongo-depl-5fd6b7d4b4
Containers:
  mongo:
    Container ID:   docker://3eff7447cc384f3b67074330b2ee2683b8a98da7a35d42d0e05d6ca39d63b564
    Image:          mongo
    Image ID:       docker-pullable://mongo@sha256:aea63042abdf9a81319d6e9ffc6a381a6af53ae81a849c59d6269c3f233028a3
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 27 Jun 2021 17:43:53 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z6vl8 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-z6vl8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z6vl8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  5m7s   default-scheduler  Successfully assigned default/mongo-depl-5fd6b7d4b4-tk44q to minikube
  Normal  Pulling    5m6s   kubelet            Pulling image "mongo"
  Normal  Pulled     2m23s  kubelet            Successfully pulled image "mongo" in 2m43.2357232s
  Normal  Created    2m20s  kubelet            Created container mongo
  Normal  Started    2m20s  kubelet            Started container mongo

C:\Users\echypal>

#attach with running container of the deployment(terminal of pod container)

7) C:\Users\echypal>kubectl exec -it mongo-depl-5fd6b7d4b4-tk44q -- bin/bash

root@mongo-depl-5fd6b7d4b4-tk44q:/#
root@mongo-depl-5fd6b7d4b4-tk44q:/#

#delete deployment

1) C:\Users\echypal>kubectl get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/mongo-depl-5fd6b7d4b4-tk44q   1/1     Running   0          9m13s
pod/nginx-depl-7fc44fc5d4-26qpw   1/1     Running   0          13m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   27m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongo-depl   1/1     1            1           9m13s
deployment.apps/nginx-depl   1/1     1            1           19m

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/mongo-depl-5fd6b7d4b4   1         1         1       9m13s
replicaset.apps/nginx-depl-5c8bf76b5b   0         0         0       19m
replicaset.apps/nginx-depl-7fc44fc5d4   1         1         1       13m

2) C:\Users\echypal>kubectl delete deployment mongo-depl
deployment.apps "mongo-depl" deleted

3) C:\Users\echypal>kubectl get all
NAME                              READY   STATUS        RESTARTS   AGE
pod/mongo-depl-5fd6b7d4b4-tk44q   0/1     Terminating   0          10m
pod/nginx-depl-7fc44fc5d4-26qpw   1/1     Running       0          14m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   28m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-depl   1/1     1            1           19m

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-depl-5c8bf76b5b   0         0         0       19m
replicaset.apps/nginx-depl-7fc44fc5d4   1         1         1       14m

C:\Users\echypal>

# create deployment from configuration file
C:\Users\echypal>kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-depl         1/1     1            1           27m
nginx-deployment   2/2     2            2           45s

C:\Users\echypal>kubectl delete deployment nginx-depl
deployment.apps "nginx-depl" deleted

C:\Users\echypal>kubectl delete deployment nginx-deployment
deployment.apps "nginx-deployment" deleted

C:\Users\echypal>kubectl get deployment
No resources found in default namespace.

C:\Users\echypal>

C:\Users\echypal>kubectl apply -f C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-deployment.yaml
deployment.apps/nginx-deployment created

C:\Users\echypal>kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-f4b7bbcbc-9l44g   1/1     Running   0          5s
nginx-deployment-f4b7bbcbc-f7dfm   1/1     Running   0          5s

C:\Users\echypal>

C:\Users\echypal>kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           43s

C:\Users\echypal>

# update number of replicas in configuration file
.....
spec:
  replicas: 1
.....  


C:\Users\echypal>kubectl apply -f C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-deployment.yaml
deployment.apps/nginx-deployment configured

C:\Users\echypal>

C:\Users\echypal>kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           2m21s

C:\Users\echypal>kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-f4b7bbcbc-9l44g   1/1     Running   0          2m27s

C:\Users\echypal>

#stop node
C:\Users\echypal>minikube stop
* Stopping node "minikube"  ...
* Powering off "minikube" via SSH ...
* 1 nodes stopped.

C:\Users\echypal>

#create deployment and service from configuration files. Validate the configuration

1) C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-deployment.yaml"
deployment.apps/nginx-deployment created

2) C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-service.yaml"
service/nginx-service created

C:\Users\echypal>

3) C:\Users\echypal>kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-f4b7bbcbc-ct6vq   1/1     Running   0          54s

4) C:\Users\echypal>kubectl get service
NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes      ClusterIP   10.96.0.1    <none>        443/TCP   2d22h
nginx-service   ClusterIP   10.98.99.0   <none>        80/TCP    26s

C:\Users\echypal>

5) C:\Users\echypal>kubectl describe service nginx-service
Name:              nginx-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP:                10.98.99.0
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         172.17.0.3:8080
Session Affinity:  None
Events:            <none>

C:\Users\echypal>

C:\Users\echypal>kubectl get pod -o wide
NAME                               READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
nginx-deployment-f4b7bbcbc-ct6vq   1/1     Running   0          8m12s   172.17.0.3   minikube   <none>           <none>

C:\Users\echypal>

check endpoints is same as pod ip address

6) C:\Users\echypal>kubectl get deployment nginx-deployment -o yaml > "C:\repository\git\kubernetes\nginx-deployment-result.yaml"

C:\Users\echypal>

7) C:\Users\echypal>kubectl delete -f "C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-deployment.yaml"
deployment.apps "nginx-deployment" deleted

C:\Users\echypal>kubectl delete -f "C:\repository\git\youtube-tutorial-series\kubernetes-configuration-file-explained\nginx-service.yaml"
service "nginx-service" deleted

C:\Users\echypal>kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d23h

C:\Users\echypal>


#see minikube ip (doesn't work in local minikube windows)

$ echo $(minikube ip)
172.17.0.52
$ 

#get resources based on label

#pod
C:\Users\echypal>kubectl get pods -l app=kubernetes-bootcamp
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running   0          36m

C:\Users\echypal>

#service
C:\Users\echypal>kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
service/kubernetes-bootcamp exposed

C:\Users\echypal>kubectl get services -l app=kubernetes-bootcamp
NAME                  TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes-bootcamp   NodePort   10.106.58.119   <none>        8080:30352/TCP   5s

C:\Users\echypal>


# add label (key/value) to existing pod

C:\Users\echypal>kubectl label pods kubernetes-bootcamp-57978f5f5d-ppsmk version=P1 --overwrite=true
pod/kubernetes-bootcamp-57978f5f5d-ppsmk labeled

if using version label for first time, overwrite is not required. 

C:\Users\echypal>kubectl describe pod kubernetes-bootcamp-57978f5f5d-ppsmk
Name:         kubernetes-bootcamp-57978f5f5d-ppsmk
Namespace:    default
Priority:     0
Node:         minikube/192.168.43.210
Start Time:   Sat, 10 Jul 2021 21:18:01 +0530
Labels:       app=kubernetes-bootcamp
              pod-template-hash=57978f5f5d
              version=P1  
Annotations:  <none>
Status:       Running
IP:           172.17.0.8
IPs:
  IP:           172.17.0.8
Controlled By:  ReplicaSet/kubernetes-bootcamp-57978f5f5d
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://5a5d42b9c2acc8380e4e8590dd3c3c07b618222a407627031a3f6a70e25c6cf4
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 10 Jul 2021 21:18:41 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-q9qkn (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-q9qkn:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-q9qkn
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  45m   default-scheduler  Successfully assigned default/kubernetes-bootcamp-57978f5f5d-ppsmk to minikube
  Normal  Pulling    45m   kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
  Normal  Pulled     44m   kubelet            Successfully pulled image "gcr.io/google-samples/kubernetes-bootcamp:v1" in 38.733643531s
  Normal  Created    44m   kubelet            Created container kubernetes-bootcamp
  Normal  Started    44m   kubelet            Started container kubernetes-bootcamp
  
--search resources with the created label  
C:\Users\echypal>kubectl get pods -l version=P1
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running   0          49m

C:\Users\echypal>  


#scale up application 

C:\Users\echypal>kubectl get deployment
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           87m
mongo-express         1/1     1            1           5d5h
mongodb-deployment    3/4     4            3           5d5h

C:\Users\echypal>kubectl scale deployment kubernetes-bootcamp --replicas=4
deployment.apps/kubernetes-bootcamp scaled

C:\Users\echypal>kubectl get deployment
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   4/4     4            4           88m
mongo-express         1/1     1            1           5d5h
mongodb-deployment    3/4     4            3           5d5h

C:\Users\echypal>kubectl get rs
NAME                             DESIRED   CURRENT   READY   AGE
kubernetes-bootcamp-57978f5f5d   4         4         4       88m
mongo-express-78fcf796b8         1         1         1       5d5h
mongodb-deployment-8f6675bc5     4         4         3       5d5h

C:\Users\echypal>kubectl get service
NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes              ClusterIP      10.96.0.1       <none>        443/TCP          5d8h
kubernetes-bootcamp     NodePort       10.106.58.119   <none>        8080:30352/TCP   47m
mongo-express-service   LoadBalancer   10.103.70.137   <pending>     8081:30000/TCP   5d5h
mongodb-deployment      NodePort       10.111.83.93    <none>        8080:31556/TCP   7m30s
mongodb-service         ClusterIP      10.108.181.61   <none>        27017/TCP        5d5h

C:\Users\echypal>kubectl get pod -o wide
NAME                                   READY   STATUS             RESTARTS   AGE    IP            NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-57978f5f5d-8xrw2   1/1     Running            0          92s    172.17.0.14   minikube   <none>           <none>
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Running            0          92s    172.17.0.12   minikube   <none>           <none>
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running            0          89m    172.17.0.8    minikube   <none>           <none>
kubernetes-bootcamp-57978f5f5d-z8xpr   1/1     Running            0          92s    172.17.0.13   minikube   <none>           <none>
mongo-express-78fcf796b8-pfrdc         1/1     Running            2          5d5h   172.17.0.5    minikube   <none>           <none>
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running            2          26m    172.17.0.11   minikube   <none>           <none>
mongodb-deployment-8f6675bc5-bqckw     1/1     Running            9          5d5h   172.17.0.3    minikube   <none>           <none>
mongodb-deployment-8f6675bc5-lzgtj     0/1     CrashLoopBackOff   9          26m    172.17.0.10   minikube   <none>           <none>
mongodb-deployment-8f6675bc5-sk6q5     1/1     Running            7          26m    172.17.0.9    minikube   <none>           <none>

C:\Users\echypal>kubectl describe service kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   app=kubernetes-bootcamp
Annotations:              <none>
Selector:                 app=kubernetes-bootcamp
Type:                     NodePort
IP:                       10.106.58.119
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30352/TCP
Endpoints:                172.17.0.12:8080,172.17.0.13:8080,172.17.0.14:8080 + 1 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

C:\Users\echypal>minikube ip
192.168.43.210

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-8xrw2 | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-hc9mk | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-ppsmk | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-8xrw2 | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-hc9mk | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-z8xpr | v=1

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-57978f5f5d-hc9mk | v=1

We hit a different Pod with every request. This demonstrates that the load-balancing is working.

#scale down application

C:\Users\echypal>kubectl scale deployment kubernetes-bootcamp --replicas=2
deployment.apps/kubernetes-bootcamp scaled

C:\Users\echypal>kubectl get deployment
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   2/2     2            2           97m
mongo-express         1/1     1            1           5d5h
mongodb-deployment    2/4     4            2           5d5h

C:\Users\echypal>

C:\Users\echypal>kubectl get pod -o wide
NAME                                   READY   STATUS    RESTARTS   AGE    IP            NODE       NOMINATED NODE   READINESS GATES
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Running   0          14m    172.17.0.12   minikube   <none>           <none>
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running   0          102m   172.17.0.8    minikube   <none>           <none>
mongo-express-78fcf796b8-pfrdc         1/1     Running   2          5d5h   172.17.0.5    minikube   <none>           <none>
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running   4          38m    172.17.0.11   minikube   <none>           <none>
mongodb-deployment-8f6675bc5-bqckw     0/1     Error     10         5d5h   172.17.0.3    minikube   <none>           <none>
mongodb-deployment-8f6675bc5-lzgtj     0/1     Error     12         38m    172.17.0.10   minikube   <none>           <none>
mongodb-deployment-8f6675bc5-sk6q5     1/1     Running   11         38m    172.17.0.9    minikube   <none>           <none>

C:\Users\echypal>

#rolling update
note:  By default, the maximum number of Pods that can be unavailable during the update and the maximum number of new Pods that can be created, is one.Both options can be configured to either numbers or percentages (of Pods)

C:\Users\echypal>kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   2/2     2            2           15h
mongo-express         1/1     1            1           5d19h
mongodb-deployment    1/1     1            1           5d19h

C:\Users\echypal>kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Running   0          13h
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running   0          15h
mongo-express-78fcf796b8-pfrdc         1/1     Running   2          5d19h
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running   4          14h

To update the image of the application to version 2, use the set image command, followed by the deployment name and the new image version:

C:\Users\echypal>kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
deployment.apps/kubernetes-bootcamp image updated

C:\Users\echypal>kubectl get pod
NAME                                   READY   STATUS              RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Running             0          13h
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Running             0          15h
kubernetes-bootcamp-769746fd4-5rd8m    0/1     ContainerCreating   0          6s
mongo-express-78fcf796b8-pfrdc         1/1     Running             2          5d19h
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running             4          14h

C:\Users\echypal>kubectl get pod
NAME                                   READY   STATUS        RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Terminating   0          13h
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Terminating   0          15h
kubernetes-bootcamp-769746fd4-4vx8r    1/1     Running       0          4s
kubernetes-bootcamp-769746fd4-5rd8m    1/1     Running       0          15s
mongo-express-78fcf796b8-pfrdc         1/1     Running       2          5d19h
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running       4          14h

C:\Users\echypal>kubectl get pod
NAME                                   READY   STATUS        RESTARTS   AGE
kubernetes-bootcamp-57978f5f5d-hc9mk   1/1     Terminating   0          13h
kubernetes-bootcamp-57978f5f5d-ppsmk   1/1     Terminating   0          15h
kubernetes-bootcamp-769746fd4-4vx8r    1/1     Running       0          25s
kubernetes-bootcamp-769746fd4-5rd8m    1/1     Running       0          36s
mongo-express-78fcf796b8-pfrdc         1/1     Running       2          5d19h
mongodb-deployment-8f6675bc5-9rfwg     1/1     Running       4          14h

C:\Users\echypal>kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-769746fd4-4vx8r   1/1     Running   0          47s
kubernetes-bootcamp-769746fd4-5rd8m   1/1     Running   0          58s
mongo-express-78fcf796b8-pfrdc        1/1     Running   2          5d19h
mongodb-deployment-8f6675bc5-9rfwg    1/1     Running   4          14h

check image version is updated

C:\Users\echypal>kubectl describe pod kubernetes-bootcamp-769746fd4-4vx8r
Name:         kubernetes-bootcamp-769746fd4-4vx8r
Namespace:    default
Priority:     0
Node:         minikube/192.168.43.210
Start Time:   Sun, 11 Jul 2021 12:28:33 +0530
Labels:       app=kubernetes-bootcamp
              pod-template-hash=769746fd4
Annotations:  <none>
Status:       Running
IP:           172.17.0.9
IPs:
  IP:           172.17.0.9
Controlled By:  ReplicaSet/kubernetes-bootcamp-769746fd4
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://500d7842d67ce09ee9be960470ba87ddd6dceed2cac0e35ef4354fa91f036c77
    Image:          jocatalin/kubernetes-bootcamp:v2
    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:fb1a3ced00cecfc1f83f18ab5cd14199e30adc1b49aa4244f5d65ad3f5feb2a5
    Port:           <none>
    Host Port:      <none>
	
.....................	
#check rolling update is ok

C:\Users\echypal>minikube ip
192.168.43.210

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-4vx8r | v=2

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-5rd8m | v=2

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-4vx8r | v=2

C:\Users\echypal>

Every time you run the curl command, you will hit a different Pod. Notice that all Pods are running the latest version (v2).

C:\Users\echypal>kubectl rollout status deployment kubernetes-bootcamp
deployment "kubernetes-bootcamp" successfully rolled out

C:\Users\echypal>

#rollback an update
1) try to perform an update with wrong image version
C:\Users\echypal>kubectl set image deployment kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
deployment.apps/kubernetes-bootcamp image updated

C:\Users\echypal>
2) Check that update is not proper

C:\Users\echypal>kubectl get pod
NAME                                  READY   STATUS             RESTARTS   AGE
kubernetes-bootcamp-597654dbd-kdwth   0/1     ImagePullBackOff   0          19s
kubernetes-bootcamp-769746fd4-4vx8r   1/1     Running            0          11m
kubernetes-bootcamp-769746fd4-5rd8m   1/1     Running            0          11m
mongo-express-78fcf796b8-pfrdc        1/1     Running            2          5d19h
mongodb-deployment-8f6675bc5-9rfwg    1/1     Running            4          14h

C:\Users\echypal>kubectl get pod
NAME                                  READY   STATUS         RESTARTS   AGE
kubernetes-bootcamp-597654dbd-kdwth   0/1     ErrImagePull   0          117s
kubernetes-bootcamp-769746fd4-4vx8r   1/1     Running        0          12m
kubernetes-bootcamp-769746fd4-5rd8m   1/1     Running        0          13m
mongo-express-78fcf796b8-pfrdc        1/1     Running        2          5d19h
mongodb-deployment-8f6675bc5-9rfwg    1/1     Running        4          14h

C:\Users\echypal>

C:\Users\echypal>kubectl get deployment
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   2/2     1            2           15h
mongo-express         1/1     1            1           5d19h
mongodb-deployment    1/1     1            1           5d19h

C:\Users\echypal>kubectl rollout status deployment kubernetes-bootcamp
Waiting for deployment "kubernetes-bootcamp" rollout to finish: 1 out of 2 new replicas have been updated...


kubernetes tries to update one pod. it failed so it didn't tried the second one. 
Now you could understand the meaning of (By default, the maximum number of Pods that can be unavailable during the update and the maximum number of new Pods that can be created, is one)

3) check the reason for the fail is wrong image version

C:\Users\echypal>kubectl describe pod kubernetes-bootcamp-597654dbd-kdwth
Name:         kubernetes-bootcamp-597654dbd-kdwth
.......
Containers:
  kubernetes-bootcamp:
    Container ID:
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v10
    .......
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  4m7s                  default-scheduler  Successfully assigned default/kubernetes-bootcamp-597654dbd-kdwth to minikube
  Normal   Pulling    2m34s (x4 over 4m6s)  kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  Warning  Failed     2m31s (x4 over 4m2s)  kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v10": rpc error: code = Unknown desc = Error response from daemon: manifest for gcr.io/google-samples/kubernetes-bootcamp:v10 not found: manifest unknown: Failed to fetch "v10" from request "/v2/google-samples/kubernetes-bootcamp/manifests/v10".
  Warning  Failed     2m31s (x4 over 4m2s)  kubelet            Error: ErrImagePull
  Warning  Failed     2m2s (x6 over 4m2s)   kubelet            Error: ImagePullBackOff
  Normal   BackOff    108s (x7 over 4m2s)   kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v10"
  
4)  Check that all request are now served by another pod(with version 2)

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-5rd8m | v=2

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-5rd8m | v=2

C:\Users\echypal>curl 192.168.43.210:30352
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-769746fd4-5rd8m | v=2

5) rollback the deployment to last working version

C:\Users\echypal>kubectl rollout undo deployment kubernetes-bootcamp
deployment.apps/kubernetes-bootcamp rolled back

C:\Users\echypal>kubectl get deployment
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   2/2     2            2           15h
mongo-express         1/1     1            1           5d19h
mongodb-deployment    1/1     1            1           5d19h

C:\Users\echypal>kubectl get pod
NAME                                  READY   STATUS    RESTARTS   AGE
kubernetes-bootcamp-769746fd4-4vx8r   1/1     Running   0          25m
kubernetes-bootcamp-769746fd4-5rd8m   1/1     Running   0          25m
mongo-express-78fcf796b8-pfrdc        1/1     Running   2          5d19h
mongodb-deployment-8f6675bc5-9rfwg    1/1     Running   4          14h

C:\Users\echypal>

C:\Users\echypal>kubectl rollout status deployment kubernetes-bootcamp
deployment "kubernetes-bootcamp" successfully rolled out

The deployment is once again using a stable version of the app (v2). The rollback was successful.


#create complete web application browser->Mongo express external service -> Mongo express pod -> mongo db internal service->mongo db pod
note:
-Base64 command only works in Linux

[root@sowlx082 ~]# echo -n 'username' | base64
dXNlcm5hbWU=
[root@sowlx082 ~]# echo -n 'passsword' | base64
cGFzc3N3b3Jk
[root@sowlx082 ~]#

1) C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\demo-kubernetes-components\mongo-secret.yaml"
secret/mongodb-secret created

2) C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\demo-kubernetes-components\mongo.yaml"
deployment.apps/mongodb-deployment created
service/mongodb-service created

C:\Users\echypal>

C:\Users\echypal>kubectl describe service mongodb-service
Name:              mongodb-service
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=mongodb
Type:              ClusterIP
IP:                10.104.97.237
Port:              <unset>  27017/TCP
TargetPort:        27017/TCP
Endpoints:         172.17.0.3:27017
Session Affinity:  None
Events:            <none>

C:\Users\echypal>

C:\Users\echypal>kubectl get pod -o wide
NAME                                 READY   STATUS    RESTARTS   AGE    IP           NODE       NOMINATED NODE   READINESS GATES
mongodb-deployment-8f6675bc5-hsp5t   1/1     Running   0          7m4s   172.17.0.3   minikube   <none>           <none>

both End points and pod ip address are same hence ok.

3)C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\demo-kubernetes-components\mongo-configmap.yaml"
configmap/mongodb-configmap created

C:\Users\echypal>

 C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\demo-kubernetes-components\mongo-express.yaml"
deployment.apps/mongo-express created

C:\Users\echypal>

C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\demo-kubernetes-components\mongo-express.yaml"
deployment.apps/mongo-express unchanged
service/mongo-express-service created

4) C:\Users\echypal>kubectl get all
NAME                                     READY   STATUS    RESTARTS   AGE
pod/mongo-express-78fcf796b8-4n7vb       1/1     Running   0          109s
pod/mongodb-deployment-8f6675bc5-hsp5t   1/1     Running   0          17m

NAME                            TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes              ClusterIP      10.96.0.1       <none>        443/TCP          2d23h
service/mongo-express-service   LoadBalancer   10.96.144.155   <pending>     8081:30000/TCP   18s
service/mongodb-service         ClusterIP      10.104.97.237   <none>        27017/TCP        17m

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongo-express        1/1     1            1           109s
deployment.apps/mongodb-deployment   1/1     1            1           17m

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/mongo-express-78fcf796b8       1         1         1       109s
replicaset.apps/mongodb-deployment-8f6675bc5   1         1         1       17m

C:\Users\echypal>

extrenal ip is not given by minikube

5) C:\Users\echypal>minikube service list
|-------------|-----------------------|--------------|---------------------------|
|  NAMESPACE  |         NAME          | TARGET PORT  |            URL            |
|-------------|-----------------------|--------------|---------------------------|
| default     | kubernetes            | No node port |
| default     | mongo-express-service |         8081 | http://192.168.49.2:30000 |
| default     | mongodb-service       | No node port |
| kube-system | kube-dns              | No node port |
|-------------|-----------------------|--------------|---------------------------|

C:\Users\echypal>


C:\Users\echypal>docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED             STATUS              PORTS                                                                                                                                  NAMES
a646a3f7f5f0        gcr.io/k8s-minikube/kicbase:v0.0.23   "/usr/local/bin/entr…"   3 hours ago         Up 3 hours          127.0.0.1:32772->22/tcp, 127.0.0.1:32771->2376/tcp, 127.0.0.1:32770->5000/tcp, 127.0.0.1:32769->8443/tcp, 127.0.0.1:32768->32443/tcp   minikube

C:\Users\echypal>docker exec -it a646a3f7f5f0 sh
.# curl http://192.168.49.2:30000
<!DOCTYPE html>
<html lang="en">
<head>
  <base href="/">
  .............
 So the service is indeed running, it's just not accessible from outside the docker container.
 
6.1) 
C:\Users\echypal>kubectl proxy
Starting to serve on 127.0.0.1:8001 
 
 
6.2) C:\Users\echypal>minikube dashboard
* Enabling dashboard ...
  - Using image kubernetesui/dashboard:v2.1.0
  - Using image kubernetesui/metrics-scraper:v1.0.4
* Verifying dashboard health ...
* Launching proxy ...
* Verifying proxy health ...
* Opening http://127.0.0.1:54771/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser... 


after running minicube dashboard successfully, we can see kubernetes-dashboard namespace


#kube public namespace
C:\Users\echypal>kubectl cluster-info
Kubernetes master is running at https://127.0.0.1:32769
KubeDNS is running at https://127.0.0.1:32769/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

C:\Users\echypal>

#creating own namespace
C:\Users\echypal>kubectl create namespace my-namespace
namespace/my-namespace created

C:\Users\echypal>kubectl get namespace
NAME                   STATUS   AGE
default                Active   4d3h
kube-node-lease        Active   4d3h
kube-public            Active   4d3h
kube-system            Active   4d3h
kubernetes-dashboard   Active   4d
my-namespace           Active   8s

C:\Users\echypal>kubectl get namespaces
NAME                   STATUS   AGE
default                Active   4d3h
kube-node-lease        Active   4d3h
kube-public            Active   4d3h
kube-system            Active   4d3h
kubernetes-dashboard   Active   4d
my-namespace           Active   11s

C:\Users\echypal>

#components of dashboard namaespace
C:\Users\echypal>kubectl get all -n kubernetes-dashboard
NAME                                            READY   STATUS    RESTARTS   AGE
pod/dashboard-metrics-scraper-f6647bd8c-p864p   1/1     Running   3          4d
pod/kubernetes-dashboard-968bcb79-xvcqz         1/1     Running   4          4d

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/dashboard-metrics-scraper   ClusterIP   10.99.9.149      <none>        8000/TCP   4d
service/kubernetes-dashboard        ClusterIP   10.106.197.174   <none>        80/TCP     4d

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dashboard-metrics-scraper   1/1     1            1           4d
deployment.apps/kubernetes-dashboard        1/1     1            1           4d

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/dashboard-metrics-scraper-f6647bd8c   1         1         1       4d
replicaset.apps/kubernetes-dashboard-968bcb79         1         1         1       4d

C:\Users\echypal>

#install kubenetes ingress controller: kubernetes nginx Ingress controller
C:\Users\echypal>minikube addons enable ingress
* After the addon is enabled, please run "minikube tunnel" and your ingress resources would be available at "127.0.0.1"
  - Using image docker.io/jettech/kube-webhook-certgen:v1.5.1
  - Using image k8s.gcr.io/ingress-nginx/controller:v0.44.0
  - Using image docker.io/jettech/kube-webhook-certgen:v1.5.1
* Verifying ingress addon...
* The 'ingress' addon is enabled

C:\Users\echypal>kubectl get namespace
NAME                   STATUS   AGE
default                Active   4d4h
ingress-nginx          Active   7m7s
kube-node-lease        Active   4d4h
kube-public            Active   4d4h
kube-system            Active   4d4h
kubernetes-dashboard   Active   4d1h
my-namespace           Active   55m

note: new namespace created ingress-nginx 

C:\Users\echypal>kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-9kwsb        0/1     Completed   0          8m15s
ingress-nginx-admission-patch-vcfpt         0/1     Completed   1          8m15s
ingress-nginx-controller-5d88495688-slv6r   1/1     Running     0          8m15s

C:\Users\echypal>


C:\Users\echypal>kubectl apply -f "C:\repository\git\youtube-tutorial-series\kubernetes-ingress\dashboard-ingress.yaml"
ingress.networking.k8s.io/dashboard-ingress created

C:\Users\echypal>kubectl get ingress -n kubernetes-dashboard
NAME                CLASS    HOSTS           ADDRESS        PORTS   AGE
dashboard-ingress   <none>   dashboard.com   192.168.49.2   80      98s
