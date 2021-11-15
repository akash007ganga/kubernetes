#dns entry format
<service-name>.<namespace>.svc.cluster.local
db-service.dev.svc.cluster.local

#imperative command for service

1: Nodeport
root@controlplane:~# `kubectl expose pod/deploy nginx --name nginx-service --port=80  --type=NodePort `

 - This will automatically use the pod's labels as selectors, 
 - generate a definition file and manually input the nodeport before creating the service.
   spec: 
     type: NodePort
     selector:
        app: MyApp
     ports:
     - port: 80
       targetPort: 80                              ||||By default `targetPort` is set to the same value as the `port` field.
       nodePort: 30007    (default: 30000-32767)   |||| only this needs to be added.
	   
2: `kubectl create svc nodeport nginx-service  --tcp=80:80 --node-port=30080`
(This will not use the pods labels as selectors)

3: ClusterIP
root@controlplane:~# `kubectl expose pod/deploy httpd --name=httpd         --port=80     --target-port=8080 `

4: Debug Nodeport Services
   k run busybox --image=busybox --restart=Never --rm -it -- wget -O- <NODE_IP>:30007 --timeout=2
   
   for node ip
   root@controlplane:~# k get no -o wide



#create a redis pod using the redis:alpine image with the labels set to tier=db. Expose it on container port 8080.
root@controlplane:~# kubectl run redis --image=redis:alpine -l tier=db --port=8080

note: This label  (-l) only works for pod (not for deploy)
note: expose container port : --port=8080
      expose pod :  k expose pod nginx ....... (service create)

#specify default limit memory=512Mi and cpu=1 for containers
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-cpu-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
	  cpu: 1
    defaultRequest:
      memory: 256Mi
	  cpu: 0.5
    type: Container
	
# Resource Quota
root@controlplane:~# kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 $do > quota.yaml
root@controlplane:~# vi quota.yaml 

#difference between Limit Range and Resource Quota
ResourceQuota: quotas for the total amount memory and CPU that can be used by all Containers running in a namespace
Limit Range:  If the Container does not specify its own memory limit, then the Container is assigned the default memory limit of the namespace. 

#Create a taint on node01 with key of spray, value of mortein and effect of NoSchedule
root@controlplane:~# kubectl taint node node01 spray=mortein:NoSchedule

#) label  node01 as size=large  
root# kubectl label nodes <node-name> <label-key>=<label-value> 
root# kubectl label nodes node01 size=Large 

# create a PV 

Purpose: To create large volume(storage) centrally by administrators so that Pod's will use chunk using PersistentVolumeClaim

apiVersion: v1
kind: PersistentVolume
metadata: 
  name: pv-vol1
spec:
  storageClass: slow
  accessModes:
  - ReadOnlyMany | ReadWriteOnce | ReadWriteMany
  capacity: 
    storage: 1Gi
  persistentVolumeReclaimPolicy: Retain | Delete | Recycle
  hostPath:
    path: /tmp/data
	
#create a PVC
Purpose: used by user to use Persistent Volumes. One PVC can be bound with only one PV. One PV can have only one PVC. Matching between PVC and PV is done with 
access mode, storage class, capacity etc.

1) Create PVC

apiVersion: v1
kind: PersistentVolumeClaim
metadata: 
  name: pvc-volume
spec:
 storageClass: slow
 accessModes: 
 - ReadOnlyMany | ReadWriteOnce | ReadWriteMany
 resources:
   requests:
     storgae: 500Mi

# cretae a namespace my-namespace
# create config map db-config-map in my-namespace with value DB_Host=localhost
# create config map all-config-map in my-namespace with value DB_NAME=prod and DB_TYPE=oracle 
# create config map redis-config-map in my-namespace with value DB_CACHE=redis

# create secret all-secret in my-namespace with value DB_PWD=password
# create secret db-secret in my-namespace with value DB_KEY=key

# create service account dashboard-sa in my-namespace


