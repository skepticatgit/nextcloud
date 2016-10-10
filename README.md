# Nextcloud

A safe home for all your data. Access & share your files, calendars, contacts, mail & more from any device, on your terms.

# Contents
This is a collection of docker-compose examples for installing nextcloud with docker containers.

Each subfolder contains a `docker-compose.yml` file and an `install` script. The install script creates mandatory configurations for the containers to run properly.

The compose files and the install script are controlled with environment variables from an `.env` file that has to be created first. You can find the environment variables in the .env-example file of each folder.

## minimal example
The minimal example just creates a nextcloud instance, a simple nginx webserver and a mariaDB database. It exposes the nextcloud installation to port 80 of your host. This example is insecure as any communication to your server is unencrypted.
Just use this image as example or for local testing.

## reverse proxy with letsencrypt
This example provides a full nextcloud installation that can be run as is. It uses the [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/) and the [jrcs/letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/) to automatically create letsencrypt certificates for you domains and redirects any request to https.
The reverse proxy resolves your containers to subdomains, so make sure to use a subdomain such as `cloud.example.com` as domain name.

## redis
The redis example builds on the reverse proxy with letsencrypt and adds file locking in a redis container. For local caching uAPC will be used.

## collabora
The collabora example builds on the reverse proxy with letsencrypt and adds the collabora [collabora/code](https://hub.docker.com/r/collabora/code/) container for online document editing.
The collabora container will use a different subdomain than you nextcloud installation, for example `office.example.com`.
To activate collabora you have to install the collabora-connector app from the productivity tab in the appstore (make sure to activate experimental apps) and enter your collabora subdomain name in the settings (without any port).

# Installation
First you have to create a `.env` file next to the `docker-compose` file you would like to use. Use the .env-example file to see which variables are needed.

```bash
cp .env-example .env
nano .env-example
```

When you have your .env file run the install script.

```bash
./install
```

After that you can start the containers. Depending on the example your using it can take a few moments for all containers to start.

```bash
docker-compose up -d
```

Once started, you'll arrive at the configuration wizard.
At the `Database Setup` step, please enter the following:

  -  Database Server: `db`
  -  Login: `nextcloud`
  -  Password: `<your password>`
  -  Database Name: `nextcloud`

And leave the rest as default.

# Update
To get recent container upgrades you just have to pull the new image and use the `up` command again. 
```bash
docker-compose pull && docker-compose up -d
```

# Contribute

Pull requests are very welcome!

We'd love to hear your feedback and suggestions in the [issue tracker](https://github.com/SnowMB/nextcloud/issues).
