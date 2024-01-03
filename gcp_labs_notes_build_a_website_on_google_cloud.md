# 

#### how to reauthenticate GKE
```
gcloud config set compute/zone us-central1-f
gcloud container clusters get-credentials fancy-cluster
```


## Deploy Your Website on Cloud Run

<https://partner.cloudskillsboost.google/course_sessions/7119517/labs/403972>

### Env

```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project
```

### Clone the source repository

```
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
cd ~/monolith-to-microservices/monolith
npm start
```

### Create a Docker container with Cloud Build

#### create a Docker container with Cloud Build

create docker repo `monolith-demo` in `us-east4`

```
configure authentication
```

#### deploy the image

```
gcloud auth configure-docker 
gcloud services enable artifactregistry.googleapis.com     cloudbuild.googleapis.com     run.googleapis.com
gcloud builds submit --tag us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:1.0.0
```

### Deploy the container to Cloud Run

```
gcloud run deploy monolith --image us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:1.0.0 --region us-east4
```

verify

```
gcloud run services list
```

### Create new revision with lower concurrency

gcloud run deploy monolith --image us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:1.0.0 --region us-east4 --concurrency 1

gcloud run deploy monolith --image us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:1.0.0 --region us-east4 --concurrency 80


### Make changes to the website

```
cd ~/monolith-to-microservices/react-app/src/pages/Home
mv index.js.new index.js
```

check
```
cat ~/monolith-to-microservices/react-app/src/pages/Home/index.js
```

build and copy
```
cd ~/monolith-to-microservices/react-app
npm run build:monolith
```

trigger updated cloudbuild
```
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:2.0.0
```

### Update website with zero downtime

```
gcloud run deploy monolith --image us-east4-docker.pkg.dev/${GOOGLE_CLOUD_PROJECT}/monolith-demo/monolith:2.0.0 --region us-east4

gcloud run services describe monolith --platform managed --region us-east4

gcloud beta run services list
```





## Hosting a Web App on Google Cloud Using Compute Engine

### env

```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project
gcloud config set compute/zone "us-west1-c"
export ZONE=$(gcloud config get compute/zone)
gcloud config set compute/region "us-west1"
export REGION=$(gcloud config get compute/region)
history
```

### Enable Compute Engine API

```
gcloud services enable compute.googleapis.com
```

### Create Cloud Storage bucket

```
gsutil mb gs://fancy-store-$DEVSHELL_PROJECT_ID
```

### Clone source repository

```
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
nvm install --lts
cd microservices
npm start
```

### Create Compute Engine instances

#### Create the startup script

touch ~/monolith-to-microservices/startup-script.sh

add

```
#!/bin/bash

# Install logging monitor. The monitor will automatically pick up logs sent to
# syslog.
curl -s "https://storage.googleapis.com/signals-agents/logging/google-fluentd-install.sh" | bash
service google-fluentd restart &

# Install dependencies from apt
apt-get update
apt-get install -yq ca-certificates git build-essential supervisor psmisc

# Install nodejs
mkdir /opt/nodejs
curl https://nodejs.org/dist/v16.14.0/node-v16.14.0-linux-x64.tar.gz | tar xvzf - -C /opt/nodejs --strip-components=1
ln -s /opt/nodejs/bin/node /usr/bin/node
ln -s /opt/nodejs/bin/npm /usr/bin/npm

# Get the application source code from the Google Cloud Storage bucket.
mkdir /fancy-store
gsutil -m cp -r gs://fancy-store-qwiklabs-gcp-00-fbc164205f82/monolith-to-microservices/microservices/* /fancy-store/

# Install app dependencies.
cd /fancy-store/
npm install

# Create a nodeapp user. The application will run as this user.
useradd -m -d /home/nodeapp nodeapp
chown -R nodeapp:nodeapp /opt/app

# Configure supervisor to run the node app.
cat >/etc/supervisor/conf.d/node-app.conf << EOF
[program:nodeapp]
directory=/fancy-store
command=npm start
autostart=true
autorestart=true
user=nodeapp
environment=HOME="/home/nodeapp",USER="nodeapp",NODE_ENV="production"
stdout_logfile=syslog
stderr_logfile=syslog
EOF

supervisorctl reread
supervisorctl update
```

