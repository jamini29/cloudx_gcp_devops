# Deploying GKE Autopilot Clusters from Cloud Shell

### Deploy GKE cluster
```
# init
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project

# env
export my_region=us-central1
export my_cluster=autopilot-cluster-1

gcloud container clusters create-auto $my_cluster --region $my_region
```

### Connect to a GKE cluster
```
gcloud container clusters get-credentials $my_cluster --region $my_region

cat ~/.kube/config
```
> ```
> apiVersion: v1
> clusters:
> - cluster:
>     certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVMVENDQXBXZ0F3SUJBZ0lSQUxGTXFXdDVmbzBpMEp3dmdCaTFvOU13RFFZSktvWklodmNOQVFFTEJRQXcKTHpFdE1Dc0dBMVVFQXhNa1lUVTFaREJtWmpNdE5EUTNZaTAwTlRsa0xUaGxZMk10WlRNMFpERmxNbUk0TVRrNApNQ0FYRFRJek1USXhNekE0TkRnMU5Gb1lEekl3TlRNeE1qQTFNRGswT0RVMFdqQXZNUzB3S3dZRFZRUURFeVJoCk5UVmtNR1ptTXkwME5EZGlMVFExT1dRdE9HVmpZeTFsTXpSa01XVXlZamd4T1Rnd2dnR2lNQTBHQ1NxR1NJYjMKRFFFQkFRVUFBNElCandBd2dnR0tBb0lCZ1FEQTRDSnJKUFhlUldzUnlvL0pMMVU0VGpTaDdrTVcva1QzVHkrQgppa0dQbEhSUURPMzM5RFFaanM4U1pzd0gyOHBWL1pDajBLR3VDWWhjUE9jQUwwSFphdDBUbGlkVFhWazM0bkY1ClBsODZ3MGxkMEJJamo4S3hBRzRXZlp6SXFUTzVWdlo0L0MxdExkb0F4c1VPZ0xBZVRGL0lpS1FaVW5kRmRPRGUKTjJGdkFHRTEra1BoRkFpRHRoeUNMY2VkYlg0ZmZFRWtHRmZ1a1NmazRrMkRSMGZ0VGRHSStwRE9jM1Y2U016ZQpwWUNhMWxuVzExMTFQWW9aNVpiWHRKdXcyUTdLM1NwOENLek1jUldodWhEK1p6QXZMQW5mYUV3Y3VUZzhCU0liCmlidjZiQTF5QkErNWNxcVAxaGx6SEFjak5hbXBuS25OeEhkT21rbUFTMlp3NVFMUVc0djBTZFZXaUcwYXl4WWoKSFU2Rm1USllDU0Vwc3JJbHNoOTZEcjNucVpWOCtVOUpDbW5tRWdyUDIzU2ozYmJYbDJRWGxLVkpQTW9ORVk2MApGN05yV2hObGljcTNRdG9OSVlNOC9WZW1GSkxzanljRlhWd3pET201d0tpdVhMZE52eGlyRStTcjY1TE1RSE5BClMxYytlSkFvYWRJRDJUL1czTHZLOW9YdzZYY0NBd0VBQWFOQ01FQXdEZ1lEVlIwUEFRSC9CQVFEQWdJRU1BOEcKQTFVZEV3RUIvd1FGTUFNQkFmOHdIUVlEVlIwT0JCWUVGQVVXTVNHZTFTNGo3cFEyWHJoZEJkZUdSdmpaTUEwRwpDU3FHU0liM0RRRUJDd1VBQTRJQmdRQ2hFUWpKUmZFMnd4ek0xeG9GWitMUm4vakFseFY4OEtXZmJCRFF0dWFjCmF5bkowV3hablkwQWptNE9INjIvL1QwWGR4WmlmUC9FbVMyM1dIK0pBQVh5ZThHV29FYXE2YWNDWTRMZWFYdHEKaWVIOHc2YmJMUDFMOVpWak1GT0laRzJFNTh2MU9Eb3BqVEdwZnRjYVE5OE5Sa2hsRXl2Q2lhSmVEeGM3ZmQ2MApkUjRPR3pvaS9QalRicnlLQnh0NTNldkt2Smg4Y0N2WUhrR0hEUmp4STFuVk5JRC8yeDVzS0VuOFViNXZZTGRTCkJOWmlodGxHck5tTnJZRFZlUjBqZVBFYUFZN0xSTDJ4RmRIVTNUZjZ3Y2RuK1ZwdDlSS0VTTVA4UDhtOHRKYWsKK0lpdFo3Sk1jSWNGYUYwUWRGbC9qd3ZLOWppRjNCZHZSbW9UTlNyN3p1eW94SXZ6Vmd2OUlzQk5aYkR0ZXQ5Ugp1bEZVSFhhZjdEU1NzbVZXbDJHaEJGSmNoNHJhaG9oN3BmeFY2TlMzRU1LK1phRWxEWlV2dGpPSyszZjBYRDVjClRKd3pDUW1RNWdudjg2UXF3M0ZQdzZXdHFXZ2Qrd3pHdnlVcGRuZkhSYkVEdTZxb3I2VXhZckNlanE4NkIreVcKQmF4UU9KUlR4MEYwZmpEUVJnRTBXemM9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
>     server: https://35.192.172.252
>   name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> contexts:
> - context:
>     cluster: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>     user: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>   name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> current-context: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> kind: Config
> preferences: {}
> users:
> - name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>   user:
>     exec:
>       apiVersion: client.authentication.k8s.io/v1beta1
>       command: gke-gcloud-auth-plugin
>       installHint: Install gke-gcloud-auth-plugin for use with kubectl by following
>         https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke
>       provideClusterInfo: true
> ```

