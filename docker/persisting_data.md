# Persisting Data
- Usually data don't store data in containers
- Non-persistant data
  - Locally on a writable layer
  - It's default, just writw to the file system
  - When containers are destroyed, so the data inside them
- Persistant data
- stored outside the container in a Volume
- A volume is mapped to a logical folder

## Volume
- Maps a folder on the host to a logical folder in the container

create new volume
> `docker creare volume [volumeName]`
> 
list the volumes
> `docker volume ls`
> 
display the volume
> `docker volume inspect [volumeName]`
> 
deletes a volume
>`docker volume rm [volumeName]`

deletes all the volume not mounted
>`docker volume prune` 

### run a container with a volume
---
>`docker run -d --name [containerName] -v myvol:/app nginx:latest`

### working with volumes on containers
----

- creat a volume
  >`docker create volume [volumeName]`
- list the volumes
  > `docker volume ls`
- run a nginx cotainer that wull use the volumes
  > `docker run -d --name voltest -v myvol:/app nginx:latest`
- connect to the instance
  >`docker exec -it voltest bash`
- create a file in the app folder and save it using some content using 
  >`apt-get update`

  > `nano test.txt`

- stop the container and remove it
 >`docker stop voltest`
 > `docker rm voltest`

- even after removing the container, you can see folder /app mounted inside the container when you freshly creates it from the same image.


## Cleanup

- >`docker volume rm myvol`
- >`docker stop voltest`
- >`docker rm voltest`