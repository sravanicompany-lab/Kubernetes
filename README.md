# Kubernetes

kubectl - command line tool used to run commands against your kubernetes cluster.
kubectl create deployment --name ngnix --image ngnix:latest --replicas 6

If we enter the kubectl create command,the first request will be sent to the api server,it is one of the major component in entire kubernetes architecture where it always interact with the external world and also always validates the request and also it is responsible for server handling requests,state management,authentication and authorization.

it validates the request and stores all the specifications in etcd,Key-value store that maintains the cluster's state and configuration

and scheduler is mainly responsible for sceduling the pods in difnmferent worker nodes on data plane.scheduler have the information of all worker nodes.

control manager - If the active state of the cluster changes,entire worker node is down specifically under this control manager there is something called nodecontroller in the repliction controller,they will work together and ensure all the pods are running on the failed node are rescheduled to the another worker node.
Manages controllers like ReplicaSet to maintain desired states.

each and every componets of control node and kube-proxy running as pods itself.

kubelet works as daemon service.kubelet will be responsible for all the containers on worker nodes running correctly.If containers are failed for some reason it will restart the container and also reports status back to the control plane.

kube-proxy:if we are mentioning the service type as nodeport or clusterport or loadbalancer,each and every pod is having some unique ip address that will be assigned by kube-proxy.

Kubernetes Architecture: 

Kubernetes consists of two main parts:
1️⃣ Control Plane – The “brains” of Kubernetes, making decisions and managing the cluster.
2️⃣ Worker Nodes – The “hands” of Kubernetes, where your applications (pods) actually run.


Common Issues and How They Relate to Components

📌Control Node Components

🚀 1. API Server
The API Server acts as the front desk for all Kubernetes operations.
Common Issue:
“kubectl commands are timing out.”
Troubleshooting Tip:
🛠 Check the API Server logs or test the connection using:
kubectl get componentstatus.

🚀 2. etcd
The etcd is like Kubernetes’ memory, storing all cluster state information.
Common Issue:
“Cluster state is out of sync or lost.”
Troubleshooting Tip:
🛠 Monitor etcd health using etcdctl and ensure backup mechanisms are in place.

🚀 3. Scheduler
The Scheduler decides which node should run a pod.
Common Issue:
“Pods are stuck in a Pending state.”
Troubleshooting Tip:
🛠 Check node resource usage with:
kubectl describe nodes.
Scale up the cluster if necessary.

🚀 4. Controller Manager
The Controller Manager ensures the cluster’s desired state matches the actual state.
Common Issue:
“ReplicaSet isn’t creating the desired number of pods.”
Troubleshooting Tip:
🛠 Inspect controller logs and verify your deployment YAML files for errors.

🚀 5. Cloud Controller Manager (CCM)
The CCM integrates Kubernetes with cloud provider services like load balancers or storage.
Common Issue:
“Load balancer isn’t getting provisioned in the cloud.”
Troubleshooting Tip:
🛠 Verify cloud provider configurations and ensure proper IAM roles or credentials are set.

📌Worker Node Components

🚀 1. Kubelet
The kubelet ensures containers within pods are running and healthy.
Common Issue:
“Pods keep restarting or crashing.”
Troubleshooting Tip:
🛠 Use:
kubectl logs <pod> and kubectl describe pod <pod>
to investigate pod events and container logs.

🚀 2. Kube-proxy
The kube-proxy manages networking within the cluster.
Common Issue:
“Pods can’t communicate with each other.”
Troubleshooting Tip:
🛠 Check network policies and ensure kube-proxy is running correctly.
Test connectivity by pinging between pods.

🚀 3. Container Runtime
The container runtime (e.g., Docker or containerd) runs the containers within a pod.
Common Issue:
“Containers are stuck in a Created state.”
Troubleshooting Tip:
🛠 Check the runtime logs and ensure it’s properly installed and running on the node.

KOPS - kubernetes operations,in the backend kops will try to create autoscaling groups if someone tries to delete our worker nodes or master nodes with the help of enhanced monitoring autoscaling group is going to create instances again.

kops will apply keys to all the nodes
ssh-keygen
cd .ssh
ls -al
.pub key

sravanikopspractice.xyz

export NAME=sravanikopspractice.xyz
export KOPS_STATE_STORE=s3://sravanikopspractice.xyz
export AWS_REGION=us-east-1
export CLUSTER_NAME=sravanikopspractice.xyz
export EDITOR='/usr/bin/nano'

kops create cluster --name=sravanikopspractice.xyz \
--state=s3://sravanikopspractice.xyz --zones=us-east-1a,us-east-1b \
--node-count=2 --control-plane-count=1 --node-size=t3.medium --control-plane-size=t3.medium \
--control-plane-zones=us-east-1a --control-plane-volume-size 10 --node-volume-size 10 \
--ssh-public-key ~/.ssh/id_ed25519.pub \
--dns-zone=sravanikopspractice.xyz --dry-run --output yaml