#Create a deployment my-deployment in my-namespace with container name my-container, image nginx which will have a pod, replicas:10, 
1: Container should run the command: while true; do date; sleep $TIME_FREQ;done and write the result to the location /opt/time/time-check.log. 
2: Container should load a property DB_Host from ConfigMap db-config-map
3: Container should load all property from ConfigMap all-config-map and secret all-secret
4: It should mount A configmap redis-config-map in a volume called redis-config at path /redis-master. Volume should be readonly
5: It should load a secret db-secret as volume but don't mount it
6: run all container with user ID 1002 (pod level), group as 3000, fsGroup 2000. Check the configuration is ok
7: run another container simple-webapp with image=busybox, command: sleep 3600 user id 1001 (container label). add capabilities to SYS_TIME (only supported at container level)
8: use service account dashboard-sa. Stop mounting default service account
9: my-container should use max 1 cpu and 500 Mebibyte of memory. Should request 0.1 cpu and 200 Mi of memory
10. Pod should tolerate the above taint of the node01 (key of spray, value of mortein and effect of NoSchedule)
11: ensure the pod must be scheduled in node01
12: System will be considered as ready when http get request to /api/ready:8080 will be successful or the container to be reachable on port 3306 or executes 
    the command cat /app/is_ready in the target container and return code is 0. Use the port name http-port instead of 3306
	Ensure kubelet should wait 5 seconds before performing the first probe. 
	kubelet should perform probe every 10 seconds for maximum 30 times(the application will have a maximum of 5 minutes (30 * 10 = 300s) to finish its startup).
13. Ensure during rollout total number of Pods available at all times during the update will be 7 (out of 10) and 
    ensure total number of old and new pod should not exceed 130% of desired pods
14: Ensure pods are restarted always by replica set in case they are not not live(as per liveness) or terminated
15: There is one directory present in /data. Mount this inside container at /opt named data-volume
16: use above created pvc named pvc-volume. Mount it inside /pvc
17: The path /opt/time on the pod should mount a volume that lasts the lifetime of this pod
18: Container should expose port 3306 with name  http-port

apiVersion: apps/v1
kind: Deployment
metadata: 
  name: my-deployment
  namespace: my-namespace