### Inspect a GKE cluster
```
kubectl config view
```
> ```
> apiVersion: v1
> clusters:
> - cluster:
>     certificate-authority-data: DATA+OMITTED
>     server: https://35.192.172.252
>   name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> contexts:
> - context:
>     cluster: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>     user: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>   name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> current-context: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> kind: Config
> preferences: {}
> users:
> - name: gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
>   user:
>     exec:
>       apiVersion: client.authentication.k8s.io/v1beta1
>       args: null
>       command: gke-gcloud-auth-plugin
>       env: null
>       installHint: Install gke-gcloud-auth-plugin for use with kubectl by following
>         https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke
>       interactiveMode: IfAvailable
>       provideClusterInfo: true
> ```

```
kubectl cluster-info
```
> ```
> Kubernetes control plane is running at https://35.192.172.252
> GLBCDefaultBackend is running at https://35.192.172.252/api/v1/namespaces/kube-system/services/default-http-backend:http/proxy
> KubeDNS is running at https://35.192.172.252/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
> Metrics-server is running at https://35.192.172.252/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
> 
> To further debug and diagnose cluster problems, use 'kubectl cluster-info dump
> ```

```
# show current cluster
kubectl config current-context
```
> ```
> gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1
> ```

```
# list of clusters
kubectl config get-contexts
```
> ```
> CURRENT   NAME                                                               CLUSTER                                                            AUTHINFO                                                           NAMESPACE
> *         gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1   gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1   gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1   
> ```

```
# switch to the cluster you need
kubectl config use-context gke_${DEVSHELL_PROJECT_ID}_us-central1_autopilot-cluster-1
```
> ```
> Switched to context "gke_qwiklabs-gcp-01-a248a4c9aa49_us-central1_autopilot-cluster-1".
> ```

```
# enable bash autocompletion for kubectl
source <(kubectl completion bash)
```

