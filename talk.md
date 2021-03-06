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

 - Used to Join a matrix channel to another chat service
 - Availabel for many different systems

## Bots

 - Can be integrated as "application services" with priviledged access

# Setup

## Manual

# Setup - The Hard Way

## Setup - VM or Raspberry Pi

Start with an ArchLinux build.

(Setting that up is another talk)

## Setup - Install the package

```
pacman -S matrix-synapse
```

## Setup - Create Initial Config

```
cd /var/lib/synapse

sudo -u synapse python -m synapse.app.homeserver \
  --server-name chat.secretlair.org \
  --config-path /etc/synapse/homeserver.yaml \
  --generate-config \
  --report-stats=yes
```

## Setup - Generate Certs

Place them in `/etc/synapse/`

There's also a built in ACME client.

## Setup - Other packages

If you want to preview links:
```
pacman -S python-lxml python-netaddr
```

## Setup - Other packages

For LDAP suppoert, from the AUR install 
```
python-matrix-synapse-ldap3
```

## Setup - Other packages

Any other packages needed for bridges and bots

We'll get to those later.

## Setup - Edit Config

```
vim /etc/synapse/homeserver.yaml
```
(because you should be using vim)

## Setup - Base URLs

```
server_name: "chat.secretlair.org"
public_baseurl: https://chat.secretlair.org/
```

## Setup - Base URLs

FYI: You *can't* change these later!

## Setup - Define ports

```
listeners:
  - port: 8448
    type: http
    tls: true
    resources:
      - names: [client, federation]
```

## Setup - Cert Path

```
tls_certificate_path: "/etc/synapse/matrix.crt"
tls_private_key_path: "/etc/synapse/matrix.key"
```

## Setup - Media Path

```
media_store_path: "/var/lib/synapse/media_store"
```

## Setup - Enable URL Preview

```
url_preview_enabled: True
url_preview-ip_range_blacklist: 10.253.0.0/16
```

## Setup - Light the candle!

```
`systemctl enable synapse.service`
`systemctl start synapse.service`
```

## Setup - Create First User

```
register_new_matrix_user -c /etc/synapse/homeserver.yaml http://127.0.0.1:8008
```

## Setup - Create First User

You also can't delete users!

## Setup - LDAP Support

Just for funsies.

```
password_providers:
 - module: "ldap_auth_provider.LdapAuthProvider"
   config:
     enabled: true
     mode: "search"
     uri: "ldaps://ldap.secretlair.org:636"
     base: "dc=secretlair,dc=org"
     # Must be true for this feature to work
     active_directory: true
     # Optional. Users from this domain may log in without specifying the domain part
     default_domain: secretlair.org
     attributes:
        uid: "uid"
        mail: "userPrincipalName"
        name: "cn"
     bind_dn: ""
     bind_password: ""
     filter: "(&(objectClass=person)(memberOf=CN=chat,CN=Users,DC=secretlair,DC=org))"
```

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

## Element Web Client

Host any web-clients at a different URL

Prevents XSS attacks against your server

## Password Policy

This is 2600.
You should know this already.

## Single Sign-On

- SAML
- OpenID Connect
- Central Authentication Service
- LDAP

## Banners and ToS

Often overlooked when self-hosting.
Legal ass-covering.

## Captcha

Supports reCaptcha

## Configurable options

Synapse has a big-ass config file!

## Configurable options - Security

```
ip_range_blacklist
ip_range_whitelist
federation_client_minimum_tls_version
federation_domain_whitelist
max_uplaodSize
max_image_pixels
```

## Configurable options - Cont.

```
url_preview_ip_range_blacklist
url_preview_url_blacklists
bcrypt_rounds
allow_guest_access
```

## Configurable options - Privacy

```
use_presence
allow_public_rooms_without_auth
allow_public_rooms_over_federation
```

# Questions?

https://github.com/STL2600/matrix-talk
