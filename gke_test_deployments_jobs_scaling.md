# Deployments, Jobs and Scaling


### After a Deployment has been created and its component Pods are running, which component is responsible for ensuring that a replacement Pod is launched whenever a Pod fails or is evicted?

	ReplicaSet

### You have configured a Kubernetes Job with a backofflimit of 4 and a completions count of 8. If the Pods launched by the Job continually fail, how long does it take for four failures to happen and what does the Job report?

	80 seconds, Job fails with BackoffLimitExceeded as the reason.

### You have made a number of changes to your deployment and applied those changes. Which command should you use to rollback the environment to the deployment identified in the deployment history as revision 2?

	Run 'kubectl rollout undo deployment --to-revision=2'.

### You are configuring a Job to process the conversion of a sample of a large number of video files from one format to another. Which parameter should you configure to ensure that you stop processing once a sufficient quantity have been processed?

	completions=4

### When specifying Inter-pod affinity rules, you need to specify an affinity rule at the zone level, not at the individual Node level. Which additional parameter in the Pod manifest YAML must you set to apply this override?

	topologyKey: failure-domain.beta.kubernetes.io/zone

### What status or event is used by the GKE autoscaler to decide when scaleout is required and a new node needs to be added?

	When the scheduler cannot schedule a Pod due to resource constraints and the Pod has been marked as unschedulable.

### With a Kubernetes Job configured with a parallelism value of 3 and no completion count what happens to the status of the Job when one of the Pods successfully terminates?

	The entire Job is considered complete and the remaining Pods are shut down.

### How do you configure a Kubernetes Job so that Pods are retained after completion?

	Configure the cascade flag for the Job with a value of false.

### You have autoscaling enabled on your cluster. What conditions are required for the autoscaler to decide to delete a node?

	If a node is underutilized and running Pods can be run on other Nodes.

### A parallel Kubernetes Job is configured with parallelism of property of 4 and a completion property of 9. How many Pods are kept in a running state by the Job controller immediately after the sixth successful completion?

	3

### You are configuring the rollout strategy for your Deployment that contains 8 Pods. You need to specify a Deployment property that will ensure at least 75% of the desired number of Pods is always running at the same time. What property and value should you set for the deployment to ensure that this is the case?

	maxUnavailable=25%

### You are resolving a range of issues with a Deployment and need to make a large number of changes. Which command can you execute to group these changes into a single rollout, thus avoiding pushing out a large number of rollouts?

	kubectl rollout pause deployment
