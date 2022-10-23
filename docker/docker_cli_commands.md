# Docker CLI commands

## Commands to manage version related info
***
display system information 
> `docker info`

display the docker's system version
> `docker version`

Login to docker Registry
> `docker login`


## Commands to manage Containers
***
pull an image from Registry
> `docker pull [imageName]`

run containers
> `docker run [imageName]`

to run docker image in detached mode
> `docker run -d [imageName]`

start stopped containersl
> `docker start [containerName]`

list running containers
> `docker ps`

list running and stopped containers
>`docker ps -a`

stop containers
> `docker stop [containerName]`

kill containers
> `docker kill [containerName]`

get image info
> `docker image inspect [imageName]`

pull and run a Nginx server
>` docker run -d -p 8080:80 --name webserver nginx`

where 
>`-p` is port which sepcifies 8080 as localhost port and 80 
was the port which container was listening to. 

> `-d` is a detached mode, when docker runs it start the container and return us the command line. 


## Commands to set limits
***
To set limits for containers 
>`docker run --memory="256m" nginx` and
`docker run --cpus=".5" nginx`


## Commands to attach shells to containers
***
we can attach and CLI tool to container and execute commands inside the container

>`docker run -it nginx bash` -> Attach shell. ***This will pull a new image from the hub, run it and attach bash to it***. 

- If we want to attach the shell to already running container, we need to execute below command
> `docker container exec -it webserver bash`

  >`docker runt -it microsoft/powershell:nanserver pwsh.execute` -> Attach powershell

>`docker container exec -it [containername] -- bash` -> Attach to a running container

## Commands to Cleaning up the containers/images
***
removes stopped containers
> `docker rm [containerName]`

removes all stopped containers
> `docer rm $(docker ps -a -q)`

list images
> ` docker images`

delete images
> `docker rmi [imageNames]`

remove all images not in use by any containers
> `docker system prune -a`

## Commands to build docker images using Dockerfile
***
Builds an image using Dockerfile located in same folder.
> `docker build -t [name:tag] .`

Builds an image using Dockerfile located in different folder.
> `docker build -t [name:tage] -f [fileName]`

Tag and existing image
>`docker tag [imageName] [name:tag]`


## Doker flow

Docker Image -- docker run --> Running container --> Stopped COntainer -- docker commit --> New Image

## How to convert docker containers into docker images
>`docker commit [container_id] (or) [container_name] [image_name`

explictly you can tag the name for the image that you created with docker container
>`docker tag [image_id] [image_name] `

### Running process in containers

if you want a container up and running from a image and should get delete once stops 
>`docker run --rm -ti [image_name] [process to start]`

Ex; >`docker run --rm -ti my_nginx_image bash -c "sleep 3; echo all done`

if you want to run a container and leaves it running in the background -- kind of detached
>`docker run -d -ti my_nginx_image bash`

and now if you want to attach the container 
>`docker attach [container_id]`

you cannot simply type exit inside bash, because it will stop running container and exits you. 
If you want container still in running mode and detach from that just type `Ctrl+P & Ctrl+Q`

### Managing Containers

if you want to see logs of the containers

>`docker run --name [container_name] -d -ti [image_name] process`

Ex; >`docker run --name nginx_cont -d -ti my_nginx_image bash -c "lose etc/passowrd" `

if you want to see logs of the container

>`docker logs [container_name]`

resource contraints
>`docker run --memory maximum-allowed-memory [image_name]`

to know CPU shares and quota

>`docker run --cpu-shares`

>`docker run --cpu-quota`

#### Remmber below things while dealing with containers
- don't let your containers fetch dependencies when containers start
- don't leave important things in unnamed stopped containers

to kill the container
>`docker kill [container_name]`

>`docker rm [container_name`


# In Nutshell

### Dockerfile
```Dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
```
The above dockerfile upon executing with command `docker build -t [image:tag] .` ==> `docker build -t webserver:1.0 .` will create a image with name **webserver** and Tag it with version **1.0** by pulling base image of nginx and copy all the current folder contents into the image. 

```
docker run -d -p 8080:80 --name myserver webserver:1.0
```
The above command will run that image that we created in a detached mode a chose `-d` and port `-p` at `8080:80` with container name as `myserver`. 