### deploy Pods to GKE
```
kubectl create deployment --image nginx nginx-1

# view all the deployed Pods in the active context cluster
kubectl get pods
```
> ```
> NAME                     READY   STATUS    RESTARTS   AGE
> nginx-1-f947b58f-5v8nj   1/1     Running   0          3m46s
> ```
```
# view the resource usage across the nodes of the cluster
kubectl top nodes

# view the resource usage across all the deployed Pods in the cluster
kubectl top pods

export my_nginx_pod=nginx-1-f947b58f-5v8nj
echo $my_nginx_pod
```
> ```
> nginx-1-f947b58f-5v8nj
> ```

```
kubectl describe pod $my_nginx_po
```
> ```
> Name:             nginx-1-f947b58f-5v8nj
> Namespace:        default
> Priority:         0
> Service Account:  default
> Node:             gk3-autopilot-cluster-1-pool-2-fce7608f-56zn/10.128.0.4
> Start Time:       Wed, 13 Dec 2023 10:11:17 +0000
> Labels:           app=nginx-1
>                   pod-template-hash=f947b58f
> Annotations:      <none>
> Status:           Running
> SeccompProfile:   RuntimeDefault
> IP:               10.8.0.21
> IPs:
>   IP:           10.8.0.21
> Controlled By:  ReplicaSet/nginx-1-f947b58f
> Containers:
>   nginx:
>     Container ID:   containerd://583b50325087f77896fd5fea0c950c0aa86a0b727705124fd42f0576b6c01dc8
>     Image:          nginx
>     Image ID:       docker.io/library/nginx@sha256:10d1f5b58f74683ad34eb29287e07dab1e90f10af243f151bb50aa5dbb4d62ee
>     Port:           <none>
>     Host Port:      <none>
>     State:          Running
>       Started:      Wed, 13 Dec 2023 10:12:36 +0000
>     Ready:          True
>     Restart Count:  0
>     Limits:
>       cpu:                500m
>       ephemeral-storage:  1Gi
>       memory:             2Gi
>     Requests:
>       cpu:                500m
>       ephemeral-storage:  1Gi
>       memory:             2Gi
>     Environment:          <none>
>     Mounts:
>       /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vx9fq (ro)
> Conditions:
>   Type              Status
>   Initialized       True 
>   Ready             True 
>   ContainersReady   True 
>   PodScheduled      True 
> Volumes:
>   kube-api-access-vx9fq:
>     Type:                    Projected (a volume that contains injected data from multiple sources)
>     TokenExpirationSeconds:  3607
>     ConfigMapName:           kube-root-ca.crt
>     ConfigMapOptional:       <nil>
>     DownwardAPI:             true
> QoS Class:                   Guaranteed
> Node-Selectors:              <none>
> Tolerations:                 kubernetes.io/arch=amd64:NoSchedule
>                              node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
>                              node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
> Events:
>   Type     Reason                  Age    From                                   Message
>   ----     ------                  ----   ----                                   -------
>   Warning  FailedScheduling        9m49s  gke.io/optimize-utilization-scheduler  no nodes available to schedule pods
>   Normal   TriggeredScaleUp        9m41s  cluster-autoscaler                     pod triggered scale-up: [{https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-01-a248a4c9aa49/zones/us-central1-c/instanceGroups/gk3-autopilot-cluster-1-pool-2-fce7608f-grp 0->1 (max: 1000)}]
>   Warning  FailedScheduling        8m44s  gke.io/optimize-utilization-scheduler  0/1 nodes are available: 1 node(s) had untolerated taint {node.cloudprovider.kubernetes.io/uninitialized: true}. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling..
>   Normal   Scheduled               8m20s  gke.io/optimize-utilization-scheduler  Successfully assigned default/nginx-1-f947b58f-5v8nj to gk3-autopilot-cluster-1-pool-2-fce7608f-56zn
>   Warning  FailedCreatePodSandBox  7m38s  kubelet                                Failed to create pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox "2870857bad22ba861d2fb29790b10728343c1fcba56028bbe7a7ec646ec3e4ed": plugin type="cilium-cni" failed (add): unable to connect to Cilium daemon: failed to create cilium agent client after 30.000000 seconds timeout: Get "http:///var/run/cilium/cilium.sock/v1/config": dial unix /var/run/cilium/cilium.sock: connect: no such file or directory
> Is the agent running?
>   Normal  Pulling  7m18s  kubelet  Pulling image "nginx"
>   Normal  Pulled   7m1s   kubelet  Successfully pulled image "nginx" in 15.503479931s (16.763367031s including waiting)
>   Normal  Created  7m1s   kubelet  Created container nginx
>   Normal  Started  7m1s   kubelet  Started container nginx
> ```

