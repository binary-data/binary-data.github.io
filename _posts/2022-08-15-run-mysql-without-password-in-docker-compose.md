---
layout: post
title: "Running MySQL without password in Docker Compose"
date: 2022-08-15 16:50:00 +0400
comments: true
tags: mysql docker docker-compose
---

It is possible to access a MySQL or MariaDB database in Docker Compose, even without knowing a password.

{% include warning.html text="Warning! Do this only for development purposes! It is completely insecure to keep a passwordless root in a database." %}

In my case I needed to create a dump from the data. I only had the `/var/lib/mysql` folder copied from production server. The folder was mounted in the docker container.

So I've added the `--skip-grant-tables` [option](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_skip-grant-tables) to my `docker-compose.yml`:

```yaml
services:

  db:
    image: mariadb:10.4
    command: "--skip-grant-tables" # allow to connect with any credentials
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - type: "bind"
        source: "./db"
        target: "/var/lib/mysql"
```

I was able to login with any (or none) credentials.
