Some Dockerfiles for creating an PHP Environment
================================================

Dockerfile -- a text file that contains all commands, in order, needed to build a docker image

- [PHP 5.6.30 on Debian 10 without webserver](php/5.6.30/Dockerfile)
- [PHP 7.4 on Debian 10 with Apache webserver](php/7.4/buster/apache/Dockerfile)
- [PHP 8.2 on Debian 11 with Apache webserver](php/8.2/bullseye/apache/Dockerfile)


Docker Engine should already be installed on you maschine. See installation instruction on: 

> [What is Docker from official site](https://www.docker.com/):
>Docker makes development efficient and predictable
Docker takes away repetitive, mundane configuration tasks and is used throughout the development lifecycle for fast, easy and portable application development – desktop and cloud. Docker’s comprehensive end to end platform includes UIs, CLIs, APIs and security that are engineered to work together across the entire application delivery lifecycle.


[Docker Engine Overview](https://docs.docker.com/engine/)

Installation
============

Please refer to the online documentation for Docker engine installation

[Docker Installation](https://docs.docker.com/engine/install/)

[Docker Installation Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu)

[Docker Installation Mint]() do same installation steps described by Ubuntu

with the following command to set up the repository:

```bash
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  <UBUNTU_VERSION_NAME> stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Value of `<UBUNTU_VERSION_NAME>` should be get on Ubuntu via command `$(lsb_release -cs)`

Setup Docker for development in new Project
=====

### Preparation
Provided `Dockerfile` includes all installation instructions for needed Software, and is prepared so for minimal necessary software stack, 
will be included after image builds.
The Installing of extra software/extensions can be enabled via uncommenting some blocks which contains installation instructions.

### Project integration
Copy `Dockerfile` you are interested e.g. [PHP 7.4 on Debian 10 with Apache webserver](php/7.4/buster/apache/Dockerfile) 
in Project directory, or in a specific subdirectory in Project e.g. `.docker`.

Get an Image complied from `Dockerfile` with specific tag name -t run build command
```bash
docker build --build-arg APPUID=$(id -u) --build-arg APPUGID=$(id -g) . -t PROJECTNAME
```

where `.` is directory wo `Dockerfile` was copied previously and `PROJECTNAME` is expected image name.

Pull docker image to docker.hub. You need username/password of
```bash
docker login
# enter credentials
# ...

# push image
docker push <TAG_NAME>
```

### Working with Docker
After image was build we could start this as a container in detached mode 
```bash
docker run -it --rm -d -v $PWD:/app
```
and check that the container is available with command `docker ps`
To open a terminal session within running container we need a `CONTAINER_ID`this one we got from output of `docker ps` command.
Open terminal session `docker exec -it -u (id -u):(id -g) CONTAINER_ID bash`

Shutdown all containers
```bash
docker ps -aq | xargs docker stop | xargs docker rm
```


Example Build and Tagging:
`docker build --build-arg APPUID=1000 --build-arg APPUGID=1000 -t waglpz/vwd-base:8.2 php/8.2/bullseye/apache/` 

...
