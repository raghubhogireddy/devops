# Docker CLI commands

### Commands to manage version related info
***
display system information 
> `docker info`

display the docker's system version
> `docker version`

Login to docker Registry
> `docker login`


### Commands to manage Containers
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


### Commands to set limits
***
To set limits for containers 
>`docker run --memory="256m" nginx` and
`docker run --cpus=".5" nginx`


### Commands to attach shells to containers
***
we can attach and CLI tool to container and execute commands inside the container

>`docker run -it nginx bash` -> Attach shell. ***This will pull a new image from the hub, run it and attach bash to it***. 

- If we want to attach the shell to already running container, we need to execute below command
> `docker container exec -it webserver bash`

  >`docker runt -it microsoft/powershell:nanserver pwsh.execute` -> Attach powershell

>`docker container exec -it [containername] -- bash` -> Attach to a running container

### Commands to Cleaning up the containers/images
***
>removes stopped containers
>
> `docker rm [containerName]`

>removes all stopped containers
>
> `docer rm $(docker ps -a -q)`

>list images
>
> ` docker images`

>`docker commit` tags images for you.
>
>This is example of the name structure
>
>`registry.example.com:port/organization/image-name:version-tag`

>convert docker containers into docker images
>
>`docker commit [container_id] (or) [container_name] [image_name]:[Tag]`

>explictly you can tag the name for the image that you created with docker container
>
>`docker tag [image_id] [image_name]`

>delete images
>
> `docker rmi [imageName] (or) docker rmi [image_id]`

>remove all images not in use by any containers
>
> `docker system prune -a`

### Commands to build docker images using Dockerfile
***
Builds an image using Dockerfile located in same folder.
> `docker build -t [name:tag] .`

Builds an image using Dockerfile located in different folder.
> `docker build -t [name:tage] -f [fileName]`

Tag and existing image
>`docker tag [imageName] [name:tag]`


### Doker flow
***

Docker Image -- docker run --> Running container --> Stopped COntainer -- docker commit --> New Image

### Running process in containers
----

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
---

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

### Exposing Ports
***
- Programs in containers are isolated from the iternet by default.
- We can group your containers into "private" networks
- Expose ports to let connections in
- Private networks to connect between containers
- Explicitly speccifies the port inside the contauner and outside
- Exposes as many ports as you want
- Requires coordination between contaiiners
- Makes it easy to find the exposed ports
> below command states to run a ubuntu which listens on two different ports 45678 & 45679 and opens a terminal interactive bash and conatiner will get removed once exited
>  
> `docker run --rm -ti -p 45678:45678 -p 45679:45679 --name echo-server ubuntu:14.04 bash`

- We do have a special variable if we want to specify host Ip inside a container by `host.docker.internal`
- The port inside the container is fixed.
- The ports on the host is chosen from the unused ports
- This allows many containers running programs with fixed ports 
- This often is used with service discovery program
> We have option to chose outside port dynamically for that you can remove `port:` 
> 
> To check dynamic external port we can use `docker port [container_name]`
> 
>`docker run --rm -ti -p 45678 -p 45679 --name echo-server ubuntu:14.04 bash`

- We can chose protocol for the container as well. 
>`docker run -p outside:inside/protocol(tcp/udp)`

### Container Networking
***
- We can see the list Networks by 
>`docker network ls`  
- you ccould see three types of Network Dirvers
  - bridge - N/w used by containers that don't specify a prefernce to be put into any other network.
  - host - when you don't want container to have N/w isolation at all.
  - none - This is in place, when there is no networking for the containers.
  
>To create a new network
>
> `docker network create [network_name]`
> 
>To add containers to the network
>
>`docker network connect [network_name] [container_name]`
>
>To run a container with specified network 
>
>`docker run --rm -ti --net [network_name] --name [container_name] ubuntu:14.04 bash`

### Volumes
***
- volumes a sort of like shared-folders or vitrual-discs to store and share data.
- Two main varities
  - Persistant : you can put data there, it will saved on host. If containers goes away, data still exist
  - Ephemeral : They exist as long as containers using it. If no container using it, they evoporate.
- Volumes are not part of images
#### Sharing Data with Host
- 'Shared folders' with the host
- Sharing a file into the container

> `mkdir [folder]`
> 
>`docker run -ti -v [path to shared folder]:/[folder to appear in container] ubuntu bash`

- same thing it goes for sharing a files as well

#### Sharing Data between containers
- we use `volumes-from`
- Shared 'discs' that exist only as long as they are being used
- Can be shared between containers

>`docker run -ti -v /[shared folder] --name container_1 ubuntu bash`
>
> please not this folder is not being shared with host and since it's shared between containers alone, it will get evoporate once containers stops using it.

>We are referring to volume created by container
>`docker run -ti --volumes-from [container_name] --name container_2 ubuntu bash`

### Docker Registries
***
- Registries that manage and Distribute images
- Docker(the company) offers these for free
- you can run your own as well\
- Finding images from the Hub/Registry
  - `docker search [image_name]` - search for particular image
  - `docker pull [image_name]` - pulls the image from the Hub
  - `docker tag [image_name]:[tag] [profile_name]/[image_name]:[tag]` - you can tag the image as if it's your own and it creates one with the details you specified.
  - `docker push [profile_name]/[image_name]:[tag]` - to push your docker image to the hub. 

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