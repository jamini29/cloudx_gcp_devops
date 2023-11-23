





## geo dns sample

### init
```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project

# enable APIs
gcloud services enable compute.googleapis.com
gcloud services enable dns.googleapis.com

gcloud services list | grep -E 'compute|dns'
```

### deploy env
```
# allow ssh to client vms from iap
gcloud compute firewall-rules create fw-default-iapproxy --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:22,icmp --source-ranges=35.235.240.0/20

# allow http to web servers
gcloud compute firewall-rules create allow-http-traffic --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

# create instance in us
gcloud compute instances create us-client-vm --machine-type=e2-micro --zone us-west1-c
# create instance in europe
gcloud compute instances create europe-client-vm --machine-type=e2-micro --zone "europe-central2-b"
# create instance in asia
gcloud compute instances create asia-client-vm --machine-type=e2-micro --zone asia-south1-a

gcloud compute instances list

# create/launch web server in us
gcloud compute instances create us-web-vm --machine-type=e2-micro --zone us-west1-c --network=default --subnet=default --tags=http-server --metadata=startup-script='#! /bin/bash
 apt-get update
 apt-get install apache2 -y
 echo "Page served from: us-west1" | \
 tee /var/www/html/index.html
 systemctl restart apache2'

# create/launch web server in europe
gcloud compute instances create europe-web-vm --machine-type=e2-micro --zone=europe-central2-b --network=default --subnet=default --tags=http-server --metadata=startup-script='#! /bin/bash
 apt-get update
 apt-get install apache2 -y
 echo "Page served from: europe-central2" | \
 tee /var/www/html/index.html
 systemctl restart apache2'

# find server IPs and put into vars
export US_WEB_IP=$(gcloud compute instances describe us-web-vm --zone=us-west1-c --format="value(networkInterfaces.networkIP)")
export EUROPE_WEB_IP=$(gcloud compute instances describe europe-web-vm --zone=europe-central2-b --format="value(networkInterfaces.networkIP)")

# create private dns zone
gcloud dns managed-zones create example --description=test --dns-name=example.com --networks=default --visibility=private

# create routing policy
gcloud dns record-sets create geo.example.com --ttl=5 --type=A --zone=example --routing-policy-type=GEO --routing-policy-data="us-west1=$US_WEB_IP;europe-central2=$EUROPE_WEB_IP"

gcloud dns record-sets list --zone=example
```

### test us
```
gcloud compute ssh europe-client-vm --zone europe-central2-b --tunnel-through-iap
```
> ```
> student-00-fad144582c2a@europe-client-vm:~$ for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done
> 1
> Page served from: europe-central2
> ...
> student-00-fad144582c2a@europe-client-vm:~$ exit
> ```

### test europe
```
gcloud compute ssh us-client-vm --zone us-west1-c --tunnel-through-iap
```
> ```
> student-00-fad144582c2a@us-client-vm:~$ for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done
> 1
> Page served from: us-west1
> ...
> student-00-fad144582c2a@us-client-vm:~$ exit
> ```

### test asia
```
gcloud compute ssh asia-client-vm --zone asia-south1-a --tunnel-through-iap
```
> ```
> student-00-fad144582c2a@asia-client-vm:~$ for i in {1..10}; do echo $i; curl geo.example.com; sleep 6; done
> 1
> Page served from: us-west1
> ...
> student-00-fad144582c2a@asia-client-vm:~$ exit
> ```

### clean env
```
#delete VMS
gcloud compute instances delete -q us-client-vm --zone us-west1-c
gcloud compute instances delete -q us-web-vm --zone us-west1-c
gcloud compute instances delete -q europe-client-vm --zone europe-central2-b
gcloud compute instances delete -q europe-web-vm --zone europe-central2-b
gcloud compute instances delete -q asia-client-vm --zone asia-south1-a

#delete FW rules
gcloud compute firewall-rules delete -q allow-http-traffic
gcloud compute firewall-rules delete fw-default-iapproxy

#delete record set
gcloud dns record-sets delete geo.example.com --type=A --zone=example

#delete private zone
gcloud dns managed-zones delete example
```


### P.S. select zone in asia
```
gcloud compute zones list | grep -iE 'NAME.*asia' | sed 's/NAME: //i' | sort
```
