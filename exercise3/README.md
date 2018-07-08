# Exercise 3

## Lesson 1

**Scenario:** Make containers talk to each other

**Objective:** Learn how to create a shared network between containers 

**Steps:**

By default containers are connected by default bridge network. You can ping containers via IP.

Listing all docker networks

```
docker network ls
```

Run postgres DB

```
docker run -e POSTGRES_PASSWORD=sosecret --name=pg -d postgres:9.6
```

Get postgres container IP

```
docker inspect <container id> | grep "IPAddress"
```

Run busybox

```
docker run -it busybox ping <container IP>
```

But its troublesome for a few reasons : 

* No DNS resolution
* No isolation and no expose of ports between containers

Hence we have the user-defined bridge network. 

```
docker network create test-net
docker run -e POSTGRES_PASSWORD=sosecret --name=pg --network=test-net -d postgres:9.6
docker run --network=test-net busybox nslookup pg
```

There are other type of networks beside Bridge

* Overlay Network ( This create a data encapsulation layer over Layer 3 network ) This enable container in hosts to communicate to each other. [more here](https://github.com/docker/labs/blob/master/networking/concepts/06-overlay-networks.md)
* Host Network ( Your container using your host network ) 
* Macvlan Network.

## Lesson 2

**Scenario:** Created persistent storage (volumes)

**Objective:** Learn how to mount a local folder into containers and make it persist bewteen new container instances

**Steps:**

The data in the containers are only removed when the containers are removed not when its stopped. 

```
docker run -it --name='test-box' busybox sh
touch test.txt

docker start test-box
docker exec -it test-box ls
```

Notice that the file is still in there. 

Docker provides 3 type of persistent storage type :

* Volumes 
	* This is managed by Docker 
	* Meant for decoupling docker host to containers
	* Can be used for other plugins liuke Azure File Storage. [more here](https://docs.docker.com/engine/extend/legacy_plugins/#volume-plugins)
	
* Bind Mounts 
	* This mount your localhost folder into the container.
	* Good for development, mounting source code into the container
	
* tmpfs - Memory ( only for Docker on Linux ) 
	* tmpfs mounts are best used for cases when you do not want the data to persist either on the host machine or within the container. This may be for security reasons or to protect the performance of the container when your application needs to write a large volume of non-persistent state data.

Using Volumes 

```
docker volume create test-vol
docker volume ls
docker volume inspect test-vol
```

```
docker run -it -v test-vol:/app --rm busybox sh
touch /app/test.txt
```

```
docker run -it -v test-vol:/app --rm busybox ls /app
```

Using Bind Mounts

```
docker run -it -v `pwd`:/app busybox sh
```

Take note that if there is existing files in the container, it will be hidden away. Note that the sbin is hidden away. 

This can be useful when you want to mount codes into the a existing image without rebuilding it.

```
docker run -it -v `pwd`:/usr busybox sh
```