kops create -f cluster.yml
kops update cluster --name cloudvishwakarma.in --yes --admin
kops validate cluster --wait 10m
kops delete -f cluster.yml  --yes

kops export kubecfg --admin 

all the components on the kubernetes runs on same ip on kube-system

kubectl get pods -n kube-system -o wide | grep -i api

Imperative way:
kubectl run testpod1 --image=nginx:latest --dry-run=client -o yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: testpod3
  name: testpod3
spec:
  containers:
  - image: nginx:latest
    name: testpod3

Declrative using yaml or json

echo '
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: testpod3
  name: testpod3
spec:
  containers:
  - image: nginx:latest
    name: testpod3' | kubectl apply -f -

if in yaml file : kubectl apply -f testpod2.yaml 

kubectl get pods -o wide

docker run --rm -d --name app2 -p 80:8080 nginx:latest

pods: smallest deployable unit,its a wrapper which contains one or more containers.
Replica set: It is responsible to run or spread pods across multiple nodes to achieve high availablility.we can mention the replicas count in deployment.
Deployments: Deployments manage the creation and scaling of ReplicaSets, ensuring your applications have the desired number of running instances.

Imperative way: means deploying the pods using kubectl commands
declaritive way: doing the deployments via yaml files

Namespaces: Namespaces are used to divide cluster resources between multiple teams. They are useful for scenarios where multiple teams (e.g., Alpha and Bravo teams) share the same cluster but require resource isolation.

isolating the process.

kube-system have all the components like apiserver,control-manager,scheduler,etcd have same ip address.

kubectl create ns alpha
kubectl create ns bravo

kubectl get ns

kubectl run alpha1 -n alpha --image kiran2361339/kubegame:v1
kubectl get pods 
kubectl api-resources

kubectl explain pod

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: alpha1
  name: nginx
  namespace: alpha
spec:
  containers:
  - image: kiran2361993/kubegame:v1
    name: alpha1


kubectl get pods -n alpha

tar zxvf .gz file to extract the file.

kubens alpha

kubectl delete alpha2 alpha3

kubeforwarding : without services we can connect to the application

cd ~/.kube/
ls -l
cat config

copy entire code go to the lens application and paste it

you will be able to see kubernetes cluster UI(~/.kube/config)

then you have to do portforwarding to open it as local host
kubectl exec -it alpha1 -n alpha -- /bin/sh

kubectl -n alpha port-forward pod/alpha1 8000:80

https://localhost:8000

kubectl get pods

kubectl -n alpha port-forward pod/alpha1 8000:80

kubectl expose alpha1 --port 8000 --target-port-80 --type NodePort

kubectl get svc

ec2 dns ip:port of service exposed

security groups 

edit inbound rules custom tcp - 32667 anywhere from ipv4 - save

kubectl delete pod alpha1

kubectl describe svc

endpoint is showing as none
if we run pod again - pod ip will come and how the service knows to connect to that pod again - label we have given as pod name

Replicaset:In the backend it works on labels

apps/v1
nano replicaset.yaml

apiVersion: apps/v1
kind: Replicaset
metadata:
  name: frontend                        
  labels: 
    app: guestbook
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
    tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: kubegame
        image: kiran2361993/kubegame:v1

Services:
Services provide stable IP addresses and DNS names to Pods. They allow you to expose your applications within or outside the cluster.

Deployments:
Deployments manage the creation and scaling of ReplicaSets, ensuring your applications have the desired number of running instances.
whenever the pod is failed deployment will automatically roll up the pods.

Daemonset : It will create single pod on each and individual nodes,this is mainly used for monitoring and logging purpose.

Statefulset : it is useful for stateful applications like db,once the container is down for any reason the date will gone because containers are ephimeral in nature and those are stateless.

we will have persistent volume in kubernetes,each and every database will be having ordering,host names and networking identities also will change.

if the worker node is down and supporting team is working,then if pod comes up the index order will change

If didn't do our deployment using stateful set this index order will change,host name will be changed and the network identities also will change.

statefulset follows order and index and the hostname also remains same,it always essential for databases like mongo and sql.


load balancing : it will make sure it is distributing the network traffic evenly across the multiple pods.

kubectl create deployment testpod1 --image kiran2361993/kubegame:v2 --replicas 6 --dry-run -o yaml

what is label: label is going to store all the information in a key-value pair,replicasetname and podname will assign for multiple pods.
it will provide unique id to each pod.

keyvalue pair - testpod1-65c6b7f7dc-2cm4g
              deployment  name   replicasetname  pod name


labels:
  app: testpod1
name: testpod1

annotations: 
  info: "saikirn deployment"
  email: "saikiranp"
  owner: "saikiranp"

  using annotations we can select what type of load balancer we need

kubectl exec -it podname --bash

kubect describe podname

