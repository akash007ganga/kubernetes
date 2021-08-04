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

#incase pod creation is required
C:\Users\echypal>kubectl run pod nginx --image=nginx
pod/pod created

C:\Users\echypal>kubectl run redis --image=redis123 --dry-run=client -o yaml > pod.yaml
C:\Users\echypal>notepad pod.yaml


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

If you are not given a pod definition file, you may extract the definition to a file using the below command:
C:\Users\echypal>kubectl get pod <pod-name> -o yaml > pod-definition.yaml

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
- For pod: kubectl logs <pod-name>.
          kubectl logs -f <pod-name>   (-f helps to see live log trail)
- For multipod container: 
    $kubectl logs webapp-2 -c    shows number of containers)
	$ db   simple-webapp
	    
    $kubectl logs -f <pod-name> -c <container-name>
	$kubectl logs webapp-2 -c simple-webapp
	or
	$kubectl logs webapp-2  simple-webapp
- For K8s resources: kubectl describe <resource like pod|deployment|service>  <name of the resource>


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
#Steps
1) Create
    kubectl create -f my-deployment-1.yaml
  
2) Get
    kubectl get deployments
  
3) Update
  a) kubectl apply -f my-deployment-2.yaml
	
  b) kubectl set image deployment/my-deployment-1 nginx=nginx:1.9.1
	
	- Update with change clause recording
	   master $ kubectl set image deployment/nginx <container-name>=nginx:1.17 --record
	            kubectl set image deployment/nginx nginx=nginx:1.17 --record
                deployment.extensions/nginx image updated
                master $master $
                 
       master $ kubectl rollout history deployment nginx
                deployment.extensions/nginx
 
                REVISION CHANGE-CAUSE
                1     <none>
                2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
       master $
	   
  c) edit an image
         master $ kubectl edit deployments. nginx --record
                  deployment.extensions/nginx edited
 
         master $ kubectl rollout history deployment nginx
                  REVISION CHANGE-CAUSE
                  1     <none>
                  2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
                  3     kubectl edit deployments. nginx --record=true
4) Status
    kubectl rollout status deployment/my-deployment-2
  
5) History

    kubectl rollout history deployment/my-deployment-2
	
   - Check status of each revision
      master $ kubectl rollout history deployment nginx --revision=1
   
	
6) Rollback
    kubectl rollout undo deployment/my-deployment-2




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

1) C:\Users\echypal>kubectl rollout status deployment kubernetes-bootcamp
    deployment "kubernetes-bootcamp" successfully rolled out

2) Rollout History
    C:\Users\echypal>kubectl rollout history deployment/myapp-depl

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
or
[root@sowlx082 ~]# echo -n 'username' | base64 –encode
dXNlcm5hbWU=
[root@sowlx082 ~]# echo -n 'passsword' | base64
cGFzc3N3b3Jk
[root@sowlx082 ~]#

decode 
[root@sowlx082 ~]# echo  'dXNlcm5hbWU=' | base64 --decode
username[root@sowlx082 ~]#

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
or
C:\Users\echypal>kubectl get ns

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


####Create a config-map with empty data. create a volume with the data of the config map. expose the data of the volume to some file. Mount the volume to some 
####location. Use the location to read data and use it to create pod

1) config-map with empty data
>>example-redis-config.yaml"
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-redis-config
data:
  redis-config: ""

note: redis-config is the key of the data which is currently empty

C:\Users\echypal>kubectl apply -f "C:\repository\git\kubernetes\redis-cache\example-redis-config.yaml"
configmap/example-redis-config created

2) Here a volume with name config is created from data of key redis-config(key present in example-redis-config.yaml file) and exposed data to file redis.conf

......
volumes:
    - name: data
      emptyDir: {}
    - name: config
      configMap:
        name: example-redis-config
        items:
        - key: redis-config
          path: redis.conf
.....	

volumeMounts:
    - mountPath: /redis-master-data
      name: data
    - mountPath: /redis-master
      name: config

.......
Note: Then volume config is mounted to redis-master
..................
containers:
  - name: redis
    image: redis:5.0.4
    command:
      - redis-server
      - "/redis-master/redis.conf"
	  
..................
note: 	data is used to create pod

C:\Users\echypal>kubectl apply -f "C:\repository\git\kubernetes\redis-cache\redis-pod.yaml"
pod/redis created

3) kubectl get pod/redis configmap/example-redis-config 
NAME        READY   STATUS    RESTARTS   AGE
pod/redis   1/1     Running   0          8s

NAME                             DATA   AGE
configmap/example-redis-config   1      14s

4) kubectl describe configmap/example-redis-config

Name:         example-redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
redis-config:

Note: data is blank under key redis-config

5) check maxmemory and maxmemory-policy of redis

C:\Users\echypal>kubectl exec -ti redis sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
# redis-cli
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "0"
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "noeviction"
127.0.0.1:6379> exit
# exit

6) Now add some data values to the key, redis-config of example-redis-config ConfigMap:

apiVersion: v1
kind: ConfigMap
metadata:
  name: example-redis-config
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru

7) Apply the change
C:\Users\echypal>kubectl apply -f "C:\repository\git\kubernetes\redis-cache\example-redis-config.yaml"
configmap/example-redis-config configured

8) check data
C:\Users\echypal>kubectl describe configmap/example-redis-config
Name:         example-redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
redis-config:
----
maxmemory 2mb
maxmemory-policy allkeys-lru

Events:  <none>

9) Check redis memory again
C:\Users\echypal>kubectl exec -it redis -- sh
# redis-cli
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "0"
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "noeviction"
127.0.0.1:6379>

Note: data not updated in pod.

10) Delete and recreate pod

C:\Users\echypal>kubectl delete pod redis
pod "redis" deleted

C:\Users\echypal>kubectl apply -f "C:\repository\git\kubernetes\redis-cache\redis-pod.yaml"
pod/redis created

11) Again check values in redis

C:\Users\echypal>kubectl exec -ti redis -- sh
# redis-cli
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "2097152"
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "allkeys-lru"
127.0.0.1:6379>

12) Inspect the volume

C:\Users\echypal>kubectl exec -it redis -- sh
# ls
bin   data  etc   lib    media  opt   redis-master       root  sbin  sys  usr
boot  dev   home  lib64  mnt    proc  redis-master-data  run   srv   tmp  var

# cd redis-master-data
# ls
#

# cd redis-master
# ls
redis.conf
# cat redis.conf
maxmemory 2mb
maxmemory-policy allkeys-lru
#

####expose an external ip addresss using load balancer
1) deleted every other pods for space problem

2) configured deployment with 1 replica(to prevent downloading the image 5 times)
.....
spec:
  replicas: 1
  selector:
.....
C:\Users\echypal>kubectl apply -f C:\repository\git\kubernetes\stateless-application\load-balancer-example.yaml
deployment.apps/hello-world created

