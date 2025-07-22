Namespace : 
- It is a virtual cluster within k8s cluster. It's logical group of k8s resource of some project.
- We can apply security and resource allocation at namespace level.
- K8s supports both json & yaml formats.

We can create a resources using :
1. Imperative  : using commands
2. Declarative : using manifest

Difference betn apply & create
- create : one time create
- apply : create & apply
------------------------------------------------------------------------------------------------------------
Pods :
What is static pods?
--> static pod will not have any controller associate with it. Static pod will be managed by kubelet which is running in that node.
static pods are stored in one directory & kubelets are monitoring this directory.

path: /etc/kubernetes/manifests

there are 2 types of pods
1. Single container Pod
2. Multi container Pod

- Network & storage are common betn Pods.
- Pods will have unique IP.
- 2 containers with same port is not possible.
- Pod to Pod communication (within namespace/other namespace) >> ip address
- Pod (A namesapce) to svc (B namespace) >> FQDN

How containers in same pod communicate with each other?
- localhost:<port>

How one pod communicate with other pod within cluster?
- Service discovery & lb
---------------------------------------------------------------------------------
Replication controller/Replicaset/Deployment
- auto heal the application
- create a new pod if existing pod crashes.
  
Repliciation Controller & Replicaset difference 
- Repliciation Controller supports only equality based selecter
- Replicaset supports equality & set based selector. (match expression's)

When we do any chges in replication controller & replicaset it cant't reflect immediately. We have to restart the pod. Thats why downtime came.
------------------------------------------------------------------------------------------
Deployment:
Benefits:
- Deploy a RS
- upadte pods
- rollback to previous version
- scale deployment up or down

Deployment strategy
1. Recreate
- All the previous pod deleted & created a new.
- Downtime

2. Rolling update (default)
- Create a new pod first first, once it is ready delete the old one.

maxSurge : how many new versions pod will be created
maxUnavailable : how many pods are unavailable
minReadySeconds : Pod will be created within how many seconds.
------------------------------------------------------------------------------------------
Daemonset:
- We can not autoscale the daemonset
- If in future we created a new node the new pod will be created automatically.
- copy of pod in each node of k8s cluster

Used for:
- monitoring agent
- logging agent
- kube-proxy
- calica

------------------------------------------------------------------------------------------
Statefulset:
used for stateful app
create a pod 1st,2nd & then 3rd

rs.initiate({
  _id: "MainRepSet",
  members: [
    { _id: 0, host: "mongod-0.mongodb-service.default.svc.cluster.local:27017" },
    { _id: 1, host: "mongod-1.mongodb-service.default.svc.cluster.local:27017" },
    { _id: 2, host: "mongod-2.mongodb-service.default.svc.cluster.local:27017" }
  ]
});

# create a username & password
db.getSiblingDB("admin").createUser({
  user: "devdb",
  pwd: "devdb123",
  roles: [{ role: "root", db: "admin" }]
});

rs.status()
------------------------------------------------------------------------------------------
Job : 
- It execute once
- Installing scripts
------------------------------------------------------------------------------------------

Cronjob
- daily reports
- cleanup jobs
- backups
------------------------------------------------------------------------------------------
labels & selectors:
- lables are key value pairs attached to object
- If one thing in k8s, to find another thing in k8s it uses labels

kubectl get pods --selector tier=frontend
------------------------------------------------------------------------------------------
Init containers:
- Init Containers are special containers that run before the main application container(s) start in a Pod.
-  Wait for a servic : Wait until a database, API, or another dependency is reachable.
------------------------------------------------------------------------------------------
Liveness Probe:
- Pod is running but application is not responding (To check health of application & restart the pods if fails)

Readiness Probe :
- to check container is ready to accept traffic.

initialDelaySecond : 5
wait for 5 sec to perform first health check

0 --> successful response
1-7 --> failure response
------------------------------------------------------------------------------------------
Configmap & secret

Configmap --> not case sensitive
Secret --> to case sensitive

To store secret
- as a volume
- image pull secret 

kubectl create secret docker-registry dockerhubsecret --docker-server=docker.io --docker-username=<name> --docker-password=<password> -n <namespace>

imagePullsecrets:
    - name: dockerhubsecret
---------------------------------------------------------------------------------------------------------------------------
Node Affinity:
requiredDuringSchedulingIgnoredDuringExecution:
- If no matching node is available, the pod will stay in the Pending state.
- Ignored during execution: If the node's labels change after scheduling, the pod won’t be evicted, even if it no longer matches.

preferredDuringSchedulingIgnoredDuringExecution:
- If a matching node exists → Kubernetes prefers it.
- If not → it still schedules the pod on a non-matching node.
---------------------------------------------------------------------------------------------------------------------------
Request & limit
1. Case I
request memory = 100
limit memory = 200
stress =250
--> out of memory error
The pod is scheduled to a node with at least 100Mi free memory (because that’s the request).
At runtime, the pod tries to use 250Mi, which exceeds the limit of 200Mi.
The container is killed by the kernel (OOMKilled) due to exceeding its memory limit.

2. Case 2
request memory = 1000
limit memory = 2000
stress =250
--> insufficient memory

The pod asks for 1000Mi of memory up front (this is the request).
Kubernetes tries to schedule it on a node with at least 1000Mi available.
If no node has 1000Mi free, scheduling fails with:
---------------------------------------------------------------------------------------------------------------------------
HPA:
What it does: Scales the number of pods.
How it works: Based on metrics like CPU, memory, or custom metrics.

Vertical Pod Autoscaler (VPA):
What it does: Adjusts the CPU and memory requests/limits for a pod.
How it works: Observes pod resource usage over time and recommends or applies updated resources.
Use case: You want to optimize individual pod resource settings, not scale out.
Example: If a pod consistently uses 400Mi memory but is allocated 1Gi, VPA may reduce the request to save resources.

Cluster Autoscaler (for node scaling)
Scale cluster compute with Karpenter and Cluster Autoscaler

---------------------------------------------------------------------------------------------------------------------------
PV & PVC :

Retain means Kubernetes won’t touch the storage after a PVC is deleted — you must clean it up manually.

Access Mode:
- ReadWriteOnce : Mounted as read-write by only one node
- ReadOnlyMany  : Mounted as read-only by many nodes
- ReadWriteMany : Mounted as read-write by multiple nodes
- ReadWriteOncePod : Mounted as read-write by a single pod on a single node (v1.22+)

Case I : If 90 GB PV ,If we are claiming 100 GB, then PVC will go to the pending state.
Case 2 : If Pv has RW mode, If pvc has different mode then it will go to the pending state.

Reclaim Policy:
1. Retain : The PV will remain exist but it will be in the release status & no one pvc can request data from it.
2. Delete policy : If PVC will be delete as well as pv will be deleted
3. recycle : PVC will be delete , the PV will be available for other.

Dyanmic volume provisioning create a volume for it (not create pv & pvc)

---------------------------------------------------------------------------------------------------------------------------
RBAC :
Create a Role >> Assign permission to a role >> assign the role to perticular user or group

cd  /etc/kubernetes/pki/. >> where all certifiates are stored

RBAC : Roll back access control of k8s

How to manage access of k8s resources?
>> We can manage access of k8s resource using RBAC.
Authentication : Checking the identity of user (IAM)
Authorization : Checking the permission of user (RBAC)

3 types of user who can access the k8s
1. developer
2. application
3. end user

Differece betn Roles & cluster role:
Roles are attached at namespace level & cluster roles has not barrier of namespace.

Service account :
Each pod runs as a user (identity) when it interacts with the Kubernetes API. This identity is provided by a ServiceAccount.
By default, pods use the default ServiceAccount in their namespace.
But for fine-grained access control, we create and use custom ServiceAccounts.

Reference doc : https://faun.pub/how-to-integrate-kubernetes-on-aws-eks-with-jenkins-the-devsecops-way-36d72407f302
----------------------------------------------------------------------------------
Service Discovery:
Service Discovery in Kubernetes (K8s) refers to the automatic detection of services within a Kubernetes cluster. It allows different components (like pods, deployments, etc.) to communicate with each other without manually managing IP addresses or port numbers.