```
    9  gcloud services enable compute.googleapis.com
   10  gsutil mb gs://fancy-store-$DEVSHELL_PROJECT_ID
   11  git clone https://github.com/googlecodelabs/monolith-to-microservices.git
   12  cd ~/monolith-to-microservices
   13  ./setup.sh
   14  nvm install --lts
   15  cd microservices
   16  npm start
   17  touch ~/monolith-to-microservices/startup-script.sh
   18  vim ~/monolith-to-microservices/startup-script.sh
   19  file ~/monolith-to-microservices/startup-script.sh
   20  gsutil cp ~/monolith-to-microservices/startup-script.sh gs://fancy-store-$DEVSHELL_PROJECT_ID
   21  cd ~
   22  rm -rf monolith-to-microservices/*/node_modules
   23  gsutil -m cp -r monolith-to-microservices gs://fancy-store-$DEVSHELL_PROJECT_ID/
   24  history
```

Deploy the backend instance

```
gcloud compute instances create backend \
    --zone=$ZONE \
    --machine-type=e2-standard-2 \
    --tags=backend \
   --metadata=startup-script-url=https://storage.googleapis.com/fancy-store-$DEVSHELL_PROJECT_ID/startup-script.sh
#Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-fbc164205f82/zones/us-west1-c/instances/backend].
#NAME     ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
#backend  us-west1-c  e2-standard-2               10.138.0.2   35.197.16.224  RUNNING

gcloud compute instances list
#NAME     ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
#backend  us-west1-c  e2-standard-2               10.138.0.2   35.197.16.224  RUNNING
```

Configure a connection to the backend 35.197.16.224

```
vim monolith-to-microservices/react-app/.env                                                                                                                                
cat monolith-to-microservices/react-app/.env
#REACT_APP_ORDERS_URL=http://35.197.16.224:8081/api/orders
#REACT_APP_PRODUCTS_URL=http://35.197.16.224:8082/api/products

cd ~/monolith-to-microservices/react-app
npm install && npm run-script build

cd ~
rm -rf monolith-to-microservices/*/node_modules
gsutil -m cp -r monolith-to-microservices gs://fancy-store-$DEVSHELL_PROJECT_ID/
```

Deploy the frontend instance

```
gcloud compute instances create frontend \
    --zone=$ZONE \
    --machine-type=e2-standard-2 \
    --tags=frontend \
    --metadata=startup-script-url=https://storage.googleapis.com/fancy-store-$DEVSHELL_PROJECT_ID/startup-script.sh

```

Configure the network

```
gcloud compute firewall-rules create fw-fe \
    --allow tcp:8080 \
    --target-tags=frontend

gcloud compute firewall-rules create fw-be \
    --allow tcp:8081-8082 \
    --target-tags=backend

gcloud compute instances list
#NAME      ZONE        MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
#backend   us-west1-c  e2-standard-2               10.138.0.2   35.197.16.224  RUNNING
#frontend  us-west1-c  e2-standard-2               10.138.0.3   34.127.47.108  RUNNING
```
http://34.127.47.108:8080/
http://34.127.47.108:8080/products
http://34.127.47.108:8080/orders


### Create managed instance groups

#### Create instance template from source instance

```
gcloud compute instances stop frontend --zone=$ZONE
gcloud compute instances stop backend --zone=$ZONE

gcloud compute instance-templates create fancy-fe \
    --source-instance-zone=$ZONE \
    --source-instance=frontend
gcloud compute instance-templates create fancy-be \
    --source-instance-zone=$ZONE \
    --source-instance=backend

gcloud compute instance-templates list
#NAME      MACHINE_TYPE   PREEMPTIBLE  CREATION_TIMESTAMP
#fancy-be  e2-standard-2               2023-12-28T08:00:36.262-08:00
#fancy-fe  e2-standard-2               2023-12-28T08:00:22.012-08:00

# not needed now
gcloud compute instances delete backend --zone=$ZONE
```


Create managed instance group

