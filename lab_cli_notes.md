

## create a VPC network and firewall rules

```
gcloud compute networks create NETWORK_NAME --project=PROJECT_ID --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional
gcloud compute networks subnets create SUBNET_NAME --project=PROJECT_ID --range=10.130.0.0/20 --stack-type=IPV4_ONLY --network=privatenet --region=us-central1
```
allow ssh from IAP <https://cloud.google.com/iap/docs/using-tcp-forwarding>
```
gcloud compute --project=PROJECT_ID firewall-rules create RULE_NAME_ALLOW_4IAP_SSH --direction=INGRESS --priority=1000 --network=NETWORK_NAME --action=ALLOW --rules=tcp:22 --source-ranges=35.235.240.0/20
```
allow ssh,rdp,icmp from all
```
gcloud compute --project=PROJECT_ID firewall-rules create RULE_NAME_ALLOW_4ALL_ICMP_SSH_RDP --direction=INGRESS --priority=1000 --network=NETWORK_NAME --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0
```

## create the VM instance interal/external IPs

```
gcloud compute instances create managementnet-us-vm --project=PROJECT_ID --zone=us-east1-c --machine-type=e2-micro --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=managementsubnet-us --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=861727198879-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --create-disk=auto-delete=yes,boot=yes,device-name=managementnet-us-vm,image=projects/debian-cloud/global/images/debian-11-bullseye-v20231010,mode=rw,size=10,type=projects/qwiklabs-gcp-03-696399c996aa/zones/us-east1-c/diskTypes/pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --labels=goog-ec-src=vm_add-gcloud --reservation-affinity=any
```

## create the VM instance with no public IP address

```
gcloud compute instances create VM_NAME --project=PROJECT_ID --zone=us-central1-c --machine-type=e2-medium --network-interface=stack-type=IPV4_ONLY,subnet=SUBNET_NAME,no-address --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=618567071006-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --create-disk=auto-delete=yes,boot=yes,device-name=vm-internal,image=projects/debian-cloud/global/images/debian-11-bullseye-v20231010,mode=rw,size=10,type=projects/qwiklabs-gcp-00-09386eccca80/zones/us-central1-a/diskTypes/pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --labels=goog-ec-src=vm_add-gcloud --reservation-affinity=any
```
ssh login to the VM through IAP
```
gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap
```

## enable/disable private google access
<https://cloud.google.com/sdk/gcloud/reference/compute/networks/subnets/update>
```
gcloud compute networks subnets update SUBNET_NAME --[no-]enable-private-ip-google-access
```



## misc

#### copy to bucket
`gcloud storage cp gs://cloud-training/gcpnet/private/access.svg gs://$MY_BUCKET`

#### copy from bucket
`gcloud storage cp gs://$MY_BUCKET/*.svg .`

#### configure project ID
`gcloud config set project PROJECT_ID`

#### check default route
`gcloud compute routes list --filter="default-internet-gateway NETWORK_NAME"`


---


#### Configuring VPC Firewalls
<https://partner.cloudskillsboost.google/course_sessions/6628549/labs/427372>


#### IDS
<https://partner.cloudskillsboost.google/course_sessions/6628549/labs/427386>





<https://brainly.in/question/14949411>

