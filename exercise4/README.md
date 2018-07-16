# Exercise 4

## Lesson 1

**Scenario:** Start a 3 tier application using Docker Compose

**Objective:** Orchestrate multiple containers with Docker Compose

**Steps:**

1. Create a new `Dockerfile` to house the Rails app:

	```
	FROM ruby:2.5.1

	RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs

	RUN mkdir /app
	WORKDIR /app

	COPY ./blog/Gemfile /app/Gemfile
	COPY ./blog/Gemfile.lock /app/Gemfile.lock
	RUN bundle install

	COPY ./blog /app
	```

	We will be copying the `blog` folder with the basic Rails app into the container.

2. Create a new file: `docker-compose.yml` to orchestrate the startup of multiple containers

	```
	version: '3'
	services:
	  db:
	    image: postgres:9.5
	    volumes:
	      - ./postgres_data:/var/lib/postgresql/data
	    ports:
	      - "5432:5432"
	    environment:
	      - POSTGRES_USER=blog_db
	      - POSTGRES_PASSWORD=sibeisecret
	  web:
	    build: .
	    image: rails_blog
	    command: bundle exec rails s -p 3000 -b '0.0.0.0'
	    ports:
	      - "3000:3000"
	    depends_on:
	      - db
	    environment:
	      - DATABASE_URL=postgres://blog_db:sibeisecret@db/blog_db

	```

3. Start the Postgres instance first

	```
	docker-compose up -d db
	```

4. Run the Rails migration

	```
	docker-compose run web rails db:migrate
	```

5. Start the Rails instance

	```
	docker-compose up -d web
	```

6. Visit the website: [http://localhost:3000](http://localhost:3000) or [http://192.168.99.100:3000](http://192.168.99.100:3000) for Windows 10 Home users.

## Lesson 2

**Scenario:** Developing new code with a docker container

**Objective:** Learn how to mount a volume from the host machine and edit it.

**Steps:**

1. Add these 2 lines to the `web` service in `docker-compose.yml`:

	```
	volumes:
   	- ./blog:/app
	```

2. Stop the containers and restart it.

	```
	docker-compose down
	docker-compose up -d
	```

3. Log in to the running `web` instance:

	```
	docker-compose exec web bash
	```

	You end up in the `/app` folder.

4. Create the `Post` scaffold:

	```
	bundle exec rails g scaffold Post title:string content:text publish_at:datetime
	```

5. Run the database migration:

	```
	rails db:migrate
	```

6. Visit the new page: [http://localhost:3000/posts](http://localhost:3000/posts) or [http://192.168.99.100:3000/posts](http://192.168.99.100:3000/posts) for Windows 10 Home users

## Further Readings

- Docker Compose CLI ([https://docs.docker.com/compose/reference/overview/](https://docs.docker.com/compose/reference/overview/))
- Docker Compose file reference ([https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/))