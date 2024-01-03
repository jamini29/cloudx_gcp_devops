# docker_lab_notes


```
docker run hello-world

docker images

docker ps

docker ps -a

```

### build a Docker image

Create a Dockerfile:
```
mkdir test && cd test

cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image
FROM node:lts

# Set the working directory in the container to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Make the container's port 80 available to the outside world
EXPOSE 80

# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
```

Create the node application:
```
cat > app.js <<EOF
const http = require('http');

const hostname = '0.0.0.0';
const port = 80;

const server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World\n');
});

server.listen(port, hostname, () => {
    console.log('Server running at http://%s:%s/', hostname, port);
});

process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
EOF
```

build the image.
```
docker build -t node-app:0.1 .
```
check
```
docker images
```

### run containers based on the image built:

run
```
docker run -p 4000:80 --name my-app node-app:0.1
```

check in another terminal
```
curl http://localhost:4000
```

stop running (in another terminal)
```
docker stop my-app && docker rm my-app
```

start container in background
```
docker run -p 4000:80 --name my-app -d node-app:0.1

docker ps
```

check logs
```
docker logs 868aea8c545f
```

#### modify the application

change in app.js "Hello World" to "Welcome to Cloud"

build with new tag, run, check
```
docker build -t node-app:0.2 .
docker run -p 8080:80 --name my-app-2 -d node-app:0.2
docker ps

curl http://localhost:8080
```

check logs and inspect
```
# -f - follow
docker logs -f 147a028e122a

docker inspect 147a028e122a
```

### Publish

!!! To push images to your private registry hosted by Artifact Registry, you need to tag the images with a registry name.
The format is `<regional-repository>-docker.pkg.dev/my-project/my-repo/my-image`.

#### Create the target Docker repository

Configure authentication
```
gcloud auth configure-docker us-west1-docker.pkg.dev
```

Create an Artifact Registry repository
```
gcloud artifacts repositories create my-repository --repository-format=docker --location=us-west1 --description="Docker repository"
```

#### Push the container to Artifact Registry

tag node-app:0.2
```
docker build -t us-west1-docker.pkg.dev/qwiklabs-gcp-01-245863ca0620/my-repository/node-app:0.2 .
```

Check
```
docker images
```

Push this image to Artifact Registry.
```
docker push us-west1-docker.pkg.dev/qwiklabs-gcp-01-245863ca0620/my-repository/node-app:0.2
```




### Test image

stop and remove all containers
```
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
```
remove all of the Docker images
```
docker rmi us-west1-docker.pkg.dev/qwiklabs-gcp-01-245863ca0620/my-repository/node-app:0.2
docker rmi node:lts
docker rmi -f $(docker images -aq) # remove remaining images
docker images
```

Pull the image and run it.
```
docker run -p 4000:80 -d us-west1-docker.pkg.dev/qwiklabs-gcp-01-245863ca0620/my-repository/node-app:0.2
```

Test running container
```
curl http://localhost:4000
```

