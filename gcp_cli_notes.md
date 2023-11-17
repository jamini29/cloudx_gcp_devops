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
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-c
export REGION=$(gcloud config get compute/region)
export ZONE=$(gcloud config get compute/zone)
echo $REGION $ZONE
```
```
gsutil mb gs://qwiklabs-gcp-01-3a8c7b56f417
```
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
gsutil cp ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417
gsutil cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
gsutil cp gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417/image-folder/
gcloud auth list
    3  gcloud config list project
    4  gcloud config set compute/region us-central1
    5  gsutil mb gs://qwiklabs-gcp-01-3a8c7b56f417
    6  curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
    7  gsutil cp ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417
    8  rm ada.jpg
    9  gsutil cp -r gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg .
   10  gsutil cp gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417/image-folder/
   11  gsutil ls gs://qwiklabs-gcp-01-3a8c7b56f417/
   12  gsutil ls -l gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
   13  gsutil acl ch -u AllUsers:R gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
   14  history
gcloud auth list
    3  gcloud config list project
    4  gcloud config set compute/region us-central1
    5  gsutil mb gs://qwiklabs-gcp-01-3a8c7b56f417
    6  curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
    7  gsutil cp ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417
    8  rm ada.jpg
    9  gsutil cp -r gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg .
   10  gsutil cp gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg gs://qwiklabs-gcp-01-3a8c7b56f417/image-folder/
   11  gsutil ls gs://qwiklabs-gcp-01-3a8c7b56f417/
   12  gsutil ls -l gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
   13  gsutil acl ch -u AllUsers:R gs://qwiklabs-gcp-01-3a8c7b56f417/ada.jpg
   14  history
