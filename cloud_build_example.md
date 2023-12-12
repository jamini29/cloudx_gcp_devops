
# Working with Cloud Build

in `APIs & Services` check that `Cloud Build` and `Artifact Registry` APIs enabled.

## Building containers with DockerFile and Cloud Build

```
vim quickstart.sh
```
> ```
> #!/bin/sh
> echo "Hello, world! The time is $(date)."
> ```

```
vim Dockerfile
```
> ```
> FROM alpine
> COPY quickstart.sh /
> CMD ["/quickstart.sh"]
> ```

chmod +x quickstart.sh

Create a new Docker repository named quickstart-docker-repo in the location us-east4 with the description "Docker repository"
```
gcloud artifacts repositories create quickstart-docker-repo --repository-format=docker \
    --location=us-east4 --description="Docker repository"
```
build the Docker container image in Cloud Build
```
gcloud builds submit --tag us-east4-docker.pkg.dev/${DEVSHELL_PROJECT_ID}/quickstart-docker-repo/quickstart-image:tag1

gcloud artifacts repositories list --location=us-east4
gcloud artifacts docker images list us-east4-docker.pkg.dev/${DEVSHELL_PROJECT_ID}/quickstart-docker-repo
```

## Building containers with a build configuration file and Cloud Build

clone the repository to the lab Cloud Shell
```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/Cloud_Build/a
```

```
export REGION=us-east4
sed -i "s/YourRegionHere/$REGION/g" cloudbuild.yaml
cat cloudbuild.yaml 
```
> ```
> steps:
> - name: 'gcr.io/cloud-builders/docker'
>   args: [ 'build', '-t', 'us-east4-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1', '.' ]
> images:
> - 'us-east4-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1'
> ```

start a Cloud Build using cloudbuild.yaml as the build configuration file
```
gcloud builds submit --config cloudbuild.yaml
gcloud artifacts docker images list us-east4-docker.pkg.dev/${DEVSHELL_PROJECT_ID}/quickstart-docker-repo
```
> ```
> Listing items under project qwiklabs-gcp-01-d80f707f6dc8, location us-east4, repository quickstart-docker-repo.
> 
> IMAGE                                                                                         DIGEST                                                                   CREATE_TIME          UPDATE_TIME
> us-east4-docker.pkg.dev/qwiklabs-gcp-01-d80f707f6dc8/quickstart-docker-repo/quickstart-image  sha256:1005c79a2e7706b5749913c6e6406c311545ea6fea4a7d881a7c9077f97205c0  2023-12-12T16:21:09  2023-12-12T16:21:09
> us-east4-docker.pkg.dev/qwiklabs-gcp-01-d80f707f6dc8/quickstart-docker-repo/quickstart-image  sha256:56033b21414c604ca89573c755f873bc716d7774bdd8d8f77759c8e1e67f9e12  2023-12-12T16:04:30  2023-12-12T16:04:30
> ```

## Building and testing containers with a build configuration file and Cloud Build

```
cd ~/ak8s/Cloud_Build/b

export REGION=us-east4
sed -i "s/YourRegionHere/$REGION/g" cloudbuild.yaml
cat cloudbuild.yaml
```
> ```
> steps:
> - name: 'gcr.io/cloud-builders/docker'
>   args: [ 'build', '-t', 'us-east4-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1', '.' ]
> - name: 'gcr.io/$PROJECT_ID/quickstart-image'
>   args: ['fail']
> images:
> - 'us-east4-docker.pkg.dev/$PROJECT_ID/quickstart-docker-repo/quickstart-image:tag1'
> ```

```
gcloud builds submit --config cloudbuild.yaml
echo $?
```
> ```
> 1
> ```

---

# Deploying GKE Autopilot Clusters from Cloud Shell

```
export my_region=us-central1
export my_cluster=autopilot-cluster-1

gcloud container clusters create-auto $my_cluster --region $my_region
gcloud container clusters get-credentials $my_cluster --region $my_region

