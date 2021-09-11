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

4: Debug Services
   k run busybox --image=busybox --restart=Never --rm -it -- wget -O- <NODE_IP>:30007 --timeout=2
   
   for node ip
   root@controlplane:~# k get no -o wide



#create a redis pod using the redis:alpine image with the labels set to tier=db. Expose it on container port 8080.
root@controlplane:~# kubectl run redis --image=redis:alpine -l tier=db --port=8080

note: This label  (-l) only works for pod (??)

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
 accessModes: 
 - ReadOnlyMany | ReadWriteOnce | ReadWriteMany
 resources:
   requests:
     storgae: 500Mi

#Create a deployment my-deployment in my-namespace with container name my-container, image busybox which will have a pod, replicas:10, 
1: Container should run the command: while true; do date; sleep $TIME_FREQ;done and write the result to the location /opt/time/time-check.log. 
2: Container should load a property DB_Host from ConfigMap db-config-map
3: Container should load all property from ConfigMap all-config-map and secret all-secret
4: It should mount A configmap redis-config-map in a volume called redis-config at path /redis-master. Volume should be readonly
5: It should load a secret db-secret as volume but don't mount it
6: run all container with user ID 1002 (pod level), group as 3000, fsGroup 2000
7: run another container simple-webapp with user id 1001 (container label). add capabilities to SYS_TIME (only supported at container level)
8: use servicce account dashboard-sa. Stop mounting default service account
9: container should use max 1 cpu and 200 Mebibyte of memory
10. Pod should tolerate the above taint of the node01 (key of spray, value of mortein and effect of NoSchedule)
11: ensure the pod must be scheduled in node01
12: System will be considered as ready when http get request to /api/ready:8080 will be successful or the container to be reachable on port 3306 or executes 
    the command cat /app/is_ready in the target container and return code is 0. 
	Ensure kubelet should wait 5 seconds before performing the first probe. 
	kubelet should perform probe every 10 seconds for maximum 30 times(the application will have a maximum of 5 minutes (30 * 10 = 300s) to finish its startup).
13. Ensure during rollout total number of Pods available at all times during the update will be 7 (out of 10) and 
    ensure total number of old and new pod should not exceed 130% of desired pods
14: Ensure pods are restarted always by replica set in case they are not not live(as per liveness) or terminated
15: There is one directory present in /data. Mount this inside container at /opt named data-volume
16: use above created pvc named pvc-volume. Mount it inside /pvc
17: The path /opt/time on the pod should mount a volume that lasts the lifetime of this pod

apiVersion: apps/v1
kind: Deployment
metadata: 
  name: my-deployment
  namespace: my-namespace
spec:
  replicas: 10 
  strategy: 
     type: RollingUpdate                              || `values: Recreate | RollingUpdate`
	 rollingUpdate                                    ||
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
	     image: log-agent                 ||
		 securityContext:                 ||
           runAsUser: 1001                ||`7: container level security context(both user and add capabilities is supported)`
		 capabilities:                    ||`
           add:                           ||
           - SYS_TIME                     ||
		 
       - name: my-container                            ||
         image: busybox                                ||
         ports:                                        ||
         - name: http                                  ||
           containerPort: 80                           ||                                               
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
             cpu: "1"               || 
		     memory: 200Mi          || `9: container level limit/request specification. 1 cpu = 1000m. Minimum can be 1m (mili)`
           requests:                || `0.1 is ame as 100m`
             cpu: "0.1"             || `remeber for memory, M is in caps(Mi), for cpu m is in small`
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
               port: 3306               ||
			initialDelaySeconds: 5      ||
            periodSeconds: 10           ||
            failureThreashold: 30       ||
									    ||
         readinessProbe:                ||
            exec:                       ||
      	     command:                   ||
               - cat                    ||
               - /app/is_ready          ||
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
				
	  restartPolicy: Always                                           ||`14: Pod level: other values OnFailure | Never `
	  
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
root@controlplane:~# kubectl describe node node01 | grep Taint 
Taints:             spray=mortein:NoSchedule
root@controlplane:~#

2: Untaint
root@controlplane:~# kubectl taint nodes node01 spray=mortein:NoSchedule- 
node/node01 untainted

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
  startingDeadlineSeconds: 17  || 7: cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time
  jobTemplate:
    metadata:
      name: math-add-job
    spec:                  (spec for job)
	  activeDeadlineSeconds: 100 [If task is not completed within 100 seconds the job should fail and pods should be terminated. takes precedence over backoffLimit]
      backoffLimit: 4               [fail a Job after 4 retries]
      completions: 3                [3 Pod created for the Job sequentially. If 2 fails then it again create 2 sequentially. Finally total successful pod will be 3]
      parallelism: 3                [3 pod started in parallel for the job. If 2 pod fails then it start 2 again. Finally total successful pod will be 3]
      template:
              ---------------------------------------------|  
        spec:                   (spec for pod)             |
          containers:                                      |
          - name: math-add                                 | Pod Definition
            image: ubuntu                                  |
    	    command: ['expr', '3', '+', '2']               |
          restartPolicy: Always | Never | OnFailure        |
    
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

# Create a temp pod with image nginx called nginx and expose traffic on port 80. use a temp pod to check its '/'
   `k run tmp --image=nginx:alpine --restart=Never --rm -it  -- curl -m 5 10.12.2.15 `    [using nginx, it has curl]

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
 or
kubectl exec -it nginx -- sh -c 'echo $var1'
 or
kubectl describe po nginx | grep val1
 or
kubectl run nginx --restart=Never --image=nginx --env=var1=val1 -it --rm -- env

# Resource Quota
root@controlplane:~# kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 $do > quota.yaml
root@controlplane:~# vi quota.yaml 

# Change the labels of pod nginx2 to be app=v2
root@controlplane:~# k label po nginx2 app=v2 --overwrite=true
pod/nginx2 labeled

#Get the label 'app' for the pods (show a column with APP labels)
  kubectl get po -L app
   or
  kubectl get po --label-columns=app
  
#List the nginx pod with custom columns POD_NAME and POD_STATUS
kubectl get po -o=custom-columns="POD_NAME:.metadata.name, POD_STATUS:.status.containerStatuses[].state"

#List all the pods sorted by name
kubectl get pods --sort-by=.metadata.name


#command and args
1: remember after -- you should give /bin/sh, then space then -c then space then <'all commands sperataed by ;'>
ex: 
kubectl create job busybox --image=busybox --dry-run=client -o yaml -- /bin/sh -c 'echo hello;sleep 30;echo world' > job.yaml

This will add three command in yaml file

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

#Create a pod with image nginx called nginx and expose its port 80. Confirm that ClusterIP has been created. Also check endpoints
1: root@controlplane:~# k run nginx --image=nginx --port=80 --expose
   service/nginx created
   pod/nginx created
   root@controlplane:~# 

2: kubectl get svc nginx # services
   kubectl get ep # endpoints
   
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

#Practice links
1: https://github.com/dgkanatsios/CKAD-exercises/
2: https://medium.com/bb-tutorials-and-thoughts/practice-enough-with-these-questions-for-the-ckad-exam-2f42d1228552		
3: https://github.com/bmuschko/ckad-prep													