spec:
  replicas: 10 
  strategy: 
     type: RollingUpdate                              || `values: Recreate | RollingUpdate`
	 rollingUpdate                                    || `Recrete = All old pods will die before scheduling new Pod`
            maxSurge:  13                             ||`13: total number of old and new pod should not exceed 130% of desired pods. Desired pod=10`
            maxUnavailable:3                          ||`total number of Pods unavailable at all times during the update. we can specify 30% or 3
  selector:
     matchLabels:
     name: my-pod  
  template:                                            ||
    metadata:                                          ||
      labels:                                          ||
         name: my-pod                                  ||`POD`
    spec:                                              ||
      containers:                                    
	   - name: simple-webapp              ||
	     image: busybox                   ||
		 securityContext:                 ||
           runAsUser: 1001                ||`7: container level security context(both user and add capabilities is supported)`
		   capabilities:                  ||`
             add:                         ||
             - SYS_TIME                   ||
		 args:
		 - /bin/sh
		 - -c
		 - 'sleep 3600'
		 
       - name: my-container                            ||
         image: busybox                                ||
         ports:                                        ||
         - name: http-port                             || 18: port is exposed by a name
           containerPort: 3306                         ||                                               
         command: ["/bin/sh"]                                                                           ||`1: run a command in a shell.`
         args: ["-c", "while true; do date; sleep $TIME_FREQ;done > /opt/time/time-check.log"]          || 
         env:                       
         - name: DB_Host                                               ||
           valueFrom:                                                  ||
           configMapKeyRef:                                            ||`2: load a properety from cm`. `for secret secretKeyRef`
               name: db-config-map                                     ||
               key: DB_Host                                            ||
			   
         envFrom:                                                ||
         - configMapRef:                                         ||
               name: all-config-map                              ||`3: load all properties of a config map and secret`. 
																 ||
	     - secretRef:                                            ||
		       name: all-secret                                  || `note it is name and not secret name like volumes(question number 5)`
          
         volumeMounts:                                ||
		 - name: redis-config                         ||
		   mountPath: /redis-master                   ||`4.2: configmap mounted inside container`
		   readOnly: true
		   
		 - name: data-volume             ||`15: Mounted host path based volume
		   mountPath: /opt               ||
		   
		 - name: pvc-volume          ||`16: mounted pvc volume
		   mountPath: /pvc           ||
		   
		 - name: empty-vol           ||`17.empty dir
		   mountPath: /opt/time      ||
		   		   
		 resources:                 ||
           limits:                  ||
             cpu: 1                 || 
		     memory: 500Mi          || `9: container level limit/request specification. 1 cpu = 1000m. Minimum can be 1m (mili)`
           requests:                || `0.1 is same as 100m`
             cpu: 0.1               || `remeber for memory, M is in caps(Mi), for cpu m is in small`
		     memory: 20Mi           ||
			 
	     readinessProbe:                ||
            httpGet:                    ||
               path: /api/ready         ||
               port: 8080               ||
			initialDelaySeconds: 5      ||
            periodSeconds: 10           ||
            failureThreashold: 30       ||
									    ||
         readinessProbe:                ||`12: readiness probe definition. `
            tcpSocket:                  ||`For liveness : only replace readinessProbe with livenessProbe`
               port: 3306 or http-port  ||
			initialDelaySeconds: 5      ||
            periodSeconds: 10           ||
            failureThreashold: 30       ||
									    ||
         readinessProbe:                ||
            exec:                       ||
      	     command:                   ||
             - cat                      ||
             - /app/is_ready            ||
            initialDelaySeconds: 5      ||
            periodSeconds: 10           ||
            failureThreashold: 30	    ||	   
												      
	  securityContext:                         ||
          runAsUser: 1002                      || `6: Pod level security context(only run is supported. capabilities not). For root this will be 0`
		  runAsGroup: 3000
		  fsGroup: 2000
	  
	  serviceAccountName: dashboard-sa                   ||`8: service account can be specified in Pod level only'
	  automountServiceAccountToken: true | false         ||
	  
	  tolerations:                                 ||
      - key: spray                                 ||
        operator: Equal                            || `10: Tolerations for Pod. Default value of the Operator is Equal`
        value: mortein                             || ` Operator values can be Equal, Exists. For Exists no value is required`
	    effect: NoSchedule                         ||
		
	  nodeSelector:                    ||
	     size: Large                   ||`11: Pod must be placed on Large node(node01)'
		 
	  affinity: 
	     nodeAffinity:                                                ||
		  requiredDuringSchedulingIgnoredDuringExecution:             || `values: preferredDuringSchedulingIgnoredDuringExecution`
		    nodeSelectorTerms:                                        ||
		    - matchExpresssions:                                      ||`11: Pod must be placed on Large node(node01)[more generic solution]'
			  - key: size                                             ||
			    operator: In                                          ||`values: In, NotIn, Exists
				values:                                               ||
				- Large                                               ||
				
	  restartPolicy: Always                                           ||`14: Pod       : Always (default)| OnFailure | Never
	                                                                  || `   Depolyment: Always `
	  
	  volumes:                                             ||
	  - name: redis-config                                 ||
	    configMap:                                         ||`4.1: configmap loaded in a volume`
		  name: redis-config-map                           ||
		  
      - name: secret-volume                        ||
        secret:                                    ||`5: secret loaded in a volume`. `Note SecretName and not name`
          secretName: db-secret					   ||	
		  
      - name: data-volume
        hostPath:                                ||
          path: /data                            ||
          type: Directory		                 ||`15: Host path based volume in the node
		  
	  - name: pvc-volume                          ||
	    persistentVolumeClaim:                    ||`16: pvc volume
		  claimName: pvc-volume                   ||
	
	  - name: empty-vol                           ||`17: empty dir
	    emptyDir: {}                              ||
		

	
# Horizontal Pod Autoscaler (HPA)	
# Make sure the replicas autoscale with minimum 2 and maximum 5 when at 80% CPU for my-deployment.

controlplane $ kubectl autoscale deployment my-deployment --cpu-percent=80 --min=2 --max=5
horizontalpodautoscaler.autoscaling/my-deployment autoscaled

controlplane $ kubectl get hpa
NAME             REFERENCE                  TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
my-deployment   Deployment/my-deployment   <unknown>/80%      2         5         0         6s
controlplane $

#remove the Taint on the node01
1: check
root@controlplane:~# kubectl describe node node01 | grep -i Taint 
Taints:             spray=mortein:NoSchedule
root@controlplane:~#

2: Untaint
root@controlplane:~# kubectl taint nodes node01 spray=mortein:NoSchedule- 
node/node01 untainted

#check annotations
kubectl annotate pod nginx1 --list

kubectl describe po nginx1 | grep -i 'annotations'

#Remove annotation flagship=finalizer form node01
kubectl annotate node node01 flagship-

#check the load of the Node node01 and all the pods
root@controlplane:~/kubernetes-metrics-server# ` kubectl top node `
      NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
      controlplane   254m         0%     943Mi           0%        
      node01         283m         0%     687Mi           0%        
      root@controlplane:~/kubernetes-metrics-server#
	
   note: 254m is 254 mili cores	
	  
root@controlplane:~/kubernetes-metrics-server#` kubectl top pod `
      NAME       CPU(cores)   MEMORY(bytes)   
      elephant   44m          52Mi            
      lion       2m           7Mi             
      rabbit     152m         106Mi 

# Change the image version to some unknown version and check the activity fails
# cancel last activity
1: kubectl set image deployment/my-deployment busybox=busybox:pal  --record

2: kubectl rollout status deployment my-deployment
   kubectl rollout history deployment my-deployment
   
3: kubectl rollout undo deployment my-deployment

4: kubectl rollout pause deploy my-deployment

5: kubectl rollout resume deploy my-deployment

6: kubectl rollout undo deploy my-deployment --to-revision=1

7: kubectl rollout history deploy my-deployment --revision=3

# Create a job with  
1: it will only calculate 3 + 2. 
2: Ensure before failing, it should tries 4 times
3: Ensure it should use 3 successful workers
4: ensure it should use 3 parallel worker
5: Schedule the job every 1 min (??)
6: If task is not completed within 100 seconds the job should fail and pods should be terminated
7: The cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time (i.e. the job missed its scheduled time).

root@controlplane:~# kubectl create job NAME --image=image [--from=cronjob/name] -- [COMMAND] [args...]
                     kubectl create job my-job --image=busybox -- date     
					 
root@controlplane:~# kubectl create cronjob  throw-dice-cron-job  --image=kodekloud/throw-dice --schedule="30 21 * * *" -- date  

apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: reporting-cron-job
spec:                       (spec for cron)
  schedule: "*/1 * * * *"
  startingDeadlineSeconds: 17  || `7: cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time`
  jobTemplate:
    metadata:
      name: math-add-job
    spec:                  (spec for job)
	  activeDeadlineSeconds: 100 `If task not done within 100s the job should fail and pods should be terminated. takes precedence over backoffLimit`
      backoffLimit: 4            `fail a Job after 4 retries`
      completions: 3             `3 Pod created sequentially. If 2 fails then it again create 2 sequentially. Finally total successful pod will be 3`
      parallelism: 3             `3 pod started in parallel. If 2 pod fails then it start 2 again. Finally total successful pod will be 3`
      template:
              ---------------------------------------------|  
        spec:                   (spec for pod)             |
          containers:                                      |
          - name: math-add                                 | Pod Definition
            image: ubuntu                                  |
    	    command: ['expr', '3', '+', '2']               |
          restartPolicy: Always | Never | OnFailure        |
		  
#difficult keywords: 
- automountServiceAccountToken: false
- initialDelaySeconds: 5
- periodSeconds: 10
- failureThreashold: 30
- activeDeadlineSeconds: 100 
- backoffLimit: 4            
- completions: 3             
- parallelism: 3
- startingDeadlineSeconds: 17
    
#ingress
 root@controlplane:~# kubectl -n app-space create ingress resource \
                            --rule="/wear*=wear-service:8080"  \
							--rule="/watch*=video-service:8080"  \
							--default-backend=default-http-backend:80 \
							--annotation nginx.ingress.kubernetes.io/rewrite-target=/   \
							--annotation nginx.ingress.kubernetes.io/ssl-redirect=false  \
							--dry-run=client -o yaml> ingress-resource.yaml
							
#netpol


#Get the name and image of all pods in skywalker namespace having label jedi=true. Write the output to /root/jedi-true.txt.
#Output should be in the format (name, image). Use jsonpath.

kubectl get pods -l=jedi=true -n skywalker -o=jsonpath='{range .items[*]}{.metadata.name},{.spec.containers[0].image}{"\n"}{end}' > /root/jedi-true.txt

#Create a pod with image nginx called nginx and expose its port 80. Confirm that ClusterIP has been created. Also check endpoints
1: root@controlplane:~# k run nginx --image=nginx --port=80 --expose
   service/nginx created
   pod/nginx created
   root@controlplane:~# 

2: kubectl get svc nginx # services
   kubectl get ep # endpoints
   
# Create a temp pod with image nginx/busybox called temp check other pod
   `k run tmp --image=nginx:alpine --restart=Never --rm -it  -- curl 10.12.2.15 -m 5`    [using nginx, it has curl]
    
	-m: Maximum time in seconds that you allow the whole operation to take
   or

  `kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.1.1.131:80 --timeout 2` [using busybox, it has wget]
   Tip: --timeout is optional, but it helps to get answer more quickly when connection fails (in seconds vs minutes)

#If pod crashed and restarted, get logs about the previous instance
kubectl logs nginx -p
 or
kubectl logs nginx --previous

#Create a busybox pod that echoes 'hello world' and then exits
kubectl run busybox --image=busybox -it --restart=Never -- echo 'hello world'
 or
kubectl run busybox --image=busybox -it --restart=Never -- /bin/sh -c 'echo hello world'

#Create an nginx pod and set an env value as 'var1=val1'. Check the env value existence within the pod
kubectl run nginx --image=nginx --restart=Never --env=var1=val1
 then
kubectl exec -it nginx -- env

# Change the labels of pod nginx2 to be app=v2
root@controlplane:~# k label po nginx2 app=v2 --overwrite=true
pod/nginx2 labeled

#Get the label 'app' for the pods (show a column with APP labels)
  kubectl get po -L app
   or
  kubectl get po --label-columns=app
  
#List the nginx pod with custom columns POD_NAME and POD_STATUS (follow cheat sheat)
kubectl get po -o=custom-columns="POD_NAME:.metadata.name, POD_STATUS:.status.containerStatuses[].state"

#List all the pods sorted by name (follow cheat sheat)
kubectl get pods --sort-by=.metadata.name


#command and args
1: remember after -- you should give /bin/sh, then space then -c then space then <'all commands sperataed by ;'>
ex: 
kubectl create job busybox --image=busybox --dry-run=client -o yaml -- /bin/sh -c 'echo hello;sleep 30;echo world' > job.yaml

This will add three args in yaml file

```YAML
.......
spec:
      containers:
      - args:
        - /bin/sh
        - -c
        - echo hello;sleep 30;echo world
        image: busybox
