# Google Kubernetes Engine Networking

### Your Pod has been rescheduled and the original IP address that was assigned to the Pod is no longer accessible. What is the reason for this?

	The new Pod has received a different IP address.

### What change can an administrator make to achieve the lowest possible latency when using a Google Cloud load-balancer service to balance network traffic, minimizing double-hop traffic between nodes? Assume that container-native load balancing is not in use.

	Set the externalTrafficPolicy field to local in the YAML manifest.

### You have updated your application and deployed a new Pod. What step can you take to ensure that you can access the Pod and and the Pod's application throughout the lifecycle of the Pod using a durable IP address?

	Deploy a Kubernetes Service with a selector that locates the application's Pods.

### You are designing a GKE solution. One of your requirements is that network traffic load balancing should be directed to Pods directly, instead of balanced across nodes. What change can you make to your environment?

	Configure or migrate your cluster to VPC-Native Mode and deploy a Container- native load balancer.

### You need to apply a network policy to five Pods to block ingress traffic from other Pods. Each Pod has a label of app:demo-app. In your network policy manifest, you have specified the label app:demo-app in spec.podSelector. The policy is configured and when you list the Network Policies on your cluster you can see the policy listed but is not having any effect as you can still ping the Pods from other Pods in the cluster. What is the problem and what action can you take to correct this?

	You have not enabled network policies on the cluster. Enable network policies on the cluster, and recreate all of the nodes.

### During testing you cannot find the Google Cloud Load Balancer that should have been configured for your application. You check the manifest for the application and notice that the application's front-end Service type is ClusterIP. How can you correct this?

	Define spec.type as LoadBalancer in the YAML manifest for the service and redeploy it.
