# cli notes

### disable accessibility features

on my system, after logging to GCP console, list commands formats output as bunch of lists intead of table - to fix need to disable accessibility features <https://cloud.google.com/sdk/docs/enabling-accessibility-features>

`gcloud config set accessibility/screen_reader false`



```
gcloud config set accessibility/screen_reader false
gcloud auth list
gcloud config list project
```

```
source <(kubectl completion bash)
```

```
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-c
export REGION=$(gcloud config get compute/region)
export ZONE=$(gcloud config get compute/zone)
echo $REGION $ZONE
```
```
gsutil mb gs://qwiklabs-gcp-01-3a8c7b56f417
```

misc history from cloud storage lab:
```
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
gsutil cp ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417
gsutil cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
gsutil cp gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417/image-folder/
gcloud auth list
gcloud config list project
gcloud config set compute/region us-central1
gsutil mb gs://qwiklabs-gcp-01-3a8c7b56f417
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
gsutil cp ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417
rm ada.jpg
gsutil cp -r gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg .
gsutil cp gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417/image-folder/
gsutil ls gs://qwiklabs-gcp-01-3a8c7b56f417/
gsutil ls -l gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
gsutil acl ch -u AllUsers:R gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
```