```
gcloud compute instance-groups managed create fancy-fe-mig \
    --zone=$ZONE \
    --base-instance-name fancy-fe \
    --size 2 \
    --template fancy-fe
#Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-fbc164205f82/zones/us-west1-c/instanceGroupManagers/fancy-fe-mig].
#NAME          LOCATION    SCOPE  BASE_INSTANCE_NAME  SIZE  TARGET_SIZE  INSTANCE_TEMPLATE  AUTOSCALED
#fancy-fe-mig  us-west1-c  zone   fancy-fe            0     2            fancy-fe           no

gcloud compute instance-groups managed create fancy-be-mig \
    --zone=$ZONE \
    --base-instance-name fancy-be \
    --size 2 \
    --template fancy-be
#Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-fbc164205f82/zones/us-west1-c/instanceGroupManagers/fancy-be-mig].
#NAME          LOCATION    SCOPE  BASE_INSTANCE_NAME  SIZE  TARGET_SIZE  INSTANCE_TEMPLATE  AUTOSCALED
#fancy-be-mig  us-west1-c  zone   fancy-be            0     2            fancy-be           no

```

for application, the frontend microservice runs on port 8080, and the backend microservice runs on port 8081 for orders and port 8082 for products
```
gcloud compute instance-groups set-named-ports fancy-fe-mig \
    --zone=$ZONE \
    --named-ports frontend:8080

gcloud compute instance-groups set-named-ports fancy-be-mig \
    --zone=$ZONE \
    --named-ports orders:8081,products:8082
```


Configure autohealing

```
gcloud compute health-checks create http fancy-fe-hc \
    --port 8080 \
    --check-interval 30s \
    --healthy-threshold 1 \
    --timeout 10s \
    --unhealthy-threshold 3

gcloud compute health-checks create http fancy-be-hc \
    --port 8081 \
    --request-path=/api/orders \
    --check-interval 30s \
    --healthy-threshold 1 \
    --timeout 10s \
    --unhealthy-threshold 3

gcloud compute firewall-rules create allow-health-check \
    --allow tcp:8080-8081 \
    --source-ranges 130.211.0.0/22,35.191.0.0/16 \
    --network default


gcloud compute instance-groups managed update fancy-fe-mig \
    --zone=$ZONE \
    --health-check fancy-fe-hc \
    --initial-delay 300

gcloud compute instance-groups managed update fancy-be-mig \
    --zone=$ZONE \
    --health-check fancy-be-hc \
    --initial-delay 300

```

### Create load balancers

```
# Create health checks
gcloud compute http-health-checks create fancy-fe-frontend-hc \
  --request-path / \
  --port 8080

gcloud compute http-health-checks create fancy-be-orders-hc \
  --request-path /api/orders \
  --port 8081

gcloud compute http-health-checks create fancy-be-products-hc \
  --request-path /api/products \
  --port 8082

# Create backend services
gcloud compute backend-services create fancy-fe-frontend \
  --http-health-checks fancy-fe-frontend-hc \
  --port-name frontend \
  --global

gcloud compute backend-services create fancy-be-orders \
  --http-health-checks fancy-be-orders-hc \
  --port-name orders \
  --global

gcloud compute backend-services create fancy-be-products \
  --http-health-checks fancy-be-products-hc \
  --port-name products \
  --global

# Add the Load Balancer's backend services
gcloud compute backend-services add-backend fancy-fe-frontend \
  --instance-group-zone=$ZONE \
  --instance-group fancy-fe-mig \
  --global

gcloud compute backend-services add-backend fancy-be-orders \
  --instance-group-zone=$ZONE \
  --instance-group fancy-be-mig \
  --global

gcloud compute backend-services add-backend fancy-be-products \
  --instance-group-zone=$ZONE \
  --instance-group fancy-be-mig \
  --global

# Create a URL map
gcloud compute url-maps create fancy-map \
  --default-service fancy-fe-frontend

# Create a path matcher to allow the /api/orders and /api/products paths to route to their respective services
gcloud compute url-maps add-path-matcher fancy-map \
   --default-service fancy-fe-frontend \
   --path-matcher-name orders \
   --path-rules "/api/orders=fancy-be-orders,/api/products=fancy-be-products"

#Create the proxy which ties to the URL map
gcloud compute target-http-proxies create fancy-proxy \
  --url-map fancy-map

#Create a global forwarding rule that ties a public IP address and port to the proxy
gcloud compute forwarding-rules create fancy-http-rule \
  --global \
  --target-http-proxy fancy-proxy \
  --ports 80

```