### Push a file into a container
```
vim ~/test.html
```
> ```
> <html> <header><title>This is title</title></header>
> <body> Hello world </body>
> </html>
> ```

```
kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html
```

### Expose the Pod for testing
Create a LoadBalancer service, which allows the nginx Pod to be accessed from internet addresses outside of the cluster
```
kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer
kubectl get services
```
> ```
> NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
> kubernetes               ClusterIP      34.118.224.1    <none>          443/TCP        34m
> nginx-1-f947b58f-5v8nj   LoadBalancer   34.118.229.16   34.122.58.116   80:30322/TCP   98s
> ```
```
curl http://34.122.58.116/test.html
```
> ```
> <html> <header><title>This is title</title></header>
> <body> Hello world </body>
> </html>
> ```

```
kubectl top pods
```

### Introspect GKE Pods

```
# prepare env
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/GKE_Shell/

cat new-nginx-pod.yaml 
```
> ```
> apiVersion: v1
> kind: Pod
> metadata:
>   name: new-nginx
>   labels:
>     name: new-nginx
> spec:
>   containers:
>   - name: new-nginx
>     image: nginx
>     ports:
>     - containerPort: 80
> ```

```
# deploy new manifest
kubectl apply -f ./new-nginx-pod.yaml

kubectl get pods
```
> ```
> NAME                     READY   STATUS    RESTARTS   AGE
> new-nginx                1/1     Running   0          69s
> nginx-1-f947b58f-5v8nj   1/1     Running   0          26m
> ```

```
# connect to the pod - almost all like docker cli
# !!! double dash separates the arguments you want to pass to the command from the kubectl arguments
kubectl exec -it new-nginx -- /bin/bash
```
> ```
> # inside container
> apt-get update
> apt-get install vim
> vim /usr/share/nginx/html/test.html
> ```
> > ```
> > <html> <header><title>This is title</title></header>
> > <body> Hello world from new-nginx</body>
> > </html>
> > ```
> ```
> exit
> ```

#### set up port forwarding from port 10081 of the Cloud Shell VM to port 80 of the nginx container
```
kubectl port-forward new-nginx 10081:80 &
```
> ```
> Forwarding from 127.0.0.1:10081 -> 80
> Handling connection for 10081
> ```
#### test availability
```
curl http://127.0.0.1:10081/test.html
```
> ```
> <html> <header><title>This is title</title></header>
> <body> Hello worl from new-nginx </body>
> </html>
> ```

#### check pod logs
!!! open new cloud shell terminal
```
kubectl logs new-nginx -f --timestamps
```
> ```
> ..
> 2023-12-13T11:12:16.697141140Z 2023/12/13 11:12:16 [notice] 1#1: start worker process 31
> 2023-12-13T11:16:21.433500554Z 127.0.0.1 - - [13/Dec/2023:11:16:21 +0000] "GET /test.html HTTP/1.1" 200 103 "-" "curl/7.74.0" "-"
> ..
> .. here we call test.html as before
> ..
> 2023-12-13T11:22:24.801809381Z 127.0.0.1 - - [13/Dec/2023:11:22:24 +0000] "GET /test.html HTTP/1.1" 200 103 "-" "curl/7.74.0" "-"
> ```




