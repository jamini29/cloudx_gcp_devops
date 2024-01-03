
## Working with Cloud Build

```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project

# enable Cloud Build and Artifact Registry APIs

```

### Building containers with DockerFile and Cloud Build

```
vim quickstart.sh
cat quickstart.sh
##!/bin/sh
#echo "Hello, world! The time is $(date)."

vim Dockerfile
cat Dockerfile
#FROM alpine
#COPY quickstart.sh /
#CMD ["/quickstart.sh"]

chmod +x quickstart.sh

# create a new Docker repository named quickstart-docker-repo in the location us-east1 with the description "Docker repository"
gcloud artifacts repositories create quickstart-docker-repo --repository-format=docker     --location=us-east1 --description="Docker repository"

# to build the Docker container image in Cloud Build
gcloud builds submit --tag us-east1-docker.pkg.dev/${DEVSHELL_PROJECT_ID}/quickstart-docker-repo/quickstart-image:tag1

# to clone the repository to the lab Cloud Shell
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/Cloud_Build/a
export REGION=us-east1
sed -i "s/YourRegionHere/$REGION/g" cloudbuild.yaml
cat cloudbuild.yaml
#steps:
#- name: 'gcr.io/cloud-builders/docker'
#  args: [ 'build', '-t', 'us-east1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1', '.' ]
#images:
#- 'us-east1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1'

# to start a Cloud Build using cloudbuild.yaml as the build configuration file
gcloud builds submit --config cloudbuild.yaml



cd ~/ak8s/Cloud_Build/b
export REGION=us-east1
sed -i "s/YourRegionHere/$REGION/g" cloudbuild.yaml
cat cloudbuild.yaml
#steps:
#- name: 'gcr.io/cloud-builders/docker'
#  args: [ 'build', '-t', 'us-east1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1', '.' ]
#- name: 'gcr.io/$PROJECT_ID/quickstart-image'
#  args: ['fail']
#images:
#- 'us-east1-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1'

# the quickstart.sh script has been modified so that it simulates a test failure when an argument ['fail'] is passed to it
gcloud builds submit --config cloudbuild.yaml
#ERROR: (gcloud.builds.submit) build dd784a31-ce00-44e0-809c-92aae8875bd9 completed with status "FAILURE"
echo $?
#1
```
