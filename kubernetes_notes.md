# kubernetes_notes

## kubectl

kubectl [command] [type] [name] [flags]

command - what do you want to do (get, describe, logs, exec)
type - on what type of object (pods, deployments, nodes)
name - what is the object's name
flags - any special requests (-o=yaml, -o=wide)

## Deployments

Deployments describe a desired state of Pods.

Deployments are designed for stateless applications - they don't store data or application state to a cluster or to persistent storage.

*.yaml file (describes desired state)  
-> Deployment object (submited to the kub control plane)  
-> Deployment controller (ponsible for converting the desired state into reality and keeping that desired state over time)  
-> Node  
-> Pod  

The Deployment is a high-level controller for a pod that declares its state.

Any Deployment has three different lifecycle states:
- the Deployment's Progressing state (tasks on ReplicaSet)
- the Deployment's Complete (all new replicas have been updated and are available, and no old replicas are running)
- the Failed state

Create deployments:
- manifest yaml file
- imperatively - kubectl run command that specifies the parameters inline
- the GKE Workloads menu in the GCP Console

Scale the deployment:
- manually using a kubectl command or in the Cloud console
- manually changing the manifest
- auto-scaling using the kubectl auto-scale command or from the Cloud Console directly

scanning the cluster as a whole, just the particular deployment within that cluster

Horizontal Pod Autoscaling is indicated for sudden increases in resource usage
Vertical Pod Autoscaling provides recommendations for resource usage over time
Multidimensional Autoscaling - you can use horizontal scaling based on CPU and vertical scaling based on memory at the same time.

## Jobs

A Job is a Kubernetes object, like a Deployment.

Job -> create one/more Pods -> run task -> track completion -> terminate created pods -> report that the Job was completed

the Job object can be created:
- kubectl apply command
- kubectl run command

CronJob is a Kubernetes object that creates Jobs in a repeatable manner to a defined schedule.












































## misc
Run a temporary Pod called "toolbox" with the label app=toolbox and get a shell in the Pod:
```
kubectl run toolbox --labels app=toolbox --image=alpine --restart=Never --rm --stdin --tty
```




```
# define vars
export my_zone=us-east1-c
export my_cluster=standard-cluster-1

# create cluster of 3 nodes
gcloud container clusters create $my_cluster --num-nodes 3 --zone $my_zone --enable-ip-alias

# modify cluster to 4 nodes
gcloud container clusters resize $my_cluster --zone $my_zone --num-nodes=4
```


```
# generate kubeconfig file
gcloud container clusters get-credentials $my_cluster --zone $my_zone

# check
cat ~/.kube/config
```

```
# inspect cluster
kubectl config view

kubectl cluster-info

kubectl config current-context

kubectl config get-contexts
kubectl config use-context gke_${GOOGLE_CLOUD_PROJECT}_us-east1-c_standard-cluster-1

kubectl top nodes
```

```
source <(kubectl completion bash)
```

```
# deploy nginx
kubectl create deployment --image nginx nginx-1

# check
kubectl get pods

export my_nginx_pod=nginx-1-6d648b45b9-sbl2p
echo $my_nginx_pod

kubectl describe pod $my_nginx_pod














