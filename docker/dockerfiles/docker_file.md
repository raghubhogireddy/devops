## DockerFile
***
- This is a 'small program' that describes how to build an image.
- You can run this program with 
  ``` dockerfile 
  docker build -t name-of-result .
  ``` 
  Note the "." here that says required files for the dockerfile creation presents in the current directory.
- When it finishes, the result will be in your local docker registry

### Producing the Next image in each step
- Each line makes the image from the previous line and makes another image.
- The previous image is unchanged.
- It does not edit the state from the previous line.
- You don't want large files to span lines or your image will be huge.
  
### Caching with each step
- This is important; watch the build output for "using cache"
- Docker skips lines that have not changed since the last build
- If your first line is "download latest file" it may not always run
- This caching saves huge amounts of time
- The parts that change the most belong to end of the Dockerfile

### Not Shell scripts
- Docker files look like shell scripts, but are not.
- Processes you start on one line will not be running on the next line
- Environment variables you set will be set on the next line
  - If you use the ENV command, remember that each line is it's own call to docker run.

## Building Dockerfile 
***

``` dockerfile
FROM busybox
RUN echo "building simple docker image."
CMD echo "hello container!!"
```

> `FROM` - Docker image creation always start with some base image. So here we are taking base image `busybox`. This should be the first statement in your dockerfile.
>> `FROM desbian:sid`
>
> `MAINTAINER` - This statement defines the author of the dockerfile.
>> `MAINTAINER FirstName LastName <eamil@example.com>`
> 
> `RUN` - statment to run inside the image. Runs the command line, waits for it to finish, saves the result.
>
>> `RUN unzip something.zip /opt/install/`
> 
>`ADD` - statement to add local files
>>`ADD run.sh /run.sh`
>
> Adds content of the tar archives
> >`ADD project.tar.gz /install/`
>
> Works with URLs as well
>>`ADD https://project.example.com/download/1.0/project.rpm /project/`
>
>`ENV` - sets environment variables
> Both during the build and when running the result
>> `ENV DB_HOST=db.production.example.com`
>
>`ENTRYPOINT` - Statement specifies the start of the command to run. If your container acts like a command line program you can use this. It's more of a Exec form
>> `["/bin/nano", "notes.txt"]`
>
> `CMD` - This statement runs, when an image started to run as container. It's more of an Shell form
>> `nano notes.txt`
>
>`EXPOSE` - statment to map ports into container
>
>`VOLUME` - statement defines shared or ephermeral volumes
>> `VOLUME ["host/path/", "/container/path/"]`
>
>> `VOLUME ["/shared-data/"]`
>
>`WORKDIR` - Statemnt to sets the directory the container starts in
>>`WORKDIR /install/`
>
>`USER` - statement to set which user the container will run as

### Installing a Program with Docker Build

```dockerfile
FROM debian:sid
RUN apt-get -y update
RUN apt-get install nano
CMD "nano" "/tmp/notes"
```

### Adding file through docker build

``` dockerfile
FROM nano:latest
ADD notes.txt /notes.txt
CMD "nano" "/notes.txt"
```
