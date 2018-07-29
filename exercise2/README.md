# Exercise 2

## Lesson 1

**Scenario** : Insert js folder and package.json

**Objective** :

* To teach `FROM`, `WORKDIR`, `COPY`, `RUN` and `CMD` from `Dockerfile`.

**Steps** :

1. Create a new file and name it `Dockerfile`. Here are the contents of the file:

	```
	FROM node:9.11.1

	WORKDIR /app

	COPY . /app

	RUN npm install

	CMD [ "node", "--version" ]

	```

2. Run this command:

	```
	docker build --tag docker_workshop .
	```

3. Start the new container

	```
	docker run docker_workshop ls /app
	```

## Lesson 2

**Scenario** : Retrieve compiled files from containers

**Objective** : To teach docker cp (might be useful for jenkins) and also pre-teaser for volume for development

**Steps** :

1. Run this command:

	```
	docker container ls --all
	```

	Take note of the `CONTAINER ID` of `docker_workshop`.

2. Run this command:

	```
	docker cp <Container ID>:/app/node_modules/ .
	```

3. Run this command:

	```
	docker cp <Container ID>:/app/package-lock.json .
	```

4. You should see the new files (`node_modules` and `package-lock.json`) in your local directory.

## Lesson 3

**Scenario** : Install server to serve index.html

**Objective** :

* Teach interactive terminal in docker.
* Teach Princple of hand roll before automating
* Understanding different between localhost and 0.0.0.0 in docker world
* Port Mapping

**Steps** :

1. Update your `Dockerfile` to the following:

	```
	FROM node:9.11.1

	WORKDIR /app

	COPY . /app

	RUN npm install

	RUN npm install http-server -g

	EXPOSE 8080

	CMD [ "http-server", "/app", "-p", "8080" ]
	```

2. Build the container:

	```
	docker build --tag docker_workshop .
	```

3. Start the new container, mapping host port 8080 to container port 8080:

	```
	docker run --publish 8080:8080 docker_workshop
	```

4. Visit the site with your web browser: [http://localhost:8080](http://localhost:8080)

	* *__Note:__ If you have any issues accessing port 8080, you might want to check if you have any other service / app using that port number.*

	* *__Windows 10 Home Users:__ You will have to access the app using the docker-machine's host ip address and your app's export port instead. This is usually [http://192.168.99.100:8080](http://192.168.99.100:8080).*

5. Press `Ctrl`+`c` to stop the container.

## Lesson 4

**Scenario** : Edit your codes and see it reflect on the container

**Objective** : Teach Volume Mapping ( in preparation for development on docker )

**Steps** :

1. Start the container again with this new command:

	```
	docker run --volume `pwd`:/app --publish 8080:8080 docker_workshop
	```

2. Edit `index.html` and see it show up on the caddy web server.

3. Stop and start the container in interactive mode:

	```
	docker run -it --volume `pwd`:/app --publish 8080:8080 docker_workshop bash
	```

4. Make changes to `index.html` and run this command:

	```
	http-server /app -p 8080
	```

## Further Reading

- Dockerfile reference ([https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/))
- Develop with Docker ([https://docs.docker.com/develop/](https://docs.docker.com/develop/))