we are going to expose the application with the help of service.

kubectl expose deployment testpod1 --name svc1 --port 8000 --target-port 80 --type NodePort

kubectl expose deployment testpod1 --name svc2 --port 5000 --target-port 5000 --type NodePort

one new ip will get create like 31058 we have to expose it in the instance.

kubectl explain deployment.spec.strategy

kubectl explain deployment.spec.strategy.rollingUpdate

rolling update:

spec:
  strategy:
    rollingUpdate:
      maxSurge: 1  ->  The maximum number of pods that can be scheduled above the desired number of pods.
      maxUnavailable: 0 -> The maximum number of pods that can be unavailable during the update.

watch kubectl get pods

ctrl+X+Y => to save in nano file
ctrl+shift+P => to check the indendation in vscode.

if we change the version its going to terminate 6 and it will start 6 this is not the good practice in real time

so once we updated the version from v1 to v2 first pod will be updated and old will be deleted.

spec:
  minreadySeconds: 10
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0

so if we use rolling update for each and every 10 seconds v1 pod is deleted and new pod will come up.

for daemonset we don't have any replicas,multiple pods are there on single node
3 pods are running on 3 nodes without giving any replicaset count.
3 pods deployed on three individual nodes.

clusterIp:

if someone tries to access our application from external world that means via internet.they cannot connect because clusterIP is only limited within our kubernetes cluster.
developer given task to test the application in the local environment in such case we can use clusterIp
we can use clusterIp for internal application and it can route the traffic to different pods.It can balance the load accordingly and it will follow round-robin model.

NodePort:

If we wanted to expose our application outside we can use NodePort,if we use nodeport clusterip will be there as it is responsible to route the traffic across multiple pods in the backend.
when you create a nodeport,unique host port will be created between 30000 and 32000.

we can create at the time of manifest file and we can mention port.

publicip:32001 customer can be able to access the application the request will be sent to nodeport.nodeport is doing natting internally.once this part is done nodeport will be writing to the clusterip.

Nodeport is not the use case for the production because clients or users we cannot give them publicip:port.we have to give some proper dns name.this is possible with loadbalancer

LoadBalancer: via LB it will provide domain name to access the application.it will give small dns name and we are going to create route53

ExternalName: As a Devops working on kubernetes application,product manager came and ask us to integrate external database means other team database for our application.db contains product information,customer feedbck and some reviews etc.that's why they want to integrate to kubernetes application.

: db is having some unique url or hostname and they wanted to migrate it to another version,then the url will be changed.if we are using that db in 29 applications we cannot go and change the db url and those details.instead of thta we can mention in external name.

External service:

# kubectl cluster-info

# kubectl create deployment app1 --image kiran2361993/kubegame:v2 --replicas 3 --dry-run -o yaml
# kubectl expose deployment app1 --port 80 --target-port 80 --type ClusterIP

# kubectl exec -it troubleshooting sh
# kubectl exec -it troubleshooting -- bash

# nslookup app1 - we are able to connect from one pod to another pod within the cluster.

it will be going to route the traffic according to the multiple pods in the different nodes.

when we expose to clusterip the port number will be 80 but when we use NodePort the port number will be assigned from 30000 to 32000

Loadbalancer is the real use case

we need one dns name to access the appplication.

 kubectl expose deployment app1 --port 80 --target-port 80 --type LoadBalancer

 cloud control manager will create one load balncer for us

root@ip-172-31-27-126:~# kubectl get svc
NAME   TYPE           CLUSTER-IP      EXTERNAL-IP                                                               PORT(S)        AGE
app1   LoadBalancer   100.64.127.73   adc189c506f6046be83093014fe44152-1261687411.us-east-1.elb.amazonaws.com   80:30907/TCP   18s


root@ip-172-31-27-126:~# kubectl get svc
NAME     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
alpha1   NodePort    100.64.171.218   <none>        8000:31383/TCP   4h
app1     ClusterIP   100.66.239.163   <none>        80/TCP           43m
svc1     NodePort    100.65.70.87     <none>        8000:30573/TCP   88m
svc2     NodePort    100.66.169.146   <none>        5000:30998/TCP   89m

we can attach this load balancer to dns name nd then access it via dns name
dns name we can configure it under Route 53

nameservers we have to configure and add it to dns

 adc189c506f6046be83093014fe44152-1261687411.us-east-1.elb.amazonaws.com

ClusterIP: it can be accessible only inside the cluster that means from pod to pod communication.
NodePort: we can expose the deployment based on nodeport 30000 to 32000 port,Exposes the service on a specific port on each node.
LoadBalancer: mainly useful for production use cases..Exposes the service externally using dns name or cloud provider’s load balancer. Suitable for production.

ExternalName: Maps a service to a DNS name for managing external resources.
Headless Service: Used for stateful applications, providing direct access to individual pods.

kubernetes Ingress:























                                                                                                                                                                                          

  