Update the configuration

```
cd ~/monolith-to-microservices/react-app/
gcloud compute forwarding-rules list --global
#NAME             REGION  IP_ADDRESS     IP_PROTOCOL  TARGET
#fancy-http-rule          34.110.191.28  TCP          fancy-proxy

vim ~/monolith-to-microservices/react-app/.env
cat ~/monolith-to-microservices/react-app/.env
#REACT_APP_ORDERS_URL=http://34.110.191.28:8081/api/orders
#REACT_APP_PRODUCTS_URL=http://34.110.191.28:8082/api/products

cd ~/monolith-to-microservices/react-app
npm install && npm run-script build
```

Update the frontend instances

```
gcloud compute instance-groups managed rolling-action replace fancy-fe-mig \
    --zone=$ZONE \
    --max-unavailable 100%


```

Test the website

```
# run and wait until the 2 services are listed as HEALTHY.
watch -n 2 gcloud compute backend-services get-health fancy-fe-frontend --global

gcloud compute forwarding-rules list --global
#NAME             REGION  IP_ADDRESS     IP_PROTOCOL  TARGET
#fancy-http-rule          34.110.191.28  TCP          fancy-proxy
```
http://34.110.191.28/

### Scaling Compute Engine

Automatically resize by utilization

```
gcloud compute instance-groups managed set-autoscaling \
  fancy-fe-mig \
  --zone=$ZONE \
  --max-num-replicas 2 \
  --target-load-balancing-utilization 0.60

gcloud compute instance-groups managed set-autoscaling \
  fancy-be-mig \
  --zone=$ZONE \
  --max-num-replicas 2 \
  --target-load-balancing-utilization 0.60

```

Enable content delivery network

```
gcloud compute backend-services update fancy-fe-frontend \
    --enable-cdn --global

```

### Update the website

```
gcloud compute instances set-machine-type frontend \
  --zone=$ZONE \
  --machine-type e2-small

gcloud compute instance-templates create fancy-fe-new \
    --region=$REGION \
    --source-instance=frontend \
    --source-instance-zone=$ZONE

gcloud compute instance-groups managed rolling-action start-update fancy-fe-mig \
  --zone=$ZONE \
  --version template=fancy-fe-new

watch -n 2 gcloud compute instance-groups managed list-instances fancy-fe-mig \
  --zone=$ZONE
#NAME           ZONE        STATUS   HEALTH_STATE  ACTION     INSTANCE_TEMPLATE  VERSION_NAME  LAST_ERROR
#fancy-fe-15qg  us-west1-c  RUNNING  HEALTHY       NONE       fancy-fe-new
#fancy-fe-rz7k  us-west1-c  RUNNING  TIMEOUT       VERIFYING  fancy-fe-new

gcloud compute instances describe fancy-fe-15qg --zone=$ZONE | grep machineType
#machineType: https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00-fbc164205f82/zones/us-west1-c/machineTypes/e2-small

```

Make changes to the website

```
cd ~/monolith-to-microservices/react-app/src/pages/Home
mv index.js.new index.js

cat ~/monolith-to-microservices/react-app/src/pages/Home/index.js

cd ~/monolith-to-microservices/react-app
npm install && npm run-script build

cd ~
rm -rf monolith-to-microservices/*/node_modules
gsutil -m cp -r monolith-to-microservices gs://fancy-store-$DEVSHELL_PROJECT_ID/

```

Push changes with rolling replacements

```
gcloud compute instance-groups managed rolling-action replace fancy-fe-mig \
  --zone=$ZONE \
  --max-unavailable=100%

watch -n 2 gcloud compute backend-services get-health fancy-fe-frontend --global

gcloud compute forwarding-rules list --global
#NAME             REGION  IP_ADDRESS     IP_PROTOCOL  TARGET
#fancy-http-rule          34.110.191.28  TCP          fancy-proxy
```
http://34.110.191.28/


