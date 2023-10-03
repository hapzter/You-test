# Yousician-test
# Build Jenkins dotnet image
```
docker build -t jenkins-dotnet:2.414.2 .
```
# Create the Docker network 'jenkins'
```
docker network create jenkins
```
# Run the Container
```
docker run --name jenkins-dotnet-server --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins-dotnet:2.414.2
```
# Get the initial Jenkins Password
```
docker exec jenkins-dotnet-server cat /var/jenkins_home/secrets/initialAdminPassword
```
# Create a socat container
Since I run Jenkins as a container I have to create another one to mediate between Docker host and Jenkins container. Basicly I want to get docker IP and its tcp port to configure the Docker Cloud plugin.
```
docker pull alpine/socat
```
```
docker run -d --restart=always \
    -p 127.0.0.1:2376:2375 \
    --network jenkins \
    -v /var/run/docker.sock:/var/run/docker.sock \
    alpine/socat \
    tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
```
# Get socat container name
```
docker ps --format '{{.Image}} --->>> {{.Names}}' | grep socat
```
->>> heuristic_ganguly
# Inspect socat container to get the IPAddress
```
docker inspect   -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' heuristic_ganguly
```
->>> 172.18.0.3
# Jenkins agent image with dotnet7-sdk and dotnet7-runtime
I created a new docker image from docker hub jenkins/agent, but with dotnet sdk and runtime.
```
FROM jenkins/agent:alpine-jdk17
USER root
RUN  apk update && apk add dotnet7-sdk dotnet7-runtime
```
# At this point it is possible to configure Docker Cloud at Jenkins
Docker Host URI:
```
tcp://172.18.0.3:2375
```
Docker Image
```
dzhap/myjenkins-agent:dotnet
```
Remote File System Root
```
/home/jenkins
```
Instance Capacity
```
3
```