C:\Users\echypal>kubectl get pod
NAME                           READY   STATUS    RESTARTS   AGE
hello-world-6df5659cb7-nk7nc   1/1     Running   0          3m3s

3) Change replica to 5
.....  
spec:
  replicas: 5
  selector:
.....  

C:\Users\echypal>kubectl apply -f C:\repository\git\kubernetes\stateless-application\load-balancer-example.yaml
deployment.apps/hello-world configured

C:\Users\echypal>kubectl get pod
NAME                           READY   STATUS              RESTARTS   AGE
hello-world-6df5659cb7-7ckvx   0/1     ContainerCreating   0          3s
hello-world-6df5659cb7-gf4bw   0/1     ContainerCreating   0          3s
hello-world-6df5659cb7-km7s6   1/1     Running             0          3s
hello-world-6df5659cb7-l9zwj   0/1     ContainerCreating   0          3s
hello-world-6df5659cb7-nk7nc   1/1     Running             0          3m23s


C:\Users\echypal>kubectl get deployments hello-world
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   5/5     5            5           4m9s

C:\Users\echypal>kubectl describe deployment hello-world
Name:                   hello-world
Namespace:              default
CreationTimestamp:      Sun, 18 Jul 2021 13:21:56 +0530
Labels:                 app.kubernetes.io/name=load-balancer-example
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app.kubernetes.io/name=load-balancer-example
Replicas:               5 desired | 5 updated | 5 total | 5 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app.kubernetes.io/name=load-balancer-example
  Containers:
   hello-world:
    Image:        gcr.io/google-samples/node-hello:1.0
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   hello-world-6df5659cb7 (5/5 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  4m29s  deployment-controller  Scaled up replica set hello-world-6df5659cb7 to 1
  Normal  ScalingReplicaSet  69s    deployment-controller  Scaled up replica set hello-world-6df5659cb7 to 5

C:\Users\echypal>kubectl get replicasets
NAME                     DESIRED   CURRENT   READY   AGE
hello-world-6df5659cb7   5         5         5       6m47s

4) C:\Users\echypal>kubectl expose deployment hello-world --type=LoadBalancer --name=my-service
service/my-service exposed

C:\Users\echypal>kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          13d
my-service   LoadBalancer   10.108.244.78   <pending>     8080:32094/TCP   2m2s

C:\Users\echypal>

C:\Users\echypal>kubectl describe service my-service
Name:                     my-service
Namespace:                default
Labels:                   app.kubernetes.io/name=load-balancer-example
Annotations:              <none>
Selector:                 app.kubernetes.io/name=load-balancer-example
Type:                     LoadBalancer
IP:                       10.108.244.78
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32094/TCP
Endpoints:                172.17.0.10:8080,172.17.0.2:8080,172.17.0.4:8080 + 2 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

C:\Users\echypal>

C:\Users\echypal>kubectl get pod -o wide
NAME                           READY   STATUS    RESTARTS   AGE   IP            NODE       NOMINATED NODE   READINESS GATES
hello-world-6df5659cb7-7ckvx   1/1     Running   0          71m   172.17.0.6    minikube   <none>           <none>
hello-world-6df5659cb7-gf4bw   1/1     Running   0          71m   172.17.0.10   minikube   <none>           <none>
hello-world-6df5659cb7-km7s6   1/1     Running   0          71m   172.17.0.4    minikube   <none>           <none>
hello-world-6df5659cb7-l9zwj   1/1     Running   0          71m   172.17.0.7    minikube   <none>           <none>
hello-world-6df5659cb7-nk7nc   1/1     Running   0          74m   172.17.0.2    minikube   <none>           <none>

C:\Users\echypal>

C:\Users\echypal>minikube service my-service
|-----------|------------|-------------|-----------------------------|
| NAMESPACE |    NAME    | TARGET PORT |             URL             |
|-----------|------------|-------------|-----------------------------|
| default   | my-service |        8080 | http://192.168.43.210:32094 |
|-----------|------------|-------------|-----------------------------|
* Opening service default/my-service in default browser...

C:\Users\echypal>


The response to a successful request is a hello message:

Hello Kubernetes!

#scale up stateful set

1) C:\Users\echypal>kubectl get pod -w -l app=nginx
NAME    READY   STATUS    RESTARTS   AGE
web-0   1/1     Running   0          2m49s
web-1   1/1     Running   0          2m47s

2) 
C:\Users\echypal>kubectl scale sts web --replicas=5
statefulset.apps/web scaled

3) C:\Users\echypal>kubectl get pod -w -l app=nginx
NAME    READY   STATUS    RESTARTS   AGE
web-0   1/1     Running   0          2m49s
web-1   1/1     Running   0          2m47s
web-2   0/1     Pending   0          0s
web-2   0/1     Pending   0          0s
web-2   0/1     Pending   0          2s
web-2   0/1     ContainerCreating   0          2s
web-2   1/1     Running             0          4s
web-3   0/1     Pending             0          0s
web-3   0/1     Pending             0          0s
web-3   0/1     Pending             0          2s
web-3   0/1     ContainerCreating   0          2s
web-3   1/1     Running             0          4s
web-4   0/1     Pending             0          0s
web-4   0/1     Pending             0          0s
web-4   0/1     Pending             0          2s
web-4   0/1     ContainerCreating   0          2s
web-4   1/1     Running             0          4s

#scale down stateful set
1) C:\Users\echypal>kubectl get pod -w -l app=nginx
NAME    READY   STATUS    RESTARTS   AGE
web-0   1/1     Running   0          6m14s
web-1   1/1     Running   0          6m12s
web-2   1/1     Running   0          2m19s
web-3   1/1     Running   0          2m15s
web-4   1/1     Running   0          2m11s

2) C:\Users\echypal>kubectl scale sts web --replicas=2
statefulset.apps/web scaled

C:\Users\echypal>

3) C:\Users\echypal>kubectl get pod -w -l app=nginx
NAME    READY   STATUS    RESTARTS   AGE
web-0   1/1     Running   0          6m14s
web-1   1/1     Running   0          6m12s
web-2   1/1     Running   0          2m19s
web-3   1/1     Running   0          2m15s
web-4   1/1     Running   0          2m11s
web-4   1/1     Terminating   0          2m19s
web-4   0/1     Terminating   0          2m20s
web-4   0/1     Terminating   0          2m21s
web-4   0/1     Terminating   0          2m21s
web-3   1/1     Terminating   0          2m25s
web-3   0/1     Terminating   0          2m26s
web-3   0/1     Terminating   0          2m27s
web-3   0/1     Terminating   0          2m27s
web-2   1/1     Terminating   0          2m31s
web-2   0/1     Terminating   0          2m32s
web-2   0/1     Terminating   0          2m33s
web-2   0/1     Terminating   0          2m33s