## Deploy, Scale, and Update Your Website on Google Kubernetes Engine

```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project
```

### 1. Create a GKE cluster

```
# !!! set the default zone
gcloud config set compute/zone us-central1-a

# enable the Container Registry API
gcloud services enable container.googleapis.com

# create a GKE cluster named fancy-cluster with 3 nodes
gcloud container clusters create fancy-cluster --num-nodes 3

# see the cluster's three worker VM instances
gcloud compute instances list
#NAME                                          ZONE           MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
#gke-fancy-cluster-default-pool-b95b6fea-0s7s  us-central1-a  e2-medium                  10.128.0.3   34.170.79.140  RUNNING
#gke-fancy-cluster-default-pool-b95b6fea-fwd3  us-central1-a  e2-medium                  10.128.0.5   35.239.95.87   RUNNING
#gke-fancy-cluster-default-pool-b95b6fea-vrg6  us-central1-a  e2-medium                  10.128.0.4   35.239.31.91   RUNNING
```

### 2. Clone source repository

```
cd ~
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
nvm install --lts
cd ~/monolith-to-microservices/monolith
npm start
```

### 3. Create Docker container with Cloud Build

```
# make sure the Cloud Build API enable
gcloud services enable cloudbuild.googleapis.com

# start the build process
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0 .
#..
#DONE
#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
#ID                                    CREATE_TIME                DURATION  SOURCE                                                                                                      IMAGES                                              STATUS
#1c87a6b2-b763-4a7a-8a2f-7761ba2761cd  2023-12-29T10:42:04+00:00  42S       gs://qwiklabs-gcp-00-f3c3a01c728e_cloudbuild/source/1703846521.486217-ec23c898aaf643b3aefc42c005875d69.tgz  gcr.io/qwiklabs-gcp-00-f3c3a01c728e/monolith:1.0.0  SUCCESS
```

### 4. Deploy container to GKE

```
# deploy application
kubectl create deployment monolith --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0

# verify
# - The Deployment, which is current
# - The ReplicaSet with desired pod count of 1
# - The Pod, which is running

kubectl get all
#NAME                            READY   STATUS    RESTARTS   AGE
#pod/monolith-666786ccd7-6l2jt   1/1     Running   0          33s
#
#NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
#service/kubernetes   ClusterIP   10.20.0.1    <none>        443/TCP   11m
#
#NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
#deployment.apps/monolith   1/1     1            1           33s
#
#NAME                                  DESIRED   CURRENT   READY   AGE
#replicaset.apps/monolith-666786ccd7   1         1         1       33s
#student_02_9dafa647627a@cloudshell:~/monolith-to-microservices/monoli

# OPTIONAL START !!!
# debug resources if needed
kubectl describe pod monolith
kubectl describe pod/monolith-7d8bc7bf68-2bxts
kubectl describe deployment monolith
kubectl describe deployment.apps/monolith

# detailed verify if needed:
# Show pods
kubectl get pods

# Show deployments
kubectl get deployments

# Show replica sets
kubectl get rs

# You can also combine them
kubectl get pods,deployments

kubectl delete pod/<POD_NAME>
kubectl get all
# OPTIONAL END !!!
```

### 5. Expose GKE deployment

```
# expose website to the Internet
kubectl expose deployment monolith --type=LoadBalancer --port 80 --target-port 8080

# to find out the external IP that GKE provisioned for the application - inspect the Service
kubectl get service
#NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
#kubernetes   ClusterIP      10.20.0.1     <none>           443/TCP        18m
#monolith     LoadBalancer   10.20.6.197   34.134.101.228   80:32252/TCP   80s
```

### 6. Scale GKE deployment

