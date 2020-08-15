---
marp: true
---
# Prepare a CentOS VM to run docker images from public repository.
## Setup CentOS VM
1. Look for a file with the name `Vagrantfile` in the current directory.
2. Run `vagrant up` to create a CentOS VM
3. Once the machine is up, execute `vagrant ssh` to SSH into the VM.

---
# Running the first Docker container
## Install Docker from the Default CentOS 7 Repository
1. Open a terminal session and log in to the server via SSH using `vagrant ssh`, if not done already.
2. Install the latest version of Docker using yum: `sudo yum -y install docker` or use `curl -fsSL https://get.docker.com/ | sh`.

## Set User Permissions
1. Drop down to root `sudo -i`.
2. Create a new group named docker: `groupadd docker`.
3. Add the `vagrant` user to the group: `usermod -aG docker vagrant`.
---
## Enable and Start Docker Service
1. Enable and start the Docker service: `systemctl enable --now docker`.
2. Back out of root: `logout`.
3. Log out of the `vagrant` session: `logout`.
4. Log back in : `vagrant ssh`

## Run the hello-world Container Image
1. Run the hello-world container image to verify installation: `docker run docker.io/hello-world`.

## Pull Images
1. Pull the following images into your Docker repository to prepare for the next exercise
   `docker pull 06kellyjac/nyancat`
   `docker pull jeremy646/doge`.
---
# Deploying a Static Website to the Container
1. Pull the required docker image: `docker pull spacebones/doge`
2. Start a container from the `spacebones/doge` image installed locally in the previous step, named 'treatseekers', in disconnected mode, redirecting container port 80 to host port 80. The command to accomplish this is:

`docker run -d --name treatseekers -p 80:80 spacebones/doge`

