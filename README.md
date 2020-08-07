# vaccinevoting-app
### Architecture

![](architecture.png)


##### Docker run
```
docker run -d --name=redis redis
docker run -d --name=db
docker run -d --name=vote-vacc -p 5000:80  vaccinevoting-app
docker run -d --name=result -p 5001:80 result-app
docker run -d --name=worker worker
```
##### Docker links: used to link two containers
```
docker --link
```
#### Example
```
docker run -d --name=vote-vacc -p 5000:80  --link redis:redis vaccinevoting-app
docker run -d --name=result -p 5001:80  --link db:db result-app
docker run -d --name=worker -p 5001:80  --link db:db --link redis:redis worker

```
##### Generating the docker-compose.yml file.
```
redis:
  image: redis
posgres-db:
  image:postgres:9.4
vote-vacc:
  image:vaccinevoting-app
     ports:
        - 5000:80
     links:
        - redis
result:
  image: result-app
    ports:
      - 5001:80
    links:
      - db
worker:
  image: worker
    links:
      - redis
      - db

```
##### In case an app has to be built, image is replaced by the directory of that app.
```
redis:
  image: redis
posgres-db:
  image:postgres:9.4
vote-vacc:
  build:./vaccinevoting-app
     ports:
        - 5000:80
     links:
        - redis
result:
  build: ./result-app
    ports:
      - 5001:80
    links:
      - db
worker:
  build: ./worker
    links:
      - redis
      - db

```

![](Tovaccinate.png)

#### Docker compose
##### docker-compose.yml
```
version: '3'
services:
  redis:
    image: redis

  db:
    image: postgres:9.4

  vote:
    image: vaccinevoting-app
    ports:
      - 5004:80
    links:
      - redis

  worker:
    image: worker-app
    ports:
       - 5007:80
    links:
       - db
       - redis

  result:
    image: result-app
    ports:
      - 5006:80
    links:
      - db
```
##### Run ``` docker-compose up ```

##### Private Registry
```
docker login private-registry.io
docker run private-registry.io/apps/internal-app

```
#### Deploy private Registry
 ```
docker run -d -p 5000:5000 --name  registry registry:2
```
#### Tag an image with the created private registry
```
docker image tag my_image localhost:5000/my_image
 ```
 #### Push an image to the private registry
 ```
 docker push localhost:5000/my_image
 ```
 #### Pull the image from the Registry
 ```
 docker pull localhost:5000/my_image
 ```
 #### Fixing cpu size
 ```
 docker run --cpu=.5 centos
 ```
 #### Fixing  Memory size usage
```
docker run --memory=200m centos
```
#### Docker PID
##### Running a tomcat container
```
docker run -it --rm -p 8888:8080 tomcat:9.0

```
##### Execute a command inside the container
```
docker exec ps -eaf
```
###### <em> -eaf: lists all the processes running in that container </em>

```
ps -eaf | grep
```
#### Docker Storage
> Docker stores data by  default in var/lib/docker
> it uses the COPY_AND_WRITE mechanism for handling code modifications.
> We can persist data in docker by adding a data volume that we specify.Example:  - docker volume create data_volume
                  - docker run -v data_volume:/var/lib/mongodb mongodb
                  - docker run -v data_volume2:/var/lib/mongodb mongodb
                  - docker run -v /data/mongodb:/var/lib/mongodb mongodb

> We can also use "mount" as follows:
```
docker run --mount type=bind, source=/data/mongodb,target=/var/lib/mongodb  mongodb
```