```
# to scale the deployment up to 3 replicas
kubectl scale deployment monolith --replicas=3

kubectl get all
#NAME                            READY   STATUS              RESTARTS   AGE
#pod/monolith-666786ccd7-6l2jt   1/1     Running             0          9m5s
#pod/monolith-666786ccd7-fhsnl   0/1     ContainerCreating   0          4s
#pod/monolith-666786ccd7-kwssl   0/1     ContainerCreating   0          4s
#
#NAME                 TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
#service/kubernetes   ClusterIP      10.20.0.1     <none>           443/TCP        19m
#service/monolith     LoadBalancer   10.20.6.197   34.134.101.228   80:32252/TCP   2m40s
#
#NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
#deployment.apps/monolith   1/3     3            1           9m6s
#
#NAME                                  DESIRED   CURRENT   READY   AGE
#replicaset.apps/monolith-666786ccd7   3         3         1       9m6s

```

### 7. Make changes to the website

```
cd ~/monolith-to-microservices/react-app/src/pages/Home
mv index.js.new index.js

cat ~/monolith-to-microservices/react-app/src/pages/Home/index.js

# to build the React app and copy it into the monolith public directory
cd ~/monolith-to-microservices/react-app
npm run build:monolith

# to trigger a new cloud build with an updated image version of 2.0.0
cd ~/monolith-to-microservices/react-app
npm run build:monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 .

```

### 8. Update website with zero downtime

```
# to tell Kubernetes that you want to update the image for your deployment to a new version
kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0

# verify
kubectl get pods

npm start
```

### 9. Cleanup

```
# delete git repository:
cd ~
rm -rf monolith-to-microservices

# delete Google Container Registry images
# Delete the container image for version 1.0.0 of the monolith
gcloud container images delete gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:1.0.0 --quiet

# Delete the container image for version 2.0.0 of the monolith
gcloud container images delete gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 --quiet


# delete Google Cloud Build artifacts from Google Cloud Storage
# The following command will take all source archives from all builds and delete them from cloud storage

# Run this command to print all sources:
# gcloud builds list | awk 'NR > 1 {print $4}'

gcloud builds list | grep 'SOURCE' | cut -d ' ' -f2 | while read line; do gsutil rm $line; done

# delete GKE Service
kubectl delete service monolith
kubectl delete deployment monolith

# delete GKE Cluster
gcloud container clusters delete fancy-cluster us-central1

```

## Migrating a Monolithic Website to Microservices on Google Kubernetes Engine

```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project
```

```
gcloud config set compute/zone us-central1-f
```

### 1. Clone the source repository

```
# to clone the git repo
cd ~
git clone https://github.com/googlecodelabs/monolith-to-microservices.git

# install the NodeJS dependencies to be able test the monolith before deploying
cd ~/monolith-to-microservices
./setup.sh
```

### 2. Create a GKE cluster

```
# to enable the Containers API - need to use Google Kubernetes Engine
gcloud services enable container.googleapis.com

# to create a GKE cluster named fancy-cluster with 3 nodes
gcloud container clusters create fancy-cluster --num-nodes 3 --machine-type=e2-standard-4

# verify
gcloud compute instances list
```

### 3. Deploy the existing monolith

```
# deploy a monolith application to the GKE cluster
cd ~/monolith-to-microservices
./deploy-monolith.sh

# to find the external IP address for the monolith application
kubectl get service monolith
#NAME       TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)        AGE
#monolith   LoadBalancer   10.20.4.170   34.28.75.139   80:30261/TCP   44s
```
http://34.28.75.139/

### 4. Migrate orders to a microservice

create new orders microservice

```
# to build the Docker container and push it to the Google Container Registry
cd ~/monolith-to-microservices/microservices/src/orders
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0 .

# to deploy the application
kubectl create deployment orders --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0

# verify
kubectl get all
#NAME                            READY   STATUS    RESTARTS   AGE
#pod/monolith-5b445bc5dd-78lxg   1/1     Running   0          4m52s
#pod/orders-6bb4c675bf-6jd7g     1/1     Running   0          33s
#
#NAME                 TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)        AGE
#service/kubernetes   ClusterIP      10.20.0.1     <none>         443/TCP        9m20s
#service/monolith     LoadBalancer   10.20.4.170   34.28.75.139   80:30261/TCP   4m51s
#
#NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
#deployment.apps/monolith   1/1     1            1           4m53s
#deployment.apps/orders     1/1     1            1           34s
#
#NAME                                  DESIRED   CURRENT   READY   AGE
#replicaset.apps/monolith-5b445bc5dd   1         1         1       4m53s
#replicaset.apps/orders-6bb4c675bf     1         1         1       34s


# to expose the website to the Internet
kubectl expose deployment orders --type=LoadBalancer --port 80 --target-port 8081

# to find out the external IP that GKE provisioned for your application
kubectl get service orders
# NAME     TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
# orders   LoadBalancer   10.20.1.77   34.41.39.41   80:31689/TCP   31s
```