4) persistent volumes of the stateful sets are not deleted
C:\Users\echypal>kubectl get pvc -l app=nginx
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
www-web-0   Bound    pvc-b3e6f2a2-5e10-4ca8-be96-2c45f54daa92   1Gi        RWO            standard       37m
www-web-1   Bound    pvc-d1a241b6-c126-42b6-be29-5ae9a7a37cbd   1Gi        RWO            standard       37m
www-web-2   Bound    pvc-e956c470-8c0f-42aa-9095-db7e5897ee0d   1Gi        RWO            standard       5m30s
www-web-3   Bound    pvc-80c5f242-519e-49dd-82dc-b1da4f4c6cc5   1Gi        RWO            standard       5m26s
www-web-4   Bound    pvc-aad929aa-b584-4921-a1b1-effd9f610d0c   1Gi        RWO            standard       5m22s

C:\Users\echypal>






#find pod in all namespacess
root@controlplane:~# kubectl get pods --all-namespaces

#create pod (command line)
Notes:
# when we create a resource like pod/deployment, we have to use small letters ass first character. Capital Deployment/Pod won't work
 - C:\Users\echypal>kubectl create deployment nginx-depl --image=nginx
   deployment.apps/nginx-depl created
   
 - C:\Users\echypal>kubectl run nginx --image=nginx
   pod/nginx created
   
 when we write yaml file, kind should start with Capital. Small won't work

 .........
 kind: Pod
 
 #####
 ......
 kind: Deployment
 
# What DNS name should the Blue application use to access the database 'db-service' in its own (marketting) namespace
db-service

# What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace 
db-service.dev.svc.cluster.local  

# count all the namespaces

root@controlplane:~# kubectl get ns --no-headers | wc -l
10
root@controlplane:~# 

# Generate POD Manifest YAML file (-o yaml) for redis Pod. Don't create it(--dry-run)

root@controlplane:~# kubectl run redis --image=redis --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
root@controlplane:~# 

root@controlplane:~# kubectl run redis --image=redis --dry-run=client -o yaml > pod.yaml
root@controlplane:~# 

--dry-run: By default as soon as the command is run, the resource will be created. If you simply want to test your command, use the --dry-run=client option. This will not create the resource, instead, tell you whether the resource can be created and if your command is right.

-o yaml: This will output the resource definition in YAML format on the screen.

if you omit the --dry-run=client but put -o yaml, then the resource will be created in the server  and output will also be directed in yaml file. 

# Generate Deployment YAML file (-o yaml) for nginx. Don't create it(--dry-run)

kubectl create deployment nginx --image=nginx  --dry-run=client -o yaml
kubectl create deployment nginx --image=nginx  --dry-run=client -o yaml > nginx-deployment.yaml

# Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
kubectl expose pod redis --port=6379 --target-port=6379 --name redis-service --dry-run=client -o yaml
(This will automatically use the pod's labels as selectors)


or
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
(This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

note: = and space can be used interchangeably
like --target-port=6379 and --target-port 6379 are same for one argument
     -l app=nginx and --labels app=nginx are same but here space iss mandatory as multiple arguments

#Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:

`kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml`
(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`
(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

#Deploy a redis pod using the redis:alpine image with the labels set to tier=db.
root@controlplane:~# kubectl run redis --image=redis:alpine --dry-run=client -l tyer=db -o yaml > redis-pod.yaml
root@controlplane:~# kubectl run redis --image=redis:alpine -l tier=db --dry-run=client -o yaml > redis-pod.yaml

#Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas. Try to use imperative commands only. Do not create definition files.
root@controlplane:~# kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3 --dry-run=client  -o yaml > webapp-depl.yaml
root@controlplane:~# 

#Create a new pod called custom-nginx using the nginx image and expose it on container port 8080.
root@controlplane:~# kubectl run custom-nginx --image=nginx --port=8080 --dry-run=client -o yaml > custom-nginx.yaml
root@controlplane:~#

#Create a new namespace called dev-ns. Use imperative commands.
root@controlplane:~# kubectl create ns dev-ns --dry-run=client -o yaml > dev-ns.yaml
root@controlplane:~#

#Create a new deployment called redis-deploy in the dev-ns namespace with the redis image. It should have 2 replicas. Use imperative commands.
root@controlplane:~# kubectl create deployment redis-deploy -n dev-ns --image=redis --replicas=2 --dry-run=client  -o yaml > redis-deploy.yaml

#Create a pod called httpd using the image httpd:alpine in the default namespace. Next, create a service of type ClusterIP by the same name (httpd). The target port for the service should be 80. Try to do this with as few steps as possible.

root@controlplane:~# kubectl run httpd --image=httpd:alpine --namespace=default --dry-run=client -o yaml > httpd-pod.yaml
root@controlplane:~#

root@controlplane:~# kubectl apply -f httpd-pod.yaml 
pod/httpd created

root@controlplane:~# kubectl expose pod httpd --name=httpd --port=80 --dry-run=client -o yaml> httpd-service.yaml
root@controlplane:~#

root@controlplane:~# kubectl apply -f httpd-service.yaml 
service/httpd created
root@controlplane:~#

or 

root@controlplane:~# kubectl run httpd --image=httpd:alpine --namespace=default --expose --port=80  --dry-run=client -o yaml
root@controlplane:~# kubectl run httpd --image=httpd:alpine --namespace=default --expose --port=80  --dry-run=client -o yaml

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  name: httpd
  namespace: default
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: httpd
status:
  loadBalancer: {}
---
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: httpd
  name: httpd
  namespace: default
spec:
  containers:
  - image: httpd:alpine
    name: httpd
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}  

#ENTRYPOINT & CMD
- If yaml file has command in spec then this will override ENTRYPOINT command of docker file. If there is no command in yaml, then docker files ENTRYPOINT command  will run
  example
  Suppose docker file has following
  .......
    ENTRYPOINT ["python", "app.py"]    
  ........	
  Pod definition file has following
  .........
     spec:
     containers:
     - name: simple-webapp
       image: kodekloud/webapp-color
       command: ["--color","green"]
  .........	   
  In this case when Pod(container) is created, it will run --color green command. 

-If yaml file has args section, then it will be placed after the command(either from Dockerfile or yaml as per above logic)

   Dockerfile                               pod-definition.yaml             final command
-  ENTRYPOINT ["python", "app.py"]          nothing                         .... python app.py
-  ENTRYPOINT ["python", "app.py"]          nothing                         .... python app.py --color red
   CMD ["--color", "red"]                                                  
-  ENTRYPOINT ["python", "app.py"]          command: ["--color","green"]    .... --color green   (if ENTRYPOINT is overriden by command, then CMD is not used even                                                                                                  if there iss no args)
   CMD ["--color", "red"]                                                   
-  ENTRYPOINT ["python", "app.py"]          command: ["python","app2.py"]    .... python app2.py --color pink
   CMD ["--color", "red"]                   args: ["--color","pink"]                      


#Create a new ConfigMap for the webapp-color POD. Use the spec given below.
#ConfigName Name: webapp-config-map
#Data: APP_COLOR=darkblue  

root@controlplane:~# kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue --dry-run=client -o yaml > webapp-config-map.yaml
root@controlplane:~#

#check any command
root@controlplane:~#kubectl explain pods --recursive | grep envFrom -A3
here 3 is number of lines below envFrom.
root@controlplane:~#kubectl explain pods --recursive | less 
(and search for envFrom)

#create secret with following property
#Secret Name: db-secret
#Secret 1: DB_Host=sql01
#Secret 2: DB_User=root
#Secret 3: DB_Password=password123
root@controlplane:~# kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123 --dry-run=client -o yaml > db-secret.yaml

note: check the generic command after secret(which is not required for config map). Imperative method like above create the secret in base 64 encoding form

#Edit the pod 'ubuntu-sleeper' to run the sleep process with user ID 1010. add capabilities to SYS_TIME

apiVersion: v1
kind: Pod
metadata:
  name: ...
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    securityContext:
     runAsUser: 1002
     capabilities:
       add:
        - SYS_TIME
    imagePullPolicy: Always
    
for root
runAsUser: 0

#service account
- first describe the service account
  controlplane $ kubectl describe serviceaccount dashboard-sa
    Name:                dashboard-sa
    Namespace:           default
    Labels:              <none>
    Annotations:         <none>
    Image pull secrets:  <none>
    Mountable secrets:   dashboard-sa-token-dmn6x
    Tokens:              dashboard-sa-token-dmn6x
    Events:              <none>

- token is a secret. check it
  controlplane $ kubectl describe secret default-token-qscb8
    Name:         default-token-qscb8
    Namespace:    default
    Labels:       <none>
    Annotations:  kubernetes.io/service-account.name: default
                  kubernetes.io/service-account.uid: 81c44a0a-d93f-4b33-bfab-45045b9a0150
    
    Type:  kubernetes.io/service-account-token
    
    Data
    ====
    ca.crt:     1066 bytes
    namespace:  7 bytes
    token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InNJbEZ3aU9VdVhFSFVSODV1SFd6YXV3TmZjbm5uMENrX0lmZTg4bEpLTDQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcXNjYjgiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjgxYzQ0YTBhLWQ5M2YtNGIzMy1iZmFiLTQ1MDQ1YjlhMDE1MCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.KDSFzXwtVl5MziDIVKZw5Ye2gHNz-1emzgkli5CIal2IFA0EtUzZqxpd5DBGsw97UVQLK8dvqRxu-ZxWxkZZe5aQsVjMvU97-JMH35jrCGqr7l9EW2WY4LeN95loQtukahdkGGvKM71QbmIR_9-PY0Di3lnoy-GAn1h82QCdBtkX0F91h7M0oiovFrgPXVOik3xKTQHZ5_NZx6og7hJ0tdGo1tqBPwMo5dS0jht-VUf2UoIs51Jd-_thb5VpkNl24nSUSvihtYVAEkialnIefSAnv4Og-iuUeMA1QUigYZtTgFMxohj_IxgyoY9VlTG0N2o2MBStoKePqj3kbb6qYA
    controlplane $
	
    This token(eyJ...YA) is used for the communication. If we check the secret we get base64 encoding values of this token as follow(ZXlKa....DBnakE=) we can't use that token
   
   controlplane $ kubectl get secret dashboard-sa-token-dmn6x -o yaml
    apiVersion: v1
    data:
    ca.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM1ekNDQWMrZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJeE1EY3pNVEUxTkRjME1Wb1hEVE14TURjeU9URTFORGMwTVZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTU11CmdjczhZSnJSQmFOMXd1U3ZmU2ZFYXRzZmExbXQzKzBQM3J4TldveWFsNGZsWjJyT3ZoTU53QUNMdnNuOFpDakYKM0FGQXpWam5uYnp0ZDZQcUpjQ1hQZFZlVXIyQjBTU3JBcU5xdzBTSUhRTWtNbHBIMnZkWGVyRlRCTStUaE92QQpWWWlQWUl5TllZNkNYVllyZ05wdWFsNFlGZ1JPTUowOFlaamhWaGthVWR0MVA5OHA2NXZXYWh4WTlGVGdMYzYxClFaWE5ZNlNJY3RmWnJ3TFpUYkgwb3BMT2NmWkJjajdadVJrcXB2L0d6L3RZTDF5ZEh4cGEyeXI4OURDbzhKRFYKMVY1SjFSUWsyNUV5Q05ucWJKb1g3NDBpaDVNdmJMenAzTS82UDFvMHR6NHFoLzhkV3ROV2FzWFpzdjRVcmdqawozMm80UlY2UnhVM0cvNXNnZGZjQ0F3RUFBYU5DTUVBd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0hRWURWUjBPQkJZRUZEWDV2T2E2NXNGUEtHbnlRWi9WTmRHZVNITWFNQTBHQ1NxR1NJYjMKRFFFQkN3VUFBNElCQVFDa1MzL29kMEpDbHpDWDV3SUthcVNsL0QrR25jWm9wL29GcVl4U01ad2JPTllzSDZjYQpmK0hBNXFJKy9DVjM0M2ZDSmg0dXRuN2pLUC9vaFR1NklOcVVHNjRIMkRsb05EREQyalhvVDU3YXhxZ1Q4MTdNCkpSUjVGbi9tWU1SVGVCbnF1MzYzRFpRcFlCU1BjSVVQUDdtOW5aRWNDc2Z3bmZvS0lOSittNElBeFNGL3NXZncKK1c1MUhvRlVRUi9Bd0t2ZWpwOWJuck1ndlNZd01nMlhPV01HOHlhblk3QUx0RGpWNjAxdVJTVzhxTzlhM3VRSwprODd3SGM3MzZYQnB3SU5MeWMwNXkwNUZHNTgxbk1LU1R0Uk9VRCtiZzdIUkVGcUVnTXdUa2x1M3BNQVpJZU10CklnWFd0SlpRMVVTckFKRXhQRkZBeG13UkZlWGwrMnNoRVpUKwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    namespace: ZGVmYXVsdA==
    token: ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkluTkpiRVozYVU5VmRWaEZTRlZTT0RWMVNGZDZZWFYzVG1aamJtNXVNRU5yWDBsbVpUZzRiRXBMVERRaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJoYzJoaWIyRnlaQzF6WVMxMGIydGxiaTFrYlc0MmVDSXNJbXQxWW1WeWJtVjBaWE11YVc4dmMyVnlkbWxqWldGalkyOTFiblF2YzJWeWRtbGpaUzFoWTJOdmRXNTBMbTVoYldVaU9pSmtZWE5vWW05aGNtUXRjMkVpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1MWFXUWlPaUkyTXpNMFpUYzFOaTAxWlRWakxUUTRZbU10T1RGa01pMWtZMlV5T1RNeU1UazVOamtpTENKemRXSWlPaUp6ZVhOMFpXMDZjMlZ5ZG1salpXRmpZMjkxYm5RNlpHVm1ZWFZzZERwa1lYTm9ZbTloY21RdGMyRWlmUS5nYzRqeTNsNVFFa1hRVm5BVHhlRVNMdlJVc3ZKOGZ5SUJxYzBZaDh0UTBJOFBETVpGUzVudXJ5XzM3eUNRWFhjeHVuUDRqMEZxREtfaHRQQ0lNSV9DZlE3OG9qb2FIUUxGWk9QUi1CZ0pHWjE2V1NFRFIyNVB3ckk2a3FVdXRYQkJZTDQ2SWRDdmdsQXRRV21DZnU4ZnA4alBDbDN3ank0R1ZiTUdKN3p4NS00N19yQ01RVDk1SjBGWXNZUS1Fc2REYklRbVNyRFRNTlpIdDZncTAwcWNzSy1majJiTUpaWXI5RWtyREhIMEV0TkhyNTRQN0ZpWVZ3NDV1RUVjcjFPNFpRbktfb3U2QldlWUhPUFcyNS04WFRZd0VJUDUycU9TUHQxYUZfZlRlYU12WGFoSk82V1V1UjliWEVLcjh5c3diZFIwUDU0RGRRNzhOZTVkaDBnakE=
    .........
	
	if you decode the above token, you will get the earlier value
	
	[root@sowlx082 ~]# echo -n 'ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNkluTkpiRVozYVU5VmRWaEZTRlZTT0RWMVNGZDZZWFYzVG1aamJtNXVNRU5UZzRiRXBMVERRaWZRLmV5SnBjM01pT2lKcmRXSmxjbTVsZEdWekwzTmxjblpwWTJWaFkyTnZkVzUwSWl3aWEzVmlaWEp1WlhSbGN5NXBieTl6WlhKMmFXTmxZV05qYjNWdWRDOXVZVzFsYzNCaFkyVWlPaUprWldaaGRXeDBJaXdpYTNWaVpYSnVaWFJsY3k1cGJ5OXpaWEoyYVdObFlXTmpiM1Z1ZEM5elpXTnlaWFF1Ym1GdFpTSTZJbVJoYzJoaWIyRnlaQzF6WVMxMGIydGxiaTFrYlc0MmVDSXNJbXQxWW1WeWJtVjBaWE11YVc4dmMyVnlkbWxqWldGalkyOTFiblF2YzJWeWRtbGpaUzFoWTJOdmRXNTBMbTVoYldVaU9pSmtZWE5vWW05aGNtUXRjMkVpTENKcmRXSmxjbTVsZEdWekxtbHZMM05sY25acFkyVmhZMk52ZFc1MEwzTmxjblpwWTJVdFlXTmpiM1Z1ZEM1MWFXUWlPaUkyTXpNMFpUYzFOaTAxWlRWakxUUTRZbU10T1RGa01pMWtZMlV5T1RNeU1UazVOamtpTENKemRXSWlPaUp6ZVhOMFpXMDZjMlZ5ZG1salpXRmpZMjkxYm5RNlpHVm1ZWFZzZERwa1lYTm9ZbTloY21RdGMyRWlmUS5nYzRqeTNsNVFFa1hRVm5BVHhlRVNMdlJVc3ZKOGZ5SUJxYzBZaDh0UTBJOFBETVpGUzVudXJ5XzM3eUNRWFhjeHVuUDRqMEZxREtfaHRQQ0lNSV9DZlE3OG9qb2FIUUxGWk9QUi1CZ0pHWjE2V1NFRFIyNVB3ckk2a3FVdXRYQkJZTDQ2SWRDdmdsQXRRV21DZnU4ZnA4alBDbDN3ank0R1ZiTUdKN3p4NS00N19yQ01RVDk1SjBGWXNZUS1Fc2REYklRbVNyRFRNTlpIdDZncTAwcWNzSy1majJiTUpaWXI5RWtyREhIMEV0TkhyNTRQN0ZpWVZ3NDV1RUVjcjFPNFpRbktfb3U2QldlWUhPUFcyNS04WFRZd0VJUDUycU9TUHQxYUZfZlRlYU12WGFoSk82V1V1UjliWEVLcjh5c3diZFIwUDU0RGRRNzhOZTVkaDBnakE=' | base64 --decode
	
    eyJhbGciOiJSUzI1NiIsImtpZCI6InNJbEZ3aU9VdVhFSFVSODV1SFd6YXV3TmZjbm5uMENrX0lmZTg4bEpLTDQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRhc2hib2FyZC1zYS10b2tlbi1kbW42eCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJkYXNoYm9hcmQtc2EiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI2MzM0ZTc1Ni01ZTVjLTQ4YmMtOTFkMi1kY2UyOTMyMTk5NjkiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpkYXNoYm9hcmQtc2EifQ.gc4jy3l5QEkXQVnATxeESLvRUsvJ8fyIBqc0Yh8tQ0I8PDMZFS5nury_37yCQXXcxunP4j0FqDK_htPCIMI_CfQ78ojoaHQLFZOPR-BgJGZ16WSEDR25PwrI6kqUutXBBYL46IdCvglAtQWmCfu8fp8jPCl3wjy4GVbMGJ7zx5-47_rCMQT95J0FYsYQ-EsdDbIQmSrDTMNZHt6gq00qcsK-fj2bMJZYr9EkrDHH0EtNHr54P7FiYVw45uEEcr1O4ZQnK_ou6BWeYHOPW25-8XTYwEIP52qOSPt1aF_fTeaMvXahJO6WUuR9bXEKr8yswbdR0P54DdQ78Ne5dh0gjA
	[root@sowlx082 ~]#
	
	
- service account should be specified in pod definition. 
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ..
  spec:
    template:
	 metadata: 
	  name: 
	 spec:
	   containers: 
	     - image:
	   serviceAccount: dashboard-sa
.......

#how to specify resource request and limit

Pod specification 
......
spec:
  containers:
  - args:
    - sleep
    - "1000"
    image: ubuntu
    imagePullPolicy: Always
    name: cpu-stress
    resources:
      limits:
        cpu: "2"
		memory: 20Mi
      requests:
        cpu: "1"
		memory: 5Mi

M = megabyte = 1000 KB
Mi = Mebibyte = 1024 Ki(kibibyte)

#Taints & Tollerations
1: Taint is for nodes and tolleration is for Pod

#Do any taints exist on node01 node?
root@controlplane:~# kubectl describe node node01 | grep taint 
root@controlplane:~#

#Create a taint on node01 with key of spray, value of mortein and effect of NoSchedule
root@controlplane:~# kubectl taint node node01 spray=mortein:NoSchedule    
or
root@controlplane:~# kubectl taint nodes node01 spray=mortein:NoSchedule    
node/node01 tainted
root@controlplane:~#

#check taint in the node

root@controlplane:~# kubectl describe node node01 | grep Taint 
Taints:             spray=mortein:NoSchedule
root@controlplane:~#

#Do you see any taints on controlplane node?
root@controlplane:~# kubectl describe node controlplane| grep Taint
Taints:             node-role.kubernetes.io/master:NoSchedule
root@controlplane:~# 

#create tolerations in the pod
apiVersion: v1
kind:Pod
metadata:
  name: bee
spec:
  containers:
   - name: bee-container
     image: nginx
  tolerations: 
   - key: "spray"
     operator: "Equal"
     value: "mortein"
	 effect: "NoSchedule"
	 
note: quations are not necessary
	 
#Check toleration in the Pod 
root@controlplane:~# kubectl describe pod bee
......
root@controlplane:~# kubectl describe pod bee
Name:         bee
.......
Containers:
  .......
Conditions:
  .......
Volumes:
  .....
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
                 spray=mortein:NoSchedule
Events:
  .....
.....

#Remove the taint on controlplane, which currently has the taint effect of NoSchedule.
1)Current status
   root@controlplane:~# kubectl describe node controlplane| grep Taint
   Taints:             node-role.kubernetes.io/master:NoSchedule
   root@controlplane:~#
   
2) Remove Taint
   root@controlplane:~# kubectl taint nodes controlplane node-role.kubernetes.io/master:NoSchedule- 
   node/controlplane untainted
   root@controlplane:~# 
   
3) root@controlplane:~# kubectl describe node controlplane| grep Taint
   Taints:             <none>
   root@controlplane:~#
   
#node selectors
1) label  the node as reqired  
    root# kubectl label nodes <node-name> <label-key>=<label-value> 
	root# kubectl label nodes node01 size=Large 

2) Use the label in Pod definition file to be scheduled in the required node
     apiVersion: v1
     kind: Pod
     metadata:
	   name: myapp-pod
	 spec:
	   containers: 
	   - name: data-processor
	     image: data-processor
       nodeSelector:
	     size: Large
		 
#limitations
can't request something like
- Large or medium
- not small  (i.e. place the pod on any node which is not small)

# Solution: Node Affinity
    apiVersion: v1
     kind: Pod
     metadata:
	   name: myapp-pod
	 spec:
	   containers: 
	   - name: data-processor
	     image: data-processor
       affinity: 
	     nodeAffinity: 
		  requiredDuringSchedulingIgnoreDuringExecution: 
		    nodeSelectorTerms: 
		    - matchExpresssions:
			  - key: size
			    operator: In
				values: 
				- Large
				- Medium
				
				
	.............................
	        - matchExpresssions:
			  - key: size
	            operator: NotIn
				values: 
				- Small
	..............................
    Place the Pod in some node which is leveled
	         - matchExpresssions:
			   - key: size
	             operator: Exists
	...............................
	
	Node Affinity types
	 - requiredDuringSchedulingIgnoreDuringExecution
	 - preferredDuringSchedulingIgnoreDuringExecution
	 
#Check Labels (and values) exist on node node01?
  root@controlplane:~# kubectl get nodes node01 --show-labels
  or  
  root@controlplane:~# kubectl describe node node01
     Name:               node01
     Roles:              <none>
     Labels:             beta.kubernetes.io/arch=amd64
                         beta.kubernetes.io/os=linux
                         kubernetes.io/arch=amd64
                         kubernetes.io/hostname=node01
                         kubernetes.io/os=linux
     Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"46:fb:b5:32:db:36"}
 
#Multi container pods
 #Sidecar
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: ..
  spec:
    template:
	 metadata: 
	  name: 
	 spec:
	   containers: 
	     - image:simple-webapp
		   name: simple-webapp
		 - image: log-agent
		   name: log-agent
	   
 #Pod lifecycle
1. Pending: Scheduler finds where to put the Pod
2. ContainerCreating: Pod scheduled in some node. Pulls image and starting container.
3. Running: All the containers in a Pod is created
4. 

#Pod Conditions
1. PodScheduled: 
2. Initialized: 
3. Containers Ready:
4. Ready: 

# Readiness Probe
1) for web server
     apiServer: v1
     kind: Pod
     metadata:
       name: simple-webapp
       labels:
         app: simple-webapp
     spec:
        containers:
        - image: simple-webapp
          name: simple-webapp
          ports:
          - containerPort: 8080
          readinesssProbe:
            httpGet: 
               path: /api/ready
               port: 8080

2) or for DB service(tcp)

      readinesssProbe:
             tcpSocket: 
                port: 3306
      
3) or for exec

      readinesssProbe:
             exec: 
      	     command: 
               - cat
               - /app/is_ready
			   
4) initial delay and probe period.      		 

readinesssProbe:
            httpGet: 
               path: /api/ready
               port: 8080
            initialDelaySeconds: 10
            periodSeconds: 5
            failureThreashold: 8  (default:3)			
#Liveness
Similar to readinessProbe
	 apiServer: v1
     kind: Pod
     metadata:
       name: simple-webapp
       labels:
         app: simple-webapp
     spec:
        containers:
        - image: simple-webapp
          name: simple-webapp
          ports:
          - containerPort: 8080
          livenessProbe:
            httpGet: 
               path: /api/ready
               port: 8080
			   
all 1-4 works for readiness

if properly configured, then ready column will correctly show the readiness (Ready=0 though status is running)
root@controlplane:~# kubectl get pod
NAME              READY   STATUS    RESTARTS   AGE
simple-webapp-1   1/1     Running   0          28m
simple-webapp-2   0/1     Running   0          90s

note: Even if livenessProbe is enabled, K8s takes some time to detects whether the Pod is really liuve or not and restarts based on it. In that time service will be down.

#Monitoring using metric server
A) Minikube
1. Enable metrics server 
    C:\Users\echypal>minikube addons enable metrics-server

2. Give some time to collect the Node & Pod level metrics for the K8s cluster


B)Othres
1. Clone required repositiories from git\kubernetes\nginx-deployment-result
     root@controlplane:~# git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git
     Cloning into 'kubernetes-metrics-server'...
     remote: Enumerating objects: 24, done.
     remote: Counting objects: 100% (12/12), done.
     remote: Compressing objects: 100% (12/12), done.
     remote: Total 24 (delta 4), reused 0 (delta 0), pack-reused 12
     Unpacking objects: 100% (24/24), done.
	 
2. Deploy the metrics-server by creating all the components downloaded.
     root@controlplane:~# ls
        kubernetes-metrics-server  sample.yaml
	 root@controlplane:~/kubernetes-metrics-server# kubectl create -f .
        clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
        clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
        rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
        apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
        serviceaccount/metrics-server created
        deployment.apps/metrics-server created
        service/metrics-server created
        clusterrole.rbac.authorization.k8s.io/system:metrics-server created
        clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
        root@controlplane:~/kubernetes-metrics-server#
		
3. Give some time to collect the Node & Pod level metrics for the K8s cluster
     [root@sowlx082 ~]# watch "kubectl top node"

C) Monitoring
   root@controlplane:~/kubernetes-metrics-server# kubectl top node
      NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
      controlplane   254m         0%     943Mi           0%        
      node01         283m         0%     687Mi           0%        
      root@controlplane:~/kubernetes-metrics-server#
	
   note: 254m is 254 mili cores	
	  
   root@controlplane:~/kubernetes-metrics-server# kubectl top pod 
      NAME       CPU(cores)   MEMORY(bytes)   
      elephant   44m          52Mi            
      lion       2m           7Mi             
      rabbit     152m         106Mi 

#select apps based on labels
apiVersion:apps/v1
kind:Deployment
metadata:
  name: simple-webapp
  labels:                             (labels for ReplicaSet)
    app: App1
	function: Front-end
  annotations:                        (for recording other details like phone number, build version etc.)
    buildVersion: 1.34
spec:
  replicas: 3
  selector:                           (connect ReplicaSet with Pod. sshould match the labels on the Pod)
    matchLabels:
      app: App1
  template:
     metadata:                      (labels for POD)
	   labels: 
	     app=App1
	     function: Front-end
	 spec:
	   containers: 
	   - name: simple-webapp
         image: simple-webapp	   
      

1) Use labels
   root@controlplane:~# kubectl get pods --show-labels

   root@controlplane:~# kubectl get pods -l env=dev --no-headers | wc -l
     7
   or
   root@controlplane:~# kubectl get pods --selector env=dev --no-headers | wc -l
     7  
2) Check labels in all objects
     root@controlplane:~# kubectl get all -l env=prod
      NAME              READY   STATUS    RESTARTS   AGE
      pod/app-1-zzxdf   1/1     Running   0          4m26s
      pod/app-2-l79qv   1/1     Running   0          4m27s
      pod/auth          1/1     Running   0          4m27s
      pod/db-2-xzcct    1/1     Running   0          4m27s
      
      NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
      service/app-1   ClusterIP   10.98.157.96   <none>        3306/TCP   4m26s
      
      NAME                    DESIRED   CURRENT   READY   AGE
      replicaset.apps/app-2   1         1         1       4m27s
      replicaset.apps/db-2    1         1         1       4m27s
      root@controlplane:~#	

3) use more than one labels
    root@controlplane:~# kubectl get pods -l bu=finance,env=prod,tier=frontend 
       NAME          READY   STATUS    RESTARTS   AGE
       app-1-zzxdf   1/1     Running   0          15m
       root@controlplane:~# 

#restartPolicy
apiVersion: v1
kind: Pod
metadata:
  name: math-pod
spec:                                   ---------------|
  containers:                                          |
  - name: math-add                                     |
    image: ubuntu                                      |
	command: ['expr', '3', '+', '2']                   |
    restartPolicy: Always | Never | OnFailure          |
													   |
#Job
apiVersion: batch/v1
kind: Job
metadata:
  name: math-add-job
spec:
  backoffLimit: 4                 [fail a Job after 4 retries]
  completions: 3                  [3 Pod created for the Job sequentially. If 2 fails then it again create 2 sequentially. Finally total successful pod will be 3]
  parallelism: 3                  [3 pod started in parallel for the job. If 2 pod fails then it start 2 again. Finally total successful pod will be 3]
  template:
          ---------------------------------------------|  
    spec:                                              |
      containers:                                      |
      - name: math-add                                 | Pod Definition
        image: ubuntu                                  |
	    command: ['expr', '3', '+', '2']               |
        restartPolicy: Always | Never | OnFailure      |
      
 - output can be seen in the log of the Pod
   kubectl logs <pod-name>
   
#CronJob
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: reporting-cron-job
spec:                       (spec for cron)
  schedule: "*/1 * * * *"
  jobTemplate: 
            ------------------------------------------------||
	 spec:                  (spec for job)                  ||
        completions: 3                                      ||
        parallelism: 3                                      ||
        template:                                           ||
          spec:             (spec for pod)                  ||
            containers:                                     ||
            - name: math-add                                || Job Definition
              image: ubuntu                                 ||
	          command: ['expr', '3', '+', '2']              ||
              restartPolicy: Always | Never | OnFailure     ||
            
 
root@controlplane:~# kubectl create cronjob  throw-dice-cron-job  --image=kodekloud/throw-dice --schedule="30 21 * * *" --dry-run=client -o yaml > cron.yaml

#Services
1) NodePort: Listen to a port on the Node and Forward request to a Pod. This service make internal Pod accessible on a port on the Node
       - Port of the Pod. TargetPort
	   - Port where service is running: 
	   - Port of the Node which is open for external world. NodePort (Range: 30000-30267)
	   
2) ClusterIp: Creates a virtual IP inside the cluster to enable communication between different services like a set of frontend servers & set of backend servers.
3) LoadBalancer: Distribute load accross web server


#Complete Service
apiVersion: v1
kind: Service
metadata:
   Name: myapp-service
   Labels: 
     app: myapp-service
spec:
   type: ClusterIP | NodePort | LoadBalancer  (if not specified then default ClusterIP)
   selector:                            ||  
     app: myapp                         || Form pod 
	 tier: front-end                    ||  
   ports:
   - protocol: 
     targetPort: 80    (port of the Pod)
     port:     80      (mandatory> Port of the Service)
	 nodePort: 30008   (exposed port for NodePort Service)


#Ingress
A) What need for Ingress Controller
- A deployment of Nginx Ingress IMAGE
- A service to expose it
- A config map to feed Nginx config data
- A service account with right permissions to acess all of this object


B) Ingress-resource

apiVersion: networking.k8s.io/v1                                apiVersion: networking.k8s.io/v1
kind: Ingress                                                   kind: Ingress
metadata:                                                       metadata:
  name: ingress-wear-watch                                        name: ingress-wear-watch
  namespace: critical-space                  					  namespace: critical-space
  annotations:                                                    annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /                    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"                nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:                                                           spec:
  rules:                                                        rules: 
                                                                - host: wear.my-online-store.com
  - http:                                                           http:                                              
     paths:                                                           paths:
	 - path: /wear
	   backend:                                                       - backend:
	     service:                                                         service: 
		    name: wear-service                                               name: wear-s ervice
		    port: 80                                                         port: 80
	                                                            - host: watch.my-online-store.com
	 - path: /watch                                                 http:
       backend:                                                       paths:
	     service:                                                     - backend: 
		    name: watch-service                                           service:
			port: 80                                                        name: watch-service
		                                                                    port: 80 
		                                                                  


root@controlplane:~# kubectl get ingress --all-namespaces
                     NAMESPACE   NAME                 CLASS    HOSTS   ADDRESS   PORTS   AGE
                     app-space   ingress-wear-watch   <none>   *                 80      18m


#ingress controller deploy

1) Create a separate namespace ingress-space  for ingress activity

       root@controlplane:~# kubectl create namespace ingress-space 
                            namespace/ingress-space created
							
       root@controlplane:~# kubectl get namespaces
                            NAME              STATUS   AGE
                            app-space         Active   4m9s
                            default           Active   7m11s
                            ingress-space     Active   11s
                            kube-node-lease   Active   7m14s
                            kube-public       Active   7m14s
                            kube-system       Active   7m14s
       root@controlplane:~# 

2) The Ingress Controller(here NGINX) requires a ConfigMap object. Create a ConfigMap object in the ingress-space (Name: nginx-configuration)

       root@controlplane:~# kubectl -n ingress-space create configmap nginx-configuration
                            configmap/nginx-configuration created
							
       root@controlplane:~# kubectl -n ingress-space get configmap
                            NAME                  DATA   AGE
                            kube-root-ca.crt      1      2m43s
                            nginx-configuration   0      11s
       root@controlplane:~# 

3) The NGINX Ingress Controller requires a ServiceAccount. Create a ServiceAccount in the ingress-space namespace(Name: ingress-serviceaccount)
       root@controlplane:~# kubectl -n ingress-space create sa ingress-serviceaccount
                            serviceaccount/ingress-serviceaccount created
							
       root@controlplane:~# kubectl -n ingress-space get serviceaccount
                            NAME                     SECRETS   AGE
                            default                  1         4m29s
                            ingress-serviceaccount   1         15s
       root@controlplane:~# 

4) Roles
       root@controlplane:~# kubectl -n ingress-space get role
                            NAME           CREATED AT
                            ingress-role   2021-08-04T14:40:57Z
							
       root@controlplane:~# kubectl -n ingress-space describe role ingress-role 
                            Name:         ingress-role
                            Labels:       app.kubernetes.io/name=ingress-nginx
                                          app.kubernetes.io/part-of=ingress-nginx
                            Annotations:  <none>
                            PolicyRule:
                              Resources   Non-Resource URLs  Resource Names                     Verbs
                              ---------   -----------------  --------------                     -----
                              configmaps  []                 []                                 [get create]
                              configmaps  []                 [ingress-controller-leader-nginx]  [get update]
                              endpoints   []                 []                                 [get]
                              namespaces  []                 []                                 [get]
                              pods        []                 []                                 [get]
                              secrets     []                 []                                 [get]
       root@controlplane:~#

5) RoleBinding
       root@controlplane:~# kubectl -n ingress-space get rolebinding
                            NAME                   ROLE                AGE
                            ingress-role-binding   Role/ingress-role   2m34s
							
       root@controlplane:~# kubectl -n ingress-space describe rolebinding ingress-role-binding 
                            Name:         ingress-role-binding
                            Labels:       app.kubernetes.io/name=ingress-nginx
                                          app.kubernetes.io/part-of=ingress-nginx
                            Annotations:  <none>
                            Role:
                              Kind:  Role
                              Name:  ingress-role
                            Subjects:
                              Kind            Name                    Namespace
                              ----            ----                    ---------
                              ServiceAccount  ingress-serviceaccount  
       root@controlplane:~#

6) Let us now deploy the Ingress Controller.

    ---
                   apiVersion: apps/v1
                   kind: Deployment
                   metadata:
                     name: ingress-controller
                     namespace: ingress-space
                   spec:
                     replicas: 1
                     selector:
                       matchLabels:
                         name: nginx-ingress
                     template:
                       metadata:
                         labels:
                           name: nginx-ingress
                       spec:
                         serviceAccountName: ingress-serviceaccount
                         containers:
                           - name: nginx-ingress-controller
                             image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
                             args:
                               - /nginx-ingress-controller
                               - --configmap=$(POD_NAMESPACE)/nginx-configuration
                               - --default-backend-service=app-space/default-http-backend
                             env:
                               - name: POD_NAME
                   			  valueFrom:
                                   fieldRef:
                                     fieldPath: metadata.name
                               - name: POD_NAMESPACE
                                 valueFrom:
                                   fieldRef:
                                     fieldPath: metadata.namespace
                             ports:
                               - name: http
                                 containerPort: 80
                               - name: https
                                 containerPort: 443
                   			  
        root@controlplane:~# kubectl -n ingress-space get deploy
                             NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
                             ingress-controller   0/1     1            0           41s
        root@controlplane:~#
   
7) Let us now create a service to make Ingress available to external users. Spec 
     Name: ingress
     Type: NodePort
     Port: 80
     TargetPort: 80
     NodePort: 30080
     Namespace: ingress-space
     Use the right selector

     root@controlplane:~# kubectl -n ingress-space create svc nodeport ingress  --tcp=80:80 --node-port=30080  --dry-run=client -o yaml > ingress-svc.yaml
     root@controlplane:~# 

              apiVersion: v1
              kind: Service
              metadata:
                name: ingress
                namespace: ingress-space
              spec:
                ports:
                - nodePort: 30080
                  port: 80
                  protocol: TCP
                  targetPort: 80
                selector:
                  name: nginx-ingress
                type: NodePort
  
8) Create the ingress resource to make the applications available at /wear and /watch on the Ingress service. Create the ingress in the app-space namespace. Spec
        Path: /wear
        Path: /watch
        Configure correct backend service for /wear
        Configure correct backend service for /watch
        Configure correct backend port for /wear service
        Configure correct backend port for /watch service


       root@controlplane:~# kubectl -n app-space create ingress resource \
                            --rule="/wear*=wear-service:8080"  \
							--rule="/watch*=video-service:8080"  \
							--default-backend=default-http-backend:80 \
							--annotation nginx.ingress.kubernetes.io/rewrite-target=/   \
							--annotation nginx.ingress.kubernetes.io/ssl-redirect=false  \
							--dry-run=client -o yaml> ingress-resource.yaml
	   
	   note: /wear* means path=/wear and pathType: Prefix

       root@controlplane:~# vi ingress-resource.yaml 
       root@controlplane:~#

                 apiVersion: networking.k8s.io/v1
                 kind: Ingress
                 metadata:
                   name: resource
                   namespace: app-space
                   annotations:
                     nginx.ingress.kubernetes.io/rewrite-target: /
                     nginx.ingress.kubernetes.io/ssl-redirect: "false"
                 spec:
                   defaultBackend:
                     service:
                       name: default-http-backend
                       port:
                         number: 80
                   rules:
                   - http:
                       paths:
                       - backend:
                           service:
                             name: wear-service
                             port:
                               number: 8080
                         path: /wear
                         pathType: Prefix
                       - backend:
                           service:
                             name: video-service
                             port:
                               number: 8080
                         path: /watch
                         pathType: Prefix
			  
"ingress-resource.yaml" 28L, 546C 

   
#aliases
export ns=default
alias k='kubectl -n $ns' # This helps when namespace in question doesn't have a friendly name 
alias kdr= 'kubectl -n $ns -o yaml --dry-run'.  # run commands in dry run mode and generate yaml.

or



alias k=kubectl