.......
```	

If instead of args, we need ccommand then add --command

`kubectl create job busybox --image=busybox --dry-run=client -o yaml --command -- /bin/sh -c 'echo hello;sleep 30;echo world' > job.yaml`

```YAML
.......
spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - echo hello;sleep 30;echo world
        image: busybox
.......
```	

#Create an nginx pod that uses 'myuser' as a service account
kubectl run nginx --image=nginx --restart=Never --serviceaccount=myuser -o yaml --dry-run=client > pod.yaml
kubectl apply -f pod.yaml

#debug pod
1: kubectl logs busybox # will bring nothing! container never started
2: kubectl describe po busybox # in the events section, you'll see the error
3: kubectl get events | grep -i error # you'll see the error here as well
4: kubectl delete po busybox --force --grace-period=0
5: kubectl get ep  (end points)

 
#Connect to the second busybox, write the first column of '/etc/passwd' file to '/etc/foo/passwd'

root@controlplane:~# k exec -it busybox -c busybox2 -- sh

/ # cat /etc/passwd | cut -f 1 -d ':' > /etc/foo/passwd 

f: field
d: delimeter

#Copy '/etc/passwd' from the pod to your local folder
root@controlplane:~# kubectl cp busybox2:/etc/foo/passwd ./passwd
tar: removing leading '/' from member names
root@controlplane:~# cat passwd 
......

#Get the pods with labels env=dev or env=prod
root@controlplane:~# kubectl get pods -l 'env in (dev,prod)'

#label 5 pods whose names are nginx1, nginx2,...,nginx5
root@controlplane:~# k label po nginx{1..5} app=nginx

or

root@controlplane:~# k label po nginx1 nginx2 nginx3 nginx4 nginx5 app=nginx

#verify runAsUser for pod
root@controlplane:~# k exec -it secbusybox -- sh
/ $ id
uid=1000 gid=2000

#verify capabilities for containers
root@controlplane:~# k exec -it nginx -- sh 
# cd /proc/1
# cat status
Name:   nginx
......
CapPrm: 00000000aa0435fb
CapEff: 00000000aa0435fb
......

#check any command
root@controlplane:~#kubectl explain pods --recursive | grep envFrom -A3
here 3 is number of lines below envFrom.
root@controlplane:~#kubectl explain pods --recursive | less 
(and search for envFrom)
root@controlplane:~# kubectl explain pod.spec.containers.livenessProbe

#autocomplete with kubectl alias
alias k=kubectl
complete -F __start_kubectl k

#export the service account, use it inside pod, check the token
$ kubectl create serviceaccount backend-team
serviceaccount/backend-team created

$ kubectl get serviceaccount backend-team -o yaml --export

$ kubectl run backend --image=nginx --restart=Never --serviceaccount=backend-team

$ kubectl exec -it backend -- /bin/sh
/ # cat /var/run/secrets/kubernetes.io/serviceaccount/token
eyJhbGciOiJ......

#create config map from file where file contains key-value pair
root@controlplane:~# k create cm db-config --from-env-file=config.txt
configmap/db-config created

Note: if file contains key/value pair, always use --from-env-file (though the extension of the file is not .env), otherwise filename will be appended in key

#Render the surrounding 3 lines of YAML of all Pods that have annotations.
kubectl get pods -o yaml | grep -C 3 'annotations:'

#use cat command to create a file

cat << EOF  > <filename> 
<contents>
EOF

root@controlplane:~# cat << EOF > pv.yaml
> apiVersion: v1
> .......
>   hostPath:
>     path: "/mnt/data"
> EOF

#ncode-decode secrets/kubernetes
root@controlplane:~# echo -n 'username' | base64
dXNlcm5hbWU=

root@controlplane:~# echo `echo dXNlcm5hbWU= | base64 -d`
username
root@controlplane:~# 

remember -o yaml gives encrypted token
describe secret gives unencryptedd token

# count all the namespaces

root@controlplane:~# kubectl get ns --no-headers | wc -l
10
root@controlplane:~# 

#What is the base Operating System used by the python:3.6 image?
$ docker run python:3.6 cat /etc/*release*

#docker commands
- version
   C:\Users\echypal> docker --version   (small output)
                     docker version  (detail output)
- create image
   C:\Users\echypal> docker build -t <hub-user/repo-name:releaseversion> <docker file location>
   C:\Users\echypal> docker build -t akash007ganga/hello-docker:latest C:\repository\git\Docker
   
- check images
   - C:\Users\echypal> docker images
   - C:\Users\echypal> docker images -q (unique images)
   
- start container from image with port forwarding and named container
   - docker run <immage-name>
   - docker run --name <container-name>  <immage-name>
   - docker run -p <to port>:<from port> image
   
   or
   - docker create <image-name>  <container-id>
     docker start <container-id>
	 
- List all running container
  - docker ps 
  
- List all container (running & exited)
  - docker ps -a

- stop a container
  - docker kill <container-id>
  
- remove container
  - docker rm <container-id>
  
- remove image
  - docker rmi <image-name>
  - docker rmi <image-name> -f (forced)
  
- enter into a container
  - docker exec -it <container-id> sh
  
- logs of the container
  -  docker logs  <container Id/name>
     

#location of kubeconfig file (Conext information: Authentication)
file location:
root@controlplane:/# vi /root/.kube/config

it has information about user, cluster & association(context)
  
conext is a triplet (cluster, user, namespace)
 - name: dev-frontend
   cluster: development
   namespace: frontend
   user: developer

1: create cluster  development & scratch

kubectl config set-cluster development --server=https://1.2.3.4 --certificate-authority=fake-ca-file

kubectl config set-cluster scratch --server=https://5.6.7.8 --insecure-skip-tls-verify

2: create context(cluster, user, namespace) for cluster

kubectl config set-context dev-frontend --cluster=development --namespace=frontend --user=developer

The dev-frontend context says, "Use the credentials of the developer user to access the frontend namespace of the development cluster".

3: set current context to dev-frontend

kubectl config use-context dev-frontend

Now whenever you enter a kubectl command, the action will apply to the cluster, and namespace listed in the dev-frontend context. And the command will use the credentials of the user listed in the dev-frontend context.

4: view
kubectl config view

5: use another config file
kubectl config --kubeconfig=/root/my-kube-config use-context research
kubectl config --kubeconfig=/root/my-kube-config current-context

6: get cluster/context/user
   root@controlplane:~# kubectl config get-clusters
   NAME
   kubernetes
   
   root@controlplane:~# kubectl config get-contexts
   CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
   *         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
   
   root@controlplane:~# kubectl config get-users   
   NAME
   kubernetes-admin



#Inspect the environment and identify the authorization modes configured on the cluster.
root@controlplane:/# kubectl describe po kube-apiserver-controlplane -n kube-system
Command:
      kube-apiserver
      --advertise-address=10.20.46.9
      ....
      --authorization-mode=Node,RBAC
	  
#Grant the dev-user permissions to create deployments in the blue namespace.
#Remember to add both groups "apps" and "extensions"

--resource format is: <kubectl resource>.<api-group>
remeber role & rolebinding is namespaced resource

root@controlplane:~# k create role dev-role --verb=create --resource=deployments.apps --resource=*.extensions -n blue
root@controlplane:~# k create rolebinding dev-role-binding  --role=dev-role --user=dev-user -n blue

[note]
User defined in : /<user-home>/.kube/config  (like /root/.kube/config)
Roles defined as kind: Role in particular namespace
User given role as RoleBinding for a particular namespace. 
If namespace is not given, it is taken as default

#Check which admission controllers are enabled by default
1) root@controlplane:~# k exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugins
   or
   root@controlplane:~#grep enable-admission-plugins /etc/kubernetes/manifests/kube-apiserver.yaml

2) root@controlplane:~# kubectl describe po kube-apiserver-controlplane -n kube-system
now look for enable-admission-plugins

3) Since the kube-apiserver is running as pod you can check the process to see enabled and disabled plugins.
root@controlplane:~# ps -ef | grep kube-apiserver | grep admission-plugins

#identify api group of job
root@controlplane:~# kubectl api-resources | grep job

#identify OS
Run the command cat /etc/*release*

#helm commands
1) Templates(yaml with place holders) will have placeholders

{{.Values.image}}
{{.Values.storage}}

2) values will be present in values.yaml like following

image: nginx:1.10
storage: 20Gi

3) Templates + values.yaml = Final version of definition files = Helm chat

4) Another file Chart.yaml has information about chart like description, maintainers, version etc.

5) Search helm charts 
  root@controlplane:~# helm search hub wordpress (in Artifact Hub)
  root@controlplane:~# helm search repo wordpress (from installed repo like Bitnami)
  
6) helm install [release-name] [chart-name]
    - downloaded
	- extracted
	- installed
   each installation is called release and this is specified in [release-name]
   same application can be installed multiple times by just specifying different release name. Each release is independant of each other
   
7) Commands
  - help about any commands
     root@controlplane:~# helm repo -h  
	 
  - Adding repo
       root@controlplane:~# helm repo add bitnami  https://charts.bitnami.com/bitnami
        name: bitnami
	    url: https://charts.bitnami.com/bitnami
		
  - List charts of the Repositories
       root@controlplane:~# helm repo list
	   
  - Search a pacakge in repo
       root@controlplane:~# helm search repo drupal
       NAME            CHART VERSION   APP VERSION     DESCRIPTION                                       
       bitnami/drupal  10.4.1          9.2.7           One of the most versatile open source content m...
	   
  - check if there is any updated chart 
       root@controlplane:~# helm repo update
	   
  - Install drupal. Release name should be bravo
     root@controlplane:~# root@controlplane:~# helm  install bravo bitnami/drupal -n default
	 	 
  - List pacakges installed using helm
       root@controlplane:~# helm list -n default
	   NAME    NAMESPACE       REVISION        UPDATED                                 STATUS         CHART            APP VERSION
       bravo   default         1               2021-10-29 11:48:08.644319701 +0000 UTC deployed       drupal-10.4.1    9.2.7 
	   
	   root@controlplane:~# helm ls -n default
	   
	   root@controlplane:~# helm ls -a -n default (show all release like pending-upgrade)
	   
  - upgrade release 
     root@controlplane:~# helm upgrade bravo bitnami/nginx -n default
	   
  - Uninstall the drupal helm package which we installed earlier.
       root@controlplane:~# helm uninstall bravo -n default
       release "bravo" uninstalled
	   
  - Download the bitnami apache package under the /root directory. Do not install the package
    root@controlplane:~# helm pull --untar bitnami/apache
    root@controlplane:~# ls   
    apache
    root@controlplane:~# ls apache
    Chart.lock  README.md  ci     templates           values.yaml
    Chart.yaml  charts     files  values.schema.json
    root@controlplane:~# 

  - rollback an upgrade
    root@controlplane:~# helm rollback <RELEASE> [REVISION]
	     
	root@controlplane:~# helm rollback <RELEASE> 0 (rollback to the last successful release)
	
  - show the customized values
    root@controlplane:~# helm show values bitnami/apache # will show a long list of all possible value-settings
    root@controlplane:~# helm show values bitnami/apache | yq e # parse yaml and show with colors
	
	Huge list, if we search in it we should find the setting replicaCount: 1 on top level. This means we can run:
	
	root@controlplane:~# helm -n mercury install internal-issue-report-apache bitnami/apache --set replicaCount=2
  
   

#Practice links
1: https://github.com/dgkanatsios/CKAD-exercises/
2: https://medium.com/bb-tutorials-and-thoughts/practice-enough-with-these-questions-for-the-ckad-exam-2f42d1228552		
3: https://github.com/bmuschko/ckad-prep
4: https://dev.to/vijaydaswani/ckad-test-4ca1	
5: https://matthewpalmer.net/kubernetes-app-developer/articles/ckad-practice-exam.html

https://www.udemy.com/course/ckad-real-exam-questions/							

https://www.youtube.com/watch?v=h9-eBWeHaAA&feature=youtu.be	


Code:   ERIC7april2153227-FNFQWkh

URL: https://trainingportal.linuxfoundation.org/
https://sso.linuxfoundation.org/
https://www.examslocal.com/linuxfoundation
username: akash007ganga
password: $Fevicol12345		

Vim setup
---------------
1) :set shiftwidth=2	
2) shift + v to select, then use arrow key to select multiple lines
   then shift > or < to indent
   press . to repeat the action
   
3) Mark lines
   Esc+V (then arrow keys)
   Copy marked lines: y
   Cut marked lines: d
   Past lines: p or P
   
3) u to undo
   ctrl + r to redo
4) :set number or :set nonumber for number

5) ctrl +insert (copy) Shift + insert (paste)


Questions
1) From google chrome? slower or ok?
2) room checking? mobile allowed?
3) copy paste working normally ctrl + c ? ctrl +insert (copy) Shift + insert (paste)
4) ssh to other nodes? how to back?
3) sudo -i for root	





------------------------------------------------------ exam details -----------------------------------
certificationsupport@linuxfoundation.org 




Linux Foundation
Certified Kubernetes Application Developer
CKAD - English
date
11/5/2021
time
12:30 PM - 2:30 PM - India Standard Time
Confirmation Code
D13-821

Candidate Id
0773629354