reconfigure the monolith

```
cd ~/monolith-to-microservices/react-app
vim .env.monolith
cat .env.monolith
#REACT_APP_ORDERS_URL=http://34.41.39.41/api/orders
#REACT_APP_PRODUCTS_URL=/service/products

# rebuild Monolith Config Files
npm run build:monolith

# create Docker Container with Cloud Build
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0 .

# deploy Container to GKE
kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:2.0.0

# verify
``
http://34.28.75.139/orders

### 5. Migrate Products to microservice

```
# create Docker Container with Cloud Build
cd ~/monolith-to-microservices/microservices/src/products
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0 .

# deploy Container to GKE
kubectl create deployment products --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0

# expose the GKE container
kubectl expose deployment products --type=LoadBalancer --port 80 --target-port 8082

# Find the public IP of the Products services
kubectl get service products
#NAME       TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
#products   LoadBalancer   10.20.10.86   35.188.62.114   80:30775/TCP   42s
```

reconfigure the monolith

```
cd ~/monolith-to-microservices/react-app
vim .env.monolith
cat .env.monolith
#REACT_APP_ORDERS_URL=http://34.41.39.41/api/orders
#REACT_APP_PRODUCTS_URL=http://35.188.62.114/api/products

# rebuild Monolith Config Files
npm run build:monolith

# create Docker Container with Cloud Build
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:3.0.0 .

# deploy Container to GKE
kubectl set image deployment/monolith monolith=gcr.io/${GOOGLE_CLOUD_PROJECT}/monolith:3.0.0

```
http://34.28.75.139/products

### 6. Migrate frontend to microservice

create a new frontend microservice

```
# to copy the microservices URL config files to the frontend microservice codebase
cd ~/monolith-to-microservices/react-app
cp .env.monolith .env
npm run build

# create Docker Container with Google Cloud Build
cd ~/monolith-to-microservices/microservices/src/frontend
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0 .

# deploy Container to GKE
kubectl create deployment frontend --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0

# expose GKE Container
kubectl expose deployment frontend --type=LoadBalancer --port 80 --target-port 8080

# verify
kubectl get service frontend
#NAME       TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)        AGE
#frontend   LoadBalancer   10.20.10.222   34.71.17.160   80:31238/TCP   3m18s

```

Delete the monolith

```
kubectl delete deployment monolith
kubectl delete service monolith
```

Verify all

```
kubectl get services
#NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
#frontend     LoadBalancer   10.20.10.222   34.71.17.160    80:31238/TCP   4m29s
#kubernetes   ClusterIP      10.20.0.1      <none>          443/TCP        46m
#monolith     LoadBalancer   10.20.4.170    34.28.75.139    80:30261/TCP   41m
#orders       LoadBalancer   10.20.1.77     34.41.39.41     80:31689/TCP   36m
#products     LoadBalancer   10.20.10.86    35.188.62.114   80:30775/TCP   15m
```
http://34.28.75.139/
http://34.28.75.139/orders
http://34.28.75.139/products


## Build a Website on Google Cloud: Challenge Lab


```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project

export ZONE=us-east1-d
export MONOLITH_IDENTIFIER=fancy-monolith-526
export CLUSTER_NAME=fancy-production-637
export ORDERS_IDENTIFIER=fancy-orders-344
export PRODUCTS_IDENTIFIER=fancy-products-661
export FRONTEND_IDENTIFIER=fancy-frontend-576

