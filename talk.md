% Project Nephology: Matrix Chat
% https://github.com/STL2600/matrix-talk

# What is it?

## A Protocol

Any system which implements the protocol is interoperable with any other

## Chat System

Similar to Slack or Discord

## Voice & Video Calling

This may take additional setup, but it is possible

## Federated

Servers are able to talk to each other, and you can talk to users on any server

## Encrypted

Both group and individual chats can be end to end encrypted with optional key verification

# Usage

## Homeserver

 - The server you log into
 - Can send messages to any server

## Element

 - Formally known as Riot
 - Available for web, desktop, iOS, and Android

## One on one chats

 - Are encrypted by default
 - Can be verified

## Rooms

 - Can be public or invite only
 - Can be encrypted

## Bridges

## Bots

# Setup

## Manual

# Setup - Docker

## Synapse - Generate a Config

```
docker run -it --rm \
    -v "$HOME/matrix/synapse:/data" \
    -e SYNAPSE_SERVER_NAME=matrix.server.com \
    -e SYNAPSE_REPORT_STATS=yes \
    matrixdotorg/synapse:latest generate
```

## Synapse - Deploy

```
matrix-synapse:
  container_name: matrix-synapse
  image: matrixdotorg/synapse
  restart: always
  ports:
    - 8008:8008
  volumes:
    - /srv/matrix-data:/data
```

## Synapse - Add a User

```
docker exec -it \
    register_new_matrix_user \
    -c /data/homeserver.yaml \
    http://localhost:8008
```

## Element - Deploy

```
matrix-element:
  container_name: matrix-element
  image: vectorim/riot-web
  restart: always
  ports:
    - 8009:80
  volumes:
    - /srv/element/config.json:/app/config.json
```

# Setup - Bridges

## Available Bridges

 - IRC
 - Slack
 - Discord
 - Signal
 - Facebook
 - And Many More

## Discord Setup

Clone repo from https://github.com/Half-Shot/matrix-appservice-discord#readme

## Discord Setup

 - Copy config file from repo to `/srv/matrix-discord/config.json`
 - Update the homeserver values in the config
 - Create an application and bot user on Discord, then copy the bot ID and token into the config

## Discord Setup

 - In the repo...
 - Run `npm install`
 - node build/src/discordas.js -r -u "http://localhost:9005" -c config.yaml
 - copy the resulting `discord_registration.yaml` file into your synapse config folder
 - Add the `discord_registration.yaml` file entry to the `app_service_config_files` array in the synapse config
 - Restart synapse

## Discord Setup

```
matrix-discord:
  container_name: matrix-discord
  image: halfshot/matrix-appservice-discord
  restart: always
  ports:
    - 9005:9005
  volumes:
    - /srv/matrix-discord:/data
```

## Discord Usage

 - In the repo...
 - Run `npm run addbot`
 - Use the link to authorize the bot for your Discord server
 - Invite the bot to a Matrix room
 - In the matrix room, say `!discord bridge <ServerID> <ChannelID>` to bridge the rooms

# Setup - Hardening

# Questions?

https://github.com/STL2600/matrix-talk
