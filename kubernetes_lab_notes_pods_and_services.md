
### GKE

```
# env
gcloud config set compute/zone us-west1-c

# create cluster
gcloud container clusters create io

# you will automatically authenticated to your cluster upon creation
# ff you lose connection to your Cloud Shell for any reason, run the `gcloud container clusters get-credentials io` command to re-authenticate
```

```
# copy sources
gsutil cp -r gs://spls/gsp021/* .

# change dir and list files
cd orchestrate-with-kubernetes/kubernetes
ls -la
```

### Quick kubernetes demo

```
# launch a single instance of the nginx container
kubectl create deployment nginx --image=nginx:1.10.0

# view the running nginx container
kubectl get pods

# expose it outside of Kubernetes
kubectl expose deployment nginx --port 80 --type LoadBalancer

# list our services
kubectl get services

# call exposed on external ip service
curl http://34.83.247.231:80
```

### Pods

```
cd ~/orchestrate-with-kubernetes/kubernetes

# Pods can be created using pod configuration files
cat pods/monolith.yaml
```
> ```
> apiVersion: v1
> kind: Pod
> metadata:
>   name: monolith
>   labels:
>     app: monolith
> spec:
>   containers:
>     - name: monolith
>       image: kelseyhightower/monolith:1.0.0
>       args:
>         - "-http=0.0.0.0:80"
>         - "-health=0.0.0.0:81"
>         - "-secret=secret"
>       ports:
>         - name: http
>           containerPort: 80
>         - name: health
>           containerPort: 81
>       resources:
>         limits:
>           cpu: 0.2
>           memory: "10Mi"
> ```
```
kubectl get pods
kubectl describe pods monolith
```

```
# by default, pods are allocated a private IP address and cannot be reached outside of the cluster
# port forward to map a local port to a port inside the monolith pod
kubectl port-forward monolith 10080:80

curl http://127.0.0.1:10080
# {"message":"Hello"}

curl http://127.0.0.1:10080/secure
# authorization failed

# login using password "password" 
curl -u user http://127.0.0.1:10080/login
# jvt tocken printout
#  {"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InVzZXJAZXhhbXBsZS5jb20iLCJleHAiOjE3MDM5NTExMjYsImlhdCI6MTcwMzY5MTkyNiwiaXNzIjoiYXV0aC5zZXJ2aWNlIiwic3ViIjoidXNlciJ9.7z1BERzUtedZ0Vuo32E2LV1HtMRAaOwC4X0boHmc9yg"}

# Cloud Shell does not handle copying long strings well, create an environment variable for the token
TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')
echo $TOKEN
# eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InVzZXJAZXhhbXBsZS5jb20iLCJleHAiOjE3MDM5NTEyODcsImlhdCI6MTcwMzY5MjA4NywiaXNzIjoiYXV0aC5zZXJ2aWNlIiwic3ViIjoidXNlciJ9.yKIXdplgC_BM7dB9X5v63Im9xVq4bxCdl-K1PzSUxSo

curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
# {"message":"Hello"}

#check logs
kubectl logs monolith

# interactive shell
kubectl exec monolith --stdin --tty -c monolith -- /bin/sh
```
> ```
> ping -c 3 google.com
> exit
> ```


### Services

!!! if need to re-authenticate - in new terminal
```
gcloud config set compute/zone us-west1-c
gcloud container clusters get-credentials io` command to re-authenticate
```

Pods aren't meant to be persistent.  
They can be stopped or started for many reasons.  
What if you want to communicate with a set of Pods?  
When they get restarted they might have a different IP address.

Services provide stable endpoints for Pods.

Services use labels to determine what Pods they operate on. If Pods have the correct labels, they are automatically picked up and exposed by our services.

The level of access a service provides to a set of pods depends on the Service's type. Currently there are three types:
- ClusterIP (internal) -- the default type means that this Service is only visible inside of the cluster,
- NodePort gives each node in the cluster an externally accessible IP
- LoadBalancer adds a load balancer from the cloud provider which forwards traffic from the service to Nodes within it.



```
cd ~/orchestrate-with-kubernetes/kubernetes

cat pods/secure-monolith.yaml

kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
kubectl create -f pods/secure-monolith.yaml

# monolith service configuration file
cat services/monolith.yaml

kubectl create -f services/monolith.yaml

# allow traffic to the monolith service on the exposed nodeport
gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000

# get an external IP address for one of the nodes
gcloud compute instances list

curl -k https://35.227.159.211:31000
# curl: (7) Failed to connect to 35.227.159.211 port 31000: Connection refused

kubectl get services monolith
kubectl describe services monolith
```
> ```
> Name:                     monolith
> Namespace:                default
> Labels:                   <none>
> Annotations:              cloud.google.com/neg: {"ingress":true}
> Selector:                 app=monolith,secure=enabled
> Type:                     NodePort
> IP Family Policy:         SingleStack
> IP Families:              IPv4
> IP:                       10.4.10.113
> IPs:                      10.4.10.113
> Port:                     <unset>  443/TCP
> TargetPort:               443/TCP
> NodePort:                 <unset>  31000/TCP
> Endpoints:                <none>
> Session Affinity:         None
> External Traffic Policy:  Cluster
> Events:                   <none>
> ```

```
# add label to pods
kubectl label pods secure-monolith 'secure=enabled'
kubectl get pods secure-monolith --show-labels

kubectl describe services monolith | grep Endpoints
# Endpoints:                10.0.1.6:443

gcloud compute instances list
curl -k https://35.233.170.105:31000
# {"message":"Hello"}
```

### Deploying applications with Kubernetes

You're going to break the monolith app into three separate pieces:
- auth - Generates JWT tokens for authenticated users.
- hello - Greet authenticated users.
- frontend - Routes traffic to the auth and hello services.

```
cat deployments/auth.yaml

kubectl create -f deployments/auth.yaml
kubectl create -f services/auth.yaml

kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

# one more step to creating the frontend because you need to store some configuration data with the container
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml

kubectl get services frontend

curl -k https://34.82.227.68
#{"message":"Hello"}

