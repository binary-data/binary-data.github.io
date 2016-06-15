---
layout: post
title:  "Running integration tests in PHPStorm with PHPUnit and Docker"
date:   2016-06-15 12:00:00 +0400
comments: true
tags: docker phpunit php mysql
---
## What are we trying to make?
Make PHPStorm run PHPUnit integration tests that use MySQL database using Docker.

If you need only to run unit tests with PHPUnit, without using database or Redis or something else, you can try this: [http://obrown.io/2015/12/23/phpunit-docker-phpstorm.html](http://obrown.io/2015/12/23/phpunit-docker-phpstorm.html)

## How we could get this done?
By using Docker Compose to tie up containers.

The main idea is to set up Docker Compose test environment, create a shell script that runs PHP in this environment, and use that shell script as PHP interpreter in PHPStorm.

### Step 1:
You will need an image with at least PHPUnit installed. You can use existing image which can be found on Docker Hub or create your own. Example Dockerfile on Github: [https://github.com/binary-data/phpunit-dockerfile](https://github.com/binary-data/phpunit-dockerfile). Image that I use for examples is located on Docker Hub: [https://hub.docker.com/r/binarydata/phpunit/](https://hub.docker.com/r/binarydata/phpunit/)

Since building Docker images is not a topic for this article, more information on this can be found in [Docker documentation](https://docs.docker.com/engine/reference/builder/)

### Step 2:
You will need to create a `docker-compose.yml` file with configuration describing your test environment, like this:

```yaml
version: '2'
services:
  phpunit:
    image: binarydata/phpunit # our PHPUnit image, created or found on Docker Hub
    volumes:
      - /home/anton/work/project/src/:/home/anton/work/project/src/ # folder where project resides
      - /tmp/:/tmp/ # linking /tmp/ folder because PHPStorm stores its stuff there
    depends_on:
      - db
    links:
      - db
  db:
    image: mariadb # image with MySQL database, I use mariadb here
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: integration_tests
```

You can add as many services as you need in your test environment, like Redis or Memcached. More information on Docker Compose you can find in its [documentation](https://docs.docker.com/compose/overview/)

### Step 3:
Create a shell script, say `docker.sh`, next to `docker-compose.yml` that runs PHP in your environment:

```bash
#!/usr/bin/env bash
cd `dirname $0` && docker-compose run --rm phpunit php "$@"
```

`cd dirname $0` part changes working directory to where `docker-compose.yml` is located, so `docker-compose run` could see it. Then it runs php inside `phpunit` container, with all linked containers started by Docker Compose.

### Step 4:
Set up PHP interpreter in PHPStorm. Go to "Settings -> Languages & Frameworks -> PHP":

<a href="#" data-featherlight="/images/2016-06/add_interpreter.png">![Add interpreter](/images/2016-06/add_interpreter.png)</a>

Add "Other Local" interpreter and provide a path to previously created `docker.sh`:

<a href="#" data-featherlight="/images/2016-06/set_interpreter.png">![Set interpreter](/images/2016-06/set_interpreter.png)</a>

You should see that PHPStorm detected PHP version. You can ignore warning about php.ini file.

Then select newly created interpreter as project interpreter.

### Step 5:
Create configuration for your tests. Go to "Run -> Edit configurations" and add PHPUnit configuration:

<a href="#" data-featherlight="/images/2016-06/create_configuration.png">![Create configuration](/images/2016-06/create_configuration.png)</a>

I have added path to PHPunit to interpreter include path there, so PHPStorm could see it:

```-d include_path=./:/usr/local/lib/php:/root/.composer/vendor/phpunit```

and set a working directory, same as in `docker-compose.yml`.

Now you should be able to run tests.
