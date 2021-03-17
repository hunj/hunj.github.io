---
layout: post
date: 2021-03-14
title: Create your own Discord bot from scratch using Python
subtitle: Use Python Discord library to develop fun bot features and deploy to free services to run the bot on your own server!
tags: python,discord,docker,bot,github,heroku
---

In this article we will: (1) create an app within the Discord developer portal to add a bot user to your own server, (2) create a dockerized environment for local debugging and development, (3) code a simple "hello world" type of functionality, and (4) deploy the bot to Heroku to have it running 24/7.

# Step 1. Register an app & add a bot user

First of all, we need to register a developer app in order to create a bot account. Simply visit the following URL:

> `https://discord.com/login?redirect_to=%2Fdevelopers%2Fapplications`


The above URL will prompt you to log in and/or if you are already logged in, you will be redirected to the developer portal.

Inside the dashboard, click on the `New Application` button on the top right. Give a nice name for your new application, then hit `Create` to finally create the app. Navigate to the `Bot` menu on the right navigation bar.

Under the `Build-A-Bot` section, click on `Add Bot` button, and confirm that you want to create a bot for this new app. Let's turn off the `Public Bot` option found in the `Authorization Flow` section since this is still under development.

Now let's navigate to the `OAuth2` menu.

In the `OAuth2 URL Generator` section's "Scopes" checkbox list, click the "bot" item to check it.

Another checkbox list named "Bot Permissions" will appear at the bottom. For now, let's just check on the "Administrator" permission to give the bot full access over the server. A URL should show up that looks like this:

> `https://discord.com/api/oauth2/authorize?client_id={{ client_id }}&permissions=8&scope=bot`

Visit the URL. It will ask where you would like to add this bot user to; select your own server and confirm.

Now check on your own discord server that you just added the bot to. It should appear under the user list. Congratulations! You just added a bot user to your own discord server. 

Next, we will create a base development environment for our bot client.

# Step 2. Create a Docker container for local development

The main reason I personally prefer using a docker container over virtualenv for local development is because we can encapsulate an entire OS. With a Docker image, you can swap out the entire OS, install and run Python on Ubuntu, Debian, Alpine, and even Windows Server Core. In short, it is easier to replicate the production server's environment within your local host development machine, or vice versa (deploying locally-built docker image to production).

Without much getting into detail, let's get going. For convenience, let's call the root directory of this project as `discordbot`, i.e.

```bash
$ mkdir -p discordbot/{bot,secrets}
$ cd discordbot
```

Inside the root directory we create three boilerplate files, (1) docker image definition file, and (2) requirements.txt for installing dependencies, and (3) a docker-compose file to run commands within the image created through Dockerfile:

`Dockerfile`

```bash
FROM python:3.9.2
ENV PYTHONUNBUFFERED 1

RUN mkdir /app
WORKDIR /app

COPY requirements.txt /app
RUN pip install -r requirements.txt

COPY . /app
```

`requirements.txt`

```
discord.py
```

Here, Docker will create a base Python image from Docker Hub, install dependency packages (in this case the Python library for Discord, as specified in requirements.txt), and copy the contents of the project directory inside the image.

Let's create a simple Python script in our Docker container so we can ensure the environment is set up properly:

`bot/client.py`

```bash
print("Hello World")
```

Lastly, create the docker-compose boilerplate file:

`docker-compose.yml`

```yml
version: '3.5'

services:
  nano:
    container_name: bot
    build: .
    command: python bot/client.py
    volumes:
      - ./bot:/app/bot
    ports:
      - "8000:8000"
    environment:
      DISCORD_BOT_TOKEN: /var/run/secrets/discord_bot_token
    secrets:
      - discord_bot_token

secrets:
  discord_bot_token:
    file: ./secrets/discord_bot_token

```

Here, place the bot token that you saved from 


In the `Token` section, click on the "Click to Reveal Token" button. It should reveal a long string that looks like this:

```
ODIxNTIzOTM2ODk4NDQ5NDA5.YFE9yQ.St5Jty68LVkmVFwedARPoQtRs3I
```

As implied, never give this token away. If you did by accident, you can simply re-create the token key by clicking on the `Regenerate` button.

Save that token in `secrets/discord_bot_token` (no file extension), as implied in the `secrets` section of the docker-compose file.

Now let's try running

```bash
$ docker-compose up
Building bot

(docker build process steps omitted here)
Successfully built 245f87862a86
Successfully tagged bot:latest
Creating bot ... done
Attaching to bot
bot     | Hello World
```

Awesome, now you created the docker image to virtualize the development environment and confirmed that the python script is working.

In the upcoming article we will get into the actual fun part--developing interactive functions and deploying it.

Stay tuned!

**You can view the source code on Github here:** [<code><i class="fab fa-github"></i> hunj/nano</code>](https://github.com/hunj/nano)