gcloud config set compute/zone $ZONE
```

### 1. Download the monolith code and build your container

```
cd ~
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
nvm install --lts
```

```
# enable cloud build apis
gcloud services enable cloudbuild.googleapis.com

# build monolith
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/${MONOLITH_IDENTIFIER}:1.0.0 .

### push to GCR
###docker push gcr.io/${GOOGLE_CLOUD_PROJECT}/${MONOLITH_IDENTIFIER}:1.0.0

```

### 2. Create a kubernetes cluster and deploy the application

```
gcloud services enable container.googleapis.com
gcloud container clusters create ${CLUSTER_NAME} --num-nodes 3 --machine-type=e2-standard-2 --disk-size=50
gcloud container clusters list
gcloud compute instances list

gcloud container clusters get-credentials ${CLUSTER_NAME}





kubectl create deployment ${MONOLITH_IDENTIFIER} --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/${MONOLITH_IDENTIFIER}:1.0.0
kubectl expose deployment ${MONOLITH_IDENTIFIER} --type=LoadBalancer --port 80 --target-port 8080
kubectl get service

MONOLITH_EXTERNAL_IP=$(kubectl get svc ${MONOLITH_IDENTIFIER} --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}"); echo $MONOLITH_EXTERNAL_IP;

echo "http://${MONOLITH_EXTERNAL_IP}/"

```



```
cd ~/monolith-to-microservices/microservices/src/orders
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/${ORDERS_IDENTIFIER}:1.0.0 .
cd ~/monolith-to-microservices/microservices/src/products
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/${PRODUCTS_IDENTIFIER}:1.0.0 .



kubectl create deployment ${ORDERS_IDENTIFIER} --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/${ORDERS_IDENTIFIER}:1.0.0
kubectl expose deployment ${ORDERS_IDENTIFIER} --type=LoadBalancer --port 80 --target-port 8081
kubectl get service ${ORDERS_IDENTIFIER}
kubectl create deployment ${PRODUCTS_IDENTIFIER} --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/${PRODUCTS_IDENTIFIER}:1.0.0
kubectl expose deployment ${PRODUCTS_IDENTIFIER} --type=LoadBalancer --port 80 --target-port 8082
kubectl get service ${PRODUCTS_IDENTIFIER}

ORDERS_EXTERNAL_IP=$(kubectl get svc ${ORDERS_IDENTIFIER} --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}"); echo $ORDERS_EXTERNAL_IP;

PRODUCTS_EXTERNAL_IP=$(kubectl get svc ${PRODUCTS_IDENTIFIER} --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}"); echo $PRODUCTS_EXTERNAL_IP;

echo "http://${ORDERS_EXTERNAL_IP}/api/orders"
echo "http://${PRODUCTS_EXTERNAL_IP}/api/products"
#http://ORDERS_EXTERNAL_IP/api/orders
#http://PRODUCTS_EXTERNAL_IP/api/products

```


```
cd ~/monolith-to-microservices/react-app

tee > .env << EOL
REACT_APP_ORDERS_URL=http://${ORDERS_EXTERNAL_IP}/api/orders
REACT_APP_PRODUCTS_URL=http://${PRODUCTS_EXTERNAL_IP}/api/products
EOL

npm run build


cd ~/monolith-to-microservices/microservices/src/frontend
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/${FRONTEND_IDENTIFIER}:1.0.0 .

kubectl create deployment ${FRONTEND_IDENTIFIER} --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/${FRONTEND_IDENTIFIER}:1.0.0
kubectl expose deployment ${FRONTEND_IDENTIFIER} --type=LoadBalancer --port 80 --target-port 8080
kubectl get service ${FRONTEND_IDENTIFIER}
FRONTEND_EXTERNAL_IP=$(kubectl get svc ${FRONTEND_IDENTIFIER} --template="{{range .status.loadBalancer.ingress}}{{.ip}}{{end}}"); echo "http://${FRONTEND_EXTERNAL_IP}/";

echo "http://${FRONTEND_EXTERNAL_IP}/"




kubectl delete service ${MONOLITH_IDENTIFIER}
kubectl delete deployment ${MONOLITH_IDENTIFIER}


```