You can verify it is running after with `docker ps`
Which will tell you the container, name and port that is currently running
---
# Building Container Images
*Activity*: In this activity, you are tasked with migrating the SpaceBones official Government website from Ubuntu to CentOS 6 by preparing a CentOS 6 image found on DockerHub to run Apache. Use the website contents found in the [content-dockerquest-spacebones](https://github.com/linuxacademy/content-dockerquest-spacebones/) GitHub repository (specifically contents found under the /doge/ directory) to recreate the website on CentOS
1. After logging into your server, pull the centos:6 Docker image : `docker pull centos:6`.
2. Once the pull completes, start the Docker container in interactive mode, name it 'websetup'. : `docker run -it --name websetup centos:6 /bin/bash`
3. Update the system, then install Apache & Git.
`yum -y update`
`yum -y install httpd git`
---
4. Once installation completes, clone the content-dockerquest-spacebones repository, then copy the contents of the /doge/ subdirectory to /var/www/html.
`git clone https://github.com/linuxacademy/content-dockerquest-spacebones`
`cp content-dockerquest-spacebones/doge/* /var/www/html`
5. In order for the site to display correctly, rename the default welcome.conf file to welcome.conf.bak.

`mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.bak`
6. Enable & start the Apache service, then exit the container environment by typing exit or hitting CTRL+C.

`chkconfig httpd on && service httpd start`
`exit`
7. Save the edited image.

`docker commit websetup spacebones:thewebsite`
---
# Dockerizing a node.js application
**Activity**: Using the example Dockerfile included in activity instructions, use Docker to build a new Node.js app image using the files under the content-dockerquest-spacebones/nodejs-app subdirectory, named baconator. Be sure to tag the image as dev.

1. Clone the content-dockerquest-spacebones GitHub repo
`git clone https://github.com/linuxacademy/content-dockerquest-spacebones`
2. Move into the nodejs-app subdirectory
`cd ~/content-dockerquest-spacebones/nodejs-app`
3. Use the Dockerfile below to build a new image
    ```
    FROM node:7
    WORKDIR /app
    COPY package.json /app
    RUN npm install
    COPY . /app
    CMD node index.js
    EXPOSE 8081
    ```
4. build container image
`docker build -t baconator:dev .`
5. (optional) Run the image to verify functionality
`docker run -d -p 80:8081 baconator:dev`
---
# Ignoring Files During Docker Build
1. Create `.dockerignore`:
Edit the included .dockerignore file under ~/content-dockerquest-spacebones/salt-example/salt-master/ to ignore the following: badscript.sh, README.md and all conf files.

.dockerignore:

    badscript.sh
    *.conf
    README.md

2. Build the Docker Image: create a new Docker image named salt-master:deb via

  `docker build -t salt-master:deb .`
From within the directory containing the Dockerfile
---
# Creating Data Containers
Use the spacebones/postgres image to create a data container named boneyard as well as three running app containers with the following names:

    cheese
    bacon
    tuna

create postgres data container: `docker create -v /data --name boneyard spacebones/postgres /bin/true`

mount boneyard on 3 containers
```
docker run -d --volumes-from boneyard --name cheese spacebones/postgres
docker run -d --volumes-from boneyard --name tuna spacebones/postgres
docker run -d --volumes-from boneyard --name bacon spacebones/postgres
```
---
# Container Networking with Links
**Activity**: Create a network link between the two Docker containers, spacebones:thewebsite & treatlist using legacy Docker links. Use the image found at spacebones/postgres to create the database. 

1. Verify SpaceBones Container is Running. If not running, start a new container using the Docker container image **spacebones:thewebsite**: `docker run -d -p 80:80 --name spacebones spacebones/spacebones:thewebsite` (this might lead to a port conflict with previously running container. Use `docker container rm -f <COntainer ID>` to remove the previous container).
2. Create Treatlist Container:
If not already running, create a new container from the spacebones/postgres image named treatlist. `docker run -d -P --name treatlist --link spacebones:spacebones spacebones/postgres`
3. Create Network Link:
Link the spacebones container with the treatlist container using legacy docker network links (above command already creates the link). Verify the link using `docker inspect -f "{{ .HostConfig.Links }}" treatlist`
---
# Container Networking with Networks
**Activity**: create a new Docker network named borkspace using the __192.168.10.0/24__ network range, with the gateway IP address __192.168.10.250__. Once the borkspace network is created, use it to launch a new app named __treattransfer__ in interactive mode using the __spacebones/nyancat:latest__.
1. create network
`docker network create --driver=bridge --subnet=192.168.10.0/24 --gateway=192.168.10.250 borkspace`
2. verify that network is created
`docker network ls`
`docker network inspect borkspace`
3. launch treattransfer container using the borkspace network
`docker run -it --name treattransfer --network=borkspace spacebones/nyancat`
4. exit the container
`CTRL+c`
---
# Persistent Data Volumes
**Activity**: create a Docker volume containing the contents of /content-dockerquest-spacebones/volumes/ named missionstatus, then mount the volume on a new httpd container named fishin-mission running on port :80 to view it!
1. if not already present, clone the content-dockerquest-spacebones repo on your machine
`git clone https://github.com/linuxacademy/content-dockerquest-spacebones.git`
2. create a new volume named missionstatus
`docker volume create missionstatus`
3. Use Docker commandline to print all information on the missionstatus volume.
`docker volume inspect missionstatus`
4. drop to root, then copy the contents of content-dockerquest-spacebones/volumes/ to missionstatus data directory
`sudo -i`
`cp -r /home/user/content-dockerquest-spacebones/volumes/ /var/lib/docker/volumes/missionstatus/_data/`
5. Exit root, then start a new container named fishin-mission using the httpd base image available on DockerHub, running on port 80.
`docker run -d -p 80:80 --name fishin-mission --mount source=missionstatus,target=/usr/local/apache2/htdocs httpd`
Visit the server IP in your browser for a special thank you from SpaceBones residents!
---
# Container Logging [DO NOT TRY THIS]
**Activity**: In this lab, we are going to configure syslog on a Docker instance, configure Docker to use syslog instead of the JSON logging driver, and spin up two containers to test our configuration.

1. elevate privileges to root. `sudo su -`
2. Configure Docker to Use Syslog. Open the rsyslog.conf file.

`vim /etc/rsyslog.conf`
`In the file editor, uncomment the two lines under `Provides UDP syslog reception` by removing `#`.`
    $ModLoad imudp
    $UDPServerRun 514
3. Then, start the syslog service.

`systemctl start rsyslog`
4. Now that syslog is running, let's configure Docker to use syslog as the default logging driver. We'll do this by creating a file called daemon.json.

`sudo mkdir /etc/docker`
`vi /etc/docker/daemon.json`

5. In the vi editor, enter the following, making sure to replace <PRIVATE_IP> with the private IP of your cloud server:

    {
    "log-driver": "syslog",
    "log-opts": {
        "syslog-address": "udp://localhost:514"
    }
    }
Next, save and quit. Then, start the Docker service.

`systemctl start docker`
The next step is to see if there are any logs coming in from Docker.

`tail /var/log/messages`

6. Now let's test our setup.

We're going to create two new containers using the `httpd` image. The first one will be called syslog-logging and will use `syslog` for the log driver. The second will be called `json-logging` and will use the JSON file for the log driver.

Let's create our first container.

`docker container run -d --name syslog-logging httpd`

7. Then run `docker ps` to make sure our container is up and running correctly.

Next, execute the `docker logs` command to see what logs we have.

`docker logs syslog-logging`

8. When we run this, we receive an error that says `Error response from daemon: configured logging does not support reading`. This is because we're using syslog instead of JSON for logging.

To confirm this, we can check the content of `/var/log/messages`. Verify that the syslog-logging container is sending its logs to syslog by running tail on the message log file.

`tail /var/log/messages`

9. The output shows us the logs that are being input to syslog.

Now let's create our second test container. This time, we'll specify the log driver as the JSON file.

`docker container run -d --name json-logging --log-driver json-file httpd`
Run the `docker ps` command again. This time, we should see two containers running.

Next, verify that the json-logging container is sending its logs to the JSON file. Execute the docker logs command on the json-logging container.

`docker logs json-logging`
This time, the logs do not appear in /var/log/messages because they are being sent to a JSON file instead.
---

# Adding Metadata and Labels
**Activity**: In this lab, you will deploy a container that uses labels. In order to complete this learning activity, you will need a Docker Hub account.

Log in to your Docker workstation and Docker server, and sudo to root. You will create the Dockerfile and image on the Docker workstation. The weather-app container will be run on your Docker server.

1. Begin by logging in to the Docker Server and Docker Workstation lab servers using the credentials provided on the hands-on lab page: `vagrant ssh`.
2. Become the root user:

`sudo su -`
3. On Docker Workstation create a Dockerfile:
`vi Dockerfile`.
4. Use the following instructions:

Note: Replace `EMAIL_ADDRESS` with your email address.
```
 FROM node

 LABEL maintainer="EMAIL_ADDRESS"

 ARG BUILD_VERSION
 ARG BUILD_DATE
 ARG APPLICATION_NAME

 LABEL org.label-schema.build-date=$BUILD_DATE
 LABEL org.label-schema.applicaiton=$APPLICATION_NAME
 LABEL org.label-schema.version=$BUILD_VERSION

 RUN mkdir -p /var/node
 ADD weather-app/ /var/node/
 WORKDIR /var/node
 RUN npm install
 EXPOSE 3000
 CMD ./bin/www
```
5. Build the Docker image
Log in to Docker Hub:

`docker login`

6. Build the Docker image using the following parameters:

Note: Be sure to replace `DOCKER_USERNAME` with your Docker Hub username.

 docker build -t <DOCKER_USERNAME>;/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
 --build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.0  -f Dockerfile .
Show image ID:

 `docker images`
Use image ID to inspect:

 `docker inspect IMAGE_ID`
## Push the image to Docker Hub
Push the weather-app image to Docker Hub.

Note: Be sure to replace USERNAME with your Docker Hub username.

    docker push USERNAME/weather-app
## Create the weather-app container
### On the Docker Server:

Start the weather-app container.

Note: Be sure to replace **USERNAME** with your Docker Hub username.

 docker run -d --name demo-app -p 80:3000 --restart always USERNAME/weather-app
Verify that the image is running:

 docker ps
Check out version v1.1 of the weather app
### On the Docker Workstation:

In the weather-app directory, check out version v1.1 of the weather app.

 cd weather-app
 git checkout v1.1
 cd ../ 
### Rebuild the weather-app image
Rebuild and push the weather-app image.

Note: Be sure to replace `USERNAME` with your Docker Hub username.
```
 docker build -t USERNAME/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') /
 --build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.1  -f Dockerfile .
 docker push USERNAME/weather-app
```
### On the Docker Server:

Show image status:

    docker ps
Using the container ID from the previous command, inspect the weather-app image:

 docker inspect IMAGE_ID
